# ai-robotics

**Learning-based robotics and embodied AI — from RL/imitation control to
vision-language-action policies — studied on a single 16 GB GPU with
simulation-first, hands-on experiments.**

📂 **[Browse this repo →](https://<owner>.github.io/ai-robotics/)** —
interactive, always-live view of experiments, concepts, literature, and maps of
content. Served via GitHub Pages from `docs/index.html`; reads the live file
tree, no build step.

## What this is

A personal research base for understanding how modern AI controls robots, built
to be *learned by doing*. The through-line: how do we get from raw perception to
useful action? That spans classical RL for continuous control, imitation and
behavior cloning from demonstrations, diffusion/flow policies, and the recent
wave of large vision-language-action (VLA) foundation models (RT-2, OpenVLA,
π0, Octo). Because there is no physical robot in the loop, everything is
**simulation-first** — MuJoCo / Gymnasium, robosuite, ManiSkill — with sim-to-real
treated as its own topic rather than a requirement.

Success looks like: (1) a well-linked literature graph covering the major
threads of embodied AI; (2) a handful of runnable experiments on this box that
reproduce a core result (e.g. SAC on a MuJoCo locomotion task, a diffusion
policy on a manipulation benchmark) and let me poke at it; (3) clear notes on
what does and doesn't fit in 16 GB.

## Compute envelope

Single NVIDIA RTX 5080 (16 GB), 92 GB system RAM, ~3 TB free on the SN850X.
This bounds the experiment menu: online RL on state-based MuJoCo tasks and
small pixel tasks are comfortable; behavior cloning and diffusion policies on
modest datasets fit; full-scale VLA *training* does not, but small-model
inference and LoRA fine-tunes may. The compute envelope is itself a running
question here — see experiments for what actually fit.

## How it's organized

Plain Markdown + flat YAML frontmatter, cross-linked with `[[wikilinks]]`:

- `concepts/` / `mocs/` — atomic ideas; promoted to a map of content when ≥5 cluster.
- `literature/` — processed notes on papers, repos, posts (0–5 relevance scored).
- `experiments/YYYY-MM-DD-<slug>/` — self-contained runs (hypothesis → result, config, metrics, log).
- `raw/` — immutable source captures · `docs/decisions/` — ADRs · `_meta/` — index, log, templates.

## Local use

```sh
make env    # uv sync
make lint   # knowledge-graph / experiment health check
```

Part of a personal research framework
([claude-system](https://github.com/eschmitt88/claude-system)). See `CLAUDE.md`
for the agent-facing orientation and `~/.claude/CLAUDE.md` for the framework's
durable principles.
