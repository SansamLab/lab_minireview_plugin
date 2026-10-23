---
name: lm_endnote-import
description: Imports a list of papers into an EndNote .enl library with full metadata, Research Notes populated from article summaries, and a named group containing all imported papers. Use whenever the user wants to add papers to EndNote, create an EndNote group for a project, populate Research Notes fields, or import a PubMed triage list into their library. Trigger on phrases like "add to EndNote", "import into EndNote", "create an EndNote group", "put these in my library", or any request combining papers and EndNote. Also trigger automatically after lm_article-summarizer completes if the user indicates they want to use EndNote.
---

# Lab EndNote Import

Inserts papers into an EndNote `.enl` library via direct SQLite editing, creates a named group, and optionally populates Research Notes from article summary files. Handles the Dropbox/journal-file conflict that prevents in-place editing of Dropbox-synced libraries.

## Two modes

**Papers + group only (no notes)**: Run this when the user wants to add papers and create the group immediately — before or instead of generating summaries. Research Notes fields are left empty for the user to fill in manually. This is the low-cost path.

**Papers + group + Research Notes**: Run this after `lm_article-summarizer` has produced `PMID_*.md` files in `04_article_summaries/`. The script reads those files and populates each paper's Research Notes field automatically.

Both modes use the same script — the `--notes-dir` argument is simply omitted in the first mode. Always clarify which mode the user wants before running.

## Critical background (read before modifying the script)

EndNote `.enl` files are SQLite databases. Three non-obvious constraints apply:

1. **Dropbox disk I/O error**: SQLite cannot commit directly to a Dropbox-mounted path. Always copy the `.enl` to `/tmp/`, modify there, then copy back.
2. **Stale journal file causes rollback**: If a `.enl-journal` file exists next to the library, SQLite rolls back all external changes on open. Write the output to a *new filename* in the user's Cowork folder — never overwrite the original library file.
3. **Members blob byte order**: The group members blob uses a big-endian header (`\x00\x00\x00\x02`) followed by little-endian count and IDs. Using little-endian for the header produces empty groups in EndNote.
4. **`number` field NOT NULL**: The `refs.number` table column has a NOT NULL constraint. Always pass `''` (empty string), never `None`.
5. **`.Data` folder workflow**: After writing the fixed `.enl`, instruct the user to: open in EndNote (creates fresh `sdb` + empty `PDF` subfolder inside `.Data`), then copy the `PDF` subfolder from the original `My EndNote Library.Data/` into the new `.Data/`. Do NOT copy the `sdb` folder — this causes reversion to the original library state.

## Workflow

### Step 1 — Locate inputs and confirm mode

Gather:
- **Library path**: the user's `.enl` file (ask if not clear; look for `My EndNote Library.enl` in their connected folders)
- **Papers**: a triage JSON file, a list of PMIDs, or article summary markdown files from `lm_article-summarizer`
- **Group name**: ask the user what to call the group — do NOT reuse any name already in the library. Suggest something specific like `"[Topic] [Year] Review"`.
- **Mode**: if not already established (e.g., the user came here directly from `lm_pubmed-triage` without going through `lm_article-summarizer`), ask:
  > "Should I populate Research Notes automatically from summaries, or add papers and the group now and leave Notes empty for you to fill in?"

If Notes are requested, check `<project>/04_article_summaries/` for `PMID_*.md` files. If that folder is empty or missing, inform the user and offer to either run `lm_article-summarizer` first or proceed without notes.

### Step 2 — Deduplicate against the existing library first

Before fetching any metadata, open the library (copy to `/tmp/` first) and query which PMIDs are already present:

```sql
SELECT accession_number, record_id, research_notes FROM refs WHERE trash_state=0
```

Partition the input list into:
- **Already present with notes** → add to group only, skip metadata fetch and notes generation entirely
- **Already present without notes** → add to group, fetch notes from `04_article_summaries/` if available
- **Not present** → full insert needed

Report this breakdown to the user before proceeding. This avoids fetching metadata for papers the library already knows about.

### Step 3 — Fetch missing metadata

For papers not yet in the library, use the PubMed connector's `get_article_metadata` tool to fetch: title, authors, year, journal, volume, issue, pages, abstract, DOI.

### Step 4 — Run the import script

Write a Python script to `/tmp/endnote_import_<timestamp>.py` using the template in `scripts/endnote_import.py` (read that file first). Then execute it via bash.

The script:
1. Copies the `.enl` to `/tmp/work.enl`
2. Registers the required SQLite collation and function
3. Inserts each paper (skipping any PMID already present)
4. Updates `research_notes` field for each paper
5. Creates the named group with correct blob format
6. Copies the result to the output path (Cowork folder, new filename)

### Step 5 — Report and instruct

After the script runs, tell the user:
- How many papers were inserted vs. already present
- The output file path
- The group name to look for in EndNote
- **Important note about Research Notes**: "Full summary content is stored without truncation in the Research Notes field. The field's display in EndNote may be limited, but you can access/edit the full content by clicking into the field and using standard text editing (copy/paste, search, etc.)."

Then give the `.Data` folder instructions:
> "Open `[filename].enl` in EndNote via File → Open Library. It will create a new `.Data` folder with an empty `PDF` subfolder. Once open, close EndNote, then copy the `PDF` folder from your original `My EndNote Library.Data/` into the new `[filename].Data/`. Do not copy the `sdb` folder."

Present the output `.enl` file with `present_files`.

## Notes

- Always verify the group name is not already in the library before inserting. Read the `groups` table and parse spec XML to check. If the name exists, append the year or ask the user for a different name.
- The `research_notes` field stores full content without truncation. While the field display in EndNote's UI may be limited, the entire summary content is stored and can be accessed/edited in full. Do not truncate summaries before import.
- Do not modify the user's original library file. Always write to a new file in the Cowork/project folder.
