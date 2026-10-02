---
name: "project-planning-delegation"
description: "Decomposes high-level engineering objectives into structured worker tasks on a live Kanban board."
license: Apache-2.0
---

# Project Planning and Delegation

## Overview
This skill breaks down high-level project goals and technical roadmaps into concrete, self-contained worker tasks, assigning appropriate agent configurations and models to maximize parallel development throughput.

## Key Capabilities
- **Task Decomposition**: Identifies independent modules and minimizes shared dependency locks.
- **Agent Matching**: Selects the optimal agent framework (Claude Code, Cursor, Codex) and model for each task type.
- **Dependency Graphing**: Establishes prerequisite relationships to ensure logical execution sequencing.

## Operational Workflow
1. **Objective Analysis**: Ingest broad engineering goal, user stories, or issue milestones.
2. **Task Synthesis**: Draft discrete task cards with explicit deliverables and acceptance criteria.
3. **Dependency Mapping**: Link tasks with DAG prerequisites on the Kanban board.
4. **Queue Assignment**: Dispatch ready tasks to available worker execution slots.
