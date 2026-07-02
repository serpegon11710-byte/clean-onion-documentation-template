# Clean Onion Documentation (COD) Standard

**Last updated:** 2026-07-01

This cross-cutting policy defines the **directionality**, **isolation**, and **internal fractal structure** of all documentation layers within the repository. It complements the Agent Governance rules defined in `AGENTS.md`.

## 1. Purpose and Onion Dependency Rule

The goal of this standard is to guarantee **absolute isolation of concerns** in project documentation. Following the concentric model of Onion Architecture, the conceptual core is located in the protected center, while management, technology, and timelines are deployed in the outer layers.

**Strict Dependency Rule:** Outer layers (higher numbers) **know**, **wrap**, and **reference** inner layers (lower numbers). Inner layers are **entirely agnostic** and **never** have visibility, links, or feedback loops regarding what happens outside them.

## 2. The Internal Fractal Block/Layer Pattern

To guarantee absolute optimization in token consumption and prevent AI context loss, **every relevant layer or sub-module** of the system must strictly follow an identical, fractal, and self-contained file system pattern.

Every block or main folder within the layers must replicate this exact scheme:

```text
📁 X-block-name/
├── 📄 README.md                 <-- Fixed instructions: HOW the AI must operate in this block.
├── 📄 index.md                  <-- Live directory/Map of all files in this section.
│
├── 📁 history/                  <-- Chronological modification log (Traceability).
│   ├── 📄 2026-W26-changes.md   <-- One file per period Week of Year.
│   └── 📄 2026-W27-changes.md
│
└── 📁 doubts-and-decisions/   <-- Doubt & Decision subsystem (debate + effective index).
    ├── 📄 README.md             <-- Strict protocol for opening/closing issues.
    ├── 📄 index.md              <-- Issue catalog (Open vs. Solved).
    ├── 📄 decision-matrix.md    <-- Dashboard: Decision Id per (element, event).
    ├── 📁 open/                 <-- Debate in progress.
    │   ├── 📄 doubt-001.md      <-- Doubt & Decision record (open phase).
    │   └── 📄 doubt-002.md
    ├── 📁 solved/               <-- Closed Doubt & Decision records (operational).
    │   └── 📄 doubt-000.md      <-- Moved from open/ on closure.
    └── 📁 superseded/           <-- Fully superseded records (forensic only).
        └── 📄 doubt-000.md      <-- Archived from solved/ when no longer indexed.

```
### Fractal Component Specification:

- **`README.md` (Static):** Defines the folder scope and fixed AI behavioral rules. Minimizes token usage as it remains constant.

- **`index.md` (Dynamic):** File catalog for the **current directory only**. The table lists exclusively `.md` files at the same path level (excluding `index.md` itself). Subdirectories are **not** listed — each child folder maintains its own `index.md`.
- **`index.md` table contract (Mandatory):** Every `index.md` must include the file catalog table with header `| File name | Description |`. The table is **required even when empty** (header + separator, zero data rows). Placeholder or dummy rows are **forbidden**. An empty table renders correctly in GitHub, VS Code, and CommonMark parsers.
- **`index.md` catalog bijection (Mandatory):** Each catalog row **must** name a `.md` file **tracked in git** at the same path level. Every tracked same-level `.md` except `index.md` **must** appear exactly once. Rows for missing, gitignored, or deleted files are **forbidden**. Subdirectory paths (e.g. `logical-domain/`) are **forbidden** in the file catalog table. On add/remove/rename of a same-level `.md`, update `index.md` in the **same commit**.

- **`history/`:** Records modifications chronologically in fragmented files. The past is frozen and does not contaminate the active chat context.
- **History time granularity (Mandatory):** Keep one file per week (`YYYY-Www-changes.md`), but every documented finding or change inside that weekly file **must** include its own day stamp (`YYYY-MM-DD`). Weekly fragmentation does not replace daily traceability.
- **History entry format (Mandatory):** Register each entry as `YYYY-MM-DD - <registrable description> - <optional Decision Id reference>`.
- **History Decision Id reference (Mandatory when present):** Use bare `D-XXX` when the doubt belongs to the same block. Use a qualified markdown link `{block}/D-XXX` when the doubt belongs to another block, targeting the owning block's `solved/doubt-XXX.md` record.

