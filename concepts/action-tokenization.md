---
kind: concept
name: "Action Tokenization"
status: seedling
added: "2026-07-01"
sources: [kim2024openvla]
related_concepts: [Vision-Language-Action Model, Foundation Models for Robotics, Behavior Cloning]
related_experiments: []
tags: [vla, tokenization, action-space]
---

# Action Tokenization

## Definition

Discretizing each continuous action dimension into a fixed number of bins that
are mapped onto tokens in the LLM's vocabulary, so actions are produced by
ordinary next-token prediction trained with cross-entropy loss.

## Why it matters here

It is the trick that lets an unmodified language-model backbone emit robot
actions, meaning a VLA can be trained and fine-tuned with the same tooling as
any autoregressive LLM — no bespoke action head required.

## Connections

- [[Vision-Language-Action Model]] — the mechanism that makes VLA control possible on an LLM backbone
- [[Foundation Models for Robotics]] — reuses pretrained-LLM machinery rather than a new regression head
- [[Behavior Cloning]] — recasts supervised action prediction as a classification problem over token bins
