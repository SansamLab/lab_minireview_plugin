---
name: lm_paragraph-editor
description: A three-pass paragraph editor. Pass 1 diagnoses structure (controlling idea, topic sentence, hierarchy, unity, coherence). Pass 2 refines sentence style (rhythm, variety, emphasis, patterns). Pass 3 removes AI-generated text patterns (em dashes, AI vocabulary, filler phrases). Optional scientific mode preserves technical hedging and precision; optional grant mode flags NIH/NSF restricted terms. Interactive prompts ask the user when scientific cues are detected, the controlling idea is ambiguous, or a subordination decision is debatable. Use when the user provides a paragraph and asks for any kind of editing, revision, analysis, tightening, humanizing, or feedback.
---

# Paragraph Editor

A three-pass paragraph editor with interactive prompts at key decision points.

- **Pass 1 — Structural analysis.** Diagnose and restructure at the level of controlling idea, topic sentence, sentence hierarchy, unity, coherence, and clause organization.
- **Pass 2 — Sentence styling.** Refine the structurally-revised paragraph at the sentence level — rhythm, variety, emphasis, verbs, sentence patterns.
- **Pass 3 — Humanizer.** Scan for and remove patterns characteristic of AI-generated text (em dashes, AI vocabulary, decorative parallelism, filler phrases, and 20+ other tells), then run a self-critique pass to catch subtler residual tells.

The default behavior is to run all three passes in sequence, with three potential pauses where the skill asks the user a question before continuing.

## When this skill activates

Activate whenever the user provides a paragraph and asks for any kind of editing, revision, analysis, diagnosis, improvement, tightening, humanizing, feedback, or rewrite — including indirect requests like "what do you think of this paragraph?" or "can this be tighter?" or "does this sound too AI?" If a paragraph appears alongside any editorial intent, run this skill.

Do not activate for:
- Multi-paragraph essays where the concern is essay-level organization rather than paragraph structure.
- Sentence-level proofreading (typos, basic grammar, punctuation correction) with no paragraph-structure or style context.
- Fiction or poetry where the principles in this skill would override the writer's voice.

## Default workflow

When the user provides a paragraph with no special instructions:

1. **Scan for scientific cues.** Before doing anything else, check the paragraph for the scientific-cue signals listed below. If cues are present, ask the user whether to apply scientific mode (Prompt 1). Wait for the answer. If the user has previously said "remember for this conversation," skip the prompt and apply their previous choice.
2. Read `references/analyzer.md`.
3. Run Pass 1 (structural analysis) up to the point of identifying the controlling idea.
4. **Check for controlling-idea ambiguity.** If there are two or more plausible readings, ask the user which one is the central claim (Prompt 2). Wait for the answer.
5. Continue Pass 1: topic sentence evaluation, hierarchy map, unity tests, extraneous-material check, development method check, sentence order and coherence, compound/complex sentence checks.
6. **Check for genuinely debatable subordination decisions.** If during the clause-level check Pass 1 identifies a subordination where the relative weight of two clauses is debatable (not obvious), ask the user (Prompt 3). Wait for the answer. If subordination decisions are all obvious, no prompt.
7. Produce the Pass 1 revision plan and structurally-revised paragraph.
8. Read `references/sentence_patterns.md`.
9. Run Pass 2 (sentence styling) on the Pass 1 revised paragraph. Identify the strongest 2–5 pattern-based changes. Apply them.
10. Read `references/humanizer.md`.
11. Run Pass 3 (humanizer) on the Pass 2 revised paragraph. Scan against all 29 patterns. Rewrite where needed. Then run the mandatory self-critique step ("What makes this still feel AI-generated?") and revise once more.
12. Read `references/output_templates.md`.
13. Generate the two output files (see "Output" below).
14. In the chat response: show the final post-Pass-3 paragraph inline, summarize the most important changes in 2–4 sentences (across all three passes), then call `present_files` on both files.

Do not show the user the intermediate Pass 1 or Pass 2 paragraphs separately — the user only needs the final result. The intermediate versions belong in the analysis file.

## Interactive prompts

