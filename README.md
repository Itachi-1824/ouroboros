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
| 23 Nov | **Honest negative: post-hoc low-rank compression of the stored base is dead for us** | A published sub-1-bit binary low-rank factorization family was tested on the stored base, losslessly (latent plus exact remainder), on 300 sampled blocks covering every segment and projection type, ranks 2 to 32, against random-sign and float low-rank controls. Best result was **about 19% larger than the current stored form** (larger, not smaller); the exact remainder alone was already as large as the current form, past the registered kill line; **0%** of rows could be safely skipped from the latent alone. CPU diagnostic on tiles with random probes, one pass; the registered decision is "dead" for compressing the existing base. Using the idea inside newly converted tissue is untested |
| 22-23 Nov | **Honest negative: two dispatch fixes falsified on the device** | The step's dependent hand-off costs **9.55 us per instruction** when every parallel worker waits on the previous step (1.43 us when one does; n = 1,000 no-op instructions, 20 repeats, same session, off/on/off arms). A direct hand-off fix gave **9.59 us** (and made narrow chains 2.3x slower); a lighter waiting mode gave **9.48 us**, both past their pre-registered kill line of 6.0 us. The registered reading is that the cost is waiting for the slowest worker, so it is cut only by having fewer dependent steps. Both fixes are recorded as failed |
| 22 Nov | **Speed measured against the base on the same card: 5.9x slower (5.2x with an unbanked tissue switched on)** | Untouched base model, same GPU, one stream: **133.6 tokens/s = 7.48 ms per token** (one owner-approved control run). Our single-stream step: **44.3 ms per token** as shipped; 39.0 ms with a thresholded tissue on (one run, 256 emitted tokens per arm). The earlier 11.9 ms figure was an 8-wide ingest wave divided by 8, not single-stream output. A thresholded tissue saved about 14 ms per wave across 3 seeds, but its output gain is still zero, so it does not reach what the model emits and no speed is banked from it. A census of which units actually change found **50.7%** recruited (one run, n = 15,424 records) (registered bar 20%), so skipping unchanged work cannot carry the speedup; firing sets have to be learned during conversion |
| 22 Nov | **Block copy-out for recalled text: CPU result; device run not yet valid** | Releasing a run of recalled text as one block: about **2 events per answer, 0 wrong tokens on 4,500 histories**; 4 histories ended incomplete on an address miss, failing closed; our runner first miscounted these as errors and the rows were re-scored (CPU, engineering store, 3 seeds). The first device run hung; the cause was our own test harness reading the whole log back for every released unit (quadratic), not the device path. Fixed and covered by a test that fails on the old code; the device run is still owed |
| 20 Nov | **Honest negative: two "hold several candidates at once" tests failed their registered gates** | A four-state release (true / false / not established / conflict) and a signed many-candidates-in-one-population read were tested on CPU fixtures (n = 900 per cell, pooled over 3 seeds). Both **failed**: the four-state read did not beat a two-state read on the gated item class, and the signed read lost to a sign-only control at 16 candidates. One rerun of each also failed; the four-state rerun's gate was vacuous through our own control error. Kept only as logged signals; no mechanism claim |
| 18 Nov | **Exact recall inside the real 27B: first run, one seed, floor NOT passed** | Inside the running 27B step, recall returned the exact stored continuation on **300/300 cues in each of 7 stream-family cells (3 + 4 across two streams, n = 2,100), 0 wrong**; confirmed false fires were **0/24,000** (near-miss and foreign cues; model body off); eviction (n = 30 + 30) and byte custody (n = 46,514 and 323,894 tokens) held. **Registered gates not passed:** a re-ask test (299/300 rows missed the registered outcome; traced mostly to faults in our own job and gate design, plus real product defects: a few tokens lost at session boundaries, and a fixed recall-length cap that ended 2 answers early); a no-fire-while-generating test (2/300 cues fired; cause predicted, not yet confirmed; our gate text conflated two properties); an ingest-equivalence check (not observed); and speed (store phase about 83 us/token on average; whole step 11.9 ms/token, about 170x over target). One seed, shared-history positions. No promotion, no further seeds, no memory claim; fixes are being built first |
| 18 Nov | **Honest negative: the converted body was worse than the unconverted model, and the cause is our own design** | Four converted areas lost **3.08 points** on a held-out knowledge cell (n = 5,330, one seed; taught variants stayed 1.0 to 2.3 points below, n = 1,000). A trace of the record found the compact form was sized to a cost budget before any task test, and was already known to lose function in an earlier pilot (one area, one seed, n = 304 windows: an unreduced starting form passed after local fitting, while the compact form missed its tolerance by 12.3x). No unreduced arm had been allowed by the cost gate. A representation-preserving candidate, with task gates still ahead, replaces the compact default |
| 18 Nov | **Representation-preserving candidate: op-level smoke only** | Four areas, on 5 synthetic sets (n = 32 events each) and banked events (n = 1,024 per area): candidate output matched the reference within **1.46e-6** at full precision and **8.55e-3** at reduced precision (the unconverted path reached 1.40e-2; cap 2e-2); a full lesion gave exact zero and restore was bit-equal. **0 task items scored; 0/64 qualifying conversions; a host stand-in (6.60 ms for one event), not wired into the live step.** An earlier one-area stop came from noise in our own stop rule. One of three predictions failed: activity is spread too broadly for a static skipping shortcut to save more than about 1.2x. Bug-smoke only: **not a capability** |
| 18 Nov | **Teaching under the compact form, one seed: a small gain, explanation not established** | One taught arm beat another by **+0.62 points** on development items (n = 8,600; lower bound +0.38) and **+1.01** on a held-out knowledge cell (n = 5,330; lower bound +0.65), with no difference on held-out math (n = 1,319). A placement control left the score unchanged (+0.07, n = 8,600; lower bound -0.17), and serving without the extra pathway was **not run**, so why the arms differ is unproven. Held-out cells stayed below the unconverted model (-1.76 and -0.75 points on knowledge, n = 5,330). Diagnostic only; promotes nothing |
| 17 Nov | **Key-value caching removed from the live path; silent-store start line logged** | Legacy harness code and build-path remnants remain, so the registered clean-slate exit is **not** met. With the memory path muted (one seed, n = 300 probes) the body scored **6,062/14,981 tokens = 0.4046** (probe-cluster bootstrap 95% CI 0.399 to 0.411) and **0/300 exact recall**; an untrained random mount gated fully on scored 0/14,981 (saturated output, a broken-initialisation reading only). A start line, **not** a result and **not** a floor for later arms |
| 17 Nov | **Honest negative: standalone device store, first measurements (27B not loaded)** | On two streams (n = 46,514 and 323,894 tokens, one seed), recall (300/300 per family), byte custody and eviction held at op level, while a width lesion and one ingest row (79.2 us/token at n = 46,514, a compile artefact) **failed**, and foreign-cue false fires were not run. Reported speeds were parallel throughput, not serial cost (serial ingest 13.7 us/token); the flat-read claim was **false in the worst case**; and the store used **38.6x** the bytes of the text it holds (n = 323,894 tokens). Defects, not wins |
| 17 Nov | **Toy memory line closed (4 rounds)** | CPU synthetic diagnostic, 31 stream rows: stream rebuild held on **31/31** rows; recall **4,248,836/4,248,839** unique cues (0 wrong, 3 misses); near-miss false fires fell from 10,478/93,000 (11.27%) to 1/93,000 in the final variant. **No 100% claim, and several gates failed:** the size-overhead target passed on 4/31 rows, read-length flatness on 8/31 (2.1x slower from 50k to 1M tokens); three further gate misses traced to one cause. A combined verification path was derived on paper, **not run**. A sparse-identity variant won 0/48 cells and made more wrong outputs (1,356 vs 199 and 785). Toy diagnostic only; no round 5 |
| 17 Nov | **Toy logic line parked (CPU fixture)** | 444 cells, 133,200 item rows, 3 seeds. Against the toy comparison arm, error hazard was **.59 [.51, .69]** and **.82 [.79, .85]** in the two final noise regimes (n = 1,800 items per arm and regime), but a registered target against the exact reference was **missed** (upper bound 1.349 > 1.25; first errors 61 vs 33 and 1,375 vs 870 against us), and speed reached parity only. **The step could not test its own change**: replacing the component with a wrong one altered 0/1,800 item rows per regime. Admission, depth, symbol binding and fitted parameters were supplied, so this shows known-table carry under noise, not rule discovery. Not an organism result |
| 18 Nov | **Speed is an open defect** | Nothing measured is faster than the base: parity at best on CPU fixtures, and 11.9 ms/token on the live 27B step (n = 46,514 tokens; 12.1 ms/token at n = 31,060), about 170x over its per-token target. The goal stands; it is not met. *(22 Nov correction: 11.9 ms was an 8-wide ingest wave per token; single-stream output is 44.3 ms per token (39.0 ms with an unbanked tissue on), see above.)* |
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
- **A compact conversion sized by cost, not function.** The converted body was worse than the unconverted model because we sized it to a cost cap before testing tasks; the new plan starts from a representation-preserving candidate, with task gates still ahead.
- **A toy step that could not test its own change.** One logic step was built to test a component that no read consumed; replacing it with a wrong one altered 0/1,800 item rows per regime.
- **Post-hoc low-rank compression of the stored base.** Bigger, not smaller (about 19% larger than the current stored form); dead by its own kill rule.
- **Two dispatch fixes.** Both falsified on the device; the remaining cost is structural.
- **"Hold many candidates at once" reads.** Both registered tests failed; kept as signals only.
- **A device test that hung because of our harness.** Quadratic readback in our own probe code; fixed, with a test that would have caught it.
- **The November items above:** a regression with no learning, unreproducible GPU runs, a harness scoring defect, a skipped add-on, a harmful zero-training probe, and the 17-18 November negatives (compact-form task loss and unproven teaching attribution, standalone store size overhead and non-flat worst-case reads, failed 27B recall gates, speed unmet).

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

<sub>Research log current as of 23 November 2026 · updated as results land.</sub>
