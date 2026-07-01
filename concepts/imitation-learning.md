---
kind: concept
name: "Imitation Learning"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion, kim2024openvla, tao2024maniskill3, zhao2023learning]
related_concepts: [Behavior Cloning, Diffusion Policy, Vision-Language-Action Model, Reinforcement Learning, Sim-to-Real Transfer]
related_experiments: []
tags: [imitation, learning-from-demonstration, policy]
---

# Imitation Learning

## Definition

Learning control policies from expert demonstrations rather than from a
reward signal, treating the demonstration set as supervision for what to do
in each observed state.

## Why it matters here

It is the overarching problem class for the project's non-RL axis: when
reward is hard to specify but demonstrations are cheap to collect (or exist
in sim), imitation is often the fastest route to a working policy on a
single 16GB GPU.

## Connections

- [[Behavior Cloning]] — the simplest instantiation of imitation as
  supervised regression on observation-action pairs
- [[Diffusion Policy]] — a modern generative imitation method built on this
  paradigm
- [[Vision-Language-Action Model]] — scales imitation to instruction-
  conditioned generalist policies
- [[Reinforcement Learning]] — the complementary reward-driven paradigm on
  the project's other axis
- [[Sim-to-Real Transfer]] — imitation in sim must still bridge to hardware
