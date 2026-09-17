<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-010 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-030, REQ-FIN-041, REQ-FIN-046, SCR-FIN-010 -->
<!-- SUB:F2-SCR-FIN-010:START traces=SCR-FIN-010,API-FIN-030,REQ-FIN-041,REQ-FIN-046 -->
### F2-SCR-FIN-010 — Balance sheet
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-010-REPORT-QUERY | read · API-FIN-030 | [balance-sheet, fiscalYearId, asOfDate] | FIN-404-YEAR → generic | LOCAL | — |
| F2-SCR-FIN-010-LOOKUP | UXD-FIN-001, UXD-FIN-002 | two [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-010-SCREEN-INIT | SCR-FIN-010 | permission read + two lookups | — | GLOBAL | — |
| F2-SCR-FIN-010-FACADE | SCR-FIN-010 | composes REPORT-QUERY · state: filters, derived loading |
<!-- SUB:F2-SCR-FIN-010:END -->
