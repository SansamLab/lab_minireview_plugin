---
name: lm_article-fetcher
description: Gathers full text or abstracts for a list of papers (from a lm_pubmed-triage JSON or a plain PMID list) and saves the raw fetched content per paper as an intermediate file, without generating summaries. This is the first phase of the article-summarization pipeline — use it when the user wants to fetch/prepare paper content on a cheaper model before generating summaries on a stronger model, or whenever lm_article-summarizer needs raw content it doesn't already have. Trigger on "fetch these papers", "gather full text for these PMIDs", "prep these articles before summarizing", "get the full text for", or as the automatic first step when lm_article-summarizer is invoked and no raw fetch exists yet.
compatibility: Requires PubMed MCP connector to be enabled. Requires Python, pdftotext (for local PDF extraction), and bash tools.
---

# Lab Article Fetcher

Fetches full text or abstracts for a list of papers and saves the raw content per paper to an intermediate file. Does not write summaries — that is `lm_article-summarizer`'s job. Splitting fetch from summarization lets the cheap, mostly-mechanical fetch pass run on an inexpensive model while the actual summary writing runs on a stronger one.

## Input formats accepted

- A triage JSON file from `lm_pubmed-triage` (use `include: true` papers)
- A plain list of PMIDs typed or pasted by the user
- A mix: "fetch these PMIDs plus everything in this triage file"

## Workflow

### Step 0 — Set up project directories

Create or confirm the project folder structure. Ask the user for a project name if not provided. Create:
```
<project_folder>/
├── 03_raw_fetched/          (for raw fetched content)
└── 04_article_summaries/    (for summarized markdown files)
```

If these directories already exist, skip creation. Report paths to the user so they know where outputs go.

### Step 1 — Collect PMIDs and ask about Research Notes mode

Parse input to get the full list of PMIDs to process. If from a triage JSON, filter to `"include": true` entries.

Then ask the user one question before proceeding:

> "How would you like to handle Research Notes?
> - **Automated** — I'll fetch full text/abstracts for each paper now, so summaries can be generated afterward. Thorough, but uses more of your usage quota.
> - **Draft first** — skip fetching. I'll write a minireview draft now using whatever notes already exist plus abstracts for the rest. Papers cited heavily without full notes get flagged for follow-up. Low cost upfront.
> - **Manual** — I'll add the papers and group to your EndNote library with empty Research Notes for you to fill in yourself. No fetching or summarization."

If they choose **Manual**, skip the rest of this skill and jump to `lm_endnote-import`. Stop there.

If they choose **Draft first**, skip the rest of this skill and jump directly to `lm_minireview-writer` in draft mode. Stop there.

If they choose **Automated**, continue with the steps below.

### Step 2 — Skip papers that already have summaries or raw fetches

Before fetching anything, check three sources for each PMID, in order:

1. **Existing summary**: look for `PMID_<pmid>_*.md` in `<project>/04_article_summaries/`. If found, skip — a summary already exists, no fetch needed.
2. **Existing raw fetch**: look for `PMID_<pmid>_raw.json` in `<project>/03_raw_fetched/`. If found, skip — raw content is already gathered and just needs summarizing.
3. **Existing EndNote Research Notes**: if the user has provided their `.enl` library path, query the `refs` table for `research_notes` where `accession_number = '<pmid>'`. If non-empty, skip — treat as already summarized.

Report skipped papers to the user ("Skipped 8 papers — already fetched or summarized"). Only proceed with the remaining PMIDs.

### Step 3 — Decide fetch depth per paper

For each remaining paper, check its triage score (from the triage JSON, if available):

- **Score ≥ 4.0**: full text — attempt full-text fetch
- **Score 2.0–3.9**: abstract only — fetch metadata only
- **No score available**: default to full text

This avoids spending effort on full-text fetches for papers that will barely be cited later.

### Step 4 — Check the EndNote library for an attached PDF FIRST (MANDATORY)