- **`doubts-and-decisions/`:** Doubt & Decision subsystem in atomic form (one file per id). Debate in progress in **`open/`**; closed Doubt & Decision records with operational value in **`solved/`**; fully superseded records in **`superseded/`** (forensic only — agents do not load without explicit human instruction). The **`index.md`** issue catalog tracks **`open/`** and **`solved/`** only (`Open Issues` / `Solved Issues` — **no** `Superseded Issues` table). Files must not move between `open/`, `solved/`, and `superseded/` without the matching index update (remove Solved row when archiving to `superseded/`). Physical filename stays **`doubt-XXX.md`** in all phases (phase is encoded by folder only).

- **`decision-matrix.md`:** Dashboard of **Decision Id** (`D-XXX`) per `(element, event)` for the owning block. Updated on every normative closure. See §2.1.

### §2.5 Normative element structure (general COD pattern)

This pattern applies to any COD block that documents a normative element (entity, rule, use-case element, policy item, or equivalent).

| Artifact | Mandatory role |
|----------|----------------|
| `README.md` | Normative definition and scope of the element (SSOT for that element) |
| `reference-matrix.md` | Consumption and dependency references only |

Mandatory rules:

1. `README.md` may link only to other artifacts that **directly complete the definition** (SSOT completion links), never as sibling pointers (forbidden pattern: "see my sibling" without normative completion purpose).
2. "This element consumes me" / inbound-consumer lists must live in `reference-matrix.md`, never in `README.md`.
3. Every element must be explicitly differentiated: the definition must justify why it is a distinct normative element and not a simple extension that should be absorbed by another element.
4. One folder per element is a mandatory COD policy. If differentiation cannot be justified, do not create a new element artifact: document the content inside the element that absorbs it.

### §2.2 Document title contracts (`index.md` and `decision-matrix.md`)

Two title prefixes; **body profiles** vary by file type and path (see §2.4 for the doubts issue catalog body).

#### `index.md` — Catalog title (all directories)

Every `index.md` **must** use:

```markdown
# Catalog : {path-readable-for-human}
```

`{path-readable-for-human}` is derived from the **directory** containing `index.md`, starting at the **layer root** (`1-product-documentation/`, `2-epics/`, `3-implementation/`, `4-sprints/`, `5-governance/`).

**`humanizePath` algorithm:**

1. Split the directory path into segments (forward slashes).
2. **First segment only:** remove the layer index prefix `^[0-9]+-` (e.g. `1-product-documentation` → `product-documentation`; `5-governance` → `governance`). Deeper segments **keep** numeric prefixes (e.g. `L4-critical-zones`).
3. **Each segment:** split on `-` and `_`; apply strict Title Case per token; join tokens with a single space within the segment.
4. **Join segments** with ` - ` (space-hyphen-space).

Adopter repos may use `-` or `_` in custom folder names; humanize **must** accept both separators. The template canonical folder name is `doubts-and-decisions/`.

**Default body profile:** File catalog table immediately after the H1 (blank line, then `| File name | Description |` per §2 table contract). **No** `## File Catalog` heading — the Catalog H1 replaces it.

#### `decision-matrix.md` — Dashboard title (owning block only)

`decision-matrix.md` exists **only** under `{block}/doubts-and-decisions/`. Every file **must** use:

```markdown
# Dashboard : {path-readable-for-human}
```

`{path-readable-for-human}` uses the **owning block** path: the directory containing `decision-matrix.md`, **minus** the trailing `doubts-and-decisions` (or legacy `doubts-and-resolutions` / `doubts_and_resolutions`) segment, humanized with the same algorithm.

**Example:**

```text
1-product-documentation/use-cases/doubts-and-decisions/decision-matrix.md
  → # Dashboard : Product Documentation - Use Cases
```

