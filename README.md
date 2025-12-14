# Personal OpenCode Agents

This repository stores the **system prompts** for my global OpenCode agents.

The goal is to keep agent prompts versioned, easy to reuse across projects, and simple to update.

## Contents

- `Sentinel.md` — system prompt for the `Sentinel` agent.

## How to use

- **Copy/paste**
  - Open the agent file (for example `Sentinel.md`) and copy its contents into the “system prompt” / “instructions” field of your OpenCode / Codex CLI / IDE agent configuration.
- **Reference from a config**
  - If your tooling supports reading prompts from a file path, point it at the corresponding `*.md` file in this repo.

## Repo structure

- `README.md` — this overview.
- `*.md` — one file per agent prompt.

## Conventions

- One agent prompt per file.
- Prefer descriptive filenames matching the agent name.

## Adding or updating an agent

- Create a new `AgentName.md` file (or edit an existing one).
- Keep the prompt **self-contained** (it should make sense when pasted alone).
- When making changes, prefer small, reviewable edits so prompt behavior changes are easy to track.

## Usage

Copy these prompts to your OpenCode / ClaudeCode / Cursor Agents configuration as needed.
