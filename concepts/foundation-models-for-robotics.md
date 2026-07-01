---
kind: concept
name: "Foundation Models for Robotics"
status: seedling
added: "2026-07-01"
sources: [kim2024openvla]
related_concepts: [Vision-Language-Action Model, Open X-Embodiment, Action Tokenization, LoRA Fine-Tuning, Sim-to-Real Transfer]
related_experiments: []
tags: [foundation-models, generalist, pretraining]
---

# Foundation Models for Robotics

## Definition

Large models pretrained on broad data and reused as generalist priors for
robot control, adapted to specific tasks rather than trained from scratch each
time.

## Why it matters here

This is the organizing thread for the project's foundation-model-for-embodied-AI
line: it reframes robot learning as adapting a shared prior, which is exactly
what makes single-GPU experiments with models like OpenVLA worthwhile.

## Connections

- [[Vision-Language-Action Model]] — the concrete robotics instance of the foundation-model paradigm
- [[Open X-Embodiment]] — the broad, multi-embodiment data such foundation models are pretrained on
- [[Action Tokenization]] — lets these models borrow LLM pretraining machinery for control
- [[LoRA Fine-Tuning]] — the cheap adaptation step that specializes the generalist prior
- [[Sim-to-Real Transfer]] — a route to grounding these priors on physical hardware
