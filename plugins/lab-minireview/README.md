# lab-minireview

Turns a research topic into a finished minireview (.docx) with an annotated bibliography.

## What it does

Say something like "write a review on how [factor X] couples [process A] and [process B]" and the `lm_minireview-pipeline` skill runs the stages below, calling each bundled skill in turn.

| Stage | Skill | Output |
|-------|-------|--------|
| Find papers | `lm_pubmed-triage` | `02_triage/triage_<topic>_<date>.json` |
| Fetch text | `lm_article-fetcher` | `03_raw_fetched/PMID_<pmid>_raw.json` |
| Summarize | `lm_article-summarizer` | `04_article_summaries/PMID_<pmid>_<AuthorYear>.md` |
| EndNote (optional) | `lm_endnote-import` | populated `.enl` library + group |
| Write | `lm_minireview-writer` | `05_output/<Topic>_minireview.docx` |
| Verify | `lm_claim-auditor`, `lm_skeptical-reviewer` | `06_audit/claim_audit_*.md`, `06_audit/skeptical_review_*.md` |

Each skill can also be used on its own.

## Verification skills

| Skill | Purpose |
|-------|---------|
| `lm_claim-auditor` | Checks every claim and bibliography annotation against the cited paper's actual text; gives a verdict per claim with a verbatim excerpt and its location |
| `lm_skeptical-reviewer` | Adversarial whole-draft review: overreach, causal language, absence claims, counter-evidence, retractions, internal consistency; ranks issues and proposes fixes |

Claims checked only against abstracts are reported as such; the verdicts are only as strong as the source text available.

## Polish skills (optional, after the review is written)

| Skill | Purpose |
|-------|---------|
| `lm_paragraph-editor` | Tighten paragraph structure and style |
| `lm_flow-editor` | Repair sentence-to-sentence flow |
| `lm_nih-grant-writer` | Reframe text for an NIH grant |
| `lm_your-voice` | Score and rewrite against a voice profile (generic template; personalize before use) |

## Requirements

- PubMed connector enabled (triage and fetch).
- Word document skill (`docx`) available for the final build.
- For the EndNote stage: path to your `.enl` library (optional).

See `CONNECTORS.md` for the full list of connectors, local-file access, and built-in tools the plugin relies on.

## Notes

- Abstract-only papers are marked † in the bibliography and listed as a gap list at the end of each run.
- Run notes are saved per topic so a review can be resumed or extended later.
- Model guidance: use a strong model (Sonnet or Opus) for summarizing and writing; fetching is mechanical and can run on a cheaper model.

## License

MIT. See `LICENSE`.
