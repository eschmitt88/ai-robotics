---
kind: concept
name: "Off-Policy RL"
status: seedling
added: "2026-07-01"
sources: [haarnoja2018soft]
related_concepts: ["On-Policy RL", "Soft Actor-Critic", "Reinforcement Learning"]
related_experiments: []
tags: [rl, sample-efficiency, replay]
---

# Off-Policy RL

## Definition

Off-policy RL learns from experience drawn from a replay buffer collected
by earlier (or other) policies, rather than requiring fresh samples from
the current policy.

## Why it matters here

Reusing stored experience is the source of SAC's sample efficiency, which
is decisive when environment steps — not GPU FLOPs — are the bottleneck.

## Connections

- [[On-Policy RL]] — the contrasting regime that discards data each iteration
- [[Soft Actor-Critic]] — the flagship off-policy algorithm used here
- [[Reinforcement Learning]] — the paradigm this data-usage strategy sits within
