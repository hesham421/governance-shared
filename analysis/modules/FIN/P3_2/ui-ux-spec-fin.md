# UI/UX spec — FIN v1

Fields, permissions and states below are copied from the SRS (`srs-fin.md`) with no
addition and no omission, narrowed only where an ADR records a divergence between the
SRS's B1/B3 wording and the published surface. Cross-module display dependencies
(`UXD-*`) are defined once here (§ Cross-module display dependencies) and cited by
screen, never restated.

## SCR-FIN-001 — شجرة الحسابات / Chart of accounts
Traces            : REQ-FIN-001, REQ-FIN-002, REQ-FIN-003, AC-FIN-001
UI pattern        : Search + Entry, ONE screen (composite) — true hierarchy (parent/child); no by-id read, entry hydrates from the search row (ADR-FIN-006)
Sub-views         : Search · Entry
Fields shown      : code, nameAr, nameEn, accountTypeCode (UXD-FIN-001), natureCode (UXD-FIN-002), parentAccountId, isLeafFl, isActiveFl, isRetainedEarningsFl (read-only) — labels ar/en per field, per SRS A3 ENT-FIN-001
Permissions       : FIN_ACCOUNTS — VIEW, CREATE, UPDATE (covers deactivate) — reference only, names owned by SEC/backend registry
Cross-module data : accountTypeCode → UXD-FIN-001 · natureCode → UXD-FIN-002 | none else
States            : empty · loading · error (generic, catalog code FIN-404-ACCOUNT / FIN-409-ACCOUNT-DUP / FIN-409-PARENT-NOT-LEAF-ELIGIBLE / FIN-409-HAS-CHILDREN)

## SCR-FIN-002 — تعريف الأبعاد وقيمها / Dimension definition & values
Traces            : REQ-FIN-004, REQ-FIN-005, REQ-FIN-006, AC-FIN-004
UI pattern        : Master (dimensions) + Detail (values), ONE screen (composite); parent dimension has no deactivate (SRS §B4, deliberate); no by-id read (ADR-FIN-006)
Sub-views         : Search · Entry (master + detail)
Fields shown      : dimension: code, nameAr, nameEn · value: code, nameAr, nameEn, sortOrder, isActiveFl — per SRS A3 ENT-FIN-002/003
Permissions       : FIN_DIMENSIONS — VIEW, CREATE, UPDATE (dimension-value deactivate only, API-FIN-035)
Cross-module data : none
States            : empty · loading · error (FIN-409-DIMENSION-DUP / FIN-409-DIMVALUE-DUP / FIN-404-DIMVALUE)

## SCR-FIN-003 — قواعد المحرك / Engine rules
Traces            : REQ-FIN-007, REQ-FIN-008, REQ-FIN-009, AC-FIN-007
UI pattern        : Master (rules) + Detail (lines), ONE screen (composite); no update and no by-id read on either (SRS §B4); rule-line delete is a v1 exclusion (ADR-FIN-006)
Sub-views         : Search · Entry (master + detail)
Fields shown      : rule: eventTypeCode (UXD-FIN-007), nameAr, nameEn · line: accountDerivationTypeCode (UXD-FIN-008), accountDerivationValue, amountSourceTypeCode (UXD-FIN-009), amountSourceValue, directionCode (UXD-FIN-002), distributionTypeCode (UXD-FIN-010), isRemainderFl — per SRS A3 ENT-FIN-009/010
Permissions       : FIN_RULES — VIEW, CREATE, UPDATE (add line — API-FIN-011; deactivate rule — API-FIN-034)
Cross-module data : eventTypeCode → UXD-FIN-007 · accountDerivationTypeCode → UXD-FIN-008 · amountSourceTypeCode → UXD-FIN-009 · directionCode → UXD-FIN-002 · distributionTypeCode → UXD-FIN-010
States            : empty · loading · error (FIN-409-RULE-DUP / FIN-409-REMAINDER-COUNT / FIN-422-REMAINDER-MARKER / FIN-400-INVALID-LOOKUP / FIN-422-MAPPING-UNSUPPORTED)

