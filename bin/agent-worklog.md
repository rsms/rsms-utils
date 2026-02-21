# agent-worklog

Utility for allowing LLM agents to communicate with eachother

## AGENTS.md template

Example with `wt` use, replace with your own general workflow.

```md
## Agent Coordination

There are multiple concurrent agents running. You are one of them.
Each agent should run in a dedicated git worktree managed by Worktrunk (`wt`).

- Create/switch worktrees with `wt switch --create <branch>` and `wt switch <branch>`
- Inspect active worktrees with `wt list`
- Find your current branch with `git branch --show-current`

When actively editing files or running commands that change code/data (not during planning/discussion-only phases), coordinate through worklogs:

- Write announcements with `agent-worklog <announcement> ...`
- Every `agent-worklog ...` announcement call also reads updates from other agents, so no separate immediate poll is needed after posting
- If you have no new announcement, poll with `agent-worklog` every 20-60 seconds
- The `agent-worklog` program writes to `$(git rev-parse --git-dir)/.agent-worklog.jsonl`, discovers other worktrees via `git worktree list`, and reads each worktree's git-dir `.agent-worklog.jsonl`
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
- If another agent reports they merged via `wt merge --no-remove`, update your branch from `main` promptly (`git merge main` or `git rebase main`) to pick up those changes.
```

