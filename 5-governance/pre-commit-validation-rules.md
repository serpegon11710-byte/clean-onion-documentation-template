# Pre-Commit Validation Rules

**Last updated:** 2026-07-03

**Source of truth** for the pre-commit integrity contract (`AGENTS.md` §9), auditor behavior ([skills/solid.md](../skills/solid.md)), and all validation criteria executed before `git commit`.

The optional Git hook (§8) validates only the audit **report artifact**. **L4 pseudocode mirror** validation is **agent-only** (§6.3, §9) — this template does not ship and will not ship a hook for that concern.

> **Related:** [clean-onion-documentation.md](clean-onion-documentation.md) §4 (COD matrix SSOT), [solid-principles-review-report.md](solid-principles-review-report.md) (gate artifact), [README.md](README.md) (report file structure).

---

## 1. Pre-Commit Integrity Contract

**Mandatory guardrail:** Any agent (Cursor, Claude Code, Aider, GitHub Copilot, or equivalent) that reads `AGENTS.md` **must** adopt this contract as a pre-commit gate. **No commit with staged changes may proceed** until this audit completes successfully.

Before any commit, perform an architectural audit of all **staged** changes. Invoke [skills/solid.md](../skills/solid.md) and validate every criterion in this document.

If the staged set is empty (for example, message-only amend), skip audit report regeneration and allow the commit.

### 1.2 Anti-evasion and execution evidence (hard gate)

1. Editing only report metadata (`Audit completed`, `STATUS`, or equivalent) to satisfy hook freshness checks without executing required validations is forbidden.
2. The report is an audit artifact, not the audit itself. It can be regenerated only after completing workflow §3.
3. Hook denial messages are operational guidance and do not define validation scope. Scope remains governed by this document and [skills/solid.md](../skills/solid.md).
4. If any required validation cannot be executed, set `**STATUS:** KO`, report blocker evidence, and abort commit.
5. If commit is blocked by audit/report freshness (for example `PRECOMMIT BLOCKED: AUDIT-EVIDENCE-MISSING`), the agent must automatically execute the required pre-commit validation workflow before any new commit attempt.
6. Bypass flags are strictly forbidden: agents must never suggest or execute `git commit --no-verify` (or `-n`) under any circumstance.
7. Unsafe amend shortcut is forbidden as a strategy: agents must not suggest `git commit --amend --no-edit` as a recovery path after hook/audit failures. Execution is allowed only when explicitly requested by the user and only after required validations are completed.
8. Protected-file detection is mandatory and command-based: before any commit attempt, the agent must evaluate protected/exempt scope using `git diff --cached --name-only` and, for amend flows, also `git show --name-only --pretty="" HEAD`.
9. If blocker is `AUDIT-EVIDENCE-MISSING`, asking user permission to run audits is forbidden; the agent must run the required validation workflow automatically.

### On failure

- **Reject** the commit process immediately.
- **Log** the conflict in the corresponding layer's `doubts-and-decisions/index.md`.
- **Report** the violation to the user for manual correction and refactor.

### 1.1 File Integrity & Environment Normalization (hard gate)

To preserve repository integrity across agents and operating systems, all automated file writes must follow this policy:

1. **Mandatory format:** Persist text files as UTF-8 with LF-only line endings.
2. **Explicit prohibition (default-deny):** Do not use OS-native shell cmdlets for persistent file writes when they can inject platform defaults (for example PowerShell `Set-Content`, `Out-File`, `Add-Content`, and redirection operators).
3. **Approved methods:**
     - **Primary:** IDE-native editing APIs and `apply_patch`.
     - **Secondary (CLI fallback):** Tooling that explicitly enforces UTF-8 + LF on output.
4. **Post-write verification (mandatory):** After any automated write, verify encoding and line endings remain compliant.
5. **Immediate remediation:** If drift is detected (for example CRLF injection), normalize using approved methods and re-verify.
6. **Fail-safe behavior:** If compliant output cannot be guaranteed with available tooling, abort the write and report the blocker.

---

## 2. Auditor Operating Mode (`skills/solid.md`)

1. **BLIND AUDIT:** Do not assume the code is correct. Analyze each component, interface, and class looking for encapsulation violations or unnecessary couplings.
2. **CONFLICT ESCALATION (COD Governance vs Product Governance):** If the agent detects a conflict between product governance rules and COD governance rules defined in [clean-onion-documentation.md](clean-onion-documentation.md), the agent must abort the task and return: `CRITICAL ERROR: conflict between COD Governance and Product Governance`. The agent must explain the conflicting rules and why a Product Owner decision is required before continuing.
3. **INTER-LAYER NORMATIVE CONFLICT (CRITICAL):** If the agent detects inconsistent or mutually incompatible normative rules across different layers, the audit must return `**STATUS:** KO` and classify the finding as critical. No layer may be prioritized arbitrarily to force a PASS.
4. **CONSTRUCTIVE CRITICISM:** Identify which specific principle (S, O, L, I, D) or COD rule is violated and propose a technical alternative aligned with the layered architecture.

No automatic precedence is allowed between governance sources or layers when a direct conflict is detected.

For **non-commit** audits (ad-hoc review), respond in chat with:

1. **STATUS:** (Compliant / Non-Compliant / Requires Refactor).
2. **VIOLATION ANALYSIS:** Detailed list of ignored SOLID principles or decoupling rules.
3. **REFACTOR PROPOSAL:** How to decouple or improve the design (no code unless explicitly requested).
4. **GOVERNANCE LINK:** Reference to the specific `5-governance/` file that justifies the audit.

### 2.1 Finding record contract (mandatory)

Every reported finding (chat or report) must include all fields below. Findings missing any field are invalid and must be rewritten.

