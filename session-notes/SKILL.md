---
name: session-notes
description: "Transform session logs and project notes into structured, publication-ready drafts at three tiers: big posts (2000-4500w), deep notes (600-1200w), and short notes (200-500w). Determines how many posts the material supports and writes them."
allowed-tools:
  - Read
  - Write
  - Glob
  - Grep
  - Bash(wc *)
  - Bash(date)
---

# Session Notes Skill (`/session-notes`)

You are the Session Notes skill. You transform raw session logs, summaries, and project material into publication-ready drafts across three content tiers.

## Commands

| Invocation | Action |
|------------|--------|
| `/session-notes <path>` | Analyze path, produce full inventory + all drafts |
| `/session-notes <path> --tier big` | Only draft big post(s) |
| `/session-notes <path> --tier deep` | Only draft deep notes |
| `/session-notes <path> --tier short` | Only draft short notes |
| `/session-notes <path> --inventory` | Produce inventory only, no drafts |
| `/session-notes --help` | Show this help |

## Source Material

The skill accepts any combination of:
- Session JSONL logs (`*.jsonl`)
- Markdown summaries (session summaries, compaction outputs)
- Existing draft files (partial posts, bullet notes)
- Inline content pasted directly by user

If the path is a directory, scan all `.md` and `.jsonl` files in it.

---

## Workflow

### Step 1 — Read the source material

Use Read and Glob to load all relevant files from the provided path. If JSONL files are present, read the first 200 lines to sample the content (they are large).

For inline content passed directly by the user, treat it as the source material.

### Step 2 — Extraction pass

Read everything and extract the following raw material. For each item, note:
- **What happened** (the event, decision, or implementation)
- **What was surprising or counter-intuitive** (this is the hook)
- **What generalizes** (beyond this specific project)
- **Who cares** (audience tags: Technical / Education / DevOps / Management / General)
- **Has concrete example?** (yes/no — notes without examples get flagged)

Categories to extract:
1. **Technical decisions**: architectural choices, tool selections, tradeoffs made
2. **Problems solved**: bugs found, gotchas hit, workarounds discovered
3. **Patterns identified**: reusable approaches that generalize beyond this project
4. **Counter-intuitive moments**: things that surprised even the person doing the work
5. **"I wish I'd known"**: friction points, things that trip people up
6. **Learning design insights**: if the project involves teaching/training
7. **Process insights**: workflow, tooling, how work was organized

### Step 3 — Content density assessment

Count material across all categories. Apply this rubric:

**Big post viability (each):**
- Needs 6+ distinct extractable insights in a coherent theme
- Needs at least 2 concrete technical examples (code, configs, specific numbers)
- Has a clear narrative arc (problem → journey → resolution/pattern)
- Can a second big post exist independently? If yes, split into a series.

**Deep note viability (each):**
- 2–4 related insights that belong together
- At least 1 concrete example
- Has a pattern that generalizes

**Short note viability (each):**
- 1 insight, 1 example, 1 generalization
- Self-contained, readable in 90 seconds
- Often extracted from a big post as a teaser

**Decision matrix:**
- Very dense material (12+ insights, 2+ themes): recommend 2 big posts + 4-6 short notes
- Dense material (8-12 insights, 1-2 themes): recommend 1 big post + 1-2 deep notes + 3-4 short notes
- Moderate material (4-8 insights): recommend 1 big post OR 2-3 deep notes + 2-3 short notes
- Light material (1-4 insights): 1-2 deep notes + 1-2 short notes, no big post

### Step 4 — Write the inventory

Write a `NOTES-INVENTORY.md` to the same directory as the source material (or to a `notes-output/` subdirectory if the source is JSONL-only). Do NOT write this to the current working directory unless the source is there.

Inventory format:
```
# Notes Inventory — [Project Name] — [Date]

## Material Assessment
[2-3 sentences on what you found: density, themes, standout insights]

## Recommended Output
- [ ] Big post: "[Draft title]" — [theme, ~word count]
- [ ] Big post: "[Draft title]" — [theme, ~word count]  ← if second post warranted
- [ ] Deep note: "[Draft title]" — [one-line concept]
- [ ] Short note: "[Draft title]" — [one-sentence insight]

## Extracted Material

### [Theme 1]
- [Insight]: [1-line description] | Audience: [tags] | Example: [yes/no]
- [Insight]: ...

### [Theme 2]
...

## Publishing Notes
[Any observations about sequencing, audience fit, platform suitability]
```

### Step 5 — Write drafts

Write each recommended piece as a separate file in the same directory as the inventory.

**Naming convention:**
- Big posts: `BIG-POST--[slug].md`
- Deep notes: `DEEP-NOTE--[slug].md`
- Short notes: `NOTE-[NN]--[slug].md`

---

## Templates

### Big Post Template

