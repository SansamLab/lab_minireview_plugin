---
name: lm_flow-editor
description: Diagnoses and repairs broken sentence-to-sentence flow in scientific paragraphs using three passes: Inferential Chain Audit (do results connect through visible scientific reasoning?), Given-New cohesion analysis (do sentences connect syntactically?), and Premise-Pivot-Deduction macro-structure (both latter passes grounded in Williams, Style: Lessons in Clarity and Grace). Use when sentences feel disconnected, choppy, or hard to follow despite supporting the same controlling idea — including when results feel like a list rather than a narrative, when the logic between observations is implicit, or when it's unclear whether a result confirms or contradicts a prediction. Also use after lm_paragraph-editor when structure and unity are sound but flow still feels off. Triggers on phrases like "sentences feel disconnected," "reads like a list," "doesn't flow," "choppy," "hard to follow," "improve flow," or any request focused on how sentences or results connect rather than what they say.
---

# Flow Editor

Three-pass flow repair for scientific paragraphs. Pass 0 is a conversational sequential walkthrough — the skill works through the paragraph from start to finish, showing each sentence and each consecutive pair as quoted text, making its best guess about context and connections, and offering labeled choices. Passes 1 and 2 run after Pass 0's answers are collected.

**Pass 0 — Inferential Chain Audit (conversational).** Starting from the opening sentence and working through each consecutive pair to the end, the skill shows the actual sentence text, states its best inference about what context the reader needs and how the sentences connect, and offers multiple-choice answers for the user to confirm or correct. The user's answers build the Prediction-Result-Implication (PRI) table that informs Passes 1 and 2.

**Pass 1 — Given-New Audit.** For each sentence boundary, identify the Given (opening, familiar) and New (closing, novel) information. Check whether each sentence's Given links to the prior sentence's New. Flag broken chains. Produce a chain diagram. Repair by moving familiar material to sentence openings.

**Pass 2 — Macro-structure check.** Label each sentence Premise (P), Pivot (V), or Deduction (D). Check whether the paragraph follows a PPD arc. Flag inversions, missing pivots, or buried deductions. Propose reordering where needed.

---

## Theoretical basis

Joseph Williams, *Style: Lessons in Clarity and Grace*, Chapters 5–6. Two components of syntactic flow:

- **Given-New**: Each sentence begins with information the reader already holds (Given) and ends with new, heavy information (New). A broken Given-New chain — where the opening of S₂ does not link to the tail of S₁ — produces the "disconnected sentence" sensation even when all sentences support the same controlling idea.
- **Paragraph macro-structure**: The sequence of premises, pivots, and deductions determines whether the reader can follow the logic. The Premise-Pivot-Deduction (PPD) arc mirrors scientific reasoning: establish what is known → introduce the gap or variable → state the deduction or implication.

A third layer operates beneath both of these:

- **Prediction-Result-Implication (PRI) chain**: Every result sentence in a scientific paragraph implicitly tests a prediction, confirms or contradicts it, and carries an implication that motivates the next observation. When any of these three links is absent from the text, the reader must supply it themselves — which produces the "list of results" sensation even when Given-New chains are intact and the PPD arc is correct. A paragraph can pass all syntactic and structural checks and still fail the PRI audit.

---

## Workflow

### Pass 0 — Inferential Chain Audit (conversational)

Run this before any syntactic analysis. Work through the paragraph sequentially from start to finish. Never refer to sentences by number in prompts — always quote the actual text.

#### Format rules (apply to every Pass 0 prompt)

- **Quote the sentence text.** Show the relevant sentence or pair as a blockquote. Never use sentence numbers when addressing the user.
- **State your best inference first.** Before asking, commit to the most plausible reading. Offer it as the first option.
- **Always offer 2–4 labeled choices.** Include at least one "No additional context needed" option and one "Something else — let me describe it" option.
- **Batch efficiently.** Use `ask_user_input_v0` with up to 3 questions per call. Group consecutive pairs where the prompts are similar in type.
- **Guess scientifically.** Use knowledge of the domain, the paragraph's conclusion, and the experimental logic to make plausible inferences. Do not ask open-ended questions.

