---
kind: candidates
topic: "learning-based robotics and embodied AI: vision-language-action models, diffusion/imitation policies, continuous-control RL, sim-to-real"
discovered: 2026-07-01
source: discover
n_requested: 12
n_returned: 12
---

Ranked triage across the four sub-themes of the project (1 = VLA foundation
models, 2 = diffusion/imitation policies, 3 = continuous-control RL, 4 =
sim-to-real & simulators). Balanced 3-per-theme, ordered best-first with an
eye to what a hands-on learner on a single 16 GB GPU can actually run.

## 1. Diffusion Policy: Visuomotor Policy Learning via Action Diffusion

- url: https://arxiv.org/abs/2303.04137
- type: paper
- summary: Represents a robot's visuomotor policy as a conditional denoising diffusion process over action sequences, improving average success by ~47% across 12 manipulation tasks.
- reason: The single most influential imitation-learning method of the era, with clean open code that trains and runs on a single 16 GB GPU in simulation benchmarks. (theme 2)

## 2. OpenVLA: An Open-Source Vision-Language-Action Model

- url: https://arxiv.org/abs/2406.09246
- type: paper
- summary: A 7B-parameter open-source VLA trained on 970k real robot demonstrations that outperforms larger closed models on manipulation.
- reason: The reference open VLA; LoRA/quantized fine-tuning is explicitly designed to fit consumer GPUs, making it the most hands-on entry point to VLA models. (theme 1)

## 3. ManiSkill3: GPU Parallelized Robotics Simulation and Rendering for Generalizable Embodied AI

- url: https://arxiv.org/abs/2410.00425
- type: paper
- summary: A GPU-parallelized manipulation simulator and benchmark supporting thousands of parallel environments with fast rendering for embodied-AI training.
- reason: Purpose-built for single-GPU sim-first research: massively parallel envs on one card make RL and imitation experiments tractable without a cluster. (theme 4)

## 4. Soft Actor-Critic: Off-Policy Maximum Entropy Deep RL with a Stochastic Actor

- url: https://arxiv.org/abs/1801.01290
- type: paper
- summary: An off-policy maximum-entropy actor-critic algorithm that achieves stable, sample-efficient continuous control.
- reason: The default modern continuous-control RL baseline, cheap to train on one GPU and implemented in every RL library a learner will touch. (theme 3)

## 5. Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware (ACT / ALOHA)

- url: https://arxiv.org/abs/2304.13705
- type: paper
- summary: Introduces Action Chunking with Transformers (ACT), a CVAE that predicts action sequences and learns dexterous bimanual tasks from ~10 minutes of demos.
- reason: A lightweight, foundational imitation-learning architecture whose small model trains comfortably on one GPU and underpins much later VLA action-decoding. (theme 2)

## 6. Proximal Policy Optimization Algorithms

- url: https://arxiv.org/abs/1707.06347
- type: paper
- summary: A clipped-objective on-policy policy-gradient method that balances stability and simplicity for continuous and discrete control.
- reason: The most widely used RL algorithm for sim training (Isaac/MuJoCo), simple to implement and light enough for a 16 GB GPU. (theme 3)

## 7. Octo: An Open-Source Generalist Robot Policy

- url: https://arxiv.org/abs/2405.12213
- type: paper
- summary: A transformer-based generalist policy trained on 800k trajectories, instructable via language or goal images and adaptable across nine robot platforms.
- reason: A smaller, open, easily fine-tuned generalist policy that is far more approachable on constrained hardware than billion-parameter VLAs. (theme 1)

## 8. Mastering Diverse Domains through World Models (DreamerV3)

- url: https://arxiv.org/abs/2301.04104
- type: paper
- summary: A single-configuration model-based RL agent that learns a world model and outperforms specialized methods across 150+ diverse tasks.
- reason: The reference modern model-based RL agent, sample-efficient enough to run on a single GPU and a must-know for anyone doing simulation RL. (theme 3)

## 9. MuJoCo Playground

- url: https://arxiv.org/abs/2502.08844
- type: paper
- summary: An open, GPU-accelerated (MJX/JAX) suite of MuJoCo environments and RL training pipelines for locomotion, manipulation, and sim-to-real.
- reason: JAX-based GPU simulation lets a single 16 GB card run thousands of parallel MuJoCo envs, making RL and sim-to-real experiments fast and cheap. (theme 4)

## 10. Domain Randomization for Transferring Deep Neural Networks from Simulation to the Real World

- url: https://arxiv.org/abs/1703.06907
- type: paper
- summary: Shows that randomizing simulator rendering and dynamics lets models trained purely in sim transfer to real robots without real training data.
- reason: The seminal, conceptually simple sim-to-real technique every simulation-first learner needs to understand and can apply with no extra compute. (theme 4)

## 11. π0: A Vision-Language-Action Flow Model for General Robot Control

- url: https://arxiv.org/abs/2410.24164
- type: paper
- summary: A VLA built on a pretrained vision-language backbone with a flow-matching action expert, trained across many robots for dexterous tasks like folding laundry.
- reason: Defines the current flow-matching VLA paradigm with released open weights, worth studying even though full training exceeds a single 16 GB GPU. (theme 1)

## 12. Consistency Policy: Accelerated Visuomotor Policies via Consistency Distillation

- url: https://arxiv.org/abs/2405.07503
- type: paper
- summary: Distills a diffusion policy into a consistency model that speeds inference by an order of magnitude while retaining competitive success rates.
- reason: Directly targets resource-constrained deployment, showing how to get fast policy inference on hardware that cannot run heavy diffusion sampling. (theme 2)
