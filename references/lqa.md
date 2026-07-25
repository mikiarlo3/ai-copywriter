# Linguistic quality assurance (LQA)

Read this when the skill is asked to review copy that is not in English, copy that was translated, or copy that ships as interface strings. LQA MODE in `SKILL.md` is the short version; this file is the procedure.

The skill's 33 patterns were written from observations of English text. Most of them describe how a model pads meaning, and those travel to any language. A handful describe English orthography, and those do not travel at all. Applying the second group outside English does not clean the copy, it breaks it. Everything below exists to prevent that, and then to give the review some structure once it is safe to run.

## Contents

1. The language gate
2. Which patterns are English-bound
3. Severity
4. The five checks
5. Interface strings
6. Report format
7. What not to flag
8. Sources

---

## 1. The language gate

Before any pattern is applied to any text, answer three questions and write the answers down:

1. **What language is the source in?** If there is no source and the copy was written natively, say so. Translated copy and original copy fail in different ways, and the accuracy check in section 4 only applies to the first.
2. **What language is the target in?** Detect it from the text. Do not infer it from the user's language, from the product's home market, or from the request being written in English.
3. **Is the target language English?** If yes, skip section 2 and run the skill normally. If no, section 2 governs before anything else happens.

When the text mixes languages, treat each span as its own target. A Hebrew landing page with an English product name and an English call to action has three targets, and the English spans still get the full English treatment.

If the target language cannot be identified with confidence, stop and ask. Guessing wrong here corrupts every downstream check.

## 2. Which patterns are English-bound

Four patterns encode English orthographic convention rather than a property of machine-generated text:

| Pattern | What it assumes | Why that assumption is local |
| --- | --- | --- |
| §14 Em and en dashes | The dash is a stylistic choice a writer made | In several languages the dash is required punctuation, not decoration |
| §17 Title case in headings | Capitalization signals emphasis | Some languages capitalize by grammatical rule, others have no letter case at all |
| §19 Curly quotation marks | Straight quotes are the neutral default | Most languages have their own quotation marks, and the straight ASCII pair is the foreign one |
| §26 Hyphenated word pairs | Compounds are joined with hyphens, variably | Compounding rules are language specific, and in some languages compounds are written as one word |

**The rule, stated so it works for languages nobody thought about:**

> Before applying §14, §17, §19, or §26 to text in a language other than English, state what the target language's convention is for that mark or form. Then follow the target language's convention, not English's. If the mark is grammatically required in the target language, the pattern does not apply, and the presence of that mark is not evidence of machine authorship.

The second half of that rule matters as much as the first. These patterns do double duty in the skill: they are edit instructions and they are detection signals. A mark that a language requires carries no information about who wrote the text, because the writer had no choice. Counting it as a tell produces false positives on correct human writing, which section 7 already warns about in the English case.

**Worked example.** Russian marks an omitted present-tense copula with a dash: *Москва — столица России*, literally "Moscow, capital of Russia", where the dash stands in for the missing verb. §14 instructs replacing the dash with a period, comma, colon, or parentheses. The comma version turns the sentence into a noun phrase with an appositive and it is no longer a sentence. Removing the dash outright is ungrammatical. The dash is carrying the verb, so §14 does not apply, and a Russian text full of dashes tells you nothing about whether a model wrote it.

Do not build a table of languages. The model running this skill already knows the conventions; what it fails to do is remember to ask. The gate in section 1 plus the rule above is the whole mechanism.

**Everything else transfers.** Patterns 1 through 13, 15, 16, 18, and 20 through 33 describe padding, hedging, false depth, promotional inflation, and conversational residue. Those are properties of how models generate text, not of English. Apply them normally, and expect the surface forms to differ: the equivalent of a "vibrant tapestry" in the target language will not be a literal translation of it, so look for the function rather than the listed words.

## 3. Severity

