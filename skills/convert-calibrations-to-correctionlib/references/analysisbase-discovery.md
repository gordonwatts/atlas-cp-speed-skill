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

Replace RELEASE with the selected and recorded version; AnalysisBase 25.2.117 was the campaign's tested version, not a permanent default. Check setup success before proceeding. Missing CVMFS or an unavailable platform/release is an environment prerequisite failure, not a calibration failure. Record the actual Python, compiler, ROOT and release paths. The release Python is the appropriate starting point for PyROOT; an unrelated virtualenv may be incompatible. Do not apply standalone CPython requirements to the original release.

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
2. Locate the selected release's object configuration module. For example, after setup:
   ```bash
   python -c 'import MuonAnalysisAlgorithms.MuonAnalysisConfig as m; print(m.__file__)'
   ```
   Inspect its option declarations, defaults and algorithm/tool creation. For other objects, discover the corresponding package under the release's analysis algorithms rather than assuming identical option names.
3. Follow common services and configuration registration as well as the object module. Determine which options control systematic execution, recalibration of PHYSLITE, efficiency weights, detailed nuisance parameters and filters. Follow properties into the actual initialized tool and calibration recommendation.
4. Capture resolved properties and the registered systematic names after initialization, using the framework's systematics output/metadata and tool interfaces where available. Keep nominal, kinematic and weight-only states distinct. Cross-check that expected output branches/categories are produced. Do not infer the list from a filename or a single boolean.
5. Freeze the configuration, source/recommendation identities and enumerated states before recording the full reference. Data/MC, working point, release, calibration files and correlation model can all change the list.

For a concrete discovery example, the inspected muon module in AnalysisBase 25.2.117 declared `systematicBreakdown` with default false and described true as one efficiency nuisance parameter per uncertainty source. The campaign therefore explicitly set:

```yaml
CommonServices:
  runSystematics: true
  systematicsHistogram: systematics
Muons:
  - containerName: AnaMuons
    recalibratePhyslite: true
    WorkingPoint:
      - selectionName: medium
        quality: Medium
        isolation: Loose_VarRad
        systematicBreakdown: true
```

This is a configuration fragment illustrating the two distinct controls, not a complete runnable configuration or a universal muon prescription. Add the required output and sample-specific configuration; verify options against the chosen release. Do not transplant these working points or option names into electrons/jets. Full systematic coverage means the requested initialized prescription, not every optional tool or working point in the release.

## Source locations for documentation

Start with [Athena analysis algorithms](https://gitlab.cern.ch/atlas/athena/-/tree/main/PhysicsAnalysis/Algorithms) and the [ATLAS analysis software tutorial](https://atlas-software.docs.cern.ch/analysis/analysis_tutorial/AnalysisSWTutorial/). Web access may require a browser or CERN authentication; installed module paths remain useful when access fails. Navigate from the collection configuration to tool sources and resolver-loaded data. Replace moving-branch navigation links with release/tag/commit-specific file links in the campaign report, and record installed-file hashes when that mapping is unavailable. Never treat the current main branch as proof of an older release's defaults.
