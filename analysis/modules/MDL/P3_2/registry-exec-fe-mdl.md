## REGISTRY — P3.2 — MDL v1
══════════════════════════════════════════════════════════════════
Module : MDL (البيانات المرجعية / Master Data Lookup)   Version : v1   Profile : erp
Source : analysis/modules/MDL/P3_2/ui-ux-spec-mdl.md · frontend-execution-plan-mdl.md
══════════════════════════════════════════════════════════════════

ID RANGES
UXD-MDL-001 .. UXD-MDL-001 · SCR-MDL-001 .. SCR-MDL-002

SCR ids : SCR-MDL-001, SCR-MDL-002
UXD ids : UXD-MDL-001
Last sequence per atom : SCR 002 · UXD 001

Neither sequence restarts and nothing is renumbered: both are the assignment this module version
already carries, cited unchanged by the decisions on disk (ADR-MDL-003, ADR-MDL-004, ADR-MDL-007)
and by every downstream artifact that names them. This run mints no new id of either kind — the
two screens the SRS declares and the one cross-module display dependency are the same two and the
same one.

SCREENS
| SCR | الاسم / Name | Owning ENT | Container pattern | Permissions |
|---|---|---|---|---|
| SCR-MDL-001 | اللوكبات العامة / Generic Lookups | ENT-MDL-001 (+ ENT-MDL-002) | TREE_MASTER_DETAIL | PERM_MDL_LOOKUPS_VIEW (gateway), PERM_MDL_LOOKUPS_CREATE, PERM_MDL_LOOKUPS_UPDATE, PERM_MDL_LOOKUPS_DELETE |
| SCR-MDL-002 | سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner | ENT-MDL-001 | FULL_PAGE — no entry sub-view (ADR-MDL-003) | PERM_MDL_TYPE_REGISTRY_VIEW |

Two composite screens, two page codes (MDL_LOOKUPS, MDL_TYPE_REGISTRY), and one `SUB` per screen
in each of the four sub-bearing phases — eight per-screen blocks, plus a level-1 block per screen
in SEC-FE. The permission names are the backend registry's; this stage cites them and declares
none.

COMPOSITION
| SCR | Secondary detail | Placement | Submits |
|---|---|---|---|
| SCR-MDL-001 | the selected type's values — records of a second entity, absent from every type write DTO | none inline · a summary row per value, its editor a second level opened from the route, rendered as a sibling and carrying no scroll region of its own | one per open surface, one surface at a time; no action of this screen owns two calls |
| SCR-MDL-002 | none — a read-only browse | none | none — the screen writes nothing |

UXD INDEX
| UXD | Screen(s) | Field | Owner module · API used |
|---|---|---|---|
| UXD-MDL-001 | SCR-MDL-001, SCR-MDL-002 | `ownerModuleCode` — the type's owning module | SEC · `ModuleRegistry` (ENT-SEC-004), read through the security module's registry search, named in ui-ux-spec-mdl.md and cited by no id inside the frontend plan (ADR-MDL-004, ADR-MDL-011) |

One `UXD-*` for the whole module, and the same shared, long-lived hook serves both screens. It is
**not** a lookup dependency: SRS §A6 records that MDL introduces no coded list of its own, and the
owner-module field's valid set is another module's registry data. It is not the backend's
cross-module record either: that one is the server-side existence check behind RULE-MDL-001 and
appears in no frontend artifact.
Grant that travels with it: every role granted `PERM_MDL_LOOKUPS_CREATE` must also hold
`PERM_SEC_MODULE_REGISTRY_VIEW`, or the owner-module select stays empty and disabled and creation
is blocked behind it. The grant is the security module's to make (ADR-MDL-013).

API COVERAGE
| Status | Count | API ids |
|---|---|---|
| used by this frontend | 10 | API-MDL-001 … API-MDL-010 |
| documented, deliberately uncalled | 1 | API-MDL-011 — the consumer read a consuming module's backend performs over the platform's in-process interface; bound in F2 and drawn on no screen (ADR-MDL-007) |
| used but undocumented | 0 | no endpoint is called that the api-docs do not publish |
| documented but unbound | 0 | all 11 published endpoints are bound, each cited by the `Contract ID` the api-docs now publish for it (ADR-MDL-011, superseding ADR-MDL-008) |

RESPONSE SHAPES — the difference is load-bearing and is stated per block in F2
| Shape | API ids |
|---|---|
| paginated (`PageLookup<T>`) | API-MDL-001, API-MDL-005 |
| a bare array | API-MDL-009, API-MDL-010, API-MDL-011 |
| a single object | API-MDL-002, API-MDL-003, API-MDL-004, API-MDL-006, API-MDL-007, API-MDL-008 |
Only the two paginated reads carry page and size in their cache keys. API-MDL-010 accepts
`filters` alone — no sort and no paging — so neither belongs in its key.

