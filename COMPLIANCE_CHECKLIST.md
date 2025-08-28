# COMPLIANCE_CHECKLIST.md
# Phase 2+ (post ≥ $500 MRR) regulatory & data protection readiness tracker.
# Not required for initial MVP launch; serves as forward plan & gap register.
# Focus region: Chile (SII invoicing, data protection), general health data good practices (NOT full HIPAA scope initially).

## 1. Scope & Triggers
- Activation Trigger: paying_clinics >= 5 OR MRR >= 500 (whichever first)
- Expansion Trigger: patient_records > 5,000 OR multi-clinic support live
- Review Cadence (post-activation): Monthly

## 2. Compliance Domains
| Domain | Goal | Current Status | Target Phase |
|--------|------|----------------|--------------|
| Data Protection (Chilean law) | Handle personal data with consent & retention policy | planned | Phase 2 |
| Electronic Invoicing (SII) | Generate compliant electronic invoices | planned | Phase 3 |
| Access Control | Role-based least privilege beyond 3 simple roles | pending | Phase 2 |
| Audit Logging | Immutable log of critical actions (login, CRUD) | planned | Phase 2 |
| Data Retention Policy | Define retention & deletion procedure | planned | Phase 2 |
| Incident Response Formalization | Documented runbook with notification steps | partial | Phase 2 |
| Encryption at Rest | Encrypt DB (Postgres + disk / column) | planned | Phase 3 |
| Secure Backups | Encrypted off-site copies | planned | Phase 3 |
| Privacy Policy (Public) | Published transparent policy in Spanish | planned | Phase 2 |
| Terms of Service | Published ToS covering liability & support | planned | Phase 2 |
| Business Continuity | Backup + restore + RTO/RPO documented | partial | Phase 2 |
| Third-Party Service Review | Vendor list & risk assessment | planned | Phase 2 |
| Data Subject Requests | Process for export / delete | planned | Phase 2 |
| Monitoring & Alerts | Threshold-based alerting (security & uptime) | planned | Phase 3 |

## 3. Gap Actions (Initial Backlog Candidates)
| Action ID | Description | Depends | Target | Notes |
|-----------|-------------|---------|--------|-------|
| C-01 | Draft Spanish Privacy Policy | none | Phase 2 | Template adapt |
| C-02 | Draft Terms of Service | C-01 | Phase 2 | Lawyer review optional |
| C-03 | Implement audit log table | Postgres migration | Phase 2 | Minimal append-only |
| C-04 | Add RBAC (doctor vs receptionist constraints) | none | Phase 2 | Fine-grain routes |
| C-05 | Implement data export (patient JSON) | C-03 | Phase 2 | On admin request |
| C-06 | Implement data deletion (logical) | C-05 | Phase 2 | Soft delete flag |
| C-07 | Encrypt backups (zip + password / key) | none | Phase 3 | Rotate key yearly |
| C-08 | Introduce Postgres with encryption-at-rest | infra readiness | Phase 3 | Migrate from SQLite |
| C-09 | SII e-invoicing integration | Postgres, RBAC | Phase 3 | API research |
| C-10 | Monitoring alerts (uptime + error rate) | deployment stack | Phase 3 | Simple cron + ping |

## 4. Consent & Data Handling (Planned Policy Elements)
- Purpose Limitation: Scheduling, patient clinical note storage (basic)
- Data Minimization: Only store required demographics + notes
- Patient Access: Export on request (C-05)
- Deletion: Soft delete until retention period passes (future)
- Retention: Define (draft) 5 years clinical note retention (local norm to confirm)

## 5. SII Integration (Outline)
Steps (future):
1. Research SII electronic invoicing API endpoints & certification
2. Register software & obtain credentials
3. Implement invoice data model (separate from medical records)
4. Logging & error retry strategy
5. Add compliance decision entry (DECISIONS_LOG) when initiated

## 6. Audit Logging Minimal Schema (Planned)
```
audit_log:
  id PK
  ts datetime
  user_id FK users.id nullable (system events)
  event_type enum('login','logout','create','update','delete','export','import')
  entity_type string
  entity_id integer
  metadata json (limited size)
```

## 7. Machine-Readable Tracking Block

```yaml
version: 1
activation:
  trigger_mrr: 500
  trigger_paying_clinics: 5
domains:
  data_protection:
    status: planned
    target_phase: 2
  sii_invoicing:
    status: planned
    target_phase: 3
  audit_logging:
    status: planned
    target_phase: 2
  access_control_rbac:
    status: pending
    target_phase: 2
  encryption_at_rest:
    status: planned
    target_phase: 3
  privacy_policy:
    status: planned
    target_phase: 2
  data_subject_requests:
    status: planned
    target_phase: 2
  secure_backups:
    status: planned
    target_phase: 3
  monitoring_alerts:
    status: planned
    target_phase: 3
gap_actions:
  - id: C-01
    status: pending
  - id: C-02
    status: pending
  - id: C-03
    status: pending
  - id: C-04
    status: pending
  - id: C-05
    status: pending
  - id: C-06
    status: pending
  - id: C-07
    status: pending
  - id: C-08
    status: pending
  - id: C-09
    status: pending
  - id: C-10
    status: pending
change_control:
  requires:
    - "DECISIONS_LOG entry for domain additions"
    - "Version bump on structural YAML changes"
```

## 8. Change Control
- Add new domain: update domains list + DECISIONS_LOG decision
- Bump version in machine-readable block for structural changes
- Reflect activation in STATE.md when triggers met

Version: 1

End of COMPLIANCE_CHECKLIST.