1. **Rule clause:** Exact clause identifier (section/check name) that is being enforced.
2. **Evidence:** Verbatim excerpt or exact path + snippet that triggers the finding.
3. **Qualification:** Why the evidence violates the clause.
4. **Boundary check:** Why this is a real violation and not an allowed contextual reference.
5. **Impact:** Enforcement result (`KO` or non-blocking note) according to the applicable section.
6. **Allowed-context check (doubt references):** For any finding involving `D-XXX`, explicitly state whether the reference appears in an allowed context (`## Matrix impact` row or top-level `**Superseded by:**`) or a forbidden doubt-body context.

For `See D-` / `See doubt-` findings, the boundary check is mandatory: the auditor must explicitly state whether the case is normative delegation to doubt records (violation) or same-layer contextual linkage with self-contained SSOT behavior (allowed).

---

## 3. Regeneration Workflow

1. Review **staged** changes (`git diff --cached`).
1b. Build protected/exempt classification input from `git diff --cached --name-only`; if flow is amend, include `git show --name-only --pretty="" HEAD` in classification.
2. If staged set is empty, allow commit and skip steps 3-13.
3. Validate **file integrity policy** per §1.1 (write method compliance + UTF-8/LF post-write verification evidence when applicable).
4. If protected classification is positive per §4.16 (staged or amend-HEAD), abort by default and request explicit user confirmation with the required token in the user's latest explicit turn at commit-execution time. Without the token in that latest explicit turn, abort pre-commit.
5. If staged paths include `**/doubts-and-decisions/**`, run [check-solve-doubt.md](../skills/check-solve-doubt.md) for each touched solved/superseded record before `solid`; if any result is `KO`, abort pre-commit and do not continue to report regeneration.
6. Validate **COD** per [clean-onion-documentation.md](clean-onion-documentation.md) §4, §2.1, §2.2–§2.4, and §2.6 (see §4, §4.1, §4.2, §4.3, §4.4, §4.8, §4.14, §4.15, and §4.16 below).
7. If staged paths include `5-governance/**`, execute downstream compatibility checks against affected lower-layer normative artifacts and fail with `KO` on any conflict (see §4.13).
8. Validate **SOLID** — at minimum **S** and **D** on staged changes (see §5 below).
9. Validate **L4 ZC pseudocode mirror** when staged changes touch Critical Zones or their Layer 3 projections (see §6 below).
10. If steps 3-9 are `PASS`, evaluate **unstaged invalidation risk**: inspect unstaged changes and return `KO` only when unstaged content invalidates staged audit conclusions (for example, staged content requires same-session companion documentation that exists only in working tree).
11. Do not fail on unrelated unstaged files that do not invalidate staged conclusions.
12. Produce execution evidence before report write: staged scope summary, checks executed, and result per check (`PASS`/`KO`/`N/A`) with blockers if any.
13. Run `Get-Date -Format "yyyy-MM-ddTHH:mm:ss"` **once**, immediately before writing report headers.
14. Overwrite **only** `## Current audit` to EOF in [solid-principles-review-report.md](solid-principles-review-report.md).
15. Set `**STATUS:** PASS` only if **all** checks pass; otherwise `**STATUS:** KO` and **abort the commit**.

### 3.1 Unstaged antirule (intersection is not the criterion)

For workflow §3.8, **path intersection between staged and unstaged is not a KO criterion by itself**.

- **PASS case:** A file may exist in both staged and unstaged sets, and still be valid when unstaged deltas do not invalidate audited staged conclusions (for example, a non-normative comment added after staging).
- **KO case:** A file may exist only in unstaged (no intersection) and still invalidate the audit when it carries required companion updates not present in staged (for example, fixing an `index.md` after audit but leaving that fix unstaged).

Decision rule: evaluate **invalidation impact**, not overlap shape.

---

## 4. COD Validation Criteria

Normative rules: [clean-onion-documentation.md](clean-onion-documentation.md) §4.

| Check | Rule |
|-------|------|
| **File integrity method** | Automated edits follow §1.1 approved methods and avoid prohibited OS-native write cmdlets |
| **File integrity output** | Any staged text file modified by automation remains UTF-8 + LF compliant after write |
| **Inward-only** | Layer N references only inner layers per the dependency matrix in §4 |
| **No stack leakage** | Only Layer 3 may name concrete stacks; forbidden in Layers 1, 2, 4, and 5 |
| **Fractal index** | Every `index.md` uses the same-level file catalog table per `clean-onion-documentation.md` §2 |
| **Catalog bijection** | Each `index.md` file catalog row maps to exactly one **git-tracked** same-level `.md` (excluding `index.md`); no row without a tracked file; no tracked same-level `.md` omitted; no subdirectory paths in the catalog table |

Record violations under **Findings** with `COD`, `COD-FORMAT`, `COD-INDEX`, `COD-HISTORY`, or affected paths.

### §4.6 RP to PP traceability in Layer 3 (hard gate)

Normative rules: [clean-onion-documentation.md](clean-onion-documentation.md) §3.1 and §4.

Apply when staged paths include any:

- `3-implementation/**/RP-*.md`
- `3-implementation/**/rp-to-pp-matrix.md`
- `3-implementation/platform-policies/**/PP-*.md`

| Check | Rule | On failure |
|-------|------|------------|
| **RP mapped to PP** | Every staged effective `RP-XXX` must appear exactly once in the nearest technology-folder `rp-to-pp-matrix.md` with a valid `PP-XX.YY` reference | `**STATUS:** KO` |
| **Matrix locality** | `rp-to-pp-matrix.md` must live in the same technology folder that groups the referenced `RP-XXX` artifacts (no cross-folder central matrix) | `**STATUS:** KO` |
| **PP origin path** | Every `PP-XX.YY` referenced by a matrix row must resolve to `3-implementation/platform-policies/**/PP-XX.YY-*.md` | `**STATUS:** KO` |

Record violations under **Findings** with tag `COD-RP-PP` and affected paths.

### §4.7 Epic layer sprint-awareness prohibition (hard gate)

Normative rules: [clean-onion-documentation.md](clean-onion-documentation.md) §4 (inward-only dependency model).

Apply when staged paths include any `2-epics/**` file.