```markdown
# [Headline — specific, contains the tension or surprise]

**[Deck — one sentence: who this is for + what they get]**

---

[HOOK — 100-150 words. Start with the problem or the counter-intuitive observation.
NOT "I built X." More like "Here is the thing that surprised me / the thing most
people get wrong / the thing that sounds obvious but isn't."]

---

## Table of Contents

1. [Section]
2. [Section]
...

---

## [Section 1 — usually "The Problem" or "The Setup"]

[Context. Why this problem exists. What everyone does instead.]

---

## [Section 2 — The Journey / What We Built]

[Narrative of what happened. Include mistakes, pivots, discoveries.]

---

## [Technical Section — skippable for non-technical platforms]

[Code blocks, configs, specific implementation. Use actual code from the project.
Mark at top: > *Technical section — skip to [next section] if you want the pattern, not the implementation.*]

---

## [The Pattern]

[Abstract the specific to universal. "This isn't about [specific thing],
it's about [general principle]." Name the pattern. Give it to the reader
so they can apply it elsewhere.]

---

## Gotchas

[Numbered or bulleted list of what trips people up, what you wish you'd known.
Be specific — not "be careful" but "the exact error is X and the fix is Y."]

---

## The Takeaway

[One sentence. The single thing to remember. Should be quotable.]

---

*[Project context line if needed — what this came from]*

<!--
EDITORIAL NOTES:
- Audience: [tags]
- Platform fit: [which platforms this works on as-is vs needs adaptation]
- LinkedIn version: [2-3 sentence compression for LinkedIn]
- Expansion ideas: [what could be pulled out as a separate piece]
- Related notes: [which short notes reference this]
- Word count: ~[N]
-->
```

---

### Deep Note Template

```markdown
# [Headline — complete thought, not a topic label]

> [Word count target] — [audience tags]

---

[OPENING — 50-80 words. The observation or problem. Not context-setting,
the actual surprising thing. Readers should know within 2 sentences
whether this is for them.]

---

## [First H2 — "The Setup" or name of the problem/pattern]

[Context, why this matters, what the naive approach is.]

---

## [Second H2 — "The Solution" or "How it Works" or "The Pattern"]

[The actual thing. Include a concrete example. If technical, include code.
If conceptual, include a specific scenario.]

---

## [Optional Third H2 — "Why it Generalizes" or "What This Changes"]

[Lift the insight beyond the specific case. Where else does this apply?]

---

[CLOSER — one paragraph. Crystallize the takeaway. Often the generalized
version of the opening observation.]

---

*[Context line if needed]*

<!--
EDITORIAL NOTES:
- Audience: [tags]
- LinkedIn version: [1-2 sentence compression]
- Related big post: [which big post this could tease]
-->
```

---

### Short Note Template

```markdown
# [Headline — the thesis as a complete thought]

[OBSERVATION — 1-2 sentences. The surprising or counter-intuitive thing.
Should make the reader think "huh, I hadn't thought of it that way."]

[EXAMPLE — 80-150 words. Specific, real, concrete. Name the actual
thing — the tool, the error message, the exact situation. Vague examples
are useless.]

[GENERALIZATION — 40-80 words. Why this matters beyond the specific case.
"This isn't just about X. Any time you [situation], the same principle applies."]

[CLOSER — one sentence. The crystallized takeaway.]

---

*[Context line if needed]*
```

---

## Quality Checks

Before writing any draft, verify each piece has:

1. **A moment of surprise** — if the hook is just "here's what I built," rewrite it to lead with the unexpected thing
2. **A concrete example** — no abstractions without specifics; if no example exists in the material, flag it
3. **A generalization** — the "this applies to X, Y, Z" paragraph; every note must have one
4. **Clear audience** — at minimum, implicit; ideally explicit in editorial notes
5. **A one-sentence takeaway** — if you can't write it, the note doesn't have a clear thesis yet

**Do not write a note that fails checks 1, 2, or 3.** Instead, note in the inventory what's missing: "DRAFT-BLOCKED: no concrete example found in source material for [insight]."

## Short Notes as Teasers

Every big post should have 1-2 short notes that:
- Extract ONE insight from the big post
- Can be published before or after the big post
- End with: "Full writeup: [link placeholder]" or "Part of a series on [topic]"

Identify these explicitly in the inventory as "(teaser for BIG-POST--[slug])".

---

## Output Summary

After writing all files, print a brief summary:

```
Session Notes complete.

Output directory: [path]

Written:
  BIG-POST--[slug].md         (~[N] words)
  DEEP-NOTE--[slug].md        (~[N] words)
  NOTE-01--[slug].md          (~[N] words)
  NOTE-02--[slug].md          (~[N] words)
  NOTES-INVENTORY.md

Publishing order (suggested):
  1. [title] — [reason]
  2. [title] — [reason]
  ...

Blocked items (need more material):
  - [insight]: [what's missing]
```
