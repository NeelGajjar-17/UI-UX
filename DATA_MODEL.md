# DATA_MODEL.md

## Entity model (v1)

### Project
- `project_id` (PK)
- `name`
- `owner_user_id`
- `status`
- `created_at`, `updated_at`

### User
- `user_id` (PK)
- `email`
- `role` (owner, reviewer, operator, admin)
- `auth_provider_id`
- `created_at`

### Agent
- `agent_id` (PK)
- `name`
- `version`
- `policy_profile`
- `active`

### Task
- `task_id` (PK)
- `project_id` (FK)
- `stage`
- `assigned_agent_id` (FK)
- `status`
- `priority`
- `created_at`, `updated_at`

### AgentRun
- `agent_run_id` (PK)
- `task_id` (FK)
- `agent_id` (FK)
- `input_context_ref`
- `output_artifact_ids[]`
- `status`
- `started_at`, `finished_at`
- `error_code`, `error_detail`

### Artifact
- `artifact_id` (PK)
- `project_id` (FK)
- `type` (requirements, plan, sitemap, content, design, qa, publish_record, etc.)
- `current_version_id` (FK)
- `created_at`

### ArtifactVersion
- `artifact_version_id` (PK)
- `artifact_id` (FK)
- `version_number`
- `parent_version_id` (nullable FK)
- `storage_uri`
- `checksum`
- `created_by_actor_id`
- `created_at`
- `change_summary`

### PromptRecord
- `prompt_record_id` (PK)
- `agent_run_id` (FK)
- `system_prompt_ref`
- `task_prompt_text`
- `context_refs[]`
- `created_at`

### Approval
- `approval_id` (PK)
- `project_id` (FK)
- `scope_type` (task/artifact/version)
- `scope_id`
- `decision` (approved/rejected)
- `comment`
- `actor_user_id`
- `created_at`
- `expires_at`

### AuditEvent
- `audit_event_id` (PK)
- `project_id` (FK)
- `event_type`
- `actor_type` (user/system/agent)
- `actor_id`
- `entity_type`
- `entity_id`
- `payload_json`
- `timestamp`
- `hash_chain_prev`
- `hash_chain_curr`

### WorkflowState
- `workflow_state_id` (PK)
- `project_id` (FK)
- `current_stage`
- `state`
- `pending_approval_scope`
- `last_transition_at`
- `resume_token`

### Feedback
- `feedback_id` (PK)
- `project_id` (FK)
- `artifact_version_id` (FK)
- `author_user_id`
- `rating`
- `comment`
- `created_at`

### LibraryEntry
- `library_entry_id` (PK)
- `project_id` (FK nullable for global template)
- `entry_type` (prompt, template, decision, quality_note, memory, trace)
- `title`
- `content_ref`
- `tags[]`
- `metadata_json`
- `created_at`

## Index recommendations
- `Task(project_id, status)`
- `AgentRun(task_id, status)`
- `Artifact(project_id, type)`
- `ArtifactVersion(artifact_id, version_number)` unique
- `Approval(project_id, scope_id, created_at)`
- `AuditEvent(project_id, timestamp)`
- `LibraryEntry(project_id, entry_type, tags)`

## Integrity constraints
- ArtifactVersion must reference valid parent or null for v1.
- Approval must include actor identity and timestamp.
- Workflow transition must write paired AuditEvent.
