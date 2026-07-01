---
kind: paper
title: "OpenVLA: An Open-Source Vision-Language-Action Model"
authors: [Moo Jin Kim, Karl Pertsch, Siddharth Karamcheti, Ted Xiao, Ashwin Balakrishna, Suraj Nair, Rafael Rafailov, Ethan Foster, Grace Lam, Pannag Sanketi, Quan Vuong, Thomas Kollar, Benjamin Burchfiel, Russ Tedrake, Dorsa Sadigh, Sergey Levine, Percy Liang, Chelsea Finn]
institutions: ["Stanford University", "UC Berkeley", "Toyota Research Institute", "Google DeepMind", "Physical Intelligence", "MIT"]
year: 2024
venue: "CoRL 2024"
peer_reviewed: true
url: https://arxiv.org/abs/2406.09246
code_url: https://openvla.github.io
citations: null
source: "raw/papers/kim2024openvla.pdf"
added: "2026-07-01"
relevance: 5
credibility: 5
status: read
related_experiments: []
related_concepts: ["Vision-Language-Action Model", "LoRA Fine-Tuning", "Behavior Cloning", "Foundation Models for Robotics", "Action Tokenization", "Model Quantization", "Open X-Embodiment", "Fused Vision Encoder"]
tags: [vla, robotics, foundation-models, imitation-learning, manipulation, lora, quantization, llama, open-source]
---

# OpenVLA: An Open-Source Vision-Language-Action Model

## TL;DR

OpenVLA is a fully open-source 7B-parameter [[Vision-Language-Action Model]] built by fine-tuning a Llama-2-7B VLM (with a fused DINOv2+SigLIP vision encoder) on 970k real robot episodes from [[Open X-Embodiment]]. It beats the closed 55B RT-2-X by 16.5% absolute on generalist manipulation with 7x fewer parameters, and — crucially for a single-GPU learner — can be adapted via [[LoRA Fine-Tuning]] and served in int4 down to 7 GB VRAM without hurting task success.

## Claims

- A 7B open VLA outperforms the 55B closed RT-2-X by **16.5% absolute success** across 29 tasks on WidowX and Google robot embodiments, despite being ~8x smaller.
- Fine-tuned OpenVLA outperforms from-scratch Diffusion Policy by **20.4%** on diverse multi-instruction/multi-object tasks, and is the only method to clear ≥50% success across all seven tested Franka tasks.
- **First** to demonstrate parameter-efficient fine-tuning (LoRA) and quantization for VLAs: LoRA (rank 32) matches full fine-tuning while training only **1.4% of parameters**, fitting a new-task adaptation into 10–15 h on a *single* A100 (8x compute reduction).
- **int4 quantization matches bf16 task success** (71.9% vs 71.3% on BridgeData V2) while cutting inference memory from 16.8 GB to **7.0 GB**.
- Full stack — model checkpoints, PyTorch training pipeline, fine-tuning notebooks, remote inference server — is released open-source (data, weights, code).

## Methods

- **Architecture** (Fig. 2): three components — (1) a fused vision encoder concatenating [[Fused Vision Encoder|DINOv2 + SigLIP]] features channel-wise (~600M params, DINOv2 adds spatial reasoning), (2) a 2-layer MLP projector into the LLM embedding space, (3) a Llama-2-7B LLM backbone. Built on the Prismatic-7B VLM.
- **[[Action Tokenization]]**: 7-DoF actions (Δx, Δθ, Δgrip) discretized per-dimension into 256 bins between the 1st and 99th percentile of training actions. The 256 *least-used* tokens in the Llama vocabulary are overwritten with these action tokens (the Llama tokenizer only reserves 100 special tokens, too few). Trained with standard next-token cross-entropy on the action tokens only — end-to-end [[Behavior Cloning]].
- **Training data**: curated from Open X-Embodiment (70+ datasets, 2M+ trajectories); restricted to manipulation datasets with a 3rd-person camera and single-arm end-effector control, then re-weighted with Octo's data-mixture heuristics → **970k episodes**. DROID was tried at 10% weight but dropped from the final third of training (action-token accuracy stayed low).
- **Compute**: 64 A100s for 14 days (**21,500 A100-hours**), batch size 2048, **27 epochs** (far more than typical 1–2 for VLMs; needed until action-token accuracy >95%), fixed LR 2e-5, 224×224 resolution (384px gave no gain at 3x cost). **Fine-tuning the vision encoder during training was found crucial** — contradicting VLM practice of freezing it.
- **Efficient adaptation**: compared full FT, last-layer-only, frozen-vision, sandwich, and LoRA (ranks 32/64). Inference quantization tested at bf16/int8/int4.

