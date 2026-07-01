---
kind: concept
name: "Actor-Critic Methods"
status: seedling
added: "2026-07-01"
sources: [haarnoja2018soft, schulman2017proximal]
related_concepts: ["Soft Actor-Critic", "Proximal Policy Optimization", "Policy Gradient Methods", "Generalized Advantage Estimation", "Reinforcement Learning"]
related_experiments: []
tags: [rl, algorithms, actor-critic]
---

# Actor-Critic Methods

## Definition

Actor-critic methods pair a policy (the actor) with a learned value
function (the critic) that supplies variance-reduced advantage estimates
to guide policy updates.

## Why it matters here

It is the architectural family both SAC and PPO belong to, so
understanding the actor/critic loss decomposition grounds every RL
baseline the project runs.

## Connections

- [[Soft Actor-Critic]] — an off-policy actor-critic with twin Q-critics
- [[Proximal Policy Optimization]] — an on-policy actor-critic using a clipped objective
- [[Policy Gradient Methods]] — the actor's update rule comes from this family
- [[Generalized Advantage Estimation]] — how the critic's value estimate becomes an advantage signal
- [[Reinforcement Learning]] — the broader paradigm this method family serves
