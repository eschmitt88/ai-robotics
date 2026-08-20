---
kind: concept
name: "Diffusion Action Head"
status: seedling
added: "2026-08-20"
sources: [ghosh2024octo]
related_concepts: ["Diffusion Policy", "Multimodal Action Distribution", "Action Tokenization", "Action Chunking", "Consistency Distillation"]
related_experiments: []
tags: [diffusion, action-decoding, architecture, inference-cost]
---

# Diffusion Action Head

## Definition

A small denoising network placed on top of a transformer backbone's readout
embedding, which runs its K denoising steps entirely inside the head so that only
**one** backbone forward pass is needed per action chunk. Distinct from
[[Diffusion Policy]], where denoising re-runs the full observation-conditioned
network at every step.

## Why it matters here

It is the cheap way to get a [[Multimodal Action Distribution]] without paying
[[Diffusion Policy]]'s inference cost — most of the saving that
[[Consistency Distillation]] achieves through a separate training stage is
obtained here architecturally, for free. [[ghosh2024octo|Octo]]'s ablation puts it decisively
ahead of the alternatives on the same backbone: 83% vs 35% (MSE) vs 18%
(discretized).

## Connections

- [[Diffusion Policy]] — the full-network formulation this simplifies
- [[Multimodal Action Distribution]] — what the diffusion objective buys over MSE
- [[Action Tokenization]] — the discrete alternative it beats in [[ghosh2024octo|Octo]]'s ablation
- [[Consistency Distillation]] — attacks the same cost from the training side
- [[Flow Matching]] — [[black2024pi0|π0]]'s alternative answer to the same problem