| Check | Rule | On failure |
|-------|------|------------|
| **Epic sprint-agnostic wording** | Staged epic-layer files must not encode assumptions about sprint existence, sprint timing, or sprint-driven motivation as part of epic normative rules | `**STATUS:** KO` |

**Scope note:** Mentioning this prohibition inside governance/audit files is allowed. The check targets epic-layer artifacts only (`2-epics/**`).

Record violations under **Findings** with tag `COD-DIRECTIONALITY` and affected paths.

### §4.3 File catalog bijection (hard gate)

Normative rules: [clean-onion-documentation.md](clean-onion-documentation.md) §2 (catalog bijection bullet).

Apply when staged paths include any `**/index.md` **or** when a same-level `.md` is added, removed, or renamed in a directory that has an `index.md`.

| Check | Rule | On failure |
|-------|------|------------|
| **Row exists on disk** | Every file name in the file catalog table (first markdown table after H1) resolves to a `.md` file at the same path | `COD-INDEX` |
| **Tracked in git** | Every cataloged `.md` is listed by `git ls-files` for that directory (not gitignored or deleted) | `COD-INDEX` |
| **No omissions** | Every git-tracked same-level `.md` except `index.md` has exactly one catalog row | `COD-INDEX` |
| **No directories** | No catalog row names a subdirectory (trailing `/` or path segment without `.md` extension) | `COD-INDEX` |

**Auditor algorithm:** For each affected `index.md`, parse rows in the **first markdown table** after the H1 (from `| File name | Description |` until the next `##` heading or EOF); compare against `git ls-files --directory <dir>` filtered to `*.md` excluding `index.md`.

Record violations under **Findings** with tag `COD-INDEX` and affected paths.

### §4.1 Intra-layer self-containment and decision matrix (hard gate)

Normative rules: [clean-onion-documentation.md](clean-onion-documentation.md) §2.1.

Apply when staged paths include any of:

- `**/doubts-and-decisions/**`
- `**/logical-domain/entities/**`
- `**/logical-domain/business-rules/**`
- `**/use-cases/**`
- `**/decision-matrix.md`

Scope boundary for this gate:

- `See D-` / `See doubt-` checks target **normative delegation to doubt records only**.
- Contextual references between SSOT artifacts in the same layer are **allowed** when the current artifact keeps implementable behavior locally.
- Cross-block same-layer links do **not** violate COD by themselves and must not be flagged as a substitution for doubt-delegation checks.

| Check | Rule | On failure |
|-------|------|------------|
| **SSOT doubt pointers** | Staged SSOT files (`entities/**`, `business-rules/**`, `use-cases/**/README.md`) **must not** contain doubt-delegation patterns (`See D-`, `See doubt-`, case-insensitive) | `**STATUS:** KO` |
| **Propagated to on solve** | Staged new or modified `doubts-and-decisions/solved/doubt-*.md` with normative resolution content **must** include a `## Propagated to` section listing at least one SSOT or matrix path | `**STATUS:** KO` |
| **Matrix impact on solve** | When a staged solved doubt is paired with staged `decision-matrix.md` changes, the solved doubt **must** include `## Matrix impact` with columns `Block`, `Element`, `Event (brief)`, `Matrix`, `Status` | `**STATUS:** KO` |
| **Matrix on solve** | When a solved doubt is staged, staged changes **must** include the corresponding `decision-matrix.md` update in each affected block, or each matrix must already list the Decision Id for each impacted `(element, event)` per §2.1 format | `**STATUS:** KO` |
| **Matrix cross-block format** | In staged `decision-matrix.md`, a bare `D-XXX` Decision Id cell in block B requires `B/doubts-and-decisions/solved/doubt-XXX.md` to exist. Cross-block **must** use `[block/D-XXX](…)` with link target under the owning block's `solved/` (never `superseded/`) | `**STATUS:** KO` |
| **Matrix uniqueness** | Within each `## {element}` section of a staged `decision-matrix.md`, no duplicate `Event (brief)` row and no duplicate `Decision Id` claiming the same event | `**STATUS:** KO` |
| **Archive on full supersede** | When a staged supersede leaves a doubt with no `Effective` rows in `## Matrix impact`, the record **must** move to `superseded/` in the same staged changeset and its Solved issue catalog row **must** be removed | `**STATUS:** KO` |
| **Effective inverse on archive** | For each row in a fully superseded doubt's `Matrix impact`, the referenced block's staged `decision-matrix.md` cell for that `(element, event)` must resolve to a successor ID (`!=` archived doubt ID), and that successor doubt's `Matrix impact` must include the same tuple as `Effective` | `**STATUS:** KO` |
| **Supersede header** | Staged superseded record may add only `**Superseded by:** {block}/D-YYY` at the top plus `Matrix impact` status updates — no other rewrites of closed debate body | `**STATUS:** KO` if debate body was rewritten |
| **Doubt context chains** | Staged doubt files **must not** add `See D-` patterns for context expansion (supersede declarations and `Matrix impact` status are allowed) | `**STATUS:** KO` |
| **Doubt Decision-Id contexts** | In staged doubt files (`open/`, `solved/`, `superseded/`), any `D-XXX` reference outside `## Matrix impact` rows and the top-level `**Superseded by:** {block}/D-YYY` header is forbidden; there is no contextual-reference exception in doubt bodies | `**STATUS:** KO` |
| **Element definition segregation** | For staged normative element folders using `README.md` + `reference-matrix.md`, `README.md` must contain only definition/scope and SSOT-completion links; inbound consumer lists belong only in `reference-matrix.md` | `**STATUS:** KO` |
| **Element differentiation** | New or materially rewritten normative element `README.md` must include explicit differentiation rationale (why the element is distinct and cannot be absorbed into another element); if not justified, content must be documented in the absorbing element instead of creating a new element artifact | `**STATUS:** KO` |

**Out of scope for this gate (by design):**

