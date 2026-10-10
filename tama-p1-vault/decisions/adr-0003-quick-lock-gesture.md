---
type: Decision
id: ADR-0003
status: Accepted
date: 2026-10-08
area: app/ui
tags:
  - ui
  - lock
  - input
  - decision
related_to:
  - "[[adr-0001-screen-lock-design|ADR-0001]]"
  - "[[tama-p1-app|Tama P1 App]]"
  - "[[gap-001-screen-lock|GAP-001]]"
---

# ADR-0003 — Quick lock via long-press on B

## Context

Locking currently requires opening the menu and selecting "Lock"
([[adr-0001-screen-lock-design|ADR-0001]]). The `Ok` key and the `b` key both map
to Tamagotchi button B, so holding B is a free gesture that will not cost an
in-game control. A one-press way to lock matches the "device in a bag" goal better
than a menu trip.

## Decision

While out of the menu, a **long press of the logical B button** (`b`) locks the
device:

- `b` is the key currently mapped to button B; in the default landscape layout
  that is the physical **Down** key. Portrait layouts remap it (Right/Left).
- On `InputTypeLong` for `b`, release BTN_LEFT/MIDDLE/RIGHT, reset the unlock
  counter, and set `locked = true`.
- The menu "Lock" row is **kept** (now labelled `Lock (hold B)`) for
  discoverability.
- Unlock and auto-lock behaviour are unchanged (triple-Back; 60 s idle).

## Consequences

- A held B first emits `Press` (B pressed) then `Long`; the lock handler must
  release the TamaLIB buttons to avoid a stuck B. Later `Repeat`/`Release` events
  are swallowed by the locked branch.
- Holding B during gameplay now locks the device. TamaLIB's B ("confirm") is
  normally tapped, so this is expected to be rare.
- Only the logical B locks; a long press of `InputKeyOk` does not.

## Alternatives considered

- **Physical `InputKeyDown` only:** rejected — inconsistent in portrait layouts,
  where Down is not button B.
- **Long `InputKeyOk`:** rejected — the requested gesture is Down/B.
- **Remove the menu item:** rejected — keep it for discoverability.
