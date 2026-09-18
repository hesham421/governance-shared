<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-005 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-015, API-FIN-016, API-FIN-017, API-FIN-037, REQ-FIN-025, REQ-FIN-026, SCR-FIN-005 -->
<!-- SUB:F2-SCR-FIN-005:START traces=SCR-FIN-005,API-FIN-015,API-FIN-016,API-FIN-017,API-FIN-037,REQ-FIN-025,REQ-FIN-026 -->
### F2-SCR-FIN-005 — Allocation rules
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-005-SEARCH-QUERY | read · API-FIN-015 | [allocation-rules, filters] | server → generic | LOCAL | — |
| F2-SCR-FIN-005-CREATE-MUTATION | mutation · API-FIN-016 | — | RULE-FIN-003 → inline | LOCAL | invalidates [allocation-rules] |
| F2-SCR-FIN-005-RUN-MUTATION | mutation · API-FIN-017 | — | FIN-409-NOT-ACTIVE → generic · RULE-FIN-006..011 → generic | GLOBAL (posts a real entry) | invalidates [allocation-rules], [journal-entries] |
| F2-SCR-FIN-005-DEACTIVATE-MUTATION | mutation · API-FIN-037 | — | FIN-404-ALLOCATION-RULE → generic | LOCAL | invalidates [allocation-rules] |
| F2-SCR-FIN-005-LOOKUP | UXD-FIN-010 | [lookup, DISTRIBUTION_TYPE] — long-lived | — | NONE | — |
| F2-SCR-FIN-005-SCREEN-INIT | SCR-FIN-005 | permission read + the lookup | — | GLOBAL | — |
| F2-SCR-FIN-005-FACADE | SCR-FIN-005 | composes SEARCH-QUERY + the three mutations · state: rule list, selected rule's targets, filters |
<!-- SUB:F2-SCR-FIN-005:END -->