- Semantic verification that SSOT files actually contain complete normative text.
- Full semantic quality of doubt narratives beyond the hard-gate context checks above.
- Full supersede coherence and effective inverse on archive — use [check-solve-doubt.md](../skills/check-solve-doubt.md) checks 11–12 before commit.
- Automated traversal of `history/` for traceability.

Record violations under **Findings** with tag `COD-SSOT` or `COD-MATRIX` and affected paths.

### §4.2 Fractal doubts issue catalog and README profiles (hard gate)

Normative rules: [clean-onion-documentation.md](clean-onion-documentation.md) §2.3, §2.4.

Apply when staged paths include any:

- `**/doubts-and-decisions/README.md`
- `**/doubts-and-decisions/index.md`

| Check | Rule | On failure |
|-------|------|------------|
| **Issue catalog sections** | Staged `doubts-and-decisions/index.md` includes `## Open Issues`, `## Deferred Issues`, and `## Solved Issues` with column headers per §2.4 | `COD-DOUBTS-BODY` |
| **Issue catalog footer** | Staged index footer matches the canonical footer in §2.4 (all four instruction lines, verbatim) | `COD-DOUBTS-BODY` |
| **Issue catalog bijection** | Every doubt listed in **Solved Issues** has a file in `solved/`; every doubt listed in **Deferred Issues** has a file in `open/`; no Solved row for a file in `superseded/`; no `## Superseded Issues` section | `COD-DOUBTS-BODY` |
| **Deferred uniqueness** | A doubt ID cannot be listed simultaneously in `Open Issues` and `Deferred Issues` | `COD-DOUBTS-BODY` |
| **Footer parity** | When any staged `doubts-and-decisions/index.md` is modified, **every** `**/doubts-and-decisions/index.md` in the repo must carry the same canonical footer (prevents arbitrary template copy drift) | `COD-DOUBTS-BODY` |
| **How-to requires matrix** | Staged `doubts-and-decisions/README.md` with `## How to manage` (any casing) **must** also contain `## Decision matrix` | `COD-README` |
| **L1 sub-block minimal** | Staged README under `1-product-documentation/*/doubts-and-decisions/README.md` or `1-product-documentation/*/*/doubts-and-decisions/README.md` (any depth under Layer 1, excluding `1-product-documentation/doubts-and-decisions/`) **must not** contain `## How to manage`, `## Folders`, or `## Status` | `COD-README` |
| **L1 sub-block matrix** | Same minimal-path READMEs **must** contain `## Decision matrix` and an `On solve:` line referencing §2.1 | `COD-README` |

**L1 sub-block path rule:** Match `1-product-documentation/{segment}/doubts-and-decisions/README.md` where `{segment}` ≠ `doubts-and-decisions`, plus any deeper nesting under `1-product-documentation/` (e.g. `logical-domain/business-rules/`). The enriched Layer 1 root `1-product-documentation/doubts-and-decisions/README.md` is **exempt** from minimal-profile checks.

Record violations under **Findings** with tag `COD-DOUBTS-BODY` or `COD-README` and affected paths.

### §4.4 Document title contracts (hard gate)

Normative rules: [clean-onion-documentation.md](clean-onion-documentation.md) §2.2 (`humanizePath` algorithm).

Apply when staged paths include any `**/index.md` or `**/decision-matrix.md`.

| Check | Rule | On failure |
|-------|------|------------|
| **Catalog H1** | Staged `index.md` line 1 equals `# Catalog : {path-readable-for-human}` where `{path-readable-for-human}` is `humanizePath` of the file's directory from the layer root | `COD-CATALOG-H1` |
| **Dashboard H1** | Staged `decision-matrix.md` line 1 equals `# Dashboard : {path-readable-for-human}` where `{path-readable-for-human}` is `humanizePath` of the **owning block** directory (parent of `doubts-and-decisions/` or legacy `doubts-and-resolutions` / `doubts_and_resolutions/`) | `COD-DASHBOARD-H1` |

**Auditor `humanizePath` steps (must match §2.2):**

1. Split directory path into segments (forward slashes, relative to repo root).
2. **First segment only:** strip leading `^[0-9]+-`.
3. **Each segment:** split on `-` and `_`; strict Title Case per token; join tokens with a space within the segment.
4. Join segments with ` - `.

**Dashboard path:** For `…/doubts-and-decisions/decision-matrix.md` (or legacy `doubts-and-resolutions` / `doubts_and_resolutions`), humanize the directory **above** the doubts folder.

Record violations under **Findings** with tag `COD-CATALOG-H1` or `COD-DASHBOARD-H1` and affected paths.

### §4.5 History entry format and decision references (hard gate)

Normative rules: [clean-onion-documentation.md](clean-onion-documentation.md) §2 (`history/` bullets).

Apply when staged paths include any `**/history/*.md` excluding `**/history/README.md` and `**/history/index.md`.

| Check | Rule | On failure |
|-------|------|------------|
| **Entry format** | Each history entry must use `YYYY-MM-DD - <registrable description> - <optional Decision Id reference>` | `COD-HISTORY` |
| **Date required** | Every logged entry must include `YYYY-MM-DD` day stamp at the start of the entry | `COD-HISTORY` |
| **Local Decision Id format** | If the referenced doubt belongs to the same block, use bare `D-XXX` (no markdown link) | `COD-HISTORY` |
| **Cross-block Decision Id format** | If the referenced doubt belongs to another block, use qualified markdown link display `{block}/D-XXX` targeting owning block `solved/doubt-XXX.md` | `COD-HISTORY` |

Record violations under **Findings** with tag `COD-HISTORY` and affected paths.

### §4.8 History README template and H1 archetype (hard gate)

Normative rules: [clean-onion-documentation.md](clean-onion-documentation.md) §2.6.

Apply when staged paths include any `**/history/README.md`.

Template source of truth:

- `5-governance/history/README.md`

