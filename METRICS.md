# METRICS.md
# KPI definitions + latest values (machine-readable YAML)

```yaml
version: 1
last_update: 2025-08-28T23:29:30Z
kpis:
  appointments_created:
    description: "Total appointments created cumulative (all statuses)"
    target_day30: 60
    current: 0
    unit: count
    source: "DB count appointments"
    update_frequency: weekly
  paying_clinics:
    description: "Number of active clinics (trial or paid) using system in past 7d"
    target_day30: 3
    current: 0
    unit: count
    source: "Manual tracking + DB usage"
    update_frequency: weekly
  payment_status_coverage:
    description: "% of appointments with payment_status marked (paid or unpaid explicitly)"
    target_day30: ">=70%"
    current: null
    unit: percent
    formula: "appointments_with_payment_status / total_appointments"
    update_frequency: weekly
  no_show_rate:
    description: "% of appointments marked no_show"
    target_day30: "<=25%"
    current: null
    unit: percent
    formula: "no_show_count / total_appointments"
    update_frequency: weekly
  feedback_items:
    description: "Cumulative distinct feedback entries (FEEDBACK_LOG.md)"
    target_day7: 3
    target_day30: 15
    current: 0
    unit: count
    source: "FEEDBACK_LOG.md entries"
    update_frequency: weekly
  visit_notes_created:
    description: "Count of visit notes created (proxy for clinical adoption)"
    target_day30: 30
    current: 0
    unit: count
    source: "DB count visit_notes"
    update_frequency: weekly
collection_procedure:
  - "Run SQL queries (see queries section) or manual counts"
  - "Update current values & last_update"
  - "If deviation >20% below linear trajectory, create risk entry"
queries:
  appointments_created: "SELECT COUNT(*) FROM appointments;"
  visit_notes_created: "SELECT COUNT(*) FROM visit_notes;"
  payment_status_coverage: "SELECT CAST(SUM(CASE WHEN payment_status IN ('paid','unpaid') THEN 1 END) AS FLOAT)/COUNT(*) FROM appointments;"
  no_show_rate: "SELECT CAST(SUM(CASE WHEN status='no_show' THEN 1 END) AS FLOAT)/COUNT(*) FROM appointments;"
alerts:
  underperformance_thresholds:
    appointments_created: "-20%"
    paying_clinics: "-1 absolute vs target line"
    payment_status_coverage: "<50% after week2"
    feedback_items: "<2 after week1"
ownership:
  primary: analytics
  escalation: product_ceo
```

# Human Notes
- Linear trajectory assumption: appointments_created ~ (target/4) per week.
- For early weeks metrics may be null until feature present.
