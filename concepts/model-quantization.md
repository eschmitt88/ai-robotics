---
kind: concept
name: "Model Quantization"
status: seedling
added: "2026-07-01"
sources: [kim2024openvla]
related_concepts: [Vision-Language-Action Model, LoRA Fine-Tuning, Foundation Models for Robotics]
related_experiments: []
tags: [quantization, inference, single-gpu]
---

# Model Quantization

## Definition

Serving model weights at reduced numeric precision (e.g. int8 or int4) to cut
inference memory and bandwidth, trading a small accuracy loss for a large
footprint reduction.

## Why it matters here

int4 quantization brings OpenVLA inference down to roughly 7GB, making a 16GB
GPU a viable deployment target for a 7B VLA — the difference between "runs
locally" and "does not fit".

## Connections

- [[Vision-Language-Action Model]] — the enabler for deploying a 7B VLA on consumer hardware
- [[LoRA Fine-Tuning]] — combines with quantization (QLoRA) so both fine-tuning and serving fit in memory
- [[Foundation Models for Robotics]] — makes large pretrained robot models practical to actually run
