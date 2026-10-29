---
name: convert-calibrations-to-correctionlib
description: Convert ATLAS physics calibrations and systematic uncertainties into composable CorrectionLib payloads, with reusable numerical primitives, framework and columnar adapters, validation against the original implementation, and an evidence-linked technical talk. Use for a substantive migration of correction prescriptions, not merely applying or presenting an existing payload.
---

# Convert calibrations to CorrectionLib

Preserve the original implementation as the numerical authority. Produce a reproducible converter, traceable payloads, shared calculation code, thin adapters and evidence of agreement. Table migrations and hybrid payload-plus-C++ implementations are acceptable completed conversions; choose where calculations live using measured runtime performance while preserving numerical correctness. A complete declarative rewrite is required only when explicitly requested; in that case the correction definition must own the physics prescription, and a table-lookup substitution alone is insufficient.

This skill supports ATLAS calibration migrations, including work from scratch. It is not a universal converter or a claim that every ATLAS calibration family has been tested. Distinguish established behavior, hypotheses and newly verified results. Keep campaign-specific configurations and results in project reports.

## Start with the requested scope

Inspect repository instructions, existing implementations and previous campaigns before editing. Establish:

- The authoritative release/toolchain, source revision, input sample and its identity.
- Object collections, data/MC campaign, nominal stages, working points and requested systematics.
- The intended framework and columnar interfaces, and whether unchanged analysis configuration is required.
- Numerical tolerances, discrete outputs/status expectations and acceptance sample size.
- Which existing artifacts must remain reproducible.

Infer routine choices from source and configuration; ask only for consequential missing information. Never silently substitute a reduced uncertainty model for a requested full model. Enumerate the actual supported states, including pseudo-data and weight-only variations when applicable.

Read [ATLAS lessons](references/atlas-lessons.md) for the required A/B/C execution paths, common PHYSLITE input and source-specific numerical traps. A is unchanged release AnalysisBase; B is modified AnalysisBase using the shared correction backend; C is columnar execution using that same backend. All three read the selected original PHYSLITE file, and B and C are independently validated against A.

## Establish the reference before replacement

Run the untouched tools on a small sample, freeze the working configuration and capture resolved defaults. Record original object order and identities before thinning or sorting. Export intermediate stages, final outputs, selections, weights and statuses wherever the original interface exposes them.

Preserve the original implementation's behavior even when it appears to contain a bug. Add a clearly marked `BUG` entry to `<task> - log.txt` with the source version/location, triggering inputs, observed behavior, why it appears defective, and how the conversion preserves it. Distinguish suspected from confirmed defects and link supporting evidence from the report. Do not silently correct the behavior or change expected results/tolerances to accommodate a fix; an intentional correction requires an explicit user decision separate from the faithful conversion.

Trace the real loading chain: configuration properties, resolver search paths, merge precedence, defaults, referenced files and named objects. For every active stage, record inputs/units/types, calculation, constants, interpolation, boundaries, output and rounding. Inspect source; names such as "spline" are not evidence of the actual algorithm.

Separate nominal recalibration, systematic application and selection/weight evaluation. Determine which inputs refer to original versus recalibrated objects and which features change under each variation.

Trace the active caller through bindings to the evaluator; distinguish table conversion from migration of the complete recipe. Coexisting prototypes and unused wrappers are not evidence of the executed path. Explain where physics instructions live and what must be rebuilt or regenerated when they change.

Maintain a responsibility map: exported constants, CorrectionLib-executed formulas/compositions, handwritten physics, external inference/RNG primitives and framework/array plumbing. Record why a stage lives outside CorrectionLib and whether that was the original design or a later change. Trace calls separately from returned-data dependencies, including constructors and payload loading; “external to CorrectionLib” does not tell the reader who calls it. This map is part of the implementation explanation and final talk, not just a list of dependencies.

In the implementation report, identify the specific formulas, physics choices and stage ordering retained in C++, alongside those described by the payload. Explain each placement decision, citing measured speed or an evidenced capability limitation where applicable; distinguish provisional choices from measured conclusions. Keeping physics in the shared C++ calculation layer is allowed, but must be reported accurately rather than hidden under the term “adapter.” Record placement decisions and subsequent changes in `<task> - log.txt`.

## Design a declarative calculation

Read [graph design and interfaces](references/graph-design.md) when designing the payload/compiler or reviewing evaluator capabilities.

