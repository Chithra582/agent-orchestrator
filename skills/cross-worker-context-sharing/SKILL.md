---
name: "cross-worker-context-sharing"
description: "Aggregates, indexes, and shares project context, dependency graphs, and review notes across worker agents."
license: Apache-2.0
---

# Cross-Worker Context Sharing

## Overview
This skill coordinates knowledge transfer and shared contextual awareness across parallel worker agents, ensuring that newly merged interfaces, shared utility functions, and architectural decisions propagate cleanly.

## Key Capabilities
- **Interface Propagation**: Alerts downstream worker sessions when upstream prerequisite PRs are merged.
- **Shared Codebase Indexing**: Maintains an updated symbol table and AST index across the repository.
- **Review Feedback Broadcast**: Shares relevant code review patterns and guidelines across all active agents.

## Operational Workflow
1. **Merge Detection**: Detect when a worker branch merges into the integration baseline.
2. **Impact Analysis**: Identify downstream worktrees dependent on modified modules.
3. **Rebase Notification**: Prompt affected workers to rebase against the updated branch.
4. **Context Injection**: Attach updated interface signatures to active worker context prompts.
