# Personal OpenCode Agents

This repository stores the **system prompts** for my global OpenCode agents.

The goal is to keep agent prompts versioned, easy to reuse across projects, and simple to update.

## Contents

- `Sentinel.md` — A high-reliability, general-purpose assistant that is a jack of all trades.
- `TheProfessor.md` — teaching-first agent for explanations, understanding, and long-term retention.
- `ProductionTeam/TheArchitect.md` — architecture and systems design agent.
- `ProductionTeam/TheDirector.md` — planning-first orchestrator for a multi-agent software team.

## How to use

- **Copy/paste**
  - Open the agent file (for example `Sentinel.md`) and copy its contents into the “system prompt” / “instructions” field of your OpenCode / ClaudeCode / Cursor Agents configuration.
- **Reference from a config**
  - If your tooling supports reading prompts from a file path, point it at the corresponding `*.md` file in this repo.

## Repo structure

- `README.md` — this overview.
- `*.md` — one file per agent prompt.
- `ProductionTeam/` — contains specialized agents for team-based workflows.
  - `plans/` — planning documents for team operations.
  - `templates/` — reusable templates for agent prompts.

## Conventions

- One agent prompt per file.
- Prefer descriptive filenames matching the agent name.

## Adding or updating an agent

- Create a new `AgentName.md` file (or edit an existing one).
- Keep the prompt **self-contained** (it should make sense when pasted alone).
- When making changes, prefer small, reviewable edits so prompt behavior changes are easy to track.

## Usage

Copy these prompts to your OpenCode / ClaudeCode / Cursor Agents configuration as needed.
