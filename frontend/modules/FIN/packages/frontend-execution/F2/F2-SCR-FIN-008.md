<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-008 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-028, REQ-FIN-039, REQ-FIN-046, SCR-FIN-008 -->
<!-- SUB:F2-SCR-FIN-008:START traces=SCR-FIN-008,API-FIN-028,REQ-FIN-039,REQ-FIN-046 -->
### F2-SCR-FIN-008 — Account ledger
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-008-REPORT-QUERY | read · API-FIN-028 | [account-ledger, accountId, fromDate, toDate, dimensionId, dimensionValueId] | FIN-404-ACCOUNT → generic | LOCAL | — |
| F2-SCR-FIN-008-LOOKUP | UXD-FIN-001, UXD-FIN-002, UXD-FIN-005 | three [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-008-SCREEN-INIT | SCR-FIN-008 | permission read + three lookups | — | GLOBAL | — |
| F2-SCR-FIN-008-FACADE | SCR-FIN-008 | composes REPORT-QUERY · state: filters (incl. drill-in accountId carried from another screen's row), derived loading |
<!-- SUB:F2-SCR-FIN-008:END -->
