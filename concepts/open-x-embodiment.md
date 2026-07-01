---
kind: concept
name: "Open X-Embodiment"
status: seedling
added: "2026-07-01"
sources: [kim2024openvla]
related_concepts: [Vision-Language-Action Model, Foundation Models for Robotics, Imitation Learning, Behavior Cloning]
related_experiments: []
tags: [dataset, pretraining, multi-embodiment]
---

# Open X-Embodiment

## Definition

A pooled, standardized-format dataset merging 70+ real-robot datasets (2M+
trajectories) spanning many robot embodiments, morphologies, and tasks into a
single training corpus.

## Why it matters here

It is the de-facto substrate for generalist robot pretraining and OpenVLA's
actual training corpus, so understanding its format and coverage is
prerequisite to fine-tuning or extending any modern VLA.

## Connections

- [[Vision-Language-Action Model]] — the pretraining corpus that gives VLAs their cross-task generality
- [[Foundation Models for Robotics]] — the large, diverse data that makes a generalist robot prior possible
- [[Imitation Learning]] — a demonstration dataset, so pretraining on it is imitation at scale
- [[Behavior Cloning]] — supplies the observation-action pairs consumed by supervised policy learning
