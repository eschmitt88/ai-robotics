---
kind: concept
name: "Reinforcement Learning"
status: seedling
added: "2026-07-01"
sources: [haarnoja2018soft, schulman2017proximal, tao2024maniskill3]
related_concepts: ["Continuous Control", "Actor-Critic Methods", "Policy Gradient Methods", "Off-Policy RL", "On-Policy RL"]
related_experiments: []
tags: [rl, control, foundations]
---

# Reinforcement Learning

## Definition

Reinforcement learning is the paradigm of learning control policies by
maximizing cumulative reward through trial-and-error interaction with an
environment, formalized as a Markov decision process.

## Why it matters here

RL is the umbrella paradigm for one of this project's two learning axes;
GPU-parallel simulation makes its otherwise sample-hungry training
tractable on a single 16GB card.

## Connections

- [[Continuous Control]] — the real-valued action setting where the RL baselines here operate
- [[Actor-Critic Methods]] — the algorithmic family both SAC and PPO instantiate
- [[Policy Gradient Methods]] — the gradient-based route to optimizing an RL policy
- [[Off-Policy RL]] — one data-usage regime (replay-buffer) for RL updates
- [[On-Policy RL]] — the contrasting fresh-data regime for RL updates
