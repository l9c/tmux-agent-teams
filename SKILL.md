---
name: tmux-agent-teams
description: Create Claude Code agent teams when users ask to "Create an agent team ..." or "Spawn an agent team ...".
metadata:
  { "openclaw": { "emoji": "🧵", "os": ["darwin", "linux"], "requires": { "bins": ["tmux", "claude"] } } }
---

# Agent Teams Workflow

## When to use this skill
Use this skill when the user wants to create a Claude Code agent team and the request is expressed as an outcome rather than a terminal procedure.

## Preconditions
- Claude Code credentials are configured.
- Claude Code Agent teams are enabled for Claude Code.


## Intent detection
1. Infer the work dir as `work_dir`, and team name as `team` from user's request.
2. If the team name is not provided, infer the name based on the teammates' tasks.

## Tmux session naming
`dir_hash`: `printf "$work_dir"|sha256sum | cut -c1-6`
`tmux_session`: `team-<dir_hash>-<team>`

## Behavior
load the bundled tmux/SKILL.md before any operations.
 
### Creation
1. If the work dir is not provided, ask for it before creating a tmux session.
2. If the request includes multiple possible directories, ask the user which work dir to use.
3. Ask the user for confirmation if a tmux session that includes the same `dir_hash` already exists.
4. In the tmux session `tmux_session`, run `source ~/.bashrc`
5. Launch Claude Code in the tmux session `tmux_session` with the command `claude`.
6. If Claude Code shows a confirmation prompt, ask the user for the choice.
7. Send the user's original request to the Claude Code lead.
8. Capture the lead pane output and report back to the user.

## Boundaries
- Do not confuse agents/teammates with ACP Agents, or sub-agents, and only use tmux skills to interact with Claude Code in tmux sessions.
- Do not assume dangerous permission bypass flags unless the user or policy explicitly allows them.
- Ask for clarification if the work dir or repository is ambiguous.
- Prefer reusing an existing team when the user clearly refers to ongoing work, but only in service of creating or continuing a requested team setup.
