# Meta Documentation Specification (AI-Agent Driven Execution Stack)

Scope: Minimal coordinated doc set enabling autonomous (AI + human) agents to deliver Lumina Health CRM MVP (see essential_PRD_medicalCRM.md) in 30 days and recover seamlessly after interruption.

---

## 1. Canonical Document Set

| File | Purpose (Single Sentence) | Primary Owner Agent | Update Frequency | Machine-Parsable? |
|------|---------------------------|---------------------|------------------|-------------------|
| essential_PRD_medicalCRM.md | Product scope, constraints, acceptance | Product/CEO Agent | Weekly / scope change | N |
| AGENTS.md | Agent roster, capabilities, I/O contracts | Orchestrator Agent | On role/change | Y (YAML blocks) |
| PLANNING.md | 4-week milestones + current sprint goals | Product/CEO Agent | Weekly (rollover) | Y (sections) |
| WORKFLOW.md | State machine + transition rules + mandatory fields | Orchestrator Agent | On process tweak | Y |
| TASKS_BACKLOG.md | Atomic tasks canonical source of truth | Orchestrator Agent | Continuous | Y (YAML list) |
| STATE.md | Latest synchronized system & project state snapshot (resume context) | Orchestrator Agent | After every state change batch | Y |
| QUALITY_GATE.md | Definition of Done + test layers & coverage targets | QA Agent | On criteria change | N |
| TEST_MATRIX.md | Feature → test cases → status map | QA Agent | Daily | Y |
| DATA_MODEL.md | Current entity & schema snapshot | Tech Lead Agent | Schema change | N/Y (code fences) |
| DECISIONS_LOG.md | ADR-lite chronological decision records | Tech Lead Agent | Decision event | N |
| METRICS.md | KPI definitions + latest values | Analytics Agent | Weekly | Y |
| RISKS_REGISTER.md | Risk list with impact/probability/mitigation | Product/CEO Agent | Weekly | Y |
| RELEASE_NOTES.md | Incremental release changelog | DevOps Agent | Each release | N |
| SOPS.md | Operational procedures (deploy, backup, hotfix) | DevOps Agent | As needed | N |
| FEEDBACK_LOG.md | Raw user feedback + tagging | Support Agent | After each session | Y |
| PROMPTS_LIBRARY.md | Standard system & agent prompt templates | Orchestrator Agent | Improvement | N |
| ENVIRONMENT_CONFIG.md | Environment variables (placeholders) & versions | DevOps Agent | Config change | Y |
| SECURITY_MIN.md | Minimal security controls checklist | Tech Lead Agent | Monthly | N |
| COMPLIANCE_CHECKLIST.md (Phase 2+) | Regulatory readiness tracking | Compliance Agent | Monthly | Y |

Optional (later): FINANCIAL_TRACKING.md, STANDUPS.md (rolling daily summaries), INCIDENT_LOG.md.

---

## 2. Agents (AGENTS.md Format Spec)

AGENTS.md MUST contain a top-level YAML array:

```yaml
- id: orchestrator
  type: control
  responsibilities:
    - Maintain workflow integrity
    - Detect stale tasks (>2d same state)
  inputs: [WORKFLOW.md, TASKS_BACKLOG.md, STATE.md]
  outputs: [TASKS_BACKLOG.md, STATE.md]
  escalation: product_ceo
  handoff_protocol: synchronous
```

Required fields per agent: id, type (control|product|dev|qa|ops|support|analytics|compliance), responsibilities[], inputs[], outputs[], escalation, handoff_protocol (sync|async), capacity_factor (1.0 default), quality_gate_role (bool).

Core Agents:

| ID | Role Intent |
|----|-------------|
| product_ceo | Vision, prioritization, scope guardrail |
| orchestrator | Routing, dependency enforcement, state sync |
| tech_lead | Architecture, code review gate, data modeling |
| backend_dev | API, persistence, migrations |
| frontend_dev | UI templates, UX tweaks |
| qa | Test design/execution, QUALITY_GATE enforcement |
| devops | Deployments, backups, env config |
| analytics | Metrics harvesting, KPI updates |
| support | User feedback capture & classification |
| compliance (later) | Security & regulatory tasks |

Fallback: If an agent unavailable, orchestrator reassigns to backup specified under fallback_map in AGENTS.md.

---

## 3. Workflow Specification (WORKFLOW.md)

Include:

