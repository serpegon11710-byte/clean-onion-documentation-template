# Domain Entities

The **WHAT** of the business model: domain objects, their attributes, constraints, and core behavior.

`domain-entities.md` is the catalog and navigation entry point for entities in this block. It does not replace per-entity normative artifacts.

## Per-entity folder structure

```text
entities/{entity}/
├── README.md       # Definition, attributes, and constraints
└── logic.md        # Core domain methods and responsibilities
```

| File | Content |
|------|---------|
| `README.md` | Ubiquitous-language definition, attributes, invariants, and relationships to other entities. |
| `logic.md` | Technology-agnostic description of domain operations the entity owns or participates in. |

## Rules

- One folder per aggregate or core entity; name with ubiquitous language (kebab-case).
- No framework types, persistence mapping, or API shapes.
- Register each entity folder in [index.md](index.md).
- Keep [domain-entities.md](domain-entities.md) aligned with available entities and links.
- Keep [../diagrams/class-diagram.mmd](../diagrams/class-diagram.mmd) aligned with entity definitions.

## Navigation

- [index.md](index.md) — Live directory of entities.
- [domain-entities.md](domain-entities.md) — Consolidated entity catalog and links to entity logic.
- [../diagrams/class-diagram.mmd](../diagrams/class-diagram.mmd) — Domain class diagram (Mermaid).
- [../README.md](../README.md) — Logical domain standard.
