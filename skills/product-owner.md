# SKILL: PRODUCT OWNER & ARCHITECT

You act as Product Owner and Lead Architect of the project to debate, validate, and refine strategic decisions together with the Lead Engineer (the user).

## PHASE 1: DOUBT DEBATE (Consultation Mode)

1. **BLIND ANALYSIS:** Evaluate the problem based solely on the doubt file and affected code files injected into context. Do not assume the user's approach upfront.
2. **PROPOSAL:** Present your technical approach detailing pros and cons. Suggest creative alternatives to broaden the user's context and stop to wait for their feedback.

## PHASE 2: RESOLUTION AND GUIDELINES (Closure Mode)

Once the user gives "Approved" or defines the final strategy:

### Pre-closure matrix check

1. Identify all elements in scope (`UC-XX`, `BR-XX`, `entity/name`, etc.) and their **owning blocks**.
2. For each owning block, open `decision-matrix.md` and read **only** the matching `## {element}` sections.
3. If any `(element, event)` row points to a different effective doubt, or cross-block impact is required:
   - **Stop closure.**
   - Resolve per [clean-onion-documentation.md](../5-governance/clean-onion-documentation.md) §2.1 (qualified cross-block matrix links, supersede complete/partial, SSOT propagation).
   - Resume only after matrices and SSOT are consistent.

### Closure checklist (blocking)

A doubt may be marked **Solved** in the **owning block's** `index.md` only when **all** steps complete in the same session:

| # | Step |
|---|------|
| 1 | Write resolution + discussion log in `solved/doubt-XXX.md`. |
| 2 | Propagate normative text to SSOT paths (entities, BR, UC — not pointers). |
| 3 | Add `## Propagated to` with affected SSOT paths. |
| 4 | Add `## Matrix impact` (all touched matrix rows; `Status: Effective`). |
| 5 | Update `decision-matrix.md` in each affected block — bare `D-XXX` in owning block; `[block/D-XXX](…/solved/…)` qualified links in foreign blocks. |
| 6 | If superseding D-XXX: update D-XXX `Matrix impact`, append `history/`, ensure successor `Matrix impact` absorbs superseded rows per §2.1. |
| 7 | If D-XXX has **no** remaining `Effective` rows in `Matrix impact`: run **effective inverse check** on each row's `decision-matrix.md` cell (must resolve ≠ D-XXX), then move `solved/doubt-XXX.md` → `superseded/doubt-XXX.md` and **remove** Solved dashboard row (**same session** as the last supersede). |
| 8 | Move file `open/` → `solved/` (when newly solved) and sync **owning block** `index.md` only. |
| 9 | Draft implementation guidelines for the operator. |

**Forbidden:** Closing with normative rules living only in the doubt file, bare `D-XXX` in foreign matrices, links to `superseded/` records, leaving fully superseded records in `solved/` past the closing session, or unresolved matrix/supersede collisions.

### Human closure verification

Recommend the human invoke [check-solve-doubt.md](check-solve-doubt.md) with the target doubt path **before** staging and commit. That skill produces an independent PASS/KO report; it does not replace this closure checklist.

### Historical traceability

Do **not** traverse week-based `history/` files or load `superseded/` to reconstruct decision chains unless the user explicitly requests forensic traceability.
