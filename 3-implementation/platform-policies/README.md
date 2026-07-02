# Platform Policies (Layer 3)

This folder stores product platform policies (`PP-XX.YY`) at Layer 3 root.

## Rules

- Policy files follow `PP-XX.YY-{slug}.md` grouped by policy type code (`XX`).
- Platform policy definitions are technology-agnostic and must not embed stack-specific implementation details.
- Runtime policies (`RP-XXX`) remain in technology folders and must map to platform policies through local `rp-to-pp-matrix.md` files.
- Mapping direction is mandatory: `RP-XXX` -> `PP-XX.YY`.

## Navigation

- [index.md](index.md) - Live file catalog for this folder.
- [01-governance-directionality/](01-governance-directionality/) - Directionality contract for RP to PP traceability.
