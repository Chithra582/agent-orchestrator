---
name: "multi-agent-supervision-kanban"
description: "Monitors parallel worker sessions, terminal TUI streams, pull requests, and CI/CD status in real time."
license: Apache-2.0
---

# Multi-Agent Supervision Kanban

## Overview
This skill provides real-time supervisory oversight across all active worker agents, rendering terminal outputs, process health metrics, pull request statuses, and CI/CD feedback in a centralized Kanban interface.

## Key Capabilities
- **Status Synchronization**: Updates task columns dynamically as workers start, code, test, and open pull requests.
- **PTY Terminal Streaming**: Captures and exposes interactive terminal streams for real-time human observation.
- **CI/CD Health Tracking**: Integrates build and test results into task cards to flag blocking regressions instantly.

## Operational Workflow
1. **Event Ingestion**: Collect heartbeat signals, process exits, and Git events from the local daemon.
2. **Column Mutation**: Advance task cards across Kanban swimlanes based on verified state transitions.
3. **Alert Emission**: Trigger notifications if workers experience stalled loops, OOM errors, or test failures.
4. **Summary Compilation**: Aggregate project-level velocity and completion statistics.