**This step MUST run before any online fetch attempt.** Check whether the user's EndNote library already has a PDF attached for this paper.

1. If the user's `.enl` path and associated `.Data` folder are available, query the library for the PMID's `record_id`:
   ```sql
   SELECT record_id FROM refs WHERE accession_number='<pmid>' AND trash_state=0
   ```
2. Look for a PDF in `{LibraryName}.Data/PDF/{record_id}/` — any `.pdf` file in that directory.
3. If found:
   - Extract text using bash:
     ```bash
     pdftotext "{pdf_path}" -  # outputs to stdout
     ```
   - Use this extracted text as the full-text source
   - Note `source: "EndNote library PDF"`
   - **Skip all of Step 5 for this paper** — do not attempt online fetches

If the `.Data` folder path is not known, ask the user for it once at the start and reuse the answer for all remaining papers in this session.

**CRITICAL**: Always check EndNote first. Many papers already in your library may be missed if this step is skipped.

### Step 4.5 — Check for user-provided local PDFs (optional, if user has them)

If the paper was not found in EndNote, check if the user has manually downloaded PDFs to a designated folder. This handles papers retrieved via institutional access or other means not yet added to EndNote.

1. Ask the user (once per session): "Do you have a folder with manually-downloaded PDFs for any of these papers?" If yes, get the folder path.
2. For each PMID without an EndNote PDF, check the folder for files matching:
   - `PMID_<pmid>.pdf`
   - `<AuthorYear>.pdf`
   - Or any PDF with the PMID in the filename
3. If found, extract text using `pdftotext`:
   ```bash
   pdftotext "{pdf_path}" - 
   ```
   Use this extracted text as the full-text source. Note `source: "user-provided local PDF"` and **skip Step 5 for this paper**.

This step is optional — only offer it if the user confirms they have papers downloaded locally.

### Step 5 — Fetch content online (if no local PDF)

For papers requiring full text with no local PDF found, attempt in order:

**5a.** Check if PMC full text is available:
- Use PubMed MCP: `convert_article_ids` to convert PMID → PMCID
- If a PMCID is returned, full text is available in PMC
- Use PubMed MCP: `get_full_text_article` with the PMCID to retrieve it

**5b.** If no PMCID returned (not in PMC, or paywall-only):
- Note the paper as "full text unavailable"
- **Pause and prompt the user**:

  > "Full text for **[Author Year] — [Title]** (PMID [PMID]) is not publicly available in PubMed Central. If you have institutional access, you can download the PDF and add it to your EndNote library. Once added, let me know and I'll use it. Or say 'skip' to fetch the abstract only."

- Wait for the user's response. If they say they've added the PDF, go back to Step 4 and re-check the library's `.Data/PDF/{record_id}/` folder. If they say 'skip', proceed with abstract-only.

**5c.** Use PubMed MCP: `get_article_metadata` — abstract as first fallback source for metadata and abstract.

**5d.** If abstract-only is insufficient and the user wants full text, attempt alternative sources:

- **bioRxiv / medRxiv preprints**: Use bioRxiv MCP to search for a preprint version of this paper (many papers are posted as preprints before peer review). If found, fetch it.
- **DOI direct resolution**: If a DOI is available, attempt to resolve it to an open-access version via CrossRef or DOAJ. If successful, fetch.
- **Generate download links** for the user to retrieve manually:
  - Publisher link (via DOI): `https://doi.org/[DOI]`
  - Google Scholar link: `https://scholar.google.com/scholar?q=[title]`
  - Institutional access (if user has an organization): `https://www.[institution].edu/` + proxy authentication
  - ResearchGate / Academia.edu (author self-archives, if available)

**5e.** Pause and ask the user:

> "Full text for **[Author Year] — [Title]** (PMID [PMID], DOI [DOI]) is not in open-access PMC and returned empty text. Options:
> - **Option A**: Download a PDF manually using these links, add it to your EndNote library, and let me know when it's there. I'll extract text from it.
>   - [DOI link]
>   - [Google Scholar link]
>   - [preprint link, if found]
> - **Option B**: If you have institutional access, download via your library's proxy and add to EndNote.
> - **Option C**: Stick with the abstract-only summary.
> 
> Which would you like?"

