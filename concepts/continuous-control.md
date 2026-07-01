---
kind: concept
name: "Continuous Control"
status: seedling
added: "2026-07-01"
sources: [haarnoja2018soft, schulman2017proximal, tao2024maniskill3]
related_concepts: ["Reinforcement Learning", "Soft Actor-Critic", "Proximal Policy Optimization", "Manipulation Benchmark"]
related_experiments: []
tags: [rl, control, action-space]
---

# Continuous Control

## Definition

Continuous control is RL and control in real-valued state and action
spaces — e.g. MuJoCo locomotion torques or robot end-effector poses —
rather than discrete action sets.

## Why it matters here

It is the target problem class for learning-based robotics and the
concrete setting in which the project's RL baselines (SAC, PPO on
ManiSkill3 tasks) are run and compared.

## Connections

- [[Reinforcement Learning]] — the paradigm applied to this real-valued problem class
- [[Soft Actor-Critic]] — an off-policy algorithm designed for continuous actions
- [[Proximal Policy Optimization]] — the on-policy default used on continuous-control tasks
- [[Manipulation Benchmark]] — the concrete continuous-control task suite used here
