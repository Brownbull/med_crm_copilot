# RISKS_REGISTER.md
# Project risk log (machine-readable YAML). Append / update entries; do not delete historical closed risks.

```yaml
version: 1
last_update: 2025-08-28T23:29:45Z
risks:
  - id: R-01
    description: "Scope creep expanding beyond MVP IN list"
    category: product
    impact: high
    probability: medium
    mitigation: "Strict adherence to MVP scope; product_ceo approval required for additions"
    owner: product_ceo
    status: open
    trigger_signals:
      - "Frequent new idea tasks added ( >3 / week )"
      - "Backlog P1 count increases week over week"
    contingency: "Freeze new feature intake; focus on acceptance checklist closure"
  - id: R-02
    description: "Deployment delay blocking early demos"
    category: delivery
    impact: high
    probability: medium
    mitigation: "Start deployment task T-007 mid W1; simple manual steps documented"
    owner: devops
    status: open
    trigger_signals:
      - "No public URL by Day 5"
    contingency: "Switch to local demo with screen share; prioritize deploy hotfix"
  - id: R-03
    description: "Insufficient early user feedback"
    category: adoption
    impact: high
    probability: medium
    mitigation: "Schedule demo by Day 4; support logs feedback after each session"
    owner: support
    status: open
    trigger_signals:
      - "FEEDBACK_LOG.md entries <2 by Day 7"
    contingency: "Cold outreach to additional practitioners; offer incentive"
  - id: R-04
    description: "Data loss (SQLite file corruption or accidental deletion)"
    category: technical
    impact: high
    probability: low
    mitigation: "Daily backup SOP; verify restore monthly"
    owner: devops
    status: open
    trigger_signals:
      - "Missed backup log for >24h"
    contingency: "Restore most recent backup; notify users of potential missing data"
  - id: R-05
    description: "Security breach via weak password or leaked secret"
    category: security
    impact: high
    probability: low
    mitigation: "Bcrypt hashing; enforce min password length; do not commit secrets"
    owner: tech_lead
    status: open
    trigger_signals:
      - "Auth log anomalies"
    contingency: "Rotate SECRET_KEY; force password reset"
  - id: R-06
    description: "Performance degradation due to unoptimized queries"
    category: technical
    impact: medium
    probability: low
    mitigation: "Simple indexed fields (email, rut, (doctor_id,start_time))"
    owner: backend_dev
    status: open
    trigger_signals:
      - "Noticeable latency >1s on CRUD"
    contingency: "Add indexes or simple caching; profile queries"
closed: []
ownership:
  review_cadence: weekly
  escalation: product_ceo
```

# Human Notes
- Close risks by moving entry to closed array with closed_on field.