| Check | Rule | On failure |
|-------|------|------------|
| **Template readable** | The canonical template file exists and is parseable as markdown for section checks | `**STATUS:** KO` |
| **Template minimum contract** | The template must contain `## Practice`, `## Navigation`, and explicit history entry format + Decision Id conventions | `**STATUS:** KO` |
| **History H1 archetype** | Each staged `history/README.md` line 1 must be `# History : {path-readable-for-human}` using the owning block path (directory above `history/`) | `**STATUS:** KO` |
| **Body profile parity** | Each staged `history/README.md` must match the canonical template body profile and rule semantics (H1 varies by owning block only) | `**STATUS:** KO` |
| **Template self-check always on** | Validate template minimum contract on every audit run, even when template is not staged | `**STATUS:** KO` |

**Fail-closed policy:** If the template cannot be parsed, is missing required sections, or is semantically incomplete for the checks above, return `KO`.

Record violations under **Findings** with tag `COD-HISTORY-README` and affected paths.

### §4.9 Sprint deferred coherence (hard gate)

Normative rules: [4-sprints/README.md](../4-sprints/README.md) and [clean-onion-documentation.md](clean-onion-documentation.md) §2.4.

Apply when staged paths include any `4-sprints/**` file.

| Check | Rule | On failure |
|-------|------|------------|
| **No sprint close with pending doubts** | A staged sprint closure action is forbidden while that sprint scope still has rows in `Open Issues` or `Deferred Issues` | `**STATUS:** KO` |
| **Transfer to closed sprint requires reopen** | If staged changes defer or transfer a doubt to a previously closed sprint, the same staged session must include reopening evidence for the target sprint | `**STATUS:** KO` |
| **Deferred traceability in sprint scope** | Staged deferred records in sprint scope must include origin and target traceability in the doubt body when transfer is involved | `**STATUS:** KO` |

Record violations under **Findings** with tag `COD-SPRINT-DEFERRED` and affected paths.

### §4.10 Subdivision governance invariants (hard gate)

Normative rules:

- [1-product-documentation/use-cases/README.md](../1-product-documentation/use-cases/README.md) (`## UC Subdivision Governance`)
- [2-epics/README.md](../2-epics/README.md) (`## Epic Subdivision Governance`)
- [4-sprints/README.md](../4-sprints/README.md) (`## Sprint Subtasks Governance`)

Apply when staged paths include any of:

- `1-product-documentation/use-cases/README.md`
- `2-epics/README.md`
- `4-sprints/README.md`

| Check | Rule | On failure |
|-------|------|------------|
| **Parent orchestrator invariant** | The staged root policy text keeps the parent node as orchestrator only (parent must not become leaf-behavior SSOT) | `**STATUS:** KO` |
| **Leaf SSOT invariant** | The staged root policy text keeps leaf nodes/subtasks as SSOT for actionable/implementable scope slices | `**STATUS:** KO` |
| **No transversal links invariant** | The staged root policy text keeps the direct-children-only rule (no cross-branch/sibling transversal links) | `**STATUS:** KO` |
| **Full coverage invariant** | The staged root policy text keeps explicit 100% child coverage of the parent scope | `**STATUS:** KO` |
| **Parent closure checklist invariant** | The staged root policy text keeps closure gated by checklist completion of direct children/subtasks | `**STATUS:** KO` |

Record violations under **Findings** with tag `COD-SUBDIVISION` and affected paths.

### Subdivided artifact invariants

Apply when staged paths include any of:

- `1-product-documentation/use-cases/**/README.md`
- `2-epics/**/README.md`
- `4-sprints/**/README.md`

For each staged Use Case, Epic, or Sprint README that declares child nodes, validate the physical subdivision and the local ownership of normative behavior:

| Check | Rule | On failure |
|-------|------|------------|
| **Declared child structure invariant** | Each staged subdivided artifact README that declares child IDs has a corresponding child directory with a `README.md` for every declared direct child; child declarations must not exist only as headings or prose in the parent README | `**STATUS:** KO` |
| **Parent SSOT invariant** | A staged subdivided artifact README contains orchestration and direct-child coverage only; implementable leaf behavior must be defined in the corresponding child artifact README | `**STATUS:** KO` |
| **Direct-child locality invariant** | A staged subdivided artifact README references only its direct children and its local hierarchy map; nested descendants and sibling/cross-branch behavior must be owned by their nearer parent or leaf artifact | `**STATUS:** KO` |

Record violations under **Findings** with tag `COD-SUBDIVISION` and affected paths.

### §4.11 Index anti-aggregation (hard gate)

Normative rules:

- [clean-onion-documentation.md](clean-onion-documentation.md) §2 (fractal `index.md` locality and `index.md` anti-aggregation)

Apply when staged paths include any of:

- `**/index.md`

| Check | Rule | On failure |
|-------|------|------------|
| **No persistent global/cross-scope index inventory** | Staged `index.md` files must not add or maintain sections whose purpose is to enumerate artifacts from child branches or multiple blocks as a consolidated persistent inventory | `**STATUS:** KO` |
| **Index locality invariant** | Staged `index.md` files must remain local catalogs of their own directory scope (except mandatory doubts issue-catalog sections defined in §4.2) | `**STATUS:** KO` |

Record violations under **Findings** with tag `COD-INDEX-AGGREGATION` and affected paths.

### §4.12 Layer outbound-reference prohibition (hard gate)

Normative rules:

- [clean-onion-documentation.md](clean-onion-documentation.md) §1 (Strict Dependency Rule)
- [clean-onion-documentation.md](clean-onion-documentation.md) §4 (inward-only dependency matrix)

Apply when staged paths include any of:

- `1-product-documentation/**`
- `2-epics/**`
- `3-implementation/**`
- `4-sprints/**`
- `5-governance/**`

| Check | Rule | On failure |
|-------|------|------------|
| **No outer-layer references from inner layers** | For any staged layer `N`, files must not add markdown links or textual references targeting layers with index `> N` (evaluate against COD dependency matrix) | `**STATUS:** KO` |
| **No operational dependency on outer governance** | Staged files in layers 1-4 must remain executable within allowed inward dependencies and must not require outer-layer governance links to operate | `**STATUS:** KO` |
| **No matrix inversion in staged links** | Any staged cross-layer reference that violates inward-only directionality (inner -> outer) is forbidden, regardless of path overlap or file type | `**STATUS:** KO` |

