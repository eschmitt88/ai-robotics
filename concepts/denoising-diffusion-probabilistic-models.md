---
kind: concept
name: "Denoising Diffusion Probabilistic Models"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion]
related_concepts: [Diffusion Policy, DDIM, Score Function, Energy-Based Model, Multimodal Action Distribution]
related_experiments: []
tags: [diffusion, generative, foundations]
---

# Denoising Diffusion Probabilistic Models

## Definition

Generative models that produce samples by iteratively denoising Gaussian
noise over many steps, trained to predict the noise added at each step —
which is equivalent to learning the score of the data distribution.

## Why it matters here

They are the generative machinery under Diffusion Policy, so understanding
DDPM training and sampling is a prerequisite to using, tuning, or modifying
diffusion-based robot policies.

## Connections

- [[Diffusion Policy]] — applies DDPM to action generation
- [[DDIM]] — an accelerated sampler for the same trained model
- [[Score Function]] — what the noise-prediction network implicitly learns
- [[Energy-Based Model]] — a related generative family with a harder
  normalization problem
- [[Multimodal Action Distribution]] — the multimodality DDPMs represent
  naturally
