# Your Voice — Scientific Writing Voice Matcher

A personal voice-matching skill for one author's scientific writing (anonymized: field-specific vocabulary and example text from the source documents were replaced with placeholders; personalize `references/voice_profile.md` with your own terms). Built from a 22-file corpus of grants, manuscripts, preprints, and research documents (about 172,000 words). Evaluates text against the voice profile, produces a 0–100 score with subscores by dimension, diagnoses deviations with corpus evidence, and rewrites text to score higher.

This skill is designed to complement the **lm_paragraph-editor** skill — when both are installed, this skill auto-detects when the paragraph editor has just run and acts as a 4th pass on its output.

---

## Quick start

1. Install (see below).
2. Paste text and ask: "Does this sound like me?" / "Score this against my voice." / "Rewrite this in my voice."
3. The skill scores the text on 6 dimensions, lists the top deviations with corpus evidence, and produces a voice-refined version.
4. Output is delivered as a file (`voice_check.md`) or appended to lm_paragraph-editor outputs when run as a 4th pass.

---

## Installation

### Claude.ai (web)

1. Go to claude.ai, click your profile icon, select **Settings**.
2. In the sidebar, **Customize** (or **Capabilities**), then **Skills**.
3. Turn on **Code execution and file creation**.
4. Click **+**, then **+ Create skill**.
5. Upload `lm_your-voice.zip`.
6. Toggle the skill **on** in your Skills list.

### Claude Code (local)

Unzip into `.claude/skills/` in your project, or `~/.claude/skills/` for global access.

```
.claude/skills/
└── lm_your-voice/
    ├── SKILL.md
    ├── README.md
    └── references/
        ├── voice_profile.md
        ├── banned_vocabulary.md
        ├── scoring.md
        └── output_templates.md
```

---

## How to trigger it

The skill responds to a range of phrasings:

- "Does this sound like me?"
- "Score this against my voice."
- "Is this in my voice?"
- "Rewrite this in my voice."
- "Voice check on this paragraph."
- "Is this AI-sounding?"
- "Make this sound like me."

You can also explicitly invoke it: "use lm_your-voice on this" or "run voice match."

When the lm_paragraph-editor skill has just produced output, this skill auto-detects and can run as a 4th pass. Trigger phrases: "now check the voice," "run voice match on that," "is the edited version in my voice?"

---

## What you get

The skill produces three things every run (unless you skip parts):

**1. A score (0–100)** on the overall voice match, with subscores on six dimensions:

- Structural fit (20%): problem → gap → model → test → significance organization
- Vocabulary fingerprint (20%): absence of banned LLM-isms; presence of mechanistic verbs and corpus-typical nouns
- Sentence rhythm (15%): mean sentence length matches the format target; healthy variance
- Mechanistic specificity (20%): named actors, perturbations, readouts, cell-cycle/developmental context
- Contrast and logic markers (10%): *however, although, whereas* used where the argument benefits
- Punctuation and formality (15%): low em-dash use, colons/semicolons for explicit logic, restrained tone

**2. A deviation report** listing each off-voice element with corpus evidence (e.g., "*pivotal* used in sentence 1 — banned adjective, weak corpus presence in 5/22 files").

**3. A rewritten version** that addresses the deviations, preserves meaning, and scores higher on the rubric. The output also reports the new score so you can see the delta.

---

## Modes

| Trigger | Effect |
|---|---|
| (default) | Score + diagnose + rewrite + re-score |
| "score only," "just score" | Score + diagnose, no rewrite |
| "diagnose only," "what's off-voice" | Score + diagnose, no rewrite |
| "just rewrite," "don't lecture me" | Brief score, full rewrite |
| "grant mode," "NIH," "research strategy" | Apply grant-prose targets: 17.4 wps, gap-statement structure |
| "manuscript mode," "paper mode" | Apply manuscript targets: 22.8 wps, specialist terminology |
| "methods mode," "protocol" | Apply methods targets: 18.4 wps, procedural specificity (low-confidence — small corpus sample) |
| "letter mode," "recommendation letter" | Allow more warmth, direct endorsement; reduce mechanistic density |
| "public-facing," "summary," "outreach" | Reduce density, define jargon earlier, retain mechanistic precision |
| "extrapolate," "email," "Slack," "casual" | Acknowledge extrapolation (no corpus evidence); apply core voice traits with reduced confidence |
| "be conservative," "minimal changes" | Only rewrite the lowest-scoring elements |

---

## File map

