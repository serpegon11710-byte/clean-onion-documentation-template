# Product Documentation

This folder manages the **product documentation** layer.

# Product Documentation (Layer 1)

This layer contains the authoritative definition of product functionality and business logic.

## Core Domain Model (`logical-domain/`)

The business model — entities, business rules, and domain diagrams — lives under [logical-domain/](logical-domain/). It is the SSOT for what the business *is* and *how it behaves*, agnostic of technology.

## Rule Scope In Layer 1

- **Domain rules** belong only to `logical-domain/business-rules/`.
- **Process rules** (application-flow orchestration) belong to `use-cases/`.
- Process rules are still **Layer 1** artifacts, but they are not domain invariants.
- No `business-rules/` sibling folder is allowed at `1-product-documentation/` root.

## Core Integration Contract

All consumers within this layer (Use Cases, Functional Modules) must comply with the
[Business Rule Integration Contract](./business-rule-integration-contract.md).
This contract enforces SSOT and bidirectional traceability across all functional entities.

## C4 Diagrams (`diagrams-c4/`)

Technology-agnostic C4 architecture diagrams and Critical Zones (ZC) live under [diagrams-c4/](diagrams-c4/). Each C4 level has its own subfolder (`L1-context`, `L2-containers`, `L3-components`, `L4-critical-zones`).

## Traceability Architecture

1. **Business Rules Repository (`logical-domain/business-rules/`):**
  - Every business rule is an atomic element with one dedicated folder: `BR-XX.YY-{short-description}/`.
  - The rule definition lives in `README.md`; consumer/dependency traceability lives in `reference-matrix.md`.

2. **Use Case Repository (`use-cases/`):**
  - Every `UC-XX/README.md` must reference consumed `BR-XX.YY` IDs in its dependency section.
  - If a rule changes, all use cases listed in that rule's `reference-matrix.md` must be reviewed.

## Navigation

- [index.md](index.md) — Live directory of all files in this layer.
- [logical-domain/](logical-domain/) — Core domain model: entities, business rules, and domain diagrams.
- [use-cases/](use-cases/) — Primary use cases with Mermaid diagrams.
- [diagrams-c4/](diagrams-c4/) — C4 conceptual diagrams.
- [doubts-and-decisions/](doubts-and-decisions/) — Atomic management of open and closed doubts.
