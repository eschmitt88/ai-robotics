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
the techniques for crossing the reality gap. The anchoring paper is
[[literature/papers/tao2024maniskill3]] (ManiSkill3), whose GPU-parallel
simulation and rendering is the concrete substrate for every concept here.

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
  compute-cheap seminal route to transfer.
- [[concepts/digital-twins]] — high-fidelity replicas for benchmarking transfer
  without touching hardware, a higher-fidelity complement to blanket
  randomization.

## Open thread

Whether domain randomization alone is enough, or whether a digital-twin
calibration step is worth the added modeling effort, is the transfer question
this project can only settle once a policy actually reaches hardware.
