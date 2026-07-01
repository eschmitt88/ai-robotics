---
kind: concept
name: "Digital Twins"
status: seedling
added: "2026-07-01"
sources: [tao2024maniskill3]
related_concepts: ["Sim-to-Real Transfer", "Domain Randomization", "Manipulation Benchmark", "GPU-Parallelized Simulation"]
related_experiments: []
tags: [sim-to-real, evaluation, digital-twin]
---

# Digital Twins

## Definition

High-fidelity simulated replicas of real-world setups, built to evaluate
real-trained (or sim-trained) policies without physical hardware. The twin mirrors
a specific rig's geometry, cameras, and dynamics closely enough to predict real
performance.

## Why it matters here

They enable fast, hardware-free policy benchmarking — SIMPLER-style twins let a
single-GPU learner estimate real-robot success rates before ever touching a robot.

## Connections

- [[Sim-to-Real Transfer]] — twins measure the transfer gap they aim to close
- [[Domain Randomization]] — the opposite fidelity strategy: match reality precisely rather than randomize over it
- [[Manipulation Benchmark]] — twins often serve as the evaluation backend for benchmark tasks
- [[GPU-Parallelized Simulation]] — the same engine renders and steps the twin at scale