The skill pauses for user input at three points. Each prompt uses `ask_user_input_v0`. After the user responds, the skill resumes where it left off.

### Prompt 1 — Scientific mode confirmation

**When it fires:** Always when scientific cues are detected (see "Scientific-cue detection" below). If no scientific cues, no prompt.

**Skip if:** the user said "remember for this conversation" earlier and chose Yes or No; the user explicitly invoked scientific mode in their request ("scientific mode," "academic mode," "technical paper," "preserve precision"); the user explicitly disabled it ("not scientific," "general prose," "ignore scientific mode").

**Question:** "This paragraph looks scientific. Apply scientific mode? It preserves technical hedging, restricts stylistic patterns that don't fit formal scientific prose, and tells the humanizer to keep technical terms repeated exactly rather than varied."

**Options:**
- Yes — apply scientific mode for this paragraph
- No — treat as general prose
- Yes, and remember for this conversation

### Prompt 2 — Controlling-idea disambiguation

**When it fires:** When Pass 1 detects more than one plausible reading of the central claim — typically when the paragraph could be read as either compound or single controlling idea, or when the main claim could plausibly be in different sentences, or when the topic sentence is implied or split.

**Skip if:** the controlling idea is clear (only one defensible reading); the paragraph is very short (1–2 sentences); the user has already specified the controlling idea in their request.

**Question:** "This paragraph has more than one plausible reading. Which is the central claim the rest of the analysis should build on?"

**Options:** Provide 2–3 candidate phrasings of the controlling idea in plain language, plus a fourth option ("Something else — let me state it"). Each candidate should be stated as the user would think of it, not in technical analyzer language.

### Prompt 3 — Subordination disambiguation (selective)

**When it fires:** When Pass 1's clause-level check identifies a sentence where Claude would normally demote one clause to subordinate position, but the relative weight of the two clauses is genuinely debatable — meaning a reasonable editor could argue either way.

**Skip if:** subordination is obvious (one clause is plainly the main idea); the sentence is fine as written; the user has asked for "minimal changes" or similar restraint.

**Question:** "I'm not sure which idea should be the main clause in sentence [N]. Which way reads right?" Show the original sentence verbatim plus 2–3 revision options where different clauses occupy the main-clause position.

**Options:** Always include the original sentence as one option ("Keep as is — both clauses equal"). Then provide 1–2 demotion options showing how the sentence would read if one clause were subordinated.

**Trigger calibration:** This prompt should fire rarely — at most once or twice per paragraph. If multiple debatable subordinations exist, batch them in a single prompt with up to three questions in one `ask_user_input_v0` call. If subordination decisions are all clear-cut, no prompt fires at all.

## Scientific-cue detection

Pass 1 looks for these signals before deciding whether to fire Prompt 1. Two or more signals = scientific cues present.

- **Technical vocabulary** — discipline-specific nouns or verbs (e.g., *ion channel, mitochondrial, eigenvalue, polymerase, p-value, stochastic*).
- **Hedging patterns** — *suggests, indicates, is consistent with, appears to, may, might, could, tends to*.
- **Citation or attribution language** — *previous studies have shown, has been demonstrated, recent work, prior research*.
- **Methods/results language** — *the data show, results indicate, was measured, was analyzed, sample size, control group*.
- **Quantitative or causal precision language** — *correlated with, associated with, mediated by, regulated by, required for, sufficient for*.
- **Gap-statement language** — *we still lack, it remains unclear, has not been well characterized, an open question*.
- **Conventional scientific passive** — *was found, has been established, is required, is observed.*

A single signal is not enough. Two or more in one paragraph triggers the scientific-cue flag.

## Modes (triggered by user phrasing)

