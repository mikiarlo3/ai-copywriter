# Hebrew and right-to-left copy

Read this when the copy is in Hebrew, when it mixes Hebrew with Latin text or numbers, when it ships as interface strings in a right-to-left language, or when the user asks for Hebrew copy to be written rather than reviewed. `SKILL.md` carries the short version inside COPYWRITING MODE; this file is the working detail.

The skill's 33 patterns were observed in English text, and most of them describe how a model pads meaning rather than anything about English. Those transfer. A few describe English orthography and do not transfer at all. Hebrew is the case where that distinction is easiest to get wrong, because two of the characters involved are not punctuation, they are spelling, and because the largest failure in Hebrew copy is a choice the English rules never make you notice you are making.

## Contents

1. Which patterns change
2. Characters that look like punctuation but are spelling
3. Addressing a reader when the language forces a gender
4. Writing copy that survives the bidirectional algorithm
5. The Hebrew surface of the AI patterns
6. Length and budgets
7. Israeli locale conventions
8. What generalizes to other right-to-left languages
9. What not to flag
10. Sources

---

## 1. Which patterns change

Apply patterns 1 to 13, 15, 16, 18, and 20 to 33 normally. They describe padding, hedging, false depth, promotional inflation, and conversational residue, which are properties of how models generate text rather than properties of English. Section 5 gives the Hebrew surface forms so you can recognize them.

Four patterns need handling, and one needs a note.

