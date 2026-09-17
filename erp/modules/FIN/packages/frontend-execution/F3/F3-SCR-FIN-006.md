<!-- source: PHASE:F3 / SUB:F3-SCR-FIN-006 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: REQ-FIN-014, REQ-FIN-017, REQ-FIN-018, RULE-FIN-006, RULE-FIN-017, SCR-FIN-006 -->
<!-- SUB:F3-SCR-FIN-006:START traces=SCR-FIN-006,REQ-FIN-014,REQ-FIN-017,REQ-FIN-018,RULE-FIN-006,RULE-FIN-017 -->
### F3-SCR-FIN-006 — Journal entries
`manualEntryFormSchema` : docDate (required) · fiscalYearId (required) · periodId (required; option list filtered by the chosen fiscalYearId — UX pre-empt of RULE-FIN-017) · journalTypeCode (fixed `MANUAL`, not a validated input field — ADR-FIN-008) · descriptionAr/descriptionEn (optional) · lines (min 2 — a balanced entry needs at least a debit and a credit side)
`journalLineFormSchema` : accountId (required) · amount (required, > 0) · directionCode (required, member of UXD-FIN-002) · dimensions[] (dimensionId+dimensionValueId pairs, optional) · descriptionAr/descriptionEn (optional) — cross-field: Σdebit = Σcredit checked across `lines` before submit (UX pre-empt of RULE-FIN-006), server remains authoritative
`searchFilterSchema` : docNo (LIKE) · docDate range · periodId · statusCode (member of UXD-FIN-006) · journalTypeCode (member of UXD-FIN-005)
<!-- SUB:F3-SCR-FIN-006:END -->
