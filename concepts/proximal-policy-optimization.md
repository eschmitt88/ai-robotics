---
kind: concept
name: "Proximal Policy Optimization"
status: seedling
added: "2026-07-01"
sources: [schulman2017proximal]
related_concepts: ["Clipped Surrogate Objective", "Generalized Advantage Estimation", "Trust Region Policy Optimization", "On-Policy RL", "Actor-Critic Methods"]
related_experiments: []
tags: [rl, on-policy, algorithms]
---

# Proximal Policy Optimization

## Definition

PPO is a first-order policy-gradient method that uses a clipped surrogate
objective to run multiple SGD epochs over each batch of experience without
taking destructively large policy steps.

## Why it matters here

PPO is the de-facto default on-policy RL algorithm and the natural RL
baseline for the project, pairing especially well with GPU-parallel sim.

## Connections

- [[Clipped Surrogate Objective]] — PPO's core stability mechanism
- [[Generalized Advantage Estimation]] — PPO's default advantage estimator
- [[Trust Region Policy Optimization]] — the predecessor PPO simplifies
- [[On-Policy RL]] — the data regime PPO operates in
- [[Actor-Critic Methods]] — the family PPO instantiates
