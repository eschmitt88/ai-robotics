---
kind: concept
name: "Policy Gradient Methods"
status: seedling
added: "2026-07-01"
sources: [schulman2017proximal]
related_concepts: ["Proximal Policy Optimization", "Trust Region Policy Optimization", "Actor-Critic Methods", "Generalized Advantage Estimation", "Reinforcement Learning"]
related_experiments: []
tags: [rl, foundations, gradients]
---

# Policy Gradient Methods

## Definition

Policy gradient methods optimize a parameterized stochastic policy by
ascending an estimator of the gradient of expected return with respect to
the policy parameters.

## Why it matters here

This is the foundational family PPO belongs to; the instability of vanilla
policy gradients is exactly what PPO's clipping was designed to tame.

## Connections

- [[Proximal Policy Optimization]] — a modern, stabilized policy-gradient method
- [[Trust Region Policy Optimization]] — a constrained policy-gradient predecessor
- [[Actor-Critic Methods]] — pairs a critic with the policy gradient to cut variance
- [[Generalized Advantage Estimation]] — the advantage estimator plugged into the gradient
- [[Reinforcement Learning]] — the paradigm these methods optimize within
