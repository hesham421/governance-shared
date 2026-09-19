<!-- source: PHASE:ALIGN-FE -->
<!-- traces: AC-MDL-001, AC-MDL-002, AC-MDL-003, AC-MDL-004, AC-MDL-005, AC-MDL-006, AC-MDL-007, AC-MDL-008, AC-MDL-009, AC-MDL-010, AC-MDL-011, AC-MDL-012, AC-MDL-013, REQ-MDL-001, REQ-MDL-002, REQ-MDL-003, REQ-MDL-004, REQ-MDL-005, REQ-MDL-006, REQ-MDL-007, REQ-MDL-008, REQ-MDL-009, REQ-MDL-010, REQ-MDL-011, REQ-MDL-012, REQ-MDL-013, SCR-MDL-001, SCR-MDL-002, UXD-MDL-001 -->
<!-- PHASE:ALIGN-FE:START traces=REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,AC-MDL-001,AC-MDL-002,AC-MDL-003,AC-MDL-004,AC-MDL-005,AC-MDL-006,AC-MDL-007,AC-MDL-008,AC-MDL-009,AC-MDL-010,UXD-MDL-001,SCR-MDL-001,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013,AC-MDL-011,AC-MDL-012,AC-MDL-013,SCR-MDL-002 -->
## PHASE ALIGN-FE — Alignment

The alignment self-check is this phase's content, and its `RESULT` row is written by the
orchestrator from the analyze report. Never split — level-1 only. Every row names the check
that backs it, and there are no other rows.

```
ALIGN — MDL v1
row           backing check   assertion
SCREENS       orphans         ✓ every SCR is referenced by a plan block — SCR-MDL-001 and
                              SCR-MDL-002 each carry a SUB in F1, F2, F3 and F4 and a block in
                              SEC-FE
COMPOSITION   screen-composition  ✓ every SCR names where its secondary detail sits and that it
                              saves once — SCR-MDL-001 inline for the owner select, a second
                              level for the value collection, one save per entry sub-view;
                              SCR-MDL-002 none, and no save at all
UXD           orphans         ✓ every UXD is cited by a plan block — UXD-MDL-001 by both
                              screens' F2 SCREEN-INIT blocks, by SCR-MDL-001's F3 validator for
                              RULE-MDL-001, and by both F4 Cross-module lines
TRACES        traces          ✓ every PHASE/SUB carries traces=, UXD-MDL-001 traces to its REQ
                              and AC, and each SCR to its REQ and UXD
API           traces          — examined nothing. This plan cites no `API-*` id, because the
                              published api-docs defines none: MDL's generator prints no
                              `Contract ID` line, so every governance id that exists for these
                              endpoints exists only in the backend plan and its registry, which
                              is precisely where this row forbids reading them from. The
                              binding is by the identifiers the document does publish — verb
                              and path — and the clause therefore examined zero subjects
                              (ADR-MDL-008)
FOREIGN       xref-surface    ✓ every reference to another module's surface resolves in that
                              module's own artifacts — this plan makes none: the one foreign
                              read, behind UXD-MDL-001, is named in `ui-ux-spec-mdl.md`
REGISTRY      registry-agree  ✓ every UXD and SCR defined here is in `registry-exec-fe-mdl.md`,
                              and nothing else is
LANGUAGES     languages       ✓ labels and messages in ar + en
MARKERS       markers         ✓ the parser reports no structural or semantic error for the
                              frontend track's exec plan
DECISIONS     refs-exist      ✓ every ADR this plan cites exists on disk in
                              analysis/decisions/MDL/ — ADR-MDL-002 … ADR-MDL-008
COVERAGE      (the report)    the clauses the analyze report lists as having examined nothing:
                              **C9.5** (traces) — and it alone. Its subject set is empty
                              because this plan cites no API id, for the reason the API row
                              above states. Every other clause of this stage examined its
                              subjects: C9.2 fourteen PHASE/SUB blocks · C9.3 one UXD · C9.4
                              two SCR · C9.6 one UXD · C9.7 two SCR · C9.8 three ids ·
                              C9.15, C9.16 and C9.17 two screens each
RESULT    PASSED ✓ — 0 findings
```

### Operations coverage

| Operation | API (published verb · path) | SCR action | Route | Status |
|---|---|---|---|---|
| search lookup types | POST `/api/v1/mdl/lookup-types/search` | SCR-MDL-001 search | /reference-data/lookups | ✓ |
| create lookup type | POST `/api/v1/mdl/lookup-types` | SCR-MDL-001 create type | /reference-data/lookups/new | ✓ |
| update lookup type | PUT `/api/v1/mdl/lookup-types/{id}` | SCR-MDL-001 edit type | /reference-data/lookups/:typeId/edit | ✓ |
| deactivate lookup type | DELETE `/api/v1/mdl/lookup-types/{id}` | SCR-MDL-001 deactivate type | /reference-data/lookups/:typeId | ✓ |
| search lookup values of a type | POST `/api/v1/mdl/lookup-types/values/search` | SCR-MDL-001 value list | /reference-data/lookups/:typeId | ✓ |
| create lookup value | POST `/api/v1/mdl/lookup-types/{id}/values` | SCR-MDL-001 create value | /reference-data/lookups/:typeId/values/new | ✓ |
| update lookup value | PUT `/api/v1/mdl/lookup-values/{id}` | SCR-MDL-001 edit value | /reference-data/lookups/:typeId/values/:valueId/edit | ✓ |
| deactivate lookup value | DELETE `/api/v1/mdl/lookup-values/{id}` | SCR-MDL-001 deactivate value | /reference-data/lookups/:typeId | ✓ |
| reorder lookup values | PATCH `/api/v1/mdl/lookup-types/{id}/values/reorder` | SCR-MDL-001 drag to reorder | /reference-data/lookups/:typeId | ✓ |
| browse the registry by owner | POST `/api/v1/mdl/lookup-types/by-owner/search` | SCR-MDL-002 render | /reference-data/type-registry | ✓ |
| read values by key (consumer API) | GET `/api/v1/mdl/lookups` | — a consuming module's backend call | — (ADR-MDL-007) | ✗ |
| activate a lookup type | — none published | SCR-MDL-001 — not drawn | — (ADR-MDL-005) | ✗ |
| activate a lookup value | — none published | SCR-MDL-001 — not drawn | — (ADR-MDL-005) | ✗ |
| read one type / one value by id | — none published | hydrated from the search cache | — (ADR-MDL-005) | ✗ |

Eleven rows carry a published endpoint; ten of those carry a route and a ✓. Four rows carry a
✗ with the ADR that explains it — one endpoint published for a caller that is not this frontend
(ADR-MDL-007), and three operations with no endpoint at all (ADR-MDL-005). No row is a ✗ for
want of a decision.

<!-- PHASE:ALIGN-FE:END -->
