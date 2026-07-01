---
kind: concept
name: "Fused Vision Encoder"
status: seedling
added: "2026-07-01"
sources: [kim2024openvla]
related_concepts: [Vision-Language-Action Model, Foundation Models for Robotics]
related_experiments: []
tags: [vision, architecture, encoder]
---

# Fused Vision Encoder

## Definition

A visual front-end that concatenates features from two pretrained vision
backbones — DINOv2 for spatial structure and SigLIP for semantics — to form
the image input the VLM's language model reasons over.

## Why it matters here

The dual-backbone fusion is credited for OpenVLA's edge on spatial reasoning,
making it a transferable architectural lesson: combining complementary
pretrained encoders can outperform a single one at little extra cost.

## Connections

- [[Vision-Language-Action Model]] — the visual perception module inside OpenVLA's VLA architecture
- [[Foundation Models for Robotics]] — reuses off-the-shelf pretrained vision priors rather than training encoders from scratch
