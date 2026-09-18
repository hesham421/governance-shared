<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
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
