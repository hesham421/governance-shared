## REGISTRY — P3.2 — MDL v1
══════════════════════════════════════════════════════════════════

ID RANGES
UXD-MDL-001 .. UXD-MDL-001 · SCR-MDL-001 .. SCR-MDL-002

SCR ids: SCR-MDL-001, SCR-MDL-002

UXD ids: UXD-MDL-001

Last sequence per atom: SCR: 002 · UXD: 001

SCREENS
| SCR | Name (ar / en) | Owning ENT | Container pattern | Composition | Permissions |
|---|---|---|---|---|---|
| SCR-MDL-001 | اللوكبات العامة / Generic Lookups | ENT-MDL-001 (+ ENT-MDL-002) | TREE_MASTER_DETAIL | owner select inline · value collection at a second level · one save per entry sub-view | PERM_MDL_LOOKUPS_VIEW, PERM_MDL_LOOKUPS_CREATE, PERM_MDL_LOOKUPS_UPDATE, PERM_MDL_LOOKUPS_DELETE |
| SCR-MDL-002 | سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner | ENT-MDL-001 | FULL_PAGE (no entry sub-view — ADR-MDL-003) | none — a read-only browse, no save | PERM_MDL_TYPE_REGISTRY_VIEW |

Page codes, from the SRS Part B: `MDL_LOOKUPS` (SCR-MDL-001) and `MDL_TYPE_REGISTRY`
(SCR-MDL-002). Two screens, and every `sub_bearing` phase splits per screen all the same: F1,
F2, F3 and F4 carry one `SUB` per `SCR-*` — 8 SUB blocks — with every SUB id phase-qualified.

UXD INDEX
| UXD | Screen(s) | Field | Owner module · API used |
|---|---|---|---|
| UXD-MDL-001 | SCR-MDL-001, SCR-MDL-002 | ownerModuleCode — the type's owning module | SEC · `ModuleRegistry` (ENT-SEC-004), read through the security module's registry search — `API-SEC-021`, named in `ui-ux-spec-mdl.md` and never in the plan |