## SCR-FIN-004 — قوالب متكررة/عكسية / Recurring / reversing templates
Traces            : REQ-FIN-022, REQ-FIN-023, REQ-FIN-024, AC-FIN-022
UI pattern        : Master (templates) + Detail (lines), ONE screen (composite); FULL_PAGE (no separate entry drawer needed beyond the master+detail form); template update NOT built — open gap (ADR-FIN-006); no by-id read (search returns the full aggregate, ADR-FIN-006)
Sub-views         : Search · Entry (master + detail)
Fields shown      : template: nameAr, nameEn, scheduleTypeCode (UXD-FIN-011), frequencyCode (UXD-FIN-012, required only when scheduleTypeCode=RECURRING), startDate, endDate, isActiveFl · line: accountId, amount, directionCode (UXD-FIN-002), dimensionValueId — per SRS A3 ENT-FIN-011/012
Permissions       : FIN_RECURRING_TEMPLATES — VIEW, CREATE, UPDATE (run — API-FIN-014; deactivate — API-FIN-036, which since 2026-09-12 also stops the run, `FIN-409-NOT-ACTIVE` — a recorded decision, not a RULE-FIN-*, SRS §B4)
Cross-module data : scheduleTypeCode → UXD-FIN-011 · frequencyCode → UXD-FIN-012 · directionCode → UXD-FIN-002
States            : empty · loading · error (FIN-400-MISSING-FREQUENCY / FIN-404-TEMPLATE / FIN-409-NOT-ACTIVE)

## SCR-FIN-005 — قواعد التوزيع / Allocation rules
Traces            : REQ-FIN-025, REQ-FIN-026, AC-FIN-025
UI pattern        : Master (rules) + Detail (targets), ONE screen (composite); rule update NOT built — open gap (ADR-FIN-006); no by-id read (ADR-FIN-006)
Sub-views         : Search · Entry (master + detail)
Fields shown      : rule: nameAr, nameEn, sourceAccountId, isActiveFl · target: targetAccountId, dimensionValueId, distributionTypeCode (UXD-FIN-010), distributionValue, isRemainderFl — per SRS A3 ENT-FIN-013/014
Permissions       : FIN_ALLOCATION_RULES — VIEW, CREATE, UPDATE (run — API-FIN-017; deactivate — API-FIN-037, stops the run since 2026-09-12, `FIN-409-NOT-ACTIVE` — recorded decision, not a RULE-FIN-*)
Cross-module data : distributionTypeCode → UXD-FIN-010
States            : empty · loading · error (FIN-404-ALLOCATION-RULE / FIN-422-REMAINDER-MARKER / FIN-422-REMAINDER-NOT-POSITIVE / FIN-409-NOT-ACTIVE)

## SCR-FIN-006 — قيود اليومية / Journal entries (view + manual entry + reverse)
Traces            : REQ-FIN-014, REQ-FIN-016, REQ-FIN-017, REQ-FIN-027, REQ-FIN-028, AC-FIN-014
UI pattern        : Search + Entry (with a Reverse action on a POSTED row), ONE screen (composite); by-id read IS built (API-FIN-022) because the entry's lines are needed beyond the search projection (SRS §B1)
Sub-views         : Search · Entry (manual) · Read (posted, incl. reverse)
Fields shown      : header: docDate, fiscalYearId, periodId, journalTypeCode (UXD-FIN-005, fixed `MANUAL` and read-only on the entry form — ADR-FIN-008), descriptionAr, descriptionEn; system: docNo, statusCode (UXD-FIN-006), postedAt, eventReference, originalEntryId, reversalEntryId · line (repeating): accountId, amount, directionCode (UXD-FIN-002), isRemainderFl (read-only), dimension value(s), descriptionAr, descriptionEn — per SRS A3 ENT-FIN-004/005/006. `fiscalYearId` is a real input, paired with periodId (RULE-FIN-017 — ADR-FIN-008)
Permissions       : FIN_JOURNAL_ENTRIES — VIEW, CREATE (incl. Post), custom Reverse (`PERM_FIN_JOURNAL_ENTRIES_REVERSE`)
Cross-module data : journalTypeCode → UXD-FIN-005 · statusCode → UXD-FIN-006 · directionCode → UXD-FIN-002
States            : empty · loading · error (FIN-409-UNBALANCED / FIN-409-NOT-POSTABLE-ACCOUNT / FIN-409-PERIOD-NOT-OPEN / FIN-409-INVALID-DIMENSION / FIN-409-DUPLICATE-EVENT / FIN-400-PERIOD-NOT-IN-YEAR / FIN-400-DOCDATE-OUTSIDE-PERIOD / FIN-404-ENTRY / FIN-409-NOT-POSTED / FIN-409-ALREADY-REVERSED)

