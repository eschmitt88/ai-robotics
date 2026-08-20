---
kind: paper
title: "Consistency Policy: Accelerated Visuomotor Policies via Consistency Distillation"
authors: [Aaditya Prasad, Kevin Lin, Jimmy Wu, Linqi Zhou, Jeannette Bohg]
institutions: ["Stanford University", "Princeton University"]
year: 2024
venue: "Robotics: Science and Systems (RSS) 2024"
peer_reviewed: true
url: https://arxiv.org/abs/2405.07503
code_url: https://consistency-policy.github.io
citations: 202
source: "raw/papers/prasad2024consistency.pdf"
added: "2026-08-20"
relevance: 4
credibility: 4
status: read
related_experiments: []
related_concepts: ["Consistency Distillation", "Diffusion Policy", "DDIM", "Probability Flow ODE", "Score Function", "Multimodal Action Distribution", "Action Chunking", "Imitation Learning", "FiLM Conditioning"]
tags: [diffusion, distillation, inference-speed, imitation-learning, visuomotor, edge-compute, low-vram, robomimic]
---

# Consistency Policy: Accelerated Visuomotor Policies via Consistency Distillation

## TL;DR

Distills a pretrained [[Diffusion Policy]] teacher into a student that generates
an action chunk in **one** forward pass (or three for accuracy), by enforcing
self-consistency along the teacher's Probability-Flow ODE trajectories. Result:
**~1–2 ms inference vs 110 ms (DDPM) / 11 ms (DDiM)** at competitive success
rates, on a laptop 3070 Ti with 8 GB VRAM. The motivating constraint —
"many robots can't carry a high-end GPU" — is a near-exact match for this
project's own 16 GB ceiling, and the paper's real value here is that its
**ablations isolate which design choices actually carry the speedup**.

## Claims

- **~1 order of magnitude faster than the fastest baseline** while maintaining
  competitive success rates across 6 sim tasks and 3 real tasks.
- **Robust to teacher quality** — distilling from teachers at .92 / .88 / .84
  success yields students at .92 / .92 / .88. Practically important: you don't
  need to extensively validate the teacher before distilling.
- **CTM-local objective (adjacent t,u; arbitrary s) beats both alternatives**
  and trains >40% faster than full CTM.
- **Low initial sample variance helps** — sampling `z ~ N(0, 1/T²)` instead of
  the standard `N(0,I)`/`N(0,T²I)` keeps the trajectory in-distribution.
- **Dropout is load-bearing, not regularization.** Without it the consistency
  loss essentially vanishes and provides no training signal.

## Methods

- **Two-stage.** (1) Train a teacher under the **EDM** framework (not DDPM) —
  EDM integrates a deterministic ODE and is the standard substrate for
  consistency distillation. Loss is Denoising Score Matching with a
  **pseudo-Huber** metric `d(x,y) = √(‖x−y‖² + c²) − c`, `c = 0.00054·√D`,
  which handles outliers better than EDM's original L2. Heun's 2nd-order solver.
  (2) Distill into a student `g_θ(x_t, t, s; o)` that maps a point at time `t`
  to an estimate at any earlier time `s`.
- **CTM objective.** Sample two points `x_t`, `x_u` on the *same* PFODE (with
  `x_u` obtained by running the teacher `t−u` steps). Denoise both to `s`, then
  bring both to time 0 before computing the loss, so the metric is always
  evaluated in fully-denoised action space. Only the `t → s` generation is
  outside `stopgrad`. Final loss `L_CP = α·L_CTM + β·L_DSM`; the DSM term is
  teacher-independent, which is what makes the method robust to teacher quality.
- **Architecture.** Deliberately unchanged from [[Diffusion Policy]] — 1D
  convolutional UNet, [[FiLM Conditioning]] on observation and diffusion
  timestep, action-sequence prediction. Student adds *expanded* FiLM blocks to
  condition on the stop timestep `s`, **zero-initialized** so they don't disrupt
  the warm-started teacher weights. The authors chose UNet over the Diffusion
  Transformer only because DP reported the transformer needed more tuning.
- **3-step inference.** Denoise T→0, re-noise to `t₁`, denoise to 0, repeat for
  `t₂`. Chaining timesteps are preset by **subdividing discretized time** (at
  `t_{2N/3}`, `t_{N/3}`), not continuous time — the discretization is warped to
  concentrate steps early, so this targets the early-middle timesteps that prior
  image-diffusion work found carry the important features. This removes the
  per-task tuning that Consistency Models originally required.
- **Baselines are handicapped in the baselines' favor:** DDPM/DDiM NFE counts
  are divided by ParaDiGMS's published 3.7×/1.6× speedups (100→27, 15→9),
  *assuming* no accuracy loss from parallel sampling. ParaDiGMS itself could not
  be run on the 8 GB laptop GPU due to its VRAM appetite.

## Results

- **Robomimic + Push-T (Table I, 200 rollouts, NFE in parens):** on Square,
  DDPM(27) .93, DDiM(9) .85, CP-1step .92, CP-3step **.96**. On Tool Hang (the
  hardest), DDPM .79, DDiM **.14**, CP-1step .70, CP-3step .77. Push-T: DDPM
  .87, DDiM .78, CP-1 .82, CP-3 .84.