#### Step 1 — Opening sentence

Quote the first sentence of the paragraph. Ask what context the reader needs coming into it. Infer from the sentence what prior finding, model, or question this paragraph is responding to, and offer your best guesses as options.

Example question: *"What should the reader already know to understand why this paragraph is asking this question?"*

Example options (tailored to content):
- [Guessed prior context A — e.g., "A prior result established that loss of factor X increases process Y, and this paragraph identifies the mechanism"]
- [Guessed prior context B — e.g., "The canonical model predicts X, and this experiment tests whether that model holds"]
- No additional context needed — the opening is self-contained
- Something else — let me describe it

#### Step 2 — Each consecutive sentence pair

For each pair of adjacent sentences, working from the first boundary to the last:

1. Quote both sentences as blockquotes.
2. State your inferred connection type (cause-effect, contrast, elaboration, method→result, result→interpretation, chronological sequence, etc.).
3. Ask whether the stated connection is accurate and whether any context is missing that a reader would need to follow the logic.
4. For result sentences, additionally infer and ask about:
   - What was predicted or expected before this result?
   - Does the result confirm, contradict, or partially support that expectation?
   - What does this result imply — what question does it open or close for the next sentence?

Example prompt:
> *"[Sentence N text]"*
> *"[Sentence N+1 text]"*
> I'm reading the connection as [inferred type, e.g., "this result motivates the next experiment because X"]. Is there context a reader would need here?
- [Guessed context A]
- [Guessed context B]
- No additional context needed — the connection is clear as written
- Something else — let me describe it

#### Step 3 — Build the PRI table

After collecting all answers, produce the confirmed PRI table before proceeding to Pass 1:

```
[Opening sentence text]
  Prior context: [stated / inferred / supplied by user]

[Sentence N text] → [Sentence N+1 text]
  Connection type: [confirmed]
  Prediction: [stated / inferred / supplied]
  Alignment: confirms / contradicts / partial / not applicable
  Implication: [stated / inferred / supplied]
  Missing from text: [list elements] / none
```

#### Step 4 — Flag repair candidates

Any element marked "missing from text" is a candidate for a bridging phrase or connective in Pass 1. Do not add language that makes a scientific claim the original did not make — propose only minimal language (a connective, a qualifier, a brief dependent clause) that makes the existing logic visible.

---

### Before Pass 1: syntactic scan

After Pass 0, read every sentence boundary silently for two types of syntactic ambiguity. Only prompt the user if ambiguity is genuine — most boundaries will not need a prompt. Use Prompt A or B (see "Interactive prompts").

**Type 1 — Referential ambiguity.** The opening of the second sentence could plausibly refer to more than one antecedent in the prior sentence or earlier, and the repair depends on which is intended.

**Type 2 — Logical ambiguity.** The logical relationship between two adjacent sentences could plausibly be read as two or more distinct types (e.g., cause-effect vs. correlation), and the connective used in the revision depends on which is correct.

---

### Pass 1 — Given-New Audit

Run on the original paragraph text, using the confirmed PRI table from Pass 0 as context.

1. For each sentence, identify:
   - **Given**: the opening ~5 words and their referential anchor in prior context
   - **New**: the final content-bearing phrase or clause

2. For each sentence boundary (Sₙ → Sₙ₊₁), check: does Sₙ₊₁'s Given reference Sₙ's New?

3. Produce a chain diagram:
   ```
   S1  Given [___________] → New [___________]
   S2  Given [___________] ✓ links to S1 New → New [___________]
   S3  Given [___________] ✗ links to S2 Given, not New → New [___________]
   ```

4. For each broken link (✗), propose a repair:
   - **Move**: reorder the clause so familiar material opens the sentence and new/heavy information closes it.
   - **Resequence**: if Sₙ₊₁ actually belongs later in the paragraph, flag it for Pass 2.
   - **Rewrite opening**: if no existing clause can serve as Given, draft a minimal bridging phrase.
   - **PRI context**: if Pass 0 identified a missing implication or alignment at this boundary, incorporate the bridging language here rather than adding a separate sentence.

