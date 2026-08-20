---
kind: concept
name: "Flow Matching"
status: seedling
added: "2026-08-20"
sources: [black2024pi0]
related_concepts: ["Probability Flow ODE", "Action Expert", "Vision-Language-Action Model", "Diffusion Policy", "Consistency Distillation", "Multimodal Action Distribution"]
related_experiments: []
tags: [flow-matching, diffusion-variant, action-decoding, vla]
---

# Flow Matching

## Definition

Train a network to regress the velocity field of a chosen probability path between
noise and data — with the linear-Gaussian (optimal transport) path
`q(Aᵗ|A) = N(τA, (1−τ)I)`, the target is simply `u = A − ε` on noisy samples
`Aᵗ = τA + (1−τ)ε`. Sampling integrates that field from `τ = 0` to `1`.

## Why it matters here

It is the third distinct answer to diffusion's inference cost, and arguably the
cleanest: the path is straight by construction, so [[black2024pi0|π0]] needs only **10 forward
Euler steps** — no distillation stage ([[Consistency Distillation]]) and no
architectural workaround ([[Diffusion Action Head]]). Combined with caching the
observation prefix's keys and values, this puts a 3.3B VLA at 73 ms on a consumer
RTX 4090, and lets it emit 50-step action chunks at up to 50 Hz.

[[black2024pi0|π0]] samples the flow timestep `τ` from a beta distribution biased toward noisy
timesteps, and uses full bidirectional attention across action tokens.

## Connections

- [[Probability Flow ODE]] — the shared deterministic-transport framing
- [[Action Expert]] — the separate weight set [[black2024pi0|π0]] applies flow matching through
- [[Diffusion Policy]] — the 100-step DDPM baseline it displaces
- [[Consistency Distillation]] — the distillation-based alternative to the same goal
- [[Vision-Language-Action Model]] — the setting where high-frequency control makes step count decisive
