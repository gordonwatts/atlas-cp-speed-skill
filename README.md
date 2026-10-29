# ATLAS CP speed skill

Agent instructions for converting ATLAS calibrations and systematic uncertainties to CorrectionLib, with shared C++ calculation code, numerical validation and measured algorithmic optimization.

Start with [the skill instructions](skills/convert-calibrations-to-correctionlib/SKILL.md). Supporting references cover [ATLAS integration](skills/convert-calibrations-to-correctionlib/references/atlas-lessons.md), [calculation design](skills/convert-calibrations-to-correctionlib/references/graph-design.md), [validation and timing](skills/convert-calibrations-to-correctionlib/references/validation-and-timing.md), [standalone packaging](skills/convert-calibrations-to-correctionlib/references/portable-demo.md), [technical talks](skills/convert-calibrations-to-correctionlib/references/talk-writing.md) and [contributions](skills/convert-calibrations-to-correctionlib/references/contributing.md).

## Comparison paths

- **A:** unchanged AnalysisBase from the release.
- **B:** modified AnalysisBase using the shared correction backend and payloads.
- **C:** columnar execution using the same backend as B.

All three test from the same selected original PHYSLITE file, with B and C independently compared against A.

## Current implementation policy

Table and hybrid conversions are acceptable, guided by measured speed and numerical correctness. Use one CPU and one execution thread unless parallelization is explicitly requested. Do not add new CorrectionLib nodes; document missing capabilities and implement unsupported calculations outside the library. Preserve source bugs and mark them clearly in the task log. Standalone tests use CPython 3.14. Completion requires all required numerical acceptance checks to pass, with missing evidence explicitly accepted as a limitation.

This repository contains the reusable skill, not calibration payloads, input data, implementation code or campaign results. Review individual instructions through GitHub issues and pull requests.
