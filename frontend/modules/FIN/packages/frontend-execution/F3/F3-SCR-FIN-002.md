<!-- source: PHASE:F3 / SUB:F3-SCR-FIN-002 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: REQ-FIN-004, REQ-FIN-005, RULE-FIN-002, SCR-FIN-002 -->
<!-- SUB:F3-SCR-FIN-002:START traces=SCR-FIN-002,REQ-FIN-004,REQ-FIN-005,RULE-FIN-002 -->
### F3-SCR-FIN-002 — Dimensions
`dimensionFormSchema` : code (required, ≤30) · nameAr/nameEn (required, ≤150)
`dimensionValueFormSchema` : code (required; client-side uniqueness pre-check against the loaded values of the selected dimension — UX pre-empt of RULE-FIN-002) · nameAr/nameEn (required) · sortOrder (required, integer)
<!-- SUB:F3-SCR-FIN-002:END -->
