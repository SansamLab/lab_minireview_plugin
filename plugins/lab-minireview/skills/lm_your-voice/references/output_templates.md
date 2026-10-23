# Output Templates — Your Voice

Two output modes:

1. **4th-pass mode** — appends sections to the lm_paragraph-editor's existing output files.
2. **Standalone mode** — produces a single `voice_check.md` file.

---

## 4th-pass mode

### Section appended to `edited_paragraph.md`

```markdown
---

## Voice match (Pass 4)

**Overall score: [N]/100**

Subscores:
- Structural fit: [N]/100
- Vocabulary fingerprint: [N]/100
- Sentence rhythm: [N]/100
- Mechanistic specificity: [N]/100
- Contrast/logic markers: [N]/100
- Punctuation/formality: [N]/100

### Top deviations

1. [Deviation with corpus evidence — e.g., "*pivotal* used in topic sentence; flagged adjective (5/22 files)"]
2. [Deviation with corpus evidence]
3. [Up to 5 total]

### Voice-refined paragraph

[The paragraph after voice-match revision. This supersedes the paragraph in the main "Revised paragraph" section above.]

**New score after voice refinement: [N]/100**
```

### Section appended to `analysis_details.md`

```markdown
---

# Pass 4 — Voice Match

## Voice-match score (input)

**Overall: [N]/100**

| Dimension | Score | Notes |
|---|---|---|
| Structural fit | [N] | [brief note on what scored well or poorly] |
| Vocabulary fingerprint | [N] | [number of banned-vocab hits; any preferred terms present] |
| Sentence rhythm | [N] | [mean sentence length, SD, target for this format] |
| Mechanistic specificity | [N] | [actors/perturbations/readouts present or missing] |
| Contrast/logic | [N] | [contrast markers used; transitions calibrated] |
| Punctuation/formality | [N] | [em-dash count, colon/semicolon use, tone] |

## Deviations found

| # | Sentence | Deviation | Corpus evidence | Suggested fix |
|---|---|---|---|---|
|   |   |   |   |   |

## Rewrite plan

- [What to change at structural level, if anything]
- [What to change at vocabulary level]
- [What to change at sentence-rhythm level]
- [What to change at mechanistic-specificity level]
- [What to change at contrast/logic level]
- [What to change at punctuation/formality level]

## Voice-refined paragraph

[The paragraph after voice-match revision.]

## Voice-match score (output)

**Overall: [N]/100** ([change: +N or -N from input])

| Dimension | Score | Change |
|---|---|---|
| Structural fit | [N] | [+/-N] |
| Vocabulary fingerprint | [N] | [+/-N] |
| Sentence rhythm | [N] | [+/-N] |
| Mechanistic specificity | [N] | [+/-N] |
| Contrast/logic | [N] | [+/-N] |
| Punctuation/formality | [N] | [+/-N] |

## Notes on this pass

[Any caveats: corpus extrapolation if non-corpus format, user-requested phrasing preserved, etc.]
```

---

## Standalone mode — `voice_check.md`

```markdown
# Voice Match — [brief title or first 5 words of text]

## Input

[The text the user provided, with sentences numbered as [1], [2], [3]...]

---

## Format detected

[Grant / Manuscript / Methods / Letter / Public-facing / Email-extrapolated / Unknown]

[If extrapolation flag is on, note: "No corpus evidence for this format. Voice traits applied with reduced confidence."]

---

## Voice-match score (input)

**Overall: [N]/100**

| Dimension | Score | Notes |
|---|---|---|
| Structural fit | [N] | [brief note] |
| Vocabulary fingerprint | [N] | [number of banned-vocab hits] |
| Sentence rhythm | [N] | [mean SL, SD, target] |
| Mechanistic specificity | [N] | [what's named, what's missing] |
| Contrast/logic | [N] | [markers used or not] |
| Punctuation/formality | [N] | [em-dash count, tone] |

[Interpretation per the score-interpretation table in `scoring.md`. E.g.: "Mixed. Some voice traits present, but significant LLM-style patterns. Substantial rewrite recommended."]

---

## Deviations

| # | Sentence | Deviation | Corpus evidence | Suggested fix |
|---|---|---|---|---|
| 1 | [N] | [e.g., "*delve* used as a verb"] | banned verb list; alternative: *examine, test, analyze* | [revised wording] |
| 2 | [N] | [e.g., "scene-setting opener"] | banned phrase pattern: *In today's...* | [revised opening] |

(List up to 10 deviations, sorted by severity. Each row should be self-explanatory.)

---

## Rewrite plan

- **Structural:** [what to change, if anything]
- **Vocabulary:** [what to change]
- **Sentence rhythm:** [what to change]
- **Mechanistic specificity:** [what to change]
- **Contrast/logic:** [what to change]
- **Punctuation/formality:** [what to change]

[Skip this section if the user asked for "diagnose only" or "score only."]

---

## Voice-refined text

[The rewritten text, preserving meaning, addressing the deviations identified above.]

[Skip this section if the user asked for "diagnose only" or "score only."]

---

## Voice-match score (after rewrite)

**Overall: [N]/100** (change: [+N or -N])

| Dimension | Score | Change |
|---|---|---|
| Structural fit | [N] | [+/-N] |
| Vocabulary fingerprint | [N] | [+/-N] |
| Sentence rhythm | [N] | [+/-N] |
| Mechanistic specificity | [N] | [+/-N] |
| Contrast/logic | [N] | [+/-N] |
| Punctuation/formality | [N] | [+/-N] |

[Skip this section if the rewrite was skipped.]

---

## Notes and limitations

- [Note any context-dependent calls. E.g., "kept '*robust*' in sentence 3 because it modified a statistical test."]
- [Note any user-requested phrasing preservation.]
- [If format is extrapolated, restate that scoring confidence is reduced.]
- [If a banned word was used in a justified exception, note which exception applies.]
```

---

## Short / diagnose-only / rewrite-only mode adjustments

When the user requests reduced output:

**"Score only" or "diagnose only":**
- Include input, format detected, voice-match score, deviations table, notes.
- Omit rewrite plan, voice-refined text, after-rewrite score.

**"Just rewrite" or "rewrite only":**
- Include input, voice-refined text, brief 1-line score comparison.
- Omit dimensional breakdown, deviations table, rewrite plan.

**"Short version":**
- Single paragraph summary at the top: overall score in/out, top 2 deviations, voice-refined text.
- Omit dimensional breakdown tables.

---

## Chat-response format

Regardless of which mode generates files, the chat response itself should:

1. State the input score: "Voice match: [N]/100."
2. Name the 1–2 most important deviations.
3. State the output score after rewrite: "After rewrite: [N]/100 ([+N])."
4. Show the voice-refined text inline.
5. Call `present_files` on the output file(s).

If the user asked for "score only," show steps 1–2 only and skip the rewrite display.
