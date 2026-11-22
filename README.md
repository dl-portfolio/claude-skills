# Claude Code Skills

Prompt-only [Claude Code](https://claude.ai/claude-code) skills for developer workflows, each shipped with its own threat model.

[![Licence: MIT](https://img.shields.io/badge/licence-MIT-blue.svg)](./LICENSE)

Each skill is a single `SKILL.md` with a pinned `allowed-tools` list and no executable scripts, plus a `SECURITY.md` that explains every permission it asks for and what it deliberately leaves out.

<!-- TODO: demo GIF -->

## Skills

| Skill | Command | Description |
|-------|---------|-------------|
| [Command Center](./command-center/) | `/cc` | GitHub Projects V2 board manager: create issues, track work, manage sprints |
| [Session Notes](./session-notes/) | `/session-notes` | Turns session logs and notes into publication-ready drafts: big posts, deep notes and short notes |

## Quick start

Each skill is a self-contained folder. Install by symlinking into `~/.claude/skills/`:

```bash
# Clone the full collection
git clone https://github.com/dl-portfolio/claude-skills.git ~/claude-skills --depth 1

# Symlink individual skills
ln -s ~/claude-skills/command-center ~/.claude/skills/command-center
ln -s ~/claude-skills/session-notes ~/.claude/skills/session-notes
```

Restart Claude Code after installing. The skills appear as `/cc` and `/session-notes`. See each skill's README for prerequisites and usage.

## How it works

A skill is a Markdown prompt with YAML frontmatter. Claude Code loads it when you type the command, and the frontmatter's `allowed-tools` list decides which tools the skill may call.

### Hard vs soft boundaries

`allowed-tools` in the YAML frontmatter is a **hard boundary**, enforced by Claude Code: the model cannot call tools that are not listed there.

Instructions in the prompt body (for example "never write to .bashrc") are **soft boundaries**: the model follows them, but a sufficiently sophisticated prompt injection could in theory get around them. The skills here use defence in depth: minimal hard permissions plus explicit soft instructions at every point where untrusted data comes in.

## Security notes

Every skill in this collection is checked against the same principles:

1. **No executable scripts.** Skills are prompt-only (`SKILL.md`). No `.sh`, `.py` or other executable files.
2. **Pinned `allowed-tools`.** Each tool permission is listed individually, down to the subcommand and, where the skill only ever needs one form, the exact flags (`git checkout -b *`, `git branch --show-current`). No broad wildcards such as `Bash(gh *)` or `Bash(git *)`. Patterns match a prefix, so extra flags can still be appended; each `SECURITY.md` says where that matters and which deny rules close it.
3. **Prompt injection defence.** External content (issues, files) is treated as data, never as instructions.
4. **File write boundaries.** Each skill states exactly which files it may write to.
5. **Mutation allowlists.** Where a skill calls an API, the prompt limits it to an allowlist of non-destructive operations. This is a soft boundary; see the skill's `SECURITY.md` for the residual risk.

Each skill's `SECURITY.md` covers its threat model, permission rationale, what is excluded and why, and how to verify it before installing:

- [command-center/SECURITY.md](./command-center/SECURITY.md)
- [session-notes/SECURITY.md](./session-notes/SECURITY.md)

### Vetting skills and plugins before you install them

These skills are written to be easy to review by hand. For third-party plugins, skills, MCP servers or CLI tools you have not written yourself, [plugin-audit](https://github.com/dl-portfolio/plugin-audit) is a companion tool by the same author: it downloads the target into a throwaway VM, scans it, runs it against decoy credentials with the network sinkholed, and produces a report with a verdict.

## Contributing

To add a new skill:

1. Create a folder: `your-skill/SKILL.md` with YAML frontmatter
2. Include `SECURITY.md` with threat model, audit findings and permission rationale
3. Include `README.md` with installation and usage
4. Follow the security principles above
5. Open a PR

## Licence

MIT. See [LICENSE](./LICENSE).
