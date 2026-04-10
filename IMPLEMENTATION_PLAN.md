# IMPLEMENTATION_PLAN.md

## Phase 1: shell + orchestration skeleton
### Goal
Establish runnable platform skeleton and workflow engine backbone.

### Exact build items
- Repo scaffolding for frontend, backend, orchestrator, workers.
- Workflow DAG definition + basic state machine.
- Task queue + agent runner stub interface.
- Minimal project/task APIs.

### Exit criteria
- Create project and run mocked intake->plan workflow path.
- Workflow states persist across service restart.

### Risks
- Over-coupling orchestrator and worker code.
- Unclear stage boundaries.

### What must not happen
- Direct agent-to-agent calls bypassing orchestrator.
- Hardcoded one-off flows without state transitions.

---

## Phase 2: identity + storage + audit + versioning
### Goal
Implement governance foundation before feature expansion.

### Exact build items
- AuthN/AuthZ with role model.
- DB schema for required entities.
- Object storage integration for artifacts.
- Append-only audit service with hash chain.
- Version service + diff generation.

### Exit criteria
- Every artifact write creates version + audit event.
- Approval records include actor identity and UTC timestamp.

### Risks
- Missing lineage on early artifact saves.
- Inconsistent actor identity propagation.

### What must not happen
- Mutable audit records.
- Overwriting artifact content in place.

---

## Phase 3: first core agents
### Goal
Deliver first real business value with governed automated flow.

### Exact build items
- Implement Intake, Planning, Sitemap, Content, Page Generation agents.
- Standard agent I/O schema + run trace model.
- Approval checkpoints on requirements, plan, sitemap, preview.

### Exit criteria
- End-to-end draft website artifacts generated through orchestrator.
- Failed runs produce retry or remediation tasks correctly.

### Risks
- Prompt drift across agents.
- Output schema inconsistency.

### What must not happen
- Agent writes outside version service.
- Unapproved artifacts moving into publish path.

---

## Phase 4: workflow memory and shared context
### Goal
Make workflows context-aware and consistent across iterations.

### Exact build items
- Context pack builder with provenance.
- LibraryEntry writes for decisions, quality notes, prompts.
- Context redaction and scope policies.

### Exit criteria
- Downstream agents consume approved upstream outputs via references.
- Re-run of task uses historical context deterministically.

### Risks
- Context bloat and irrelevant retrieval.
- Leakage of unapproved drafts.

### What must not happen
- Passing raw ungoverned context between agents.
- Hidden prompt mutations without records.

---

## Phase 5: unified UX
### Goal
Deliver single user workspace with approval-driven flow control.

### Exact build items
- Workflow timeline UI.
- Approval inbox + diff viewer.
- Artifact version browser + rollback action UI.
- Clear status surfaces for blocked/waiting/resumed states.

### Exit criteria
- Non-technical user can run full workflow without per-agent control.
- Every approval and change visible in UI.

### Risks
- UX complexity exposing too much internal detail.
- Weak explanations for blocked states.

### What must not happen
- Hidden transitions.
- Publish action without explicit approval UI path.

---

## Phase 6: retrieval-ready project library
### Goal
Implement governed knowledge library for future retrieval and review.

### Exact build items
- Library ingestion for prompts, outputs, traces, decisions, templates.
- Metadata tagging and filtering strategy.
- Retrieval API with provenance output.
- Quality note capture linked to artifacts/agent runs.

### Exit criteria
- Retrieval query returns relevant entries with source pointers.
- Auditors can trace output back to prompts and approvals.

### Risks
- Noisy library entries reducing retrieval precision.
- Missing metadata standards.

### What must not happen
- Claims of “self-learning model training” from user data in v1.
- Retrieval results without provenance.

---

## Phase 7: scale-out agents, observability, and hardening
### Goal
Prepare platform for production reliability and expansion.

### Exact build items
- Add QA, Launch, Maintenance, Asset, Design agents.
- Observability: metrics, traces, SLOs, alerting.
- Failure injection testing and chaos scenarios.
- Concurrency controls, rate limits, and tenant isolation checks.

### Exit criteria
- System meets reliability targets on representative load.
- Critical flows have runbooks and on-call alerts.
- Governance invariants pass automated policy tests.

### Risks
- Agent sprawl increasing orchestration complexity.
- Cost/latency spikes under parallel workloads.

### What must not happen
- Scaling by weakening audit/version controls.
- Launching without tested rollback path.

---

## Implementation guardrails (all phases)
- Workflow over isolated tools.
- Governance first, always.
- Version everything.
- Never silently overwrite.
- Preserve full audit trail.
- No unverifiable AI claims.
