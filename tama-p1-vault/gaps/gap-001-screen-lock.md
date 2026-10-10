---
type: Gap
id: GAP-001
priority: feature
status: Resolved
area: app/ui
tags:
  - ui
  - lock
  - gap
related_to:
  - "[[tama-p1-app|Tama P1 App]]"
  - "[[overview|Architecture Overview]]"
---

# Screen Lock (run all day in a bag)

Add a screen-lock while the app runs: while locked, the emulator keeps running but
A/B/C/OK/menu input is ignored, so the device can sit in a bag without accidental
exits or misinput. Unlock with three presses of the Back key.

## Design

Settled in [[adr-0001-screen-lock-design|ADR-0001]]:

- Manual "Lock" menu item (v1); auto-lock deferred to [[gap-003-auto-lock|GAP-003]].
- Locked display: blank screen + "Locked — press Back 3× to unlock" hint.
- Unlock: three short Back presses within a 2 s window.
- Buzzer keeps sounding; hold-Back save & exit disabled while locked.

## Acceptance criteria

- Locked device does not forward A/B/C/OK/menu keys to TamaLIB.
- Back ×3 unlocks; no conflict with hold-Back save & exit.
- Emulator continues stepping while locked.

## Resolution

Implemented in [[tama-p1-app|Tama P1 App]] (`tama_p1.c`, 2026-10-08):

- `locked` state + `UNLOCK_BACK_PRESSES` / `UNLOCK_WINDOW_MS` defines (tama_p1.c:18).
- "Lock" menu row (index 3, `menu_items` now 5) releases all TamaLIB buttons on
  activation and clears the unlock counter.
- Locked draw branch shows `Locked` + `Press Back 3x to unlock`.
- Input loop `locked` branch counts `InputKeyBack` + `InputTypeShort` within a 2 s
  rolling window (`furi_get_tick` / `furi_ms_to_ticks`); all other keys — including
  hold-Back — are swallowed.
- Verified: `./fbt fap_tama_p1` compiles and links against firmware API 87.1.
