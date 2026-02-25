# Session Notes (`/session-notes`)

A skill that transforms session logs, project notes, and raw summaries into publication-ready drafts across three content tiers — automatically determining how much material exists and how many posts it supports.

## What It Does

Point it at a folder of session logs or notes. It reads everything, extracts the insights, determines content density, and writes structured drafts. One run produces an inventory plus all the drafts ranked and ready for editing.

**Three output tiers:**

| Tier | Length | When used |
|------|--------|-----------|
| Big post | 2000–4500w | Full narrative + technical depth, bookmarkable reference |
| Deep note | 600–1200w | One concept explored properly with context and examples |
| Short note | 200–500w | One insight, one example, one generalization |

## Installation

```bash
# Clone the collection
git clone https://github.com/dl-portfolio/claude-skills.git ~/claude-skills --depth 1

# Symlink into your skills directory
ln -s ~/claude-skills/session-notes ~/.claude/skills/session-notes
```

Restart your AI coding assistant. The `/session-notes` command will be available in all projects.

## Usage

```bash
# Full run — inventory + all drafts
/session-notes /path/to/notes-folder

# Just the big post(s)
/session-notes /path/to/notes-folder --tier big

# Just short notes
/session-notes /path/to/notes-folder --tier short

# Inventory only — see what you have before committing to drafts
/session-notes /path/to/notes-folder --inventory

# Deep notes only
/session-notes /path/to/notes-folder --tier deep
```

## Source Material

Accepts any combination of:
- Session JSONL logs (`*.jsonl`)
- Markdown summaries and compaction outputs
- Existing partial drafts or bullet notes
- Inline content pasted directly into the conversation

If the path is a directory, it scans all `.md` and `.jsonl` files in it.

## Output

All files are written to the source directory (or a `notes-output/` subdirectory for JSONL sources).

**File naming:**
```
NOTES-INVENTORY.md          — content assessment + recommended post plan
BIG-POST--[slug].md         — long-form post with ToC
DEEP-NOTE--[slug].md        — focused deep note
NOTE-01--[slug].md          — short note
NOTE-02--[slug].md
...
```

### What the inventory contains

- Material assessment (density, themes, standout insights)
- Recommended post list with titles and word count estimates
- All extracted insights tagged by audience and whether they have a concrete example
- Publishing order suggestions
- Blocked items (insights that lack examples and can't be drafted yet)

### What every draft contains

Every note at every tier is required to have:
- A moment of surprise or counter-intuition (the hook)
- A concrete example (specific, real — not abstract)
- A generalization paragraph (why this applies beyond the specific case)
- A one-sentence takeaway

Drafts that can't meet these requirements are flagged in the inventory as `DRAFT-BLOCKED` with a note on what's missing.

Each draft also includes an HTML comment with editorial notes: audience tags, LinkedIn compression, platform fit, and expansion ideas. Not published — for the author's reference.

## How Content Density Maps to Posts

| Material | Output |
|----------|--------|
| 12+ insights, 2+ themes | 2 big posts + 4–6 short notes |
| 8–12 insights, 1–2 themes | 1 big post + 1–2 deep notes + 3–4 short notes |
| 4–8 insights | 1 big post OR 2–3 deep notes + 2–3 short notes |
| 1–4 insights | 1–2 deep notes + 1–2 short notes only |

When two big posts are warranted, the skill splits them as a series — each independently readable, with the second referencing the first.

Short notes are also identified as potential teasers for big posts: publish before the main piece to build interest, end with a "full writeup" link.

## Security

This skill reads and writes local files only. It makes no network calls, runs no shell commands beyond `wc`, and has no access to git, GitHub CLI, or any credentials.

See [SECURITY.md](./SECURITY.md) for the full permission model.

## License

MIT — see [LICENSE](./LICENSE).
