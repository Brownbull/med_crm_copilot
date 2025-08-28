# STATE.md
# Canonical snapshot for autonomous agent resume. All sections machine-readable YAML.
# Update after any batch of state transitions, meta doc creation, or release event.

```yaml
version: 1
timestamp: 2025-08-28T23:28:20Z
workflow_version: 1
data_model_hash: sha256:TO_COMPUTE_AFTER_IMPLEMENTATION
current_sprint: W1
focus_objective: "Ship patient CRUD + basic scheduling demo and collect first feedback"
backlog_digest:
  counts:
    total: 23
    by_state:
      idea: 15
      spec_ready: 2
      in_dev: 0
      code_review: 0
      qa: 0
      ready_release: 0
      released: 4
      validated: 0
    by_priority:
      P1: 15
      P2: 6
      P3: 2
open_tasks:
  spec_ready: [T-001, T-904]
  idea:
    - T-002
    - T-003
    - T-004
    - T-005
    - T-006
    - T-007
    - T-008
    - T-009
    - T-010
    - T-011
    - T-012
    - T-013
    - T-014
    - T-905
    - T-906
released_tasks: [T-900, T-901, T-902, T-903]
recent_releases: []   # Filled upon first application release (not meta docs)
next_actions:
  - "Advance T-001 to in_dev and implement scaffold"
  - "Define acceptance criteria fully for T-002 then move to spec_ready"
  - "Initiate deployment pipeline task T-007 early to avoid delay risk"
pending_blockers: []
handoff_queue: []
metrics_cache:
  appointments_created: 0
  patients_created: 0
  paying_clinics: 0
  feedback_items: 0
risks_hot:
  - id: R-01
    description: "Scope creep"
    mitigation: "Strict adherence to IN list"
  - id: R-02
    description: "Deployment delay"
    mitigation: "Start T-007 mid-week"
  - id: R-03
    description: "Lack of early feedback"
    mitigation: "Schedule demo by Day 4"
env_config_version: 0   # Will become 1 after ENVIRONMENT_CONFIG.md creation
agents_activity_overview:
  backend_dev:
    active_tasks: [T-001, T-002, T-004, T-006]
  frontend_dev:
    active_tasks: [T-003, T-005]
  devops:
    active_tasks: [T-007]
  support:
    active_tasks: [T-008]
  orchestrator:
    meta_tasks: [T-904, T-905, T-906]
  tech_lead:
    meta_tasks: []
  qa:
    active_tasks: []
  analytics:
    active_tasks: []
  product_ceo:
    meta_tasks: [T-900, T-901]
notes:
  - "State initialized before any code implementation tasks enter in_dev."
  - "data_model_hash placeholder pending first migration generation."
```

# Human Notes
- After moving T-001 to in_dev, orchestrator must update timestamp, counts, and open_tasks.
- metrics_cache fields to sync with METRICS.md once created.
