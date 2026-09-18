<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-009 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-029, REQ-FIN-040, REQ-FIN-046, SCR-FIN-009 -->
<!-- SUB:F2-SCR-FIN-009:START traces=SCR-FIN-009,API-FIN-029,REQ-FIN-040,REQ-FIN-046 -->
### F2-SCR-FIN-009 — Trial balance
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-009-REPORT-QUERY | read · API-FIN-029 | [trial-balance, periodId, accountTypeCode] | FIN-404-PERIOD → generic | LOCAL | — |
| F2-SCR-FIN-009-LOOKUP | UXD-FIN-001, UXD-FIN-002 | two [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-009-SCREEN-INIT | SCR-FIN-009 | permission read + two lookups | — | GLOBAL | — |
| F2-SCR-FIN-009-FACADE | SCR-FIN-009 | composes REPORT-QUERY · state: filters, derived loading |
<!-- SUB:F2-SCR-FIN-009:END -->
