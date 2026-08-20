---
kind: moc
name: "Reinforcement Learning for Control"
status: active
added: "2026-07-01"
concepts: [[[concepts/reinforcement-learning]], [[concepts/continuous-control]], [[concepts/actor-critic-methods]], [[concepts/policy-gradient-methods]], [[concepts/soft-actor-critic]], [[concepts/maximum-entropy-rl]], [[concepts/off-policy-rl]], [[concepts/reparameterization-trick]], [[concepts/proximal-policy-optimization]], [[concepts/clipped-surrogate-objective]], [[concepts/on-policy-rl]], [[concepts/trust-region-policy-optimization]], [[concepts/generalized-advantage-estimation]], [[concepts/model-based-rl]], [[concepts/world-model]], [[concepts/recurrent-state-space-model]], [[concepts/symlog-transformation]], [[concepts/return-normalization]]]
tags: [moc, reinforcement-learning, control]
---

# Reinforcement Learning for Control

How do you get a policy when there is a reward but no demonstrations? This
theme answers that. It collects the reward-driven half of the project's two
learning axes — the paradigm, the two dominant algorithm families, and the
components that make each one work. The organizing tension for a single-16GB-GPU
learner is sample efficiency versus stability: off-policy methods reuse a replay
buffer to squeeze more learning out of every environment step, while on-policy
methods discard data after each update but pair cleanly with the thousands of
parallel environments a GPU simulator can stream. A third line — model-based —
sidesteps the tradeoff by learning the dynamics and improving the policy inside
imagined rollouts. All three are practical here because GPU-parallel simulation
removes the environment-step bottleneck that otherwise makes RL too sample-hungry
for one card. The anchoring papers are the canonical entry points:
[[literature/papers/haarnoja2018soft]] for the off-policy line,
[[literature/papers/schulman2017proximal]] for the on-policy line, and
[[literature/papers/hafner2023mastering]] (DreamerV3, *Nature* 2025) for the
model-based line.

## The problem and the families

- [[concepts/reinforcement-learning]] — the umbrella paradigm: learn a control
  policy by maximizing cumulative reward through trial-and-error in an MDP.
- [[concepts/continuous-control]] — the real-valued action setting all the
  baselines here operate in (robot joints, torques), not discrete moves.
- [[concepts/actor-critic-methods]] — the algorithmic family both SAC and PPO
  instantiate: a policy actor guided by a learned value critic.
- [[concepts/policy-gradient-methods]] — the gradient-based route to optimizing
  a parameterized policy directly.

## The off-policy line — sample efficiency via replay

- [[concepts/soft-actor-critic]] — the strong default off-policy algorithm: a
  stochastic Gaussian actor, twin soft Q-critics, and a replay buffer.
- [[concepts/maximum-entropy-rl]] — the objective that adds SAC's entropy bonus,
  rewarding exploration alongside return.
- [[concepts/off-policy-rl]] — the replay-based data regime that gives SAC its
  sample efficiency and suits tight per-card budgets.
- [[concepts/reparameterization-trick]] — how SAC gets low-variance gradients
  through its stochastic actor.

## The on-policy line — stability via constrained steps

- [[concepts/proximal-policy-optimization]] — the de-facto default on-policy
  algorithm and the natural RL baseline, especially with GPU-parallel sim.
- [[concepts/clipped-surrogate-objective]] — PPO's core stability mechanism,
  bounding how far each update can move the policy.
- [[concepts/on-policy-rl]] — the fresh-data regime PPO operates in, where each
  batch is used once.
- [[concepts/trust-region-policy-optimization]] — the theoretically grounded
  predecessor PPO simplifies into a first-order method.
- [[concepts/generalized-advantage-estimation]] — PPO's default advantage
  estimator, trading bias against variance via a λ knob.

## The model-based line — learn the dynamics, imagine the rollouts

- [[concepts/model-based-rl]] — learn a dynamics model and use it to improve the
  policy, rather than learning only from observed transitions; the third family
  alongside on- and off-policy.
- [[concepts/world-model]] — the learned generative model of dynamics that the
  actor and critic train inside, without further environment interaction.
- [[concepts/recurrent-state-space-model]] — Dreamer's specific architecture,
  splitting a deterministic recurrent memory from stochastic per-step latents so
  the model state is Markovian and the actor can stay feedforward.

## Robustness across domains — the portable tricks

DreamerV3's headline is not peak score but *one fixed hyperparameter set across
150+ tasks*. Two of the techniques that buy this are independent of world models
and drop into any critic or policy-gradient implementation, which makes them the
cheapest testable claims in the model-based line.

- [[concepts/symlog-transformation]] — a bi-symmetric log transform plus a
  symexp-twohot regression loss, decoupling gradient magnitude from target
  magnitude so rewards spanning orders of magnitude stay learnable.
- [[concepts/return-normalization]] — divide returns by a percentile range with a
  `max(1, S)` floor, so a single entropy coefficient works under both sparse and
  dense rewards; argued against PPO-style advantage normalization, which
  amplifies noise when reward is out of reach.

## Open thread

Two questions now sit here. First, where the SAC/PPO crossover actually falls
once GPU-parallel simulation makes on-policy sampling nearly free — does
off-policy sample efficiency still buy anything on one card? Second, and
sharpened by the same fact: model-based RL's whole pitch is sample efficiency,
so if [[literature/papers/zakka2025mujoco]]-class throughput makes environment
steps nearly free, does DreamerV3's extra complexity — an RSSM, a decoder, five
loss terms — still pay for itself on wall-clock? The portable tricks above may
turn out to be the durable contribution rather than the world model itself.
