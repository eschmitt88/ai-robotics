---
kind: concept
name: "DDIM"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion]
related_concepts: [Denoising Diffusion Probabilistic Models, Diffusion Policy, Score Function]
related_experiments: []
tags: [diffusion, sampling, inference]
---

# DDIM

## Definition

Denoising Diffusion Implicit Models — a deterministic (non-Markovian)
sampler that decouples the number of training denoising steps from the
number of inference steps, allowing far fewer steps at generation time.

## Why it matters here

It is the practical lever for hitting real-time control rates with a
diffusion policy: dropping from hundreds to a handful of denoising steps
keeps inference latency inside a single-GPU budget.

## Connections

- [[Denoising Diffusion Probabilistic Models]] — DDIM is a faster sampler
  for a DDPM-trained model
- [[Diffusion Policy]] — relies on DDIM to meet control-loop latency
- [[Score Function]] — the learned quantity DDIM integrates during sampling
