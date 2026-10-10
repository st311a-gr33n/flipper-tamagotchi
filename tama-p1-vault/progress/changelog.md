---
type: Progress
tags: [progress]
status: Active
phase: Phase 0
related_to:
  - "[[phases|Phases]]"
  - "[[current-state|Current State]]"
---

# Changelog

User-facing changes, newest first. This is a distilled log — the authoritative
file-level history is `git log`.

## How to maintain

Add a dated bullet whenever user-facing behavior changes. Do not backfill
rewrites; append corrections instead.

## 2026-10-10

- The locked screen now shows the Flipper's status bar (time, battery, Bluetooth,
  SD), so you can check the device without unlocking. It uses the firmware's own
  status bar, so it follows your status-bar theme and settings. The bar is hidden
  again as soon as you unlock.

## 2026-10-08

- You can now lock quickly by holding the Down button (B) while playing — no menu
  needed. The menu still has a `Lock (hold B)` entry.
- Screen lock now also engages automatically after 60 seconds with no input, so the
  device locks itself when left alone (e.g. in a bag).
- Added screen lock. A new "Lock" menu item locks the device: the display shows
  `Locked — press Back 3x to unlock`, game buttons and hold-Back save & exit are
  ignored, and the Tamagotchi keeps running (buzzer included). Unlock with three
  short Back presses within 2 seconds.
