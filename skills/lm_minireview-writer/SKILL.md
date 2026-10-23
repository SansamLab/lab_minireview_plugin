---
name: lm_minireview-writer
description: Drafts a focused ~2000-word lab minireview as a Word .docx document from a set of article summaries, synthesizing findings into a coherent narrative with introduction, thematic body sections, identified gaps, conclusion, and Current Opinion in Cell Biology style annotated bibliography. Each reference includes an extensive 3–5 sentence annotation covering experimental approach, main findings, specificity/scope, and significance. Use whenever the user wants to write a minireview, short review, or literature summary document — even if they just say "write a review", "draft a minireview", "summarize the field", or "put the summaries together into a document". Trigger any time article summaries (from lm_article-summarizer) exist and the user wants a written synthesis.
---

# Lab Minireview Writer

Synthesizes article summaries into a focused, well-structured ~2000-word minireview suitable for a lab reading group, grant background section, or eventual journal submission. The output is a formatted Word `.docx` file with a **Current Opinion in Cell Biology style annotated bibliography** — each reference is followed by an extensive 3–5 sentence annotation covering the experimental approach, main findings, specificity of results, and broader significance. This format allows readers to quickly understand what each paper contributed without needing to open the original work.

## Two modes

**Draft mode** (low cost): Write the review using whatever is already available — existing Research Notes or `PMID_*.md` summaries where present, abstracts for everything else. Papers cited heavily without full notes are flagged in the bibliography and in a "Recommended follow-up" section. The user can then decide which papers to summarize deeply and rerun. Invoke this mode when the user chooses "Draft first" or when no summaries exist yet.

**Full mode** (standard): All papers have been summarized by `lm_article-summarizer`. Write the review from complete notes. This is the default when summaries are available.

The mode is determined by what content is available and what the user requested. If it is ambiguous, ask.

## Workflow

### Step 1 — Gather summaries and topic

Locate the article summaries to synthesize. Accept:
- A directory of `PMID_*.md` files from `lm_article-summarizer`
- A triage JSON file (use abstracts + scores for papers without full summaries)
- Summaries pasted directly into the conversation

Also clarify with the user if not already clear:
1. **Central question** — what biological/mechanistic question does the review address?
2. **Audience** — lab members (assume graduate-level biology), grant reviewers, or broader?
3. **Tone** — for a grant, use NIH plain-language style; for lab use, allow more mechanistic depth
4. **Key argument** — is there a gap, controversy, or emerging model the review should highlight?

### Step 2 — Inventory available content

For each paper, check what content is available, in this order:

1. `PMID_*.md` file in `<project>/04_article_summaries/` — full summary
2. Research Notes field in the EndNote library for that PMID — may be a full summary or partial notes the user added manually
3. Abstract from PubMed metadata — fallback, fetch with `get_article_metadata`

Classify each paper as:
- **Full notes** — has a summary file or populated Research Notes
- **Abstract only** — no summary, abstract fetched as fallback

Do not fetch full text here — this step is intentionally low-cost. Abstract-only papers get an abstract fetch; that is all.

### Step 3 — Select papers before reading full summaries

From titles, abstracts, and triage scores, identify which papers are likely **central** to the review (6–8 papers) vs. **supporting** (cited in passing). Read full summaries only for central papers with full notes available. For everything else, the abstract is sufficient to write an accurate in-text citation.

### Step 4 — Read and synthesize

Read full summaries for central papers. Group papers thematically — do not simply summarize each paper in sequence. Good groupings might be:

- By mechanism (e.g., chromatin remodeling vs. direct replication factors)
- By experimental model (human cancer lines, mouse models, biochemistry)
- By temporal order of the field's development
- By whether findings support or complicate the central model

Identify:
- **Consensus** — what is well-established?
- **Emerging findings** — what is new in the last 3–5 years?
- **Gaps / open questions** — what is not yet known or contested?
- **Clinical / translational angle** — any relevance to disease, therapy, or biomarkers?

### Step 5 — Outline before writing

Produce a brief outline (in the conversation, not the document) and confirm with the user before writing the full draft. Structure:

```
1. Introduction (~300 words)
   - Biological context and importance of topic
   - Central question the review addresses
   - Brief roadmap of what the review covers

2. [Thematic section 1] (~400 words)
   - e.g., "SWI/SNF Complexes at Replication Origins"

3. [Thematic section 2] (~400 words)
   - e.g., "Replication Stress and SWI/SNF Loss"

4. [Thematic section 3, optional] (~300 words)
   - e.g., "Therapeutic Implications"

5. Gaps and Future Directions (~300 words)
   - Specific open questions, with reference to key papers

6. Conclusion (~200 words)
   - Synthesizing statement, not just a summary
```

Adjust sections to fit the actual content. Aim for 1800–2200 words total.

### Step 6 — Write the draft

Write in clear, precise scientific prose. Follow these principles:

- **Lead with findings, not methods**: "SMARCA4 depletion reduces origin firing" not "Smith et al. used siRNA to deplete SMARCA4 and found..."
- **Active voice** throughout
- **One idea per paragraph**, with a clear topic sentence
- **Cite papers by (Author Year)** in-text — the docx skill will format these as superscripts if needed
- **No bullet points in the body text** — this is flowing prose
- **Avoid AI tells**: no em dashes, no "delves into", no "underscores the importance of"

### Step 7 — Build the .docx

Read the `docx` skill instructions (available in your skill list) and use them to create a formatted Word document:

- Title: [Topic] — A Minireview
- Authors: [Lab or author names], [Date]
- Heading 1 for each major section
- Body text in 12pt font, 1-inch margins
- Annotated reference list at the end (Current Opinion style)

