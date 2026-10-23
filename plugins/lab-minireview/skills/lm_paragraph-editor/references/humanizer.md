# Pass 3 — Humanizer Reference

This document defines the humanizer pass. Pass 3 takes the output of Pass 2 (or Pass 1 if Pass 2 was skipped) and removes patterns characteristic of AI-generated text. Based on Wikipedia's "Signs of AI writing" guide, maintained by WikiProject AI Cleanup.

Load this file whenever Pass 3 runs.

---

## Pass 3 workflow

1. Read the input paragraph (the output of Pass 2).
2. Scan against the 29 patterns below.
3. Rewrite each section that exhibits an AI pattern, preserving meaning and register.
4. **Self-critique step.** Ask: "What makes this still feel AI-generated?" Answer briefly with any remaining tells.
5. Revise based on the self-critique.
6. Output the final paragraph.

The self-critique step is essential. The first humanizer pass usually catches the obvious patterns; the second pass catches subtler tells (overly even rhythm, plausible-but-bland phrasing, structural symmetry, generic vocabulary).

---

## Default register rule

Default Pass 3 behavior is to remove AI tells without changing the register or injecting personality. For academic, scientific, professional, or technical prose — which is the default assumption — do NOT add first-person voice, opinions, asides, or casual rhythm. Just remove the patterns.

The "Add Voice" section at the end of this file applies only when:
- the user explicitly asks for voice or personality injection ("make it more personal," "add voice," "humanize the tone"), OR
- the paragraph is clearly informal (blog post, personal essay, casual writing) AND the user has not asked for an academic register.

---

## Content patterns

### 1. Undue emphasis on significance, legacy, broader trends

Words to watch: *stands/serves as, is a testament/reminder, a vital/significant/crucial/pivotal/key role/moment, underscores/highlights its importance/significance, reflects broader, symbolizing its ongoing/enduring/lasting, contributing to the, setting the stage for, marking/shaping the, represents/marks a shift, key turning point, evolving landscape, focal point, indelible mark, deeply rooted.*

Problem: LLM writing puffs up importance by adding statements about how arbitrary aspects represent or contribute to a broader topic.

Before: "The Statistical Institute of Catalonia was officially established in 1989, marking a pivotal moment in the evolution of regional statistics in Spain. This initiative was part of a broader movement across Spain to decentralize administrative functions and enhance regional governance."

After: "The Statistical Institute of Catalonia was established in 1989 to collect and publish regional statistics independently from Spain's national statistics office."

### 2. Undue emphasis on notability and media coverage

Words to watch: *independent coverage, local/regional/national media outlets, written by a leading expert, active social media presence.*

Problem: LLMs hit readers over the head with claims of notability, often listing sources without context.

### 3. Superficial -ing analyses

Words to watch: *highlighting/underscoring/emphasizing..., ensuring..., reflecting/symbolizing..., contributing to..., cultivating/fostering..., encompassing..., showcasing...*

Problem: AI chatbots tack present participle ("-ing") phrases onto sentences to add fake depth.

Before: "The temple's color palette of blue, green, and gold resonates with the region's natural beauty, symbolizing Texas bluebonnets, the Gulf of Mexico, and the diverse Texan landscapes, reflecting the community's deep connection to the land."

After: "The temple uses blue, green, and gold colors. The architect said these were chosen to reference local bluebonnets and the Gulf coast."

### 4. Promotional and advertisement-like language

Words to watch: *boasts a, vibrant, rich (figurative), profound, enhancing its, showcasing, exemplifies, commitment to, natural beauty, nestled, in the heart of, groundbreaking (figurative), renowned, breathtaking, must-visit, stunning.*

Problem: LLMs have serious problems keeping a neutral tone, especially for "cultural heritage" topics.

### 5. Vague attributions and weasel words

Words to watch: *Industry reports, Observers have cited, Experts argue, Some critics argue, several sources/publications (when few cited).*

Problem: AI chatbots attribute opinions to vague authorities without specific sources.

Before: "Experts believe it plays a crucial role in the regional ecosystem."

