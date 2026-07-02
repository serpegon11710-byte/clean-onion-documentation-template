# Epics

This folder manages the **epics** layer.

## Epic Subdivision Governance

When an epic (or sub-epic) is only partially addressable by one delivery subtask, the epic scope must be subdivided into child epic nodes.

- The parent epic/sub-epic remains an orchestrator node.
- The subdivision must create 2 or more child epic nodes that fully cover the parent scope.
- The parent epic/sub-epic can be closed only after a closure checklist confirms all child nodes are complete.

## Epic Subdivision Record Rule

When a subdivision is agreed, the epic layer records only epic-structure information:

- Register the doubt at epic root scope (`E-XXX`), even if the affected epic node is deeper (`E-XXX.*`).
- Document only the structural split.
- Minimal wording is valid, for example: "It is agreed to split epic E-XXX.YY into E-XXX.YY.Z1 and E-XXX.YY.Z2."

## Navigation

- [index.md](index.md) — Live directory of all files in this layer.
- [doubts-and-decisions/](doubts-and-decisions/) — Atomic management of open and closed doubts.
