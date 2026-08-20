---
kind: concept
name: "Model-Based RL"
status: seedling
added: "2026-08-20"
sources: [hafner2023mastering]
related_concepts: ["World Model", "Reinforcement Learning", "Off-Policy RL", "On-Policy RL", "Continuous Control"]
related_experiments: []
tags: [rl, model-based, sample-efficiency]
---

# Model-Based RL

## Definition

Reinforcement learning that learns a model of environment dynamics and uses it —
by imagination, planning, or lookahead search — to improve the policy, rather than
learning the policy purely from observed transitions.

## Why it matters here

It is the third axis alongside [[On-Policy RL]] and [[Off-Policy RL]], and the one
the project has not yet touched. [[hafner2023mastering|DreamerV3]] is the natural probe: it sets state
of the art on both Proprio Control and Visual Control at 500k/1M steps, beats
[[Proximal Policy Optimization]] on all 8 of its benchmark domains, and trains one
agent per A100. The cost is complexity — five loss terms and an RSSM where
[[Soft Actor-Critic]] has a policy and two Q-functions.

## Connections

- [[World Model]] — the learned component that defines the family
- [[Off-Policy RL]] — shares replay-buffer reuse, but learns from real transitions only
- [[On-Policy RL]] — the sample-hungry contrast
- [[Continuous Control]] — where the sample-efficiency argument bites hardest
