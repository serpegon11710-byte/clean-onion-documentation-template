# Doubts & Resolutions

This folder manages doubts and resolutions for this layer.

## Status

Consult [index.md](index.md) in this folder to view the current status of all doubts.

## Folders

- **`open/`** — Active work. Place doubt files here while they are under discussion or pending resolution.
- **`solved/`** — Closed actas with operational vigente value.
- **`superseded/`** — Fully superseded actas (forensic only; archive from `solved/` per §2.1).

## How to manage Doubts

**Open a new doubt:** Create a new markdown file in the `/open/` folder following the `doubt-XXX.md` naming convention.

**Register it:** Add a new row to the "Open Issues" table in `index.md`.

**Solving:** Once resolved, move the file to `/solved/`, update the status in `index.md`, and move the row to the "Solved Issues" table.

**Keep it clean:** Do not move files across these folders manually without updating the `index.md` dashboard.

## Decision matrix

This block maintains [decision-matrix.md](decision-matrix.md): vigente doubts indexed by **this block's elements only**.

| Folder | Role |
|--------|------|
| `open/` | Active debate — one self-contained file per doubt |
| `solved/` | Closed actas with operational vigente value |
| `superseded/` | Fully superseded actas — forensic only; not in dashboard |
| `decision-matrix.md` | Vigente doubt per `(element, event)` — links to `solved/` actas only |
| `history/` | Supersede/merge chain — forensic only; not loaded by default |

On solve: propagate to SSOT + `## Matrix impact` + update matrices per [clean-onion-documentation.md](../../5-governance/clean-onion-documentation.md) §2.1.

**Forbidden:** `see D-XXX` to expand doubt context. Use `superseded by D-XXX` when replacing a prior decision.