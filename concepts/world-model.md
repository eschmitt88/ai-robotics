---
kind: concept
name: "World Model"
status: seedling
added: "2026-08-20"
sources: [hafner2023mastering]
related_concepts: ["Model-Based RL", "Recurrent State-Space Model", "Reinforcement Learning", "Actor-Critic Methods"]
related_experiments: []
tags: [world-model, model-based, imagination, representation-learning]
---

# World Model

## Definition

A learned generative model of environment dynamics that predicts future latent
states, rewards, and episode continuation given actions. Once learned, the actor
and critic can be trained entirely on *imagined* rollouts inside the model,
without further environment interaction.

## Why it matters here

It decouples policy improvement from environment sampling, which is the classic
sample-efficiency argument for model-based control. [[hafner2023mastering|DreamerV3]]'s ablation makes
a stronger and less obvious claim: its performance rests **predominantly on the
unsupervised reconstruction loss** of the world model, not on reward/value
gradients — the reverse of model-free agents. That points at unsupervised
pretraining as a route to control.

## Connections

- [[Model-Based RL]] — the algorithm family built on world models
- [[Recurrent State-Space Model]] — the specific architecture [[hafner2023mastering|DreamerV3]] uses
- [[Actor-Critic Methods]] — trained inside the world model rather than the environment
- [[GPU-Parallelized Simulation]] — the competing answer to sample cost; when env steps are nearly free, the sample-efficiency argument weakens