Choose the division between CorrectionLib tables/formulas and shared C++ physics using comparable runtime measurements, retaining reference validation for each candidate. More physics in the payload is not inherently a better result. For portions represented declaratively, put their component formulas, systematic branches, stage ordering, validity rules and precision decisions in the correction definition stored in the payload. Use existing schema nodes where they represent the calculation faithfully. Author readable named expressions and lower them to the distribution format; do not make nested JSON or scratch-register manipulation the primary human interface.

For this version of the workflow, do not add new CorrectionLib nodes or modify its schema/parser/evaluator to introduce missing operations. Authoring helpers that expand into existing supported nodes are allowed. When an operation cannot be represented faithfully by the available nodes, implement that part outside CorrectionLib in the shared calculation layer. If a new node would be useful, record it as an unimplemented future opportunity in the report and `<task> - log.txt`: explain the calculation it would support, the current limitation and its proposed generic behavior. Distinguish correctness limitations from measured performance reasons for using external code; do not present a proposed node as an available capability.

Whenever an unsupported CorrectionLib capability forces calculation outside standard CorrectionLib, add an explicit entry to the implementation report; the responsibility map alone is insufficient. Keep a chronological log in a text file named `<task> - log.txt`, replacing `<task>` with the task name, in the task's report/documentation location. Log each such limitation when encountered and append implementation decisions and validation outcomes as work proceeds; preserve earlier entries and link the log from the report. Record the affected stage and required semantics, the inspected CorrectionLib version and evidence for the limitation (for example, a missing node), why existing nodes or authoring helpers do not suffice, and the chosen implementation with its actual caller and physics ownership. Distinguish retained handwritten logic, a companion evaluator and a modification to CorrectionLib itself. Include added runtime dependencies/compatibility requirements, reference-validation evidence or outstanding checks, and the effect on the claimed declarative scope. Create the report entry when the limitation is encountered and update it after implementation and validation; an unresolved limitation must remain visible in the final report. A capability missing from the pinned version is not evidence that every CorrectionLib version lacks it.

State accurately whether the implementation uses standard CorrectionLib, a versioned extension, interpreted graphs or generated code. A compiled C++ evaluator is not itself a per-payload JIT compiler.

Choose execution granularity separately from authoring granularity. Small reusable definitions need not become small interpreted runtime calls; consider fusion or generated compiled code when measured execution costs justify it. Preserve a traceable executable prescription and numerical semantics across any lowering. A hybrid table-plus-C++ migration can be appropriate to the requested scope; identify the physics still owned by C++ rather than describing it as a complete declarative rewrite.

## Compile and package numerical data

Build conversion as a repeatable offline step using the original resolver/configuration semantics. Export all consumed constants, axes, contents, errors, function parameters and model dependencies. Preserve interpolation and extrapolation, not just bin contents.

Map every generated correction to its source setting/object and checksum. Record units, numerical types, flow behavior, validity and evaluator requirements. Reject unsupported active settings or required object types; unused source objects may be inventoried without conversion. Test completeness by resolving every active recipe dependency.

Keep source calibration files as conversion inputs. Package everything required at runtime, including explicitly retained inference models. Verify runtime dependency claims with an isolated resolution environment; claim a file-open audit only if one was actually performed.

## Integrate without duplicating physics

Expose the same evaluator to framework and columnar callers. Framework adapters bind original interfaces/properties, extract inputs and translate results/statuses. Do not load competing implementations of the same component in one process.

Columnar execution must read original inputs independently. Reconstruct event offsets, per-vertex indexing, original object order and matching features. Reference outputs are comparison targets, never physics inputs. An intermediate cache is acceptable only if produced from original inputs by the tested path, with identity/provenance checks.

Document remaining collection operations and external numerical primitives. Preserve random seeds/streams, rounding, coordinate conventions and event reductions. Use nominal as the starting point for each independent systematic, unless the source explicitly specifies otherwise.

Preserve established paths and benchmarks. Put a materially different implementation or validation campaign in a new directory. Rebuild only the environments intended to change.

## Demonstrate use outside the source environment

When portability or a standalone analysis is part of the requested scope, read [portable demo packaging](references/portable-demo.md). Use CPython 3.14 for the standalone package and demonstration test; do not substitute another Python minor version unless the user explicitly changes this requirement. Package the complete runtime closure for its stated Python ABI and platform, then test it in an isolated environment without the original release setup. Give the demonstration script direct access to the intended analysis input and run the full requested state set; keep frozen reference fixtures in separate acceptance tests. Record the exact install/run command, wheel and bundle sizes, output contents, validation coverage and environment limits. A local wheel beside a PEP 723 script can be resolved by `uv run` without publishing it.