After: "The river supports several endemic fish species, according to a 2019 survey by the Chinese Academy of Sciences."

For scientific writing: this matters especially for citation. If the original paragraph hedged with "several studies suggest," preserve that hedging unless the user provides a specific source.

### 6. Formulaic "Challenges and Future Prospects" sections

Words to watch: *Despite its... faces several challenges..., Despite these challenges, Challenges and Legacy, Future Outlook.*

Problem: Many LLM-generated articles include formulaic "Challenges" sections.

---

## Language and grammar patterns

### 7. Overused "AI vocabulary" words

High-frequency AI words: *actually, additionally, align with, crucial, delve, emphasizing, enduring, enhance, fostering, garner, highlight (verb), interplay, intricate/intricacies, key (adjective), landscape (abstract noun), pivotal, showcase, tapestry (abstract noun), testament, underscore (verb), valuable, vibrant.*

Plus the inflated vocabulary commonly seen alongside these: *robust, comprehensive, seamless, cutting-edge, innovative, nuanced, meticulous, journey, elevate, optimize, vital, essential, profound, transformative, paradigm, holistic, multifaceted, intersection (figurative), realm, sphere, fabric (figurative), backbone (figurative).*

Problem: These words appear far more frequently in post-2023 text. They often co-occur.

In scientific prose, words like *robust* (for a statistical estimator) or *foster* (in developmental biology) may be technically precise. Keep them when they are. Replace when they are decorative.

### 8. Copula avoidance (avoidance of "is"/"are")

Words to watch: *serves as/stands as/marks/represents [a], boasts/features/offers [a].*

Problem: LLMs substitute elaborate constructions for simple copulas.

Before: "Gallery 825 serves as LAAA's exhibition space for contemporary art. The gallery features four separate spaces and boasts over 3,000 square feet."

After: "Gallery 825 is LAAA's exhibition space for contemporary art. The gallery has four rooms totaling 3,000 square feet."

### 9. Negative parallelisms and tailing negations

Problem: Constructions like *not only...but...* or *it's not just about..., it's...* are overused. So are clipped tailing-negation fragments such as "no guessing" or "no wasted motion" tacked onto a sentence.

Before: "It's not just about the beat riding under the vocals; it's part of the aggression and atmosphere. It's not merely a song, it's a statement."

After: "The heavy beat adds to the aggressive tone."

This filter directly contradicts Pattern 16A from `sentence_patterns.md` (paired construction for contrast). The rule: Pattern 16A is permitted when the contrast is genuinely load-bearing. When it is decorative, Pass 3 removes it.

### 10. Rule-of-three overuse

Problem: LLMs force ideas into groups of three to appear comprehensive.

Before: "The event features keynote sessions, panel discussions, and networking opportunities. Attendees can expect innovation, inspiration, and industry insights."

After: "The event includes talks and panels. There's also time for informal networking between sessions."

This filter restricts Patterns 4 and 4A from `sentence_patterns.md` when three items aren't naturally present. A pair is often more honest.

### 11. Elegant variation (synonym cycling)

Problem: AI has repetition-penalty code causing excessive synonym substitution.

Before: "The protagonist faces many challenges. The main character must overcome obstacles. The central figure eventually triumphs. The hero returns home."

After: "The protagonist faces many challenges but eventually triumphs and returns home."

For scientific writing, this filter is especially important: technical terms should be repeated exactly, not varied. If the original uses "ion channel" consistently, do not switch to "channel complex" or "pore machinery" for variety.

### 12. False ranges

Problem: LLMs use *from X to Y* constructions where X and Y aren't on a meaningful scale.

Before: "Our journey through the universe has taken us from the singularity of the Big Bang to the grand cosmic web, from the birth and death of stars to the enigmatic dance of dark matter."

After: "The book covers the Big Bang, star formation, and current theories about dark matter."

### 13. Passive voice and subjectless fragments

Problem: LLMs often hide the actor or drop the subject entirely with lines like "No configuration file needed" or "The results are preserved automatically."

