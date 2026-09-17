<!-- source: PHASE:F1 — preamble before the first SUB -->
<!-- traces: SCR-FIN-001, SCR-FIN-002, SCR-FIN-003, SCR-FIN-004, SCR-FIN-005, SCR-FIN-006, SCR-FIN-007, SCR-FIN-008, SCR-FIN-009, SCR-FIN-010, SCR-FIN-011, SCR-FIN-012 -->
## PHASE F1 — Models & Types

**RF1 — Models & types.** Field/DTO binding: see `_inputs/api-docs-fin.md` — the published
request/response shapes are the source, not restated here. Every lookup-coded field types as
`string` everywhere in the models (ADR-FIN-004) — never an enum, never a union of literals —
so a value MDL has not yet seeded is still assignable to the type; validity is a runtime
concern (§F3), not a type concern.
