# Grant-Restricted Terms — Reference

A list of terms that have been flagged in NIH and NSF grant applications. When grant mode is active, the skill scans the paragraph against this list and reports any matches.

**Treat this list neutrally.** The skill's job is to help the user navigate a real funder constraint. The user always makes the final call on whether to keep a flagged term, replace it, or restructure. The skill does not editorialize about the list, does not refuse to discuss flagged terms, and does not introduce flagged terms in its own revisions.

Load this file whenever grant mode is active. Skip otherwise.

---

## How the skill uses this list

When grant mode is active:

1. **Scan the original paragraph** against the list. Note every match with its location.
2. **During Pass 2 and Pass 3,** do not introduce any flagged term in a revision. Check the proposed revision against the list before applying it.
3. **In the final output,** report:
   - Any flagged terms that remain in the final paragraph (the user will need to decide).
   - Any flagged terms that were present in the original and were successfully removed or replaced by revision (so the user can verify the replacement preserves meaning).
4. **Suggest alternatives** where reasonable scientific or technical substitutes exist. Acknowledge honestly when no substitute works.

Grant mode does NOT:
- Refuse to discuss or analyze the flagged terms.
- Refuse to report findings to the user.
- Automatically rewrite flagged terms out of the original — only Pass 1, 2, or 3 might do that in the normal course of revision, and only if the revision serves the paragraph's structural or stylistic goals.

---

## Matching rules

- **Case-insensitive.** "Diversity" and "diversity" both match.
- **Word-boundary matching.** "Race" matches "race" but not "trace" or "racemic." Match whole words or hyphenated compounds.
- **Morphological variants are explicit.** The list includes inflected forms separately (e.g., "bias," "biased," "biases"). Match the forms as listed.
- **Multi-word phrases match in order.** "Assigned female at birth" matches that exact phrase; "assigned at birth" matches its own phrase.
- **Plus-notation entries** like "breastfeed + people" match when both words appear near each other (within the same sentence, in either order). This handles inflectional variants ("breastfeed," "breastfeeding," "breastfed") combined with the second word ("people," "person").
- **Hyphenation variants.** Match both hyphenated and unhyphenated forms ("anti-racism" and "antiracism"; "non-binary" and "nonbinary"; "gender-based" and "gender based").

---

## The list

Alphabetized. Plus-notation indicates phrasal patterns (see matching rules above).

accessible, activism, activists, advocacy, advocate, advocates, affirming care, all-inclusive, allyship, anti-racism, antiracist, assigned at birth, assigned female at birth, assigned male at birth, at risk, barrier, barriers, belong, bias, biased, biased toward, biases, biases towards, biologically female, biologically male, BIPOC, Black, breastfeed + people, breastfeed + person, chestfeed + people, chestfeed + person, clean energy, climate crisis, climate science, commercial sex worker, community diversity, community equity, confirmation bias, cultural competence, cultural differences, cultural heritage, cultural sensitivity, culturally appropriate, culturally responsive, DEI, DEIA, DEIAB, DEIJ, disabilities, disability, discriminated, discrimination, discriminatory, disparity, diverse, diverse backgrounds, diverse communities, diverse community, diverse group, diverse groups, diversified, diversify, diversifying, diversity, enhance the diversity, enhancing diversity, environmental quality, equal opportunity, equality, equitable, equitableness, equity, ethnicity, excluded, exclusion, expression, female, females, feminism, fostering inclusivity, GBV, gender, gender based, gender based violence, gender diversity, gender identity, gender ideology, gender-affirming care, genders, Gulf of Mexico, hate speech, health disparity, health equity, hispanic minority, historically, identity, immigrants, implicit bias, implicit biases, inclusion, inclusive, inclusive leadership, inclusiveness, inclusivity, increase diversity, increase the diversity, indigenous community, inequalities, inequality, inequitable, inequities, inequity, injustice, institutional, intersectional, intersectionality, key groups, key people, key populations, Latinx, LGBT, LGBTQ, marginalize, marginalized, men who have sex with men, mental health, minorities, minority, most risk, MSM, multicultural, Mx, Native American, non-binary, nonbinary, oppression, oppressive, orientation, people + uterus, people-centered care, person-centered, person-centered care, polarization, political, pollution, pregnant people, pregnant person, pregnant persons, prejudice, privilege, privileges, promote diversity, promoting diversity, pronoun, pronouns, prostitute, race, race and ethnicity, racial, racial diversity, racial identity, racial inequality, racial justice, racially, racism, segregation, sense of belonging, sex, sexual preferences, sexuality, social justice, sociocultural, socioeconomic, status, stereotype, stereotypes, systemic, systemically, they/them, trans, transgender, transsexual, trauma, traumatic, tribal, unconscious bias, underappreciated, underprivileged, underrepresentation, underrepresented, underserved, undervalued, victim, victims, vulnerable populations, women

---

## Suggested alternatives

Where a reasonable scientific or technical substitute exists, suggest it. Where no clean substitute exists, say so honestly. The user decides.

### Reasonably substitutable in many scientific contexts

