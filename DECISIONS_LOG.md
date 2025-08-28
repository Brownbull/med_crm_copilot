# DECISIONS_LOG.md
# Architectural / Product Decisions (ADR-lite). Append-only. Most recent at top.
# Template:
# ### D-XXX | YYYY-MM-DD
# Context:
# Decision:
# Alternatives:
# Consequences (Short-term / Long-term):
# Status: accepted | superseded(by D-YYY)
# Links: (PRD#/DATA_MODEL.md#anchor/etc)

### D-003 | 2025-08-28
Context:
Need rapid user communication and reminder functionality without incurring integration complexity or delays.
Decision:
Use manual WhatsApp reminder generation (copy-paste template) instead of automated API integration for MVP.
Alternatives:
1) WhatsApp Business API (longer approval timeline) 2) Twilio / third-party messaging gateway (added cost + setup) 3) No reminders at all (higher no-show rate).
Consequences (Short-term / Long-term):
Short-term: Faster delivery; immediate value; minimal engineering. Long-term: Requires future automation refactor; manual step could be forgotten; need audit later.
Status: accepted
Links: essential_PRD_medicalCRM.md#18-outbound-messaging-template, TASKS_BACKLOG.md(T-012)

### D-002 | 2025-08-28
Context:
Scheduling implementation complexity risk with variable appointment durations early.
Decision:
Fix all appointments to 30-minute duration for MVP.
Alternatives:
1) Variable durations 2) Slot matrix configuration 3) Free-form start/end times.
Consequences (Short-term / Long-term):
Short-term: Simplified UI & validation; faster development. Long-term: Requires migration to support variable duration; potential re-indexing.
Status: accepted
Links: DATA_MODEL.md(Appointment.duration_minutes), essential_PRD_medicalCRM.md#10-data-model

### D-001 | 2025-08-28
Context:
Need fast development stack aligned with developer experience and low operational overhead.
Decision:
Use Flask + SQLite + Jinja + Bootstrap for MVP monolith.
Alternatives:
1) FastAPI + Postgres + React SPA 2) Django full-stack 3) Node/Express + Vue.
Consequences (Short-term / Long-term):
Short-term: Rapid iteration, minimal infra, low cognitive load. Long-term: Potential refactor for scaling, migration to PostgreSQL & API separation.
Status: accepted
Links: essential_PRD_medicalCRM.md#11-architecture, DATA_MODEL.md#1-overview

# Supersession Log
# (None yet)

# Change Process
# New decision: append entry, reference impacted docs, update STATE.md.last_decisions on snapshot refresh.
