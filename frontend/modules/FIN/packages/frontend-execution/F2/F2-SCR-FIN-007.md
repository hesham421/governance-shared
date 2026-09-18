<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-007 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-023, API-FIN-024, API-FIN-025, API-FIN-026, API-FIN-027, API-FIN-033, REQ-FIN-031, REQ-FIN-034, REQ-FIN-036, REQ-FIN-037, SCR-FIN-007 -->
<!-- SUB:F2-SCR-FIN-007:START traces=SCR-FIN-007,API-FIN-023,API-FIN-024,API-FIN-025,API-FIN-026,API-FIN-027,API-FIN-033,REQ-FIN-031,REQ-FIN-034,REQ-FIN-036,REQ-FIN-037 -->
### F2-SCR-FIN-007 — Fiscal periods & years
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-007-SEARCH-QUERY | read · API-FIN-033 | [fiscal-periods, filters] (fiscalYearId?, statusCode?) | server → generic | LOCAL | — |
| F2-SCR-FIN-007-CREATE-YEAR-MUTATION | mutation · API-FIN-023 | — | FIN-409-YEAR-DUP → inline | LOCAL | invalidates [fiscal-periods] |
| F2-SCR-FIN-007-OPEN-MUTATION | mutation · API-FIN-024 | — | FIN-409-NOT-REOPENABLE → generic | LOCAL | invalidates [fiscal-periods] |
| F2-SCR-FIN-007-SOFT-CLOSE-MUTATION | mutation · API-FIN-025 | — | FIN-409-INVALID-TRANSITION → generic | LOCAL | invalidates [fiscal-periods] |
| F2-SCR-FIN-007-HARD-CLOSE-MUTATION | mutation · API-FIN-026 | — | RULE-FIN-014/015 → generic (FIN-403-FORBIDDEN routes to unauthorized) | LOCAL | invalidates [fiscal-periods] |
| F2-SCR-FIN-007-YEAR-END-CLOSE-MUTATION | mutation · API-FIN-027 | — | FIN-409-PERIODS-NOT-CLOSED → inline | GLOBAL (posts closing+opening entries) | invalidates [fiscal-periods], [journal-entries] |
| F2-SCR-FIN-007-LOOKUP | UXD-FIN-003, UXD-FIN-004 | two [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-007-SCREEN-INIT | SCR-FIN-007 | permission read (VIEW/CREATE/UPDATE + Close-approve) + two lookups | — | GLOBAL | — |
| F2-SCR-FIN-007-FACADE | SCR-FIN-007 | composes SEARCH-QUERY + the five mutations · state: year list (derived from period rows' fiscalYearId, ADR-FIN-006), periods per year, filters · Run-year-end-close enabled only once every period of the selected year reads HARD_CLOSE |
<!-- SUB:F2-SCR-FIN-007:END -->
