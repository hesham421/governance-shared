<!-- source: PHASE:F4 / SUB:F4-SCR-FIN-010 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-030, REQ-FIN-041, REQ-FIN-046, SCR-FIN-010 -->
<!-- SUB:F4-SCR-FIN-010:START traces=SCR-FIN-010,REQ-FIN-041,REQ-FIN-046,API-FIN-030 -->
### F4-SCR-FIN-010-SCREEN — Balance sheet
Routes       : `/fin/reports/balance-sheet` (FULL_PAGE, no entry sub-view — ADR-FIN-003); filter state in route search params
Guard        : `FIN_BALANCE_SHEET` present in the effective menu
Facade       : F2-SCR-FIN-010-FACADE
Cross-module : UXD-FIN-001, UXD-FIN-002
<!-- SUB:F4-SCR-FIN-010:END -->
