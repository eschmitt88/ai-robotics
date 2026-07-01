---
kind: paper
title: "ManiSkill3: GPU Parallelized Robotics Simulation and Rendering for Generalizable Embodied AI"
authors: [Stone Tao, Fanbo Xiang, Arth Shukla, Yuzhe Qin, Xander Hinrichsen, Xiaodi Yuan, Chen Bao, Xinsong Lin, Yulin Liu, Tse-kai Chan, Yuan Gao, Xuanlin Li, Tongzhou Mu, Nan Xiao, Arnav Gurha, Viswesh Nagaswamy Rajesh, Yong Woo Choi, Yen-Ru Chen, Zhiao Huang, Roberto Calandra, Rui Chen, Shan Luo, Hao Su]
institutions: ["UC San Diego", "Carnegie Mellon University", "Hillbot", "TU Dresden", "Tsinghua University", "King's College London"]
year: 2024
venue: null
peer_reviewed: unknown
url: https://arxiv.org/abs/2410.00425
code_url: https://github.com/haosulab/ManiSkill
citations: null
source: "raw/papers/tao2024maniskill3.pdf"
added: "2026-07-01"
relevance: 5
credibility: 5
status: read
related_concepts: ["GPU-Parallelized Simulation", "Parallel Rendering", "Heterogeneous Simulation", "Manipulation Benchmark", "Sim-to-Real Transfer", "Reinforcement Learning", "Imitation Learning", "Domain Randomization", "Digital Twins"]
tags: [simulation, gpu-parallel, manipulation, embodied-ai, sim2real, rl, imitation-learning, sapien, benchmark, single-gpu]
---

# ManiSkill3: GPU Parallelized Robotics Simulation and Rendering for Generalizable Embodied AI

## TL;DR

ManiSkill3 is an open-source (Apache-2.0), SAPIEN/PhysX-based GPU-parallelized
robotics simulator that runs contact-rich manipulation with simultaneous
GPU rendering at up to **30,000+ FPS** while using **2-3x less GPU memory**
than alternatives like Isaac Lab. It ships 12 task domains and 20+ robot
embodiments, uniquely supports *heterogeneous* parallel simulation (different
scenes/articulations per environment), and turns visual-RL runs that took
hours into runs that take minutes — making serious embodied-AI research
tractable on a single modest GPU.

## Claims

- First general benchmark to enable **visual RL on complex manipulation** via
  fast, low-overhead GPU parallel rendering; PPO can now solve visual tasks in
  minutes rather than hours.
- **2-3x lower GPU memory** than other simulators: for Cartpole with 128
  parallel envs, ManiSkill3 uses 3.5 GB vs Isaac Lab's 14.1 GB — leaving
  headroom for replay buffers and large VLA models on-device.
- Only framework supporting **fully heterogeneous GPU simulation**: different
  object geometries, object counts, and articulation DoFs across parallel
  environments simultaneously (e.g., a different cabinet in every env).
- Most comprehensive task coverage: **12 distinct domains** (tabletop, mobile
  manipulation, room-scale, quadruped/humanoid locomotion, bi-manual, dexterous,
  drawing/cleaning, vision-tactile, classic control, digital twins, soft-body,
  multi-agent) and **20+ robot embodiments**.
- Clean **object-oriented, unified API** (batched `Pose` objects, no complex
  tensor indexing) that dramatically simplifies task-building vs IsaacGymEnvs /
  Isaac Lab (see code comparison, Fig. 9-10).
- Reproducible **sim2real** with a $300 Koch arm: RL-trained vision policy
  zero-shots to 91.6% real-world success on cube picking.
- Scalable demonstration-generation pipeline from a few demos (motion planning,
  RL, teleop) via online-from-demos algorithms RFCL / RLPD.

## Methods

- **Backend:** built on the open-source SAPIEN engine using NVIDIA **PhysX** for
  GPU rigid-body sim; parallel **rasterization** rendering (RGB, depth, segmentation,
  pointcloud/voxel) with an optional non-parallel ray-tracing mode. Isaac Lab by
  contrast uses ray-tracing on closed-source Isaac Sim.
- **Data-oriented / object-oriented design** manages GPU memory for objects and
  articulations even when they differ across envs, enabling heterogeneous sim.
- **Robots/controllers:** native URDF + Mujoco MJCF import; prebuilt joint-position
  and inverse-kinematics controllers parallelized on GPU (built on PyTorch Kinematics).
