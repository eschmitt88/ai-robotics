---
kind: concept
name: "Manipulation Benchmark"
status: seedling
added: "2026-07-01"
sources: [tao2024maniskill3]
related_concepts: ["GPU-Parallelized Simulation", "Heterogeneous Simulation", "Sim-to-Real Transfer", "Imitation Learning", "Reinforcement Learning"]
related_experiments: []
tags: [benchmark, manipulation, evaluation]
---

# Manipulation Benchmark

## Definition

A curated, reproducible suite of contact-rich manipulation tasks with standardized
observations, action spaces, and reference baselines. It fixes the evaluation
protocol so results are comparable across methods and runs.

## Why it matters here

It provides ready-made tasks instead of forcing the learner to build simulations
from scratch, giving both RL and imitation experiments a common, low-friction
proving ground on one GPU.

## Connections

- [[GPU-Parallelized Simulation]] — the engine that makes running the benchmark's tasks fast
- [[Heterogeneous Simulation]] — supplies the task and object diversity the benchmark spans
- [[Reinforcement Learning]] — one class of methods the benchmark scores
- [[Imitation Learning]] — the other axis the benchmark evaluates via demonstration-trained policies
- [[Sim-to-Real Transfer]] — benchmark tasks often pair with real or twin evaluation to gauge transfer
