# Profiling calibration code efficiently

Read before investigating a performance bottleneck. Choose the least intrusive tool that answers the question; profiles select experiments, while uninstrumented repeated workflows establish gains.

## Terms to define in reports

**Sampled CPU share** estimates the fraction of recorded CPU observations at a function or stack; it is not a wall-time fraction and does not include time blocked waiting for I/O. **Self (exclusive) cost** counts samples executing inside a function itself, excluding functions it calls. **Inclusive cost** also includes its callees; inclusive rows overlap and must not be added. Neither term says how many CPUs were used. Define these terms next to the first profiling table in the report, using plain labels such as “exclusive share of sampled CPU time.” Keep unresolved function names distinct from known functions that have not been assigned to input/evaluation/output stages.

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

This is a portable command template; the separately documented WSL verification below establishes one tested environment, not universal support. Check exit status, sample count, lost samples and stack quality. Record the kernel/tool/event versions and errors. Software CPU sampling can identify CPU hotspots without hardware cycles/cache counters, but it does not explain sleeping time or provide hardware metrics.

Use optimized code with debug information and preserve matching binaries/build IDs. DWARF unwinding has overhead and depends on support and stack capture; frame-pointer unwinding is an alternative only when relevant binaries retain frame pointers. A debug/unwind rebuild changes the measured binary: treat it as a diagnostic artifact, not automatically as the production baseline. Missing symbols, inlining and truncated stacks limit attribution.

For framework jobs, verify samples include the event-processing descendants, not only a launcher that waits. For standalone demos, distinguish launcher/startup profiling from profiling the actual Python analysis. Keep CPU affinity and numerical thread limits; do not use system-wide collection by default.

In WSL/VMs, the guest distribution and host-provided kernel can differ, and hardware PMUs may be unavailable. An installed perf package alone proves neither event support nor kernel compatibility. Inspect package availability and use a compatible tool build when needed; do not silently change system permissions, kernel settings or the execution environment. If sampling is blocked, record the precise limitation and use targeted instrumentation rather than spending an unbounded session on profiler setup. See [perf record options](https://man7.org/linux/man-pages/man1/perf-record.1.html) and [kernel perf access controls](https://docs.kernel.org/admin-guide/perf-security.html).

## Collect at least 60 seconds for the final native profile

A short run is sufficient to check event support and symbols, but the final hotspot study must cover at least **60 seconds of actual repeated input processing**, excluding dependency installation, waiting/sleep and one-time launcher/setup work. Use the standalone script's `-n N` mode to reopen and process the original input repeatedly, with the full requested systematic set and unchanged per-pass work. Estimate N from measured processing time per pass, for example `ceil(60 / seconds_per_pass)` with a modest margin, then verify and record the observed processing duration. If it falls short, preserve that diagnostic and collect a new profile with a sufficient N; never pad with sleep or claim installation time as analysis coverage.

Record N, unique input events/objects, total event/object visits, per-pass counts/agreement, pass timing boundaries, sample rate/count, lost samples and symbol/stack coverage. More samples reduce sampling noise; they do not fix missing symbols or establish exact stage ownership. Keep the sample rate modest initially, inspect losses/overhead, and retain matching uninstrumented measurements separately. Repeated passes amortize initialization and tend to warm filesystem caches: describe this as a sustained repeated-pass workload and do not silently replace the single-pass workflow profile or benchmark. State whether the native profile covers the whole process or only repeated processing and account for startup/final output accordingly.

Keep calibration and input execution on one thread pinned to one CPU, including implicit library workers. Profiler collection/control work may use a separate CPU; set and verify collector and analysis affinities independently, ensuring descendants inherit the intended analysis affinity. Do not enable system-wide collection or a second analysis worker to obtain more samples. Record effective placement and any remaining profiling interference.

## Verified WSL software-sampling recipe

On 2026-11-01, software sampling was verified in the AlmaLinux 9 WSL2 environment `atlas_al9`, using perf `5.14.0-687.61.1.el9_8.x86_64` with kernel `6.18.33.2-microsoft-standard-WSL2`. The distribution perf package worked for this test despite the different kernel version; a custom kernel or perf rebuild was not needed.

Use the following as the first native sampling attempt on that environment, with the real analysis command and a fresh output path:

```bash
taskset -c 0 perf record -e cpu-clock:u -F 99 --call-graph dwarf \
  -o NEW-PROFILE.data -- <analysis command and arguments>
perf report --stdio -i NEW-PROFILE.data
# Flat self-cost view, without duplicating inclusive child costs:
perf report --stdio --no-children --call-graph none \
  --sort dso,symbol -i NEW-PROFILE.data
```

The verification used the installed standalone analysis on the full original input and requested states, with CPU 0 affinity and one observed analysis thread. It captured 472 samples with zero lost samples, resolved useful native provider/context stacks, and preserved exact saved-histogram agreement with the original framework. Some binding frames remained unnamed. This establishes useful native sampling, not complete symbolization, source-line attribution or a precise profiler-overhead estimate.

Hardware `cycles:u,instructions:u` were reported as **not supported**, even though `perf stat` returned exit status zero. Inspect counter values and diagnostics, not merely the exit code. Use `cpu-clock:u` explicitly when hardware events are unavailable; do not infer hardware counter support from successful software sampling.

These are dated environment compatibility observations, not universal WSL guarantees or calibration-specific optimization advice. Recheck event support, lost samples, worker coverage and stack quality after environment changes. Whole-process CPU samples include startup, input preparation and output as well as evaluation; do not label their percentages evaluation-only or replace uninstrumented workflow measurements with this diagnostic. Detailed campaign evidence remains in its project records; no local artifact path is presented as a public evidence link.

## Make instrumentation answer a narrow question

Use isolated diagnostic builds and verify the actual loaded provider/adapter paths. Keep the accepted build intact. For timers, define non-overlapping phases or track nesting explicitly: inclusive child time cannot also be added to an exclusive parent budget. Keep timing/counter aggregation in memory and emit it after the measured region.

Compare three modes when adding detailed native instrumentation: accepted uninstrumented, instrumentation compiled but disabled, and enabled. Compare outputs in all modes and quantify overhead. If overhead is substantial, coarsen scopes or switch to sampling; do not subtract a global overhead percentage to invent precise kernel times. This matters especially for millions of short metadata/cache calls.

Counters are appropriate for proving cache eligibility, publication, bypass and native batch dispatch, including fallback cases. Count the production call graph; a diagnostic interface's available output count may differ from the calls the analysis actually makes. Timers and counters must not change exception order, RNG draws, input mutation or thread behavior.

## VTune and stopping conditions

Check `command -v vtune`, legacy `amplxe-cl`, installed packages and likely installation locations before assuming availability. Do not infer absence everywhere merely from PATH. VTune can be considered for a specific threading or microarchitectural question when its collector and required VM/hardware features are available; it is not a prerequisite for these conversions, and installation alone does not establish collector support.

Keep tool selection proportional to the question. Sampling is generally the better first native hotspot investigation; semantic counters and targeted scopes remain useful when samples cannot distinguish why work repeats. Neither replaces original numerical acceptance or uninstrumented paired timing. Do not promise that a profiler would have found a particular optimization or saved a measured amount of engineering time without evidence.