The Dashboard title identifies the **Decision Id index** for the owning block (§2.1). Matrix structure (`## {element-id}` sections, `Decision Id` column) remains normative in §2.1 — the H1 does not replace that body contract.

### §2.3 `doubts-and-decisions/README.md` profiles

Two profiles are allowed. Operational how-to **must not** appear without `## Decision matrix` and §2.1 propagation rules (anti-pattern: file-move workflow only).

| Profile | Applies to | Required sections | Forbidden |
|---------|------------|-------------------|-----------|
| **Minimal** | Layer 1 **sub-blocks** only: `1-product-documentation/{sub}/doubts-and-decisions/README.md` where `{sub}` is any first-level folder under Layer 1 **except** `doubts-and-decisions` (includes nested sub-blocks such as `logical-domain/business-rules/`) | `## Navigation`, `## Decision matrix`, §2.1 `On solve` + `Forbidden` lines | `## How to manage Doubts`, `## Folders`, `## Status` |
| **Enriched** | `1-product-documentation/doubts-and-decisions/README.md` and layers **2–5** `doubts-and-decisions/README.md` | `## Status` or equivalent, `## Folders`, `## How to manage Doubts`, `## Decision matrix`, §2.1 `On solve` + `Forbidden` | `## How to manage` without `## Decision matrix` |

**Minimal profile — mechanical steps:** Do not duplicate long how-to text. After `## Navigation`, state that open/solve file moves and dashboard rows are defined in the **footer** of [index.md](index.md) in the same folder. Normative closure remains §2.1 + matrix.

**Enriched profile:** Full mechanical how-to may live in the README. The doubts issue catalog footer (§2.4) stays identical across all blocks for agents that load only `index.md`.

**Canonical `Forbidden` line (copy verbatim into every `doubts-and-decisions/README.md`):**

```markdown
**Forbidden:** `See D-XXX` to expand doubt context. Supersede via `**Superseded by:** {block}/D-YYY` and `Matrix impact` status updates; archive to `superseded/` when fully superseded (same session).
```

### §2.4 Doubts issue catalog body (`doubts-and-decisions/index.md`)

Beyond the Catalog H1 (§2.2), every `doubts-and-decisions/index.md` **must** include:

| Section | Contract |
|---------|----------|
| `## Open Issues` | Table header: `\| ID \| Title \| Priority \| Date Created \|` |
| `## Solved Issues` | Table header: `\| ID \| Title \| Resolution Date \|` |
| Footer | Static canonical text below (identical in every doubts issue catalog; not duplicated in COD governance prose) |

**Canonical footer (copy verbatim into every doubts issue catalog):**

```markdown
---
*Instructions: To add a new doubt, create a file in open/ named doubt-XXX.md and append a row to the Open Issues table.*
*Once a doubt is solved, move the file to solved/ and move the row to the Solved Issues table.*
*When every row in ## Matrix impact is Superseded by … and no decision-matrix cell for those rows points at this doubt, move solved/doubt-XXX.md to superseded/ and remove its Solved Issues row in the same session as the last supersede (no Superseded Issues table).*
```

Mechanical how-to belongs in this footer (dynamic artifact, point of use), not in `5-governance/`. Structural sync rules remain in §2 (`doubts-and-decisions/` bullet) and closure propagation in §2.1.

### §2.1 Intra-layer self-containment and decision traceability

**Problem addressed:** Normative domain knowledge must not fragment across `solved/` doubts, use cases, and entity stubs within the same layer. Layer isolation (outer → inner) does **not** justify scattering implementable rules inside Layer 1.

#### SSOT normative artifacts

Files that define **implementable** domain behavior (`logical-domain/entities/{entity}/README.md`, `logic.md`, `business-rules/**/BR-*.md`, `use-cases/UC-*/README.md`, and equivalent normative paths) **must**:

