# AI Copywriter

by [Mickey Haslavsky](https://github.com/mikiarlo3)

A portable agent skill that does the two halves of the copy job most tools split apart: it writes copy that earns attention (clickbait titles, short descriptions, microcopy, subject lines), and it strips out every sign of AI-generated writing so the result reads like a person wrote it. It is plain Markdown, so it runs in any harness that supports skill-style instructions.

It is built on [blader's Humanizer](https://github.com/blader/humanizer), which packaged Wikipedia's "Signs of AI writing" guide into 33 detectable, fixable patterns. Those 33 patterns are all still here, unchanged. What this skill adds is the other direction: not just cleaning up prose after the fact, but writing headlines, product blurbs, and button labels that convert without tripping a single one of those patterns.

## Why both jobs in one skill

Ask a model for a headline and you get "Unlock the Ultimate Guide to Revolutionize Your Workflow." Ask it to tone that down and you get something so flat nobody clicks it. The two failure modes come from the same place: the model doesn't know that good copy and AI slop are opposites.

Copy that works is specific. "We cut our AWS bill by $40,000 in one afternoon" gets the click because the promise is concrete and checkable. "Game-changing cloud savings" gets scrolled past because the reader's filter deleted it before it registered. The humanizer rules aren't a constraint on the copywriting; they're most of what makes it good.

The skill also refuses to invent product facts. If the strongest headline needs a number, the number has to come from you. It will ask rather than make one up.

## Installation

### Skills CLI

Install globally with the cross-agent skills CLI so the skill is available in every project:

```bash
npx skills add mikiarlo3/ai-copywriter --global
```

Update an existing install:

```bash
npx skills update ai-copywriter --global
```

To install globally into every supported agent harness:

```bash
npx skills add mikiarlo3/ai-copywriter --global --agent '*'
```

Omit `--global` for a project-local install that can be committed and shared with collaborators. Start a new agent session or reload skills after installation.

### Claude Code plugin

Claude Code users can also install it as a plugin:

```
/plugin marketplace add mikiarlo3/ai-copywriter
/plugin install ai-copywriter@ai-copywriter
```

The skill is then invoked as `/ai-copywriter:ai-copywriter`.

### Manual

Any agent harness can use the skill directly because the runtime artifact is `SKILL.md`. Install it wherever your harness expects skill directories:

```bash
git clone https://github.com/mikiarlo3/ai-copywriter.git /path/to/your/skills/ai-copywriter
```

## Usage

### Writing copy

Ask for what you need and give it the raw material (the product, the audience, the facts). Titles come back as 5 to 10 variants across different angles, with a pick:

```
/ai-copywriter

Write titles for this blog post: [paste draft or summary]
```

```
Write the empty state, error message, and button copy for an invoicing
app's client list screen.
```

```
Meta description for this landing page, 155 characters:
[paste page copy]
```

### Humanizing text

Paste text and it comes back with the AI tells removed:

```
/ai-copywriter

Please humanize this text: [your text]
```

Point it at a file and it rewrites the prose in place:

```
Humanize the prose in docs/launch-post.md
```

### Voice calibration

To match your personal writing style, provide a sample of your own writing:

```
/ai-copywriter

Here's a sample of my writing for voice matching:
[paste 2-3 paragraphs of your own writing]

Now humanize this text:
[paste AI text to humanize]
```

The skill analyzes your sentence rhythm, word choices, and quirks, then applies them to the rewrite instead of producing generic "clean" output.

## What the copywriting mode covers

**Clickbait titles and headlines.** Specific promises, honest curiosity gaps, the reader's vocabulary instead of the industry's. Banned on sight: ultimate, game-changer, unlock, elevate, revolutionize, "you won't believe." You get variants across angles (number, question, contradiction, outcome, named enemy, how-to), not one take.

**Short descriptions.** App store blurbs, meta descriptions, one-liners. The first five words carry the benefit, one idea per description, and character budgets are respected by cutting ideas, not by compressing sentences into fragments.

**Microcopy.** Buttons name the result ("Send invoice," never "Submit"). Errors say what went wrong and how to fix it, without blaming the user. Empty states sell the first action. Destructive confirmations state the consequence.

**Subject lines and hooks.** Written to one person, payoff in the first 30 to 40 characters, no fake urgency and no fake familiarity.

## The humanizer engine

Based on [Wikipedia's "Signs of AI writing"](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing) guide, maintained by WikiProject AI Cleanup, via [blader/humanizer](https://github.com/blader/humanizer). Every rewrite runs a draft, then an "obviously AI generated" audit pass, then a second rewrite to catch lingering AI-isms.

Rewrites follow a no-fabrication rule: they never add facts, names, dates, or citations that aren't in the source text. Specificity has to come from the source or the author, not from the rewrite.

### 33 patterns detected

#### Content patterns

| # | Pattern | Before | After |
|---|---------|--------|-------|
| 1 | **Significance inflation** | "marking a pivotal moment in the evolution of..." | "was established in 1989 as part of a wider decentralization" |
| 2 | **Notability name-dropping** | "cited in NYT, BBC, FT, and The Hindu" | Trim the list; keep only sourced context |
| 3 | **Superficial -ing analyses** | "symbolizing... reflecting... showcasing..." | Remove, or keep only what the source supports |
| 4 | **Promotional language** | "nestled within the breathtaking region" | "is a town in the Gonder region" |
| 5 | **Vague attributions** | "Experts believe it plays a crucial role" | Name a real source or cut the claim |
| 6 | **Formulaic challenges** | "Despite challenges... continues to thrive" | Keep the sourced facts; cut the boosterism |

#### Language patterns

| # | Pattern | Before | After |
|---|---------|--------|-------|
| 7 | **AI vocabulary** | "Actually... additionally... testament... landscape... showcasing" | "also... remain common" |
| 8 | **Copula avoidance** | "serves as... features... boasts" | "is... has" |
| 9 | **Negative parallelisms / tailing negations** | "It's not just X, it's Y", "..., no guessing" | State the point directly |
| 10 | **Rule of three** | "innovation, inspiration, and insights" | Use natural number of items |
| 11 | **Synonym cycling** | "protagonist... main character... central figure... hero" | "protagonist" (repeat when clearest) |
| 12 | **False ranges** | "from the Big Bang to dark matter" | List topics directly |
| 13 | **Passive voice / subjectless fragments** | "No configuration file needed" | Name the actor when it helps clarity |

#### Style patterns

| # | Pattern | Before | After |
|---|---------|--------|-------|
| 14 | **Em/en dashes** | "institutions—not the people—yet this continues—" | Cut them: periods, commas, colons, or parentheses |
| 15 | **Boldface overuse** | "**OKRs**, **KPIs**, **BMC**" | "OKRs, KPIs, BMC" |
| 16 | **Inline-header lists** | "**Performance:** Performance improved" | Convert to prose |
| 17 | **Title Case Headings** | "Strategic Negotiations And Partnerships" | "Strategic negotiations and partnerships" |
| 18 | **Emojis** | "🚀 Launch Phase: 💡 Key Insight:" | Remove emojis |
| 19 | **Curly quotes** | `said “the project”` | `said "the project"` |
| 26 | **Hyphenated word pairs** | “cross-functional, data-driven, client-facing” | Drop hyphens on common word pairs |
| 27 | **Persuasive authority tropes** | "At its core, what matters is..." | State the point directly |
| 28 | **Signposting announcements** | "Let's dive in", "Here's what you need to know" | Start with the content |
| 29 | **Fragmented headers** | "## Performance" + "Speed matters." | Let the heading do the work |
| 30 | **Diff-anchored writing** | "This function was added to replace..." | Describe what it does, not what changed |
| 31 | **Manufactured punchlines / staccato drama** | "It had no preference. No prior. No nostalgia." | Use varied sentence lengths and concrete claims |
| 32 | **Aphorism formulas** | "Symmetry is the language of trust" | Replace the formula with the actual claim |
| 33 | **Conversational rhetorical openers** | "Honestly? It depends..." | Remove the fake-candid setup |

#### Communication patterns

| # | Pattern | Before | After |
|---|---------|--------|-------|
| 20 | **Chatbot artifacts** | "I hope this helps! Let me know if..." | Remove entirely |
| 21 | **Cutoff disclaimers** | "While details are limited in available sources..." | Find sources or remove |
| 22 | **Sycophantic tone** | "Great question! You're absolutely right!" | Respond directly |

#### Filler and hedging

| # | Pattern | Before | After |
|---|---------|--------|-------|
| 23 | **Filler phrases** | "In order to", "Due to the fact that" | "To", "Because" |
| 24 | **Excessive hedging** | "could potentially possibly" | "may" |
| 25 | **Generic conclusions** | "The future looks bright" | Specific plans or facts |

## Example: same product, both modes

Say you hand it a note-taking app for lawyers and ask for launch copy.

**What a raw model produces:**

> 🚀 Introducing CaseNotes: The Ultimate Game-Changing Solution for Legal Professionals! Unlock unprecedented productivity, elevate your practice, and revolutionize the way you capture, organize, and leverage your case notes — all in one seamless, intuitive platform.

**What this skill produces:**

> Headline options:
> 1. "Your case notes, searchable before you're back at your desk"
> 2. "Stop losing billable hours to notes you can't find"
> 3. "The note app that knows what a deposition is"
>
> Pick: #2. It names the cost in the reader's own units (billable hours), and the pain is one every lawyer has had this month.
>
> Meta description (148 chars): "CaseNotes turns your hearing and deposition notes into a searchable case file. Find any quote, date, or exhibit in seconds, from any device."
>
> Empty state: "Add your first case to start taking notes."

The second version sells harder than the first. It just doesn't sound like a press release fed through a blender.

## References

- [Wikipedia: Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing), the source of the pattern list
- [WikiProject AI Cleanup](https://en.wikipedia.org/wiki/Wikipedia:WikiProject_AI_Cleanup), the maintaining organization
- [blader/humanizer](https://github.com/blader/humanizer), the upstream skill this one extends (MIT)

## Version history

- **1.0.0** - First release of AI Copywriter. Forked from blader/humanizer v2.9.1 (all 33 patterns retained unchanged) and added COPYWRITING MODE: clickbait titles and headlines, short descriptions, microcopy, and subject lines, plus a copy-request invocation mode that delivers variants with a pick, a no-fabrication rule for product facts, and a billboard-test audit question for copy.

## License

MIT. Original humanizer copyright Siqi Chen; copywriting additions copyright Mickey Haslavsky.
