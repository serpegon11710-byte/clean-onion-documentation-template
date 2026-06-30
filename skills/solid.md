# SKILL: SOLID & COUPLING AUDITOR

You act as a Senior Software Quality Auditor. Your objective is to audit the implementation or proposed design to ensure it strictly complies with SOLID principles, COD rules, and L4 Critical Zone pseudocode mirrors.

## Authority

All pre-commit validation rules — operating mode, workflow, COD/SOLID/L4 criteria, and report format — are defined in [5-governance/pre-commit-validation-rules.md](../5-governance/pre-commit-validation-rules.md). **Load and follow that document in full** when invoked for `AGENTS.md` §9 or any commit gate audit.

Do not infer criteria from this stub.

## Invocation

- **Pre-commit (`AGENTS.md` §9):** Execute the workflow in `pre-commit-validation-rules.md` §3 and regenerate [5-governance/solid-principles-review-report.md](../5-governance/solid-principles-review-report.md) using the format in §7 of that document.
- **Ad-hoc review:** Follow `pre-commit-validation-rules.md` §2 (non-commit response format).

## Propagation coherence check (blocking — skill-only)

This check is **not** documented in `pre-commit-validation-rules.md`. Do not describe its algorithm there — agents must not learn to satisfy it by bumping file timestamps without propagating content.

When staged changes include any `doubts_and_resolutions/solved/doubt-*.md` that declares `## Propagated to` or `## Matrix impact`:

1. Parse each path listed under `Propagated to` (SSOT files, `decision-matrix.md`, etc.).
2. Parse each `Matrix` link target under `Matrix impact`.
3. Compare each listed path's last modification time with the staged doubt file's last modification time.
4. If **any** listed path is **strictly older** than the doubt file, the declared propagation is inconsistent with the filesystem.

**On failure:**

- Set `**STATUS:** KO` in the audit report.
- Record under **Findings** with tag `COD-PROPAGATION-STALE`, the doubt path, and each stale target path.
- Tell the human that SSOT/matrix propagation must be completed before commit — do not assume `Propagated to` is truthful.

**Remediation (required before re-audit):**

- Apply the actual normative content to each stale SSOT or matrix path listed in `Propagated to`.
- Do **not** suggest touching files only to refresh modification times.

**Scope:** Only paths explicitly listed in `Propagated to` or `Matrix impact` of **staged** solved doubts in the current audit. Do not scan the whole repository.

**Pass:** When every listed path was modified at or after the doubt closure edit, or when no staged solved doubt declares `Propagated to` or `Matrix impact`.