- **DDiM collapses on Tool Hang (.14)** — this is the clearest result in the
  paper. Naively cutting denoising steps destroys hard-task performance;
  distillation does not.
- **Wall clock (Robomimic Square, P5000):** DDPM 110 ms, DDiM 11 ms, CP-1step
  **1 ms**, CP-3step 2 ms.
- **Franka Kitchen (state, long-horizon):** CP matches at p1/p2 but trails
  DDPM at p3/p4 (.95/.93 vs 1.00/.98). Degradation compounds over stages.
- **Real world (3070 Ti, 8 GB laptop):** Trash Clean Up — DDiM .8 @ 192 ms vs
  CP .8 @ **21 ms**. Plug Insertion — DDiM .6 @ 198 ms vs CP .7 @ **22 ms**.
  Microwave (mobile manipulator) — DDiM .5 vs CP .4. Speedup is ~9×, not 15×,
  because the observation encoder is fixed overhead outside the denoiser.
- **Ablations:** consistency objective — CTM-local .92 > CTM .91 > Consistency
  Distillation .88 (and CTM trains 40% slower). Initial variance — 1/T² beats 1
  at both 1-step (.92 vs .90) and 3-step (.96 vs .91). Chaining — discretized
  subdivision beats continuous, decisively on Tool Hang (.77 vs .72). Dropout —
  disabling it on the `s→0` steps drops Square .92 → .86. Consistency *training*
  (teacher-free, the concurrent RL approach) collapses on images: .55 vs .92 on
  Square.

## Critique / open questions

- **Multimodality is lost, and the authors say so.** Distillation targets a
  deterministic ODE, so both the EDM teacher and CP shed the
  [[Multimodal Action Distribution]] that DDPM's SDE integration provides —
  observably favoring one side of Push-T. Since multimodality is *the* headline
  argument for [[Diffusion Policy]] over MSE regression, this is a real
  concession, and the "it doesn't hurt on standard benchmarks" defense is an
  argument that the benchmarks don't test for it.
- **Training gets more expensive, not less.** CP needs more epochs *and* more
  time per epoch (teacher forward pass + multiple student passes), on top of
  training the teacher first. The win is purely at inference. Worse on harder
  tasks — Microwave was never trained to convergence "due to external time
  constraints," which is also the one task where CP loses.
- **Slightly less stable training** than DP, attributed to the self-referential
  consistency objective.
- The **dropout finding is empirical and unexplained.** The authors offer a
  vanishing-loss hypothesis with supporting measurement (the `s→0` distance is
  two orders of magnitude smaller than the `x_s` distance without dropout), but
  flag deeper explanation as future work. Anyone reimplementing should treat
  dropout=0.2 on those steps as a load-bearing magic number.
- **Long-horizon degradation is unexplained** — CP trails at Kitchen p3/p4 and
  the authors explicitly say "more exploration is required to understand what
  Consistency Policy struggles to learn."
- Real-world sample sizes are small (10–20 trials), so the real-world success
  deltas (.6 vs .7, .5 vs .4) are within noise; the **latency** numbers are the
  trustworthy part of those tables.
- Baseline NFEs are adjusted by ParaDiGMS speedups *that were not reproduced
  here*. Honest and conservative in direction, but it means Table I's NFE column
  is partly hypothetical.

## Trust signals

- **Credibility:** 4 — RSS 2024, peer-reviewed; Jeannette Bohg's group at
  Stanford with Princeton; 202 citations (Semantic Scholar, checked 2026-08-20);
  code and project page released; funded by Toyota Research Institute. The
  ablation discipline is well above average — 6 separate ablations, each
  isolating one design decision, with the baselines adjusted *in the baselines'
  favor*. Held back from 5 by small real-world trial counts (10–20), one
  under-trained real task, and the unresolved dropout mechanism.

## Follow-up

- **Relevance:** 4 — squarely on this project's constraint axis. It is the
  cheapest paper here to reproduce end-to-end: Robomimic/Push-T are small,
  200-demo, single-GPU benchmarks, and the teacher is a [[Diffusion Policy]]
  already covered in this graph.
- Candidate experiment: reproduce **DP(DDPM) → EDM teacher → CP** on Push-T or
  Robomimic Square within 16 GB, measuring the full cost ledger — teacher
  training + distillation time vs inference latency saved. The paper reports the
  inference win but never totals the training cost, and on one GPU that total is
  the number that decides whether distillation is worth it.
- Candidate experiment: **test the lost-multimodality claim directly.** Push-T
  has a known bimodal solution (go around the T either way). Measure mode
  coverage across seeds for DP vs EDM teacher vs CP. The authors observed the
  asymmetry but did not quantify it, and it is a cheap, well-posed gap.
- The **teacher-robustness result** (.84 teacher → .88 student) is the most
  practically useful claim and the easiest to verify: distill from deliberately
  under-trained teacher checkpoints and plot student vs teacher success.
- Relates to [[ghosh2024octo|Octo]]'s [[Diffusion Action Head]]: Octo already runs denoising
  in a small head after a single backbone pass, so it has architecturally
  captured much of the same saving. The authors explicitly note Octo could also
  be distilled — an open direction that would compose the two.
- The [[DDIM]] Tool Hang collapse (.14) is direct evidence for this project's
  open question about DDIM step-count sensitivity: **step reduction and
  distillation are not interchangeable**, and the gap widens with task difficulty.