SHAPE DIFFS AGAINST THE BACKEND PLAN
Three reads are `POST …/search` where `backend-execution-plan-mdl.md`'s contract summary still
predicts `GET`: API-MDL-001, API-MDL-005, API-MDL-010. The api-docs and the SRS §B5 tables agree
with each other, so the backend plan is the one artifact that lags; `gov.py analyze` reports it
there (C8.4). Correcting it is three rows in a P3.1 artifact, outside this stage's boundary —
recorded here and in ADR-MDL-002, not silently corrected.

OPERATIONS WITHOUT AN ENDPOINT
read one lookup type by id · read one lookup value by id — named by SRS Part B, required by no
`REQ-*`, and omitted from the frontend rather than faked; both entry sub-views hydrate from the
row the search query already holds (ADR-MDL-005). This SRS version names no `activate` action at
either level, so deactivation being one-way is the SRS's own statement, not an omission.

LOOKUPS
None. MDL introduces no domain-specific coded list of its own — it is the generic mechanism every
other module's lookup types run on. No lookup hook exists anywhere in the plan, no validator binds
an option set, and no enum is modelled. The one constrained field, `ownerModuleCode`, binds to the
runtime-loaded list of UXD-MDL-001.

ALIGN
Verdict as stamped in frontend-execution-plan-mdl.md → ALIGN-FE → the `RESULT` line (written by
the orchestrator from the analyze report). Findings fixed during this run: three statements of the
previous revision that no longer describe the inputs — the plan-local endpoint labels it used
while the api-docs carried no governance id, the `activate` rows it listed as omitted operations
after the SRS stopped naming them, and the unqualified per-screen phase content that the
`sub_bearing` phases now address one screen at a time. None outstanding.

ADRs
analysis/decisions/MDL/ADR-MDL-002.md (ACCEPTED — three reads are POST `…/search`; the backend
contract summary is what lags) ·
analysis/decisions/MDL/ADR-MDL-003.md (ACCEPTED — container pattern for the read-only browse) ·
analysis/decisions/MDL/ADR-MDL-004.md (ACCEPTED — the owner-module field reads the security
module's registry; UXD-MDL-001) ·
analysis/decisions/MDL/ADR-MDL-005.md (ACCEPTED — operations with no published endpoint are
omitted, not faked) ·
analysis/decisions/MDL/ADR-MDL-006.md (ACCEPTED — `isActiveFl` read-only at both levels) ·
analysis/decisions/MDL/ADR-MDL-007.md (ACCEPTED — API-MDL-011 bound and called by no screen) ·
analysis/decisions/MDL/ADR-MDL-011.md (ACCEPTED, raised this run — the re-fetched api-docs
publishes a Contract ID per endpoint, so the plan cites API ids; supersedes ADR-MDL-008) ·
analysis/decisions/MDL/ADR-MDL-012.md (ACCEPTED, raised this run — no published surface tells a
screen which actions its caller holds) ·
analysis/decisions/MDL/ADR-MDL-013.md (ACCEPTED, raised this run — the grant that travels with
UXD-MDL-001)
Superseded, kept on disk and cited only by the decisions that replaced them: ADR-MDL-001,
ADR-MDL-008. Carried from earlier stages: ADR-MDL-009 and ADR-MDL-010 (P2) touch no frontend
artifact and are not applied here. No BLOCKED ADR — the pass was not stopped, and no question was
raised.

TRACEABILITY
REQ reached by ≥1 F-block: 13/13 — REQ-MDL-001 … REQ-MDL-010 appear in the `traces=` of
SCR-MDL-001's blocks, and REQ-MDL-011, REQ-MDL-012, REQ-MDL-013 in SCR-MDL-002's. Orphan REQ:
none.
REQ traced by an `SCR-*` or `UXD-*` record: **11/13**, and the two numbers are not in conflict.
REQ-MDL-011 and REQ-MDL-012 are the consumer's read by key — a server-to-server call with no
screen (ADR-MDL-007) — so they are reached by the F2 block that binds API-MDL-011 and by the
values and active flags managed on SCR-MDL-001 that decide what that read returns, and they are
traced by no screen, because no screen implements them. The analyze report's `req-ux` ratio says
84.6% for exactly this reason; inflating it would mean asserting a screen that does not exist.
AC covered: 13/13 — each AC accompanies its REQ in the same traces.
SCR covered: 2/2 — each carries a SUB in F1, F2, F3 and F4 and a block in SEC-FE.
UXD cited by an F-block: 1 of 1 — none unreferenced, none dangling.
Screen operations the SRS names, resolved to a route: 8/8 on SCR-MDL-001 (search, create, read,
update, deactivate, reorder across both levels) and 2/2 on SCR-MDL-002 (search, browse).

Event
"P3.2 completed: MDL v1 — 2 screens, 1 UXD, 11/11 API bound (10 called), 4 sub-bearing phases
split per screen (8 SUB blocks), ALIGN-FE stamped by the orchestrator, 3 new ADRs"
══════════════════════════════════════════════════════════════════
