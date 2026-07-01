---
kind: paper
title: "Diffusion Policy: Visuomotor Policy Learning via Action Diffusion"
authors: ["Cheng Chi", "Zhenjia Xu", "Siyuan Feng", "Eric Cousineau", "Yilun Du", "Benjamin Burchfiel", "Russ Tedrake", "Shuran Song"]
institutions: ["Columbia University", "Toyota Research Institute", "MIT", "Stanford University"]
year: 2023
venue: "RSS 2023 (extended journal version, IJRR)"
peer_reviewed: true
url: https://arxiv.org/abs/2303.04137
code_url: https://diffusion-policy.cs.columbia.edu
citations: null
source: "raw/papers/chi2023diffusion.pdf"
added: "2026-07-01"
relevance: 5
credibility: 5
status: read
related_experiments: []
related_concepts: ["Diffusion Policy", "Behavior Cloning", "Imitation Learning", "Denoising Diffusion Probabilistic Models", "Action Chunking", "Receding Horizon Control", "Multimodal Action Distribution", "Energy-Based Model", "Score Function", "FiLM Conditioning", "Position vs Velocity Control", "DDIM"]
tags: [diffusion-policy, imitation-learning, visuomotor, manipulation, behavior-cloning, ddpm]
---

# Diffusion Policy: Visuomotor Policy Learning via Action Diffusion

## TL;DR
Diffusion Policy recasts a robot's visuomotor policy as a conditional [[Denoising Diffusion Probabilistic Models|DDPM]] over *action sequences*: instead of regressing an action from an observation, the network learns the score/gradient of the action-distribution and refines Gaussian noise into an action chunk over K denoising steps, conditioned on visual observations. This buys three properties that ordinary [[Behavior Cloning]] lacks — faithful [[Multimodal Action Distribution|multimodal]] action modeling, graceful scaling to high-dimensional action sequences, and stable training — and yields an average **46.9%** success-rate improvement over prior state-of-the-art across 15 tasks / 4 benchmarks.

## Claims
- Formulating a visuomotor policy as conditional action diffusion consistently beats prior BC methods (LSTM-GMM/BC-RNN, [[Energy-Based Model|IBC]], BET) on 15 simulated + real tasks, +46.9% average success rate.
- Diffusion naturally expresses **multimodal** action distributions (short-horizon: two valid ways to reach the same goal; long-horizon: arbitrary sub-goal ordering), where GMM/BET/IBC collapse to one mode or produce jittery mode-switching.
- Predicting **action sequences** with [[Receding Horizon Control]] (predict T_p steps, execute T_a) gives temporal consistency, robustness to idle actions, and latency tolerance (peak performance up to ~4 steps latency).
- Diffusion training is **stable** — it sidesteps the intractable normalization constant Z that makes [[Energy-Based Model|EBM/IBC]] training unstable, because it learns the [[Score Function]] where the ∇log Z term vanishes.
- **[[Position vs Velocity Control|Position control]]** outperforms velocity control for Diffusion Policy, reversing the usual BC preference, because DP better exploits action multimodality and suffers less compounding error.

## Methods
- **Backbone formulation:** conditional DDPM predicting noise ε on an action chunk A_t^k, conditioned on the latest T_o observation steps O_t. Inference runs K denoising steps (each a noisy gradient-descent step), executes only the first T_a actions, then re-plans.
- **Two architectures:** (1) a **CNN-based** 1D temporal U-Net with [[FiLM Conditioning]] injecting the observation feature channel-wise — robust default, little tuning; (2) a **time-series diffusion Transformer** (minGPT-style decoder with cross-attention to the observation embedding) that avoids the CNN's low-frequency over-smoothing and wins on high-rate/velocity-sensitive tasks, at the cost of more hyperparameter sensitivity.
- **Visual conditioning** (not joint modeling): the image encoder (ResNet-18, no pretraining, GroupNorm + spatial-softmax pooling, trained end-to-end) produces O_t used only as *conditioning*, so it runs once per inference rather than per denoising step — key for real-time control.
- **Noise schedule:** iDDPM Square Cosine works best. **[[DDIM]]** decouples training (100) and inference (10) iterations, giving ~0.1s latency on a 3080.

## Results
- **Simulation (RoboMimic, Push-T, Block Push, Franka Kitchen):** state and image variants, +46.9% average. E.g. Transport ph state 1.00/0.94 vs LSTM-GMM 0.76; ToolHang 1.00 vs 0.67; Kitchen p4 0.99/0.96 vs LSTM-GMM 0.34 (+213%); Block Push p2 0.91/0.94.
- **Multimodality:** on Push-T, DP commits to a single mode per rollout while approaching the contact point equally left/right; baselines bias or jitter.
- **Real world:** Push-T IoU 0.80 (E2E) vs human 0.84, IBC 0.19, LSTM-GMM 0.25; also demonstrated on 6DoF mug flip (0.9), sauce pouring/spreading, and three **bimanual** tasks (egg beater 0.55, mat unrolling 0.75, shirt folding 0.75) — all with no per-task hyperparameter tuning.
- **Ablations:** action horizon of ~8 optimal; end-to-end trained vision beats frozen pretrained encoders; finetuning a pretrained encoder at 10x-lower LR is best overall (CLIP ViT-B/16 → 98%).

## Critique / open questions
- Inherits BC's ceiling: suboptimal/inadequate demonstrations cap performance; no mechanism to exceed the demonstrator. Authors point to RL/offline-RL extensions (IDQL, etc.) as future work.
- Higher compute and inference latency than LSTM-GMM; multi-step denoising is the cost of expressivity. Consistency models / better solvers could shrink this — relevant on a **single 16GB GPU**, where the CNN backbone + ResNet-18 + short chunks are the practical fit.
- Real-world numbers come from small trial counts (20 rollouts, best checkpoint) — sound but not a large statistical sample.
- Transformer variant is hyperparameter-sensitive; the paper recommends starting with the CNN variant, which matters for a solo simulation-first workflow.

## Trust signals
- **Credibility:** 5 — Columbia + Toyota Research Institute + MIT (Tedrake) + Stanford (Song); published at RSS 2023 with an extended journal version; public code, data, and training details at diffusion-policy.cs.columbia.edu; broad and now heavily-cited benchmark evaluation across 4 standard suites. Among the most influential robot-learning papers of its cohort.

## Follow-up
- Reproduce Push-T (state variant) with the CNN U-Net backbone as a first single-GPU experiment — cheapest of the suite, exercises multimodality and receding-horizon control.
- Contrast against a plain MLP/[[Behavior Cloning]] baseline on the same split to feel the multimodality gap first-hand.
- Read the RoboMimic paper (Mandlekar 2021) for the benchmark and LSTM-GMM baseline; and DDPM (Ho 2020) / DDIM (Song 2021) for the generative machinery.
- Later: probe inference acceleration (DDIM step count, consistency models) under the 16GB latency budget.
