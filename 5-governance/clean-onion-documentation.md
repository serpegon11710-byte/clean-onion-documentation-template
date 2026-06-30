# Clean Onion Documentation (COD) Standard

**Last updated:** 2026-06-30

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
└── 📁 doubts_and_resolutions/   <-- Atomic management of doubts and FAQs.
    ├── 📄 README.md             <-- Strict protocol for opening/closing issues.
    ├── 📄 index.md              <-- Dashboard (Open vs. Solved doubts).
    ├── 📄 decision-matrix.md    <-- Effective decision index per block element type.
    ├── 📁 open/                 <-- Active tracking (work in progress).
    │   ├── 📄 doubt-001.md      <-- Isolated file exclusively for open doubt 001.
    │   └── 📄 doubt-002.md
    ├── 📁 solved/               <-- Closed records with operational value.
    │   └── 📄 doubt-000.md      <-- Resolved doubt moved from open/.
    └── 📁 superseded/           <-- Fully superseded records (forensic only).
        └── 📄 doubt-000.md      <-- Archived from solved/ when no longer effective.

```
### Fractal Component Specification:

- **`README.md` (Static):** Defines the folder scope and fixed AI behavioral rules. Minimizes token usage as it remains constant.

- **`index.md` (Dynamic):** File catalog for the **current directory only**. The table lists exclusively `.md` files at the same path level (excluding `index.md` itself). Subdirectories are **not** listed — each child folder maintains its own `index.md`.
- **`index.md` table contract (Mandatory):** Every `index.md` must include the file catalog table with header `| File name | Description |`. The table is **required even when empty** (header + separator, zero data rows). Placeholder or dummy rows are **forbidden**. An empty table renders correctly in GitHub, VS Code, and CommonMark parsers.
- **`index.md` catalog bijection (Mandatory):** Each catalog row **must** name a `.md` file **tracked in git** at the same path level. Every tracked same-level `.md` except `index.md` **must** appear exactly once. Rows for missing, gitignored, or deleted files are **forbidden**. Subdirectory paths (e.g. `logical-domain/`) are **forbidden** in the file catalog table. On add/remove/rename of a same-level `.md`, update `index.md` in the **same commit**.

- **`history/`:** Records modifications chronologically in fragmented files. The past is frozen and does not contaminate the active chat context.

- **`doubts_and_resolutions/`:** Isolates issues in atomic format (one file per doubt). Open doubts live in **`open/`**; closed records with operational value in **`solved/`**; fully superseded records in **`superseded/`** (forensic only — agents do not load without explicit human instruction). The **`index.md`** dashboard tracks **`open/`** and **`solved/`** only (`Open Issues` / `Solved Issues` — **no** `Superseded Issues` table). Files must not move between `open/`, `solved/`, and `superseded/` without the matching dashboard update (remove Solved row when archiving to `superseded/`).

- **`decision-matrix.md`:** Per-block index of effective doubts by element and event. Updated on every normative doubt closure. See §2.1.

### §2.2 `index.md` heading archetypes

Every `index.md` belongs to **one** archetype. The H1 must match the archetype; do not use `Dashboard` for block or history catalogs.

| Archetype | Path pattern | Canonical H1 | Body sections |
|-----------|--------------|--------------|---------------|
| **Block catalog** | `{block}/index.md` (not under `history/` or `doubts_and_resolutions/`) | `# {Scope}` | `## File Catalog` only |
| **History catalog** | `{block}/history/index.md` | `# History - {Scope}` | `## File Catalog` only |
| **Doubt dashboard** | `{block}/doubts_and_resolutions/index.md` | `# Doubt Dashboard - {Scope}` | `## File Catalog`, `## Open Issues`, `## Solved Issues`, canonical footer (§2.4) |

`{Scope}` is the human-readable block name (e.g. `Use Cases`, `5 Governance`, `L4 Critical Zones`). Same scope label may appear in more than one path; disambiguation is by directory path, not H1 alone.

### §2.3 `doubts_and_resolutions/README.md` profiles

Two profiles are allowed. Operational how-to **must not** appear without `## Decision matrix` and §2.1 propagation rules (anti-pattern: file-move workflow only).

