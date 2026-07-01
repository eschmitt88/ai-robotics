---
kind: concept
name: "Soft Actor-Critic"
status: seedling
added: "2026-07-01"
sources: [haarnoja2018soft]
related_concepts: ["Actor-Critic Methods", "Maximum Entropy RL", "Off-Policy RL", "Reparameterization Trick", "Continuous Control"]
related_experiments: []
tags: [rl, off-policy, algorithms]
---

# Soft Actor-Critic

## Definition

Soft Actor-Critic (SAC) is an off-policy maximum-entropy actor-critic
algorithm with a stochastic Gaussian actor, twin soft Q-critics to curb
overestimation, and a replay buffer.

## Why it matters here

SAC is a strong default off-policy RL algorithm whose sample efficiency
makes it well-suited to single-GPU, sim-first experimentation.

## Connections

- [[Actor-Critic Methods]] — the family SAC instantiates
- [[Maximum Entropy RL]] — the objective that adds SAC's entropy bonus
- [[Off-Policy RL]] — the replay-based regime giving SAC its sample efficiency
- [[Reparameterization Trick]] — how SAC gets low-variance gradients through its stochastic actor
- [[Continuous Control]] — the real-valued action setting SAC targets
