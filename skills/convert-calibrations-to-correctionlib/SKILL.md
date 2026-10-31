---
name: convert-calibrations-to-correctionlib
description: Convert ATLAS physics calibrations and systematic uncertainties into composable CorrectionLib payloads, with reusable numerical primitives, framework and columnar adapters, validation against the original implementation, and an evidence-linked technical talk. Use for a substantive migration of correction prescriptions, not merely applying or presenting an existing payload.
---

# Convert calibrations to CorrectionLib

Use this skill to convert ATLAS physics calibrations and systematic uncertainties into composable CorrectionLib payloads, with reusable numerical primitives, framework and columnar adapters. Once the conversion is complete, validate against the original implementation. Then use this skill to optimize the columnar implementation, measure performance, and prepare an evidence-linked technical talk.

CorrectionLib must be the runtime entry point for all calibration, systematic, selection and weight calculations in paths B and C. Implement these calculations using existing nodes or new nodes registered through the selected CorrectionLib extension mechanism. Custom C++ and numerical libraries may execute behind those nodes. Adapters may read inputs, arrange arrays and translate outputs/statuses; they must not independently implement or orchestrate the physics prescription. Performance optimizations must preserve this boundary.

Follow these steps:

1. Measure current performance in AnalysisBase (AB) measure the I/O time and the time to run full calibration, and calibration and full systematics. The results of this provide the baseline timing and reference values for all subsequent steps.
   - Build AB configuration files to run and time the requested calibration. Produce a histogram of the calibrated values (whatever the calibration affects).
   - Build an AB configuration that just reads and times the PHYSLITE values that are used as input to the calibration. This will measure pure I/O time.
   - Build an AB configuration that just runs and times the calibration and fills a histogram with the calibrated values.
   - Build an AB configuraiton that runs calibration and full systematics and writes out each value (nominal and calibrated). These are the reference values for testing later modifications.
   - This is called Path A - the original implementation.
