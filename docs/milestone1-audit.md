# Milestone 1 audit

Survey of mathlib and StatLean for a dataset-to-machine-checked inference path, and the gap judgment that follows.

| | |
| --- | --- |
| Observation date | 2026-11-06 |
| This repository | `struktured-labs/lean-verified-stats` at the documentation stage |
| Status | Read-only audit. No dependency adopted. No upstream pull request selected |
| Outreach | **Deferred. Not done.** |

## No external contact

This note does not post to Zulip, email, or any other project. It does not open or comment on issues or pull requests in mathlib, StatLean, or any other upstream repository, and it does not tag maintainers. Public GitHub and raw-file reads were used to inspect sources. The only publication intended from this work is a documentation pull request in this repository.

GitHub's ordinary behavior when that pull request is opened is to notify this repository's owner and existing watchers. That notification is the pull request itself. No review request, subscription, or external listener was added.

## Executive recommendation

Do not open a mathlib or StatLean pull request on the strength of this audit. No confirmed mathematical gap is both missing and a good first upstream patch.

Two statements that are easy to mistake for gaps are already in StatLean at the pinned revision: the sample-average identity for an empirical measure, and Beta–Bernoulli conjugacy (almost everywhere under the prior predictive). mathlib at the same pin already has the posterior kernel, the Beta measure, and a two-point Bernoulli measure. Current mathlib `master`, which is newer than the pin, also has an object named `empiricalMeasure`, but it is an orbit average for a dynamical system, not a sample empirical measure. Reusing that name for the statistical object would collide.

What is actually missing for milestones 2 and 3 is the companion bridge in this repository's own future scope: a versioned certificate, a parser with a denotation, and an executable rational check. That bridge is not an upstream mathematics patch. It is not implemented here.

StatLean and mathlib stay **unadopted**. The pinned StatLean sources were not built, `#print axioms` was not run, source copyright headers are not uniform, and the library repository has no Actions workflow that builds proofs. A root Apache-2.0 text does not close that review.

## What was checked, and what was not

| Check | Result |
| --- | --- |
| StatLean sources at `e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2` | Checked out read-only and searched |
| mathlib `v4.34.1` = `d13f23b723b8a846827a245b89c10fc7d3f11612`, directory `Mathlib/Probability` | 140 `.lean` files checked out and searched |
| mathlib `master` `8ae26cc6219b144662f6fa5198925d7c871f3373` | Not checked out as a tree. One file was read after a code-search hit, and the pin was checked for that path |
| `lake build`, `lean`, `#print axioms` | **Not run.** `lean`, `lake`, and `elan` are absent from this workspace (`command -v` found none) |
| Transitive proof-dependency closure | **Not run.** `Scripts/ExtractDeps.lean` is a post-build graph extractor; it was read, not executed |
| Repository-wide elaboration of `sorry` | **Not done.** A comment-stripping text scan is recorded below and is not a kernel check |
| Community discussion | **Deferred** |

No generated build directory, toolchain file, or Lake manifest is added to this repository.

## Revisions

