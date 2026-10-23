# Banned Vocabulary — LLM-ism Ban List with Exceptions

The default ban or strong-avoid list. Use these as flags when scoring text against the voice profile. Each section ends with the exceptions where the word IS allowed in specific technical or scientific contexts.

**Matching rule:** case-insensitive whole-word match. Note that some entries are conditional (e.g., "*robust* outside technical/statistical/reproducibility contexts") and require checking context before flagging.

---

## Banned or strongly discouraged adjectives

- actionable
- bespoke
- bustling
- captivating
- commendable
- comprehensive (as filler)
- cutting-edge
- daunting
- ever-evolving
- game-changing
- groundbreaking
- holistic
- impactful
- innovative (when unsupported)
- insightful
- intricate (when used as ornament)
- invaluable
- meticulous
- multifaceted
- notable
- noteworthy
- nuanced (as filler)
- paramount
- pivotal
- profound
- remarkable (unless referring to a specific surprising result)
- revolutionary
- robust (outside technical/statistical/reproducibility contexts)
- seamless
- state-of-the-art
- substantial (when vague)
- tailored
- thought-provoking
- transformative
- unparalleled
- unwavering
- vibrant
- whimsical

---

## Banned or strongly discouraged nouns

- beacon
- blueprint (as metaphor)
- ecosystem (as metaphor)
- enigma
- frontier
- implications (when vague)
- interplay (when vague)
- intricacies
- journey (as metaphor)
- kaleidoscope
- landscape (as metaphor)
- linchpin
- metropolis
- milestone (as metaphor)
- paradigm
- paradigm shift
- plethora
- quest
- realm
- resonance
- roadmap (as metaphor)
- synergy
- tapestry
- testament
- touchpoint
- treasure trove
- value proposition

---

## Banned or strongly discouraged verbs

- align (as metaphor)
- amplify
- bolster
- champion
- craft a solution
- delve
- elevate
- embark
- empower
- encompass
- enhance (when vague)
- enrich (when vague)
- facilitate (when plain *help* or *allow* works)
- foster
- harness
- leverage
- navigate (as metaphor)
- pioneer
- resonate
- safeguard (when not literal)
- shed light
- showcase
- spearhead
- streamline (outside technical workflows)
- supercharge
- tailor
- transcend
- underscore
- uncover (when *identify*, *test*, or *show* is more precise)
- unleash
- unlock
- unpack
- unravel
- unveil
- utilize

---

## Banned or strongly discouraged adverbs / transitions

- accordingly
- arguably
- consequently
- effectively (as filler)
- essentially (as filler)
- furthermore (when stacked)
- hence
- holistically
- meticulously
- moreover
- namely
- nonetheless
- notwithstanding
- seamlessly
- significantly (when not statistical)
- strategically
- subsequently
- substantially (when vague)
- thereby
- therefore (when overused)
- thus (when overused)
- ultimately (when overused)
- undeniably
- undoubtedly

---

## Banned phrase patterns

- **Scene-setting openers:** *In today's fast-paced world, In an era of, Now more than ever, As science continues to evolve.*
- **Importance filler:** *It is important to note, It should be noted, This is crucial, This is pivotal, This is paramount, Needless to say.*
- **Grandiose action phrases:** *navigate the complexities of, unlock the secrets of, unravel the mysteries of, shed light on the intricate interplay, delve into the landscape, harness the power of.*
- **Formulaic praise/openers:** *Great question, Excellent point, I'm glad you asked.*
- **Formulaic closings:** *I hope this helps, Let me know if you need anything else, In conclusion* (when the section already signals closure).
- **Structural gimmicks:** compulsory three-part lists when two items are enough; repeated *not only... but also...* constructions; stacked short punch sentences; repetitive *This work will...* impact sentences.
- **Grant-inflation phrases:** *far-reaching implications, valuable insights, critical gap* (when overused), *dynamic interplay, comprehensive understanding, highly tractable, powerful platform* (unless supported by specifics).

---

## Exceptions — words ALLOWED in specific contexts

These appear in the corpus with technical or specific meanings and should NOT be flagged when used in those contexts.

| Word | Allowed when | Corpus evidence |
|---|---|---|
| **robust** | Technical: *robust detection, robust checkpoint response, robust transition, robust statistical result, reproducibility claim* | 17 occurrences in 14/22 files |
| **significantly** | Statistical significance or quantitatively demonstrated differences | 48 in 16/22 files |
| **landscape** | Established scientific phrases: *[process] landscape, chromatin landscape* (genome-wide distributions) | 18 in 9/22 files |
| **facilitate** | Molecular mechanisms where a factor enables recruitment, processing, comparison, or assembly. Replace with *allow, help, enable* when not mechanistic. | (in corpus) |
| **comprehensive** | Complete gene set, genome-wide screen, genuinely broad measurement. Avoid *provide a comprehensive understanding* unless scope justifies it. | (in corpus) |
| **innovative** | Grant Innovation sections only, tied to a concrete conceptual or experimental departure | (in corpus) |
| **therefore, thus, thereby** | When marking real causal inference. Prune when decorative. | *therefore* 108 in 21/22; *thus* 59 in 19/22 — overuse is a known risk |
| **uncover** | Sparingly, when describing discovery from data. Prefer *identify, define, test, show, determine.* | 15 in 13/22 files |
| **interplay** | Specific named molecular interaction; not vague abstract use | 6/22 files — weak signal |
| **dynamic** | Specific dynamic process being measured, not as adjective filler | (in corpus) |

---

## Scoring guidance

When scoring text against this list:

- **Each unambiguous ban-list hit** (no exception applies) is a deviation. Cite the category (adjective/noun/verb/adverb/phrase) and any corpus frequency that supports the ban.
- **Conditional bans** (e.g., *robust* outside technical contexts) require context check. If context appears technical, do not flag.
- **Phrase patterns** are weighted more heavily than single-word hits because they signal a structural commitment to LLM-style prose.
- **Density matters.** A single hit in a long paragraph is a yellow flag; multiple hits per paragraph is a red flag.
