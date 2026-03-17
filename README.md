# tmux-agent-teams

A high-level AI agent skill that abstracts away repetitive instructions for managing Claude Code agent teams in tmux. Instead of requiring users to describe every operation in detail, this skill converts a channel request into the necessary steps to start or manage an agent team, streamlining the process of creating and coordinating collaborative AI agents.

For a detailed setup, see the guide at <https://tmuxcheatsheet.com/openclaw-tmux-setup/>.

## example usages

### Creating an Agent Team
Prompt:
```
Create an agent team to review the repository in ~/example_repo. Use a security reviewer, a performance reviewer, and a QA reviewer. Send me the combined findings when they finish.
```

Response:
```
I can do that — I'll create a Claude Code agent team in a tmux session for ~/example_repo with security, performance, and QA reviewers. Quick confirmations before I start:
• Team name to use (I'll default to "review" if you don't care):
• Proceed now to create the tmux session and launch Claude Code in ~/example_repo? (Say "yes" to proceed or tell me changes.)
```

### Listing Agent Teams
Prompt:
```
List agent teams.
```

Response:
```
I found 1 Claude Code agent team running:
Session name: claude-3a9ee5-review
working directory: ~/example_repo
status (inferred): actively working — "Doodling… (3m 54s · thinking)" and waiting at the prompt (❯).
Would you like me to capture more output, attach to the session, or do anything else with this team?
```


