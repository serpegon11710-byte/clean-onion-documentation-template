# Sprints

This folder manages the **sprints** layer.

## Sprint Subtasks Governance

When a sprint is decomposed into subtasks, subtasks must be treated as independent work items by default:

- Subtasks do not reference each other.
- No predefined sequential execution order is assumed between subtasks.
- The parent sprint acts as the orchestrator and source of closure control.
- The parent sprint must maintain a closure checklist that verifies all subtasks are completed before the sprint can be closed.

## Sprint-Epic Partial Coverage Rule

If a sprint subtask only covers part of an epic (or sub-epic), sprint planning must trigger epic subdivision and map the sprint subtask to exactly one resulting epic subtask.

## SSOT Governance Epics

- Epic subdivision rules are defined only in [2-epics/README.md](../2-epics/README.md).
- Sprint must not redefine or duplicate epic-side structure criteria.
- Epic records remain sprint-agnostic and must not include outer-layer temporal rationale.

## Sprint-Initiated Subdivision Traceability

If the subdivision trigger is discovered in sprint planning or execution:

- Create the sprint-side doubt at sprint root scope (`S-XXX`) and reference the corresponding epic doubt.
- The sprint doubt may include temporal or sprint-driven rationale (for example, postponement to another sprint).
- The sprint doubt must reference the corresponding epic doubt as the authoritative epic-structure change request.
- Traceability is one-way under COD: Sprint may reference Epic, but Epic remains sprint-agnostic.
- Sprint records may enforce that epic records remain free of outer-layer rationale (for example sprint timing or temporal motivation).

## Navigation

- [index.md](index.md) — Live directory of all files in this layer.
- [doubts-and-decisions/](doubts-and-decisions/) — Atomic management of open and closed doubts.
