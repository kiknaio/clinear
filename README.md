# Clinear

CLI tool for [Linear.app](https://linear.app) optimized for AI agents. JSON output, smart ID resolution, token-efficient usage commands, and a discover-then-act workflow that keeps agent context small. Works just as well for humans who prefer structured data on the command line.

## Why?

The official Linear MCP works fine, but it eats up ~13k tokens just by being connected -- before the agent does anything. Clinear takes a different approach: instead of exposing the full API surface upfront, agents discover what they need through a two-tier usage system. `clinear usage` gives an overview in ~200 tokens, then `clinear <domain> usage` provides the full reference for one area in ~300-500 tokens. A typical agent interaction costs ~500-700 tokens of context, not ~13k.

The trade-off is coverage. An MCP exposes the entire Linear API; Clinear covers the operations that matter for day-to-day work with issues, comments, cycles, documents, and files. If you need to manage custom workflows, integrations, or workspace settings, the MCP is the better choice.

**This project scratches my own itches,** and satisfies my own usage patterns of working with Linear: I **do** work with tickets/issues and comments on the command line; I **do not** manage projects or workspaces etc. there. YMMV.

## Installation

```bash
npm install -g clinear
```

Requires Node.js >= 22.

## Authentication

```bash
clinear auth login
```

This opens Linear in your browser, guides you through creating an API key, and stores the token encrypted in `~/.clinear/token`.

Alternatively, provide a token directly:

```bash
# Via CLI flag
clinear --api-token <token> issues list

# Via environment variable
LINEAR_API_TOKEN=<token> clinear issues list
```

Token resolution order: `--api-token` flag > `LINEAR_API_TOKEN` env > `~/.clinear/token` > `~/.linear_api_token` (deprecated).

## Usage

All output is JSON. Pipe through `jq` or similar for formatting.

```bash
# Discovery
clinear usage                # overview of all domains
clinear issues usage         # detailed usage for one domain
```

### Issues

```bash
# List recent issues
clinear issues list --limit 10

# Only include specific fields (id and identifier always included)
clinear issues list --limit 10 --fields title,state,priority
clinear issues list --fields title,state,assignee,team

# Search issues by text
clinear issues list --query "authentication" --team Platform

# Read issue details with field selection
clinear issues read DEV-456 --fields title,description,state,labels

# Create an issue
clinear issues create "Fix login timeout" --team Backend \
  --assignee "Jane Doe" --labels "Bug,Critical" --priority 1 \
  --description "Users report session expiry after 5 minutes"

# Read issue details (supports ABC-123 identifiers)
clinear issues read DEV-456

# Update status, priority, labels
clinear issues update ABC-123 --status "In Review" --priority 2
clinear issues update DEV-789 --labels "Frontend,UX" --label-mode add
clinear issues update ABC-123 --clear-labels

# Parent-child relationships
clinear issues update SUB-001 --parent-ticket EPIC-100

# Issue relations
clinear issues create "Blocked task" --team Backend --blocked-by DEV-123
clinear issues update ABC-123 --blocks DEV-456
clinear issues update ABC-123 --relates-to DEV-789
clinear issues update ABC-123 --remove-relation DEV-456
```

### Comments

```bash
clinear comments create ABC-123 --body "Fixed in PR #456"
```

### Documents

```bash
# Create a document (optionally link to a project and/or issue)
clinear documents create --title "API Design" --content "# Overview..."
clinear documents create --title "Bug Analysis" --project "Backend" --issue ABC-123

# List documents
clinear documents list
clinear documents list --project "Backend"
clinear documents list --issue ABC-123

# Read, update, delete
clinear documents read <document-id>
clinear documents update <document-id> --title "New Title" --content "Updated content"
clinear documents delete <document-id>
```

### Cycles

```bash
# List cycles for a team
clinear cycles list --team Backend --limit 10

# Active cycle only
clinear cycles list --team Backend --active

# Active cycle +/- 3 neighbors
clinear cycles list --team Backend --window 3

# Read cycle details
clinear cycles read "Sprint 2025-10" --team Backend
```

### Milestones

```bash
# List milestones in a project
clinear milestones list --project "Backend"

# Read milestone details
clinear milestones read "Beta Release" --project "Backend"

# Create and update milestones
clinear milestones create "v2.0" --project "Backend" --target-date 2025-06-01
clinear milestones update "v2.0" --project "Backend" --description "Major release"
```

### Files

```bash
# Download a file from Linear storage
clinear files download "https://uploads.linear.app/.../file.png" --output ./screenshot.png

# Upload and reference in a comment
URL=$(clinear files upload ./bug.png | jq -r .assetUrl)
clinear comments create ABC-123 --body "Screenshot: ![$URL]($URL)"
```

### Projects, Labels, Teams, Users

```bash
clinear projects list
clinear labels list --team Backend
clinear teams list
clinear users list --active
```

### Pagination

List commands support cursor-based pagination via `--after <cursor>`. The cursor is the `id` of the last item in the previous response:

```bash
clinear issues list --limit 25
clinear issues list --limit 25 --after "<id-of-last-issue>"
```

## AI Agent Integration

### How agents use Clinear

The CLI is structured around a discover-then-act pattern that matches how agents work:

1. **Discover** -- `clinear usage` returns a compact overview of all domains (~200 tokens). The agent reads this once to understand what's available.
2. **Drill down** -- `clinear <domain> usage` gives the full command reference for one domain (~300-500 tokens). The agent only loads what it needs.
3. **Execute** -- All commands return structured JSON. No parsing of human-readable tables or prose.

This means the agent never loads the full API surface into context. It pays for what it uses, one domain at a time.

For further token savings, `list` and `read` commands support `--fields` to return only the fields the agent needs. `id` and `identifier` are always included. Empty fields (labels, assignee, cycle, etc.) are omitted from output automatically.

### Claude Code skills (slash commands)

Clinear ships with Claude Code slash commands in `.claude/commands/`. Copy the `.claude/` directory into any project and agents get instant access to:

| Command | Purpose |
|---|---|
| `/linear-issues` | List, search, and read issues |
| `/linear-create` | Create a new issue |
| `/linear-update` | Update status, priority, relations, and more |

**Installing in another project:**

```bash
cp -r /path/to/clinear/.claude /path/to/your-project/
```

Or copy just the commands directory:

```bash
cp -r /path/to/clinear/.claude/commands /path/to/your-project/.claude/
```

Once copied, any Claude Code agent session in that project can invoke `/linear-issues`, `/linear-create`, or `/linear-update` directly.

### Clinear vs. MCP

| | Clinear | Linear MCP |
|---|---|---|
| Context cost | ~500-700 tokens per interaction | ~13k tokens on connect |
| Coverage | Common operations (issues, comments, cycles, docs, files) | Full Linear API |
| Output | JSON via stdout | Tool call responses |
| Setup | `npm install -g clinear` + bash tool | MCP server connection |

Use Clinear when token efficiency matters and you work primarily with issues and related data. Use the MCP when you need full API coverage or tight tool-call integration.

### Example prompt

```markdown
## Linear (project management)

Tool: `clinear` CLI via Bash. All output is JSON.

Discovery: Run `clinear usage` once to see available domains. Run `clinear <domain> usage` for full command reference of a specific domain. Do NOT guess flags or subcommands -- check usage first.

Ticket format: "ABC-123". Always reference tickets by their identifier.

Workflow rules:
- When creating a ticket, ask the user which project to assign it to if unclear.
- For subtasks, inherit the parent ticket's project by default.
- When a task in a ticket description changes status, update the description.
- For progress beyond simple checkbox changes, add a comment instead of editing the description.

File handling: `issues read` returns an `embeds` array with signed download URLs and expiration timestamps. Use `files download` to retrieve them. Use `files upload` to attach new files, then reference the returned URL in comments or descriptions.
```

Add this (or a version adapted to your workflow) to your `AGENTS.md` or `CLAUDE.md` so every agent session has it in context automatically.

## Contributing

Want to contribute? See [CONTRIBUTING.md](CONTRIBUTING.md).

## Maintainer

Fabian Jocks -- [github.com/iamfj](https://github.com/iamfj) | [linkedin.com/in/fabianjocks](https://linkedin.com/in/fabianjocks)

## Original Author

Carlo Zottmann -- [c.zottmann.dev](https://c.zottmann.dev) | [github.com/czottmann](https://github.com/czottmann)

Carlo created Clinear and drove its early development. As interest in the project grew, he handed maintenance over to Fabian.

This project is neither affiliated with nor endorsed by Linear.

### Sponsoring Carlo's work

Carlo doesn't accept sponsoring in the "GitHub sponsorship" sense[^1] but [next to his own apps, he also sells "Tokens of Appreciation"](https://actions.work/store/?ref=github). Any support is appreciated!

[^1]: Apparently, the German revenue service is still having some fits over "money for nothing??".

> [!TIP]
> Carlo makes Shortcuts-related macOS & iOS productivity apps like [Actions For Obsidian](https://actions.work/actions-for-obsidian), [Browser Actions](https://actions.work/browser-actions) (which adds Shortcuts support for several major browsers), and [BarCuts](https://actions.work/barcuts) (a surprisingly useful contextual Shortcuts launcher). Check them out!

## Contributors

- [Fabian Jocks](https://github.com/iamfj)
- [Ryan Rozich](https://github.com/ryanrozich)
- [Chad Walters](https://github.com/chadrwalters)
- [Louis Mandelstam](https://github.com/man8)
- [Ralf Schimmel](https://github.com/ralfschimmel)

## License

MIT. See [LICENSE.md](LICENSE.md).
