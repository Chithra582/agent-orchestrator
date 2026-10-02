---
name: "worktree-isolated-agent-execution"
description: "Spawns and supervises coding agents within dedicated, isolated Git worktrees and branches."
license: Apache-2.0
---

# Worktree Isolated Agent Execution

## Overview
This skill implements the core isolation architecture of Agent Orchestrator, provisioning unique Git worktrees and task branches for each coding agent to prevent filesystem conflicts and cross-branch collisions.

## Key Capabilities
- **Worktree Provisioning**: Creates and checks out isolated worktrees linked to the primary Git repository.
- **Process Confinement**: Spawns agent CLI processes strictly within the isolated worktree directory.
- **Cleanup & Tear Down**: Safely cleans up worktrees and prunes temporary branches upon task completion.

## Operational Workflow
1. **Task Receipt**: Ingest task specifications, target repository, and agent profile.
2. **Worktree Creation**: Execute `git worktree add` with a fresh task branch.
3. **Worker Launch**: Spawn coding agent process inside the newly created worktree path.
4. **Lifecycle Monitoring**: Track worker process execution until commit, PR creation, or termination.
