---
kind: paper
title: "π0: A Vision-Language-Action Flow Model for General Robot Control"
authors: [Kevin Black, Noah Brown, Danny Driess, Adnan Esmail, Michael Equi, Chelsea Finn, Niccolo Fusai, Lachy Groom, Karol Hausman, Brian Ichter, Szymon Jakubczak, Tim Jones, Liyiming Ke, Sergey Levine, Adrian Li-Bell, Mohith Mothukuri, Suraj Nair, Karl Pertsch, Lucy Xiaoyang Shi, James Tanner, Quan Vuong, Anna Walling, Haohuan Wang, Ury Zhilinsky]
institutions: ["Physical Intelligence"]
year: 2024
venue: "Robotics: Science and Systems (RSS) 2025"
peer_reviewed: true
url: https://arxiv.org/abs/2410.24164
code_url: https://physicalintelligence.company/blog/pi0
citations: 2419
source: "raw/papers/black2024pi0.pdf"
added: "2026-08-20"
relevance: 4
credibility: 4
status: read
related_experiments: []
related_concepts: ["Flow Matching", "Vision-Language-Action Model", "Action Expert", "Action Chunking", "Cross-Embodiment Transfer", "Open X-Embodiment", "Foundation Models for Robotics", "Multimodal Action Distribution", "Action Tokenization", "Diffusion Policy"]
tags: [vla, flow-matching, paligemma, cross-embodiment, dexterous-manipulation, action-chunking, foundation-model, mixture-of-experts]
---

# π0: A Vision-Language-Action Flow Model for General Robot Control

## TL;DR

π0 grafts a **flow-matching action expert** (300M params, randomly initialized)
onto a frozen-architecture PaliGemma VLM backbone (3B), producing a 3.3B VLA
that emits 50-step action chunks at up to **50 Hz** — fast and expressive enough
for genuinely dexterous, minutes-long tasks like folding laundry from a dryer.
The recipe deliberately mirrors LLM practice: pre-train on 10,000 h across 7
robot configurations / 68 tasks (plus OXE), then post-train on curated
high-quality data. **Inference is 73 ms on an RTX 4090**, so *running* π0 is
within this project's hardware; *training* it is emphatically not — which makes
this a "study the architecture, borrow the ideas" paper rather than a
reproduction target.

## Claims

- **First flow-matching VLA producing high-frequency action chunks** for
  dexterous control. The contrast is with autoregressive-discretization VLAs
  (RT-2, OpenVLA), which the authors argue structurally cannot do action chunking
  or high-frequency control.
- **Largest robot-learning pre-training mixture to date** — ~10,000 h / 903M
  timesteps of in-house dexterous data plus OXE, Bridge v2, DROID.
