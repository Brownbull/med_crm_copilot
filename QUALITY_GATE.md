# QUALITY_GATE.md
# Definition of Done (DoD) and release quality criteria (human-readable + machine-checkable sections)

## 1. Definition of Done (Applies to any task leaving in_dev → code_review)
- Code implements all listed acceptance criteria
- No high severity lint errors (optional if linter configured)
- Basic runtime smoke passes locally
- Relevant docs updated (DATA_MODEL.md / SOPS.md / PROMPTS_LIBRARY.md if impacted)
- Tests added/updated (unit or integration where applicable)
- Task entry updated (estimate unchanged OR variance noted in comments field if added later)

## 2. Code Review Gate (code_review → qa)
Reviewer: tech_lead  
Checklist:
- [ ] Security considerations addressed (auth, input validation)
- [ ] Migration impact assessed (if schema touched)
- [ ] No obvious performance pitfalls for expected scale
- [ ] Simplicity: no premature abstraction
- [ ] DATA_MODEL hash unaffected or updated with decision log reference

Failure handling: Add note to task; return state to in_dev.

## 3. QA Gate (qa → ready_release)
Gate Agent: qa  
Required artifacts:
- Test notes (TASKS_BACKLOG test_notes field)
- Updated TEST_MATRIX case statuses

Checklist:
- [ ] All acceptance criteria pass
- [ ] Regression smoke (patient CRUD, appointment create) passes
- [ ] Critical path manual tests executed (booking → payment mark path)
- [ ] No open P1 bugs linked
- [ ] Known limitations documented (if any)

## 4. Release Gate (ready_release → released)
Gate Agent: devops  
Checklist:
- [ ] Deployment plan defined or referenced in SOPS.md
- [ ] Environment variables present (ENVIRONMENT_CONFIG.md version current)
- [ ] Backup completed within last 24h
- [ ] Rollback steps validated
- [ ] RELEASE_NOTES.md draft entry prepared

## 5. Validation (released → validated)
Owner: product_ceo / analytics  
Criteria:
- [ ] Feature exercised by at least one real user OR demo feedback logged (FEEDBACK_LOG.md)
- [ ] Metric updated in METRICS.md (if feature affects tracked KPI)
- [ ] No showstopper issues within 48h (or earlier for fast iteration)

## 6. Test Layers (MVP Scope)
| Layer | Scope | Tools | Required? |
|-------|-------|-------|-----------|
| Unit | Model helpers, validators | pytest | Target key helpers only |
| Integration | DB CRUD for core entities | pytest + temp SQLite | REQUIRED |
| Manual UI Smoke | Patient CRUD, booking flow | Human checklist | REQUIRED |
| Regression Script (future) | Critical paths | TBD | Optional (Phase 2) |

## 7. Minimum Coverage Expectations (Lightweight)
- Patient model CRUD integration tests
- Appointment creation + overlap prevention test
- Payment field conditional test (when implemented)
Coverage metrics not enforced numerically MVP; focus on critical path presence.

## 8. Severity Classification (For bugs)
| Severity | Description | Action SLA |
|----------|-------------|-----------|
| P1 | Data loss, security, core path broken | Fix or rollback < 24h |
| P2 | Functional error with workaround | Next sprint |
| P3 | Minor UI / cosmetic | Backlog grooming |

## 9. Automation Roadmap (Deferred)
- Lint + basic tests pre-commit (Phase 2)
- CI pipeline (Phase 2)
- Synthetic booking scenario (Phase 2)

## 10. Machine-Readable Gate Summary (YAML)

```yaml
version: 1
gates:
  code_review:
    required_fields: [pr_link]
    checklist:
      - security_considered
      - migration_assessed
      - simplicity_verified
  qa:
    required_fields: [test_notes]
    checklist:
      - acceptance_pass
      - regression_smoke_pass
      - no_open_p1
  ready_release:
    required_fields: [deployment_plan]
    checklist:
      - env_config_current
      - recent_backup
      - rollback_defined
  validated:
    required_fields: [usage_metric_ref]
    checklist:
      - user_feedback_logged
      - metric_updated
severity_sla_hours:
  P1: 24
  P2: 168
  P3: 0   # backlog grooming only
```

## 11. Change Control
Any adjustment to gate checklists requires:
1. DECISIONS_LOG entry (reason + impact)
2. Increment machine-readable version
3. STATE.md update referencing new quality_gate_version (to add when implemented)

End of QUALITY_GATE.
