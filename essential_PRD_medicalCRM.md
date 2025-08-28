# Essential Product Requirements Document (PRD) – Lumina Health CRM (MVP Focus)

Version 1.0 (Distilled)  
Time Horizon: First 30 Days (Revenue MVP)  
Goal: Acquire 3 paying solo practitioners at $30/month each within 30 days

---

## 1. Product Summary (One Line)
Spanish-first lightweight health CRM replacing WhatsApp + Excel for solo Chilean practitioners: scheduling, patient records, and payment tracking with minimal friction.

## 2. Core Problem (Validated)
Practitioners waste daily time coordinating appointments, lose track of payments, and lack centralized patient history because data is scattered across WhatsApp chats, paper, and spreadsheets.

## 3. Target User (Initial Narrow Segment)
Solo private medical professionals (generalists & common specialists) in Villarrica using manual / ad‑hoc tools.  
Tech level: Basic. Device: Desktop + Smartphone (WhatsApp Web). Budget: $30–50/month.

## 4. Primary Value Proposition
Centralize appointments, patient info, visit notes, and payment status in one simple, Spanish interface that reduces no‑shows and admin time without requiring training.

## 5. Success Criteria (First 30 Days)
- 3 clinics actively booking (≥10 appointments each)
- ≥60 total appointments logged
- ≥70% appointments marked with payment status
- No-show rate baseline captured and reminder workflow tested
- Verbal willingness from all 3 to continue paying after trial

## 6. MVP Scope (IN)
1. Authentication (basic: email + password; 3 roles: Admin, Doctor, Receptionist)
2. Patient CRUD (RUT, name, phone, optional email, birth date, notes)
3. Scheduling: Create / edit / cancel 30‑min appointments; day & week agenda view; status: scheduled, completed, no_show, cancelled
4. Visit notes (SOAP free-text fields) tied to appointment
5. Payment status per appointment (paid / unpaid + method + amount)
6. Manual WhatsApp reminder helper: generate prefilled text + copy button
7. Daily income summary (aggregate of marked paid visits)
8. Spanish UI (single locale)
9. File attachments (images only, size limit 5MB) to visit

## 7. Explicitly OUT of Scope (Phase Later)
- Multi-clinic multi-tenant billing logic
- Automated WhatsApp API sending
- Advanced permissions / custom roles
- Electronic invoicing (SII)
- Reporting dashboards beyond daily summary
- Patient portal / self-booking
- Mobile app / React SPA
- Telemedicine / video
- Complex recurrence scheduling
- Analytics / exports (CSV optional later)

## 8. User Roles (Simple)
- Admin: Full (patients, appointments, notes, payments, users)
- Doctor: Appointments, patients, notes, mark payments
- Receptionist: Patients, appointments, payment marking (no user management)
No granular permission matrix initially.

## 9. Key Workflows (Performance Targets)
- Add patient: < 20s
- Book appointment: < 30s
- Mark payment: < 10s
- Create visit note: < 2 min
- Send reminder: < 15s (generate + copy)

## 10. Data Model (Initial)

```python
class User:
    id: int
    email: str
    password_hash: str
    role: str  # 'admin','doctor','receptionist'
    created_at: datetime

class Patient:
    id: int
    rut: str  # unique
    name: str
    phone: str
    email: str | None
    birth_date: date | None
    medical_notes: str | None
    created_at: datetime

class Appointment:
    id: int
    patient_id: int
    doctor_id: int  # references User with role doctor
    start_time: datetime
    duration_minutes: int  # default 30
    status: str  # 'scheduled','completed','no_show','cancelled'
    notes: str | None
    payment_status: str  # 'paid','unpaid'
    payment_method: str | None  # 'cash','transfer','card' (if paid)
    payment_amount: int | None
    created_at: datetime

class VisitNote:
    id: int
    appointment_id: int
    subjective: str
    objective: str
    assessment: str
    plan: str
    created_at: datetime

class Attachment:
    id: int
    visit_note_id: int
    file_path: str
    original_name: str
    uploaded_at: datetime
```

Indexes: (Patient.rut), (Appointment.start_time, doctor_id), (Appointment.patient_id)

## 11. Architecture (Phase 0–1)
Flask (monolith) + SQLite + Jinja Templates + Bootstrap 5  
Static file storage local (filesystem path per attachment)  
Session auth (Flask-Login) + bcrypt hashing

Deployment baseline: Single VPS / Render instance with HTTPS (Let’s Encrypt)  
Backup: Daily SQLite file copy (manual script acceptable)

## 12. UI Modules (Pages)
1. Login
2. Dashboard (today’s appointments + quick stats: total / paid / no-show)
3. Patients list + create/edit
4. Patient detail (demographics + visit history)
5. Calendar (week/day switch)
6. Appointment modal (create/edit/cancel)
7. Visit note form (if completed)
8. Payments daily summary
9. Reminder generator (modal or inline button)

Minimal navigation bar: Pacientes | Agenda | Resumen Diario | Salir

## 13. Non-Functional Requirements (MVP)
- Response time < 500ms typical CRUD
- Concurrency: Single clinic, low volume (< 500 rows total)
- Uptime target: Best effort (no SLA; manual restarts)
- Security: HTTPS, hashed passwords, input validation, file type whitelist (MIME + extension)
- Localization: Hardcoded Spanish strings

## 14. Metrics to Track (Manual / Simple Table)
- Total appointments created (weekly)
- Completion vs no-show count
- Payments marked paid (%)
- Avg time from appointment to note completion (optional)
- Active clinics count

## 15. Risks & Mitigations
- Over-engineering → Time-box features (max 2h blocks)
- Feature creep → Only build items in Section 6
- Low adoption → In-person demos weekly
- WhatsApp dependency → Frame reminder tool as enhancement not replacement
- Personal energy → Morning coding window; ship partial if time ends

## 16. Validation Plan (Weeks 1–4)
Week 1: Basic patients + calendar + booking (demo)  
Week 2: Add payments + visit notes + reminders  
Week 3: Deploy to 3 clinics (shadow usage)  
Week 4: Collect payments, refine UX pain points only

## 17. Acceptance Checklist (Go / No-Go for Charging)
- [ ] Users can add & edit patients
- [ ] Users can view week & day agenda
- [ ] Users can book / reschedule / cancel
- [ ] Payment status + method recorded
- [ ] Reminder text generation working
- [ ] Visit notes saved & retrievable
- [ ] Daily income summary totals accurate
- [ ] All UI Spanish & legible on mobile viewport
- [ ] Basic auth + session expiry verified

## 18. Outbound Messaging Template (Reminder)
Ejemplo:  
Hola <Nombre>, le recordamos su cita con el Dr. <Doctor> el <fecha> a las <hora>. Responda OK para confirmar. Gracias.

## 19. Future (Parked Until Post-MRR)
- Automated WhatsApp sending API
- Multi-doctor per clinic
- Reports (monthly revenue, patient growth)
- SII electronic invoicing
- Patient self-service booking

## 20. Immediate Next Actions (Today)
1. Scaffold Flask app + models above
2. Implement Patient CRUD
3. Implement appointment creation (manual list before calendar grid)
4. Basic week view (table)
5. Deploy and demo to first practitioner

---

Essentials Only: This document is intentionally minimal to prevent scope drift. Any proposed addition must displace or simplify an existing item.

End of essential PRD.