**In the reference list**, use Current Opinion in Cell Biology format: each reference is followed by a detailed annotation (3–5 sentences) summarizing the paper's key findings, experimental approach, and significance:

```
1. Smith J, Jones K. Title of paper. Journal. 2024;12(3):456–470.
   Using chromatin immunoprecipitation and DNA fiber analysis in HCT116 cells, 
   Smith and colleagues demonstrate that SMARCA4 depletion reduces origin firing 
   efficiency by approximately 40%, with the greatest effect observed at origins 
   in chromatin-rich domains. Notably, SMARCA4-dependent origin selection appears 
   to operate independently of ORC recruitment but requires active nucleosome 
   remodeling. This work suggests that SWI/SNF complexes gate replication timing 
   by controlling chromatin accessibility at early-firing origins, with 
   implications for understanding how cancer-associated SMARCA4 mutations 
   dysregulate replication.

2. Garcia M, et al. Another paper title. Nature. 2023;15(2):123–135.
   Garcia et al. used live-cell imaging and biochemical reconstitution to show 
   that CDK activity directly phosphorylates BAF180 (a SWI/SNF subunit), promoting 
   complex recruitment to stalled replication forks. In both mouse and human cells, 
   CDK inhibition prevented fork restart and increased replication fork collapse, 
   even in the presence of functional homologous recombination machinery. These 
   findings establish a direct mechanistic link between cell cycle kinases and 
   chromatin remodeling in replication stress tolerance.
```

**Annotation structure:**
- **Sentence 1–2**: Experimental approach and main finding (methods + result)
- **Sentence 3–4**: Specificity, scope, or surprising aspects of the result
- **Sentence 5** (optional): Broader significance or clinical/mechanistic implications

**Annotations should be extracted from:**
1. The paper's full summary (`PMID_*.md` file) if available — pull multiple sections (Methods, Key Finding, Significance)
2. The paper's Research Notes field in EndNote if available
3. The abstract (PubMed metadata) as fallback — expand to capture mechanism and context

**Mark evidence basis** (as before):
- No marker — full summary/Research Notes available
- `†` — abstract only; annotation may be less detailed

Place the marker after the citation line:
```
3. Brown A, et al. Paper title. Journal. 2024;5(1):12–28. †
   Based on the abstract, this work identifies origin licensing activity during 
   S phase using in vitro licensing assays with purified replication proteins. 
   The authors report that CDT1 remains bound to chromatin throughout S phase 
   and can support re-licensing of replicated DNA templates, contrary to 
   conventional models that assumed CDT1 was exclusively degraded. This finding 
   has important implications for understanding how cells might tolerate 
   replication errors.
```

**In draft mode**, append a "Recommended Follow-up" section after the references:

```
## Recommended Follow-up

The following papers were cited in this draft but were read at abstract level only.
Papers are ranked by how central they are to the review's argument — prioritize
those listed first when adding Research Notes.

1. [Author Year] — [Title] (PMID: XXXX) — cited N times; central to [section name]
2. ...

To add Research Notes, either:
- Run lm_article-summarizer (automated) to generate and populate them
- Add them manually in EndNote, then rerun lm_minireview-writer for a revised draft
```

Only include papers in this list that were actually cited in the body — do not list papers that appear only in the reference list but were not discussed.

Save to:
```
<project_folder>/05_output/<Topic_slug>_minireview_draft.docx   ← draft mode
<project_folder>/05_output/<Topic_slug>_minireview.docx         ← full mode
```

Present the file with `present_files`.

### Step 8 — Offer next steps

In **draft mode**, offer:
- "Run `lm_article-summarizer` on the flagged papers (automated) to deepen the notes and rerun"
- "Add Research Notes manually in EndNote for the flagged papers, then rerun"

In **full mode**, offer:
- "Apply `lm_paragraph-editor` to any section you want tightened"
- "Apply `lm_nih-grant-writer` to reframe for a grant application"
- "Apply `lm_your-voice` to match your own writing style"

## Tone and style notes

The default voice is: mechanistic, precise, restrained. Findings are stated directly. Uncertainty is acknowledged but not overstated. The narrative moves from what is known → what is emerging → what is unknown. This is a scientific document, not a press release.

If the audience is grant reviewers, also read the `lm_nih-grant-writer` skill and apply its principles, particularly: lead with significance, quantify where possible, and make the gap statement sharp and concrete.

## Annotated Bibliography (Current Opinion in Cell Biology style)

Each reference in the bibliography is followed by an **extensive annotation** (3–5 sentences) that includes the paper's experimental approach, main findings, specificity, and broader significance. This style is used by Current Opinion journals and provides readers with detailed context about each cited work.

**How to write extensive annotations:**
- **Sentence 1–2**: State the experimental approach clearly, then the main finding. Example: "Using chromatin immunoprecipitation in HCT116 cells, Smith et al. demonstrate that SMARCA4 depletion reduces origin firing by 40%."
- **Sentence 3–4**: Add specificity, scope, or surprising aspects. Example: "The effect is most pronounced at origins in chromatin-rich domains and occurs independently of ORC recruitment."
- **Sentence 5** (optional): Broader significance or clinical implications. Example: "This work suggests that cancer-associated SMARCA4 mutations dysregulate replication timing through altered chromatin access."
- Avoid vague language. Be specific about cell types, quantitative findings, and mechanistic insights.
- Use active voice: "Smith et al. demonstrate that..." not "It was shown that..."
- For papers with only abstracts, still aim for 3–4 sentences based on abstract content, and mark with `†`.

**Annotation length**: Aim for 80–150 words per annotation (roughly 3–5 sentences). Current Opinion journals typically provide this level of detail so readers can assess the paper's relevance without opening it.