Note the source used for each paper (local PDF / PMC full text / preprint / open access / abstract only).

---

**Note on empty PMC text**: Some papers have valid PMCIDs but PMC's API returns empty full text. This is often due to:
- Embargo periods (text will unlock later)
- HTML-only formatting (not PDF, API limitation)
- Access restrictions or licensing
- Incomplete indexing

Do not retry these repeatedly — once empty, treat as "PMC available but inaccessible" and move to Step 5d.

### Step 6 — Save raw content

For each paper, save the fetched content as JSON in `<project_folder>/03_raw_fetched/`:

```
<project_folder>/03_raw_fetched/PMID_<pmid>_raw.json
```

Format:

```json
{
  "pmid": "12345678",
  "citation": "Smith J, Jones K. Title. Journal. 2024;12(3):456-470. doi:...",
  "author_year": "Smith2024",
  "depth": "full",
  "source": "PMC full text",
  "is_review_article": false,
  "raw_text": "... full extracted text or abstract ..."
}
```

- `depth`: `"full"` or `"abstract"`
- `source`: `"local PDF"`, `"PMC full text"`, or `"abstract only"`
- `is_review_article`: true if the paper is itself a review, not primary research (note this — it changes the summary template downstream)

This is a mechanical extraction step. Do not summarize, interpret, or condense the content here — save it as close to verbatim as the source allows. Interpretation is `lm_article-summarizer`'s job.

### Step 7 — Report and hand off

After saving all raw files, report:
- How many were fetched (full vs. abstract-only)
- How many were skipped (already had summaries, raw fetches, or Research Notes)
- Any PMIDs that failed (not found, access error)

Offer next step:
- "Pass these to `lm_article-summarizer` to generate the actual summaries — consider switching to a stronger model for that step, since it's the part that benefits most from it."

## Quality notes

- **CRITICAL**: PubMed's `get_full_text_article` requires PMC IDs, not PMIDs. Always use `convert_article_ids` first to get the PMCID from the PMID. If no PMCID is returned, the article is not in PMC and you must proceed to Step 5d (alternatives).
- **Source priority (in order)**: 
  1. EndNote library PDF (Step 4) — ALWAYS CHECK FIRST
  2. User-provided local PDF (Step 4.5 — only if not in EndNote)
  3. PMC full text (Step 5a — only if PMCID returns non-empty text)
  4. Preprint (bioRxiv/medRxiv, Step 5d)
  5. Open-access via DOI (Step 5d)
  6. User-requested PDF (after pause, Step 5e)
  7. Abstract-only (Step 5c as fallback)
  Always use the richest available source.
- **Empty PMC text**: Some articles have valid PMCIDs but PMC's API returns empty `full_text` fields. This is a known API limitation (embargo periods, HTML-only formatting, incomplete indexing), not a fetch bug. Do not retry these — move to Step 5d (alternatives) immediately.
- **Paywall pause (Step 5e) is intentional**: High-score papers behind paywalls deserve full-text treatment. Stop and let the user download manually rather than silently dropping to abstract-only. Many important papers are paywalled; user access often exists through institutions.
- `pdftotext` is available in the bash environment. For scanned PDFs (image-only), it returns empty output — note this and fall back to abstract.
- This skill is intentionally "dumb" — no synthesis, no interpretation, minimal reasoning per paper. That is what makes it safe to run on a cheap/fast model even across dozens of papers.
- The skip-if-exists check (Step 2) is the highest-leverage optimization in this workflow. Always run it before doing anything else.
- Do not fabricate content. If a paper's text is unavailable, say so explicitly in the raw file (`raw_text: null`, `source: "unavailable"`) rather than skipping the file entirely — `lm_article-summarizer` needs to know it was attempted.
