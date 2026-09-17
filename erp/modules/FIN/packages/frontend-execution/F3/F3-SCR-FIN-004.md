<!-- source: PHASE:F3 / SUB:F3-SCR-FIN-004 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: REQ-FIN-022, RULE-FIN-006, SCR-FIN-004 -->
<!-- SUB:F3-SCR-FIN-004:START traces=SCR-FIN-004,REQ-FIN-022,RULE-FIN-006 -->
### F3-SCR-FIN-004 — Recurring/reversing templates
`recurringTemplateFormSchema` : nameAr/nameEn (required, ≤150) · scheduleTypeCode (required, member of UXD-FIN-011) · frequencyCode (required only when scheduleTypeCode=RECURRING, member of UXD-FIN-012 — cross-field, pre-empts FIN-400-MISSING-FREQUENCY) · startDate (required) · endDate (optional)
`recurringTemplateLineFormSchema` : accountId (required) · amount (required, > 0) · directionCode (required, member of UXD-FIN-002) · dimensionValueId (optional) — the set's debit=credit balance (RULE-FIN-006 reused) is checked before submit as a UX pre-empt; the server re-checks at run time
<!-- SUB:F3-SCR-FIN-004:END -->
