# Security Model

This document describes the threat model, permission rationale, and audit guidelines for the Command Center skill.

## Threat Model

### Attackers

| Actor | Vector | Goal |
|-------|--------|------|
| **Malicious fork author** | Modified SKILL.md, added scripts | Execute arbitrary code on user's machine |
| **Issue author** | Prompt injection in issue body/title | Trick Claude into running unintended commands |
| **PR author** | Injection in PR description | Same as issue author |
| **Supply chain** | Compromised upstream repo | Distribute malicious skill update |

### Targets

| Target | Impact |
|--------|--------|
| GitHub OAuth token | Full account takeover |
| SSH keys | Access to all repos |
| Local files | Data theft, code modification |
| GitHub account | Repo deletion, secret modification |
| CI/CD pipelines | Supply chain attack |

## Permission Rationale

### Why each `allowed-tools` entry exists

**GitHub CLI (specific subcommands):**
- `gh api graphql *` — Required for all Projects V2 operations (no REST equivalent for custom fields)
- `gh issue create/view/edit/list/pin/close` — Core issue management
- `gh label create` — Label setup during connect
- `gh project create/field-create/field-list/item-list/list` — Project board management
- `gh pr create` — PR creation in `/cc done`
- `gh auth status` — Pre-flight auth check (read-only, no token exposure)
- `gh repo view` — Get repo metadata for linking

**Git (specific subcommands):**
- `git checkout/branch` — Branch creation in `/cc work`
- `git push -u/origin` — Push in `/cc done` (pinned to specific prefixes)
- `git pull` — Sync before branching
- `git status/log/diff` — Read-only state inspection
- `git rev-parse/symbolic-ref` — Detect repo root, current branch
- `git stash` — Suggested recovery when working tree is dirty

**Utilities (read-only):**
- `jq` — Parse JSON output from `gh` commands
- `ls/head/tail/wc/sort/basename/dirname/test/[/tr/cut` — Safe read-only file inspection

**Claude tools:**
- `Read` — Read reference files, configs, user files for import
- `Write` — Write config files (bounded to two paths in SKILL.md)
- `Grep/Glob` — Search for files and content

### What's explicitly excluded and why

| Excluded | Risk |
|----------|------|
| `source`, `eval`, `bash -c` | Arbitrary code execution |
| `sed`, `awk` | Shell escapes via `e` flag / `system()` |
| `echo`, `printf` | File writes via `>` / `>>` redirection |
| `curl`, `wget` | Data exfiltration, arbitrary downloads |
| `mktemp` | Staging area for multi-step attacks |
| `gh gist create` | Primary data exfiltration vector |
| `gh auth token` | Raw OAuth token exposure |
| `gh repo delete` | Destructive |
| `gh ssh-key add` | Can add attacker's SSH keys |
| `gh secret set` | Can overwrite repository secrets |
| `gh workflow run` | Can trigger arbitrary CI/CD |
| `git push --force` | Destructive history rewrite |
| `git reset --hard` | Destroys uncommitted work |
| `git config` | Can modify hooks, remotes, aliases |
| `Edit` tool | `Write` is sufficient; Edit adds surface area |

## Known Risks

These risks exist in the current design and cannot be fully mitigated:

1. **Write tool is unbounded**: Claude Code's Write tool can write to any path. SKILL.md constrains this via prompt instructions, but this is not a hard boundary. A prompt injection attack could theoretically bypass it.

2. **GraphQL mutation surface**: `gh api graphql` can execute any GraphQL mutation. SKILL.md constrains to an allowlist, but this is also prompt-enforced. The mitigation: the `gh` token's scopes limit what mutations can actually succeed.

3. **Token scope creep**: If the user's `gh` token has broad scopes (e.g., `admin:org`), the skill could theoretically be tricked into destructive org-level operations. Mitigation: use minimum necessary scopes (`repo`, `project`).

4. **Issue content injection**: While SKILL.md includes explicit injection defense instructions, a sophisticated attack embedded in issue content could theoretically influence Claude's behavior. Mitigation: always display issue content as quoted data, never process it as instructions.

## Verification

Before installing this skill (or after updating it), verify authenticity:

1. **Check the source**: Only install from the canonical repository.
2. **Review SKILL.md**: The `allowed-tools` frontmatter is the most critical section. Verify no wildcards for `gh`, `git`, or shell commands that enable code execution.
3. **Check for scripts**: There should be NO `.sh`, `.bash`, `.py`, or other executable files. All logic lives in SKILL.md as prompt instructions.
4. **Verify no `source` or `eval`**: Search the entire skill directory for these keywords.
5. **Review recent commits**: `git log --oneline -20` — look for suspicious changes to `allowed-tools` or addition of script files.

## For Forkers

If you fork this skill, **DO NOT** add any of the following to `allowed-tools`:

- `Bash(source *)` or `Bash(eval *)` or `Bash(bash -c *)`
- `Bash(curl *)` or `Bash(wget *)`
- `Bash(gh gist *)` or `Bash(gh auth token)`
- `Bash(gh repo delete *)` or `Bash(gh secret *)`
- `Bash(sed *)` or `Bash(awk *)`
- `Bash(echo *)` or `Bash(printf *)`
- Any broad wildcard like `Bash(gh *)` or `Bash(git *)`

If you need additional `gh` subcommands, add them specifically (e.g., `Bash(gh issue comment *)`).

## Reporting

If you find a security vulnerability in this skill, please report it by opening a security advisory on the repository. Do not open a public issue for security vulnerabilities.
