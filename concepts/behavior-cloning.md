---
kind: concept
name: "Behavior Cloning"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion, kim2024openvla, zhao2023learning]
related_concepts: [Imitation Learning, Compounding Error, Action Chunking, Diffusion Policy, Multimodal Action Distribution]
related_experiments: []
tags: [imitation, supervised-learning, baseline]
---

# Behavior Cloning

## Definition

Supervised learning of a policy that maps observations directly to expert
actions, minimizing prediction error over a fixed demonstration dataset.

## Why it matters here

It is the baseline imitation paradigm that Diffusion Policy, ACT, and
OpenVLA all build on and improve; understanding its failure modes explains
why those methods exist and gives a cheap first policy to beat.

## Connections

- [[Imitation Learning]] — behavior cloning is its canonical supervised form
- [[Compounding Error]] — the central weakness of naive behavior cloning
- [[Action Chunking]] — a fix that reduces compounding error in BC
- [[Multimodal Action Distribution]] — unimodal BC regression fails to
  capture multiple valid actions
- [[Diffusion Policy]] — a BC method that models the full action
  distribution instead of a point estimate
