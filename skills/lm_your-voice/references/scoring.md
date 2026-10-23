# Voice-Match Scoring Rubric

A 6-dimension scoring rubric for evaluating text against the voice profile. Each dimension produces a subscore from 0–100; the overall score is a weighted average.

The scoring is empirical where possible (corpus frequencies, sentence length, banned-word counts) and rubric-based where empirical signals are weaker (claim-driven structure, mechanistic specificity).

---

## The 6 dimensions

| # | Dimension | Weight | What it measures |
|---|---|---|---|
| 1 | Structural fit | 20% | Does the text follow problem → gap → model → test → significance? Does it open with a concrete process/problem? |
| 2 | Vocabulary fingerprint | 20% | Are banned LLM-isms absent? Are mechanistic verbs and corpus-typical nouns present? |
| 3 | Sentence rhythm | 15% | Does mean sentence length match the format target? Is there a short-claim-followed-by-longer-explanation pattern? |
| 4 | Mechanistic specificity | 20% | Are molecular actors, perturbations, readouts, and cell-cycle/developmental context named concretely? |
| 5 | Contrast and logic markers | 10% | Are contrast transitions (*although, however, whereas, in contrast*) used where the argument benefits? |
| 6 | Punctuation and formality | 15% | Em-dash count low? Commas/colons/semicolons used for explicit logic? Tone restrained, not conversational? |

Weights sum to 100%. The overall score is the weighted average.

---

## Subscore computation

### Dimension 1 — Structural fit (20%)

Score from 0–100 based on the following checklist. Award points additively, then cap at 100.

- +25 if the first sentence names a concrete biological process or document purpose (not a flourish or societal frame).
- +25 if a knowledge gap or unresolved question is stated explicitly somewhere in the text.
- +20 if a model, hypothesis, or testable claim is stated.
- +15 if an experimental test, readout, or evidence is described.
- +15 if interpretation or bounded significance is stated (not inflated impact).

