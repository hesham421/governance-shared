<!-- source: PHASE:F3 / SUB:F3-SCR-FIN-001 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: REQ-FIN-001, REQ-FIN-002, RULE-FIN-001, SCR-FIN-001 -->
<!-- SUB:F3-SCR-FIN-001:START traces=SCR-FIN-001,REQ-FIN-001,REQ-FIN-002,RULE-FIN-001 -->
### F3-SCR-FIN-001 — Chart of accounts
`accountFormSchema` : code (required, ≤30) · nameAr/nameEn (required, ≤200) · accountTypeCode/natureCode (required, member of the runtime-loaded UXD-FIN-001/002 option list) · parentAccountId (optional) · isLeafFl (boolean; disabled and forced false client-side once the account has ≥1 child in the loaded tree — a UX pre-empt of RULE-FIN-001, server remains authoritative)
<!-- SUB:F3-SCR-FIN-001:END -->
