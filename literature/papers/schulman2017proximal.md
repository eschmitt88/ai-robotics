---
kind: paper
title: "Proximal Policy Optimization Algorithms"
authors: [John Schulman, Filip Wolski, Prafulla Dhariwal, Alec Radford, Oleg Klimov]
institutions: [OpenAI]
year: 2017
venue: null
peer_reviewed: false
url: https://arxiv.org/abs/1707.06347
code_url: https://github.com/openai/baselines
citations: null
source: "raw/papers/schulman2017proximal.pdf"
added: "2026-07-01"
relevance: 5
credibility: 5
status: read
related_experiments: []
related_concepts: [Proximal Policy Optimization, Policy Gradient Methods, On-Policy RL, Continuous Control, Actor-Critic Methods, Trust Region Policy Optimization, Generalized Advantage Estimation, Clipped Surrogate Objective]
tags: [reinforcement-learning, policy-gradient, ppo, on-policy, actor-critic, continuous-control, mujoco, atari, deep-rl]
---

# Proximal Policy Optimization Algorithms

## TL;DR

PPO is a first-order policy-gradient method that gets much of the reliability and sample efficiency of [[Trust Region Policy Optimization]] but is far simpler to implement. Its key trick is a *clipped surrogate objective* that lets you run multiple epochs of minibatch SGD on the same batch of on-policy data without the policy taking destructively large steps. It is now the default workhorse for [[Continuous Control]] and a natural fit for single-GPU learning-based robotics.

## Claims

- A **clipped probability-ratio objective** ($L^{CLIP}$) forms a pessimistic (lower-bound) estimate of policy performance and empirically outperforms both an unclipped surrogate and a KL-penalty variant.
- PPO attains **TRPO-like data efficiency and reliability using only first-order optimization** — no conjugate gradient, no Hessian-vector products, no hard KL constraint.
- Unlike TRPO, PPO is **compatible with parameter sharing** between policy and value networks, dropout/noise, and auxiliary tasks.
- On MuJoCo continuous control it **beats A2C, A2C+trust-region, CEM, vanilla PG, and TRPO** on almost all 7 environments; on Atari it is more sample-efficient than A2C and competitive with ACER while being much simpler.

## Methods

- **Probability ratio** $r_t(\theta) = \pi_\theta(a_t|s_t)/\pi_{\theta_{old}}(a_t|s_t)$; the TRPO "CPI" surrogate is $\hat{\mathbb{E}}_t[r_t(\theta)\hat{A}_t]$.
- **Clipped surrogate objective** (Eq. 7): $L^{CLIP}(\theta) = \hat{\mathbb{E}}_t[\min(r_t\hat{A}_t,\ \mathrm{clip}(r_t, 1-\epsilon, 1+\epsilon)\hat{A}_t)]$, with $\epsilon = 0.2$ as the default. Taking the min of clipped and unclipped means the ratio is only clipped when doing so makes the objective *worse*, removing the incentive to move $r_t$ outside $[1-\epsilon, 1+\epsilon]$.
- **Adaptive KL penalty** (Eq. 8) is presented as an alternative baseline: penalize $\beta \cdot \mathrm{KL}$ and adjust $\beta$ up/down to hit a target KL $d_{targ}$. Found to work *worse* than clipping.
- **Combined loss** (Eq. 9) for shared policy/value nets: $L^{CLIP} - c_1 L^{VF} + c_2 S[\pi_\theta]$, where $L^{VF}$ is squared value error and $S$ is an entropy bonus.
- **Advantage estimation** via truncated [[Generalized Advantage Estimation]] (Eqs. 11-12) over fixed-length-$T$ trajectory segments.
- **Algorithm 1 (Actor-Critic style):** $N$ parallel actors each collect $T$ timesteps, compute advantages, then optimize the surrogate on $NT$ samples with $K$ epochs of minibatch SGD/Adam. Fully on-policy — data is discarded after each iteration.
- **Hyperparameters:** MuJoCo used $T{=}2048$, Adam $3\times10^{-4}$, 10 epochs, minibatch 64, $\gamma{=}0.99$, $\lambda{=}0.95$ (Table 3). Atari used $T{=}128$, 8 actors, 3 epochs, $\epsilon{=}0.1\times\alpha$, $c_1{=}1$, $c_2{=}0.01$, linear annealing (Table 5). Policy = 2-hidden-layer MLP of 64 units, tanh, Gaussian output for continuous control.

## Results

- **Surrogate ablation (Table 1):** averaged normalized score over 7 MuJoCo tasks — no clipping/penalty −0.39; clipping $\epsilon{=}0.2$ best at **0.82**; adaptive/fixed KL variants 0.62-0.74. Clipping at $\epsilon{=}0.2$ is the clear winner.
- **Continuous control (Fig. 3):** on 7 MuJoCo envs (1M timesteps, 3 seeds) PPO matches or beats A2C, A2C+trust-region, CEM, vanilla PG-adaptive, and TRPO on almost all tasks.
- **Humanoid showcase (Fig. 4):** trains 3D Roboschool humanoid running, steering (Flagrun), and get-up-under-perturbation (FlagrunHarder) to strong policies over 50-100M timesteps.
- **Atari (Table 2):** across 49 games, PPO wins 30 (vs ACER 18, A2C 1) on avg-reward-over-all-training (sample efficiency / fast learning); ACER edges it 28-19 on final performance (last 100 episodes). PPO is much simpler than ACER.

## Critique / open questions

- Purely **on-policy** — each batch is used for a handful of epochs then thrown away, so it is less sample-efficient than off-policy methods (SAC, TD3) that came later. For a 16GB single-GPU robotics setup this means leaning on massively parallel *simulation* (Isaac Gym / Brax style) rather than replay.
- The clipping objective is a **heuristic**, not the monotonic-improvement guarantee TRPO derives; it does not strictly bound the KL step, and pathological updates can still occur.
- Sensitivity to $\epsilon$, GAE $\lambda$, number of epochs $K$, and advantage normalization is real; the paper's "no tuning needed" framing is optimistic — reproducibility work (e.g. the "implementation matters" line of research) later showed many unlisted code-level details (value clipping, reward scaling, orthogonal init, LR annealing) drive much of PPO's performance.
- Only 3 seeds per env; deep-RL variance makes 3 seeds thin evidence, though the effect sizes are large.
- Value function on Atari shares the network but no separate ablation isolates its contribution.

## Trust signals

- **Credibility:** 5 — OpenAI authors including the TRPO/GAE author (Schulman); the method has been independently reproduced thousands of times and is the de-facto standard on-policy RL algorithm; reference implementation released in OpenAI Baselines. Not formally peer-reviewed (arXiv only), but its real-world validation vastly exceeds any single review.

## Follow-up

- Contrast against off-policy alternatives for sim-first robotics; PPO's on-policy nature pairs with GPU-parallel sims where sample cost is cheap.
- Read [[Trust Region Policy Optimization]] (Sch+15b) and [[Generalized Advantage Estimation]] (Sch+15a) as direct prerequisites.
- Investigate the code-level "implementation details" literature before trusting a from-scratch PPO — the gap between paper-PPO and library-PPO is large.
- Candidate first experiment: PPO on a single MuJoCo / Gym continuous-control task on the 16GB GPU as a baseline for [[Actor-Critic Methods]] in this project.
