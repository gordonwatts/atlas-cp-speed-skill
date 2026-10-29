# Updating this skill from another chat

Maintain reusable ATLAS conversion rules. Campaign results, implementation histories, host paths and speaker-specific decisions belong in project reports and outlines, not in this skill.

When asked to add another chat's lessons:

1. Read the current skill and applicable references; inspect the chat's actual source/results when available.
2. Separate a reusable decision rule from a calibration-specific observation, environment workaround or proposed design. Mark proposals as unimplemented.
3. Put broadly applicable workflow changes in SKILL.md; keep reusable ATLAS numerical traps in the appropriate reference and task-specific context/results in project reports. Add a new reference only when there is a distinct substantial topic, and link it with a clear reading trigger.
4. Resolve conflicts using source/version/configuration evidence. Preserve differing behavior when releases differ; do not silently replace an older verified fact with an untested generalization.
5. Do not append entire transcripts, duplicate implementation code, hard-code new sample settings into the general workflow, or claim the skill is fully tested because a historical demonstration passed.
6. Validate frontmatter and links with the installed skill-creator validator where available. If adding executable helpers, exercise meaningful behavior in an isolated workspace. Document limits of any dry-run review.
7. Review the diff and make a coherent local commit under the repository working agreement, leaving unrelated work untouched. Do not push.

Useful scenario checks during final review:

- A fresh calibration uses text coefficients, interpolated ROOT tables and a model file. Does the skill prompt complete dependency tracing rather than copying only the tables?
- Two rewritten paths agree but both disagree with the original tool on a boundary. Does the workflow retain the original authority and diagnose the first divergence?
- The requested uncertainty model is unavailable. Does the agent disclose the supported model rather than claim full coverage?
- A graph needs explicit float rounding and shared intermediates. Does the design use existing nodes or external shared code, recording a useful new node only as a future opportunity?
- The reference lacks status branches. Does the report limit its claim and seek independent probes?
- A diagnostic columnar workflow is faster/slower than an earlier framework job. Does the report account for timing scope and variability before attributing the difference to the evaluator?
- A replacement is slower and loads a large payload. Does the agent distinguish fixed cost from sample-dependent work, verify the libraries in component-isolation jobs, and retain per-run validation?
- A scratch optimization matches sample hashes and reduces kernel time by 40%. Does the agent preserve its experimental status, validate branch/random semantics, and avoid promising a 40% full-workflow improvement?
- Named pipelines call a shared nominal graph for every variation. Does the agent distinguish reuse of the definition from reuse of computed nominal values?
- A file contains a NumPy prototype and a custom C++ wrapper. Does the agent follow actual imports and bindings before explaining which implementation runs?
- Missing-primary-object handling exists in a graph, but extraction indexes an empty associated collection first. Does validation include the adapter boundary?
- A talk follows the migration. Does it begin with the original reference and actual question, use the latest accepted evidence, distinguish numerical parity from visual overlays, label unmatched timing scopes, and keep draft visuals provisional?
- A user asks for a transferable local-wheel demo. Does the script read the intended original file directly, include the complete requested states, keep reference fixtures in separate acceptance checks, and disclose the platform, download needs, exact command and output schema?
- A user asks whether inference was optimized out of CorrectionLib. Does the explanation distinguish the original architecture from later caching/buffer changes, and inference from efficiency-table lookup?
- A candidate uniform encoding changes only unused tables and the new job happens to run faster than last week's number. Does the agent report active-node coverage and use a fresh unchanged control rather than claim a lookup speedup?
- A caller changes model, order or chunking, or mutates cached arrays. Do cache rules preserve dependencies and ownership, and do no-allocation tests verify the optimized path rather than a staging-header fallback?
- A user requests an outline now and a deck in another chat. Does the handoff contain validated source links, working versus illustrative code, major-change slides and smaller-change backups, without silently creating the deck or overwriting newer speaker edits?

Run expensive full physics validation only when required by the task or changed implementation. Skill wording edits alone do not justify rerunning the entire demo.

For deck-building updates, also check these scenarios:

- The speaker asks whether external inference is called by CorrectionLib. Does the explanation trace real callers and data flow, starting at the public entry point, rather than repeat a dependency list?
- A histogram slide contains diagnostic recording and validation helpers. Does the revision show loading and a clear fill loop, label its adaptation and omissions, and distinguish kinematic from weight-only systematics?
- The speaker manually edited the deck after the outline was written. Does the revision preserve those edits, use native hanging lists and equations, and disclose a renderer that cannot verify the equations?
- A talk needs commit links but only source publication was authorized. Does the workflow preserve implementation history, exclude presentation assets, distinguish local from public references, and avoid claiming a standalone repository is a fork?
