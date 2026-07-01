---
kind: concept
name: "GPU-Parallelized Simulation"
status: seedling
added: "2026-07-01"
sources: [tao2024maniskill3]
related_concepts: ["Parallel Rendering", "Heterogeneous Simulation", "Manipulation Benchmark", "Reinforcement Learning", "On-Policy RL"]
related_experiments: []
tags: [simulation, gpu, throughput]
---

# GPU-Parallelized Simulation

## Definition

Running many robot environments concurrently on the GPU so that physics
stepping scales with GPU cores rather than CPU threads, yielding orders-of-magnitude
higher sample throughput. This keeps the whole environment-to-policy loop resident
on the accelerator, avoiding CPU-GPU transfer stalls.

## Why it matters here

This is ManiSkill3's core contribution and the reason sim-first RL is feasible on
one card: hours-long training runs collapse to minutes because thousands of
environments step in parallel on a single 16GB GPU.

## Connections

- [[Parallel Rendering]] — the visual counterpart, batching image generation across the same parallel envs
- [[Heterogeneous Simulation]] — the parallel envs need not be identical, enabling per-env scene variety
- [[Manipulation Benchmark]] — the task suite that this throughput makes cheap to train and evaluate on
- [[On-Policy RL]] — massively parallel sampling is what makes sample-hungry on-policy methods practical
- [[Reinforcement Learning]] — the paradigm whose environment-step bottleneck this directly removes
