# Package a portable analysis demonstration

Read this when a migration must run outside its original framework/release or when the user asks for a transferable wheel or one-command demonstration. This is a deployment and usability check, separate from the full numerical acceptance campaign.

## Build and isolate the runtime

Target CPython 3.14 for the standalone package and demonstration test. Select the interpreter explicitly, record its exact version, and constrain the demonstration script to `requires-python = ">=3.14,<3.15"` so resolution cannot silently select another minor version. Do not substitute Python 3.13 or another minor version when dependencies or builds fail; report the blocker unless the user explicitly changes the requirement. This standalone requirement does not change the authoritative source framework's Python version.

Build in the authoritative toolchain, but include every runtime payload, model, extension and native library needed by the installed package. State the wheel's Python ABI, operating system and architecture tags. A wheel that runs on one Linux distribution with a `linux_x86_64` tag has not thereby earned a `manylinux` or cross-distribution claim. Record exact wheel bytes and hash; distinguish the wheel from a compressed transfer archive. Preserve earlier wheel/acceptance artifacts and use a new result directory.

Install the built wheel with `uv` into a fresh, dedicated Python environment and run with release setup absent. Inspect the installed package and loaded libraries to confirm they came from the wheel or declared public dependencies. If the test input lives on CVMFS or another external filesystem, say so: release-independent execution is a narrower claim than data-independent or offline execution. Keep an independent small reference fixture/checker for numerical acceptance, but do not silently make that fixture the demo's physics input.

## Make the script runnable from a local wheel

A PEP 723 script can declare `requires-python` and `dependencies` in its header. With uv, `[tool.uv.sources]` can point a package name to a wheel path relative to the script. Keep the script and wheel together and run `uv run script.py /path/to/input`; GitHub hosting is unnecessary. The path mapping is uv-specific, whereas the inline dependency block follows PEP 723. A prior `pip install` in an unrelated virtual environment does not satisfy the isolated script environment automatically. An uncached first run may download Python and public dependencies, so do not call the transfer archive fully offline or imply that the script file alone contains the native payload.

Choose the input users actually have, such as the original ROOT analysis file, and read it directly in bounded chunks. Avoid requiring a prepared NumPy/NPZ conversion merely to demonstrate the correction package. Validate required branches and preserve event/object identity and collection-dependent features. Run every requested nominal, kinematic and weight-only state. For a histogram example, use an explicit numeric axis and a category axis covering the states; state whether fills are weighted, what selections apply, and why weight-only variations may repeat nominal kinematic bins. Save axis names/edges, category labels, regular counts, flow counts and useful per-state summaries, then print the output path, processed events/objects, total entries and category list. Refuse to overwrite an existing result or put each run in a new directory.

## Keep the analysis visible in the standalone script

The delivered example must be a standalone PEP 723 script invoked at the top level as `uv run script.py INPUT OUTPUT`, with its Python requirement and dependencies declared in the script. Package the native provider, bindings and payload as needed, but do not make the example a thin wrapper around a packaged analysis CLI or an object-specific facade such as `MuonCorrections.iterate`.

The script itself must visibly:

1. Import `uproot`, specify the tree and requested branches, and iterate over input chunks using uproot. A branch list may come from a documented layout manifest, but the branch selection and read loop must be present in the script.
2. Import `correctionlib`, locate the packaged configuration/payload, load any required native plugin using the pinned API, construct the CorrectionSet and select the named correction. Show these operations rather than hiding them behind a convenience object's `load()`.
3. Map each chunk's arrays into the correction's inputs and explicitly call its `evaluate(...)` (or the verified native multiple-output API). Keep the systematic/output traversal visible. Explain units, types and event/object alignment.
4. Fill and save the requested histograms from the returned arrays and print the saved-output summary.

Some custom nodes require a native raw-input context instead of directly accepting every branch array. In that case a small, documented packing/binding helper is permitted: show its input arrays, context lifetime and the subsequent explicit CorrectionLib call in the script. The helper must not open the input file, hide chunk iteration, select physics states, run corrections or implement calibration/selection/weight arithmetic. Keep physics inside CorrectionLib nodes and keep data transport distinct from physics orchestration.

Convenience facades may remain available as an additional user interface, but they do not replace this transparent demonstration. If the current binding prevents a direct call, document the exact limitation and implement or expose the minimal transport interface; do not silently substitute an opaque wrapper or claim the requirement is satisfied.

Validate and time this exact visible script after extraction on the original input with all requested states. Earlier facade/CLI acceptance and timing do not automatically validate a rewritten input loop.

## Repeat complete input passes for profiling

Expose `-n N` (optionally also `--repeat N`) in the visible PEP 723 script, defaulting to one pass. Require a positive integer. Each pass must reopen the original ROOT input, repeat the same configured event range and selected-branch chunk reads, and execute the complete requested correction/state/histogram loop. Do not replace rereading with replay of prepared arrays, cached correction results or an evaluation-only loop. Bound memory by releasing per-pass arrays and contexts; retain immutable provider/payload setup outside the loop, but reset pass-local calculation state so results cannot leak between passes. Preserve original event/object identifiers and RNG semantics.

Reinitialize histograms for every pass. By default, save a single-pass result from the final pass, rather than silently multiplying yields by N; record the pass count and total processed event/object visits separately from unique input counts. Retain per-pass counts and deterministic output checks where the reference domain permits them, with each pass agreeing with the accepted one-pass output. Document whether output writing is repeated or final-only and keep that choice visible in profiling/timing scope. Verify `-n 1` compatibility, two-pass rereading and agreement, and rejection of zero, negative and noninteger values when implementing this option.

Use this mode for the [at-least-60-second final native profile](profiling.md#collect-at-least-60-seconds-for-the-final-native-profile). It is a repeated in-process diagnostic, not N independent process-startup measurements or a replacement for the ordinary benchmark repetition policy. Warm filesystem caches are permitted and must be stated; a file reopen does not imply physical disk/network reads.

## Verify and measure the demonstration

Check the runnable bundle after extraction, including its file list and checksums. Exercise a small sample from real input against the frozen reference where possible, then run the complete intended input. Compare category coverage, histogram contents and weight/selection summaries, not only successful exit. Keep the acceptance claim separate from the demo's aggregate-output check.

When timing repeated `uv run` calls, record the **exact command** and input path, cache state, Python and package installation behavior, timer boundaries, CPU/thread policy, every run and a deterministic comparison of saved outputs. Distinguish cold install from warm execution. Keep extraction, preparation and output moves inside or outside the timer consistently and say which. Compare against another workflow only after matching input, correction states, validation, output, affinity and timing boundaries; otherwise present the runs as separate endpoints. Earlier phase timings from another checkpoint cannot be subtracted from a later total to explain an exact gap.
