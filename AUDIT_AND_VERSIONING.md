# AUDIT_AND_VERSIONING.md

## Governance objectives
- Complete auditability.
- Immutable history.
- Explicit approvals.
- Reversible change management.
- No silent overwrites.

## Immutable audit log expectations
- Audit log is append-only.
- No update/delete operations in normal runtime.
- Every critical action creates an AuditEvent.
- Events are hash-chained for tamper evidence.

## Required audited actions
- project created/updated
- task created/routed/completed/failed
- agent run start/finish/retry
- prompt record creation
- artifact save/version creation
- approval requested/submitted/rejected
- workflow state transition
- publish/rollback actions
- retrieval query on regulated scopes

## Output versioning rules
- Every artifact write creates a new ArtifactVersion.
- Must include `parent_version_id` except first version.
- Must include checksum + actor identity + change summary.
- “Latest” is pointer, never replacement of historical data.

## Change history and diff visibility
- Diff required between adjacent versions.
- UI must show what changed, who changed it, and when.
- For binary assets, show metadata diff + preview hash change.

## Rollback policy
- Rollback creates new version that references target version (no destructive restore).
- Launch rollback uses saved release pointer.
- Rollback reason required.

## Approval records
- Approval includes scope, decision, actor identity, timestamp, comment.
- Expired approvals are invalid for publish-critical actions.
- Rejection requires remediation task creation.

## Actor identity requirements
- All writes must include authenticated actor.
- Agent actions recorded with both service identity and run identity.
- Human approvals require user identity + role.

## Timestamping rules
- UTC timestamps only.
- Monotonic ordering enforced per project stream.
- Client timestamps never trusted as source of truth.

## Retention expectations
- Audit events: retain indefinitely (or per legal policy, minimum 7 years).
- Artifact versions: retain all production versions; drafts per retention tier.
- Prompt records and traces: retain for governance and retrieval readiness.