```yaml
version: 1
states:
  idea:
    required: [summary]
    allow_back: []
  triaged:
    required: [summary, priority, estimate]
    allow_back: []
  spec_ready:
    required: [acceptance_criteria]
    allow_back: [triaged]
  in_dev:
    required: [assignee]
    allow_back: [spec_ready]
  code_review:
    gate: tech_lead
    required: [pr_link]
    allow_back: [in_dev]
  qa:
    gate: qa
    required: [test_notes]
    allow_back: [in_dev]
  ready_release:
    gate: devops
    required: [deployment_plan]
    allow_back: [qa]
  released:
    required: [release_tag]
    allow_back: []
  validated:
    required: [usage_metric_ref]
    allow_back: []
transitions:
  - from: in_dev
    to: code_review
    condition: pr_link exists
  - from: code_review
    to: qa
    condition: review_status == approved
sla_hours:
  in_dev: 48
  code_review: 8
  qa: 16
```

Any addition: bump version.

---

## 4. Task Backlog Schema (TASKS_BACKLOG.md)

YAML list; each item includes:

Required: id (T-###), title, state, priority (P1..P3), estimate (h), assignee, deps[], acceptance[], created_at, updated_at  
Optional: tags[], risk_flag (bool), sprint (W1..W4), metric_link

Example:

```yaml
- id: T-001
  title: Scaffold Flask project
  state: released
  priority: P1
  estimate: 2h
  assignee: backend_dev
  deps: []
  acceptance:
    - "App boots locally"
    - "Health endpoint returns 200"
  sprint: W1
  created_at: 2025-08-28T10:00:00Z
  updated_at: 2025-08-28T12:05:00Z
- id: T-002
  title: Patient model + migration
  state: in_dev
  priority: P1
  estimate: 2h
  assignee: backend_dev
  deps: [T-001]
  acceptance:
    - "Migration creates patients table"
    - "RUT unique constraint enforced"
  sprint: W1
```

---

## 5. State Persistence (STATE.md)

Purpose: Single snapshot file for context continuity & agent reboot.

Format (YAML with deterministic ordering):

```yaml
version: 1
timestamp: 2025-08-28T19:15:00Z
current_sprint: W1
focus_objective: "Ship scheduling + patient CRUD demo"
open_tasks:
  in_dev: [T-002]
  code_review: []
  qa: []
recent_releases:
  - tag: v0.0.1
    date: 2025-08-28
    highlights: ["Bootstrap skeleton", "Health endpoint"]
metrics_cache:
  total_appointments: 0
  paying_clinics: 0
last_decisions:
  - id: D-003
    summary: "Chose SQLite for MVP"
    date: 2025-08-28
risks_hot:
  - id: R-01
    description: "Scope creep"
    mitigation: "Strict IN list"
env_config_version: 1
data_model_hash: sha256:abcd1234
pending_blockers:
  - task: T-002
    reason: "Awaiting RUT format validation rule"
backlog_digest:
  counts:
    P1: 5
    P2: 7
    P3: 3
next_actions:
  - "Complete patient migration"
  - "Implement patient CRUD views"
handoff_queue:
  - from: backend_dev
    to: tech_lead
    task: T-002
    reason: "Design review after model implementation"
```

Update Triggers: task state change, release creation, planning rollover, decision logged.  
Orchestrator merges incremental changes then writes full snapshot (no partial diffs).  
Agents MUST read STATE.md at startup; if timestamp older than 2h, orchestrator regenerates.

---

## 6. Planning Structure (PLANNING.md)

Sections:
1. Vision (1 paragraph)
2. 30-Day Objective (single measurable target)
3. Weekly Milestones (W1..W4 table)
4. Current Sprint Focus (bulleted)
5. Sprint Commitment (list of task IDs)
6. Stretch Goals (optional)
7. Review Notes (appended weekly under dated heading)

---

## 7. Decision Logging (DECISIONS_LOG.md)

Template:

```
### D-004 | 2025-08-29
Context:
Decision:
Alternatives:
Consequences (Short-term / Long-term):
Status: accepted | superseded(by D-###)
Links: DATA_MODEL.md#patient, PRD#section
```

New decision appended; if superseded add status update to prior entry.

---

## 8. Quality & Testing (QUALITY_GATE.md + TEST_MATRIX.md)

QUALITY_GATE.md contents:
- Definition of Done checklist (code, tests, review, doc update, state sync)
- Test Layers: unit, integration (DB + model), UI manual smoke
- Minimum Requirements MVP: unit coverage (critical models only), integration tests for CRUD, manual smoke pre-release

TEST_MATRIX.md schema (YAML):

```yaml
features:
  patient_crud:
    cases:
      - id: TC-001
        desc: "Create patient valid RUT"
        status: pass
        last_run: 2025-08-28
      - id: TC-002
        desc: "Reject duplicate RUT"
        status: fail
        bug: T-010
```

---

## 9. Metrics (METRICS.md)

YAML:

```yaml
kpis:
  appointments_created:
    target: 60
    current: 0
    last_update: 2025-08-28
  paying_clinics:
    target: 3
    current: 0
  no_show_rate:
    target: "<=25%"
    current: null
```

---

## 10. Risks (RISKS_REGISTER.md)

YAML:

```yaml
- id: R-01
  description: Scope creep
  impact: high
  probability: medium
  mitigation: Strict IN list enforcement
  owner: product_ceo
  status: open
```

---

## 11. Feedback Capture (FEEDBACK_LOG.md)

Structure:

```yaml
- id: F-001
  date: 2025-08-29
  source: Dr. X
  raw: "Quiero ver todas las citas del día en una sola pantalla."
  tags: [usability, scheduling]
  interpretation: "Need condensed agenda view"
  action: candidate
  linked_task: null
```

Support Agent promotes to backlog via new T-### referencing F-###.

---

## 12. Prompts Library (PROMPTS_LIBRARY.md)

Sections per agent:

```
## orchestrator.system
<system prompt>

## backend_dev.task_template
<structured prompt with slots: {task_id} {acceptance} {dependencies}>
```

Changes require note in RELEASE_NOTES.md under "Meta".

---

## 13. Environment Config (ENVIRONMENT_CONFIG.md)

YAML:

```yaml
version: 1
envs:
  APP_ENV: development
  DB_URL: sqlite:///app.db
  SECRET_KEY: {{GENERATE_AT_DEPLOY}}
  ATTACHMENTS_PATH: ./uploads
rotation_policy:
  SECRET_KEY: on compromise
  DB_BACKUP: daily
```

Secrets never committed—placeholders only.

---

## 14. Release Notes (RELEASE_NOTES.md)

Append style:

```
## v0.0.2 - 2025-08-30
Added: Patient CRUD (T-002,T-003)
Changed: Data model hash sha256:ef90...
Meta: Updated WORKFLOW.md (added SLA for code_review)
```

---

## 15. Operational Procedures (SOPS.md)

Required initial SOPs:
- Deployment (manual steps + rollback)
- Backup & Restore (SQLite copy, test monthly)
- Incident Triage (severity levels, response time)
- Access Provisioning (who edits what docs)

---

## 16. Interaction Protocol

| Interaction | Initiator → Receiver | Medium | SLA | Failure Escalation |
|-------------|---------------------|--------|-----|--------------------|
| Code Review Request | backend_dev → tech_lead | TASKS_BACKLOG state change | 8h | orchestrator after 8h |
| QA Handover | tech_lead → qa | STATE.handoff_queue | 16h | orchestrator |
| Release Approval | qa → devops | TASK transition ready_release | 8h | product_ceo |
| Feedback Ingestion | support → product_ceo | FEEDBACK_LOG update | 24h | orchestrator |
| Metrics Update | analytics → product_ceo | METRICS.md commit | 24h (weekly cycle) | orchestrator |

---

## 17. Recovery & Resume Procedure

1. On restart, orchestrator loads STATE.md, validates data_model_hash vs DATA_MODEL.md.
2. If mismatch, raises T-REFRESH-SCHEMA task.
3. Reconstruct in-memory queues from:
   - TASKS_BACKLOG states
   - STATE.handoff_queue
4. Validate staleness: any task with updated_at older than SLA threshold triggers ALERT tasks (priority P1).
5. Regenerate snapshot at completion of first cycle.

---

## 18. Change Control

Any change to PROCESS (WORKFLOW.md / AGENTS.md):
- Create task T-PROC-###
- tech_lead + product_ceo approval (two sign-offs noted in DECISIONS_LOG.md)
- Bump version field
- Update STATE.md with new workflow_version

---

## 19. Minimal Daily Cadence (Recommended)

(Optional STANDUPS.md)

```
### 2025-08-29
yesterday_done: [T-001]
today_focus: [T-002, T-003]
blockers: []
risks_new: none
```

Orchestrator summarizes from task diffs.

---

## 20. Initial Boot Sequence (Order)

1. Create AGENTS.md
2. Create WORKFLOW.md
3. Create TASKS_BACKLOG.md with seed tasks (T-001..T-005)
4. Generate initial STATE.md snapshot
5. Populate PLANNING.md (Weeks 1–4)
6. Add QUALITY_GATE.md & TEST_MATRIX.md (empty skeleton)
7. Commit baseline
8. Begin execution loop (orchestrator tick every hour)

---

## 21. Acceptance of Meta Layer

Meta layer considered operational when:
- [ ] All required files exist with non-empty core sections
- [ ] STATE.md generated and hash fields present
- [ ] At least 5 seed tasks in TASKS_BACKLOG.md
- [ ] WORKFLOW.md validates (no undefined states)
- [ ] AGENTS.md lists all core agents + fallback_map
- [ ] Orchestrator dry-run passes (simulation script or manual checklist)

---

Guiding Principle: Every artifact must either enable autonomy (machine readability), reduce ambiguity, or accelerate recovery. If not, remove it.

End of meta documentation specification.
