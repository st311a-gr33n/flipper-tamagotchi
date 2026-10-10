---
type: Decision
id: ADR-0001
status: Accepted
date: 2026-10-07
area: app/ui
tags:
  - ui
  - lock
  - decision
related_to:
  - "[[gap-001-screen-lock|GAP-001]]"
  - "[[tama-p1-app|Tama P1 App]]"
---

# ADR-0001 — Screen lock design (manual lock + triple-Back unlock)

## Context

The fork's goal is to let the Tamagotchi run all day in a bag without accidental
exits or misinput. The app already uses **hold-Back (`InputTypeLong`) to save &
exit** (tama_p1.c:1141), so the lock must suppress that too while active.

## Decision

Add a `locked` UI state in `tama_p1_app` (alongside `in_menu`), with:

- **Activation (v1):** a new **"Lock"** item in the existing menu. Selecting it
  sets `locked = true` and exits the menu. (Auto-lock is deferred — see
  [[gap-003-auto-lock|GAP-003]].)
- **Locked behavior:** the draw callback renders a hint — blank screen with
  "Locked — press Back 3× to unlock" — and A/B/C/OK/menu keys are **not**
  forwarded to TamaLIB. The emulator thread keeps stepping and the buzzer keeps
  sounding.
- **Unlock:** count **short** `InputKeyBack` presses; three presses within a
  **2-second window** unlock. The counter resets when the window expires.
- **Hold-Back while locked is disabled** — it does **not** save & exit. To exit,
  unlock first, then hold Back. This preserves the feature's intent (no accidental
  exits from bag presses).

## Consequences

- Input handling gains a `locked` branch before the menu/out-of-menu branches in
  the main loop (tama_p1.c:831+).
- Menu gains one item (`menu_items` increases); existing menu indices may shift.
- The triple-Back gesture must distinguish short presses from the hold gesture
  (use `InputTypePress`/`InputTypeShort`, not `InputTypeLong`).

## Alternatives considered

- **Blank screen (no hint):** more battery-friendly but confusing to unlock.
  Rejected in favor of the hint.
- **Keep game visible while ignoring input:** risks looking "stuck"; hint is
  clearer. Rejected.
- **Mute while locked:** rejected — audible care alerts are wanted in a bag.
