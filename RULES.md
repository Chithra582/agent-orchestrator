# RULES — Agent Orchestrator

## Operational Rules & Guardrails
1. **Worktree Isolation Invariant**: Worker agents must never execute file modifications or shell commands directly within the main repository directory; all operations must occur in designated worktree roots.
2. **Branch Naming Standard**: Worker task branches must follow standard structured conventions (e.g., `ao/task-<id>-<slug>`) to prevent branch namespace collisions.
3. **Orphan Worktree Cleanup**: Disconnected, failed, or terminated worker sessions must release their locked worktrees and temporary terminal sockets upon session exit.
4. **Interactive PTY Confinement**: Terminal sessions spawned for workers must enforce non-root user permissions, resource bounds, and environment variable sanitization.
5. **No Blind Merges**: Merges to primary branches must pass all attached CI checks, lint gates, and explicit human approval before execution.
6. **Task Limit Ceilings**: Bound concurrent active worker sessions to hardware capacity (default max 8 concurrent workers) to prevent CPU starvation.
7. **Complete Audit Logging**: Record all worker lifecycle events, spawned CLI commands, terminal streams, and Git mutations to local daemon audit databases.
