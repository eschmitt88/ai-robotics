---
kind: paper
title: "MuJoCo Playground"
authors: [Kevin Zakka, Baruch Tabanpour, Qiayuan Liao, Mustafa Haiderbhai, Samuel Holt, Jing Yuan Luo, Arthur Allshire, Erik Frey, Koushil Sreenath, Lueder A. Kahrs, Carmelo Sferrazza, Yuval Tassa, Pieter Abbeel]
institutions: ["UC Berkeley", "Google DeepMind", "University of Toronto", "University of Cambridge"]
year: 2025
venue: null
peer_reviewed: unknown
url: https://arxiv.org/abs/2502.08844
code_url: https://mujocoplayground.github.io
citations: null
source: "raw/papers/zakka2025mujoco.pdf"
added: "2026-08-20"
relevance: 5
credibility: 5
status: read
related_experiments: []
related_concepts: ["GPU-Parallelized Simulation", "Parallel Rendering", "Sim-to-Real Transfer", "Domain Randomization", "Proximal Policy Optimization", "Soft Actor-Critic", "Reinforcement Learning", "Manipulation Benchmark", "Continuous Control"]
tags: [simulation, mjx, jax, gpu-parallel, sim2real, locomotion, manipulation, madrona, vision-based-rl, single-gpu, open-source]
---

# MuJoCo Playground

## TL;DR

A `pip install playground` framework wrapping **MJX** (the JAX/GPU branch of
MuJoCo) plus the **Madrona** batch renderer, with environments for quadrupeds,
humanoids, dexterous hands, and arms. Most tasks train in **minutes on one
GPU**, and the authors deployed state- *and* pixel-based policies zero-shot to
six real robot platforms in under eight weeks. The headline technical claim for
this project: Madrona lets you train vision policies **end-to-end on device
without the usual state-policy → vision-policy distillation step**. Fully
open-source top to bottom, unlike the IsaacGym/Isaac Lab stack it competes with.

## Claims

- **Comprehensive MJX environment suite** with demonstrated sim-to-real across
  quadrupeds (Unitree Go1, Spot, Barkour), humanoids (Berkeley Humanoid, Unitree
  H1/G1, Booster T1, Robotis OP3), a dexterous hand (LEAP), and arms
  (Franka Panda, Aloha bi-arm).
- **End-to-end vision-based RL on a single GPU** via Madrona batch rendering,
  removing the teacher-student distillation stage that the field had adopted
  because rendering was too slow to keep in the training loop.
- **Fully open-source stack.** The explicit contrast is PhysX/IsaacGym/Isaac Lab,
  which are closed-source and cannot be extended by researchers. Prior
  open-source GPU engines (MJX, Brax, Warp, Taichi, Genesis) existed but had
  few environments and thin sim-to-real evidence.
- **Reproducible pipeline** — notebooks, hyperparameters, and training curves
  released; the whole loop runs in one Colab notebook.

## Methods

- **Physics:** MJX, MuJoCo XLA, JAX-based, GPU-resident. Whole agent-environment
  loop stays on device.
- **Rendering:** Madrona, a GPU entity-component-system. Two backends — a CUDA
  software batch **ray tracer** (used for all results here, supporting complex
  lighting, shadows, textures, materials) and a Vulkan rasterizer. Integrated
  into MJX via low-level JAX primitives so it composes with `jit` and `vmap`.
- **Per-instance visual randomization.** Each environment instance can have
  different geometry size, color, lighting, and camera pose — i.e.
  [[Domain Randomization]] applied *inside* the batched renderer, which the
  authors identify as crucial to vision sim-to-real.
- **Environment families:** (a) DM Control Suite reimplemented in MJX as
  entry-level tasks; (b) Locomotion — joystick velocity-command tracking per
  embodiment, plus Go1 fall-recovery and handstand; (c) Manipulation — LEAP
  in-hand cube reorientation, Franka non-prehensile block reorientation via
  **direct 200 Hz torque control**, vision-based pick-cube, Aloha bi-arm peg
  insertion.
- **Training:** Brax PPO and SAC for all headline results; RSL-RL (PyTorch) also
  supported and benchmarked. Robots use `MuJoCo Menagerie` assets.
- **Sim-to-real recipe (consistent across tasks):** domain randomize sensor
  noise, dynamics, and task parameters; train on the easy setting first
  (flat ground, restricted command range) then finetune wider; inject stochastic
  action/observation delays to mimic hardware latency; progressive curriculum
  that widens the goal distribution on each success.

## Results

- **Training times (all single- or dual-GPU):** most DM Control Suite tasks
  <10 min on one GPU. Go1 flat-ground joystick **5 min** (2× 4090). Berkeley
  Humanoid <15 min; Unitree G1 and Booster T1 <30 min (2× 4090). LEAP in-hand
  reorientation ~30 min (2× 4090). Vision-based Franka pick-cube **10 min on a
  single RTX 4090**.
- **Throughput with rendering (Fig. 7):** CartpoleBalance ~403,000 steps/s;
  PandaPickCubeCartesian ~37,000 steps/s. Franka physics is >20× costlier than
  Cartpole's, so its FPS is far less sensitive to image resolution (64→512).
- **The bottleneck has moved.** In a PPO loop, physics + rendering + inference
  are only **9% (Cartpole) and 43% (Franka)** of total training time — the rest
  is CNN gradient updates. Explicitly: "we have shifted our bottleneck from
  collecting data to processing it."
