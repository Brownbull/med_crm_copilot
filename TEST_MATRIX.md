# TEST_MATRIX.md
# Feature → Test Case mapping (machine-readable YAML). Update statuses after execution.
# status: pass | fail | blocked | n/a | pending

```yaml
version: 1
last_run: 2025-08-28T23:28:50Z
features:
  patient_crud:
    description: "Create, read, update patient records with unique RUT"
    cases:
      - id: TC-001
        desc: "Create patient with valid unique RUT"
        pre: "No existing patient with RUT 11.111.111-1"
        steps:
          - "Navigate to patient create form"
          - "Fill mandatory fields"
          - "Submit"
        expected: "Success; patient listed"
        status: pending
      - id: TC-002
        desc: "Reject duplicate RUT"
        pre: "Existing patient with RUT 11.111.111-1"
        steps:
          - "Create second patient with same RUT"
        expected: "Validation error displayed"
        status: pending
      - id: TC-003
        desc: "Edit patient phone number"
        pre: "Existing patient"
        steps:
          - "Open edit form"
          - "Change phone"
          - "Save"
        expected: "Updated phone persists"
        status: pending
  appointment_basic:
    description: "Book fixed 30-min appointment without overlap"
    cases:
      - id: TC-010
        desc: "Create appointment for free slot"
        pre: "Slot 09:00 empty"
        steps:
          - "Open create appointment"
          - "Select patient"
          - "Select 09:00"
          - "Save"
        expected: "Appointment appears in agenda"
        status: pending
      - id: TC-011
        desc: "Prevent overlapping appointment"
        pre: "Existing appointment 09:00–09:30"
        steps:
          - "Attempt create 09:00 for another patient"
        expected: "Overlap error shown"
        status: pending
  auth_basic:
    description: "Session-based login protects private pages"
    cases:
      - id: TC-020
        desc: "Redirect unauthenticated to login"
        pre: "No active session"
        steps:
          - "Navigate /patients"
        expected: "Redirect to /login"
        status: pending
      - id: TC-021
        desc: "Successful login with seeded admin"
        pre: "Seed admin user exists"
        steps:
          - "Submit credentials"
        expected: "Redirect to dashboard"
        status: pending
  payments_future:
    description: "Payment fields behavior (placeholder until implemented)"
    cases:
      - id: TC-030
        desc: "Mark appointment paid requires method + amount"
        status: n/a
  visit_notes_future:
    description: "SOAP note creation logic"
    cases:
      - id: TC-040
        desc: "Create visit note sets appointment completed"
        status: n/a
metadata:
  required_pass_before_release:
    - TC-001
    - TC-002
    - TC-010
    - TC-011
    - TC-020
    - TC-021
  deferred_cases:
    - TC-030
    - TC-040
```

# Human Notes
- Update last_run when batch executing tests.
- Expand payments & visit_notes sections when features move into development.
