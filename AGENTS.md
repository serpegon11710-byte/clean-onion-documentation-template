# AI Agent Governance & Execution Framework (AGENTS.md)

This document constitutes the unbreachable behavioral framework for any AI Agent (Cursor, Claude Code, GitHub Copilot) operating within this repository. You must read and comply with these rules upon initialization.

---

## 1. Temporal & Runtime Anchor (The "System Truth" Protocol)

To prevent context drift and stale documentation caused by IDE metadata, you must follow this protocol:

- **No Rely on Context Date:** Never use `Today's date` provided in the IDE's system context; it is unreliable in long-running sessions.

- **The "Get-Date" Mandate:** Whenever you are instructed to document the **current time of generation** (e.g., `Last updated:` headers, sprint closure notes, or sync-logs), you must execute this command in the terminal before writing:

```powershell
Get-Date -Format "yyyy-MM-dd"
```

- **Strict Usage (Generation Timestamps Only):** Use the exact output of the `Get-Date` command **only** for metadata fields representing the moment of document generation.

- **Historical Data Preservation:** **Never** alter dates referring to historical events (e.g., meeting dates, past sprint occurrences, or logs of finished tasks) based on the `Get-Date` command. Historical dates are immutable and must remain unchanged unless explicitly corrected for factual accuracy.

- **Header Correction:** You are authorized to correct a header date **only if** the header specifically claims to represent the "current" status or "last update" of the document.

---

## 2. Clean Onion Documentation (COD) Rules

You are operating inside a concentric, modular repository structure. You must strictly follow the dependency rules:

1. **Outer Layers Dependency:** Files in outer layers (`4-sprints`, `3-implementation`) can reference inner layers (`1-product-documentation`). The authoritative **layer dependency matrix** and **stack leakage** rules live in [5-governance/clean-onion-documentation.md](5-governance/clean-onion-documentation.md) **§4** — do not duplicate them here.

2. **Inner Layer Agnosticism:** Core business logic and use cases inside `1-product-documentation` must remain entirely agnostic of technological stacks, deployment details, or sprint timelines.

3. **Atomic Fractal Pattern:** Every micro-feature folder should contain an independent ecosystem (`README.md`, `index.md`, `history/`, and `doubts-and-decisions/`). Never mix scopes.

4. **Token Management:** Do not read full directories recursively unless explicitly instructed. Lean on local `index.md` files at layer root to navigate the architecture efficiently.

---

## 3. Environment & Shell Safeguards (Windows PowerShell 5.x)

When writing automation scripts, scaffolding files, or updating documentation through terminal commands, you **must** comply with the following PowerShell restrictions:

- **No Heredocs (`<<`):** PowerShell 5.x does not support bash-style Heredocs. Never emit scripts using this syntax.

- **File Write Prohibition (Default-Deny):** Do not use OS-native PowerShell write cmdlets for persistent edits (`Set-Content`, `Out-File`, `Add-Content`, `>`/`>>`) because they can inject platform defaults (for example CRLF) and break repository integrity.

- **Approved Editing Methods:** Use IDE-native editing operations or `apply_patch` as primary methods. CLI fallback is allowed only when UTF-8 + LF normalization is explicitly enforced.

- **Mandatory Post-Write Verification:** After any automated write, verify UTF-8 + LF compliance and immediately normalize with approved methods if drift is detected.

- **Temporary Files Safeguard:** If complex string escaping is required, write the content to an atomic temporary file (`*.tmp`), execute the transfer, and delete the file immediately after confirmation.

Normative source: [5-governance/pre-commit-validation-rules.md](5-governance/pre-commit-validation-rules.md) §1.1.

---

## 4. Formatting & Syntax Enforcement

- **Strict Markdown Standards:** Maintain clean `LF` line endings across all OS environments (enforced by `.editorconfig` and `.gitattributes`).

