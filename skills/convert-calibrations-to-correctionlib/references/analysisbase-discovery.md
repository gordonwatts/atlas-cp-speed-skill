# AnalysisBase setup and systematic configuration discovery

Read this before the first original-framework run, or when changing object type or release. Setup itself is usually short; discovering the resolved physics configuration requires inspecting the selected release.

## Start the original reference in a clean environment

Use the repository's authoritative Linux/WSL environment with CVMFS mounted. Start a fresh shell without a replacement-tool overlay. The setup pattern used successfully in the campaign is:

```bash
export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase
source "$ATLAS_LOCAL_ROOT_BASE/user/atlasLocalSetup.sh" --quiet
asetup AnalysisBase,RELEASE
export OMP_NUM_THREADS=1 OPENBLAS_NUM_THREADS=1 MKL_NUM_THREADS=1
export NUMEXPR_NUM_THREADS=1 VECLIB_MAXIMUM_THREADS=1 BLIS_NUM_THREADS=1
command -v python
command -v CPRun.py
CPRun.py --help
```

Replace RELEASE with the selected and recorded version. Check setup success before proceeding. Missing CVMFS or an unavailable platform/release is an environment prerequisite failure, not a calibration failure. Record the actual Python, compiler, ROOT and release paths. The release Python is the appropriate starting point for PyROOT; an unrelated virtualenv may be incompatible. Do not apply standalone CPython requirements to the original release.

The observed CPRun interface supports this smoke-run shape; verify flags against the selected release's help:

```bash
# Define absolute paths to the selected original input, frozen YAML and new output.
input_file=/absolute/path/to/original.pool.root
config_file=/absolute/path/to/reference.yaml
result_dir=/absolute/path/to/new-smoke-result
mkdir "$result_dir"
cd "$result_dir"
taskset -c 0 CPRun.py -i "$input_file" -t "$config_file" -o reference.root -e 100 >job.log 2>&1
```

Use a fresh result directory, inspect the log and output, then run the required full sample. The 100-event example is a smoke test, not acceptance. Numerical-library limits and CPU affinity are not proof of a single analysis thread: observe actual workers and loaded libraries as described in [validation and timing](validation-and-timing.md). Keep setup outside benchmark clocks and keep A's release libraries separate from B's replacements.

## Find defaults from configuration to initialized tools

There is no universal YAML that means every systematic for every object. A job can enable systematics while a tool uses a reduced uncertainty representation or a stage is disabled.

1. Read the existing analysis YAML and its input/campaign settings first. If it contains only another object, explicitly establish the new object's working scope; do not call inferred working points existing defaults.
2. Find the package for the requested object under the selected release's analysis algorithms. Locate the configuration module in the active runtime and inspect its option declarations, defaults and algorithm/tool creation.
3. Follow common services and configuration registration as well as the object module. Determine which options control systematic execution, recalibration of PHYSLITE, efficiency weights, detailed nuisance parameters and filters. Follow properties into the actual initialized tool and calibration recommendation.
4. Capture resolved properties and the registered systematic names after initialization, using the framework's systematics output/metadata and tool interfaces where available. Keep nominal, kinematic and weight-only states distinct. Cross-check that expected output branches/categories are produced. Do not infer the list from a filename or a single boolean.
5. Freeze the configuration, source/recommendation identities and enumerated states before recording the full reference. Data/MC, working point, release, calibration files and correlation model can all change the list.

Verify global execution and each tool's uncertainty configuration from the selected release. Full systematic coverage means the requested initialized prescription. Consult earlier worked configurations only when the campaign's reuse rules permit them.

## Distinguish systematic scope and alternative models

Treat **available** as an explicitly defined inventory (for example source/data-file components or supported alternative models), not a universal API or one executable uncertainty prescription. Query **affecting** and **recommended** states from each initialized tool: the first describes what that instance accepts as affecting it, while the second describes its recommended set under the selected settings. Neither guarantees a nonzero effect on every object. Establish the **configured/executed** set by tracing enabled stages, resolved properties, service enumeration, filters, input dependencies and output coverage. In the inspected AnalysisBase service, recommended states drive ordinary enumeration; affecting states alone do not cause execution. Record nominal and weight-only outputs separately from kinematic shifts.

Resolve the actual scope of a setting such as `AllSys` in the pinned release. A scheme may select a decorrelated representation in place of a correlated variation and may act on only one tool. Inspect global execution, tool enablement, correlation choices and per-tool uncertainty breakdown separately. For example, in AnalysisBase 25.2.117, trace `AllSys` from the muon momentum configuration through `MuonCalibTool` to the scale/smear and sagitta tools. Record the initialized states for the chosen prescription and check other calibration tools independently.

For a faithful conversion, preserve the requested initialized prescription in A/B/C. When the scope explicitly requires all supported alternatives, inventory omissions and test valid models or non-recommended diagnostic states in separately identified configurations. Do not combine alternative correlated/decorrelated bases or totals and their components as independent uncertainties; some tools reject such combinations, and broadening a state list can double count uncertainty. Check data/MC applicability, working points, calibration files, disabled stages, pseudo-data, filters and toy/ensemble handling. Capture initialized state lists and actual output/status coverage before claiming completeness. Static inspection establishes configuration semantics, not numerical acceptance of every setting; do not retroactively fail a previously satisfied frozen contract merely because a broader prescription exists.

## Capture the original behavior

Smoke-test the observer on real input and freeze the resolved configuration. Preserve object identity and order before thinning or sorting. Capture the exposed stages, calibrated values, selections, weights and statuses for the required state set. Use [independent acceptance](validation-and-timing.md#independent-authority) for the required identity, scope and comparison checks.

Trace the active loading chain through defaults, resolver search paths, merge precedence and data objects. For each active stage, record inputs, types, units, calculation, constants, interpolation, boundaries, output and rounding. Identify stages that use the original object and those that use the recalibrated object. Record which features change under each variation. Confirm the actual execution path from the caller through bindings to the numerical implementation.

Preserve observed source defects in the faithful conversion. Record a `BUG` entry with the source revision/location, trigger, observed behavior, evidence, and how parity is preserved. Distinguish suspected defects from confirmed ones. Intentional changes to the physics prescription beyond the authorized experimental stream require an explicit user decision.

## Source locations for documentation

Start with [Athena analysis algorithms](https://gitlab.cern.ch/atlas/athena/-/tree/main/PhysicsAnalysis/Algorithms) and the [ATLAS analysis software tutorial](https://atlas-software.docs.cern.ch/analysis/analysis_tutorial/AnalysisSWTutorial/). Web access may require a browser or CERN authentication; installed module paths remain useful when access fails. Navigate from the collection configuration to tool sources and resolver-loaded data. Replace moving-branch navigation links with release/tag/commit-specific file links in the campaign report, and record installed-file hashes when that mapping is unavailable. Never treat the current main branch as proof of an older release's defaults.