Record violations under **Findings** with tag `COD-DIRECTIONALITY` and affected paths.

### §4.13 Layer 5 downstream-compatibility and inter-layer consistency (hard gate)

Normative rules:

- [clean-onion-documentation.md](clean-onion-documentation.md) §1 (Strict Dependency Rule)
- [clean-onion-documentation.md](clean-onion-documentation.md) §4 (dependency matrix)
- This file §2 (`INTER-LAYER NORMATIVE CONFLICT`) and §3 workflow step 6

Apply when staged paths include any of:

- `5-governance/**`

| Check | Rule | On failure |
|-------|------|------------|
| **Layer 5 compatibility sweep required** | Any staged change in Layer 5 that alters normative behavior, constraints, or validation semantics must be checked against affected lower-layer contracts (Layers 1-4) for compatibility | `**STATUS:** KO` |
| **No contradictory obligations across layers** | If Layer 5 staged rules impose, permit, or forbid behavior that conflicts with existing lower-layer normative contracts, the audit is a critical inconsistency | `**STATUS:** KO` |
| **No arbitrary precedence resolution** | Conflict resolution by unilateral precedence selection (for example "Layer 5 wins" without explicit PO decision and synchronized lower-layer updates) is forbidden | `**STATUS:** KO` |
| **Synchronized remediation requirement** | To pass, either (a) staged Layer 5 change must be shown compatible with lower layers, or (b) the same staged set must include synchronized lower-layer updates removing the inconsistency | `**STATUS:** KO` |

Record violations under **Findings** with tag `COD-CONFLICT-CRITICAL` and affected paths.

### §4.14 Business Rule Integration Contract enforcement (hard gate)

Normative rules:

- [1-product-documentation/business-rule-integration-contract.md](../1-product-documentation/business-rule-integration-contract.md)
- [clean-onion-documentation.md](clean-onion-documentation.md) §1 (Strict Dependency Rule)

Apply when staged paths include any of:

- `1-product-documentation/business-rule-integration-contract.md`
- `1-product-documentation/use-cases/**`
- `1-product-documentation/logical-domain/business-rules/**`
- `1-product-documentation/logical-domain/entities/**`

| Check | Rule | On failure |
|-------|------|------------|
| **Contract isolation preserved** | Staged contract text must remain internal to Layer 1 and must not add dependencies on outer layers | `**STATUS:** KO` |
| **Reference-only BR consumption** | Staged consumer artifacts (use cases and entities) must reference `BR-XX.YY` IDs and must not duplicate normative business-rule definitions owned by `logical-domain/business-rules/**` | `**STATUS:** KO` |
| **Bidirectional traceability artifacts** | Staged business-rule folders must preserve the `README.md` + `reference-matrix.md` split, and staged consuming artifacts (use cases and entities) must keep/update BR dependency tables | `**STATUS:** KO` |
| **Rule link target policy** | Staged cross-document links to business rules must target the rule `README.md`, never the rule folder path | `**STATUS:** KO` |

Record violations under **Findings** with tag `COD-BR-CONTRACT` and affected paths.

### §4.15 COD-inherited contract/policy clause enforcement (hard gate)

Normative rules:

- [clean-onion-documentation.md](clean-onion-documentation.md) §1 (Strict Dependency Rule)
- This repository's COD-inherited contract/policy profiles in Layers 1-3

Apply when staged paths include any of:

- `1-product-documentation/business-rule-integration-contract.md`
- `3-implementation/bootstrap-policy.md`
- `3-implementation/component-decoupling-contracts.md`
- `3-implementation/i18n-implementation-contract.md`
- `3-implementation/platform-policies/01-governance-directionality/PP-01.01-platform-to-runtime-directionality.md`
- `3-implementation/platform-policies/01-governance-directionality/rp-to-pp.runtime.md`

Also apply when running `git commit --amend` if the commit being amended contains any protected file above, even when current staged scope does not include protected files.

| Check | Rule | On failure |
|-------|------|------------|
| **Inheritance clause presence** | Each staged COD-inherited document must include a `## COD Inheritance And Liability Boundary` section | `**STATUS:** KO` |
| **No unilateral semantic drift** | Staged changes in COD-inherited documents must not alter inherited normative semantics unilaterally; changes require synchronized governance alignment in the same staged set | `**STATUS:** KO` |
| **Liability-boundary clause preserved** | The section must explicitly state that unsynchronized local modifications invalidate COD conformance and are outside COD responsibility coverage | `**STATUS:** KO` |

Record violations under **Findings** with tag `COD-INHERITED-PROFILE` and affected paths.

### §4.16 Protected COD-inherited files explicit-confirmation gate (hard gate)

Normative rules:

- This document §3 workflow step 4
- COD-inherited protection clauses declared in protected files

Apply when staged paths include any of:

- `AGENTS.md`
- `5-governance/README.md`
- `skills/README.md`
- `1-product-documentation/business-rule-integration-contract.md`
- `3-implementation/bootstrap-policy.md`
- `3-implementation/component-decoupling-contracts.md`
- `3-implementation/i18n-implementation-contract.md`
- `3-implementation/platform-policies/01-governance-directionality/PP-01.01-platform-to-runtime-directionality.md`
- `3-implementation/platform-policies/01-governance-directionality/rp-to-pp.runtime.md`

Exempt from this gate:

- `AGENTS.custom.md`
- `5-governance/customized/**`
- `skills/customized/**`

Classification guard (mandatory):