## SCR-FIN-007 — الفترات والسنوات المالية / Fiscal periods & years + close approval + year-end close
Traces            : REQ-FIN-031, REQ-FIN-034, REQ-FIN-037, AC-FIN-031
UI pattern        : Master (years) + Detail (periods), ONE screen (composite); no fiscal-year search — known gap (ADR-FIN-006), year ids discovered via period rows' `fiscalYearId`
Sub-views         : Search (periods) · Entry (create year) · row actions (open/soft-close/hard-close/year-end-close)
Fields shown      : year: code, startDate, endDate, periodCount · period: periodNo, nameAr, nameEn, startDate, endDate, statusCode (UXD-FIN-003), closedBy, closedAt — per SRS A3 ENT-FIN-007/008
Permissions       : FIN_PERIODS — VIEW (gateway + API-FIN-033 read), CREATE (year), UPDATE (open/soft-close), custom Close-approve (`PERM_FIN_PERIODS_CLOSE_APPROVE` — hard-close, year-end-close; RULE-FIN-015)
Cross-module data : statusCode → UXD-FIN-003 (period) / UXD-FIN-004 (year, binary, not applicable for a lookup select but the code still resolves through the same MDL mechanism)
States            : empty · loading · error (FIN-409-YEAR-DUP / FIN-404-YEAR / FIN-404-PERIOD / FIN-409-NOT-REOPENABLE / FIN-409-INVALID-TRANSITION / FIN-409-PERIODS-NOT-CLOSED)

## SCR-FIN-008 — دفتر الحساب / Account ledger
Traces            : REQ-FIN-039, REQ-FIN-046, AC-FIN-039
UI pattern        : FULL_PAGE (no entry sub-view — ADR-FIN-003); single screen (report)
Sub-views         : none
Fields shown      : accountId, accountCode, accountNameAr/En, accountTypeCode (UXD-FIN-001), natureCode (UXD-FIN-002), fromDate/toDate, dimensionId/dimensionValueId, debitTotal, creditTotal, closingBalance, rows[] (journalEntryId, docNo, docDate, journalTypeCode (UXD-FIN-005), eventReference, lineNo, amount, directionCode (UXD-FIN-002), signedAmount, runningBalance, descriptions) — per api-docs `AccountLedgerResponse`
Permissions       : FIN_ACCOUNT_LEDGER — VIEW
Cross-module data : accountTypeCode → UXD-FIN-001 · natureCode → UXD-FIN-002 · journalTypeCode → UXD-FIN-005 · directionCode → UXD-FIN-002
States            : empty · loading · error (FIN-404-ACCOUNT)

## SCR-FIN-009 — ميزان المراجعة / Trial balance
Traces            : REQ-FIN-040, REQ-FIN-046, AC-FIN-040
UI pattern        : FULL_PAGE (no entry sub-view — ADR-FIN-003); single screen (report)
Sub-views         : none
Fields shown      : periodId?, accountTypeCode? (UXD-FIN-001) filters; rows[] (accountId, accountCode, names, accountTypeCode, natureCode (UXD-FIN-002), debitTotal, creditTotal, debitBalance, creditBalance, signedBalance); totalDebitBalance, totalCreditBalance, balanced — per api-docs `TrialBalanceResponse`
Permissions       : FIN_TRIAL_BALANCE — VIEW
Cross-module data : accountTypeCode → UXD-FIN-001 · natureCode → UXD-FIN-002
States            : empty · loading · error (FIN-404-PERIOD, only when periodId supplied and unknown)

## SCR-FIN-010 — الميزانية العمومية / Balance sheet
Traces            : REQ-FIN-041, REQ-FIN-046, AC-FIN-041
UI pattern        : FULL_PAGE (no entry sub-view — ADR-FIN-003); single screen (report)
Sub-views         : none
Fields shown      : fiscalYearId (required), asOfDate?; groups[] by accountTypeCode (UXD-FIN-001) → rows[] (accountId, accountCode, names, natureCode (UXD-FIN-002), debitTotal, creditTotal, debitBalance, creditBalance, signedBalance) → groupTotal — per api-docs `BalanceSheetResponse`
Permissions       : FIN_BALANCE_SHEET — VIEW
Cross-module data : accountTypeCode → UXD-FIN-001 · natureCode → UXD-FIN-002
States            : empty · loading · error (FIN-404-YEAR)

## SCR-FIN-011 — قائمة الدخل / Income statement
Traces            : REQ-FIN-042, REQ-FIN-046, AC-FIN-042
UI pattern        : FULL_PAGE (no entry sub-view — ADR-FIN-003); single screen (report)
Sub-views         : none
Fields shown      : fiscalYearId (required), fromPeriodId?, toPeriodId?; groups[] by accountTypeCode (UXD-FIN-001, REVENUE/EXPENSE only) → rows[] (as trial balance) → groupTotal; netResult — per api-docs `IncomeStatementResponse`
Permissions       : FIN_INCOME_STATEMENT — VIEW
Cross-module data : accountTypeCode → UXD-FIN-001 · natureCode → UXD-FIN-002
States            : empty · loading · error (FIN-404-YEAR / FIN-404-PERIOD)

