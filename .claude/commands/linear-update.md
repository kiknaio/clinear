# linear-update

Update an existing Linear issue using the `clinear` CLI.

## Usage

```bash
clinear issues update <identifier> [options]
# e.g. clinear issues update SPA-37 --status "In Progress"
```

Accepts both UUID and short identifier (e.g. `SPA-37`).

## Common options

| Flag | Description |
|---|---|
| `--status <name>` | Change status (e.g. "In Progress", "Done") |
| `--priority <1-4>` | 1=urgent 2=high 3=medium 4=low |
| `--assignee <name>` | Reassign |
| `--title <text>` | Rename |
| `--description <text>` | Replace description |
| `--labels <names>` | Set labels (comma-separated) |
| `--label-mode add` | Add labels without overwriting existing |
| `--clear-labels` | Remove all labels |
| `--parent-ticket <id>` | Set parent |
| `--clear-parent-ticket` | Remove parent |

## Relation flags (one at a time)

```bash
--blocks <identifier>       # this issue blocks another
--blocked-by <identifier>   # this issue is blocked by another
--relates-to <identifier>   # add a relates-to relation
--remove-relation <identifier>  # remove any relation with that issue
```

## Examples

```bash
# Mark as done
clinear issues update SPA-37 --status "Done"

# Escalate priority and assign
clinear issues update SPA-38 --priority 1 --assignee "George"

# Add a relation
clinear issues update SPA-38 --blocks SPA-40
```

## Output

Returns the updated issue object.
