# PLANNING.md

Machine-parsable sections for 30‑day MVP execution (reference: essential_PRD_medicalCRM.md)

---

## 1. Vision
Deliver a Spanish-first lightweight health CRM that replaces fragmented WhatsApp + Excel workflows for solo practitioners with unified scheduling, patient records, visit notes, and payment tracking—optimized for speed to first revenue.

## 2. 30-Day Objective
Acquire 3 active paying (or trial-committed) clinics logging ≥60 appointments collectively with ≥70% payment status coverage by Day 30.

## 3. Weekly Milestones (W1–W4)

| Week | Theme | Functional Deliverables (Demo-Ready) | Exit Criteria |
|------|-------|---------------------------------------|---------------|
| W1 (Days 1–7) | Core Scaffold & Patients | Project scaffold, auth, patient model + CRUD, basic agenda skeleton, appointment create | Demo: Add/search patients; book single appointment; deployment reachable |
| W2 (Days 8–14) | Scheduling Depth + Payments | Full week/day agenda, reschedule/cancel, payment status + methods, daily income summary | Demo: Book/reschedule/cancel; mark paid; daily summary shows totals |
| W3 (Days 15–21) | Clinical Notes + Reminders + Attachments | Visit SOAP notes, manual WhatsApp reminder generator, image attachment upload + storage | Demo: Complete visit note; generate reminder text; attach image |
| W4 (Days 22–30) | Hardening & Validation | UX polish (Spanish copy, mobile adjustments), bug fixes, seed metrics, acceptance checklist closure, onboarding 3 clinics | Demo: All Acceptance Checklist items pass; 3 clinics active; baseline metrics captured |

## 4. Current Sprint Focus (W1)
- Scaffold Flask app + repo structure
- Implement Patient model + unique RUT constraint
- Patient CRUD (list, create, edit) with form validation
- Basic authentication (session login/logout)
- Minimal agenda (list of today’s appointments) + appointment creation
- Deploy first public demo endpoint
- Collect 1st practitioner feedback (log F-001+)

## 5. Sprint Commitment (W1 Task IDs)
Committed:
- T-001 Scaffold Flask project
- T-002 Patient model + migration
- T-003 Patient CRUD views
- T-004 Basic auth (login/logout + role seed)
- T-005 Agenda skeleton (today list)
- T-006 Appointment create endpoint/form
- T-011 Deployment pipeline (manual)
- T-012 Initial feedback capture (create FEEDBACK_LOG.md entry)

Buffer (only if ahead):
- T-013 Reschedule (simple edit start_time)
- T-014 RUT format validation enhancement

## 6. Stretch Goals (Non-Blocking)
- Early daily summary placeholder (count appointments)
- Basic styling polish (Bootstrap theme colors)
- Simple health check endpoint for uptime monitoring

## 7. Dependency Notes
- T-002 depends on T-001
- T-003 depends on T-002
- T-006 depends on T-005 (UI slot) + T-003 (patient selection)
- T-011 can run after T-001 (parallel to CRUD work)

## 8. Capacity & Load Assumptions
- Effective dev capacity (backend + frontend overlap): ~40 focused hours this week
- Allocation: 70% feature, 20% stabilization, 10% meta docs/state maintenance

## 9. Risk Watch (Week 1)
| Risk | Indicator | Mitigation |
|------|-----------|------------|
| Over-engineering | >2h on single CRUD screen | Time-box & ship minimal form |
| Delay in deployment | No deployed URL by Day 5 | Prioritize T-011 by mid-week |
| Feedback absence | No user feedback logged by Day 7 | Schedule demo by Day 4 |

## 10. Metrics Capture Setup (Week 1 Targets)
| Metric | Target W1 End | Capture Method |
|--------|---------------|----------------|
| Appointments created | ≥5 | Manual DB query → METRICS.md |
| Patients added | ≥10 | Manual DB query |
| Feedback items | ≥3 | FEEDBACK_LOG.md entries |
| Deployment uptime (manual) | ≥90% (business hours) | Manual checks logged in STATE.md |

## 11. Exit Criteria (Week 1)
- Patients: create/edit/list functional (RUT uniqueness enforced)
- Appointment creation works (single doctor context)
- Basic auth restricts patient/appointment pages
- Deployed URL accessible externally (HTTPS)
- At least 1 practitioner viewed demo (feedback logged)
- STATE.md updated with focus_objective + open_tasks populated

## 12. Forward Look (Preview)
- W2 will introduce payment fields early (T-020..T-024) to accelerate value perception
- Early design decision: keep fixed 30-minute slot; no variable duration until post-Day 30

## 13. Review Notes (Log After Week Completion)
### W1 Review (TO FILL)
- achievements:
- variances:
- carry_over_tasks:
- new_risks:
- adjustments_for_W2:

(Orchestrator appends summary block here at week rollover)

---
Generated: 2025-08-28T19:25:00Z  
Owner: product_ceo  
Version: 1 (bump on structural change)
