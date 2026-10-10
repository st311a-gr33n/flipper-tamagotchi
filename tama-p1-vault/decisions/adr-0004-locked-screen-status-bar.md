---
type: Decision
id: ADR-0004
status: Superseded
date: 2026-10-10
area: app/ui
superseded_by:
  - "[[adr-0005-app-drawn-locked-status-bar|ADR-0005]]"
tags:
  - ui
  - lock
  - decision
related_to:
  - "[[adr-0001-screen-lock-design|ADR-0001]]"
  - "[[adr-0003-quick-lock-gesture|ADR-0003]]"
  - "[[tama-p1-app|Tama P1 App]]"
---

# ADR-0004 — Show the firmware status bar on the locked screen

## Context

While locked the screen only shows the unlock hint, so the user cannot glance at
the time, battery, Bluetooth, or SD status without unlocking. The firmware draws
its system status bar (battery/SD/BT/clock) in the GUI service, but only when no
**fullscreen** view port is active:

```c
if(!gui_redraw_fs(gui)) {              // fullscreen present => status bar skipped
    if(!gui_redraw_window(gui)) gui_redraw_desktop(gui);
    gui_redraw_status_bar(gui, false);
}
```

The app registers at `GuiLayerFullscreen` (tama_p1.c:854), which suppresses the
bar. There is no public API to force the status bar over a fullscreen view port
(`gui_set_hide_statusbar` only hides, and is not in the stock SDK).

## Decision

Show the real system status bar **only while locked** by moving the single view
port between GUI layers:

- **Unlocked:** `GuiLayerFullscreen` — unchanged 128×64 game view.
- **Locked:** `GuiLayerWindow` — the firmware then draws its status bar on top;
  the view port's canvas frame becomes y 13..63 (128×51).

Implementation (tama_p1.c):

- File-statics `g_gui`, `g_view_port`, `g_window_layer`, `g_pending_layer`.
- `apply_layer_for_lock(bool)` removes and re-adds the view port on the target
  layer, guarded by `g_window_layer`.
- `set_locked(bool)` sets `locked`, clears the unlock counter, and raises a
  pending flag. The four lock transitions (auto-lock, unlock, menu "Lock",
  quick lock) call it.
- The swap runs in `tama_p1_app` **after** `furi_mutex_release(g_state_mutex)`,
  never inside the critical section.
- The locked hint moves up (y 18 / 32) to fit the 51 px window frame.

## Consequences

- The status bar is drawn by the firmware, so it follows the user's theme and
  status-bar settings (battery icon style, clock, borders, background).
- Brief redraw when the view port changes layer (one frame at lock/unlock).
- The lock hint area shrinks to 128×51; text was repositioned accordingly.
- No new SDK dependency and no icons: works on stock and Momentum firmware.

## Alternatives considered

- **Always `GuiLayerWindow`:** status bar always visible, but the game view loses
  the top 13 px — landscape 4× (128×64) no longer fits. Rejected; the request was
  for the locked screen only.
- **Hand-draw the status bar** (battery via `furi_hal_power_*`, BT via
  `furi_hal_bt_is_active`, clock via `furi_hal_rtc_get_datetime`, plus icons):
  duplicates firmware, ignores user theme, and adds code/assets. Rejected.

## References

- `Momentum-Firmware/applications/services/gui/gui.c` (`gui_redraw`,
  `gui_redraw_status_bar`, `gui_add_view_port`).
- `Momentum-Firmware/applications/services/gui/gui.h` (`GuiLayer` enum).
