<!-- source: PHASE:F4 / SUB:F4-SCR-FIN-006 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-018, API-FIN-019, API-FIN-021, API-FIN-022, REQ-FIN-014, REQ-FIN-016, REQ-FIN-017, REQ-FIN-027, REQ-FIN-028, SCR-FIN-006 -->
<!-- SUB:F4-SCR-FIN-006:START traces=SCR-FIN-006,REQ-FIN-014,REQ-FIN-016,REQ-FIN-017,REQ-FIN-027,REQ-FIN-028,API-FIN-018,API-FIN-019,API-FIN-021,API-FIN-022 -->
### F4-SCR-FIN-006-SCREEN — Journal entries
Routes       : `/fin/journal-entries` (search) · `/fin/journal-entries/new` (manual entry) · `/fin/journal-entries/:id` (read + Reverse action on a POSTED row)
Guard        : `FIN_JOURNAL_ENTRIES` present in the effective menu; `/new` additionally requires the CREATE affordance, Reverse the custom `PERM_FIN_JOURNAL_ENTRIES_REVERSE` affordance (both rendered by ADR-FIN-005's own convention — presence check only, no client-composed permission string)
Facade       : F2-SCR-FIN-006-FACADE
Cross-module : UXD-FIN-005, UXD-FIN-006, UXD-FIN-002
<!-- SUB:F4-SCR-FIN-006:END -->
