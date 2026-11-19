# Efficient campaigns and reusable infrastructure

Read this at campaign startup and before committing to another optimization experiment. Reduce repeated engineering and invalid experiments, not independent numerical acceptance. These procedures were distilled from an extended conversion campaign; they are not a measured promise of a particular reduction in engineering time.

## Match completion evidence to the assignment

Record the mode, deliverables and acceptance evidence at intake. Apply the stages that establish the requested result.

| Assignment | Completion evidence |
| --- | --- |
| Full conversion campaign | Independent Path A reference, validated B/C checkpoints, matched final benchmarks and profiles, isolated ROOT-free wheel and runnable demo, standalone report and talk, and the 20 ranked future ideas. |
| Continuation of an existing campaign | Identify the accepted checkpoint and requested deliverables. Rerun affected checks and measurements, and link earlier evidence that still satisfies the identity and scope checks below. |
| Scoped worker assignment | Deliver the assigned code or measurements, the requested checks, the resulting checkpoint and unresolved decisions to the named lead. Use the accepted baseline, prior evidence limits and result directory from the assignment. |
| Documentation-only revision | Verify the cited implementation and results, revise the requested material, and check the presentation and links. Use the existing evidence for unchanged numerical and performance claims. |

## Reuse inventory before implementation

Check the campaign's evidence and reuse boundaries first. Inspect eligible prior campaigns and classify each component as reusable unchanged, reusable after adaptation, or calibration-specific. Record its location, revision, interface, validation evidence and invalidation conditions. Do not build a general framework from scratch merely to satisfy this inventory.

| Component | Reusable part | What must be checked again |
| --- | --- | --- |
| Release/bootstrap and core build | Setup scripts, pinned core build, parser regression reproductions, plugin loading | Release/compiler/core ABI and whether a local patch is still necessary |
| Original observer | Event/object/state identity, output serialization, status/availability convention | Actual tool calls, active branches, units, missing observables and reference authority |
| Table exporter | Resolver tracing, source hashes, axes/contents/errors inventory, boundary probe machinery | Each source object's lookup/interpolation/flow semantics and active dependency closure |
| Native provider and bindings | Registration, loader, CMake and binding freshness checks | Numerical node contracts, signatures, dependencies and actual dispatch |
| Columnar facade | Chunk iteration, offsets, typed jagged results, histogram serialization | Collection/branch/link mapping and event identity; never transplant another object's physics |
| Benchmark runner | Fresh result directories, argv/environment capture, affinity/thread observation, warm-up policy, summaries | Timing scope, original references and changed runtime identities |
| Distribution | Wheel assembly, dependency relocation/inventory, isolated installation, PEP script wrapper | New provider/payload closure, ABI, notices and actual installed execution |
| Evidence/reporting | Comparison summaries, cumulative ledger, plot generators, link/hash checks | Source-to-result provenance and the current final checkpoint |

Reusable machinery may still be embedded in a previous campaign. Adapt a small proven helper with explicit inputs rather than copying its fixed paths, object names and state counts. Do not describe a proposed extraction as an already available generic library. Retain licensing/notice requirements when publishing extracted code or source copies.

## Collect and transfer optimization ideas

Keep a living register of ideas throughout the campaign, not a speculative list assembled only when writing the conclusion. At startup, find relevant work within the campaign's reuse boundaries. Read eligible reports, optimization notes, timing tables, rejected-trial decisions and implementation details. Revisit those sources before choosing the next experiment when they could explain a measured cost. For example, a jet study's buffer reuse, name-resolution or invariant-calculation strategy might help a muon calculation, even though its equations and data dependencies differ.

For each distinct idea retain a stable identifier, a plain-language mechanism, source/evidence links (including the originating calibration), the current cost it could reduce, applicability assumptions, constraint classification, expected effort or architectural impact, next test, status and outcome. Statuses should distinguish proposed, being tested, adopted, rejected and deferred; keep unsuccessful measurements and the reason for deferral. Label predicted benefit as a hypothesis unless measured on this calibration. Verify cache/state/input dependencies, numerical semantics, stage gates and RNG behavior afresh before transferring an implementation. Never copy another calibration's acceptance result as proof for this one.

