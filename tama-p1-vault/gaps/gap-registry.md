---
type: Project
tags: [project]
status: Active
stage: Living index
related_to:
  - "[[Home|Tama P1 — Project Home]]"
  - "[[current-state|Current State]]"
---

# Gap Registry

Master index of all known gaps, limitations, bugs, and future work. Each row links
to an individual `Gap` note. Filter interactively with the saved views:
`gaps-high`, `gaps-medium`, `gaps-low`, `gaps-feature`, `open-gaps`.

| ID | Priority | Status | Area | Gap |
|---|---|---|---|---|
| [[gap-001-screen-lock|GAP-001]] | feature | Resolved | app/ui | Screen lock — triple-Back unlock |
| [[gap-002-button-mashing|GAP-002]] | medium | Open | input | Button mashing unresponsiveness |
| [[gap-003-auto-lock|GAP-003]] | feature | Resolved | app/ui | Auto-lock after idle |

## Summary

- **High:** 0 · **Medium:** 1 · **Low:** 0 · **Feature:** 0
- **Resolved:** 2

## How to use

- Move a gap to `status: Resolved` (never delete) and link the resolving decision,
  commit, or component in its body.
- Open a new gap for any novel limitation; increment the ID, never reuse.
- Update this table whenever a gap's status changes.
