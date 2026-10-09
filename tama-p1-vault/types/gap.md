---
type: Type
tags: [owner:]
_icon: alert-triangle
_color: "#dc2626"
_order: 7
_list_properties_display:
  - id
  - priority
  - status
  - area
sort: "property:id:asc"
id:
priority:
status:
area:
owner:
related_to:
---
# Gap

A known gap, limitation, bug, or future-work item. Required properties:

- `id` — `GAP-001`, `GAP-002`, … (never reused)
- `priority` — exactly one of `high`, `medium`, `low`, `feature`
- `status` — `Open`, `In Progress`, `Resolved`, `Won't Fix`, `Deferred`
- `area` — a domain label (optional)

Do not edit an accepted gap's `id`. Move it to `Resolved` when fixed, with a link
to the resolving decision/commit in the body.
