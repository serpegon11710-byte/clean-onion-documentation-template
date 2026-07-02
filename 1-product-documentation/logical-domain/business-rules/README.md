# Business Rules Standard

This standard governs the definition, grouping, and traceability of business logic within `logical-domain/`.

## 1. Semantic Grouping

All business rules must be organized into semantic folders based on their domain context (for example, `BR-01-taxation`, `BR-02-identity`).

- Each semantic folder must contain a `README.md` explaining the domain context and business foundation.

## 2. Rule Coding & Atomicity

Rules are defined atomically, one folder per rule:

- **Rule folder:** `BR-XX.YY-{short-description}/`
- **Mandatory files inside each rule folder:**
  - `README.md` -> authoritative business rule definition.
  - `reference-matrix.md` -> bidirectional references only.
- **Link policy:** any link to a business rule must target `README.md`, never the folder path.

## 3. Governance Constraints

- **Atomic definition:** Each rule is defined once and reused by reference.
- **Single source of truth:** If a rule changes, all consuming use cases must be reviewed.
- **Context minimization:** Keep references out of `README.md`; place them in `reference-matrix.md`.

## 4. Implementation Traceability

Every `UC-XX/README.md` must include a dependency section:

| Rule ID | Description | Impact |
|---|---|---|
| `BR-XX.YY` | Rule description | `Critical` / `Minor` |

## Integration Contract

This folder follows [business-rule-integration-contract.md](../../business-rule-integration-contract.md).