Classify ideas on two separate questions: does the idea respect current rules, and how large is its implementation scope? Mark each as compatible with current constraints or requiring an explicitly named rule change, and separately flag major architectural work. A large interface redesign can respect every physics/runtime rule yet exceed the current campaign's scope. Keep that idea with a small discriminating experiment and its likely value, rather than discarding it or silently turning the campaign into a redesign. Ideas that violate a rule are future alternatives, not permission to relax it now.

Follow the [architecture-first sequence](validation-and-timing.md#explore-and-benchmark-architectural-refactors) and make a serious attempt at promising compatible, in-scope candidates while work is active. Use the full call graph, profiles and targeted measurements to decide what to try. Preserve one-change commits, full original-reference acceptance and matched timing. A register is not a substitute for trying a well-supported opportunity. It is also not an instruction to run 20 experiments, extend an inconclusive study indefinitely or resurrect a rejected candidate without new evidence. Record why a compatible idea remains untried (for example low expected benefit, missing evidence, architectural scope or an unresolved prerequisite). Finish with the [ranked report list](report-and-talk-guide.md#future-optimization-ideas-at-the-end-of-the-report).

Two current constraints must not become generic future-work recommendations: starting from the original PHYSLITE input is externally fixed, and calibration execution must remain single-threaded/single-CPU during this work. Do not recommend replacing PHYSLITE with prepared input or simply adding workers. Future production parallelism is expected at the event/chunk or job level. A specific idea comparing internal algorithm parallelism with that top-level strategy is allowed in the register only if it identifies work that might benefit, explains a possible advantage and overhead/oversubscription/RNG trade-offs, and proposes a separate controlled comparison. It remains future work, not authorization to change current single-thread benchmarks.

## Common optimization techniques

Choose techniques based on the measured cost and the execution paths that trigger it.

- Combine or split tools and nodes where the architecture benefits from shared work, fewer copies or less dispatch. Keep each reusable calculation in a shared internal routine callable from both the combined CorrectionLib node and existing independent tool workflows. See [architectural exploration and timing](validation-and-timing.md#explore-and-benchmark-architectural-refactors).
- Reuse invariant preparation, lookups and intermediate results across outputs or systematic states. Verify which inputs, branch gates, configuration and RNG state affect the cache or reuse decision.
- Reduce materialization, allocation and data movement through buffer reuse, fewer conversions or cheaper input access. Measure the complete affected path.
- Use native multiple outputs to share calculations or input handling across results. Try native array processing when batching can reduce calls or marshalling. Check support in the pinned CorrectionLib build, verify dispatch through the intended implementation, and measure the two mechanisms independently.

## Resolve the expensive unknowns early

1. Freeze the original configuration and enumerate states before conversion. Capture all required outputs, statuses and availability from the original in one planned observer campaign where feasible; late missing diagnostics otherwise require another original capture.
2. Define the public API and output contract before performance work: empty events, field types, offsets, selection masks, state/output subsets and ordering, saved axes/flows/variances, overwrite policy and printed demo summary.
3. Smoke-test the command chain in scope on small real input: original observer, B/C loading, comparison and histogram writer. For standalone delivery, check wheel ABI, plugin loading and a visible script on a small real input as soon as one stage works. Test the extracted, installed final runtime on its frozen identity later. Verify synthetic fixture shape and failure location before a large acceptance campaign.
4. Establish the required CPU policy before the first baseline. Library thread limits alone do not constrain background readers. Observe analysis descendants and actual loaded libraries; distinguish launcher thread counts from analysis thread counts.
5. Record a stage dependency map and capture original results once. A changed provider requires affected numerical checks; a wording or plot change requires document checks, not another physics campaign.

Use explicit working directories and argument lists in launchers. In mixed Windows/WSL environments, preserve shell quoting and line endings; avoid repeatedly reconstructing fragile nested shell commands. Keep the authoritative build/results location separate from document inspection copies.

## Reuse evidence only with a validity check

Record source revision plus content hashes for the relevant source, provider, core, bindings, payload, input layout, configuration, original input and checker. A commit name alone is insufficient when binaries or uncommitted sources can differ. Observe loaded paths, not only intended build outputs.

For each proposed reuse, list which dependencies changed and which acceptance gates they affect. Reuse an unchanged expensive observation only when its full relevant identity and acceptance contract match; run the current comparison against it and link the reused record. Regenerate references after source-tool/configuration/input changes. Missing identity information means reuse is unproven. A final package still needs installed-environment acceptance even when its provider matches the development build.

Preserve rejected candidates, failed harness attempts and superseding records. A byte-identical restoration plus a focused loading smoke check can restore the earlier accepted timing's applicability; it does not make that old timing a fresh control for attributing a new small gain.

## Bound the optimization loop

Keep one active candidate and one runtime owner. Other agents can inspect source, prepare tests or review documentation; they must not overlap builds, validation, plotting or timing on the benchmark host. Prepare the next proposal without modifying the active candidate's sources or libraries.

For each candidate, record the measured cost it targets, expected end-to-end benefit, affected acceptance gates and retention criterion before running it. Check resource or maintenance tradeoffs where the mechanism introduces them, such as retained bytes or key checks for a new cache. Use this sequence:

1. Run focused semantic tests, including error and skip behavior.
2. Optionally screen the candidate with a short, representative real-input diagnostic. Reject and restore a clearly unpromising change when the observed margin is large enough for this limited test. Record the diagnostic scope and limits in the idea register. Advance uncertain or promising candidates to the full checks when warranted.
3. Run the required full original comparisons and affected B/C integration gates before a retention decision.
4. Run one matched production timing campaign, then adopt and commit or restore and reject. When a small apparent gain is comparable to drift, use at most one bounded alternating control/candidate study and retain every repetition. Reject or defer a result that remains unresolved.

Prioritize profile-supported work. Instrumentation must have disabled/enabled controls and known overhead; nested times cannot be added as exclusive costs. Count the production calls actually executed, not all outputs available in a diagnostic interface. An existing native default batch loop is not a Python loop; prove specialized callback dispatch before attributing a gain to batching. Test native multiple outputs and specialized batching as separate mechanisms when they address observed costs.

Cache tests should precede broad adoption: immutable ownership, exact key/state/configuration identity, reordered/subset outputs, missing unrequested fields, lazy exceptions, traces and nondeterministic RNG domains. Computing an eager cache record must not read fields the scalar request would never touch. Reuse rules are generic; which quantities remain invariant is calibration-specific.

Freeze the final B and C runtime identities for delivery. Rebuild against the accepted provider and verify the installed closure and loaded artifacts. Check earlier framework results against the final B identity and scope using the [Path B framework budget](validation-and-timing.md#apply-the-path-b-framework-budget). Time the actual delivered C launcher. Keep installation, warm execution and independent acceptance distinct. State whether cache terminology refers to dependency caches or operating-system caches.

When extending a mature payload, compare the executed embedded leaf configuration with the current accepted payload before regenerating it. A framework raw-input leaf and an optimized columnar context-input leaf may be numerically equivalent while exposing different caching and transport paths. Preserve the accepted interface, state routes and optimization eligibility; normalize only the explicitly authorized changes in a structural comparison. Numerical equality alone does not prove that an earlier performance optimization survived. Also record effective compiler flags: removing a dependency can change the language standard or other inherited build settings.

When most local trials yield marginal gains, revisit the executed call graph and measured cost distribution before continuing local tuning or ending the search. Look for a larger change that removes repeated work, expensive copies or avoidable stages. Prototype the strongest credible candidate within scope or record why the larger opportunities were deferred.

The optimization campaign may stop when ten consecutive measured attempts each improve end-to-end C time by at most 0.5%, or when no concrete, profile-supported opportunity remains in scope. Compare each attempt with its current accepted C checkpoint using quality-screened, matched full-systematics histogram timings. Count a resolved result of 0.5% or less, and reset the count after a resolved gain above 0.5%. A diagnostic, unusable timing batch or result still inconclusive relative to 0.5% after the bounded recheck leaves the count unchanged. Report the achieved A/C speedup and deferred ideas.

## Keep evidence maintenance cheap

Generate the report's optimization tables and figures from the machine-readable timing ledger and idea register, using existing tooling when available. Include baseline, controls, correctness-only checkpoints, rejected trials and restorations; derive repeated-run statistics consistently. Link detailed gate records rather than copying them into every report section. Render milestone/final figures after runtime handoffs, not after every metadata edit. If only the ledger's unrelated rows changed, verify plotted rows are unchanged before refreshing its provenance hash.

Retain immutable source inspection copies only when a document needs them, and verify hashes when synchronizing. Preserve artifact bytes across platforms with appropriate repository attributes. Build a compact handoff containing accepted identities, exact commands, pending decisions, runtime ownership and next action; do not reread the full chronological log on every continuation.

For future elapsed-time reviews, record start/end times, stage, attempt, outcome and whether work was build, validation, benchmark, packaging, documentation or blocked. Summed subprocess runtimes are not elapsed project time when authoring or investigation overlaps them. Without such records, report qualitative causes rather than inventing hour allocations or predicted savings.

## Source discovery and publishable links

Start from the configured algorithm, follow its properties to the tool, and then follow the resolver to the actual calibration data. Useful navigation entry points:

- [Athena analysis algorithms](https://gitlab.cern.ch/atlas/athena/-/tree/main/PhysicsAnalysis/Algorithms): look for the relevant collection's `*AnalysisConfig.py` and common configuration machinery. This navigation page may require a browser; a fetched loading shell is not inspected source.
- [ATLAS analysis software tutorial](https://atlas-software.docs.cern.ch/analysis/analysis_tutorial/AnalysisSWTutorial/): setup/configuration orientation; access may require CERN authentication. Use installed release sources when web access is unavailable.
- [Selected CorrectionLib develop branch](https://github.com/gordonwatts/correctionlib/tree/develop): inspect the pinned implementation for plugin, multiple-output and batch contracts, rather than assuming the released API supports them.
- [uv script documentation](https://docs.astral.sh/uv/guides/scripts/): launcher and inline dependency behavior; distinguish the standard metadata block from uv-specific local-wheel resolution.

These are discovery links, not evidence that a moving branch matches the installed release. For documentation, resolve repository commit/tag and link the exact file and relevant lines; retain source hashes for installed or inaccessible originals. Keep recommendation data identity separate from software identity. Never fabricate a public URL for a private/local campaign artifact. Publish small generic reproducers and extracted helpers only when authorized; otherwise preserve local evidence and clearly label its availability.

## Review scenarios

- New electron campaign: reuse loader/runner/packaging machinery, but recapture electron tools, links, statuses and systematics from the original.
- Jet payload changes with unchanged Python code: invalidate numerical gates and package identity; do not reuse acceptance merely because the facade is unchanged.
- Documentation-only edit: check links, evidence and snippets without repeating timings.
- Numerically valid candidate with inconclusive paired gain: restore once, preserve the negative result and move on.
- Final wheel uses a stale extension or a release library outside the wheel: fail freshness/isolation preflight before the full package campaign.

See [validation and timing](validation-and-timing.md) for numerical requirements and [portable packaging](portable-demo.md) for delivery requirements. This reference changes work sequencing and reuse decisions, not their acceptance thresholds.
