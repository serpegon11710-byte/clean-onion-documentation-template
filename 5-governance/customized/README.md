# Customized Governance Overlay

This folder is a product-owned placeholder for custom governance rules.

## Purpose

Define product-specific pre-commit rules, audit exceptions, or operational notes here without changing the template COD documents.

## Exemption

This folder is explicitly exempt from the layer-wide inheritance clause declared in `5-governance/README.md`. It remains product-owned and must stay COD-compatible.

## Constraints

- Do not contradict [clean-onion-documentation.md](../clean-onion-documentation.md).
- Do not introduce stack leakage into inner layers.
- If a custom rule conflicts with COD, document the conflict and resolve it at product level before execution.

## Pre-commit Use

When a product repository adopts custom pre-commit behavior, its auditor or wrapper should consult this overlay in addition to the standard governance files.

## Ownership

The product team maintains this overlay.
