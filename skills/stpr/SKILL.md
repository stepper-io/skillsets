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
[Stepper](https://stepper.io) Skill Sets. Skill Sets let you bundle integration
actions into curated, authenticated toolkits — and expose them to AI agents,
CLIs, and any MCP-compatible client.

## Installation

Install the `stpr` CLI before using any skill commands:

```bash
npm install -g stpr
```

## Authentication

Before using any skill commands, ensure the user is authenticated. There are
three authentication methods:

1. **OAuth login (recommended):** Run `stpr login` to open a browser and
   select a Skill Set. Credentials are saved to
   `~/.config/stepper-skillsets/config.json` and automatically refreshed when
   they expire.
2. **Static token:** Pass `--token sst_<token>` on any command. Tokens are
   generated at <https://app.stepper.io/flow/skill-sets>.
3. **Environment variable:** Set `STEPPER_SKILL_TOKEN=sst_<token>`.

Check the current session with `stpr whoami`.

## Profile Management

```bash
stpr login                 # Authenticate via OAuth (opens browser)
stpr logout [name]         # Remove a saved profile, or all profiles if no name given
stpr profiles              # List all saved profiles
stpr use <name>            # Switch the active profile
stpr whoami                # Show active profile and server info
```

## Discovering Skills

```bash
stpr list                  # List all available skills, grouped by service
stpr list --verbose        # Include full input schemas
stpr list <service>        # List skills for a specific service
stpr <service>             # Shorthand for listing a service's skills
```

## Inspecting Parameters

Many skills have dynamic parameters — fields that change based on the values of
other fields. Calling a skill **without** `--call` returns its current parameter
schema:

```bash
# See what fields are needed for add_row, given a spreadsheet
stpr google-sheets add_row -i '{"spreadsheet_id": "abc123"}'
```

Some parameters have dynamic dropdown options. Fetch them with `--options`:

```bash
stpr google-sheets add_row --options worksheet_id \
  -i '{"spreadsheet_id": "abc123"}' \
  --search "Sheet" \
  --cursor "next_page"
```

## Executing Skills

Use the `--call` flag to execute an action:

```bash
stpr google-sheets create_sheet --call \
  -i '{"name": "Q1 Report", "columns": "Name, Email, Phone"}'
```

## Polling Async Results

Component library tools run asynchronously. Poll for results with:

```bash
stpr status <statusId>
```

## Input

Pass JSON input via the `-i` / `--input` flag or pipe it through stdin:

```bash
# Flag
stpr slack send_message --call -i '{"channel": "#general", "text": "Hello!"}'

# Stdin
echo '{"channel": "#general", "text": "Hello!"}' | stpr slack send_message --call
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

## Options Reference

| Flag | Description |
|---|---|
| `--token <token>` | Auth token (overrides saved profiles and `STEPPER_SKILL_TOKEN`) |
| `--base-url <url>` | Override MCP server URL (default: `https://mcp.stepper.io`) |
| `--skillset <name>` | Use a specific saved profile instead of the active one |
| `--call` | Execute the skill (default behavior is parameter inspection) |
| `--verbose` | Include full `inputSchema` when listing skills |
| `-i, --input <json>` | JSON input for calls, parameter fetches, or option queries |
| `--options <param>` | Fetch dynamic dropdown options for a parameter |
| `--search <query>` | Filter dropdown options by search term |
| `--cursor <cursor>` | Pagination cursor for dropdown options |
| `-h, --help` | Show help |
| `-v, --version` | Show version |

## Environment Variables

| Variable | Description |
|---|---|
| `STEPPER_SKILL_TOKEN` | Auth token (used when no `--token` flag and no saved profile) |
| `STEPPER_URL` | Override the MCP server base URL |

## Important Notes

- Always inspect parameters before calling a skill to understand required fields.
- Dynamic parameters change based on other field values -- resolve them
  step-by-step using `--options`.
- JSON input can be passed via `-i '{"key": "value"}'` or piped through stdin.
- The MCP server endpoint is `https://mcp.stepper.io/skill-sets/mcp` for
  MCP-compatible clients.
