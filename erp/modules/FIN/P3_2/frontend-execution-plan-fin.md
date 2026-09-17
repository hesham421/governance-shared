# Frontend execution plan — FIN v1 (خطة تنفيذ الواجهة الأمامية — الحسابات العامة)

Stack : `react-ts-vite` · routing `react-router` · server-state `tanstack-query` · forms
`react-hook-form` · validation `zod` · state `zustand` · one lazy chunk per composite screen
(`profile.stack.frontend`).

Screens (ar/en) : شجرة الحسابات (Chart of accounts) · تعريف الأبعاد وقيمها (Dimensions) ·
قواعد المحرك (Engine rules) · قوالب متكررة/عكسية (Recurring/reversing templates) ·
قواعد التوزيع (Allocation rules) · قيود اليومية (Journal entries) ·
الفترات والسنوات المالية (Fiscal periods & years) · دفتر الحساب (Account ledger) ·
ميزان المراجعة (Trial balance) · الميزانية العمومية (Balance sheet) ·
قائمة الدخل (Income statement) · تقارير الأبعاد (Dimension reports)

## API SURFACE — FIN v1

Shapes: `_inputs/api-docs-fin.md` — cited by `API-FIN-*` id, never restated. The id↔endpoint
binding is `_inputs/api-docs-binding-fin.md` (ADR-FIN-002); id 001–032 are also in
`registry-exec-be-fin.md`, id 033–037 are not yet (ADR-FIN-002 — P3.1's to fix, non-breaking).

```
BINDING     REQ-FIN-001 → API-FIN-002 (create), API-FIN-003 (update, RULE-FIN-001)
            REQ-FIN-002 → API-FIN-002, API-FIN-003 (RULE-FIN-001)
            REQ-FIN-003 → API-FIN-004
            REQ-FIN-004 → API-FIN-006
            REQ-FIN-005 → API-FIN-007, API-FIN-008, API-FIN-035
            REQ-FIN-006 → API-FIN-007 (RULE-FIN-002)
            REQ-FIN-007 → API-FIN-009, API-FIN-010, API-FIN-034
            REQ-FIN-008 → API-FIN-011
            REQ-FIN-009 → API-FIN-011 (RULE-FIN-003)
            REQ-FIN-010 → API-FIN-020
            REQ-FIN-011 → API-FIN-020 (RULE-FIN-004)
            REQ-FIN-012 → API-FIN-020, API-FIN-014, API-FIN-017 (RULE-FIN-010)
            REQ-FIN-013 → API-FIN-020 (RULE-FIN-005)
            REQ-FIN-014 → API-FIN-019
            REQ-FIN-015 → API-FIN-019
            REQ-FIN-016 → API-FIN-022
            REQ-FIN-017 → API-FIN-019, API-FIN-020
            REQ-FIN-018 → API-FIN-019, API-FIN-020 (RULE-FIN-006)
            REQ-FIN-019 → API-FIN-019, API-FIN-020 (RULE-FIN-007)
            REQ-FIN-020 → API-FIN-019, API-FIN-020 (RULE-FIN-008)
            REQ-FIN-021 → API-FIN-019, API-FIN-020 (RULE-FIN-009)
            REQ-FIN-022 → API-FIN-012, API-FIN-013, API-FIN-036
            REQ-FIN-023 → API-FIN-014
            REQ-FIN-024 → API-FIN-014
            REQ-FIN-025 → API-FIN-015, API-FIN-016, API-FIN-037
            REQ-FIN-026 → API-FIN-017
            REQ-FIN-027 → API-FIN-018, API-FIN-022
            REQ-FIN-028 → API-FIN-021 (RULE-FIN-011)
            REQ-FIN-029 → API-FIN-021 (RULE-FIN-012)
            REQ-FIN-030 → API-FIN-021 (RULE-FIN-013)
            REQ-FIN-031 → API-FIN-023, API-FIN-033
            REQ-FIN-032 → API-FIN-024
            REQ-FIN-033 → API-FIN-025
            REQ-FIN-034 → API-FIN-026 (RULE-FIN-015)
            REQ-FIN-035 → API-FIN-024 (RULE-FIN-014)
            REQ-FIN-036 → API-FIN-027
            REQ-FIN-037 → API-FIN-026
            REQ-FIN-038 → API-FIN-026
            REQ-FIN-039 → API-FIN-028
            REQ-FIN-040 → API-FIN-029
            REQ-FIN-041 → API-FIN-030
            REQ-FIN-042 → API-FIN-031
            REQ-FIN-043 → API-FIN-032
            REQ-FIN-044 → (onboarding, no screen — traceability matrix)
            REQ-FIN-045 → (onboarding, no screen — traceability matrix)
            REQ-FIN-046 → API-FIN-028, API-FIN-029, API-FIN-030, API-FIN-031 (drill-down chain)
UNMAPPED    none — every REQ with a screen has ≥1 API-FIN-* above; every published endpoint
            has ≥1 REQ above (API-FIN-020 has REQ-FIN-010..013 but no screen call — ADR-FIN-007,
            not an UNMAPPED case: it is mapped, only not drawn on a form)
CODES       FIN-409-DIMVALUE-DUP → RULE-FIN-002 · FIN-409-RULE-DUP → REQ-FIN-007 (§6.4, one rule per type — no numbered RULE-FIN-*)
            FIN-409-REMAINDER-COUNT → RULE-FIN-003 · FIN-409-PARENT-NOT-LEAF-ELIGIBLE → RULE-FIN-001
            FIN-409-HAS-CHILDREN → RULE-FIN-001 · FIN-409-NOT-POSTABLE-ACCOUNT → RULE-FIN-007
            FIN-409-NOT-REOPENABLE → RULE-FIN-014 · FIN-403-SOD-VIOLATION → unreachable, removed by decision (SRS Access summary SoD note)
            FIN-409-UNBALANCED → RULE-FIN-006 · FIN-409-PERIOD-NOT-OPEN → RULE-FIN-008
            FIN-409-INVALID-DIMENSION → RULE-FIN-009 · FIN-409-DUPLICATE-EVENT → RULE-FIN-004
            FIN-409-NOT-POSTED → RULE-FIN-013 · FIN-409-INVALID-TRANSITION → RULE-FIN-014
            FIN-404-ENTRY/ACCOUNT/DIMENSION/DIMVALUE/RULE/YEAR/PERIOD/ALLOCATION-RULE/TEMPLATE → not-found (data lookup, no RULE)
            FIN-400-INVALID-LOOKUP → validation (bad lookup code) · FIN-400-INVALID-SORT → validation (generic)
            FIN-409-ACCOUNT-DUP/DIMENSION-DUP/YEAR-DUP → uniqueness (data integrity, no numbered RULE-FIN-*)
            FIN-400-MISSING-FREQUENCY → ENT-FIN-011 field rule (frequencyCode required when RECURRING)
            FIN-404-NO-ACTIVE-RULE → RULE-FIN-005 · FIN-409-PERIODS-NOT-CLOSED → REQ-FIN-036 precondition
            FIN-422-MAPPING-UNSUPPORTED → REQ-FIN-008/ENT-FIN-010 (MAPPING derivation not yet supported)
            FIN-409-ALREADY-REVERSED → RULE-FIN-013 · FIN-422-REMAINDER-MARKER → RULE-FIN-003
            FIN-422-REMAINDER-NOT-POSITIVE → RULE-FIN-010 · FIN-422-INVALID-PERCENTAGE-VALUE → RULE-FIN-010
            FIN-400-PERIOD-NOT-IN-YEAR → RULE-FIN-017 · FIN-400-DOCDATE-OUTSIDE-PERIOD → RULE-FIN-017
            FIN-403-FORBIDDEN → SEC-enforced permission denial (ADR-FIN-005, not a FIN RULE)
            FIN-409-NOT-ACTIVE → recorded decision, not a RULE-FIN-* (SRS §B4 "DEFECT CLOSED 2026-09-12")
```

<!-- PHASE:F1:START traces=SCR-FIN-001,SCR-FIN-002,SCR-FIN-003,SCR-FIN-004,SCR-FIN-005,SCR-FIN-006,SCR-FIN-007,SCR-FIN-008,SCR-FIN-009,SCR-FIN-010,SCR-FIN-011,SCR-FIN-012 -->
## PHASE F1 — Models & Types

**RF1 — Models & types.** Field/DTO binding: see `_inputs/api-docs-fin.md` — the published
request/response shapes are the source, not restated here. Every lookup-coded field types as
`string` everywhere in the models (ADR-FIN-004) — never an enum, never a union of literals —
so a value MDL has not yet seeded is still assignable to the type; validity is a runtime
concern (§F3), not a type concern.

<!-- SUB:F1-SCR-FIN-001:START traces=SCR-FIN-001,API-FIN-001,API-FIN-002,API-FIN-003,API-FIN-004 -->
### F1-SCR-FIN-001 — Chart of accounts
Types : `Account` (`AccountResponse`), `AccountCreateRequest`, `AccountUpdateRequest`, `AccountSearchRequest` — api-docs binding API-FIN-001..004
<!-- SUB:F1-SCR-FIN-001:END -->

<!-- SUB:F1-SCR-FIN-002:START traces=SCR-FIN-002,API-FIN-005,API-FIN-006,API-FIN-007,API-FIN-008,API-FIN-035 -->
### F1-SCR-FIN-002 — Dimensions
Types : `Dimension` (`DimensionResponse`), `DimensionCreateRequest`, `DimensionValue` (`DimensionValueResponse`), `DimensionValueCreateRequest`, `DimensionSearchRequest`, `DimensionValueSearchRequest` — api-docs binding API-FIN-005..008,035
<!-- SUB:F1-SCR-FIN-002:END -->

<!-- SUB:F1-SCR-FIN-003:START traces=SCR-FIN-003,API-FIN-009,API-FIN-010,API-FIN-011,API-FIN-034 -->
### F1-SCR-FIN-003 — Engine rules
Types : `EventTypeRule` (`EventTypeRuleResponse`), `EventTypeRuleCreateRequest`, `RuleLine` (embedded in response), `RuleLineCreateRequest`, `EventTypeRuleSearchRequest` — api-docs binding API-FIN-009..011,034
<!-- SUB:F1-SCR-FIN-003:END -->

<!-- SUB:F1-SCR-FIN-004:START traces=SCR-FIN-004,API-FIN-012,API-FIN-013,API-FIN-014,API-FIN-036 -->
### F1-SCR-FIN-004 — Recurring/reversing templates
Types : `RecurringTemplate` (`RecurringTemplateResponse`, lines included), `RecurringTemplateCreateRequest`, `RecurringTemplateSearchRequest` — api-docs binding API-FIN-012..014,036
<!-- SUB:F1-SCR-FIN-004:END -->

<!-- SUB:F1-SCR-FIN-005:START traces=SCR-FIN-005,API-FIN-015,API-FIN-016,API-FIN-017,API-FIN-037 -->
### F1-SCR-FIN-005 — Allocation rules
Types : `AllocationRule` (`AllocationRuleResponse`, targets included), `AllocationRuleCreateRequest`, `AllocationRuleSearchRequest` — api-docs binding API-FIN-015..017,037
<!-- SUB:F1-SCR-FIN-005:END -->

<!-- SUB:F1-SCR-FIN-006:START traces=SCR-FIN-006,API-FIN-018,API-FIN-019,API-FIN-021,API-FIN-022 -->
### F1-SCR-FIN-006 — Journal entries
Types : `JournalEntry` (`JournalEntryResponse`, lines + line-dimensions included), `JournalEntryCreateRequest`, `JournalEntrySearchRequest` — api-docs binding API-FIN-018,019,021,022. `EventEntryBuildRequest`/API-FIN-020 is bound (ADR-FIN-007) but its type is not consumed by any form model — no screen constructs it.
<!-- SUB:F1-SCR-FIN-006:END -->

<!-- SUB:F1-SCR-FIN-007:START traces=SCR-FIN-007,API-FIN-023,API-FIN-024,API-FIN-025,API-FIN-026,API-FIN-027,API-FIN-033 -->
### F1-SCR-FIN-007 — Fiscal periods & years
Types : `FiscalYear` (`FiscalYearResponse`, periods included), `FiscalYearCreateRequest`, `FiscalPeriod` (`FiscalPeriodResponse`), `FiscalPeriodSearchRequest` — api-docs binding API-FIN-023..027,033
<!-- SUB:F1-SCR-FIN-007:END -->

<!-- SUB:F1-SCR-FIN-008:START traces=SCR-FIN-008,API-FIN-028 -->
### F1-SCR-FIN-008 — Account ledger
Types : `AccountLedgerResponse` (rows embedded) — api-docs binding API-FIN-028
<!-- SUB:F1-SCR-FIN-008:END -->

<!-- SUB:F1-SCR-FIN-009:START traces=SCR-FIN-009,API-FIN-029 -->
### F1-SCR-FIN-009 — Trial balance
Types : `TrialBalanceResponse` (rows embedded) — api-docs binding API-FIN-029
<!-- SUB:F1-SCR-FIN-009:END -->

<!-- SUB:F1-SCR-FIN-010:START traces=SCR-FIN-010,API-FIN-030 -->
### F1-SCR-FIN-010 — Balance sheet
Types : `BalanceSheetResponse` (groups/rows embedded) — api-docs binding API-FIN-030
<!-- SUB:F1-SCR-FIN-010:END -->

<!-- SUB:F1-SCR-FIN-011:START traces=SCR-FIN-011,API-FIN-031 -->
### F1-SCR-FIN-011 — Income statement
Types : `IncomeStatementResponse` (groups/rows embedded) — api-docs binding API-FIN-031
<!-- SUB:F1-SCR-FIN-011:END -->

<!-- SUB:F1-SCR-FIN-012:START traces=SCR-FIN-012,API-FIN-032 -->
### F1-SCR-FIN-012 — Dimension reports
Types : `DimensionReportResponse` (rows embedded) — api-docs binding API-FIN-032
<!-- SUB:F1-SCR-FIN-012:END -->
<!-- PHASE:F1:END -->

<!-- PHASE:F2:START traces=SCR-FIN-001,SCR-FIN-002,SCR-FIN-003,SCR-FIN-004,SCR-FIN-005,SCR-FIN-006,SCR-FIN-007,SCR-FIN-008,SCR-FIN-009,SCR-FIN-010,SCR-FIN-011,SCR-FIN-012 -->
## PHASE F2 — Data Hooks

**RF2 — Data hooks.** State rule: page and page size live inside the filter object that
forms the cache key. Components use the facade only; the facade uses the declared queries
only.

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

<!-- SUB:F2-SCR-FIN-008:START traces=SCR-FIN-008,API-FIN-028,REQ-FIN-039,REQ-FIN-046 -->
### F2-SCR-FIN-008 — Account ledger
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-008-REPORT-QUERY | read · API-FIN-028 | [account-ledger, accountId, fromDate, toDate, dimensionId, dimensionValueId] | FIN-404-ACCOUNT → generic | LOCAL | — |
| F2-SCR-FIN-008-LOOKUP | UXD-FIN-001, UXD-FIN-002, UXD-FIN-005 | three [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-008-SCREEN-INIT | SCR-FIN-008 | permission read + three lookups | — | GLOBAL | — |
| F2-SCR-FIN-008-FACADE | SCR-FIN-008 | composes REPORT-QUERY · state: filters (incl. drill-in accountId carried from another screen's row), derived loading |
<!-- SUB:F2-SCR-FIN-008:END -->

<!-- SUB:F2-SCR-FIN-009:START traces=SCR-FIN-009,API-FIN-029,REQ-FIN-040,REQ-FIN-046 -->
### F2-SCR-FIN-009 — Trial balance
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-009-REPORT-QUERY | read · API-FIN-029 | [trial-balance, periodId, accountTypeCode] | FIN-404-PERIOD → generic | LOCAL | — |
| F2-SCR-FIN-009-LOOKUP | UXD-FIN-001, UXD-FIN-002 | two [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-009-SCREEN-INIT | SCR-FIN-009 | permission read + two lookups | — | GLOBAL | — |
| F2-SCR-FIN-009-FACADE | SCR-FIN-009 | composes REPORT-QUERY · state: filters, derived loading |
<!-- SUB:F2-SCR-FIN-009:END -->

<!-- SUB:F2-SCR-FIN-010:START traces=SCR-FIN-010,API-FIN-030,REQ-FIN-041,REQ-FIN-046 -->
### F2-SCR-FIN-010 — Balance sheet
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-010-REPORT-QUERY | read · API-FIN-030 | [balance-sheet, fiscalYearId, asOfDate] | FIN-404-YEAR → generic | LOCAL | — |
| F2-SCR-FIN-010-LOOKUP | UXD-FIN-001, UXD-FIN-002 | two [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-010-SCREEN-INIT | SCR-FIN-010 | permission read + two lookups | — | GLOBAL | — |
| F2-SCR-FIN-010-FACADE | SCR-FIN-010 | composes REPORT-QUERY · state: filters, derived loading |
<!-- SUB:F2-SCR-FIN-010:END -->

<!-- SUB:F2-SCR-FIN-011:START traces=SCR-FIN-011,API-FIN-031,REQ-FIN-042,REQ-FIN-046 -->
### F2-SCR-FIN-011 — Income statement
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-011-REPORT-QUERY | read · API-FIN-031 | [income-statement, fiscalYearId, fromPeriodId, toPeriodId] | FIN-404-YEAR / FIN-404-PERIOD → generic | LOCAL | — |
| F2-SCR-FIN-011-LOOKUP | UXD-FIN-001, UXD-FIN-002 | two [lookup, <key>] — long-lived | — | NONE | — |
| F2-SCR-FIN-011-SCREEN-INIT | SCR-FIN-011 | permission read + two lookups | — | GLOBAL | — |
| F2-SCR-FIN-011-FACADE | SCR-FIN-011 | composes REPORT-QUERY · state: filters, derived loading |
<!-- SUB:F2-SCR-FIN-011:END -->

<!-- SUB:F2-SCR-FIN-012:START traces=SCR-FIN-012,API-FIN-032,REQ-FIN-043 -->
### F2-SCR-FIN-012 — Dimension reports
| Hook | Kind · API | Cache key | Errors | Loading | Invalidation |
|---|---|---|---|---|---|
| F2-SCR-FIN-012-REPORT-QUERY | read · API-FIN-032 | [dimension-report, dimensionId, dimensionValueId, periodId] | server → generic | LOCAL | — |
| F2-SCR-FIN-012-LOOKUP | UXD-FIN-002 | [lookup, DEBIT_CREDIT] — long-lived | — | NONE | — |
| F2-SCR-FIN-012-SCREEN-INIT | SCR-FIN-012 | permission read + the lookup | — | GLOBAL | — |
| F2-SCR-FIN-012-FACADE | SCR-FIN-012 | composes REPORT-QUERY · state: filters, derived loading |
<!-- SUB:F2-SCR-FIN-012:END -->
<!-- PHASE:F2:END -->

<!-- PHASE:F3:START traces=SCR-FIN-001,SCR-FIN-002,SCR-FIN-003,SCR-FIN-004,SCR-FIN-005,SCR-FIN-006,SCR-FIN-007,SCR-FIN-008,SCR-FIN-009,SCR-FIN-010,SCR-FIN-011,SCR-FIN-012 -->
## PHASE F3 — Forms & Validators

No content role in `factory.yaml` names this phase (§3.2); filled per the profile's own
convention: one `zod` schema per entry/filter form, field constraints bound to the
published request DTO (`_inputs/api-docs-fin.md`), business-rule checks that a form can
usefully pre-empt named against their `RULE-FIN-*`, and the rest left to the server.

<!-- SUB:F3-SCR-FIN-001:START traces=SCR-FIN-001,REQ-FIN-001,REQ-FIN-002,RULE-FIN-001 -->
### F3-SCR-FIN-001 — Chart of accounts
`accountFormSchema` : code (required, ≤30) · nameAr/nameEn (required, ≤200) · accountTypeCode/natureCode (required, member of the runtime-loaded UXD-FIN-001/002 option list) · parentAccountId (optional) · isLeafFl (boolean; disabled and forced false client-side once the account has ≥1 child in the loaded tree — a UX pre-empt of RULE-FIN-001, server remains authoritative)
<!-- SUB:F3-SCR-FIN-001:END -->

<!-- SUB:F3-SCR-FIN-002:START traces=SCR-FIN-002,REQ-FIN-004,REQ-FIN-005,RULE-FIN-002 -->
### F3-SCR-FIN-002 — Dimensions
`dimensionFormSchema` : code (required, ≤30) · nameAr/nameEn (required, ≤150)
`dimensionValueFormSchema` : code (required; client-side uniqueness pre-check against the loaded values of the selected dimension — UX pre-empt of RULE-FIN-002) · nameAr/nameEn (required) · sortOrder (required, integer)
<!-- SUB:F3-SCR-FIN-002:END -->

<!-- SUB:F3-SCR-FIN-003:START traces=SCR-FIN-003,REQ-FIN-007,REQ-FIN-008,REQ-FIN-009,RULE-FIN-003 -->
### F3-SCR-FIN-003 — Engine rules
`eventTypeRuleFormSchema` : eventTypeCode (required, ≤50, member of UXD-FIN-007) · nameAr/nameEn (required, ≤150)
`ruleLineFormSchema` : accountDerivationTypeCode (required, member of UXD-FIN-008) · accountDerivationValue (required) · amountSourceTypeCode (required, member of UXD-FIN-009) · amountSourceValue (required unless amountSourceTypeCode=REMAINDER) · directionCode (required, member of UXD-FIN-002) · distributionTypeCode (required, member of UXD-FIN-010) · isRemainderFl (boolean) — cross-field: exactly one line of the set may carry isRemainderFl=true when any sibling is PERCENTAGE, checked across the whole line list before submit (UX pre-empt of RULE-FIN-003)
<!-- SUB:F3-SCR-FIN-003:END -->

<!-- SUB:F3-SCR-FIN-004:START traces=SCR-FIN-004,REQ-FIN-022,RULE-FIN-006 -->
### F3-SCR-FIN-004 — Recurring/reversing templates
`recurringTemplateFormSchema` : nameAr/nameEn (required, ≤150) · scheduleTypeCode (required, member of UXD-FIN-011) · frequencyCode (required only when scheduleTypeCode=RECURRING, member of UXD-FIN-012 — cross-field, pre-empts FIN-400-MISSING-FREQUENCY) · startDate (required) · endDate (optional)
`recurringTemplateLineFormSchema` : accountId (required) · amount (required, > 0) · directionCode (required, member of UXD-FIN-002) · dimensionValueId (optional) — the set's debit=credit balance (RULE-FIN-006 reused) is checked before submit as a UX pre-empt; the server re-checks at run time
<!-- SUB:F3-SCR-FIN-004:END -->

<!-- SUB:F3-SCR-FIN-005:START traces=SCR-FIN-005,REQ-FIN-025,RULE-FIN-003 -->
### F3-SCR-FIN-005 — Allocation rules
`allocationRuleFormSchema` : nameAr/nameEn (required, ≤150) · sourceAccountId (required)
`allocationTargetFormSchema` : targetAccountId (required) · dimensionValueId (optional) · distributionTypeCode (required, member of UXD-FIN-010) · distributionValue (required unless distributionTypeCode=REMAINDER) · isRemainderFl (boolean) — same cross-field remainder check as F3-SCR-FIN-003 (RULE-FIN-003)
<!-- SUB:F3-SCR-FIN-005:END -->

<!-- SUB:F3-SCR-FIN-006:START traces=SCR-FIN-006,REQ-FIN-014,REQ-FIN-017,REQ-FIN-018,RULE-FIN-006,RULE-FIN-017 -->
### F3-SCR-FIN-006 — Journal entries
`manualEntryFormSchema` : docDate (required) · fiscalYearId (required) · periodId (required; option list filtered by the chosen fiscalYearId — UX pre-empt of RULE-FIN-017) · journalTypeCode (fixed `MANUAL`, not a validated input field — ADR-FIN-008) · descriptionAr/descriptionEn (optional) · lines (min 2 — a balanced entry needs at least a debit and a credit side)
`journalLineFormSchema` : accountId (required) · amount (required, > 0) · directionCode (required, member of UXD-FIN-002) · dimensions[] (dimensionId+dimensionValueId pairs, optional) · descriptionAr/descriptionEn (optional) — cross-field: Σdebit = Σcredit checked across `lines` before submit (UX pre-empt of RULE-FIN-006), server remains authoritative
`searchFilterSchema` : docNo (LIKE) · docDate range · periodId · statusCode (member of UXD-FIN-006) · journalTypeCode (member of UXD-FIN-005)
<!-- SUB:F3-SCR-FIN-006:END -->

<!-- SUB:F3-SCR-FIN-007:START traces=SCR-FIN-007,REQ-FIN-031,REQ-FIN-034 -->
### F3-SCR-FIN-007 — Fiscal periods & years
`fiscalYearFormSchema` : code (required, ≤10) · startDate/endDate (required, endDate > startDate) · periodCount (required, positive integer)
Row actions (open/soft-close/hard-close/year-end-close) take no form input beyond the row id — the affordance itself is the "form".
<!-- SUB:F3-SCR-FIN-007:END -->

<!-- SUB:F3-SCR-FIN-008:START traces=SCR-FIN-008,REQ-FIN-039 -->
### F3-SCR-FIN-008 — Account ledger
`ledgerFilterSchema` : accountId (required) · fromDate/toDate (optional range) · dimensionId/dimensionValueId (optional pair — dimensionValueId requires dimensionId)
<!-- SUB:F3-SCR-FIN-008:END -->

<!-- SUB:F3-SCR-FIN-009:START traces=SCR-FIN-009,REQ-FIN-040 -->
### F3-SCR-FIN-009 — Trial balance
`trialBalanceFilterSchema` : periodId (optional) · accountTypeCode (optional, member of UXD-FIN-001)
<!-- SUB:F3-SCR-FIN-009:END -->

<!-- SUB:F3-SCR-FIN-010:START traces=SCR-FIN-010,REQ-FIN-041 -->
### F3-SCR-FIN-010 — Balance sheet
`balanceSheetFilterSchema` : fiscalYearId (required) · asOfDate (optional)
<!-- SUB:F3-SCR-FIN-010:END -->

<!-- SUB:F3-SCR-FIN-011:START traces=SCR-FIN-011,REQ-FIN-042 -->
### F3-SCR-FIN-011 — Income statement
`incomeStatementFilterSchema` : fiscalYearId (required) · fromPeriodId/toPeriodId (optional pair)
<!-- SUB:F3-SCR-FIN-011:END -->

<!-- SUB:F3-SCR-FIN-012:START traces=SCR-FIN-012,REQ-FIN-043 -->
### F3-SCR-FIN-012 — Dimension reports
`dimensionReportFilterSchema` : dimensionId (required) · dimensionValueId (optional) · periodId (optional)
<!-- SUB:F3-SCR-FIN-012:END -->
<!-- PHASE:F3:END -->

<!-- PHASE:F4:START traces=SCR-FIN-001,SCR-FIN-002,SCR-FIN-003,SCR-FIN-004,SCR-FIN-005,SCR-FIN-006,SCR-FIN-007,SCR-FIN-008,SCR-FIN-009,SCR-FIN-010,SCR-FIN-011,SCR-FIN-012 -->
## PHASE F4 — Screens & Routes

**RF4 — Screens & routes.** Every route element is guarded by its page code being present
in the effective menu the security module serves (ADR-FIN-005) — never a composed
permission string read client-side. No route is drawn for an operation with no published
endpoint (ADR-FIN-006).

<!-- SUB:F4-SCR-FIN-001:START traces=SCR-FIN-001,REQ-FIN-001,REQ-FIN-002,REQ-FIN-003,API-FIN-001,API-FIN-002,API-FIN-003,API-FIN-004 -->
### F4-SCR-FIN-001-SCREEN — Chart of accounts
Routes       : `/fin/accounts` (search + inline entry — Composite screen, no `:id` route: entry hydrates from the search row, ADR-FIN-006)
Guard        : `FIN_ACCOUNTS` present in the effective menu (ADR-FIN-005)
Facade       : F2-SCR-FIN-001-FACADE · page never calls the queries/mutations directly
Cross-module : UXD-FIN-001, UXD-FIN-002 (lookup labels only) — no foreign business data displayed
<!-- SUB:F4-SCR-FIN-001:END -->

<!-- SUB:F4-SCR-FIN-002:START traces=SCR-FIN-002,REQ-FIN-004,REQ-FIN-005,REQ-FIN-006,API-FIN-005,API-FIN-006,API-FIN-007,API-FIN-008,API-FIN-035 -->
### F4-SCR-FIN-002-SCREEN — Dimensions
Routes       : `/fin/dimensions` (master list + detail values panel, no `:id` route — ADR-FIN-006)
Guard        : `FIN_DIMENSIONS` present in the effective menu
Facade       : F2-SCR-FIN-002-FACADE
Cross-module : none
<!-- SUB:F4-SCR-FIN-002:END -->

<!-- SUB:F4-SCR-FIN-003:START traces=SCR-FIN-003,REQ-FIN-007,REQ-FIN-008,REQ-FIN-009,API-FIN-009,API-FIN-010,API-FIN-011,API-FIN-034 -->
### F4-SCR-FIN-003-SCREEN — Engine rules
Routes       : `/fin/rules` (master list + detail lines panel, no `:id` route — ADR-FIN-006; no line-delete affordance — ADR-FIN-006)
Guard        : `FIN_RULES` present in the effective menu
Facade       : F2-SCR-FIN-003-FACADE
Cross-module : UXD-FIN-007, UXD-FIN-008, UXD-FIN-009, UXD-FIN-002, UXD-FIN-010
<!-- SUB:F4-SCR-FIN-003:END -->

<!-- SUB:F4-SCR-FIN-004:START traces=SCR-FIN-004,REQ-FIN-022,REQ-FIN-023,REQ-FIN-024,API-FIN-012,API-FIN-013,API-FIN-014,API-FIN-036 -->
### F4-SCR-FIN-004-SCREEN — Recurring/reversing templates
Routes       : `/fin/recurring-templates` (master + detail, no `:id` route — ADR-FIN-006; no Edit affordance — update not published, ADR-FIN-006)
Guard        : `FIN_RECURRING_TEMPLATES` present in the effective menu
Facade       : F2-SCR-FIN-004-FACADE
Cross-module : UXD-FIN-011, UXD-FIN-012, UXD-FIN-002
<!-- SUB:F4-SCR-FIN-004:END -->

<!-- SUB:F4-SCR-FIN-005:START traces=SCR-FIN-005,REQ-FIN-025,REQ-FIN-026,API-FIN-015,API-FIN-016,API-FIN-017,API-FIN-037 -->
### F4-SCR-FIN-005-SCREEN — Allocation rules
Routes       : `/fin/allocation-rules` (master + detail, no `:id` route — ADR-FIN-006; no Edit affordance — update not published, ADR-FIN-006)
Guard        : `FIN_ALLOCATION_RULES` present in the effective menu
Facade       : F2-SCR-FIN-005-FACADE
Cross-module : UXD-FIN-010
<!-- SUB:F4-SCR-FIN-005:END -->

<!-- SUB:F4-SCR-FIN-006:START traces=SCR-FIN-006,REQ-FIN-014,REQ-FIN-016,REQ-FIN-017,REQ-FIN-027,REQ-FIN-028,API-FIN-018,API-FIN-019,API-FIN-021,API-FIN-022 -->
### F4-SCR-FIN-006-SCREEN — Journal entries
Routes       : `/fin/journal-entries` (search) · `/fin/journal-entries/new` (manual entry) · `/fin/journal-entries/:id` (read + Reverse action on a POSTED row)
Guard        : `FIN_JOURNAL_ENTRIES` present in the effective menu; `/new` additionally requires the CREATE affordance, Reverse the custom `PERM_FIN_JOURNAL_ENTRIES_REVERSE` affordance (both rendered by ADR-FIN-005's own convention — presence check only, no client-composed permission string)
Facade       : F2-SCR-FIN-006-FACADE
Cross-module : UXD-FIN-005, UXD-FIN-006, UXD-FIN-002
<!-- SUB:F4-SCR-FIN-006:END -->

<!-- SUB:F4-SCR-FIN-007:START traces=SCR-FIN-007,REQ-FIN-031,REQ-FIN-034,REQ-FIN-036,REQ-FIN-037,API-FIN-023,API-FIN-024,API-FIN-025,API-FIN-026,API-FIN-027,API-FIN-033 -->
### F4-SCR-FIN-007-SCREEN — Fiscal periods & years
Routes       : `/fin/periods` (master years + detail periods, no `:id` route, no fiscal-year search — ADR-FIN-006)
Guard        : `FIN_PERIODS` present in the effective menu; hard-close/year-end-close affordances additionally require the custom Close-approve presence flag (RULE-FIN-015, ADR-FIN-005 — the separation of duty is server-enforced, not hidden by this guard alone)
Facade       : F2-SCR-FIN-007-FACADE
Cross-module : UXD-FIN-003, UXD-FIN-004
<!-- SUB:F4-SCR-FIN-007:END -->

<!-- SUB:F4-SCR-FIN-008:START traces=SCR-FIN-008,REQ-FIN-039,REQ-FIN-046,API-FIN-028 -->
### F4-SCR-FIN-008-SCREEN — Account ledger
Routes       : `/fin/reports/account-ledger` (FULL_PAGE, no entry sub-view — ADR-FIN-003); filter state mirrored into route search params so a filtered report and a drill-in from another screen are both linkable
Guard        : `FIN_ACCOUNT_LEDGER` present in the effective menu
Facade       : F2-SCR-FIN-008-FACADE
Cross-module : UXD-FIN-001, UXD-FIN-002, UXD-FIN-005
<!-- SUB:F4-SCR-FIN-008:END -->

<!-- SUB:F4-SCR-FIN-009:START traces=SCR-FIN-009,REQ-FIN-040,REQ-FIN-046,API-FIN-029 -->
### F4-SCR-FIN-009-SCREEN — Trial balance
Routes       : `/fin/reports/trial-balance` (FULL_PAGE, no entry sub-view — ADR-FIN-003); filter state in route search params
Guard        : `FIN_TRIAL_BALANCE` present in the effective menu
Facade       : F2-SCR-FIN-009-FACADE
Cross-module : UXD-FIN-001, UXD-FIN-002
<!-- SUB:F4-SCR-FIN-009:END -->

<!-- SUB:F4-SCR-FIN-010:START traces=SCR-FIN-010,REQ-FIN-041,REQ-FIN-046,API-FIN-030 -->
### F4-SCR-FIN-010-SCREEN — Balance sheet
Routes       : `/fin/reports/balance-sheet` (FULL_PAGE, no entry sub-view — ADR-FIN-003); filter state in route search params
Guard        : `FIN_BALANCE_SHEET` present in the effective menu
Facade       : F2-SCR-FIN-010-FACADE
Cross-module : UXD-FIN-001, UXD-FIN-002
<!-- SUB:F4-SCR-FIN-010:END -->

<!-- SUB:F4-SCR-FIN-011:START traces=SCR-FIN-011,REQ-FIN-042,REQ-FIN-046,API-FIN-031 -->
### F4-SCR-FIN-011-SCREEN — Income statement
Routes       : `/fin/reports/income-statement` (FULL_PAGE, no entry sub-view — ADR-FIN-003); filter state in route search params
Guard        : `FIN_INCOME_STATEMENT` present in the effective menu
Facade       : F2-SCR-FIN-011-FACADE
Cross-module : UXD-FIN-001, UXD-FIN-002
<!-- SUB:F4-SCR-FIN-011:END -->

<!-- SUB:F4-SCR-FIN-012:START traces=SCR-FIN-012,REQ-FIN-043,API-FIN-032 -->
### F4-SCR-FIN-012-SCREEN — Dimension reports
Routes       : `/fin/reports/dimension` (FULL_PAGE, no entry sub-view — ADR-FIN-003); filter state in route search params
Guard        : `FIN_DIMENSION_REPORTS` present in the effective menu
Facade       : F2-SCR-FIN-012-FACADE
Cross-module : UXD-FIN-002
<!-- SUB:F4-SCR-FIN-012:END -->
<!-- PHASE:F4:END -->

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

<!-- PHASE:ALIGN-FE:START traces=SCR-FIN-001,SCR-FIN-002,SCR-FIN-003,SCR-FIN-004,SCR-FIN-005,SCR-FIN-006,SCR-FIN-007,SCR-FIN-008,SCR-FIN-009,SCR-FIN-010,SCR-FIN-011,SCR-FIN-012 -->
## PHASE ALIGN-FE — Alignment

**RF6 — Alignment.**

```
ALIGN — FIN v1
row           backing check   assertion
SCREENS       orphans         every SCR is referenced by a plan block
UXD           orphans         every UXD is cited by a plan block — this is where a UX decision closes
TRACES        traces          every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces          every API this plan cites is defined in the fetched api-docs — never in the backend plan's contract draft
FOREIGN       xref-surface    every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree  every UXD and SCR defined here is in the stage registry, and nothing else is
LANGUAGES     languages       labels and messages in ar + en
MARKERS       markers         the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist      every ADR this plan cites exists on disk in erp/decisions/FIN/
COVERAGE      (the report)    the clauses the analyze report lists as having examined nothing — verbatim, or `none`
RESULT    PASSED ✓ — 0 findings
```

Operations coverage table (operation │ API │ SCR action │ route │ status):

| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| search accounts | API-FIN-001 | SCR-FIN-001 view | /fin/accounts | ✓ |
| create account | API-FIN-002 | SCR-FIN-001 create | /fin/accounts | ✓ |
| update account | API-FIN-003 | SCR-FIN-001 update | /fin/accounts | ✓ |
| deactivate account | API-FIN-004 | SCR-FIN-001 deactivate | /fin/accounts | ✓ |
| search dimensions | API-FIN-005 | SCR-FIN-002 view | /fin/dimensions | ✓ |
| create dimension | API-FIN-006 | SCR-FIN-002 create | /fin/dimensions | ✓ |
| create dimension value | API-FIN-007 | SCR-FIN-002 create | /fin/dimensions | ✓ |
| search dimension values | API-FIN-008 | SCR-FIN-002 view | /fin/dimensions | ✓ |
| search event-type rules | API-FIN-009 | SCR-FIN-003 view | /fin/rules | ✓ |
| create event-type rule | API-FIN-010 | SCR-FIN-003 create | /fin/rules | ✓ |
| add rule line | API-FIN-011 | SCR-FIN-003 update | /fin/rules | ✓ |
| search recurring templates | API-FIN-012 | SCR-FIN-004 view | /fin/recurring-templates | ✓ |
| create recurring template | API-FIN-013 | SCR-FIN-004 create | /fin/recurring-templates | ✓ |
| run recurring template | API-FIN-014 | SCR-FIN-004 update | /fin/recurring-templates | ✓ |
| search allocation rules | API-FIN-015 | SCR-FIN-005 view | /fin/allocation-rules | ✓ |
| create allocation rule | API-FIN-016 | SCR-FIN-005 create | /fin/allocation-rules | ✓ |
| run allocation rule | API-FIN-017 | SCR-FIN-005 update | /fin/allocation-rules | ✓ |
| search journal entries | API-FIN-018 | SCR-FIN-006 view | /fin/journal-entries | ✓ |
| create manual journal entry | API-FIN-019 | SCR-FIN-006 create | /fin/journal-entries/new | ✓ |
| build journal entry from event | API-FIN-020 | none (system caller, ADR-FIN-007) | — | ✗ (ADR-FIN-007) |
| reverse journal entry | API-FIN-021 | SCR-FIN-006 update | /fin/journal-entries/:id | ✓ |
| read journal entry | API-FIN-022 | SCR-FIN-006 view | /fin/journal-entries/:id | ✓ |
| create fiscal year | API-FIN-023 | SCR-FIN-007 create | /fin/periods | ✓ |
| open fiscal period | API-FIN-024 | SCR-FIN-007 update | /fin/periods | ✓ |
| soft-close fiscal period | API-FIN-025 | SCR-FIN-007 update | /fin/periods | ✓ |
| hard-close fiscal period | API-FIN-026 | SCR-FIN-007 close-approve | /fin/periods | ✓ |
| run year-end close | API-FIN-027 | SCR-FIN-007 close-approve | /fin/periods | ✓ |
| account ledger | API-FIN-028 | SCR-FIN-008 view | /fin/reports/account-ledger | ✓ |
| trial balance | API-FIN-029 | SCR-FIN-009 view | /fin/reports/trial-balance | ✓ |
| balance sheet | API-FIN-030 | SCR-FIN-010 view | /fin/reports/balance-sheet | ✓ |
| income statement | API-FIN-031 | SCR-FIN-011 view | /fin/reports/income-statement | ✓ |
| dimension report | API-FIN-032 | SCR-FIN-012 view | /fin/reports/dimension | ✓ |
| search fiscal periods | API-FIN-033 | SCR-FIN-007 view | /fin/periods | ✓ |
| deactivate event-type rule | API-FIN-034 | SCR-FIN-003 update | /fin/rules | ✓ |
| deactivate dimension value | API-FIN-035 | SCR-FIN-002 update | /fin/dimensions | ✓ |
| deactivate recurring template | API-FIN-036 | SCR-FIN-004 update | /fin/recurring-templates | ✓ |
| deactivate allocation rule | API-FIN-037 | SCR-FIN-005 update | /fin/allocation-rules | ✓ |
| update recurring template | (not published) | — | — | ✗ (ADR-FIN-006) |
| update allocation rule | (not published) | — | — | ✗ (ADR-FIN-006) |
| search fiscal years | (not published) | — | — | ✗ (ADR-FIN-006) |
| deactivate dimension (parent) | (not published) | — | — | ✗ (ADR-FIN-006) |
| delete rule line / template line / allocation target | (not published) | — | — | ✗ (ADR-FIN-006) |
<!-- PHASE:ALIGN-FE:END -->
