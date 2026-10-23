---
name: lm_nih-grant-writer
description: "Rewrites scientific and medical text for NIH grant applications. Primarily modeled on Atul Gawande: spare, precise, procedurally exact, no wasted motion. Mukherjee's logical argument structure is used selectively in narrative-heavy sections. Use this skill whenever the user asks to rewrite, revise, or improve text for an NIH grant, R01, K-award, T32, or any federal research proposal. Also trigger when the user says their writing sounds passive, wordy, cluttered, unclear to reviewers, or needs to convey clinical impact and innovation. Trigger on phrases like 'NIH prose,' 'grant language,' 'reviewer clarity,' 'specific aims,' 'significance section,' or 'rewrite for a grant.'"
---

# NIH Grant Writer

Rewrites scientific text in the style of Atul Gawande: spare, procedurally exact, no wasted motion. Every sentence earns its place. Reviewers skim — every sentence must reward that skim.

Mukherjee's cumulative logical architecture is applied selectively to Significance and Specific Aims, where argument-building matters. For everything else — Approach, Innovation, preliminary data — Gawande dominates: precise, sequential, technically exact.

---

## The Gawande Standard (Primary)

Gawande writes the way a surgeon operates: no decorative language, no redundant steps, no motion that doesn't advance the procedure. Applied to grant prose:

- **Each sentence has one job.** State a finding, establish a gap, describe a method, or name a consequence. Not two of these — one.
- **Verbs carry the weight.** Strong, specific verbs replace noun clusters. "VEGF-A drives angiogenesis" not "VEGF-A is involved in the process of angiogenic activation."
- **Sequence is argument.** The order of sentences makes the case. No sentence should be movable without losing meaning.
- **Clinical stakes are explicit.** Name the patients, the disease, the unmet need. Don't imply — state.
- **Numbers are specific.** "1.5 million Americans" not "millions of Americans." "38% five-year survival" not "poor prognosis."

## The Mukherjee Standard (Narrative Sections Only)

Applied to Specific Aims and Significance, where the argument must build across paragraphs:

- Each paragraph ends closer to the central question than it began.
- The gap in knowledge emerges from the logic of what is known — it is not announced, it is revealed.
- The hypothesis feels like the only reasonable conclusion from the evidence presented.

---

## Voice and Style Rules

**Active voice, always.** The investigator acts. The data show. The intervention reduces. Never: "it was found," "has been demonstrated," "was observed."

**Sentences under 20 words.** Count when uncertain. Split at conjunctions or relative clauses. One idea per sentence.

**No filler transitions.** Cut: "notably," "importantly," "interestingly," "of note," "furthermore," "in addition." Restructure the logic so transitions are unnecessary.

**No performative hedging.** Cut "may," "might," "could," "potentially" unless genuine uncertainty must be conveyed. Commit to the claim.

**No throat-clearing openers.** Cut: "It is well established that," "It has been demonstrated that," "It is important to note that." Start with the finding.

**Precise terminology throughout.** Retain all scientific and medical vocabulary. Clarity comes from structure, not simplification.

---

## Rewrite Process

### Step 1 — Diagnose

Before rewriting, identify:
- Passive constructions
- Sentences over 20 words
- Filler transitions and throat-clearing openers
- Clinical impact buried or absent
- Innovation implicit rather than stated
- Vague quantifiers ("various," "numerous," "many studies")
- Noun clusters masking weak verbs

### Step 2 — Rewrite

| Problem | Fix |
|---|---|
| Passive voice | Name the agent; make it the subject |
| Sentence >20 words | Split at conjunction or relative clause |
| "It has been shown that X" | "X [citation]." |
| "It is important to note that" | Delete; state the note directly |
| "Various studies have demonstrated" | State the finding; cite it |
| Clinical impact buried | Move to opening sentence of the paragraph |
| Innovation implicit | "This study is the first to [specific claim]." |
| Consecutive abstract sentences | Anchor with a mechanism, number, or clinical fact |
| Noun cluster | Replace with verb + concrete subject |

### Step 3 — Verify flow

Read only the first sentence of each paragraph. Each must either state the paragraph's core claim or name the problem the paragraph resolves. If it does neither, restructure.

---

## Section-Specific Guidance

### Specific Aims
- Sentence 1: disease burden or unresolved clinical problem. One sentence.
- Sentence 2: the gap — what is unknown or what has failed.
- Sentence 3: the central hypothesis, stated directly.
- Each aim: parallel structure; independently legible without context.
- Closing: expected impact on the field, 1–2 sentences.

### Significance
- Paragraph 1: establish what is known and why it matters clinically.
- Each subsequent paragraph: advance one mechanistic claim. No paragraph carries two arguments.
- Cite findings as conclusions: "VEGF-A drives angiogenesis in hypoxic tumor microenvironments [Smith, 2019]" — not "Smith et al. found that VEGF-A appeared to be involved in angiogenesis."
- Final paragraph: name the gap. Let it emerge from the argument.

### Innovation
- State what has not been done. Be specific and falsifiable.
- Never use "novel," "cutting-edge," "groundbreaking," or "transformative" as bare adjectives.
- Replace with: what exactly is new + why it was not possible before + what it now enables.

### Approach
- Present tense: "We perform," "We measure," "We analyze."
- Each aim follows this sequence: rationale → design → expected outcome → potential pitfall → alternative approach.
- Quantitative endpoints stated explicitly. Power calculations named, not gestured at.
- No vague method references: name the assay, the instrument, the software, the threshold.

---

## Output Format

1. **Rewritten text** — clean, ready to paste.
2. **Revision notes** — 3–6 bullets identifying the main transformations (passive → active, sentence splits, buried impact surfaced, etc.).

For input over 300 words, offer section-by-section work or complete the full rewrite and offer targeted revision on any paragraph.