| Pattern | In Hebrew | What to do |
| --- | --- | --- |
| §14 Em and en dashes | Hebrew uses `—` and `–` for asides much as English does, so the ban holds. But the maqaf `־` (U+05BE) joins words, and the ASCII hyphen attaches prefixes to Latin and numeric tokens | Apply §14 to `—` and `–` only. Never touch `־`, and never remove the hyphen in `ב-Dropbox` or `ל-2026` |
| §17 Title case in headings | Hebrew has no letter case, so there is nothing to lower | No-op on Hebrew spans. Latin spans inside a Hebrew heading still get §17 |
| §19 Curly quotation marks | Hebrew quotes text with the same double marks English does, so the rule is nearly harmless. The hazard is the homoglyph: gershayim `״` and geresh `׳` sit inside words | Normalize quotation marks only when the mark is quoting something. Section 2 covers the rest |
| §26 Hyphenated word pairs | Hebrew compounds through the construct state (smichut), not through hyphens, so there is no equivalent construction | No-op. Do not read `ב-Dropbox` as a hyphenated pair |
| §13 Passive voice | Hebrew marks passive morphologically through the binyanim (nif'al, pu'al, huf'al) rather than with an auxiliary verb | The preference for active voice holds. Detection is morphological, so scanning for an equivalent of "was" finds nothing |

The second half of the rule matters as much as the first: a mark the language requires carries no information about who wrote the text, because the writer had no choice. A Hebrew text full of maqafs and prefix hyphens is not evidence a model wrote it.

## 2. Characters that look like punctuation but are spelling

Three Hebrew characters are routinely mistaken for punctuation and normalized away. All three changes are misspellings, not style edits.

**Gershayim `״` (U+05F4).** Placed before the final letter of an acronym or abbreviation: `ד״ר` (doctor), `צה״ל` (the IDF), `ארה״ב` (the USA), `תנ״ך` (the Tanakh). It also marks Hebrew numerals, as in the year `תשפ״ו`. It is not a quotation mark, and it is not a curly quote that §19 should straighten. In practice most Hebrew typing substitutes the ASCII `"` here, which is typographically wrong but universal, so both forms appear in real copy. Leave whichever one the source uses. Changing it is never the highest-value edit and is sometimes a corruption.

**Geresh `׳` (U+05F3).** Marks consonants Hebrew has no letter for, which is how loanwords are written: `ג׳ינס` (jeans), `צ׳ק` (check), `ז׳אנר` (genre). It also ends single-word abbreviations: `פרופ׳`, `עמ׳`, `ד׳`. Same rule as gershayim, and the stakes are higher, because removing the geresh from `ג׳ינס` changes which consonant the reader sounds out.

**Maqaf `־` (U+05BE).** The Hebrew word-joiner, set at the height of the letter tops rather than the middle: `כָּל־הַיּוֹם`, `בֵּית־סֵפֶר`. It is not an en dash and §14 must not catch it. Modern non-liturgical copy uses it sparingly, so its presence usually signals a deliberate register rather than an error.

**Nikud (vowel points).** Ordinary modern Hebrew is written without them. Adding nikud to marketing copy shifts the register toward children's books, poetry, or liturgy, so do not add it. Preserve it wherever it already appears, since in those registers it is meaningful, and never strip it from quoted material.

## 3. Addressing a reader when the language forces a gender

This is the failure that produces no character to notice, which is why it survives every check built around detecting a mark.

Hebrew has no gender-neutral second person. Verbs, adjectives, and pronouns all inflect, so a button meaning "choose a file" has to be one of these:

| Form | Reads as | Cost |
| --- | --- | --- |
| `בחר קובץ` | Masculine singular | Addresses half the audience as though the other half is not there. It is the corpus default, so it is what a model produces when nobody decides |
| `בחרי קובץ` | Feminine singular | Same problem, mirrored. Some brands ship it deliberately as a stance |
| `בחרו קובץ` | Plural | Warm and standard in Israeli marketing. Formally addresses a group, which can read oddly in a one-user interface |
| `לבחור קובץ` | Infinitive | Genuinely neutral. Reads slightly systemic rather than personal, which suits interface labels better than it suits a headline |
| `בחירת קובץ` | Verbal noun | Neutral and compact. Names the action instead of asking for it, so it fits a label and not a call to action |
| `בחר/י קובץ` | Slash form | Unambiguous and inclusive, and reads like a government form. Common in official Israeli text, rare in copy that wants warmth |

Two consequences for the way this skill works.

**Raise it in the intake.** The ICP question already asks who exactly the copy is for. When the target language forces a gender or formality choice, that question has an extra half: how does this brand address one reader? Ask it in the same batch as the ICP, the category, and the story. Do not decide it silently, and do not decide it by defaulting, because defaulting is the decision most likely to be wrong.

**Sidestep before you slash.** When there is no answer available, prefer the infinitive or the verbal noun for interface labels and the plural for marketing copy. Reach for `בחר/י` only when the surface is formal enough to carry it. In embedded mode, where there is no user to ask, pick the neutral form and name the assumption next to the output.

This section generalizes. Any language that inflects for the addressee's gender, or that forces a formality choice English does not make, hits the same wall, and the same instruction applies: surface the choice during the intake rather than resolving it by default.

## 4. Writing copy that survives the bidirectional algorithm

Hebrew paragraphs run right to left, but embedded Latin text, numbers, and code stay left to right, and the final order is resolved by the Unicode bidirectional algorithm rather than by the order the characters were typed. Reviewing for this after the fact is the expensive way. Writing to avoid it costs nothing.

**Do not end a line with a Latin word or a number when punctuation follows.** In `הקובץ נשמר ב-Dropbox.` the period is a neutral character with no direction of its own. It takes the paragraph's direction and renders at the far left of the line, visibly detached from the word it belongs to. Rewrite so the sentence ends in Hebrew, or drop the period, which the microcopy rules already prefer for labels and buttons.

**Keep Latin and numeric tokens away from the edges.** A brand name, a version number, or a price at the start or end of a string is where the ordering gets decided by the surrounding neutrals. In the middle of a Hebrew clause, the same token is unambiguous.

**Do not hand-mirror brackets.** Parentheses, brackets, and quotation marks are mirrored by the algorithm at render time. Typing them reversed in the source produces text that is wrong twice.

**Do not paste invisible characters into copy.** When a string genuinely needs an isolate, the fix is `<bdi>`, CSS `unicode-bidi: isolate`, or the isolate characters in the string format, and that is an engineering change. Copy carrying stray marks breaks search, deduplication, and diffing, and it is invisible to the person maintaining it. Report the string and say what it needs instead.

**Numbers stay left to right.** `3 קבצים` reads with the digits in Latin order inside a Hebrew clause. This is correct and is not a finding.

Layout mirroring, icon direction, and progress-bar direction are design questions and sit outside this skill. Flag any string whose meaning depends on reading order and leave the layout to the designer.

## 5. The Hebrew surface of the AI patterns

The patterns transfer, but the words do not, so a scan for the English vocabulary in §4 and §7 finds nothing in Hebrew text that is obviously machine-written. These are the local forms. Treat the list the way §7 asks you to treat its own: a single hit means little, and a cluster is a confession.

| Skill pattern | Hebrew surface | The natural version |
| --- | --- | --- |
| §8 Copula avoidance | `מהווה`, `משמש כ־` | Hebrew present tense has no copula verb at all, so `X הוא Y` or plain `X Y` is the ordinary form. `מהווה` is a strong marker of officialese and of translated text, and it is §8 exactly |
| §7 AI vocabulary | `אשר` used as a relative pronoun throughout | `ש־`. `אשר` is correct and formal, and at volume it is the register of a legal notice rather than a person |
| §23 Filler phrases | `חשוב לציין כי`, `יש לציין כי`, `ראוי להדגיש` | Cut them. All three are direct calques of "it is important to note that" |
| §7 AI vocabulary | `בנוסף לכך`, `יתרה מזאת`, `זאת ועוד`, `כמו כן` stacked at paragraph openings | One connective, or none. Hebrew tolerates fewer of these than translated text assumes |
| §1 Significance inflation | `בעידן ה־`, `בעולם ה־`, `בעשור האחרון` as an opening move | Start with the claim |
| §4 Promotional language | `מגוון רחב של`, `חוויה`, `פתרון מתקדם`, `ברמה הגבוהה ביותר` | Name the thing. `פתרון` in particular has become the Hebrew equivalent of "solution" and carries as little meaning |
| §11 Synonym cycling | Alternating between a loanword and its Hebrew equivalent for one concept, such as `אפליקציה` and `יישום` | Pick one and repeat it. Hebrew tolerates repetition better than English does |

Two fluency signals are Hebrew-specific rather than pattern-specific, and both indicate translated text rather than written text:

**Definiteness in the construct state.** The article attaches to the second noun: `בית הספר`, never `הבית ספר`. Machine-translated Hebrew often gets this wrong when the English source has the article in front.

**English word order preserved.** Hebrew word order is flexible, and translated Hebrew tends to hold the source's subject-verb-object arrangement rigidly across every sentence. The sentences are grammatical and the paragraph reads translated. This is the Hebrew form of the source interference that fluency review looks for.

## 6. Length and budgets

The character budgets in the Short descriptions section of `SKILL.md` are English budgets, and Hebrew moves in both directions from them.

Running Hebrew prose is usually shorter than the English it came from. Vowels are not written, and prepositions, the definite article, and conjunctions attach to the following word as single letters rather than standing alone. A meta description translated from English generally has room left over, and the right response is to use it on a concrete detail rather than to pad.

Interface labels often go the other way, because the neutral forms in section 3 cost characters that the masculine singular does not: `בחר` is three letters and `לבחור` is five. A label that fits its control in English and in masculine singular Hebrew can overflow once the gender-neutral form is chosen. Check the label against the actual control rather than against an expansion percentage.

## 7. Israeli locale conventions

For he-IL, the conventions that differ from the English defaults and that a translator most often leaves untouched:

- Dates are day first: `25/07/2026` or `25.7.2026`. Never the month-first form.
- Time is 24 hour: `14:30`.
- The week starts on Sunday, and the standard working week runs Sunday to Thursday. Copy about business days, weekly reports, or scheduling breaks quietly if this is assumed away.
- Currency is the shekel, `₪` or `ILS`. The symbol commonly follows the amount in ordinary writing.
- Decimal and thousands separators match the English convention: `1,250.50`. Do not apply the European swap.

## 8. What generalizes to other right-to-left languages

Section 4 is about direction rather than about Hebrew, so all of it holds for Arabic, Persian, Urdu, and any other right-to-left script. Section 3 holds in shape for Arabic, which also inflects for the addressee's gender and adds a dual number, and for Persian, which does not inflect for gender but does force a formality choice.

Sections 2, 5, 6, and 7 are Hebrew. Arabic has its own set of characters that are structure rather than punctuation, its own translationese signature, and its own locale conventions, including a second set of digit forms, and none of them are covered here. When the target is Arabic, run the general language gate, use section 4, and state what you are unsure about rather than borrowing from this file.

## 9. What not to flag

The false positive guidance in `SKILL.md` applies and gains a few entries:

- **Gershayim, geresh, and the maqaf.** Repeated from section 2 because it is the most likely mistake. These are spelling.
- **The prefix hyphen before a Latin or numeric token.** `ב-Dropbox` and `ל-2026` are how Hebrew attaches a preposition to a word it cannot attach to directly. Not a §26 finding.
- **A masculine plural addressed to one reader.** `בחרו` is the ordinary inclusive form in Israeli copy, not an agreement error.
- **Untranslated product and interface terms.** Israeli technology copy keeps a high proportion of English terms in Latin script deliberately, and translating them into Hebrew equivalents nobody uses is the defect rather than the fix.
- **Repetition of a key noun.** Hebrew reads repetition as clarity more readily than English does, so the §11 instinct to vary is weaker here, not stronger.

## 10. Sources

- [Unicode Standard Annex #9, the Bidirectional Algorithm](https://www.unicode.org/reports/tr9/), the resolution order behind section 4.
- [W3C, Structural markup and right-to-left text in HTML](https://www.w3.org/International/questions/qa-html-dir), the `<bdi>` and `dir` guidance referenced in section 4.
- [Unicode CLDR, Hebrew locale data](https://cldr.unicode.org/), the source for the date, time, week, and number conventions in section 7.
- [Unicode Hebrew code chart, U+0590 to U+05FF](https://www.unicode.org/charts/PDF/U0590.pdf), the codepoints and general categories for the maqaf, geresh, and gershayim in section 2. The maqaf is listed there as `Pd`, the same category as the em dash and the en dash that §14 targets.
- [Hebrew punctuation](https://en.wikipedia.org/wiki/Hebrew_punctuation), an overview of how the marks in section 2 are used in running text.
