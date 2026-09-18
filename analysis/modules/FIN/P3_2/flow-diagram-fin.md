# Flow diagram — FIN v1

One flow block per navigation path. Screens carry the atoms (`SCR-*`); flows carry none of
their own, per `profile.conventions.composite_screen`.

```
FLOW — Chart of accounts / شجرة الحسابات                traces=US-FIN-001,REQ-FIN-001,SCR-FIN-001
Screens   : SCR-FIN-001
Sequence  : FIN → Setup → Chart of accounts → Search → Entry (create/update) → Deactivate
Trigger   : finance administrator manages the account hierarchy
Priority  : HIGH
```

```
FLOW — Dimensions & values / تعريف الأبعاد وقيمها       traces=US-FIN-002,REQ-FIN-004,SCR-FIN-002
Screens   : SCR-FIN-002
Sequence  : FIN → Setup → Dimensions → Master (dimension) → Detail (values) → Deactivate value
Trigger   : finance administrator defines a new analysis segment
Priority  : HIGH
```

```
FLOW — Engine rules / قواعد المحرك                       traces=US-FIN-003,REQ-FIN-007,SCR-FIN-003
Screens   : SCR-FIN-003
Sequence  : FIN → Setup → Engine rules → Master (rule) → Detail (lines) → Deactivate rule
Trigger   : finance administrator maps an event type to posting lines, as data
Priority  : HIGH
```

```
FLOW — Recurring/reversing templates / قوالب متكررة/عكسية traces=US-FIN-006,REQ-FIN-022,SCR-FIN-004
Screens   : SCR-FIN-004
Sequence  : FIN → Setup → Recurring/reversing templates → Master (template) → Detail (lines) → Run / Deactivate
Trigger   : accountant sets up an accrual/reversal that recurs on a schedule
Priority  : MEDIUM
```

```
FLOW — Allocation rules / قواعد التوزيع                   traces=US-FIN-007,REQ-FIN-025,SCR-FIN-005
Screens   : SCR-FIN-005
Sequence  : FIN → Setup → Allocation rules → Master (rule) → Detail (targets) → Run / Deactivate
Trigger   : accountant defines how a source balance splits across targets
Priority  : MEDIUM
```

```
FLOW — Manual journal entry / إدخال يدوي                  traces=US-FIN-005,REQ-FIN-014,SCR-FIN-006
Screens   : SCR-FIN-006
Sequence  : FIN → Operations → Journal entries → Search → Entry (header + lines) → Post
Trigger   : accountant records an adjustment or opening entry with no source event
Priority  : HIGH
```

```
FLOW — Review and reverse a posted entry / عكس قيد مُرحَّل traces=US-FIN-009,REQ-FIN-028,SCR-FIN-006
Screens   : SCR-FIN-006
Sequence  : FIN → Operations → Journal entries → Search → open a POSTED row → Reverse
Trigger   : accountant corrects a mistake without ever editing the original
Priority  : HIGH
```

```
FLOW — Fiscal years & periods / السنوات والفترات المالية   traces=US-FIN-010,REQ-FIN-031,SCR-FIN-007
Screens   : SCR-FIN-007
Sequence  : FIN → Control → Fiscal periods & years → create year (generates periods) → Open/Soft-close per period
Trigger   : finance administrator sets up the accounting calendar
Priority  : HIGH
```

```
FLOW — Approve period close / اعتماد إغلاق الفترة           traces=US-FIN-011,REQ-FIN-037,SCR-FIN-007
Screens   : SCR-FIN-007
Sequence  : FIN → Control → Fiscal periods & years → select Soft Closed period → Hard-close (approval)
Trigger   : financial controller, independent of any entry creator, closes the books for a period
Priority  : HIGH
```

```
FLOW — Run year-end close / تشغيل إقفال نهاية السنة          traces=US-FIN-010,REQ-FIN-036,SCR-FIN-007
Screens   : SCR-FIN-007
Sequence  : FIN → Control → Fiscal periods & years → select fiscal year (all periods Hard Closed) → Run year-end close
Trigger   : approver rolls the ledger into the next fiscal year
Priority  : HIGH
```

