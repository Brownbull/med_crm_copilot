# WORKFLOW.md
# Canonical task lifecycle state machine (machine-readable YAML). Version bump on structural change.

```yaml
version: 1
states:
  idea:
    description: "Captured but not yet triaged"
    required: [summary]
    allow_back: []
  triaged:
    description: "Prioritized & estimated"
    required: [summary, priority, estimate]
    allow_back: []
  spec_ready:
    description: "Acceptance criteria defined"
    required: [acceptance_criteria]
    allow_back: [triaged]
  in_dev:
    description: "Actively being implemented"
    required: [assignee]
    allow_back: [spec_ready]
  code_review:
    description: "Awaiting technical approval"
    gate: tech_lead
    required: [pr_link]
    allow_back: [in_dev]
  qa:
    description: "Under test execution"
    gate: qa
    required: [test_notes]
    allow_back: [in_dev]
  ready_release:
    description: "Approved for deployment"
    gate: devops
    required: [deployment_plan]
    allow_back: [qa]
  released:
    description: "Deployed to production environment"
    required: [release_tag]
    allow_back: []
  validated:
    description: "Usage/value confirmed post-release"
    required: [usage_metric_ref]
    allow_back: []

transitions:
  - from: in_dev
    to: code_review
    condition: pr_link_exists
  - from: code_review
    to: qa
    condition: review_status == approved
  - from: qa
    to: ready_release
    condition: all_blocking_tests_pass
  - from: ready_release
    to: released
    condition: deployment_completed
  - from: released
    to: validated
    condition: usage_metric_captured

sla_hours:
  in_dev: 48
  code_review: 8
  qa: 16
  ready_release: 8

fields_required_definitions:
  summary: "One-line concise description"
  priority: "P1|P2|P3"
  estimate: "Numerical hours (int)"
  acceptance_criteria: "List of testable bullet points"
  assignee: "Agent id from AGENTS.md"
  pr_link: "URL to code diff"
  test_notes: "Execution summary / failing cases"
  deployment_plan: "Steps or reference to SOP"
  release_tag: "Semantic/tag reference"
  usage_metric_ref: "Pointer to METRICS.md entry or raw metric"

rejection_rules:
  code_review:
    must_include: ["security considerations", "migration impact"]
  qa:
    must_include: ["test environment", "cases executed"]

rollbacks:
  - trigger: critical_bug_found
    from: released
    to: in_dev
    action: "Create hotfix task; append RELEASE_NOTES.md"

blocked_task_policy:
  detection: "No updated_at change within SLA * 1.25"
  action: "Orchestrator creates ALERT task referencing blocker"

versioning:
  change_requires:
    - Create DECISIONS_LOG entry
    - Increment version
    - Update STATE.md.workflow_version
```

# Human Notes
- Only forward movement except explicit allow_back arrays.
- Gates enforce approval by listed agent id.
- Orchestrator validates required fields before transition commit.
