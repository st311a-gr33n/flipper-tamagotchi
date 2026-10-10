---
type: Decision
id: ADR-0005
status: Accepted
date: 2026-10-10
area: app/ui
supersedes:
  - "[[adr-0004-locked-screen-status-bar|ADR-0004]]"
related_to:
  - "[[adr-0001-screen-lock-design|ADR-0001]]"
  - "[[tama-p1-app|Tama P1 App]]"
tags:
  - ui
  - lock
  - decision
---

# ADR-0005 — App-drawn status bar on the locked screen

## Context

[[adr-0004-locked-screen-status-bar|ADR-0004]] showed the firmware's real status
bar by moving the view port to `GuiLayerWindow` while locked. On device this made
the app freeze on lock (loader "spinning clock" / hang), requiring a hardware
reboot. Two attempts at hardening (a non-blocking input callback, a deeper event
queue) did not resolve it. Changing GUI layers at runtime from a running
`ViewPort` app is fragile here: it takes `gui->mutex` and the view-port mutex and
runs concurrently with the GUI and emulation threads.

## Decision

Do **not** change GUI layers at runtime. Keep the view port on
`GuiLayerFullscreen` for the whole app lifetime and draw a compact status bar
**inside the app's own draw callback** on the locked screen:

- `draw_locked_screen(Canvas*)` draws, at the top, a small bar with:
  - Bluetooth marker when `furi_hal_bt_is_active()`,
  - clock `HH:MM` from `furi_hal_rtc_get_datetime()`,
  - battery percent from `furi_hal_power_get_pct()` (or `CHG` while charging),
  - a separator line, then the `Locked` / `Press Back 3x to unlock` hint.
- Revert the ADR-0004 mechanics: `apply_layer_for_lock()` and the
  `g_gui`/`g_view_port`/`g_window_layer`/`g_pending_layer` state are removed;
  `set_locked()` only sets `locked` and resets the unlock counter.

## Consequences

- No GUI-thread mutex interaction at runtime → no freeze; the full 128×64
  fullscreen game view is preserved.
- The bar is app-drawn, so it does **not** follow the user's status-bar theme and
  only shows BT/clock/battery (no SD, notifications, or custom icon styles).
- RTC/BT are read directly in the draw callback (cheap register/flag reads). Battery
  is polled on the app thread at 1 Hz and cached: `furi_hal_power_get_pct()` is an
  I2C read and `power_get_info()` is a blocking IPC call, neither safe in the draw
  path (which runs on the GUI thread holding `gui->mutex`).

## Alternatives considered

- **Keep ADR-0004 (firmware status bar via `GuiLayerWindow`):** superseded —
  reproduced a hard freeze on lock.
- **Two-viewport enable/disable toggle:** still couples the app thread to GUI
  view-port mutexes and did not address the observed crash; rejected.
- **Always `GuiLayerWindow`:** status bar always visible but the game loses the
  top 13 px and layout modes no longer fit; rejected.
