---
name: index
description: Entry-point index for this project's knowledge graph.
---

# Index

Orientation for the project knowledge graph. Updated by `/wrap`, `/ingest`,
and `/new-experiment`.

## Maps of Content

- [[mocs/reinforcement-learning]] — Reinforcement Learning for Control (13 concepts)
- [[mocs/imitation-and-diffusion-policies]] — Imitation Learning & Diffusion Policies (15 concepts)
- [[mocs/vision-language-action-models]] — Vision-Language-Action Models (7 concepts)
- [[mocs/simulation-and-sim-to-real]] — Simulation & Sim-to-Real (8 concepts)

## Active experiments

(list of `experiments/YYYY-MM-DD-<slug>/` folders currently in flight)

## Open questions

- What continuous-control RL actually fits in 16 GB, and how do
  [[Soft Actor-Critic]] (off-policy) and [[Proximal Policy Optimization]]
  (on-policy) trade sample-efficiency vs wall-clock on a single GPU?
- Can [[Diffusion Policy]] be reproduced on a lightweight sim manipulation
  task within the compute envelope, and how sensitive is it to
  [[Action Chunking]] horizon and [[DDIM]] inference steps?
- Where does the 16 GB ceiling actually bite for VLAs? Partly answered by the
  2026-08-20 ingest: [[ghosh2024octo|Octo]]-scale (27M/93M) finetunes in ~5 h on one consumer
  card, while [[black2024pi0|π0]] (3.3B) runs inference in 73 ms on a 4090 but cannot be
  trained here. Open: where between 93M and 3.3B does finetuning stop fitting?
- How much does [[GPU-Parallelized Simulation]] (ManiSkill3 / MuJoCo
  Playground) change what a single-GPU learner can attempt? Neither paper
  benchmarks on 16 GB, and MuJoCo Playground reports the bottleneck has already
  moved from data collection to gradient updates (9-43% of training time is
  physics+render) — so more parallel envs may not help.
- Three distinct answers now exist to diffusion's inference cost:
  [[Diffusion Action Head]] (architectural), [[Consistency Distillation]]
  (training-time), and [[Flow Matching]] (straighter path). Which wins under a
  fixed 16 GB budget once *total* cost including training is counted?
- Is model-based RL ([[World Model]] / [[hafner2023mastering|DreamerV3]]) worth its complexity here, or
  does cheap GPU-parallel sampling erase its sample-efficiency advantage?
