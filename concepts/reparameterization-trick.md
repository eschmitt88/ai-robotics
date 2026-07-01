---
kind: concept
name: "Reparameterization Trick"
status: seedling
added: "2026-07-01"
sources: [haarnoja2018soft]
related_concepts: ["Soft Actor-Critic", "Policy Gradient Methods", "Conditional VAE Policy"]
related_experiments: []
tags: [rl, gradients, variance-reduction]
---

# Reparameterization Trick

## Definition

The reparameterization trick expresses a stochastic policy as a
deterministic function of the state and injected noise (e.g. a ∼ μ + σ·ε),
so gradients can flow through the sampling step.

## Why it matters here

It gives SAC a low-variance policy gradient for its entropy-regularized
stochastic actor, improving training stability under tight seed budgets.

## Connections

- [[Soft Actor-Critic]] — uses this trick to backprop through its Gaussian actor
- [[Policy Gradient Methods]] — an alternative, lower-variance route to the policy gradient
- [[Conditional VAE Policy]] — the same trick enables gradient flow through the VAE latent
