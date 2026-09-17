<!-- source: PHASE:F4 / SUB:F4-SCR-FIN-011 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-031, REQ-FIN-042, REQ-FIN-046, SCR-FIN-011 -->
<!-- SUB:F4-SCR-FIN-011:START traces=SCR-FIN-011,REQ-FIN-042,REQ-FIN-046,API-FIN-031 -->
### F4-SCR-FIN-011-SCREEN — Income statement
Routes       : `/fin/reports/income-statement` (FULL_PAGE, no entry sub-view — ADR-FIN-003); filter state in route search params
Guard        : `FIN_INCOME_STATEMENT` present in the effective menu
Facade       : F2-SCR-FIN-011-FACADE
Cross-module : UXD-FIN-001, UXD-FIN-002
<!-- SUB:F4-SCR-FIN-011:END -->
