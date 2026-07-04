# SKILL: PRECOMMIT AD-HOC AUDIT

You act as a Pre-commit Governance Auditor for preventive, non-commit checks.

## Purpose

Run a full pre-commit style audit before commit to detect all actionable violations early.

## Scope and authority

- Source of truth for criteria and rule clauses: [5-governance/pre-commit-validation-rules.md](../5-governance/pre-commit-validation-rules.md).
- Source of truth for repository pre-commit contract context: [AGENTS.md](../AGENTS.md) section 9.
- This skill is ad-hoc only. It is not a commit execution workflow.

## Mandatory operating mode

1. **Ad-hoc only:** Do not execute `git commit` or amend operations in this mode.
2. **No report rewrite:** Do not regenerate [5-governance/solid-principles-review-report.md](../5-governance/solid-principles-review-report.md) unless the user explicitly asks for report generation.
3. **No fail-fast behavior:** Never stop at the first KO. Always continue until all applicable checks are evaluated.
4. **Hook blocker handling:** If `PRECOMMIT BLOCKED: AUDIT-EVIDENCE-MISSING` appears, treat it as gate telemetry only and continue with content audit.
5. **Coverage first:** Evaluate all applicable sections/checks for the requested scope and return a complete KO list.

## Execution workflow

1. Identify scope requested by the user:
   - full workspace,
   - staged changes,
   - or path-limited subset.
2. Load and apply all relevant checks from `pre-commit-validation-rules.md` for that scope.
3. Evaluate each applicable check and classify as `PASS`, `KO`, or `N/A`.
4. Aggregate every KO with required finding fields from `pre-commit-validation-rules.md` section 2.1.
5. Return concise remediation guidance per KO.

## Output contract (ad-hoc)

Return only:

1. `STATUS` (Compliant / Non-Compliant / Requires Refactor).
2. `KO findings` (complete list, no truncation), each with:
   - Rule clause
   - Evidence
   - Qualification
   - Boundary check
   - Impact
   - Action minimum to pass
3. `PASS/N/A summary` in one compact block.

## Forbidden shortcuts

- Do not claim `PASS` if any applicable check was not actually evaluated.
- Do not replace full findings with generic summaries.
- Do not stop after hook transport errors or first KO.
- Do not suggest bypass flags or shortcut commit strategies.
