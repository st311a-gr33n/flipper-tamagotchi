---
type: Project
status: Active
stage: Phase 3
repo: tama_p1
related_to:
  - "[[overview|Architecture Overview]]"
  - "[[current-state|Current State]]"
  - "[[gap-registry|Gap Registry]]"
tags:
  - tama-p1
---

# Tama P1 — Project Home

Dashboard and map of content for **Tama P1**.

A fork of the Tamagotchi P1 emulator for Flipper Zero (TamaLIB) that adds screen-lock while running — press the Back key three times to unlock — so the device can run all day in a bag without accidental exits or misinput.

This vault is the project's persistent memory for humans and AI agents. Start with
`progress/current-state.md`, then navigate the category views.

## Status

- **Stage:** `Phase 3` — manual/menu/hold-B lock, triple-Back unlock, and 60 s auto-lock delivered.
- **Stack:** C · Flipper Zero firmware (furi) · TamaLIB · fbt
- **Repo:** `tama_p1` (see `README.md` at the repository root).
- **Open gaps:** see [[gap-registry|Gap Registry]].

## Categories

| Category | View | Notes |
|---|---|---|
| Architecture | [[overview|Architecture Overview]] | `architecture/` |
| Components | `components/` | one module or package per note |
| Decisions | `decisions/` | ADR-0001 … |
| Source files | `files/` | per-file indexes |
| Progress | [[current-state|Current State]], [[phases|Phases]], [[changelog|Changelog]], [[worklog|Worklog]], [[sessions|Sessions]] | `progress/` |
| References | `references/` | papers, tools, links |
| Gaps | [[gap-registry|Gap Registry]] | [high](gaps-high) · [medium](gaps-medium) · [low](gaps-low) · [feature](gaps-feature) |

## Quick links

- [[overview|Architecture Overview]]
- [[current-state|Current State]]
- [[phases|Phases]]
- [[gap-registry|Gap Registry]]

## How to use this vault

- **Humans:** open it in Obsidian or Tolaria and browse the saved views.
- **Agents:** follow the workflow in `AGENTS.md`; read [[current-state|Current State]] first and
  append to [[worklog|Worklog]] after every session.
