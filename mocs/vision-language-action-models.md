---
kind: moc
name: "Vision-Language-Action Models"
status: active
added: "2026-07-01"
concepts: [[[concepts/vision-language-action-model]], [[concepts/foundation-models-for-robotics]], [[concepts/open-x-embodiment]], [[concepts/action-tokenization]], [[concepts/fused-vision-encoder]], [[concepts/lora-fine-tuning]], [[concepts/model-quantization]], [[concepts/generalist-robot-policy]], [[concepts/cross-embodiment-transfer]], [[concepts/goal-image-conditioning]], [[concepts/action-expert]]]
tags: [moc, vision-language-action, foundation-models]
---

# Vision-Language-Action Models

Can a single pretrained model map a camera image and a natural-language
instruction straight to robot actions — and can you actually fine-tune and run
one on a single 16GB GPU? This theme is the project's reference point for
language-conditioned generalist control. It gathers the paradigm (fine-tune a
pretrained vision-language model into a policy), the mechanics that let an
unmodified LLM backbone emit continuous actions, and the memory-shrinking tricks
that make a 7B-parameter model tractable on one modest card. The anchoring paper
is [[literature/papers/kim2024openvla]] (OpenVLA), small and open enough to
serve as the hands-on instance for every concept below.

The 2026-08-20 ingest added the two ends of the size range that bracket what this
project can do: [[literature/papers/ghosh2024octo]] (Octo, 27M/93M) finetunes in
~5 h on one consumer card, while [[literature/papers/black2024pi0]] (π0, 3.3B)
runs inference in 73 ms on an RTX 4090 but cannot be trained here. Together they
turn "can a VLA run on 16 GB?" from a yes/no into a question about where between
93M and 3.3B finetuning stops fitting.

## The paradigm

- [[concepts/vision-language-action-model]] — a robot policy built by
  fine-tuning a pretrained VLM to map image + instruction to actions; OpenVLA is
  the canonical open-weights instance.
- [[concepts/foundation-models-for-robotics]] — the broader idea of a large,
  pretrained generalist prior adapted into task policies.
- [[concepts/open-x-embodiment]] — the pooled 70+ dataset, 2M+ trajectory
  multi-embodiment corpus OpenVLA is pretrained on.

- [[concepts/generalist-robot-policy]] — the broader class VLAs belong to: one
  pretrained policy finetuned to a downstream setup rather than trained fresh,
  whether or not a language model is involved.
- [[concepts/cross-embodiment-transfer]] — pooling data across robots with
  different morphologies and action spaces, which is what makes the pretraining
  corpora large enough to matter.

## How actions come out

Two independent lines of evidence now weigh against discretization for
high-frequency control: Octo's same-backbone ablation puts discretized actions at
18% against 83% for a diffusion head, and π0 argues autoregressive discretization
structurally precludes action chunking at all.

- [[concepts/action-tokenization]] — discretize each action dimension into
  vocabulary token bins so actions are ordinary next-token prediction, no
  bespoke regression head needed. The original OpenVLA/RT-2 approach, and the
  one both newer papers move away from.
- [[concepts/action-expert]] — π0's alternative: a second, separate set of
  transformer weights dedicated to state and action tokens, letting a frozen-purpose
  VLM backbone keep its semantics while a small expert learns
  [[concepts/flow-matching]] over continuous actions.
- [[concepts/fused-vision-encoder]] — combine complementary pretrained vision
  encoders into the backbone's visual front-end for richer features.

## How tasks are specified

- [[concepts/goal-image-conditioning]] — specify the task with an image of the
  desired end state rather than a sentence. Octo measured this **25% more
  successful than language conditioning** on matched tasks, and it needs no
  annotation: hindsight relabeling manufactures the supervision from unlabeled
  demonstrations.

## Fitting on modest hardware

- [[concepts/lora-fine-tuning]] — freeze the pretrained weights and train small
  low-rank adapters, keeping the trainable-parameter and optimizer footprint
  small enough to fit alongside a 7B model.
- [[concepts/model-quantization]] — lower-precision weights that shrink both
  training (as QLoRA) and serving into tight 16GB memory.

## Open thread

The open practical question is how much task performance survives the
quantize-plus-LoRA compression needed to fit OpenVLA on one card — and whether a
smaller, purpose-built diffusion policy beats a compressed generalist on a narrow
task. Octo makes that testable rather than rhetorical: at 27M/93M it is a
generalist that genuinely finetunes here, so the comparison against a
task-specific [[concepts/diffusion-policy]] can be run rather than argued.

An awkward loose end worth keeping visible: Octo reports that finetuning was
often *stronger without* the wrist camera, and that language conditioning
underperforms goal images by 25% — both uncomfortable for the language-instructed
generalist framing, and both attributed to pretraining-data composition rather
than explained.
