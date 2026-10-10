---
type: Gap
id: GAP-002
priority: medium
status: Open
area: input
tags:
  - input
  - emulator
  - gap
related_to:
  - "[[tama-p1-app|Tama P1 App]]"
---

# Button mashing unresponsiveness

From the upstream README To-Do: when not at 1× speed, mashing buttons in quick
succession makes buttons stop responding for a few seconds while the ROM keeps
running.

## Notes

- Likely related to input debouncing/queue handling at non-1× speed.
- Verify whether it reproduces after the screen-lock work (input path changes).
