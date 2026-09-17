<!-- source: PHASE:F4 / SUB:F4-SCR-FIN-003 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-009, API-FIN-010, API-FIN-011, API-FIN-034, REQ-FIN-007, REQ-FIN-008, REQ-FIN-009, SCR-FIN-003 -->
<!-- SUB:F4-SCR-FIN-003:START traces=SCR-FIN-003,REQ-FIN-007,REQ-FIN-008,REQ-FIN-009,API-FIN-009,API-FIN-010,API-FIN-011,API-FIN-034 -->
### F4-SCR-FIN-003-SCREEN — Engine rules
Routes       : `/fin/rules` (master list + detail lines panel, no `:id` route — ADR-FIN-006; no line-delete affordance — ADR-FIN-006)
Guard        : `FIN_RULES` present in the effective menu
Facade       : F2-SCR-FIN-003-FACADE
Cross-module : UXD-FIN-007, UXD-FIN-008, UXD-FIN-009, UXD-FIN-002, UXD-FIN-010
<!-- SUB:F4-SCR-FIN-003:END -->
