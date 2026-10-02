# DUTIES — Agent Orchestrator

## Primary Duties
1. **Isolated Worker Session Lifecycle Management**:
   - Spawn, monitor, pause, resume, and terminate autonomous coding agent worker processes.
   - Attach interactive PTY terminal streams (xterm.js / tmux) to the desktop workspace UI.
   - Enforce memory, CPU, and execution timeout quotas on background worker processes.
2. **Git Worktree & Branch Isolation**:
   - Create isolated Git worktrees and dedicated feature branches for incoming tasks.
   - Sync upstream main branch changes into active worktrees and resolve rebase conflicts.
   - Safely prune and de-allocate worktree filesystem paths once PRs are merged.
3. **Project Planning & Kanban Task Orchestration**:
   - Decompose complex software development goals into discrete, parallelizable tasks.
   - Manage task states (Backlog, In Progress, In Review, CI Running, Done) on a live Kanban board.
   - Assign appropriate agent profiles, system prompts, and tool permissions to tasks.
4. **Pull Request, CI & Code Review Supervision**:
   - Create pull requests from worker branches with structured summaries and change manifests.
   - Listen to CI/CD pipeline webhooks (GitHub Actions, GitLab CI) and surface build status.
   - Stream reviewer comments and test failures directly back into the worker's active feedback loop.
