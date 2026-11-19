# ATLAS CP speed skill

Use [Convert Calibrations to CorrectionLib](skills/convert-calibrations-to-correctionlib/SKILL.md) to migrate ATLAS calibrations and systematic uncertainties to a shared CorrectionLib backend, validate against the original release tools, and measure the resulting workflows.

Install the skill directory under `.agents/skills/` in your campaign checkout and invoke `$convert-calibrations-to-correctionlib` with the calibration scope. The current project workflow uses WSL `atlas_al9` with CVMFS-mounted AnalysisBase for authoritative builds and measurements, and a Windows checkout for reports, talk assets, and source inspection copies. Resolve the authoritative checkout, release, inputs, and CorrectionLib revision from the campaign handoff.

## Comparison paths

- **A** runs unmodified release AnalysisBase.
- **B** runs the replacement in AnalysisBase with the existing analysis configuration.
- **C** reads the original PHYSLITE file with uproot and runs the columnar workflow in Python.

All three use the same original PHYSLITE input. B and C use CorrectionLib as the runtime entry point for physics calculations, including any registered custom C++ nodes, and are independently compared against A. Campaign calibration and benchmarking use one CPU and one execution thread unless requested otherwise.

## Reference guides

- [AnalysisBase discovery](skills/convert-calibrations-to-correctionlib/references/analysisbase-discovery.md) and [ATLAS numerical guidance](skills/convert-calibrations-to-correctionlib/references/atlas-lessons.md)
- [Correction graph design](skills/convert-calibrations-to-correctionlib/references/graph-design.md) and [validation and timing](skills/convert-calibrations-to-correctionlib/references/validation-and-timing.md)
- [Campaign reuse and optimization](skills/convert-calibrations-to-correctionlib/references/efficient-campaigns.md) and [profiling](skills/convert-calibrations-to-correctionlib/references/profiling.md)
- [Standalone packaging](skills/convert-calibrations-to-correctionlib/references/portable-demo.md) and [reports and talks](skills/convert-calibrations-to-correctionlib/references/report-and-talk-guide.md)
- [Maintaining the skill](skills/convert-calibrations-to-correctionlib/references/contributing.md)