Before: "No configuration file needed. The results are preserved automatically."

After: "You do not need a configuration file. The system preserves the results automatically."

Caveat: In scientific writing, passive voice is sometimes appropriate (especially in methods sections). The rule is to keep passive only when the actor is genuinely unknown, irrelevant, or conventionally elided. Otherwise prefer active.

---

## Style patterns

### 14. Em dash overuse

Problem: LLMs use em dashes more than humans, mimicking "punchy" sales writing. In practice, most of these can be rewritten more cleanly with commas, periods, or parentheses.

Before: "The term is primarily promoted by Dutch institutions—not by the people themselves. You don't say 'Netherlands, Europe' as an address—yet this mislabeling continues—even in official documents."

After: "The term is primarily promoted by Dutch institutions, not by the people themselves. You don't say 'Netherlands, Europe' as an address, yet this mislabeling continues in official documents."

This is the most reliable AI tell. Pass 3 removes all em dashes by default, even those introduced by Pass 2 patterns (6, 7, 7A, 10A, 11, 11A). Replace with commas, parentheses, period split, or restructure.

### 15. Overuse of boldface

Problem: AI chatbots emphasize phrases in boldface mechanically.

Not usually relevant within a paragraph, but flag if present.

### 16. Inline-header vertical lists

Not usually relevant to paragraph editing — paragraphs should not contain vertical lists.

### 17. Title case in headings

Not usually relevant within a paragraph.

### 18. Emojis

Not usually relevant within a paragraph. Remove if present.

### 19. Curly quotation marks

Problem: ChatGPT uses curly quotes ("...") instead of straight quotes ("...").

Replace all curly quotes and curly apostrophes with their straight equivalents.

---

## Communication patterns

### 20. Collaborative communication artifacts

Words to watch: *I hope this helps, Of course!, Certainly!, You're absolutely right!, Would you like..., let me know, here is a...*

Problem: Text meant as chatbot correspondence gets pasted as content.

If any chatbot-correspondence artifacts appear inside a paragraph, remove them.

### 21. Knowledge-cutoff disclaimers

Words to watch: *as of [date], Up to my last training update, While specific details are limited/scarce..., based on available information...*

Problem: AI disclaimers about incomplete information get left in text.

### 22. Sycophantic / servile tone

Problem: Overly positive, people-pleasing language. Not usually inside a paragraph, but flag if present in commentary.

---

## Filler and hedging

### 23. Filler phrases

Before → After:
- "In order to achieve this goal" → "To achieve this"
- "Due to the fact that it was raining" → "Because it was raining"
- "At this point in time" → "Now"
- "In the event that you need help" → "If you need help"
- "The system has the ability to process" → "The system can process"
- "It is important to note that the data shows" → "The data shows"
- "It is worth mentioning that" → cut
- "It bears noting that" → cut

### 24. Excessive hedging

Problem: Over-qualifying statements.

Before: "It could potentially possibly be argued that the policy might have some effect on outcomes."

After: "The policy may affect outcomes."

Caveat: In scientific writing, hedging is often genuine and required. Preserve "suggests," "is consistent with," "appears to" when these correctly reflect the evidence. Cut hedging only when it stacks redundantly ("could potentially possibly might").

### 25. Generic positive conclusions

Problem: Vague upbeat endings.

Before: "The future looks bright for the company. Exciting times lie ahead as they continue their journey toward excellence. This represents a major step in the right direction."

After: "The company plans to open two more locations next year."

For scientific paragraphs ending in a gap statement, this filter usually does not apply — the gap statement is a specific, substantive closer, not a generic positive conclusion.

### 26. Hyphenated word pair overuse

Words to watch: *third-party, cross-functional, client-facing, data-driven, decision-making, well-known, high-quality, real-time, long-term, end-to-end.*

Problem: AI hyphenates common word pairs with perfect consistency. Humans rarely hyphenate uniformly.

This is a soft filter — well-established compound modifiers are sometimes hyphenated in formal writing. Apply judgment.

### 27. Persuasive authority tropes

