<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-011 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-031, REQ-FIN-042, REQ-FIN-046, SCR-FIN-011 -->
<!-- SUB:F2-SCR-FIN-011:START traces=SCR-FIN-011,API-FIN-031,REQ-FIN-042,REQ-FIN-046 -->
### F2-SCR-FIN-011 — Income statement
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-011-REPORT-QUERY | read · API-FIN-031 | [income-statement, fiscalYearId, fromPeriodId, toPeriodId] | FIN-404-YEAR / FIN-404-PERIOD → generic | LOCAL | — |
| F2-SCR-FIN-011-LOOKUP | UXD-FIN-001, UXD-FIN-002 | two [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-011-SCREEN-INIT | SCR-FIN-011 | permission read + two lookups | — | GLOBAL | — |
| F2-SCR-FIN-011-FACADE | SCR-FIN-011 | composes REPORT-QUERY · state: filters, derived loading |
<!-- SUB:F2-SCR-FIN-011:END -->
