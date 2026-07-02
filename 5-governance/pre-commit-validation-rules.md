# Pre-Commit Validation Rules

**Last updated:** 2026-07-01

**Source of truth** for the pre-commit integrity contract (`AGENTS.md` §9), auditor behavior ([skills/solid.md](../skills/solid.md)), and all validation criteria executed before `git commit`.

The optional Git hook (§8) validates only the audit **report artifact**. **L4 pseudocode mirror** validation is **agent-only** (§6.3, §9) — this template does not ship and will not ship a hook for that concern.

> **Related:** [clean-onion-documentation.md](clean-onion-documentation.md) §4 (COD matrix SSOT), [solid-principles-review-report.md](solid-principles-review-report.md) (gate artifact), [README.md](README.md) (report file structure).

---

## 1. Pre-Commit Integrity Contract

**Mandatory guardrail:** Any agent (Cursor, Claude Code, Aider, GitHub Copilot, or equivalent) that reads `AGENTS.md` **must** adopt this contract as a pre-commit gate. **No commit may proceed** until this audit completes successfully.

Before any commit, perform an architectural audit of all **staged** changes. Invoke [skills/solid.md](../skills/solid.md) and validate every criterion in this document.

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
2. **GOVERNANCE PRIORITY:** If a SOLID principle or a Layer 5 rule conflicts with a quick implementation, governance takes absolute priority.
3. **CONSTRUCTIVE CRITICISM:** Identify which specific principle (S, O, L, I, D) or COD rule is violated and propose a technical alternative aligned with the layered architecture.

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

For `See D-` / `See doubt-` findings, the boundary check is mandatory: the auditor must explicitly state whether the case is normative delegation to doubt records (violation) or same-layer contextual linkage with self-contained SSOT behavior (allowed).

---

## 3. Regeneration Workflow

1. Review **staged** changes (`git diff --cached`).
2. Validate **file integrity policy** per §1.1 (write method compliance + UTF-8/LF post-write verification evidence when applicable).
3. Validate **COD** per [clean-onion-documentation.md](clean-onion-documentation.md) §4, §2.1, §2.2–§2.4 (see §4, §4.1, §4.2, §4.3, and §4.4 below).
4. Validate **SOLID** — at minimum **S** and **D** on staged changes (see §5 below).
5. Validate **L4 ZC pseudocode mirror** when staged changes touch Critical Zones or their Layer 3 projections (see §6 below).
6. Run `Get-Date -Format "yyyy-MM-ddTHH:mm:ss"` **once**, immediately before writing report headers.
7. Overwrite **only** `## Current audit` to EOF in [solid-principles-review-report.md](solid-principles-review-report.md).
8. Set `**STATUS:** PASS` only if **all** checks pass; otherwise `**STATUS:** KO` and **abort the commit**.

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
| **Effective inverse on archive** | For each row in a fully superseded doubt's `Matrix impact`, the referenced block's staged `decision-matrix.md` cell for that `(element, event)` must **not** resolve to the archived doubt ID | `**STATUS:** KO` |
| **Supersede header** | Staged superseded record may add only `**Superseded by:** {block}/D-YYY` at the top plus `Matrix impact` status updates — no other rewrites of closed debate body | `**STATUS:** KO` if debate body was rewritten |
| **Doubt context chains** | Staged doubt files **must not** add `See D-` patterns for context expansion (supersede declarations and `Matrix impact` status are allowed) | `**STATUS:** KO` |

**Out of scope for this gate (by design):**

- Semantic verification that SSOT files actually contain complete normative text.
- Verification that a self-contained doubt file is fully self-contained.
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
| **Issue catalog sections** | Staged `doubts-and-decisions/index.md` includes `## Open Issues` and `## Solved Issues` with column headers per §2.4 | `COD-DOUBTS-BODY` |
| **Issue catalog footer** | Staged index footer matches the canonical footer in §2.4 (all three instruction lines, verbatim) | `COD-DOUBTS-BODY` |
| **Issue catalog bijection** | Every doubt listed in **Solved Issues** has a file in `solved/`; no Solved row for a file in `superseded/`; no `## Superseded Issues` section | `COD-DOUBTS-BODY` |
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

<file integrity policy §1.1 + inward-only + stack leakage + fractal index + catalog bijection §4.3 + §4.1 self-containment/matrix + §4.2 doubts issue catalog/README + §4.4 Catalog/Dashboard H1 when applicable per clean-onion-documentation.md §4>

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

When active, `.githooks/pre-commit` and `.cursor/hooks.json` block `git commit` unless:

1. `5-governance/solid-principles-review-report.md` **exists**.
2. `**STATUS:** PASS` in `## Current audit`.
3. `**Audit completed:**` is **≤ 60 seconds** old.

On failure:

```text
Validaciones COD / SOLID KO, repita las validaciones
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
