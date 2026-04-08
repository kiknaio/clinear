# linear-issues

List, search, or read Linear issues using the `clinear` CLI.

## List open issues

```bash
clinear issues list --limit 20
```

Use `--fields` to reduce output when you only need specific fields:

```bash
clinear issues list --fields title,state,priority,assignee
```

## Search issues by keyword

```bash
clinear issues list --query "auth bug"
```

## Read a specific issue (includes description)

```bash
clinear issues read <identifier>
# e.g. clinear issues read SPA-37
# e.g. clinear issues read SPA-37 --fields title,description,state,labels
```

## Output format

- Returns a flat JSON array. No pagination wrapper.
- `id` and `identifier` are always present.
- Nested objects are flattened: `state` is a name string, `team` is a key string, `assignee` is a name string.
- Empty/null fields are omitted.
- `description` and `branchName` are excluded from `list` — use `read` to get them.
- `priority` values: 1=urgent, 2=high, 3=medium, 4=low. Omitted when unset.
