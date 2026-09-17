<!-- source: PHASE:F4 / SUB:F4-SCR-FIN-001 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-001, API-FIN-002, API-FIN-003, API-FIN-004, REQ-FIN-001, REQ-FIN-002, REQ-FIN-003, SCR-FIN-001 -->
<!-- SUB:F4-SCR-FIN-001:START traces=SCR-FIN-001,REQ-FIN-001,REQ-FIN-002,REQ-FIN-003,API-FIN-001,API-FIN-002,API-FIN-003,API-FIN-004 -->
### F4-SCR-FIN-001-SCREEN — Chart of accounts
Routes       : `/fin/accounts` (search + inline entry — Composite screen, no `:id` route: entry hydrates from the search row, ADR-FIN-006)
Guard        : `FIN_ACCOUNTS` present in the effective menu (ADR-FIN-005)
Facade       : F2-SCR-FIN-001-FACADE · page never calls the queries/mutations directly
Cross-module : UXD-FIN-001, UXD-FIN-002 (lookup labels only) — no foreign business data displayed
<!-- SUB:F4-SCR-FIN-001:END -->
