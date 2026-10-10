---
type: Progress
tags: [progress]
status: Active
phase: Phase 3
related_to:
  - "[[Home|Tama P1 — Project Home]]"
  - "[[phases|Phases]]"
  - "[[gap-registry|Gap Registry]]"
---

# Current State

> **Read this first.** Single source of current truth for agents and humans.
> Update at the end of every work session.

## Snapshot

- **Phase:** 3 — manual lock, menu "Lock", hold-B quick lock, triple-Back unlock, and 60 s auto-lock delivered.
- **Product:** Tama P1 — A fork of the Tamagotchi P1 emulator for Flipper Zero (TamaLIB) that adds screen-lock while running — press the Back key three times to unlock — so the device can run all day in a bag without accidental exits or misinput.
- **Stack:** C · Flipper Zero firmware (furi) · TamaLIB · fbt
- **Tests:** none tracked yet (build via `./fbt launch_app APPSRC=applications_user/TAMA-P1`).

## What works end-to-end

- Full emulator baseline: ROM load, stepping thread, LCD + icons rendering, buzzer,
  save/load state, menu (layout/speed/A+C), hold-Back save & exit.
- Screen lock ([[gap-001-screen-lock|GAP-001]]): menu "Lock" row; locked screen shows
  the unlock hint; A/B/C/OK/menu and hold-Back are suppressed; three short Back
  presses within 2 s unlock while the emulator keeps stepping and the buzzer sounds.
  **Verified on device (2026-10-08).**
- Auto-lock ([[gap-003-auto-lock|GAP-003]] / [[adr-0002-auto-lock-design|ADR-0002]]):
  locks after 60 s with no input (when not already locked and the emulator is live);
  any input postpones it; unlocking resets the idle clock.
  **Verified on device (2026-10-08).**
- Quick lock ([[adr-0003-quick-lock-gesture|ADR-0003]]): holding the B/Down button
  while playing locks immediately, without opening the menu.
  **Verified on device (2026-10-08).**
- Locked-screen status bar ([[adr-0005-app-drawn-locked-status-bar|ADR-0005]],
  supersedes [[adr-0004-locked-screen-status-bar|ADR-0004]]): the locked screen shows
  an app-drawn bar — BT marker, clock, battery/% — above the unlock hint. Drawn
  inside the app's own draw callback on the fullscreen view port; no GUI layer swap.
  (ADR-0004's `GuiLayerWindow` swap froze the device on lock and was removed.)
  **Compile-verified; on-device re-check pending.**

## Known active gaps (pointer)

- [[gap-002-button-mashing|GAP-002]] — button mashing unresponsiveness (bug).
- [[gap-001-screen-lock|GAP-001]] — screen lock (resolved Phase 1).
- [[gap-003-auto-lock|GAP-003]] — auto-lock after idle (resolved Phase 2).
- See [[gap-registry|Gap Registry]].

## Next suggested work

1. Re-verify [[gap-002-button-mashing|GAP-002]] after input-path changes.
2. Optional: make the auto-lock timeout a user setting.
3. Optional: keep watching for accidental locks from holding B during play.

## Source docs

- `README.md` (repository root).
