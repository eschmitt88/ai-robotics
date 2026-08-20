---
kind: concept
name: "Action Expert"
status: seedling
added: "2026-08-20"
sources: [black2024pi0]
related_concepts: ["Flow Matching", "Vision-Language-Action Model", "Action Chunking", "Generalist Robot Policy", "Diffusion Action Head"]
related_experiments: []
tags: [architecture, mixture-of-experts, vla, action-decoding]
---

# Action Expert

## Definition

A second, separate set of transformer weights dedicated to robotics-specific
tokens (proprioceptive state and actions), sharing the sequence with a
vision-language backbone that handles image and text tokens. Effectively a
two-element mixture of experts split by modality rather than learned routing.

## Why it matters here

It is how [[black2024pi0|π0]] bolts continuous action generation onto a pretrained VLM without
disturbing it: PaliGemma (3B) keeps its Internet-scale semantics, a
randomly-initialized 300M expert learns [[Flow Matching]] over actions, and the
authors report that separate weights outperformed sharing. It also makes the
inference optimization possible — the observation prefix is encoded once and
cached, and only the action-expert suffix recomputes across the 10 integration
steps.

The same "small dedicated head on a large frozen-purpose backbone" pattern appears
in [[ghosh2024octo|Octo]]'s [[Diffusion Action Head]], which suggests it is the general shape of
efficient VLA action decoding rather than a [[black2024pi0|π0]] idiosyncrasy.

## Connections

- [[Flow Matching]] — the objective the expert is trained with
- [[Diffusion Action Head]] — the same structural idea with a diffusion objective
- [[Action Chunking]] — the expert emits an H=50 chunk with bidirectional attention
- [[Vision-Language-Action Model]] — what the expert converts a VLM into
