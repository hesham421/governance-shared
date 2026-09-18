<!-- source: PHASE:F4 / SUB:F4-SCR-FIN-005 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-015, API-FIN-016, API-FIN-017, API-FIN-037, REQ-FIN-025, REQ-FIN-026, SCR-FIN-005 -->
<!-- SUB:F4-SCR-FIN-005:START traces=SCR-FIN-005,REQ-FIN-025,REQ-FIN-026,API-FIN-015,API-FIN-016,API-FIN-017,API-FIN-037 -->
### F4-SCR-FIN-005-SCREEN — Allocation rules
Routes       : `/fin/allocation-rules` (master + detail, no `:id` route — ADR-FIN-006; no Edit affordance — update not published, ADR-FIN-006)
Guard        : `FIN_ALLOCATION_RULES` present in the effective menu
Facade       : F2-SCR-FIN-005-FACADE
Cross-module : UXD-FIN-010
<!-- SUB:F4-SCR-FIN-005:END -->
