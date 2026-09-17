<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-004 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-012, API-FIN-013, API-FIN-014, API-FIN-036, REQ-FIN-022, REQ-FIN-023, REQ-FIN-024, SCR-FIN-004 -->
<!-- SUB:F2-SCR-FIN-004:START traces=SCR-FIN-004,API-FIN-012,API-FIN-013,API-FIN-014,API-FIN-036,REQ-FIN-022,REQ-FIN-023,REQ-FIN-024 -->
### F2-SCR-FIN-004 — Recurring/reversing templates
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-004-SEARCH-QUERY | read · API-FIN-012 | [recurring-templates, filters] | server → generic | LOCAL | — |
| F2-SCR-FIN-004-CREATE-MUTATION | mutation · API-FIN-013 | — | FIN-400-MISSING-FREQUENCY → inline | LOCAL | invalidates [recurring-templates] |
| F2-SCR-FIN-004-RUN-MUTATION | mutation · API-FIN-014 | — | FIN-409-NOT-ACTIVE → generic · RULE-FIN-006/007/008/009/011 → generic (server-built entry) | GLOBAL (posts a real entry) | invalidates [recurring-templates], [journal-entries] |
| F2-SCR-FIN-004-DEACTIVATE-MUTATION | mutation · API-FIN-036 | — | FIN-404-TEMPLATE → generic | LOCAL | invalidates [recurring-templates] |
| F2-SCR-FIN-004-LOOKUP | UXD-FIN-011, UXD-FIN-012, UXD-FIN-002 | three [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-004-SCREEN-INIT | SCR-FIN-004 | permission read + three lookups | — | GLOBAL | — |
| F2-SCR-FIN-004-FACADE | SCR-FIN-004 | composes SEARCH-QUERY + the three mutations · state: template list, selected template's lines, filters |
<!-- SUB:F2-SCR-FIN-004:END -->
