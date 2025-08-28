# AGENTS.md
# Top-level YAML array defines all agents (machine-readable). Fields required:
# id, type, responsibilities, inputs, outputs, escalation, handoff_protocol, capacity_factor, quality_gate_role, active, fallback
# Additional optional fields: description, phase (for deferred agents)

- id: product_ceo
  type: product
  description: Vision holder, prioritization, scope guardian
  responsibilities:
    - Maintain PRD and PLANNING milestones
    - Prioritize backlog (P1/P2/P3)
    - Approve process changes with tech_lead
    - Manage risk register
  inputs: [essential_PRD_medicalCRM.md, FEEDBACK_LOG.md, METRICS.md, RISKS_REGISTER.md]
  outputs: [PLANNING.md, TASKS_BACKLOG.md, RISKS_REGISTER.md]
  escalation: board
  handoff_protocol: async
  capacity_factor: 0.6
  quality_gate_role: false
  active: true
  fallback: [orchestrator]

- id: orchestrator
  type: control
  description: Central router enforcing workflow & state synchronization
  responsibilities:
    - Enforce WORKFLOW state transitions
    - Detect SLA breaches and create ALERT tasks
    - Maintain STATE.md snapshot
    - Resolve simple dependency deadlocks
    - Publish daily standup summary (if enabled)
  inputs: [WORKFLOW.md, TASKS_BACKLOG.md, STATE.md, AGENTS.md]
  outputs: [TASKS_BACKLOG.md, STATE.md, RELEASE_NOTES.md]
  escalation: product_ceo
  handoff_protocol: synchronous
  capacity_factor: 1.0
  quality_gate_role: false
  active: true
  fallback: [product_ceo]

- id: tech_lead
  type: dev
  description: Architectural oversight and code review gate
  responsibilities:
    - Approve code reviews for gate states
    - Maintain DATA_MODEL.md and DECISIONS_LOG.md
    - Define architectural patterns and guardrails
    - Security_min checklist oversight
  inputs: [essential_PRD_medicalCRM.md, DECISIONS_LOG.md, DATA_MODEL.md, TASKS_BACKLOG.md]
  outputs: [DECISIONS_LOG.md, DATA_MODEL.md]
  escalation: product_ceo
  handoff_protocol: synchronous
  capacity_factor: 0.7
  quality_gate_role: true
  active: true
  fallback: [backend_dev]

- id: backend_dev
  type: dev
  description: Implements API endpoints, models, persistence layer
  responsibilities:
    - Implement tasks in in_dev state assigned
    - Write migrations and update DATA_MODEL proposals
    - Provide PR links for code_review state
    - Add technical notes for QA reproducibility
  inputs: [TASKS_BACKLOG.md, DATA_MODEL.md, DECISIONS_LOG.md]
  outputs: [repository_code, TASKS_BACKLOG.md, DECISIONS_LOG.md]
  escalation: tech_lead
  handoff_protocol: async
  capacity_factor: 1.0
  quality_gate_role: false
  active: true
  fallback: [tech_lead]

- id: frontend_dev
  type: dev
  description: Builds Jinja templates, forms, usability improvements
  responsibilities:
    - Implement UI tasks (patients, calendar, notes)
    - Maintain consistent Spanish terminology
    - Provide accessibility notes (contrast, keyboard nav)
  inputs: [essential_PRD_medicalCRM.md, TASKS_BACKLOG.md, FEEDBACK_LOG.md]
  outputs: [repository_code]
  escalation: tech_lead
  handoff_protocol: async
  capacity_factor: 0.6
  quality_gate_role: false
  active: true
  fallback: [backend_dev]

- id: qa
  type: qa
  description: Quality gate for functional correctness
  responsibilities:
    - Maintain QUALITY_GATE.md and TEST_MATRIX.md
    - Execute test cases for qa state tasks
    - File bugs and link test cases
  inputs: [QUALITY_GATE.md, TEST_MATRIX.md, TASKS_BACKLOG.md]
  outputs: [TEST_MATRIX.md, TASKS_BACKLOG.md]
  escalation: tech_lead
  handoff_protocol: synchronous
  capacity_factor: 0.5
  quality_gate_role: true
  active: true
  fallback: [tech_lead]

- id: devops
  type: ops
  description: Deployment, environment, backups
  responsibilities:
    - Maintain ENVIRONMENT_CONFIG.md and SOPS.md
    - Perform releases (ready_release -> released)
    - Update RELEASE_NOTES.md
    - Ensure daily backup procedure
  inputs: [ENVIRONMENT_CONFIG.md, SOPS.md, TASKS_BACKLOG.md]
  outputs: [RELEASE_NOTES.md, ENVIRONMENT_CONFIG.md]
  escalation: tech_lead
  handoff_protocol: synchronous
  capacity_factor: 0.4
  quality_gate_role: true
  active: true
  fallback: [tech_lead]

- id: analytics
  type: analytics
  description: Collects and updates KPI metrics
  responsibilities:
    - Update METRICS.md weekly
    - Provide metric deltas to product_ceo
    - Flag anomalies (e.g., no new appointments)
  inputs: [METRICS.md, STATE.md]
  outputs: [METRICS.md, TASKS_BACKLOG.md]
  escalation: product_ceo
  handoff_protocol: async
  capacity_factor: 0.2
  quality_gate_role: false
  active: true
  fallback: [product_ceo]

- id: support
  type: support
  description: Captures user feedback and converts to structured insights
  responsibilities:
    - Update FEEDBACK_LOG.md after each user interaction
    - Propose backlog tasks referencing feedback IDs
    - Monitor validated state usage notes
  inputs: [FEEDBACK_LOG.md, STATE.md]
  outputs: [FEEDBACK_LOG.md, TASKS_BACKLOG.md]
  escalation: product_ceo
  handoff_protocol: async
  capacity_factor: 0.3
  quality_gate_role: false
  active: true
  fallback: [product_ceo]

- id: compliance
  type: compliance
  description: Deferred until Phase 2 (MRR ≥ 500) for regulatory tracking
  phase: deferred
  responsibilities:
    - Prepare COMPLIANCE_CHECKLIST.md
    - Security_min quarterly review
  inputs: [SECURITY_MIN.md, COMPLIANCE_CHECKLIST.md]
  outputs: [COMPLIANCE_CHECKLIST.md, TASKS_BACKLOG.md]
  escalation: product_ceo
  handoff_protocol: async
  capacity_factor: 0.0
  quality_gate_role: false
  active: false
  fallback: [tech_lead]

# Fallback Policy Notes:
# 1. Orchestrator reassigns task if primary agent inactive > SLA.
# 2. If no fallback active, escalate to product_ceo for manual intervention.
