# Product Overlay

This file defines product-owned operational overlays that extend the repository template without redefining the template's core COD rules.

## Purpose

Use this file as the product-specific entry point for custom governance, custom skills, and agent-facing rules that are not part of the template SSOT.

## Loading Order

Read this file after [AGENTS.md](AGENTS.md) when the repository includes product-specific overlays.

## Scope

- Product-specific pre-commit rules.
- Product-specific command or workflow definitions.
- Product-specific agent instructions that remain compatible with the repository COD.

## Ownership

The product team owns this file and keeps it aligned with any product overlays under `5-governance/customized/` and `skills/customized/`.
