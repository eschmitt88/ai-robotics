---
kind: paper
title: "Soft Actor-Critic: Off-Policy Maximum Entropy Deep Reinforcement Learning with a Stochastic Actor"
authors: [Tuomas Haarnoja, Aurick Zhou, Pieter Abbeel, Sergey Levine]
institutions: ["Berkeley Artificial Intelligence Research, University of California, Berkeley"]
year: 2018
venue: ICML 2018
peer_reviewed: true
url: https://arxiv.org/abs/1801.01290
code_url: https://github.com/haarnoja/sac
citations: null
source: "raw/papers/haarnoja2018soft.pdf"
added: "2026-07-01"
relevance: 5
credibility: 5
status: read
related_experiments: []
related_concepts: ["Soft Actor-Critic", "Maximum Entropy RL", "Off-Policy RL", "Continuous Control", "Actor-Critic Methods", "Reparameterization Trick"]
tags: [reinforcement-learning, off-policy, maximum-entropy, continuous-control, actor-critic, sample-efficiency, sac, deep-rl]
---

# Soft Actor-Critic: Off-Policy Maximum Entropy Deep Reinforcement Learning with a Stochastic Actor

## TL;DR

SAC is an off-policy actor-critic algorithm that maximizes expected reward *plus* policy entropy, giving sample-efficient, remarkably stable continuous control. It combines a replay buffer (off-policy reuse), a stochastic Gaussian actor trained via the [[Reparameterization Trick]], and clipped double Q-functions to reach state-of-the-art on MuJoCo benchmarks while being far less brittle across random seeds than DDPG or PPO.

## Claims

- A single algorithm can be simultaneously **sample-efficient** (off-policy, reuses replay data) and **stable** (low variance across seeds) — a combination prior methods lacked (on-policy PPO is stable but sample-hungry; off-policy DDPG is efficient but brittle).
- Adding an **entropy-maximization term** to the RL objective improves exploration and robustness, and is the key to the observed stability.
- SAC is derived from a **soft policy iteration** scheme with a convergence proof (to the optimal policy within a parameterized policy class) in the tabular setting.
- SAC **exceeds** the final performance of off-policy DDPG (not just matches it) and outperforms on-policy PPO and prior max-entropy methods (Soft Q-Learning), especially on hard high-dimensional tasks like the 21-DoF Humanoid.
- Stochasticity from the entropy objective is what stabilizes training: a deterministic ablation of SAC shows much higher seed-to-seed variance.

## Methods

- **Maximum-entropy objective** (Eq. 1): J(π) = Σₜ E[r(sₜ,aₜ) + α·H(π(·|sₜ))]. The temperature α trades off reward vs. entropy; standard RL is recovered as α→0. In this paper α is folded into the reward via scaling (reward scale is the one key hyperparameter).
- **Soft policy iteration** (Sec 4.1): alternates soft policy evaluation (a modified Bellman backup that adds an entropy/soft-value term, Eqs. 2-3) and soft policy improvement (project the policy onto the exponential of the soft Q via a KL projection, Eq. 4). Lemmas 1-2 and Theorem 1 prove convergence in the tabular case.
- **Practical algorithm** (Sec 4.2, Algorithm 1): three function approximators — a soft state-value network V_ψ (with a target network V_ψ̄ updated by exponential moving average, τ=0.005), a soft Q-function Q_θ, and a tractable Gaussian policy π_φ. Losses: squared soft-value residual (Eq. 5), soft Bellman residual for Q (Eq. 7), and expected KL for the policy (Eq. 10).
- **Reparameterized policy** (Eq. 11-13): aₜ = f_φ(εₜ; sₜ) with Gaussian noise ε, giving a low-variance policy gradient that extends the DDPG-style gradient to arbitrary stochastic policies.
- **Two Q-functions** (clipped double-Q, following TD3/Fujimoto 2018): the min of two independently trained critics is used to mitigate positive bias in the value update — found to significantly speed up training on harder tasks.
- Alternates one environment step with one (or several) gradient steps on batches from the replay buffer D.

## Results

- Evaluated on OpenAI Gym MuJoCo tasks — Hopper-v1, Walker2d-v1, HalfCheetah-v1, Ant-v1, Humanoid-v1 — plus the rllab Humanoid (harder). Baselines: DDPG, PPO, Soft Q-Learning (SQL), and concurrent TD3. Five seeds each, eval rollout every 1000 steps (Fig. 1).
- SAC is **comparable on easy tasks and dominant on hard tasks**: on HalfCheetah, Ant, Humanoid-v1, and rllab Humanoid it learns faster and reaches higher final return. DDPG fails to make progress on Ant/Humanoid; PPO is much slower on high-dimensional tasks.
- **Stability**: on rllab Humanoid, the stochastic SAC has tight seed-to-seed spread while a deterministic variant shows very high variance (Fig. 2) — evidence that entropy maximization stabilizes learning.
- **Ablations** (Fig. 3): (a) using the deterministic mean action for evaluation generally beats sampling; (b) performance is sensitive to **reward scale** (too small → near-uniform policy, fails to exploit reward; too large → premature determinism, poor local optima); (c) target smoothing τ has a wide usable range, 0.005 used across all tasks.

## Critique / open questions

- **Reward scale must be tuned per task** — the "one hyperparameter" claim is honest but this is still a per-environment knob acting as an implicit inverse temperature. (Note: the follow-up SAC paper introduces automatic temperature tuning, resolving this.)
- All results are in **simulation (MuJoCo/rllab)**; no real-robot demonstration in this paper. Sample counts (millions of steps, up to 10M for Humanoid) are cheap in sim but relevant to gauge for real hardware.
- Uses a **separate value network V_ψ** in addition to two Q-functions; later SAC formulations drop V and derive it from Q + policy, simplifying the algorithm. Worth noting the version implemented matters.
- Convergence guarantees are **tabular only**; the deep function-approximation version is heuristic (as with all deep RL).
- Sample efficiency is state-of-the-art *for its time* but still large in absolute terms for a single-GPU learner running many seeds.

## Trust signals

- **Credibility:** 5 — BAIR / UC Berkeley (Haarnoja, Zhou, Abbeel, Levine), published at ICML 2018, code released at github.com/haarnoja/sac, foundational and very heavily cited. Claims backed by multi-seed benchmarks and a convergence proof. SAC is now a standard baseline across the field.

## Follow-up

- Implement SAC on a single MuJoCo/Gymnasium task (HalfCheetah or Hopper) as a from-scratch [[Continuous Control]] baseline — good fit for a 16GB GPU; the bottleneck is env steps, not model size.
- Compare the V-network formulation here vs. the later V-free SAC (Haarnoja et al. 2018b, "Algorithms and Applications") with automatic temperature tuning.
- Contrast the clipped double-Q trick against its origin in TD3 (Fujimoto 2018).
- Relate to [[Maximum Entropy RL]] lineage: Soft Q-Learning (Haarnoja 2017), max-ent inverse RL (Ziebart 2008).
- Candidate as the default off-policy RL algorithm for any sim-first embodied-AI experiment in this project.
