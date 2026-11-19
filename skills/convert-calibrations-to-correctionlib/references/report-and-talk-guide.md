# Report and talk guide

Use this guide when preparing or revising the migration's report, outline or deck. A full campaign delivers `report.md` and a talk. Follow the [scope set at intake](efficient-campaigns.md#match-completion-evidence-to-the-assignment) for narrower work. Write for ATLAS colleagues who may be new to CorrectionLib or this calibration.

For every talk produced with this workflow, read and apply the [scientific-talks skill](https://github.com/gordonwatts/scientific-talk-marketplace/blob/main/plugins/scientific-talks/skills/scientific-talks/SKILL.md) as the foundation for outlining, evidence, style, deck construction and revision. Use its Gordon speaker profiles for talks written for Gordon: content guidance when outlining and visual guidance when building or editing slides. Select the talk-type profile from the brief; do not assume every talk is a colloquium. If the skill is unavailable locally, read its linked source and relevant references. This guide supplements it with calibration-specific content and evidence requirements. Preserve explicit user choices, including an existing DeckCraft requirement; otherwise follow the scientific-talks authoring-tool defaults.

Continue the selected outline and existing deck; preserve their general structure, filenames and speaker edits. Follow repository instructions for document locations and commits. Use the task log and accepted evidence; preparing documentation alone does not justify rerunning benchmarks.

## Write for a reader new to the work

The report must stand on its own for an ATLAS colleague new to the work. The reader must be able to understand the motivation, original workflow, final architecture, why it changed, validation, measurements and limitations from the report itself. Include the essential setup, definitions and interpretation in the main text so the reader can follow the argument without the chat, task log, previous drafts or linked evidence. Apply the same standard to the talk.

- Explain the systematic-uncertainty configuration in the testing setup, using the actual executed configuration as evidence: identify the enabled scheme, detailed versus reduced/grouped uncertainty breakdown, nominal and variation counts, and disabled corrections where relevant. Summarize these choices in readable prose and link the configuration; a configuration filename or “all systematics” alone does not explain what was run. Distinguish explicit settings from release defaults, verifying defaults before claiming them.
- If a coordinating custom node evaluates other CorrectionLib corrections, explain that call chain directly: where the node is registered, which calculations it calls, and which sequencing decisions its C++ implementation owns. Distinguish this extension from standard table/formula nodes and link the actual implementation.
- Define A/B/C, unfamiliar terms, tool/node roles and measurement conventions before using them. Explain what each mechanism does and why it matters; commit IDs, local filenames and labels such as “restored baseline” cannot carry that explanation on their own.
- Describe validation in particle-physics terms: name the quantities compared (for example calibrated momentum, selections and scale factors), the original and converted paths, the number of input events and objects, the nominal/systematic coverage, and the observed agreement. Lead with a statement such as “We compared the calibrated values from the CP tools in the ATLAS release (A) and the columnar implementation (C) for N events containing M muons, for nominal calibration and all S systematic states. All compared values were numerically identical,” only when that exact scope and equality are supported. If agreement is within tolerances, give the tolerances and relevant differences instead; disclose exceptional/nonfinite handling and exclusions as needed. Avoid leading with “the rebuilt baseline passes X framework/observer comparisons.” Aggregate scalar-check counts are software bookkeeping, not numbers of independent events or independent physics tests: keep them in supporting evidence, explain what they count when used, and do not sum overlapping checks into a headline claim. Apply this guidance to report prose, slide content and spoken notes.
- Describe the final executed implementation and accepted scope directly. When revising a conclusion, rewrite the affected paragraph/section and reconcile related tables, captions and conclusions. Do not append “now updated,” “as requested,” “the previous answer,” or similar editing commentary that requires conversation history. Record editorial decisions, requested changes and progress in the task log instead.
- Retain investigation history when it explains a technical conclusion: for an optimization or rejected trial, introduce the starting behavior, hypothesis, actual change, validation, matched result and consequence. Explain what was restored and why if that matters. This causal history and the complete timing ledger remain evidence; they must not be confused with a document revision diary.
- State the interpretation of results, failure modes and accepted exclusions in the narrative itself. A link supplies supporting detail, not missing context. Use descriptive evidence labels that say what a source demonstrates; keep exhaustive commands, hashes and provenance in linked records or backup without making them prerequisites for understanding the main claim.
- Apply the same standard to the talk's audience content, spoken explanation, notes and backup. Introduce the necessary context as the argument develops; do not assume listeners know internal checkpoint names or have read the report. A revision should leave one consistent explanation of the final result, not contradictory old and new slide claims.

Before delivering a new or revised report, read the current draft straight through from its opening to its final section as an ATLAS colleague encountering the work for the first time. Check that each section builds on context the reader already has, that terms are defined before use, and that the evidence leads to the stated conclusions and limitations. Integrate revisions into the sections they affect. Remove superseded claims, update notes and draft history that do not help explain the work. Keep the investigation history that supports technical decisions. Reconcile the text, tables, figures and cross-references, then check the talk and outline against the report using the existing evidence.

## Plain language for ATLAS readers

Use familiar ATLAS terminology without explaining routine AnalysisBase behavior. Identify the baseline as the CP tools in the named ATLAS release. Introduce study labels and CorrectionLib terms before relying on them; explain a node as a component of a calculation and say which nodes this work adds. Familiar terms such as Medium ID need no invented replacement.

- Open with a short statement of the physics coverage and a compact results table. Label the paths and timed endpoints in the table. Put material timing caveats and acceptance status immediately below it. When variability affects a claim, define the spread metric and identify the screen it was assessed against. Put detailed comparisons and optimization decisions in their own sections.
- Prefer concrete headings such as “Testing setup,” “Implementation” and “Validation.” Use short sentences with explicit subjects. Replace vague “reference,” “configured choices” or “defined-result domain” with the tools, settings or limitation actually meant.
- Describe configuration choices in prose, especially systematic schemes and detailed versus grouped uncertainty breakdowns. Keep physics tasks distinct from their software representation: calibration constants may be stored in ROOT histograms in the CP tools and CorrectionLib tables in the conversion.
- Separate test criteria from observed results. Explain tolerances plainly, link their numerical definitions, then state the observed agreement directly. Do not add defensive assurances that convey no additional result, such as unchanged tolerances when every compared value already matched exactly.
- Keep routine precision/identifier checks, separate observer organization, build restoration details and workspace history in supporting evidence unless they explain a material finding. Preserve limitations and evidence, but do not make readers follow the investigation diary. Exceptional malformed-input behavior belongs in a concise limitations section when outside the study's purpose.

When proofreading with the user, quote the saved text verbatim when asked, including tables rather than silently skipping them. Apply approved edits to the document, and collect recurring principles for this guide. Preserve the user's approved wording while revising subsequent sections.

## Independent report review

After writing the report and before delivery, give a fresh subagent the report and the applicable requirements from this guide. In Codex, call `spawn_agent` with `model: "gpt-6.2-sol"`, `reasoning_effort: "low"`, and `fork_turns: "none"`. Provide the report path, the scope agreed at intake, a concise description of the required report contents, and paths to the guide and relevant evidence. Ask it to read the full report as an ATLAS colleague new to the work.

Ask the reviewer to check completeness, clarity, internal consistency, and whether the evidence supports the main conclusions. For each finding, ask for a section or line, the relevant requirement or evidence, and a suggested correction. Have the reviewer inspect files and report findings. Its assignment is read-only and uses the existing evidence.

The lead waits for the review, verifies the findings, fixes confirmed issues within scope, and checks the affected sections and artifacts before delivery. Record material limitations or unresolved findings in the handoff.

## Report

Write a high-level account of what the conversion achieved, how it got there, and how each path was configured. Cover:

1. **Question and reference:** investigation goals, supported configuration, original release/source revision, input identity, event/object counts and Path A baseline.
2. **Implementation:** conversion and runtime call path, shared B/C integration and responsibility map. Identify existing/custom nodes, payload-owned configuration, C++ physics, numerical-library dependencies, node contracts and version/build/loading requirements. Explain implementation choices and distinguish historical or proposed architectures from executed code.
3. **Correctness:** the original-tool acceptance contract, quantities, units, tolerances, systematic states, boundary/status checks and actual results. Keep source bugs preserved by the conversion, unresolved failures, missing observables and accepted limitations visible.
4. **Performance:** original A, first validated B/C and the delivered paths. Summarize measured improvements and identify which stages and outputs the timings cover. Give the CPU policy, repetitions and spread for headline results, along with any failed screen or explicitly approved exception. Distinguish early measurements from later timed comparisons of the same build.
5. **Standalone use:** show how to invoke the delivered C script and include it in full. State the tested platform and ABI, input and state coverage, output, wheel and bundle sizes, and runtime dependencies. Link dependency-closure and loaded-library evidence.
6. **Conclusions and evidence:** state measured benefits, limitations and remaining opportunities. Link the frozen configuration and provenance, build and rerun commands, acceptance and timing records, and optimization register. Keep routine smoke tests, profiling tables, and operational reproduction instructions in these supporting records. Mention a smoke test in the report only when its outcome affects a claim or limitation.

Keep numerical, architectural and performance evidence distinct. Use [validation and timing](validation-and-timing.md) for acceptance, hardware, benchmark and payload-size methods, and [portable demo packaging](portable-demo.md) for the standalone procedure.

For a full A/B/C comparison, use the [opening timing table template](#opening-timing-table-template). State the physics-agreement scope. Report the matched B/A framework ratios and budget outcomes in the performance section, with linked validation and timings for the final loaded provider, payload and stream. Explain any over-budget B result and the bounded B-only work attempted. Give the first-to-final C comparison separately from A-to-C. See the [benchmark endpoints and speed goal](validation-and-timing.md#measure-the-physlite-to-histogram-workflow), [framework budget](validation-and-timing.md#apply-the-path-b-framework-budget) and [architectural exploration and timing](validation-and-timing.md#explore-and-benchmark-architectural-refactors).

When the requested work intentionally changes the random stream or another numerical prescription, distinguish the faithful-conversion checkpoint from the changed implementation throughout the introduction, validation, timing tables and figures. State which implementation is delivered, which comparisons establish exact original agreement, which values or selections change, and what the new checks actually establish. Separate deterministic replay and unchanged-formula checks from distribution/systematic-response sanity and physics approval. Identify the final implementation behind each profile and timing. Attribute engineering gains using matched same-stream comparisons and label any measured packaging or stream effect separately. Describe a comparison that includes several changes as a combined result. If dependencies are removed, report measured package/runtime closure and footprint separately from any resolved workflow-speed gain. The standalone package must be ROOT-free as specified in the [portable guide](portable-demo.md#deliver-a-root-free-python-package); a conversion with no RNG need not introduce one.

One possible outline for the major sections follows. Adapt the headings and grouping to the campaign while following the closing section requirements below.

1. Physics question, results table and caveats
2. From the original A workflow to the first C and the delivered C
3. Physics agreement and its scope
4. Path A configuration, Path B with the same configuration, and delivered Path C
5. Complete Path C script
6. Twenty optimization ideas to try next

## Opening timing table template

Use these columns for the headline comparison. Give A, B and C as total elapsed times through the same output endpoint, with one row per reported workload and scope. Use the same matched timing cohort across a row. Define what the totals include and state the repetition count and spread statistic below the table. Use the comment for timing-quality flags and any separate paired recheck. Mark missing measurements explicitly.

| Workload | A · Stock total (s) | B · Framework replacement total (s) | C · Standalone total (s) | A/C | Comment |
| --- | ---: | ---: | ---: | ---: | --- |
| Scope and workload | Mean ± spread | Mean ± spread | Mean ± spread | Speedup | Qualification or recheck, if relevant |

The histogram time is included in the workflow total when the benchmark endpoint is a saved histogram. A separate histogram timing column is optional in supporting evidence when it explains a result. Use framework-only times for the B/A budget.

## Completed optimization results in the main report

Give the adopted optimizations their own section and link to it from the opening. For each delivered optimization, explain what changed, which paths or collections use it, and what the evidence showed. Describe the original and revised work in terms of the data and calculations a reader already knows. For code changes, give the repository or checkout, exact Git revisions and relevant file or entry point. Label an introducing revision, a measured build receipt and a shipped revision according to what each identifies. Link the full trial register for provenance and the ranked future experiments for follow-up.

Explain how the first validated C differed from A before describing later tuning. State the actual timing stage, including whether a number comes from the first measurement or from a later rerun of that baseline. Identify work that the conversion introduced, removed or reused, such as intermediate file I/O, data preparation, or repeated evaluations across systematic states. Link the caller, node source or call counts when they substantiate the explanation. Attribute a time saving to a specific mechanism only when a comparison isolates it.

Use a short two-column table for retained C speed improvements near the performance story. Describe the code change in the first column, including which calculation, lookup, allocation or data movement it avoids. In the second, show before and after times and time saved on separate lines or in an equally readable format. Use short, unambiguous commit IDs and link the full revision, matched timings and acceptance evidence. State the measurement scope and show spread in the linked results or alongside a material caveat.

Say what was actually timed in plain language. For example, “For each change, we timed the unchanged build and the build with that change in the same session.” If rows use different starting builds or sessions, use the separately measured first-to-final comparison for the combined gain. Do not add savings or multiply gains across independent series.

Discuss portability and package changes separately from measured speed improvements. Keep passed-over trials in the linked register with the attempted change, numerical checks, timing, decision and restoration evidence. Distinguish correctness failures, regressions and inconclusive trials. Bring a passed-over trial into the report when it explains a conclusion. Discuss B's budget separately and include its detailed optimization history when requested.

## Inspectable reference setup and timing

Put the executed setup and launch example under a visible **Code and how to run it** heading in the report. Place a **Complete Path C script** heading immediately below the Path C instructions, link to it from the opening, and put this section near the end if the script is long. Populate one fenced code block from the actual distribution file. After the final edit, extract the block with LF line endings and compare it byte for byte with the shipped script. Verify the fence closes and the navigation link reaches it. Ask the independent report reviewer to repeat this check.

- **Path A:** show the analysis configuration used in the measured original release, including the relevant YAML or equivalent, runner settings and launch overrides. Include enough detail for an expert to verify the input, working points, units, enabled systematics and saved outputs. Explain what differs between nominal and full-systematics runs. Link the frozen setup script, source and exact commands.
- **Path B:** show how the replacement is enabled on top of A's configuration. Give the actual adapter or runner settings and which provider and payload they select. Identify any change to the analysis configuration or output.
- **Path C:** show the invocation for the delivered package and include the complete tested standalone Python script inline, including its dependency declarations. Orient the reader to the uproot read, explicit CorrectionLib evaluation and histogram output. Link the separately distributed script as well.

Explain where the headline clocks start and stop, including any intermediate ntuple, reread and histogram step. Link all repetitions and exact commands. Keep detailed timing splits, full environment setup, rebuild instructions, and rerun recipes in the supporting records or talk backup.

Name controls by what they do, such as **read without corrections**. Explain historical command labels such as `stored` only where needed. A control including setup, input preparation or histogram output is not a pure I/O measurement. If a separate complete-input reader differs from the no-correction framework control, state the input, driver and output differences and why their timings are not interchangeable. Explain why prepared column counts can differ from ROOT branch counts (linked-object gathering, vector components, availability flags).

## Units and standalone-package descriptions

When introducing the standalone package, give the compressed download size of its wheel, the size of any separately supplied matching CorrectionLib wheel and the combined wheel download size. Give the compressed bundle size if one is delivered. State whether the totals include public dependencies, and report installation footprint separately if measured. Use readable decimal MB and KB per event with enough precision to distinguish the artifacts. Define the unit basis and link exact bytes and hashes. Identify the tested platform, required downloads and separately supplied analysis input.

For every shipped wheel, report compressed and uncompressed member sizes by major component, regardless of wheel size. Measure the delivered archive and account for its entire byte count, including ZIP overhead. Use categories that reflect the contents, such as calibration data, providers, evaluator and bindings, bundled runtimes, and other files. Label the uncompressed member total and any measured installation footprint separately.

## Future optimization ideas at the end of the report

End the full report with **20 ranked, distinct next experiments**, drawn from the [living idea register and other calibrations' work](efficient-campaigns.md#collect-and-transfer-optimization-ideas). A collapsible table at the end keeps the main discussion easy to scan. Consider ambitious architectural experiments and ideas that would need a rule change. Each needs an observed cost, source finding, cross-calibration precedent or credible technical mechanism, and a way to test it. Clearly label untried proposals and speculative benefits. If fewer than 20 grounded ideas are available, report the shortfall. Keep adopted changes and rejected trials with their results. Revisit a rejected idea only when new evidence supports a different experiment.

Sort the table into **compatible with current constraints**, **requiring a stated rule change**, and **major architectural experiments**, with an overall rank for every row and ranking within each group. State whether each architectural experiment is itself constraint-compatible; scope and rule compliance are different questions. Use these columns (combine concise fields if needed for readability): **overall rank / group; idea and affected A/B/C path; why it might help and supporting evidence; constraints or architectural scope; first experiment and prior attempts/deferral**. For each item explain, in language an ATLAS reader can understand:

- What would change and why it might reduce measured reading, preparation, correction or output cost; identify the affected A/B/C path.
- The evidence or source of the idea, including another calibration's report or implementation when relevant. Distinguish observed cost from an unmeasured speedup prediction.
- The first useful experiment, prerequisites, numerical checks and approximate implementation scale. Name the exact conflicting rule if one would have to change.
- What has already been tried and why this remains a next step. For compatible untried ideas, explain the deferral rather than implying that all feasible work was exhausted.

Do not list changing away from PHYSLITE input or generic multithreading as opportunities. PHYSLITE is an external input constraint. The present optimization target remains one analysis thread on one CPU, with future parallelism expected above the algorithms, across events/chunks/jobs. A technically motivated future experiment comparing internal and top-level parallelism may be listed if it explains a specific potential advantage and trade-off; it does not change the current benchmark policy. Keep larger but rule-compatible redesigns visible as future experiments even when they were beyond the completed campaign's scope.

Link the full register, including attempted ideas and outcomes, so the next campaign can continue without rediscovering them. During the investigation, keep that register current and test the promising, compatible in-scope ideas under the existing bounded-study rules. Place bibliography and artifact appendices before this final table, and put source and evidence links in the rows.

## Required ROOT input footprint in the report

Report how much original event data the configured calibration reads, alongside its I/O timing:

- **Distinct ROOT leaves loaded:** inventory fully qualified tree/branch/leaf names actually read, including linked tracks, vertices, event metadata and count/offset dependencies. Count each leaf once across chunks and systematic states. Distinguish ROOT leaves from branches and expanded adapter fields; do not substitute the number of packed numerical inputs. State the branch-to-leaf mapping where a branch contains multiple leaves.
- **Average loaded data in KB/event:** report total bytes for those inputs divided by the number of input events read, including events with zero selected objects, then divided by 1,000. Label KiB separately if using 1,024. State sample/file identity, tree, entry range, event count, nominal/full-state scope and reader configuration. Do not multiply by the number of systematic states when raw data is read once and reused.
- **Byte basis:** give compressed ROOT basket bytes and uncompressed/decoded bytes separately where measurable, with the latter's representation defined (for example serialized uncompressed basket data or materialized arrays including offsets). State the counting method and treatment of metadata, basket boundaries, read-ahead and repeated reads. Basket metadata totals are estimates of the selected input footprint, not measured physical disk/network traffic; physical traffic depends on caching and read behavior. Do not use total file size/event or post-packing array size as an unlabelled input-read measurement. If a quantity cannot be measured, retain that limitation rather than inventing a value.

Separate inputs required by the active calibration/selection/weight prescription from extra fields loaded by transport, validation, output or diagnostics, and report the actual reader total. Label differences between paths A/B/C and between nominal/full modes instead of assuming identical leaf sets. Retain a machine-readable inventory with per-leaf or non-overlapping per-branch byte counts, totals and reproducible measurement commands; link it from the report. Reuse valid input metadata or perform a separate read diagnostic outside benchmark timing; this requirement alone does not justify rerunning numerical acceptance or the timing campaign.

## Required profiling study in the supporting evidence

For a full campaign, deliver a linked technical study answering **where most execution time is spent in the final accepted workflow**. Keep tables, commands and diagnostic detail in that study. In the main report, use profiling findings when they explain a design or measured result. Follow [profiling tool selection](profiling.md) for perf sampling and appropriate alternatives. Label the implementation behind each profile.

Report elapsed-time and CPU evidence together with their units and measurement scopes.

Define “sampled CPU,” “self/exclusive” and “inclusive” beside the first profiling table, following the [profiling terminology](profiling.md#terms-to-define-in-reports). Explain that self excludes callees, inclusive includes them, and neither describes the number of CPUs. Prefer the label “exclusive share of sampled CPU time” over unexplained “self sampled CPU.”

- An uninstrumented workflow and tool timing breakdown with startup/loading, input/preparation, correction evaluation, histogram/output and unassigned time. Include process CPU time from the matched workload where available. Define timer boundaries, denominator and any overlap.
- A fine-grained cost table that names the routines, call paths or operations within tools where execution spends its time. Use call stacks to connect costs such as branch decoding, object or argument packing, allocation and copies, dispatch, interpolation, inference, and output filling to their executing callers and workflow stages where supported. Give measured self CPU shares or counts and useful inclusive costs, plus the event/sample basis. Include supported cycles or instruction counts where they explain costs and state which metrics were unavailable. Distinguish known omitted functions, unresolved symbols and samples with unknown stage attribution.

When a shortened hotspot table has an “all other functions” row, break it down in the study by executing library or supported functional category, with examples of the work in each group. Retain the full per-symbol evidence and report unresolved-symbol coverage separately. Keep the same whole-profile denominator, use non-overlapping self costs, and explain rounding differences when reconciling the groups with the remainder.

Explain any remaining analysis-stage attribution gap: knowing the executing library/function does not establish whether shared interpreter, allocation or copying routines ran during input, evaluation or output. State whether sample-by-sample stage classification was performed and what the stacks can actually support. Present the separately measured elapsed-time phase breakdown alongside this explanation, including its own unassigned remainder; sampled CPU shares and elapsed phase shares are different measurements and must not be combined.

State the input, state set, source/binary identity, tool version, exact command, CPU policy, sample count, lost samples, symbol/unwind quality and measured instrumentation overhead where available. For the full campaign's standalone runtime, the final native study must use the [60-second repeated-input protocol](profiling.md#collect-at-least-60-seconds-for-the-final-native-profile): report `-n N`, observed processing duration, unique inputs versus total visits, per-pass agreement and cache/output scope. State profiler and calibration CPU placement separately. Link raw profiles or their retained location, reproducible report commands and machine-readable summaries. Record the output agreement check for the profiled run. Clearly separate sampled CPU percentages from elapsed-time fractions; never convert them into exact seconds or predicted speedups using a different run.

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

Inspect rendered slides and saved list/equation structure, including wrapping, captions, code and links. Disclose target-application rendering or equation limitations. Keep exact commands and full evidence in the linked records or talk backup.

Publication requires explicit authorization. Preserve cited implementation commits and the user's source/artifact scope. Distinguish local evidence from audience-accessible links; never fabricate a public replacement.
