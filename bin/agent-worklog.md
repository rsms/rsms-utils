# agent-worklog

Utility for allowing LLM agents to communicate with eachother

## .gitignore

Patterns to add to your gitignore:

```
/.agent-worklog.jsonl
/.agent-worklog.jsonl.lock
/.agent-worklog-state.json
```

- `.agent-worklog.jsonl` contains the actual log data. This file and the related .lock file are stored only in the main checkout of the repo, not in worktrees.
- `.agent-worklog-state.json` contains the current "last timestamp read" info and is stored per worktree. It is intentionally "lost" when a worktree is deleted.

## AGENTS.md template

```md
## Agent Coordination

There are multiple concurrent agents running. You are one of them.
Each agent should run in a dedicated git worktree.

When editing files or running commands that change things (not during planning or discussion-only phases), coordinate through worklogs:

- Write announcements with `agent-worklog <announcement> ...`
- Every `agent-worklog ...` announcement also read updates from other agents, so no separate immediate poll is needed after posting
- If you have no new announcement, poll with `agent-worklog` every 20-60 seconds
- The `agent-worklog` program writes to a per git repo (not per worktree) JSONL file (see `agent-worklog --print-db-path`)
- Keep announcements short, clear, concise, and to the point
- Announce before starting a concrete change and after each major step
- When starting up, run `agent-worklog` to catch up on what's happening.

Announcement format:

- Preferred: plain short message text, e.g. `agent-worklog "editing typecheck: fix mutable slice assign"`
- Optional: JSON object when structured fields help, e.g. `agent-worklog '{"message":"running tests","proposal":"14A"}'`
- If plain text is used, `agent-worklog` wraps it into JSON and adds `timestamp` plus `from` (current branch name)

Reacting to important updates:

- If another agent announces a change that may affect your current work, use git to inspect or integrate it now.
- Integration options include: `git stash` + merge/rebase + `git stash pop`, committing your WIP and then merging/rebasing, or another safe git workflow.
- If another agent reports they merged, update your branch from `main` promptly (`git merge main` or `git rebase main`) to pick up those changes.
```

