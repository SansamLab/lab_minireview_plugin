# Connectors and requirements

This plugin does not bundle any connector configuration. Enable the items below in your Claude settings before running the pipeline.

## Connectors

| Need | Used by | Required? | Notes |
|------|---------|-----------|-------|
| PubMed | `lm_pubmed-triage`, `lm_article-fetcher`, `lm_claim-auditor`, `lm_skeptical-reviewer` | Yes | Search, article metadata, and PMC full text. Triage and fetch cannot run without it. The verification skills use it to retrieve missing full text, check retraction status, and search for counter-evidence. |

## Local files (desktop app)

| Need | Used by | Required? | Notes |
|------|---------|-----------|-------|
| Connected folder containing your EndNote `.enl` library | `lm_endnote-import`, and the EndNote check in `lm_article-fetcher` | No | Open the chat in the Claude desktop app and connect the folder holding the library. If it is not connected, EndNote steps are skipped and the rest of the pipeline still runs. The original `.enl` is never overwritten. |

## Built-in skills and tools

| Need | Used by | Required? | Notes |
|------|---------|-----------|-------|
| Word document (`docx`) skill | `lm_minireview-writer` | Yes | Builds the final `.docx`. |
| Python and `pdftotext` in the workspace | `lm_article-fetcher` | Only for local PDFs | Used to extract text from PDFs found in your EndNote library. |
| Projects | `lm_minireview-pipeline` | No | Used to save per-topic run notes. If no project is attached, notes are written to the workspace instead. |

## Optional skills

`lm_paragraph-editor`, `lm_flow-editor`, `lm_nih-grant-writer`, and `lm_your-voice` need no connectors. The verification skills run best with the PubMed connector but can audit from existing `03_raw_fetched/` files without it.
