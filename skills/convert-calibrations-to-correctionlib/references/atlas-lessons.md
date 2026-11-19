# ATLAS calibration implementation lessons

Read this for ATLAS conversions. These are source-inspection prompts and numerical hazards, not universal prescriptions: verify the active release, calibration recommendation and settings before applying any example. Keep task-specific configurations, measurements and implementation history in project documentation. Follow repository instructions for authoritative source, builds, reports and artifact locations.

Read [AnalysisBase setup and configuration discovery](analysisbase-discovery.md) before the first original run or when changing object/release. It gives the setup/run pattern and explains how to resolve full systematic coverage from installed configuration and initialized tools.

## Three execution paths and common input

- **A — release AnalysisBase:** use AnalysisBase straight out of the release, without modified tool implementations or replacement libraries. Configure the requested calculations normally; unmodified does not mean disabling calibration. This is the numerical authority.
- **B — modified AnalysisBase:** integrate the conversion and accepted optimizations through the original framework interfaces, using the same shared correction calculation code and CorrectionLib payloads as C. C++ physics must execute behind the same registered CorrectionLib nodes used by C; framework adapters provide input/output plumbing.
- **C — columnar:** read original PHYSLITE columns independently and call the shared backend used by B, preserving event boundaries, identities and source precision.

Use the original PHYSLITE input chosen for the current campaign. Resolve and record its full identity from the current handoff or configuration. A, B and C must read the same original file with matched event ranges and resolved correction and systematic configuration.

Compare B and C independently against A. B/C agreement is useful integration evidence but cannot replace the original-tool oracle. Keep the release and replacement library environments separate. Use one CPU and one execution thread unless the user explicitly requests parallelization. A standalone C test must use CPython 3.14 without the release setup.

## Resolver and numerical semantics

- Trace PathResolver, ROOT TEnv merge precedence, defaults and versioned calibration areas. Active dependencies may include text arrays, ROOT histograms/functions and inference/cut JSON.
- Inspect the actual lookup operation: a configuration named spline may invoke histogram interpolation. Preserve bin-center arithmetic, low-energy behavior, extrapolation and high-energy freezing.
- Inspect residual pile-up reset conditions exactly. A negative-momentum reset is not equivalent to clamping every small positive momentum.
- Sequential calibrations such as GSC may evaluate each response at the momentum updated by the preceding stage. Use CompoundCorrection only when its update and accumulation semantics match the source.
- Direction-changing eta corrections require coordinate reconstruction; the full calibration need not be one multiplicative scale. Preserve stage rounding and ROOT normalization for negative scales or unusual mass conventions.
- TH1/TH2 interpolation, including singleton axes, differs from constant bin lookup. Test each boundary and source flow policy.
- Uniform bin encodings may choose different bins near floating-point edges despite equal nominal spacing. Check every edge and adjacent representable input before replacing explicit edges.
- JSON parsing can change the last bit of a constant and amplify downstream differences. Check round trips through each used loading entry point in the selected evaluator; investigate precision defects rather than assuming a historical patch is needed.
- Nominal and uncertainty tools may define NPV differently. Inspect vertex-type/track requirements and nonpositive-input sentinels independently.
- Preserve constituent-scale versus calibrated inputs, DetectorEta versus calibrated eta, area, density, per-vertex moments, units and explicit float conversion order.
- Flavour branches and correlation settings control component combination. Do not infer linear or quadrature behavior from another recommendation.
- Preserve TRandom3 seed derivation, fallback seeds, draws, sign rules, pseudo-data treatment and rejection behavior. HashPRNG is not automatically an equivalent substitute.
- Inspect NNJVT feature, eta, cut-bin and efficiency conventions. Recompute momentum-dependent features under variations; preserve right-closed bins where the source uses lower_bound.
- Determine whether truth matching uses original or recalibrated directions. Preserve source thresholds, wrapping, float rounding and operation order.
- For failing-object weights, inspect expressions such as (1 - SF * efficiency)/(1 - efficiency), variations of both inputs, validity and event-product exclusions.
- Boundary probes complement ordinary PHYSLITE samples. Mark suspected source defects with BUG in the task log and preserve their behavior under the conversion policy.

## Authorized random-stream changes and ROOT removal

The experimental ROOT-free campaign authorizes the stream described in the [packaging guide](portable-demo.md#deliver-a-root-free-python-package). Preserve an accepted original-stream checkpoint first and report the changed-stream variant separately. Replace unrelated numerical dependencies separately and compare their source-equivalent operations against the original, including float rounding, operation order, zero vectors, negative mass conventions and singular pseudorapidity encodings. Algebraic simplifications such as replacing a vector theta calculation with an eta identity can alter rounding.

Version the changed stream in the node/payload contract. Specify engine rounds, key/counter mapping, word order, uniform/Gaussian precision, spare-value caching, reseeding, zero/out-of-range seeds and reuse across systematic states. Counter-based engines do not by themselves guarantee unchanged event-level values or cross-platform bitwise Gaussian results. Validate upstream known answers, adapter replay/reseed behavior, observed seed domains, distribution/tail checks and cross-draw correlations. Test systematic, chunk and ordering invariance in the real recipe. Distinguish these finite-sample checks from physics certification and exact original-tool agreement; never silently widen the old agreement claim.

Removing an RNG dependency does not establish ROOT-free execution. Inventory vectors, angle/energy operations, histograms, transitive ELF dependencies, preload configuration and runtime resources. Keep reference/conversion-only ROOT tools optional and outside the installed runtime closure. Inspect the final installed wheel and actual loaded libraries in a release-independent environment, then validate both framework and columnar execution using the same changed prescription.

## Framework and tooling boundaries

Preserve public component names, properties and decorations when unchanged analysis configuration is required; explicitly reject unsupported settings. A successful narrow integration does not establish every specialist tool API or Athena compatibility.

For package-isolation studies, keep implementation libraries, component factories and ROOT dictionaries ABI-consistent. Verify loaded libraries, including descendant processes when applicable; setup-script names alone are insufficient. Avoid blindly preloading ROOT-linked libraries.

When driving Linux commands through another shell, protect variables from premature expansion and verify that work starts and completion markers belong to the current run. Follow the repository's execution environment rather than hard-coding host paths here.

Diagnose dependency and SSL failures in the actual environment. Use verified downloads and explicit installation steps rather than disabling TLS checks.

Follow real imports and bindings before describing an implementation. Array input, GIL release, scalar C++ loops, threading and SIMD are separate properties. Check empty collections, absent vertices and empty systematic batches before native evaluation.

Trace inference separately from efficiency/scale-factor lookups and show which CorrectionLib node invokes each calculation. Standard node availability depends on the selected CorrectionLib version. Implement missing operations as registered nodes using the selected extension mechanism; record their contracts, dependencies and validation in the task log. Numerical libraries may be dependencies of nodes, but adapters must not call them independently to perform physics.

NOSYS may still run nominal calibration, selection and weights. A stored-object or no-calibration control must explicitly disable the relevant work and identify its output scope.

Record missing original statuses as a limitation. Synthetic status probes, composition tests and shared-backend agreement do not establish full-sample original-tool status parity.
