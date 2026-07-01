---
kind: concept
name: "Maximum Entropy RL"
status: seedling
added: "2026-07-01"
sources: [haarnoja2018soft]
related_concepts: ["Soft Actor-Critic", "Off-Policy RL", "Reinforcement Learning"]
related_experiments: []
tags: [rl, entropy, exploration]
---

# Maximum Entropy RL

## Definition

Maximum-entropy RL augments the standard reward objective with a
policy-entropy bonus (weighted by a temperature α) so the agent seeks to
succeed while acting as randomly as possible.

## Why it matters here

It is the framework driving SAC's exploration and cross-seed stability,
which matters when a single-GPU budget limits how many seeds you can
afford to average over.

## Connections

- [[Soft Actor-Critic]] — the algorithm that operationalizes this objective
- [[Off-Policy RL]] — the data regime SAC pairs with the entropy objective
- [[Reinforcement Learning]] — the base objective this framework modifies
