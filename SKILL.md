---
name: tmux-agent-teams
description: Create Claude Code agent teams when users ask to "Create an agent team ..." or "Spawn an agent team ...", or list Claude Code agent teams when users ask to "List agent teams ..." or "Show agent teams ...".
metadata:
  { "openclaw": { "emoji": "🧵", "os": ["darwin", "linux"], "requires": { "bins": ["tmux", "claude"] } } }
---

# Agent Teams Workflow

## When to use this skill
Use this skill when the user wants to create a Claude Code agent team, or list Claude Code agent teams already running in tmux, and the request is expressed as an outcome rather than raw tmux commands.

## Preconditions
- Claude Code credentials are configured.
- Claude Code Agent teams are enabled.

## Intent detection
1. Infer whether the request is for `listing` or `creation`.
2. Treat create, spawn, start, and build intents as `creation`.
3. Treat list, show, inspect, what-is-running, and status intents as `listing`.
4. Infer the work dir as `work_dir`, and team name as `team`, from the user's request when provided.
5. If the team name is not provided for creation, infer it based on the teammates' tasks.

## Tmux session naming
`dir_hash`: `printf "$work_dir"|sha256sum | cut -c1-6`
`tmux_session`: `claude-<dir_hash>-<team>`

### Creation
1. Read tmux skill before proceeding.
2. If the work dir is not provided, ask for it before creating a tmux session.
3. If the request includes multiple possible directories, ask the user which work dir to use.
4. If an existing managed tmux session with the same `dir_hash` already exists, ask the user whether to reuse it. Reuse only after user confirmation.
5. Create `tmux_session` if it does not exist, and attach to `tmux_session`.
6. In the session, execute the exact command `claude`.
7. If Claude Code shows a confirmation prompt, ask the user for the choice.
8. Send the user's original request without rephrasing to the Claude Code lead.
9. Capture the lead pane to verify that the pane reads "❯ <request>" before submitting the request. If not, report the mismatch and ask the user whether to proceed anyway.
10. Hit Enter/Return key to submit the request.
11. Wait for Claude Code to output acknowledgment (poll for 30 seconds with 1-second intervals).
12. Capture the tmux pane output, report it back to the user without rephrasing.

### Listing
1. Read tmux skill before proceeding.
2. Keep listing read-only: do not create, attach, rename, kill, or otherwise modify tmux sessions when the request is only to list, show, or inspect teams.
3. Run tmux session listing.
4. Filter sessions that match the managed naming convention `claude-<dir_hash>-<team>`.
5. If the user provides `work_dir`, compute `dir_hash` from it and use that to narrow matches. Report whether each returned session matches the requested work dir.
6. For each matching session, inspect the first 10 lines of the lead pane to infer work-dir.
7. For each matching session, inspect the last 30 lines of the lead pane to infer a high-level status such as `waiting for input`, `still working`, `idle`, or `unknown`.
8. Report the session name, optional work-dir, and inferred status for each match.
9. If no matching sessions exist, report that clearly.

## Boundaries
- Do not rely on ACP. 
- Only use tmux skills to interact with Claude Code in tmux sessions.
- Do not use `agents_list` tool, as we are using only tmux skills to manage the teams.
- Do not assume dangerous permission bypass flags unless the user or policy explicitly allows them.
- Ask for clarification if the work dir or repository is ambiguous.