1. Protected-file detection uses only this section's protected list (`Apply when staged...`) minus this section's exempt list.
2. Exempt paths are never treated as protected, even if they contain COD-related wording.
3. If staged scope contains only exempt paths, no token request is allowed.
4. If staged scope contains both protected and exempt paths, token request applies only to protected paths, and the user-facing affected-files list must exclude exempt paths.
5. Detection source of truth is command output, not text search over repository content. Searching for words like `protected` is non-authoritative and must not drive the decision.
6. Fail-closed: if classification cannot be computed from the mandatory command outputs, result is `KO` and commit flow must stop.

Confirmation protocol (mandatory):

1. The agent must stop and show this exact user-facing request:

```text
Protected COD files are staged and commit is blocked by default.
Reason: these files define inherited COD governance semantics.
Affected files: <exact staged protected file list>
If you want to proceed under your responsibility, reply exactly:
USER_CONFIRMS_PROTECTED_OVERRIDE: YES
```

2. The only valid confirmation is the exact token line: `USER_CONFIRMS_PROTECTED_OVERRIDE: YES`.
3. The token is valid only when it appears in the user's **latest explicit turn** at the moment the commit command is executed.
4. If a newer user turn exists and does not include the token, any earlier token is expired and cannot be reused.
5. Any other response is invalid (`ok`, `continue`, `haz commit`, `adelante`, or similar).
6. Confirmation must never be inferred.
7. Execution evidence must include the exact token and indicate it came from the most recent user turn before report write.

User guidance when asked about clause meaning (mandatory):

When the user asks what the clause means, the agent must offer exactly two options:

1. **Revert protected-file changes** and use authorized customized routes:
     - `AGENTS.custom.md`
     - `5-governance/customized/`
     - `skills/customized/`
2. **Approve protected changes under user responsibility** by replying with the exact command/token:
     - `USER_CONFIRMS_PROTECTED_OVERRIDE: YES`

| Check | Rule | On failure |
|-------|------|------------|
| **Default KO on protected-file staged changes** | Any staged change touching protected files must be blocked by default until explicit user confirmation is obtained | `**STATUS:** KO` |
| **Explicit token required** | The auditor must request explicit user confirmation and receive the exact token `USER_CONFIRMS_PROTECTED_OVERRIDE: YES`; silence or ambiguous answers are invalid | `**STATUS:** KO` |
| **No inferred confirmation** | Generic commit intent or non-token approvals must not be interpreted as confirmation for protected override | `**STATUS:** KO` |
| **Override evidence required** | Commit may proceed only if execution evidence includes exact line `USER_CONFIRMS_PROTECTED_OVERRIDE: YES` plus the confirmed file list and user-responsibility note | `**STATUS:** KO` |

Record violations under **Findings** with tag `COD-PROTECTED-OVERRIDE` and affected paths.

---

## 5. SOLID Validation Criteria

| Principle | Question | §9 minimum |
|-----------|----------|------------|
| **S** | Does each changed unit have a single reason to change? | **Required** |
| **O** | Can behavior be extended without modifying existing source code? | If relevant |
| **L** | Do implementations break the base interface contract? | If relevant |
| **I** | Are there unused methods in interfaces clients do not need? | If relevant |
| **D** | Do dependents target abstractions, not concrete outer-layer details? | **Required** |

Record violations under **Findings** with principle id (S/O/L/I/D) and affected paths.

---

## 6. L4 Critical Zone — Pseudocode Mirror Validation

### 6.1 Layer 1 artifacts (structure)

Each Critical Zone (`ZC-XX-{description}`) in [1-product-documentation/diagrams-c4/L4-critical-zones/](../1-product-documentation/diagrams-c4/L4-critical-zones/) **must** follow the folder layout defined in that layer's README (`README.md`, `logic-model.md`, `flow-chart.mmd`, `implementation-contracts/`).

Roles, shadowing consistency, and mirror semantics: [3-implementation/README.md](../3-implementation/README.md) (L4 Critical Zones section).

### 6.2 Layer 3 projection (what must mirror)

For each component that implements a ZC, the **active technology** folder must contain:

```text
3-implementation/{component}/{active-technology}/L4-critical-zones/ZC-XX-{description}/logic-implementation.{ext}
```

`logic-implementation.{ext}` is a **direct translation** of the **Pseudocode** section from:

```text
1-product-documentation/diagrams-c4/L4-critical-zones/ZC-XX-{description}/implementation-contracts/{logical-component-name}.md
```

That slice must stay aligned with `logic-model.md` per the **Shadowing Consistency Rule** in Layer 3.

The validated pair is:

```text
{pseudocode}  ←—— mirror ——→  {active-technology code}
     Layer 1                         Layer 3
```

`flow-chart.mmd` is **not** mirrored as code. When a ZC changes, update `logic-model.md`, affected **Pseudocode** slices, matching `logic-implementation.{ext}` files, then derive `flow-chart.mmd`.

### 6.3 Pre-commit check (agent audit)

When staged files include any path under:

- `1-product-documentation/diagrams-c4/L4-critical-zones/`, or
- `3-implementation/*/L4-critical-zones/`, or
- a component `README.md` listing `ZC-XX` implementations,

the auditor **must**:

1. Identify each affected `ZC-XX` and logical component.
2. Locate `logic-model.md` and the **Pseudocode** section in the matching `implementation-contracts/{component}.md`.
3. Apply the **Shadowing Consistency Rule** from [3-implementation/README.md](../3-implementation/README.md) if `flow-chart.mmd` is part of the staged change.
4. Locate the Layer 3 `logic-implementation.{ext}` under the component's **active** technology folder.
5. Verify structural and behavioral parity (control flow, ports invoked, failure semantics). Naming may follow the target language; logic must not drift.
6. If Layer 1 pseudocode changed without a matching Layer 3 update (or vice versa), set `**STATUS:** KO`.

Obsolete technology folders (`logic-frozen.{ext}`) are excluded from the active mirror check unless the staged change explicitly touches frozen history.

### 6.4 Bidirectional traceability (Layer 3 obligation)

Each `3-implementation/{component}/README.md` **must** list every `ZC-XX` the component implements. Cross-check against the **Affected Components** table in the Layer 1 ZC `README.md`. Layer 1 is the SSOT for participation; Layer 3 declares materialization.

