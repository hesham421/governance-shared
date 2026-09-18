<!-- source: PHASE:F4 / SUB:F4-SCR-FIN-008 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-028, REQ-FIN-039, REQ-FIN-046, SCR-FIN-008 -->
<!-- SUB:F4-SCR-FIN-008:START traces=SCR-FIN-008,REQ-FIN-039,REQ-FIN-046,API-FIN-028 -->
### F4-SCR-FIN-008-SCREEN — Account ledger
Routes       : `/fin/reports/account-ledger` (FULL_PAGE, no entry sub-view — ADR-FIN-003); filter state mirrored into route search params so a filtered report and a drill-in from another screen are both linkable
Guard        : `FIN_ACCOUNT_LEDGER` present in the effective menu
Facade       : F2-SCR-FIN-008-FACADE
Cross-module : UXD-FIN-001, UXD-FIN-002, UXD-FIN-005
<!-- SUB:F4-SCR-FIN-008:END -->
