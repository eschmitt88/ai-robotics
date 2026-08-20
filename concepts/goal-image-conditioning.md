---
kind: concept
name: "Goal-Image Conditioning"
status: seedling
added: "2026-08-20"
sources: [ghosh2024octo]
related_concepts: ["Generalist Robot Policy", "Vision-Language-Action Model", "Imitation Learning"]
related_experiments: []
tags: [task-specification, goal-conditioned, hindsight-relabeling]
---

# Goal-Image Conditioning

## Definition

Specifying a task by supplying an image of the desired final state rather than a
language instruction. Training data is generated for free by **hindsight
relabeling** — pick a state uniformly from later in a demonstration and call it
the goal — so it needs no human annotation.

## Why it matters here

[[ghosh2024octo|Octo]] measured goal-image conditioning at **25% higher success than language**
conditioning on the same WidowX tasks, plausibly because an image specifies the
target more completely than a sentence. It also sidesteps annotation cost: only
56% of Octo's pretraining data had language labels, but every trajectory can be
relabeled into goal-image supervision.

## Connections

- [[Generalist Robot Policy]] — goal images are one of the flexible task interfaces a GRP exposes
- [[Vision-Language-Action Model]] — the language-conditioned alternative it outperformed
- [[Imitation Learning]] — hindsight relabeling turns unlabeled demos into supervision
