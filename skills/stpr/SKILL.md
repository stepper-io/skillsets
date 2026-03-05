---
name: stpr
description: >
  Interact with Stepper Skill Sets via the stpr CLI to discover, inspect, and
  execute integration actions (Google Sheets, Slack, and more). Use when the user
  wants to call third-party service APIs through Stepper, list available skills,
  authenticate with Stepper, or run integration actions from the terminal.
metadata:
  author: stepper-io
  version: "1.0"
allowed-tools: Bash(stpr:*)
---

# Stepper Skills CLI (`stpr`)

You have access to the `stpr` CLI for executing integration actions through
[Stepper](https://stepper.io) Skill Sets.

## Installation

Install the `stpr` CLI before using any skill commands:

```bash
npm install -g @anthropic-ai/stpr
```

Or with Homebrew:

```bash
brew install stepper-io/tap/stpr
```

## Authentication

Before using any skill commands, ensure the user is authenticated. There are
three authentication methods:

1. **OAuth login (recommended):** Run `stpr login` to open a browser and save
   credentials to `~/.config/stepper-skillsets/config.json`. Tokens refresh
   automatically.
2. **Static token:** Pass `--token sst_<token>` on any command. Tokens are
   generated at <https://app.stepper.io/flow/skill-sets>.
3. **Environment variable:** Set `STEPPER_SKILL_TOKEN=sst_<token>`.

Check the current session with `stpr whoami`.

## Discovering Skills

```bash
stpr list                  # List all available skills grouped by service
stpr list --verbose        # Include full input schemas
stpr list <service>        # List skills for a specific service
stpr <service>             # Shorthand for listing a service's skills
```

## Inspecting Parameters

Calling a skill **without** `--call` returns its current parameter schema:

```bash
stpr google-sheets add_row -i '{}'
```

For dynamic dropdown fields, use `--options`:

```bash
stpr google-sheets add_row --options worksheet_id \
  -i '{"spreadsheet_id": "abc123"}'
```

Add `--search <query>` and `--cursor <cursor>` for filtering and pagination.

## Executing Skills

Use the `--call` flag to execute an action. Pass JSON input with `-i` or via
stdin:

```bash
stpr google-sheets create_sheet --call \
  -i '{"name": "Q1 Report", "columns": "Name, Email, Phone"}'
```

For async operations, poll the result with:

```bash
stpr status <statusId>
```

## Workflow

Follow this sequence when the user asks you to perform an integration action:

1. **Authenticate** -- Run `stpr whoami` to check. If not logged in, run
   `stpr login` or ask the user for a token.
2. **Discover** -- Run `stpr list` to find the relevant service and skill.
3. **Inspect** -- Call the skill without `--call` to see required parameters.
   Use `--options` to resolve any dynamic dropdown fields.
4. **Execute** -- Call the skill with `--call` and the full JSON input.
5. **Report** -- Show the user the result. If the response contains a
   `statusId`, poll with `stpr status <statusId>` and report the final result.

## Profile Management

Users can manage multiple Stepper Skill Set profiles:

```bash
stpr profiles              # List saved profiles
stpr use <name>            # Switch the active profile
stpr logout [name]         # Remove a profile (or all if no name given)
```

## Global Flags

| Flag | Description |
|---|---|
| `--token <token>` | Auth token (overrides saved profiles and env var) |
| `--base-url <url>` | Override MCP server URL (default: `https://mcp.stepper.io`) |
| `--skillset <name>` | Use a specific saved profile |
| `--call` | Execute the skill action |
| `--verbose` | Include full input schemas in listings |
| `-i, --input <json>` | JSON input for calls or parameter fetches |
| `--options <param>` | Fetch dynamic dropdown options for a parameter |
| `--search <query>` | Filter dropdown options |
| `--cursor <cursor>` | Pagination cursor for dropdown options |

## Important Notes

- Always inspect parameters before calling a skill to understand required fields.
- Dynamic parameters change based on other field values -- resolve them
  step-by-step using `--options`.
- JSON input can be passed via `-i '{"key": "value"}'` or piped through stdin.
- The MCP server endpoint is `https://mcp.stepper.io/skill-sets/mcp` for
  MCP-compatible clients.
