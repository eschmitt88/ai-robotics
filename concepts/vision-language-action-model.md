---
kind: concept
name: "Vision-Language-Action Model"
status: seedling
added: "2026-07-01"
sources: [kim2024openvla]
related_concepts: [Action Tokenization, Foundation Models for Robotics, LoRA Fine-Tuning, Open X-Embodiment, Behavior Cloning]
related_experiments: []
tags: [vla, foundation-models, policy]
---

# Vision-Language-Action Model

## Definition

A robot policy built by fine-tuning a pretrained vision-language model so it
maps a camera image plus a natural-language instruction directly to control
actions. OpenVLA is the canonical open-weights instance of this family.

## Why it matters here

VLAs are the project's reference point for language-conditioned generalist
control, and OpenVLA is small and open enough to fine-tune and serve on a
single 16GB GPU with the right adaptation and quantization tricks.

## Connections

- [[Action Tokenization]] — the mechanism that lets the VLM's LLM head emit continuous robot actions
- [[Foundation Models for Robotics]] — VLAs are the concrete embodiment of the foundation-model-as-generalist-prior idea
- [[Open X-Embodiment]] — the pooled multi-embodiment corpus OpenVLA is pretrained on
- [[LoRA Fine-Tuning]] — the practical route to adapting a 7B VLA on modest hardware
- [[Behavior Cloning]] — VLAs are ultimately supervised imitation, scaled onto a pretrained backbone
