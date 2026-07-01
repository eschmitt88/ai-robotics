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
- Where does the 16 GB ceiling actually bite for VLAs — is quantized
  [[Vision-Language-Action Model]] inference / [[LoRA Fine-Tuning]] feasible,
  or inference-only?
- How much does [[GPU-Parallelized Simulation]] (ManiSkill3 / MuJoCo
  Playground) change what a single-GPU learner can attempt?
