---
kind: concept
name: "Position vs Velocity Control"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion]
related_concepts: [Diffusion Policy, Receding Horizon Control, Action Chunking, Continuous Control]
related_experiments: []
tags: [control, action-space, design-choice]
---

# Position vs Velocity Control

## Definition

The choice of action-space representation for a policy: commanding target
end-effector or joint positions versus commanding velocities to be
integrated by the controller.

## Why it matters here

Diffusion Policy finds position control consistently outperforms velocity
control — an actionable, transferable default that removes a design guess
when setting up any new imitation task on this project.

## Connections

- [[Diffusion Policy]] — the source of the position-control recommendation
- [[Receding Horizon Control]] — executes commands in whichever action space
  is chosen
- [[Action Chunking]] — chunks are sequences in the chosen action space
- [[Continuous Control]] — the real-valued setting where this choice applies
