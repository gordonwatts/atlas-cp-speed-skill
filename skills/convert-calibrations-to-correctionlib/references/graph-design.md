# Graph design and human interfaces

## Use the library accurately

Inspect the selected CorrectionLib source/schema and authoritative documentation before selecting operations. Check support for Formula/FormulaRef, Binning, Transform, CompoundCorrection and Category as needed. Bin selection is not histogram interpolation: encode the source's interpolation arithmetic and boundary behavior explicitly.

Check CompoundCorrection's ordered scalar application, input updates, output accumulation and permitted stack references in the selected version. Do not assume general shared intermediates, nested compounds, multiple outputs or arbitrary correction calls are available as standard nodes.

CorrectionLib's C++ evaluator executes loaded definitions. Batch bindings do not establish SIMD, GPU execution or specialized code generation. Verify performance properties separately.

When asked what the library contributes, identify actual executed nodes and give a concrete composition example from the current implementation. Distinguish table searches, formula evaluation, transforms and sequential compositions from the physics retained in shared C++. Exported interpolation formulas are numerical calculations, not merely packaging machinery; explain who executes them.

Record external numerical stages explicitly. Trace NNJVT inference separately from JVT scale-factor/efficiency/error lookups. Inspect available operations in the selected version and preserve source inference semantics; do not assume inference is external or claim it was moved out of CorrectionLib without evidence from the actual callers.

Inspect how the selected Formula implementation handles `parser: "TFormula"`: expression syntax does not imply ROOT's TFormula runtime. Distinguish load-time parsing, interpreted expression-tree evaluation and specialized compiled code. Trace Python bindings to the native core before describing execution behavior.

When explaining or authoring a formula, distinguish declared runtime `inputs` from stored `parameters`. The ordered `variables` list maps named inputs to formula symbols `x`, `y`, `z`, `t`; `[0]`, `[1]`, etc. index stored coefficients. Evaluation arguments follow the correction's declared input order and strict types. State whether the returned scalar is a corrected value, additive offset or multiplicative factor; callers must not apply a corrected value as a factor. Keep manifests/settings distinct from executable corrections when describing a bundle.

## Choose calculation placement and reusable operations

For stages chosen for declarative representation, place interpolation choices, response inverses, flavour/pile-up expressions, group combinations, systematic signs, validity and stage order in payload definitions. Table and hybrid migrations may retain these rules in shared C++ when measured runtime performance supports that placement; document the division and evidence as required by SKILL.md. Cache only where declared dependencies permit reuse. For example, original-direction truth matching can remain unchanged while momentum-dependent tagging features change under a jet variation.

Use authoring helpers that expand into existing nodes. This workflow does not add new CorrectionLib nodes or extend its schema/parser/evaluator for missing operations. Implement unsupported calculations outside CorrectionLib and record useful candidate nodes as unimplemented future opportunities in the report and task log. Never present external operations as upstream schema capabilities.

## Distinguish definitions from execution reuse

Describe three separate forms of reuse:

- An authoring helper expands a recipe into generated instructions. Multiple callers may contain duplicated instructions.
- A named runtime subgraph lets programs call one definition. Each call may still execute it again.
- Reuse of computed values requires an execution plan or cache with correct dependencies. Neither of the first two forms establishes this optimization.

Check the actual call graph across requested outputs and variations. A complete per-state pipeline may recompute nominal calibration for every state even when it calls a shared nominal graph. A staged runner can compute nominal once yet still repeat systematic calculations in a later diagnostic phase. Preserve numerical ordering while identifying work that can safely be shared; recompute descendants affected by changed inputs.

When comparing architectures, explain the practical change: table lookup plus handwritten physics requires changing C++ for a formula change; a payload-defined recipe can change through regeneration when existing generic operations suffice. Count named reusable stages separately from component helpers expanded during authoring. A dense register-building script does not by itself provide a readable physicist-facing authoring interface.

Reusable authoring and interpreted execution are independent choices. Distinguish definitions lowered to supported nodes from external compiled calculations; do not imply a compiler or graph engine exists without inspecting it. Retain source mapping, explicit precision/branch semantics and reference validation when changing backends. Measure dispatch/traversal cost rather than inferring an end-to-end effect. Evaluate whether standard CorrectionLib is best used as the table/formula layer while shared compiled code composes the larger calculation.

## Authoring experience

An illustrative target interface (not an existing API):

```python
response = interpolate(response_histogram, pt, abs(eta))
uncertainty = response * (mu - mu_reference)
pileup_component = correction(
    name="pileup_mu",
    inputs=[pt, eta, mu],
    value=uncertainty,
    validity=valid_jet_region,
)
```

These expressions construct a definition, not event-time Python calculations. A small typed authoring library should lower readable expressions, track units/types and make rounding explicit. Shared expressions need stable names and dependency information. Preserve arithmetic order where precision matters; algebraic equivalence does not guarantee the same rounded result.

Do not introduce a large framework before demonstrating a real component from source constants through authoring, lowering and reference comparison.

## Application experience

An illustrative facade:

