# ORCHESTRATION_RULES.md

## Routing logic
1. Start from `WorkflowState.current_stage`.
2. Resolve next node in workflow DAG.
3. Check prerequisites:
   - required artifact versions exist,
   - required approvals present and unexpired,
   - policy constraints satisfied.
4. Select agent by node-to-agent mapping.
5. Create `Task` and `AgentRun` records before execution.

## Context passing rules
- Context envelope includes:
  - immutable project metadata,
  - approved artifact version pointers,
  - constrained prompt package,
  - relevant library entries.
- Never pass unapproved draft artifacts to downstream “approved-only” nodes.
- Include provenance pointers for every context object.

## Project state preservation
- State machine values: `INIT`, `IN_PROGRESS`, `WAITING_APPROVAL`, `BLOCKED`, `READY_TO_LAUNCH`, `LIVE`, `MAINTENANCE`.
- Transition is atomic with audit write.
- Recovery on restart uses latest committed `WorkflowState` + pending queue tasks.

## Approval interrupt/resume
- If node requires approval, orchestrator emits `APPROVAL_REQUESTED` and transitions to `WAITING_APPROVAL`.
- Any subsequent execution is blocked until valid approval decision (`APPROVED`/`REJECTED`).
- On approval, orchestrator resumes at same node’s post-approval edge.
- On rejection, open remediation task and route back to owning agent.

## Failure handling and fallback
- Failure classes:
  - transient tool failure,
  - deterministic validation failure,
  - policy violation,
  - unknown runtime error.
- Retries:
  - transient: max 2 with exponential backoff,
  - deterministic: no blind retry; route to remediation.
- Fallback:
  - use simplified template mode,
  - route to human review queue,
  - if unresolved, mark project `BLOCKED` with reason code.

## No silent overwrite policy
- Any artifact mutation must reference `parent_version_id`.
- Version service rejects writes without explicit lineage.
- Orchestrator refuses “replace in place” operations.

## Unverifiable claims policy
- Agents must label uncertain outputs and request source confirmation.
- Orchestrator blocks publish if claim validation gate fails.
