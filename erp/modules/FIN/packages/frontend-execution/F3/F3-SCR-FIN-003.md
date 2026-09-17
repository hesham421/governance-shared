<!-- source: PHASE:F3 / SUB:F3-SCR-FIN-003 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: REQ-FIN-007, REQ-FIN-008, REQ-FIN-009, RULE-FIN-003, SCR-FIN-003 -->
<!-- SUB:F3-SCR-FIN-003:START traces=SCR-FIN-003,REQ-FIN-007,REQ-FIN-008,REQ-FIN-009,RULE-FIN-003 -->
### F3-SCR-FIN-003 — Engine rules
`eventTypeRuleFormSchema` : eventTypeCode (required, ≤50, member of UXD-FIN-007) · nameAr/nameEn (required, ≤150)
`ruleLineFormSchema` : accountDerivationTypeCode (required, member of UXD-FIN-008) · accountDerivationValue (required) · amountSourceTypeCode (required, member of UXD-FIN-009) · amountSourceValue (required unless amountSourceTypeCode=REMAINDER) · directionCode (required, member of UXD-FIN-002) · distributionTypeCode (required, member of UXD-FIN-010) · isRemainderFl (boolean) — cross-field: exactly one line of the set may carry isRemainderFl=true when any sibling is PERCENTAGE, checked across the whole line list before submit (UX pre-empt of RULE-FIN-003)
<!-- SUB:F3-SCR-FIN-003:END -->