| Profile | Applies to | Required sections | Forbidden |
|---------|------------|-------------------|-----------|
| **Minimal** | Layer 1 **sub-blocks** only: `1-product-documentation/{sub}/doubts_and_resolutions/README.md` where `{sub}` is any first-level folder under Layer 1 **except** `doubts_and_resolutions` (includes nested sub-blocks such as `logical-domain/business-rules/`) | `## Navigation`, `## Decision matrix`, §2.1 `On solve` + `Forbidden` lines | `## How to manage Doubts`, `## Folders`, `## Status` |
| **Enriched** | `1-product-documentation/doubts_and_resolutions/README.md` and layers **2–5** `doubts_and_resolutions/README.md` | `## Status` or equivalent, `## Folders`, `## How to manage Doubts`, `## Decision matrix`, §2.1 `On solve` + `Forbidden` | `## How to manage` without `## Decision matrix` |

**Minimal profile — mechanical steps:** Do not duplicate long how-to text. After `## Navigation`, state that open/solve file moves and dashboard rows are defined in the **footer** of [index.md](index.md) in the same folder. Normative closure remains §2.1 + matrix.

**Enriched profile:** Full mechanical how-to may live in the README. The doubt dashboard footer (§2.4) stays identical across all blocks for agents that load only `index.md`.

**Canonical `Forbidden` line (copy verbatim into every `doubts_and_resolutions/README.md`):**

```markdown
**Forbidden:** `See D-XXX` to expand doubt context. Supersede via `**Superseded by:** {block}/D-YYY` and `Matrix impact` status updates; archive to `superseded/` when fully superseded (same session).
```

### §2.4 Doubt dashboard (`doubts_and_resolutions/index.md`)

Beyond §2.2, every doubt dashboard **must** include:

| Section | Contract |
|---------|----------|
| `## Open Issues` | Table header: `\| ID \| Title \| Priority \| Date Created \|` |
| `## Solved Issues` | Table header: `\| ID \| Title \| Resolution Date \|` |
| Footer | Static canonical text below (identical in every dashboard; not duplicated in COD governance prose) |

**Canonical footer (copy verbatim into every doubt dashboard):**

```markdown
---
*Instructions: To add a new doubt, create a file in open/ named doubt-XXX.md and append a row to the Open Issues table.*
*Once a doubt is solved, move the file to solved/ and move the row to the Solved Issues table.*
*When every row in ## Matrix impact is Superseded by … and no decision-matrix cell for those rows points at this doubt, move solved/doubt-XXX.md to superseded/ and remove its Solved Issues row in the same session as the last supersede (no Superseded Issues table).*
```

Mechanical how-to belongs in this footer (dynamic artifact, point of use), not in `5-governance/`. Structural sync rules remain in §2 (`doubts_and_resolutions/` bullet) and closure propagation in §2.1.

### §2.1 Intra-layer self-containment and decision traceability

**Problem addressed:** Normative domain knowledge must not fragment across `solved/` doubts, use cases, and entity stubs within the same layer. Layer isolation (outer → inner) does **not** justify scattering implementable rules inside Layer 1.

#### SSOT normative artifacts

Files that define **implementable** domain behavior (`logical-domain/entities/{entity}/README.md`, `logic.md`, `business-rules/**/BR-*.md`, `use-cases/UC-*/README.md`, and equivalent normative paths) **must**:

- Contain the full rule text required to implement or review behavior **without** opening doubt files.
- **Must not** reference doubt IDs for normative delegation (patterns such as `See D-`, `See doubt-`, or equivalent).
- **Must not** use pointer-only stubs as sole content.

Decision history is **not** normative. It is indexed separately (see **Decision matrix** below).

#### Decision matrix (`decision-matrix.md`)

Every fractal block that owns `doubts_and_resolutions/` **must** include `decision-matrix.md` alongside `index.md`.

**`Effective` (term):** The doubt indexed in `decision-matrix.md` for a given `(element, event)` — authoritative for that row until superseded. **Not** the same as a doubt in `open/` (debate in progress).

