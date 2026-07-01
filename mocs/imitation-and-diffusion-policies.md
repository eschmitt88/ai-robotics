---
kind: moc
name: "Imitation Learning & Diffusion Policies"
status: active
added: "2026-07-01"
concepts: [[[concepts/imitation-learning]], [[concepts/behavior-cloning]], [[concepts/compounding-error]], [[concepts/multimodal-action-distribution]], [[concepts/action-chunking]], [[concepts/temporal-ensembling]], [[concepts/conditional-vae-policy]], [[concepts/receding-horizon-control]], [[concepts/diffusion-policy]], [[concepts/denoising-diffusion-probabilistic-models]], [[concepts/ddim]], [[concepts/score-function]], [[concepts/energy-based-model]], [[concepts/film-conditioning]], [[concepts/position-vs-velocity-control]]]
tags: [moc, imitation-learning, diffusion]
---

# Imitation Learning & Diffusion Policies

When reward is hard to specify but demonstrations are cheap — or already exist
in sim — imitation is the fastest route to a working policy on a single 16GB
GPU. This theme groups the demonstration-driven half of the project. It starts
from the paradigm and its defining failure mode (small per-step errors compound
into unseen states), then follows the two lines of defense that dominate modern
practice: predicting whole action chunks instead of single steps, and using a
generative model expressive enough to capture the many valid ways an expert
might act. The two anchors are complementary reference implementations that both
fit comfortably on one card: [[literature/papers/zhao2023learning]] (ACT, the
action-chunking transformer) and [[literature/papers/chi2023diffusion]]
(Diffusion Policy).

## The paradigm and its failure mode

- [[concepts/imitation-learning]] — learn from expert demonstrations rather
  than a reward signal; the overarching problem class here.
- [[concepts/behavior-cloning]] — the simplest instantiation: supervised
  regression on observation-action pairs.
- [[concepts/compounding-error]] — BC's defining weakness, where covariate
  shift drives the agent into states its errors only make worse.
- [[concepts/multimodal-action-distribution]] — why naive regression to a mean
  fails: several distinct actions can all be correct in one state.

## The action-chunking family — commit to a plan

- [[concepts/action-chunking]] — predict a short future action sequence per
  query, cutting the number of drift-inducing decision points.
- [[concepts/temporal-ensembling]] — average overlapping chunks for smoother
  motion and further-damped drift.
- [[concepts/conditional-vae-policy]] — ACT's generative backbone, modeling the
  distribution over chunks rather than a point estimate.
- [[concepts/receding-horizon-control]] — execute only part of each predicted
  chunk before re-planning.

## The diffusion machinery — generate multimodal actions

- [[concepts/diffusion-policy]] — denoise Gaussian noise into a coherent action
  chunk conditioned on observations; the reference generative-imitation baseline.
- [[concepts/denoising-diffusion-probabilistic-models]] — the generative
  machinery underneath the policy.
- [[concepts/ddim]] — the deterministic sampler that cuts denoising steps for
  faster inference.
- [[concepts/score-function]] — the gradient-of-log-density view that connects
  diffusion to energy-based modeling.
- [[concepts/energy-based-model]] — the implicit-policy framing diffusion
  approximates for multimodal actions.
- [[concepts/film-conditioning]] — how the CNN variant injects visual
  observations into the denoiser.

## A design choice that cuts across both

- [[concepts/position-vs-velocity-control]] — whether the action space is target
  positions or velocities, a choice that materially affects both families'
  stability and transfer.

## Open thread

Chunking and diffusion attack the same compounding-error problem from different
angles; whether their gains stack — or whether a chunked diffusion policy is
just paying twice for one fix — is worth an experiment on the same task.