- **The pre-train/post-train split is load-bearing, not cosmetic.** Their explicit
  argument: high-quality-only training yields a brittle policy that never learned
  to recover from mistakes (because mistakes don't appear in clean data);
  pre-train-only yields a policy that is broadly capable but not fluent. You need
  both.
- **Separate weights for robot tokens ("action expert") beat shared weights** —
  a two-element mixture-of-experts where element one handles image/text and
  element two handles state/action.
- **VLM initialization matters**: π0 beats π0-small (470M, no VLM init) across
  the board, most visibly on language following.

## Methods

- **Backbone.** PaliGemma (3B, open-source), chosen for the size/performance
  tradeoff needed for real-time control; the authors note the framework is
  backbone-agnostic. +300M action expert = 3.3B total.
- **Conditional flow matching.** Model `p(A_t | o_t)` where `A_t` is an
  **H = 50** action chunk and `o_t = [I¹…Iⁿ, ℓ, q]` (2–3 RGB images, language
  tokens, joint angles). Linear-Gaussian probability path
  `q(Aᵗ|A) = N(τA, (1−τ)I)`; train `v_θ(Aᵗ, o)` to match the vector field
  `u = A − ε`; sample τ from a **beta distribution biased toward noisy (low)
  timesteps**. The action expert uses **full bidirectional attention** across
  action tokens.
- **Inference.** Forward-Euler integrate τ = 0 → 1 in **10 steps**. Keys/values
  for the observation prefix are cached, so each of the 10 steps only recomputes
  the action suffix. This is the design detail that makes 3.3B params viable at
  50 Hz.
- **Cross-embodiment padding.** Config/action vectors are always 18-D (two 6-DoF
  arms + 2 grippers + mobile base + vertical torso); smaller robots are
  zero-padded and missing camera slots masked.
- **Data weighting.** Task-robot combinations weighted by `n^0.43` to down-weight
  over-represented combinations (laundry folding is over-collected).
- **Open-source share is only 9.1%** of the mixture. The other ~91% is
  proprietary Physical Intelligence data.
- **High-level policy.** For semantically complex tasks, a separate VLM decomposes
  "bus the table" into ~2-second subtask commands (SayCan-style) that π0 consumes
  as language.
- **Action execution is open-loop per chunk.** They tried
  [[Temporal Ensembling]] (from ACT) early and **found it hurt performance**, so
  chunks are executed without aggregation: re-infer every 0.8 s (16 actions) at
  20 Hz, every 0.5 s (25 actions) at 50 Hz.

## Results

- **Inference time on an RTX 4090** (Table I, 3 cameras): image encoders 14 ms +
  observation forward pass 32 ms + 10 flow steps 27 ms = **73 ms on-board**
  (86 ms off-board with Wi-Fi latency).
- **Out-of-box direct prompting** (Fig. 7, 10 episodes/task, fractional-progress
  scoring): π0 best on all five tasks — near-perfect on shirt folding and easy
  bussing. The **compute-parity π0 (160k steps, matched to the baselines) still
  beats every baseline**, and even π0-small beats OpenVLA and Octo.
- **OpenVLA (7B) does poorly** on this mixture, attributed to autoregressive
  discretization precluding action chunks. A UR5e-only OpenVLA does better but
  still trails. **Octo (93M)** supports chunking but is capacity-limited.
- **Fine-tuning** (Fig. 11, 20+ tasks, 10 trials each, 1/5/10 h of data): π0
  generally best. Pre-training helps most at small data — the 1-hour Tupperware
  policy is decisively better than baselines while the 5-hour one is comparable.
- **Notable inversion:** among prior methods, the strongest baselines are **ACT
  and Diffusion Policy trained entirely from scratch** on the target task,
  beating pre-trained OpenVLA/Octo checkpoints. The authors read this as evidence
  that *leveraging* pre-training is itself hard for prior approaches.
- **Task scale:** laundry folding runs tens of minutes end-to-end — fetch from
  dryer, pack hamper, transport, fold each article. Claimed as the longest
  dexterous tasks in the end-to-end robot learning literature.

## Critique / open questions

- **Not reproducible.** ~91% of the pre-training mixture is proprietary,
  collected by Physical Intelligence's own operators. Weights were released
  later, but the *paper's* central claim — that this pre-training recipe works —
  cannot be independently checked. This is the single largest caveat.
- **The baseline comparisons are structurally unfair and the authors say so.**
  OpenVLA and Octo were undertrained "due to time constraints"; the compute-parity
  π0 run is an honest partial mitigation. But OpenVLA is also being run on a
  mixture the authors themselves call "very difficult" for it (high-frequency,
  chunked) — i.e. the baseline is evaluated outside its design envelope.
- **π0 vs π0-small confounds two variables** — VLM initialization *and* model
  size (3.3B vs 470M). The authors flag this explicitly and note it is hard to
  remove, since large models are impractical to train without pre-training. So
  "VLM pre-training matters" is suggestive, not isolated.
- **No ablation of flow matching against diffusion** on the same backbone. The
  paper's architectural claim rests on comparisons to *different models* that
  also differ in size, data, and training budget.
- **10 trials per task** across all real-robot evaluations, with
  fractional-progress scoring rubrics defined per task in an appendix. Reasonable
  for real-robot work, but the error bars are wide and the metric is bespoke.
- **Authors' own stated limitations:** no understanding of how to *compose* the
  pre-training mixture (they used everything available); no way to predict how
  much data a task needs; unclear how much positive transfer diverse data
  actually provides; "not all tasks in our evaluation work reliably."
- **Temporal ensembling hurting** is an interesting negative result that
  contradicts ACT's finding — worth knowing, unexplained here.
- Single-institution paper with no external replication of the hardware results.

## Trust signals

- **Credibility:** 4 — published at **RSS 2025** (per the arXiv comment;
  Semantic Scholar's venue field is stale), 2419 citations (checked 2026-08-20).
  Author roster is exceptionally strong: Sergey Levine, Chelsea Finn, Karol
  Hausman, Brian Ichter, Danny Driess. Model weights and a detailed blog release
  followed, and `openpi` has since been widely used, which is meaningful
  post-hoc validation. Held to 4 rather than 5 because the **pre-training data is
  proprietary and the results are therefore not independently reproducible**, the
  baseline comparisons are acknowledged-unfair, and it is a single-company paper
  with commercial incentive. The limitations section is genuinely candid, which
  cuts the other way.

## Follow-up

- **Relevance:** 4 — defines the current flow-matching VLA paradigm and is
  essential framing for where [[Vision-Language-Action Model]] work is going.
  Not a 5 for this project because training is far outside a 16 GB budget and the
  data is closed, so it seeds understanding rather than experiments.
- **Inference-only work is feasible here**: 73 ms on a 4090 means a 16 GB card
  can plausibly *run* π0 (3.3B) with quantization. That directly addresses the
  project's open question about whether VLAs are inference-only on this hardware
  — for π0, yes; for [[ghosh2024octo|Octo]], no.
- The **flow-matching action expert is the portable idea**: 10 Euler steps with a
  cached observation prefix, versus [[Diffusion Policy]]'s 100 DDPM steps and
  [[prasad2024consistency|Consistency Policy]]'s distillation. All three are attacking the same
  inference-cost problem from different angles, and π0's answer (fewer steps by
  construction, via a straighter probability path) is the one that needs no
  distillation stage. Worth a small-scale head-to-head on a sim task.
- **`n^0.43` data weighting** is a concrete, cheap, transferable recipe detail for
  any imbalanced multi-task mixture.
- Contrast the **discretization-vs-continuous verdict** with [[ghosh2024octo|Octo]]'s: Octo's
  ablation found discretized actions catastrophic (18% vs 83%), and π0 argues
  autoregressive discretization structurally blocks action chunking. Two
  independent lines of evidence pointing the same way — worth recording in
  [[Action Tokenization]] as a substantive strike against token-based action heads
  for high-frequency control.
- The **pre-train-then-post-train argument** ("clean data alone never teaches
  recovery") is a claim about [[Compounding Error]] and is testable cheaply in
  sim: train on expert-only demos vs expert + noisy-recovery demos and measure
  recovery from induced perturbations.
