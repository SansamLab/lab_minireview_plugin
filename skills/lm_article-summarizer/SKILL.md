---
name: lm_article-summarizer
description: Generates detailed figure-by-figure summaries for scientific papers from raw fetched content (produced by lm_article-fetcher) and saves each summary as a markdown file. Use whenever the user wants to summarize papers for a literature review, minireview, or grant — even if they just say "summarize these papers", "get me summaries", "generate the summaries now", "write summaries for these PMIDs", or hand over raw-fetch files from lm_article-fetcher. If raw fetched content doesn't exist yet for the requested PMIDs, run lm_article-fetcher first.
compatibility: Runs best on Sonnet 4.6 or stronger. Depends on lm_article-fetcher for input files.
---

# Lab Article Summarizer

Turns raw fetched paper content into structured, figure-by-figure summaries saved as markdown files. These summaries feed directly into `lm_minireview-writer` and `lm_endnote-import` (as Research Notes).

This skill does no fetching itself — it reads the intermediate files `lm_article-fetcher` produces. If those don't exist yet, run `lm_article-fetcher` first (on a cheap/fast model), then run this skill (on a stronger model) for the actual writing.

## Input

- A directory of `PMID_<pmid>_raw.json` files from `lm_article-fetcher`, in `<project>/03_raw_fetched/`
- If the user instead hands over a triage JSON or plain PMID list with no raw fetches present, tell them raw content needs to be gathered first and offer to invoke `lm_article-fetcher` (mention it can run on a cheaper model, then hand back here for this step).

## Workflow

### Step 0 — Confirm project directories exist

Confirm `<project>/03_raw_fetched/` and `<project>/04_article_summaries/` directories exist. Create `04_article_summaries/` if missing. Ask the user for the project path if not already established.

### Step 1 — Collect raw-fetch files to process

Find all `PMID_<pmid>_raw.json` files in `<project>/03_raw_fetched/` for the requested PMIDs (or all of them, if the user says "summarize everything that's been fetched").

Skip any PMID that already has a summary file (`PMID_<pmid>_*.md` in `<project>/04_article_summaries/`) **UNLESS** the user explicitly asks to re-summarize or the raw file has changed since the last summary was created (check file timestamps). 

If a paper was previously summarized as abstract-only but now has full text (user provided a PDF), the raw file's `source` field will have changed — re-process it with the full-text template.

Report skipped papers and any re-processed papers to the user.

### Step 2 — Generate summary

For each remaining raw-fetch file, write a structured markdown summary from its `raw_text`. **Generate comprehensive, full-length summaries** — do not limit length. Full content will be stored untruncated in EndNote's Research Notes field.

**Important**: Before generating the summary, check if the `raw_text` contains **verifiable figure citations**. Some PMC full-text documents have inline figure references stripped (e.g., text says "the result appeared in ()" instead of "(Fig. 2a)") with no figure legends available. If figure citations are systematically absent or unverifiable:
- Do NOT fabricate or guess at figure numbers
- Instead, use the "Results" template below with numbered result sections
- Add a note explaining the source limitation

---

**Template A: Full text with verifiable figures**

Use this if `depth` is `"full"`, `raw_text` is present, AND figure citations are intact:

```markdown
# [Author Last Name] [Year] — [Abbreviated Title]
**PMID:** [PMID]
**Citation:** [citation field from raw-fetch file]

## Key Question
One sentence: what biological question does this paper address?

## Main Findings
2–4 bullet points covering the paper's major conclusions.

## Figures / Experiments
For each major figure or experiment (as many as the content supports):

### Figure [N] — [Short descriptor]
- **What they did:** [Assay, cell line, conditions]
- **What they found:** [Result]
- **Why it matters:** [Interpretation / significance]

## Relevance to [Topic]
1–3 sentences connecting this paper's findings to the review topic.

## Limitations / Open Questions
Brief note on caveats or what remains unresolved.
```

---

**Template B: Full text with stripped figure citations**

Use this if `depth` is `"full"`, `raw_text` is present, BUT figure numbers/legends are not recoverable from the source:

```markdown
# [Author Last Name] [Year] — [Abbreviated Title]
**PMID:** [PMID]
**Citation:** [citation field from raw-fetch file]

## Key Question
One sentence: what biological question does this paper address?

## Main Findings
2–4 bullet points covering the paper's major conclusions.

## Results
*Note: PubMed Central's full-text API for this article does not include verifiable figure numbers or legends. The following sections describe the major experimental results in order of presentation.*

### Result 1 — [Description of first major experiment]
- **Methods:** [Assay, conditions, model system]
- **Findings:** [What they observed]
- **Significance:** [Why it matters to the field]

### Result 2 — [Description of second major experiment]
[Same structure as Result 1]

[Continue for each major result section...]

## Relevance to [Topic]
1–3 sentences connecting this paper's findings to the review topic.

## Limitations / Open Questions
Brief note on caveats or what remains unresolved.
```