Grade every finding. An ungraded list of problems gives the reader no way to decide what to fix before shipping.

- **Critical.** The copy is wrong in a way that misleads, breaks the product, or creates legal or safety exposure. A wrong price, a wrong currency, a wrong dosage, a reversed instruction, a truncated string that changes the meaning of a button, a broken placeholder that shows raw code to the user. Ship-blocking.
- **Major.** The reader understands the copy but the copy is visibly wrong: a grammatical error, an inconsistent product term, a register that clashes with the brand, a locale convention applied from the wrong locale. Damages trust, does not break function.
- **Minor.** The copy is correct and would read better changed. Awkward phrasing, a stiffer word than needed, punctuation that is defensible but unusual.

Two findings of different severity in one sentence are two findings. Do not average them.

If the review produces no Critical or Major findings, say so in one line rather than promoting Minor findings to fill space.

## 4. The five checks

This follows the shape of MQM, the framework localization vendors grade against, reduced to what a copywriting skill can actually assess from text.

**Accuracy.** Only when there is a source text. Does the target say what the source says? Look for meaning that changed, content that was dropped, content that was added, and spans left untranslated. Numbers, units, currencies, dates, and names are where this fails most often and matters most: a number that survived translation unchanged is not automatically correct, since the figure itself may be locale dependent.

**Fluency.** Does the target read as correct in its own language, judged on its own terms and not as a reflection of the source? Grammar, agreement, spelling, punctuation. The specific failure to watch for is source interference: syntax that is legal in the target language but arranged the way the source language arranges it. Translated copy usually reads translated because of word order and connective choice, not vocabulary.

**Terminology.** Is the same thing called the same thing everywhere? Pick the product's nouns and check each one across every string. Two words for one feature is a Major finding even when both words are correct in isolation. If the user has a glossary, check against it; if not, build the list from the copy itself and report the collisions.

**Locale conventions.** Formats that change by locale rather than by language: date order, decimal and thousands separators, currency placement and symbol, time notation, units of measurement, phone and address shape, name order, sort order. These are the findings a reader notices instantly and a translator often misses, because they live in the parts of a string that look like they need no translation.

**Style and register.** Does the copy address the reader the way the brand intends and the language allows? Formality is grammatical in many languages rather than a matter of word choice, so the question is not "is the tone friendly" but "which form of address is this text using, and is it the same one used everywhere else?" Inconsistent formality across a product is a Major finding.

## 5. Interface strings

Everything above applies to prose. Copy that ships inside an application has failure modes that prose does not, and the Microcopy and Short descriptions sections of `SKILL.md` set conventions and character budgets that quietly assume English. Run these checks whenever the copy is destined for a user interface.

**Placeholder integrity.** Strings carry variables: `{name}`, `%s`, `{{count}}`, `<b>`. Every placeholder in the source must survive into the target, spelled identically, with nothing translated inside the delimiters. Translating the word inside a placeholder is the single most common way localized software breaks, and it is always Critical, because it either crashes or prints raw markup to the user. Placeholder order may change, and that is fine when the format supports named or positional arguments; check that the format does.

**Grammatical agreement around variables.** A string that reads correctly in English can be impossible in the target language once a variable is substituted, because the words around the variable have to agree with it.

- *Number.* English has two plural forms. Other languages have anywhere from one to six, and the boundaries are not intuitive: several Slavic languages need distinct forms for quantities ending in 1, in 2 through 4, and in 5 through 20. A string built as "You have %d file" plus an "s" cannot be translated correctly no matter who translates it. Flag the construction, not the translation.
- *Gender.* Where the variable is a person or a noun with grammatical gender, the verb, adjective, or article around it may have to agree. "Welcome, {name}" is safe in English and needs a gendered verb form in many languages. If the application does not know the user's gender, the string has to be rewritten to avoid the agreement, not translated around it.
- *Case.* In languages with grammatical case, a name or noun dropped into a sentence has to take the case the sentence requires. Concatenating a nominative form into a prepositional slot produces text that is visibly wrong to every native reader.

