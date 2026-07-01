---
kind: concept
name: "Heterogeneous Simulation"
status: seedling
added: "2026-07-01"
sources: [tao2024maniskill3]
related_concepts: ["GPU-Parallelized Simulation", "Parallel Rendering", "Domain Randomization", "Manipulation Benchmark"]
related_experiments: []
tags: [simulation, generalization, gpu]
---

# Heterogeneous Simulation

## Definition

Simulating different scenes, geometries, and articulations in each parallel
environment at once, rather than replicating one identical environment. Each GPU
env can hold a distinct object, layout, or physical configuration.

## Why it matters here

This lets a single RL run train across a whole object dataset simultaneously,
producing generalizable manipulation policies without serially iterating over
assets — a decisive efficiency win on one GPU.

## Connections

- [[GPU-Parallelized Simulation]] — the substrate; heterogeneity is what varies across the parallel batch
- [[Parallel Rendering]] — renders the differing scenes so visual policies see the variety
- [[Domain Randomization]] — a related but distinct axis of variation aimed specifically at sim-to-real robustness
- [[Manipulation Benchmark]] — supplies the object/task diversity that populates the heterogeneous envs