Phrases to watch: *The real question is, at its core, in reality, what really matters, fundamentally, the deeper issue, the heart of the matter.*

Problem: LLMs use these phrases to pretend they are cutting through noise to some deeper truth, when the sentence that follows usually just restates an ordinary point with extra ceremony.

Before: "The real question is whether teams can adapt. At its core, what really matters is organizational readiness."

After: "The question is whether teams can adapt. That mostly depends on whether the organization is ready to change its habits."

### 28. Signposting and announcements

Phrases to watch: *Let's dive in, let's explore, let's break this down, here's what you need to know, now let's look at, without further ado.*

Problem: LLMs announce what they are about to do instead of doing it.

Not usually inside a paragraph, but flag if present.

### 29. Fragmented headers

Not relevant to paragraph editing — paragraphs do not have headers.

---

## Self-critique step (mandatory)

After the first humanizer revision, do the following internally:

1. Reread the revised paragraph.
2. Ask: "What makes this still feel AI-generated?" List remaining tells honestly. Common late-stage tells:
   - The rhythm is still too even — every sentence the same length.
   - The vocabulary is still bland or interchangeable.
   - Structural symmetry feels designed rather than natural.
   - The transitions are still too smoothed.
   - The closing sentence still feels like a "wrap-up" rather than a real ending.
   - Phrases that sound plausible but say nothing specific.
3. Revise once more to address the remaining tells.

Document this critique briefly in the analysis file (under the Pass 3 section) so the user can see what was caught on the second sweep.

---

## Cross-reference with Pass 2 patterns

Pass 2's `sentence_patterns.md` defines what is structurally possible. Pass 3 filters which patterns make it through. Specific overlaps:

| Pass 2 pattern | Pass 3 filter applies when |
|---|---|
| 4 / 4A (series) | Items are forced into three, third item is weak, or multiple consecutive sentences use triadic series |
| 6 (introductory series with dash) | Always — the dash variant is off |
| 7 / 7A (appositive with dashes) | Use the comma or parenthesis variants instead |
| 9 / 9A (key term repetition) | Reject if the repetition is elegant-variation synonym cycling rather than genuine key-term echo |
| 10A (appositive after dash) | Always — use Pattern 10 (colon) or restructure |
| 11 / 11A (interrupting modifier with dashes) | Always — use commas or parentheses |
| 16 (paired constructions like *not only X, but also Y*) | Reject when the contrast is decorative rather than load-bearing |
| 16A (this, not that) | Same — reject when decorative |

Patterns 1, 1A, 1B, 1C, 2, 3, 5, 7 (comma/parens), 7A (comma/parens), 8, 9 (when genuine), 9A (when genuine), 10, 11 (comma/parens), 12, 13, 14, 15, 17, 18, 19, 19A, 20 are not restricted by the humanizer pass.

---

## Add Voice section (optional, off by default)

Activate only when:
- the user explicitly requests voice or personality ("add voice," "make it personal," "more human"), OR
- the writing is clearly informal (blog post, casual essay, personal narrative) AND no academic register has been requested.

When active, in addition to removing AI patterns:

**Have a take.** Don't just report — react. Where the paragraph makes a claim, the writer's stance can be visible.

**Vary rhythm deliberately.** Short punchy sentences next to longer ones. Mix it up.

**Acknowledge mess.** Mixed feelings, half-formed thoughts, and tangents read as human.

**Use first person when natural.** "I keep thinking about..." is honest, not unprofessional.

**Be specific about feelings or stakes.** Not "this is concerning" but a concrete description of what's unsettling about it.

**Let some looseness in.** Perfectly balanced structure feels algorithmic.

Do not apply this section for academic, scientific, professional, or technical prose unless the user explicitly asks for it.

---

## Reference

Based on [Wikipedia:Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing), maintained by WikiProject AI Cleanup.

Key insight: "LLMs use statistical algorithms to guess what should come next. The result tends toward the most statistically likely result that applies to the widest variety of cases." Humanizing means breaking that statistical-likeliness habit.
