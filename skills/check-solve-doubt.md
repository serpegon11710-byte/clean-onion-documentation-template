# SKILL: CHECK SOLVE DOUBT

Independent closure audit for a **single** solved doubt. Gives the human reviewer evidence that COD §2.1 closure was completed correctly before commit.

**Human-owned:** This skill is invoked **manually** by the human (or at their explicit request). It does not replace PO judgment or the pre-commit `solid` audit.

## Authority

Structural rules: [clean-onion-documentation.md](../5-governance/clean-onion-documentation.md) §2.1.

Closure workflow: [skills/product-owner.md](product-owner.md) Phase 2.

## When to invoke

- After PO approves closure and propagation work is done, **before** `git add` / `git commit`.
- When the human wants confirmation that a doubt closure is complete.

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

1. If a full path to `doubt-XXX.md` is given, use it (file may be in `open/` during final edit, `solved/` after solve, or `superseded/` only when human requests forensic audit).
2. If block + doubt ID are given, resolve via that block's `doubts_and_resolutions/solved/` first, then `open/` if missing.
3. Determine **owning block** (folder immediately under which `doubts_and_resolutions/` lives, e.g. `use-cases`, `logical-domain`).
4. Load **only**:
   - The target doubt file.
   - Paths listed under `## Propagated to`.
   - `## Matrix impact` (if present) and superseded doubt actas in `solved/` referenced for supersede checks.
   - `decision-matrix.md` `## {element}` sections for elements in `Matrix impact` or `Propagated to`.
   - Owning-block `doubts_and_resolutions/index.md` (dashboard row for this doubt — owning block only).
5. Do **not** traverse week `history/` or load `superseded/` unless the human explicitly requests forensic traceability.

## Vigente doubt resolution (checks 6–8)

Apply when reading a `Vigente doubt` cell or validating matrix consistency:

```text
IF cell is bare D-XXX
  → acta = {matrix-block}/doubts_and_resolutions/solved/doubt-XXX.md
IF cell is [block/D-XXX](path)
  → acta = {block}/doubts_and_resolutions/solved/doubt-XXX.md (path must resolve under solved/)
IF matrix-block ≠ acta block AND cell is bare D-XXX → KO (MATRIX-CROSS-BLOCK-FORMAT)
IF path targets superseded/ → KO (MATRIX-SUPERSEDED-LINK)
```

Qualified display text must use `{block}/D-XXX` where `{block}` matches the owning block slug.

**Resolve identity for comparison:** bare `D-XXX` in owning block = `{owning-block}/D-XXX`; qualified `{block}/D-XXX` as-is.

## Audit checks

Run every check below. Record pass/fail per item.

| # | Check | Tag on fail |
|---|-------|-------------|
| 1 | Doubt file is self-contained: problem, options, decision, impact — no `see D-XXX` / `Ver D-XXX` for context expansion | `DOUBT-CONTEXT-CHAIN` |
| 2 | Resolution section exists and is unambiguous | `DOUBT-INCOMPLETE` |
| 3 | `## Propagated to` exists and lists at least one SSOT or `decision-matrix.md` path when the decision is normative | `PROPAGATED-MISSING` |
| 4 | Every path under `Propagated to` exists on disk | `PROPAGATED-INVALID-PATH` |
| 5 | Staged or working-tree SSOT targets contain normative text, not pointer-only stubs or `see D-` / `Ver D-` delegation | `SSOT-POINTER` |
| 6 | For each `Vigente` row in this doubt's `## Matrix impact`, the linked block's `decision-matrix.md` has a matching `(element, event)` row pointing at this doubt (`D-XXX` local or `[owning-block/D-XXX](…)` cross-block) | `MATRIX-MISSING` |
| 7 | No duplicate vigente row for the same `(element, event)` in any referenced matrix section | `MATRIX-COLLISION` |
| 8 | Cross-block cells use qualified `[block/D-XXX](…)` links to `solved/` only, not bare `D-XXX`; acta exists in owning block `solved/` | `MATRIX-CROSS-BLOCK-FORMAT` |
| 9 | `## Matrix impact` exists with correct columns when this closure updated any matrix row | `MATRIX-IMPACT-MISSING` |
| 10 | Owning-block `index.md` dashboard lists this doubt under **Solved Issues** when file is in `solved/`; **no** dashboard row when file is in `superseded/` | `DASHBOARD-SYNC` |
| 11 | **Supersede closure:** `history/` records supersede; superseded acta has `**Superseded by:**` or all-`Superseded by` `Matrix impact`; superseded rows appear as `Vigente` in successor `Matrix impact` where required by §2.1 | `SUPERSEDE-INCOMPLETE` |
| 12 | **Archive (same session):** when D-XXX has no `Vigente` rows left in `Matrix impact`, file is in `superseded/`, removed from Solved dashboard, and **vigente inverse** holds: for every row, that block's `decision-matrix.md` cell resolves to a doubt **≠ D-XXX** | `ARCHIVE-INCOMPLETE` |

**Check 5 (normative content):** Verify the propagated files contain implementable rule text aligned with the doubt resolution — not only headings or links. This is a **semantic** check allowed here because the human requested closure verification.

**Check 11:** Apply when closing a doubt that supersedes another.

**Check 12:** Apply when the target doubt D-XXX was fully superseded in this session (all `Matrix impact` rows `Superseded by …`). Vigente inverse uses **decision-matrix.md** only — do not require the matrix cell to match the `Superseded by` doubt named in the acta.

## Response format

Report **only** in chat. Do **not** write `solid-principles-review-report.md` or commit on behalf of the human.

```markdown
## Doubt closure audit — D-XXX

**Scope:** <resolved path>
**Owning block:** <block slug>
**STATUS:** PASS | KO

### Checklist

| # | Check | Result | Notes |
|---|-------|--------|-------|
| 1 | … | PASS/KO | … |

### Findings

<empty or list with tags: DOUBT-CONTEXT-CHAIN, MATRIX-MISSING, etc.>

### Human action

- **PASS:** Closure appears complete per COD §2.1. Human may stage and run `solid` before commit.
- **KO:** List required fixes. Do not recommend timestamp-only updates. Human fixes and re-runs this skill.
```

Set `**STATUS:** KO` if **any** check fails.

## Forbidden

- Auto-committing or regenerating `solid-principles-review-report.md`.
- Suggesting file touches only to refresh modification times.
- Loading unrelated matrix sections, `superseded/`, or full repository scans (unless forensic scope declared).
- Rewriting superseded acta beyond `**Superseded by:**` header and `Matrix impact` status updates unless the human explicitly asks.

## Relationship to other skills

| Skill | Role |
|-------|------|
| [refactor-doubts.md](refactor-doubts.md) | Before closure — overlap detection |
| [product-owner.md](product-owner.md) | Debate and closure execution |
| **check-solve-doubt** | After closure — human verification |
| [solid.md](solid.md) | Pre-commit — staged changes gate |