**`Record` (term):** A closed doubt file in `solved/doubt-XXX.md` (or archived in `superseded/`) that documents resolution, debate, and propagation. **Not** used for doubts still in `open/`.

| Rule | Requirement |
|------|-------------|
| **Scope** | The matrix lists **only elements owned by this block** (e.g. a `use-cases/.../doubts_and_resolutions/decision-matrix.md` contains only `## UC-XX` sections; a `business-rules/...` matrix contains only `## BR-XX` sections). |
| **Structure** | One `## {element-id}` heading per indexed element. Under each heading, a table with columns `Event (brief)` and `Effective doubt`. |
| **Uniqueness** | At most **one** effective doubt per `(element, event)` pair within a block. |
| **Role** | Operational **SSOT of effective doubt identity** per `(block, element, event)`. It does **not** replace SSOT normative files (entities, BR, UC). |
| **Historical chain** | Supersede and merge chains are **not** stored in the matrix. They live in `doubts_and_resolutions/history/` (append-only, brief). |
| **Doubt ID scope** | Each `doubts_and_resolutions/` block has its **own** `D-001`, `D-002`, … sequence in its dashboard. IDs may repeat across blocks. Global identity uses the **block-qualified** form `{block}/D-XXX` (e.g. `use-cases/D-002`). |
| **Owning block** | The fractal block where the doubt record file lives under `{block}/doubts_and_resolutions/solved/` (e.g. a record in `use-cases/.../solved/doubt-008.md` has owning block `use-cases`). Foreign `decision-matrix.md` cells **must** use qualified links `[owning-block/D-XXX](…)` — never bare `D-XXX` when the record is elsewhere. |

**`Effective doubt` cell format:**

| Case | Required cell value | Record resolution |
|------|---------------------|-----------------|
| **Local** (record in this block) | `D-XXX` (bare ID) | `{current-block}/doubts_and_resolutions/solved/doubt-XXX.md` |
| **Cross-block** (record in another block) | Markdown link: display text `{block}/D-XXX`, target = owning block's `solved/doubt-XXX.md` | Follow link to the owning block's record |
| **Forbidden** | Bare `D-XXX` in block B when the record file lives only under another block's `solved/` | — |

**Matrix template — local effective (per element section):**

```markdown
## UC-02.03

| Event (brief) | Effective doubt |
|---------------|---------------|
| Sparse persist on materialized Edit | D-008 |
```

**Matrix template — cross-block effective (element owned by this block, record in another block):**

```markdown
## project

| Event (brief) | Effective doubt |
|---------------|---------------|
| Edge cases on persist | [use-cases/D-002](../../use-cases/doubts_and_resolutions/solved/doubt-002.md) |
```

**Agent resolution (matrix cell → record path):**

```text
Read Effective doubt cell in current block's decision-matrix.md
  IF cell matches bare D-XXX
    → {current-block}/doubts_and_resolutions/solved/doubt-XXX.md (must exist)
  IF cell is Markdown link with display {block}/D-XXX
    → {block}/doubts_and_resolutions/solved/doubt-XXX.md (must exist)
  IF bare D-XXX in block B but record missing locally → KO / escalate (use qualified link)
```

#### Matrix impact (`## Matrix impact` in solved record)

Operational map of **which matrix rows** this doubt touches — used for closure and supersede propagation without scanning the repository.

| Artifact | Role |
|----------|------|
| `decision-matrix.md` | **Effective** pointer per `(block, element, event)` — links only to `solved/` records |
| `## Matrix impact` | **Operational** index for closures and supersede updates |
| `solved/` | Records with operational value (may have partial supersede rows still `Effective`) |
| `superseded/` | Fully superseded records — forensic only |
| `doubts_and_resolutions/history/` | **Forensic** brief log — agents must not traverse without explicit human instruction |

**When required:** Every solved doubt that updates at least one row in any `decision-matrix.md` (local or cross-block) **must** include `## Matrix impact` in the same work session.

**Structure:**