## Results

- **Out-of-the-box** (Fig. 3/4): highest overall success on BridgeData V2 and Google robot; strongly beats RT-1-X (35M) and Octo (93M); comparable-or-better than RT-2-X (55B) everywhere except semantic generalization (where RT-2-X's Internet co-training helps).
- **Fine-tuning to new setups** (Fig. 5, Franka-Tabletop + Franka-DROID): highest aggregate success; generalist pretraining (OpenVLA/Octo) wins on diverse multi-instruction tasks, while Diffusion Policy is competitive on narrow single-instruction tasks (smoother, more dexterous trajectories).
- **PEFT (Table 1)**: LoRA rank-32 = 68.2% success at 97.6M train params / 59.7 GB VRAM (batch 16); full FT = 69.7% / 7188M params / 163 GB. Last-layer-only (30.3%) and frozen-vision (47.0%) are poor — vision-encoder adaptation matters.
- **Quantization (Table 2)**: bf16 71.3% / 16.8 GB; int8 58.1% / 10.2 GB (slower inference lowers control freq, hurting success); int4 71.9% / **7.0 GB**.
- **Inference**: 15 GB in bf16, ~6 Hz on an RTX 4090.

## Critique / open questions

- **Single-image, no proprioception, no history** — the authors flag this as the top limitation; real setups are multi-sensor. No action chunking, so throughput caps out below high-frequency (e.g. 50 Hz ALOHA) control.
- **Reliability ceiling**: success rates typically <90%; not yet deployment-grade.
- **Fine-tuning still needs a big GPU**: even LoRA reports ~60 GB VRAM at batch 16, so it does *not* fit a 16 GB card at that batch — a single-GPU learner must shrink batch/rank or use gradient accumulation/8-bit optimizers. Only *inference* (int4, 7 GB) comfortably fits 16 GB.
- **Quantization framing is subtle**: int4 matching bf16 depends on maintaining control frequency; int8 lost accuracy purely from slower inference on the test GPU, not from precision — an artifact of closed-loop real-robot eval that may not generalize.
- No sim experiments in the main text (deferred to appendix); this project is simulation-first, so replication would need the sim fine-tuning recipe.

## Trust signals

- **Credibility:** 5 — Stanford / UC Berkeley / TRI / Google DeepMind / Physical Intelligence / MIT authorship (Finn, Levine, Liang, Tedrake, Sadigh); published at CoRL 2024 (peer-reviewed); fully open-source weights, data mixture, and training code with reproducible pipeline; became a widely-cited standard VLA baseline.

## Follow-up

- Reproduce **int4 inference on the 16 GB GPU** — verify the 7 GB footprint and measure control frequency vs the RTX 4090's ~6 Hz.
- Prototype **LoRA fine-tuning that fits 16 GB**: rank 32 with small batch + gradient accumulation + 8-bit optimizer, on a small Franka-style sim task, to test whether the PEFT claim holds at consumer scale.
- Compare against a from-scratch [[Behavior Cloning]] / Diffusion Policy baseline in sim to feel the pretraining benefit on multi-instruction tasks.
- Read the successor work on action chunking / continuous action heads (the flagged throughput + dexterity gap) — e.g. OpenVLA-OFT and flow/diffusion action experts.
