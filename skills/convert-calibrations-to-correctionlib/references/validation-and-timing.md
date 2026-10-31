# Validation and timing

## Independent authority

Completion requires every required numerical acceptance check to pass; unresolved required failures keep the conversion experimental regardless of speed or ordinary-sample agreement. Record failures and missing checks in the acceptance records and task log. Missing reference information cannot count as a pass. If it blocks a required check, only an explicit user acceptance of that limitation permits completion with a documented narrower claim.

Freeze the original release/configuration/input and run it unchanged. Never make a rewritten backend the sole oracle. A second implementation is useful for diagnosis, but two paths sharing the same bug can agree.

Capture file identity, entry, original object index and systematic name. Preserve all objects before selection/thinning/sorting. Compare coverage and shapes before numerical arrays. Compare integer identifiers directly, not after conversion to floating point.

Observe source statuses explicitly when possible. Success-only output, inferred flags and missing statuses do not prove complete status parity. Distinguish tests on the acceptance sample from independent synthetic probes.

## Progressive comparisons

1. Test converted constants and primitive arithmetic against the original library.
2. Compare interpolation at bin centers, edges, boundaries and singleton axes; include flow, extrapolation and freezing.
3. Compare each calibration stage and the first divergent inputs, not only final histograms.
4. Test branching, validity, selections, passing/failing weights, correlated versus independent combinations and random behavior.
5. Test the entire resolved systematic list on the requested sample, independently restarting variations from nominal where required.
6. Reorder inputs and change chunk sizes. Test changed worker counts only when the user explicitly requests parallel execution; otherwise retain one CPU and one execution thread. Separate evaluator invariance from full input-pipeline invariance if only one is tested.

Before replacing explicit bin edges with `UniformBinning`, test the installed evaluator's actual floating-point arithmetic at every edge and its adjacent representable values, including the upper limit and each applicable flow mode. Equal spacing alone does not guarantee equivalent membership: rounding can move an input across an interior boundary or produce index `n` for an in-range input. Check both Binning and MultiBinning where used, and retain explicit edges when equivalence is not established. Record the evaluator version and revalidate after implementation or compiler changes; a sample pass alone does not establish boundary equivalence.

Use meaningful synthetic cases absent from the sample: missing/invalid inputs, thresholds, negative/near-zero momenta, special feature sentinels, flavour branches and zero-seed behavior. Test representation round trips, extension version failures and deterministic regeneration when introducing new payload formats.

Exercise adapter boundaries as well as numerical graphs: empty chunks/collections, absent primary objects and inconsistent associated-array lengths can fail before evaluation. Distinguish a complete-pipeline versus staged-call composition test from an independent original-tool comparison; agreement between two executions of the same prescription establishes composition, not independent physics correctness.

Do not automatically widen tolerances, remove failing objects or replace invalid values to pass. Record actual maximum differences and mismatch counts per state/quantity; show a useful first failure. Distribution overlays are presentation aids, not substitutes for object-level checks.

## Acceptance contract

Read the established project acceptance configuration before choosing tolerances. Preserve approved limits when continuing work; keep exact numbers and their rationale in project configuration and acceptance records rather than treating one campaign's values as universal defaults. Define quantity-specific absolute/relative tolerances and native units, wrap phi differences, and require exact identities, counts, coverage, selections and recorded discrete statuses. Record missing reference observables explicitly. Check B and C independently against A on the common selected PHYSLITE input; checkpoint parity between replacement versions is a separate regression check.

## Benchmark after correctness

Use one CPU and one execution thread unless the user explicitly requests parallelization. Disable implicit library thread pools and additional processing workers, restrict CPU affinity where needed, and record effective limits. Optimize the algorithm and execution overhead rather than adding parallelism. Historical multi-worker campaigns describe earlier measurements, not the current execution policy.

Distinguish evaluator-only timing from a complete analysis or diagnostic workflow. State inclusion of setup, imports, initialization, input I/O, feature preparation, reference comparison, output writing and repeated calculations. Do not put fabricated phase estimates into columns where only total time was measured.

For a four-run campaign with first excluded, run each path four times sequentially, retain all measurements, and compute mean and sample SD from runs 2-4. Sample SD describes run-to-run spread, not physics uncertainty or standard error of the mean. Use a monotonic wall clock, preserve full commands/environment and record validation success for each run.

Use fresh result directories; do not overwrite accepted artifacts. Regenerate preparation caches inside the timed scope when claiming input-to-output timing. State cache policy and possible warm filesystem/CVMFS caches. Avoid concurrent heavy work; record observed interference without inventing a causal explanation.

If comparing to measurements from an earlier campaign, label that fact. Different diagnostic overhead and runtime variability prevent an isolated kernel-speed claim. Profile the actual workload before attributing a slowdown to CorrectionLib itself.