5. Apply repairs and produce the **post-Pass-1 paragraph**.

---

### Pass 2 — Macro-structure check

1. Label each sentence in the post-Pass-1 paragraph:
   - **P (Premise)**: establishes known information, background, or prior findings
   - **V (Pivot)**: introduces the gap, complication, variable, contrast, or exception
   - **D (Deduction)**: states the implication, conclusion, or consequence

   A sentence may carry more than one label if it performs a transitional role (e.g., P→V).

2. Produce an arc diagram:
   ```
   S1: P
   S2: P
   S3: V
   S4: D
   Arc: P–P–V–D ✓
   ```

3. Check for violations:
   - **Buried deduction**: D appears before V — the conclusion precedes the gap that motivates it.
   - **Missing pivot**: P...P...D with no V — the logical jump is implicit; the reader must supply the gap.
   - **Inverted opening**: V or D opens the paragraph — the reader lacks the baseline needed to evaluate the pivot or deduction.
   - **Orphaned premise**: a P sentence appears after D — reintroduces background after the conclusion, deflating it.

4. Cross-check against the PRI table. A "missing pivot" violation often corresponds to a missing Implication or Prediction in the PRI chain. If so, note the correspondence — the same repair addresses both.

5. If a violation exists, propose a resequencing. Before applying:
   - Check whether the proposed resequencing would break any Given-New chain repaired in Pass 1.
   - If it would, **surface the conflict explicitly**: state the violation, state the repair, state which Given-New link it would break, and ask the user which to prioritize (see "Interactive prompts").

6. Apply repairs and produce the **post-Pass-2 paragraph**.

---

## Interactive prompts

All prompts use `ask_user_input_v0`. Pass 0 prompts fire sequentially during the conversational walkthrough. Prompts A and B fire during the syntactic scan after Pass 0, only when genuine ambiguity exists. Prompt C fires only if needed during Pass 2.

**Universal format rule: never use sentence numbers in any prompt. Always quote the relevant sentence text.**

---

### Pass 0 prompts — opening sentence and consecutive pairs

See "Pass 0 — Inferential Chain Audit" above for full format guidance. Applied here:

**Opening sentence prompt.** Quote the first sentence. Ask what context the reader needs coming into the paragraph. State your best inference as the first option.

Example:
> *"[First sentence text]"*
> What should the reader already know to understand why this paragraph is asking this question?
- [Guessed prior context A]
- [Guessed prior context B]
- No additional context needed — the opening is self-contained
- Something else — let me describe it

**Sentence pair prompt.** Quote both sentences. State the inferred connection. Ask whether it is accurate and whether any context is missing.

Example:
> *"[Sentence N text]"*
> *"[Sentence N+1 text]"*
> I'm reading the connection as [inferred type]. Is there context a reader would need here?
- [Guessed context A]
- [Guessed context B]
- No additional context needed — the connection is clear as written
- Something else — let me describe it

Batch up to 3 questions per `ask_user_input_v0` call. Always include "No additional context needed" and "Something else" as options.

---

### Prompt A — Referential assumption check

**When it fires:** The opening of one sentence could plausibly refer to more than one antecedent in the prior sentence or earlier, and the repair depends on which is intended. Fire only when ambiguity is genuine.

**Format:**
> *"[Prior sentence text]"*
> *"[Current sentence text]"*
> The opening "[exact phrase]" — I'm reading this as referring back to "[inferred referent]." Is that right?

**Options:**
- Yes — [inferred referent] is correct
- No — it refers to [let me specify]
- It's intentionally linking back to an earlier sentence

---

### Prompt B — Logical connection check

**When it fires:** The logical relationship between two adjacent sentences could be read as two or more distinct types, and the connective used in the revision depends on which is correct. Fire only when ambiguity is genuine.

**Format:**
> *"[Sentence N text]"*
> *"[Sentence N+1 text]"*
> I'm reading the connection as [inferred relationship]. Is that scientifically accurate?

