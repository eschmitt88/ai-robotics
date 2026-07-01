---
kind: concept
name: "Clipped Surrogate Objective"
status: seedling
added: "2026-07-01"
sources: [schulman2017proximal]
related_concepts: ["Proximal Policy Optimization", "Trust Region Policy Optimization", "Policy Gradient Methods"]
related_experiments: []
tags: [rl, ppo, optimization]
---

# Clipped Surrogate Objective

## Definition

The clipped surrogate objective takes the minimum of the clipped and
unclipped ratio-weighted advantage — a pessimistic bound that penalizes
probability-ratio moves outside [1−ε, 1+ε].

## Why it matters here

It is the single mechanism that gives PPO TRPO-like stability with plain
SGD, so tuning ε is one of the highest-leverage knobs on the RL baseline.

## Connections

- [[Proximal Policy Optimization]] — the algorithm built around this objective
- [[Trust Region Policy Optimization]] — the hard-constraint method this approximates cheaply
- [[Policy Gradient Methods]] — the base gradient this clipping stabilizes
