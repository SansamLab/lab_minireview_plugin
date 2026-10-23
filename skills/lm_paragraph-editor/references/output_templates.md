# Output Templates

This document defines the formats for the two output files. Use the appropriate template based on the user's mode.

The skill always produces two files in `/mnt/user-data/outputs/`:

1. **`edited_paragraph.md`** — clean, immediately-usable revised paragraph (post-Pass-3)
2. **`analysis_details.md`** — full diagnostic from all three passes

---

## Template: `edited_paragraph.md`

Use this format every time, regardless of mode. Keep it short. The user should be able to glance at this file and grab the revised paragraph.

```markdown
# Edited Paragraph

## Summary of changes
[2–4 sentences. Cover the most important changes across Passes 1 (structural), 2 (stylistic), and 3 (humanizer). Be specific. Do not list every minor edit.]

## Revised paragraph
[The final paragraph after all three passes have been applied. No commentary, no markup, no inline annotations — just the clean paragraph as the user would use it.]

## Grant-mode notes (only if grant mode is active)

If grant mode is active AND flagged terms remain in the final paragraph:

```
The following flagged term(s) remain in the final paragraph. Decide whether each is unavoidable for your scientific meaning, or whether further revision is warranted:

- "[term]" in sentence [N] — [suggested alternative if available, or "no clean substitute available"]
- "[term]" in sentence [N] — [...]
```

If grant mode is active AND no flagged terms remain:

```
No flagged terms remain in the final paragraph.
```

Omit this section entirely if grant mode is inactive.
```

If only one pass ran (because the user said "analysis only" or "style only"), name the section "Proposed revision" instead of "Revised paragraph" and adjust the summary to reflect which pass ran.

---

## Template: `analysis_details.md` (default — all three passes)

```markdown
# Paragraph Analysis — Full Diagnostic

## Run configuration

- **Scientific mode:** [active / inactive] — [reason: user requested / scientific cues detected and user confirmed / no cues / user declined]
- **Grant mode:** [active / inactive] — [reason: user requested / not requested]
- **Add Voice mode:** [active / inactive]
- **Interactive prompts fired:**
  - Prompt 1 (scientific mode): [not fired / fired, user chose: Yes / No / Yes and remember]
  - Prompt 2 (controlling idea disambiguation): [not fired / fired, user chose: X]
  - Prompt 3 (subordination disambiguation): [not fired / fired N times, user chose: X for sentence N]

## Original paragraph
[Paste the user's paragraph exactly as submitted, with sentence numbers in brackets at the start of each sentence: [1], [2], [3]...]

---

# Pass 1 — Structural Analysis

## 1. Controlling idea
[State the controlling idea in one sentence.]

Simple or compound: [simple / compound]

If compound:
- Component A:
- Component B:
- Relationship: [comparison / contrast / cause-consequence / problem-solution / condition-outcome / qualification / sequence / coordination]

## 2. Controlling idea placement and phrasing
- Location: [first sentence / middle / final sentence / implied / absent / split]
- Orients the reader early: [yes / no]
- Definite claim, not just topic: [yes / no]
- Predicts the paragraph: [yes / no]
- Diagnosis: [too broad / too narrow / vague / mismatched / works]
- Suggested sharper version, if needed:

## 3. Topic sentence evaluation
- Topic sentence:
- Contains controlling idea: [yes / no]
- Works where located: [yes / no]
- Forecasts support structure: [yes / no]
- Action: [keep / revise / move / split / replace]

## 4. Sentence-by-sentence hierarchy map

| # | Sentence idea | Role | Type | Supports | Diagnosis |
|---|---|---|---|---|---|
|   |   |   |   |   |   |

Roles: TS = topic sentence; P = primary support; S = secondary support; X = extraneous; M = misplaced; U = unclear.
Types: D = detail; I = illustration/example; R = reason.

## 5. Unity tests

| # | Test sentence | Pass/fail | Diagnosis |
|---|---|---|---|
|   | [e.g., "X because Y" or "X for instance Y"] |   |   |

For failed sentences, specify the action: cut, move, promote, subordinate, or rewrite.

## 6. Extraneous or misplaced material
- Belongs:
- Extraneous:
- Misplaced:
- Needs clearer link:
- Introduces a second controlling idea:

## 7. Development method check
- Method: [detail / reason / illustration / definition / comparison / contrast / analogy / mixed]
- Fits the controlling idea: [yes / no]
- Maintained throughout: [yes / no]
- Pitfalls present:
- Fix:

## 8. Sentence order and coherence
- Current ordering principle:
- Best ordering principle:
- Maintained: [yes / no]
- POV shifts:
- Pronoun problems:
- Transition problems:
- Sentences out of order:
- Recommended order:

### Diagram
```
(TS) [Topic sentence]
(I-R/D/I) [Primary support I]
    (A-R/D/I) [Secondary support]
    (B-R/D/I) [Secondary support]
(II-R/D/I) [Primary support II]
    (A-R/D/I) [Secondary support]
