# Research specification

Dataset-to-machine-checked statistical inference, as an open companion to mathlib and StatLean.

| | |
| --- | --- |
| Repository | [struktured-labs/lean-verified-stats](https://github.com/struktured-labs/lean-verified-stats) |
| Status | Documentation and planning only |
| Date of this text | 2026-11-06 |
| Implementation | Not started |
| Milestones | Proposed. None is implemented |
| First upstream pull request | Not chosen |

This file was assembled on that date from the owner's planning brief, with an agent drafting the prose. It is a plan for the owner to review. It contains no proofs, no Lean sources, and no dependency pins.

The repository name states a research aim. "Machine-checked" in this document means: the Lean kernel accepted a proof, and the certificate names the assumptions of that proof. It does not mean that the assumptions are true of a market, a laboratory, or a file whose provenance the checker never saw.

## 1. Vision

The project is open community research toward a path from a dataset to a statistical conclusion that a proof checker can audit. It is meant to complement mathlib and StatLean. It is a public plan with room for iterative work by the owner, by agents, and by other contributors. It is a research-scale effort: each formal statement stays small enough that the owner understands it, including every proof offered in the owner's name.

A running theme is the gap between arithmetic and a statistical model. Updating a count, hashing a file, or printing a posterior mean is a computation. A theorem about a probability measure has hypotheses. Those hypotheses are part of the result. They are not discharged by the computation that implements one formula inside them.

## 2. Scope

The long-term scope is a documented chain with four kinds of piece:

1. **Mathematical theorems** about measures, kernels, posteriors, conjugacy, permutation and randomization, empirical measures, and dependence. These land upstream when they are genuinely missing and the API is appropriate (section 5).
2. **Executable finite representations** of the special cases where a sufficient statistic, a count, or a rational functional is computable, even if the surrounding measure or density is not.
3. **A certificate boundary** in which an untrusted program proposes an answer and a Lean checker recomputes the sufficient statistics from the recorded observation bytes and accepts or rejects the proposal against a proved specification.
4. **Demonstrations** on synthetic or clearly public data, including finance-shaped examples, once a theorem exists whose hypotheses match the story being told.

The concrete path proposed here is four milestones: survey and alignment, one exact Beta–Bernoulli certificate, one exact permutation test, then approximation methods that stay honest about error. Section 8 states deliverables and acceptance criteria. Sections 9 and 10 state what is deliberately unresolved.

## 3. Non-goals

The following are outside this project.

- Replacing mathlib or StatLean, or maintaining a private fork of their statistical theorems.
- Treating this repository as the home for generic measure theory.
- Choosing Lean, mathlib, or StatLean versions in this planning stage.
- Opening an upstream pull request whose topic was not shown to be missing.
- Claiming that Beta–Bernoulli conjugacy, or the identity "integral against the empirical measure equals the sample average," is a new result. Both are present in the StatLean revision inspected in section 11.
- Treating an incremented count as a proof of conjugacy or of model adequacy.
- Treating a hash or a count as a proof of where data came from or of what the columns mean.
- Treating imported Stan or PyMC draws as verified posterior samples merely because a file was parsed.
- Claiming that Hamiltonian Monte Carlo or variational inference is supported.
- Claiming that a rank test or a Wilcoxon statistic is already implemented. Those are optional later extensions of milestone 3.
- Putting secrets or customer market data in examples, issues, or history.
- Announcing schedules, completeness, or success probabilities for the milestones.
- Contacting upstream communities as a side effect of editing this file. A Zulip discussion is proposed in milestone 1 and has not been posted.

## 4. Current status

| Artifact | State in this repository |
| --- | --- |
| This specification, README, contribution guide | Present |
| Lean modules, examples, and proof scripts | Absent |
| `lakefile`, `lean-toolchain`, `lake-manifest`, dependency pins | Absent |
| CI workflows, axiom audits, `sorry` scans | Absent |
| Datasets, CSV fixtures, certificate files | Absent |
| Upstream pull requests from this effort | None |
| Zulip or email sent by this effort | None |
| Milestones 1–4 | Proposed only |

Future scaffolding is described in section 8.5 so that implementers inherit a policy. Describing it does not create it. A reader who looks at the tree and finds only Markdown should believe the tree.

When a milestone later starts or finishes, an edit to this section says so. Until that edit, the status line stays "documentation and planning only."

## 5. Architecture

Two upstream libraries and one future companion have different jobs.

**mathlib** receives generic theorems about measures, kernels, posteriors, conjugacy, permutations, empirical measures, and dependence, and only when two conditions hold: a search shows the statement is actually missing, and the statement fits mathlib's API and style. A lemma that is really a statistics-textbook special case, or a reparsing of an existing StatLean theorem, stays out of mathlib.

**StatLean** is the statistics library to reuse, audit, and coordinate with. The default is to depend on a reviewed StatLean surface, or to contribute a missing piece there, rather than to copy its developments into this repository. Copying is a last resort after the audit in milestone 1 records a reason (license incompatibility, an unreviewed `sorry` on the needed surface, or a maintained-status decision). No such reason is recorded yet, and no dependency is adopted yet.

**The companion**, meaning the future Lean package in this repository, holds the executable finite layer: parsers, canonical certificate formats, a command-line checker, versioned schema identifiers, adapters that let Python or R emit proposals, and demonstrations. It calls upstream theorems for the measure-theoretic content. It does not reimplement those theorems in order to own them.

```text
untrusted producer          companion checker              upstream mathematics
Python / R / other    -->   parse bytes                    mathlib kernels,
proposes a result           recompute statistics           measures, posteriors
                            check a certificate      -->   StatLean probability
                            accept or reject               results, after audit
```

The producer is untrusted. Acceptance is a successful check against a specification that has a proof. Malformed, stale, and mismatched inputs are specified rejections.

### 5.1 What is computable

Several objects in the inspected developments are `noncomputable` measure-theoretic definitions. That is expected. Milestone 2 can still execute rational arithmetic on sufficient statistics.

For Beta–Bernoulli, the executable objects are the counts computed from the observation list and the posterior parameters and posterior mean as rational numbers. The Beta density and the Beta measure may remain noncomputable, including where they depend on the Gamma or Beta function. A theorem may still identify the mathematical posterior with that measure after the coercion from rationals to reals. Executing the mean does not execute the density.

The same split applies later. An exact permutation p-value on rational data is a finite count. A Gaussian approximation to that p-value is a different object and belongs to milestone 4.

### 5.2 Certificate record

The planned certificate is a versioned, explicit, canonical format. A checker that does not yet exist will require at least these fields:

| Field | Role |
| --- | --- |
| Format version | Unknown versions are rejected |
| Schema identifier | Names the observation grammar and the statistical procedure |
| Raw input bytes | The observations and declared prior or design, as bytes |
| Hash | Hash of those bytes, with the algorithm fixed by the format version |
| Prior or design parameters | For example positive rational Beta parameters, or the two group labels |
| Procedure | Which statistic and which tail or functional |
| Proposal | The producer's claimed output |
| Certificate payload | Whatever the checker needs beyond the bytes themselves |

The checker parses the raw input bytes with the function the specification describes, recomputes sufficient statistics from the parsed observations, and compares the proposal to that recomputation or to a proved bound. A hash binds the certificate to a byte string. It does not say who produced the bytes, and it does not give the bytes a meaning. Meaning is the parser's denotation. A count stored beside the bytes is a claim about the bytes; the checker repeats the count from the records and rejects a mismatch.

Metadata about provenance (a file name, a vendor, a time range) may be stored and must be labeled untrusted. The checker does not turn that metadata into a hypothesis discharge.

Imported theorem statements are data. The checker trusts a result because it elaborated a Lean proof in the development under review, not because a file asserted a theorem name.

### 5.3 Demonstrations

Finance-shaped demonstrations are in scope only as illustrations of a certificate whose hypotheses are written down. A series of prices does not become independent, identically distributed, stationary, or exchangeable by being placed in an example. Synthetic series are the default. Public series, if used later, need a license that allows redistribution, and the example text states what was not proved about them. Customer market data is out of scope.

## 6. Upstream contribution split

| Work | Where it goes |
| --- | --- |
| A generic theorem about measures, kernels, posteriors, conjugacy, permutations, empirical measures, or dependence, shown to be missing, with an API that fits | mathlib, as a small pull request |
| A statistics result in the style of an existing StatLean area, or a repair to one | StatLean, after the milestone 1 audit and coordination |
| Docstrings, naming, lint, build, or CI fixes upstream | The upstream repository, in a small reviewable change, with license attribution preserved |
| Executable parsers, CSV and other input grammars, CLI, certificate formats, Python or R adapters, demonstrations | This companion, once implementation of that milestone has started |
| A duplicate of `integral_empiricalMeasure` or of Beta–Bernoulli conjugacy | Nowhere. They are already in the inspected StatLean revision. The companion may *use* them after an audit |

Pull requests are small. A change that mixes a new mathematical definition, a refactor, and a stylistic sweep waits and is split.

Community norms for mathlib, as published on 2026-11-06 and to be re-read at the time of any pull request:

- [How to contribute to mathlib](https://leanprover-community.github.io/contribute/how-to-contribute.html), including small self-contained pull requests, discussion on Zulip when a design needs it, and a `master` branch that CI keeps free of `sorry`.
- [Style guide](https://leanprover-community.github.io/contribute/style.html).
- The [contribution landing page](https://leanprover-community.github.io/contribute/) section on AI use. As published on that date, AI assistance is disclosed in the pull-request description, naming the tools and how they were used; a substantial amount of LLM-generated code is marked with the `LLM-generated` label; the human author understands the content and can justify the design without an AI; and LLM-written comments on GitHub or Zulip are not accepted. The page also describes reviewer responses to low-quality AI pull requests. The policy is the page's, and it can change. This paragraph is a pointer, not a substitute for reading it.

The owner understands proofs that are offered in the owner's name. Agent drafts are allowed in this research effort under that condition, with the disclosure above when the target community requires it. The same disclosure applies to companion-library code once any exists.

License attribution travels with reused code. Milestone 1 records the license of each adopted surface before adoption. This planning repository has not chosen a license for future code; choosing one is part of starting implementation, not part of this document's commit.

## 7. Trust boundary

The producer of a proposal may be Python, R, or another program. It is outside the trusted base. It may suggest counts, posterior parameters, p-values, sampler traces, or bounds. The Lean development accepts a suggestion only by checking it.

### 7.1 What a successful check can establish

Depending on the milestone and the theorem, a successful check can establish one of these:

- The proposal's rational arithmetic matches the recomputation from the parsed observations (milestone 2).
- The proposal's counts, statistic, and inclusive-tail p-value match an exhaustive enumeration of the allowed assignments (milestone 3).
- A numerical claim lies inside an enclosure produced by a checker whose correctness is proved, including any rounding the proof accounts for (milestone 4).

In each case the statistical reading — "this is the posterior," "this p-value is valid under the null" — is the theorem's statement, and it holds when the theorem's hypotheses hold. The checker enforces the computational side and records the hypotheses. It does not discover that a market satisfied them.

### 7.2 What a successful check does not establish

- That the bytes are the measurements someone claims they are. A hash commits the certificate to bytes. Provenance is an assumption.
- That column meanings match the schema, except as far as the parser's syntactic checks go. A column of zeros and ones is binary data. It is a sequence of Bernoulli trials only under an extra modeling hypothesis.
- That a likelihood, a prior, exchangeability, or independence holds.
- That an arbitrary file of Stan or PyMC samples is a set of draws from the posterior. Those files are summaries the checker can store, display, or test against a *proved* property. They become verified posterior draws only under an additional correctness argument that this plan does not supply.
- That a theorem imported as a string was proved. The trusted theorems are the ones elaborated in the reviewed development.

Invalid input, a stale format version, a hash mismatch, a schema mismatch, and a proposal that disagrees with the recomputation are rejections.

### 7.3 Components of the trusted base

These components are distinct. A proof checked by the kernel does not vouch for the others. The minimization column is the plan for when code exists.

| Component | What it is trusted for | Planned minimization |
| --- | --- | --- |
| Lean kernel | Checking that elaborated proofs follow the logic | Keep the statistical claims inside ordinary proofs. Record `#print axioms` for the public theorems |
| Parser | Turning bytes into the mathematical data the theorem mentions | One pure function, specified by a denotation, covered by negative tests. The theorem cites that function |
| Runtime and IO | Reading the bytes that are actually hashed | The proof is about the pure function on an explicit byte string. "The disk file is that byte string" stays an IO assumption, stated as such |
| Compiler and compiled binary | A native executable matching the Lean program | Prefer checking by elaborating the Lean source. Treat a native binary as a convenience with a larger trusted base |
| FFI | Foreign code on the checking path | Keep foreign code out of the checker. Producers stay outside |
| Serialization | Preserving fields across tools | Versioned canonical layout. Reject unknown versions, truncated records, and trailing junk |
| Randomness | Bits used by a Monte Carlo check | Exact milestones use none. Later certificates name the random-bit model. Recorded bits can be replayed; replay does not prove the bits were uniform |

Standard logical axioms reported by `#print axioms` (whatever they are on the toolchain eventually chosen) are recorded. Project-specific axioms are not added unless a reviewed allowlist names them. `sorry` and `admit` are absent from anything presented as a result.

### 7.4 Stan, PyMC, and other sampler output

A sampler trace may be attached to a certificate as an untrusted artifact. The checker may confirm file-level summaries that have a decision procedure: length, recorded seeds if present, hash, moments recomputed from the stored numbers. Those checks do not promote the trace to a theorem about the posterior. Milestone 4 may add a proved link for a *specified* algorithm, for example an exact finite Markov chain with a proved stationary distribution, or a Monte Carlo estimator with a proved error bound under a stated random-bit model. A general "import a Stan run" feature is not that link.

## 8. Milestones

Each milestone is a proposal. Exit criteria are checks a later change can be measured against. They are not a claim that the check already passes.

### 8.1 Milestone 1 — Survey, audit, and a proposed community discussion

**Intent.** Learn the existing APIs well enough to avoid duplicate theorems, and write down a dependency-adoption record before this repository depends on anything. Align with upstream communities through a discussion the owner may post. Select a first upstream pull request only if the search finds a genuine gap.

**Survey surface, at least:**

- mathlib's posterior kernel, as documented in [`Mathlib.Probability.Kernel.Posterior`](https://leanprover-community.github.io/mathlib4_docs/Mathlib/Probability/Kernel/Posterior.html) (see section 11 for what was already read).
- mathlib's Beta distribution module, [`Mathlib.Probability.Distributions.Beta`](https://leanprover-community.github.io/mathlib4_docs/Mathlib/Probability/Distributions/Beta.html).
- The StatLean files and build targets named in section 11, plus the neighboring modules those files import when the gap search needs them: randomization asymptotics, any finite MCMC modules the README points at, and any second definition of `empiricalMeasure`.
- Enough of TinyPPL and SampCert to say, with a pinned revision, whether either already does dataset-to-certificate inference of the kind in this plan. Section 11 records only a shallow identification.

**Dependency audit, before any adoption.** For each candidate commit:

- Build that commit, or record that the build was not run.
- `#print axioms` on the declarations the companion would rely on, including what transitively appears.
- A search for `sorry` and `admit` on that surface.
- The license text, read from the repository, and a compatibility note relative to code that would sit beside mathlib.
- A maintenance note: whether the project documents a release or toolchain policy, and whether the inspected commit is a tag, a branch tip, or an arbitrary revision. This is an observation, not a promise that the project will remain maintained.

No version is chosen in milestone 1. StatLean's own pin, observed in section 11, is a fact about StatLean. It is not a pin for this repository. Adopting it later would be a separate decision with a reason.

**Gap search.** The output is a list of candidate statements, each with a search note: mathlib declaration or "not found," StatLean declaration or "not found," and a one-line API judgment. The first upstream pull request is whatever that list justifies, or the explicit decision that there is no upstream pull request yet.

The following are already recorded as **present** in StatLean at `e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2`, so they are not the novel first pull request:

- `empiricalMeasure` and `integral_empiricalMeasure` (the integral equals the sample average `mcEstimate`).
- Beta–Bernoulli conjugacy, `beta_bernoulli_posterior_ae`, for a positive real Beta prior and an i.i.d. Bernoulli kernel, almost everywhere under the prior predictive.

A companion proof may apply those theorems. Application is a dependency, after the audit, and it is a different artifact from an upstream patch that reintroduces them.

**Proposed Zulip discussion.** Not posted. The owner writes the message in their own words if they choose to post it. Talking points already fixed by this plan:

- This repository is a public plan for a dataset-to-certificate companion, complementary to mathlib and StatLean.
- Ask which home is appropriate for a small generic lemma if the gap search finds one.
- Ask whether executable certificate work should be coordinated inside StatLean or kept beside it.
- State that no first pull-request topic has been chosen, and that the two items above were found already formalized in the inspected StatLean revision.

Posting is a human action. An edit that only updates this file does not post it. Milestone 1's community-alignment deliverable is complete when the owner records either a thread URL and the outcome, or a decision to defer the post. The survey and the audit can be written before that decision.

**Deliverables.**

- A survey note in this file or a document it links, with repository URLs and commits for every declaration it relies on.
- An audit note satisfying the checklist above for every dependency proposed for adoption. "Not adopted" is an acceptable outcome.
- A gap list whose conclusion is either a specific missing API or "no upstream pull request proposed."
- The Zulip outcome recorded as a URL or as a deferral. The draft talking points above are the planning artifact; they are not themselves a posted discussion.

**Acceptance.**

- A reader can tell which facts were rebuilt and which were read-only. Today, section 11 is entirely read-only.
- This repository still has no toolchain file and no dependency pin at the end of milestone 1, unless a later amendment starts implementation and says why a pin was added.
- The empirical-measure integration identity and Beta–Bernoulli conjugacy are not opened upstream as if they were missing, unless a new note shows that the inspected theorems fail to cover the proposed statement.
- No message has been sent merely by merging documentation.

### 8.2 Milestone 2 — Exact Beta–Bernoulli, from a tiny CSV to a posterior certificate

**Intent.** One end-to-end exact example in the companion: binary observations in a CSV, a positive rational Beta prior, a checker that recomputes counts from the records, and a theorem that ties that computation to a probability-measure posterior under hypotheses written in the statement.

**Mathematical claim to be proved later.** Under a conditional i.i.d. Bernoulli likelihood and a Beta prior with the stated parameters, the posterior of the success probability is the Beta measure with updated parameters, and the posterior mean of the success probability equals the rational formula below. The theorem names the kernel and the measure it actually uses (mathlib's posterior, a StatLean conjugacy lemma, or a lemma that reduces to one of those after the milestone 1 audit). Incrementing `α` by the number of ones is the arithmetic inside that story. The increment by itself is not conjugacy, and it says nothing about whether the likelihood was Bernoulli or the trials conditionally independent.

The inspected StatLean theorem `beta_bernoulli_posterior_ae` is an almost-everywhere statement under the prior predictive, and the mathlib Bayes form read on the docs page is almost-everywhere as well. A certificate about one recorded file needs a reading that applies to that file: a pointwise identity for every binary sequence under hypotheses that are stated, or an explicit limitation that the cited theorem does not single out the observed sample. Milestone 2 does not treat the almost-everywhere theorem as if it were already that pointwise certificate.

**Executable part.** Let the parsed sample be a list `y` of bits, `n` its length, `s` the number of ones, so `n - s` is the number of zeros. Both `n` and `s` are computed from `y`. For a prior `(α, β)` of positive rationals,

- posterior parameters `(α + s, β + (n - s))`,
- posterior mean `(α + s) / (α + β + n)`.

These operations stay in the rationals. The mean is the mean of the success probability under the updated Beta law, once the measure-theoretic theorem is proved; the certificate text says that, and it says the theorem's hypotheses.

`n = 0` is a specified edge, not an accident: the posterior parameters equal the prior and the mean is `α / (α + β)`. Empty input is well-defined only when the grammar explicitly allows an empty observation list. It is rejected if the chosen grammar requires a header the file lacks.

**Schema, proposed as `beta-bernoulli-binary-v1`.** The concrete byte grammar is a milestone 2 deliverable; the logical content is fixed now.

- Observations are a sequence of records whose value is `0` or `1` and nothing else.
- The prior is two rationals `α` and `β`, each strictly positive, so the hypothesis `0 < α` and `0 < β` of the inspected conjugacy theorem can apply after coercion to the reals.
- The checker rejects non-binary tokens, extra columns, non-rational or non-positive prior components, an unknown schema or format version, a hash mismatch, truncated records, and trailing junk.
- If the file or the proposal also states counts, those stated counts must equal the counts recomputed from the observation records. Otherwise the checker rejects the file. The theorem uses the recomputed counts.

**Parser semantics.** The milestone specifies a denotation: accepted byte strings map to `(y, α, β)`, and rejected byte strings are a defined failure. The correspondence theorem says that the sample and the prior appearing in the posterior statement are that denotation, not a count supplied by the producer. The function named by the theorem is the function the executable runs, or the development contains a proof that the two functions agree on the accepted language.

**Noncomputable remainder.** The posterior as a measure or density may be noncomputable even though `s`, `n`, the updated parameters, and the mean are computable. Milestone 2's executable deliverable stops at the sufficient statistic and the rational mean. It does not promise a computable real density. Where the measure-theoretic proof uses Gamma or Beta values, those stay inside `Prop`.

**Deliverables.**

- A written grammar and denotation for the tiny CSV and for the canonical certificate bytes.
- A pure parser and checker in the companion, once implementation starts.
- The posterior-measure theorem under the conditional i.i.d. Bernoulli likelihood and Beta prior, with parameters taken from the denotation.
- Tests: malformed input rejected; a lying count rejected; at least one accepted file whose recomputed mean matches a hand-computed rational; the `n = 0` edge matches the specification of the grammar.
- A short note, aimed at a reader of the certificate, listing hypotheses the checker did not establish.

**Acceptance.**

- Every accepted example in the repository is synthetic or clearly public, and small.
- The mean reported on an accepted file equals `(α + s) / (α + β + n)` with `s` and `n` defined from the parsed records.
- The formal statement's hypotheses include the likelihood and the prior. A reader can point at the line that does that work.
- The statement's quantifiers match the certificate. An almost-everywhere upstream lemma is cited as almost-everywhere, unless a pointwise lemma for the recorded sample has been proved or found.
- There is no claim that a density value was computed, unless a later amendment proves an executable density and changes this criterion.
- Floats are not the numeric type of this milestone.

### 8.3 Milestone 3 — One small exact permutation test

**Intent.** One exact finite-sample test in the companion, small enough to enumerate, with a proved counting implementation and a superuniform p-value theorem under an explicit randomization or exchangeability null.

The default procedure is a two-group difference of means. Rank statistics and the Wilcoxon statistic are optional later work. They are not part of this milestone and must not be described as done.

StatLean at the inspected revision already contains an asymptotic two-sample permutation central limit theorem (section 11). That development is a limit under growing samples. Milestone 3 is an exhaustive finite enumeration. Whether StatLean or mathlib already contains that enumeration is a milestone 1 gap-search item. This specification does not declare the enumeration missing beyond the files that were opened, and it does not declare it the first upstream pull request.

**Design that the milestone implements, unless a recorded amendment changes it first.**

- Data are rational numbers with group labels. Group A has size `m ≥ 1` and group B has size `n ≥ 1`. Empty groups are rejected. (The inspected StatLean definition `twoSampleMeanDiff` documents junk values when a group size is zero, because it uses real inversion. The exact companion test refuses that corner instead of inheriting a junk value.)
- The allowed assignments are the combinations that choose `m` indices out of `m + n` for group A, equivalently the placements of labels with both sizes fixed. Each combination is one point of the null. The denominator of the p-value is the number of combinations, `C(m + n, m)`.
- The statistic is the difference of the two group means, as a rational. Comparisons use exact rational order.
- Ties in the data are handled by multiplicity of assignments, not by collapsing equal statistic values into one point. If two different labelings produce the same number, both count. An implementation that aggregates by statistic value stores the number of assignments that produce that value.
- Tail convention: upper tail, inclusive. The p-value is the number of allowed assignments whose statistic is greater than or equal to the observed statistic, divided by the number of allowed assignments. The observed assignment is included. A one-sided lower tail or a two-sided rule is a different procedure and needs its own statement; it is not the default.

**Theorems.**

1. **Implementation correctness.** The enumerated set equals the set of combinations above; the statistic equals the difference of means on each assignment; the aggregated multiplicities sum to `C(m + n, m)`; the inclusive upper-tail count matches that definition.
2. **Finite-sample validity.** Under the explicit null — uniform random assignment of labels with sizes fixed and the pooled values held fixed, or a finite exchangeability assumption that implies the same uniform distribution on labelings — the p-value `p` is superuniform: for every rational `t` in `(0, 1]`, the null probability of `p ≤ t` is at most `t`. The proof treats the inclusive comparison and tied statistics so the inequality direction is proved.

**Hypotheses the theorem must name, because they are not free.**

- The null is about the randomization distribution or about finite exchangeability of the pooled sample. It is not a theorem that the observations are independent, identically distributed, or independent of a market.
- A superuniform result under label randomization does not by itself test equality of two population means under an independent-samples model. The header of the inspected StatLean two-sample file records the known asymptotic warning: an unstudentized permutation test can fail to be asymptotically level `α` for equality of means when variances differ and the sample sizes are unbalanced, citing Romano. Milestone 3 does not adopt that asymptotic theorem. It also does not whisper past it. Certificate text for this test describes the randomization null that was proved.
- Exchangeability of a finite sample does not by itself supply a Beta prior, a Bernoulli likelihood, or an infinite de Finetti mixture. Those belong to milestone 2's hypotheses when they are used, and they are separate assumptions.

**Scale.** The first implementation and its reference counter cover small sizes only, including at least one case with tied values and one case with `m ≠ n`. A reference counter is a second program or a hand enumeration used as a test oracle. Agreement with the oracle is evidence about the implementation tests. The proof is the Lean theorem, not the oracle. Randomized or Monte Carlo permutation tests are milestone 4 and wait until this exact baseline exists.

**Deliverables.**

- The specification of assignments, multiplicities, and the inclusive upper tail, as above, or an amendment recorded before the proofs.
- An executable enumeration for small `m, n` and a rational statistic.
- The two theorems above, with the null written in the statements.
- Tests comparing the enumeration to a reference on a documented finite set of synthetic inputs, including ties and unequal sizes.
- Certificate text that states the null in the same words as the theorem.

**Acceptance.**

- The p-value's denominator is the number of allowed assignments.
- An input with tied values produces multiplicities that sum to the number of assignments.
- Empty groups are rejected.
- The development contains no Wilcoxon or rank-statistic claim.
- No example assumes market independence as an unstated background fact.

### 8.4 Milestone 4 — Approximation, after an exact baseline

**Intent.** Extend a procedure that already has an exact baseline (milestone 2 or 3) to a larger size by a stated approximation: a randomized permutation test, a bootstrap, a Markov-chain Monte Carlo method, or another algorithm with a proof, a certificate, or a bound. Hamiltonian Monte Carlo and variational inference are long-term research options on this list. They are not supported, not implemented, and not implied by the existence of this section.

**Error sources stay distinct.** A milestone 4 result says which of these it bounds, and it does not use one of them as a stand-in for another.

| Source | What it is |
| --- | --- |
| Rounding | Replacement of a number by a nearby representable number, including floating-point evaluation |
| Deterministic approximation bias | A deterministic substitute for the exact object: a truncated series, a reduced enumeration, a discretized kernel, an optimization gap |
| Monte Carlo uncertainty | The randomness of a finite sample from a sampler, and any convergence statement about that sample |
| Data and model uncertainty | Uncertainty about the observations and about whether the model's hypotheses hold. A Monte Carlo standard error does not address it |

**Certificates.** A numerical claim uses an enclosure checked by a program whose correctness is proved, or it is labeled as an untrusted summary. If the enclosure depends on a transcendental (a normal tail, a Gamma value, a logarithm), that dependence is explicit: either the bound is proved, or the development records an audited axiom. An external floating-point call is not a proof.

Bootstrap and randomized permutation work in this milestone cite their exact counterparts. A bootstrap identity that StatLean already proves at the level of the empirical measure (section 11) is reused after the audit, not rewritten. The companion's new obligation is the link from recorded bytes and recorded random bits to that identity, plus the separation of Monte Carlo error from model error.

A finite Markov chain may be in scope when its transition kernel and stationary distribution are the ones in the proof. A trace from a general-purpose probabilistic programming system is still an untrusted file summary unless that link exists (section 7.4).

**Deliverables.**

- For each approximation that is actually built: the exact baseline it approximates, the algorithm, the random-bit or rounding model, and a theorem or a checked bound that names the error sources above.
- A regression test on a size the exact enumerator can still finish, comparing the approximation's certificate to the exact answer within the stated error.
- Documentation that Hamiltonian Monte Carlo and variational inference remain research options until a design for one of them is accepted as an amendment.

**Acceptance.**

- No approximation milestone is presented as complete while its exact baseline is missing, except by an amendment that explains why the exact case is being approximated by a different proved object.
- Rounding, deterministic bias, Monte Carlo uncertainty, and model uncertainty are separate lines in the certificate explanation.
- Sampler files without a proved link are labeled as unchecked summaries.
- The text does not say that Hamiltonian Monte Carlo or variational inference is available.

### 8.5 Future scaffolding and CI

This subsection is policy for a later change. The repository has none of these files now.

When implementation starts, the project pins the Lean toolchain and the mathlib revision it builds, and it records the StatLean revision if it depends on StatLean. Pins are chosen then, with the audit from milestone 1, not by copying a pin into the tree during the planning stage.

The intended CI checks are:

- The theorem library builds at the pinned revisions.
- The build fails if a result uses `sorry` or `admit`.
- The build fails if a public theorem's axiom footprint contains a name outside a reviewed allowlist. The allowlist is produced from `#print axioms`, not from memory. Transitive axioms are included.
- The executable parser has unit tests and property tests, including malformed input and mismatched counts.
- The certificate checker used in CI is the pure function the proofs talk about, or CI also runs the proof that the two agree.

A workflow file, a Lake project, and a toolchain file are implementation artifacts. They land in a change that is explicitly an implementation change, and the status section of this specification is updated in the same change so the tree and the document agree.

## 9. Risks and open questions

These items are open. Writing them down is not a resolution.

**Representation bridge.** The executable layer wants rationals (and natural-number counts). The probability theorems use real numbers, measures, and kernels, often noncomputable. Each milestone has to say where `ℚ → ℝ` happens and which equalities are definitional, which are proved, and which are left noncomputable. A separate quantifier gap sits next to that bridge: the inspected Beta–Bernoulli posterior identity is almost everywhere under the prior predictive, while a certificate talks about one file. Closing that gap is part of milestone 2's theorem obligation, and it is open here.

**Parser correctness.** Tests can miss a byte string the denotation classifies differently from the program that shipped. The mitigation in section 7.3 is a single pure function cited by the theorem. That mitigation is itself unproved until the function and the proof exist. IO still sits outside the kernel: the hashed bytes are whatever the runtime supplied.

**Rounding and transcendentals.** Interval certificates for floating-point evaluation, and any dependence on transcendental functions, are unsolved design problems for milestone 4. Milestone 2 avoids them for the mean by staying in the rationals. It does not remove Gamma from an upstream Beta density; it refuses to pretend that density was executed.

**Approximation versus sampling.** Deterministic bias and Monte Carlo error need separate statements. A small Monte Carlo error around a biased procedure is not an approximation to the exact baseline.

**Random bits.** "Uniform" requires a model of bits. Recorded bits can be replayed. A pseudorandom generator requires an assumption that this plan has not formalized. Exact milestones use no randomness, which postpones the question and does not answer it.

**Dependence and exchangeability.**

- Stationarity of a series is not, by itself, the hypothesis a milestone 2 or milestone 3 theorem will need.
- Exchangeability alone does not specify a Beta prior. The inspected conjugacy theorem assumes the Beta prior and an i.i.d. Bernoulli kernel.
- Finite exchangeability does not automatically yield the infinite de Finetti representation. The bibliographic comment in StatLean's Beta–Bernoulli file refers to de Finetti in prose; the theorem that was read does not derive the prior from exchangeability. A future certificate that wants that implication has to prove the version it uses, finite or infinite, and name the prior.
- Trading data can have dependence, regime changes, and non-exchangeable stretches. Mixing or block-resampling assumptions are extra hypotheses. A block bootstrap that ignores them has not been validated by the fact that the file parsed.
- Model misspecification is not formally refuted or confirmed by the observations alone. The certificate can say "under this model, this functional equals this number." It cannot say "the model is the data-generating process" unless a theorem with that conclusion exists, which is not proposed here.

**Asymptotic permutation level.** The inspected two-sample limit theorem is carefully asymmetric in its two variances. Using the milestone 3 exact p-value as if it were an asymptotic level-`α` test for equal population means, under unequal variances and unbalanced sizes, would repeat a mistake the StatLean header already flags. This plan does not license that use.

**First pull request.** The topic is to be determined after the milestone 1 gap audit. This document does not invent one.

**Maintenance and license.** StatLean and mathlib are external projects. Their licenses, axiom footprints, and `sorry` status on the surfaces we might use were not audited here. A dependency adopted before that audit would be a process failure.

**Trust creep.** Convenience features (a native binary, a Python helper that "just" counts, a cached axiom) widen the trusted base. Section 7.3 is the checklist against that creep. It is only a checklist until the code exists.

## 10. Reading a future claim

A future result in this repository should be readable as follows.

- The status section says whether the milestone is still a proposal.
- The theorem statement contains the likelihood, prior, null, or error model.
- The certificate names the byte string, the schema, and the recomputed statistics.
- Computable outputs and noncomputable measures are distinguished.
- Upstream declarations are cited with a commit, and the citation says whether that commit was rebuilt.

A pull request that skips those distinctions is not implementing this specification, even if the arithmetic is right.

## 11. Source provenance

Inspection for this specification was done on 2026-11-06. Unless a row says otherwise, sources were read, not built, and were not searched for `sorry` across a whole repository.

### 11.1 mathlib documentation

[Posterior kernel](https://leanprover-community.github.io/mathlib4_docs/Mathlib/Probability/Kernel/Posterior.html), fetched that day from the public docs site. The page documents `ProbabilityTheory.posterior` (`κ†μ`) for a kernel `κ` and a finite prior measure `μ`, defined via the conditional kernel of the swapped composition product, and the characterizing theorem `compProd_posterior_eq_map_swap`. The page states that the posterior is a Markov kernel, records uniqueness almost everywhere, and gives a Bayes form `posterior_eq_withDensity` under an absolute-continuity hypothesis. The definition is marked `noncomputable` on that page. The docs site does not display a git revision in the text that was read, so this specification does not assign a commit to the page. A milestone 1 survey that cites a posterior lemma cites a mathlib commit, not this URL alone.

[Beta distribution](https://leanprover-community.github.io/mathlib4_docs/Mathlib/Probability/Distributions/Beta.html). The URL is recorded as the mathlib Beta module. Its theorem list was not transcribed into this specification. Any claim about a particular Beta lemma waits for the milestone 1 survey at a pinned commit.

mathlib's contribution and style guides were fetched on the same day:

- <https://leanprover-community.github.io/contribute/how-to-contribute.html>
- <https://leanprover-community.github.io/contribute/style.html>
- <https://leanprover-community.github.io/contribute/> (AI-use section, summarized in section 6)

Those pages can change. The operative policy for an upstream pull request is the page on the day the pull request is opened.

### 11.2 StatLean at a pinned revision

Repository: <https://github.com/StatLean/Stat-Lean>

Revision inspected: [`e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2`](https://github.com/StatLean/Stat-Lean/commit/e436d8a184421c2a0ed8db0b2688c6cd26a7f5a2)

Mode: read-only source inspection. That revision was not rebuilt. Axioms were not printed. A repository-wide `sorry` search was not run. The `LICENSE` file and `LICENSES` directory are present at that revision and were not interpreted. The `lean-toolchain` file was not opened.

`lakefile.lean` at that revision sets the default target to the library `StatLean` and defines one executable, `deps`, rooted at `Scripts.ExtractDeps`, described there as a dependency-graph extractor for the website. The file requires mathlib from git tag `v4.34.1`, and a comment identifies that tag with the project's Lean toolchain. This repository does not adopt that pin. No target in the inspected lakefile is a raw-data parser, a certificate checker, or a dataset-to-inference executable. That is a statement about the lakefile that was read, not a proof that no such code exists outside those targets.

The README at that revision describes a broad theory library. Topics it names include conjugate families and MCMC correctness (Bayesian), the finite-sample bootstrap (computational statistics), and permutation tests and the bootstrap (hypothesis tests). Those are README claims. The MCMC modules were not opened.

Files opened:

| File | What the opened text supports |
| --- | --- |
| `StatLean/ComputationalStatistics/Core/Defs.lean` | Defines `empiricalMeasure` as `(1/n)` times the sum of Diracs on a finite sample, plus `weightedMeasure`, `categorical`, and `mcEstimate`. The module comment says this defs surface is definitions and `rfl`-level lemmas and describes it as "0-sorry"; that is the file's comment about itself, not a library audit. The same comment says `n = 0` makes `empiricalMeasure` the zero measure and `mcEstimate` a junk `0`, and that other areas of the repository also define empirical-measure objects under other names. |
| `StatLean/ComputationalStatistics/Core/EmpiricalMeasure.lean` | Proves `integral_empiricalMeasure`: for a strongly measurable real integrand, the integral against `empiricalMeasure` equals `mcEstimate`. Also proves the probability-measure instance under `NeZero n`, a counting form, and the link to the categorical index draw. |
| `StatLean/ComputationalStatistics/Resampling/BootstrapMoments.lean` | Finite-sample bootstrap identities conditional on the data, using `integral_empiricalMeasure`. Includes `bootstrap_linearStatistic_expectation` and `bootstrapMean_variance`. The header distinguishes the ideal bootstrap expectation from a Monte Carlo approximation and points at `MonteCarlo/Estimation.lean`, which was not opened. |
| `StatLean/Bayesian/Conjugacy/BetaBernoulli.lean` | `beta_bernoulli_posterior_ae`: for real `α, β > 0` and `n : ℕ`, almost every sample under the prior predictive of `n` i.i.d. draws from `bernoulliKernel` has posterior equal to `betaMeasure (α + successes y) (β + (n - successes y))`. Also a posterior-mean lemma for `unitClamp` and Laplace's rule of succession as a posterior predictive. Parameters in the theorem are reals, not a rational CSV schema. The bibliographic comment mentions de Finetti; the theorem assumes the Beta prior and the i.i.d. Bernoulli kernel. |
| `StatLean/HypothesisTesting/Randomization/TwoSamplePermutation.lean` | Asymptotic two-sample permutation theory: `twoSampleLaw`, `twoSampleMeanDiff`, and limit theorems headed as the permutation central limit theorem, citing Lehmann and Romano, *Testing Statistical Hypotheses*, 4th ed., Theorem 17.3.1. The header states that the unstudentized test can fail to be asymptotically level `α` for equality of means when variances differ and sizes are unbalanced. The file imports randomization modules (`Randomization/Asymptotics`, `PairCLT`, and others) that were not opened, so this note does not say whether an exact enumerator lives there. |

The README also documents areas this inspection did not open (causal inference, concentration, time series, and others). Their existence on the topic list is not an audit of their proofs.

### 11.3 Related systems, not inspected in source

[TinyPPL](https://github.com/SHoltzen/tinyppl-lean). Linked as related work on exact discrete probabilistic programs. The planning brief treats it as special-purpose rather than a general dataset-to-certificate library. Its Lean sources were not reviewed, and no revision was pinned. Milestone 1 either pins a revision and looks, or leaves the question open in writing.

[SampCert](https://github.com/leanprover/SampCert). The repository subtitle on 2026-11-06 read "SampCert : Verified Differential Privacy." That identifies a verified-sampling project aimed at differential privacy. It is not, on the strength of that subtitle, a general statistical-inference checker. Sources were not reviewed, and no revision was pinned. The same milestone 1 obligation applies.

### 11.4 What this section refuses to carry

Institutional scope statements, theorem counts, star counts, and completeness claims that were not recomputed from the opened sources are omitted on purpose. The StatLean README is evidence about how that project describes itself at one commit. The opened files are evidence about the declarations they contain. Neither is a build log.

## 12. Amendment rule

A change to scope, to a tail convention, to a schema, or to an acceptance criterion is an edit to this file, made before a proof is described as meeting the new criterion. The status table in section 4 changes when, and only when, the tree gains or loses the corresponding artifact. Silent drift between the plan and the repository is a defect in the plan.
