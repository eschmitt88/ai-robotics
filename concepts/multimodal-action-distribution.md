---
kind: concept
name: "Multimodal Action Distribution"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion]
related_concepts: [Diffusion Policy, Behavior Cloning, Conditional VAE Policy, Energy-Based Model, Denoising Diffusion Probabilistic Models]
related_experiments: []
tags: [imitation, distribution, generative]
---

# Multimodal Action Distribution

## Definition

An action distribution in which several distinct actions are all valid for
the same observation (e.g. going left or right around an obstacle), so the
correct output is a set of modes rather than a single value.

## Why it matters here

Faithfully modeling it is Diffusion Policy's key edge: unimodal regression
averages competing modes into an invalid in-between action, so the ability
to represent multimodality is what makes generative policies work on human
data.

## Connections

- [[Diffusion Policy]] — captures multimodality via iterative denoising
- [[Behavior Cloning]] — unimodal BC regression fails on multimodal data
- [[Conditional VAE Policy]] — an alternative multimodal representation via a
  latent variable
- [[Energy-Based Model]] — another multimodal formulation Diffusion Policy is
  compared against
- [[Denoising Diffusion Probabilistic Models]] — the generative model that
  represents the modes
