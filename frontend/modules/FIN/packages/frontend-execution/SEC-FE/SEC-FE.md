<!-- source: PHASE:SEC-FE -->
<!-- traces: RULE-FIN-015, SCR-FIN-001, SCR-FIN-002, SCR-FIN-003, SCR-FIN-004, SCR-FIN-005, SCR-FIN-006, SCR-FIN-007, SCR-FIN-008, SCR-FIN-009, SCR-FIN-010, SCR-FIN-011, SCR-FIN-012 -->
<!-- PHASE:SEC-FE:START traces=SCR-FIN-001,SCR-FIN-002,SCR-FIN-003,SCR-FIN-004,SCR-FIN-005,SCR-FIN-006,SCR-FIN-007,SCR-FIN-008,SCR-FIN-009,SCR-FIN-010,SCR-FIN-011,SCR-FIN-012,RULE-FIN-015 -->
## PHASE SEC-FE — Security (frontend half)

**RF5 — Security.** Per `SCR-*`: navigation guard (no VIEW → unauthorized redirect) and UI
behaviour per action (no VIEW/CREATE/UPDATE/DELETE → its affordance hidden / read-only);
forbidden server responses (`FIN-403-FORBIDDEN`) shown as the localized catalog message.
Permission names are the backend/SEC registry's own — never redeclared here (ADR-FIN-005).

| Screen | Navigation guard | Action affordances |
|---|---|---|
| SCR-FIN-001 | `FIN_ACCOUNTS` present → else unauthorized redirect | CREATE/UPDATE hidden without the respective grant; deactivate follows UPDATE |
| SCR-FIN-002 | `FIN_DIMENSIONS` present | CREATE hidden without grant; value-deactivate follows UPDATE; dimension itself has no deactivate (SRS §B4) |
| SCR-FIN-003 | `FIN_RULES` present | CREATE hidden without grant; add-line and rule-deactivate follow UPDATE |
| SCR-FIN-004 | `FIN_RECURRING_TEMPLATES` present | CREATE hidden without grant; run and deactivate follow UPDATE |
| SCR-FIN-005 | `FIN_ALLOCATION_RULES` present | CREATE hidden without grant; run and deactivate follow UPDATE |
| SCR-FIN-006 | `FIN_JOURNAL_ENTRIES` present | CREATE (incl. Post) hidden without grant; Reverse is its own custom affordance (`PERM_FIN_JOURNAL_ENTRIES_REVERSE`), independent of CREATE |
| SCR-FIN-007 | `FIN_PERIODS` present | CREATE (year) and UPDATE (open/soft-close) hidden without grant; hard-close/year-end-close are the Close-approve custom affordance (RULE-FIN-015) — a caller holding only entry-creation never sees them enabled, and the server's `FIN-403-FORBIDDEN` is the authority on the write, never duplicated as a client-side separation-of-duty check |
| SCR-FIN-008 .. SCR-FIN-012 | respective page code present | read-only, VIEW only — no write affordance exists |

A failed or empty menu load renders no FIN entry and grants no FIN route on any screen —
access narrows, never widens (ADR-FIN-005).
<!-- PHASE:SEC-FE:END -->
