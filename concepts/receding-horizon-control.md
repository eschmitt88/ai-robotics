---
kind: concept
name: "Receding Horizon Control"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion]
related_concepts: [Action Chunking, Diffusion Policy, Temporal Ensembling, Position vs Velocity Control]
related_experiments: []
tags: [control, planning, inference]
---

# Receding Horizon Control

## Definition

Predicting a horizon of future actions but executing only the first few
before re-planning, so each control cycle acts on a freshly updated plan
(the classic model-predictive-control execution scheme).

## Why it matters here

It is how Diffusion Policy balances the long-horizon consistency of chunked
prediction against reactivity to new observations — an important knob for
robustness on real robots and a tunable latency/quality trade-off on one GPU.

## Connections

- [[Action Chunking]] — receding horizon is the execution strategy for a
  predicted chunk
- [[Diffusion Policy]] — uses receding-horizon execution of its denoised
  chunks
- [[Temporal Ensembling]] — an alternative way to consume overlapping chunks
- [[Position vs Velocity Control]] — the action-space choice being executed
  each cycle