- **GPU scaling (Fig. 6, LeapCubeReorient, fixed 8192 envs):** 1× 4090 ≈ 2080 s
  → 8× H100 ≈ 670 s. Only ~3× from a 4090 to eight H100s, and the authors note
  they did not retune env count per topology.
- **Sim-to-real:** Go1 joystick/handstand/footstand/fall-recovery all transfer,
  including recovery from a kick at ~2 m/s on grass and concrete. Humanoid
  joystick transfers zero-shot on three platforms. Franka non-prehensile
  reorientation: **85.7% ± 12.2 real success** over 35 trials (median 100%),
  1.95 cm median position error, 1.72° median rotation error. Vision pick-cube:
  **12/12 real trials** from 64×64 RGB, robust to lighting variation and camera
  shake. LEAP in-hand: median 3.5 / mean 7.1 consecutive rotations over 10
  trials — the weakest result, and reported as such.
- **RL library parity (Fig. 8):** Brax and RSL-RL PPO reach comparable reward at
  comparable wall-clock on a 4090, 3 seeds each.

## Critique / open questions

- **Peer-review status unknown.** arXiv preprint (Feb 2025) with no venue given
  in the paper or arXiv comments. Credibility rests on the author roster
  (Tassa is a MuJoCo author; Abbeel, Sreenath) and the open artifact, not review.
- **JAX static-shape penalty is a real limitation the authors own:** contact
  compute scales with the *number of possible contacts*, not active ones, so
  cluttered scenes pay for contacts that never happen. JIT compile is 1–3 min per
  task. Both argue against MJX for heavy-clutter manipulation, where
  [[tao2024maniskill3|ManiSkill3]] (PhysX) may be the better fit.
- **"Vision-based training with Madrona is still at an early stage"** — their
  words. Only *two* vision environments ship (CartpoleBalance,
  PandaPickCubeCartesian), and the pick-cube task is heavily simplified: the
  end-effector is restricted to a 2D Y-Z plane, the action space is 3D, and the
  gripper is binary. That is a long way from general vision-based manipulation.
- **Compute reporting is inconsistent across tasks** — 2× 4090 here, 16× A100
  there (Franka non-prehensile), single 4090 elsewhere. The "minutes on a single
  GPU" headline is true for many tasks but not uniformly, and the paper does not
  give single-GPU equivalents for the multi-GPU results.
- **No head-to-head benchmark against Isaac Lab or ManiSkill3** in the main
  text (only "a rough comparison" deferred to an appendix section). Given the
  paper's positioning against closed-source PhysX, the absence of a controlled
  FPS/memory comparison is a gap — [[tao2024maniskill3|ManiSkill3]] does provide such numbers.
- **Real-robot trial counts are small** (10–35), and the in-hand result has high
  variance (median 3.5 vs mean 7.1 rotations — a single 27-rotation trial is
  carrying the mean).
- Locomotion sim-to-real is evaluated **indoors on standard floors** for the
  humanoids, with only "slight variations in surface friction."

## Trust signals

- **Credibility:** 5 — despite unknown venue. Yuval Tassa (a MuJoCo core author)
  and Baruch Tabanpour at Google DeepMind, with Pieter Abbeel and Koushil
  Sreenath at Berkeley; direct continuation of the MuJoCo / MJX / Menagerie
  lineage. Fully open-source: physics, renderer, environments, training code,
  notebooks, hyperparameters, and training curves. Six real robot platforms
  across independent labs is strong external validation. The limitations section
  is unusually candid — JIT cost, contact-scaling penalty, and "vision-based
  training is still at an early stage" are all stated plainly. Competing
  interest (Sreenath's financial interest in Boston Dynamics AI Institute) is
  disclosed.

## Follow-up

- **Relevance:** 5 — the other half of this project's GPU-simulation question,
  alongside [[tao2024maniskill3|ManiSkill3]]. MJX/JAX vs SAPIEN/PhysX is a genuine fork in the
  tooling road and the answer determines what a single 16 GB card can attempt.
- Candidate experiment: **head-to-head MuJoCo Playground vs ManiSkill3 on 16 GB**
  — matched task (cube pick), matched algorithm (PPO), measuring achievable env
  count, FPS, peak VRAM, and time-to-solve. The two papers report throughput
  under different protocols and on different hardware, so neither answers the
  question for this box. This is the single most decision-relevant experiment
  available from either paper.
- The **9% / 43% bottleneck breakdown is the most actionable finding here**: on
  a 16 GB card, more parallel envs will *not* help once gradient updates
  dominate. Verify where the crossover sits locally before scaling env count.
- MJX's contact-scaling penalty predicts that Playground wins on locomotion and
  loses on cluttered manipulation relative to ManiSkill3. Directly testable and
  worth knowing before committing tooling.
- The **200 Hz direct-torque-control recipe** for the Franka block reorientation
  is called out by the authors as holding "broad value for practitioners" —
  torque control yields compliant behavior that transfers better than position
  control, at the cost of a harder learning problem. Relevant to
  [[Position vs Velocity Control]].
- Provides a clean DM Control Suite reimplementation, which makes it a natural
  host for extending `2026-07-01-sac-vs-ppo-halfcheetah`: the same HalfCheetah
  comparison at 4-5 orders of magnitude more throughput would turn a
  sample-efficiency study into a wall-clock study.
