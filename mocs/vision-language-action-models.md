---
kind: moc
name: "Vision-Language-Action Models"
status: active
added: "2026-07-01"
concepts: [[[concepts/vision-language-action-model]], [[concepts/foundation-models-for-robotics]], [[concepts/open-x-embodiment]], [[concepts/action-tokenization]], [[concepts/fused-vision-encoder]], [[concepts/lora-fine-tuning]], [[concepts/model-quantization]]]
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

## The paradigm

- [[concepts/vision-language-action-model]] — a robot policy built by
  fine-tuning a pretrained VLM to map image + instruction to actions; OpenVLA is
  the canonical open-weights instance.
- [[concepts/foundation-models-for-robotics]] — the broader idea of a large,
  pretrained generalist prior adapted into task policies.
- [[concepts/open-x-embodiment]] — the pooled 70+ dataset, 2M+ trajectory
  multi-embodiment corpus OpenVLA is pretrained on.

## How actions come out

- [[concepts/action-tokenization]] — discretize each action dimension into
  vocabulary token bins so actions are ordinary next-token prediction, no
  bespoke regression head needed.
- [[concepts/fused-vision-encoder]] — combine complementary pretrained vision
  encoders into the backbone's visual front-end for richer features.

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
task.