| Flagged term | Possible alternatives | Notes |
|---|---|---|
| diverse / diversity / diverse populations | varied, heterogeneous, broad-ranging; or specify the dimension (e.g., "populations spanning a wide age range," "samples from multiple geographic regions") | Specifying the dimension is often stronger writing |
| bias (statistical sense) | systematic error, skew, confounding | "Bias" in the cognitive sense is harder to replace |
| at risk / most risk | with elevated likelihood of, with higher incidence of, more likely to experience | Specify the outcome rather than using "at risk" generically |
| barrier / barriers | obstacle, limitation, constraint, factor that limits | Match the specific kind of obstacle when possible |
| underserved / underrepresented | with limited access to, with low participation rates in, with limited prior representation in | Specify the resource or context |
| vulnerable populations | populations with elevated susceptibility to X, individuals with limited resources for Y | Specify the vulnerability |
| marginalized | with limited structural access to X, historically excluded from Y | Specify the structural factor |
| disparity / health disparity | difference in outcomes, gap in incidence, variation in access | Specify what differs |
| inequality / inequity | difference, gap, asymmetry, uneven distribution | Specify what is unequal |
| stereotypes | overgeneralizations, presumptions, common assumptions | Use only when the original claim is about a cognitive phenomenon |
| socioeconomic | economic, income-related, occupational, educational | Often more precise to name the specific variable |
| trauma / traumatic (clinical) | injury, severe stress, acute stressor, adverse event | "Traumatic" in the medical sense (e.g., "traumatic brain injury") may be unavoidable as a technical term |
| disabilities / disability | functional limitation, condition affecting X, impairment of Y | "Disability" is sometimes the established medical term and may be unavoidable |
| historically | in prior decades, since [date], previously, until recently | Specify the time frame |
| systemic / systemically | population-level, organization-wide, structural (avoid), distributed across the system | Often replaceable by naming the specific level |

### Hard to substitute — usually unavoidable

| Flagged term | Why substitution is hard | Recommended action |
|---|---|---|
| sex | In genetics, endocrinology, and many areas of biology, "sex" is the technical term (sex chromosomes, sex-linked, sex differences in pharmacokinetics) | Flag but acknowledge unavoidable in many scientific contexts |
| female / females / male / males (as biological descriptors) | Standard terminology in biology and medicine | Flag but acknowledge unavoidable when describing biological sex variables |
| race (as a demographic variable) | Used in epidemiology and clinical research to describe study populations | Flag and note that recent guidance in some fields favors "racialized" or specific ancestry terms |
| mental health | Name of a discipline and clinical category | No clean substitute; flag with note |
| women / men | Standard demographic descriptors in clinical research | Flag but acknowledge unavoidable when describing study populations by self-reported gender |
| climate science | Name of a discipline | No substitute |
| pollution | Standard environmental science term | "Contamination," "emission," or specific pollutant names sometimes work |
| status (as in "socioeconomic status," "status as X") | Generic English noun | Replace only if context allows |
| accessible | Standard usability and disability term | "Available to," "usable by," "open to" sometimes work |

### Context-dependent — judgment required

Many other terms on the list are either common English words used in specific senses (e.g., "expression" in molecular biology — gene expression — is unrelated to the flagged sense), or are technical terms in specific fields. For these, the skill flags the match and notes the technical context if obvious.

| Flagged term | Notes |
|---|---|
| expression | If used in the molecular biology sense ("gene expression," "protein expression"), the flag is a false positive but the user should be aware that automated screens may not distinguish. |
| orientation | If used in a physical or structural sense ("orientation of the protein in the membrane"), false positive — note context. |
| identity | If used in a chemistry or molecular biology sense ("sequence identity," "compound identity"), false positive. |
| institutional | Common in research administration contexts ("institutional review board," "institutional funding"). |
| polarization | A physics and chemistry term ("light polarization," "molecular polarization"). |
| Gulf of Mexico | Geographic name — flag as appears on the list, but obviously unavoidable when describing the actual geographic feature in oceanography or marine biology contexts. |

When a flagged term appears in a clearly technical context that does not match the social/political sense, the skill notes this in the report so the user knows the flag may be a false positive on this particular use.

---

## Output format

The grant-mode report appears in two places:

### In `edited_paragraph.md`

If the final paragraph still contains flagged terms, add a section at the bottom:

```markdown
## Grant-mode notes

The following flagged term(s) remain in the final paragraph. Decide whether each is unavoidable for your scientific meaning, or whether further revision is warranted:

- "[term]" in [sentence N] — [suggested alternative if available, or "no clean substitute"]
```

If the final paragraph is clear of flagged terms, add a brief confirmation:

```markdown
## Grant-mode notes

No flagged terms remain in the final paragraph.
```

### In `analysis_details.md`

Add a section after Pass 3 (or wherever fits the run structure):

```markdown
## Grant mode — flagged term report

### Terms found in the original paragraph

| Term | Sentence | Context (brief) | Likely false positive? | Suggested action |
|---|---|---|---|---|
|   |   |   | yes / no | replace with X / keep (unavoidable) / restructure |

### Terms removed during revision

| Term | Originally in sentence | Removal occurred during | Replacement |
|---|---|---|---|
|   |   | Pass 1 / Pass 2 / Pass 3 |   |

### Terms remaining in the final paragraph

| Term | Sentence | Why kept | User decision needed? |
|---|---|---|---|
|   |   | unavoidable / user should review | yes / no |
```

---

## Important notes

- **The skill flags, it does not block.** A grant writer often has to use some of these terms because the scientific topic requires them. The skill's job is to surface the matches and provide alternatives; the user decides what stays.
- **False positives are possible.** Many words on this list are common English words or technical terms in specific fields. The skill notes likely false positives (e.g., "expression" in molecular biology) but errs on the side of flagging.
- **List updates.** This list reflects terms reported as flagged by NIH and NSF in early 2025. Specific terms and policies may change. Users should verify against current funder guidance.
- **The skill does not editorialize.** Grant mode is a practical tool for navigating a real constraint. The skill helps the user achieve their goal of submitting a fundable application; it does not comment on the merits of the constraint itself.