## Validate and measure

Read [validation and timing](references/validation-and-timing.md) before defining acceptance tests or presenting performance claims.

Keep runtime execution and performance measurements on a single CPU with one execution thread unless the user explicitly requests parallelization. Do not introduce multiple processing workers or enable implicit numerical-library threading; configure thread/worker limits and CPU affinity as needed, and record the effective policy. Concentrate optimization on algorithms, data movement, allocation and avoiding repeated work, not parallel speedup. Historical multi-worker results are not permission to use multiple CPUs. Concurrency checks apply only to an explicitly requested parallel scope and do not change the single-CPU benchmark policy unless requested.

Validate source primitive behavior, boundary cases, stage outputs, full state coverage and the complete requested sample. Identify the first divergent stage and inputs. Do not relax tolerances after failures. If an authoritative output was not recorded, state that limitation rather than inferring exact agreement.

A conversion remains experimental and must not be declared complete until all required numerical acceptance checks pass. Unresolved required failures, including boundary-case failures, cannot be overridden by faster execution or agreement on an ordinary sample. Keep failures and missing checks visible in the implementation report and `<task> - log.txt`. Missing reference information is a limitation, never a pass; if it prevents a required check, completion requires an explicit user decision accepting that limitation, recorded with the resulting narrower acceptance claim. Do not silently waive checks or treat an accepted limitation as evidence of agreement.

After correctness, measure initialization, input/preparation, evaluation and output separately where practical. Label diagnostic workflows that include reference comparisons or repeat calculations. For a nominal-versus-full-systematics claim, use a paired input-to-output harness with the same inputs, selection, output conventions and CPU policy; rerun both modes rather than dividing an older nominal number into a new full-run number. Keep independent validation outside the timer when measuring analysis runtime. Match other timing scopes or explain the differences. Honor the requested repetition/warm-up policy and retain every measurement.

For an observed migration overhead, use the diagnostic procedure in that reference to separate fixed from sample-dependent cost, isolate replaced components and test specific causes. Keep kernel gains distinct from full-workflow gains; preserve physics validation when optimizing.

For an optimization campaign, keep one cumulative timing table from the initial baseline, including controls, no-gain experiments, reversions and CPU-policy changes. Change one mechanism at a time, preserve each accepted implementation/validation step, and check affected framework integration as well as the columnar path. A user requesting only a final framework timing does not require rerunning its entire historical sequence. Use a fresh unchanged control when attributing a small gain across sessions.

## Deliver and preserve

Deliver the frozen configuration, reproducible build/run/conversion steps, payload and provenance, evaluator/adapters, source-to-correction map, machine-readable comparisons and a short implementation explanation. Include supported configurations, actual validation coverage, remaining boundaries and benchmark scope.

At the end of a substantive migration, prepare an evidence-linked technical talk about the work unless the user has explicitly narrowed the deliverables. Read [talk writing](references/talk-writing.md) when outlining or building it. Ground the narrative in the original-tool baseline, the actual executed architecture, a readable real-code use case, numerical validation, measured performance, portability demonstration when performed, limitations and the next question. Draft the talk's argument and evidence map before committing to visual design; keep candidate visuals provisional until the narrative is settled. Continue any existing user-selected outline rather than starting a competing one; follow repository instructions for document locations. A talk does not justify rerunning benchmarks on its own.

When the work reveals a possible evaluator defect or generally useful optimization, distinguish it from application-specific changes. Check the current upstream source and existing issues/PRs before recommending a report or patch. Prepare a minimal independent reproduction, affected revision, expected and observed behavior, boundary regression tests and relevant performance tradeoffs; distinguish measured results from hypotheses. A local vendor patch may already be upstream. Do not submit externally without authorization.

Follow the repository's artifact and local-commit rules. For an authorized source publication, read the publication guidance in [talk writing](references/talk-writing.md): preserve implementation history so performance claims can link to their actual commits, and distinguish source distribution size from runtime payload size. Do not push or submit upstream changes unless requested. This skill does not require delegation or grant authorization for external actions.

When extending this skill from another chat, read [contribution guidance](references/contributing.md). Add verified lessons to the relevant reference rather than appending a transcript or turning a sample-specific workaround into a universal rule.
