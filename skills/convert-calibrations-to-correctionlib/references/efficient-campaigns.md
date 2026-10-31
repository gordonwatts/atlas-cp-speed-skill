# Efficient campaigns and reusable infrastructure

Read this at campaign startup and before committing to another optimization experiment. Reduce repeated engineering and invalid experiments, not independent numerical acceptance. These procedures were distilled from an extended conversion campaign; they are not a measured promise of a particular reduction in engineering time.

## Reuse inventory before implementation

Inspect existing campaigns and classify each component as reusable unchanged, reusable after adaptation, or calibration-specific. Record its location, revision, interface, validation evidence and invalidation conditions. Do not build a general framework from scratch merely to satisfy this inventory.

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

## Resolve the expensive unknowns early

1. Freeze the original configuration and enumerate states before conversion. Capture all required outputs, statuses and availability from the original in one planned observer campaign where feasible; late missing diagnostics otherwise require another original capture.
2. Define the public API and output contract before performance work: empty events, field types, offsets, selection masks, state/output subsets and ordering, saved axes/flows/variances, overwrite policy and printed demo summary.
3. Smoke-test the complete command chain on a small real input: original observer, B/C loading, comparison, histogram writer and extracted standalone launcher. Validate synthetic fixture shape and the intended failure location before a large acceptance campaign. A test that fails for the wrong reason is not coverage.
4. Establish the required CPU policy before the first baseline. Library thread limits alone do not constrain background readers. Observe analysis descendants and actual loaded libraries; distinguish launcher thread counts from analysis thread counts.
5. Record a stage dependency map and capture original results once. A changed provider requires affected numerical checks; a wording or plot change requires document checks, not another physics campaign.

Use explicit working directories and argument lists in launchers. In mixed Windows/WSL environments, preserve shell quoting and line endings; avoid repeatedly reconstructing fragile nested shell commands. Keep the authoritative build/results location separate from document inspection copies.

## Reuse evidence only with a validity check

Record source revision plus content hashes for the relevant source, provider, core, bindings, payload, input layout, configuration, original input and checker. A commit name alone is insufficient when binaries or uncommitted sources can differ. Observe loaded paths, not only intended build outputs.

For each proposed reuse, list which dependencies changed and which acceptance gates they affect. Reuse an unchanged expensive observation only when its full relevant identity and acceptance contract match; run the current comparison against it and link the reused record. Regenerate references after source-tool/configuration/input changes. Missing identity information means reuse is unproven. A final package still needs installed-environment acceptance even when its provider matches the development build.

Preserve rejected candidates, failed harness attempts and superseding records. A byte-identical restoration plus a focused loading smoke check can restore the earlier accepted timing's applicability; it does not make that old timing a fresh control for attributing a new small gain.

## Bound the optimization loop

Keep one active candidate and one runtime owner. Other agents can inspect source, prepare tests or review documentation; they must not overlap builds, validation, plotting or timing on the benchmark host. Prepare the next proposal without modifying the active candidate's sources or libraries.

For each candidate, record the measured cost it targets, expected mechanism, affected acceptance gates and retention criterion before running it. Use this sequence:

1. Focused semantic tests, including error/skip behavior, before full acceptance.
2. Required full original comparisons and affected B/C integration gates.
3. One matched timing campaign, then commit/adopt or restore/reject.
4. For a small apparent gain comparable to drift, one bounded alternating control/candidate study. Keep all repetitions under the established policy. If the result is unresolved, reject or defer; do not keep sampling until it looks favorable.

Prioritize profile-supported work. Instrumentation must have disabled/enabled controls and known overhead; nested times cannot be added as exclusive costs. Count the production calls actually executed, not all outputs available in a diagnostic interface. An existing native default batch loop is not a Python loop; prove specialized callback dispatch before attributing a gain to batching. Introduce native multiple outputs and specialized batching separately after ordinary optimizations, as required by the main workflow.

Cache tests should precede broad adoption: immutable ownership, exact key/state/configuration identity, reordered/subset outputs, missing unrequested fields, lazy exceptions, traces and nondeterministic RNG domains. Computing an eager cache record must not read fields the scalar request would never touch. Reuse rules are generic; which quantities remain invariant is calibration-specific.

Freeze the accepted provider before final framework and standalone campaigns. Rebuild bindings/package against that identity, then verify the installed closure. Time the actual delivered launcher, not only a convenient underlying CLI. Keep installation, warm execution and independent acceptance distinct; record whether cache terminology refers to dependency caches or operating-system caches.

Stop when no concrete profile-supported opportunity remains within the requested scope. Record deferred hypotheses and achieved results rather than treating a target speedup as an unlimited search mandate.

## Keep evidence maintenance cheap

Use one machine-readable timing ledger as the source for tables and figures. Include baseline, controls, correctness-only checkpoints, rejected trials and restorations; derive repeated-run statistics consistently. Link detailed gate records rather than copying them into every report section. Render milestone/final figures after runtime handoffs, not after every metadata edit. If only the ledger's unrelated rows changed, verify plotted rows are unchanged before refreshing its provenance hash.

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

