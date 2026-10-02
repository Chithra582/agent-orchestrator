# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **Agent Orchestrator** (`agent-orchestrator`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** Agent Orchestrator (`agent-orchestrator`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Developer Tools / Multi-Agent Orchestration & Isolated Workspace Management  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), OWASP LLM Top 10, MITRE ATLAS  

---

## How the Agent Decides

Agent Orchestrator (AO) is a desktop workspace and local daemon designed to plan, run, isolate, and supervise fleets of autonomous coding agents across complex software projects. Rather than forcing multiple agents to collide inside a single shared working directory or scattering developers across disconnected terminal windows, AO organizes development around a strict architectural invariant: **One Task, One Agent, One Worktree**. Every coding worker is provisioned with an isolated Git worktree, dedicated task branch, interactive terminal PTY, and end-to-end status tracking on a centralized Kanban board.

### 1. Decision Architecture

The project planning intake, worktree allocation, worker process execution, telemetry supervision, and pull request integration pipeline operates across a deterministic, five-stage architecture:

```
User Project Goal / Task Card (e.g., "Implement OAuth2 PKCE login and migrate user session tokens")
    │
    ▼
[Stage 1: Task Decomposition & Agent Profile Matching]
    │  - Deconstructs project milestone into modular, non-overlapping task specifications
    │  - Matches task requirements to appropriate agent framework (Claude Code, Cursor, Codex)
    │  - Validates hardware capacity (CPU cores, memory) against active worker allocations
    ▼
[Stage 2: Git Worktree Provisioning & Branch Isolation]
    │  - Allocates dedicated Git worktree linked to the primary repository
    │  - Creates isolated feature branch (`ao/task-<id>-<slug>`) off latest integration baseline
    │  - Mounts project context, environment variable manifests, and linter configurations
    ▼
[Stage 3: Worker Process Spawning & PTY Terminal Supervision]
    │  - Launches coding agent CLI within the confined worktree root
    │  - Spawns interactive PTY session (tmux / xterm.js bridge) and streams stdout/stderr
    │  - Monitors CPU, RAM, execution timeouts, and stuck loop indicators
    ▼
[Stage 4: Automated Verification & CI Status Gathering]
    │  - Executes local test runners and linter suites within the worktree boundary
    │  - Commits validated changes and pushes task branch to remote origin
    │  - Creates pull request and tracks asynchronous CI/CD pipeline webhooks
    ▼
[Stage 5: Review Feedback Streaming & Worktree Teardown]
    │  - Streams reviewer feedback and CI status directly to the Kanban board card
    │  - Upon approved merge, cleans up worktree filesystem path and prunes local branch
    │  - Commits structured session telemetry to the local SQLite audit store
    ▼
Merged Pull Request / Verified Code Changes & Auditable Orchestration Trace
```

### 2. Decision Logic & Routing Formulations

Agent Orchestrator evaluates worker allocation, concurrency load, and worktree safety using deterministic mathematical models:

1. **Worker Capacity & Allocation Index ($A_{\text{worker}}$)**:
   $$A_{\text{worker}}(w, T) = (w_m \cdot M_{\text{match}}) + (w_c \cdot C_{\text{cpu}}) + (w_r \cdot R_{\text{ram}})$$
   where:
   - $M_{\text{match}} \in [0, 1]$ represents affinity between task requirements $T$ and agent capabilities $w$.
   - $C_{\text{cpu}} = \max\left(0, 1 - \frac{\text{CPU}_{\text{used}}}{\text{CPU}_{\text{total}}}\right)$ measures available host compute bandwidth.
   - $R_{\text{ram}} = \max\left(0, 1 - \frac{\text{RAM}_{\text{used}}}{\text{RAM}_{\text{total}}}\right)$ measures available memory headroom.
   - Weights: $w_m = 0.50, w_c = 0.25, w_r = 0.25$ ($\sum w_i = 1.0$).
   - A new worker spawns only when $A_{\text{worker}} \ge 0.60$ and active workers $< N_{\text{max}}$.

2. **Branch Conflict & Rebase Risk ($R_{\text{conflict}}$)**:
   $$R_{\text{conflict}} = \frac{|F_{\text{modified}} \cap F_{\text{upstream\_changed}}|}{|F_{\text{modified}}|}$$
   where $F_{\text{modified}}$ is the set of files touched by the worker branch and $F_{\text{upstream\_changed}}$ represents files altered on `main` since branch creation. If $R_{\text{conflict}} > 0.0$, AO triggers automated worktree rebase or conflict alerts.

### 3. Thresholding & Refusal Decision Criteria

Agent Orchestrator enforces strict operational guardrails to prevent host corruption and branch damage:
- **Refusal to Modify Primary Repository Directly**: Worker processes attempting to modify files outside their designated worktree are blocked (`ERR_OUT_OF_BOUNDS_FILESYSTEM_ACCESS`).
- **Concurrent Worker Ceiling Enforcement**: Spawning additional workers beyond host capacity (default max 8) is queued on the Kanban board (`WARN_MAX_CONCURRENT_WORKERS_REACHED`).
- **Refusal of Force Pushes to Protected Branches**: Destructive git commands (`git push --force` to `main`/`master`) are intercepted and rejected (`ERR_DESTRUCTIVE_GIT_OPERATION_FORBIDDEN`).
- **Worker Process Timeout**: Worker sessions exceeding configured execution ceilings (default 1 hour without commit) trigger inactivity pauses (`WARN_WORKER_INACTIVITY_TIMEOUT`).

