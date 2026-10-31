# Report and talk guide

Use this guide when preparing or revising the migration's report, outline or deck. Write `report.md` and prepare the talk unless the user narrows the deliverables. Both address ATLAS colleagues who may not know CorrectionLib or the calibration and optimization details.

For every talk produced with this workflow, read and apply the [scientific-talks skill](https://github.com/gordonwatts/scientific-talk-marketplace/blob/main/plugins/scientific-talks/skills/scientific-talks/SKILL.md) as the foundation for outlining, evidence, style, deck construction and revision. Use its Gordon speaker profiles for talks written for Gordon: content guidance when outlining and visual guidance when building or editing slides. Select the talk-type profile from the brief; do not assume every talk is a colloquium. If the skill is unavailable locally, read its linked source and relevant references. This guide supplements it with calibration-specific content and evidence requirements. Preserve explicit user choices, including an existing DeckCraft requirement; otherwise follow the scientific-talks authoring-tool defaults.

Continue the selected outline and existing deck; preserve their general structure, filenames and speaker edits. Follow repository instructions for document locations and commits. Use the task log and accepted evidence; preparing documentation alone does not justify rerunning benchmarks.

## Report

Keep the report concise, linking detailed artifacts rather than reproducing them. Cover:

1. **Question and reference:** investigation goals, supported configuration, original release/source revision, input identity, event/object counts and Path A baseline.
2. **Implementation:** conversion and runtime call path, shared B/C integration and responsibility map. Identify existing/custom nodes, payload-owned configuration, C++ physics, numerical-library dependencies, node contracts and version/build/loading requirements. Explain implementation choices and distinguish historical or proposed architectures from executed code.
3. **Correctness:** the original-tool acceptance contract, quantities, units, tolerances, systematic states, boundary/status checks and actual results. Keep source bugs preserved by the conversion, unresolved failures, missing observables and accepted limitations visible.
4. **Performance:** initial and final comparisons for the requested paths, plus the cumulative optimization history. Identify each mechanism, implementation commit, validation scope and measured gain; retain controls, minor changes and unsuccessful/reverted experiments. Label timing boundaries, CPU policy, repetitions/spread and historical versus freshly repeated measurements.
5. **Standalone use:** the runnable input-to-output example, exact install/run command, environment/ABI, state coverage, output schema, wheel/bundle sizes and observed result. Separate portability from offline or cross-platform claims, and cold installation from warm execution.
6. **Reproduction and conclusions:** links to configuration, source revisions, payload provenance, build/conversion/run commands, raw acceptance/timing results and the task log. State measured benefits, costs, remaining limits and the next question.

Keep numerical, architectural and performance evidence distinct. Use [validation and timing](validation-and-timing.md) for acceptance, hardware, benchmark and payload-size methods, and [portable demo packaging](portable-demo.md) for the standalone procedure.

## Talk structure

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

Give each major optimization its own slide; put smaller changes, the complete timing history, controls, residual plots and detailed methods in backup. Include no-gain results when they explain the conclusion. Keep one point per main slide, with supporting detail in notes or backup.

## Evidence and explanations

Show what CorrectionLib actually executes, including custom nodes and their library calls. A call-path diagram should start at the public entry point and distinguish calls from returned data. Report any application-owned physics as an incomplete migration boundary; label historical architectures accurately. Do not infer per-payload JIT compilation or SIMD from a C++ evaluator or array interface.

Use readable code from the executed analysis: loading, input mapping, variations and histogram filling. Label actual excerpts versus simplified illustrations, identify omissions and avoid invented APIs or validation-only clutter. State inputs/units and whether a result is a value, offset or scale.

Link each result to its checkpoint and evidence; put full immutable source links and method details in notes or the report. State what was checked with explicit counts and scopes. Tolerance agreement is not bitwise identity, and a histogram overlay is not per-object acceptance. Explain residual signs and absolute/relative tolerances with units. Compare speedups only at matched scopes and distinguish kernel gains from complete-workflow gains.

## Outline handoff and deck checks

For an outline handed to another chat, include audience/duration, each slide's point and suggested content, code examples, notes, exact evidence links and unresolved measurements. Verify local links and runnable teaching snippets. If only an outline was requested, deliver that scope.

Build from the settled outline and inspect the current deck before revising it. Preserve manual edits and keep a recoverable copy before structural changes. Retain editable source; use native lists with hanging indents and native equations, readable code, authentic proportional logos and verified affiliation. Use readable text links for evidence.

Inspect rendered slides and saved list/equation structure, including wrapping, captions, code and links. Disclose target-application rendering or equation limitations. Keep exact commands and full evidence in the report/backup.

Publication requires explicit authorization. Preserve cited implementation commits and the user's source/artifact scope. Distinguish local evidence from audience-accessible links; never fabricate a public replacement.