- Contain the full rule text required to implement or review behavior **without** opening doubt files.
- **Must not** reference doubt IDs for normative delegation (patterns such as `See D-`, `See doubt-`, or equivalent).
- **Must not** use pointer-only stubs as sole content.

**Interpretation boundary (Permitido vs Prohibido):**

- **Prohibido:** `See D-XXX` (or equivalent) when used to move normative behavior out of SSOT artifacts into doubt records.
- **Permitido:** contextual references across blocks in the same layer (for example `logical-domain` <-> `use-cases`) when normative behavior remains self-contained in the current SSOT artifact.
- **Permitido (and required when applicable):** cross-block decision traceability in `decision-matrix.md` using qualified ownership links (`{block}/D-XXX`).

Decision history is **not** normative. It is indexed separately (see **Decision matrix** below).

#### Decision matrix (`decision-matrix.md`)

Every fractal block that owns `doubts-and-decisions/` **must** include `decision-matrix.md` alongside `index.md`.

**`D-XXX` (Decision Id):** Logical identifier for a **Doubt & Decision** record. Physical file: `{phase}/doubt-XXX.md` where `{phase}` is `open/`, `solved/`, or `superseded/`. The letter **D** denotes **Doubt & Decision**, not a separate numeric scheme (`DEC-XXX` is **forbidden**). Matrix cells display bare `D-XXX` in the owning block or qualified `{block}/D-XXX` cross-block. One Decision Id may index **multiple** matrix rows (same id, different `(element, event)`).

**`Record` (term):** A Doubt & Decision file (`doubt-XXX.md`) documenting problem context, debate, and closed decision. **`open/`** — debate in progress. **`solved/`** — operational record. **`superseded/`** — forensic only.

| Rule | Requirement |
|------|-------------|
| **Scope** | The matrix lists **only elements owned by this block** (e.g. a `use-cases/.../doubts-and-decisions/decision-matrix.md` contains only `## UC-XX` sections; a `business-rules/...` matrix contains only `## BR-XX` sections). |
| **Structure** | One `## {element-id}` heading per indexed element. Under each heading, a table with columns `Event (brief)` and `Decision Id`. |
| **Uniqueness** | At most **one** Decision Id per `(element, event)` pair within a block. |
| **Role** | Operational **SSOT of Decision Id** per `(block, element, event)`. It does **not** replace SSOT normative files (entities, BR, UC). The matrix lists **current** ids only — not `open/` debate or `superseded/` records. |
| **Historical chain** | Supersede and merge chains are **not** stored in the matrix. They live in `doubts-and-decisions/history/` (append-only, brief). |
| **Decision Id scope** | Each `doubts-and-decisions/` block has its **own** `D-001`, `D-002`, … sequence. IDs may repeat across blocks. Global identity uses the **block-qualified** form `{block}/D-XXX` (e.g. `use-cases/D-002`). |
| **Owning block** | The fractal block where the record file lives under `{block}/doubts-and-decisions/solved/` (e.g. a record in `use-cases/.../solved/doubt-008.md` has owning block `use-cases`). Foreign `decision-matrix.md` cells **must** use qualified links `[owning-block/D-XXX](…)` — never bare `D-XXX` when the record is elsewhere. |

**`Decision Id` cell format:**

| Case | Required cell value | Record resolution |
|------|---------------------|-----------------|
| **Local** (record in this block) | `D-XXX` (bare ID) | `{current-block}/doubts-and-decisions/solved/doubt-XXX.md` |
| **Cross-block** (record in another block) | Markdown link: display text `{block}/D-XXX`, target = owning block's `solved/doubt-XXX.md` | Follow link to the owning block's record |
| **Forbidden** | Bare `D-XXX` in block B when the record file lives only under another block's `solved/` | — |

**Matrix template — local (per element section):**

```markdown
## UC-02.03

| Event (brief) | Decision Id |
|---------------|-------------|
| Sparse persist on materialized Edit | D-008 |
```

**Matrix template — cross-block (element owned by this block, record in another block):**

