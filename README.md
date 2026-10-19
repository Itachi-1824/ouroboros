<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark) and (max-width: 640px)" srcset="assets/hero-m-dark.svg">
  <source media="(max-width: 640px)" srcset="assets/hero-m-light.svg">
  <source media="(prefers-color-scheme: dark)" srcset="assets/hero-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/hero-light.svg">
  <img alt="Ouroboros: a public research log" src="assets/hero-light.svg" width="100%">
</picture>

<br>
<br>

<h3>A new composite language-model architecture — built by one person on a laptop and rented cloud GPUs, one mechanism at a time, where every design choice had to survive a pre-registered ablation and every failure is kept on the record.</h3>

<br>

<picture>
  <source media="(prefers-color-scheme: dark) and (max-width: 640px)" srcset="assets/diagrams/status-m-dark.svg">
  <source media="(max-width: 640px)" srcset="assets/diagrams/status-m-light.svg">
  <source media="(prefers-color-scheme: dark)" srcset="assets/diagrams/status-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/diagrams/status-light.svg">
  <img alt="Status: active research · Validation: 1M-param prototypes + 1.5B base · Hardware: 8 GB laptop + cloud GPU · Every claim: ablation-proven · License: evaluation only" src="assets/diagrams/status-light.svg" width="100%">
</picture>

<br>
<br>

