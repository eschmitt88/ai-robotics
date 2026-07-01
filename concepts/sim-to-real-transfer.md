---
kind: concept
name: "Sim-to-Real Transfer"
status: seedling
added: "2026-07-01"
sources: [tao2024maniskill3]
related_concepts: ["Domain Randomization", "Digital Twins", "GPU-Parallelized Simulation", "Manipulation Benchmark", "Vision-Language-Action Model"]
related_experiments: []
tags: [sim-to-real, deployment, robustness]
---

# Sim-to-Real Transfer

## Definition

Training a policy entirely in simulation and deploying it on physical hardware
with little or no real-world fine-tuning. The challenge is closing the "reality
gap" between simulated and true dynamics, sensing, and appearance.

## Why it matters here

It is the bridge from cheap sim-first learning to actual robots, and a project
sub-theme in its own right: everything trained on one GPU is only useful if it
survives contact with the real world.

## Connections

- [[Domain Randomization]] — the primary technique for making a sim-trained policy robust enough to transfer
- [[Digital Twins]] — high-fidelity replicas used to benchmark transfer without touching hardware
- [[GPU-Parallelized Simulation]] — the training source whose output must cross the reality gap
- [[Manipulation Benchmark]] — provides the tasks whose transfer success is measured
- [[Vision-Language-Action Model]] — a policy class that likewise aims to generalize from broad data to real deployment
