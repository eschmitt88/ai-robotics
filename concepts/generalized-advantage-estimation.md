---
kind: concept
name: "Generalized Advantage Estimation"
status: seedling
added: "2026-07-01"
sources: [schulman2017proximal]
related_concepts: ["Proximal Policy Optimization", "Actor-Critic Methods", "Policy Gradient Methods"]
related_experiments: []
tags: [rl, advantage, bias-variance]
---

# Generalized Advantage Estimation

## Definition

GAE is an exponentially-weighted (λ) multi-step advantage estimator that
interpolates between high-bias one-step TD and high-variance Monte-Carlo
returns to trade bias against variance.

## Why it matters here

It is PPO's default advantage estimator (typically λ=0.95) and one of the
most important practical knobs for getting good on-policy performance.

## Connections

- [[Proximal Policy Optimization]] — consumes GAE advantages in its clipped objective
- [[Actor-Critic Methods]] — GAE turns the critic's values into an advantage signal
- [[Policy Gradient Methods]] — supplies the low-variance advantage the gradient needs
