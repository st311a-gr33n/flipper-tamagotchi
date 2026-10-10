---
type: Decision
id: ADR-0002
status: Accepted
date: 2026-10-08
area: app/ui
tags:
  - ui
  - lock
  - decision
related_to:
  - "[[adr-0001-screen-lock-design|ADR-0001]]"
  - "[[gap-003-auto-lock|GAP-003]]"
  - "[[tama-p1-app|Tama P1 App]]"
---

# ADR-0002 — Auto-lock after idle (60 s)

## Context

[[adr-0001-screen-lock-design|ADR-0001]] shipped manual lock only and deferred
auto-lock to [[gap-003-auto-lock|GAP-003]]. Phase 1 was verified on device, and the
product goal ("run all day in a bag") is better served if the device locks itself
when untouched.

## Decision

Add auto-lock on top of the existing `locked` state:

- **Idle timeout:** `AUTO_LOCK_MS = 60000` (60 s). Configurable via one macro.
- **Idle clock:** `last_activity_tick` is set on every `EventTypeInput`, and once at
  startup. The existing 30 Hz `EventTypeTick` evaluates
  `furi_get_tick() - last_activity_tick > furi_ms_to_ticks(AUTO_LOCK_MS)`.
- **Gate:** auto-lock only when `!locked` and the emulator is live
  (`g_ctx->rom != NULL && !g_ctx->halted`).
- **Effect:** identical to selecting "Lock" — release all TamaLIB buttons, reset the
  unlock counter, set `locked = true`, clear `in_menu`.
- **Reset:** any input event postpones auto-lock; unlocking resets the clock.

## Consequences

- The tick branch in `tama_p1_app` (tama_p1.c:844) gains the idle check.
- Auto-lock can fire from the menu as well as the game; leaving the menu open idle
  will lock the device (matches "no input = idle").
- No new timer is allocated; the existing 30 Hz tick is reused.
- The timeout is a compile-time macro for now; persisting a user setting remains
  future work.

## Alternatives considered

- **Dedicated `FuriTimer`:** rejected — duplicates the existing tick.
- **Count ticks in the tick handler:** rejected — wall-clock via `furi_get_tick()`
  is simpler and immune to tick-rate assumptions.
- **Auto-lock only from the game (not menu):** rejected — a bagged device left on
  the menu should also lock.
