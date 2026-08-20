---
kind: paper
title: "Domain Randomization for Transferring Deep Neural Networks from Simulation to the Real World"
authors: [Josh Tobin, Rachel Fong, Alex Ray, Jonas Schneider, Wojciech Zaremba, Pieter Abbeel]
institutions: ["OpenAI", "UC Berkeley EECS", "ICSI"]
year: 2017
venue: "IEEE/RSJ IROS 2017"
peer_reviewed: true
url: https://arxiv.org/abs/1703.06907
code_url: null
citations: null
source: "raw/papers/tobin2017domain.pdf"
added: "2026-08-20"
relevance: 4
credibility: 5
status: read
related_experiments: []
related_concepts: ["Domain Randomization", "Sim-to-Real Transfer", "Digital Twins", "Parallel Rendering"]
tags: [sim-to-real, domain-randomization, seminal, object-detection, mujoco, robustness, low-compute]
---

# Domain Randomization for Transferring Deep Neural Networks from Simulation to the Real World

## TL;DR

The paper that named and established [[Domain Randomization]]. Train a VGG-16
object detector on hundreds of thousands of **deliberately non-photorealistic**
MuJoCo renders — random RGB / gradient / checker textures, random lighting,
random camera pose, 0–10 random distractors — and it localizes real objects to
**~1.5 cm** with zero real training images. The central hypothesis: *with enough
simulated variability, the real world looks to the model like just another
sample from the training distribution*. Its enduring value is not the detector
but the **ablation study**, which is the actual evidence for what randomization
buys and what it doesn't.

## Claims

- **First successful transfer of a network trained only on simulated RGB images
  (no real pretraining on the task, no domain adaptation) to real-world robotic
  control.** Prior work (Sadeghi & Levine) transferred a quadrotor policy but
  used mostly *realistic* textures from a ~200-material library and did not
  demonstrate precision tasks.
- **Photorealism is unnecessary.** Textures are drawn from three trivially
  generated families: a random RGB value, a gradient between two random RGB
  values, a checker pattern between two random RGB values. The renderer is
  MuJoCo's built-in, explicitly "not intended to be photo-realistic."
- **ImageNet pretraining turns out not to be essential** — the authors state
  their hypothesis that it would be "proved to be false." With enough data,
  random initialization matches it; the *best* detector per object was often
  randomly initialized. Pretraining only helps in the low-data regime.
- **Precision, not just classification.** 1.5 cm accuracy is comparable to
  traditional monocular pose-estimation pipelines that use higher-resolution
  images and hand-engineered features.

## Methods

- **Task.** `d(I₀) → {(xᵢ, yᵢ, zᵢ)}` — map one monocular RGB frame to Cartesian
  object-center coordinates. Table height is *fixed* in sim, so this is
  effectively 2D pose estimation with a known plane. No orientation.
- **What is randomized, per training sample:** number and shape of distractors
  (0–10); position and texture of every object; textures of table, floor,
  skybox, and robot; camera position (uniform in a 10×5×10 cm box), orientation
  (±0.1 rad, aim-point computed analytically), and field of view (±5%); number,
  position, orientation, and specular properties of lights; type and amount of
  image noise.
- **Deliberate omission:** object color is *not* available to the detector at
  training time — only size and shape. Colors are drawn uniformly at random, so
  the network cannot use color as a shortcut. This is the design choice that
  makes the "detect a target among same-colored distractors" result meaningful.
- **Model.** Modified VGG-16: standard conv stack, smaller FC layers (256, 64),
  no dropout, 224×224 input. L2 loss on coordinates, Adam at **1e-4** — the
  authors note the standard 1e-3 falls into a local optimum that maps every
  object to the table center.
- **Evaluation.** 480 real webcam images, 8 geometric objects × 60 images (20
  object-only, 20 with distractors, 20 with partial occlusion), 70–105 cm from a
  fixed camera. Lighting and surrounding scene *not* controlled.

## Results

- **Localization:** ~1.3–1.5 cm mean error object-only; degrades gracefully to
  ~1.8–2.4 cm with distractors and occlusions. Simulated-domain error is
  0.3–0.5 cm, so the models are still overfitting sim — the gap is real but
  bounded.
- **Ablation — texture count is the dominant factor.** With training images
  fixed at 10,000, performance degrades *significantly* below **1,000 unique
  texturizations**. At 1,000 textures, using 10,000 images performs comparably
  to using 1,000 — i.e. **in the low-data regime, texture randomization matters
  more than object-position randomization.**
