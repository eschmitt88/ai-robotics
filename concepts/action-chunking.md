---
kind: concept
name: "Action Chunking"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion, zhao2023learning]
related_concepts: [Compounding Error, Temporal Ensembling, Receding Horizon Control, Diffusion Policy, Behavior Cloning]
related_experiments: []
tags: [imitation, action-space, temporal]
---

# Action Chunking

## Definition

Predicting a short sequence of future actions in a single forward pass
instead of one next action, so the policy commits to a coherent multi-step
plan per query.

## Why it matters here

It is an architecture-agnostic lever that improves temporal consistency and
sharply reduces compounding error, and it appears in both ACT and Diffusion
Policy — a cheap, transferable win for any single-GPU imitation setup.

## Connections

- [[Compounding Error]] — chunking cuts the number of drift-inducing
  decision points
- [[Temporal Ensembling]] — combines overlapping chunks for smoother motion
- [[Receding Horizon Control]] — executes only part of each predicted chunk
  before re-planning
- [[Diffusion Policy]] — generates its action chunk by denoising
- [[Behavior Cloning]] — chunking is a modification to the basic BC recipe