- **Trajectory replay/conversion tool** (from ManiSkill2): change observation mode,
  shaders, rewards, or controller action space; replay CPU/GPU demos with explicit
  per-env RNG seeding so data collected on a big-memory machine replays on a small one.
- **VR teleoperation** (Meta Quest 3 via ALVR, 4K stereo @ 60 Hz) integrated for
  demo collection.
- **Baselines provided:** RL — PPO, SAC (CleanRL/LeanRL-based, torch.compile +
  cudagraphs), TD-MPC2. Offline IL — Behavior Cloning, [[Diffusion Policy]], ACT,
  PerACT. Online IL — RLPD, RFCL. VLA evaluation (not training) — Octo, RT-X, RDT-1B.

## Results

- **Throughput/memory:** up to 30,000+ FPS sim+render; Cartpole @128 envs 3.5 GB
  (ManiSkill3) vs 14.1 GB (Isaac Lab), and Isaac Lab OOMs beyond 128 envs at
  640x480 while ManiSkill3 scales to 1024.
- **RL training speed (RTX 4090, PickCube, Franka):** state-based reaches ~100%
  success in ~1 min (**15x** faster than ManiSkill2 CPU sim); RGB/vision-based
  solved in ~10 min (**8x** faster than ManiSkill2). Fig. 11 shows GPU sim
  massively accelerates both state and vision training vs CPU sim.
- **Sim2real:** vision-based PPO policy (256 envs, 15M samples, ~1 hr on RTX 4090)
  zero-shots to a Koch arm at **91.6%** real cube-pick success (avg over 3 runs;
  22/24 on the grasp subtask); good sim/real success-rate correlation (Fig. 13).
  Vision-tactile peg insertion achieved 95.08% real success (cited prior work).
- **Digital twins:** 4 SIMPLER environments reproduced; evaluate Octo/RT-1X at
  60-100x real-world speed (~10x faster than original SIMPLER), correlation 0.9284
  with real success, MMRV 0.0147.

## Critique / open questions

- The v2 PDF is titled "Demonstrating GPU Parallelized Robot Simulation..." —
  positioned as a demonstration/systems paper; **venue/peer-review status is
  unclear** (arXiv preprint, last revised May 2025). Credibility rests on the
  established ManiSkill lineage and heavy community use, not formal review.
- Benchmark comparison to Isaac Lab is **not strictly apples-to-apples**:
  rasterization (ManiSkill3) vs ray-tracing (Isaac Lab), and Isaac Lab is run at
  its fastest (lowest-quality) render setting. FPS/memory advantages may not hold
  for every environment or at matched visual fidelity.
- Soft-body and vision-tactile environments are **not batched/GPU-parallelized**
  the way rigid-body ones are — the headline throughput applies to rigid-body tasks.
- Sim2real still needed reward engineering, green-screening of the background, and
  static cameras; authors call zero-shot RGB sim2real "far from solved."
- Room-scale / many-geometry scenes force a lower env count per GPU (limitation §V).
- VLA models can be evaluated but **not yet trained** in the provided baselines.

## Trust signals

- **Credibility:** 5 — Hao Su lab (UC San Diego) with strong co-author roster
  across CMU, Tsinghua, TU Dresden, KCL; third major release of a widely-adopted
  benchmark; fully open-source under Apache-2.0 with extensive docs, tutorials,
  and reproducible RL/IL baselines; backed by Qualcomm Embodied AI Fund / Hillbot.
  Only ding is unclear formal peer-review venue.

## Follow-up

- Directly relevant to this project's single-16GB-GPU constraint: the 2-3x memory
  advantage is the key enabler — [[GPU-Parallelized Simulation]] with visual RL
  is plausible at meaningful env counts on 16 GB where Isaac Lab would OOM.
- Candidate experiment: reproduce PickCube state-based PPO, then vision-based PPO
  on the 16 GB GPU; measure achievable env count and FPS vs the paper's RTX 4090.
- Explore [[Sim-to-Real Transfer]] via [[Domain Randomization]] on a tabletop task
  even without real hardware (evaluate transfer robustness in-sim).
- Investigate [[Heterogeneous Simulation]] (PartNet cabinets / YCB clutter) for
  training generalizable [[Reinforcement Learning]] policies across object sets.
- Try the RFCL / RLPD online [[Imitation Learning]] pipeline to bootstrap from a
  handful of motion-planned demos on a hard-reward task.
