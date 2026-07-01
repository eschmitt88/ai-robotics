---
kind: paper
title: "Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware"
authors: ["Tony Z. Zhao", "Vikash Kumar", "Sergey Levine", "Chelsea Finn"]
institutions: ["Stanford University", "UC Berkeley", "Meta"]
year: 2023
venue: "RSS 2023"
peer_reviewed: true
url: https://arxiv.org/abs/2304.13705
code_url: https://github.com/tonyzhaozh/aloha
citations: null
source: "raw/papers/zhao2023learning.pdf"
added: "2026-07-01"
relevance: 5
credibility: 5
status: read
related_experiments: []
related_concepts: ["Action Chunking", "Imitation Learning", "Behavior Cloning", "Conditional VAE Policy", "Temporal Ensembling", "Compounding Error", "Bimanual Manipulation"]
tags: [imitation-learning, behavior-cloning, action-chunking, cvae, transformer, bimanual-manipulation, aloha, act, low-cost-robotics, single-gpu]
---

# Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware

## TL;DR

Introduces **ACT (Action Chunking with Transformers)**, an imitation-learning
algorithm that predicts *sequences* of future actions (chunks) with a
transformer-based [[Conditional VAE Policy]], plus **ALOHA**, a <$20k open-source
bimanual teleoperation rig. From only ~10 minutes (50 demos) of human
demonstrations, ACT learns 6 fine-grained real-world bimanual tasks at 80-90%
success, drastically outperforming prior [[Behavior Cloning]] methods.

## Claims

- **Action chunking** — predicting the next `k` actions at once instead of one
  step at a time — reduces the effective task horizon by `k`-fold and mitigates
  the [[Compounding Error]] problem of [[Behavior Cloning]]. Also helps with
  temporally-correlated confounders (e.g. demonstration pauses) that break
  Markovian single-step policies.
- **[[Temporal Ensembling]]** (querying the policy every timestep and
  exponentially-weighted-averaging overlapping chunk predictions) yields smooth,
  precise motion at no training cost — only extra inference compute.
- Training the policy as a **[[Conditional VAE Policy]]** (CVAE) is *essential*
  for learning from noisy, multimodal human data — removing it drops human-data
  success from 35.3% to 2% (but makes no difference on deterministic scripted
  data).
- Low-cost, imprecise hardware (ViperX arms, Logitech webcams) can perform
  fine, contact-rich, dynamic manipulation via closed-loop learned visual
  feedback, without industrial-grade proprioception.
- Achieves this from a **single 16GB-class GPU** budget (see Methods) with a
  compact 80M-param model.

## Methods

- **ALOHA hardware**: two leader (WidowX, $330) + two follower (ViperX 6-DoF,
  $5.6k) arms, joint-space leader→follower teleoperation, 3D-printed
  "see-through" grippers and "handle-and-scissor" leader mechanism. 4x Logitech
  C922x webcams (2 wrist, 1 front, 1 top) at 480×640. Teleop + recording at
  **50Hz**; total system <$20k.
- **ACT architecture** (Fig 4): a CVAE with a transformer encoder + decoder.
  - *CVAE encoder* (train-only, discarded at test): BERT-like transformer over
    `[CLS] + joints + action-sequence` (length `k+2`), outputs mean/variance of
    style variable `z` (proprioception + actions only, no images for speed).
  - *CVAE decoder = the policy*: ResNet18 per-camera image encoders → 300×512
    feature tokens each (with 2D sinusoidal pos-emb), 4 cams → 1200×512, plus
    current joints and `z`. Transformer encoder synthesizes; transformer decoder
    cross-attends with fixed `k×512` position embeddings → MLP → `k×14` absolute
    joint-position targets (7+7 DoF).
- **Losses**: L1 reconstruction (L1 noted to beat L2 for precise action
  modeling) + β-weighted KL to N(0,I) prior. Absolute joint targets beat delta
  targets.
- **Inference**: set `z=0` (prior mean, deterministic). FIFO buffers store
  overlapping predictions per timestep; temporal ensemble weights are
  `w_i = exp(-m·i)`, smaller `m` = faster incorporation of new observations.
- **Training cost**: ~80M params, trained from scratch per task, **~5 hours on a
  single 11GB RTX 2080 Ti**, ~0.01s inference. Chunk size `k` and β are the key
  hyperparameters.

## Results

- **Real-world (25 trials, 1 seed, 25-100 demos)**: Slot Battery 96%, Slide
  Ziploc 88%, Open Cup 84%, Put On Shoe 92%, Prep Tape 64%, Thread Velcro 20%
  (hardest — thin, low-contrast tie hard to perceive). Best baseline BeT scores
  **0%** past the first subtask on essentially all of these.
- **Simulated (3 seeds, 50 trials)**: Cube Transfer 86%/50% (scripted/human
  final), Bimanual Insertion 32%/20% — outperforms BC-ConvMLP, BeT, RT-1, VINN
  by 59/49/29/20 points on the four tabulated tasks.
- **Ablation — chunking**: success rises from 1% at `k=1` (no chunking) to 44%
  at `k=100`, tapering slightly at `k=200,400` (near open-loop). Chunking also
  lifts baselines BC-ConvMLP and VINN when retrofitted.
- **Ablation — temporal ensemble**: +3.3% for ACT, +4% for BC-ConvMLP, but
  -20% for the non-parametric VINN (which retrieves ground-truth actions).
- **Ablation — CVAE**: crucial for human data (35.3%→2% without), irrelevant
  for scripted data.
- **User study**: 50Hz teleop vs 5Hz gives 62% faster task completion
  (p<0.001), motivating high-frequency control.

## Critique / open questions

- Single seed / 25 trials on real-world tasks → wide confidence intervals; the
  headline 80-90% numbers are point estimates.
- Thread Velcro at 20% shows the perception bottleneck: ACT still fails when the
  object is small/low-contrast in the image — no depth, no tactile sensing.
- Per-task training from scratch; no multi-task or language conditioning here
  (later ALOHA/ACT work addresses this). No generalization claims beyond the
  15cm reference line randomization.
- `z=0` at test discards the CVAE's multimodality — the policy is effectively
  deterministic at inference; the CVAE mainly regularizes training against noisy
  demos rather than enabling multimodal rollouts.
- Absolute-joint-target design ties the policy to ALOHA's low-level PID; the
  "force is implicit in leader-follower difference" trick is hardware-specific.
- Stated 5h/11GB training makes replication trivial on the project's 16GB GPU —
  a strong candidate for a [[Behavior Cloning]] baseline in sim (see Follow-up).

## Trust signals

- **Credibility:** 5 — Stanford / UC Berkeley / Meta (Zhao, Kumar, Levine,
  Finn), published at RSS 2023, fully open-sourced hardware + software + sim
  tasks (github.com/tonyzhaozh/aloha), and a now-foundational, heavily-cited
  reference for real-world imitation learning. Strong ablations isolate each
  design choice.

## Follow-up

- Replicate ACT on the two MuJoCo sim tasks (Cube Transfer, Bimanual Insertion)
  as a single-GPU [[Imitation Learning]] baseline — the ~5h/11GB budget fits
  this project's 16GB constraint. Candidate for `/derive-experiment`.
- Ablate chunk size `k` and temporal-ensemble `m` on sim to reproduce Fig 8(a).
- Compare ACT's CVAE-over-chunks against diffusion-policy action generation
  (contemporaneous line of work) for the multimodal-demo problem.
- Read successor work: Mobile ALOHA, ALOHA 2, and ACT scaling / language-
  conditioned variants.
