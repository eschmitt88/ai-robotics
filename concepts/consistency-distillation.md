---
kind: concept
name: "Consistency Distillation"
status: seedling
added: "2026-08-20"
sources: [prasad2024consistency]
related_concepts: ["Probability Flow ODE", "Diffusion Policy", "DDIM", "Diffusion Action Head", "Flow Matching"]
related_experiments: []
tags: [distillation, diffusion, inference-speed, edge-compute]
---

# Consistency Distillation

## Definition

Training a student network to map any two points on the *same*
[[Probability Flow ODE]] trajectory to the same output, using a pretrained
diffusion teacher to generate the trajectory pairs. The resulting student
generates in one or a few steps instead of tens or hundreds.

## Why it matters here

It is the training-time answer to diffusion inference cost, and
[[prasad2024consistency|Consistency Policy]] shows it is not interchangeable with the cheap
alternative: naively cutting [[DDIM]] steps collapses on hard tasks (.14 on Tool
Hang) while distillation holds (.77). The catch is that the saving is purely at
inference — distillation *increases* total training cost, since you must train the
teacher first and each student step runs the teacher plus multiple student passes.

Two non-obvious implementation facts from [[prasad2024consistency|Consistency Policy]]: the **local**
CTM objective (adjacent `t,u`, arbitrary `s`) beats full CTM while training >40%
faster, and **dropout on the `s→0` steps is load-bearing** — without it the
consistency loss vanishes and provides no signal.

## Connections

- [[Probability Flow ODE]] — the deterministic trajectory whose self-consistency is enforced
- [[Diffusion Policy]] — the teacher being distilled
- [[DDIM]] — the step-reduction alternative that distillation outperforms on hard tasks
- [[Diffusion Action Head]] — an architectural route to the same saving, no distillation needed
- [[Flow Matching]] — a third route: fewer steps by construction
- [[Multimodal Action Distribution]] — what distillation to a deterministic ODE partly gives up
