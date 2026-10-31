# Package a portable analysis demonstration

Read this when a migration must run outside its original framework/release or when the user asks for a transferable wheel or one-command demonstration. This is a deployment and usability check, separate from the full numerical acceptance campaign.

## Build and isolate the runtime

Target CPython 3.14 for the standalone package and demonstration test. Select the interpreter explicitly, record its exact version, and constrain the demonstration script to `requires-python = ">=3.14,<3.15"` so resolution cannot silently select another minor version. Do not substitute Python 3.13 or another minor version when dependencies or builds fail; report the blocker unless the user explicitly changes the requirement. This standalone requirement does not change the authoritative source framework's Python version.

Build in the authoritative toolchain, but include every runtime payload, model, extension and native library needed by the installed package. State the wheel's Python ABI, operating system and architecture tags. A wheel that runs on one Linux distribution with a `linux_x86_64` tag has not thereby earned a `manylinux` or cross-distribution claim. Record exact wheel bytes and hash; distinguish the wheel from a compressed transfer archive. Preserve earlier wheel/acceptance artifacts and use a new result directory.

Install the built wheel with `uv` into a fresh, dedicated Python environment and run with release setup absent. Inspect the installed package and loaded libraries to confirm they came from the wheel or declared public dependencies. If the test input lives on CVMFS or another external filesystem, say so: release-independent execution is a narrower claim than data-independent or offline execution. Keep an independent small reference fixture/checker for numerical acceptance, but do not silently make that fixture the demo's physics input.

## Make the script runnable from a local wheel

A PEP 723 script can declare `requires-python` and `dependencies` in its header. With uv, `[tool.uv.sources]` can point a package name to a wheel path relative to the script. Keep the script and wheel together and run `uv run script.py /path/to/input`; GitHub hosting is unnecessary. The path mapping is uv-specific, whereas the inline dependency block follows PEP 723. A prior `pip install` in an unrelated virtual environment does not satisfy the isolated script environment automatically. An uncached first run may download Python and public dependencies, so do not call the transfer archive fully offline or imply that the script file alone contains the native payload.

Choose the input users actually have, such as the original ROOT analysis file, and read it directly in bounded chunks. Avoid requiring a prepared NumPy/NPZ conversion merely to demonstrate the correction package. Validate required branches and preserve event/object identity and collection-dependent features. Run every requested nominal, kinematic and weight-only state. For a histogram example, use an explicit numeric axis and a category axis covering the states; state whether fills are weighted, what selections apply, and why weight-only variations may repeat nominal kinematic bins. Save axis names/edges, category labels, regular counts, flow counts and useful per-state summaries, then print the output path, processed events/objects, total entries and category list. Refuse to overwrite an existing result or put each run in a new directory.

## Verify and measure the demonstration

Check the runnable bundle after extraction, including its file list and checksums. Exercise a small sample from real input against the frozen reference where possible, then run the complete intended input. Compare category coverage, histogram contents and weight/selection summaries, not only successful exit. Keep the acceptance claim separate from the demo's aggregate-output check.

When timing repeated `uv run` calls, record the **exact command** and input path, cache state, Python and package installation behavior, timer boundaries, CPU/thread policy, every run and a deterministic comparison of saved outputs. Distinguish cold install from warm execution. Keep extraction, preparation and output moves inside or outside the timer consistently and say which. Compare against another workflow only after matching input, correction states, validation, output, affinity and timing boundaries; otherwise present the runs as separate endpoints. Earlier phase timings from another checkpoint cannot be subtracted from a later total to explain an exact gap.
