<!-- source: PHASE:F4 / SUB:F4-SCR-FIN-007 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-023, API-FIN-024, API-FIN-025, API-FIN-026, API-FIN-027, API-FIN-033, REQ-FIN-031, REQ-FIN-034, REQ-FIN-036, REQ-FIN-037, SCR-FIN-007 -->
<!-- SUB:F4-SCR-FIN-007:START traces=SCR-FIN-007,REQ-FIN-031,REQ-FIN-034,REQ-FIN-036,REQ-FIN-037,API-FIN-023,API-FIN-024,API-FIN-025,API-FIN-026,API-FIN-027,API-FIN-033 -->
### F4-SCR-FIN-007-SCREEN — Fiscal periods & years
Routes       : `/fin/periods` (master years + detail periods, no `:id` route, no fiscal-year search — ADR-FIN-006)
Guard        : `FIN_PERIODS` present in the effective menu; hard-close/year-end-close affordances additionally require the custom Close-approve presence flag (RULE-FIN-015, ADR-FIN-005 — the separation of duty is server-enforced, not hidden by this guard alone)
Facade       : F2-SCR-FIN-007-FACADE
Cross-module : UXD-FIN-003, UXD-FIN-004
<!-- SUB:F4-SCR-FIN-007:END -->