The finding here is usually about the string's construction rather than its wording, so report it as such and say what shape the string needs instead.

**Length and layout.** Translated strings rarely match source length, and short strings vary proportionally more than long ones: a one word button label can double or triple, while a paragraph moves much less. Do not apply an average expansion percentage. Instead, check the actual target length against the budget the surface allows, and treat a label that no longer fits its control as Critical when truncation changes the meaning and Major when it merely looks bad. The character budgets in the Short descriptions section of `SKILL.md` are English budgets: a 30 character app store subtitle is a different constraint in every language, and meeting it may require cutting an idea rather than compressing a phrase.

**Bidirectional text.** When the target language is written right to left, the paragraph direction flips but embedded Latin text, numbers, and code stay left to right, and the ordering is resolved by the Unicode bidirectional algorithm rather than by the order the characters were typed. Watch for punctuation landing on the wrong end of a line, parentheses and brackets appearing reversed, and mixed strings whose pieces read in an order nobody intended. Layout mirroring is a design question and outside this skill, but flag any string whose meaning depends on reading order.

**Case transformations.** Interface code that uppercases or lowercases a string for display can corrupt it: at least one widely used writing system distinguishes dotted and dotless letters whose case mappings do not round trip, and several languages have letters whose uppercase form is two characters. If the copy will be transformed by the interface rather than shipped as written, say so as a finding.

## 6. Report format

Deliver findings as a list, ordered Critical first, then Major, then Minor. For each one give the string or line, the severity, the check it failed, and the fix. One line of explanation, not a paragraph.

```
CRITICAL  Accuracy         "…" → source says 100 EUR, target says 100 USD.
CRITICAL  Placeholders     "…" → {userName} translated to {имяПользователя}, will not resolve.
MAJOR     Terminology      "…" → "subscription" rendered two different ways across screens.
MAJOR     Locale           "…" → date written 03/04/2026, ambiguous in this locale, use the local order.
MINOR     Fluency          "…" → grammatical, but the clause order follows the source.
```

Close with the target language you detected, the source language if there was one, and the count at each severity. If a finding depends on information you do not have, such as a glossary or the width of a control, name the assumption instead of guessing.

Then apply the skill's normal loop to whatever you rewrite, subject to section 2.

## 7. What not to flag

The false positive guidance in `SKILL.md` applies here and gains a few entries:

- **A required mark.** Covered in section 2, repeated because it is the most likely mistake: punctuation the target language requires is not a tell and is not an error.
- **A different rhythm.** Sentence length norms, paragraph length norms, and how much repetition reads as natural all vary by language. Prose that would seem long-winded in English may be ordinary in the target.
- **Formality that differs from the source.** Many languages force a formality choice English does not make. A target text that is more formal than its source is not automatically wrong; it is only wrong if it is inconsistent with the rest of the product.
- **A translation that is not literal.** Copy is supposed to be adapted rather than transcribed. Judge whether the target achieves what the source was trying to do. A headline that mirrors the source word for word and lands flat is the defect, not the fix.
- **Untranslated proper nouns.** Product names, brand names, and interface terms are often deliberately left in the source language.

## 8. Sources

- [Multidimensional Quality Metrics (MQM)](https://themqm.org/), the error typology and severity model that section 3 and section 4 follow in reduced form.
- [Unicode CLDR plural rules](https://cldr.unicode.org/index/cldr-spec/plural-rules), the definition of the plural categories referenced in section 5.
- [Unicode Standard Annex #9, the Bidirectional Algorithm](https://www.unicode.org/reports/tr9/), the resolution order behind the bidirectional findings in section 5.
- [ICU MessageFormat](https://unicode-org.github.io/icu/userguide/format_parse/messages/), the string format that makes plural and gender agreement expressible rather than concatenated.