[**Progress**](#progress-update-october-2026) &nbsp;·&nbsp; [**Results**](#selected-measured-results) &nbsp;·&nbsp; [**Method**](#the-method--this-is-the-point) &nbsp;·&nbsp; [**Open questions**](#whats-proven-whats-open-and-what-failed) &nbsp;·&nbsp; [**Roadmap**](#have--doing-now--will-explore) &nbsp;·&nbsp; [**Contact**](#about--contact)

</div>

<br>

> [!IMPORTANT]
> **This repository is a public research log — not the source code.**
> The implementation, model checkpoints, training recipes, and detailed roadmap are held in a **private repository** and are not included here. What you're reading documents *what was built, how it was tested, and what the measurements showed* — including the many things that did not work. It is shared so the **method and the evidence** can be evaluated. The code is available to serious partners under separate arrangement.

<br>

<img src="assets/sections/01.svg" height="24" alt="01 · Now">

## Progress update (October 2026)

> [!NOTE]
> This update states **what** has been achieved and **what** the project is aimed at. It deliberately does not describe **how**; the implementation stays private (see above). Every number below is a measurement with its sample size and caveats, and nothing here is a claim beyond what was run.

**Where the project stands.** The work has moved from ~1M-parameter prototypes to a **1.5B-parameter block-diffusion base**. Experiments now run on a rented cloud GPU as well as the original 8&nbsp;GB laptop, which stays the target device for on-device checks.

### What the project aims at

| &emsp;&emsp;&emsp; | Aim |
|:---:|---|
| <img src="assets/icons/infinity.svg" width="36" alt=""> | **A truly windowless model.** No context window: memory, input and output are unbounded, limited by hardware rather than by a fixed design cap. |
| <img src="assets/icons/bolt.svg" width="36" alt=""> | **Speed that stays flat as history grows.** Generation speed should not degrade as the amount of history grows from thousands to hundreds of millions of tokens. |
| <img src="assets/icons/chip.svg" width="36" alt=""> | **Weights hold skills; knowledge stays external.** Reasoning, learning and tool use live in the model; bulk facts live in external memory and references that can be updated without retraining. |
| <img src="assets/icons/orbit.svg" width="36" alt=""> | **A biologically inspired, single unified system.** One model, not a pipeline of separate models. |
| <img src="assets/icons/flag.svg" width="36" alt=""> | **Long-term aspirations (not results):** a finished model that beats much larger models on matched public benchmarks, and high-speed generation on a single laptop GPU. |

### Measured results so far

<picture>
  <source media="(prefers-color-scheme: dark) and (max-width: 640px)" srcset="assets/diagrams/results-m-dark.svg">
  <source media="(max-width: 640px)" srcset="assets/diagrams/results-m-light.svg">
  <source media="(prefers-color-scheme: dark)" srcset="assets/diagrams/results-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/diagrams/results-light.svg">
  <img alt="Measured results so far: speed flat from 1K to 12M tokens of history (pilot, n=40 per cell); decode speed 74 to 167 tok/s (n=900 per arm); entity identity AUROC ~0.78 vs ~0.5 control (n=400 pairs per cell)" src="assets/diagrams/results-light.svg" width="100%">
</picture>

| Result | Measured | n and caveats |
|---|---|---|
| Speed vs history length | **Flat from 1K to 12M tokens of history**: about 200-240 tok/s for a single stream, about 550-600 tok/s with parallel generation | n=40 per cell. **Pilot, not claimable.** Known open issues remain before this can be claimed. |
| Decode speed | **74 → 167 tok/s** | n=900 per arm, single cloud GPU. Measures one decoding change, not the full system. |
| Entity identity in a frozen model | Same-entity detection across distances with no word overlap: **AUROC ~0.78** (range 0.76-0.79) vs **~0.5** for a randomly initialised control | n=400 pairs per cell, real text. Diagnostic only, on the unmodified model. A synthetic source gave the control spurious signal, so it is given no weight. |

These are early, partly pilot-level measurements. They show the direction is physically possible; they are not yet end-to-end claims about a finished model.

### How results are kept honest

| &emsp;&emsp;&emsp; | Rule |
|:---:|---|
| <img src="assets/icons/lock.svg" width="36" alt=""> | **Pre-registered kill rules.** The pass/fail bar is written before the run; failures are documented and cut. |
| <img src="assets/icons/scale.svg" width="36" alt=""> | **Random baselines and fair, tuned controls** on every comparison, with sample sizes of a few hundred or more before anything is believed. |
| <img src="assets/icons/search.svg" width="36" alt=""> | **A corrected false win, on the record.** An under-tuned control initially inflated a result; the claim was re-measured under fairer tuning and a harder setup before it was trusted. |
| <img src="assets/icons/shield.svg" width="36" alt=""> | **Public benchmarks are never trained on.** They are held-out evaluation only, run once the system is built. |

### Next milestones

1. Settle the remaining speed defects and move the flat-speed result from pilot to a claimable, larger-n measurement.
2. Run the public long-context and memory benchmark baselines on the 1.5B base (held-out only).
3. Verify that the windowless system loses nothing against the unmodified base model before any further training.
4. Then general training, reasoning-focused training, and the remaining capabilities, in that order, each gated on the previous stage.

<br>

<img src="assets/sections/02.svg" height="24" alt="02 · Overview">

## What this is

Most language models share one blueprint: read left-to-right, predict one token at a time, never revise, and stop learning when training ends. **Ouroboros is a different architecture.** It began from a *block-diffusion* foundation — a model that drafts and revises whole spans rather than emitting one token at a time — and is being extended into a **composite design that absorbs the strongest, individually-proven ideas from across the architecture landscape** (state-space memory efficiency, adaptive-depth reasoning, retrieval-augmented recall, continual-learning consolidation), each folded in only after it earns its place on measured evidence.

The result is a compact model designed to:

| &emsp;&emsp;&emsp; | Capability | What it means |
|:---:|---|---|
| <img src="assets/icons/revise.svg" width="36" alt=""> | **Draft & revise** | writes a whole reply at once, then refines it — a bidirectional design that *can* revise an earlier span rather than only appending, which a left-to-right model structurally cannot (demonstrated at prototype scale; carrying it to a larger base is in-progress work) |
| <img src="assets/icons/layers.svg" width="36" alt=""> | **Remember in layers** | short-term context → per-conversation memory → compressed gist → a large lookup store |
| <img src="assets/icons/reset.svg" width="36" alt=""> | **Forget on purpose** | a learned gate clears stale session state on reset while keeping fresh state intact |
| <img src="assets/icons/gauge.svg" width="36" alt=""> | **Know when it doesn't know** | one calibrated confidence signal drives abstention — it says "I don't know" instead of guessing |
| <img src="assets/icons/effort.svg" width="36" alt=""> | **Scale effort to difficulty** | spends more compute on harder problems, automatically |
| <img src="assets/icons/moon.svg" width="36" alt=""> | **Consolidate offline** | facts learned quickly at runtime are carried into the slow weights via a replay step |

<br>

<img src="assets/sections/03.svg" height="24" alt="03 · Approach">

## Why this approach

**The goal.** Build a small model that punches far above its parameter count — one that reasons, follows instructions, uses tools, and keeps learning — by giving it *capabilities the standard recipe can't*, rather than by simply making it bigger. The bet is that careful architecture and a rigorous, one-mechanism-at-a-time build can close much of the gap that the field currently closes only with scale.

**Why a block-diffusion foundation.** A standard autoregressive model commits to each token left-to-right and can never revise it. A block-diffusion model instead drafts a whole span and refines it, with attention running in both directions — which unlocks things an autoregressive model *structurally cannot do*:

- **It can revise an earlier part of its own answer** after seeing what came later — genuine self-revision rather than re-generation (shown at prototype scale; making it hold on a larger base is exactly the kind of open question tracked below).
- **It can decide its own order of work** — commit the parts it's confident about first, resolve the hard parts with more passes.
- **It exposes a richer confidence signal** (from the refinement trajectory, not a single guess), which the calibrated "know when you don't know" component builds on.

Autoregressive left-to-right generation is a *special case* of this more general process — so the foundation gives up nothing the standard approach has, while adding a headroom it lacks.

**Why not stop there.** Block-diffusion is the starting substrate, not the destination. Other architecture families each have a genuinely proven strength — efficient long-range memory, adaptive reasoning depth, retrieval-augmented recall. Rather than pick one, Ouroboros **absorbs each proven mechanism onto the foundation**, but only after it earns its place under the same falsifiable test as everything else. The aim is a single composite architecture that combines these strengths instead of trading between them.

<br>

<img src="assets/sections/04.svg" height="24" alt="04 · Method">

## The method — this is the point

The distinguishing feature of this work is not any one mechanism. It's the **discipline that decides what survives.** Every component follows the same rules:

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/diagrams/method-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/diagrams/method-light.svg">
  <img alt="Pre-register the pass/fail bar → prove it in isolation → anti-cheat battery (leak / ablation / control) → integrate + regression-gate the whole system → beats the cheap baseline? Yes: seated. No: documented and cut, with root cause." src="assets/diagrams/method-light.svg" width="100%">
</picture>

1. **Earn your place or die.** The bar is written down *before* the run. Fail → documented and cut.
2. **Anti-cheat everything.** Surface-leak probes, token ablations, key-only probes. A result that can't survive its anti-cheat is void.
3. **One primitive at a time.** Prove solo → integrate → regression-gate → only then add the next.
4. **Non-saturating metrics.** Accuracy saturates at toy scale, so components are judged on calibration, selective prediction, hallucination rate, and recovery speed.
5. **The honesty rule.** No claim without the run output. Confounds get forensics, not hand-waving.

> **The lesson that reshaped everything — the cheap-baseline gate:** *name the trivial baseline that could pass your bar without the mechanism, and show your design beats it.* Applied consistently, it revealed that most apparent "failures" were measurement artifacts — and, just as often, that an apparent *win* collapsed once the right baseline was named:
>
> - A component that **beat its bar by a wide margin** was caught as **noise between two models that had never learned the task.**
> - A variance-reduction method looked like a **30× win**; a crash forced an accidental re-run of the identical config, and one seed swung from success to total collapse. The "30×" was a single knife-edge seed. *Measure a stability method with a statistic that would move if it worked — not a mean over three seeds.*
> - A memory mechanism the literature said *"don't bother"* worked fine — but the real blocker was a baseline nobody named: an exact-match lookup already inside the model, scoring perfectly. *Enumerate every cheap baseline, not just the obvious one.*

The failures are the most instructive part of this repository, and they're all on the record with root causes and comeback paths.

<br>

<img src="assets/sections/05.svg" height="24" alt="05 · Evidence">

## Selected measured results

> **Scale note:** the chart and the first table below are **prototype-scale** results (~1M-param models, one 8&nbsp;GB laptop GPU). The final row-set is separately labeled where it applies to a larger base. Every number is the output of a pre-registered, anti-cheated ablation.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/lifts-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/lifts-light.svg">
  <img alt="Before-and-after results: each mechanism earning its lift over its baseline" src="assets/lifts-light.svg" width="100%">
</picture>

The story these show is not a wall of perfect scores — it's the **delta**: each mechanism moving its metric decisively past the baseline that would pass *without* it.

**Prototype scale (~1M params):**

| Result | Baseline / control | With the mechanism |
|---|:---:|:---:|
| Runtime-learned facts survive a memory wipe (via "sleep" consolidation) | 0.000 | **1.000** |
| Recall of facts beyond parametric capacity (16k-fact store) | 0.101 | **1.000** (fabrication 0.000 off-coverage) |
| A binding problem, solved in the memory value | 0.438 | **0.998** (at ⅓ the storage) |
| Curiosity ≈ oracle study-selection under a fixed budget | 0.599 | **0.994** (oracle 0.995) |
| Tool-use reads the world, not memory | — | corrupt the tool output → the model follows it, **1.000** |
| Effort dial: match max-compute accuracy for less compute | full compute | **−61% compute**, equal accuracy |
| Hallucination on unanswerable questions (held across 15 integration steps) | — | **0.000** (coverage 1.000) |

**Larger-base result (a stronger 1.5B-class open model):**

| Result | Measured |
|---|---|
| A knowledge-boundary detector — in-flight, no extra training | **AUROC 1.00** on a curated known-vs-unknown evaluation set, separating what the model knows from what it doesn't |

<sub>*(Honest scope: this was measured on a controlled evaluation set; a production-grade form that runs against an arbitrary live datastore is in-progress work, not a shipped result.)*</sub>

<br>

<img src="assets/sections/06.svg" height="24" alt="06 · Ledger">

## What's proven, what's open, and what failed

An architecture you can trust is one that draws these lines clearly. The full picture — not just the wins:

### <img src="assets/icons/check.svg" width="28" align="absmiddle" alt=""> &nbsp;Proven (at prototype scale)

The results above — each a pre-registered, anti-cheated ablation on ~1M-param models. These are real and reproducible, at that scale.

### <img src="assets/icons/flask.svg" width="28" align="absmiddle" alt=""> &nbsp;Proven, but not yet at scale — the honest open questions

These are the claims still to be earned. Naming them is the point:

- **Memory at large scale is unmeasured.** The retrieval store holds exact recall up to ~170k keys today. The larger tiers (1M+) that a real "long memory" claim depends on are **pre-registered but not yet run** — until they are, the honest ceiling is 170k, not infinity.
- **A stronger base absorbs much of the prototype roster.** Porting the toy-scale components onto a capable 1.5B-class model showed that a strong base already does several of them natively — so the *added* value at that scale is smaller than the prototype work alone would suggest. This is a genuine finding that cuts against the project, and it reshaped the plan (toward training-time integration, not decode-time bolt-ons).
- **Single-pass long-form has a measured wall.** In one uninterrupted pass the model tops out around a few hundred words before its own stopping behavior kicks in; longer output currently needs a hierarchical, multi-pass scaffold rather than one continuous generation.
- **Reasoning on the larger base is still weak in places** (e.g. multi-step logic) — measured, named, and the target of the next phase, not glossed over.

### <img src="assets/icons/archive.svg" width="28" align="absmiddle" alt=""> &nbsp;Failed, and why

Pre-registered tests that *didn't* earn a seat. Each carries its root cause:

- **Recurrent (adaptive) depth** — no measurable iteration margin across two separate designs; the mechanism halts at a fixed depth rather than scaling with difficulty. Re-registered for re-test at larger scale, where the headroom may physically exist.
- **A surprise-gated memory module (Titans-style)** — couldn't fit its own study set on deterministic footing; more inner compute only overfit. Shelved intact.
- **A central / portfolio resource allocator** — a decentralized alternative won, then a follow-up proved there was *no headroom left to win at all* at this scale — even an oracle allocation tied uniform.
- **An introspective self-model** — a synthetic head scored well but did not transfer to real lifetime history; the existing per-item confidence signal already beat even a perfect-oracle version, so the mechanism was structurally dominated.
- **A self-correction / draft-repair pass** — capability-limited, not gradient-limited; the repair plateaued regardless of training budget.
- **A predictive-coding core** (tied flat), **a global decay-gate** (redundant, twice), **a dedicated verifier** (absorbed into the confidence signal).

Several of these failures produced doctrines that improved everything after them — which is the real return on keeping them on the record.

<br>

<img src="assets/sections/07.svg" height="24" alt="07 · Architecture">

## Architecture at a glance

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/diagrams/architecture-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/diagrams/architecture-light.svg">
  <img alt="Spine (block-diffusion, drafts and revises spans) feeds layered memory (context → session → gist → lookup) and functional components (confidence · effort · causal · tool-use · curiosity · consolidation); each component is proven by ablation, then integrated." src="assets/diagrams/architecture-light.svg" width="100%">
</picture>

Every component is added **one at a time**, with a regression gate at every step — so nothing degrades, everything complements, and every regression is attributable. Prototype validation ran on a single laptop GPU (RTX 5070, 8&nbsp;GB); the 1.5B-base work also uses a rented cloud GPU. Each experiment prints its own ablation table.

**The scale of the program is the point.** This isn't one clever trick — it's a **24-step, pre-registered integration ladder**: a dozen-plus distinct mechanisms designed, ablated, and composed into a *single* prototype model, with a documented failure ledger for everything that didn't make it, and the whole lineage re-run from scratch on independent hardware to check it replicates. That volume of falsifiable, regression-gated work — not any single component — is what's hard to fake, and it's the real asset. The roster is a snapshot of a process that keeps running: take one candidate, push it to its limit, stress-test it, add it only if it survives.

### On memory and context length

The layered store means context isn't capped by a fixed attention window the way it is in a standard model — the large lookup tier holds what's been seen and is queried on demand, returning exact matches when the answer is in-store and abstaining (rather than fabricating) when it isn't. **The practical ceiling is set by available memory, not by a hardcoded limit** — more RAM means more retained context, so capacity scales with the hardware it runs on rather than being fixed in the weights. At prototype scale this retrieval tier has been validated at exact recall across several orders of magnitude of store size (up to ~170k keys, with larger tiers pre-registered for measurement). It's best described as *effectively unbounded within a given hardware budget* — not literally infinite, and every scale claim is tied to a measurement.

<br>

<img src="assets/sections/08.svg" height="24" alt="08 · Roadmap">

## Have / Doing now / Will explore

Drawing the line between what's *done*, what's *in progress*, and what's a *hypothesis* is the whole discipline. So, plainly:

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/diagrams/roadmap-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/diagrams/roadmap-light.svg">
  <img alt="Have (proven at prototype scale) → Doing now (on a stronger base) → Will explore (hypotheses, not results) → Long horizon (ambition, not achievement)" src="assets/diagrams/roadmap-light.svg" width="100%">
</picture>

### <img src="assets/icons/check.svg" width="28" align="absmiddle" alt=""> &nbsp;Have (proven at prototype scale)
- A single integrated model combining a dozen-plus mechanisms — memory tiers, calibrated confidence, adaptive effort, tool-use, curiosity, offline consolidation — each earned through a pre-registered ablation.
- A 24-step integration ladder with a full failure ledger, re-run on independent hardware to confirm it replicates.
- The measured results and the honest bounds above.

### <img src="assets/icons/clock.svg" width="28" align="absmiddle" alt=""> &nbsp;Doing now (on a stronger base)
- Porting the validated components onto a capable 1.5B-class open model — which surfaced the honest finding that a strong base already does several of them natively, reshaping the plan toward **training-time integration** rather than decode-time add-ons.
- Standing up the training pipeline for the first result that is *novel and holds on a real base* — the single thing that would move this from "rigorous prototype" to "fundable system." This is the current bottleneck, and it's honest work, not a wording problem.
- Measuring, not assuming: reasoning and instruction-following on the larger base are weak in places today (named in the open questions above); closing that gap is the near-term target.

### <img src="assets/icons/compass.svg" width="28" align="absmiddle" alt=""> &nbsp;Will explore (hypotheses, not results)
These are *directions with a rationale*, explicitly not claimed as working:
- **Reasoning methods that a bidirectional model can do but a left-to-right one cannot** — e.g. revising an earlier reasoning step after seeing a later contradiction. Batteries are designed; none have returned a verdict yet.
- **Efficient serving** — a stack of individually-known techniques that, combined, should raise throughput substantially. Projected, not yet measured end-to-end.
- **Large-scale memory** — the retrieval tiers beyond the ~170k already validated.

Each of these enters the *same* ablation ladder as everything else, and will be reported here whether it works or not.

### <img src="assets/icons/server.svg" width="28" align="absmiddle" alt=""> &nbsp;What compute would unlock

The prototype work was done on a single 8&nbsp;GB laptop GPU — which is the point, but also the ceiling. A concrete, checkable first milestone: **given a single modern GPU for a few weeks, run the pre-registered training experiment on the 1.5B base and report the result — success criterion fixed in advance, published either way.** Beyond that, more compute scales the same rigorous ladder — more scale, more seeds, faster iteration — never skipping a regression gate.

### <img src="assets/icons/spark.svg" width="28" align="absmiddle" alt=""> &nbsp;The long-horizon vision

> **This section is ambition, not achievement.** Nothing below is built or measured — it's the destination the current, evidence-based work is aimed at. It's here so the *scale* of the bet is clear; every step toward it enters the same ablation ladder as everything above, and will be reported honestly.

If the composite architecture holds as it scales, the endpoint it's aimed at is a genuinely different point on the cost/capability curve:

- **A diffusion-based mixture-of-experts at the ~20–30B class** — combining this architecture's revision, memory, and confidence machinery with sparse expert routing, so that *frontier-relevant* capability is reachable at a fraction of the active compute a dense model of that size would need.
- **Driven toward extreme quantization** (sub-2-bit weights) — the research direction where a model of that class could, in principle, run on consumer and even mobile hardware rather than a datacenter.

The through-line of the whole project is the same: **capability from architecture and efficiency, not from raw scale.** The prototype work is the evidence that the method is sound; this is where the method is pointed. Stated as a goal, held to the same standard of proof as everything else.

<br>

<img src="assets/sections/09.svg" height="24" alt="09 · About">

## Status & license

**Private research, made viewable for evaluation.** The implementation, checkpoints, training recipes, and detailed roadmap are not public — they live in a private repository and are available to serious partners under separate arrangement. This log is shared so the method and the measured results can be assessed.

**All rights reserved.** No license grant; viewable for evaluation only; do not redistribute or reuse. See [`LICENSE`](LICENSE).

## About & contact

Ouroboros is the work of a solo founder, built independently, starting on a single laptop. That constraint is deliberate, and it shows in the results: an entire architecture designed, implemented, and validated across a 24-step build — on one 8&nbsp;GB consumer GPU, with every claim held to a pre-registered ablation. The efficiency of the approach *is* the thesis — that careful architecture, not raw scale, is the lever a small team can pull.

**I'm looking for compute and support to carry this validated foundation onto a larger base.** If you're evaluating this for a grant, compute allocation, or collaboration — or want to see the implementation under NDA — reach out on [X (@\_dr\_misterio\_)](https://x.com/_dr_misterio_) or open an issue here.

<br>

<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/brand/mark-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/brand/mark-light.svg">
  <img alt="Ouroboros" src="assets/brand/mark-light.svg" width="44">
</picture>

<sub>Research log current as of October 2026 · updated as results land.</sub>

</div>
