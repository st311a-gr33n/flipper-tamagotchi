---
type: Progress
tags: [progress]
status: Active
phase: Phase 0
related_to:
  - "[[current-state|Current State]]"
  - "[[sessions|Sessions]]"
---

# Worklog

Append-only log of work sessions. **Never rewrite history** — add a new dated
entry at the top (or bottom, consistently) after each session.

Format:
```
## YYYY-MM-DD — <agent/human> — <short title>
- What changed
- Files touched
- Gaps opened/closed
- Next steps
```

---

<!-- Add new entries above this line. -->

## 2026-10-08 — opencode — On-device verification of Phase 2 & 3
- User confirmed on hardware: hold-Down/B quick lock works, and the 60 s auto-lock
  works. No accidental locks reported so far.
- Marked [[adr-0003-quick-lock-gesture|ADR-0003]] and [[gap-003-auto-lock|GAP-003]]
  behaviours as device-verified in [[current-state|Current State]].
- Normalized `README.md` image links (relative, GitHub-safe) and removed the
  device-specific absolute path from `AGENTS.md`.

## 2026-10-08 — opencode — Phase 3 quick lock (hold B)
- Implemented quick lock in `tama_p1.c`: out-of-menu `b` + `InputTypeLong` releases
  all TamaLIB buttons, resets the unlock counter, and sets `locked`.
- Menu row relabelled `Lock (hold B)`; documented the gesture in `README.md`.
- Wrote [[adr-0003-quick-lock-gesture|ADR-0003]].
- Synced updated `tama_p1.c` + `README.md` into `applications_user/tama_p1`.
- Verified: clean `./fbt fap_tama_p1` recompile + link + APPCHK against API 87.1.
- Next: on-device test hold-B lock and confirm no accidental locks during play.

## 2026-10-08 — opencode — Phase 2 auto-lock (60 s)
- Implemented auto-lock ([[gap-003-auto-lock|GAP-003]]) in `tama_p1.c`: `AUTO_LOCK_MS`
  (60000) + `last_activity_tick`; 30 Hz tick branch locks after 60 s idle when not
  locked and the emulator is live; any input resets the clock.
- Wrote [[adr-0002-auto-lock-design|ADR-0002]].
- Synced updated `tama_p1.c` into `applications_user/tama_p1` (the build/flash copy).
- Verified: clean `./fbt fap_tama_p1` recompile + link + APPCHK against API 87.1.
- Next: on-device test the 60 s auto-lock; re-check [[gap-002-button-mashing|GAP-002]].

## 2026-10-08 — opencode — On-device verification of Phase 1 lock
- User confirmed on hardware: menu "Lock" works and triple-Back unlocks.
- Confirmed there is **no** auto-lock: Phase 1 is manual-only. Auto-lock remains
  deferred in [[gap-003-auto-lock|GAP-003]]; no idle timer exists, so the app will
  not lock itself.
- Next: decide whether to implement [[gap-003-auto-lock|GAP-003]] (pick idle timeout).

## 2026-10-08 — opencode — Implement Phase 1 screen lock
- Added `locked` state + `UNLOCK_BACK_PRESSES`/`UNLOCK_WINDOW_MS` defines and the
  "Lock" menu row (menu_items 4→5) in `tama_p1.c`.
- Locked draw branch shows `Locked` + `Press Back 3x to unlock`.
- Locked input branch counts short Back presses in a 2 s rolling window and
  swallows all other input (hold-Back save & exit disabled); emulator/buzzer run on.
- Files touched: `tama_p1.c`.
- Gaps: closed [[gap-001-screen-lock|GAP-001]].
- Verified: `./fbt fap_tama_p1` compiles + links against firmware API 87.1.
- Next: on-device test; re-check [[gap-002-button-mashing|GAP-002]].

## 2026-10-07 — opencode — Plan screen-lock feature
- Documented baseline architecture (overview + 3 component notes).
- Opened GAP-001 (screen lock), GAP-002 (button-mashing bug), GAP-003 (auto-lock, deferred).
- Wrote ADR-0001: manual lock + triple-Back (2 s window) unlock, hint display, buzzer on, hold-Back disabled while locked.
- Next: implement Phase 1 in tama_p1.c (menu item, locked state, input gate, unlock counter).