### 4. Fallback Decision Mechanism

Continuous multi-agent operational availability is guaranteed through multi-tier fault recovery:
- **Automatic Worktree Clean Re-Creation**: If an agent process corrupts its worktree or leaves uncommitted broken states, AO resets the worktree to the latest clean commit.
- **PTY Disconnect Persistence**: If the desktop UI or terminal disconnects, worker processes continue running under background daemon supervision and reconnect smoothly.
- **Provider Cascade**: When foundation model providers return rate limit errors (HTTP 429), the orchestrator pauses affected workers and cascades to alternate model endpoints.

### 5. Human-in-the-Loop Governance

Human software engineers retain sovereign operational authority:
- **Interactive Kanban Oversight**: Developers monitor all active workers, drag task cards across columns, and review terminal streams live.
- **Manual Takeover Mode**: Developers can attach directly to any worker's terminal PTY session at any moment, issue manual commands, and hand control back to the agent.
- **Mandatory PR Merge Approval**: Pull requests cannot be merged to production branches without explicit human sign-off and passing CI checks.

---

## The Data It Uses

Agent Orchestrator enforces strict data minimization, local machine isolation, and enterprise privacy standards.

### 1. Ingested Input Data

The agent processes only operational assets necessary to orchestrate worker tasks:
- **Task Specifications**: Natural language goals, user stories, acceptance criteria, and priority tags.
- **Source Code Repositories**: Git branches, commits, file trees, and configuration manifests.
- **Worker Execution Streams**: Terminal PTY stdout/stderr, process exit codes, and resource metrics.

### 2. Configuration & Reference Data

- **Workspace Settings**: Repository paths, maximum worker concurrency limits, and agent profiles.
- **Git Provider Configurations**: Scoped GitHub/GitLab personal access tokens or OAuth credentials.
- **Daemon Database**: SQLite database storing task card states, session mappings, and worktree paths.

### 3. Base Model & Inference Lineage

- **Deterministic Orchestrator Core**: Go backend daemon, Electron frontend, Git worktree CLI adapters, and PTY multiplexers operate with 100% determinism.
- **Pluggable Worker LLMs**: Connects to developer-chosen coding agents (Claude 3.5 Sonnet, GPT-4o, Codex) running under user-provided API keys.
- **Zero Training on Repository Code**: Source code, terminal logs, and project tasks are never collected or transmitted for foundation model training.

### 4. Data Privacy, Storage, and Retention

- **OWASP LLM & MITRE ATLAS Hardened**: Defended against prompt injection in untrusted repositories, command injection in worktree names, and credential leakage.
- **Local Machine Isolation**: All task databases, worktree filesystems, and terminal logs reside strictly on the local developer workstation.
- **Automated Credential Redaction**: API tokens, private SSH keys, and personal access tokens are scrubbed from exported logs.
- **Zero Commercial Monetization**: Source code, task descriptions, and developer telemetry are never commercialized, aggregated, or shared with third parties.

---

## Limitations

Understanding the operational boundaries and technical constraints of Agent Orchestrator ensures productive deployment.

### 1. Host Hardware Memory and CPU Bounds
- **Limitation**: Running multiple full-stack coding agents with local compilers or test suites can exhaust physical host RAM and CPU cores.
- **Mitigation**: AO enforces configurable concurrent worker limits, memory caps, and process priority throttling.

### 2. Large Monorepo Worktree Disk Overhead
- **Limitation**: Multi-gigabyte monorepos can consume substantial disk space when multiple worktrees are checked out simultaneously.
- **Mitigation**: AO utilizes Git shared object caches (`git worktree`) and prunes completed worktrees automatically upon PR merge.

### 3. Deep Merge Conflicts Across Parallel Workers
- **Limitation**: Two workers modifying the same core interface file concurrently will inevitably produce merge conflicts.
- **Mitigation**: The planner skill analyzes task dependency graphs to schedule overlapping tasks sequentially rather than in parallel.

### 4. Proprietary Closed-Source Agent CLI Sandboxing
- **Limitation**: Third-party agent CLIs that execute arbitrary system binaries may bypass worktree isolation if not properly bounded.
- **Mitigation**: AO executes worker processes within scoped sub-shells, restricted environment namespaces, and chroot/container boundaries where configured.

### 5. Multi-Repository Distributed Workflows
- **Limitation**: Pull requests spanning multiple detached git repositories cannot be encapsulated in a single Git worktree.
- **Mitigation**: AO provisions linked multi-repo task groups coordinated via shared Kanban dependency tags.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Decision logic & routing formulations | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested task specs, git repos & execution streams | Section 1 | Verified |
| - Configuration, workspace settings & daemon database | Section 2 | Verified |
| - Base model lineage & deterministic orchestrator core | Section 3 | Verified |
| - Data privacy, retention lifecycle & MITRE/OWASP | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - Host hardware memory and CPU bounds | Section 1 | Verified |
| - Large monorepo worktree disk overhead | Section 2 | Verified |
| - Deep merge conflicts across parallel workers | Section 3 | Verified |
| - Proprietary closed-source agent CLI sandboxing | Section 4 | Verified |
| - Multi-repository distributed workflows | Section 5 | Verified |
