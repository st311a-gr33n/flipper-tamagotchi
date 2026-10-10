---
type: Component
status: Active
path: hal.c
area: integration
tags:
  - hal
  - flipper-zero
  - component
depends_on:
  - "[[tamalib-core|TamaLIB core]]"
related_to:
  - "[[tama-p1-app|Tama P1 App]]"
---

# HAL

Flipper hardware abstraction layer for TamaLIB (`hal_t`). Wires TamaLIB's
`malloc`/`free`/`sleep_until`/timers and its LCD/buzzer callbacks to `furi`.

## Responsibilities

- `tama_p1_hal_set_lcd_matrix` / `set_lcd_icon` → `g_ctx->framebuffer[]` / `icons`.
- `tama_p1_hal_play_frequency` / `set_frequency` → `furi_hal_speaker_*`.
- `tama_p1_hal_sleep_until` → TIM2-based delay with mutex hand-off.
- Logging via `furi` log macros.

## Notes

- LCD matrix is 32×16 pixels packed into `uint32_t framebuffer[16]`.
- Buzzer stays owned while enabled; released on stop.
