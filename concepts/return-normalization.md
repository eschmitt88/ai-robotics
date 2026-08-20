---
kind: concept
name: "Return Normalization"
status: seedling
added: "2026-08-20"
sources: [hafner2023mastering]
related_concepts: ["Symlog Transformation", "Policy Gradient Methods", "Maximum Entropy RL", "Proximal Policy Optimization"]
related_experiments: []
tags: [normalization, exploration, entropy, policy-gradient, portable-trick]
---

# Return Normalization

## Definition

Dividing policy-gradient returns by `S = EMA(percentile(R, 95) − percentile(R, 5))`
with a **denominator limit** — the divisor is `max(1, S)`, so only large return
ranges are scaled down and small ones pass through untouched. This lets a single
fixed entropy coefficient work across domains.

## Why it matters here

The contrast with standard practice is the point. Normalizing *advantages* (as
[[Proximal Policy Optimization]] does) puts fixed emphasis on returns regardless of
whether reward is reachable, amplifying function-approximation noise under sparse
rewards until exploration stalls. Normalizing by standard deviation fails outright
when it approaches zero. The percentile range handles outliers; the `max(1, S)`
floor is what preserves reward-frequency information.

It is a one-line change to any policy-gradient implementation, so the claim is
cheap to test against PPO's advantage normalization on a sparse-reward task.

## Connections

- [[Symlog Transformation]] — the sibling technique, applied to critic and reward targets
- [[Policy Gradient Methods]] — what it modifies
- [[Proximal Policy Optimization]] — the advantage-normalization baseline it argues against
- [[Maximum Entropy RL]] — the entropy scale this normalization is designed to keep fixed
