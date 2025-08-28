# SOPS.md
# Standard Operating Procedures (MVP). Keep minimal & actionable.

## 1. Deployment (Manual)
Environment: single VPS / Render (production == staging)
Pre-req: ENVIRONMENT_CONFIG.md variables populated (SECRET_KEY generated)
Steps:
1. Pull latest main branch
2. Install deps: pip install -r requirements.txt (if requirements file present)
3. Run DB migrations (if any) OR create fresh SQLite file if first deploy
4. Start (or restart) gunicorn / flask run service (document command used)
5. Visit /health (add later) to confirm 200
6. Update RELEASE_NOTES.md (add pending section)
7. Capture deployment timestamp in STATE.md recent_releases (on first real release)

Rollback:
1. Stop service
2. Replace app directory with previous tagged commit
3. Restore last backup SQLite file
4. Restart service and verify /health

## 2. Backup & Restore
Frequency: Daily (automated script later; manual now)
Backup Steps:
1. Stop writes (optional for low traffic) or ensure low activity
2. Copy app.db to backups/app-{YYYYMMDD}.db
3. Verify file size > 0
4. Log success (append note in STATE.md notes)
Retention: Keep last 14 daily copies
Restore Steps:
1. Stop service
2. Copy chosen backup file to app.db
3. Restart service
4. Verify patient count matches expected
Test Restore: Perform monthly (create T-RESTORE task)

## 3. Incident Triage
Severity Levels (map to QUALITY_GATE.md severities):
- P1: Data loss, security breach, core path down
- P2: Functional error with workaround
- P3: Minor UI issue
Process:
1. Classify severity
2. Create/Update task (tag: incident)
3. For P1: freeze feature work; assign to backend_dev or tech_lead
4. Post-mitigation: add summary to RELEASE_NOTES (Meta section) and DECISIONS_LOG if permanent change

## 4. Access Provisioning
Roles: admin (bootstrap), doctor, receptionist
Procedure:
1. Admin creates user via manual script (until UI implemented)
2. Store only hashed password (bcrypt)
3. Revoke access: mark user inactive (future) or delete (MVP)
Principle: Minimum accounts (≤5) during MVP

## 5. Environment Variable Management
File: ENVIRONMENT_CONFIG.md
Secrets (local .env not committed):
- SECRET_KEY
- (Future) DB_URL override for Postgres
Rotation (if compromise suspected):
1. Generate new SECRET_KEY
2. Restart service
3. Invalidate sessions (optional MVP)

## 6. Logging & Monitoring (Lightweight)
- Console/stdout logs only
- Manual log review when bug suspected
- Future: structured logging (Phase 2)

## 7. Release Procedure Checklist
1. All required test cases pass (TEST_MATRIX required_pass list)
2. QUALITY_GATE release checklist satisfied
3. Increment version in RELEASE_NOTES.md
4. Tag Git commit (optional MVP)
5. Update STATE.md recent_releases

## 8. Hotfix Procedure
1. Branch from current main (or apply minimal patch)
2. Implement fix (skip non-critical refactors)
3. Minimal test (affected path + smoke)
4. Deploy following Deployment SOP
5. Update RELEASE_NOTES (Fixed section)
6. Add DECISIONS_LOG if architectural change

## 9. Data Migration Procedure (Schema Change)
1. Draft migration SQL (idempotent check)
2. Backup DB
3. Apply migration locally & verify
4. Apply to production
5. Update DATA_MODEL.md + data_model_hash
6. DECISIONS_LOG entry referencing migration

## 10. File Attachments Handling
Upload Path: ATTACHMENTS_PATH (ENV)
Validation:
- MIME & extension check
- Size ≤ 5MB
Process:
1. Save with UUID filename
2. Store relative path in DB
3. On deletion: remove file; keep DB record until explicit removal logic added

## 11. Security Quick Audit (Weekly)
Checklist:
- HTTPS certificate valid
- SECRET_KEY not rotated recently without documentation
- SQLite file permissions (not world-writable)
- No secrets in repo history (spot check)

## 12. SOP Change Control
Changes require:
- Update this file section
- DECISIONS_LOG entry if impacting reliability/security
- Update version marker below if structural (current version 1)

Version: 1
