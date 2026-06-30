# Doubts & Resolutions

This folder manages doubts and resolutions for this layer.

## Status

Consult [index.md](index.md) in this folder to view the current status of all doubts.

## Folders

- **`open/`** — Active work. Place doubt files here while they are under discussion or pending resolution.
- **`solved/`** — Closed decisions and historical record. Move doubt files here once resolved.

## How to manage Doubts

**Open a new doubt:** Create a new markdown file in the `/open/` folder following the `doubt-XXX.md` naming convention.

**Register it:** Add a new row to the "Open Issues" table in `index.md`.

**Solving:** Once resolved, move the file to `/solved/`, update the status in `index.md`, and move the row to the "Solved Issues" table.

**Keep it clean:** Do not move files across these folders manually without updating the `index.md` dashboard.

## Decision matrix

This block maintains [decision-matrix.md](decision-matrix.md): vigente doubts indexed by **this block's elements only** (epic IDs).

| Folder | Role |
|--------|------|
| `open/` | Active debate — one self-contained file per doubt |
| `solved/` | Closed acta — not the sole copy of normative rules |
| `decision-matrix.md` | Which `D-XXX` is vigente per `(element, event)` |
| `history/` | Supersede/merge chain — forensic only; not loaded by default |

On solve: propagate to SSOT + update matrix per [clean-onion-documentation.md](../../5-governance/clean-onion-documentation.md) §2.1.

**Forbidden:** `see D-XXX` to expand doubt context. Use `superseded by D-XXX` when replacing a prior decision.
