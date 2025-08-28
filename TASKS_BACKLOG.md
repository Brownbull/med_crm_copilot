# TASKS_BACKLOG.md
# Canonical backlog (machine-readable). Maintain YAML list only below.
# States per WORKFLOW.md: idea | triaged | spec_ready | in_dev | code_review | qa | ready_release | released | validated
# Time format: ISO 8601 UTC. Estimates in hours (int). priority: P1|P2|P3

```yaml
- id: T-001
  title: Scaffold Flask project structure
  state: spec_ready
  priority: P1
  estimate: 2
  assignee: backend_dev
  deps: []
  acceptance:
    - "Repository initialized"
    - "Flask app runs locally (hello route 200)"
  tags: [foundation]
  sprint: W1
  created_at: 2025-08-28T23:27:00Z
  updated_at: 2025-08-28T23:27:00Z

- id: T-002
  title: Patient model + migration
  state: idea
  priority: P1
  estimate: 2
  assignee: backend_dev
  deps: [T-001]
  acceptance:
    - "patients table created with RUT unique"
    - "Foreign keys pragma enabled"
  tags: [data]
  sprint: W1
  created_at: 2025-08-28T23:27:00Z
  updated_at: 2025-08-28T23:27:00Z

- id: T-003
  title: Patient CRUD views (list/create/edit)
  state: idea
  priority: P1
  estimate: 4
  assignee: frontend_dev
  deps: [T-002]
  acceptance:
    - "Create form validates required fields"
    - "Edit updates persisted data"
    - "List paginated or simple full list (≤50 ok)"
  tags: [ui,patients]
  sprint: W1
  created_at: 2025-08-28T23:27:00Z
  updated_at: 2025-08-28T23:27:00Z

- id: T-004
  title: Basic authentication (login/logout, session)
  state: idea
  priority: P1
  estimate: 3
  assignee: backend_dev
  deps: [T-001]
  acceptance:
    - "Bcrypt password hash stored"
    - "Protected routes redirect when unauthenticated"
    - "Seed admin user documented"
  tags: [auth]
  sprint: W1
  created_at: 2025-08-28T23:27:00Z
  updated_at: 2025-08-28T23:27:00Z

- id: T-005
  title: Agenda skeleton (today appointments list)
  state: idea
  priority: P2
  estimate: 2
  assignee: frontend_dev
  deps: [T-004]
  acceptance:
    - "Shows date header"
    - "Lists appointments with patient name + time"
  tags: [ui,scheduling]
  sprint: W1
  created_at: 2025-08-28T23:27:00Z
  updated_at: 2025-08-28T23:27:00Z

- id: T-006
  title: Appointment creation form (30-min fixed)
  state: idea
  priority: P1
  estimate: 3
  assignee: backend_dev
  deps: [T-005, T-003]
  acceptance:
    - "Form selects patient"
    - "Prevents overlapping slot"
    - "Persists appointment"
  tags: [scheduling]
  sprint: W1
  created_at: 2025-08-28T23:27:10Z
  updated_at: 2025-08-28T23:27:10Z

- id: T-007
  title: Deployment pipeline (manual Render/VPS)
  state: idea
  priority: P1
  estimate: 2
  assignee: devops
  deps: [T-001]
  acceptance:
    - "Public HTTPS URL reachable"
    - "Document deploy steps in SOPS.md"
  tags: [devops,deployment]
  sprint: W1
  created_at: 2025-08-28T23:27:10Z
  updated_at: 2025-08-28T23:27:10Z

- id: T-008
  title: Feedback capture initial demo (log F-001)
  state: idea
  priority: P2
  estimate: 1
  assignee: support
  deps: [T-003, T-006]
  acceptance:
    - "At least one practitioner feedback entry in FEEDBACK_LOG.md"
  tags: [feedback]
  sprint: W1
  created_at: 2025-08-28T23:27:10Z
  updated_at: 2025-08-28T23:27:10Z

- id: T-009
  title: Payment fields addition (model extension)
  state: idea
  priority: P1
  estimate: 2
  assignee: backend_dev
  deps: [T-006]
  acceptance:
    - "payment_status default unpaid"
    - "Validation requires method+amount when paid"
  tags: [payments]
  sprint: W2
  created_at: 2025-08-28T23:27:10Z
  updated_at: 2025-08-28T23:27:10Z

- id: T-010
  title: Daily income summary view
  state: idea
  priority: P2
  estimate: 3
  assignee: frontend_dev
  deps: [T-009]
  acceptance:
    - "Shows total paid amount for chosen day"
    - "Shows count paid vs unpaid"
  tags: [ui,reporting]
  sprint: W2
  created_at: 2025-08-28T23:27:15Z
  updated_at: 2025-08-28T23:27:15Z

- id: T-011
  title: Visit notes (SOAP) model + form
  state: idea
  priority: P1
  estimate: 4
  assignee: backend_dev
  deps: [T-006]
  acceptance:
    - "All four SOAP fields stored"
    - "Note creation marks appointment completed"
  tags: [clinical]
  sprint: W3
  created_at: 2025-08-28T23:27:15Z
  updated_at: 2025-08-28T23:27:15Z

- id: T-012
  title: Manual WhatsApp reminder generator
  state: idea
  priority: P2
  estimate: 2
  assignee: frontend_dev
  deps: [T-006]
  acceptance:
    - "Generates Spanish template with patient & date"
    - "Copy to clipboard button"
  tags: [reminders]
  sprint: W3
  created_at: 2025-08-28T23:27:15Z
  updated_at: 2025-08-28T23:27:15Z

- id: T-013
  title: Attachment upload (images)
  state: idea
  priority: P2
  estimate: 3
  assignee: backend_dev
  deps: [T-011]
  acceptance:
    - "Accepts jpeg/png/webp ≤5MB"
    - "Stored under ATTACHMENTS_PATH"
  tags: [files]
  sprint: W3
  created_at: 2025-08-28T23:27:15Z
  updated_at: 2025-08-28T23:27:15Z

- id: T-014
  title: Mobile layout polish (responsive adjustments)
  state: idea
  priority: P3
  estimate: 2
  assignee: frontend_dev
  deps: [T-003, T-005]
  acceptance:
    - "Primary pages usable at 375px width"
    - "Tap targets ≥ 40px height"
  tags: [ux]
  sprint: W4
  created_at: 2025-08-28T23:27:20Z
  updated_at: 2025-08-28T23:27:20Z

# Meta / Process Tasks (some already released)

- id: T-900
  title: Create AGENTS.md
  state: released
  priority: P1
  estimate: 1
  assignee: product_ceo
  deps: []
  acceptance:
    - "AGENTS.md present with all core agents"
  tags: [meta]
  sprint: W1
  created_at: 2025-08-28T23:20:00Z
  updated_at: 2025-08-28T23:26:00Z

- id: T-901
  title: Create PLANNING.md
  state: released
  priority: P1
  estimate: 1
  assignee: product_ceo
  deps: []
  acceptance:
    - "Weekly milestones documented"
  tags: [meta]
  sprint: W1
  created_at: 2025-08-28T23:20:30Z
  updated_at: 2025-08-28T23:26:10Z

- id: T-902
  title: Create WORKFLOW.md
  state: released
  priority: P1
  estimate: 1
  assignee: orchestrator
  deps: []
  acceptance:
    - "State machine YAML present"
  tags: [meta,workflow]
  sprint: W1
  created_at: 2025-08-28T23:21:00Z
  updated_at: 2025-08-28T23:26:20Z

- id: T-903
  title: Create DATA_MODEL.md
  state: released
  priority: P1
  estimate: 1
  assignee: tech_lead
  deps: []
  acceptance:
    - "Entities & relationships documented"
  tags: [meta,data]
  sprint: W1
  created_at: 2025-08-28T23:21:30Z
  updated_at: 2025-08-28T23:27:25Z

- id: T-904
  title: Seed TASKS_BACKLOG.md
  state: spec_ready
  priority: P1
  estimate: 1
  assignee: orchestrator
  deps: [T-902]
  acceptance:
    - "Initial tasks T-001..T-014 listed"
  tags: [meta]
  sprint: W1
  created_at: 2025-08-28T23:22:00Z
  updated_at: 2025-08-28T23:27:25Z

- id: T-905
  title: Create remaining meta docs batch 1
  state: idea
  priority: P2
  estimate: 4
  assignee: orchestrator
  deps: [T-904]
  acceptance:
    - "QUALITY_GATE, TEST_MATRIX, DECISIONS_LOG, METRICS, RISKS_REGISTER exist"
  tags: [meta]
  sprint: W1
  created_at: 2025-08-28T23:22:30Z
  updated_at: 2025-08-28T23:22:30Z

- id: T-906
  title: Create remaining meta docs batch 2
  state: idea
  priority: P2
  estimate: 4
  assignee: orchestrator
  deps: [T-905]
  acceptance:
    - "RELEASE_NOTES, SOPS, FEEDBACK_LOG, PROMPTS_LIBRARY, ENVIRONMENT_CONFIG, SECURITY_MIN, COMPLIANCE_CHECKLIST exist"
  tags: [meta]
  sprint: W1
  created_at: 2025-08-28T23:23:00Z
  updated_at: 2025-08-28T23:23:00Z
