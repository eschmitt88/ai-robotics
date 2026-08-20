---
kind: concept
name: "Probability Flow ODE"
status: seedling
added: "2026-08-20"
sources: [prasad2024consistency]
related_concepts: ["Score Function", "DDIM", "Denoising Diffusion Probabilistic Models", "Consistency Distillation", "Flow Matching"]
related_experiments: []
tags: [diffusion, ode, sampling, theory]
---

# Probability Flow ODE

## Definition

The deterministic ordinary differential equation whose solutions carry a simple
Gaussian at time `T` to the data distribution at time `0`, with drift given by the
[[Score Function]]. Integrating it backwards denoises. It is the deterministic
counterpart to the stochastic differential equation that DDPM sampling integrates.

## Why it matters here

The stochastic/deterministic split explains a real tradeoff. Because
[[Denoising Diffusion Probabilistic Models]] integrate an SDE, they inject fresh
Brownian noise at every step and thereby represent a
[[Multimodal Action Distribution]] well — but need 100+ steps. ODE-based
formulations ([[DDIM]], EDM, [[Flow Matching]]) allow far fewer steps and admit
[[Consistency Distillation]], but [[prasad2024consistency|Consistency Policy]] observed that both its
EDM teacher and its student **lose some of the teacher's multimodality**, visibly
favoring one side of the bimodal Push-T task.

## Connections

- [[Score Function]] — supplies the ODE's drift term
- [[Denoising Diffusion Probabilistic Models]] — the SDE counterpart
- [[DDIM]] — the first widely used ODE sampler for diffusion
- [[Consistency Distillation]] — exploits uniqueness of ODE solutions
- [[Flow Matching]] — learns a straighter path between the same endpoints
