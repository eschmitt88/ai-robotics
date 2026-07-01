---
name: index
description: Entry-point index for this project's knowledge graph.
---

# Index

Orientation for the project knowledge graph. Updated by `/wrap`, `/ingest`,
and `/new-experiment`.

## Maps of Content

(promote a cluster of ≥5 related concepts into `mocs/<theme>.md`)

Four ripe clusters already exist in `concepts/` (each ≥7 notes), ready to
promote:
- **Reinforcement learning** — 13 concepts ([[Soft Actor-Critic]], [[Proximal Policy Optimization]], …)
- **Imitation learning & diffusion policies** — 15 concepts ([[Diffusion Policy]], [[Action Chunking]], …)
- **Vision-language-action models** — 7 concepts ([[Vision-Language-Action Model]], …)
- **Simulation & sim-to-real** — 8 concepts ([[GPU-Parallelized Simulation]], [[Sim-to-Real Transfer]], …)

## Active experiments

(list of `experiments/YYYY-MM-DD-<slug>/` folders currently in flight)

## Open questions

- What continuous-control RL actually fits in 16 GB, and how do
  [[Soft Actor-Critic]] (off-policy) and [[Proximal Policy Optimization]]
  (on-policy) trade sample-efficiency vs wall-clock on a single GPU?
- Can [[Diffusion Policy]] be reproduced on a lightweight sim manipulation
  task within the compute envelope, and how sensitive is it to
  [[Action Chunking]] horizon and [[DDIM]] inference steps?
- Where does the 16 GB ceiling actually bite for VLAs — is quantized
  [[Vision-Language-Action Model]] inference / [[LoRA Fine-Tuning]] feasible,
  or inference-only?
- How much does [[GPU-Parallelized Simulation]] (ManiSkill3 / MuJoCo
  Playground) change what a single-GPU learner can attempt?
