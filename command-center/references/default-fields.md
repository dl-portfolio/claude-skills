# Default Fields & Labels

> **Read-only reference file.** Used by `/cc init` and `/cc connect` to create
> consistent project fields and repository labels.

---

## Project Fields

### Status (built-in — modify, don't recreate)

Every GitHub Project V2 ships with a Status field. `/cc init` adds "Backlog"
to the default options.

| Option | Color | Description |
|--------|-------|-------------|
| Backlog | GRAY | Not yet scheduled |
| Todo | BLUE | Scheduled for work |
| In Progress | YELLOW | Actively being worked on |
| Done | GREEN | Completed |

### Priority (create new single-select)

| Option | Color |
|--------|-------|
| High | RED |
| Medium | ORANGE |
| Low | BLUE |

### Effort (create new single-select)

| Option | Color |
|--------|-------|
| Small | GREEN |
| Medium | YELLOW |
| Large | RED |

### Type (create new single-select)

| Option | Color |
|--------|-------|
| Feature | PURPLE |
| Bug | RED |
| Research | BLUE |
| Maintenance | GRAY |

### Project (create new single-select)

Starts empty. Each `/cc connect` adds the repo as a new option using the
repo's short name (e.g., "mission-hub", "api-gateway").

| Option | Color |
|--------|-------|
| *(added per-repo)* | PINK |

---

## Repository Labels

Created by `/cc connect` using `gh label create --force` (idempotent).

| Name | Color (hex) | Description |
|------|-------------|-------------|
| priority:high | `d73a4a` | High priority |
| priority:medium | `fbca04` | Medium priority |
| priority:low | `0075ca` | Low priority |
| type:feature | `a2eeef` | New feature or enhancement |
| type:bug | `d73a4a` | Something isn't working |
| type:research | `0075ca` | Research or investigation |
| type:maintenance | `e4e669` | Maintenance or chore |
| effort:small | `c5def5` | Small effort (< 2 hours) |
| effort:medium | `bfd4f2` | Medium effort (2-8 hours) |
| effort:large | `b60205` | Large effort (> 8 hours) |
| context | `5319e7` | Pinned context issue |

---

## Customization

Users can customize fields after creation:

- **Add field options**: `/cc init` and `/cc connect` create the defaults above. Users can add more options directly in GitHub's project settings UI or by editing `references/default-fields.md` in their fork.
- **Rename fields**: Rename in GitHub UI. Update `.cc-config.json` field IDs remain stable (node IDs don't change on rename).
- **Remove fields**: Remove in GitHub UI. The skill will skip fields it can't find in the config.
- **Custom labels**: Add any additional labels via `gh label create`. The skill only manages the labels listed above.
