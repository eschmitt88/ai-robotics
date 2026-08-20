---
kind: concept
name: "Symlog Transformation"
status: seedling
added: "2026-08-20"
sources: [hafner2023mastering]
related_concepts: ["Return Normalization", "World Model", "Recurrent State-Space Model", "Reinforcement Learning"]
related_experiments: []
tags: [normalization, robustness, regression-target, portable-trick]
---

# Symlog Transformation

## Definition

`symlog(x) = sign(x)·ln(|x| + 1)`, with inverse `symexp(x) = sign(x)·(exp(|x|) − 1)`.
A bi-symmetric logarithm that compresses large magnitudes of either sign while
approximating the identity near zero. Paired in [[hafner2023mastering|DreamerV3]] with the **symexp
twohot loss**: predict logits over exponentially spaced bins and train on twohot
targets with cross-entropy, so gradient size is decoupled from target size.

## Why it matters here

It is the trick that lets one hyperparameter set span domains whose rewards differ
by orders of magnitude — squared loss on large targets diverges, absolute/Huber
losses stall, and running-statistics normalization injects non-stationarity.
[[hafner2023mastering|DreamerV3]]'s BSuite results show its largest gains precisely in the
reward-scale-robustness category, which is direct evidence the mechanism works as
claimed.

Worth isolating because it is a **drop-in change to any critic**, testable
independently of world models.

## Connections

- [[Return Normalization]] — the sibling robustness technique, applied to the actor
- [[Recurrent State-Space Model]] — applies symlog to vector observations and decoder targets
- [[World Model]] — the setting [[hafner2023mastering|DreamerV3]] validated it in
