---
type: Component
status: Active
path: tamalib/
area: emulator-core
related_to:
  - "[[hal|HAL]]"
  - "[[tama-p1-app|Tama P1 App]]"
---

# TamaLIB core

Third-party Tamagotchi P1 emulator (upstream: jcrona/tamalib). Provides the
emulated CPU, memory (RAM/IO), timers, interrupts, and input buttons.

## Key API (used by this app)

- `tamalib_init`, `tamalib_release`, `tamalib_step`, `tamalib_set_speed`.
- `tamalib_set_button` (BTN_LEFT/MIDDLE/RIGHT states).
- `tamalib_get_state` / `tamalib_refresh_hw` for save/load.

## Notes

- ROM is byte-swapped on load (tama_p1.c:743) before `tamalib_init`.
- The HAL is registered via `tamalib_register_hal`.
