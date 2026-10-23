---
name: lm_your-voice
description: Evaluates whether writing matches the author's scientific writing voice (restrained, mechanistic, claim-driven, derived from a 22-file corpus of grants, manuscripts, preprints, and research documents) and rewrites text to match the voice better. Produces a voice-match score (0–100) with subscores by dimension, a deviation report citing corpus evidence, and a rewritten version. Auto-detects when the lm_paragraph-editor skill has just run and acts as a 4th pass on its output. Use when the user asks to check whether writing sounds like them, score a draft against their voice, rewrite text in their voice, or evaluate AI-generated drafts for voice authenticity.
---

# Your Voice — Scientific Writing Voice Matcher

A voice-matching skill that evaluates text against the author's scientific writing voice and rewrites it for better fit. The voice profile comes from corpus analysis of 22 files (grants, manuscripts, preprints, research documents) totaling about 172,000 words.

## What this skill does

When the user provides text:

1. **Score it** against the voice profile (0–100 overall, with 6 subscores).
2. **Diagnose the deviations** — what is off-voice, with corpus-frequency evidence.
3. **Rewrite the text** to score higher, preserving meaning.
4. **Report** the final score after the rewrite, so the user can see the delta.

What this skill does NOT do:

- Draft new text in the voice from a blank slate (use a separate drafting skill if needed).
- Imitate the voice for content types not in the corpus (emails, Slack, blog posts, casual messages) without flagging the extrapolation.
- Override the user's intentional stylistic choices (the user can say "keep this phrasing").

## When this skill activates

Activate when the user:

- pastes text and asks "does this sound like me?" or "is this in my voice?" or "score this against my voice"
- asks to rewrite text in their voice
- asks to evaluate an AI draft for voice authenticity
- explicitly invokes the skill: "use lm_your-voice on this," "voice check," "voice match"
- has just run the lm_paragraph-editor skill on a scientific paragraph and wants to refine for voice (see auto-detection below)

Do NOT activate when:

- The user is editing someone else's writing (theirs is not the voice to match).
- The text is clearly not scientific writing and the user hasn't explicitly asked.
- The user has said "skip voice match" or "default voice."

## Auto-detection as 4th pass

The voice skill is designed to extend the lm_paragraph-editor skill. When both are installed and the paragraph editor has just produced output, this skill can run automatically as a 4th pass on the editor's final paragraph.

**Detection signals (any one is enough):**

- The conversation just produced `edited_paragraph.md` and `analysis_details.md` files via the lm_paragraph-editor skill.
- The user's most recent message references the paragraph editor's output ("now check the voice," "now run voice match," "is this in my voice?").
- The user invokes the paragraph editor and the voice skill together ("edit this and check the voice").

**4th-pass workflow:**

1. Read the paragraph editor's final output (from `edited_paragraph.md` in the conversation's outputs).
2. Score it against the voice profile.
3. Diagnose deviations. The paragraph editor's humanizer pass will have caught the obvious AI-tells; this pass catches voice-specific deviations (your specific banned vocabulary, missing contrast markers, missing problem→gap→model→test structure, sentence-length deviations from your corpus norms).
4. Produce a voice-refined version.
5. Add a "Voice match" section to `edited_paragraph.md` showing the score, the top deviations, and the voice-refined paragraph.
6. Add a "Pass 4 — Voice match" section to `analysis_details.md`.

**Standalone workflow (no paragraph editor):**

When run alone, the skill takes the user's pasted text as input directly, scores it, diagnoses, rewrites, and produces a single `voice_check.md` output file.

## Workflow

1. Read `references/voice_profile.md` for the full voice rules.
2. Read `references/banned_vocabulary.md` for the empirical ban lists.
3. Read `references/scoring.md` for the scoring rubric and subscore weights.
4. Score the text on each of the 6 voice dimensions.
5. Compose the overall score as a weighted average (per the scoring rubric).
6. Diagnose the top 5 deviations with corpus evidence.
7. Rewrite the text to address the deviations, preserving meaning.
8. Re-score the rewritten text.
9. Read `references/output_templates.md` for the output format.
10. Produce the output file(s).

## Modes (triggered by user phrasing)

| If the user says | Behavior |
|---|---|
| (nothing special) | Default: score + diagnose + rewrite + final score |
| "just score," "score only," "don't rewrite" | Score and diagnose. Skip the rewrite. |
| "just rewrite," "don't lecture me," "rewrite only" | Rewrite. Show the score briefly but skip the deviation report. |
| "diagnose only," "what's off-voice" | Score and diagnose. Skip the rewrite. |
| "letter mode," "recommendation letter," "support letter" | Apply letter-context adjustments: allow more warmth, direct endorsement, reduced mechanistic density. |
| "manuscript mode," "paper mode" | Apply manuscript-context norms: sentence length around 22.8 words, more specialist terminology. |
| "grant mode," "NIH," "research strategy," "specific aims" | Apply grant-context norms: sentence length around 17.4 words, gap-statement structure, mechanistic verbs. |
| "public-facing," "summary," "outreach" | Apply public-context adjustments: reduce density, define jargon earlier, retain mechanistic precision. |
| "extrapolate," "email," "Slack," "casual" | Acknowledge the extrapolation (no corpus evidence for these formats). Apply core voice traits with reduced confidence. |
| "be conservative," "minimal changes" | In the rewrite, change only the items scoring lowest. Leave high-scoring sentences alone. |

## Output

When running as 4th pass (auto-detected after paragraph editor):

- Append a **Voice match** section to `edited_paragraph.md`.
- Append a **Pass 4 — Voice match** section to `analysis_details.md`.

When running standalone:

- Produce `voice_check.md` in `/mnt/user-data/outputs/`.

In the chat response (either mode):

- State the overall voice score and the 1–2 most important deviations.
- Show the voice-refined text inline (or note that no rewrite was requested).
- Call `present_files` on the output file(s).

## Core principles

- **Empirical, not prescriptive.** Score against the corpus, not against general "good writing." Cite corpus frequencies when calling out deviations.
- **Preserve meaning.** A voice rewrite is not a content rewrite. Do not add or remove scientific claims.
- **Acknowledge corpus limits.** The corpus is grant-heavy and manuscript-heavy. Other formats are extrapolation; mark them as such.
- **Respect user intent.** If the user has chosen non-voice phrasing deliberately (e.g., a particular metaphor that's banned in the corpus but they want it), acknowledge and leave it.
- **Differentiate from generic humanizing.** The paragraph editor's humanizer catches generic AI tells. This skill catches *your specific* off-voice patterns: words that appear in 0/22 of your files, missing contrast markers your corpus uses heavily, sentence-length deviations from your corpus norms.

## References

- `references/voice_profile.md` — full voice profile derived from the 22-file corpus. The empirical fingerprint: opening patterns, sentence patterns, structural patterns, vocabulary fingerprint, tone, punctuation, format-specific guidance, VOICE/PLATFORM/BORDERLINE classification.
- `references/banned_vocabulary.md` — the LLM-ism ban list (adjectives, nouns, verbs, adverbs, phrase patterns) with exceptions and corpus evidence for each.
- `references/scoring.md` — the 6-dimension scoring rubric with subscore weights, target ranges per format (grant/manuscript/methods/letter/public), and how to compute the overall score.
- `references/output_templates.md` — output formats for the 4th-pass mode (appendix to edited_paragraph.md) and the standalone mode (voice_check.md).