For an input-to-output nominal/full comparison, hold the source file, branch list, chunking, feature preparation, selection, histogram axes/output format, CPU affinity and worker counts fixed between modes. Interleave modes to expose drift, preserve each run, and check that the nominal result is identical between them. Validate aggregate output against the independent original outside the measured interval when the desired timing excludes checks. Keep this separate from per-object numerical acceptance: matching histogram bins and selection counts cannot prove all jet values or weight sums match.

### Diagnose a declarative slowdown

A migration can add graph dispatch, subgraph calls, scalar formula evaluations, Python/C++ conversion and allocation while retaining the same underlying table evaluator. More payload-defined physics and a slower total workflow do not establish that CorrectionLib lookup is the cause.

Use the measured phase breakdown to choose the next probe. Separate input/feature preparation, transfer across bindings, numerical graph execution, table/formula evaluation, model inference, validation and output where practical. Compare the same resolved states and prepared inputs when isolating execution; retain a separate input-to-output measurement. Account for repeated nominal/systematic work and full-state materialization before comparing a staged runner with a fused pipeline. A named complete pipeline is not necessarily fused execution or shared computation across states.

Treat allocation, dispatch and inference costs as hypotheses until measured. For changes intended to remove an overhead, compare against the preserved baseline with unchanged physics and acceptance criteria. Report improvements only for the measured scope. Do not conclude that performance inherently worsens with greater CorrectionLib use from one architectural comparison.

### Separate fixed and sample-dependent overhead

When phase timings are unavailable, compare the original and replacement at a very small positive event count and at larger counts with the same configuration and state coverage. Record both event and object counts. Use matched repetitions and reversed/interleaved order or fresh reference brackets to expose drift. Record wall time and process CPU time, including worker descendants when the workflow launches subprocesses. Keep environment setup and validation consistently inside or outside the timer.

The difference versus sample size can distinguish fixed startup/shutdown cost from accumulated processing cost. A two-point slope/intercept is diagnostic evidence, not a precise scaling law; label extrapolation separately from a measured full-sample run. Constructor times are not net initialization penalties against the original tools and exclude cleanup unless explicitly timed. Inspect repeated payload ownership/loading before attributing fixed cost to file size alone.

### Isolate components and mechanisms