- **Ablation — distractors are critical, and only for distractor robustness.**
  Removing distractors from training: object-only error barely moves
  (1.3 → 1.5 cm) but distractor error explodes **1.8 → 7.2 cm** and occlusion
  error **2.4 → 7.4 cm**. Clean 4× failure, precisely localized.
- **Ablation — camera randomization gives a consistent but modest gain**
  (1.3 → 2.0 cm object-only when removed). Enough to avoid calibration, not
  transformative.
- **Ablation — image noise is negligible** for accuracy, though the authors keep
  it because it improves convergence and reduces local-minimum susceptibility.
- **Sample count:** usable transfer from ~5,000 samples (pretrained), improving
  up to ~50,000.
- **Robotics:** 38/40 successful detect-and-grasp trials on a Fetch robot in
  increasingly cluttered scenes, including distractors at orientations never
  seen in training. A YCB Spam can detector trained against *geometric*
  distractors generalized to unseen *food-item* distractors: 9/10 grasps.

## Critique / open questions

- **The task is object localization, not control.** The policy is off-the-shelf
  motion planning consuming a predicted coordinate. Nothing here shows that
  domain randomization transfers a *learned closed-loop policy* — that claim
  came from later work. The paper is honest about this framing ("a stepping
  stone").
- **Fixed table height reduces the problem to 2D.** No orientation is predicted.
  The randomization recipe is not directly transferable to full 6-DoF pose.
- **Visual randomization only.** Dynamics, friction, mass, and actuation delays
  are untouched — the reality gap addressed is purely a *rendering* gap. For
  contact-rich manipulation the dynamics gap is usually the harder one.
- **No released code**, and the era's reproducibility norms show: the ablations
  report "performance of the best network" after a small hyperparameter search
  over 2 learning rates × 3 batch sizes.
- **Randomization has a cost the paper doesn't price.** Training on maximum
  variability trades peak in-domain accuracy for robustness; the 0.3 cm sim vs
  1.5 cm real gap hints at where. Later work (automatic / structured domain
  randomization, real-to-sim [[Digital Twins]]) exists mostly because blanket
  randomization is wasteful.
- Evaluated on **geometric primitives** plus one textured YCB object. Generalization
  to cluttered, deformable, or reflective real objects is unestablished.

## Trust signals

- **Credibility:** 5 — IROS 2017, peer-reviewed; OpenAI + Pieter Abbeel's
  Berkeley group. This is the canonical, heavily-replicated origin of the
  technique and its findings have been independently reproduced across a decade
  of sim-to-real work. Its strongest credibility signal is methodological: it
  runs and reports a real ablation, including the honest reversal of its own
  stated hypothesis about ImageNet pretraining, and reports the one factor
  (image noise) that turned out not to matter. No code release, which is the
  only ding and is typical of 2017.

## Follow-up

- **Relevance:** 4 — the anchor citation for the [[Domain Randomization]]
  concept, which is already seeded in this graph from
  [[tao2024maniskill3|ManiSkill3]] but had no primary source. Compute-free to
  understand and applicable in-sim without any real hardware, which fits this
  project exactly. Not a 5 because the specific technique (visual randomization
  for coordinate regression) is a narrower slice than the concept it named.
- **Directly testable without real hardware:** train in sim with randomization,
  evaluate on a *held-out* randomization distribution (unseen textures /
  lighting / camera poses). That measures the robustness-transfer mechanism
  without a robot, and is the cheapest sim-to-real experiment available here.
- Replicate the **texture-count ablation** as the headline experiment — it is
  the paper's most load-bearing and least-cited finding (>1,000 textures needed;
  texture diversity dominates position diversity in the low-data regime), and it
  is cheap in [[GPU-Parallelized Simulation]] where randomized renders are nearly
  free.
- The **distractor ablation (1.8 → 7.2 cm)** is a clean specification of what
  randomization does *not* give you for free: robustness is only acquired along
  axes you actually randomize. Worth stating explicitly in the
  [[Domain Randomization]] concept note.
- Connects to [[tao2024maniskill3|ManiSkill3]] and [[zakka2025mujoco|MuJoCo Playground]]: both make the
  randomize-and-render loop cheap enough that the 2017 constraint (hundreds of
  thousands of renders) is now minutes of GPU time rather than the bottleneck.
