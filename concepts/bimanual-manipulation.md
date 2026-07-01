---
kind: concept
name: "Bimanual Manipulation"
status: seedling
added: "2026-07-01"
sources: [zhao2023learning]
related_concepts: ["Action Chunking", "Imitation Learning", "Temporal Ensembling", "Conditional VAE Policy", "Manipulation Benchmark"]
related_experiments: []
tags: [manipulation, bimanual, aloha]
---

# Bimanual Manipulation

## Definition

Coordinated two-arm robotic manipulation aimed at contact-rich, high-precision
tasks that a single arm cannot perform. It demands tight temporal coordination
between the arms and tolerance to cumulative error.

## Why it matters here

It is the application domain motivating action chunking, and ALOHA shows a low-cost
two-arm rig makes such tasks accessible — grounding the project's imitation-learning
work in a concrete, buildable setting.

## Connections

- [[Action Chunking]] — introduced specifically to make precise bimanual tasks feasible
- [[Imitation Learning]] — the paradigm used to teach these coordinated skills from demonstrations
- [[Temporal Ensembling]] — smooths the chunked bimanual actions at inference for precision
- [[Conditional VAE Policy]] — absorbs the multimodal human demonstrations collected on the rig
- [[Manipulation Benchmark]] — situates bimanual tasks within standardized evaluation
