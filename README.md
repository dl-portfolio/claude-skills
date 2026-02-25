# Claude Skills

A collection of security-hardened [Claude Code](https://claude.ai/claude-code) skills for developer workflows.

## Skills

| Skill | Command | Description |
|-------|---------|-------------|
| [Command Center](./command-center/) | `/cc` | GitHub Projects V2 board manager — create issues, track work, manage sprints |

## Installation

Each skill is a self-contained folder. Install by copying into `~/.claude/skills/`:

```bash
# Clone the full collection
git clone https://github.com/dl-portfolio/claude-skills.git ~/Projects/claude-skills

# Symlink a specific skill
ln -s ~/Projects/claude-skills/command-center ~/.claude/skills/command-center
```

Or install a single skill directly:

```bash
# Sparse checkout for just one skill
git clone https://github.com/dl-portfolio/claude-skills.git ~/.claude/skills/command-center \
  --branch main --depth 1 --no-checkout && \
cd ~/.claude/skills/command-center && \
git sparse-checkout set command-center && \
git checkout
```

After installation, restart Claude Code. Skills will appear as `/command` shortcuts.

## Security Philosophy

Every skill in this collection follows these principles:

1. **No executable scripts** — Skills are prompt-only (SKILL.md). No `.sh`, `.py`, or other executable files.
2. **Pinned allowed-tools** — Each tool permission is individually specified. No broad wildcards.
3. **Prompt injection defense** — External content (issues, files) is treated as data, never instructions.
4. **File write boundaries** — Each skill declares exactly which files it can write to.
5. **Mutation allowlists** — API operations are limited to non-destructive actions.

See each skill's `SECURITY.md` for its full threat model and audit checklist.

## Contributing

To add a new skill:

1. Create a folder: `your-skill/SKILL.md` with YAML frontmatter
2. Include `SECURITY.md` with threat model and permission rationale
3. Include `README.md` with installation and usage
4. Follow the security principles above
5. Open a PR

## License

MIT — see individual skill directories for their licenses.
