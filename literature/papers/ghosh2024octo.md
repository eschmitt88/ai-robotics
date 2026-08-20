---
kind: paper
title: "Octo: An Open-Source Generalist Robot Policy"
authors: [Dibya Ghosh, Homer Walke, Karl Pertsch, Kevin Black, Oier Mees, Sudeep Dasari, Joey Hejna, Tobias Kreiman, Ria Doshi, Charles Xu, Jianlan Luo, You Liang Tan, Lawrence Yunliang Chen, Pannag Sanketi, Quan Vuong, Ted Xiao, Dorsa Sadigh, Chelsea Finn, Sergey Levine]
institutions: ["UC Berkeley", "Stanford University", "Carnegie Mellon University", "Google DeepMind"]
year: 2024
venue: "Robotics: Science and Systems (RSS) 2024"
peer_reviewed: true
url: https://arxiv.org/abs/2405.12213
code_url: https://octo-models.github.io
citations: 1679
source: "raw/papers/ghosh2024octo.pdf"
added: "2026-08-20"
relevance: 5
credibility: 5
status: read
related_experiments: []
related_concepts: ["Generalist Robot Policy", "Vision-Language-Action Model", "Open X-Embodiment", "Cross-Embodiment Transfer", "Diffusion Action Head", "Action Chunking", "Goal-Image Conditioning", "Imitation Learning", "Foundation Models for Robotics", "Multimodal Action Distribution"]
tags: [vla, generalist-policy, open-x-embodiment, transformer, diffusion, imitation-learning, finetuning, single-gpu, open-source]
---

# Octo: An Open-Source Generalist Robot Policy

## TL;DR

Octo is a fully open-source transformer policy (27M "Small" / 93M "Base")
pretrained on 800k trajectories from [[Open X-Embodiment]] — the largest robot
manipulation demonstration mix to date. Its distinguishing feature is not raw
scale but **interface flexibility**: block-wise attention masking over
modality-specific token blocks lets you add a wrist camera, swap in
force-torque proprioception, or change the action space at *finetune* time
without re-initializing the pretrained backbone. Finetuning to a new robot
takes ~100 demos and **under 5 hours on a single 24 GB A5000** — which is what
makes it the realistic VLA entry point on this project's hardware, where
[[kim2024openvla|OpenVLA]]-scale (7B) or π0-scale training is not.

## Claims

- **First GRP that finetunes to new observation *and* action spaces.** Prior
  cross-embodiment models (RT-1-X, RT-2-X, RoboCat) lock downstream users into
  the pretraining sensor/action interface; Octo does not.
- **First fully open-source generalist manipulation policy** — checkpoints,
  pretraining pipeline, finetuning scripts, and OXE data loaders all released.
- **Beats RT-1-X zero-shot by 29%** absolute average success across three
  embodiments, and matches RT-2-X (55B) on the tested WidowX and RT-1 tasks at
  93M parameters.
- **Diffusion action head beats both alternatives**: a [[Diffusion Action Head]]
  models multi-modal action distributions that an MSE head cannot, while keeping
  the continuous precision that discretized-token heads lose.
- **Transformer-first beats ResNet-first at scale** — but the authors explicitly
  note the ordering *inverts* on small datasets, where ResNet encoders win.
- **Data diversity monotonically helps**: 25-dataset mix > 11-dataset RT-X mix >
  single-robot Bridge data.

## Methods

- **Architecture.** Language → `t5-base` (111M) embeddings; images → a *shallow*
  CNN patch encoder (deliberately not a heavy ResNet), then flattened patches.
  Tokens are assembled as `[task tokens | observation tokens | readout tokens]`
  with **block-wise causal masking**: observation tokens attend causally to
  same-or-earlier observations plus task tokens; readout tokens (BERT `[CLS]`
  analogues) passively read but are never attended *to*. Missing modalities are
  fully masked out. This masking structure is the whole trick — adding or
  removing an input block requires only a new positional embedding and encoder,
  not surgery on the backbone.
- **Action decoding.** A lightweight conditional-diffusion head (standard DDPM
  objective, cosine noise schedule) applied to readout embeddings, predicting an
  [[Action Chunking|action chunk]] of several consecutive actions. Crucially,
  **only one transformer forward pass per action prediction** — the K denoising
  steps run entirely inside the small head, so inference cost is near-MSE.
- **Data.** 25 curated Open-X datasets (from ~1.5M episodes → 800k). Filters:
  drop no-image datasets, drop non-delta-end-effector control, drop repetitive /
  low-res / niche sets. "More diverse" sets get 2× weight. Gripper action
  conventions aligned across sets (+1 open, 0 closed); missing camera channels
  zero-padded.
- **Task conditioning.** Hindsight goal relabeling for goal images; language
  instruction or goal image randomly zeroed per example so one model serves both.
  Only 56% of pretraining data has language annotations, 27% has wrist cameras.
