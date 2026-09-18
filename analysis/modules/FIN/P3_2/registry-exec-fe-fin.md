REGISTRY — P3.2 — FIN v1
══════════════════════════════════════════════════════════════════

ID RANGES     UXD-FIN-001..012 · SCR-FIN-001..012

SCREENS
| SCR | Name (ar/en) | Owning ENT | Permissions |
|---|---|---|---|
| SCR-FIN-001 | شجرة الحسابات / Chart of accounts | ENT-FIN-001 | PERM_FIN_ACCOUNTS_VIEW/CREATE/UPDATE |
| SCR-FIN-002 | تعريف الأبعاد وقيمها / Dimension definition & values | ENT-FIN-002, ENT-FIN-003 | PERM_FIN_DIMENSIONS_VIEW/CREATE/UPDATE |
| SCR-FIN-003 | قواعد المحرك / Engine rules | ENT-FIN-009, ENT-FIN-010 | PERM_FIN_RULES_VIEW/CREATE/UPDATE |
| SCR-FIN-004 | قوالب متكررة/عكسية / Recurring/reversing templates | ENT-FIN-011, ENT-FIN-012 | PERM_FIN_RECURRING_TEMPLATES_VIEW/CREATE/UPDATE |
| SCR-FIN-005 | قواعد التوزيع / Allocation rules | ENT-FIN-013, ENT-FIN-014 | PERM_FIN_ALLOCATION_RULES_VIEW/CREATE/UPDATE |
| SCR-FIN-006 | قيود اليومية / Journal entries | ENT-FIN-004, ENT-FIN-005, ENT-FIN-006 | PERM_FIN_JOURNAL_ENTRIES_VIEW/CREATE, PERM_FIN_JOURNAL_ENTRIES_REVERSE |
| SCR-FIN-007 | الفترات والسنوات المالية / Fiscal periods & years | ENT-FIN-007, ENT-FIN-008 | PERM_FIN_PERIODS_VIEW/CREATE/UPDATE, PERM_FIN_PERIODS_CLOSE_APPROVE |
| SCR-FIN-008 | دفتر الحساب / Account ledger | ENT-FIN-005 | PERM_FIN_ACCOUNT_LEDGER_VIEW |
| SCR-FIN-009 | ميزان المراجعة / Trial balance | ENT-FIN-005 | PERM_FIN_TRIAL_BALANCE_VIEW |
| SCR-FIN-010 | الميزانية العمومية / Balance sheet | ENT-FIN-005 | PERM_FIN_BALANCE_SHEET_VIEW |
| SCR-FIN-011 | قائمة الدخل / Income statement | ENT-FIN-005 | PERM_FIN_INCOME_STATEMENT_VIEW |
| SCR-FIN-012 | تقارير الأبعاد / Dimension reports | ENT-FIN-005, ENT-FIN-006 | PERM_FIN_DIMENSION_REPORTS_VIEW |

UXD INDEX
| UXD | Screen(s) | Field | Owner module · API used |
|---|---|---|---|
| UXD-FIN-001 | SCR-FIN-001, 008, 009, 010, 011 | accountTypeCode | MDL · API-MDL-011 |
| UXD-FIN-002 | SCR-FIN-001, 003, 004, 006, 008, 009, 010, 011, 012 | directionCode / natureCode (DEBIT_CREDIT) | MDL · API-MDL-011 |
| UXD-FIN-003 | SCR-FIN-007 | statusCode (PERIOD_STATE) | MDL · API-MDL-011 |
| UXD-FIN-004 | SCR-FIN-007 | statusCode (FISCAL_YEAR_STATUS) | MDL · API-MDL-011 |
| UXD-FIN-005 | SCR-FIN-006, 008 | journalTypeCode | MDL · API-MDL-011 |
| UXD-FIN-006 | SCR-FIN-006 | statusCode (JOURNAL_STATUS) | MDL · API-MDL-011 |
| UXD-FIN-007 | SCR-FIN-003 | eventTypeCode | MDL · API-MDL-011 |
| UXD-FIN-008 | SCR-FIN-003 | accountDerivationTypeCode | MDL · API-MDL-011 |
| UXD-FIN-009 | SCR-FIN-003 | amountSourceTypeCode | MDL · API-MDL-011 |
| UXD-FIN-010 | SCR-FIN-003, 005 | distributionTypeCode | MDL · API-MDL-011 |
| UXD-FIN-011 | SCR-FIN-004 | scheduleTypeCode | MDL · API-MDL-011 |
| UXD-FIN-012 | SCR-FIN-004 | frequencyCode | MDL · API-MDL-011 |

API COVERAGE
37 documented endpoints (API-FIN-001..037, `_inputs/api-docs-binding-fin.md` — ADR-FIN-002).
32 used by a screen action; 1 bound but drawn on no screen (API-FIN-020 — ADR-FIN-007, its
entries are seen and reversed on SCR-FIN-006 like any other source, never invoked from a
form); 4 deactivate endpoints added 2026-09-12 (API-FIN-034..037) used by their screen's
UPDATE action, cited in registry-exec-be's stale 001..032 range not yet regenerated
(ADR-FIN-002, P3.1's to fix). 6 SRS-named operations have no published endpoint and are
omitted, not faked (ADR-FIN-006).

ALIGN      PASSED ✓ · 0 findings (see frontend-execution-plan-fin.md → PHASE ALIGN-FE)
           — findings fixed: n/a (first pass)

ADRs       decisions/FIN/ADR-FIN-002.md (ACCEPTED — api-docs ID binding annex, registry-exec-be stale range)
           decisions/FIN/ADR-FIN-003.md (ACCEPTED — FULL_PAGE container for the five report screens)
           decisions/FIN/ADR-FIN-004.md (ACCEPTED — one UXD per displayed lookup key, via MDL API-MDL-011)
           decisions/FIN/ADR-FIN-005.md (ACCEPTED — screen gating via SEC effective menu, no FIN permission-read endpoint)
           decisions/FIN/ADR-FIN-006.md (ACCEPTED — SRS-named operations with no published endpoint are omitted)
           decisions/FIN/ADR-FIN-007.md (ACCEPTED — API-FIN-020 bound, drawn on no screen)
           decisions/FIN/ADR-FIN-008.md (ACCEPTED — journalTypeCode fixed MANUAL, fiscalYearId a real input)

TRACEABILITY  REQ covered by ≥1 SCR/F-block: 44/46 · orphan REQ: none — REQ-FIN-044,045
              trace to onboarding with no screen by the P0.5 traceability matrix itself,
              not a gap this stage introduces
══════════════════════════════════════════════════════════════════