- **The MD040 Guardrail:** Every single code block you generate inside any markdown file **must** have an explicitly assigned language identifier (e.g., `javascript`, `text`, `powershell`). Writing generic unassigned fenced blocks is strictly forbidden.

---

## 5. Interaction Protocol

Before modifying any architectural layout or drafting a core system entity:

1. Scan `5-governance/` for active engineering constraints.

2. Cross-reference the scope with `1-product-documentation/`.

3. If an architectural ambiguity arises, log the query under the local `doubts-and-decisions/` index and ask the user for confirmation before writing code.

---

## 6. Source of Truth Registry

All authoritative content lives in **one place per concern**. Adapters (`CLAUDE.md`, `.github/copilot-instructions.md`, `.cursor/skills/*/SKILL.md`) **redirect** here — they never duplicate policy text.

| Concern | Authoritative path | Agent action |
|---------|-------------------|--------------|
| **Constitution** (dates, shell, MD040, navigation law, pre-commit gate §9) | `AGENTS.md` | Loaded automatically (Cursor, Codex, Windsurf) or via `@AGENTS.md` (Claude) |
| **Repository map & philosophy** | `README.md` | Read once per session if task touches structure |
| **Human getting started** | `GETTING_STARTED.md` | Clone, hooks, first commit — see §2 |
| **Contributing** | `CONTRIBUTING.md` | External PR workflow and pre-commit gates |
| **Cross-cutting policies** | `5-governance/index.md` → linked `*.md` | Open index; load **only** policies cited by the task |
| **COD fractal standard** | `5-governance/clean-onion-documentation.md` | Load when creating/editing layer structure |
| **Layer dependency matrix & stack leakage (SSOT)** | `5-governance/clean-onion-documentation.md` **§4** | Pre-commit and structural audits — see §9 |
| **Multi-agent architecture** | `5-governance/multi-agent-governance.md` | Maintainers configuring agents; adapters and skills contract |
| **Pre-commit validation rules (SSOT)** | `5-governance/pre-commit-validation-rules.md` | COD, SOLID, L4 mirror criteria; auditor workflow — see §9 |
| **SOLID & COD pre-commit report** | `5-governance/solid-principles-review-report.md` | Regenerated each §9 audit (`## Current audit`); usage in `5-governance/README.md` |
| **Operational modes** | `skills/<name>.md` + `skills/README.md` + §8 below + agent stubs | Triple contract — see [skills/README.md](skills/README.md) and [multi-agent-governance.md](5-governance/multi-agent-governance.md) §5 |
| **Layer scope & navigation** | `{N-layer}/README.md` | Read when working inside that layer |
| **Layer file catalog** | `{N-layer}/index.md` | **Mandatory** file discovery — never list-dir |
| **Doubts issue catalog** | `{N-layer}/doubts-and-decisions/index.md` | Load when managing doubt lifecycle (open/solved) |
| **Decision matrix (Decision Id index)** | `{block}/doubts-and-decisions/decision-matrix.md` | Load when closing doubts; scope by `## {element}` only; resolve cross-block via `[block/D-XXX](…)` |
| **Matrix impact (closure map)** | `## Matrix impact` in solved record | Load when closing or superseding; operational SSOT of touched matrix rows |
| **Superseded records (forensic)** | `{block}/doubts-and-decisions/superseded/` | Do not load unless human requests forensic traceability |
| **Intra-layer SSOT & decision matrix** | `5-governance/clean-onion-documentation.md` §2.1 | Load when closing doubts or editing entities/BR/UC normative files |
| **Fractal document titles & doubts README profiles** | `5-governance/clean-onion-documentation.md` §2.2–§2.4 | Load when creating or editing `index.md`, `decision-matrix.md`, or `doubts-and-decisions/README.md` |
| **Doubt overlap analysis** | `skills/refactor-doubts.md` | Invoke before PO closure when overlaps are suspected |
| **Doubt closure verification** | `skills/check-solve-doubt.md` | Human invokes after closure, before commit |
| **History map** | `{N-layer}/history/index.md` | Load when writing traceability logs |

