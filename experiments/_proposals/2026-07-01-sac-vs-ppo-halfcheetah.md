---
kind: proposal
slug: sac-vs-ppo-halfcheetah
date: 2026-07-01
status: proposed
hypothesis: "At an equal budget of 200k environment steps on HalfCheetah-v5, off-policy SAC reaches a higher mean evaluation return than on-policy PPO — SAC's replay-buffer sample-efficiency dominates when env steps are the bottleneck."
rationale: "Haarnoja et al. show SAC matches or beats PPO/DDPG on MuJoCo locomotion and is far more stable across seeds, driven by off-policy replay reuse and a max-entropy objective. Schulman et al.'s PPO is stable but on-policy, so it discards each batch and is sample-hungry. On a single 16 GB GPU where wall-clock and sample budget both matter, quantifying this gap on the canonical HalfCheetah task is the cheapest possible first hands-on experiment and calibrates the whole RL axis."
reads:
  - "[[literature/papers/haarnoja2018soft]]"
  - "[[literature/papers/schulman2017proximal]]"
  - "[[concepts/soft-actor-critic]]"
  - "[[concepts/proximal-policy-optimization]]"
  - "[[concepts/off-policy-rl]]"
  - "[[concepts/on-policy-rl]]"
  - "[[concepts/continuous-control]]"
expected_metric:
  name: mean_eval_return_at_200k
  target: 1.5
  direction: higher-is-better
design_sketch:
  - "Env: Gymnasium HalfCheetah-v5 (MuJoCo via the `mujoco` pip wheel — no license needed)."
  - "Algos: stable-baselines3 SAC and PPO with library-default hyperparameters (honest baseline; no per-algo tuning)."
  - "Budget: 200k env steps each, 3 seeds (0/1/2); evaluate 10 deterministic episodes every 10k steps."
  - "Metric: final mean eval return per algo (mean±std over seeds); report the SAC/PPO ratio as the headline number (target ≥1.5)."
  - "Log learning curves to results/; write means to metrics.json. Record wall-clock and peak VRAM per algo — the secondary question is whether the GPU even helps for small MLP policies."
risks:
  - "MuJoCo wheel / GL headless rendering hiccups on first install (mitigation: state-based obs only, no rendering; EGL/OSMesa not required)."
  - "200k steps may be too short for PPO to catch up on HalfCheetah, or too short for either to plateau — curves may not have converged (acceptable: the sample-efficiency question is about equal-budget comparison, not asymptote)."
  - "Small MLP policies may run faster on CPU than GPU; GPU 'help' could be negative — that is itself a reportable finding for the compute-envelope question."
related_prior: []
estimated_runtime: "~45–75 min for both algos × 3 seeds at 200k steps (well under budget.yaml's 24 wall-hours and 500 GB disk)."
---

## Why this experiment, now

This is the calibration experiment for the entire reinforcement-learning axis
of the project, and deliberately the first thing to run: it is cheap, it uses
only library defaults, and it turns two of the project's most load-bearing
concept notes — [[concepts/soft-actor-critic]] and
[[concepts/proximal-policy-optimization]] — into a curve I can actually stare
at. The literature is unambiguous in its qualitative claim ([[literature/papers/haarnoja2018soft]]:
SAC is more sample-efficient and more stable than on-policy baselines) but a
qualitative claim is not the same as watching it happen on *this* box under a
fixed step budget.

The hypothesis is falsifiable in the way that matters: if PPO matches or beats
SAC at 200k steps with default hyperparameters, then either my mental model of
the off-policy/on-policy trade-off is wrong for this task-and-budget regime, or
SB3's PPO defaults are unusually strong here — both worth knowing before I lean
on either algorithm downstream. The headline metric is the SAC/PPO return
ratio at 200k steps; a ratio ≥1.5 confirms the sample-efficiency story, a ratio
near 1.0 falsifies it at this budget.

The secondary payload is the compute-envelope question the project keeps
returning to: does a 16 GB GPU actually accelerate these small-MLP continuous-
control runs, or is the CPU faster because the bottleneck is environment
stepping, not matrix multiplies? Logging wall-clock and peak VRAM per algo
costs nothing extra and gives the first real data point on what "fits" means
here. Because RL evaluation is episodic return in a live env — there is no
held-out data split — the HCE rule does not apply and no `splits.yaml` is
needed.
