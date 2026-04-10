# API_SURFACE.md

## API style
- REST JSON (v1), idempotency keys on mutating endpoints.
- Auth: OAuth/JWT.
- Correlation headers: `X-Request-ID`, `X-Project-ID`.

## Core endpoints

### 1) Project creation
`POST /v1/projects`
- Request: name, owner, website_type, constraints
- Response: project_id, initial workflow_state

### 2) Task routing
`POST /v1/projects/{projectId}/tasks/route`
- Request: stage, trigger_reason
- Response: task_id, assigned_agent, status

### 3) Agent execution
`POST /v1/tasks/{taskId}/execute`
- Request: run_mode, context_refs, policy_profile
- Response: agent_run_id, status

### 4) Artifact save
`POST /v1/projects/{projectId}/artifacts`
- Request: type, content_ref, metadata
- Response: artifact_id, artifact_version_id

### 5) Artifact versioning
`POST /v1/artifacts/{artifactId}/versions`
- Request: parent_version_id, content_ref, change_summary
- Response: artifact_version_id, version_number, diff_ref

### 6) Audit write
`POST /v1/audit/events`
- Request: project_id, event_type, actor, entity, payload
- Response: audit_event_id, hash_chain_curr

### 7) Workflow resume
`POST /v1/projects/{projectId}/workflow/resume`
- Request: resume_token
- Response: workflow_state, next_task

### 8) Approval submission
`POST /v1/projects/{projectId}/approvals`
- Request: scope_type, scope_id, decision, comment
- Response: approval_id, workflow_effect

### 9) Retrieval query
`POST /v1/library/query`
- Request: project_id, query_text, filters, top_k
- Response: entries[], provenance

## Supporting endpoints
- `GET /v1/projects/{projectId}/workflow`
- `GET /v1/projects/{projectId}/timeline`
- `GET /v1/artifacts/{artifactId}/versions`
- `GET /v1/agent-runs/{agentRunId}`
- `POST /v1/projects/{projectId}/rollback`

## Error model
```json
{
  "error_code": "APPROVAL_REQUIRED",
  "message": "Launch requires valid approval.",
  "details": {"scope": "launch"},
  "request_id": "..."
}
```

## Policy enforcement notes
- Publish endpoints must validate current QA gate + launch approval.
- Versioning endpoints reject writes without parent lineage.
- Retrieval endpoint must include provenance references.
