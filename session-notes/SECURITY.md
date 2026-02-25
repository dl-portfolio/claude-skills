# Security Model

This document describes the threat model, permission rationale, and audit findings for the Session Notes skill.

## Threat Surface

Session Notes has a minimal threat surface. It reads files and writes files. No network calls, no git operations, no credentials.

### Attackers

| Actor | Vector | Goal |
|-------|--------|------|
| **Malicious fork author** | Modified SKILL.md | Add network calls or writes to sensitive paths |
| **Injected content author** | Prompt injection in source files | Trick the skill into writing to unintended locations |

### Targets

| Target | Impact |
|--------|--------|
| Local files | Writing to sensitive paths (shell profiles, SSH config, etc.) |

No credentials, tokens, GitHub access, or external APIs are involved.

## Security Audit

### Findings and Resolutions

**HIGH — `find` with trailing `*` allows `-exec` and `-delete`** ✅ Fixed

The original patterns `Bash(find * -name "*.md" *)` and `Bash(find * -name "*.jsonl" *)` had a trailing wildcard that matched any additional arguments, including:
- `-exec rm {} \;` — arbitrary file deletion
- `-delete` — delete all matched files
- `-exec sh -c 'payload' {} \;` — arbitrary shell execution

**Resolution**: Removed `find` entirely. The `Glob` built-in tool handles file discovery without any shell involvement.

---

**MEDIUM — `Bash(ls *)` allows listing sensitive directories** ✅ Fixed

`ls ~/.ssh`, `ls ~/.aws`, `ls ~/` are all valid matches for `Bash(ls *)`. While read-only, this is broader than needed.

**Resolution**: Removed `Bash(ls *)`. `Glob` replaces all directory inspection needs.

---

**LOW — `Bash(date *)` allows arbitrary date format flags** ✅ Fixed

The `*` wildcard allowed any flags, including unusual ones. For the skill's purpose (adding a date to the inventory header), no flags are needed.

**Resolution**: Narrowed to `Bash(date)` — no arguments, no wildcards.

---

**INFO — `Bash(wc *)` is fully wildcarded** — Accepted risk

`wc` with any flags and any path is allowed. This permits `wc ~/.ssh/id_rsa`, which reveals the file's size and line count (not its contents). `wc` cannot write, execute, or exfiltrate data. Both `-w` (word count for draft estimates) and `-l` (line count for JSONL sampling) are used by the skill.

**Resolution**: Accepted. `wc` is read-only and cannot execute shell commands or write files.

---

**INFO — Write tool is unbounded at the system level** — Known residual risk

Claude Code's Write tool can write to any file path. SKILL.md constrains output to the source directory or a `notes-output/` subdirectory, but this constraint is prompt-enforced, not a hard system boundary.

**Mitigation**: Source files are treated as untrusted data. SKILL.md instructs the skill never to follow embedded directives in content it reads.

## Permission Rationale

### Final `allowed-tools`

| Tool | Why it exists |
|------|--------------|
| `Read` | Read source material (session logs, markdown, JSONL) |
| `Write` | Write draft files and inventory to output directory |
| `Glob` | Find `.md` and `.jsonl` files — replaces `find` and `ls` |
| `Grep` | Search content within source files |
| `Bash(wc *)` | Word counts for draft estimates, line counts for JSONL sampling |
| `Bash(date)` | Add date to inventory header |

### What's excluded and why

| Excluded | Risk |
|----------|------|
| `Bash(find *)` | Trailing `*` enabled `-exec`, `-delete`, arbitrary shell execution |
| `Bash(ls *)` | Unnecessary; Glob covers all directory inspection |
| `Bash(gh *)` | No GitHub operations needed |
| `Bash(git *)` | No version control operations needed |
| `Bash(curl *)`, `Bash(wget *)` | No network calls needed |
| `Bash(source *)`, `Bash(eval *)` | Arbitrary code execution |
| `Bash(echo *)`, `Bash(printf *)` | File writes via `>` redirection |
| `Bash(rm *)` | No deletions needed |
| `Edit` tool | `Write` is sufficient |

## Verification

Before installing (or after updating):

```bash
# Check for dangerous patterns in allowed-tools
grep -E "find|source|eval|curl|wget|gh |git " ~/.claude/skills/session-notes/SKILL.md

# Verify no executable files exist
find ~/.claude/skills/session-notes -type f ! -name "*.md" ! -name "*.json"
# Should return nothing
```

The `allowed-tools` block should contain only: `Read`, `Write`, `Glob`, `Grep`, `Bash(wc *)`, `Bash(date)`.

## For Forkers

Safe additions:
- Additional read-only Bash utilities with no write capability and no shell escapes

Do not add:
- `Bash(find *)` with a trailing wildcard
- `Bash(ls *)` — use Glob instead
- Any network commands (`gh`, `curl`, `wget`, `ssh`)
- Any shell execution (`source`, `eval`, `bash -c`)
- Any write commands that bypass the Write tool (`echo >`, `tee`, `sed -i`)

## Reporting

Open a security advisory on the repository. Do not open a public issue for security vulnerabilities.