```markdown
## project

| Event (brief) | Decision Id |
|---------------|-------------|
| Edge cases on persist | [use-cases/D-002](../../use-cases/doubts-and-decisions/solved/doubt-002.md) |
```

**Agent resolution (matrix cell → record path):**

```text
Read Decision Id cell in current block's decision-matrix.md
  IF cell matches bare D-XXX
    → {current-block}/doubts-and-decisions/solved/doubt-XXX.md (must exist)
  IF cell is Markdown link with display {block}/D-XXX
    → {block}/doubts-and-decisions/solved/doubt-XXX.md (must exist)
  IF bare D-XXX in block B but record missing locally → KO / escalate (use qualified link)
```

#### Matrix impact (`## Matrix impact` in solved record)

Operational map of **which matrix rows** this doubt touches — used for closure and supersede propagation without scanning the repository.

| Artifact | Role |
|----------|------|
| `decision-matrix.md` | **Decision Id** pointer per `(block, element, event)` — links only to `solved/` records |
| `## Matrix impact` | **Operational** index for closures and supersede updates |
| `solved/` | Records with operational value (may have partial supersede rows still `Effective`) |
| `superseded/` | Fully superseded records — forensic only |
| `doubts-and-decisions/history/` | **Forensic** brief log — agents must not traverse without explicit human instruction |

**When required:** Every solved doubt that updates at least one row in any `decision-matrix.md` (local or cross-block) **must** include `## Matrix impact` in the same work session.

**Structure:**

```markdown
## Matrix impact

| Block | Element | Event (brief) | Matrix | Status |
|-------|---------|---------------|--------|--------|
| use-cases | UC-02 | Sparse persist | [decision-matrix.md](../decision-matrix.md) | Effective |
| logical-domain | project | Edge cases on persist | [decision-matrix.md](../../logical-domain/doubts-and-decisions/decision-matrix.md) | Effective |
```

| Column | Rule |
|--------|------|
| `Block` | Fractal block slug (e.g. `use-cases`, `logical-domain`) — same slug used in `{block}/D-XXX` |
| `Element` | Element ID in that block's matrix (`UC-XX`, `project`, `BR-XX`, …) |
| `Event (brief)` | Must match the `Event (brief)` row in the linked matrix |
| `Matrix` | Link to the owning block's `decision-matrix.md` |
| `Status` | `Effective` while this Decision Id is indexed for that row; `Superseded by {block}/D-YYY` when replaced |

**Dashboard rule:** Cross-block impact does **not** add rows to foreign blocks' `index.md` dashboards. Traceability is via matrix + `Matrix impact` only.

#### Doubt closure propagation contract

A doubt is **not fully closed** until all of the following complete in the **same work session**:

| Step | Action |
|------|--------|
| 1 | Record resolution and debate log in `solved/doubt-XXX.md` (historical record). |
| 2 | **Propagate** each normative decision to SSOT artifacts (entities, business-rules, use-cases as applicable). |
| 3 | Add a **`## Propagated to`** section in the solved doubt (SSOT paths only — no duplicate normative tables). Navigational index for humans/agents; **not** a machine-verifiable propagation contract (closure gates: SSOT content, `Matrix impact`, and `decision-matrix.md`). |
| 4 | Add **`## Matrix impact`** when any `decision-matrix.md` row is created or updated (see **Matrix impact** above). |
| 5 | Update `decision-matrix.md` in every affected block (effective row per `(element, event)` — use qualified links in non-owning blocks). |
| 6 | Append a brief entry to `doubts-and-decisions/history/` when a doubt supersedes or merges another (**mandatory** on supersede). |
| 7 | Update the **owning block's** `doubts-and-decisions/index.md` dashboard only (no foreign-dashboard rows). |

**Closure criterion:** No implementer should need to read **only** `solved/doubt-XXX.md` to build domain behavior.

#### Cross-block propagation

When closing a doubt in block A affects an element owned by block B (e.g. a UC doubt impacts an entity):