- **Training cost.** Octo-Base: 300k steps, batch 2048, TPU v4-128 pod, 14 hours.
  Finetuning: 50k steps, cosine decay with linear warmup, full-model update
  (beating recipes that freeze subsets), ~5 h on one A5000.

## Results

- **Zero-shot** (Fig. 5): Octo (93M) > RT-1-X (35M) by 29% avg success across
  WidowX / UR5 / RT-1 Robot; ≈ RT-2-X (55B). Goal-image conditioning scores
  **25% higher than language** conditioning on WidowX.
- **Finetuning** (Table I, ~100 demos, 20 trials each, identical hyperparameters
  across all six domains): Octo **72%** avg vs 20% from-scratch
  ResNet+Transformer and 15% VC-1 pretrained-vision — a 52-point margin over the
  next best. Includes new-observation (force-torque, Berkeley Insertion: 70% vs
  10%/5%) and new-action-space (joint position, Berkeley Pick-Up: 60% vs 0%/0%)
  transfers.
- **Ablations** (Table II, WidowX, 40 trials): Octo-Small 83%; RT-X 11-dataset
  mix 60%; Bridge-only 43%; discretized actions **18%**; MSE actions 35%;
  ResNet-50+Transformer 70%.
- **Scaling** (Fig. 6): monotone zero-shot improvement Tiny (10M) → Small (27M)
  → Base (93M); Base is notably more robust to initial scene configuration.

## Critique / open questions

- **The discretized-action ablation (18%) is startlingly bad** relative to
  RT-1/RT-2's reported success with exactly that parameterization. This is a
  same-backbone, same-data comparison, so it is evidence about *this* setup, not
  a general verdict on [[Action Tokenization]] — π0 and OpenVLA reach strong
  numbers with discrete or flow-based heads. Worth holding loosely.
- **Wrist cameras hurt.** The authors report finetuning was often *stronger with
  the third-person camera alone*. They attribute this to only 27% of pretraining
  data having wrist views, but it is an unresolved negative result for anyone
  planning a wrist-camera setup.
- **Language conditioning underperforms goal-image conditioning by 25%** —
  awkward for a model whose headline framing is language instructability.
- All evaluation is **real-robot**, none in simulation. There is no reported
  sim benchmark number, so transferring these results to a sim-first workflow
  (this project's constraint) requires re-establishing the baseline.
- Trained purely on **optimal demonstrations** via imitation; no mechanism to
  exploit suboptimal or online interaction data. Authors flag this.
- Zero-shot degrades sharply on novel *behaviors* (flipping, precise insertion)
  even though it holds up on novel objects (Table VII) — generalization is
  visual, not behavioral.

## Trust signals

- **Credibility:** 5 — RSS 2024 (peer-reviewed), 1679 citations (Semantic
  Scholar, checked 2026-08-20), Sergey Levine / Chelsea Finn / Dorsa Sadigh
  author roster across Berkeley, Stanford, CMU, and Google DeepMind. Fully
  open-source: checkpoints, pretraining pipeline, finetuning scripts, and
  standalone OXE data loaders (JAX + PyTorch compatible). Evaluated on 9 real
  robot setups across 4 independent institutions — unusually strong external
  validity for a robotics paper. Negative results (wrist cameras, language vs
  goal images) are reported rather than buried.

## Follow-up

- **Relevance:** 5 — this is the most tractable VLA on a 16 GB card. Octo-Small
  (27M) and Octo-Base (93M) finetune in ~5 h on a 24 GB A5000; the Small model
  should fit 16 GB comfortably. It directly answers the project's open question
  about whether VLAs are inference-only here — for Octo-scale models, no.
- Candidate experiment: finetune Octo-Small on a [[tao2024maniskill3|ManiSkill3]] or
  [[zakka2025mujoco|MuJoCo Playground]] manipulation task from ~100 scripted/motion-planned
  demos, and measure the 16 GB memory ceiling and wall-clock vs the paper's
  A5000 numbers.
- Candidate experiment: replicate the **action-head ablation** (diffusion vs MSE
  vs discretized) in sim on one task. It is the cheapest ablation in the paper
  and the 18% discrete result is the most surprising claim to stress-test.
- Contrast with [[kim2024openvla|OpenVLA]] (7B, LoRA/quantized finetuning) on the same task to
  map the parameter-count/quality frontier under a fixed 16 GB budget — the two
  bracket the feasible range.
- The [[Diffusion Action Head]] here is a strict simplification of
  [[Diffusion Policy]]: denoising happens in a small head on top of a single
  backbone pass, not over the full visual encoder. Relevant to
  [[prasad2024consistency|Consistency Policy]]'s distillation argument — Octo has already paid most of
  that cost architecturally.
