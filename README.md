# Stepper Skill Sets for Claude Code

A [Claude Code skill](https://docs.anthropic.com/en/docs/claude-code/skills) that lets Claude interact with third-party services (Google Sheets, Slack, and more) through the [Stepper](https://stepper.io) `stpr` CLI.

## Installation

### 1. Install the `stpr` CLI

```bash
npm install -g @anthropic-ai/stpr
```

Or with Homebrew:

```bash
brew install stepper-io/tap/stpr
```

### 2. Add the skill to Claude Code

From your project directory, install the skillset:

```bash
claude skill add --from stepper-io/skillsets
```

This registers the `stpr` skill so Claude can discover and execute Stepper actions.

### 3. Authenticate

Log in to Stepper so the CLI can make authenticated requests:

```bash
stpr login
```

Alternatively, pass a static token via the `--token` flag or set the `STEPPER_SKILL_TOKEN` environment variable. Tokens can be generated at <https://app.stepper.io/flow/skill-sets>.

Verify your session:

```bash
stpr whoami
```

## Usage

Once installed, ask Claude to perform integration actions in natural language:

> "Add a row to my Google Sheet with Name=Alice, Email=alice@example.com"

Claude will authenticate, discover the right skill, inspect its parameters, and execute the action — all through the `stpr` CLI.

## Available Skills

Run `stpr list` to see all services and actions available through your Stepper account.

## Learn More

- [Stepper documentation](https://stepper.io)
- [Claude Code skills](https://docs.anthropic.com/en/docs/claude-code/skills)
