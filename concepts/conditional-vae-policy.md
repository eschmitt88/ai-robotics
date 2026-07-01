---
kind: concept
name: "Conditional VAE Policy"
status: seedling
added: "2026-07-01"
sources: [zhao2023learning]
related_concepts: [Multimodal Action Distribution, Action Chunking, Behavior Cloning, Temporal Ensembling, Bimanual Manipulation]
related_experiments: []
tags: [imitation, generative, latent-variable]
---

# Conditional VAE Policy

## Definition

A policy trained as a conditional variational autoencoder with a latent
"style" variable that captures demonstration variability during training and
is set to its prior mean (z=0) at test time.

## Why it matters here

The latent absorbs noisy, multimodal human demonstrations that would
otherwise confuse a deterministic regressor — the ACT ablation shows it is
essential, making it a key ingredient when learning from imperfect human
data.

## Connections

- [[Multimodal Action Distribution]] — the CVAE latent lets one policy
  represent several valid behaviors
- [[Action Chunking]] — ACT decodes an action chunk conditioned on the latent
- [[Behavior Cloning]] — a generative upgrade over point-estimate BC
- [[Temporal Ensembling]] — pairs with the CVAE at inference for smooth motion
- [[Bimanual Manipulation]] — the two-arm ALOHA setting this policy targets
