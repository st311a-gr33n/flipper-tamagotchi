---
type: Gap
id: GAP-003
priority: feature
status: Resolved
area: app/ui
tags:
  - ui
  - lock
  - gap
related_to:
  - "[[gap-001-screen-lock|GAP-001]]"
  - "[[adr-0001-screen-lock-design|ADR-0001]]"
  - "[[adr-0002-auto-lock-design|ADR-0002]]"
---

# Auto-lock after idle

Automatically enter the locked state after N seconds of no input, instead of
requiring a manual menu action. Follow-up to [[gap-001-screen-lock|GAP-001]].

## Notes

- Deferred in [[adr-0001-screen-lock-design|ADR-0001]] (manual lock first);
  designed in [[adr-0002-auto-lock-design|ADR-0002]].

## Resolution

Implemented in [[tama-p1-app|Tama P1 App]] (`tama_p1.c`, 2026-10-08):

- `AUTO_LOCK_MS` (60000) and `last_activity_tick`, set on every input and at startup.
- The 30 Hz tick branch locks after 60 s idle when not already locked and the
  emulator is live; locked state reuses the manual-lock path.
- Verified: `./fbt fap_tama_p1` compiles + links against firmware API 87.1.
- Verified on device (2026-10-08): auto-lock engages after 60 s idle.
