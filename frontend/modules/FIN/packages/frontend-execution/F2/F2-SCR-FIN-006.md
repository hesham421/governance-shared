<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-006 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-018, API-FIN-019, API-FIN-021, API-FIN-022, REQ-FIN-014, REQ-FIN-016, REQ-FIN-017, REQ-FIN-027, REQ-FIN-028, SCR-FIN-006 -->
<!-- SUB:F2-SCR-FIN-006:START traces=SCR-FIN-006,API-FIN-018,API-FIN-019,API-FIN-021,API-FIN-022,REQ-FIN-014,REQ-FIN-016,REQ-FIN-017,REQ-FIN-027,REQ-FIN-028 -->
### F2-SCR-FIN-006 — Journal entries
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-006-SEARCH-QUERY | read · API-FIN-018 | [journal-entries, filters] (docNo/docDate/period/status/type + page/size) | server → generic | LOCAL | — |
| F2-SCR-FIN-006-READ-QUERY | read · API-FIN-022 | [journal-entries, id] | FIN-404-ENTRY → generic | LOCAL | — |
| F2-SCR-FIN-006-CREATE-MUTATION | mutation · API-FIN-019 | — | RULE-FIN-006/007/008/009/017 → inline (field-level where the offending line/field is named) | LOCAL | invalidates [journal-entries] |
| F2-SCR-FIN-006-REVERSE-MUTATION | mutation · API-FIN-021 | — | RULE-FIN-013 → generic (FIN-409-NOT-POSTED / FIN-409-ALREADY-REVERSED) | LOCAL | invalidates [journal-entries] |
| F2-SCR-FIN-006-LOOKUP | UXD-FIN-005, UXD-FIN-006, UXD-FIN-002 | three [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-006-SCREEN-INIT | SCR-FIN-006 | permission read (VIEW/CREATE + custom Reverse) + three lookups + fiscal-period list (for the year/period pair, RULE-FIN-017) | — | GLOBAL | — |
| F2-SCR-FIN-006-FACADE | SCR-FIN-006 | composes SEARCH-QUERY, READ-QUERY, CREATE/REVERSE-MUTATION · state: list, selected entry, filters, live debit/credit totals while entering (client-side echo of RULE-FIN-006, server remains authoritative) |
<!-- SUB:F2-SCR-FIN-006:END -->