Replace one package or stage at a time while retaining the original implementations of the others, with identical inputs, outputs and resolved states. Verify which libraries actually loaded, rather than trusting setup-script names. Keep ABI-dependent factories, dictionaries and implementation libraries consistent; local ROOT-specific cautions are in [ATLAS lessons](atlas-lessons.md#framework-and-tooling-boundaries). Validate each successful isolation job against the original reference outside the timer when that matches the benchmark scope.

Report spread and observed host activity. Do not add noisy package differences into an exact overhead budget: drift, shared initialization and interactions can prevent additivity. Do not stop unrelated workloads to obtain a quiet machine without authorization. CPU affinity can make native microbenchmarks more comparable; record whether workflow jobs used the same affinity and cache policy.

Use representative prepared inputs for an isolated C++ benchmark when Python conversion, framework access or I/O would obscure the suspected mechanism. Keep the input preparation independently traceable to original data and the tested nominal path. State whether the sample count means events, objects or evaluations, which states are covered, and whether argument/result allocation remains timed. Record source, compiler/optimization settings and binary identity; a separately compiled backend is not a profile of the actual framework process.

Use one-change experiments to test candidates such as runtime recipe-field/string/constant processing, table-name/handle resolution, unnecessary lookups before an inactive branch, and repeated random-engine construction. Measure a combined experiment separately; individual savings need not add. Distinguish custom adapter/recipe interpretation from CorrectionLib formula parsing and evaluation. Preserve a complete-workflow baseline and remeasure it before claiming an end-to-end improvement.

Profile only the intended region when isolating execution; exclude payload construction deliberately rather than accidentally including it. Callgrind instruction shares are not elapsed-time shares. Inclusive call counts overlap and must not be summed; a large copy/allocation total does not identify its callers without call-path inspection. If a package-level cost exceeds the isolated numerical-kernel cost, investigate adapter input access, output writes, initialization and cleanup instead of assigning the unexplained remainder to the evaluator.

### Validate optimization experiments at their claimed scope

Check full state/output coverage for every experimental variant and repetition. Deterministic hashes of all returned quantities are useful regression evidence, but matching hashes of a sample do not establish independent physics correctness or replace boundary/status checks. Retain the original tool as authority. Before adopting a performance experiment, rerun the applicable full-sample, boundary, ordering/chunking/concurrency and integration checks, then benchmark the integrated implementation.

Moving a no-op guard ahead of a lookup must preserve validity/error behavior, rounding and random-stream consumption as well as ordinary numerical outputs. Reusing a random engine is safe only when its seed/reset and draw semantics preserve the source contract; thread-local reuse from a per-object-reseeded example is not a general prescription for stateful streams. Keep uninstalled scratch experiments clearly distinct from production changes.

## Compare payload sizes fairly

Inventory the files actually consumed by each runtime and report exact bytes, separating correction tables, executable recipes/graphs, inference models, settings/manifests and provenance-only files. Label totals accordingly; do not silently compare a nominal-only file with a full nominal/systematics bundle. Separate stored/compressed bytes from decompressed JSON bytes and record compression settings when known. Different gzip levels, JSON whitespace or merged representations can change size without changing the physics complexity.

A payload-only comparison excludes compiled logic and libraries: a hybrid implementation may keep substantial prescription in C++ while a declarative one serializes it. Explain that difference. Large numerical tables or a larger compressed bundle alone do not establish poor composability, a runtime memory requirement or an evaluator scaling limit. Measure load time and memory separately if those are the concern.

## Recording optimization and detailed costs

### Maintain the complete optimization record

Keep a cumulative table beginning at the original optimization baseline, not just the latest subcampaign. For each row record the implementation change, source location, authoritative implementation commit, timing scope, CPU/worker policy, repetitions, discarded warm-up, mean/spread and acceptance status. Include rebuild controls, policy transitions, inconclusive changes and reverted attempts. Profiling studies and final framework integration checks belong in the record but are not new full-workflow optimization timings. Record the actual candidate commit, not only the pre-change HEAD captured by a timer.

Keep original reference measurements, fresh unchanged controls, repeated final results and single integration runs distinct. A later faster repeat with unchanged active code is not evidence of a payload optimization. Do not add individual gains into a predicted combined result. Preserve source/library/payload hashes, loaded-library evidence where needed, and all earlier artifacts; update bundle manifests when changing payload bytes. No-gain changes may still establish a useful capability, but should not be sold as performance improvements.

For requested no-calibration controls, explicitly disable recalibration, variation evaluation, tagging/selection and weights as appropriate. `NOSYS` or “systematics off” can still run nominal calibration. Compare stored-object read/plot, extra-input I/O, nominal-only and full workflows with their output scopes visible. A reference-framework run that writes an intermediate tree before plotting is not the same reader benchmark as direct columnar file-to-histogram processing. If a mode has not been measured, leave it missing rather than inferring its time.

### Measure original versus replacement lookup directly

Use the actual original lookup implementation on the same source tables, interpolation modes and input points as the exported CorrectionLib corrections. Resolve names and load/construct tables outside the timing region. Include the original boundary behavior and any projection caches, and validate values before comparing speed. For paired quantities such as content/error, measure the same pair: a shared ROOT bin search and two independent correction evaluations do different amounts of work.

Separate the current production call shape from experiments such as reused argument vectors, uniform edges or MultiBinning. Use one CPU and alternating/rotated order when that matches the objective; preserve all repetitions. Report time per lookup/pair and whether allocation is timed. Sweeping every table equally is not a production-weighted workload: also consider a test-only lookup swap inside the unchanged shared calculation, with identical prepared inputs, state coverage and outputs. Keep that replay distinct from complete original/replacement framework jobs. Do not install the alternate backend merely to obtain a benchmark.

Uniform encoding is a representation choice, not a license to approximate boundary decisions. Count converted nodes and determine whether the requested workload uses them. Changes confined to inactive nodes cannot establish a hot-lookup gain, even if a later workflow run is faster. Validate toy payloads too: check whether the selected parser requires floating-point JSON edge literals such as `0.0`, not integer literals such as `0`.

### Use higher-resolution profiles without overstating them

Start with scoped timers around executed stages, then capture representative inputs only from calls that reach the numerical backend after caching/selection. Capture outside the timed kernel region. Inspect allocations and arithmetic separately with a native replay when useful, documenting replay sample size, compiler and cache/branch state. A cold random cache over a small replay is not the production traversal.

Use exclusive instruction counts when quantifying allocation functions; inclusive graph/table totals overlap them. Do not convert instruction shares directly into seconds. If a profiler has inconsistent call accounting or significant instrumentation effects, use independent elapsed timers for the workflow budget rather than normalizing its totals. Keep an unattributed remainder visible. After optimizing a measured hotspot, label the old profile with its checkpoint; a new total runtime alone does not establish the new distribution of CPU cost. Reprofile when that distribution is the question.

## Evidence package

Retain raw comparisons/timings, resolved configuration, input and payload hashes, source revision/patches, dependency versions and command recipes. Record CPU model, physical/logical cores, host memory, OS/virtualization and guest-visible memory where applicable; distinguish machine capacity from each run's CPU policy and measured peak memory. Validation metadata should reflect completed checks, not merely successful conversion or build. Use the [report and talk guide](report-and-talk-guide.md) when turning this evidence into deliverables.
