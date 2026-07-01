---
kind: concept
name: "Diffusion Policy"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion]
related_concepts: [Denoising Diffusion Probabilistic Models, Multimodal Action Distribution, Action Chunking, Receding Horizon Control, FiLM Conditioning]
related_experiments: []
tags: [imitation, diffusion, visuomotor]
---

# Diffusion Policy

## Definition

A visuomotor imitation policy that generates an action chunk by running
conditional denoising diffusion — starting from Gaussian noise and
iteratively refining it into a coherent action sequence conditioned on
observations.

## Why it matters here

It is the era's most influential imitation method and a strong, highly
tunable default that fits comfortably on a single 16GB GPU; the project uses
it as the reference generative-imitation baseline.

## Connections

- [[Denoising Diffusion Probabilistic Models]] — the generative machinery
  underneath the policy
- [[Multimodal Action Distribution]] — the property diffusion captures well
- [[Action Chunking]] — Diffusion Policy denoises whole action chunks
- [[Receding Horizon Control]] — how the denoised chunk is executed
- [[FiLM Conditioning]] — how the CNN variant injects visual observations
