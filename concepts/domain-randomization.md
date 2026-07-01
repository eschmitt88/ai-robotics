---
kind: concept
name: "Domain Randomization"
status: seedling
added: "2026-07-01"
sources: [tao2024maniskill3]
related_concepts: ["Sim-to-Real Transfer", "Digital Twins", "Heterogeneous Simulation", "Parallel Rendering"]
related_experiments: []
tags: [sim-to-real, robustness, augmentation]
---

# Domain Randomization

## Definition

Randomizing simulation properties — camera pose, lighting, textures, and dynamics
parameters — during training so the resulting policy treats the real world as just
another sample from the training distribution. Variation forces invariance rather
than exact modeling.

## Why it matters here

It is the seminal, compute-cheap route to sim-to-real transfer, central to getting
a policy trained on one GPU to hold up on hardware without expensive real data
collection.

## Connections

- [[Sim-to-Real Transfer]] — the goal domain randomization exists to serve
- [[Digital Twins]] — a complementary, higher-fidelity alternative to blanket randomization
- [[Heterogeneous Simulation]] — shares the parallel-variation machinery, applied here for robustness rather than task coverage
- [[Parallel Rendering]] — makes randomizing visual appearance across many envs cheap