1. **Do not close** until block B is consistent (SSOT + matrix).
2. Consult block B's `decision-matrix.md` for the affected `## {element}` section only.
3. In block B's matrix, set **Decision Id** to a **qualified link** `[A/D-XXX](…/A/…/solved/doubt-XXX.md)` — never bare `D-XXX`.
4. Propagate normative text to block B SSOT artifacts.
5. List block B rows in block A's `## Matrix impact` with `Status: Effective`.
6. **Do not** add a dashboard row for this doubt in block B's `index.md`.

Debate origin (block A vs B) does **not** change the rule — only the effective solution matters.

#### Supersede (complete and partial)

When doubt **D-YYY** supersedes **D-XXX**, **D-XXX** loses operational value (forensic review via `history/` only, with explicit human authorization).

| Mode | When | Matrix / impact rules |
|------|------|------------------------|
| **Complete** (default) | D-YYY covers the same scope as D-XXX or the PO explicitly expands D-YYY to absorb the full `Matrix impact` of D-XXX | Every `Effective` row in D-XXX's `Matrix impact` becomes `Superseded by {block}/D-YYY`; all such rows appear as `Effective` in D-YYY's `Matrix impact`; no matrix cell may remain pointing at D-XXX for those events |
| **Partial** | D-YYY is too narrow to absorb unrelated context from D-XXX without polluting the record | Only touched rows in D-XXX's `Matrix impact` become `Superseded by {block}/D-YYY`; remaining rows stay `Effective` on D-XXX; `history/` must state `partial` and one-line PO rationale |

**On closing D-YYY that supersedes D-XXX (same session, blocking):**

1. Append **`history/`** entry: `{block}/D-XXX superseded (complete|partial) by {block}/D-YYY`.
2. Add at the top of D-XXX's record: `**Superseded by:** {block}/D-YYY` (qualified ID only — no `See D-` chains).
3. Update D-XXX's `## Matrix impact`: superseded rows → `Status: Superseded by {block}/D-YYY`.
4. D-YYY's `## Matrix impact` **must include** every row marked `Superseded by {block}/D-YYY` in D-XXX's `Matrix impact` (as `Effective`).
5. Update all affected `decision-matrix.md` cells to point at D-YYY (qualified link in foreign blocks).
6. D-YYY record is **self-contained** for its scope; a generic note that prior behavior was superseded is allowed — **without** referencing origin record paths for context expansion.
7. If D-XXX has **no** remaining `Effective` rows in `Matrix impact`, run **Archive to `superseded/`** in the **same session** (effective inverse check + move file + remove Solved dashboard row).

**Supersede gate (KO):** Any row in D-XXX's `Matrix impact` with `Superseded by {block}/D-YYY` that is missing from D-YYY's `Matrix impact` as `Effective` blocks commit.

#### Archive to `superseded/` (same session as last supersede)

When the **last** `Effective` row in D-XXX's `## Matrix impact` becomes `Superseded by …` in the **same work session**, archive D-XXX immediately after the superseding closure completes.

**Effective inverse check:** For each row in D-XXX's `## Matrix impact`, read that block's `decision-matrix.md` at the matching `(element, event)`. The **Decision Id** cell **must resolve to an id other than D-XXX**. This verifies the operational matrix pointers only — it confirms no matrix cell still indexes D-XXX. It is **not** a full supersede-coherence or `history/` traceability audit (see [check-solve-doubt.md](../skills/check-solve-doubt.md) checks 11–12).

| Step | Action |
|------|--------|
| 1 | Verify every row in D-XXX's `## Matrix impact` has `Status: Superseded by {block}/D-…` (no `Effective` rows remain). |
| 2 | Run the **effective inverse check** (per row) as defined above. |
| 3 | Move `solved/doubt-XXX.md` → `superseded/doubt-XXX.md`. |
| 4 | Remove D-XXX from **Solved Issues** in the owning block's `index.md` (no new dashboard section). |
| 5 | Append brief note to `history/` if not already recorded for this archival step. |

