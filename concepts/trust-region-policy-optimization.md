---
kind: concept
name: "Trust Region Policy Optimization"
status: seedling
added: "2026-07-01"
sources: [schulman2017proximal]
related_concepts: ["Proximal Policy Optimization", "Clipped Surrogate Objective", "Policy Gradient Methods"]
related_experiments: []
tags: [rl, trust-region, optimization]
---

# Trust Region Policy Optimization

## Definition

TRPO maximizes a surrogate objective subject to a hard KL-divergence
constraint between the old and new policies, solved with conjugate-gradient
and a line search.

## Why it matters here

TRPO is PPO's direct predecessor; understanding it clarifies why PPO trades
its second-order machinery for a much simpler first-order clip.

## Connections

- [[Proximal Policy Optimization]] — the first-order successor that replaces the KL constraint
- [[Clipped Surrogate Objective]] — PPO's cheap stand-in for TRPO's trust region
- [[Policy Gradient Methods]] — the family TRPO refines with a constraint