**Deductions:**
- −10 if the opening uses banned scene-setting phrasing (*In today's, In an era of, Now more than ever*).
- −10 if rhetorical questions are used as the structural device.
- −10 if no claim is identifiable (text is mere topic coverage).

Floor at 0.

### Dimension 2 — Vocabulary fingerprint (20%)

Score from 0–100. Start at 100 and deduct for banned-vocabulary hits.

**Deductions per unambiguous ban-list hit (no exception applies):**
- −5 for each banned adjective (e.g., *pivotal, profound, groundbreaking*).
- −5 for each banned noun (e.g., *landscape* as metaphor, *journey, paradigm*).
- −7 for each banned verb (e.g., *delve, leverage, harness, shed light, unlock*).
- −5 for each banned adverb/transition (e.g., *moreover, accordingly, undeniably*).
- −10 for each banned phrase pattern (e.g., *In today's, It is important to note, navigate the complexities of*).

**Bonuses:**
- +5 if mechanistic verbs from the preferred list are used (e.g., *determine, test, identify, measure, regulate, suppress*). Maximum +10.
- +5 if corpus-typical mechanistic nouns appear when contextually appropriate (e.g., the recurring process and state terms of the author's own field). Maximum +10.

Floor at 0; cap at 100.

### Dimension 3 — Sentence rhythm (15%)

Score from 0–100 based on three components.

**Component A: mean sentence length match (50 points).**

Target depends on format:
- Grant prose: target 17.4 words/sentence (acceptable range 14–21).
- Manuscript prose: target 22.8 words/sentence (acceptable range 18–27).
- Methods/protocol: target 18.4 words/sentence (acceptable range 15–22).
- Letter/public-facing: target 18–22 words/sentence (more flexible).

Score 50 if within acceptable range. Linearly decrease as deviation grows. Score 0 if deviation > 50% from target.

**Component B: sentence-length variance (25 points).**

The voice uses short claim sentences mixed with longer explanatory sentences. Compute the standard deviation of sentence lengths.

- Score 25 if SD is in healthy range (roughly 5–12 words).
- Score 0 if all sentences are similar length (SD < 3, monotonous) or extremely variable (SD > 18, chaotic).

**Component C: short-claim-then-explanation pattern (25 points).**

Check whether at least one short declarative claim sentence (<13 words) is followed by a longer mechanistic explanation. The corpus shows this pattern strongly.

- Score 25 if pattern is present at least once.
- Score 0 if all sentences are similar length and no short-claim moments.

### Dimension 4 — Mechanistic specificity (20%)

Score from 0–100 based on the presence of concrete biological content.

**Checklist:**
- +20 if molecular actors are named (genes, proteins, complexes).
- +20 if a perturbation or experimental manipulation is described (depletion, mutation, inhibition, dosage change, developmental stage).
- +20 if a readout or measurement is described (a measured rate or timing, ChIP-seq, imaging, assay).
- +20 if cell-cycle, developmental, or chromatin context is specified (G1/S/G2/M; early/late embryo; chromatin state).
- +20 if the inference or interpretation is bounded (not "far-reaching" or "transformative").

**Deductions:**
- −15 if generic abstractions replace concrete content (*the complex relationship between, the multifaceted nature of, the dynamic interplay*).
- −10 if the text is about biology but names no specific actors, perturbations, or readouts.

**Context exception:** For letters, public-facing summaries, or extrapolated formats, the deduction for missing molecular actors is reduced. In a letter, score this dimension on whether the writer's scientific identity comes through (their problems, approaches, conclusions) rather than on whether genes/proteins are named.

Floor at 0; cap at 100.

### Dimension 5 — Contrast and logic markers (10%)

Score from 0–100.

**Component A: contrast markers (60 points).**

The corpus uses contrast heavily: *however* (78 in 20/22 files), *although* (100 in 18/22), *whereas* (29 in 9/22), *in contrast* (34 in 13/22). For a paragraph that draws a distinction (gap from established knowledge, alternative models, comparison of conditions):

- Score 60 if at least one contrast marker is used appropriately.
- Score 30 if a contrast is implied but not signaled with a marker.
- Score 0 if a clear contrast goes unmarked.

For paragraphs where no contrast is needed, score this component 60 by default.

**Component B: causal/logical transitions calibrated (40 points).**

The corpus uses *therefore, thus, thereby* (combined ~167 occurrences across 21/22 files) but warns against decorative stacking.

- Score 40 if transitions mark real causal inference and are not stacked.
- Score 20 if some decorative use is detected.
- Score 0 if banned adverbs (*moreover, accordingly, consequently, ultimately, undeniably*) are present without justification.

### Dimension 6 — Punctuation and formality (15%)

Score from 0–100 based on three components.

**Component A: em-dash use (40 points).**
- Score 40 if no em dashes are present.
- Score 20 if one em dash is present and appears to do work no other punctuation could.
- Score 0 if multiple em dashes are present, or if em dashes appear in grant prose at all.

**Component B: explicit-logic punctuation (30 points).**
- Score 30 if colons or semicolons are used to introduce explanations, lists, or join related clauses.
- Score 15 if the prose uses only commas and periods despite places where colon/semicolon would clarify.
- Score 0 if punctuation is haphazard or follows AI-style patterns (heavy comma splicing, dash overuse).

**Component C: tone restraint (30 points).**
- Score 30 if tone is formal, mechanistic, restrained.
- Score 15 if conversational hedges (*maybe, sort of, kind of, I guess*) appear.
- Score 0 if tone is sycophantic, anecdotal, or breezy in scientific contexts.

---

## Computing the overall score

```
overall = (dim1 × 0.20) + (dim2 × 0.20) + (dim3 × 0.15) + (dim4 × 0.20) + (dim5 × 0.10) + (dim6 × 0.15)
```

Round to the nearest whole number. Always report the overall score AND all six subscores so the user can see where the deviations are.

---

## Score interpretation

| Range | Reading |
|---|---|
| 90–100 | Strong voice match. Few or no deviations. Rewrite unlikely to improve much. |
| 75–89 | Good voice match with specific deviations. Targeted rewrite will help. |
| 60–74 | Mixed. Some voice traits present, but significant LLM-style or generic patterns. Substantial rewrite recommended. |
| 40–59 | Weak voice match. Generic scientific or AI-style prose dominates. Full rewrite needed. |
| 0–39 | Off-voice. Reads as not the author's. May be someone else's voice, or AI-generated unfiltered. |

---

## Format calibration

The dimension weights stay the same across formats, but specific targets adjust:

| Format | Sentence-length target | Mechanistic specificity expectation | Notes |
|---|---|---|---|
| Grant / research plan | 17.4 wps (14–21) | Very high | Hypothesis, perturbations, readouts expected |
| Manuscript / article | 22.8 wps (18–27) | High | Specialist terminology accepted, define less |
| Methods / protocol | 18.4 wps (15–22) | High but procedural | Lower-confidence due to sample size |
| Research document | 17.5 wps | Moderate-high | Lower confidence; defaults to grant voice |
| Recommendation letter | 18–22 wps (flexible) | Moderate | Warmth allowed; mechanistic actors optional |
| Public-facing / summary | 16–20 wps | Lower | Define jargon early; reduce density |
| Email / Slack / casual | (no corpus) | (not enforced) | Mark all output as extrapolation |

---

## Re-scoring after rewrite

After the rewrite, run the same scoring routine and report the new score alongside the original. The output should show:

```
Original score: 67/100
- Structural fit: 70
- Vocabulary: 50
- Sentence rhythm: 60
- Mechanistic specificity: 80
- Contrast/logic: 75
- Punctuation/formality: 65

After rewrite: 89/100
- Structural fit: 95
- Vocabulary: 85
- Sentence rhythm: 85
- Mechanistic specificity: 90
- Contrast/logic: 90
- Punctuation/formality: 85
```

If a rewrite scores LOWER than the original, something went wrong — either the rewrite over-corrected, lost meaning, or introduced new deviations. Diagnose and try again.
