---
name: lm_skeptical-reviewer
description: Reviews a drafted minireview as a hostile, evidence-focused peer reviewer, looking for overreach, unsupported generalizations, causal language beyond the data, unsupported absence claims, ignored counter-evidence, retracted or preprint sources, and internal inconsistency. Use when the user asks for a "skeptical review", "be a harsh reviewer", "stress-test the review", "what would a reviewer attack", or "is this ready to share", and after lm_claim-auditor has run. Produces a severity-ranked issue list with proposed fixes and a ready / revise / not-ready verdict.
---

# Skeptical Reviewer

Read the draft the way a critical reviewer in the field would: looking for what the evidence does not license. Where `lm_claim-auditor` checks individual claims against source text, this skill judges whether the review as a whole is defensible.

## Inputs

- The draft `.docx` or text with its bibliography.
- The audit report in `06_audit/` from `lm_claim-auditor`. If none exists, run `lm_claim-auditor` first; do not review a draft whose claims have not been checked against source text.
- `03_raw_fetched/` and `04_article_summaries/` for reference, plus the PubMed connector for targeted searches.

## Checks

Work through each check and record findings with the exact location in the draft.

1. **Scope creep.** Compare each conclusion with the systems actually studied. Flag statements that generalize from one organism, cell line, locus, or condition to a general principle, and conclusions that treat results from different systems as the same mechanism without saying so.
2. **Causal language.** Flag "drives", "is required for", "controls", or "establishes" where the cited evidence is association, overexpression, or a single perturbation without rescue. Check whether knockdown or knockout results are described as proving sufficiency, or the reverse.
3. **Consensus claims.** Flag "it is well established", "widely accepted", and "the field agrees". Each needs a review or several independent primary papers, and the audit must show that they say so.
4. **Absence and novelty claims.** "No study has...", "first to show...", "remains unknown". Require a documented search (queries, source, date). Otherwise recommend softening to "in the papers retrieved here". Run one or two fresh PubMed searches to test the strongest absence claims, and report any hits.
5. **Counter-evidence.** For the three to five strongest claims in the draft, search PubMed for papers that report the opposite or a complicating result. Report what turns up, including papers outside the review's set. A review that omits a well-known contrary finding is vulnerable.
6. **Source quality.** Flag preprints, abstract-only sources carrying key arguments, reviews cited as primary evidence, and any paper whose PubMed metadata shows a retraction, expression of concern, or correction. Check that every flagged item is disclosed in the text or bibliography.
7. **Internal consistency.** Compare the introduction, section headings, conclusion, and any bibliography annotations. Flag claims that change strength between sections, numbers that disagree, and conclusions that promise more than the body delivers.
8. **Inference versus evidence.** Check that the author's own inferences, proposed controls, and speculation are labelled as such and not written in the voice of established fact.
9. **Selective emphasis.** Check whether the paper set is lopsided: one lab dominating a mechanism, one model system, or a narrow date range. State the imbalance if it affects the conclusions.

## Severity

- **Critical**: contradicted or unsupported claims, misattributions, retracted sources, unsupported absence claims that anchor the argument. Fix before sharing.
- **Major**: overgeneralization, causal overreach, undisclosed abstract-only dependence for a key claim, missing counter-evidence.
- **Minor**: wording that is slightly stronger than the evidence, missing hedges, inconsistent terminology.

## Output

Write `06_audit/skeptical_review_<topic>_<date>.md`:

1. **Verdict**: Ready, Revise, or Not ready, with two sentences of justification. Not ready if any Critical issue remains open.
2. **Issues table**: ID, severity, location in the draft, the problem, the evidence (audit claim IDs, excerpts, or search results), and a proposed replacement wording that stays within the evidence.
3. **Counter-evidence found**: papers, with PMIDs and one sentence each on what they report.
4. **Strongest parts**: two or three things the review gets right, so the user knows what to keep.
5. **Open verification gaps**: papers still needing PDFs and the claims that depend on them.

In the reply, give the verdict and the three most serious issues.

## Fix pass (only when the user asks)

1. Show the proposed edits and get a yes for the batch or for individual issues.
2. Apply only the approved edits to a copy of the draft and rebuild the `.docx` with the same pipeline used for the original.
3. Re-run `lm_claim-auditor` on the changed claims and any claim that cites the same papers. Confirm that no new problems appeared.
4. Report what changed, what was declined, and what is still open.

## Standards

- Be specific. Every criticism points at a sentence and states what evidence would resolve it.
- Do not invent counter-evidence. Report only papers actually retrieved, with PMIDs.
- Do not pad. If the draft is solid on a check, say so in one line and move on.
- Do not rewrite for style. This skill judges evidence and logic.