---

**Template C: Abstract-only or unavailable text**

Use this if `depth` is `"abstract"`, or `source` is `"unavailable"` with no `raw_text`:

Condense to Key Question, Main Findings, and Relevance only — skip the figure-by-figure section and note that this is an abstract-only or unavailable-text summary.

---

**Template D: Review articles**

Use this if `is_review_article` is `true`:

Replace the Figures / Results section with "Key Arguments" and note it is a review article, not primary research.

---

**Quality standards for all templates:**
- Keep language precise and mechanistic. Avoid vague phrases like "the authors showed that..." — write the finding directly: "SMARCA4 depletion reduces origin firing by 40% in S phase."
- Do not fabricate figures, figure numbers, or findings. If `raw_text` is missing, thin, or has stripped citations, say so explicitly rather than guessing.
- When using Template B (stripped citations), make the note about the source limitation clear and upfront so the reader knows why figure numbers are absent.

### Step 3 — Save files

Save each summary as:
```
<project_folder>/04_article_summaries/PMID_<pmid>_<AuthorYear>.md
```

Example: `PMID_42002776_Harrod2026.md`

### Step 4 — Report coverage and identify gaps

After saving all files, generate a comprehensive report:

**Summary statistics:**
- How many summaries were generated
  - Full figure-by-figure or result-by-result detail (from full text)
  - Abstract-only (from abstract or inaccessible full text)
- How many were skipped (already had summaries)
- Any raw-fetch files that had no usable content

**Critical gap report:**
- List all papers that are **still abstract-only** (depth: "abstract" in their raw JSON)
- For each, show:
  - PMID, Author Year, Citation
  - Reason: "No PMC access", "PMC returned empty text", "No PMCID exists"
  - What the user should do: "Check if this paper is already in your EndNote library but wasn't detected. If not, download the PDF manually and add to your EndNote library, then re-run lm_article-fetcher and lm_article-summarizer."

This gap report is essential — it shows you exactly which papers need manual intervention.

**Next steps offered:**
- If no gaps: "All papers have figure/result-level summaries. Pass these to `lm_minireview-writer` to draft the review or `lm_endnote-import` to add as Research Notes."
- If gaps exist: "Papers listed below need manual PDF downloads. Once you add them to your EndNote library, re-run lm_article-fetcher (which will extract text from EndNote), then re-run this skill to create full summaries for those papers."

## Quality notes

- This is the step worth running on a strong model (Sonnet 4.6 or Opus) — it's the only part of the fetch→summarize pipeline that requires real interpretation and synthesis per paper.
- Don't re-fetch or re-derive anything `lm_article-fetcher` already gathered — trust its `raw_text`, `depth`, and `source` fields.
- If a raw-fetch file shows `source: "unavailable"`, still generate a minimal stub summary noting the paper couldn't be fetched, rather than silently omitting it — downstream skills expect one file per requested PMID.
- Generate full, untruncated summaries. The entire content will be stored in EndNote's Research Notes field without truncation. Do not limit length to fit display constraints — the display limit in EndNote's UI does not constrain storage.
- Be mechanistic and specific. A good summary reads like: "Figure 3 shows that PARP inhibition in HR-deficient cells causes centrosome amplification through persistent S-phase entry." Not: "The paper demonstrates the importance of PARP in cell cycle regulation."
- **Critical**: Some PMC full-text documents have figure citations stripped by the API (e.g., "(Fig. 2a)" becomes "()"), and no figure legends are available. In these cases, use Template B ("Results" with numbered subsections) instead of guessing at figure numbers. Always be honest about source limitations — never fabricate figure numbers you can't verify from the `raw_text`.
- **Gap report is essential**: The Step 4 gap report shows which papers are still abstract-only. Use it to guide your manual downloads. Papers listed there should be downloaded and added to your EndNote library, then re-fetched and re-summarized.
- **Fetcher must check EndNote first**: The lm_article-fetcher MUST check your EndNote library before attempting any online fetches (Step 4). If that step is skipped or doesn't work properly, papers already in your library will be re-fetched unnecessarily or missed entirely. Always verify that Step 4 is executing in the fetcher.