```markdown
## Matrix impact

| Block | Element | Event (brief) | Matrix | Status |
|-------|---------|---------------|--------|--------|
| use-cases | UC-02 | Sparse persist | [decision-matrix.md](../decision-matrix.md) | Effective |
| logical-domain | project | Edge cases on persist | [decision-matrix.md](../../logical-domain/doubts_and_resolutions/decision-matrix.md) | Effective |
```

| Column | Rule |
|--------|------|
| `Block` | Fractal block slug (e.g. `use-cases`, `logical-domain`) — same slug used in `{block}/D-XXX` |
| `Element` | Element ID in that block's matrix (`UC-XX`, `project`, `BR-XX`, …) |
| `Event (brief)` | Must match the `Event (brief)` row in the linked matrix |
| `Matrix` | Link to the owning block's `decision-matrix.md` |
| `Status` | `Effective` while this doubt is the effective index for that row; `Superseded by {block}/D-YYY` when replaced |

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
| 6 | Append a brief entry to `doubts_and_resolutions/history/` when a doubt supersedes or merges another (**mandatory** on supersede). |
| 7 | Update the **owning block's** `doubts_and_resolutions/index.md` dashboard only (no foreign-dashboard rows). |

**Closure criterion:** No implementer should need to read **only** `solved/doubt-XXX.md` to build domain behavior.

#### Cross-block propagation

When closing a doubt in block A affects an element owned by block B (e.g. a UC doubt impacts an entity):

1. **Do not close** until block B is consistent (SSOT + matrix).
2. Consult block B's `decision-matrix.md` for the affected `## {element}` section only.
3. In block B's matrix, set `Effective doubt` to a **qualified link** `[A/D-XXX](…/A/…/solved/doubt-XXX.md)` — never bare `D-XXX`.
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

**Effective inverse check:** For each row in D-XXX's `## Matrix impact`, read that block's `decision-matrix.md` at the matching `(element, event)`. The `Effective doubt` cell **must resolve to a doubt other than D-XXX**. This verifies the **effective index** (operational matrix pointers) only — it confirms no matrix cell still treats D-XXX as effective. It is **not** a full supersede-coherence or `history/` traceability audit (see [check-solve-doubt.md](../skills/check-solve-doubt.md) checks 11–12).

| Step | Action |
|------|--------|
| 1 | Verify every row in D-XXX's `## Matrix impact` has `Status: Superseded by {block}/D-…` (no `Effective` rows remain). |
| 2 | Run the **effective inverse check** (per row) as defined above. |
| 3 | Move `solved/doubt-XXX.md` → `superseded/doubt-XXX.md`. |
| 4 | Remove D-XXX from **Solved Issues** in the owning block's `index.md` (no new dashboard section). |
| 5 | Append brief note to `history/` if not already recorded for this archival step. |

**Archive gate (KO):** If any `decision-matrix.md` cell for a row in D-XXX's `Matrix impact` still resolves to D-XXX, archive **must not** proceed.

**Qualified links:** Operational `Effective doubt` cells and cross-block links **must** target records under `solved/` only — never `superseded/`.

**Agent rule:** Do **not** load `superseded/` unless the human gives an explicit forensic instruction (same policy as `history/`).

#### Doubt self-containment and inter-doubt rules

| Allowed | Forbidden |
|---------|-----------|
| `**Superseded by:** {block}/D-YYY` at the top of a superseded record | `See D-XXX` to expand debate context |
| `Status: Superseded by {block}/D-YYY` in `## Matrix impact` | Bare `D-XXX` in a foreign block's matrix when record is elsewhere |
| Full problem, options, decision, and impact inside each self-contained doubt file | Chains of doubt-to-doubt reading as substitute for self-contained record |
| Generic note in D-YYY that prior decisions were superseded (no origin links) | Normative `See D-` delegation in SSOT artifacts |

#### Agent navigation: matrix vs history

| Action | Default during debate / closure |
|--------|----------------------------------|
| Read `decision-matrix.md` | **Allowed** — only `## {element}` sections matching elements in current debate scope |
| Read other matrix sections | **Forbidden** unless scope expands |
| Traverse `doubts_and_resolutions/history/` week files to reconstruct traceability | **Forbidden** unless the human gives an **explicit** instruction (e.g. "investigate historical traceability for this event") |
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