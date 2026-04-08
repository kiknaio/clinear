# Changelog

All notable changes to this project will be documented in this file. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [2026.4.1] - 2026-04-07

[2026.4.1]: https://github.com/clinear-oss/clinear/compare/v2025.12.3...v2026.4.1

### Breaking Changes

- **Complete architecture rewrite** to a strict five-layer architecture: CLI Input → Command → Resolver → Service → JSON Output. [#45](https://github.com/clinear-oss/clinear/issues/45), [#27](https://github.com/clinear-oss/clinear/issues/27), [#43](https://github.com/clinear-oss/clinear/issues/43), [#47](https://github.com/clinear-oss/clinear/issues/47), [PR#49](https://github.com/clinear-oss/clinear/pull/49)
- **`embeds` commands renamed to `files`** — `embeds download` → `files download`, `embeds upload` → `files upload`
- **`project-milestones` commands renamed to `milestones`**
- **`search` subcommands merged into `list`** — use `issues list --status ...` instead of `issues search --status ...`

### Added

- **Encrypted token authentication** — `clinear auth login` opens Linear in the browser and stores the token encrypted in `~/.clinear/token`. New subcommands: `auth login`, `auth status`, `auth logout`
- **Issue relation flags** — `--blocks`, `--blocked-by`, `--relates-to` on issue update
- **Cursor pagination** — `--after` and `--limit` flags on all list commands
- **Assignee resolution** — `--assignee` flag resolves by name or email
- **`usage` subcommand** on every command group for self-documenting CLI help
- **Request timeouts** — GraphQL API requests time out after 30 seconds, file download/upload after 60 seconds. Prevents indefinite hangs, especially important for LLM agent tool timeouts
- **GraphQL Code Generator pipeline** — queries and mutations defined in `.graphql` files under `graphql/`, codegen produces typed DocumentNodes
- **Biome** for formatting and linting (replaces previous setup)
- **Lefthook** git hooks with **commitlint** for conventional commit enforcement
- **Security policy** (`SECURITY.md`) with responsible disclosure process
- **GitHub community templates** — bug report form, feature request form, PR template

### Fixed

- File download and upload commands now use proper error exit codes (exit 1) on failure instead of returning exit code 0 with a success envelope

### Documentation

- Complete documentation rewrite for v2 architecture
- New docs: `architecture.md`, `development.md`, `testing.md`, `build-system.md` with layer invariants, mock patterns, and service/resolver/command templates
- `AGENTS.md` restructured for machine-first readability with decision trees and anti-patterns
- `README.md` rewritten for current CLI commands and agent optimization
- `CONTRIBUTING.md` expanded with dev setup, testing, and architecture pointer
- Removed obsolete 26k-line GraphQL schema dump and completed implementation plans

---

## [2025.12.3] - 2025-12-11

[2025.12.3]: https://github.com/czottmann/clinear/compare/v2025.12.2...v2025.12.3

### Fixed

- Version string now read from `package.json` instead of being hardcoded

---

## [2025.12.2] - 2025-12-11

[2025.12.2]: https://github.com/czottmann/clinear/compare/v2025.11.3...v2025.12.2

### Added

- New `embeds upload` command to upload files to Linear storage – thanks, [@chadrwalters](https://github.com/chadrwalters)! [PR#23](https://github.com/czottmann/clinear/pull/23)
- New `documents` commands for Linear document management – thanks, [@ralfschimmel](https://github.com/ralfschimmel)! [PR#21](https://github.com/czottmann/clinear/pull/21)
- `issues` commands now include the `branchName` field (the git branch name associated with the issue). [#14](https://github.com/czottmann/clinear/issues/14) <!-- ZCO-1629 -->
- Diagnostic output for issue transform errors, showing raw API response and stack trace to help debug null field issues. [#6](https://github.com/czottmann/clinear/issues/6) <!-- ZCO-1630 -->

### Breaking Changes

- **Issue "status" flag renamed**: `--state`/`--states` options renamed to `--status` for consistency with Linear's UI terminology. Thanks for the (appreciated but ultimately unused) PR, [@ralfschimmel](https://github.com/ralfschimmel)! <!-- ZCO-1641 -->
  - `issues search --states` → `--status` (still accepts comma-separated values)
  - `issues update --state` → `--status` (short flag `-s` unchanged)

### Tooling

- Prepublish validation to ensure `dist/main.js` exists and is executable before publishing to npm <!-- ZCO-1604 -->
- Cleaned up the tiny `pnpm` vs `npm` mess, it's now `npm` all the things <!-- ZCO-1603 -->

---

## [2025.11.3] - 2025-11-20

[2025.11.3]: https://github.com/czottmann/clinear/compare/2025.11.2...v2025.11.3

### Added

- New `teams` command with `list` subcommand 🎉 – thanks, [@chadrwalters](https://github.com/chadrwalters)! [PR#13](https://github.com/czottmann/clinear/pull/13)
  - Lists all teams in workspace with id, key, name, and description
  - Results sorted alphabetically by name
- New `users` command with `list` subcommand [PR#13](https://github.com/czottmann/clinear/pull/13)
  - Lists all users with id, name, displayName, email, and active status
  - Supports `--active` flag to filter for active users only
  - Results sorted alphabetically by name
- Integration tests for teams and users commands [PR#13](https://github.com/czottmann/clinear/pull/13)

### Fixed

- GraphQL orderBy error resolved by implementing client-side sorting for teams and users list commands [PR#13](https://github.com/czottmann/clinear/pull/13)
- Project name matching is now case-insensitive (using `eqIgnoreCase`) for better UX [PR#13](https://github.com/czottmann/clinear/pull/13)

### Documentation

- Added "Teams & Users" section to README.md with usage examples
- Updated docs/architecture.md, docs/development.md, and docs/files.md to reference new commands

---

## [2025.11.2] - 2025-11-11

[2025.11.2]: https://github.com/czottmann/clinear/compare/2025.11.1...2025.11.2

### Added

- New `cycles` and `project-milestones` commands 🎉 – thanks, [Ryan](https://github.com/ryanrozich)! [PR#7](https://github.com/czottmann/clinear/pull/7)
- The `issues` commands now include parent and child issue relationships <!-- ZCO-1574, ZCO-1586 -->
  - `parentIssue` field with `{ id, identifier, title }` for parent issue (if exists)
  - `subIssues` array with `{ id, identifier, title }` for immediate child issues
  - Available in all issue commands: `read`, `list`, and `search`

### Fixed

- `issues` commands' embed parser now correctly ignores markdown URLs inside code blocks and inline code <!-- ZCO-1587 -->
  - Previously extracted URLs from code examples and documentation
  - Ensures only actual embedded files are detected, not code examples
- All date/time fields now output in ISO 8601 format (`2025-11-09T23:00:00.000Z`) instead of verbose JavaScript date strings <!-- ZCO-1577 -->
- Under-the-hood stability bug fixes.

---

## [2025.11.1] - 2025-11-06

[2025.11.1]: https://github.com/czottmann/clinear/compare/1.1.0...2025.11.1

### Added

- `issues` commands' results now include `embeds` array containing tickets' file embeds
  - Embed extraction from issue descriptions and comments
    - Parses markdown for Linear upload URLs (`![label](url)` and `[label](url)`)
    - Returns `embeds` array in `issues read` command output
    - Each embed includes `label`, `url`, and `expiresAt` (ISO 8601 timestamp)
- New `embeds` command group for downloading embedded files from Linear's cloud storage
  - `embeds download <url>` command to download files
    - `--output <path>` option for custom output location
    - `--overwrite` flag to replace existing files
    - Automatic directory creation for output paths

### Documentation

- Renamed CLAUDE.md to AGENTS.md, re-added CLAUDE.md as a symlink
- Updated AGENTS.md with file download features and signed URL documentation
- Added File Downloads section to README.md with usage examples
- Updated docs/files.md with new command and utility files
- Added embeds command flow and extraction flow diagrams to documentation

---

## [1.1.0] - 2025-10-21

[1.1.0]: https://github.com/czottmann/clinear/compare/1.0.0...1.1.0

### Fixes

- Updated CLI program name from "linear" to "clinear" for consistency with project name

### Documentation

- Added section "Example rule for your LLM agent of choice" to README

---

## [1.0.0] - 2025-10-21

[1.0.0]: https://github.com/czottmann/clinear/releases/tag/1.0.0

### Added

- Initial release of Clinear CLI tool
