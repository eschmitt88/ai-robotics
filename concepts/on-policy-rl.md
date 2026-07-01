---
kind: concept
name: "On-Policy RL"
status: seedling
added: "2026-07-01"
sources: [schulman2017proximal]
related_concepts: ["Off-Policy RL", "Proximal Policy Optimization", "GPU-Parallelized Simulation", "Reinforcement Learning"]
related_experiments: []
tags: [rl, on-policy, parallelism]
---

# On-Policy RL

## Definition

On-policy RL updates only from data collected by the current policy,
discarding that data after each iteration rather than reusing it.

## Why it matters here

This defines PPO's sample-efficiency profile and is precisely why
massively-parallel simulation on one GPU pays off — it makes the constant
demand for fresh samples affordable.

## Connections

- [[Off-Policy RL]] — the contrasting replay-based regime
- [[Proximal Policy Optimization]] — the canonical on-policy algorithm here
- [[GPU-Parallelized Simulation]] — the compute that makes on-policy sampling cheap
- [[Reinforcement Learning]] — the paradigm this data regime belongs to
