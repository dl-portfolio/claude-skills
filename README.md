# Claude Code Skills

A collection of security-hardened [Claude Code](https://claude.ai/claude-code) skills for developer workflows.

## Skills

| Skill | Command | Description |
|-------|---------|-------------|
| [Command Center](./command-center/) | `/cc` | GitHub Projects V2 board manager — create issues, track work, manage sprints |
| [Session Notes](./session-notes/) | `/session-notes` | Transform session logs and notes into publication-ready drafts — big posts, deep notes, and short notes |

## Installation

Each skill is a self-contained folder. Install by symlinking into `~/.claude/skills/`:

```bash
# Clone the full collection
git clone https://github.com/dl-portfolio/claude-skills.git ~/claude-skills --depth 1

# Symlink individual skills
ln -s ~/claude-skills/command-center ~/.claude/skills/command-center
ln -s ~/claude-skills/session-notes ~/.claude/skills/session-notes
```

After installation, restart Claude Code. Skills will appear as `/command` shortcuts.

## Security Philosophy

Every skill in this collection is audited against the same principles:

1. **No executable scripts** — Skills are prompt-only (SKILL.md). No `.sh`, `.py`, or other executable files.
2. **Pinned allowed-tools** — Each tool permission is individually specified. No broad wildcards.
3. **Prompt injection defense** — External content (issues, files) is treated as data, never instructions.
4. **File write boundaries** — Each skill declares exactly which files it can write to.
5. **Mutation allowlists** — API operations are limited to non-destructive actions.

Each skill includes a `SECURITY.md` with its full threat model, audit findings, and permission rationale.

### Hard vs soft boundaries

`allowed-tools` in the YAML frontmatter is a **hard boundary** — enforced by Claude Code. The AI cannot call tools not listed there.

Instructions in the prompt body (e.g. "never write to .bashrc") are **soft boundaries** — the AI follows them, but they can theoretically be bypassed by a sophisticated prompt injection. The skills in this collection use defense-in-depth: minimal hard permissions plus explicit soft instructions at every untrusted-data touchpoint.

## Contributing

To add a new skill:

1. Create a folder: `your-skill/SKILL.md` with YAML frontmatter
2. Include `SECURITY.md` with threat model, audit findings, and permission rationale
3. Include `README.md` with installation and usage
4. Follow the security principles above
5. Open a PR

## License

MIT — see individual skill directories for their licenses.
