# Business Rule Integration Contract

This contract defines how `use-cases/` and `logical-domain/business-rules/` interact to preserve a single source of truth and bidirectional traceability in Layer 1.

## Mandatory Rules

1. **Single source of truth:** Business logic is authored only in `logical-domain/business-rules/` using one folder per rule.
2. **Reference-only consumption:** Use cases must reference business rule IDs and must not duplicate rule definitions.
3. **Bidirectional traceability:**  
   - Each business rule stores outgoing and incoming references in `reference-matrix.md`.
   - Each use case lists all consumed business rules in a dependency table.
4. **Impact review:** Any change in a business rule triggers review of all linked use cases.
5. **Layer isolation:** This contract is internal to `1-product-documentation/` and must not depend on outer layers.
6. **Rule scope boundary:** Domain rules stay in `logical-domain/business-rules/`; process rules stay in `use-cases/` as Layer 1 orchestration artifacts.
7. **Link target policy:** Cross-document links to rules must point to the rule `README.md` file, never to the rule folder path.

## Business Rule Folder Structure (mandatory)

Each rule uses one dedicated folder:

```text
logical-domain/business-rules/{category}/BR-XX.YY-{short-description}/
├── README.md
└── reference-matrix.md
```

- `README.md`: normative definition of the rule (single source of truth).
- `reference-matrix.md`: references only (consumers, dependencies, and related artifacts).
- This split prevents loading reference noise when an agent only needs the rule definition.
- This structure is a concrete application of the general COD normative element pattern.

## Rule Type Clarification

- **Domain rule (`BR-XX.YY`)**: timeless business invariant or policy.
- **Process rule**: use-case flow constraint, sequencing, or orchestration behavior.
- Process rules may reference domain rules, but must not be stored as `BR-XX.YY` files.

## Required Traceability Format

### In business rule `README.md`

- **Definition**
- **Scope** (what the rule governs and what it excludes)

### In business rule `reference-matrix.md`

- **Referenced by** (`UC-XX` / `UC-XX.YY`)
- **Dependencies** (other `BR-XX.YY` if strictly needed)
- **Context links** (optional links to related Layer 1 documents)

### In use case files

| Rule ID | Description | Impact |
|---|---|---|
| `BR-XX.YY` | Rule description | `Critical` / `Minor` |

