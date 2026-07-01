---
kind: concept
name: "FiLM Conditioning"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion]
related_concepts: [Diffusion Policy, Denoising Diffusion Probabilistic Models, Fused Vision Encoder]
related_experiments: []
tags: [architecture, conditioning, vision]
---

# FiLM Conditioning

## Definition

Feature-wise Linear Modulation — injecting a conditioning signal into a
network by predicting per-channel scale and shift parameters that modulate
intermediate activations.

## Why it matters here

It is how the CNN variant of Diffusion Policy conditions its denoising
process on visual observations; a lightweight, general conditioning
mechanism worth reusing whenever a policy needs to be steered by an external
signal.

## Connections

- [[Diffusion Policy]] — the CNN version uses FiLM to condition on
  observations
- [[Denoising Diffusion Probabilistic Models]] — FiLM conditions each
  denoising step
- [[Fused Vision Encoder]] — an alternative way of supplying visual features
  to a policy
