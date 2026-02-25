# Note Anatomy Reference

## Three Tiers

| Tier | Length | Purpose | Has ToC? |
|------|--------|---------|----------|
| Big post | 2000–4500w | Full narrative + technical depth, bookmarkable reference | Yes |
| Deep note | 600–1200w | One concept explored properly | No |
| Short note | 200–500w | One insight, one example, one generalization | No |

---

## Big Post — Required Sections (in order)

1. **Headline** — Specific, contains the tension or surprise. NOT "Building X." More like "Why the first version of X was a disaster" or "The thing everyone gets wrong about X."
2. **Deck** — One sentence: who this is for + what they get.
3. **Hook** (100-150w) — Lead with the counter-intuitive thing or the problem, NOT the solution. Reader should know within 30 seconds if this is for them.
4. **Table of Contents** — Required for 2000w+.
5. **The Problem / Setup** — Why this problem exists. What everyone does instead.
6. **The Journey** — What happened. Narrative form, not a list. Include the wrong turns.
7. **Technical Section** — Code, configs, specific implementation. Mark as skippable for non-technical readers.
8. **The Pattern** — Abstract the specific to universal. "This isn't about X, it's about Y." Name the pattern.
9. **Gotchas** — What trips people up. Specific, not vague.
10. **The Takeaway** — One sentence. Quotable.
11. **Editorial Notes** (HTML comment, not published) — Platform variants, expansion ideas, related notes, LinkedIn compression.

---

## Deep Note — Required Sections

1. **Headline** — Complete thought, not a topic label. "Deterministic tests first, LLM second" not "AI Code Review."
2. **Opening** — The observation or problem. Not context-setting — the actual surprising thing.
3. **H2: Setup** — Context and naive approach.
4. **H2: Solution / Pattern** — With a concrete example.
5. **H2: Generalization** (optional) — Where else does this apply?
6. **Closer** — One paragraph crystallizing the takeaway.
7. **Editorial Notes** (HTML comment) — Audience, LinkedIn version, related big post.

---

## Short Note — Required Elements

1. **Headline** — The thesis as a complete thought.
2. **Observation** — 1-2 sentences. The surprising or counter-intuitive thing.
3. **Example** — 80-150 words. Specific and real. Name the actual tool/error/situation.
4. **Generalization** — 40-80 words. Beyond the specific case.
5. **Closer** — One sentence. Crystallized takeaway.

---

## Required in EVERY note (all tiers)

- **Moment of surprise** — if the hook is just "here's what I built," rewrite it
- **Concrete example** — no abstractions without specifics
- **Generalization** — the "this applies to X, Y, Z" passage
- **One-sentence takeaway** — if you can't write it, the note doesn't have a clear thesis

---

## Content Density → Post Count

| Material | Recommended Output |
|----------|-------------------|
| 12+ insights, 2+ themes | 2 big posts + 4-6 short notes |
| 8-12 insights, 1-2 themes | 1 big post + 1-2 deep notes + 3-4 short notes |
| 4-8 insights | 1 big post OR 2-3 deep notes + 2-3 short notes |
| 1-4 insights | 1-2 deep notes + 1-2 short notes only |

---

## Short Notes as Teasers

Each big post should generate 1-2 short note teasers that:
- Extract ONE insight from the big post
- Can publish before or after the main post
- End with "Full writeup: [link]" or "Part of a series on [topic]"

Mark these in the inventory as "(teaser for BIG-POST--[slug])".

---

## Audience Tags

- **Technical** — engineers who will use or adapt the implementation
- **Education** — instructional designers, trainers, educators
- **DevOps** — infrastructure, automation, platform engineers
- **Management** — team leads, PMs, CTOs who want the insight not the implementation
- **General** — anyone building or learning something, no domain knowledge required

---

## Platform Adaptation

| Platform | Format | Max Length | Tone |
|----------|--------|-----------|------|
| Substack | Full post as-is | No limit | Personal, narrative |
| Dev.to / Hashnode | Add code blocks, expand technical sections | No limit | Technical |
| LinkedIn | Opening paragraph only + key insight | 200-300 words | Direct, practical |
| Twitter/X | Thread from gotchas or from short note | 10-15 tweets | Punchy |
| Hacker News | Technical section + pattern | Full post | Evidence-first, no hype |

Every draft should include a LinkedIn compression in the editorial notes.