## SCR-FIN-012 — تقارير الأبعاد / Dimension reports
Traces            : REQ-FIN-043, AC-FIN-043
UI pattern        : FULL_PAGE (no entry sub-view — ADR-FIN-003); single screen (report)
Sub-views         : none
Fields shown      : dimensionId (required), dimensionValueId?, periodId?; rows[] (accountId, accountCode, names, natureCode (UXD-FIN-002), dimensionId, dimensionValueId, dimensionValueCode, dimensionValueNameAr/En, debitTotal, creditTotal, signedBalance) — per api-docs `DimensionReportResponse`
Permissions       : FIN_DIMENSION_REPORTS — VIEW
Cross-module data : natureCode → UXD-FIN-002
States            : empty · loading · error (no keying-id 404 published for this report as built)

## Cross-module display dependencies

One `UXD-*` per FIN-owned lookup key that is actually displayed (ADR-FIN-004) — the key is
the unit the shared hook is built on, not the screen. Each resolves through the Lookup
module's consumer read, `API-MDL-011` (`GET /api/v1/mdl/lookups?type=<key>`,
`backend-execution-plan-mdl.md`) — a foreign module's endpoint, named here and never cited
as an `API-*` id in `frontend-execution-plan-fin.md` (C9.5 binds only FIN's own ids).
`PAYMENT_METHOD` is registered (REQ-FIN-045) but cited by no FIN field this version, so it
mints no UXD.

## UXD-FIN-001 — ACCOUNT_TYPE label
Traces  : REQ-FIN-001, AC-FIN-001
Screens : SCR-FIN-001, SCR-FIN-008, SCR-FIN-009, SCR-FIN-010, SCR-FIN-011
Source  : MDL, API-MDL-011 (type=ACCOUNT_TYPE)

## UXD-FIN-002 — DEBIT_CREDIT label
Traces  : REQ-FIN-001, AC-FIN-001
Screens : SCR-FIN-001, SCR-FIN-003, SCR-FIN-004, SCR-FIN-006, SCR-FIN-008, SCR-FIN-009, SCR-FIN-010, SCR-FIN-011, SCR-FIN-012
Source  : MDL, API-MDL-011 (type=DEBIT_CREDIT)

## UXD-FIN-003 — PERIOD_STATE label
Traces  : REQ-FIN-031, AC-FIN-031
Screens : SCR-FIN-007
Source  : MDL, API-MDL-011 (type=PERIOD_STATE)

## UXD-FIN-004 — FISCAL_YEAR_STATUS label
Traces  : REQ-FIN-031, AC-FIN-031
Screens : SCR-FIN-007
Source  : MDL, API-MDL-011 (type=FISCAL_YEAR_STATUS)

## UXD-FIN-005 — JOURNAL_TYPE label
Traces  : REQ-FIN-027, AC-FIN-027
Screens : SCR-FIN-006, SCR-FIN-008
Source  : MDL, API-MDL-011 (type=JOURNAL_TYPE)

## UXD-FIN-006 — JOURNAL_STATUS label
Traces  : REQ-FIN-027, AC-FIN-027
Screens : SCR-FIN-006
Source  : MDL, API-MDL-011 (type=JOURNAL_STATUS)

## UXD-FIN-007 — ACCOUNTING_EVENT_TYPE label
Traces  : REQ-FIN-007, AC-FIN-007
Screens : SCR-FIN-003
Source  : MDL, API-MDL-011 (type=ACCOUNTING_EVENT_TYPE) — host-defined, zero seeded values at v1 (SRS A6)

## UXD-FIN-008 — ACCOUNT_DERIVATION_TYPE label
Traces  : REQ-FIN-008, AC-FIN-008
Screens : SCR-FIN-003
Source  : MDL, API-MDL-011 (type=ACCOUNT_DERIVATION_TYPE)

## UXD-FIN-009 — AMOUNT_SOURCE_TYPE label
Traces  : REQ-FIN-008, AC-FIN-008
Screens : SCR-FIN-003
Source  : MDL, API-MDL-011 (type=AMOUNT_SOURCE_TYPE)

## UXD-FIN-010 — DISTRIBUTION_TYPE label
Traces  : REQ-FIN-008, REQ-FIN-025, AC-FIN-008
Screens : SCR-FIN-003, SCR-FIN-005
Source  : MDL, API-MDL-011 (type=DISTRIBUTION_TYPE)

## UXD-FIN-011 — RECURRING_SCHEDULE_TYPE label
Traces  : REQ-FIN-022, AC-FIN-022
Screens : SCR-FIN-004
Source  : MDL, API-MDL-011 (type=RECURRING_SCHEDULE_TYPE)

## UXD-FIN-012 — RECURRING_FREQUENCY label
Traces  : REQ-FIN-022, AC-FIN-022
Screens : SCR-FIN-004
Source  : MDL, API-MDL-011 (type=RECURRING_FREQUENCY)
