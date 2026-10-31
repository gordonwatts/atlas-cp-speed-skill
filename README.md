# ATLAS CP speed skill

Agent instructions for converting ATLAS calibrations and systematic uncertainties to CorrectionLib, with shared C++ calculation code, numerical validation and measured algorithmic optimization.

Start with [the skill instructions](skills/convert-calibrations-to-correctionlib/SKILL.md). Supporting references cover [ATLAS integration](skills/convert-calibrations-to-correctionlib/references/atlas-lessons.md), [calculation design](skills/convert-calibrations-to-correctionlib/references/graph-design.md), [validation and timing](skills/convert-calibrations-to-correctionlib/references/validation-and-timing.md), [standalone packaging](skills/convert-calibrations-to-correctionlib/references/portable-demo.md), [technical talks](skills/convert-calibrations-to-correctionlib/references/talk-writing.md) and [contributions](skills/convert-calibrations-to-correctionlib/references/contributing.md).

## Comparison paths

- **A:** unchanged AnalysisBase from the release.
- **B:** modified AnalysisBase using the shared correction backend and payloads.
- **C:** columnar execution using the same backend as B.

All three test from the same selected original PHYSLITE file, with B and C independently compared against A.

## Current implementation policy

- The outputs must remain the same. Small variations (due to different random number generators, for example, are acceptable).
- CorrectionLib must be the top level interface, and everything must be under it. Creating new nodes is acceptable.
- All testing occurs in a single CPU - do not run anything in parallel.
- If you find source bugs, keep the behavior, but mark them in the log and eventual report.
- Optimize for speed in the columnar version.
- Once done optimizing, build a wheel that can be installed in a stand-alone environment (e.g. without `asetup` or similar) and can run the corrections.
