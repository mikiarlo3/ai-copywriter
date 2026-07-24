# AGENTS.md

Guidance for AI coding agents (Claude Code, Codex, Warp, etc.) working in this repository.

## What this repo is

A portable agent skill implemented entirely as Markdown. The runtime artifact is `SKILL.md`: the agent reads its YAML frontmatter and prompt. There is no build step, and the repo should avoid wording that limits support to one or two harnesses.

The skill has two halves: COPYWRITING MODE (clickbait titles, short descriptions, microcopy, subject lines) and the humanizer engine (33 numbered AI-writing patterns forked unchanged from blader/humanizer v2.9.1). The copywriting sections depend on the patterns; don't edit one side without checking the other still makes sense.

## Key files

- `SKILL.md` — the skill itself. Portable YAML frontmatter (`name`, `description`, `license`, `metadata.version`) followed by the copywriting mode and the canonical, numbered pattern list with before/after examples. **This is the source of truth.**
- `README.md` — for humans: installation, usage, the copywriting mode summary, a summary table of the patterns, and a version history.
- `.claude-plugin/plugin.json` — optional Claude Code plugin manifest.
- `.claude-plugin/marketplace.json` — optional single-repo marketplace entry so `/plugin marketplace add mikiarlo3/ai-copywriter` works.
- `scripts/validate-package.py` — dependency-free package and synchronization checks used locally and in CI.

## The maintenance contract

`SKILL.md` and `README.md` must stay in sync. When you change behavior or content:

- **Patterns:** the skill currently defines **33 numbered patterns**. If you add, remove, or renumber any, update the README pattern table, the validator's expected range, and every cross-reference in the same change. Keep numbering stable unless you are deliberately renumbering.
- **Version:** `SKILL.md` frontmatter stores the version under `metadata.version`, `README.md` has a "Version history" section, and `.claude-plugin/plugin.json` has a `version` field. Bump them together so package metadata matches the skill. Keep the skill version under `metadata`; a top-level `version` key is not portable across Agent Skills hosts. (`marketplace.json` intentionally omits a version so `plugin.json` stays the package source of truth.)
- **Compatibility:** keep install and usage language harness-neutral. The skill should work in any agent harness that can load Markdown skill instructions; Claude Code, OpenCode, Codex, and other harnesses are examples, not limits.
- **Validation:** run `python3 scripts/validate-package.py`, `npx skills add . --list`, and `claude plugin validate .` before publishing.
- **Non-obvious fixes:** if you change the prompt to handle a tricky failure mode (a repeated mis-edit, an unexpected tone shift), add a short note to the README version history explaining what was fixed and why.

## Editing SKILL.md

- Preserve valid YAML frontmatter (formatting and indentation).
- The prompt below the frontmatter is the product. Edit it like a careful instruction document, not code.
- The copywriting sections must themselves pass the skill's own rules: no em dashes, no banned hype vocabulary outside of "banned words" lists, sentence-case headings in examples.
