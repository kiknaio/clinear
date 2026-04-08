# linear-create

Create a new Linear issue using the `clinear` CLI.

## Usage

```bash
clinear issues create "<title>" --team <team-key> [options]
```

`--team` is required. Use the team key (e.g. `SPA`, `DEV`).

## Common options

| Flag | Description |
|---|---|
| `--description <text>` | Issue body |
| `--priority <1-4>` | 1=urgent 2=high 3=medium 4=low |
| `--assignee <name-or-email>` | Assign to user |
| `--status <name>` | Set initial status |
| `--project <name>` | Add to project |
| `--labels <names>` | Comma-separated label names |
| `--parent-ticket <identifier>` | Set parent issue |

## Examples

```bash
# Minimal
clinear issues create "Fix login timeout" --team SPA

# With details
clinear issues create "Fix login timeout" --team SPA --priority 2 --assignee "George" --status "In Progress"

# Sub-task
clinear issues create "Write unit tests for auth" --team SPA --parent-ticket SPA-37
```

## Output

Returns the created issue object with `id` and `identifier`.
Use `identifier` to reference the issue in subsequent commands.
