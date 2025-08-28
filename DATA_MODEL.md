# DATA_MODEL.md
# Canonical MVP schema snapshot (source of truth for persistence). Update on any structural change.
# Hash (placeholder) for current entity definitions: sha256:TO_COMPUTE_AFTER_IMPLEMENTATION

## 1. Overview
Relational (SQLite) single-tenant (one clinic) for MVP. All tables include created_at (UTC) and updated_at (UTC) managed by application hooks.

## 2. Entities

```yaml
User:
  table: users
  fields:
    id: integer PK autoincrement
    email: string(120) unique not null
    password_hash: string(255) not null
    role: enum('admin','doctor','receptionist') not null
    created_at: datetime not null
    updated_at: datetime not null
  indexes:
    - email (unique)
  notes:
    - Seed single admin on bootstrap
    - Future: add is_active, last_login

Patient:
  table: patients
  fields:
    id: integer PK autoincrement
    rut: string(15) unique not null          # Chilean ID format validated app-side
    name: string(120) not null
    phone: string(30) not null
    email: string(120) nullable
    birth_date: date nullable
    medical_notes: text nullable
    created_at: datetime not null
    updated_at: datetime not null
  indexes:
    - rut (unique)
    - name (for LIKE search)
  notes:
    - Soft delete NOT implemented (keep simple)
    - Future: add gender, address

Appointment:
  table: appointments
  fields:
    id: integer PK autoincrement
    patient_id: integer FK patients.id not null on delete restrict
    doctor_id: integer FK users.id not null (role must be 'doctor')
    start_time: datetime not null            # 30-minute slot start
    duration_minutes: smallint not null default 30
    status: enum('scheduled','completed','no_show','cancelled') not null default 'scheduled'
    notes: text nullable
    payment_status: enum('paid','unpaid') not null default 'unpaid'
    payment_method: enum('cash','transfer','card') nullable
    payment_amount: integer nullable         # Chilean pesos
    created_at: datetime not null
    updated_at: datetime not null
  indexes:
    - (doctor_id, start_time)
    - (patient_id)
    - (status)
  constraints:
    - Unique (doctor_id, start_time) to prevent overlap per slot
  notes:
    - For MVP no multi-duration logic; uniform 30 min
    - Payment fields reside here (no separate payment table initial)

VisitNote:
  table: visit_notes
  fields:
    id: integer PK autoincrement
    appointment_id: integer FK appointments.id unique not null on delete cascade
    subjective: text not null
    objective: text not null
    assessment: text not null
    plan: text not null
    created_at: datetime not null
    updated_at: datetime not null
  indexes:
    - appointment_id (unique)
  notes:
    - One-to-one with appointment (only when completed)
    - Future: versioning / revisions

Attachment:
  table: attachments
  fields:
    id: integer PK autoincrement
    visit_note_id: integer FK visit_notes.id not null on delete cascade
    file_path: string(255) not null
    original_name: string(255) not null
    mime_type: string(80) not null
    size_bytes: integer not null
    uploaded_at: datetime not null
  indexes:
    - visit_note_id
  constraints:
    - size_bytes <= 5_000_000
    - mime_type in ('image/jpeg','image/png','image/webp')
  notes:
    - Stored on filesystem path from ENV var ATTACHMENTS_PATH
    - Future: external object storage

DecisionLog (meta - not persisted in app DB initially)
  rationale:
    - Maintained in DECISIONS_LOG.md only; no table needed MVP
```

## 3. Relationships (ER Summary)
- User (doctor) 1—* Appointment
- Patient 1—* Appointment
- Appointment 1—1 VisitNote
- VisitNote 1—* Attachment

## 4. Derived / Business Rules
- A VisitNote may only be created when Appointment.status in ('completed','scheduled') but on create transition should set status to 'completed'.
- payment_amount required iff payment_status == 'paid'.
- payment_method required iff payment_status == 'paid'.
- status 'no_show' forbids creation of VisitNote (prevent next_action; enforced in service layer).

## 5. Migration Ordering
1. users
2. patients
3. appointments
4. visit_notes
5. attachments
(Each dependent table after its parents)

## 6. Sample SQL (Illustrative)

```sql
CREATE TABLE users (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  email TEXT NOT NULL UNIQUE,
  password_hash TEXT NOT NULL,
  role TEXT NOT NULL CHECK(role IN ('admin','doctor','receptionist')),
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL
);

CREATE TABLE patients (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  rut TEXT NOT NULL UNIQUE,
  name TEXT NOT NULL,
  phone TEXT NOT NULL,
  email TEXT,
  birth_date DATE,
  medical_notes TEXT,
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL
);

CREATE TABLE appointments (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  patient_id INTEGER NOT NULL REFERENCES patients(id),
  doctor_id INTEGER NOT NULL REFERENCES users(id),
  start_time DATETIME NOT NULL,
  duration_minutes INTEGER NOT NULL DEFAULT 30,
  status TEXT NOT NULL CHECK(status IN ('scheduled','completed','no_show','cancelled')) DEFAULT 'scheduled',
  notes TEXT,
  payment_status TEXT NOT NULL CHECK(payment_status IN ('paid','unpaid')) DEFAULT 'unpaid',
  payment_method TEXT CHECK(payment_method IN ('cash','transfer','card')),
  payment_amount INTEGER,
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL,
  UNIQUE(doctor_id, start_time)
);

CREATE TABLE visit_notes (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  appointment_id INTEGER NOT NULL UNIQUE REFERENCES appointments(id) ON DELETE CASCADE,
  subjective TEXT NOT NULL,
  objective TEXT NOT NULL,
  assessment TEXT NOT NULL,
  plan TEXT NOT NULL,
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL
);

CREATE TABLE attachments (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  visit_note_id INTEGER NOT NULL REFERENCES visit_notes(id) ON DELETE CASCADE,
  file_path TEXT NOT NULL,
  original_name TEXT NOT NULL,
  mime_type TEXT NOT NULL,
  size_bytes INTEGER NOT NULL,
  uploaded_at DATETIME NOT NULL
);
```

## 7. Pending / Deferred Fields
- Users.last_login (deferred)
- Appointment.reminder_sent_at (planned for automated reminders phase)
- Patient.address (low priority)
- Audit columns (who created/updated) — phase 2

## 8. Integrity Validation Checklist
- Foreign key constraints enabled (SQLite: PRAGMA foreign_keys=ON)
- Application enforces business rule for payment fields
- RUT validation regex (implement in service layer) before insert/update

## 9. Hash Generation Procedure (For data_model_hash in STATE.md)
1. Concatenate canonical YAML entity definitions (section 2) in deterministic order.
2. Normalize line endings to LF.
3. sha256 digest → update STATE.md.data_model_hash.
4. Log change in DECISIONS_LOG.md referencing new hash.

## 10. Change Control
Any field / constraint change requires:
- New DECISIONS_LOG entry
- Migration script
- DATA_MODEL.md update
- STATE snapshot update (data_model_hash)
- Increment semantic tag at next release

End of schema snapshot.
