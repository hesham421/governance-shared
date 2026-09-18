<!-- source: PHASE:F2 / SUB:F2-SCR-FIN-001 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-FIN-001, API-FIN-002, API-FIN-003, API-FIN-004, REQ-FIN-001, REQ-FIN-002, REQ-FIN-003, SCR-FIN-001 -->
<!-- SUB:F2-SCR-FIN-001:START traces=SCR-FIN-001,API-FIN-001,API-FIN-002,API-FIN-003,API-FIN-004,REQ-FIN-001,REQ-FIN-002,REQ-FIN-003 -->
### F2-SCR-FIN-001 — Chart of accounts
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-001-SEARCH-QUERY | read · API-FIN-001 | [accounts, filters] (code/name/type/active + page/size) | server → generic | LOCAL | — |
| F2-SCR-FIN-001-CREATE-MUTATION | mutation · API-FIN-002 | — | RULE-FIN-001 → inline (parent has children) · FIN-409-ACCOUNT-DUP → inline (code) | LOCAL | invalidates [accounts] |
| F2-SCR-FIN-001-UPDATE-MUTATION | mutation · API-FIN-003 | — | RULE-FIN-001 → inline | LOCAL | invalidates [accounts] |
| F2-SCR-FIN-001-DEACTIVATE-MUTATION | mutation · API-FIN-004 | — | server → generic | LOCAL | invalidates [accounts] |
| F2-SCR-FIN-001-LOOKUP | UXD-FIN-001, UXD-FIN-002 | [lookup, ACCOUNT_TYPE] / [lookup, DEBIT_CREDIT] — long-lived | — | NONE | — |
| F2-SCR-FIN-001-SCREEN-INIT | SCR-FIN-001 | permission read (VIEW/CREATE/UPDATE via ADR-FIN-005 menu) + the two lookups | — | GLOBAL (first paint) | — |
| F2-SCR-FIN-001-FACADE | SCR-FIN-001 | composes SEARCH-QUERY, CREATE/UPDATE/DEACTIVATE-MUTATION · state: list, selection, filters (incl. page/size), derived loading · deactivate first checks a usage confirmation (RULE-FIN-001 parent case is server-checked, not pre-empted client-side) |
<!-- SUB:F2-SCR-FIN-001:END -->
