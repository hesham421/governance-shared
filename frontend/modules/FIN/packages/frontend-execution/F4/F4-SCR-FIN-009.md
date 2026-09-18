<!-- source: PHASE:F4 / SUB:F4-SCR-FIN-009 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-029, REQ-FIN-040, REQ-FIN-046, SCR-FIN-009 -->
<!-- SUB:F4-SCR-FIN-009:START traces=SCR-FIN-009,REQ-FIN-040,REQ-FIN-046,API-FIN-029 -->
### F4-SCR-FIN-009-SCREEN — Trial balance
Routes       : `/fin/reports/trial-balance` (FULL_PAGE, no entry sub-view — ADR-FIN-003); filter state in route search params
Guard        : `FIN_TRIAL_BALANCE` present in the effective menu
Facade       : F2-SCR-FIN-009-FACADE
Cross-module : UXD-FIN-001, UXD-FIN-002
<!-- SUB:F4-SCR-FIN-009:END -->
