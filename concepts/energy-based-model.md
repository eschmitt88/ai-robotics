---
kind: concept
name: "Energy-Based Model"
status: seedling
added: "2026-07-01"
sources: [chi2023diffusion]
related_concepts: [Diffusion Policy, Score Function, Denoising Diffusion Probabilistic Models, Multimodal Action Distribution]
related_experiments: []
tags: [generative, energy, theory]
---

# Energy-Based Model

## Definition

A policy represented as a scalar energy function over actions, where
sampling means searching for low-energy (high-probability) actions; the
partition function normalizing this distribution is intractable.

## Why it matters here

It is the closest prior approach to Diffusion Policy: its intractable
normalization forces unstable contrastive training, which is exactly the
difficulty diffusion avoids — a useful contrast for understanding why
diffusion won.

## Connections

- [[Diffusion Policy]] — supersedes EBM policies with a stable objective
- [[Score Function]] — score matching removes the normalization term EBMs
  struggle with
- [[Denoising Diffusion Probabilistic Models]] — the diffusion alternative
  to explicit energy models
- [[Multimodal Action Distribution]] — both EBMs and diffusion can represent
  multiple modes
