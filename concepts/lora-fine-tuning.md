---
kind: concept
name: "LoRA Fine-Tuning"
status: seedling
added: "2026-07-01"
sources: [kim2024openvla]
related_concepts: [Vision-Language-Action Model, Model Quantization, Foundation Models for Robotics]
related_experiments: []
tags: [peft, fine-tuning, single-gpu]
---

# LoRA Fine-Tuning

## Definition

Low-Rank Adaptation freezes the pretrained weights and injects small trainable
low-rank adapter matrices into each layer, so only a tiny fraction of
parameters is updated during fine-tuning.

## Why it matters here

LoRA is the path to adapting a 7B-parameter VLA to a new task on modest
hardware, keeping the optimizer state and trainable-parameter count small
enough to fit alongside the model on a single 16GB GPU.

## Connections

- [[Vision-Language-Action Model]] — the practical way to specialize a large VLA to a downstream task
- [[Model Quantization]] — pairs with LoRA (as QLoRA) to fit both training and serving in tight memory
- [[Foundation Models for Robotics]] — the adaptation step that turns a frozen generalist prior into a task policy
