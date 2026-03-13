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
- Claude Code Agent teams are enabled.

## Intent detection
1. Infer the work dir as `work_dir`, and team name as `team` from user's request.
2. If the team name is not provided, infer it based on the teammates' tasks.

## Tmux session naming
`dir_hash`: `printf "$work_dir"|sha256sum | cut -c1-6`
`tmux_session`: `claude-<dir_hash>-<team>`


### Creation
0. Read tmux skill before proceeding.
1. If the work dir is not provided, ask for it before creating a tmux session.
2. If the request includes multiple possible directories, ask the user which work dir to use.
3. Ask the user for confirmation if an existing tmux session that includes the same `dir_hash` already exists.
4. Create `tmux_session` if it does not exist, and attach to `tmux_session`.
5. In the session, execute the exact command `claude`.
6. If Claude Code shows a confirmation prompt, ask the user for the choice.
7. Send the user's original request without rephrasing to the Claude Code lead, then send `Enter` key.
8. Wait for Claude Code to output acknowledgment (poll for 30 seconds with 1-second intervals).
9. Capture the tmux pane output, report it back to the user without rephrasing.

## Boundaries
- Do not confuse agents/teammates with ACP Agents, or sub-agents. Only use tmux skills to interact with Claude Code in tmux sessions.
- Do not use `agents_list` tool, as we are using only tmux skills to manage the teams.
- Do not assume dangerous permission bypass flags unless the user or policy explicitly allows them.
- Ask for clarification if the work dir or repository is ambiguous.
