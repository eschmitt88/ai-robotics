---
kind: concept
name: "Parallel Rendering"
status: seedling
added: "2026-07-01"
sources: [tao2024maniskill3]
related_concepts: ["GPU-Parallelized Simulation", "Heterogeneous Simulation", "Manipulation Benchmark", "Diffusion Policy"]
related_experiments: []
tags: [simulation, rendering, vision]
---

# Parallel Rendering

## Definition

Simultaneously generating RGB, depth, and segmentation observations for all
parallel environments directly on-GPU with minimal per-frame overhead. The rendered
tensors stay on-device, feeding visual policies without a CPU round-trip.

## Why it matters here

This unlocks visual RL and visuomotor imitation at scale; the low-overhead on-GPU
path is what keeps thousands of camera streams inside a 16GB memory budget.

## Connections

- [[GPU-Parallelized Simulation]] — the physics-side counterpart; together they keep the full loop on-GPU
- [[Heterogeneous Simulation]] — rendering varied scenes per env is what makes visual generalization data possible
- [[Diffusion Policy]] — a visuomotor consumer of the RGB/depth observations rendered this way
- [[Manipulation Benchmark]] — supplies the standardized visual observation specs being rendered
