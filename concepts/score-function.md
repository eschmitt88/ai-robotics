---
kind: concept
name: "Score Function"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion]
related_concepts: [Denoising Diffusion Probabilistic Models, Energy-Based Model, DDIM, Diffusion Policy]
related_experiments: []
tags: [diffusion, theory, gradients]
---

# Score Function

## Definition

The gradient of the log-probability of the data distribution with respect to
the input; in diffusion models it is approximated (up to scale) by the
network's noise prediction.

## Why it matters here

It explains why diffusion training is stable where energy-based models are
not — the intractable normalization constant drops out of the score, so
learning reduces to a simple regression, a key intuition for trusting
Diffusion Policy.

## Connections

- [[Denoising Diffusion Probabilistic Models]] — noise prediction is a
  scaled score estimate
- [[Energy-Based Model]] — score matching sidesteps the EBM normalization
  problem
- [[DDIM]] — sampling follows the learned score field
- [[Diffusion Policy]] — inherits this training stability