**Options:**
- Yes — [inferred relationship] is correct
- No — it's [alternative, e.g., correlation not causation]
- No — it's [second alternative]
- Something else — let me describe it

**Why this matters:** "Therefore" implies mechanism. "Consistent with" implies association. "Following" implies chronology. The wrong connective misrepresents the science.

---

### Prompt C — Pass 1 / Pass 2 conflict

**When it fires:** A Pass 2 resequencing would break a Given-New chain repaired or confirmed in Pass 1.

**Format:**
> *"[Sentence that would be moved]"*
> Fixing the [violation type] requires moving this sentence to [new position], but that would break the flow link between:
> *"[sentence before the break]"* → *"[sentence after the break]"*
> Which should take priority?

**Options:**
- Fix the macro-structure — I'll accept the flow break
- Preserve the flow chain — leave the order as is
- Show me both versions and I'll decide

---

## Output

Produce two files in `/mnt/user-data/outputs/`:

1. **`flow_edited.md`** — the final revised paragraph, preceded by a 1–2 sentence summary of the most important repairs made.

2. **`flow_analysis.md`** — the full diagnostic:
   - **Pass 0:** PRI table (confirmed through conversational walkthrough), list of repair candidates with proposed bridging language.
   - **Syntactic scan:** any Prompt A or B that fired, the user's answer, and how it affected the diagnosis.
   - **Pass 1:** Given-New chain diagram (sentence numbers are fine here as internal notation), list of broken links with repair rationale, post-Pass-1 paragraph.
   - **Pass 2:** sentence-by-sentence P/V/D labels, arc diagram, violations found, cross-check against PRI table, resequencing rationale (or "no violation found"), any Pass 1–Pass 2 conflict and resolution, post-Pass-2 paragraph.

In the chat response:
- Show the final revised paragraph inline.
- Summarize the repairs in 2–3 sentences.
- Call `present_files` on both files.

---

## Relationship to lm_paragraph-editor

These skills are complementary, not redundant.

| Skill | Diagnoses |
|---|---|
| lm_paragraph-editor | Unity (does each sentence belong?), structure (topic sentence, hierarchy, clause emphasis), style (patterns, rhythm), AI tells |
| lm_flow-editor | Inferential logic (does the scientific reasoning connect?), cohesion (do sentences connect syntactically?), logical sequence (is the PPD arc visible?), causal accuracy (does the connective match the evidence?) |

A paragraph can pass all of lm_paragraph-editor's checks and still have broken flow. Run lm_paragraph-editor first when structure or unity is in doubt. Run lm_flow-editor after, or alone when the sentences are individually correct but don't read as a connected sequence.

---

## Core principles

- **Diagnose before revising.** Produce the PRI table, chain diagram, and arc labels before touching the text.
- **Preserve meaning.** Repairs are syntactic, sequential, and connective. Do not introduce scientific claims, mechanisms, or causal relationships the original did not make. PRI bridging language makes existing logic visible — it does not add new logic.
- **Given before New.** Heavy, complex, novel information belongs at the end of sentences. Light, familiar, referential information belongs at the front.
- **Quote text, not numbers.** Never refer to sentences by number when addressing the user. Always show the relevant sentence text.
- **Guess before asking.** State the most plausible inference as the first option. Do not ask open-ended questions.
- **Logical connectives must match the evidence.** A causal connective ("therefore," "thus," "as a result") implies mechanism. A correlative connective ("consistent with," "suggesting") implies association. Prompt B exists to catch mismatches before they appear in the revision.
- **Surface conflicts, don't silently resolve them.** When Pass 1 and Pass 2 repairs are in tension, show the user both options.
- **Do not over-repair.** One broken link at one boundary is one fix. Do not restructure the whole paragraph to resolve a single gap.
- **PRI and PPD cross-check.** A missing pivot in the PPD arc and a missing implication in the PRI chain are often the same problem. When they coincide, one repair addresses both — note this explicitly rather than proposing two separate fixes.
