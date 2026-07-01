---
kind: proposal
slug: diffusion-policy-pusht
date: 2026-07-01
status: proposed
hypothesis: "A CNN-based Diffusion Policy trained on the PushT benchmark within a single 16 GB GPU reproduces the paper's headline advantage — matching its reported ~0.9 success rate and beating a matched behavior-cloning MLP baseline by a wide margin, because it models the task's multimodal action distribution that the unimodal baseline cannot."
rationale: "Chi et al. report ~47% average improvement over prior methods across 12 tasks, attributing the gain to diffusion's ability to represent multimodal action distributions and to action chunking with receding-horizon execution. PushT is their lightest task and the standard entry point, with clean released code that fits comfortably on one GPU. Reproducing it — and pinning the gain to multimodality via a BC ablation — is the cleanest hands-on test of the era's most influential imitation method."
reads:
  - "[[literature/papers/chi2023diffusion]]"
  - "[[concepts/diffusion-policy]]"
  - "[[concepts/behavior-cloning]]"
  - "[[concepts/multimodal-action-distribution]]"
  - "[[concepts/action-chunking]]"
  - "[[concepts/ddim]]"
  - "[[concepts/receding-horizon-control]]"
expected_metric:
  name: pusht_success_rate
  target: 0.85
  direction: higher-is-better
design_sketch:
  - "Task: PushT (state or image obs) from the released Diffusion Policy benchmark; use the provided demonstration dataset."
  - "Models: CNN-based Diffusion Policy (DDPM training) vs a matched-capacity BC-MLP baseline on identical demos."
  - "Ablations: DDIM inference steps (100 → 10 → 1) to map the success-rate/latency trade-off under the compute envelope; action-chunk horizon sweep."
  - "Metric: rollout success rate over N seeded episodes; secondary: inference latency per action."
risks:
  - "Diffusion Policy repo pins older torch/gym versions — env resolution under uv may need a pinned lockfile or a separate venv."
  - "Full image-obs training may exceed a comfortable time budget; fall back to state-based PushT first."
  - "PushT demo dataset download size / availability; verify before committing a DVC stage."
related_prior: []
estimated_runtime: "state-based: ~1–2 h single GPU; image-based: several hours (schedule against budget.yaml)."
---

## Why this experiment, now

Where the SAC-vs-PPO run calibrates the reinforcement-learning axis, this
calibrates the imitation-learning axis — the other half of the project. It
takes [[concepts/diffusion-policy]], the single most-cited method in the graph,
and asks the only question that matters for a hands-on learner: *does it
actually reproduce on my hardware, and is the advantage real for the reason the
authors claim?*

The hypothesis has two testable parts. First, absolute reproduction: hit ~0.85+
success on PushT, near the paper's number. Second, and more interesting, the
*mechanism*: a matched-capacity [[concepts/behavior-cloning]] MLP should fail
where Diffusion Policy succeeds, and it should fail specifically on the
multimodal decisions ([[concepts/multimodal-action-distribution]]) — going left
vs right around the T-block — that a unimodal regressor averages into an
invalid middle action. If the BC baseline is competitive, the multimodality
story is weaker than advertised for this task.

The [[concepts/ddim]] step-count sweep is where this experiment pays rent
against the project's compute-envelope question: diffusion sampling is the
latency cost, and distilling 100 steps down toward 1 is exactly the lever a
16 GB-GPU deployment would pull. Mapping success-rate against inference steps
gives a reusable curve for every later diffusion experiment. This is the more
ambitious of the two opening proposals and depends on resolving an older
dependency stack, so it is sequenced *after* the SAC-vs-PPO calibration run.
