<picture>
  <source media="(prefers-color-scheme: dark) and (max-width: 640px)" srcset="assets/hero-m-dark.svg">
  <source media="(max-width: 640px)" srcset="assets/hero-m-light.svg">
  <source media="(prefers-color-scheme: dark)" srcset="assets/hero-dark.svg">
  <img alt="Ouroboros: a public research log." src="assets/hero-light.svg" width="100%">
</picture>

<br>

### A biologically inspired, multi-species "mutated chimera" organism, built in place on an open ~27B base model: effectively unbounded memory and context, continual learning, and speed that beats the base. Built by one person, research-first, with every claim pre-registered, labelled with its sample size, and every failure kept on the record.

<sub>**Status** active research &nbsp;·&nbsp; **Base** open ~27B model &nbsp;·&nbsp; **Evidence** n and status on every number &nbsp;·&nbsp; **License** evaluation only</sub>

[Direction](#direction) &nbsp;·&nbsp; [Progress](#progress-november-2026) &nbsp;·&nbsp; [Method](#method) &nbsp;·&nbsp; [Roadmap](#roadmap) &nbsp;·&nbsp; [Failures](#failures-on-the-record) &nbsp;·&nbsp; [Earlier work](#earlier-work-october-2026-and-before) &nbsp;·&nbsp; [Contact](#about--contact)

> **This repository is a public research log, not the source code.**
> Implementation, weights, training recipes and detailed design are held privately and are not included here. This log documents *what is being attempted, how it is tested, and what the measurements showed*, including what did not work, so the method and the evidence can be evaluated.

<br>

## Direction

Ouroboros started as a from-scratch composite language model on a single 8 GB GPU. It has since changed course: the work is now **built in place on an open ~27B base model**, treating that model as the knowledge and communication cortex of a larger organism rather than training a small model alone.

The organism is a **mutated chimera**: circuits modelled on measured brain wiring from several species, fused with engineering techniques (retrieval, graphs, exact lookup) rebuilt as neural parts, so there is one model and one set of weights rather than a pipeline of separate tools.

What it is aimed at:

- **Effectively unbounded memory and context.** Memory is built by the model itself as it reads and writes, bounded by hardware rather than by a fixed window.
- **Continual learning.** Learning from live experience without forgetting, so knowledge does not go stale.
- **Faster than the base.** At equal or better quality, the organism should out-run the unmodified base model.
- **No tradeoffs.** It must match or beat the base on every registered axis; a regression is treated as a defect, not a cost.
- **Skills in the weights, knowledge outside.** Reasoning, learning and tool use live in the model; facts live in memory and external sources.

These are goals. Nothing in this list is claimed as achieved; see [Progress](#progress-november-2026) for what has actually been measured.

<br>

## Progress (November 2026)

Every number below states its sample size and status. Development results are not claims.

| Date | Milestone | Evidence and caveats |
|---|---|---|
| Oct 2026 | **Integration contract written**: a single contract fixing where each family of mechanisms lives, so later stages fill reserved places instead of forcing rewiring | Design document; no measurement |
| 3 Nov | **Exact-memory development panel**: 35 cells, n = 300 each, on a CPU fixture; exact recall output with **0 errors** after one defect was found and fixed | One cell first showed **3/300 errors** (the reader mutated corrupt files); it was fixed and reconfirmed on fresh roots, and the original failure stays recorded. Development profile only, **not a pass** of the registered test: one seed, several registered sub-cells not yet run |
| 3 Nov | **First real-27B learning screen: running.** Cost admitted; screen cells completed at n = 300 | Public benchmark cells sit at the base model's ceiling (base 300/300 on two long-context cells; base .977 on a math cell under the publisher's protocol), so they test **non-regression** only. The learning signal comes from separate development arms, **verdict pending** |
| 3 Nov | **Honest negative: a regression logged** | With the new tissue mounted and **no learning**, one long-context cell scored 297/300 then 294/300 against the base's 300/300 (n = 300). Treated as a defect; cause **not yet attributed** |
| 3 Nov | **Honest negative: unreproducible GPU runs** | Identical code and greedy decoding gave different scores across two runs (math cell .897 vs .877; long-context 297 vs 294, n = 300). GPU forward passes are not bitwise reproducible, so comparisons are paired within one run and run-to-run variance is reported |
| 1-3 Nov | **Honest negative: our own harness was wrong** | A first base-model math score of .393 (n = 300) was a scoring-format defect in our harness, not capability; under the publisher's protocol the same model scored .977 (n = 300). The harness was fixed, and the invalid cell is not used |
| 3 Nov | **Honest negative: a planned addition was not built** | A pre-registered headroom check (n = 300) showed the proposed memory add-on had no room to help where exact lookup already decides the answer; it is deferred to cases where exact lookup cannot decide |
| 27-30 Oct | **Honest negative: re-entrant reasoning depth** | A zero-training probe made things worse (74/300 vs 89/300, exact McNemar p = 0.0015) or neutral (90 vs 89 of 300). Later fitted variants reached unseen depths on one seed, but acquisition was bimodal across seeds; not a claim |

<br>

## Method

The distinguishing feature is not a mechanism but the **discipline that decides what survives**.

<picture>
  <source media="(prefers-color-scheme: dark) and (max-width: 640px)" srcset="assets/diagrams/method-m-dark.svg">
  <source media="(max-width: 640px)" srcset="assets/diagrams/method-m-light.svg">
  <source media="(prefers-color-scheme: dark)" srcset="assets/diagrams/method-dark.svg">
  <img alt="Pre-register the pass/fail bar, prove it in isolation, run an anti-cheat battery, integrate and regression-gate the whole system. Beats the cheap baseline? Yes: seated. No: documented and cut, with root cause." src="assets/diagrams/method-light.svg" width="100%">
</picture>

1. **Research first.** Prior work, papers and official implementations are read before anything is built; nothing is re-derived that already exists.
2. **Pre-registered tests.** The bar, sample size and stopping rule are written before the run: a fixed n of at least 300, or an anytime-valid paired sequential test.
3. **Promotion is stricter.** Fresh held-out items, at least three seeds, correction for multiple arms, and a lesion that removes the effect.
4. **Baselines and controls.** A random or legal baseline and the cheapest plausible competitor are measured before any win is banked.
5. **Prove a channel is live.** A component that cannot change an output has contributed nothing, however plausible it looks.
6. **No tradeoffs against the base.** Every result is compared with the unmodified base; regressions are defects.
7. **Public benchmarks are never trained on.** They are held-out evaluation only.

> **The lesson that reshaped everything: name the trivial baseline.** Several apparent wins collapsed once the cheap baseline was run, and several apparent failures were measurement artifacts. A control described in a comment is not a control; the check itself is often the bug.

<br>

## Roadmap

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/diagrams/roadmap.svg">
  <img alt="Roadmap: Stage 1 memory, context and attention; 1.1 output; 1.2 text input; then reasoning and permanent learning, autonomous tool use, and omni input last." src="assets/diagrams/roadmap.svg" width="100%">
</picture>

- **Stage 1: memory, context, attention** (in progress). Effectively unbounded, exact, hardware-bounded memory and context, proven on real tasks before anything else advances.
- **1.1 Output.** Unbounded generation.
- **1.2 Text input.** Unbounded text input.
- **Later.** Self-built reasoning, permanent learning, autonomous tool use with verified effects.
- **Last.** Omni input (image, audio, video, 3D, screen).

Stages are not skipped; each is gated on measurement of the one before. Dates are deliberately absent.

<br>

## Failures on the record

Kept because they are the most instructive part of the log.

- **Recurrent (adaptive) depth, earlier prototypes.** No measurable iteration margin across two designs; re-registered for re-test at larger scale.
- **A surprise-gated memory module.** Could not fit its own study set; shelved.
- **A central resource allocator.** A decentralised alternative won, then a follow-up showed no headroom existed at that scale.
- **An introspective self-model.** Did not transfer to real history and was dominated by the existing confidence signal.
- **A self-correction pass, a predictive-coding core, a global decay gate, a dedicated verifier.** Capability-limited, tied, redundant, or absorbed elsewhere.
- **The November items above:** a regression with no learning, unreproducible GPU runs, a harness scoring defect, a skipped add-on, and a harmful zero-training probe.

<br>

## Earlier work (October 2026 and before)

Superseded framing, kept for provenance. The first phase built and tested mechanisms at roughly 1M parameters on a single 8 GB laptop GPU, then explored a 1.5B-class base. Selected results, all prototype scale and each a pre-registered ablation:

| Result | Baseline | With mechanism | Scope |
|---|:---:|:---:|---|
| Runtime-learned facts survive a memory wipe | 0.000 | 1.000 | ~1M params |
| Recall beyond parametric capacity (16k-fact store) | 0.101 | 1.000 | ~1M params |
| Effort dial: equal accuracy at less compute | full | -61% compute | ~1M params |
| Knowledge-boundary detector, no extra training | n/a | AUROC 1.00 | controlled set, 1.5B-class base |
| Decode speed, one decoding change | 74 tok/s | 167 tok/s | n = 900 per arm; **provisional**, raw run not archived |
| Speed vs history length, 1K to 12M tokens | n/a | flat | n = 40 per cell; **pilot, not claimable** |
| Session memory beyond the live view | 0.297 | 0.293 | n = 300; **no gain**, defect found and fixed |

These do not transfer automatically: a stronger base already does several of these things natively, which is part of why the project moved to building in place on one.

<br>

## Status & license

**Private research, made viewable for evaluation.** **All rights reserved.** No license grant; viewable for evaluation only; do not redistribute or reuse. See [`LICENSE`](LICENSE).

## About & contact

Ouroboros is the work of a solo founder, built independently. I am looking for compute and collaborators to carry the validated parts forward. For grants, compute, collaboration, or to discuss the implementation under NDA: [X (@\_dr\_misterio\_)](https://x.com/_dr_misterio_), [portfolio](https://me.itachi1824.org), [GitHub](https://github.com/Itachi-1824), or open an issue here.

<br>

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/brand/mark-dark.svg">
  <img alt="Ouroboros" src="assets/brand/mark-light.svg" width="28">
</picture>

<sub>Research log current as of 4 November 2026 · updated as results land.</sub>
