# SECURITY_MIN.md
# Minimal viable security controls for MVP (non-regulated early stage). Lift to formal compliance later.

## 1. Objectives
Provide baseline protection for authentication, data integrity, and confidentiality suitable for pre-revenue healthcare scheduling MVP (NOT full medical compliance).

## 2. Control Summary (Checklist)

| Control | Status | Owner | Verification Frequency |
|---------|--------|-------|------------------------|
| HTTPS enforced (redirect HTTP→HTTPS) | pending | devops | per deploy |
| Password hashing (bcrypt, cost factor >=12) | pending | backend_dev | code review |
| Session protection (secure cookies, HttpOnly) | pending | backend_dev | code review |
| Input validation (RUT, length constraints) | pending | backend_dev | per feature |
| ORM parameterization (avoid raw SQL) | pending | backend_dev | code review |
| File upload whitelist (MIME + size <=5MB) | pending | backend_dev | QA |
| Daily DB backup (SQLite file copy) | pending | devops | daily |
| Backup restore test (monthly) | pending | devops | monthly |
| Secrets not committed (SECRET_KEY via env) | pending | devops | per review |
| Least privilege FS perms (uploads, backups) | pending | devops | monthly |
| Basic audit trail (manual: RELEASE_NOTES + DECISIONS_LOG) | active | product_ceo | weekly |
| Dependency vulnerability scan (manual pip list review) | planned | tech_lead | monthly |

Status values: pending | active | planned | deprecated

## 3. Authentication & Authorization
- Single admin seeded; roles: admin, doctor, receptionist (no granular ACL yet).
- Password Policy (MVP): min 8 chars; future: complexity & 2FA (Phase 2).
- Session timeout (recommendation): 8h inactivity (implement later—manual logout acceptable MVP).

## 4. Data Protection
- At rest: Plain SQLite file (acceptable MVP); encrypt at rest planned with Postgres migration.
- In transit: Must serve only via HTTPS (Let’s Encrypt certificate).
- Attachments: No PHI beyond visit images (still treat as sensitive—avoid public listing).

## 5. Input Validation Requirements
| Field | Rule |
|-------|------|
| email | RFC-like format check |
| rut | Chilean RUT regex + verifier digit |
| phone | Digits + optional + / spaces, length <= 20 |
| name | <= 120 chars |
| payment_amount | int >= 0 |

Reject or sanitize; never trust raw form data.

## 6. Logging & Monitoring (Minimal)
- Log authentication failures (without passwords).
- Log unexpected exceptions (stack trace).
- Do NOT log SECRET_KEY, password hashes, or full session cookies.

## 7. File Upload Security
- Accept only image/jpeg, image/png, image/webp.
- Verify MIME (server-side) AND extension, store with UUID filename.
- No image processing (avoid library attack surface) MVP.

## 8. Backup & Recovery
- Daily copy to backups/ (SOPS.md).
- Manual restore drill monthly; record in STATE.md notes.

## 9. Dependency Management
- Pin critical versions (future requirements.txt freeze).
- Manual monthly review: compare versions to latest; patch security releases.

## 10. Incident Response (Abbreviated)
1. Identify (log anomaly, user report)
2. Classify severity (see QUALITY_GATE)
3. Contain (revoke credentials / rotate SECRET_KEY)
4. Eradicate (patch vulnerability)
5. Recover (redeploy, validate core flows)
6. Document (DECISIONS_LOG + RELEASE_NOTES meta)
7. Follow-up (add tasks to prevent recurrence)

## 11. Secret Management
- Stored in environment (.env local, service config prod).
- Rotation triggers: suspicion, quarterly (optional), external disclosure.
- Procedure: generate new value, redeploy, optionally invalidate sessions.

## 12. Roadmap to Phase 2 Security
| Future Control | Trigger |
|----------------|---------|
| 2FA for admin accounts | ≥3 paying clinics |
| Automated dependency scanning (GitHub Dependabot) | Repo public or >5 dependencies |
| Audit logging table | Clinical notes usage implemented |
| Database encryption (Postgres + TDE or app-layer) | Patient volume > 5k records |
| Role-based fine-grained permissions | Multi-doctor multi-clinic support |

## 13. Machine-Readable Block

```yaml
version: 1
controls:
  https_enforced:
    status: pending
    owner: devops
  bcrypt_password_hashing:
    status: pending
    owner: backend_dev
    params:
      cost: ">=12"
  session_secure_cookies:
    status: pending
    owner: backend_dev
  input_validation_core:
    status: pending
    owner: backend_dev
  orm_parameterization:
    status: active
    owner: backend_dev
  upload_whitelist:
    status: pending
    owner: backend_dev
  daily_backup:
    status: pending
    owner: devops
  backup_restore_test:
    status: pending
    owner: devops
  secrets_externalized:
    status: active
    owner: devops
  fs_permissions_restricted:
    status: pending
    owner: devops
  minimal_audit_trail:
    status: active
    owner: product_ceo
  dependency_review_monthly:
    status: planned
    owner: tech_lead
roadmap_triggers:
  enable_2fa: "paying_clinics >= 3"
  add_audit_table: "visit_notes_created >= 10"
```

## 14. Change Control
- Any new control: append to table + YAML + DECISIONS_LOG reference.
- Version bump on structural change to machine-readable block.

Version: 1
