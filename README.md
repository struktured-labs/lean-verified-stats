# lean-verified-stats

Open community research toward dataset-to-machine-checked statistical inference in Lean. The aim is to complement [mathlib](https://github.com/leanprover-community/mathlib4) and [StatLean](https://github.com/StatLean/Stat-Lean).

**Status: documentation and planning only.** This repository contains this note, the research specification, and a short contribution guide. It has no Lean sources, examples, Lake project, toolchain or dependency pins, scripts, datasets, or CI workflows. The four milestones in the specification are proposals. None is implemented. This repository states no schedule and no guarantee that a milestone will succeed.

Read the specification: [SPEC.md](SPEC.md). It records scope, architecture, milestones and acceptance criteria, the upstream contribution split, the trust boundary, open questions, and the provenance of sources that were actually inspected.

While the repository is still a plan, [CONTRIBUTING.md](CONTRIBUTING.md) says what a change may contain and how upstream contact is handled.

The repository name states a research aim. A future result is machine-checked only in the sense the specification defines: a Lean checker has accepted a proof, and the certificate states the assumptions of that proof.