```
FLOW — Account ledger / دفتر الحساب                           traces=US-FIN-012,REQ-FIN-039,SCR-FIN-008
Screens   : SCR-FIN-008
Sequence  : FIN → Reports → Account ledger → select account + range → running-balance list
Trigger   : accountant/controller inspects one account's live-derived history
Priority  : HIGH
```

```
FLOW — Trial balance / ميزان المراجعة                          traces=US-FIN-013,REQ-FIN-040,SCR-FIN-009
Screens   : SCR-FIN-009
Sequence  : FIN → Reports → Trial balance → select period → balanced account list
Trigger   : financial controller reviews the always-balanced control report
Priority  : HIGH
```

```
FLOW — Balance sheet / الميزانية العمومية                        traces=US-FIN-014,REQ-FIN-041,SCR-FIN-010
Screens   : SCR-FIN-010
Sequence  : FIN → Reports → Balance sheet → select fiscal year (+ as-of date) → grouped statement
Trigger   : financial controller reviews year-over-year balance-sheet continuity
Priority  : HIGH
```

```
FLOW — Income statement / قائمة الدخل                            traces=US-FIN-015,REQ-FIN-042,SCR-FIN-011
Screens   : SCR-FIN-011
Sequence  : FIN → Reports → Income statement → select fiscal year (+ period range) → grouped statement
Trigger   : financial controller reviews period results that open at zero each year
Priority  : HIGH
```

```
FLOW — Dimension reports / تقارير الأبعاد                          traces=US-FIN-016,REQ-FIN-043,SCR-FIN-012
Screens   : SCR-FIN-012
Sequence  : FIN → Reports → Dimension reports → select dimension (+ value, period) → account×value rows
Trigger   : financial controller analyzes results per project/department/investor
Priority  : MEDIUM
```

```
FLOW — Drill-down: ledger to source entry                          traces=US-FIN-019,REQ-FIN-046,SCR-FIN-008,SCR-FIN-006
Screens   : SCR-FIN-008, SCR-FIN-006
Sequence  : Account ledger row → originating JournalEntry (SCR-FIN-006, read-only) → its eventReference
Trigger   : controller explains a ledger row back to its root cause
Priority  : MEDIUM
```

```
FLOW — Drill-down: trial balance to ledger                          traces=US-FIN-019,REQ-FIN-046,SCR-FIN-009,SCR-FIN-008
Screens   : SCR-FIN-009, SCR-FIN-008
Sequence  : Trial balance row (accountId) → Account ledger (SCR-FIN-008), same period
Trigger   : controller drills a control-report row into its account detail
Priority  : MEDIUM
```

```
FLOW — Drill-down: balance sheet to trial balance                    traces=US-FIN-019,REQ-FIN-046,SCR-FIN-010,SCR-FIN-009
Screens   : SCR-FIN-010, SCR-FIN-009
Sequence  : Balance sheet row (accountId) → Trial balance (SCR-FIN-009), same period
Trigger   : controller drills a statement line into the control report behind it
Priority  : MEDIUM
```

```
FLOW — Drill-down: income statement to trial balance                  traces=US-FIN-019,REQ-FIN-046,SCR-FIN-011,SCR-FIN-009
Screens   : SCR-FIN-011, SCR-FIN-009
Sequence  : Income statement row (accountId) → Trial balance (SCR-FIN-009), same period
Trigger   : controller drills a statement line into the control report behind it
Priority  : MEDIUM
```

## Reconciliation self-check (SRS B1–B4 ↔ draft) — no human gate

```
RECONCILIATION — FIN v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)   → all 19 US-FIN-001..019 covered, none invented
B2 no RULE-* contradicts a flow/spec outcome                          → no contradiction found
B3 every field/permission on a screen exists in the SRS               → checked against SRS A3/A4/A6/Access summary; none extra, none missing
B4 every screen entry of the SRS has exactly one SCR-* block          → 12 SCR-REQ-FIN-* → 12 SCR-FIN-* (1:1), composite rule applied throughout
RESULT  reconciled 12 · reworked 0 · ADRs none (this reconciliation raised none; ADR-FIN-002..008 record other P3.2 decisions, see frontend-execution-plan-fin.md and ui-ux-spec-fin.md)
```
