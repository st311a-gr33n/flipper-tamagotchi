---
type: Architecture
status: Draft
area: overview
belongs_to:
  - "[[Home|Tama P1 — Project Home]]"
related_to:
  - "[[current-state|Current State]]"
tags:
  - tama-p1
  - architecture
---

# Architecture Overview

Top-level design and data flow for **Tama P1**.

A fork of the Tamagotchi P1 emulator for Flipper Zero (TamaLIB) that adds screen-lock while running — press the Back key three times to unlock — so the device can run all day in a bag without accidental exits or misinput.

## Stack

C · Flipper Zero firmware (furi) · TamaLIB · fbt

## Structure

Three layers, all C:

- **[[tama-p1-app|Tama P1 App]]** (`tama_p1.c`, `tama.h`) — Flipper UI shell:
  owns the `ViewPort`, event queue, menu, draw callbacks, and save/load state.
- **[[hal|HAL]]** (`hal.c`, `hal_types.h`) — bridges TamaLIB's hardware
  abstraction (LCD matrix, icons, buzzer, timers) to Flipper `furi` APIs.
- **[[tamalib-core|TamaLIB core]]** (`tamalib/`) — the emulator: CPU, RAM/IO
  memory, timers, and input buttons.

## Data flow

1. Input: Flipper key events → `tama_p1_input_callback` → `FuriMessageQueue`.
2. Main loop (`tama_p1_app`): reads queue, translates keys to TamaLIB buttons
   (`tamalib_set_button`) or menu navigation; timer ticks drive `view_port_update`.
3. Emulation: a `FuriThread` (`tama_p1_worker`) steps `tamalib_step()` in a loop,
   gated by `g_state_mutex`; TIM2 provides emulator timing.
4. Output: TamaLIB HAL writes pixels into `TamaApp.framebuffer`; draw callbacks
   render `framebuffer` + `icons` to the canvas.

## Screen-lock feature (implemented)

See [[gap-001-screen-lock|GAP-001]], [[gap-003-auto-lock|GAP-003]],
[[adr-0001-screen-lock-design|ADR-0001]] and [[adr-0002-auto-lock-design|ADR-0002]].
The lock is a UI state in `tama_p1_app` (`locked`) that stops forwarding
A/B/C/OK/menu keys to TamaLIB while the emulator keeps running; the draw callback
shows an unlock hint. Three short Back presses within a 2 s rolling window unlock.
It engages manually from the menu or automatically after 60 s with no input.

## Related

- [[current-state|Current State]]
- [[gap-registry|Gap Registry]]