```python
jets = JetCorrections.load("ATLAS/Run3/EMPFlow/<version>")
result = jets.evaluate(jet_columns, event_columns, systematics="recommended")
```

Implement and document real bundle resolution before promising such an API. Resolve "recommended" to a recorded list. Expose required columns, units, shapes, output names and invalid-input behavior. Preserve jagged event boundaries and original identities. Support systematic subsets or streamed outputs when full materialization is costly.

Load payloads/models once, resolve graph names once, prepare input features once per chunk, and reuse only unchanged dependencies. Avoid repeated Python inner-loop calls, array-to-list copies and payload parsing. Measure bottlenecks before optimizing.

If the requested interface is columnar, show original-file reading and a real array reduction or histogram fill. Loops over input chunks and fixed field names are compatible with that contract; Python loops over events or objects are not. A shared scalar C++ loop behind a dense-array boundary can satisfy the task without a SIMD rewrite. Keep example chunk-size settings separate from benchmark settings and distinguish a nominal-only facade from the wider runner that supplies variations and weights.

Separate load-time recipe processing from event-time numerical evaluation. Parse manifest fields and constant strings into typed immutable execution records, and resolve correction handles once, where the API and ownership permit. Resolve a systematic's execution state when it changes rather than reconstructing its meaning for every object. Such records are a lowering of the authoritative prescription, not a second handwritten physics definition or a cache of results whose inputs may change.

Check lazy branches for avoidable numerical work before their guards. Skipping lookups for an inactive variation can help, but must preserve domain/status/error semantics and random draws. Likewise, inspect numerical-library object lifetime: repeated random-engine construction followed by explicit reseeding can be avoidable for a per-object-reseeded contract, but a persistent stochastic stream has different requirements. Use the experiments in [validation and timing](validation-and-timing.md#isolate-components-and-mechanisms) to establish costs and validate any change.

Inspect object ownership as well as constructors: a Python table handle may be unused while several C++ engines independently load the same payload. Inspect bindings and batch bodies before describing vectorization or concurrency. Array acceptance, GIL release, scalar row loops, internal threading and SIMD are distinct properties. Nested-vector bindings and per-instruction temporary allocations are profiling candidates, not measured causes of slowdown.

## Exact caches and reusable numerical buffers

Use these only when profiling and the numerical contract support them; do not prescribe a cache for every calibration.

- Key inference reuse by all effective inputs after the source's casts/rounding. Associate it with the immutable model/payload identity and verify the entire key on a hit; a hash alone is insufficient. Define capacity, collision behavior, ownership and concurrency. Handle nonfinite inputs deliberately. Feature-keyed reuse need not depend on stable object position, but any position-keyed cache must track identity across chunks and reorderings.
- Cache only the quantity determined by those inputs. Reusing an NN score does not authorize reusing variation-dependent selection, validity, scale factors or event weights. Whole-batch caches must protect against caller mutation of returned arrays and invalidate on changed values, model/tool, variation and relevant configuration.
- Test repeats, collisions, reordered inputs, model replacement, chunk changes, empty inputs and thread isolation as applicable. Preserve a reference evaluator and check values as well as statuses. Cache overhead can outweigh saved work; retain a cache-only measurement instead of crediting it with a later combined gain.
- Reusable intermediates address allocations on cache misses; a cache hit skips inference altogether. Preserve arithmetic precision, matrix layout, input ordering, bias/activation ordering and clipping. Fall back explicitly for unsupported graphs/activations. Fusion or fixed-size kernels are separate experiments because algebraic equivalence does not establish identical rounding.
- Verify that the optimized branch is actually exercised and that the tested headers/library are the intended build; stale include paths can select a staging implementation. A no-allocation claim must name the scope (for example, Eigen heap allocation during supported inference after warm-up), not the entire workflow.
- Random-number reuse has a different contract. For a per-object-reseeded implementation, first-draw caching by seed requires proof of equivalent correlations and replay of any width-dependent rejection stream. Caching the accepted smear value or substituting another RNG need not be equivalent. Inspect source stream, seed and rejection semantics before choosing reuse.

Define empty collection/chunk behavior and result shapes, absent-primary-object behavior, and per-associated-object indexing contracts. A numerical graph's missing-input branch cannot help if Python extraction indexes an absent vertex or another associated collection first. Preserve validity masks and document how invalid object weights enter event reductions; returning numeric arrays alone is insufficient.

Describe, explain, compare and provenance views should derive from the executed definitions: show stages and requirements, trace one object, locate first divergence and identify source constants. Do not maintain a second handwritten physics prescription for documentation.

External inference, random sampling, coordinate arithmetic and collection operations must remain explicit boundaries. A generic primitive may call a mature numerical library; that does not make the model or collection-processing dependencies disappear.

## Sources

- Official overview: https://cms-nanoaod.github.io/correctionlib/
- Schema: https://cms-nanoaod.github.io/correctionlib/schemav2.html
- Source: https://github.com/cms-nanoAOD/correctionlib

Check against the version actually used; do not infer capabilities from another release or implementation.
