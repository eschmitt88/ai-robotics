---
kind: concept
name: "Generalist Robot Policy"
status: seedling
added: "2026-08-20"
sources: [black2024pi0, ghosh2024octo, kim2024openvla]
related_concepts: ["Foundation Models for Robotics", "Vision-Language-Action Model", "Cross-Embodiment Transfer", "Open X-Embodiment", "Imitation Learning"]
related_experiments: []
tags: [generalist, policy, pretraining, multi-robot]
---

# Generalist Robot Policy

## Definition

A single pretrained policy that performs low-level visuomotor control across many
tasks, environments, and robot embodiments, and is then prompted or finetuned to a
downstream setup rather than trained from scratch. Abbreviated GRP in [[ghosh2024octo|Octo]].

## Why it matters here

It reframes the unit of work: instead of training a task-specific policy, you
finetune a shared one. Whether that is affordable on a 16 GB GPU depends entirely
on scale — [[ghosh2024octo|Octo]] (27M/93M) finetunes in ~5 h on one consumer card, while
[[black2024pi0|π0]] (3.3B) and [[kim2024openvla|OpenVLA]] (7B) are inference-or-LoRA-only here.

## Connections

- [[Vision-Language-Action Model]] — the subclass of GRPs built on a pretrained VLM
- [[Cross-Embodiment Transfer]] — the mechanism that makes one policy serve many robots
- [[Open X-Embodiment]] — the dataset most GRPs are pretrained on
- [[Foundation Models for Robotics]] — the broader framing this sits inside
- [[Diffusion Action Head]] — the decoding scheme [[ghosh2024octo|Octo]] found best for GRP pretraining
