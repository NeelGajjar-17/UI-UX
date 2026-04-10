# AGENTS.md

## Agent catalog
Each agent has a narrow mission. All execution is orchestrator-invoked.

---

## 1) Intake & Requirements Agent
- **Mission:** Normalize user goals, constraints, budget, timeline, and business context.
- **Inputs:** raw request, user profile, prior project memory.
- **Outputs:** `requirements_brief.md`, open questions list, risk flags.
- **Dependencies:** retrieval library, policy templates.
- **Handoff rules:** hands off only when required fields complete or marked unknown.
- **Approval checkpoints:** requirements sign-off required.
- **Audit events:** `INTAKE_STARTED`, `INTAKE_COMPLETED`, `REQUIREMENTS_APPROVED`.
- **Failure/retry:** retry once for parse issues; escalate if ambiguity persists.
- **Prohibited behaviors:** inventing requirements; bypassing missing-field flags.

## 2) Website Planning Agent
- **Mission:** Convert requirements into execution plan and milestone scope.
- **Inputs:** approved requirements brief.
- **Outputs:** `website_plan.md` with scope, milestones, risks.
- **Dependencies:** project templates, prior plans.
- **Handoff rules:** requires dependency map + milestone dates.
- **Approval checkpoints:** plan approval required.
- **Audit events:** `PLAN_DRAFTED`, `PLAN_APPROVED`.
- **Failure/retry:** retry with stricter template; fallback to minimal plan mode.
- **Prohibited behaviors:** changing signed requirements silently.

## 3) Sitemap/Page Structure Agent
- **Mission:** Produce site map, page hierarchy, and navigation model.
- **Inputs:** approved plan + requirements.
- **Outputs:** `sitemap.json`, `navigation_map.md`.
- **Dependencies:** IA templates.
- **Handoff rules:** must include page purposes and CTA per page.
- **Approval checkpoints:** sitemap approval required.
- **Audit events:** `SITEMAP_CREATED`, `SITEMAP_APPROVED`.
- **Failure/retry:** retry with reduced complexity.
- **Prohibited behaviors:** creating pages outside approved scope without change request.

## 4) Content Drafting Agent
- **Mission:** Draft copy per page and content blocks.
- **Inputs:** sitemap + brand voice + constraints.
- **Outputs:** `content_pack_vN.md`.
- **Dependencies:** content style guides, reusable snippets.
- **Handoff rules:** each section tagged by page + component.
- **Approval checkpoints:** content approval by page set.
- **Audit events:** `CONTENT_DRAFTED`, `CONTENT_APPROVED`.
- **Failure/retry:** retry with reading-level simplification.
- **Prohibited behaviors:** unverifiable claims; unsupported legal/medical promises.

## 5) Design System Guidance Agent
- **Mission:** Recommend typography, color roles, spacing, and component style rules.
- **Inputs:** brand inputs, content pack, accessibility constraints.
- **Outputs:** `design_tokens.json`, `design_guide.md`.
- **Dependencies:** accessibility policy pack.
- **Handoff rules:** produce contrast checks and responsive rules.
- **Approval checkpoints:** design guidance approval required.
- **Audit events:** `DESIGN_GUIDE_CREATED`, `DESIGN_GUIDE_APPROVED`.
- **Failure/retry:** retry with accessible-safe defaults.
- **Prohibited behaviors:** inaccessible palette recommendation without flag.

## 6) Asset Preparation Agent
- **Mission:** Prepare/optimize images and media metadata.
- **Inputs:** source assets, page mapping, design guide.
- **Outputs:** optimized assets + manifest.
- **Dependencies:** media processing pipeline.
- **Handoff rules:** every asset linked to target page/component.
- **Approval checkpoints:** optional, policy-based.
- **Audit events:** `ASSET_PIPELINE_RUN`, `ASSET_MANIFEST_SAVED`.
- **Failure/retry:** retry conversion; fallback to original with warning.
- **Prohibited behaviors:** destructive overwrite of source assets.

## 7) Page Generation Agent
- **Mission:** Assemble pages in target platform using approved inputs.
- **Inputs:** sitemap, content, design tokens, asset manifest.
- **Outputs:** platform page drafts + page build report.
- **Dependencies:** platform connectors.
- **Handoff rules:** one build report per page with version links.
- **Approval checkpoints:** preview approval required before publish.
- **Audit events:** `PAGE_BUILD_STARTED`, `PAGE_BUILD_COMPLETED`, `PREVIEW_APPROVED`.
- **Failure/retry:** retry failed page only; preserve successful pages.
- **Prohibited behaviors:** publishing without approval token.

## 8) QA/Testing Agent
- **Mission:** Validate links, forms, responsiveness, SEO basics, and performance thresholds.
- **Inputs:** preview build URL + page reports.
- **Outputs:** `qa_report.md`, issue list with severity.
- **Dependencies:** test ruleset.
- **Handoff rules:** block launch on critical issues.
- **Approval checkpoints:** QA waiver or pass required.
- **Audit events:** `QA_STARTED`, `QA_COMPLETED`, `QA_GATE_DECISION`.
- **Failure/retry:** rerun failed checks after fixes.
- **Prohibited behaviors:** downgrading severity without policy.

## 9) Launch/Publish Agent
- **Mission:** Execute governed publish and post-launch validation.
- **Inputs:** approval token, QA pass/waiver, environment config.
- **Outputs:** publish record, launch checklist completion.
- **Dependencies:** deployment connector.
- **Handoff rules:** generate rollback pointer.
- **Approval checkpoints:** explicit launch approval required.
- **Audit events:** `LAUNCH_APPROVED`, `PUBLISH_EXECUTED`, `POSTLAUNCH_VALIDATED`.
- **Failure/retry:** safe rollback then retry once.
- **Prohibited behaviors:** publish on stale approvals.

## 10) Maintenance/Update Requests Agent
- **Mission:** Process post-launch change requests under same governance.
- **Inputs:** change request, current live version, historical context.
- **Outputs:** change plan, updated artifacts, maintenance report.
- **Dependencies:** retrieval library + version service.
- **Handoff rules:** all changes mapped to affected artifacts.
- **Approval checkpoints:** required for content/design/launch-impacting updates.
- **Audit events:** `MAINT_REQ_CREATED`, `MAINT_CHANGE_APPLIED`, `MAINT_RELEASED`.
- **Failure/retry:** partial rollback by artifact scope.
- **Prohibited behaviors:** editing live artifacts without version creation.
