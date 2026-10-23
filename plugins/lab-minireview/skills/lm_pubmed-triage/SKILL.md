---
name: lm_pubmed-triage
description: Searches PubMed for papers on a research topic, scores each result for relevance to a user-defined focus, and produces a curated PMID list ready for summarization and EndNote import. Use whenever the user wants to find papers for a lab minireview, literature review, or reading list — even if they just name a gene, pathway, or biological question without saying "PubMed" or "search". Trigger on phrases like "find papers on", "search the literature for", "what's been published about", "gather references for", or any request that implies building a reading list around a scientific topic.
---

# Lab PubMed Triage

Searches PubMed, scores hits for relevance, and saves a curated paper list the user can hand directly to the article-summarizer and endnote-import skills.

## Workflow

### Step 1 — Check for existing triage file

Before searching PubMed, check whether `<project>/02_triage/` already contains a `triage_*.json` file for the same topic. If found, show the user:
- The file date, topic, and number of included papers
- Ask: "A triage file already exists for this topic. Use it, update it with a new search, or start fresh?"

If the user says "use it", skip Steps 2–5 entirely and go straight to Step 6 (save/confirm). This avoids a full PubMed search and scoring pass when the list was already curated.

### Step 2 — Clarify scope

Before searching, confirm three things with the user if not already clear:

1. **Topic** — the biological question or gene/pathway of interest
2. **Focus** — what angle matters for this review (e.g., "role in DNA replication", "cancer mutations", "therapeutic targeting")
3. **Exclusions** — any papers, authors, or subtopics to skip

Use `AskUserQuestion` if any of these are genuinely ambiguous. If the user's message already contains enough, proceed directly.

### Step 3 — Build search queries

Construct 2–3 PubMed queries that together cover the topic from different angles. Use MeSH terms where appropriate. Prefer specific over broad.

Example for topic "SMARCA4 and DNA replication":
- `SMARCA4[Gene Name] AND "DNA replication"[MeSH]`
- `BRG1 AND ("replication stress" OR "origin firing")`
- `SWI/SNF AND ("S phase" OR "replication fork")`

### Step 4 — Search and deduplicate

Use the `search_articles` tool of the PubMed connector to run each query. Collect all PMIDs, deduplicate, and fetch metadata (title, authors, year, abstract, journal) for each using the connector's `get_article_metadata` tool.

Cap total candidates at 60 unless the user requests more.

### Step 5 — Score for relevance

For each paper, score 1–5 on two axes:

**Relevance** (does the abstract address the focus?):
- 5 = directly on-topic, central to the focus
- 3 = related but peripheral
- 1 = tangentially related or off-topic

**Recency/impact** (rough proxy):
- Weight papers from the last 5 years slightly higher
- High-profile journals (Nature, Cell, Science, Mol Cell, eLife, PNAS) get a small bonus

Compute a combined score (relevance × 0.8 + recency_bonus × 0.2). Sort descending.

Do NOT exclude papers based on score alone — present everything scored ≥ 2 and let the user decide.

### Step 6 — Present and confirm

Show the user a ranked table:

```
PMID       | Score | Year | Authors        | Title (truncated to 80 chars)
-----------|-------|------|----------------|-----------------------------
12345678   | 4.8   | 2024 | Smith et al.   | SMARCA4 regulates replication...
...
```

Ask the user to confirm which papers to include. They can say "all of them", "top 15", or list specific PMIDs to exclude.

### Step 7 — Save output

Save the curated list as JSON to the project folder:

```
<project_folder>/02_triage/triage_<topic_slug>_<date>.json
```

Format:
```json
{
  "topic": "SMARCA4 and DNA replication",
  "focus": "role of SWI/SNF in replication stress",
  "date": "2026-06-22",
  "excluded_pmids": [],
  "papers": [
    {
      "pmid": "12345678",
      "title": "...",
      "authors": "Smith J, Jones K",
      "year": 2024,
      "journal": "Molecular Cell",
      "abstract": "...",
      "score": 4.8,
      "include": true
    }
  ]
}
```

Tell the user the file path and that they can pass it directly to `lm_article-summarizer` or `lm_endnote-import`.

## Project folder convention

The skill infers the project folder from context — either the user's mounted workspace folder or a subfolder named after the topic. If ambiguous, ask. The expected folder structure (created automatically if missing) is:

```
<project>/
  02_triage/          ← triage JSON files
  04_article_summaries/  ← per-paper markdown summaries
  05_output/          ← minireview .docx
```

## Notes

- Always exclude retracted papers (flag in PubMed metadata if present).
- If the user mentions a specific PMID to exclude (like a paper they've already read or that is out of scope), honor it silently.
- The triage JSON is the handoff format to the other skills — keep it complete and well-formed.
