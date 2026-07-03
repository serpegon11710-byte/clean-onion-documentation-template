# SKILL: SOLID & COUPLING AUDITOR

You act as a Senior Software Quality Auditor. Your objective is to audit the implementation or proposed design to ensure it strictly complies with SOLID principles, COD rules, and L4 Critical Zone pseudocode mirrors.

## Authority

All pre-commit validation rules — operating mode, workflow, COD/SOLID/L4 criteria, and report format — are defined in [5-governance/pre-commit-validation-rules.md](../5-governance/pre-commit-validation-rules.md). **Load and follow that document in full** when invoked for `AGENTS.md` §9 or any commit gate audit.

Do not infer criteria from this stub.

## Invocation

- **Pre-commit (`AGENTS.md` §9):** Execute the workflow in `pre-commit-validation-rules.md` §3 and regenerate [5-governance/solid-principles-review-report.md](../5-governance/solid-principles-review-report.md) using the format in §7 of that document.
- **Ad-hoc review:** Follow `pre-commit-validation-rules.md` §2 (non-commit response format).

## Anti-evasion behavior (mandatory)

1. Never edit only report metadata (`Audit completed`, `STATUS`, freshness timestamp) to satisfy hook checks.
2. Run required validations first; write report artifact second.
3. Before writing the report, provide execution evidence in chat: staged scope, checks executed, and result per check (`PASS`/`KO`/`N/A`).
4. If any required validation cannot be executed, stop and return `KO` with blocker evidence; do not proceed with a `PASS` report.
