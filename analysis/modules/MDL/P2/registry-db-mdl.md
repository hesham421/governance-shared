## REGISTRY — P2 — MDL v1
══════════════════════════════════════════════════════════════════

Tables
| Table | ENT id | Kind | DBF range |
|---|---|---|---|
| MDL_LOOKUP_TYPE | ENT-MDL-001 | master | DBF-MDL-001 … DBF-MDL-010 |
| MDL_LOOKUP_VALUE | ENT-MDL-002 | lookup | DBF-MDL-011 … DBF-MDL-021 |

Both names are the ones the platform structural registry already fixed for this module
(project-registry STRUCTURAL / IMPLEMENTATION REGISTRY, row MDL v1); neither is new here.

DB sequences (one per table, profile.stack.db.naming.sequence_pattern — carried into the
backend plan by P3.1): SEQ_MDL_LOOKUP_TYPE, SEQ_MDL_LOOKUP_VALUE. Every PK column is a plain
BIGINT NOT NULL fed from its sequence by the application — no identity clause anywhere.

DBF ids (full detail in db-script-mdl.md → §1): DBF-MDL-001, DBF-MDL-002, DBF-MDL-003,
DBF-MDL-004, DBF-MDL-005, DBF-MDL-006, DBF-MDL-007, DBF-MDL-008, DBF-MDL-009, DBF-MDL-010,
DBF-MDL-011, DBF-MDL-012, DBF-MDL-013, DBF-MDL-014, DBF-MDL-015, DBF-MDL-016, DBF-MDL-017,
DBF-MDL-018, DBF-MDL-019, DBF-MDL-020, DBF-MDL-021

XM index
| XM id | Type | From | To | Consumes | Status |
|---|---|---|---|---|---|
| XM-MDL-001 | SOFT-READ | MDL | SEC | ModuleRegistry (ENT-SEC-004, table SEC_MODULE_REG) — existence of a lookup type owner module code, RULE-MDL-001 | ACTIVE |

Lookups
| Key | Seeded values count | Owner |
|---|---|---|
| — | 0 | — |
MDL owns no lookup key and consumes none (SRS A6): it is the mechanism every other module
runs its coded lists on. No seed block exists in this script and none is owed to another
module's table; a consuming module registers and seeds its own keys through this module's
API (API-MDL-002, API-MDL-006).

Sequences
Last DBF: DBF-MDL-021 · Last XM: XM-MDL-001

Decisions
ADR-MDL-009 (ACCEPTED, non-breaking) — index strategy for this module's two tables.
ADR-MDL-010 (ACCEPTED, non-breaking) — string precisions for the six unsized text columns.
The ADR sequence continues from ADR-MDL-008 (raised at P3.2); nothing is renumbered, and no
decision is BLOCKED — the pass completed.

Cascade
No registry XM row anywhere in the platform targets MDL with status DEFERRED, so there is
nothing to resolve: the one inbound row, XM-FIN-001 (FIN → MDL), is a SOFT-READ already
recorded ACTIVE, and this script now gives it the structure it was already written against
(MDL_LOOKUP_TYPE, MDL_LOOKUP_VALUE, unchanged names and ranges). Outbound, XM-MDL-001
resolves to ACTIVE immediately: SEC v1 is gated (pass-1 APPROVE) and SEC_MODULE_REG exists.

Event
"P2 completed: MDL v1 — 2 tables, 21 DBF, 1 XM"
══════════════════════════════════════════════════════════════════
