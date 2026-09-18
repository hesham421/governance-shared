<!-- source: PHASE:F3 / SUB:F3-SCR-FIN-005 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: REQ-FIN-025, RULE-FIN-003, SCR-FIN-005 -->
<!-- SUB:F3-SCR-FIN-005:START traces=SCR-FIN-005,REQ-FIN-025,RULE-FIN-003 -->
### F3-SCR-FIN-005 — Allocation rules
`allocationRuleFormSchema` : nameAr/nameEn (required, ≤150) · sourceAccountId (required)
`allocationTargetFormSchema` : targetAccountId (required) · dimensionValueId (optional) · distributionTypeCode (required, member of UXD-FIN-010) · distributionValue (required unless distributionTypeCode=REMAINDER) · isRemainderFl (boolean) — same cross-field remainder check as F3-SCR-FIN-003 (RULE-FIN-003)
<!-- SUB:F3-SCR-FIN-005:END -->
