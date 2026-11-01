# Profiling calibration code efficiently

Read before investigating a performance bottleneck. Choose the least intrusive tool that answers the question; profiles select experiments, while uninstrumented repeated workflows establish gains.

## Tool selection

| Question | Start with | Helps with | Does not establish |
| --- | --- | --- | --- |
| Where does total time go? | Coarse `time.perf_counter()` phase timers | Startup/loading, reading/preparation, evaluation, histogram/output | Internal native hotspots or exact allocation costs |
| Is Python orchestration expensive? | `cProfile` and `pstats` | Python call counts, self/cumulative time, repeated reader/wrapper calls | Breakdown inside a long C++ extension call; profiler timings are not benchmarks |
| Which native functions consume CPU? | `perf record` sampling, if usable | C++/ROOT/evaluator stacks without adding timers to every call | Exact call counts, cache hit rates, or time spent blocked on I/O |
| Is a specific stage or adapter expensive? | Targeted C++ `std::chrono::steady_clock` scopes | Input preparation, argument packing, node dispatch and selected numerical regions | Unperturbed costs when tiny scopes are measured millions of times |
| Did reuse/batching actually execute? | Lightweight native counters | Hits/misses/bypasses, actual production calls, specialized/default dispatch | Time saved; fewer calls need not improve the full workflow |
| Is the bottleneck microarchitectural? | Hardware perf events or VTune, if supported | Cycles/instructions and supported cache/branch/memory investigations | Hardware insights when the VM does not expose the required PMU |

## Recommended sequence

1. Retain a validated, uninstrumented baseline and inspect coarse phase times.
2. Use one short representative Python profile if ownership of the cost is unclear. Once most time is inside native evaluation, stop refining the Python profile to explain C++ stages.
3. Check native sampling availability before building a custom profiler. Start with a modest sampling rate and inspect whether the actual worker/provider appears with useful symbols.
4. Add only the counters or coarse native scopes needed to distinguish the remaining hypotheses. Prefer counts for tiny repeated operations.
5. Validate instrumented outputs against the accepted implementation, then remove/disable instrumentation for repeated performance measurements. Keep all diagnostic results separate from the timing ledger's production measurements.

A useful Python invocation, using the same interpreter/environment as the analysis:

```bash
taskset -c 0 python -m cProfile -o NEW-PROFILE.pstats analysis.py INPUT OUTPUT
python -c 'import pstats; pstats.Stats("NEW-PROFILE.pstats").strip_dirs().sort_stats("cumulative").print_stats(30)'
```

Adapt arguments to the real entry point and use fresh outputs. Inspect self time and call counts as well as cumulative time. A C++ extension entry consuming almost all time tells you where to switch tools, not which native algorithm is slow. See [Python 3.14 profiling documentation](https://docs.python.org/3.14/library/profile.html).

## Check perf rather than assuming it works

Check independently: executable availability, event permission/support, successful recording, and usable symbols/call stacks.

```bash
command -v perf
perf --version
uname -r
cat /proc/sys/kernel/perf_event_paranoid
perf list
perf stat -e cpu-clock:u -- /bin/true
perf stat -e cycles:u,instructions:u -- /bin/true
```

A successful trivial event-open/count probe does not establish sampling quality. If hardware events fail but software CPU-clock events work, try software sampling on a short CPU-active real workload:

```bash
taskset -c 0 perf record -e cpu-clock:u -F 99 --call-graph dwarf \
  -o NEW-PROFILE.data -- python analysis.py INPUT OUTPUT
perf report --stdio -i NEW-PROFILE.data
```

This is a command template, not a claim that perf was installed or validated in a previous campaign. Check exit status, sample count, lost samples and stack quality. Record the kernel/tool/event versions and errors. Software CPU sampling can identify CPU hotspots without hardware cycles/cache counters, but it does not explain sleeping time or provide hardware metrics.

Use optimized code with debug information and preserve matching binaries/build IDs. DWARF unwinding has overhead and depends on support and stack capture; frame-pointer unwinding is an alternative only when relevant binaries retain frame pointers. A debug/unwind rebuild changes the measured binary: treat it as a diagnostic artifact, not automatically as the production baseline. Missing symbols, inlining and truncated stacks limit attribution.

For framework jobs, verify samples include the event-processing descendants, not only a launcher that waits. For standalone demos, distinguish launcher/startup profiling from profiling the actual Python analysis. Keep CPU affinity and numerical thread limits; do not use system-wide collection by default.

In WSL/VMs, the guest distribution and host-provided kernel can differ, and hardware PMUs may be unavailable. An installed perf package alone proves neither event support nor kernel compatibility. Inspect package availability and use a compatible tool build when needed; do not silently change system permissions, kernel settings or the execution environment. If sampling is blocked, record the precise limitation and use targeted instrumentation rather than spending an unbounded session on profiler setup. See [perf record options](https://man7.org/linux/man-pages/man1/perf-record.1.html) and [kernel perf access controls](https://docs.kernel.org/admin-guide/perf-security.html).

## Make instrumentation answer a narrow question

Use isolated diagnostic builds and verify the actual loaded provider/adapter paths. Keep the accepted build intact. For timers, define non-overlapping phases or track nesting explicitly: inclusive child time cannot also be added to an exclusive parent budget. Keep timing/counter aggregation in memory and emit it after the measured region.

Compare three modes when adding detailed native instrumentation: accepted uninstrumented, instrumentation compiled but disabled, and enabled. Compare outputs in all modes and quantify overhead. If overhead is substantial, coarsen scopes or switch to sampling; do not subtract a global overhead percentage to invent precise kernel times. This matters especially for millions of short metadata/cache calls.

Counters are appropriate for proving cache eligibility, publication, bypass and native batch dispatch, including fallback cases. Count the production call graph; a diagnostic interface's available output count may differ from the calls the analysis actually makes. Timers and counters must not change exception order, RNG draws, input mutation or thread behavior.

## VTune and stopping conditions

Check `command -v vtune`, legacy `amplxe-cl`, installed packages and likely installation locations before assuming availability. Do not infer absence everywhere merely from PATH. VTune can be considered for a specific threading or microarchitectural question when its collector and required VM/hardware features are available; it is not a prerequisite for these conversions, and installation alone does not establish collector support.

Keep tool selection proportional to the question. Sampling is generally the better first native hotspot investigation; semantic counters and targeted scopes remain useful when samples cannot distinguish why work repeats. Neither replaces original numerical acceptance or uninstrumented paired timing. Do not promise that a profiler would have found a particular optimization or saved a measured amount of engineering time without evidence.