---

## 7. Audit Report Format

Update [solid-principles-review-report.md](solid-principles-review-report.md) per [README.md](README.md):

- **Preserve** everything above `## Current audit`.
- **Overwrite** from `## Current audit` to EOF:

```markdown
## Current audit

**Audit completed:** <yyyy-MM-ddTHH:mm:ss from Get-Date -Format "yyyy-MM-ddTHH:mm:ss">
**STATUS:** PASS

### Scope of last audit

<staged files / layers touched>

### Findings

<S, O, L, I, D + COD notes; or "No violations.">

Each finding line in this section must follow the §2.1 finding record contract (Rule clause, Evidence, Qualification, Boundary check, Impact).

### COD cross-check

<file integrity policy §1.1 + inward-only + stack leakage + fractal index + catalog bijection §4.3 + §4.1 self-containment/matrix + §4.2 doubts issue catalog/README + §4.4 Catalog/Dashboard H1 + §4.10 subdivision invariants when applicable per clean-onion-documentation.md §4>

Mandatory lines in this section:

- `- **Unstaged invalidation risk (workflow §3.8):** PASS|KO|N/A`

### SOLID cross-check

<DIP + SRP at minimum; other principles if relevant>

### L4 ZC pseudocode mirror cross-check

<per §6.3: ZC/component pairs reviewed, parity result, or "Not applicable — no ZC paths staged.">
```

- Use **`STATUS: PASS`** only when COD §4, SOLID (S + D minimum), and L4 mirror (when applicable) all pass.
- Run **`Get-Date`** immediately before writing `**Audit completed:**` — hooks reject timestamps older than **60 seconds**.

---

## 8. Hook Enforcement (Optional — Audit Report Only)

The template ships [validate-integrity-report.ps1](../.githooks/validate-integrity-report.ps1) for teams that choose to activate it (`git config core.hooksPath .githooks`). **Activation is optional** per [GETTING_STARTED.md](../GETTING_STARTED.md) §2.

When active, `.githooks/pre-commit` and `.cursor/hooks.json` block `git commit` with staged changes unless:

If staged content is empty (for example, message-only amend), hook validation is skipped and commit is allowed.

1. `5-governance/solid-principles-review-report.md` **exists**.
2. `**STATUS:** PASS` in `## Current audit`.
3. `**Audit completed:**` is **≤ 60 seconds** old.
4. The report includes `**Unstaged invalidation risk (workflow §3.8):** PASS` or `N/A`.

On failure:

```text
PRECOMMIT BLOCKED: AUDIT-EVIDENCE-MISSING

This repository requires real pre-commit audit execution before commit.
Updating only report timestamp/STATUS is forbidden.

Required actions (in order):
1) Run full workflow from AGENTS.md section 9 and 5-governance/pre-commit-validation-rules.md section 3.
2) If staged paths include doubts-and-decisions, run skills/check-solve-doubt.md for each touched solved/superseded record.
3) Run SOLID/COD/L4 audit on staged changes via skills/solid.md.
4) Regenerate only "## Current audit" in 5-governance/solid-principles-review-report.md with actual results.
5) Set STATUS: PASS only if all checks pass; otherwise STATUS: KO and abort commit.

Normative sources:
- AGENTS.md section 9
- 5-governance/pre-commit-validation-rules.md section 1 and section 3
- skills/solid.md
```

One-time setup per clone: [GETTING_STARTED.md](../GETTING_STARTED.md) §2.

---

## 9. L4 Mirror Validation — Agent-Only (No Template Hook)

### Policy

This template **does not** and **will not** include a Git hook (or any scripted gate) that compares Layer 1 pseudocode to Layer 3 `logic-implementation.{ext}` files. That decision is **out of scope** for the template.

Teams that fork this template may add project-specific automation on their own responsibility. The template assumes they will **not** — **agent enforcement is sufficient**.

### Why no mirror hook ships here

| Factor | Implication |
|--------|-------------|
| **Unknown stacks** | `{component}`, `{active-technology}`, and `logic-implementation.{ext}` are defined per project, not in the template. |
| **Semantic parity** | File-existence checks are weak; behavioral equivalence requires agent or human review. |
| **Maintenance cost** | A generic mirror hook would be fragile and unused in practice. |

### Enforcement model

| Concern | Enforced by |
|---------|-------------|
| **L4 pseudocode mirror** (§6.3) | **Agent** — mandatory audit before commit when ZC paths are staged; `**STATUS:** KO` on drift |
| **Audit report freshness** (§8) | **Optional Git hook** — `validate-integrity-report.ps1` only; teams enable via `core.hooksPath` per [GETTING_STARTED.md](../GETTING_STARTED.md) §2 |

The agent contract (`AGENTS.md` §9 + this document) is the **authoritative guarantee** that ZC changes replicate to Layer 3 code. No additional template artifact is required.

---

## 10. Related Paths

| Topic | Path |
|-------|------|
| Constitution pointer | [AGENTS.md](../AGENTS.md) §9 |
| Getting started | [GETTING_STARTED.md](../GETTING_STARTED.md) |
| Contributing | [CONTRIBUTING.md](../CONTRIBUTING.md) |
| Auditor skill stub | [skills/solid.md](../skills/solid.md) |
| COD dependency matrix | [clean-onion-documentation.md](clean-onion-documentation.md) §4 |
| L4 ZC logical standard | [1-product-documentation/diagrams-c4/L4-critical-zones/README.md](../1-product-documentation/diagrams-c4/L4-critical-zones/README.md) |
| Logical domain standard | [1-product-documentation/logical-domain/README.md](../1-product-documentation/logical-domain/README.md) |
| L4 ZC technical mirror | [3-implementation/README.md](../3-implementation/README.md) |
| Gate artifact | [solid-principles-review-report.md](solid-principles-review-report.md) |
| Hook script | [validate-integrity-report.ps1](../.githooks/validate-integrity-report.ps1) |
