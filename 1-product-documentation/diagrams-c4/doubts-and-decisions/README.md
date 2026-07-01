# Doubts & Decisions

Manages questions and decisions for `diagrams-c4/`.

## Navigation

Use [index.md](index.md) as the dashboard for open and solved doubts.

**Mechanical steps (open/solve file moves and dashboard rows):** see footer of [index.md](index.md). Normative closure: §2.1 and matrix below.

## Decision matrix

This block maintains [decision-matrix.md](decision-matrix.md): Decision Id index by **this block's elements only**.

| Folder | Role |
|--------|------|
| `open/` | Active debate — one self-contained file per doubt |
| `solved/` | Closed records with operational value |
| `superseded/` | Fully superseded records — forensic only; not in dashboard |
| `decision-matrix.md` | Decision Id per `(element, event)` — links to `solved/` records only |
| `history/` | Supersede/merge chain — forensic only; not loaded by default |

On solve: propagate to SSOT + `## Matrix impact` + update matrices per [clean-onion-documentation.md](../../../5-governance/clean-onion-documentation.md) §2.1.

**Forbidden:** `See D-XXX` to expand doubt context. Supersede via `**Superseded by:** {block}/D-YYY` and `Matrix impact` status updates; archive to `superseded/` when fully superseded (same session).