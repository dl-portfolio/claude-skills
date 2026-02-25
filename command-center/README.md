# Command Center (`/cc`)

A security-hardened Claude Code skill for managing GitHub Projects V2 boards directly from your terminal.

Create issues, manage project boards, track work, and maintain living project context — all through Claude Code's `/cc` command.

## Prerequisites

- [GitHub CLI](https://cli.github.com) (`gh`) installed and authenticated
- [Claude Code](https://claude.ai/claude-code) CLI
- GitHub token with `project` scope: `gh auth refresh -s project`

## Installation

```bash
# Clone the skills collection
git clone https://github.com/dl-portfolio/claude-skills.git ~/claude-skills --depth 1

# Symlink the command-center skill into Claude Code's skills directory
ln -s ~/claude-skills/command-center ~/.claude/skills/command-center
```

Or manually: copy the `command-center/` folder into `~/.claude/skills/`.

After installation, restart Claude Code. The `/cc` command will be available in all projects.

## Quick Start

```bash
# 1. Create a new project board
/cc init --title "My Project"

# 2. Connect your repo (creates labels, links to board)
/cc connect

# 3. Add your first issue
/cc add "Set up CI/CD pipeline" --priority high --type maintenance

# 4. Start working on it
/cc work 1

# 5. When done, create a PR
/cc done

# 6. Set up living project context
/cc context init

# 7. Check your dashboard
/cc status
```

## Commands

### `/cc init [--title "..."] [--owner ...]`

Create a new GitHub Projects V2 board with pre-configured fields (Status, Priority, Effort, Type, Project) and standard options. Automatically connects if you're in a git repo.

### `/cc connect [project-number]`

Wire the current repository to an existing project. Creates standard labels, links the repo, and writes local config. Works across owners (user projects + org repos).

### `/cc add "Title" [flags]`

Create an issue and add it to the board with field values set.

**Flags:**
- `--priority high|medium|low`
- `--effort small|medium|large`
- `--type feature|bug|research|maintenance`
- `--status backlog|todo|"in progress"|done`
- `--body "Description text"`

```bash
/cc add "Fix login redirect bug" --priority high --type bug --effort small
/cc add "Research caching strategies" --type research --effort medium --body "Compare Redis vs in-memory"
```

### `/cc list [filters]`

Query project items with field:value filters. Comma = OR within a field, space = AND across fields.

```bash
/cc list status:todo
/cc list status:todo priority:high
/cc list type:bug,feature status:"In Progress"
/cc list project:mission-hub
```

### `/cc work <N>`

Read the issue, create a feature branch (`feat/issue-N-title`), and set status to In Progress. Branch prefix auto-detected from labels (bug→`fix/`, maintenance→`chore/`).

### `/cc done [N]`

Push the current branch, create a PR linked to the issue, and set status to Done. Issue number is inferred from branch name if not provided.

### `/cc sync`

Bulk-add all open repository issues to the project board. Skips issues already present. Infers field values from labels.

### `/cc status`

Dashboard showing item counts by status, in-progress items with details, and high-priority todo items as "Up Next".

### `/cc import <file>`

Convert a TODO list, plan file, or markdown checklist into issues. Supports markdown checklists, numbered lists, and header-grouped items. Shows preview before creating.

### `/cc context [init|update "..."]`

Manage a pinned GitHub issue as living project documentation.

```bash
/cc context              # Read current context
/cc context init         # Create and pin a context issue
/cc context update "Switched to PostgreSQL for the database"
```

### `/cc help`

Show all commands with usage examples.

## Configuration

Command Center uses a two-tier config system:

- **Repo-level** `.cc-config.json` — project connection + cached field IDs (gitignored by default)
- **User-level** `~/.claude/cc-config.json` — default project, personal overrides

See `templates/cc-config.example.json` for the full schema.

### Cross-owner projects

GitHub Projects V2 can't directly link repos from different owners (e.g., a user project with org repos). Command Center handles this gracefully — the link step warns and continues, and issues are added via GraphQL mutations which work regardless of ownership.

## Security

This skill is designed with security as a first-class concern. Key measures:

- **No executable scripts** — all logic is in SKILL.md as prompt instructions, no `.sh` or `.py` files
- **Pinned `allowed-tools`** — each `gh` and `git` subcommand is individually allowlisted
- **Prompt injection defense** — issue bodies and imported files are treated as data, never as instructions
- **File write boundaries** — only writes to `.cc-config.json` (repo) and `~/.claude/cc-config.json` (user)
- **GraphQL mutation allowlist** — only non-destructive mutations permitted

See [SECURITY.md](./SECURITY.md) for the full threat model, permission rationale, and audit checklist.

### Before installing any Claude Code skill

1. Read the `allowed-tools` in `SKILL.md` frontmatter — this is what the skill can do
2. Check for executable scripts (`.sh`, `.py`) — there should be none
3. Search for `source`, `eval`, `curl`, `wget` — these should not appear in allowed-tools
4. Verify the repository source

## License

MIT — see [LICENSE](./LICENSE).
