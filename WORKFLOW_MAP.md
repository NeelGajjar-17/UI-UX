# WORKFLOW_MAP.md

## End-to-end workflow map

1. **Intake**
   - Capture requirements and constraints.
   - Output: requirements brief.
   - Gate: requirements approval.

2. **Planning**
   - Build scoped implementation plan.
   - Output: website plan.
   - Gate: plan approval.

3. **Information architecture**
   - Produce sitemap + nav model.
   - Output: sitemap artifacts.
   - Gate: sitemap approval.

4. **Content & design prep**
   - Draft content and design guidance.
   - Output: content pack + design tokens.
   - Gate: combined content/design approval.

5. **Asset preparation**
   - Optimize media + metadata.
   - Output: asset manifest.

6. **Page generation**
   - Build page drafts in target platform.
   - Output: preview build + per-page reports.
   - Gate: preview approval.

7. **QA/testing**
   - Run governed checks.
   - Output: QA report.
   - Gate: QA pass or waiver.

8. **Launch**
   - Execute publish.
   - Output: publish record + rollback pointer.
   - Gate: launch approval.

9. **Maintenance loop**
   - Process updates using same governance workflow.

## Workflow state graph (text)
- `INIT -> IN_PROGRESS`
- `IN_PROGRESS -> WAITING_APPROVAL` (at gated nodes)
- `WAITING_APPROVAL -> IN_PROGRESS` (approved)
- `WAITING_APPROVAL -> BLOCKED` (rejected/no action timeout)
- `IN_PROGRESS -> READY_TO_LAUNCH` (after QA pass)
- `READY_TO_LAUNCH -> LIVE` (publish success)
- `LIVE -> MAINTENANCE` (change request opened)
- `MAINTENANCE -> LIVE` (maintenance release complete)

## Parallelization policy
- Allowed: content drafting and design guidance can run in parallel after sitemap approval.
- Disallowed: launch before QA gate; page generation before content/design approved.

## Human-in-the-loop checkpoints
- Requirements
- Plan
- Sitemap
- Content/Design
- Preview
- QA waiver (if applicable)
- Launch

## SLA targets (initial)
- Intake + planning: < 1 business day
- Content/design cycle: < 2 business days
- QA + launch prep: < 1 business day
- Maintenance request triage: < 4 hours