```

## 9. Compound and complex sentence checks

| Sentence # | Clause/connective issue | Diagnosis | Suggested fix |
|---|---|---|---|
|   |   |   |   |

## 10. Revision plan (Pass 1)
- Keep:
- Cut:
- Move:
- Promote:
- Subordinate:
- Reorder:
- Fix POV / pronouns / transitions:
- Rewrite topic sentence:

## 11. Structurally-revised paragraph (output of Pass 1)
[The paragraph after Pass 1 has been applied. This is the input for Pass 2.]

---

# Pass 2 — Sentence Styling

## Paragraph-level diagnosis
- Controlling idea (carried forward):
- Current sentence rhythm:
- Main opportunity for improvement:

## Sentence-by-sentence analysis
For each sentence in the Pass 1 revised paragraph:

**Sentence [n]:**
- Function in the paragraph:
- Structural issue, if any:
- Pattern opportunity, if any:

## Recommended pattern changes (strongest 2–5)

**Change 1**
- Sentence #:
- Pattern used: [e.g., Pattern 1B, Pattern 12, Pattern 19]
- Why this pattern fits:
- Revised sentence:

**Change 2**
- Sentence #:
- Pattern used:
- Why this pattern fits:
- Revised sentence:

[…etc, up to 5]

## Post-Pass-2 paragraph
[The paragraph after Pass 2 has been applied. This is the input for Pass 3.]

---

# Pass 3 — Humanizer

## AI patterns detected and removed

For each pattern from `references/humanizer.md` that applies, document the instance(s) found and the fix.

| Pattern # | Pattern name | Instance found | Fix applied |
|---|---|---|---|
|   |   |   |   |

Patterns not found in this paragraph: list briefly, or note "none of the remaining patterns applied."

## First humanizer revision
[Paragraph after the first humanizer pass.]

## Self-critique

**What still makes this feel AI-generated?**
[Brief honest list of remaining tells, if any. Common late-stage tells: rhythm still too even, vocabulary still bland, structural symmetry, smoothed transitions, plausible-but-bland phrasing.]

## Second humanizer revision (after self-critique)
[Paragraph after the self-critique-driven revision. This is the final paragraph.]

## Final edited paragraph
[The final paragraph, identical to the "Revised paragraph" in `edited_paragraph.md`.]

---

# Grant mode — flagged term report (if grant mode active)

Omit this entire section if grant mode was inactive.

## Terms found in the original paragraph

| Term | Sentence | Context (brief) | Likely false positive? | Suggested action |
|---|---|---|---|---|
|   |   |   | yes / no | replace with X / keep (unavoidable) / restructure |

## Terms removed during revision

| Term | Originally in sentence | Removal occurred during | Replacement |
|---|---|---|---|
|   |   | Pass 1 / Pass 2 / Pass 3 |   |

## Terms remaining in the final paragraph

| Term | Sentence | Why kept | User decision needed? |
|---|---|---|---|
|   |   | unavoidable / user should review | yes / no |
```

---

## Template: `analysis_details.md` (short version)

Use when the user says "short version," "brief," or "quick." Replaces the default analysis template.

```markdown
# Paragraph Analysis — Short Version

## Original paragraph
[Paste with numbered sentences.]

## Controlling idea
[One sentence.]

## Topic sentence
[Identify and diagnose in 1–2 sentences.]

## Diagram
```
(TS) [Topic sentence]
(I-R/D/I) [Primary support]
    (A-R/D/I) [Secondary support]
(II-R/D/I) [Primary support]
```

## Main structural problem
[One concise paragraph identifying the dominant problem in the original.]

## Pass 1 revised paragraph
[Paragraph after structural revision.]

## Main stylistic opportunities
[Brief list of 2–3 key sentence-level patterns applied.]

## Final edited paragraph
[Paragraph after both passes.]
```

---

## Template: `analysis_details.md` (analysis only)

Use when the user says "only analyze," "just diagnose," "don't rewrite," "analysis only." Pass 2 is skipped.

Use the default `analysis_details.md` template but stop after Section 11 (Structurally-revised paragraph). Omit the entire Pass 2 section.

In `edited_paragraph.md`, use "Proposed revision" instead of "Revised paragraph" and note in the summary that this is the structural revision only, without sentence-level styling.

---

## Template: `analysis_details.md` (style only)

Use when the user says "only style," "just style," "style only," "skip the analysis." Pass 1 is skipped.

```markdown
# Paragraph Analysis — Style Only

## Original paragraph
[Paste with numbered sentences.]

## Pass 2 — Sentence Styling

[Same structure as the Pass 2 section of the default template: paragraph-level diagnosis, sentence-by-sentence analysis, recommended pattern changes, final paragraph.]
```

---

## Template: Compound controlling idea

Use when the paragraph has a compound controlling idea, or the user explicitly requests this template. Insert into Section 1 of the default `analysis_details.md` template, replacing the simple-controlling-idea content.

```markdown
## 1. Controlling idea — Compound

### Central thought
[Full central thought.]

### Components
1. [Component A]
2. [Component B]

### Relationship
[comparison / contrast / cause-consequence / problem-solution / condition-outcome / qualification / sequence / coordination]

### Component support map

**Component A**
- Major support:
  - Minor support:

**Component B**
- Major support:
  - Minor support:

### Diagnosis
Functions as one paragraph: [yes / no / should be split]

Justification:

### Revised topic sentence (if needed)
[New topic sentence.]
```

Then continue with Section 2 onward normally.

---

## Template: Coordination/subordination correction

Use when the user explicitly asks for a coordination/subordination check, or when Pass 1 identifies clause-level mismatches as the dominant problem. This template can be used standalone or inserted into Section 9 of the default template.

```markdown
# Coordination and Subordination Check

## 1. Idea list
1. [Idea]
2. [Idea]
3. [Idea]

## 2. Controlling idea
[Controlling idea.]

## 3. Idea classification

| # | Idea | Main or subordinate? | Directly supports | Test used | Diagnosis |
|---|---|---|---|---|---|
| 1 |   | Main / Subordinate | Controlling idea / Idea # | because / specifically / for instance / therefore |   |

## 4. Grouping

**Main idea I**
- Subordinate idea A:
- Subordinate idea B:

**Main idea II**
- Subordinate idea A:
- Subordinate idea B:

## 5. Revision plan
- Coordinate (equal value):
- Subordinate (unequal value):
- Move or cut:

## 6. Revised paragraph
[Revision.]
```
