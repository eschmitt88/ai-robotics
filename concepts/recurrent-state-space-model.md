---
kind: concept
name: "Recurrent State-Space Model"
status: seedling
added: "2026-08-20"
sources: [hafner2023mastering]
related_concepts: ["World Model", "Model-Based RL", "Symlog Transformation"]
related_experiments: []
tags: [rssm, world-model, latent-dynamics, architecture]
---

# Recurrent State-Space Model

## Definition

The world-model architecture underlying Dreamer: an encoder maps observations to
stochastic discrete latents `z_t`; a deterministic recurrent state `h_t` predicts
the next latent from past actions; the model state `s_t = {h_t, z_t}` feeds reward,
continuation, and reconstruction heads. Splitting deterministic memory from
stochastic per-step latents is the defining choice.

## Why it matters here

The model state is Markovian by construction, so the actor and critic can be plain
feedforward networks operating on `s_t`. [[hafner2023mastering|DreamerV3]]'s stability rests on two
specific fixes inside the RSSM: **KL free bits** (clip both KL terms below 1 nat),
which removes the per-environment representation-loss tuning earlier versions
needed, and parameterizing the categoricals as **1% uniform + 99% network output**
so they can never go deterministic and spike the KL.

## Connections

- [[World Model]] — what the RSSM instantiates
- [[Model-Based RL]] — the algorithm family it serves
- [[Symlog Transformation]] — applied to the RSSM's vector observations and decoder targets
