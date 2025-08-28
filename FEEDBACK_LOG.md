# FEEDBACK_LOG.md
# Raw practitioner / user feedback entries (machine-readable YAML list). Append new at top or maintain chronological order (configurable). MVP starts empty.

```yaml
version: 1
entries: []
conventions:
  id_format: "F-### incremental"
  required_fields: [id, date, source, raw, tags, interpretation, action, linked_task]
  action_values: [candidate, accepted, rejected, deferred]
  tagging_guidelines:
    - "usability: UI layout / clarity"
    - "scheduling: calendar or booking flow"
    - "performance: speed concerns"
    - "payments: payment tracking"
    - "clinical: notes / medical data capture"
    - "security: auth / privacy"
process:
  capture:
    - "Support agent records immediately post-interaction"
  triage:
    - "product_ceo reviews weekly; converts accepted items to tasks referencing id"
  linkage:
    - "linked_task holds T-### or null"
```

# Template (example – do not uncomment until real data)
# - id: F-001
#   date: 2025-08-29
#   source: "Dr. Pérez"
#   raw: "Necesito ver rápidamente todas las citas del día sin tanto scroll."
#   tags: [usability, scheduling]
#   interpretation: "Need condensed daily agenda view"
#   action: candidate
#   linked_task: null
