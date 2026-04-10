# SYSTEM_ARCHITECTURE.md

## Purpose
Production-oriented architecture for a governed multi-agent platform that automates website building through one orchestrated workflow.

## Core components

### 1) Frontend (Unified UX)
- Single project workspace for intake, approvals, previews, and publishing.
- No direct per-agent UI required for end users.
- Shows workflow status, pending approvals, diffs, and audit timeline.

### 2) Backend API
- AuthN/AuthZ, project/task APIs, artifact APIs, approval APIs, retrieval APIs.
- Enforces governance constraints (approval gates, versioning, immutability rules).

### 3) Orchestrator (Control Plane)
- Owns workflow graph, routing, policy checks, and state transitions.
- Decides **which agent runs next**, with what context and constraints.
- Pauses on approval checkpoints; resumes only after valid approval events.

### 4) Agent Runtime (Execution Plane)
- Runs specialized agents in isolated workers.
- Executes prompts/tools within per-agent policy envelopes.
- Returns structured outputs + traces + confidence/quality notes.

### 5) Storage layer
- Relational DB for projects, tasks, runs, states, approvals, and metadata.
- Object store for artifacts (docs, pages, assets, exports).
- Search index/vector store for retrieval-ready library.

### 6) Audit service
- Append-only immutable event log.
- Records all material actions: requests, runs, approvals, versions, publishes.

### 7) Version service
- Creates immutable ArtifactVersions.
- Produces diffs and supports rollback pointers.
- Prevents silent overwrite by requiring explicit parent version references.

### 8) Retrieval Library service
- Curates project memory and reusable entries.
- Stores prompts, outputs, quality notes, decisions, and template references.
- Supports retrieval queries for future runs and review.

## Request-to-workflow lifecycle
1. User submits request (e.g., “build a 5-page service site”).
2. Backend validates identity, creates Project + initial Task.
3. Orchestrator initializes WorkflowState and routes to Intake Agent.
4. Each agent run writes outputs as new ArtifactVersions.
5. Approval-required nodes pause workflow until user/authorized reviewer approves.
6. On approval, orchestrator resumes and routes next task.
7. QA and Launch agents produce readiness and publish artifacts.
8. Final state + complete audit chain stored in library for retrieval and maintenance.

## Orchestration vs execution boundaries

### Orchestration responsibilities
- Workflow graph and sequencing.
- Policy enforcement and governance checks.
- Context assembly and redaction.
- Retry/fallback strategy.
- Pause/resume and approval gating.

### Execution responsibilities (agents)
- Perform scoped domain task only.
- Produce outputs in required schema.
- Emit trace + quality notes.
- Never bypass governance or write directly without version service.

## Deployment view (minimum production)
- Frontend app
- Backend API service
- Orchestrator service
- Agent workers queue/runner
- DB + object storage + search index
- Audit log store (append-only)
- Monitoring stack (metrics, tracing, alerting)

## Non-functional requirements
- Idempotent task operations.
- End-to-end traceability by ProjectID/TaskID/AgentRunID.
- Strong access control by role.
- Deterministic state transitions.
- Safe degradation on agent/tool failure.