| If the user's request includes | Behavior |
|---|---|
| (nothing special, no scientific cues) | Default workflow above (all three passes, no scientific mode) |
| (nothing special, scientific cues present) | Default workflow with Prompt 1 firing |
| "only analyze," "just diagnose," "don't rewrite," "analysis only" | Run Pass 1 only. Skip Passes 2 and 3. The edited paragraph file becomes a proposed structural revision; the analysis file contains the Pass 1 diagnostic. |
| "only style," "just style," "style only" | Skip Pass 1. Run Pass 2 directly on the user's paragraph, then Pass 3. |
| "only humanize," "just humanize," "humanizer only," "make it sound less AI" | Skip Passes 1 and 2. Run Pass 3 directly on the user's paragraph. |
| "skip the humanizer," "no humanizer" | Run Passes 1 and 2, skip Pass 3. |
| "scientific mode," "academic mode," "technical paper," "preserve precision" | Apply scientific/academic constraints in all three passes (see below). Skip Prompt 1 since it's already invoked. |
| "not scientific," "general prose," "ignore scientific mode" | Skip Prompt 1 even if cues are detected. |
| "NIH," "NSF," "grant mode," "grant application," "specific aims," "R01," "K99," "F31," "F32," "federal grant," "fundable" | Activate grant mode. Scan for restricted terms per `references/grant_restricted_terms.md`. Can be combined with scientific mode. |
| "add voice," "make it personal," "more human voice" | In Pass 3, activate the "Add Voice" section of `humanizer.md`. Default is off. |
| "short version," "brief," "quick" | Use the short analysis template from `references/output_templates.md` for the details file. The edited paragraph file is unchanged. |
| "minimal changes," "be conservative," "don't change much" | Suppress Prompt 3 entirely. Pass 1 makes only obvious changes. |
| "compound controlling idea" or the paragraph clearly has one | Use the compound controlling idea template in the analysis file. All three passes still run. |

## Scientific / academic mode

Activate when (a) the user explicitly requests it, or (b) the user confirms via Prompt 1. When active:

- **Pass 1:** Preserve technical precision. Flag causal language when evidence shows only association or illustration. Do not promote an illustration into a claim. Preserve hedging (*suggests, is consistent with, appears to*).
- **Pass 2:** Favor restraint. Prefer precise verbs (*suggests, indicates, supports, tests, defines, distinguishes, links, constrains, regulates, alters, is required for, is sufficient to*). Avoid complete inversions (Pattern 15A) and deliberate fragments (Pattern 20) unless they fit the surrounding prose naturally. Avoid Pattern 19A (short rhetorical questions) except for stating research questions. Where a transition, hedge, definition, or attribution is missing, use the plainest standard wording. Humanizer constraints win when a conventional academic phrasing would trigger a humanizer pattern.
- **Pass 3:** Keep technical hedging that scientific mode requires (do not over-cut as "excessive hedging" per Pattern 24). Keep technical terms repeated exactly rather than varied (Pattern 11 elegant variation is especially harmful in science). Do not apply the "Add Voice" section.

## Grant mode

Activate when the user invokes grant mode (see triggers above). Grant mode is **orthogonal to scientific mode** — both can be active, neither, or either alone. Most grant applications will run with both modes active.

When grant mode is active:

- **Before Pass 1:** Read `references/grant_restricted_terms.md`. Scan the original paragraph against the list. Note every match with location and brief context.
- **During Pass 2 and Pass 3:** Do not introduce any flagged term in a revision. Before applying any proposed revision, check it against the list. If a proposed revision introduces a flagged term that was not in the original, restructure to avoid it.
- **In the output:**
  - `edited_paragraph.md` gets a "Grant-mode notes" section listing any flagged terms remaining in the final paragraph, with suggested alternatives.
  - `analysis_details.md` gets a "Grant mode — flagged term report" section with three sub-tables: terms found in the original, terms removed during revision, terms remaining in the final.

Grant mode does NOT:
- Refuse to discuss or analyze the flagged terms.
- Automatically rewrite flagged terms out of the original — only normal Pass 1/2/3 revision might do that, and only if it serves the paragraph's structural or stylistic goals.
- Editorialize about the list or its origin.

The user always makes the final call on whether to keep, replace, or restructure around a flagged term. Many flagged terms are unavoidable in specific scientific contexts ("sex" in genetics, "mental health" as a field name, "Gulf of Mexico" in marine biology). The skill flags them, suggests alternatives where reasonable substitutes exist, and lets the user decide.

## Output

Always produce two files in `/mnt/user-data/outputs/`:

1. **`edited_paragraph.md`** — the final post-Pass-3 paragraph (changes already applied), preceded by a brief 1-paragraph note describing the most important changes across all three passes. No diagnostic tables. The user should be able to read this file and immediately use the revised paragraph.

2. **`analysis_details.md`** — the full diagnostic from all three passes, formatted per `references/output_templates.md`. Includes:
   - Whether scientific mode was active.
   - Any prompts that fired and the user's answers.
   - Pass 1: controlling idea, topic sentence evaluation, sentence hierarchy map, unity tests, extraneous-material list, development method check, sentence order and coherence, compound/complex sentence checks, revision plan, structurally-revised paragraph.
   - Pass 2: paragraph-level diagnosis, sentence-by-sentence analysis, recommended pattern changes with rationale, post-Pass-2 paragraph.
   - Pass 3: list of AI patterns detected and removed, self-critique notes, final paragraph.

In the chat response itself:
- Show the final post-Pass-3 paragraph inline so the user can read it without opening a file.
- Add a short summary (2–4 sentences) of the most important structural, stylistic, and humanizing changes.
- Then call `present_files` with both files.

## Core principles (apply in all three passes)

- **Preserve meaning.** Do not introduce claims, mechanisms, or evidence the original did not assert. Preserve uncertainty and hedging that is genuine.
- **Diagnose before revising.** The user wants to see structural reasoning, not just a rewrite. Pass 1 produces the diagnostic; Passes 2 and 3 produce the polish.
- **Unity before coherence.** Identify what belongs before reorganizing. A sentence on the same topic is not automatically unified — it must support the controlling idea directly or indirectly.
- **Main idea in the main clause.** Subordinate genuinely subordinate material. Flag sentences that bury the main claim in a dependent clause. When the call is debatable, ask the user (Prompt 3).
- **Stronger verbs over to-be constructions.** Avoid expletive openings (*there is, there are, it is*) when they delay the subject. But do not over-correct: simple "is" and "are" copulas are good prose; avoid the AI habit of substituting *serves as, stands as, represents* for them.
- **Patterns are tools, not decoration.** Recommend a sentence pattern only when it solves a real problem. Forced variety is a defect.
- **Avoid AI tells by default.** Em dashes, AI vocabulary, decorative parallelism, forced rule-of-three, filler abstractions, and the other patterns in `humanizer.md` are off by default. Pass 3 enforces this; Pass 2 should not introduce them in the first place.
- **Humanizer overrides conventional academic phrasing.** When a standard academic phrase triggers a pattern in `humanizer.md`, the humanizer wins; prefer the shorter, plainer wording.
- **Preserve register.** Default Pass 3 behavior is to remove AI tells without changing the register or injecting personality. The "Add Voice" section of `humanizer.md` activates only when the user explicitly requests it.
- **Prefer diagnostic feedback over praise.** Identify problems plainly. Do not soften with unnecessary compliments.
- **Ask, don't guess, when the call is genuinely ambiguous.** The three interactive prompts exist for this reason. Do not use them as crutches for obvious decisions.

## References

Load these on demand:

- `references/analyzer.md` — Pass 1 workflow: the full 11-step structural analysis, plus principles for controlling idea, topic sentence, hierarchy, unity, coherence, evidence types, coordination/subordination, and connectives. Also defines the trigger conditions for Prompts 2 and 3. **Load whenever Pass 1 runs.**
- `references/sentence_patterns.md` — Pass 2 toolkit: the full 20+ sentence pattern reference, with structures, use-when conditions, and checks for each pattern. **Load whenever Pass 2 runs.**
- `references/humanizer.md` — Pass 3 toolkit: 29 patterns characteristic of AI-generated text (based on Wikipedia's Signs of AI writing guide), plus the mandatory self-critique step and the optional Add Voice section. **Load whenever Pass 3 runs.**
- `references/grant_restricted_terms.md` — Grant mode reference: list of terms flagged in NIH and NSF grant applications, with suggested alternatives and matching rules. **Load whenever grant mode is active.**
- `references/output_templates.md` — Output formats: full analysis template, short analysis template, coordination/subordination template, and compound controlling idea template. **Load when generating the analysis_details.md file.**
