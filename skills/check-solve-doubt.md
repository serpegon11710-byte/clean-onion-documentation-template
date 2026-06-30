# SKILL: CHECK SOLVE DOUBT

Independent closure audit for a **single** solved doubt. Gives the human reviewer evidence that COD §2.1 closure was completed correctly before commit.

**Human-owned:** This skill is invoked **manually** by the human (or at their explicit request). It does not replace PO judgment or the pre-commit `solid` audit.

## Authority

Structural rules: [clean-onion-documentation.md](../5-governance/clean-onion-documentation.md) §2.1.

Closure workflow: [skills/product-owner.md](product-owner.md) Phase 2.

## When to invoke

- After PO approves closure and propagation work is done, **before** `git add` / `git commit`.
- When the human wants confirmation that a doubt closure is complete.
- After a `COD-PROPAGATION-STALE` failure from [skills/solid.md](solid.md), to verify remediation.

**Example invocation:**

```text
@skills/check-solve-doubt.md
Scope: 1-product-documentation/use-cases/doubts_and_resolutions/solved/doubt-008.md
```

Alternate form:

```text
Block: use-cases
Doubt: D-008
```

## Scope resolution

1. If a full path to `doubt-XXX.md` is given, use it (file may be in `open/` during final edit or `solved/` after move).
2. If block + doubt ID are given, resolve via that block's `doubts_and_resolutions/` folder.
3. Load **only**:
   - The target doubt file.
   - Paths listed under `## Propagated to`.
   - `decision-matrix.md` `## {element}` sections for elements referenced in the doubt or `Propagated to`.
   - Owning-block `doubts_and_resolutions/index.md` (dashboard row for this doubt).
4. Do **not** traverse week `history/` unless the human explicitly requests forensic traceability.

## Audit checks

Run every check below. Record pass/fail per item.

| # | Check | Tag on fail |
|---|-------|-------------|
| 1 | Doubt file is self-contained: problem, options, decision, impact — no `see D-XXX` / `Ver D-XXX` for context expansion | `DOUBT-CONTEXT-CHAIN` |
| 2 | Resolution section exists and is unambiguous | `DOUBT-INCOMPLETE` |
| 3 | `## Propagated to` exists and lists at least one SSOT or `decision-matrix.md` path when the decision is normative | `PROPAGATED-MISSING` |
| 4 | Every path under `Propagated to` exists on disk | `PROPAGATED-INVALID-PATH` |
| 5 | Staged or working-tree SSOT targets contain normative text, not pointer-only stubs or `see D-` / `Ver D-` delegation | `SSOT-POINTER` |
| 6 | Each listed SSOT/matrix path modification time is **at or after** the doubt file modification time | `PROPAGATION-STALE` |
| 7 | For each impacted element, owning block `decision-matrix.md` has a `## {element}` row with this doubt as `Vigente doubt` for the declared event(s) | `MATRIX-MISSING` |
| 8 | No duplicate vigente row for the same `(element, event)` in that matrix section | `MATRIX-COLLISION` |
| 9 | Cross-block: if doubt or `Propagated to` references elements owned by another block, that block's matrix sections are consistent (same rules 7–8) | `CROSS-BLOCK` |
| 10 | `index.md` dashboard reflects the doubt in **Solved Issues** if file is under `solved/`, or **Open Issues** if still under `open/` | `DASHBOARD-SYNC` |

**Check 5 (normative content):** Verify the propagated files contain implementable rule text aligned with the doubt resolution — not only headings or links. This is a **semantic** check allowed here because the human requested closure verification.

## Response format

Report **only** in chat. Do **not** write `solid-principles-review-report.md` or commit on behalf of the human.

```markdown
## Doubt closure audit — D-XXX

**Scope:** <resolved path>
**STATUS:** PASS | KO

### Checklist

| # | Check | Result | Notes |
|---|-------|--------|-------|
| 1 | … | PASS/KO | … |

### Findings

<empty or list with tags: DOUBT-CONTEXT-CHAIN, PROPAGATION-STALE, etc.>

### Human action

- **PASS:** Closure appears complete per COD §2.1. Human may stage and run `solid` before commit.
- **KO:** List required fixes. Do not recommend timestamp-only updates. Human fixes and re-runs this skill.
```

Set `**STATUS:** KO` if **any** check fails.

## Forbidden

- Auto-committing or regenerating `solid-principles-review-report.md`.
- Suggesting file touches only to refresh modification times.
- Loading unrelated matrix sections or full repository scans.
- Rewriting `solved/` acta files unless the human explicitly asks for remediation edits.

## Relationship to other skills

| Skill | Role |
|-------|------|
| [refactor-doubts.md](refactor-doubts.md) | Before closure — overlap detection |
| [product-owner.md](product-owner.md) | Debate and closure execution |
| **check-solve-doubt** | After closure — human verification |
| [solid.md](solid.md) | Pre-commit — staged changes gate |
