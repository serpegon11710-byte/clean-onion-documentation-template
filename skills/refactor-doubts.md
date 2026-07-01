# SKILL: REFACTOR DOUBTS

Analyze `doubts-and-decisions/` for overlapping or conflicting open doubts **before** PO closure. Complements the decision matrix; does not replace human decisions.

## Authority

Structural rules: [clean-onion-documentation.md](../5-governance/clean-onion-documentation.md) §2.1.

## When to invoke

- Before closing a doubt that touches multiple elements or blocks.
- Periodic hygiene when `open/` grows or matrix pre-check reveals risk.
- After explicit user request: "refactor doubts" / "audit doubt overlaps".

## Operating mode

1. **Scope:** Load `index.md` and all files in `open/` for the target block(s). Load `decision-matrix.md` `## {element}` sections for elements in scope only.
2. **Exclude:** Doubts in `superseded/` and records marked fully superseded. Do not traverse week `history/` or load `superseded/` unless the user explicitly requests forensic traceability.
3. **Detect:**
   - Duplicate scope (same element + same event in multiple open doubts).
   - Semantic conflict (contradictory decisions proposed for the same element).
   - Cross-block overlap (UC doubt impacting BR already debated elsewhere).
4. **Report** to the PO (user) with:
   - Doubt IDs involved.
   - Overlap type: `duplicate` | `conflict` | `cross-block`.
   - Recommended action: `merge into open D-XXX` | `supersede with new D-YYY` | `split scope` | `keep separate`.
5. **Never auto-close, auto-merge, or auto-supersede.** Wait for explicit PO approval.

## Forbidden

- Using `See D-XXX` links as the remediation mechanism.
- Rewriting files in `solved/` to merge content (use supersede + new doubt per COD §2.1).
- Loading entire matrix or full history trees without scope filter.
