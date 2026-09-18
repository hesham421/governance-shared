<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-003 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-009, API-FIN-010, API-FIN-011, API-FIN-034, REQ-FIN-007, REQ-FIN-008, REQ-FIN-009, SCR-FIN-003 -->
<!-- SUB:F2-SCR-FIN-003:START traces=SCR-FIN-003,API-FIN-009,API-FIN-010,API-FIN-011,API-FIN-034,REQ-FIN-007,REQ-FIN-008,REQ-FIN-009 -->
### F2-SCR-FIN-003 — Engine rules
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-003-SEARCH-QUERY | read · API-FIN-009 | [event-rules, filters] | server → generic | LOCAL | — |
| F2-SCR-FIN-003-CREATE-MUTATION | mutation · API-FIN-010 | — | FIN-409-RULE-DUP → inline · FIN-400-INVALID-LOOKUP → inline | LOCAL | invalidates [event-rules] |
| F2-SCR-FIN-003-ADD-LINE-MUTATION | mutation · API-FIN-011 | — | RULE-FIN-003 → inline · FIN-422-MAPPING-UNSUPPORTED → inline | LOCAL | invalidates [event-rules] |
| F2-SCR-FIN-003-DEACTIVATE-MUTATION | mutation · API-FIN-034 | — | FIN-404-RULE → generic | LOCAL | invalidates [event-rules] |
| F2-SCR-FIN-003-LOOKUP | UXD-FIN-007, UXD-FIN-008, UXD-FIN-009, UXD-FIN-002, UXD-FIN-010 | five [lookup, <key>] entries — long-lived | — | NONE | — |
| F2-SCR-FIN-003-SCREEN-INIT | SCR-FIN-003 | permission read + five lookups | — | GLOBAL | — |
| F2-SCR-FIN-003-FACADE | SCR-FIN-003 | composes SEARCH-QUERY + the three mutations · state: rule list, selected rule's lines, filters |
<!-- SUB:F2-SCR-FIN-003:END -->
