---
type: Note
---

# AGENTS.md — tama-p1-vault

This is a [Tolaria](https://github.com/refactoringhq/tolaria) / Obsidian vault that
serves as the persistent memory for the **Tama P1** project
(`tama-p1`). The vault lives inside the project repository so agents can
read and write it alongside the code.

A fork of the Tamagotchi P1 emulator for Flipper Zero (TamaLIB) that adds screen-lock while running — press the Back key three times to unlock — so the device can run all day in a bag without accidental exits or misinput.

Keep this file focused on vault conventions and the agent memory workflow. For
general Tolaria behavior, use the bundled Tolaria agent docs provided by the app
session context.

## Where the code lives

The project source is at the repository root (`/home/st3114-gr33n/Documents/sg-git/flipper-projects/tama_p1`); this vault is
the `tama-p1-vault/` subdirectory inside it.

## What this vault tracks

Architecture, components, decisions (ADRs), source files, progress, references,
types, views, and a prioritized gap register.

## Core conventions

- Notes are Markdown files.
- Use the first H1 as the note title. Tolaria uses this title in the note list,
  wikilinks, search, and other display surfaces.
- Store the note type in the `type:` frontmatter field. Folders are a human
  convenience only — **never infer type or meaning from the folder**.
- Use wikilinks in body text and frontmatter fields to connect notes. Link to a
  note's **filename**, never its H1 title (see "Filenames, wikilinks, and tags").
- Tolaria reads notes recursively from all folders and stores new notes in the
  vault root by default.
- Saved views live in `views/*.yml`.
- Files in `attachments/` are assets, not notes.
- Frontmatter properties that start with `_` are Tolaria-managed state. Leave
  them alone unless the user explicitly asks for them to change.

## Note types

| Type | Folder | Purpose |
|---|---|---|
| `Project` | root | Project-level metadata and index notes |
| `Architecture` | `architecture/` | System-level design and data flow |
| `Component` | `components/` | One module or package |
| `Decision` | `decisions/` | ADR-style immutable decision record |
| `Source File` | `files/` | Lightweight index of one source file |
| `Progress` | `progress/` | State, phases, changelog, worklog, sessions |
| `Reference` | `references/` | Papers, tools, links |
| `Gap` | `gaps/` | Known gap, bug, or future work |

Type documents (notes with `type: Type`) live in `types/`.

## Relationships

Any frontmatter property whose value contains wikilink syntax is treated as a
relationship. This vault uses:

- `related_to` — general association
- `depends_on` — component/module dependency
- `supersedes` / `superseded_by` — decision lineage
- `component` — a `Source File` points at its `Component`
- `belongs_to` — a note's parent architecture/project note

Use quoted wikilinks for scalar frontmatter values and YAML lists for
multi-value relationships.

## Identifier scheme

- Decisions: `ADR-0001`, `ADR-0002`, … (`id` property, immutable)
- Gaps: `GAP-001`, `GAP-002`, … (`id` property, never reused)
- Phase names: `Phase 0` … `Phase N`

## Gap priority vocabulary

`priority` is exactly one of: `high`, `medium`, `low`, `feature`.
`status` is one of: `Open`, `In Progress`, `Resolved`, `Won't Fix`, `Deferred`.

## Agent memory workflow

When starting any work on the project:

1. Read `progress/current-state.md` first — it is the single source of current truth.
2. Check the `open-gaps` and priority views before choosing what to work on.
3. Read the relevant `components/` and `decisions/` notes before editing code.
4. Build/test with `./fbt launch_app APPSRC=applications_user/TAMA-P1` after code changes.
When finishing work:

1. Append a dated entry to `progress/worklog.md` (append-only; never rewrite history).
2. Update `progress/changelog.md` if user-facing behavior changed.
3. Update `progress/current-state.md` (phase, what works, active gaps).
4. Open a `Gap` note for anything unresolved; close gaps by setting `status`.
5. Update or add `Component` / `Source File` notes if structure changed.
6. Never edit an `ADR` after it is accepted — write a new ADR that supersedes it.

## Views

Saved views live in `views/*.yml` (kebab-case filename = stable view id). The
gap views filter `type = Gap` by `priority`.

## Filenames, wikilinks, and tags

- Filenames are kebab-case: `my-note-title.md`. One note per file.
- **Wikilinks must target a note's filename.** Obsidian (and Tolaria) resolve
  `[[...]]` by filename, not H1 title. Always write the filename, adding an alias
  for readability: `[[my-note|My Note]]`. Linking with the bare title
  (`[[My Note]]`) looks broken and creates an empty note when clicked.
- **Tags** classify notes by domain/facet. Prefer tags for broad cross-cutting
  classification; reserve wikilinks for real relationships (`depends_on`,
  parent/child, `ADR`↔component, `Gap`↔component).

## What agents should avoid

- Do not infer note type or meaning from folders.
- Do not treat files in `attachments/` as notes, types, or view definitions.
- Do not silently overwrite this `AGENTS.md`.
- Do not edit accepted decisions; supersede them.
- Do not duplicate the root `README.md` content — reference it by path instead.