| Project | Revision | Role |
| --- | --- | --- |
| StatLean | [`e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2`](https://github.com/StatLean/Stat-Lean/commit/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2) | The revision named in `SPEC.md`. Commit date on the commit object: 2026-10-14. Commit subject: "Upgrade Lean and Mathlib to v4.34.1 (#18)" |
| mathlib tag `v4.34.1` | [`d13f23b723b8a846827a245b89c10fc7d3f11612`](https://github.com/leanprover-community/mathlib4/commit/d13f23b723b8a846827a245b89c10fc7d3f11612) | StatLean's `lake-manifest.json` `inputRev` `v4.34.1` resolves to this commit. Tag object type `commit`, same SHA. Commit date 2026-09-24. Toolchain file at this commit: `leanprover/lean4:v4.34.1` |
| mathlib `master` | [`8ae26cc6219b144662f6fa5198925d7c871f3373`](https://github.com/leanprover-community/mathlib4/commit/8ae26cc6219b144662f6fa5198925d7c871f3373) | Default-branch tip observed 2026-11-06, commit date 2026-11-06. Used only to avoid calling a post-pin file absent. Not the comparison pin |

StatLean's manifest also records inherited packages at that lock: `plausible` `118aa17ee84656b8bd727fef7c458ee8c833385c`, `LeanSearchClient` `ddf04cf3949fa556442341e87d47f9f6e6074707`, `importGraph` `e928b72544873815af278d38681b31c0293588e3`, `ProofWidgets4` `106ff4fafc74ef4ac99d81dbf3ab399118f497a5`, `aesop` `355695d523e41d0554926416cba2a2b3544fbbc9`, `Qq` `6a489d9af5d0c47e5b259e2e8bcdfc1811b5a259`, `batteries` `f2effa3d803fda822b1f97b806c47cf2adfbcbc2`, `Cli` `e92c9f15fdfacc8536f31cfb3b7ad26c3c8cd204` (`inputRev` `v4.34.0`). Those license files were not read. `fixedToolchain` in the manifest is `false`.

## mathlib APIs at the pin

Scope of the file search: every `.lean` file under `Mathlib/Probability` at `d13f23b` (140 files). A string search there for `empirical`, `bootstrap`, `conjug`, `permutation test`, `randPValue`, and `pValue` returned no matches. Declarations below were read in the named files.

| Declaration | Location at `d13f23b` | Note |
| --- | --- | --- |
| `ProbabilityTheory.posterior` (`κ†μ`) | [`Mathlib/Probability/Kernel/Posterior.lean`](https://github.com/leanprover-community/mathlib4/blob/d13f23b723b8a846827a245b89c10fc7d3f11612/Mathlib/Probability/Kernel/Posterior.lean#L70) | `noncomputable`. Defined as the conditional kernel of `(μ ⊗ₘ κ).map Prod.swap`. Needs `StandardBorelSpace`, `Nonempty`, `IsFiniteMeasure`, `IsFiniteKernel` |
| `compProd_posterior_eq_map_swap` | same file | Characterizing property of the posterior. Uniqueness lemmas in that file are almost-everywhere |
| `posterior_eq_withDensity` | same file | Bayes form: almost everywhere in the data, under almost-everywhere absolute continuity |
| `posterior_eq_withDensity_of_countable` | same file | Still almost-everywhere in the data. The countable hypothesis is on the **parameter** space |
| `posterior_boolKernel_apply_true`, `posterior_boolKernel_apply_false` | same file | Two-point parameter, not a Beta prior |
| `beta`, `betaPDFReal`, `betaPDF`, `betaMeasure`, `lintegral_betaPDF_eq_one`, `isProbabilityMeasureBeta` | [`Mathlib/Probability/Distributions/Beta.lean`](https://github.com/leanprover-community/mathlib4/blob/d13f23b723b8a846827a245b89c10fc7d3f11612/Mathlib/Probability/Distributions/Beta.lean#L132) | `betaMeasure α β = volume.withDensity (betaPDF α β)`. Probability-measure instance needs `0 < α` and `0 < β`. This is the measure StatLean's conjugacy theorem names |
| `bernoulliMeasure` | [`Mathlib/Probability/Distributions/Bernoulli.lean`](https://github.com/leanprover-community/mathlib4/blob/d13f23b723b8a846827a245b89c10fc7d3f11612/Mathlib/Probability/Distributions/Bernoulli.lean#L47) | Two-point measure on a type, parameter in the unit-interval subtype `I`. This is not StatLean's `bernoulliKernel : Kernel ℝ Bool`, which clamps a real parameter |
| `binomial` and integral lemmas | `Mathlib/Probability/Distributions/Binomial.lean` | Including `binomial_one_eq_bernoulliMeasure` |
| Kernel composition, `withDensity`, disintegration, `condKernel` | `Mathlib/Probability/Kernel/` | The posterior is built on this layer |
| `IsBayesEstimator`, `IsArgminEstimator` | `Mathlib/Probability/Decision/BayesEstimator.lean` | Estimators that attain Bayes risk, written with the posterior kernel almost everywhere. Not a conjugate-update theorem |
| Other distributions in this directory | `Distributions/{Binomial,Cauchy,Exponential,Gamma,Gaussian,Geometric,Pareto,Poisson,SetBernoulli,TwoValued,Uniform}.lean` | Present as laws. No conjugacy file was found in the 140-file search |

Also present under `Mathlib/Probability` and only listed, not transcribed: independence, characteristic functions, moments and variance, the strong law, a central limit theorem module, martingales, conditional expectation, and product measures. They are the ambient probability library. They are not a certificate checker.

### A different `empiricalMeasure` on current master

GitHub code search on 2026-11-06 for `empiricalMeasure` in `leanprover-community/mathlib4` returned one path, `Mathlib/Dynamics/Ergodic/EmpiricalMeasure.lean` (`total_count` 1). That search indexes the default branch, not `v4.34.1`.

- At the pin, the contents API returns 404 for that path, and the directory `Mathlib/Dynamics/Ergodic` listing does not include the file.
- At `master` `8ae26cc6219b144662f6fa5198925d7c871f3373`, the file exists (blob `c85af70969c60b7789e19311f295e66556c03c7f`). [`MeasureTheory.empiricalMeasure`](https://github.com/leanprover-community/mathlib4/blob/8ae26cc6219b144662f6fa5198925d7c871f3373/Mathlib/Dynamics/Ergodic/EmpiricalMeasure.lean#L58) is the Birkhoff average of Dirac measures along the orbit of a map `f` starting at `x`, using `n + 1` iterates. [`integral_empiricalMeasure_eq_birkhoffaverage`](https://github.com/leanprover-community/mathlib4/blob/8ae26cc6219b144662f6fa5198925d7c871f3373/Mathlib/Dynamics/Ergodic/EmpiricalMeasure.lean#L71) equates its integral to that Birkhoff average. The module's theorem is Krylov–Bogolyubov existence of an invariant probability measure.

That is not `(1/n) ∑ᵢ δ_{xᵢ}` for an arbitrary finite sample, and the integral lemma is not the sample-average identity. The name collision is a reason not to propose StatLean's sample empirical measure to mathlib under the same declaration name.

The rest of mathlib `master` was not searched file by file. Absence claims below are about the pinned `Mathlib/Probability` tree plus this one master file, not a claim that every future master commit lacks a sample empirical measure.

## StatLean at the pinned revision

### Build targets and graph

[`lakefile.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/lakefile.lean) at this revision:

- Package version `v!"0.1.0"`.
- `require mathlib` at git `v4.34.1`.
- `doc-gen4` at `v4.34.1` only when Lake config `env` is `dev`. It is not the default target.
- Default target: `lean_lib StatLean`.
- One executable: `lean_exe deps`, root `Scripts.ExtractDeps`. The file's header says it reads `website/targets.txt` after `lake build` and writes JSON dependency graphs for the website.

[`lean-toolchain`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/lean-toolchain) is the single line `leanprover/lean4:v4.34.1`.

Root module [`StatLean.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean.lean) imports 17 area umbrellas: `AsymptoticStatistics`, `ConcentrationInequalities`, `HighDimensionalStatistics`, `Optimization`, `MultipleTesting`, `Minimaxity`, `Bayesian`, `NonparametricStatistics`, `PointEstimation`, `HypothesisTesting`, `StatisticalModels`, `TimeSeries`, `CausalInference`, `ExperimentalDesign`, `StatisticalLearning`, `ComputationalStatistics`, `RobustStatistics`. Each umbrella was not expanded into a compiled import closure.

The only `IO` `main` found under `*.lean` is `Scripts/ExtractDeps.lean`. No `.lean` file under the checkout matches `CSV` or `csv`. There is no dataset-to-certificate executable in the Lake targets that were read.

File counts from `find /tmp/audit/Stat-Lean -name '*.lean'` (a file count, not a theorem count). That listing had 1154 paths. The prefix histogram below sums to 1151; three paths were outside these prefixes. Neither number is a theorem count.

| Prefix | `.lean` files |
| --- | ---: |
| `StatLean/AsymptoticStatistics` | 390 |
| `StatLean/Bayesian` | 106 |
| `StatLean/ConcentrationInequalities` | 96 |
| `StatLean/HypothesisTesting` | 73 |
| `StatLean/StatisticalModels` | 72 |
| `StatLean/NonparametricStatistics` | 67 |
| `StatLean/PointEstimation` | 58 |
| `StatLean/TimeSeries` | 55 |
| `StatLean/HighDimensionalStatistics` | 32 |
| `StatLean/Minimaxity` | 31 |
| `StatLean/CausalInference` | 31 |
| `StatLean/RobustStatistics` | 29 |
| `StatLean/MultipleTesting` | 29 |
| `StatLean/StatisticalLearning` | 20 |
| `StatLean` (root module and area umbrellas in that bucket) | 17 |
| `StatLean/ExperimentalDesign` | 15 |
| `StatLean/ComputationalStatistics` | 15 |
| `StatLean/Optimization` | 14 |
| `Scripts` | 1 |

These numbers do not measure how much of the library elaborates, and they are not a count of proved theorems. The README's topic list and any website result counts were not used as evidence of coverage.

### Declarations relevant to the specification

Permalinks use the pinned SHA.

| Declaration | File | Role |
| --- | --- | --- |
| `empiricalMeasure`, `mcEstimate` | [`ComputationalStatistics/Core/Defs.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/ComputationalStatistics/Core/Defs.lean#L63) | `(1/n)` times a finite sum of Diracs. `noncomputable`. The module comment says `n = 0` yields the zero measure |
| `integral_empiricalMeasure`, `lintegral_empiricalMeasure` | [`ComputationalStatistics/Core/EmpiricalMeasure.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/ComputationalStatistics/Core/EmpiricalMeasure.lean#L74) | Integral against that measure equals `mcEstimate`, for a strongly measurable integrand |
| `empiricalMeasure` | [`AsymptoticStatistics/EmpiricalProcess/EmpiricalMeasure.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/AsymptoticStatistics/EmpiricalProcess/EmpiricalMeasure.lean#L74) | Another sample empirical measure, in the van der Vaart vocabulary. The computational-statistics module comment says these coexist on purpose |
| `empiricalMeasure` | [`HypothesisTesting/Bootstrap/Defs.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/HypothesisTesting/Bootstrap/Defs.lean#L58) | A third sample empirical measure, for the bootstrap development |
| `empiricalCDF`, `empRisk` | `AsymptoticStatistics/EmpiricalProcess/EmpiricalDistribution.lean`, `MultipleTesting/ForMathlib/EmpiricalCDF.lean`, `StatisticalLearning/Core/Defs.lean` | Related finite-sample averages, different names |
| `bootstrap_linearStatistic_expectation`, `bootstrapMean_variance` | `ComputationalStatistics/Resampling/BootstrapMoments.lean` | Conditional-on-the-data bootstrap identities, using `integral_empiricalMeasure`. The header separates the ideal expectation from a Monte Carlo approximation |
| `betaMeasure` is mathlib's | imported through the conjugacy development | Not redefined as the Beta law |
| `successes`, `bernoulliDensity`, `bernoulliKernel` | [`Bayesian/Conjugacy/Defs.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/Bayesian/Conjugacy/Defs.lean#L61) | `successes` is computable (`∑ (y i).toNat`). The kernel and density are `noncomputable` |
| `generalizedPosterior_eq_of_withDensity_eq_smul` | [`Bayesian/Conjugacy/Criterion.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/Bayesian/Conjugacy/Criterion.lean#L45) | **Pointwise** in the data point `x`: if the unnormalized posterior equals `c • ρ` with `c` finite and nonzero and `ρ` a probability measure, the generalized posterior at that `x` is `ρ` |
| `posterior_ae_eq_of_withDensity_eq_smul` | [same file](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/Bayesian/Conjugacy/Criterion.lean#L68) | Lifts a pointwise recognized form to `(κ†π) x = ρ x` for **predictive-almost every** `x` |
| `beta_bernoulli_posterior_ae` | [`Bayesian/Conjugacy/BetaBernoulli.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/Bayesian/Conjugacy/BetaBernoulli.lean#L128) | For real `α, β > 0`, almost every `y` under the prior predictive has kernel posterior `betaMeasure (α + successes y) (β + (n - successes y))`. The proof's `intro y` block builds the recognized-form hypothesis used by the almost-everywhere criterion. There is no separate exported theorem quantified by `∀ y` |
| `beta_bernoulli_posteriorPredictive_true_ae` | same file | Posterior predictive of one more success, almost everywhere |
| `beta_binomial_posterior_ae`, `gamma_poisson_posterior_ae`, `normal_normal_posterior_ae`, `dirichlet_multinomial_posterior_ae`, `expFamily_iid_posterior_ae` | sibling files in `Bayesian/Conjugacy/` | Further conjugate updates, also in the `_ae` form. Theorem-line search of that directory found no pointwise `forall` variant of these posterior identities |
| `predictiveDensity_pos_ae` | `Bayesian/Dominated/PredictiveDensity.lean` | Positivity of the predictive density almost everywhere. Not a lemma that every function `Fin n → Bool` has positive prior-predictive mass |
| `randPValue` | [`HypothesisTesting/Randomization/Defs.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/HypothesisTesting/Randomization/Defs.lean#L109) | `noncomputable`. Inclusive upper tail over a finite group: proportion of `g` with `T x ≤ T (g • x)`. Multiplicity is the number of group elements |
| `RandomizationHypothesis` | same file | `P.map (g • ·) = P` for every group element |
| `superUniform_randPValue` | [`HypothesisTesting/Randomization/ExactLevel.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/HypothesisTesting/Randomization/ExactLevel.lean#L520) | Under measurability and the randomization hypothesis, the p-value is superuniform. A text search finds this theorem defined once and mentioned from `CausalInference/Randomized/Fisher.lean`; it is not applied to `twoSampleMeanDiff` in the hits |
| `randDist_eq_condExp_orbit` | `HypothesisTesting/Randomization/OrbitConditional.lean` | Conditional-on-the-orbit description of the randomization distribution |
| `twoSampleMeanDiff`, `twoSampleLaw` | [`HypothesisTesting/Randomization/TwoSamplePermutation.lean`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/StatLean/HypothesisTesting/Randomization/TwoSamplePermutation.lean#L125) | Scaled difference of means. Lemmas identify `randDist` of this statistic with a permuted block sum, on the way to a central limit theorem. Empty group sizes are a documented junk corner of real inversion. This file does not export an exhaustive combination counter |
| `prob_fisherPValue_le`, `sharpNull_prob_fisherPValue_le` | `CausalInference/Randomized/Fisher.lean` | Design-based superuniformity for a finite-population Fisher p-value. A different null from the group-invariance theorem, and also not a CSV enumerator |
| `signScore` | `CausalInference/Sensitivity/Rosenbaum.lean` | A sign-score sum whose module comment says the score `q` can be chosen as a sign test, a paired `t`, or Wilcoxon's signed-rank statistic. That is not a two-sample Wilcoxon permutation procedure and not an executable rank enumerator |
| Metropolis–Hastings reversibility | `Bayesian/MCMC/MetropolisHastings.lean` | Countable state space, detailed balance and stationarity. The header defers Harris recurrence and ergodic theorems. This is theory about a kernel, not a checked sampler trace |

`RandomizationHypothesis` occurs as a definition and as a hypothesis of the exact-level and orbit-conditional theorems. No hit in this search was a lemma proving it for `twoSampleLaw` under equal marginals.

### Text scan for `sorry`, `admit`, and `axiom`

A Python scan over `*.lean` under the checkout stripped block comments and line comments, then looked for the tokens `sorry`, `sorryAx`, `admit`, and a line-starting `axiom`.

- One code-token hit: the identifier ``sorryAx`` inside the exclusion list in `Scripts/ExtractDeps.lean`. That list is not a proof.
- No `axiom` declaration line was found by a separate search for `^axiom `.
- A raw substring search for `sorry` hit 99 files and 164 lines. The matches inspected are comments (claims that a file is sorry-free, historical notes, or the English verb "admits"). Those comments are not kernel evidence. Several files describe themselves as "axiom-clean"; this audit did not check that with `#print axioms`.

The scan can miss a `sorry` hidden by a comment syntax it mishandles. It says nothing about `sorry` inside mathlib or the other locked packages, because those packages were not checked out. It is not a build.

### Licenses

This is a reading of notices, not a decision that reuse is allowed.

- [`LICENSE`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/LICENSE) is the Apache License 2.0 text. Its appendix contains the line `Copyright 2024 Junwei Lu`. GitHub's repository metadata field `license.spdx_id` for this repository was `NOASSERTION` on 2026-11-06. The metadata field and the file are not the same fact; the file was read, the SPDX field was only the API value.
- Source headers are not uniform. Examples that were opened: `HypothesisTesting/ForMathlib/GaussianShell.lean` says `Copyright (c) 2026 Junwei Lu` and points at `LICENSE`; `PointEstimation/ForMathlib/MeasureMap.lean` says `Copyright (c) 2026 StatLean contributors` and points at `LICENSE`; `AsymptoticStatistics/ForMathlib/WeakConvergence/Outer.lean` says `Copyright (c) 2026` with no name; `Bayesian/Conjugacy/BetaBernoulli.lean` has no copyright lines in its opening. A search for `Copyright` or `SPDX` hits many files and does not hit every file.
- [`LICENSES/formal-learning-theory-kernel-Apache-2.0.txt`](https://github.com/StatLean/Stat-Lean/blob/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2/LICENSES/formal-learning-theory-kernel-Apache-2.0.txt) is another Apache-2.0 text. It cites Gupta, D. (2026), Formal Learning Theory Kernel, and the repository `Zetetic-Dhruv/formal-learning-theory-kernel`, and it asks for citation when the work is reused. `AsymptoticStatistics/ForMathlib/ChoquetCapacity/Analytic.lean` says it adapts that project's `ChoquetCapacity.lean` at commit `b1b9d16a552e3e09bfbb8151fe6aa14c805d7979`, copyright 2026 Dhruv Gupta, under that license file. The upstream file at that commit was not diffed against the copy.
- mathlib's `LICENSE` at `d13f23b` is the Apache-2.0 text. GitHub's SPDX field for mathlib was `Apache-2.0`. The license appendix still has the placeholder `{yyyy}` / `{name of copyright owner}`. Individual mathlib modules carry their own copyright headers. Transitive package licenses in the manifest were not opened.

No license is added or changed in this repository. Nothing here is an adoption of StatLean or mathlib.

### Maintenance

The pinned snapshot is a historical revision. Separately, the default branch was observed on 2026-11-06:

- The commits API's newest entry is `e436d8a` itself, committer date 2026-10-15. `pushed_at` on the repository object was `2026-10-15T01:21:48Z`. `updated_at` was `2026-11-04T23:14:22Z` (repository metadata, not a new commit).
- Earlier 2026 history on that list includes commits in August and September (areas landing, a website team-page edit, contribution docs, an asymptotic-statistics synchronization, an Efron–Stein commit). That is recent activity relative to a multi-year abandonment, and it is also three weeks without a new commit before this audit. Three quiet weeks are not an abandonment proof.
- Releases API returned length 0.
- Actions workflows on `StatLean/Stat-Lean`: `total_count` 0. The `.github` directory at the pin contains `pull_request_template.md` and no workflow file.
- The README badge labeled "GitHub CI" points at `StatLean/statlean.github.io` workflow `deploy.yml`, not at a proof build of the library. That website repository's workflow list on 2026-11-06 was "Build and deploy StatLean site", "Deploy website (fast)", and `pages-build-deployment`. Those are site jobs. They were not opened and they were not treated as evidence that the Lean library builds.
- The issues API, which returns issues and pull requests together, listed three open items. Checked individually: item 20 is an open pull request; item 21 is an open issue, updated 2026-10-26. Item 19 was not classified. Bodies were not read, and nothing was posted.

Judgment: **uncertain**. The project was active into October 2026, has open tracker items, has no release, and does not show a library proof CI. Activity on the tracker is not a support commitment. An academic affiliation is not recorded here and would not be a support commitment if it were.

## Gap judgment

Confidence is about the search that was actually run. "Missing" means not found in that search, not a proof of absence from every branch of every repository.

### Rejected as novel mathematics

**Sample empirical integral.** StatLean already has `integral_empiricalMeasure` for `ComputationalStatistics.empiricalMeasure`, and it has further sample empirical measures in the asymptotic and bootstrap areas. mathlib at the pin has `Measure.dirac` and integrals against Dirac measures, which is the material the StatLean proof uses. Current mathlib `master` has a different `empiricalMeasure`. Proposing the sample identity as a new StatLean theorem would duplicate it. Proposing it to mathlib under that name would collide with the orbit measure and would be a thin wrapper over Dirac sums. **No pull request.**

**Beta–Bernoulli conjugacy.** `beta_bernoulli_posterior_ae` is in StatLean. mathlib already supplies `betaMeasure` and `posterior`. The conjugacy theorem is a statistics-shaped application of StatLean's criterion, not a missing generic kernel lemma. **No pull request that presents it as new.**

### Not recommended as a first pull request

**1. Named pointwise generalized posterior for every binary sample.**

- Future home, if written at all: StatLean, beside `beta_bernoulli_posterior_ae`. Not mathlib: the generic pointwise normalization principle is already `generalizedPosterior_eq_of_withDensity_eq_smul`.
- Why it is not already the exported theorem: the theorem lines in `Bayesian/Conjugacy/*.lean` export `beta_bernoulli_posterior_ae` (almost everywhere, for the kernel posterior `†`) and the predictive almost-everywhere theorem. The criterion file separately exports a pointwise statement about `generalizedPosterior`, not specialized to Beta–Bernoulli.
- Why it may be short: the almost-everywhere proof applies the criterion, whose hypothesis is a recognized form at **every** `y`, and the proof contains an `intro y` block that builds that form from `betaPDF_mul_clampPow`. This audit did not re-elaborate that block, so "short corollary" is a reading of the source, confidence **medium**. Confidence that no exported `∀ y` Beta–Bernoulli kernel-posterior theorem was found is **high** for this revision's conjugacy directory.
- Why the countable Bayes lemma does not close it: `posterior_eq_withDensity_of_countable` quantifies countability on the parameter space. The Beta parameter is a real. The data space `Fin n → Bool` being finite does not match that hypothesis.
- Why full support was not claimed: `predictiveDensity_pos_ae` is almost-everywhere. A search under `StatLean/Bayesian` for `measure_singleton` and predictive positivity did not find a theorem that every boolean sequence has positive prior-predictive mass. That search was not library-wide.
- Acceptance criterion if it is later written: for all `n`, all `y : Fin n → Bool`, and all real `α, β > 0` satisfying the same side conditions as the existing theorem, an exported equality identifies a named posterior object at that `y` with `betaMeasure (α + successes y) (β + (n - successes y))`. The statement says whether the object is `generalizedPosterior` or the kernel posterior `†`. An almost-everywhere theorem is not reworded as this equality. The proof is checked by elaboration, which this audit did not do.
- This is not the executable rational mean, and it does not discharge conditional i.i.d. Bernoulli or the Beta prior.

**2. Specializing the existing randomization p-value to the two-sample mean difference.**

- Already present: `randPValue` and `superUniform_randPValue` for an arbitrary finite group and a measurable statistic, inclusive upper tail, ties counted by how many group elements hit the value. `twoSampleMeanDiff` is already tied to `randDist` for asymptotic theorems. Fisher and Rosenbaum developments are further finite-population randomization results.
- Not found: a theorem whose conclusion is `superUniform_randPValue` at `G = Equiv.Perm (Fin (m+n))` and `T = twoSampleMeanDiff`, or a lemma that `twoSampleLaw` with equal marginals satisfies `RandomizationHypothesis`. Also not found: a computable enumerator of combinations with a proved equality to that group p-value. The group p-value sums over permutations; a combination count is the same ratio only when within-group multiplicity cancels. That cancellation was not found as a named lemma.
- Future home, if written: StatLean, as a specialization of its own randomization API. mathlib portability of the generic group theorem is a design question for a later human discussion, which this audit does not start. It is not a missing theorem.
- Acceptance criterion: a theorem that assumes only the hypotheses it names (group invariance, or finite exchangeability that yields it), applies to the difference of means with `m, n ≥ 1`, uses the inclusive tail, and is elaborated. A central limit theorem is not this result. An executable counter of combinations may accompany it in the companion; the executable counter is not by itself the superuniformity proof.
- Wilcoxon and rank statistics stay out of this criterion. `signScore` does not meet it.

### The gap that is real, and is not upstream mathematics

**Companion certificate bridge**, future home this repository, after a dependency decision that this audit does not make.

StatLean's relevant definitions are `noncomputable` measure-theoretic objects, except for arithmetic such as `successes`. The Lake project has no parser and no checker. Milestone 2's rational posterior parameters and mean, milestone 3's exhaustive count on rational data, and the versioned record of bytes, hash, schema, and proposal are absent because this repository has not started them, not because mathlib lacks a posterior.

Acceptance criterion, unchanged in spirit from `SPEC.md`, and still not satisfied by any file in either upstream tree that was read:

- A pure function from bytes to a parse result is the function named by the theorem.
- Counts are computed from the parsed records. A stored count that disagrees is a rejection.
- For Beta–Bernoulli, the rational mean equals `(α + s) / (α + β + n)` with `s` and `n` from those records, and a separate theorem, with explicit likelihood and prior, identifies that arithmetic with a posterior mean. Almost-everywhere upstream lemmas stay labeled almost-everywhere until a pointwise lemma exists.
- For the permutation example, the p-value's denominator is the number of allowed assignments, ties use the stated multiplicity, and superuniformity cites an explicit null.
- Rounding, deterministic approximation bias, and Monte Carlo uncertainty stay distinct, and none of them is treated as proof that the model matches the data. No approximation is selected by this audit.
- Python, R, Stan, and PyMC output remain untrusted proposals. Importing a file is not a posterior theorem.

## Deferred questions

- Whether any small generic lemma should move to mathlib. No candidate here cleared both "missing" and "API-appropriate." The orbit-versus-sample name collision makes an empirical-measure move a poor candidate.
- Whether the pointwise corollary or the two-sample specialization is worth a StatLean patch later. Both look like specializations of existing theorems. Outreach that would ask maintainers is deferred.
- The axiom footprint of any declaration this project might import, including transitive mathlib axioms.
- Whether the prior predictive charges every binary sequence, which would be one route from the almost-everywhere kernel posterior to a statement about one file.
- Dependency adoption, license headers, and the third-party Choquet-capacity notice.
- Protocol evolution and ecosystem lock-in, recorded in `SPEC.md` rather than by choosing a producer here.
- Hamiltonian Monte Carlo, variational inference, and a standalone Wilcoxon permutation test. Not found as the procedures in the specification, and not proposed.

## Search record

Commands were run locally on the checkouts unless noted. They are search scope, not a certificate of completeness.

1. StatLean checkout of `e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2` (full files at that commit).
2. `rg` of theorem and definition lines for posterior, empirical, Bernoulli, permutation, bootstrap, and p-value names under `StatLean/`.
3. `rg '^theorem |^lemma |^noncomputable def |^def '` in `StatLean/Bayesian/Conjugacy/*.lean`.
4. `rg` for `superUniform_randPValue`, `RandomizationHypothesis`, `generalizedPosterior_eq_of_withDensity_eq_smul`, `twoSampleMeanDiff`, `wilcoxon`, `CSV`, and `\bIO\b`.
5. Comment-stripping Python scan for `sorry` / `admit` / `axiom` tokens, described above.
6. mathlib sparse checkout of `Mathlib/Probability` and `lean-toolchain` at `d13f23b`. String search for the empirical, bootstrap, conjugacy, and p-value vocabulary across those 140 files: no matches. Direct reads of `Posterior.lean`, `Beta.lean`, `Bernoulli.lean`, `Binomial.lean`, and the Bayes-estimator module header.
7. A `git grep` of the entire pinned mathlib commit was started and **killed**. A partial clone began fetching the whole repository and auto-packing it. That command returned no matches and is not evidence of absence.
8. GitHub code search `empiricalMeasure` in `leanprover-community/mathlib4` (default-branch index): one file. Two further code searches (`def betaMeasure`, `def posterior`) returned HTTP 429 and were not repeated. The pin's Beta and posterior files had already been read locally.
9. Contents API for `Mathlib/Dynamics/Ergodic/EmpiricalMeasure.lean` at the pin (404) and at `8ae26cc` (present). Raw file read at `8ae26cc` for the declaration and the integral lemma.
10. GitHub API reads, not writes: StatLean commits, repository object, releases, Actions workflow list, issues 20 and 21; mathlib tag `v4.34.1`, master tip, license SPDX field; website-repo workflow names.

## Consistency with the specification

This audit does not change milestones 2–4, the trust split between kernel, parser, IO, compiler, and randomness, or the separation of rounding, approximation bias, Monte Carlo error, and model error. It records that the mathematical ingredients those milestones would cite are largely already formalized, almost everywhere or at the level of a finite group, and that the executable and certificate layers are not. Dependencies stay unpinned in this repository.