**Archive gate (KO):** If any `decision-matrix.md` cell for a row in D-XXX's `Matrix impact` still resolves to D-XXX, archive **must not** proceed.

**Qualified links:** Operational **Decision Id** cells and cross-block links **must** target records under `solved/` only — never `superseded/`.

**Agent rule:** Do **not** load `superseded/` unless the human gives an explicit forensic instruction (same policy as `history/`).

#### Doubt self-containment and inter-doubt rules

| Allowed | Forbidden |
|---------|-----------|
| `**Superseded by:** {block}/D-YYY` at the top of a superseded record | `See D-XXX` to expand debate context |
| `Status: Superseded by {block}/D-YYY` in `## Matrix impact` | Bare `D-XXX` in a foreign block's matrix when record is elsewhere |
| Full problem, options, decision, and impact inside each self-contained doubt file | Chains of doubt-to-doubt reading as substitute for self-contained record |

**Zero-tolerance rule for doubt records (`open/`, `solved/`, `superseded/`):**

- Any `D-XXX` reference is **forbidden** outside `## Matrix impact` and the top-level `**Superseded by:** {block}/D-YYY` header.
- There is **no** "contextual reference" exception for doubt bodies (problem, options, decision, impact, resolution, scope, rationale).
- If understanding or implementing a doubt requires reading another doubt record, the record is non-compliant and must be rewritten to be self-contained.
| Generic note in D-YYY that prior decisions were superseded (no origin links) | Normative `See D-` delegation in SSOT artifacts |

#### Agent navigation: matrix vs history

| Action | Default during debate / closure |
|--------|----------------------------------|
| Read `decision-matrix.md` | **Allowed** — only `## {element}` sections matching elements in current debate scope |
| Read other matrix sections | **Forbidden** unless scope expands |
| Traverse `doubts-and-decisions/history/` week files to reconstruct traceability | **Forbidden** unless the human gives an **explicit** instruction (e.g. "investigate historical traceability for this event") |
| Load files under `superseded/` | **Forbidden** unless the human gives an **explicit** forensic instruction |

**Reading hierarchy:**

```text
Implement / explain domain     →  entities/ + business-rules/ + normative UC sections
Actor flow and invocation      →  use-cases/UC-XX
Which doubt explains a decision →  decision-matrix.md (scoped by element; resolve qualified links)
Operational closure / supersede map →  ## Matrix impact in solved record with effective matrix rows
Fully superseded record (forensic)   →  superseded/ — human-authorized only
Why a decision evolved (forensic)    →  doubts history — human-authorized only
```

Each self-contained doubt file in `open/` or `solved/` **must** be readable on its own for the debate it records. Records in `superseded/` retain forensic value only.

## 3. Structured Hierarchy (Inside-Out)

### Layer 1: Agnostic Documentation (The Core)

- **Nature:** The conceptual heart: logical domain model (`logical-domain/`), use cases, conceptual C4 diagrams, and ubiquitous language.

- **Rule:** Most protected layer. **100% timeless** and decoupled from technology, functional expansion, or management. **Does not know** any other system layer.

- **Prohibited:** Mentioning specific frameworks, libraries, databases (e.g., NestJS, PostgreSQL), development epics, or live code paths.

- **Path:** `1-product-documentation/`

### Layer 2: Epics (Business Functional Layer)

- **Nature:** Functional subdivision of the project scope.

- **Rule:** An epic (tagged as `T-XXX`) **maps** to a use case or sub-UC in Layer 1 to set practical boundaries. It looks **inward** to reference the core.

- **Prohibited:** Mentions of the tech stack (Layer 3), code paths, or temporal project organization (Layer 4).

- **Path:** `2-epics/`

### Layer 3: Implementation (Tech Layer)

- **Nature:** Real source code, adopted tech stack, bootstrap, and technical design guides.

- **Rule:** Acts as an **adapter** wrapping inner layers. Infrastructure-level code and C4 diagrams look **inward** to fulfill logical and business contracts.

- **Path:** `3-implementation/`

### Layer 4: Sprints (Temporal Management)

