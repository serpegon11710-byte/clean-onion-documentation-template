# History : Governance

This folder provides traceability for changes made within this block.

## Practice

Create one file per period (e.g., `2026-W26-changes.md`) to keep the history fragmented and avoid contaminating the active chat context.

Weekly fragmentation is mandatory, but daily traceability is also mandatory:

- Keep one history file per ISO week (`YYYY-Www-changes.md`).
- Every logged finding/change inside that file must include the day it was documented (`YYYY-MM-DD`).
- Do not batch multiple days under a single undated note.
- Use this line format for each entry: `YYYY-MM-DD - <registrable description> - <optional Decision Id reference>`.
- If the doubt belongs to this block, reference it as bare `D-XXX`.
- If the doubt belongs to another block, reference it as qualified markdown link `{block}/D-XXX` pointing to the owning block `solved/doubt-XXX.md`.

## Navigation

Consult [index.md](index.md) in this folder for the chronological map of all history entries.
