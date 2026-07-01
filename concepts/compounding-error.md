---
kind: concept
name: "Compounding Error"
status: seedling
added: "2026-07-01"
sources: [zhao2023learning]
related_concepts: [Behavior Cloning, Action Chunking, Temporal Ensembling, Imitation Learning]
related_experiments: []
tags: [imitation, failure-mode, distribution-shift]
---

# Compounding Error

## Definition

The accumulation of small per-step behavior-cloning prediction errors that
progressively push the agent into states unseen during training, where its
errors grow even larger (covariate shift).

## Why it matters here

It is the central failure mode of behavior cloning and the reason long-
horizon manipulation from demonstrations is hard; naming it clarifies why
action chunking and temporal ensembling are worth their added complexity.

## Connections

- [[Behavior Cloning]] — compounding error is BC's defining weakness
- [[Action Chunking]] — predicting sequences reduces the number of error-
  accumulating decision points
- [[Temporal Ensembling]] — averaging overlapping chunks further damps drift
- [[Imitation Learning]] — the broader class where this shift arises