| File | Purpose |
|---|---|
| `SKILL.md` | Entry point. Workflow, modes, auto-detection logic for 4th-pass mode |
| `references/voice_profile.md` | Full voice profile from the 22-file corpus: opening patterns, sentence patterns, structural patterns, vocabulary, tone, punctuation, format-specific guidance, VOICE/PLATFORM/BORDERLINE table |
| `references/banned_vocabulary.md` | LLM-ism ban list (adjectives, nouns, verbs, adverbs, phrase patterns) with exceptions and corpus evidence |
| `references/scoring.md` | The 6-dimension scoring rubric with weights, computation rules, and format-specific targets |
| `references/output_templates.md` | Output formats for 4th-pass mode (appends to lm_paragraph-editor files) and standalone mode (`voice_check.md`) |

You can edit any of these files to tune the skill — for example, adding terms to the ban list as you notice new ones, or adjusting subscore weights to reflect what matters most to you in practice.

---

## How it differs from the lm_paragraph-editor's humanizer

The lm_paragraph-editor's Pass 3 (humanizer) catches generic AI tells: em dashes, "not just X, but Y" antithesis, copula avoidance, rule-of-three, filler phrases. That's a *universal* AI filter.

This skill catches *your specific* off-voice patterns:

- **Specific banned vocabulary** that goes beyond the generic AI list (e.g., *pivotal, profound, dynamic interplay, shed light, harness, leverage*).
- **Missing voice signals**: contrast markers your corpus uses heavily but the input lacks; mechanistic verbs your corpus prefers; corpus-typical nouns that fit the context.
- **Sentence-length mismatch** with your corpus norms (17.4 wps for grants, 22.8 for manuscripts).
- **Structural mismatch** with the problem → gap → model → test → significance pattern.
- **Mechanistic specificity** — whether named actors, perturbations, and readouts appear at the rate your corpus shows.

The two skills are complementary: humanizer removes universal AI tells; voice match enforces *your* writing style. When both run, you get text that is both not-AI and authentically yours.

---

## Sample test prompts

After installing, try:

**Standalone voice check on AI-generated text:**
> Score this against my voice: [paste an AI-generated paragraph about your field]

**Voice rewrite:**
> Rewrite this in my voice: [paste text]

**4th-pass after paragraph editor:**
> [paste a paragraph] Edit this, then check the voice.

**Diagnose only:**
> What's off-voice about this? [paste text]

**Letter mode:**
> Rewrite this recommendation letter in my voice. Letter mode. [paste letter]

---

## Limitations and caveats

The voice profile is genre-skewed toward grants and manuscripts. Other formats are extrapolation:

- **Emails, Slack, blog posts, casual messages** — no corpus evidence. The skill applies core voice traits (direct, restrained, specific) but flags output as extrapolated. Don't take voice rewrites for these formats as definitive.
- **Recommendation letters** — partial corpus evidence. The skill softens mechanistic density and allows more warmth, but the specific letter style is partly inferred.
- **Methods/protocol prose** — one corpus file. Targets are low-confidence.

The corpus is also several years old as captured. If your style has shifted, the skill won't know. Update the voice profile in `references/voice_profile.md` if you notice systematic mismatches.

---

## Updating the skill

If you find the skill is overreacting or underreacting on a specific dimension, edit the relevant reference file:

- To add a word to the ban list: edit `references/banned_vocabulary.md`.
- To adjust scoring weights: edit the dimension-weight table at the top of `references/scoring.md`.
- To change format-specific sentence-length targets: edit `references/scoring.md`.
- To refine the voice profile (e.g., after building a larger corpus): edit `references/voice_profile.md`.

Re-zip and re-upload to apply changes. Bump a version comment at the top of `SKILL.md` each time so you can roll back.

---

## Troubleshooting

**The skill triggers when I don't want it to.** Say "default voice" or "skip voice match" in your request.

**The score feels too harsh.** Check whether the format detected matches what you wrote (grant vs. manuscript have different sentence-length targets). If the wrong format was detected, explicitly invoke the right mode ("grant mode," "manuscript mode," etc.). If the score still feels harsh, the scoring weights in `references/scoring.md` may need calibration to match your judgment.

**The score feels too generous.** Common cause: text used a banned word that fell into an exception bucket. Check `references/banned_vocabulary.md` for the exceptions and decide whether they're too permissive.

**The 4th-pass mode doesn't trigger.** The skill detects the paragraph editor's output through the conversation context. If detection fails, explicitly say "run voice match on the edited paragraph" or paste the edited paragraph directly.

**The rewrite changed the meaning.** Voice rewriting should preserve scientific claims. If meaning shifted, the skill over-corrected. Re-run with "be conservative" or "minimal changes" mode.

**The deviations cite irrelevant corpus evidence.** Edit the voice profile or banned vocabulary to clarify exceptions. For example, if the skill keeps flagging *robust* in your statistical contexts, ensure the exception in `banned_vocabulary.md` is clear.
