# Report and talk guide

Use this guide when preparing or revising the migration's report, outline or deck. Write `report.md` and prepare the talk unless the user narrows the deliverables. Both address ATLAS colleagues who may not know CorrectionLib or the calibration and optimization details.

For every talk produced with this workflow, read and apply the [scientific-talks skill](https://github.com/gordonwatts/scientific-talk-marketplace/blob/main/plugins/scientific-talks/skills/scientific-talks/SKILL.md) as the foundation for outlining, evidence, style, deck construction and revision. Use its Gordon speaker profiles for talks written for Gordon: content guidance when outlining and visual guidance when building or editing slides. Select the talk-type profile from the brief; do not assume every talk is a colloquium. If the skill is unavailable locally, read its linked source and relevant references. This guide supplements it with calibration-specific content and evidence requirements. Preserve explicit user choices, including an existing DeckCraft requirement; otherwise follow the scientific-talks authoring-tool defaults.

Continue the selected outline and existing deck; preserve their general structure, filenames and speaker edits. Follow repository instructions for document locations and commits. Use the task log and accepted evidence; preparing documentation alone does not justify rerunning benchmarks.

## Write for a reader new to the work

The report and talk must be understandable to an ATLAS colleague who has not participated in the investigation, read the chat, seen a previous draft or opened the task log. They are a coherent account of the scientific/technical work, not a sequence of status updates. Establish the question and motivation, explain the original workflow and proposed replacement, then connect implementation choices, validation, measurements and limitations into an argument the reader can assess.

- Define A/B/C, unfamiliar terms, tool/node roles and measurement conventions before using them. Explain what each mechanism does and why it matters; commit IDs, local filenames and labels such as “restored baseline” cannot carry that explanation on their own.
- Describe validation in particle-physics terms: name the quantities compared (for example calibrated momentum, selections and scale factors), the original and converted paths, the number of input events and objects, the nominal/systematic coverage, and the observed agreement. Lead with a statement such as “We compared the calibrated values from the original ATLAS implementation (A) and the columnar implementation (C) for N events containing M muons, for nominal calibration and all S systematic states. All compared values were numerically identical,” only when that exact scope and equality are supported. If agreement is within tolerances, give the tolerances and relevant differences instead; disclose exceptional/nonfinite handling and exclusions as needed. Avoid leading with “the rebuilt baseline passes X framework/observer comparisons.” Aggregate scalar-check counts are software bookkeeping, not numbers of independent events or independent physics tests: keep them in supporting evidence, explain what they count when used, and do not sum overlapping checks into a headline claim. Apply this guidance to report prose, slide content and spoken notes.
- Describe the final executed implementation and accepted scope directly. When revising a conclusion, rewrite the affected paragraph/section and reconcile related tables, captions and conclusions. Do not append “now updated,” “as requested,” “the previous answer,” or similar editing commentary that requires conversation history. Record editorial decisions, requested changes and progress in the task log instead.
- Retain investigation history when it explains a technical conclusion: for an optimization or rejected trial, introduce the starting behavior, hypothesis, actual change, validation, matched result and consequence. Explain what was restored and why if that matters. This causal history and the complete timing ledger remain evidence; they must not be confused with a document revision diary.
- State the interpretation of results, failure modes and accepted exclusions in the narrative itself. A link supplies supporting detail, not missing context. Use descriptive evidence labels that say what a source demonstrates; keep exhaustive commands, hashes and provenance in linked records or backup without making them prerequisites for understanding the main claim.
- Apply the same standard to the talk's audience content, spoken explanation, notes and backup. Introduce the necessary context as the argument develops; do not assume listeners know internal checkpoint names or have read the report. A revision should leave one consistent explanation of the final result, not contradictory old and new slide claims.

Before delivery, read the report and outline as if the conversation and task log were unavailable. For each section, check that a new reader can identify what is being discussed, why it was done, how the evidence supports the result, and what the result permits or limits. Rewrite unexplained references and update-style paragraphs; check definitions, cross-references and consistency across report, talk and figures. This is an editorial review, not a reason to rerun valid numerical work.

## Report

Keep the report concise, linking detailed artifacts rather than reproducing them. Cover:

1. **Question and reference:** investigation goals, supported configuration, original release/source revision, input identity, event/object counts and Path A baseline.
2. **Implementation:** conversion and runtime call path, shared B/C integration and responsibility map. Identify existing/custom nodes, payload-owned configuration, C++ physics, numerical-library dependencies, node contracts and version/build/loading requirements. Explain implementation choices and distinguish historical or proposed architectures from executed code.
3. **Correctness:** the original-tool acceptance contract, quantities, units, tolerances, systematic states, boundary/status checks and actual results. Keep source bugs preserved by the conversion, unresolved failures, missing observables and accepted limitations visible.
4. **Performance:** initial and final comparisons for the requested paths, plus the cumulative optimization history. Identify each mechanism, implementation commit, validation scope and measured gain; retain controls, minor changes and unsuccessful/reverted experiments. Label timing boundaries, CPU policy, repetitions/spread and earlier-campaign versus freshly repeated measurements (earlier-campaign B/C may contain modified code; reserve “unmodified ATLAS release” for untouched Path A).
5. **Standalone use:** the runnable input-to-output example, exact install/run command, environment/ABI, state coverage, output schema, wheel/bundle sizes and observed result. Separate portability from offline or cross-platform claims, and cold installation from warm execution.
6. **Reproduction and conclusions:** links to configuration, source revisions, payload provenance, build/conversion/run commands, raw acceptance/timing results and the task log. State measured benefits, costs, remaining limits and the next question.

Keep numerical, architectural and performance evidence distinct. Use [validation and timing](validation-and-timing.md) for acceptance, hardware, benchmark and payload-size methods, and [portable demo packaging](portable-demo.md) for the standalone procedure.

Report B's matched performance against A explicitly. If B is slower, describe the dedicated investigation, validated changes and remaining adoption blocker; a faster C is not a substitute for framework non-regression. Show the checkpoint after straightforward optimizations with original tool boundaries, then any subsequent tool combination as its own architectural step, with separate numerical acceptance and timing attribution. See [framework non-regression and tool-boundary sequencing](validation-and-timing.md#protect-framework-path-b-from-regression).

## Required ROOT input footprint in the report

Report how much original event data the configured calibration reads, alongside its I/O timing:

- **Distinct ROOT leaves loaded:** inventory fully qualified tree/branch/leaf names actually read, including linked tracks, vertices, event metadata and count/offset dependencies. Count each leaf once across chunks and systematic states. Distinguish ROOT leaves from branches and expanded adapter fields; do not substitute the number of packed numerical inputs. State the branch-to-leaf mapping where a branch contains multiple leaves.
- **Average loaded data in KB/event:** report total bytes for those inputs divided by the number of input events read, including events with zero selected objects, then divided by 1,000. Label KiB separately if using 1,024. State sample/file identity, tree, entry range, event count, nominal/full-state scope and reader configuration. Do not multiply by the number of systematic states when raw data is read once and reused.
- **Byte basis:** give compressed ROOT basket bytes and uncompressed/decoded bytes separately where measurable, with the latter's representation defined (for example serialized uncompressed basket data or materialized arrays including offsets). State the counting method and treatment of metadata, basket boundaries, read-ahead and repeated reads. Basket metadata totals are estimates of the selected input footprint, not measured physical disk/network traffic; physical traffic depends on caching and read behavior. Do not use total file size/event or post-packing array size as an unlabelled input-read measurement. If a quantity cannot be measured, retain that limitation rather than inventing a value.

Separate inputs required by the active calibration/selection/weight prescription from extra fields loaded by transport, validation, output or diagnostics, and report the actual reader total. Label differences between paths A/B/C and between nominal/full modes instead of assuming identical leaf sets. Retain a machine-readable inventory with per-leaf or non-overlapping per-branch byte counts, totals and reproducible measurement commands; link it from the report. Reuse valid input metadata or perform a separate read diagnostic outside benchmark timing; this requirement alone does not justify rerunning numerical acceptance or the timing campaign.

## Required profiling study in the report

Include a profiling study answering **where most execution time is spent in the final accepted workflow**. Use [profiling tool selection](profiling.md): perf sampling when usable, otherwise explain the concrete limitation and use an appropriate alternative. Historical profiles can explain optimization decisions but must not be presented as the final implementation's cost distribution.

Report both levels of evidence:

Define “sampled CPU,” “self/exclusive” and “inclusive” beside the first profiling table, following the [profiling terminology](profiling.md#terms-to-define-in-reports). Explain that self excludes callees, inclusive includes them, and neither describes the number of CPUs. Prefer the label “exclusive share of sampled CPU time” over unexplained “self sampled CPU.”

- An uninstrumented workflow phase breakdown (startup/loading, input/preparation, correction evaluation, histogram/output and unassigned time) from the same retained campaign where available. Define timer boundaries, denominator and any overlap.
- A native hotspot table or plot with readable function/stage names, self versus inclusive cost, event/sample basis and the workload profiled. Inspect call stacks to connect low-level symbols to analysis work. Distinguish identified functions omitted from a short list, unresolved symbols and samples not mapped to an analysis stage; do not merge these into one unknown/unassigned category.

When a shortened hotspot table has an “all other functions” row, include its breakdown in the report itself, grouped by executing library or supported functional category, with examples of the work in each group. Retain the full per-symbol evidence and report unresolved-symbol coverage separately. Keep the same whole-profile denominator, use non-overlapping self costs, and explain rounding differences when reconciling the groups with the remainder. Do not leave most of the profile hidden behind an unexplained remainder or describe identified omitted functions as unknown execution.

Explain any remaining analysis-stage attribution gap: knowing the executing library/function does not establish whether shared interpreter, allocation or copying routines ran during input, evaluation or output. State whether sample-by-sample stage classification was performed and what the stacks can actually support. Present the separately measured elapsed-time phase breakdown alongside this explanation, including its own unassigned remainder; sampled CPU shares and elapsed phase shares are different measurements and must not be combined.

State the input, state set, source/binary identity, tool version, exact command, CPU policy, sample count, lost samples, symbol/unwind quality and measured instrumentation overhead where available. The final native study must use the [60-second repeated-input protocol](profiling.md#collect-at-least-60-seconds-for-the-final-native-profile): report `-n N`, observed processing duration, unique inputs versus total visits, per-pass agreement and cache/output scope. State profiler and calibration CPU placement separately. Link raw profiles or their retained location, reproducible report commands and machine-readable summaries. Record the output agreement check for the profiled run. Clearly separate sampled CPU percentages from elapsed-time fractions; never convert them into exact seconds or predicted speedups using a different run.

Conclude with the dominant observed costs, actionable hypotheses and what the study cannot resolve. A short low-statistics profile can establish coarse hotspots, but do not claim precise rankings or a complete cost attribution from it. If phase data or symbol coverage is missing, disclose that gap. Reuse existing valid profiles and timing records where possible; do not rerun a benchmark just to redraw a table.

## Visual agreement for every calibrated variable

Generate and deliver an original AnalysisBase A versus columnar C comparison for **every calibrated output variable in the requested scope**, not only one illustrative pT histogram. Inventory the variables first (including derived calibrated quantities exposed by the analysis); mark unchanged passthrough quantities and unavailable original observables explicitly. Do not silently substitute a different quantity or omit a variable because its reference was not captured.

For each variable, plot the actual saved A and C results on identical bins with matched input/event range, object identity, selection and weights. Include a clearly labelled absolute residual C minus A beneath the overlay; if using a ratio, mask and disclose zero-denominator bins. Use physical units and readable legends, distinguish curves even when they coincide, and report entries, underflow/overflow and nonfinite handling. Do not normalize the two distributions independently to conceal yield differences.

Provide nominal overlays for all variables and a systematic agreement summary covering every requested state for each variable (for example a variable-by-state maximum-residual plot). Include detailed overlays for discrepant states and representative kinematic variations; do not claim that a nominal plot demonstrates all-state agreement. For unchanged variables under weight-only variations, state that distinction explicitly. Discrete decisions/statuses need count or mismatch summaries rather than misleading continuous distributions.

Retain generators, source artifact paths/hashes, accepted implementation identity, plotted values and machine-readable comparison results. Visually inspect the rendered figures, link the complete set from the report, and show at least one actual comparison directly in the delivery response. A short talk may show representative plots while linking the full set in backup. Histogram agreement supplements per-object/boundary acceptance and cannot replace it.

Reuse preserved numerical outputs where available; plotting alone does not justify another benchmark campaign. If an original or converted variable was not saved, record the gap and capture the missing comparison outside timing before claiming complete visual coverage.

## Talk structure

The main talk must include at least one dedicated visual-agreement slide showing an actual AnalysisBase-versus-columnar overlay with its residual, before performance claims. Include plots for every calibrated variable and the all-state agreement summary in the main talk or explicit backup slides; a report link alone is insufficient. The outline must identify the slide IDs, variables, figure assets and evidence. Keep representative plots and their residuals together on the main slide; move only additional plots and detailed methods to backup.

Keep the existing talk's order and emphasis when revising it. For a new campaign, the established structure is a useful starting point:

1. Motivation, the architectural choice and investigation questions.
2. Original ATLAS software, selected sample, resolved configuration and saved Path A reference.
3. CorrectionLib's contract and a small example, followed by real file-to-histogram use.
4. Exported payloads and the acceptance contract against the original tool.
5. A conversion-to-endpoints map, shared columnar/framework implementation, first comparison and a visual agreement check.
6. Optimization constraints and major mechanisms, with a small before/after example, checkpoint timing and validation scope for each.
7. Final comparison across the requested paths.
8. Standalone wheel/script demonstration from original input to saved output.
9. Measured lessons, limitations and next steps.

Give each major optimization its own slide; put smaller changes, the complete timing history, controls, additional comparison plots and detailed methods in backup. Include no-gain results when they explain the conclusion. Keep one point per main slide, with supporting detail in notes or backup.

## Evidence and explanations

Show what CorrectionLib actually executes, including custom nodes and their library calls. A call-path diagram should start at the public entry point and distinguish calls from returned data. Report any application-owned physics as an incomplete migration boundary; label historical architectures accurately. Do not infer per-payload JIT compilation or SIMD from a C++ evaluator or array interface.

Use readable code from the executed analysis: loading, input mapping, variations and histogram filling. Label actual excerpts versus simplified illustrations, identify omissions and avoid invented APIs or validation-only clutter. State inputs/units and whether a result is a value, offset or scale.

Link each result to its checkpoint and evidence; put full immutable source links and method details in notes or the report. State what was checked with explicit counts and scopes. Tolerance agreement is not bitwise identity, and a histogram overlay is not per-object acceptance. Explain residual signs and absolute/relative tolerances with units. Compare speedups only at matched scopes and distinguish kernel gains from complete-workflow gains.

## Outline handoff and deck checks

For an outline handed to another chat, include audience/duration, each slide's point and suggested content, code examples, notes, exact evidence links and unresolved measurements. Verify local links and runnable teaching snippets. If only an outline was requested, deliver that scope.

Build from the settled outline and inspect the current deck before revising it. Preserve manual edits and keep a recoverable copy before structural changes. Retain editable source; use native lists with hanging indents and native equations, readable code, authentic proportional logos and verified affiliation. Use readable text links for evidence.

Inspect rendered slides and saved list/equation structure, including wrapping, captions, code and links. Disclose target-application rendering or equation limitations. Keep exact commands and full evidence in the report/backup.

Publication requires explicit authorization. Preserve cited implementation commits and the user's source/artifact scope. Distinguish local evidence from audience-accessible links; never fabricate a public replacement.
