# Contributing

This repository is a public research plan. The normative document is [SPEC.md](SPEC.md). Read the status section before opening a change.

## What belongs here now

Changes at this stage are documentation: corrections to the specification, clearer acceptance criteria, and provenance notes that distinguish inspection from a rebuild.

A change that adds Lean sources, a Lake project, a toolchain file, a dependency pin, a script, a dataset, an example input, or a CI workflow is outside the current stage. Those artifacts wait until the specification says implementation of that piece has started, and they follow the milestone that calls for them.

## Upstream work

Generic measure, kernel, posterior, conjugacy, permutation, empirical, and dependence theorems belong in mathlib when they are genuinely missing and the API fits. Statistics material that already fits StatLean stays coordinated with StatLean. Executable parsing, certificates, and adapters are companion-library work, and that library is not started.

Upstream pull requests follow the current mathlib contribution and style guides linked from the specification, including the community policy on AI-assisted work. The person who opens an upstream pull request understands the proofs and can justify the design. Disclosure names the tools and how they were used. Comments on GitHub and Zulip are written in the author's own words.

The first upstream topic is undecided. The specification explains why an empirical-measure integration identity and Beta–Bernoulli conjugacy are not candidates for a "first missing API" pull request on the strength of the inspection already done.

## Community contact

Milestone 1 includes a Zulip discussion proposed in the specification. That discussion has not been posted. Posting it is a human action by the owner, in the owner's words, after they decide the audience and the wording. An agent change to this repository does not send email, open Zulip threads, or ping maintainers.

## Data

Examples and fixtures, when they eventually exist, are synthetic or clearly public. Secrets and customer market data stay out of the repository, the issues, and the certificates.

## Claims

A count, a hash, a posterior mean, or a p-value is an output of a stated procedure. It becomes a machine-checked statistical claim only under the theorem and trust boundary in the specification. Arithmetic on counts does not prove that a likelihood, a prior, or an independence assumption holds.
