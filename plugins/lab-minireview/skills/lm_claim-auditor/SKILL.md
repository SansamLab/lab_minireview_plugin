---
name: lm_claim-auditor
description: Audits a drafted minireview claim by claim, checking that every factual statement and every bibliography annotation is backed by the actual text of the paper it cites. Use when the user asks to "fact-check the review", "verify the citations", "check that every claim is supported", "audit the draft against the literature", or after lm_minireview-writer produces a draft. Produces a verdict per claim with a verbatim excerpt and its location in the source. Does not edit the draft.
---

# Claim Auditor

Act as a skeptical fact-checker. Assume every claim is unsupported until the cited paper's own text shows otherwise. The goal is a record, per claim, of what the source actually says.

## Inputs

- The draft: a `.docx` (extract text with pandoc or python-docx) or pasted text, including the annotated bibliography.
- Raw source text: `03_raw_fetched/PMID_<pmid>_raw.json` from `lm_article-fetcher` (fields `raw_text`, `depth`, `source`).
- Optional, for navigation only: `04_article_summaries/`. Summaries are model-written. Never cite a summary as evidence for a claim.

If the project folders are missing, ask for the draft and the paper PMIDs, then fetch text with the PubMed connector before auditing.

## Evidence rules

1. Evidence is verbatim text from the cited paper (title, abstract, or full text). Recall from memory or from summaries is never evidence.
2. Record a short verbatim excerpt (one sentence at most) plus its location (abstract, results section, figure legend, discussion). Never paste long passages.
3. Check every dimension of the claim against the excerpt: system (organism, cell line, in vitro or in vivo), factor or perturbation, readout, direction of effect, magnitude or numbers, and strength of language (association vs causation, "required" vs "contributes").
4. Background statements inside a paper's Introduction or Discussion are usually attributed by that paper to other work. If a draft claim cites the paper for a finding the paper itself did not produce, mark MISATTRIBUTED and name the original source if the paper gives it.
5. A review article supports "the field holds that..." statements but is not primary evidence for a specific experimental result.
6. An abstract-only source supports only what the abstract states. Detail beyond that is ABSTRACT-LIMITED, not supported.
7. If `raw_text` is null or truncated, try the PubMed connector's full-text tool once. If still unavailable, mark UNVERIFIABLE and list the paper for a manual PDF.

## Workflow

1. **Citation inventory.** Map each in-text citation to a bibliography entry, a PMID, and a raw file. Flag: citations with no bibliography entry, bibliography entries never cited in the body, entries with no raw text, preprints, and papers with retraction or correction notices in their PubMed metadata.
2. **Claim extraction.** Split the body and each bibliography annotation into atomic claims, one factual assertion each. Give every claim an ID (C001...), its section, its text, its citation(s), and a type: finding, mechanism, generalization, consensus statement, background fact, numerical, absence claim ("no study has..."), or inference by the author.
3. **Verification.** For each claim, search the cited paper's text for the supporting passage and assign a verdict from the table below. For claims citing several papers, verify against each and report per paper. For uncited factual claims, search the other papers in the set before concluding.
4. **Report.** Write the outputs described below.

For more than about 40 claims, split the work by cited paper across parallel subagents when the session offers them. Give each subagent only the claim text, the citation, and the path to the raw file, not the draft's surrounding argument, so each check is independent. Merge the results into one report.

## Verdicts

| Verdict | Meaning |
|---|---|
| SUPPORTED | The source states this finding in the same system, direction, and strength. |
| SUPPORTED-WEAKER | The source supports a narrower, hedged, or correlational version. Give the exact supported wording. |
| ABSTRACT-LIMITED | Only an abstract is available and it does not cover the claim's detail. |
| MISATTRIBUTED | The source reports this as another group's finding, or a different paper in the set is the real source. |
| NOT FOUND | No passage in the cited paper supports the claim. |
| CONTRADICTED | The source says something incompatible with the claim. |
| UNCITED | A factual claim with no citation. State whether any paper in the set supports it. |
| UNVERIFIABLE | The source text is unavailable. |

Absence claims ("no study has directly tested...") can only be supported by a documented search. Mark them UNCITED unless the draft states the searches run, and recommend either adding that statement or softening the claim.

## Output

Write to `06_audit/` in the project folder:

- `claim_audit_<topic>_<date>.json`: one record per claim with ID, section, claim text, citation, PMID, verdict, excerpt, location, suggested fix.
- `claim_audit_<topic>_<date>.md`: a readable report with
  1. verdict counts, overall and for body versus bibliography annotations;
  2. every non-SUPPORTED claim, each with the excerpt, location, and a suggested replacement sentence that stays within the evidence;
  3. reference coverage: full-text versus abstract-only papers, and which claims depend on each;
  4. the citation inventory problems from step 1;
  5. a papers-needing-PDF list.

In the reply, give the counts and the five most serious problems. Do not edit the draft. Offer to hand the report to `lm_skeptical-reviewer` or to apply the suggested fixes.

## Standards

- Never mark a claim SUPPORTED because it sounds plausible or because related papers say it.
- Never soften a verdict to avoid bad news. A report with many NOT FOUND or SUPPORTED-WEAKER entries is a useful report.
- Say how many claims were checked against full text and how many against abstracts only, so the user knows how far the check can be trusted.
