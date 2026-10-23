---
name: lm_minireview-pipeline
description: Runs the full lab minireview pipeline end to end (triage, fetch, summarize, optional EndNote import, write the .docx, verify every claim against the literature) and coordinates the lm_* skills in order. Use when the user asks to "write a review on", "write a minireview about", "run the minireview pipeline", "do a literature review on", or gives a research topic or set of questions and expects a finished review document. Also use to resume a partly finished run or to add a section to a delivered review.
---

# Minireview Pipeline

Coordinate the bundled skills so a topic becomes a finished, verified minireview. Each stage has its own skill with detailed instructions; load each one with the Skill tool when its turn comes and follow it. This skill covers order, hand-offs, checkpoints, and lessons from earlier runs.

## Stages

| # | Skill | Output |
|---|-------|--------|
| 1 | `lm_pubmed-triage` | `02_triage/triage_<topic>_<date>.json` |
| 2 | `lm_article-fetcher` | `03_raw_fetched/PMID_<pmid>_raw.json` |
| 3 | `lm_article-summarizer` | `04_article_summaries/PMID_<pmid>_<AuthorYear>.md` |
| 4 (optional) | `lm_endnote-import` | populated `.enl` library plus a named group |
| 5 | `lm_minireview-writer` | `05_output/<Topic>_minireview.docx` |
| 6 | `lm_claim-auditor`, then `lm_skeptical-reviewer` | `06_audit/claim_audit_*.md`, `06_audit/skeptical_review_*.md` |

Work in a project folder with those numbered subfolders. Reuse any that already exist; resume at the first missing stage instead of restarting.

## Before starting

1. Restate the request as scope: organisms or systems, which questions the review must answer, what is excluded. If a question changes what to search for, ask once with AskUserQuestion. Otherwise proceed.
2. Ask whether an EndNote `.enl` library should be used. If no path is available, skip stage 4 and the EndNote checks in stage 2, and say so in the final notes.
3. Create the task list: one task per stage plus a final verification task.

## Stage guidance

**Triage.** Prefer simple natural-language PubMed queries; queries stacked with many AND terms often return zero hits. Run several searches, then score. Show the user the selected set (count, year range, any preprints) and confirm before fetching when the set is larger than about 15 papers or the topic is broad. Flag bioRxiv or other preprints in the text and bibliography.

**Fetch.** Mechanical, so it can run inline or on a cheaper model or subagent. Choose "Automated" for Research Notes mode. Record per paper whether PMC full text or only an abstract was obtained. Mark abstract-only papers with a † in the bibliography. Do not stop for each paywalled paper; collect them into a gap list instead.

**Summarize.** This stage does the paper interpretation, so use a strong model. For more than about 10 papers, split the files across parallel subagents (roughly 8 papers each) and give each the summarizer skill's instructions. Tag each summary with the key experimental details the review will need (system, gene or factor, readout).

**EndNote (optional).** Only if the user gave a library path. Never overwrite the original `.enl`.

**Write.** Run `lm_minireview-writer` on a strong model. Confirm the outline (sections and the question each answers) with the user before drafting when the request contains several explicit questions. If a question needs its own section (for example a yes/no comparison between two systems), give it one. After building the .docx, render it to PDF and images and inspect the pages before delivering.

**Verify.** Never deliver a review that has not been audited. Run `lm_claim-auditor` on the finished draft, then `lm_skeptical-reviewer`. Use a strong model and, for large drafts, independent subagents so the checker has not seen the writing process. Show the user the verdict counts and every Critical and Major issue. Fix problems by applying the suggested wording within the evidence, or by fetching better sources, then re-audit the changed claims. If the user chooses to ship with open issues, state them in the delivery message and the run notes. A review whose claims were checked mostly against abstracts must say so.

## Integrity rules for every run

- Never invent counts, trends, or per-year numbers. If the literature search cannot support a quantitative claim, describe it qualitatively.
- When no retrieved study directly tests the question, say so in the review and mark inferences as inferences. Proposed controls or experiments that are not from the retrieved literature must be labelled as proposals.
- Keep claims at the strength of the evidence: abstract-only papers support what the abstract states and no more.
- Cite only papers that were actually retrieved. Do not add references from memory.

## Delivery and run notes

1. Send the .docx to the user with a short summary: length, number of papers, how many were full text versus abstract-only, and the audit result (claims checked, verdict counts, open issues). Send the audit and skeptical-review reports alongside it.
2. Give the gap list (abstract-only papers worth re-running with PDFs) in the reply.
3. Save a run-notes file to the project as `claude/<topic>-minireview-notes.md` (use the Projects tool when a project is attached). Include: request, deliverable, scope, pipeline as run (counts at each stage), deviations from the standard flow, key caveats, and the gap list. Keep it short; it exists so a later session can resume or extend the review.

## Extending a delivered review

When asked to add a section or revise an argument, first read the run notes and the existing summaries. Draw on evidence already gathered; only triage and fetch new papers when the new section needs literature the set lacks. Rebuild the .docx, re-verify the render, re-audit the new and changed claims with `lm_claim-auditor`, and update the run notes with a dated addendum.

## Optional polish

After delivery, offer one of these bundled skills on the finished review:

- `lm_paragraph-editor`: paragraph structure and unity.
- `lm_flow-editor`: sentence-to-sentence flow.
- `lm_nih-grant-writer`: reframe passages for an NIH grant.
- `lm_your-voice`: score and rewrite against a voice profile. The shipped profile is a generic template; personalize `references/voice_profile.md` before relying on it.