One `UXD-*` for the whole module. It is **not** a lookup dependency: SRS §A6 records that MDL
introduces no coded list of its own, and the owner-module field's valid set is another module's
registry data, consumed through XM-MDL-001 (SOFT-READ) — §A.5's definition exactly
(ADR-MDL-004). The screen gate (the security module's effective menu) is an authorization
dependency, not a displayed field, and is recorded in the plan's SEC-FE phase rather than
minted here.

API COVERAGE
The published api-docs for this module carries **no governance `API-*` id** — no `Contract ID`
line, unlike the security and finance modules' published documents. The frontend plan therefore
cites none and binds by the identifiers the document does publish: the verb and the path
(ADR-MDL-008, which supersedes ADR-MDL-001). The id ↔ endpoint binding below is kept here, in
the registry, where it is a record of what `registry-exec-be-mdl.md` registers and not a claim
about what the api-docs define.

| Backend id (registry-exec-be) | Plan label | Published verb · path | Called by |
|---|---|---|---|
| API-MDL-001 | TYPE-SEARCH | POST `/api/v1/mdl/lookup-types/search` | SCR-MDL-001 |
| API-MDL-002 | TYPE-CREATE | POST `/api/v1/mdl/lookup-types` | SCR-MDL-001 |
| API-MDL-003 | TYPE-UPDATE | PUT `/api/v1/mdl/lookup-types/{id}` | SCR-MDL-001 |
| API-MDL-004 | TYPE-DEACTIVATE | DELETE `/api/v1/mdl/lookup-types/{id}` | SCR-MDL-001 |
| API-MDL-005 | VALUE-SEARCH | POST `/api/v1/mdl/lookup-types/values/search` | SCR-MDL-001 |
| API-MDL-006 | VALUE-CREATE | POST `/api/v1/mdl/lookup-types/{id}/values` | SCR-MDL-001 |
| API-MDL-007 | VALUE-UPDATE | PUT `/api/v1/mdl/lookup-values/{id}` | SCR-MDL-001 |
| API-MDL-008 | VALUE-DEACTIVATE | DELETE `/api/v1/mdl/lookup-values/{id}` | SCR-MDL-001 |
| API-MDL-009 | VALUE-REORDER | PATCH `/api/v1/mdl/lookup-types/{id}/values/reorder` | SCR-MDL-001 |
| API-MDL-010 | REGISTRY-BY-OWNER | POST `/api/v1/mdl/lookup-types/by-owner/search` | SCR-MDL-002 |
| API-MDL-011 | CONSUMER-READ-BY-KEY | GET `/api/v1/mdl/lookups` | no screen — a consuming module's backend (ADR-MDL-007) |

The eleven ids and the eleven published endpoints match 1:1 on verb and path, with three
verb/path diffs against the backend plan's own contract table (ADR-MDL-002) and none against
the SRS Part B tables. Used by this frontend: 10 · documented and deliberately uncalled: 1
(the consumer read) · used but undocumented: 0 · documented but unbound: 0.

RESPONSE SHAPES — the difference is load-bearing and is stated per endpoint in F2
| Shape | Endpoints |
|---|---|
| paged (`PageLookup<T>`) | TYPE-SEARCH, VALUE-SEARCH |
| a bare array | VALUE-REORDER, REGISTRY-BY-OWNER, CONSUMER-READ-BY-KEY |
| a single object | TYPE-CREATE, TYPE-UPDATE, TYPE-DEACTIVATE, VALUE-CREATE, VALUE-UPDATE, VALUE-DEACTIVATE |
Only the two paged reads carry `page` and `size` in their cache keys. REGISTRY-BY-OWNER accepts
`filters` alone — no sort and no paging — so neither belongs in its key.

SHAPE DIFFS AGAINST THE BACKEND PLAN
Three reads are `POST …/search` where `backend-execution-plan-mdl.md`'s contract table still
predicts `GET`: the type search, the value search and the by-owner browse. The api-docs and the
SRS Part B `B5` tables agree with each other, so the backend plan is the one artifact that lags,
and `gov.py analyze` reports it there as three C8.4 findings. Correcting it is three rows in a
P3.1 artifact, which §8 puts outside this stage's boundary. Recorded here and in ADR-MDL-002.

OPERATIONS WITHOUT AN ENDPOINT
activate a lookup type · activate a lookup value · read one type by id · read one value by id —
named by SRS Part B, required by no `REQ-*`, and omitted from the frontend rather than faked
(ADR-MDL-005). Deactivation is therefore one-way from the screen, and the confirmation says so.

LOOKUPS
None. SRS §A6: MDL introduces no domain-specific coded list of its own — it is the generic
mechanism every other module's lookup types run on top of. No lookup hook exists anywhere in
the plan, no `LOOKUP_VALID` validator binds an option set, and no enum is modelled. The one
constrained field, `ownerModuleCode`, binds to the runtime-loaded module list of UXD-MDL-001.

ALIGN
The verdict row inside the plan's ALIGN-FE block is written by the orchestrator from the
analyze report; findings fixed by this stage: 0. One row of that block is not a ✓ and says so:
the API row examined nothing, because the published document defines no id for this plan to
cite (ADR-MDL-008).

ADRs
analysis/decisions/MDL/ADR-MDL-001.md (SUPERSEDED by ADR-MDL-008 — the API id binding annex, erased by the re-fetch that regenerates the api-docs) ·
analysis/decisions/MDL/ADR-MDL-002.md (ACCEPTED, non-breaking — three POST `…/search` reads, and the stale backend contract table) ·
analysis/decisions/MDL/ADR-MDL-003.md (ACCEPTED, non-breaking — container pattern for the read-only registry browse) ·
analysis/decisions/MDL/ADR-MDL-004.md (ACCEPTED, non-breaking — the owner-module field reads the security module's registry; UXD-MDL-001) ·
analysis/decisions/MDL/ADR-MDL-005.md (ACCEPTED, non-breaking — operations with no published endpoint) ·
analysis/decisions/MDL/ADR-MDL-006.md (ACCEPTED, non-breaking — `isActiveFl` read-only vs SRS B3's input list) ·
analysis/decisions/MDL/ADR-MDL-007.md (ACCEPTED, non-breaking — the consumer read bound but drawn on no screen) ·
analysis/decisions/MDL/ADR-MDL-008.md (ACCEPTED, non-breaking — the published api-docs carries no governance id, so the plan binds by verb and path)
Carried from earlier stages: none — MDL raised no ADR before P3.2. No BLOCKED ADR.

TRACEABILITY
REQ covered by ≥1 SCR/F-block: 13/13 — REQ-MDL-001..010 appear in the `traces=` of SCR-MDL-001's
blocks in every phase, and REQ-MDL-011, REQ-MDL-012, REQ-MDL-013 in SCR-MDL-002's. REQ-MDL-011
and REQ-MDL-012 are covered the only way a UI can cover a server-to-server read: through the
bound F2 block of the consumer read, and through the values and active flags managed on
SCR-MDL-001 that decide what that read returns.
Orphan REQ: none.
AC covered: 13/13 (each AC accompanies its REQ in the same traces).
SCR covered: 2/2 — both carry a block in F1, F2, F3 and F4 and a block in SEC-FE.
UXD cited by an F-block: 1 of 1 — none unreferenced, none dangling.

Event
"P3.2 completed: MDL v1 — 2 screens, 1 UXD, 11/11 published endpoints bound (10 called) by verb
and path, 4 sub-bearing phases split per screen (8 SUB), ALIGN-FE stamped by the orchestrator,
8 ADRs (1 superseded)"
══════════════════════════════════════════════════════════════════
