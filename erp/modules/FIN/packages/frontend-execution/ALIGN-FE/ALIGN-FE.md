<!-- source: PHASE:ALIGN-FE -->
<!-- traces: SCR-FIN-001, SCR-FIN-002, SCR-FIN-003, SCR-FIN-004, SCR-FIN-005, SCR-FIN-006, SCR-FIN-007, SCR-FIN-008, SCR-FIN-009, SCR-FIN-010, SCR-FIN-011, SCR-FIN-012 -->
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
