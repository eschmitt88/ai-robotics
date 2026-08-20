---
kind: moc
name: "Simulation & Sim-to-Real"
status: active
added: "2026-07-01"
concepts: [[[concepts/gpu-parallelized-simulation]], [[concepts/parallel-rendering]], [[concepts/heterogeneous-simulation]], [[concepts/manipulation-benchmark]], [[concepts/bimanual-manipulation]], [[concepts/sim-to-real-transfer]], [[concepts/domain-randomization]], [[concepts/digital-twins]]]
tags: [moc, simulation, sim-to-real]
---

# Simulation & Sim-to-Real

This project is simulation-first for a hardware reason: a single 16GB GPU can
step thousands of environments in parallel, collapsing hours-long training runs
into minutes. That makes the simulator both the training source and the
evaluation harness — but everything learned in sim is only useful if it survives
contact with the real world. This theme spans that whole arc: the GPU-parallel
engine that makes sim-first learning feasible, the benchmark tasks it runs, and
the techniques for crossing the reality gap. Three papers anchor it:
[[literature/papers/tao2024maniskill3]] (ManiSkill3, SAPIEN/PhysX) and
[[literature/papers/zakka2025mujoco]] (MuJoCo Playground, MJX/JAX) are the two
live options for the parallel substrate, and
[[literature/papers/tobin2017domain]] (Tobin et al., IROS 2017) is the primary
source for domain randomization, the transfer technique this project can practice
entirely in simulation.

## GPU-parallel simulation — the throughput engine

- [[concepts/gpu-parallelized-simulation]] — run many environments concurrently
  on the GPU so sampling scales with GPU cores, not CPU threads; the reason
  sim-first RL is feasible on one card.
- [[concepts/parallel-rendering]] — the visual counterpart, batching image
  generation across the same parallel environments.
- [[concepts/heterogeneous-simulation]] — the parallel environments need not be
  identical, enabling per-env scene and task variety.

## Benchmarks and tasks — the proving ground

- [[concepts/manipulation-benchmark]] — a curated, reproducible suite of
  contact-rich tasks with fixed observation and evaluation protocols, so RL and
  imitation results are comparable.
- [[concepts/bimanual-manipulation]] — two-arm tasks that stress coordination
  and long-horizon planning, a demanding subclass of the benchmark.

## Crossing the reality gap

- [[concepts/sim-to-real-transfer]] — deploy a sim-trained policy on hardware
  with little or no real fine-tuning; the bridge from cheap learning to real
  robots.
- [[concepts/domain-randomization]] — randomize appearance and dynamics during
  training so the real world looks like just another training sample; the
  compute-cheap seminal route to transfer. Tobin's ablations pin down what it
  actually buys: **texture count dominates** (performance degrades below ~1,000
  unique texturizations, and matters more than object-position variety in the
  low-data regime), and robustness is acquired **only along axes you randomize**
  — dropping distractors from training left object-only error unchanged while
  quadrupling distractor error.
- [[concepts/digital-twins]] — high-fidelity replicas for benchmarking transfer
  without touching hardware, a higher-fidelity complement to blanket
  randomization.

## Choosing the substrate

ManiSkill3 and MuJoCo Playground answer the same question with different engines,
and neither benchmarks on 16 GB, so the choice is genuinely open here. The
predictive difference is structural: MJX inherits JAX's static-shape requirement,
so contact cost scales with the number of *possible* contacts rather than active
ones — which should favor Playground on locomotion and ManiSkill3 (PhysX) on
cluttered manipulation. Playground also reports that the bottleneck has already
moved: physics plus rendering account for only 9–43% of PPO training time, the
rest being gradient updates. On one card that implies more parallel environments
stop helping well before the simulator saturates.

## Open thread

Whether domain randomization alone is enough, or whether a digital-twin
calibration step is worth the added modeling effort, is the transfer question
this project can only settle once a policy actually reaches hardware. Until then
the tractable proxy is Tobin's own setup run in reverse: randomize during
training, evaluate on a *held-out* randomization distribution. That measures the
transfer mechanism with no robot involved, and GPU-parallel rendering makes the
texture-count ablation — the paper's most load-bearing and least-cited finding —
nearly free to reproduce.
