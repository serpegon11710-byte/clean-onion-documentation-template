# Template Owner Feedback — Cross-Block Decision Matrix & Doubt Identity

**Status:** Closed — incorporated in COD §2.1 (2026-06-30)  
**Recorded:** 2026-06-30  
**Resolved:** 2026-06-30 — cross-block qualified matrix links, `## Matrix impact`, supersede complete/partial  
**Product repo:** Planificacion 2.0 (`chore/documentation-planification-2.0-pilot--`)  
**Template `main`:** `1477f63` (includes PR #2 @ `7282040` + catalog bijection)  
**Normative SSOT:** [clean-onion-documentation.md](clean-onion-documentation.md) §2.1 — *Cross-block propagation*, *Matrix impact*, *Supersede*  
**Related:** [template-owner-feedback-doubts-dashboard.md](template-owner-feedback-doubts-dashboard.md) (dashboard conventions — closed by owner)

---

## Purpose

During Phase 3 propagation (COD §2.1 closure for solved doubts D-001–D-006), the product team hit an **ambiguous cross-block rule**: a doubt closed in `use-cases/doubts_and_resolutions/` was written into `logical-domain/doubts_and_resolutions/decision-matrix.md` as bare `D-002`, while **no** `logical-domain/.../solved/doubt-002.md` exists.

Agents and humans reasonably assume `D-002` in the logical-domain matrix refers to a doubt **in that block's** `solved/` folder and dashboard. That file does not exist. The acta lives only under use-cases.

This document is the **traceability record** and **issue text** for the template owner to normatively define cross-block matrix behaviour so agents do not mis-resolve doubt identity.

---

## Exemplar scenario (product pilot)

### Setup

| Item | Location |
|------|----------|
| Closed doubt **D-002** (domain edge cases) | `1-product-documentation/use-cases/doubts_and_resolutions/solved/doubt-002.md` |
| Dashboard row D-002 | `use-cases/doubts_and_resolutions/index.md` only |
| Normative SSOT updated | `logical-domain/entities/project/`, `item/`, `planning/`, `schedule-definition/`, etc. |
| Matrix updated (use-cases) | `## UC-01`, `UC-02`, `UC-03` → vigente `D-002` |
| Matrix updated (logical-domain) | `## project`, `item`, `planning`, `schedule-definition` → vigente `D-002` |
| logical-domain `solved/` | **Empty** (`.gitkeep` only) |

### What went wrong (PO / agent confusion)

1. **Fractal navigation:** Agent loads `logical-domain/doubts_and_resolutions/decision-matrix.md`, sees `D-002`, resolves path to `logical-domain/.../solved/doubt-002.md` → **missing**.
2. **False locality:** Bare `D-002` in the matrix **implies** the doubt belongs to logical-domain's numbering space (same as dashboard in that block).
3. **COD §2.1 tension:** Step 4 says update matrix in every affected block; **Cross-context collisions** says resolve in block B's context (extend open doubt or **supersede with new doubt in block B**). Product interpreted step 4 as "repeat the same ID" — PO rejects that reading.

### What worked

- `## Propagated to` in `use-cases/.../solved/doubt-002.md` lists both matrix paths and entity SSOT paths.
- Entity `logic.md` / `README.md` contain **inline normative text** (not `see D-002`).

---

## What COD §2.1 says today (template @ `1477f63`)

### Closure propagation (step 4)

> Update `decision-matrix.md` in **every affected block** (vigente row per `(element, event)`).

### Matrix scope

> The matrix lists **only elements owned by this block** (e.g. use-cases matrix → `## UC-XX`; business-rules matrix → `## BR-XX`).

### Cross-context collisions (block A closes, block B owns element)

1. Do not close until block B is consistent.
2. Consult block B's matrix for affected `## {element}` only.
3. Resolve **in block B's context**:
   - Target still in `open/` → extend that debate.
   - Target already in `solved/` → **supersede with a new self-contained doubt in block B**.
4. Propagate SSOT in B, update B's matrix, then complete closure in A.

### Inter-doubt rules

| Allowed | Forbidden |
|---------|-----------|
| `superseded by D-XXX` when replacing a prior decision | `see D-XXX` / `Ver D-XXX` to expand context |

### Gap (not specified)

- **Format** of `Vigente doubt` when the acta file lives in **another** block's `doubts_and_resolutions/`.
- Whether **doubt IDs are global** or **per-block** (each block has its own `D-001`, `D-002`, … in its dashboard).
- Whether block B's matrix may reference block A's doubt ID **without** a local acta in B.
- Agent resolution algorithm: matrix cell → which file path to open.

---

## Issue text for template owner (copy-paste)

**Suggested title:** `COD §2.1 — cross-block decision-matrix vigente doubt identity`

### Summary

Product pilot closed **D-002** in `use-cases/doubts_and_resolutions/` with propagation to **logical-domain** entities. Updating `logical-domain/.../decision-matrix.md` with bare `D-002` breaks fractal navigation: agents look for `logical-domain/.../solved/doubt-002.md`, which does not exist.

We need a **single normative contract** for cross-block closure so agents and `check-solve-doubt` / pre-commit rules do not guess.

**Exemplar:** D-002 in Planificacion 2.0 (`use-cases` acta + `logical-domain` entity matrix rows).

### Questions for owner (pick one model or define a fourth)

#### M1 — Per-block doubt IDs; matrix references **local** doubts only

- Each `doubts_and_resolutions/` block has its **own** `D-001`, `D-002`, … sequence in its dashboard.
- Block B's matrix lists **only** doubts whose acta file lives under **that block's** `solved/`.
- When block A's closure affects B: **open or supersede a doubt in B** (self-contained acta), propagate SSOT, set B's matrix to B's local ID (e.g. logical-domain `D-001`), optionally `superseded by` / `implements decisions from use-cases/D-002` in prose — **not** as `see D-002` for normative delegation.
- Block A's matrix may still reference A's `D-002` for `## UC-XX` rows.

**Agent rule:** `Vigente doubt` in matrix M → `"{current-block}/doubts_and_resolutions/solved/doubt-XXX.md"` always exists.

#### M2 — Qualified vigente reference across blocks

- Keep one global doubt ID sequence (product-wide) **or** per-block IDs with **qualified** matrix cell.
- `Vigente doubt` column allows a **block-qualified** value, e.g. `use-cases/D-002` or markdown link to the owning acta path.
- Forbidden: bare `D-002` in block B when acta is in block A.
- Pre-commit / `check-solve-doubt`: validate that qualified reference resolves to an existing `solved/doubt-XXX.md`.

**Agent rule:** Parse vigente cell → if qualified, open acta in owning block; SSOT still in entities/UC/BR.

#### M3 — Matrix only in **owning block of the doubt**; other blocks SSOT-only

- Step 4 "every affected block" means **SSOT + optional history**, not necessarily matrix rows in B when the doubt was filed in A.
- Block B's matrix stays empty for events covered solely by A's acta; normative truth only in `entities/`.
- Matrix in A lists both `## UC-XX` and (unusual) cross refs — **or** entity events are never listed in A's matrix.

**Agent rule:** For `project` element → read `entities/project/logic.md`; matrix in logical-domain only for doubts **opened in logical-domain**.

#### M4 — (Owner alternative)

Template owner may combine or refine the above. Product needs **one** documented algorithm in COD §2.1 + pre-commit §4.x + `check-solve-doubt` check 7/9.

### Deliverables requested

1. **COD §2.1** subsection: *Cross-block vigente doubt identity* (chosen model + examples).
2. **Matrix column contract:** allowed values in `Vigente doubt` (bare ID, qualified ID, link, local-only).
3. **Doubt ID scope:** global vs per-block dashboard numbering.
4. **Closure workflow update:** when to create a new doubt in block B vs qualified reference vs matrix-skip.
5. **Pre-commit / skill alignment:** `check-solve-doubt` checks 7, 9; `pre-commit-validation-rules.md` §4.1 matrix rules.
6. **Agent stub** in `skills/product-owner.md` and `skills/check-solve-doubt.md` with the resolution algorithm (pseudocode or table).

### Acceptance scenario (must pass after template PR)

Given:

- Doubt closed in `use-cases/.../solved/doubt-002.md` with propagation to `logical-domain/entities/project/logic.md`.
- No `logical-domain/.../solved/doubt-002.md`.

Then an agent that:

1. Opens `logical-domain/.../decision-matrix.md` for `## project`, **or**
2. Runs `check-solve-doubt` on `use-cases/.../solved/doubt-002.md`,

**must** resolve doubt identity and file paths **without** assuming a non-existent local acta — per the published model.

---

## Product impact until owner responds

| Area | Interim PO policy |
|------|-------------------|
| `logical-domain/.../decision-matrix.md` rows pointing bare `D-002` | **Treat as provisional** — do not rely for agent navigation; SSOT is entity files |
| Further propagation (D-001, D-003–D-006) | Pause matrix updates in non-owning blocks until model M1–M4 is chosen |
| `use-cases/.../decision-matrix.md` | Continue updating `## UC-XX` for doubts filed in use-cases |
| Amend Phase 3 | Include only matrices consistent with interim policy or revert logical-domain matrix D-002 rows |

---

## Suggested agent decision tree (pending owner — illustrative)

```text
Load decision-matrix.md in current block
  For each ## {element} row:
    Read Vigente doubt cell
    IF model = M1 (local only):
      Resolve to {current-block}/doubts_and_resolutions/solved/doubt-XXX.md
      IF missing → KO / open forensic or escalate
    IF model = M2 (qualified):
      Parse block prefix (e.g. use-cases/D-002)
      Resolve to {block}/doubts_and_resolutions/solved/doubt-002.md
    IF model = M3 (SSOT-only in foreign block):
      IF no local acta for event → read entities/ SSOT only; do not open foreign acta unless human asks
    Implement normative behaviour from SSOT paths (entities, UC, BR) — never from acta alone
```

---

## Follow-up

| Step | Owner | Status |
|------|-------|--------|
| Forward issue to template owner | PO | Done |
| Owner publishes model + COD PR | Template owner | Done (2026-06-30) |
| Product realigns D-002 matrices + propagation | Product | Unblocked — apply §2.1 qualified links + `Matrix impact` |
| Close this document | PO | Closed |

**Closure:** Model M2+ published in COD §2.1 — qualified `[block/D-XXX](…)` matrix cells, `## Matrix impact`, supersede complete/partial with retro-feed gate.
