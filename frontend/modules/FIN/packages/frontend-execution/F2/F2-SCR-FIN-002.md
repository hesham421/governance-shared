<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-002 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-005, API-FIN-006, API-FIN-007, API-FIN-008, API-FIN-035, REQ-FIN-004, REQ-FIN-005, REQ-FIN-006, SCR-FIN-002 -->
<!-- SUB:F2-SCR-FIN-002:START traces=SCR-FIN-002,API-FIN-005,API-FIN-006,API-FIN-007,API-FIN-008,API-FIN-035,REQ-FIN-004,REQ-FIN-005,REQ-FIN-006 -->
### F2-SCR-FIN-002 — Dimensions
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-002-SEARCH-DIM-QUERY | read · API-FIN-005 | [dimensions, filters] | server → generic | LOCAL | — |
| F2-SCR-FIN-002-SEARCH-VALUE-QUERY | read · API-FIN-008 | [dimension-values, filters incl. dimensionId] | server → generic | LOCAL | — |
| F2-SCR-FIN-002-CREATE-DIM-MUTATION | mutation · API-FIN-006 | — | FIN-409-DIMENSION-DUP → inline | LOCAL | invalidates [dimensions] |
| F2-SCR-FIN-002-CREATE-VALUE-MUTATION | mutation · API-FIN-007 | — | RULE-FIN-002 → inline (duplicate code) | LOCAL | invalidates [dimension-values] |
| F2-SCR-FIN-002-DEACTIVATE-VALUE-MUTATION | mutation · API-FIN-035 | — | FIN-404-DIMVALUE → generic | LOCAL | invalidates [dimension-values] |
| F2-SCR-FIN-002-SCREEN-INIT | SCR-FIN-002 | permission read (VIEW/CREATE/UPDATE) | — | GLOBAL | — |
| F2-SCR-FIN-002-FACADE | SCR-FIN-002 | composes both search queries + the three mutations · state: dimension list, selected dimension's values, filters |
<!-- SUB:F2-SCR-FIN-002:END -->