- **Nature:** Orchestration, project development timeline, and academic milestone tracking.

- **Rule:** Organizes tasks and delivery phases. Looks **inward** to plan which epics (Layer 2) are executed and which code deliverables (Layer 3) are completed.

- **Path:** `4-sprints/`

### Layer 5: Cross-cutting Policies (The Outer Crust)

- **Nature:** Governance, global standards, AI style guides (`.cursorrules`), and project directives.

- **Rule:** The outermost layer: wraps and protects the entire system. Defines absolute norms governing architecture and AI behavior across all layers.

- **Path:** `5-governance/`

### 3.1 Rule scope taxonomy (mandatory)

Rules must be classified by **nature**, not by document format:

| Rule class | Canonical layer/path | Purpose | Forbidden placement |
|------------|----------------------|---------|---------------------|
| **Domain rules** | `1-product-documentation/logical-domain/business-rules/` | Timeless business truth and invariants | Layer 3 runtime folders, Layer 5 policy folders |
| **Process/Application rules** | `1-product-documentation/use-cases/` | Flow orchestration and use-case behavior | `logical-domain/business-rules/` when rule is orchestration-only |
| **Runtime policies** | `3-implementation/` | Technology-specific operational policy materialization | Layer 1 artifacts |
| **Platform policies** | `5-governance/platform-policies/` | Cross-cutting governance policy, mapped to runtime enforcement | Layer 1 artifacts |

**Mandatory flow:** Platform Policies (Layer 5) → Runtime Policies (Layer 3).

- Every effective platform policy must map to at least one runtime policy.
- Reverse normative dependency (Layer 3 → Layer 5) is forbidden.
- `index.md` catalog tables remain same-level catalogs only; cross-layer mapping belongs in dedicated policy mapping artifacts (for example `runtime.time.md`).
- **PP-01.01** (`platform-policies/01-governance-directionality/PP-01.01-platform-to-runtime-directionality.md`) is the mandatory COD policy defining this directionality contract.

## 4. Reference Directionality and Traceability

**Source of truth:** this section defines the layer dependency matrix and tech-agnosticism rules. [pre-commit-validation-rules.md](pre-commit-validation-rules.md) and any agent audit **must** validate COD against this section — do not duplicate the table elsewhere.

### Layer dependency matrix (inward-only)

Inner layers (Layer N) **must not** have knowledge, references, or dependencies on outer layers (Layer N+1, N+2, etc.). References flow **exclusively** from outside to inside (higher layer numbers → lower).

| Layer | Path | May reference only |
|-------|------|-------------------|
| **1** | `1-product-documentation/` | *(none — fully isolated core)* |
| **2** | `2-epics/` | Layer 1 |
| **3** | `3-implementation/` | Layers 2 and 1 |
| **4** | `4-sprints/` | Layers 3, 2, and 1 |
| **5** | `5-governance/` | Layers 4, 3, 2, and 1 |

- **Layer 1** must be entirely agnostic of Layers 2, 3, 4, and 5.
- **Any upward reference** (e.g., Layer 1 → Layer 2, Layer 3 → Layer 4) is a **grave architectural violation**.

### No stack leakage

- Only **Layer 3** (`3-implementation/`) may mention specific technology stacks, frameworks, libraries, databases, or infrastructure details.
- **Strictly forbidden** in Layers 1 and 2.
- **Also forbidden** in Layers 4 and 5 — temporal and governance documents must not embed Layers 3/4 implementation jargon or stack-specific paths.

### Traceability rules

- **Invariable unidirectional flow:** references, hyperlinks, and mentions are made **exclusively** from outside to inside (higher numbers to lower numbers).
- **Clear boundaries:** any attempt by an inner layer to reference or "link-delegate" to an outer layer is considered a **grave architectural violation**.
- **Platform/runtime policy mapping:** Layer 5 policy groups must maintain a runtime mapping matrix (`runtime.time.md`) linking each effective `PP-XX.YY` to Layer 3 `RP-XXX` artifacts.