---
type: Component
status: Active
path: tama_p1.c
area: app
tags:
  - app
  - ui
  - lock
  - component
depends_on:
  - "[[hal|HAL]]"
  - "[[tamalib-core|TamaLIB core]]"
related_to:
  - "[[overview|Architecture Overview]]"
---

# Tama P1 App

Flipper UI shell for the Tamagotchi P1 emulator. Implements `tama_p1_app`
(`application.fam` entry point) plus draw/input/timer callbacks.

## Responsibilities

- Own the `ViewPort`, `FuriMessageQueue`, and the 30 Hz `FuriTimer`.
- Main loop in `tama_p1_app` (tama_p1.c:821) dispatches `EventTypeTick` and
  `EventTypeInput`.
- Emulation thread `tama_p1_worker` (tama_p1.c:694) steps `tamalib_step()`.
- Menu (layout, speed, A+C, lock, close/save/save&exit) and portrait/landscape layouts.
- Save/load state via `tama_p1_save_state` / `tama_p1_load_state`.

## Key input semantics

- `m` (menu) = `InputKeyUp` in landscape; remapped per layout mode.
- `a`/`b`/`c` = Left/Down/Right (remapped per layout).
- **Out of menu:** `InputKeyBack` + `InputTypeLong` → save & exit (tama_p1.c:1141).
- `InputKeyOk` maps to button B.
- **Screen-lock** (implemented, [[gap-001-screen-lock|GAP-001]]): menu "Lock" row
  sets `locked`; while locked, A/B/C/OK/menu input is swallowed (hold-Back save &
  exit included) and the draw callback shows `Locked — press Back 3x to unlock`.
  Three `InputKeyBack` + `InputTypeShort` presses within a 2 s rolling window
  unlock; the emulator thread keeps stepping and the buzzer keeps sounding.
- **Auto-lock** (implemented, [[gap-003-auto-lock|GAP-003]] / [[adr-0002-auto-lock-design|ADR-0002]]):
  `last_activity_tick` updates on every input; the 30 Hz `EventTypeTick` branch
  locks the device after `AUTO_LOCK_MS` (60 s) idle when not already locked and the
  emulator is live.
- **Quick lock** (implemented, [[adr-0003-quick-lock-gesture|ADR-0003]]): out of the
  menu, a long press of the logical B button (`b`; physical Down in landscape)
  releases all TamaLIB buttons and sets `locked` — no menu needed. The menu "Lock"
  row remains and is labelled `Lock (hold B)`.
- **Locked-screen status bar** (implemented, [[adr-0004-locked-screen-status-bar|ADR-0004]]):
  while locked the view port is moved to `GuiLayerWindow` so the firmware draws its
  system status bar (battery/SD/BT/clock) above the hint; it returns to
  `GuiLayerFullscreen` on unlock. `set_locked()` raises `g_pending_layer`, and
  `apply_layer_for_lock()` performs the swap after the state mutex is released.

## Notes

- `g_ctx`, `g_state_mutex`, `layout_mode`, `in_menu`, `locked`, `last_activity_tick`,
  `g_gui`, `g_view_port`, `g_window_layer`, `g_pending_layer` are file-global state.
