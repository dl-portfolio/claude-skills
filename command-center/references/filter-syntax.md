# GitHub Projects Filter Syntax

> **Read-only reference file.** Used by `/cc list` to translate user filters
> into GitHub Projects V2 filter strings.

---

## Basic Syntax

Filters are applied client-side by the skill after querying all items via
GraphQL (GitHub Projects V2 API does not support server-side filtering on
custom fields).

### User filter format

```
/cc list status:todo priority:high
/cc list type:bug,feature             # comma = OR within a field
/cc list status:"In Progress"         # quote multi-word values
/cc list project:mission-hub status:todo priority:high
```

### Parsing rules

| Syntax | Meaning | Example |
|--------|---------|---------|
| `field:value` | Exact match (case-insensitive) | `status:todo` |
| `field:a,b` | OR — matches a or b | `priority:high,medium` |
| Multiple filters | AND — all must match | `status:todo priority:high` |
| `"multi word"` | Quote values with spaces | `status:"In Progress"` |

### Supported filter fields

| Field | Matches on |
|-------|-----------|
| `status` | Status field value |
| `priority` | Priority field value |
| `effort` | Effort field value |
| `type` | Type field value |
| `project` | Project field value |
| `label` | Issue label name |
| `assignee` | Assignee login |

---

## Limitations

- Filters are case-insensitive but must match option names exactly (after case folding).
- No negation support (no `status:!done`). Filter positively.
- No date range filtering. Use GitHub's built-in project views for date filters.
- Maximum 100 items per page. The skill paginates automatically but very large projects (500+ items) may be slow.
- Draft items (items without linked issues) do not have labels or assignees.