1. Convert the calibration and systematic uncertainties into CorrectionLib payloads, with reusable numerical primitives, framework and columnar adapters.
   - Use the [`develop` branch of correctionlib](https://github.com/gordonwatts/correctionlib/tree/develop). You will have to build this - there are no builds of this in pypi.
   - AB is arranged around tools to perform various functions. Initial conversion should match the tool boundaries. Create a node in CorrectionLib for each tool and copy over the configuration required into the correction lib config.
   - Use the C++ calling conventions to replace the original AB implementation - _but_ the configuration of AB must remain the same. In short, it should be backwards compatible.
   - Build a columnar implementation that uses the CorrectionLib payloads and new nodes to accomplish the same tasks as the original AB implementation. This should be based on `uproot` to read in the PHYSLITE file.
   - The reimplementation in AB is called Path B. The columnar implementation in python is called Path C.
   - Derive the same timing values as for the Path A for B and C.
1. Optimize the columnar implementation
   - The outputs must remain the same (random number generators can violate this rule).
   - The goal is the best possible performance for the columnar implementation. Aim for x10 speedup at least, but keep going if there are any optimizations you can see further.
   - There are two features in CorrectionLib that can really help here - the ability to return multiple results from a single node, and the ability to process arrays. Make as many other optimizations first before you make these ones. This is because these features are not part of the released correctionlib, and understanding how big a difference they make is important.
   - While you started by keeping tool boundaries as they were in the AB implementation, during optimization you do not have to keep those boundaries. You can combine nodes, or split them up, as long as the outputs remain the same.
1. Build and test and time a standalone package that can be used outside of the ATLAS software environment.
   - The main script that opens the PHYSLITE file and implements the calibration should be a PEP 723 script which installs a wheel that contains all the correction code and config payloads. It should read in a file given as an argument with uproot, call CorrectionLib to run calibration and full systematics, and then fill a `hist` (form the `hist` package), with one axis a category which is for each systematic, and the other axis is the calibrated value. The histogram should be written out to a file.
   - Measure timings for this one step.
1. Write a report and a talk.
   - Use the `deckcraft` MCP to write the talk. The report should be written as `report.md`
   - Both need to be written for an audience that knows about ATLAS, but does not know much about CorrectionLib or how it works. And may not know all the tricks that are pulled to optimize the code.
   - Write a slide for each major optimization that was done, and place a slide for the minor optimizations in the end of the talk in backup.

## Working Environment

Use the `wsl2` distro called `atlas_al9` to run all three paths. You can use `setupATLAS` and then setup the most recent AnalysisBase release to run the original implementation. You will have to build the `develop` branch of CorrectionLib to run the reimplementation and columnar implementation. Do all your work in a directory called `~/atlas-cp-speed/<xxx>`, where `<xxx>` is the name of the calibration you are working on. If asked to continue some work, make sure to re-use the same directory.

You can use `git` to manage your work, but do not push anything to the ATLAS gitlab repository. This is especially important when you start to optimize the columnar implementation. For each change you make please make a new commit, and keep the commit message descriptive of what you changed. This will help you when you are writing your talk.

Keep a log for yourself called `log.txt` in the directory on `wsl2`. This log is primarily for you to keep track of what you have done, but it will also be useful when you are writing your talk. You can also use this log to keep track of any issues you run into, and how you solved them. Remember that you may be asked to continue work in a second prompt and not always have the full context - make sure to take this into account when writing the log. When writing the talk, be sure to read [the scientific talk writing guide](references/talk-writing.md).

You will discover various things that obviously need optimizing as you are reading the code. Just track them in your log until you can get back to them. You can also profile the code to best understand where extra time is being spent. You can use `cProfile` to profile the code, along with other tools that are available in the environment.

Feel free to spawn sub-agents when needed - especially if it will help keep your work organized and your context free. And if it will speed things up because you can work in parallel. But make sure to keep your log up to date with what you are doing, and what the sub-agent is doing. You will be asked to write a talk at the end of this skill, and it will be much easier if you have a good log of what you did.

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

Maintain a responsibility map: exported constants, existing CorrectionLib nodes, custom nodes and their C++ implementations, numerical libraries called by nodes, and framework/array plumbing. Trace calls separately from returned-data dependencies, including constructors and payload loading. Show how every physics stage is reached through CorrectionLib, including inference and RNG. Distinguish historical implementations with physics outside CorrectionLib from the required architecture for paths B and C. This map is part of the implementation explanation and final talk, not just a list of dependencies.

In the implementation report, identify the specific formulas, physics choices and stage ordering implemented by custom nodes, alongside those expressed using existing nodes. Explain each choice, citing measured speed or an evidenced capability limitation where applicable; distinguish provisional choices from measured conclusions. C++ physics must execute behind CorrectionLib nodes, and must be reported accurately rather than hidden under the term “adapter.” Record node implementations, payload configuration and subsequent changes in `<task> - log.txt`.

## Design a declarative calculation

Read [graph design and interfaces](references/graph-design.md) when designing the payload/compiler or reviewing evaluator capabilities.

Choose between existing CorrectionLib nodes and custom nodes using faithful numerical semantics and comparable runtime measurements, retaining reference validation for each candidate. Both paths B and C must execute the same node implementations and payload configuration. Keep calibration-specific constants and configuration in the payload; identify which formulas, systematic branches, stage ordering, validity rules and precision decisions are expressed there and which are implemented inside custom nodes. Use existing schema nodes where they represent the calculation faithfully. Author readable named expressions and lower them to the distribution format; do not make nested JSON or scratch-register manipulation the primary human interface.

When existing nodes cannot represent a required calculation faithfully, implement and register a new node through the selected CorrectionLib extension mechanism. Inspect that mechanism in the pinned source version rather than assuming a released version supports it. Record the limitation, node contract, configuration, dependencies and build/loading requirements in the report and task log. Validate node behavior, payload loading and execution through CorrectionLib in both paths B and C. An unimplemented required node remains an incomplete conversion; a separate calculation backend is not a completed substitute.

## Compile and package numerical data

Build conversion as a repeatable offline step using the original resolver/configuration semantics. Export all consumed constants, axes, contents, errors, function parameters and model dependencies. Preserve interpolation and extrapolation, not just bin contents.

Map every generated correction to its source setting/object and checksum. Record units, numerical types, flow behavior, validity and evaluator requirements. Reject unsupported active settings or required object types; unused source objects may be inventoried without conversion. Test completeness by resolving every active recipe dependency.

Keep source calibration files as conversion inputs. Package everything required at runtime, including explicitly retained inference models. Verify runtime dependency claims with an isolated resolution environment; claim a file-open audit only if one was actually performed.

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

When the work reveals a possible evaluator defect or generally useful optimization, distinguish it from application-specific changes. Check the current upstream source and existing issues/PRs before recommending a report or patch. Prepare a minimal independent reproduction, affected revision, expected and observed behavior, boundary regression tests and relevant performance tradeoffs; distinguish measured results from hypotheses. A local vendor patch may already be upstream. Do not submit externally without authorization.

Follow the repository's artifact and local-commit rules. For an authorized source publication, read the publication guidance in [talk writing](references/talk-writing.md): preserve implementation history so performance claims can link to their actual commits, and distinguish source distribution size from runtime payload size. Do not push or submit upstream changes unless requested. This skill does not require delegation or grant authorization for external actions.

When extending this skill from another chat, read [contribution guidance](references/contributing.md). Add verified lessons to the relevant reference rather than appending a transcript or turning a sample-specific workaround into a universal rule.