### Navigation law (anti-scan)

```text
PROHIBITED:  glob **/*, recursive list_dir, "explore the codebase"
REQUIRED:    AGENTS.md → target layer README → target layer index → specific file
```

Token cost stays bounded because the agent reads **at most 3–5 index hops**, not the whole tree.

---

## 7. Bootstrap Sequence

When starting work, read files in this order — stop as soon as the task scope is clear:

```text
1. AGENTS.md                    (this file — already loaded)
2. README.md                    (if task scope touches repository structure)
2b. GETTING_STARTED.md            (if human setup: clone, core.hooksPath, first commit)
3. 5-governance/index.md     (if policy or architecture is involved)
4. {layer}/README.md            (if editing inside a layer)
5. {layer}/index.md          (mandatory file discovery within the layer)
6. skills/{mode}.md             (only if user invokes an operational mode)
```

Do **not** scan the repository beyond these hops.

---

## 8. Skills Catalog

**Creating or updating a skill:** follow [skills/README.md](skills/README.md) before adding or changing files.

Operational modes live in `skills/`. Load the file when invoked; do not infer rules from this catalog.

| Name | Path | When to use |
|------|------|-------------|
| analyze | [skills/analyze.md](skills/analyze.md) | Planning or scope review — no code generation |
| architect | [skills/architect.md](skills/architect.md) | Extract conceptual contracts for a blank workspace |
| document | [skills/document.md](skills/document.md) | Documentation-only turns — no source code changes |
| implement | [skills/implement.md](skills/implement.md) | Atomic code changes on approved tasks |
| product-owner | [skills/product-owner.md](skills/product-owner.md) | PO debate and doubt resolution |
| refactor-doubts | [skills/refactor-doubts.md](skills/refactor-doubts.md) | Audit overlapping open doubts before PO closure |
| check-solve-doubt | [skills/check-solve-doubt.md](skills/check-solve-doubt.md) | Closure audit for one doubt before commit (mandatory when staged doubt records are present) |
| solid | [skills/solid.md](skills/solid.md) | SOLID and Layer 5 decoupling audit |

---

## 9. Pre-Commit Integrity Contract

**Mandatory guardrail:** Any agent that reads this file **must** adopt the pre-commit gate defined in [5-governance/pre-commit-validation-rules.md](5-governance/pre-commit-validation-rules.md). **No commit may proceed** until that audit completes successfully.

Before any commit:

1. If staged paths include `**/doubts-and-decisions/**`, run [check-solve-doubt](skills/check-solve-doubt.md) for each touched solved/superseded doubt; any `KO` result blocks commit.
2. Invoke [solid](skills/solid.md), which loads and executes **all** criteria in `pre-commit-validation-rules.md` (COD, SOLID, L4 ZC pseudocode mirror when applicable) **and** the skill-only propagation coherence check in that skill.
3. Regenerate `## Current audit` in [solid-principles-review-report.md](5-governance/solid-principles-review-report.md) per that document.
4. Abort the commit on `**STATUS:** KO` or any unresolved violation.
5. **Anti-evasion (hard rule):** It is forbidden to update only `Audit completed`, `STATUS`, or other report metadata to satisfy the hook without executing the required audits.
6. **Source-of-truth discipline:** Hook scripts are transport gates only; they never define validation scope. Validation scope is defined exclusively by `pre-commit-validation-rules.md` and `skills/solid.md`.

Hook enforcement for the **audit report artifact** is documented in §8. L4 pseudocode mirror validation has **no automated hook** in this template — see §9.

## 10. Linguistic Policy

You must strictly comply with the [Repository Language Standard](5-governance/repository-language-standard.md). 
- **Reasoning:** In the user's language.
- **Persistence:** English only. 
- **No redundancy:** Single-language repository.
