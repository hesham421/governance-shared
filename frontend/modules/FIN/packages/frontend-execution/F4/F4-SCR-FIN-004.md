<!-- source: PHASE:F4 / SUB:F4-SCR-FIN-004 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-012, API-FIN-013, API-FIN-014, API-FIN-036, REQ-FIN-022, REQ-FIN-023, REQ-FIN-024, SCR-FIN-004 -->
<!-- SUB:F4-SCR-FIN-004:START traces=SCR-FIN-004,REQ-FIN-022,REQ-FIN-023,REQ-FIN-024,API-FIN-012,API-FIN-013,API-FIN-014,API-FIN-036 -->
### F4-SCR-FIN-004-SCREEN — Recurring/reversing templates
Routes       : `/fin/recurring-templates` (master + detail, no `:id` route — ADR-FIN-006; no Edit affordance — update not published, ADR-FIN-006)
Guard        : `FIN_RECURRING_TEMPLATES` present in the effective menu
Facade       : F2-SCR-FIN-004-FACADE
Cross-module : UXD-FIN-011, UXD-FIN-012, UXD-FIN-002
<!-- SUB:F4-SCR-FIN-004:END -->
