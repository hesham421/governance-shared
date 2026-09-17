<!-- source: PHASE:F3 — preamble before the first SUB -->
<!-- traces: SCR-FIN-001, SCR-FIN-002, SCR-FIN-003, SCR-FIN-004, SCR-FIN-005, SCR-FIN-006, SCR-FIN-007, SCR-FIN-008, SCR-FIN-009, SCR-FIN-010, SCR-FIN-011, SCR-FIN-012 -->
## PHASE F3 — Forms & Validators

No content role in `factory.yaml` names this phase (§3.2); filled per the profile's own
convention: one `zod` schema per entry/filter form, field constraints bound to the
published request DTO (`_inputs/api-docs-fin.md`), business-rule checks that a form can
usefully pre-empt named against their `RULE-FIN-*`, and the rest left to the server.
