<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-012 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-032, REQ-FIN-043, SCR-FIN-012 -->
<!-- SUB:F2-SCR-FIN-012:START traces=SCR-FIN-012,API-FIN-032,REQ-FIN-043 -->
### F2-SCR-FIN-012 — Dimension reports
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-012-REPORT-QUERY | read · API-FIN-032 | [dimension-report, dimensionId, dimensionValueId, periodId] | server → generic | LOCAL | — |
| F2-SCR-FIN-012-LOOKUP | UXD-FIN-002 | [lookup, DEBIT_CREDIT] — long-lived | — | NONE | — |
| F2-SCR-FIN-012-SCREEN-INIT | SCR-FIN-012 | permission read + the lookup | — | GLOBAL | — |
| F2-SCR-FIN-012-FACADE | SCR-FIN-012 | composes REPORT-QUERY · state: filters, derived loading |
<!-- SUB:F2-SCR-FIN-012:END -->
