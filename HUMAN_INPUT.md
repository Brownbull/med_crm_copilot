# HUMAN_INPUT.md
# Consolidated pending human (founder/product_ceo) inputs & decisions.
# Provide answers in the Answer column (fill with selected option letter OR free text).
# Unanswered items adopt defaults after stated deadline (24h from generation unless amended).

## Legend
Priority: (H) high impact launch blocker, (M) medium, (L) low / can defer  
Default Action: Action system will assume if no response by deadline (UTC).  
Answer Field: Leave chosen option letter (e.g. A) OR write explicit value if “Other / specify”.

---

## 1. Product Scope & Prioritization
| Q# | Topic | Question | Options / Guidance | Priority | Default (Deadline +24h) | Answer |
|----|-------|----------|--------------------|----------|-------------------------|--------|
| 1 | W1 Scope Freeze | Approve current W1 task list (T-001..T-008) as frozen (no additions)? | (A) Approve freeze (B) Add tasks (list) | H | A |  |
| 2 | Pricing Messaging | Confirm pilot pricing strategy for doctor demos? | (A) $30/mo after free first month (B) Collect feedback before quoting (C) Other | M | A |  |
| 3 | Early Demo Timing | Commit day for first practitioner demo? | (A) Day 4 (B) Day 5 (C) Other date | H | A (Day 4) |  |
| 4 | Reminder Template Tone | Keep current formal tone or make more casual? | (A) Keep (B) Casual (provide sample) | L | A |  |
| 5 | Spanish Terminology Standard | Approve using “Agenda” vs “Calendario” label? | (A) Agenda (B) Calendario | M | A |  |

## 2. Data Model & Validation
| Q# | Topic | Question | Options | Priority | Default | Answer |
|----|-------|----------|---------|----------|---------|--------|
| 6 | RUT Validation Format | Implement full Chilean RUT check-digit validation now? | (A) Full validation (B) Basic length/format only | M | B |  |
| 7 | Phone Normalization | Normalize phone to digits only? | (A) Yes (strip symbols) (B) Store as entered | L | B |  |
| 8 | Payment Amount Currency | Fix currency as CLP integer only? | (A) Yes (B) Add currency field | L | A |  |

## 3. Security & Operations
| Q# | Topic | Question | Options | Priority | Default | Answer |
|----|-------|----------|---------|----------|---------|--------|
| 9 | Bcrypt Cost Factor | Set bcrypt rounds (cost) value? | (A) 12 (B) 13 (C) 14 | M | A (12) |  |
|10 | Session Timeout | Enforce auto logout (inactivity) now? | (A) No (B) Yes 8h (C) Yes 4h | L | A |  |
|11 | Hosting Choice | Choose initial hosting platform? | (A) Render (B) VPS (C) Other | H | A |  |
|12 | Backup Time Window | Preferred daily backup time (local)? | Provide HH:MM (24h) | M | 02:00 |  |
|13 | Secret Rotation Policy | Rotate SECRET_KEY periodically even if no incident? | (A) Only on incident (B) Quarterly | L | A |  |

## 4. Execution Cadence & Metrics
| Q# | Topic | Question | Options | Priority | Default | Answer |
|----|-------|----------|---------|----------|---------|--------|
|14 | Standups Doc (STANDUPS.md) | Enable daily standup summary generation? | (A) Yes (B) No | M | B |  |
|15 | Metric Update Day | Weekly metrics update day? | (A) Monday (B) Friday (C) Other | M | B (Friday) |  |
|16 | Linear Trajectory Interpretation | Use 25% per week linear target? | (A) Yes (B) Custom (specify) | M | A |  |
|17 | Feedback Threshold | Minimum weekly new feedback entries? | (A) 3 (B) 5 (C) Other | M | A (3) |  |

## 5. UX / UI Decisions
| Q# | Topic | Question | Options | Priority | Default | Answer |
|----|-------|----------|---------|----------|---------|--------|
|18 | Primary Color | Keep green (#198754) or change? | (A) Keep (B) Provide hex | L | A |  |
|19 | Mobile Nav Pattern | Navigation style MVP? | (A) Top simple bar (B) Collapsible | L | A |  |
|20 | Date/Time Format | Use 24h HH:MM & dd-MM-yyyy? | (A) Yes (B) Alternative | M | A |  |

## 6. Future Feature Flags (Prepare or Defer)
| Q# | Topic | Question | Options | Priority | Default | Answer |
|----|-------|----------|---------|----------|---------|--------|
|21 | No-Show Reason Field | Add free-text reason now? | (A) Defer (B) Add optional | L | A |  |
|22 | Multi-Doctor Placeholder | Show doctor selection UI now? | (A) Add (B) Hide until needed | M | B |  |
|23 | Soft Delete Patients | Implement soft delete now? | (A) Defer (B) Implement | L | A |  |

## 7. Compliance Preparation
| Q# | Topic | Question | Options | Priority | Default | Answer |
|----|-------|----------|---------|----------|---------|--------|
|24 | Activation Monitoring | Track compliance triggers weekly now? | (A) Passive (B) Active weekly check | L | A |  |
|25 | Privacy Policy Draft Source | Initial drafting approach? | (A) Template adapt (B) Legal review | L | A |  |

## 8. Process & Workflow
| Q# | Topic | Question | Options | Priority | Default | Answer |
|----|-------|----------|---------|----------|---------|--------|
|26 | Code Review Timing | Enforce 8h SLA strictly or auto-advance? | (A) Strict (B) Auto-advance after 8h | M | A |  |
|27 | Blocked Task Escalation | SLA*1.25 ALERT creation policy? | (A) Yes (B) Adjust multiplier (specify) | M | A |  |
|28 | Batch State Updates | Group multiple transitions per snapshot? | (A) Yes (B) One per change | L | A (Yes) |  |
|29 | Release Tagging | Tag Git commits for each release now? | (A) Yes (B) Defer | L | B |  |

## 9. Data & Instrumentation
| Q# | Topic | Question | Options | Priority | Default | Answer |
|----|-------|----------|---------|----------|---------|--------|
|30 | Minimal Analytics Hook | Log appointment creation count now? | (A) Yes (B) Defer | M | A |  |
|31 | Metrics Owner Confirmation | Are you also acting as analytics owner now? | (A) Yes (B) Reassign | M | A |  |

## 10. Language & Localization
| Q# | Topic | Question | Options | Priority | Default | Answer |
|----|-------|----------|---------|----------|---------|--------|
|32 | Formality Level Spanish | Maintain formal “usted” tone? | (A) Sí (formal) (B) Más informal (tú) | M | A |  |
|33 | Error Message Style | Style for error messages? | (A) Short neutral (B) Instructive | L | A |  |

---

## Response Template (Copy & Fill)
```
Q1: A
Q2: A
Q3: A
...
```

## Critical Blocking Items (Must Answer before Development Starts)
- Q1 (W1 Scope Freeze)
- Q3 (Demo Day)
- Q6 (RUT validation depth)
- Q9 (bcrypt cost)
- Q11 (Hosting choice)

If no response in 24h defaults applied automatically.

## Assumption Log (Auto-Applied if No Response)
Will be stored in STATE.md notes section with “ASSUMPTION: Q# default applied on <timestamp>”.

End of HUMAN_INPUT.md
