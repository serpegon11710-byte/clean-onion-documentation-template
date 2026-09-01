# Working-Tree Validation

## Purpose

Validate the complete working-tree candidate against the governance rules defined by [precommit-audit.md](../precommit-audit.md) before any `git add` operation.

This is a pre-add audit workflow. It is not a commit workflow and it does not replace the staged pre-commit audit.

## Invocation

Use this workflow when the user explicitly requests validation of the working tree with `@file:precommit-audit`, for example:

> Validate the working tree with the rules from `@file:precommit-audit`.

The working-tree audit must load and apply the complete operational rules from [precommit-audit.md](../precommit-audit.md), together with the scope rules defined in this file.

## Scope

The audit target is the complete candidate state represented by the working tree relative to `HEAD`.

Include:

- Tracked files modified in the working tree, whether staged or unstaged.
- Tracked files deleted in the working tree.
- Tracked files renamed in the working tree.
- Non-ignored untracked files.
- Required companion files whose presence or absence is necessary to validate the affected artifact.
- Relevant parent and child indexes, catalogs, maps, matrices, and traceability files.

Do not limit the scope to `git diff --cached`.

Do not treat an empty staged set as an empty working tree. A working tree may contain unstaged or untracked changes even when `git diff --cached` is empty.

Ignored files are excluded unless the user explicitly includes them.

## Candidate-State Rule

Evaluate the content that would exist after staging the complete working-tree candidate:

```text
candidate state = HEAD + tracked working-tree changes + non-ignored untracked files
```

## Applicability Of Staged-Only Rules

This workflow validates a pre-add candidate, so rules in `skills/precommit-audit.md` and
`5-governance/pre-commit-validation-rules.md` that are worded as applying to "staged
paths" must be evaluated against the candidate path set, not only against
`git diff --cached`.

Build the candidate path set from:

- tracked files modified, deleted, or renamed in the working tree;
- non-ignored untracked files; and
- required companion files whose absence or inconsistency affects the candidate.

For this workflow, a rule is applicable when its staged-path condition matches any
candidate path. This includes staged-only content checks such as:

- COD subdivision governance;
- COD directionality and stack-leakage checks;
- SSOT, business-rule, matrix, catalog, history, and doubt checks;
- Layer 5 compatibility checks;
- L4 pseudocode mirror checks; and
- SOLID checks for changed implementation artifacts.

The candidate must be audited as one coherent post-add state. Do not classify a rule
as `N/A` merely because `git diff --cached` is empty or because the changed path is
unstaged.

## Commit-Only Controls

This is not a commit workflow. Apply the substantive validation rules above to the
candidate, but do not execute commit operations or regenerate the audit report.

The protected-file confirmation protocol in §4.16 is commit-time authorization, not
a reason to skip content validation here. If a candidate contains a protected file,
report it as a pre-add warning/blocker according to the protected-file rule, but do
not request or infer `USER_CONFIRMS_PROTECTED_OVERRIDE: YES` in this workflow.

Likewise, do not require staged-only hook evidence, a fresh audit timestamp, or
`solid-principles-review-report.md` regeneration to complete this pre-add audit.

## Result Requirements

Evaluate every applicable check and classify it as `PASS`, `KO`, or `N/A`.

- `PASS` means the candidate satisfies the applicable rule.
- `KO` means the candidate violates the rule, lacks a required companion artifact,
	or the check cannot be executed with available evidence.
- `N/A` is allowed only when the candidate path set does not touch that rule's
	applicability scope.

When a rule is expressed in terms of staged paths, the report must state the matching
candidate path that activated it and must not describe the check as staged-only.