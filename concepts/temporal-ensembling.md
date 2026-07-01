---
kind: concept
name: "Temporal Ensembling"
status: seedling
added: "2026-07-01"
sources: [zhao2023learning]
related_concepts: [Action Chunking, Compounding Error, Receding Horizon Control, Conditional VAE Policy]
related_experiments: []
tags: [imitation, inference, smoothing]
---

# Temporal Ensembling

## Definition

Querying the policy at every timestep so that multiple overlapping action
chunks predict the same instant, then combining those predictions with an
exponentially-weighted average.

## Why it matters here

It buys smoother, more precise motion at zero extra training cost — a pure
inference-time trick from ACT that is trivial to add and directly useful
when deploying chunked policies on real hardware.

## Connections

- [[Action Chunking]] — temporal ensembling exists to exploit overlapping
  chunks
- [[Compounding Error]] — averaging predictions damps error accumulation
- [[Receding Horizon Control]] — an alternative chunk-execution strategy
- [[Conditional VAE Policy]] — the ACT policy whose outputs are ensembled
