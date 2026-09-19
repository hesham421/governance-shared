# REVISE BRIEF — gate `pass-2` · module MDL · v1 · profile `erp`

Lane `merge-review-notes` · implementers ['claude:sonnet'] · effort low

## What to do
The reviewers returned **REVISE**. Apply EVERY finding below in the artifact it names,
with the fix it states. Where a fix needs a choice, take the best-practice one and record it as an ADR
(`analysis/decisions/MDL/ADR-{MOD}-{seq:03d}.md`, next sequence, status ACCEPTED). Never re-number an id,
never restart a sequence, never raise a `[QUESTION]` — this pass's stages forbid questions.
Respond with one `<<<FILE: <path>>>> … <<<END FILE>>>` block per file you change — the COMPLETE file — and nothing else
for files you do not change. The files you may write:
- `governance-shared/analysis/modules/MDL/P3_2/flow-diagram-mdl.md` (P3.2)
- `governance-shared/analysis/modules/MDL/P3_2/ui-ux-spec-mdl.md` (P3.2)
- `governance-shared/analysis/modules/MDL/P3_2/frontend-execution-plan-mdl.md` (P3.2)
- `governance-shared/analysis/modules/MDL/P3_2/registry-exec-fe-mdl.md` (P3.2 · registry)

## Findings to apply
```json
{
  "verdict": "REVISE",
  "scores": {
    "unambiguous": 2,
    "verifiable": 2,
    "complete": 1,
    "consistent": 1,
    "singular": 3,
    "feasible": 2,
    "traceable": 3
  },
  "findings": [
    {
      "id": "G1",
      "severity": "MAJOR",
      "artifact": "frontend-execution-plan",
      "line": null,
      "clause": "GOVERNANCE-CORE.md §1 (upstream wins)",
      "problem": "The client field constraints contradict the deployed precisions in six places: F1 and F3 state `key : maxLength 80` (SCR-MDL-001 create and the SCR-MDL-002 key filter) and `nameAr/nameEn : maxLength 150` on both entities, while db-script §1 and ADR-MDL-010 declare VARCHAR(50) for `key` and `code` and VARCHAR(200) for every name column, and backend-execution-plan API-MDL-002/003/006/007 state maximum 50 and maximum 200 on every Request line. The consequence runs both ways: a 51-80 character key passes client validation and is refused by the database, and a 151-200 character label the platform accepts is refused by the client. This is one rule breached six times, not six defects — F3's own preamble says the constraints are 'the field constraints the published DTOs state', and the numbers state something else.",
      "fix": "Set `key` and `code` to maxLength 50 and every `nameAr`/`nameEn` to maxLength 200 in F1-MODEL (both entities), F3-FIELD (all four blocks) and the SCR-MDL-002 key filter, sourcing each from ADR-MDL-010 by citation rather than a restated number. If `_inputs/api-docs-mdl.md` genuinely publishes 80/150, the numbers are a backend-repo defect: record an OPEN platform-findings row against the MDL api-doc generator with the db-script column widths as evidence, and bind the plan to the db-script meanwhile.",
      "adr": false
    },
    {
      "id": "G2",
      "severity": "MAJOR",
      "artifact": "frontend-execution-plan",
      "line": null,
      "clause": "C9.2 / GOVERNANCE-CORE.md §1",
      "problem": "The detail read is modelled as paginated and no upstream artifact says it is. F1-SCREEN gives the detail search model `page, size, sortField, sortDirection`; F2 heads API-MDL-005 'paginated response' and puts page and size in its cache key; registry-exec-fe lists API-MDL-005 under 'paginated (PageLookup<T>)'. Against that, SRS B2 states the detail is shown whole because it is confined to one type, backend-execution-plan API-MDL-005 states 'No paging: the result is bounded by one type and returned whole, ordered', and QR-MDL-005 records `Pagination: NO`. ADR-MDL-002 shows this pass knows how to record a divergence between the published surface and a plan; this one is recorded nowhere, so a reader cannot tell whether the api-docs page this endpoint or whether the shape was assumed from API-MDL-001's neighbour.",
      "fix": "Resolve against `_inputs/api-docs-mdl.md` and state the answer once. If the endpoint is unpaged, drop page/size/sort from the F1 detail search model, from the F2 cache key and from the registry's RESPONSE SHAPES table, and move API-MDL-005 to the bare-array row. If it is paged, record the divergence as an ADR in the form ADR-MDL-002 uses, naming SRS B2 and backend-execution-plan API-MDL-005 as the artifacts that lag, so the gate sees a declared diff rather than a silent one.",
      "adr": false
    },
    {
      "id": "G3",
      "severity": "MAJOR",
      "artifact": "frontend-execution-plan",
      "line": null,
      "clause": "QUALITY-RUBRIC.md §2 complete",
      "problem": "The reorder can submit a partial set, and the plan asserts the opposite is impossible. F2 says 'The whole ordered set is submitted, never one row's new position: the endpoint takes `orderedValueIds[]`, and the mismatch code exists precisely because a partial set is wrong.' MDL-400-REORDER-MISMATCH does not mean that: QR-MDL-009's predicate is `lookup_value_pk = :id AND lookup_type_id = :typeId`, so the code fires only when a submitted id belongs to another type. A subset of the correct type is accepted, each submitted id is written its position in the submitted list, and those ranks collide with the ranks of the values that were not submitted. The screen can produce exactly that subset: the detail pane carries a `code` LIKE filter (F1, F3) and, per G2, a page and size, so dragging inside a filtered or paged pane submits part of the type's values and re-ranks them from 1.",
      "fix": "State in F2-QUERY VALUE REORDER and in F4 that the submitted list is the type's complete value set, and disable the drag affordance whenever the pane shows less than that — a non-empty `code` filter, or a page other than the whole result — with the reason shown on the list. Add the counter-case as its own line: a reorder submitted from a filtered pane is refused by the client, because the server cannot distinguish it from a deliberate whole-set submission.",
      "adr": false
    },
    {
      "id": "G4",
      "severity": "MAJOR",
      "artifact": "frontend-execution-plan",
      "line": null,
      "clause": "QUALITY-RUBRIC.md §2 feasible",
      "problem": "The deep-linked entry routes cannot be built as specified. F4 registers `/reference-data/lookups/:typeId/edit`, `/values/new` and `/values/:valueId/edit` and states 'a deep link opens it'; ADR-MDL-005 states both entry sub-views 'hydrate from the row the search query already holds' and that no by-id read is published. On a cold load there is no search result in cache, and no read can produce one: the published filter set for API-MDL-001 is key, ownerModuleCode, name and isActiveFl, and for API-MDL-005 it is lookupTypeId and code — neither carries the record's own id, so even a one-row search cannot resolve `:typeId` or `:valueId`. The editor opens empty on the one entry path F4 explicitly promises.",
      "fix": "State the cold-load behaviour on each entry route: when the hydrating row is absent from cache, redirect to the parent surface — `/reference-data/lookups` for a type editor, `/reference-data/lookups/:typeId` for a value editor — and show the localized 'open the record from the list' message rather than rendering an empty form. Best practice over the alternatives because it needs no unpublished endpoint and no invented filter, and it keeps the route addressable for the in-session case F4 was written for. Record the underlying gap where its owner can act: an OPEN platform-findings row that the published search envelopes carry no id operator, which is what makes by-id hydration impossible for every module that omits a by-id read.",
      "adr": true
    },
    {
      "id": "G5",
      "severity": "MAJOR",
      "artifact": "frontend-execution-plan",
      "line": null,
      "clause": "REGISTRY-SCHEMA.md §4 (platform findings)",
      "problem": "The pass found a cross-artifact permission divergence and answered it with a paragraph instead of a row. SEC-FE states: 'The api-docs put `PERM_MDL_LOOKUPS_UPDATE` on both deactivate endpoints, so no published endpoint of this module requires `PERM_MDL_LOOKUPS_DELETE`' — against backend-execution-plan PHASE 7, whose matrix marks the DELETE column for API-MDL-004 and API-MDL-008 and whose BOOTSTRAP DATA seeds that permission with grant targets. The consequence is a privilege widening, not a naming detail: as built, a caller holding UPDATE alone can deactivate, and a caller granted DELETE alone cannot. 'Recorded, not corrected — the permission an endpoint requires is the backend's to state' is the correct scope judgement and the wrong disposal: a module-scoped worker saying 'not mine to settle' into prose is what the platform-findings category exists to stop.",
      "fix": "File an OPEN platform-findings row: owner = the MDL backend track (P3.1 plus the backend repo's method-level security annotations); evidence = backend-execution-plan PHASE 7 permission matrix and BOOTSTRAP DATA versus the api-docs' stated requirement on API-MDL-004 and API-MDL-008; impact = UPDATE grants deactivation and DELETE grants nothing, so the SRS B4 DELETE row is unenforceable as built. Keep the SEC-FE paragraph as the frontend's reading and cite the row's id from it.",
      "adr": false
    },
    {
      "id": "G6",
      "severity": "MAJOR",
      "artifact": "frontend-execution-plan",
      "line": null,
      "clause": "QUALITY-RUBRIC.md §2 complete",
      "problem": "The owner-module filter on SCR-MDL-002 has no stated behaviour when its source read fails. F3-FIELD SCR-MDL-002 makes it a select over the UXD-MDL-001 list; ADR-MDL-013 states the degraded path for that screen only as 'the group headings showing the bare code the browse already returns' and asserts 'browsing is never blocked by it'. Both hold for the headings and neither holds for the filter: a select over an empty list cannot be used, so the EXACT owner filter SRS B2 requires — the one filter the screen exists for — silently becomes unavailable while the plan says nothing is blocked. F2-SCREEN-INIT repeats the heading-only wording.",
      "fix": "State the filter's degraded source in F3-FIELD SCR-MDL-002 and in ADR-MDL-013: when the UXD-MDL-001 read is refused or fails, the owner select falls back to the distinct `ownerModuleCode` values present in the API-MDL-010 response, which the browse already returns on every group. Best practice over the alternatives because it needs no foreign grant, keeps the SRS B2 filter usable, and cannot offer a code the registry does not hold — narrower than free text, which would let a reviewer filter for a code no group carries.",
      "adr": true
    },
    {
      "id": "G7",
      "severity": "MAJOR",
      "artifact": "ui-ux-spec",
      "line": null,
      "clause": "QUALITY-RUBRIC.md §2 complete",
      "problem": "Deactivating a value permanently consumes its code under that type, and nothing in the pass says so. `UQ_MDL_LOOKUP_VALUE_TYPE_CODE` is (lookup_type_id, code) over every row, active or not (db-script BLOCK 5b), and no activate endpoint exists (ADR-MDL-005), so a code deactivated by mistake can never be re-created under its type through any surface this plan draws. The deactivate confirmation states only that the act 'is not reversible from this screen'; a user who then recreates the value is answered by MDL-409-VALUE-DUP routed inline to `code`, whose text — 'This code is already used within this type' — reads as a collision with a live value and offers no route out. The same closure question for a type is answered the same way: `key` is platform-unique forever and equally unrecoverable.",
      "fix": "Name the consequence where the act is taken and where it is met. In F4 and in ui-ux-spec SCR-MDL-001, the value deactivate confirmation states that the code stays reserved under this type and cannot be reused while no activate endpoint exists; in F2-QUERY VALUE CREATE, the MDL-409-VALUE-DUP inline routing adds that a deactivated value of the same code may be holding it, with the list's own inactive row as the evidence the user can see. Add the same sentence to the type deactivate confirmation for `key`.",
      "adr": false
    },
    {
      "id": "G8",
      "severity": "MINOR",
      "artifact": "ADR-MDL-008",
      "line": null,
      "clause": "GOVERNANCE-CORE.md §1 (ADR status)",
      "problem": "The status is stale. ADR-MDL-008's header reads 'Status: ACCEPTED (non-breaking) — supersedes ADR-MDL-001', while ADR-MDL-011's header and registry-exec-fe both record it as superseded ('Superseded, kept on disk and cited only by the decisions that replaced them: ADR-MDL-001, ADR-MDL-008'). A reader who opens the file alone is told a withdrawn decision is in force, and its content — plan-local labels, an ALIGN API row written '— examined nothing' — contradicts the plan as delivered.",
      "fix": "Rewrite the header the way ADR-MDL-001 already does it: 'Status : SUPERSEDED by ADR-MDL-011 (was ACCEPTED, non-breaking)', with the one-line note that the Contract ID lines it asked the backend for have since landed.",
      "adr": false
    },
    {
      "id": "G9",
      "severity": "MINOR",
      "artifact": "ADR-MDL-005",
      "line": null,
      "clause": "GOVERNANCE-CORE.md §1 (a decision follows its cited source)",
      "problem": "The Context table cites an SRS line the current SRS does not contain. It attributes 'Buttons: activate/deactivate per row at both levels' to B3; SRS B3 in this pass's state reads «لا إجراء تفعيل ولا محو نهائي على أي من المستويين» — no activate action at either level — and A2 puts reactivation out of scope. The frontend plan already notices this ('This SRS version names no `activate` action at either level … so the half-toggle ADR-MDL-005 also covers is not a gap in this version'), which leaves the ADR as the only artifact asserting the quotation.",
      "fix": "Drop the `activate` row from ADR-MDL-005's Context table, or mark it explicitly as a quotation from a superseded SRS revision, and keep the decision on the two by-id reads, which SRS B1 Operations still names. The plan's compensating sentence can then cite the corrected ADR instead of correcting it in passing.",
      "adr": false
    },
    {
      "id": "G10",
      "severity": "MINOR",
      "artifact": "frontend-execution-plan",
      "line": null,
      "clause": "QUALITY-RUBRIC.md §2 complete",
      "problem": "The optimistic reorder has no stated failure path. F2-QUERY VALUE REORDER holds 'the dragged order held until the call settles' and routes MDL-400-REORDER-MISMATCH to 'a user message on the value list', but never says what the list shows afterwards. The invalidation line is unconditional, so an implementer can read it as firing on failure too, and the user is left looking at a dragged order the server did not store.",
      "fix": "Add one line to the block: on any non-2xx answer the pending order is discarded and the list re-renders from the last persisted response; invalidation of `[lookup-values, *]` runs on success only.",
      "adr": false
    },
    {
      "id": "G11",
      "severity": "MINOR",
      "artifact": "frontend-execution-plan",
      "line": null,
      "clause": "QUALITY-RUBRIC.md §2 singular",
      "problem": "F1-SCR-MDL-002 models `LookupValueResponse[]` for the consumer read 'for completeness of the published surface', and states in the same block that no component renders it. The F2 block for API-MDL-011 already accounts for that surface, with its caller, its error answers and `Cache key : n/a` — so the F1 entry instructs an implementer to write a type nothing in the delivered frontend consumes.",
      "fix": "Remove the F1-MODEL for the consumer read and leave the accounting to the F2 block ADR-MDL-007 points at; if the model is kept, say in the block that it documents a foreign contract and is not to be emitted as a client type.",
      "adr": false
    }
  ],
  "extra_checks": [],
  "analyze_confirmed": [
    {
      "finding": "C5.16 / srs:47 / feature-unwanted US-MDL-005",
      "confirmed": true,
      "note": "Not a false positive. REQ-MDL-013 is the story's only requirement and is an `event` statement; SRS B2 states the groups come back whole with no state filter, and no statement anywhere in the pass says what the registry returns when the owner filter names a code no active type carries. The fix belongs to the SRS (P1), outside this pass's editable scope; this pass's artifacts inherit it rather than cause it."
    }
  ]
}
```

---
# ARTIFACTS UNDER REVISION (generated current state — write the source file listed above, not this copy)

<<<ARTIFACT: current-backend-execution-plan.md>>>
# BACKEND EXECUTION PLAN — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module : MDL   Version : v1   Profile : erp   Dialect : postgresql16
Framework : spring-boot-java (profile.stack.backend.framework)
Inputs : srs (v1, PRD-approved) · db-script (v1) · registry-srs (v1) · registry-db (v1)
Governance : FULL — the db-script exists and binds both entities of the SRS; no entity is `PENDING DB`.
Open ADRs : 0 new at this stage — 10 earlier ADRs applied, none BLOCKED (see DECISIONS APPLIED)
══════════════════════════════════════════════════════════════════

## PRE-GENERATION EXTRACTION — MDL v1 (working set; not part of the plan proper)

```
── FROM srs ──────────────────────────────────────────────────────────────
ENTITIES      2 — ENT-MDL-001 نوع اللوكب / LookupType, kind master, SHARED (owner)
                  ENT-MDL-002 قيمة اللوكب / LookupValue, kind lookup, SHARED (owner)
REQUIREMENTS  13 — REQ-MDL-001..013, 13 AC-MDL-001..013 (one AC per REQ)
RULES         4 — RULE-MDL-001..004, ar+en message on every one
SCREENS       2 — SCR-REQ-MDL-001 اللوكبات العامة / Generic Lookups (composite: master+detail,
                  search+entry on both levels) · SCR-REQ-MDL-002 سجل أنواع اللوكب حسب المالك /
                  Lookup-type registry by owner (composite: grouped browse, read-only)
PERMISSIONS   page MDL_LOOKUPS · VIEW (gateway) · CREATE · UPDATE · DELETE (soft deactivate)
              page MDL_TYPE_REGISTRY · VIEW (gateway) only
              roles: منسّق المنصة / Platform administrator · مدير قوائم الوحدة المالكة /
              Owning-module lookup manager · الحساب الخدمي للوحدة المستهلكة / consuming-module
              service account (VIEW on MDL_LOOKUPS only)
LOOKUPS       none owned, none consumed (SRS A6) — MDL is the mechanism every other module's
              coded lists run on; it does not consume itself
BUSINESS CODE none on either entity (SRS A3 §3.3 NUMBERING): `key` is a business key the
              registrar writes, not a number the platform generates; the value is identified
              by `code` within its type. No numbering-engine call anywhere in this module.
── FROM db-script ────────────────────────────────────────────────────────
TABLES        2 — MDL_LOOKUP_TYPE (ENT-MDL-001) · MDL_LOOKUP_VALUE (ENT-MDL-002)
PK GENERATION strategy `sequence` — SEQ_MDL_LOOKUP_TYPE and SEQ_MDL_LOOKUP_VALUE
              (db-script BLOCK 1), one per table; both PK columns are plain BIGINT NOT NULL
              with no identity clause, no sequence default and no trigger
COLUMNS       21 — DBF-MDL-001..021
CONSTRAINTS   PK_MDL_LOOKUP_TYPE · PK_MDL_LOOKUP_VALUE · UQ_MDL_LOOKUP_TYPE_KEY ·
              UQ_MDL_LOOKUP_VALUE_TYPE_CODE · FK_LOOKUP_VALUE_TYPE · no CHECK, no trigger
INDEXES       IDX_MDL_LOOKUP_TYPE_OWNER · IDX_MDL_LOOKUP_TYPE_NAME_AR ·
              IDX_MDL_LOOKUP_TYPE_NAME_EN · IDX_MDL_LOOKUP_VALUE_TYPE_SORT
XM            1 — XM-MDL-001 SOFT-READ, MDL_LOOKUP_TYPE.owner_module_code → SEC_MODULE_REG
              (SEC, ENT-SEC-004), status ACTIVE, no FK and no constraint
── FROM registries ───────────────────────────────────────────────────────
SHARED ENTITIES CONSUMED   ENT-SEC-004 (ModuleRegistry) — reached through XM-MDL-001, never
                           redeclared and never joined
EXISTING LOOKUP KEYS       none to reuse — this module registers no key of its own
ID RANGES already used     API: API-MDL-001..011 · QR: QR-MDL-001..015 — both continue from
                           the ranges registry-db records for this module version; nothing is
                           renumbered and neither sequence restarts. This run adds
                           QR-MDL-016 and QR-MDL-017 (the two sequence allocations the
                           `sequence` PK strategy makes explicit) and no API id.
──────────────────────────────────────────────────────────────────────────
§2A.3 extraction failure: no row. Every table, column, constraint, sequence, rule text,
message and permission name below is bound to an input; none is invented and none is PENDING.
```

## EXECUTION PLAN INDEX — MDL v1 — backend-execution-plan-mdl.md
Profile: erp · dialect: postgresql16 · framework: spring-boot-java
Open ADRs: 0 — analysis/decisions/MDL/

**ENTITY REGISTRY**
| ENT | Name (ar / en) | Table | Business code | Operations |
|---|---|---|---|---|
| ENT-MDL-001 | نوع اللوكب / LookupType | MDL_LOOKUP_TYPE | none — `key` is the registrar's business key | VIEW · CREATE · UPDATE · DELETE |
| ENT-MDL-002 | قيمة اللوكب / LookupValue | MDL_LOOKUP_VALUE | none — `code`, unique within its type | VIEW · CREATE · UPDATE · DELETE |

**FIELD REGISTRY**
| DBF | Property | Read-only | ENT |
|---|---|---|---|
| DBF-MDL-001 | lookupTypePk | Yes (drawn from the sequence) | ENT-MDL-001 |
| DBF-MDL-002 | key | Yes after creation (RULE-MDL-003) | ENT-MDL-001 |
| DBF-MDL-003 | ownerModuleCode | Yes after creation | ENT-MDL-001 |
| DBF-MDL-004 | nameAr | No | ENT-MDL-001 |
| DBF-MDL-005 | nameEn | No | ENT-MDL-001 |
| DBF-MDL-006 | isActiveFl | Yes (flipped by the deactivate endpoint only) | ENT-MDL-001 |
| DBF-MDL-007 | createdBy | Yes (audit) | ENT-MDL-001 |
| DBF-MDL-008 | createdAt | Yes (audit) | ENT-MDL-001 |
| DBF-MDL-009 | updatedBy | Yes (audit) | ENT-MDL-001 |
| DBF-MDL-010 | updatedAt | Yes (audit) | ENT-MDL-001 |
| DBF-MDL-011 | lookupValuePk | Yes (drawn from the sequence) | ENT-MDL-002 |
| DBF-MDL-012 | lookupTypeId | Yes after creation (the parent is chosen, never retyped) | ENT-MDL-002 |
| DBF-MDL-013 | code | Yes after creation (RULE-MDL-002 contract at consumers) | ENT-MDL-002 |
| DBF-MDL-014 | nameAr | No | ENT-MDL-002 |
| DBF-MDL-015 | nameEn | No | ENT-MDL-002 |
| DBF-MDL-016 | sortOrder | No | ENT-MDL-002 |
| DBF-MDL-017 | isActiveFl | Yes (flipped by the deactivate endpoint only) | ENT-MDL-002 |
| DBF-MDL-018 | createdBy | Yes (audit) | ENT-MDL-002 |
| DBF-MDL-019 | createdAt | Yes (audit) | ENT-MDL-002 |
| DBF-MDL-020 | updatedBy | Yes (audit) | ENT-MDL-002 |
| DBF-MDL-021 | updatedAt | Yes (audit) | ENT-MDL-002 |

**API REGISTRY**
| API | Operation | Verb | Path | Traces (REQ · DBF) |
|---|---|---|---|---|
| API-MDL-001 | search lookup types | POST | /api/v1/mdl/lookup-types/search | REQ-MDL-001, REQ-MDL-003, REQ-MDL-013 · DBF-MDL-002, DBF-MDL-003, DBF-MDL-004, DBF-MDL-005, DBF-MDL-006 |
| API-MDL-002 | create lookup type | POST | /api/v1/mdl/lookup-types | REQ-MDL-001, REQ-MDL-002 · DBF-MDL-002, DBF-MDL-003, DBF-MDL-004, DBF-MDL-005, DBF-MDL-006 |
| API-MDL-003 | update lookup type | PUT | /api/v1/mdl/lookup-types/{id} | REQ-MDL-003 · DBF-MDL-004, DBF-MDL-005, DBF-MDL-009, DBF-MDL-010 |
| API-MDL-004 | deactivate lookup type | DELETE | /api/v1/mdl/lookup-types/{id} | REQ-MDL-004 · DBF-MDL-006, DBF-MDL-009, DBF-MDL-010 |
| API-MDL-005 | search values of a type | POST | /api/v1/mdl/lookup-types/values/search | REQ-MDL-005 · DBF-MDL-012, DBF-MDL-013, DBF-MDL-014, DBF-MDL-015, DBF-MDL-016, DBF-MDL-017 |
| API-MDL-006 | create lookup value | POST | /api/v1/mdl/lookup-types/{id}/values | REQ-MDL-006, REQ-MDL-007 · DBF-MDL-012, DBF-MDL-013, DBF-MDL-014, DBF-MDL-015, DBF-MDL-016, DBF-MDL-017 |
| API-MDL-007 | update lookup value | PUT | /api/v1/mdl/lookup-values/{id} | REQ-MDL-008 · DBF-MDL-014, DBF-MDL-015, DBF-MDL-016, DBF-MDL-020, DBF-MDL-021 |
| API-MDL-008 | deactivate lookup value | DELETE | /api/v1/mdl/lookup-values/{id} | REQ-MDL-009 · DBF-MDL-017, DBF-MDL-020, DBF-MDL-021 |
| API-MDL-009 | reorder a type's values | PATCH | /api/v1/mdl/lookup-types/{id}/values/reorder | REQ-MDL-010 · DBF-MDL-016, DBF-MDL-020, DBF-MDL-021 |
| API-MDL-010 | browse the type registry by owner | POST | /api/v1/mdl/lookup-types/by-owner/search | REQ-MDL-013 · DBF-MDL-002, DBF-MDL-003, DBF-MDL-004, DBF-MDL-005, DBF-MDL-006 |
| API-MDL-011 | read active values by key (consumer) | GET | /api/v1/mdl/lookups | REQ-MDL-011, REQ-MDL-012 · DBF-MDL-002, DBF-MDL-006, DBF-MDL-013, DBF-MDL-016, DBF-MDL-017 |

**RULE REGISTRY**
| RULE | Name (en) | Scope | ENT | Message ar/en |
|---|---|---|---|---|
| RULE-MDL-001 | The owner module is registered in SEC | on create | ENT-MDL-001 | ✓ |
| RULE-MDL-002 | No duplicate code within one type | on create / on update | ENT-MDL-002 | ✓ |
| RULE-MDL-003 | A type's key is immutable after creation | on update | ENT-MDL-001 | ✓ |
| RULE-MDL-004 | An inactive type hides its values from consumers | on evaluate (every consumer read) | ENT-MDL-001, ENT-MDL-002 | ✓ |

**SCREEN REGISTRY**
| Screen | Type | ENT | Permission names |
|---|---|---|---|
| MDL_LOOKUPS | composite (master+detail, search+entry on both levels) | ENT-MDL-001, ENT-MDL-002 | PERM_MDL_LOOKUPS_VIEW, PERM_MDL_LOOKUPS_CREATE, PERM_MDL_LOOKUPS_UPDATE, PERM_MDL_LOOKUPS_DELETE |
| MDL_TYPE_REGISTRY | composite (grouped browse, read-only) | ENT-MDL-001 | PERM_MDL_TYPE_REGISTRY_VIEW |

**LOOKUP REGISTRY** — MDL owns no lookup key and consumes none (SRS A6). The table is written
empty rather than omitted, so the absence is a stated fact and not a gap: every key the
platform holds belongs to the module that registers it here, through API-MDL-002 and
API-MDL-006, and is seeded by that module — never by this one.

| Lookup key | Used in field (`DBF-*`) | `ENT-*` | Table owner module |
|---|---|---|---|
| — | — | — | — |

**QRC SUMMARY**
| QR | Operation | Phase | ENT |
|---|---|---|---|
| QR-MDL-001 | FIND_BY_CRITERIA | SVC-API | ENT-MDL-001 |
| QR-MDL-002 | SAVE | SVC-API | ENT-MDL-001 |
| QR-MDL-003 | UPDATE | SVC-API | ENT-MDL-001 |
| QR-MDL-004 | UPDATE (deactivate) | SVC-API | ENT-MDL-001 |
| QR-MDL-005 | FIND_BY_CRITERIA | SVC-API | ENT-MDL-002 |
| QR-MDL-006 | SAVE | SVC-API | ENT-MDL-002 |
| QR-MDL-007 | UPDATE | SVC-API | ENT-MDL-002 |
| QR-MDL-008 | UPDATE (deactivate) | SVC-API | ENT-MDL-002 |
| QR-MDL-009 | UPDATE (batch reorder) | SVC-API | ENT-MDL-002 |
| QR-MDL-010 | FIND_BY_CRITERIA | SVC-API | ENT-MDL-001 |
| QR-MDL-011 | FIND_BY_CRITERIA | SVC-API | ENT-MDL-002 |
| QR-MDL-012 | EXISTS (cross-module) | SVC-API | ENT-SEC-004 via XM-MDL-001 |
| QR-MDL-013 | EXISTS | SVC-API | ENT-MDL-001 |
| QR-MDL-014 | EXISTS | SVC-API | ENT-MDL-002 |
| QR-MDL-015 | FIND_ONE | SVC-API | ENT-MDL-001 |
| QR-MDL-016 | NATIVE (sequence allocation) | SVC-API | ENT-MDL-001 |
| QR-MDL-017 | NATIVE (sequence allocation) | SVC-API | ENT-MDL-002 |

**DB ALIGNMENT** — see the manifest below — ALIGNED ✓ / issues: 0
**XM STATUS** — 0 deferred · 1 ACTIVE (XM-MDL-001 → SEC), placed in INT-C and resolved in INT-R
**SECURITY** — 2 screens × 3 roles
**DBF ROWS** — 21
**XM ROWS** — 1
**API ROWS** — 11
**QR ROWS** — 17

## DB Alignment Manifest — MDL v1

Column names, DB types and SRS references are sourced by lookup from the db-script and are
not reproduced here. A required column that no endpoint writes carries its reason on the row.

| DBF | ENT | plan property | plan type | XM | status |
|---|---|---|---|---|---|
| DBF-MDL-001 | ENT-MDL-001 | lookupTypePk | Long | — | ✓ · system-generated (drawn from the table's sequence, `{entity}Pk`) |
| DBF-MDL-002 | ENT-MDL-001 | key | String | — | ✓ |
| DBF-MDL-003 | ENT-MDL-001 | ownerModuleCode | String | XM-MDL-001 (SOFT-READ, never blocking) | ✓ |
| DBF-MDL-004 | ENT-MDL-001 | nameAr | String | — | ✓ |
| DBF-MDL-005 | ENT-MDL-001 | nameEn | String | — | ✓ |
| DBF-MDL-006 | ENT-MDL-001 | isActiveFl | Boolean | — | ✓ |
| DBF-MDL-007 | ENT-MDL-001 | createdBy | String | — | ✓ · system-generated (audit) |
| DBF-MDL-008 | ENT-MDL-001 | createdAt | Instant | — | ✓ · system-generated (audit) |
| DBF-MDL-009 | ENT-MDL-001 | updatedBy | String | — | ✓ · system-generated (audit) |
| DBF-MDL-010 | ENT-MDL-001 | updatedAt | Instant | — | ✓ · system-generated (audit) |
| DBF-MDL-011 | ENT-MDL-002 | lookupValuePk | Long | — | ✓ · system-generated (drawn from the table's sequence, `{entity}Pk`) |
| DBF-MDL-012 | ENT-MDL-002 | lookupTypeId | Long | — | ✓ |
| DBF-MDL-013 | ENT-MDL-002 | code | String | — | ✓ |
| DBF-MDL-014 | ENT-MDL-002 | nameAr | String | — | ✓ |
| DBF-MDL-015 | ENT-MDL-002 | nameEn | String | — | ✓ |
| DBF-MDL-016 | ENT-MDL-002 | sortOrder | Integer | — | ✓ |
| DBF-MDL-017 | ENT-MDL-002 | isActiveFl | Boolean | — | ✓ |
| DBF-MDL-018 | ENT-MDL-002 | createdBy | String | — | ✓ · system-generated (audit) |
| DBF-MDL-019 | ENT-MDL-002 | createdAt | Instant | — | ✓ · system-generated (audit) |
| DBF-MDL-020 | ENT-MDL-002 | updatedBy | String | — | ✓ · system-generated (audit) |
| DBF-MDL-021 | ENT-MDL-002 | updatedAt | Instant | — | ✓ · system-generated (audit) |

Legend ✓ aligned · ✗ type mismatch (finding) · ⏸ deferred XM.
No derived or computed property exists in this module: every plan property above is a column
of one of the two tables, so no row carries `— (derived)` and no row needs an ADR.
`ownerModuleCode` is marked with its XM and **not** ⏸ — a SOFT-READ carries no constraint and
blocks no column (db-script §2.1); the value is stored whether or not SEC is reachable at the
moment of a later read, and only the create-time check consults SEC at all.

## Query Reference Catalog (QR-MDL-*)

> Logical specification only, never executable code: the implementer rewrites every entry with
> the real entity classes, mapped property names and the project's query strategy. Copying an
> entry into production code is a violation.

#### QR-MDL-016 — allocate a lookup type primary key
Phase        : SVC-API
API          : API-MDL-002
Entity       : ENT-MDL-001
Operation    : NATIVE
Intent       : draw the next primary key for a new lookup type from the object the db-script declares
Logical spec : SELECT nextval FROM SEQUENCE SEQ_MDL_LOOKUP_TYPE
Join         : NONE
Transaction  : READ_WRITE (participates in the caller's create transaction)
Locking      : NONE — the sequence allocates atomically, so two simultaneous creates can never
               receive the same key. This is why the key is never computed as MAX+1.
Pagination   : NO
Filters      : —
Result shape : count (one BIGINT value)
Null handling: never null

#### QR-MDL-017 — allocate a lookup value primary key
Phase        : SVC-API
API          : API-MDL-006
Entity       : ENT-MDL-002
Operation    : NATIVE
Intent       : draw the next primary key for a new lookup value from the object the db-script declares
Logical spec : SELECT nextval FROM SEQUENCE SEQ_MDL_LOOKUP_VALUE
Join         : NONE
Transaction  : READ_WRITE (participates in the caller's create transaction)
Locking      : NONE — allocated atomically by the sequence
Pagination   : NO
Filters      : —
Result shape : count (one BIGINT value)
Null handling: never null

#### QR-MDL-001 — search lookup types
Phase        : SVC-API
API          : API-MDL-001
Entity       : ENT-MDL-001
Operation    : FIND_BY_CRITERIA
Intent       : the page of lookup types matching the screen's filters — the master list of SCR-REQ-MDL-001
Logical spec : SELECT … FROM MDL_LOOKUP_TYPE WHERE [key LIKE :key] AND [owner_module_code = :owner]
               AND [(name_ar LIKE :name OR name_en LIKE :name)] AND [is_active_fl = :active]
               ORDER BY key — page/size
Join         : NONE — single-table; the owner module code is displayed as the stored code and
               is never joined to SEC (join governance, and XM-MDL-001 is a SOFT-READ)
Transaction  : READ_ONLY
Locking      : NONE — nothing is decided on and written back
Pagination   : YES (Page<T>) — default size 20, maximum 200
Filters      : key: LIKE · ownerModuleCode: EXACT · name: LIKE over both languages at once ·
               isActiveFl: EXACT
Result shape : full entity
Null handling: updatedBy / updatedAt null until the first update
Notes        : an empty result is success with empty content, never "not found"

#### QR-MDL-002 — persist a new lookup type
Phase        : SVC-API
API          : API-MDL-002
Entity       : ENT-MDL-001
Operation    : SAVE
Intent       : store a registered lookup type, active, with its key, owner and both names
Logical spec : INSERT INTO MDL_LOOKUP_TYPE (lookup_type_pk, key, owner_module_code, name_ar,
               name_en, is_active_fl, created_by, created_at) VALUES (…)
Join         : NONE
Transaction  : READ_WRITE
Locking      : the UNIQUE constraint is the guard. Two simultaneous registrations of the same
               key must not both succeed: `UQ_MDL_LOOKUP_TYPE_KEY` lets exactly one INSERT
               commit and the loser's integrity violation is mapped to MDL-409-TYPE-DUP. The
               EXISTS pre-check (QR-MDL-013) is a friendly message, never the guard — two
               requests both pass it.
Pagination   : NO
Filters      : —
Result shape : full entity
Null handling: updated_by / updated_at stay null until the first update

#### QR-MDL-003 — rename a lookup type
Phase        : SVC-API
API          : API-MDL-003
Entity       : ENT-MDL-001
Operation    : UPDATE
Intent       : replace the stored Arabic and English names of an existing type (REQ-MDL-003)
Logical spec : UPDATE MDL_LOOKUP_TYPE SET name_ar = :nameAr, name_en = :nameEn,
               updated_by = :principal, updated_at = :now WHERE lookup_type_pk = :id
Join         : NONE
Transaction  : READ_WRITE
Locking      : NONE, and the reason is stated rather than assumed: the statement writes the
               values the request carried, not a value derived from a prior read, so two
               simultaneous renames end with one of the two submissions stored whole — never a
               mixture. `key` and `owner_module_code` are absent from the statement, so neither
               can be moved by a race (RULE-MDL-003). No version column exists in the
               db-script, so no optimistic-lock check is specified in v1.
Pagination   : NO
Filters      : lookupTypePk: EXACT
Result shape : full entity
Null handling: —

#### QR-MDL-004 — deactivate a lookup type
Phase        : SVC-API
API          : API-MDL-004
Entity       : ENT-MDL-001
Operation    : UPDATE
Intent       : take a type out of service without removing it or touching its values (REQ-MDL-004)
Logical spec : UPDATE MDL_LOOKUP_TYPE SET is_active_fl = FALSE, updated_by = :principal,
               updated_at = :now WHERE lookup_type_pk = :id AND is_active_fl = TRUE
Join         : NONE
Transaction  : READ_WRITE
Locking      : the conditional predicate carries the state, so two simultaneous deactivations
               of the same type cannot both affect a row. The second affects none, and that is
               success, not an error: the requested end state already holds and REQ-MDL-004
               names no "already deactivated" outcome. Nothing is written to the type's values
               — RULE-MDL-004 is a read-time filter, not a cascade.
Pagination   : NO
Filters      : lookupTypePk: EXACT · isActiveFl: EXACT
Result shape : count (rows affected) + the stored entity
Null handling: —

#### QR-MDL-005 — search the values of one type
Phase        : SVC-API
API          : API-MDL-005
Entity       : ENT-MDL-002
Operation    : FIND_BY_CRITERIA
Intent       : the detail list of SCR-REQ-MDL-001 — every value of the selected type, active and
               inactive alike, in display order (REQ-MDL-005, AC-MDL-005)
Logical spec : SELECT … FROM MDL_LOOKUP_VALUE WHERE lookup_type_id = :typeId
               AND [code LIKE :code] ORDER BY sort_order ASC, code ASC
Join         : NONE — the parent type is already the caller's selection; its name is not
               re-read here
Transaction  : READ_ONLY
Locking      : NONE — nothing is decided on and written back
Pagination   : NO — the detail is bounded by one type and is returned whole, ordered
               (SRS §B2); the master list is the paginated half
Filters      : lookupTypeId: EXACT (mandatory — the detail is confined to the selected type) ·
               code: LIKE
Result shape : full entity
Null handling: updatedBy / updatedAt null until the first update
Notes        : deactivated values are part of this result by requirement — they disappear only
               from the consumer read (AC-MDL-009)

#### QR-MDL-006 — persist a new lookup value
Phase        : SVC-API
API          : API-MDL-006
Entity       : ENT-MDL-002
Operation    : SAVE
Intent       : store a new value under a selected type, active, with its code, labels and rank
Logical spec : INSERT INTO MDL_LOOKUP_VALUE (lookup_value_pk, lookup_type_id, code, name_ar,
               name_en, sort_order, is_active_fl, created_by, created_at) VALUES (…)
Join         : NONE
Transaction  : READ_WRITE
Locking      : the UNIQUE constraint is the guard. Two simultaneous creates of the same code
               under the same type must not both succeed: `UQ_MDL_LOOKUP_VALUE_TYPE_CODE`
               admits exactly one and the loser's integrity violation is mapped to
               MDL-409-VALUE-DUP. QR-MDL-014 is the friendly pre-check, never the guard.
Pagination   : NO
Filters      : —
Result shape : full entity
Null handling: updated_by / updated_at stay null until the first update

#### QR-MDL-007 — update a lookup value
Phase        : SVC-API
API          : API-MDL-007
Entity       : ENT-MDL-002
Operation    : UPDATE
Intent       : replace the stored labels and rank of an existing value (REQ-MDL-008)
Logical spec : UPDATE MDL_LOOKUP_VALUE SET name_ar = :nameAr, name_en = :nameEn,
               sort_order = :sortOrder, updated_by = :principal, updated_at = :now
               WHERE lookup_value_pk = :id
Join         : NONE
Transaction  : READ_WRITE
Locking      : NONE, stated: every written column comes from the request, none from a prior
               read, so concurrent edits end with one submission stored whole. `code` and
               `lookup_type_id` are absent from the statement, so no race can move a value
               between types or change the contract its consumers stored.
Pagination   : NO
Filters      : lookupValuePk: EXACT
Result shape : full entity
Null handling: —

#### QR-MDL-008 — deactivate a lookup value
Phase        : SVC-API
API          : API-MDL-008
Entity       : ENT-MDL-002
Operation    : UPDATE
Intent       : withdraw a value from consumer reads without losing the rows that reference it
Logical spec : UPDATE MDL_LOOKUP_VALUE SET is_active_fl = FALSE, updated_by = :principal,
               updated_at = :now WHERE lookup_value_pk = :id AND is_active_fl = TRUE
Join         : NONE
Transaction  : READ_WRITE
Locking      : the state is carried in the predicate, so of two simultaneous deactivations only
               one affects a row; the other affects none and that is the same success — the
               requested end state holds either way, and REQ-MDL-009 names no second outcome.
Pagination   : NO
Filters      : lookupValuePk: EXACT · isActiveFl: EXACT
Result shape : count (rows affected) + the stored entity
Null handling: —

#### QR-MDL-009 — persist a new display order for a type's values
Phase        : SVC-API
API          : API-MDL-009
Entity       : ENT-MDL-002
Operation    : UPDATE
Intent       : set every submitted value's rank to its position in the submitted order (REQ-MDL-010)
Logical spec : UPDATE MDL_LOOKUP_VALUE SET sort_order = :position, updated_by = :principal,
               updated_at = :now WHERE lookup_value_pk = :id AND lookup_type_id = :typeId
               — one statement per submitted id, all inside one transaction
Join         : NONE
Transaction  : READ_WRITE — the whole reorder commits or none of it does, so no caller ever
               observes half a reordering
Locking      : two simultaneous reorders of the same type are possible and neither is refused:
               each writes an explicit rank per id in one transaction, so the later transaction's
               order is the one stored whole — there is no read-then-write and no derived value
               to lose. `sort_order` carries no UNIQUE constraint, so a value created
               concurrently with a reorder keeps its own rank and may tie; the tie is broken by
               `code` in every ordered read (QR-MDL-005, QR-MDL-011), which is why a tie is a
               display detail and not a corruption. The `lookup_type_id` predicate is what makes
               an id from another type unwritable rather than merely rejected beforehand.
Pagination   : NO
Filters      : lookupValuePk: SET (the submitted ids) · lookupTypeId: EXACT
Result shape : count (rows affected per statement; a count below the submitted size means an id
               did not belong to the type → MDL-400-REORDER-MISMATCH, and the transaction rolls back)
Null handling: —

#### QR-MDL-010 — browse lookup types grouped by owner module
Phase        : SVC-API
API          : API-MDL-010
Entity       : ENT-MDL-001
Operation    : FIND_BY_CRITERIA
Intent       : the audit view of SCR-REQ-MDL-002 — every active type, grouped under its owner
               module code (REQ-MDL-013, AC-MDL-013)
Logical spec : SELECT … FROM MDL_LOOKUP_TYPE WHERE is_active_fl = TRUE
               AND [owner_module_code = :owner] AND [key LIKE :key]
               ORDER BY owner_module_code ASC, key ASC
Join         : NONE — single-table. The grouping is assembled in the service layer from an
               ordered single-table read; the owner module's own name is NOT fetched from SEC,
               because the screen groups by the stored code and the module publishes no
               grouped-name contract for it (join governance; XM-MDL-001 stays a create-time check)
Transaction  : READ_ONLY
Locking      : NONE — nothing is decided on and written back
Pagination   : NO — the result is bounded by the number of registered modules (SRS §B2)
Filters      : ownerModuleCode: EXACT · key: LIKE
Result shape : projection (key, nameAr, nameEn, ownerModuleCode), assembled into owner groups
Null handling: —
Notes        : inactive types are excluded by the requirement itself, so this read carries no
               state filter for a caller to widen

#### QR-MDL-011 — the active values of an active type, by key
Phase        : SVC-API
API          : API-MDL-011
Entity       : ENT-MDL-002
Operation    : FIND_BY_CRITERIA
Intent       : the reason the module exists — a consuming module's backend asks for a type's
               values by key and receives the active ones, ordered (REQ-MDL-011, AC-MDL-011)
Logical spec : SELECT v.… FROM MDL_LOOKUP_VALUE v JOIN MDL_LOOKUP_TYPE t
               ON v.lookup_type_id = t.lookup_type_pk
               WHERE t.key = :key AND t.is_active_fl = TRUE AND v.is_active_fl = TRUE
               ORDER BY v.sort_order ASC, v.code ASC
Join         : required — intra-module (value → its type), because the caller addresses the set
               by the type's `key` and the value table carries no key column (db-script §2,
               inbound note). No ADR is owed: the join governance rule covers a join to another
               entity for a filter, and both entities are this module's own; nothing here joins
               a lookup label from a foreign table.
Transaction  : READ_ONLY
Locking      : NONE — nothing is decided on and written back
Pagination   : NO — a coded list is returned whole, ordered; consumers render it as a select list
Filters      : key: EXACT · both active flags: EXACT (fixed TRUE, never caller-supplied)
Result shape : projection (code, nameAr, nameEn, sortOrder) — the code is what the consumer
               stores, the names are what it displays
Null handling: —
Notes        : a type that exists and is active but holds no active value returns an empty list
               with success — that is the FIND_BY_CRITERIA default, and it is a different answer
               from the unknown key of REQ-MDL-012, which QR-MDL-015 separates before this query runs

#### QR-MDL-012 — is the owner module registered in SEC?
Phase        : SVC-API
API          : API-MDL-002
Entity       : ENT-SEC-004 (ModuleRegistry, reached through XM-MDL-001) — not a table of this module
Operation    : EXISTS
Intent       : RULE-MDL-001 — refuse a lookup type whose owner module code has no registry row
Logical spec : not SQL. This module issues no statement against SEC's schema and holds no FK to
               it: the question is asked through SEC's injected in-process interface (INT-C,
               XM-MDL-001) and the answer is a boolean. It is catalogued as a QR because it is a
               repository-level fact the create path depends on, and the implementer must see
               that it is NOT a join and NOT a query of this module's own tables.
Join         : NONE — a cross-module call, never a SQL join (db-script §2.1, precedent ADR-FIN-001)
Transaction  : READ_ONLY — it participates in the caller's create transaction as a read; it
               opens none of its own and it commits nothing in SEC
Locking      : NONE, and the limit is stated: the answer can go stale between this check and the
               INSERT, and that is accepted — RULE-MDL-001's own Test-Hint says a module later
               unregistered in SEC does not invalidate the types it already owns, so there is no
               invariant here for two requests to break.
Pagination   : NO
Filters      : ownerModuleCode: EXACT
Result shape : count (exists / does not)
Null handling: an absent or inactive registry row is the "does not exist" answer → MDL-409-MODULE-NOT-REGISTERED

#### QR-MDL-013 — is this type key already taken?
Phase        : SVC-API
API          : API-MDL-002
Entity       : ENT-MDL-001
Operation    : EXISTS
Intent       : answer a duplicate key with the catalog message instead of a raw integrity error
Logical spec : SELECT COUNT(*) FROM MDL_LOOKUP_TYPE WHERE key = :key
Join         : NONE
Transaction  : READ_ONLY (inside the create transaction)
Locking      : NONE — deliberately. This query is not the uniqueness guard and must not be read
               as one: `UQ_MDL_LOOKUP_TYPE_KEY` is (QR-MDL-002). Two simultaneous creates both
               pass this check.
Pagination   : NO
Filters      : key: EXACT
Result shape : count
Null handling: —
Notes        : the key is immutable (RULE-MDL-003), so no update path needs the
               "excluding the current PK" form of this check

#### QR-MDL-014 — is this code already used under this type?
Phase        : SVC-API
API          : API-MDL-006
Entity       : ENT-MDL-002
Operation    : EXISTS
Intent       : RULE-MDL-002 — a friendly refusal before the database's own would fire
Logical spec : SELECT COUNT(*) FROM MDL_LOOKUP_VALUE WHERE lookup_type_id = :typeId AND code = :code
Join         : NONE
Transaction  : READ_ONLY (inside the create transaction)
Locking      : NONE — deliberately: `UQ_MDL_LOOKUP_VALUE_TYPE_CODE` is the guard (QR-MDL-006).
               The same code under a different type is legal and this predicate says so.
Pagination   : NO
Filters      : lookupTypeId: EXACT · code: EXACT
Result shape : count
Null handling: —

#### QR-MDL-015 — resolve a type by key and confirm it is active
Phase        : SVC-API
API          : API-MDL-011
Entity       : ENT-MDL-001
Operation    : FIND_ONE
Intent       : REQ-MDL-012 and RULE-MDL-004 — separate "no such key" from "a key whose values
               are simply all inactive" before the value read runs
Logical spec : SELECT … FROM MDL_LOOKUP_TYPE WHERE key = :key
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — nothing is decided on and written back
Pagination   : NO
Filters      : key: EXACT
Result shape : full entity (the caller needs `is_active_fl` as well as existence)
Null handling: an empty result, and an inactive row, both answer MDL-404-TYPE-KEY — a consuming
               module is told the same thing either way, because in both cases the platform
               holds no usable list under that key (AC-MDL-012, RULE-MDL-004)

**Standard operation defaults** apply as the engine states them (FIND_ONE by PK → read-only,
not found → the catalog row; FIND_BY_CRITERIA → read-only, filters and allowed sort fields
declared per search, an empty result is success; SAVE → PK and audit fields system-set;
UPDATE → PK, business key and audit excluded from the request; deactivate → `soft` per
profile.stack.db.delete_semantics, the active flag flipped; EXISTS → uniqueness check) except
where an entry above overrides them.

**No usage check precedes deactivation**, and the reason is a requirement, not an omission: a
lookup type or value that consumers already stored is exactly what the soft flag preserves.
Deactivating removes the value from future consumer reads (RULE-MDL-004, REQ-MDL-011) and
leaves every stored code intact, so there is no "in use" state that could block the act —
AC-MDL-004 asserts the type's three values stay stored and unchanged.

**Join governance**: one join exists in this module, QR-MDL-011, and it is intra-module and
required by the key-addressed read. No query joins another module's table; no display name of a
coded value is joined anywhere, because this module IS the store those names come from and it
returns them directly; the grouped browse (QR-MDL-010) assembles its groups in the service
layer over a single-table ordered read rather than joining SEC.

## ERROR CATALOG — MDL v1

Envelope `LocalizedException → {code, messageAr, messageEn}`; the runtime code format is
declared once in PHASE 1 — CORE and every row below is an instance of it. Downstream consumers
cite the **code** and never reproduce the message text.

| code | RULE | API | HTTP | trigger | message-AR | message-EN |
|---|---|---|---|---|---|---|
| MDL-409-MODULE-NOT-REGISTERED | RULE-MDL-001 | API-MDL-002 | 409 | the submitted owner module code has no registry row in SEC (QR-MDL-012) | الوحدة المالكة غير مسجّلة في وحدة الأمان | The owning module is not registered in the Security module |
| MDL-409-TYPE-DUP | PLATFORM-STD — ADR-SEC-002 | API-MDL-002 | 409 | the submitted key is already held by another type; the platform-unique key of REQ-MDL-001 is a structural constraint, not a business rule | هذا المفتاح مستخدم بالفعل | This key is already in use |
| MDL-409-VALUE-DUP | RULE-MDL-002 | API-MDL-006, API-MDL-007 | 409 | the submitted code already exists under the same type (QR-MDL-014, and `UQ_MDL_LOOKUP_VALUE_TYPE_CODE` behind it) | هذا الرمز مستخدم بالفعل ضمن هذا النوع | This code is already used within this type |
| MDL-404-TYPE | PLATFORM-STD — ADR-SEC-002 | API-MDL-003, API-MDL-004, API-MDL-005, API-MDL-006, API-MDL-009 | 404 | the path or filter names a lookup type id that no row carries | نوع اللوكب غير موجود | The lookup type was not found |
| MDL-404-VALUE | PLATFORM-STD — ADR-SEC-002 | API-MDL-007, API-MDL-008 | 404 | the path names a lookup value id that no row carries | قيمة اللوكب غير موجودة | The lookup value was not found |
| MDL-400-REORDER-MISMATCH | PLATFORM-STD — ADR-SEC-002 | API-MDL-009 | 400 | a submitted id does not belong to the type in the path, so the reorder writes nothing and rolls back | إحدى القيم لا تنتمي لهذا النوع | One of the values does not belong to this type |
| MDL-404-TYPE-KEY | RULE-MDL-004 | API-MDL-011 | 404 | the requested key matches no type, or matches an inactive one (QR-MDL-015) — an unknown key is never answered with an empty success (REQ-MDL-012) | لا يوجد نوع لوكب بهذا المفتاح | No lookup type exists with this key |

**The platform's own rows, not this module's** — a forbidden response, an unauthenticated
request, a malformed body and an unexpected failure are raised by the shared handler every
module sits behind, with the platform's own code strings; this module mints no code for them
and must not, or two code strings would exist for one condition. They are listed here so an
endpoint block can cite them, and they are outside the module code format by construction:

| code | RULE | API | HTTP | trigger | message-AR | message-EN |
|---|---|---|---|---|---|---|
| ACCESS_DENIED | PLATFORM-STD — ADR-SEC-002 | every endpoint of PHASE 3 | 403 | the caller lacks the screen's gateway permission or the action's own | لا تملك صلاحية تنفيذ هذا الإجراء | You are not authorised to perform this action |
| VALIDATION_ERROR | PLATFORM-STD — ADR-SEC-002 | every endpoint carrying a body | 400 | a required field is missing or exceeds the column width the db-script declares | البيانات المُرسلة غير صحيحة | The submitted data is not valid |
| INTERNAL_ERROR | PLATFORM-STD — ADR-SEC-002 | every endpoint of PHASE 3 | 500 | an unexpected server-side failure; the database error itself never reaches the caller | حدث خطأ غير متوقع | An unexpected error occurred |

**No row describes a network failure**, and that is a consequence of the platform's shape, not
an oversight: `profile.conventions.module_interface` is `in_process`, so XM-MDL-001 is an
injected call inside one deployable. There is no hop to time out and no service-unavailable
condition for this module to report. A 503 row was carried by the previous revision of this
plan for exactly that imagined failure; it is struck here, and it could not have been raised by
any code path — `profile.stack.backend.api.http_statuses` declares no 503 either.

**RULE-MDL-003 carries no catalog row, deliberately.** A type's key is immutable because it is
absent from the update request entirely (the rule's own Test-Hint): a submitted key is ignored,
never rejected, so a row here would be raisable by nothing. This is the same reading the SRS
states, not a decision taken here.

---

<!-- PHASE:CORE:START traces=REQ-MDL-005,REQ-MDL-010,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013 -->
## PHASE 1 — CORE

**Layers** — controller → service → mapper → domain → repository
(`profile.stack.backend.layers`). Domain-behaviour placement: `domain_classes` — the
single-entity invariants (activation state, rank assignment) are methods on the domain classes;
the service layer owns the cross-module check, the transaction boundary and the mapping between
request and domain.

**Type mapping — postgresql16 → Java** (from `profile.stack.db.syntax_map`; column types only —
the PK-generation clause is not a type):

| postgresql16 | Java |
|---|---|
| BIGINT (pk) | Long |
| VARCHAR(n) | String |
| BOOLEAN | Boolean |
| TIMESTAMPTZ | Instant |
| INTEGER | Integer |

The INTEGER row is not a deviation taken here: `profile.stack.db.syntax_map` carries no integer
row and the db-script declared the addition itself, for `sort_order`, in its own header. No
other type in this module departs from the map, and no ADR is owed.

**Primary keys** — strategy `sequence` (`profile.stack.db.pk_generation`). The application
draws every key from the table's own sequence before the insert: `SEQ_MDL_LOOKUP_TYPE`
(QR-MDL-016) and `SEQ_MDL_LOOKUP_VALUE` (QR-MDL-017). The db-script declares no identity clause,
no sequence default and no PK trigger, so a JPA identity or table generator would contradict the
deployed schema; the entity contract is `GenerationType.SEQUENCE`. This paragraph is the
correction db-script §4.2 asks this stage for — the previous revision of this plan carried
`GENERATED ALWAYS AS IDENTITY` on both BINDINGS lines, which described nothing that was built.

**Runtime error-code format** — `{MOD}-{http}[-{SLUG}]`
(`profile.stack.backend.api.error_code_format`; `{MOD}` is this module's code, `{http}` is the
row's HTTP status, `{SLUG}` is SCREAMING-KEBAB and the bracketed half is optional). Every
module row of the ERROR CATALOG above is an instance of this one string, and every status it
carries is one `profile.stack.backend.api.http_statuses` declares. The three platform rows
beside them carry the shared handler's own code strings and are not instances of it, which is
stated there rather than disguised.

**Error signalling** — `LocalizedException → {code, messageAr, messageEn}`; responses are
wrapped in `ApiResponse<T>`, pages in `Page<T>`.

**Transaction scope** — `READ_ONLY` for every FIND_* and EXISTS query reached on a read path;
`READ_WRITE` for every SAVE and UPDATE, with the reorder's whole batch inside one transaction.
No endpoint of this module needs `REQUIRES_NEW`.

**Search contract** — the filters and sort fields this module offers are exactly the ones the
two screen requirements name, and are not widened here:

| Screen | filters | ordering |
|---|---|---|
| SCR-REQ-MDL-001 master | key (LIKE) · ownerModuleCode (EXACT) · name (LIKE, both languages at once) · isActiveFl (EXACT) | key ASC — paged, default 20, maximum 200 |
| SCR-REQ-MDL-001 detail | lookupTypeId (EXACT, mandatory) · code (LIKE) | sortOrder ASC, code ASC — not paged, bounded by one type |
| SCR-REQ-MDL-002 | ownerModuleCode (EXACT) · key (LIKE) | ownerModuleCode ASC, key ASC — not paged, bounded by the module count |

An empty result is success on every one of them. A request to sort on any other field is
refused by the shared handler's VALIDATION_ERROR; the module offers no free-form sort parameter.

**Lookup values** — all LOV values are runtime-loaded from the lookup module and no enum is
hardcoded in an API or a field spec (`profile.conventions.lookups`). In this module the rule is
not vacuous, it is inverted: MDL is the store that serves it. API-MDL-011 is the runtime load
every other module performs, and this module consumes no coded list of its own (SRS A6).
`isActiveFl` is a platform boolean, not a coded list, and `ownerModuleCode` is a module code
read from SEC's registry, not a lookup key.

**Numbering** — document numbers come from the platform numbering engine and are never
generated in a module (`profile.conventions.numbering`). Neither entity is a numbered document:
`key` and `code` are written by the user and validated for uniqueness, never allocated
(SRS A3 §3.3 NUMBERING). No endpoint of PHASE 3 calls a numbering service.

**Workflow engine** — forbidden (`profile.conventions.workflow_engine`). Both entities are
two-state (active / inactive) with one transition and no reverse (SRS A7); that is a flag, not
a workflow, and no engine is introduced for it.

**Audit fields** — `createdBy`, `createdAt`, `updatedBy`, `updatedAt` are filled by the platform
on both tables and never appear in a request DTO. The db-script sets no database DEFAULT on
them deliberately, so exactly one writer exists.

**Identity** — the authenticated principal reaches the audit columns as a string handed over by
the standard platform interceptor. That is a value, not a dependency on SEC's table, and it is
why no XM row exists for it (db-script §2, precedent ADR-FIN-001).

**Authorization** — evaluated by the platform authorisation layer on the page code and the
action the operation carries. No role name is written into any service of this module: services
see permissions, never roles. `VIEW` is the gateway — without it no other permission applies.

**Cross-module access** — one mechanism, stated once and not chosen per row:
`profile.conventions.module_interface` is `in_process`, so this module reaches SEC through an
injected interface inside the same deployable. No HTTP client, no base path to another module,
no timeout, no retry policy and no network error path exists anywhere in this plan.

**Languages** — every message in this plan is present in ar and en, and every field of both
entities carries an ar and an en label (PHASE 2). Both entities are bilingual by requirement:
`nameAr` and `nameEn` are stored on each, and the consumer read returns both so the caller can
render either without a second call.
<!-- PHASE:CORE:END -->

<!-- PHASE:DATA-DOM:START traces=REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013 -->
## PHASE 2 — DATA-DOM

Entity count is 2 — below the split self-check, so no SUB is opened and both entity blocks are
written flat, master first and its dependent lookup second, the order the db-script creates the
tables in.

#### ENT-MDL-001 — نوع اللوكب / LookupType      kind: master
BINDINGS   table `MDL_LOOKUP_TYPE` · PK `lookup_type_pk` (DBF-MDL-001) · PK generation
`sequence` → sequence `SEQ_MDL_LOOKUP_TYPE` (db-script BLOCK 1, one per table) · db-script
version v1. The PK column is a plain BIGINT NOT NULL: no identity clause, no default, no trigger.
BUSINESS CODE  none — the entity is not a numbered document and its identifier is never used
outside the system (SRS A3 §3.3 NUMBERING). `key` (DBF-MDL-002) plays the role the `master`
default field `code` would play: it is the registrar's own business key, unique platform-wide
through `UQ_MDL_LOOKUP_TYPE_KEY`, generated by nobody and immutable after creation
(RULE-MDL-003). No second column named `code` exists on this table and none is invented here.
DEFAULT FIELDS (profile.conventions.entity_defaults.master: nameAr, nameEn, code, isActiveFl,
createdBy, createdAt, updatedBy, updatedAt) — carried in full, with `key` standing in for `code`
by the SRS A3 note, and `ownerModuleCode` added above the set because the namespacing policy
requires it.
FIELDS
| DBF | property | column | type (postgresql16) | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-MDL-001 | lookupTypePk | lookup_type_pk | BIGINT | NOT NULL | Yes | PK_MDL_LOOKUP_TYPE | معرّف نوع اللوكب / LookupType id |
| DBF-MDL-002 | key | key | VARCHAR(50) | NOT NULL | create-only | UQ_MDL_LOOKUP_TYPE_KEY | المفتاح / Key |
| DBF-MDL-003 | ownerModuleCode | owner_module_code | VARCHAR(10) | NOT NULL | create-only | — (XM-MDL-001, application read) | رمز الوحدة المالكة / Owner module code |
| DBF-MDL-004 | nameAr | name_ar | VARCHAR(200) | NOT NULL | No | — (IDX_MDL_LOOKUP_TYPE_NAME_AR) | الاسم (عربي) / Name (Arabic) |
| DBF-MDL-005 | nameEn | name_en | VARCHAR(200) | NOT NULL | No | — (IDX_MDL_LOOKUP_TYPE_NAME_EN) | الاسم (إنجليزي) / Name (English) |
| DBF-MDL-006 | isActiveFl | is_active_fl | BOOLEAN | NOT NULL | Yes | — (DEFAULT TRUE) | نشط / Active |
| DBF-MDL-007 | createdBy | created_by | VARCHAR(100) | NOT NULL | Yes | — | أنشأه / Created by |
| DBF-MDL-008 | createdAt | created_at | TIMESTAMPTZ | NOT NULL | Yes | — | تاريخ الإنشاء / Created at |
| DBF-MDL-009 | updatedBy | updated_by | VARCHAR(100) | NULL | Yes | — | عدّله / Updated by |
| DBF-MDL-010 | updatedAt | updated_at | TIMESTAMPTZ | NULL | Yes | — | تاريخ التعديل / Updated at |

DTO MEMBERSHIP
- create-request `LookupTypeCreateRequest {key, ownerModuleCode, nameAr, nameEn}` — excludes the
  PK, the active flag (the platform sets it TRUE, REQ-MDL-001) and the four audit fields.
- update-request `LookupTypeUpdateRequest {nameAr, nameEn}` — excludes the PK (a path
  parameter), `key` (RULE-MDL-003 — absent from the schema, so a submitted key is ignored, not
  rejected), `ownerModuleCode` (create-only by the same reading), the active flag (only the
  deactivate endpoint moves it) and the audit fields.
- response `LookupTypeResponse {lookupTypePk, key, ownerModuleCode, nameAr, nameEn, isActiveFl,
  createdBy, createdAt, updatedBy, updatedAt}` — the PK and the business key are always present;
  there is no business code to add.

LOOKUP FIELDS  none — no property of this entity stores a lookup code. `ownerModuleCode` is a
module code validated against SEC's registry (XM-MDL-001), not a value of any coded list, and
the module owns no key of its own (SRS A6).

DOMAIN RULES
- **RULE-MDL-001** — الوحدة المالكة مسجَّلة في الأمان / The owner module is registered in SEC ·
  scope CREATE · trigger: on create · statement: "The system shall reject a lookup type
  registration whose owner module code has no ModuleRegistry row in SEC." · message ar:
  «الوحدة المالكة غير مسجّلة في وحدة الأمان» · en: "The owning module is not registered in the
  Security module" · Data source: ENT-MDL-001.ownerModuleCode · DB enforcement: none — no
  cross-module foreign key exists in this platform; the check is an application read carried by
  the XM-MDL-001 contract (QR-MDL-012) · owner layer: service. On create only: a module
  unregistered later does not invalidate the types it already owns (the rule's own Test-Hint).
- **RULE-MDL-003** — مفتاح النوع لا يتغيّر بعد الإنشاء / A type's key is immutable after creation ·
  scope UPDATE · trigger: on update · statement: "The system shall prevent editing a lookup
  type's key after creation." · message ar: «لا يمكن تعديل مفتاح نوع اللوكب بعد إنشائه» · en:
  "A lookup type's key cannot be changed after creation" · Data source: ENT-MDL-001.key · DB
  enforcement: none and none needed — the key is absent from the update DTO and from QR-MDL-003's
  statement, so no code path can write it · owner layer: controller/service (DTO shape). No
  error-catalog row: nothing can raise one.
- **RULE-MDL-004** — النوع المعطَّل يحجب قيمه عن المستهلك / An inactive type hides its values from
  consumers · scope ALL · trigger: on evaluate, at every consumer read · statement: "While a
  lookup type is inactive, the system shall exclude its values from consumer reads." · message
  ar: «هذا النوع معطّل حاليًا» · en: "This lookup type is currently inactive" · Data source:
  ENT-MDL-001.isActiveFl, ENT-MDL-002.isActiveFl · DB enforcement: none — a read-time filter
  carried in QR-MDL-015 and in QR-MDL-011's own predicate, never a stored change: deactivating a
  type writes nothing to its values (AC-MDL-004) · owner layer: repository predicate + service.

STATE MACHINE  state column `is_active_fl` (DBF-MDL-006) · values TRUE (نشط / active), FALSE
(معطَّل / inactive) · initial TRUE at creation (REQ-MDL-001) · transition TRUE → FALSE, trigger
`deactivate`, actor the registrar · terminal FALSE — there is no reverse transition in v1
(reactivation is out of scope, SRS A2, and no endpoint offers it) · no invalid-transition rule
is needed: the second deactivation of an already-inactive type affects no row and reports the
same success (QR-MDL-004). The row is never removed.

CROSS-MODULE  XM-MDL-001 — SOFT-READ · local column `owner_module_code` (DBF-MDL-003) ·
target SEC · SEC_MODULE_REG (ENT-SEC-004) · status ACTIVE · no foreign key and no constraint.

OPERATIONS  VIEW · CREATE · UPDATE · DELETE — the four actions SCR-REQ-MDL-001 §B4 grants on the
page code `MDL_LOOKUPS`, where `DELETE` is the soft deactivation of REQ-MDL-004. Every one of
them is answered by an `API-*` block in PHASE 3 that names this entity; the read-only browse of
SCR-REQ-MDL-002 is the same `VIEW` action on a second page code.

REPOSITORY OPS → QR-MDL-016 (NATIVE, key allocation) · QR-MDL-001 (FIND_BY_CRITERIA) · QR-MDL-002
(SAVE) · QR-MDL-003 (UPDATE) · QR-MDL-004 (UPDATE, deactivate) · QR-MDL-010 (FIND_BY_CRITERIA,
grouped) · QR-MDL-013 (EXISTS, key uniqueness) · QR-MDL-015 (FIND_ONE, by key).

#### ENT-MDL-002 — قيمة اللوكب / LookupValue      kind: lookup
BINDINGS   table `MDL_LOOKUP_VALUE` · PK `lookup_value_pk` (DBF-MDL-011) · PK generation
`sequence` → sequence `SEQ_MDL_LOOKUP_VALUE` (db-script BLOCK 1, one per table) · db-script
version v1. The PK column is a plain BIGINT NOT NULL with no identity clause and no default.
BUSINESS CODE  none — the value is identified by `code` (DBF-MDL-013) within its type, unique
through `UQ_MDL_LOOKUP_VALUE_TYPE_CODE`, written by the user and never generated. The same code
under another type is legal, so it is not a platform-wide business number and no numbering
source exists for it.
DEFAULT FIELDS (profile.conventions.entity_defaults.lookup: code, nameAr, nameEn, sortOrder,
isActiveFl) — carried in full, plus the four audit fields, which the `lookup` default set omits
and the SRS A3 note deliberately adds here: these rows are edited by a user through a screen,
not seeded once, so the audit trail is owed.
FIELDS
| DBF | property | column | type (postgresql16) | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-MDL-011 | lookupValuePk | lookup_value_pk | BIGINT | NOT NULL | Yes | PK_MDL_LOOKUP_VALUE | معرّف القيمة / LookupValue id |
| DBF-MDL-012 | lookupTypeId | lookup_type_id | BIGINT | NOT NULL | create-only | FK_LOOKUP_VALUE_TYPE | نوع اللوكب / Lookup type |
| DBF-MDL-013 | code | code | VARCHAR(50) | NOT NULL | create-only | UQ_MDL_LOOKUP_VALUE_TYPE_CODE | الرمز / Code |
| DBF-MDL-014 | nameAr | name_ar | VARCHAR(200) | NOT NULL | No | — | الاسم (عربي) / Name (Arabic) |
| DBF-MDL-015 | nameEn | name_en | VARCHAR(200) | NOT NULL | No | — | الاسم (إنجليزي) / Name (English) |
| DBF-MDL-016 | sortOrder | sort_order | INTEGER | NOT NULL | No | — (DEFAULT 0, IDX_MDL_LOOKUP_VALUE_TYPE_SORT) | الترتيب / Sort order |
| DBF-MDL-017 | isActiveFl | is_active_fl | BOOLEAN | NOT NULL | Yes | — (DEFAULT TRUE) | نشط / Active |
| DBF-MDL-018 | createdBy | created_by | VARCHAR(100) | NOT NULL | Yes | — | أنشأها / Created by |
| DBF-MDL-019 | createdAt | created_at | TIMESTAMPTZ | NOT NULL | Yes | — | تاريخ الإنشاء / Created at |
| DBF-MDL-020 | updatedBy | updated_by | VARCHAR(100) | NULL | Yes | — | عدّلها / Updated by |
| DBF-MDL-021 | updatedAt | updated_at | TIMESTAMPTZ | NULL | Yes | — | تاريخ التعديل / Updated at |

DTO MEMBERSHIP
- create-request `LookupValueCreateRequest {code, nameAr, nameEn, sortOrder}` — the parent type
  arrives as the path parameter, never in the body (the type is chosen, not retyped); excludes
  the PK, the active flag (set TRUE by the platform, REQ-MDL-006) and the four audit fields.
- update-request `LookupValueUpdateRequest {nameAr, nameEn, sortOrder}` — excludes the PK, the
  parent type, `code` (the contract consumers stored), the active flag and the audit fields.
- reorder-request `LookupValueReorderRequest {orderedValueIds}` — ids only; the rank is the
  position in the submitted list, never a number the caller supplies (REQ-MDL-010).
- response `LookupValueResponse {lookupValuePk, lookupTypeId, code, nameAr, nameEn, sortOrder,
  isActiveFl, createdBy, createdAt, updatedBy, updatedAt}`. The consumer read returns the
  narrowed projection of QR-MDL-011, not this whole shape.

LOOKUP FIELDS  none — this entity is a lookup value; it does not reference one.

DOMAIN RULES
- **RULE-MDL-002** — لا رمز مكرَّر ضمن النوع الواحد / No duplicate code within one type · scope
  CREATE|UPDATE · trigger: on create / on update · statement: "The system shall reject a lookup
  value whose code already exists under the same lookup type." · message ar: «هذا الرمز مستخدم
  بالفعل ضمن هذا النوع» · en: "This code is already used within this type" · Data source:
  ENT-MDL-002.lookupTypeId, ENT-MDL-002.code · DB enforcement:
  `UQ_MDL_LOOKUP_VALUE_TYPE_CODE` (lookup_type_id, code) — structural, and the real guard —
  with the service pre-check QR-MDL-014 producing the friendly message before the database
  would · owner layer: repository constraint + service. On the update path the rule cannot be
  violated at all: `code` is absent from the update DTO, so the row keeps the code it was
  created with; the trigger is listed as the SRS states it and the endpoint records why the
  update half raises nothing.
- **RULE-MDL-004** applies to this entity as well, as the SRS scopes it to both: an inactive
  type's values are excluded from consumer reads without any flag on the values changing. Its
  full text, message and Data source are stated once, in ENT-MDL-001 above; it is not restated
  here as a second rule.

STATE MACHINE  state column `is_active_fl` (DBF-MDL-017) · values TRUE (نشط / active), FALSE
(معطَّل / inactive) · initial TRUE at creation (REQ-MDL-006) · transition TRUE → FALSE, trigger
`deactivate`, actor the lookup manager · terminal FALSE — no reactivation in v1 (SRS A2) · the
deactivated value stays visible on the management screen and disappears only from the consumer
read (AC-MDL-009). The row is never removed.

CROSS-MODULE  none — this entity is internally dependent: it reaches its type through
`lookup_type_id` (DBF-MDL-012), an intra-module foreign key, and touches no other module.

OPERATIONS  VIEW · CREATE · UPDATE · DELETE — the same four actions of the page code
`MDL_LOOKUPS`, exercised on the detail half of the composite screen, where `DELETE` is the soft
deactivation of REQ-MDL-009 and the reorder of REQ-MDL-010 is an `UPDATE` of the rank column,
not a fifth action: SRS §B4 grants no other.

REPOSITORY OPS → QR-MDL-017 (NATIVE, key allocation) · QR-MDL-005 (FIND_BY_CRITERIA) · QR-MDL-006
(SAVE) · QR-MDL-007 (UPDATE) · QR-MDL-008 (UPDATE, deactivate) · QR-MDL-009 (UPDATE, batch
reorder) · QR-MDL-011 (FIND_BY_CRITERIA, the consumer read) · QR-MDL-014 (EXISTS, code uniqueness
within the type).
<!-- PHASE:DATA-DOM:END -->

<!-- PHASE:SVC-API:START traces=REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013 -->
## PHASE 3 — SVC-API

API count is 11, at or above the split threshold of 8, so this phase is split before its first
block into the three groups the profile names — CRUD, SEARCH and INT — and every atom sits
inside one of them. The eleven are exactly the operations SRS §B5 names across both screen
requirements: none is added and none is dropped.

Three reads are `POST …/search` rather than `GET`, because their filter sets are composite and
that is the platform's search convention; SRS §B5 states the same three that way. ADR-MDL-002
records the divergence this stage now closes — the previous revision of this plan carried the
`GET` forms it had predicted before implementation, and it was the plan, not the requirement,
that was stale.

<!-- SUB:SVC-API-SEARCH:START traces=REQ-MDL-001,REQ-MDL-003,REQ-MDL-005,REQ-MDL-013 -->
### SUB SVC-API-SEARCH — the reads a screen performs

<!-- API:API-MDL-001:START traces=REQ-MDL-001,REQ-MDL-003,REQ-MDL-013,DBF-MDL-002,DBF-MDL-003,DBF-MDL-004,DBF-MDL-005,DBF-MDL-006 -->
### API-MDL-001 — search lookup types
Entity       : ENT-MDL-001 · operation VIEW
Endpoint     : POST /api/v1/mdl/lookup-types/search   verb: POST
Layers       : controller → `LookupTypeController.search` ; service → `LookupTypeService.search`
Request      : body `LookupTypeSearchRequest` — a composite filter set over the properties PHASE 1 declares for this screen: key (DBF-MDL-002, String, LIKE) · ownerModuleCode (DBF-MDL-003, String, EXACT) · name (DBF-MDL-004 and DBF-MDL-005 together, String, LIKE over both languages at once) · isActiveFl (DBF-MDL-006, Boolean, EXACT) · page, size (default 20, maximum 200). No path param. The verb carries a body and mutates nothing.
Response     : 200 · `Page<LookupTypeResponse>` {lookupTypePk, key, ownerModuleCode, nameAr, nameEn, isActiveFl, createdBy, createdAt, updatedBy, updatedAt} · paginated (`Page<T>`) · envelope `ApiResponse<T>`
Validations  : none of this module's RULEs fires on a read — RULE-MDL-004 is scoped to the consumer read (API-MDL-011), not to the management screen, which shows inactive rows by requirement (AC-MDL-005). The requested sort field is confined to the set PHASE 1 declares.
Errors       : VALIDATION_ERROR (400, PLATFORM-STD) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD). An empty page is success, never a not-found row.
Orchestration : authorise → bind the declared filters → load (QR-MDL-001, table MDL_LOOKUP_TYPE) → map into the page envelope. This endpoint writes no column of any kind.
Repository   : QR-MDL-001 · join NONE · transaction READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes.
Security     : screen MDL_LOOKUPS · permission `PERM_MDL_LOOKUPS_VIEW` (the gateway action) — verified before processing
Localization : messages in ar + en; every row carries both name fields, so the caller renders either language without a second call
<!-- API:API-MDL-001:END -->

<!-- API:API-MDL-005:START traces=REQ-MDL-005,DBF-MDL-012,DBF-MDL-013,DBF-MDL-014,DBF-MDL-015,DBF-MDL-016,DBF-MDL-017 -->
### API-MDL-005 — search the values of a type
Entity       : ENT-MDL-002 · operation VIEW
Endpoint     : POST /api/v1/mdl/lookup-types/values/search   verb: POST
Layers       : controller → `LookupValueController.search` ; service → `LookupValueService.search`
Request      : body `LookupValueSearchRequest` — lookupTypeId (DBF-MDL-012, Long, EXACT, mandatory: the detail is confined to the selected type, REQ-MDL-005) · code (DBF-MDL-013, String, LIKE). No paging: the result is bounded by one type and returned whole, ordered.
Response     : 200 · list of `LookupValueResponse` {lookupValuePk, lookupTypeId, code, nameAr, nameEn, sortOrder, isActiveFl, createdBy, createdAt, updatedBy, updatedAt}, ordered by sortOrder then code · not paginated · envelope `ApiResponse<T>`
Validations  : none — the management detail shows active and inactive values alike (AC-MDL-005); RULE-MDL-004 governs the consumer read only.
Errors       : MDL-404-TYPE (404, PLATFORM-STD — the filter names a type id no row carries) · VALIDATION_ERROR (400, PLATFORM-STD) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD). A type with no values at all is an empty list and success.
Orchestration : authorise → bind the mandatory type filter → load (QR-MDL-005, table MDL_LOOKUP_VALUE) → map. This endpoint writes no column of any kind.
Repository   : QR-MDL-005 · join NONE · transaction READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes.
Security     : screen MDL_LOOKUPS · permission `PERM_MDL_LOOKUPS_VIEW` (the gateway action) — verified before processing
Localization : messages in ar + en; both label fields are returned on every row
<!-- API:API-MDL-005:END -->

<!-- API:API-MDL-010:START traces=REQ-MDL-013,DBF-MDL-002,DBF-MDL-003,DBF-MDL-004,DBF-MDL-005,DBF-MDL-006 -->
### API-MDL-010 — browse the type registry by owner
Entity       : ENT-MDL-001 · operation VIEW
Endpoint     : POST /api/v1/mdl/lookup-types/by-owner/search   verb: POST
Layers       : controller → `LookupTypeController.browseByOwner` ; service → `LookupTypeService.browseByOwner`
Request      : body `LookupTypeByOwnerSearchRequest` — ownerModuleCode (DBF-MDL-003, String, EXACT — the group heading itself) · key (DBF-MDL-002, String, LIKE). No paging and no state filter: REQ-MDL-013 admits active types only, so `is_active_fl` (DBF-MDL-006) is fixed TRUE in the query and is not a parameter a caller can widen.
Response     : 200 · list of owner groups, each carrying its ownerModuleCode and its types {key, nameAr, nameEn, ownerModuleCode} · not paginated · envelope `ApiResponse<T>`
Validations  : none of this module's RULEs fires on this read. The grouping is assembled from the ordered result, not from a second query and not from a join to SEC.
Errors       : VALIDATION_ERROR (400, PLATFORM-STD) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD). No registered type at all is an empty list and success.
Orchestration : authorise → bind the declared filters → load ordered by owner then key (QR-MDL-010, table MDL_LOOKUP_TYPE) → assemble the groups in the service layer → map. This endpoint writes no column of any kind.
Repository   : QR-MDL-010 · join NONE (single-table; the grouping is a service-layer fold) · transaction READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes.
Security     : screen MDL_TYPE_REGISTRY · permission `PERM_MDL_TYPE_REGISTRY_VIEW` (the gateway action of its own page) — verified before processing. This is the one endpoint of the module served by the second page code.
Localization : messages in ar + en; each type row carries both names
<!-- API:API-MDL-010:END -->
<!-- SUB:SVC-API-SEARCH:END -->

<!-- SUB:SVC-API-CRUD:START traces=REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010 -->
### SUB SVC-API-CRUD — the writes of the generic lookups screen

<!-- API:API-MDL-002:START traces=REQ-MDL-001,REQ-MDL-002,DBF-MDL-002,DBF-MDL-003,DBF-MDL-004,DBF-MDL-005,DBF-MDL-006 -->
### API-MDL-002 — create lookup type
Entity       : ENT-MDL-001 · operation CREATE
Endpoint     : POST /api/v1/mdl/lookup-types   verb: POST
Layers       : controller → `LookupTypeController.create` ; service → `LookupTypeService.create`
Request      : body `LookupTypeCreateRequest` — key (DBF-MDL-002, String, required, maximum 50, UQ_MDL_LOOKUP_TYPE_KEY) · ownerModuleCode (DBF-MDL-003, String, required, maximum 10, validated through XM-MDL-001) · nameAr (DBF-MDL-004, String, required, maximum 200) · nameEn (DBF-MDL-005, String, required, maximum 200). No path param, no query param. Excluded system fields: the key column of the table, the active flag and the four audit fields — a value supplied for any of them is ignored.
Response     : 201 · `LookupTypeResponse` {lookupTypePk, key, ownerModuleCode, nameAr, nameEn, isActiveFl, createdBy, createdAt, updatedBy, updatedAt} · not paginated · envelope `ApiResponse<T>`
Validations  : RULE-MDL-001 "The system shall reject a lookup type registration whose owner module code has no ModuleRegistry row in SEC." (trigger: on create · ar: «الوحدة المالكة غير مسجّلة في وحدة الأمان» · en: "The owning module is not registered in the Security module") — checked through XM-MDL-001 (QR-MDL-012), an injected in-process call, never an HTTP request. Key uniqueness is structural (REQ-MDL-001): QR-MDL-013 produces the message, `UQ_MDL_LOOKUP_TYPE_KEY` produces the guarantee.
Errors       : MDL-409-MODULE-NOT-REGISTERED (409, RULE-MDL-001) · MDL-409-TYPE-DUP (409, PLATFORM-STD) · VALIDATION_ERROR (400, PLATFORM-STD) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD)
Orchestration : authorise → validate (RULE-MDL-001) → integrate: ask SEC through XM-MDL-001 whether the owner module is registered (QR-MDL-012) → check the key is free (QR-MDL-013) → set the fields the request does not carry: the active flag is_active_fl (DBF-MDL-006) to TRUE (REQ-MDL-001), the audit columns created_by (DBF-MDL-007) and created_at (DBF-MDL-008) from the principal and the clock → allocate the key (QR-MDL-016, from SEQ_MDL_LOOKUP_TYPE) → persist (QR-MDL-002, table MDL_LOOKUP_TYPE)
Repository   : QR-MDL-016, QR-MDL-013, QR-MDL-012, QR-MDL-002 · join NONE (QR-MDL-012 is a cross-module call, not a SQL join) · transaction READ_WRITE
Concurrency  : two simultaneous registrations of the same key must not both be stored. The guard is `UQ_MDL_LOOKUP_TYPE_KEY`: exactly one INSERT commits and the other's integrity violation is translated to MDL-409-TYPE-DUP. QR-MDL-013 is not the guard — both requests pass it, which is precisely why the constraint is named here. The primary key is allocated atomically by the sequence (QR-MDL-016), so no two creates can collide on it either. The SEC check (QR-MDL-012) guards nothing concurrent by design: its answer may go stale, and RULE-MDL-001's Test-Hint accepts that.
Security     : screen MDL_LOOKUPS · permission `PERM_MDL_LOOKUPS_CREATE`, gated by `PERM_MDL_LOOKUPS_VIEW` — both verified before the method body runs
Localization : messages in ar + en (see Validations and the ERROR CATALOG); both name fields are required, so no type can be registered in one language only
<!-- API:API-MDL-002:END -->

<!-- API:API-MDL-003:START traces=REQ-MDL-003,DBF-MDL-004,DBF-MDL-005,DBF-MDL-009,DBF-MDL-010 -->
### API-MDL-003 — update lookup type
Entity       : ENT-MDL-001 · operation UPDATE
Endpoint     : PUT /api/v1/mdl/lookup-types/{id}   verb: PUT
Layers       : controller → `LookupTypeController.update` ; service → `LookupTypeService.update`
Request      : path param `{id}` → DBF-MDL-001 (Long, required) · body `LookupTypeUpdateRequest` — nameAr (DBF-MDL-004, String, required, maximum 200) · nameEn (DBF-MDL-005, String, required, maximum 200). Excluded system fields: the business key (RULE-MDL-003), the owner module code, the active flag and the four audit fields.
Response     : 200 · `LookupTypeResponse` {lookupTypePk, key, ownerModuleCode, nameAr, nameEn, isActiveFl, createdBy, createdAt, updatedBy, updatedAt} · not paginated · envelope `ApiResponse<T>`
Validations  : RULE-MDL-003 "The system shall prevent editing a lookup type's key after creation." (trigger: on update · ar: «لا يمكن تعديل مفتاح نوع اللوكب بعد إنشائه» · en: "A lookup type's key cannot be changed after creation") — enforced by DTO shape and by QR-MDL-003's statement, which never names the key column; a submitted key is ignored, not rejected, so this rule raises no catalog row and no branch exists to test.
Errors       : MDL-404-TYPE (404, PLATFORM-STD) · VALIDATION_ERROR (400, PLATFORM-STD) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD)
Orchestration : authorise → load by id → an absent row raises MDL-404-TYPE → update the two names and write the columns the request does not carry: updated_by (DBF-MDL-009) and updated_at (DBF-MDL-010) from the principal and the clock (QR-MDL-003, table MDL_LOOKUP_TYPE) → map. The key, the owner module code and the active flag are not in the statement and are never written here.
Repository   : QR-MDL-003 · join NONE · transaction READ_WRITE
Concurrency  : NONE by the stated reading, not by omission: the endpoint allocates no unique value, and every column it writes comes from the request rather than from a prior read, so two simultaneous renames end with one submission stored whole. No version column exists in the db-script, so no optimistic-lock rejection is specified in v1; last writer wins, and both callers see their own submission echoed by the response they receive.
Security     : screen MDL_LOOKUPS · permission `PERM_MDL_LOOKUPS_UPDATE`, gated by `PERM_MDL_LOOKUPS_VIEW` — both verified before processing
Localization : messages in ar + en; both names are required, so a rename cannot leave one language behind
<!-- API:API-MDL-003:END -->

<!-- API:API-MDL-004:START traces=REQ-MDL-004,DBF-MDL-006,DBF-MDL-009,DBF-MDL-010 -->
### API-MDL-004 — deactivate lookup type
Entity       : ENT-MDL-001 · operation DELETE
Endpoint     : DELETE /api/v1/mdl/lookup-types/{id}   verb: DELETE
Layers       : controller → `LookupTypeController.deactivate` ; service → `LookupTypeService.deactivate`
Request      : path param `{id}` → DBF-MDL-001 (Long, required). No query params and no body.
Response     : 200 · `LookupTypeResponse` {lookupTypePk, key, ownerModuleCode, nameAr, nameEn, isActiveFl, createdBy, createdAt, updatedBy, updatedAt} with the flag now false · not paginated · envelope `ApiResponse<T>`
Validations  : RULE-MDL-004 "While a lookup type is inactive, the system shall exclude its values from consumer reads." (trigger: on evaluate · ar: «هذا النوع معطّل حاليًا» · en: "This lookup type is currently inactive") — this endpoint is what makes the rule's condition true; the rule itself is enforced at read time by API-MDL-011, and no value of the type is written here (AC-MDL-004).
Errors       : MDL-404-TYPE (404, PLATFORM-STD) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD)
Orchestration : authorise → load by id → an absent row raises MDL-404-TYPE → flip the flag the request does not carry: is_active_fl (DBF-MDL-006) to FALSE, with updated_by (DBF-MDL-009) and updated_at (DBF-MDL-010) written from the principal and the clock, in one conditional statement (QR-MDL-004, table MDL_LOOKUP_TYPE) → map. The verb is DELETE and the effect is `soft` (profile.stack.db.delete_semantics): no row is removed here or anywhere in this module, and the type's values keep their own flags untouched.
Repository   : QR-MDL-004 · join NONE · transaction READ_WRITE
Concurrency  : two simultaneous deactivations of the same type must not produce two different outcomes. The state is carried in QR-MDL-004's own predicate, so only one statement affects a row; the other affects none, and that is reported as the same success, because the end state REQ-MDL-004 asks for holds either way and the SRS names no "already deactivated" error. Nothing is read, decided on and then written: the target state is a constant.
Security     : screen MDL_LOOKUPS · permission `PERM_MDL_LOOKUPS_DELETE`, gated by `PERM_MDL_LOOKUPS_VIEW` — both verified before processing
Localization : messages in ar + en
<!-- API:API-MDL-004:END -->

<!-- API:API-MDL-006:START traces=REQ-MDL-006,REQ-MDL-007,DBF-MDL-012,DBF-MDL-013,DBF-MDL-014,DBF-MDL-015,DBF-MDL-016,DBF-MDL-017 -->
### API-MDL-006 — create lookup value
Entity       : ENT-MDL-002 · operation CREATE
Endpoint     : POST /api/v1/mdl/lookup-types/{id}/values   verb: POST
Layers       : controller → `LookupValueController.create` ; service → `LookupValueService.create`
Request      : path param `{id}` → lookup_type_id (DBF-MDL-012, Long, required, FK_LOOKUP_VALUE_TYPE) — the parent type is selected, never retyped · body `LookupValueCreateRequest` — code (DBF-MDL-013, String, required, maximum 50, UQ_MDL_LOOKUP_VALUE_TYPE_CODE) · nameAr (DBF-MDL-014, String, required, maximum 200) · nameEn (DBF-MDL-015, String, required, maximum 200) · sortOrder (DBF-MDL-016, Integer, required, 0 when the caller sends none). Excluded system fields: the value's own key, the active flag and the four audit fields.
Response     : 201 · `LookupValueResponse` {lookupValuePk, lookupTypeId, code, nameAr, nameEn, sortOrder, isActiveFl, createdBy, createdAt, updatedBy, updatedAt} · not paginated · envelope `ApiResponse<T>`
Validations  : RULE-MDL-002 "The system shall reject a lookup value whose code already exists under the same lookup type." (trigger: on create · ar: «هذا الرمز مستخدم بالفعل ضمن هذا النوع» · en: "This code is already used within this type") — QR-MDL-014 produces the message and `UQ_MDL_LOOKUP_VALUE_TYPE_CODE` produces the guarantee. The same code under a different type is accepted, by the rule's own Test-Hint.
Errors       : MDL-409-VALUE-DUP (409, RULE-MDL-002) · MDL-404-TYPE (404, PLATFORM-STD — the path names a type that does not exist) · VALIDATION_ERROR (400, PLATFORM-STD) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD)
Orchestration : authorise → confirm the parent type exists → validate (RULE-MDL-002, QR-MDL-014) → set the fields the request does not carry: the active flag is_active_fl (DBF-MDL-017) to TRUE (REQ-MDL-006), the audit columns created_by (DBF-MDL-018) and created_at (DBF-MDL-019) from the principal and the clock → allocate the key (QR-MDL-017, from SEQ_MDL_LOOKUP_VALUE) → persist with the parent id from the path (QR-MDL-006, table MDL_LOOKUP_VALUE)
Repository   : QR-MDL-017, QR-MDL-014, QR-MDL-006 · join NONE · transaction READ_WRITE
Concurrency  : two simultaneous creates of the same code under the same type must not both be stored. The guard is `UQ_MDL_LOOKUP_VALUE_TYPE_CODE`, which admits one INSERT and turns the other into MDL-409-VALUE-DUP; QR-MDL-014 is the friendly message and not the guard, since both requests pass it. The primary key comes from the sequence (QR-MDL-017) and cannot collide.
Security     : screen MDL_LOOKUPS · permission `PERM_MDL_LOOKUPS_CREATE`, gated by `PERM_MDL_LOOKUPS_VIEW` — both verified before processing
Localization : messages in ar + en; both labels are required, so no value can be created in one language only
<!-- API:API-MDL-006:END -->

<!-- API:API-MDL-007:START traces=REQ-MDL-008,DBF-MDL-014,DBF-MDL-015,DBF-MDL-016,DBF-MDL-020,DBF-MDL-021 -->
### API-MDL-007 — update lookup value
Entity       : ENT-MDL-002 · operation UPDATE
Endpoint     : PUT /api/v1/mdl/lookup-values/{id}   verb: PUT
Layers       : controller → `LookupValueController.update` ; service → `LookupValueService.update`
Request      : path param `{id}` → DBF-MDL-011 (Long, required) · body `LookupValueUpdateRequest` — nameAr (DBF-MDL-014, String, required, maximum 200) · nameEn (DBF-MDL-015, String, required, maximum 200) · sortOrder (DBF-MDL-016, Integer, required). Excluded system fields: the value's key, its parent type, its code, the active flag and the four audit fields.
Response     : 200 · `LookupValueResponse` {lookupValuePk, lookupTypeId, code, nameAr, nameEn, sortOrder, isActiveFl, createdBy, createdAt, updatedBy, updatedAt} · not paginated · envelope `ApiResponse<T>`
Validations  : RULE-MDL-002 "The system shall reject a lookup value whose code already exists under the same lookup type." (trigger: on update · ar: «هذا الرمز مستخدم بالفعل ضمن هذا النوع» · en: "This code is already used within this type") — the SRS scopes the rule to the update path too, and this endpoint closes it by shape: `code` is absent from the request and from QR-MDL-007's statement, so an update cannot create a duplicate and raises no row of its own. The catalog row for MDL-409-VALUE-DUP names this endpoint so the mapping is complete in both directions, and the only path that can emit it here is a database integrity violation that no request can provoke.
Errors       : MDL-404-VALUE (404, PLATFORM-STD) · MDL-409-VALUE-DUP (409, RULE-MDL-002 — unreachable through the request shape, listed because the rule names this trigger) · VALIDATION_ERROR (400, PLATFORM-STD) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD)
Orchestration : authorise → load by id → an absent row raises MDL-404-VALUE → update the two labels and the rank, writing the columns the request does not carry: updated_by (DBF-MDL-020) and updated_at (DBF-MDL-021) from the principal and the clock (QR-MDL-007, table MDL_LOOKUP_VALUE) → map. The code, the parent type and the active flag are not in the statement.
Repository   : QR-MDL-007 · join NONE · transaction READ_WRITE
Concurrency  : NONE by the stated reading: no unique value is allocated and every written column comes from the request, not from a prior read, so concurrent edits end with one submission stored whole rather than a mixture. A concurrent reorder writes the same rank column (QR-MDL-009) and the later transaction wins; because `sort_order` carries no uniqueness constraint and every ordered read breaks ties by code, the outcome is a display order, never an inconsistency.
Security     : screen MDL_LOOKUPS · permission `PERM_MDL_LOOKUPS_UPDATE`, gated by `PERM_MDL_LOOKUPS_VIEW` — both verified before processing
Localization : messages in ar + en
<!-- API:API-MDL-007:END -->

<!-- API:API-MDL-008:START traces=REQ-MDL-009,DBF-MDL-017,DBF-MDL-020,DBF-MDL-021 -->
### API-MDL-008 — deactivate lookup value
Entity       : ENT-MDL-002 · operation DELETE
Endpoint     : DELETE /api/v1/mdl/lookup-values/{id}   verb: DELETE
Layers       : controller → `LookupValueController.deactivate` ; service → `LookupValueService.deactivate`
Request      : path param `{id}` → DBF-MDL-011 (Long, required). No query params and no body.
Response     : 200 · `LookupValueResponse` {lookupValuePk, lookupTypeId, code, nameAr, nameEn, sortOrder, isActiveFl, createdBy, createdAt, updatedBy, updatedAt} with the flag now false · not paginated · envelope `ApiResponse<T>`
Validations  : none of this module's RULEs refuses a deactivation — SRS §B3 lists no rule for the act, and the deactivated value stays visible on the management screen while disappearing from the consumer read (AC-MDL-009).
Errors       : MDL-404-VALUE (404, PLATFORM-STD) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD)
Orchestration : authorise → load by id → an absent row raises MDL-404-VALUE → flip the flag the request does not carry: is_active_fl (DBF-MDL-017) to FALSE, with updated_by (DBF-MDL-020) and updated_at (DBF-MDL-021) written from the principal and the clock, in one conditional statement (QR-MDL-008, table MDL_LOOKUP_VALUE) → map. The effect is `soft`: the row survives, and every consumer row that stored this code keeps its meaning.
Repository   : QR-MDL-008 · join NONE · transaction READ_WRITE
Concurrency  : two simultaneous deactivations of the same value cannot produce two outcomes: the state is in QR-MDL-008's predicate, so one statement affects a row and the other affects none, and both report the same success — the end state REQ-MDL-009 asks for holds either way, and the SRS names no second outcome to distinguish.
Security     : screen MDL_LOOKUPS · permission `PERM_MDL_LOOKUPS_DELETE`, gated by `PERM_MDL_LOOKUPS_VIEW` — both verified before processing
Localization : messages in ar + en
<!-- API:API-MDL-008:END -->

<!-- API:API-MDL-009:START traces=REQ-MDL-010,DBF-MDL-016,DBF-MDL-020,DBF-MDL-021 -->
### API-MDL-009 — reorder a type's values
Entity       : ENT-MDL-002 · operation UPDATE
Endpoint     : PATCH /api/v1/mdl/lookup-types/{id}/values/reorder   verb: PATCH
Layers       : controller → `LookupValueController.reorder` ; service → `LookupValueService.reorder`
Request      : path param `{id}` → lookup_type_id (DBF-MDL-012, Long, required) · body `LookupValueReorderRequest` — orderedValueIds (list of DBF-MDL-011, required, the values in their new display order). No rank number is accepted from the caller: the rank is the position in the list (REQ-MDL-010).
Response     : 200 · list of `LookupValueResponse`, in the stored order · not paginated · envelope `ApiResponse<T>`
Validations  : none of this module's RULEs applies to a reorder; the one precondition is membership — every submitted id must belong to the type in the path, which QR-MDL-009 enforces in the statement rather than in a branch before it.
Errors       : MDL-400-REORDER-MISMATCH (400, PLATFORM-STD) · MDL-404-TYPE (404, PLATFORM-STD) · VALIDATION_ERROR (400, PLATFORM-STD) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD)
Orchestration : authorise → confirm the type exists → assign each submitted id its position as the new rank and persist all of them in one transaction, writing sort_order (DBF-MDL-016) and the columns the request does not carry, updated_by (DBF-MDL-020) and updated_at (DBF-MDL-021), on every row touched (QR-MDL-009, table MDL_LOOKUP_VALUE) → a statement that affects no row means the id does not belong to the type: raise MDL-400-REORDER-MISMATCH and roll the whole batch back → return the reordered list.
Repository   : QR-MDL-009 · join NONE · transaction READ_WRITE
Concurrency  : two simultaneous reorders of the same type are both allowed and neither is refused: each writes an explicit rank per id inside one transaction, nothing is read and then written back, and the later transaction's order is the one stored whole — a half-applied order is impossible because the batch is one transaction. A value created while a reorder is in flight is not in the submitted list, keeps its own rank and may tie with another; the tie is resolved by code in every ordered read, so it is a display detail, not a lost update.
Security     : screen MDL_LOOKUPS · permission `PERM_MDL_LOOKUPS_UPDATE`, gated by `PERM_MDL_LOOKUPS_VIEW` — both verified before processing. The reorder is an UPDATE of the rank column and SRS §B4 grants no separate action for it.
Localization : messages in ar + en
<!-- API:API-MDL-009:END -->
<!-- SUB:SVC-API-CRUD:END -->

<!-- SUB:SVC-API-INT:START traces=REQ-MDL-011,REQ-MDL-012 -->
### SUB SVC-API-INT — the surface other modules consume

<!-- API:API-MDL-011:START traces=REQ-MDL-011,REQ-MDL-012,DBF-MDL-002,DBF-MDL-006,DBF-MDL-013,DBF-MDL-016,DBF-MDL-017 -->
### API-MDL-011 — read active values by key (consumer)
Entity       : ENT-MDL-001, ENT-MDL-002 · operation VIEW
Endpoint     : GET /api/v1/mdl/lookups   verb: GET
Layers       : controller → `LookupConsumerController.readByKey` ; service → `LookupConsumerService.readByKey`
Request      : query param `type` → the lookup type key (DBF-MDL-002, String, required). No body and no paging: a coded list is returned whole, ordered.
Response     : 200 · list of `LookupValueResponse` narrowed to what a consumer needs — code (DBF-MDL-013), nameAr, nameEn, sortOrder (DBF-MDL-016) — ordered by sortOrder then code · not paginated · envelope `ApiResponse<T>`
Validations  : RULE-MDL-004 "While a lookup type is inactive, the system shall exclude its values from consumer reads." (trigger: on evaluate, at every consumer read · ar: «هذا النوع معطّل حاليًا» · en: "This lookup type is currently inactive") — enforced as a predicate over is_active_fl on both tables (DBF-MDL-006 and DBF-MDL-017), never as a branch after the read, and never as a write. An unknown key and an inactive type are both answered MDL-404-TYPE-KEY, because in both cases the platform holds no usable list under that key (REQ-MDL-012, AC-MDL-012). An active type whose values are all inactive returns an empty list with success — a different answer, deliberately.
Errors       : MDL-404-TYPE-KEY (404, RULE-MDL-004) · VALIDATION_ERROR (400, PLATFORM-STD — no key supplied) · ACCESS_DENIED (403, PLATFORM-STD) · INTERNAL_ERROR (500, PLATFORM-STD)
Orchestration : authorise → resolve the type by key and confirm it is active (QR-MDL-015, table MDL_LOOKUP_TYPE) → an absent or inactive type raises MDL-404-TYPE-KEY → load the type's active values ordered by rank (QR-MDL-011, table MDL_LOOKUP_VALUE joined to its type) → map into the narrowed projection. This endpoint writes no column of any kind.
Repository   : QR-MDL-015, QR-MDL-011 · join intra-module (value → type, both owned by this module; required because the caller addresses the set by key and the value table carries no key column) · transaction READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes. A deactivation committed between QR-MDL-015 and QR-MDL-011 can only shrink the returned list, which is the same outcome the next call would give; nothing is decided on and written back.
Security     : screen MDL_LOOKUPS · permission `PERM_MDL_LOOKUPS_VIEW` (the gateway action) — verified before processing. This operation has no screen surface of its own: a consuming module's backend calls it, and its authorisation is the VIEW grant on MDL_LOOKUPS held by that module's service account (SRS §B4, ADR-MDL-007), not a permission of its own.
Localization : the response carries both labels on every value, so the consumer renders either language without a second call; error messages in ar + en
<!-- API:API-MDL-011:END -->
<!-- SUB:SVC-API-INT:END -->
<!-- PHASE:SVC-API:END -->

<!-- PHASE:DOC:START traces=REQ-MDL-011,REQ-MDL-012 -->
## PHASE 4 — DOC

The implementer publishes `backend/modules/MDL/api-docs` from the surface it actually builds:
the eleven endpoints of PHASE 3 with their verbs, paths, request and response types, their HTTP
statuses and the error codes of the ERROR CATALOG. That published file — not this plan's
contract draft — is what the frontend stage and `api-verify` read, and the previous revision of
this plan is the standing example of why: its contract table predicted three `GET` collection
reads that were built as `POST …/search`, and every downstream reader that trusted the table
rather than the publication was wrong (ADR-MDL-002).

What the api-docs must carry for this module: the two base paths `/api/v1/mdl/lookup-types` and
`/api/v1/mdl/lookup-values`, plus the consumer path `/api/v1/mdl/lookups`; the `ApiResponse<T>`
and `Page<T>` envelopes; the paging defaults (20, maximum 200) on the one paged read; the filter
envelope the three search endpoints take; and the status → code table for every catalog row.
Nothing is documented that PHASE 3 does not specify, and no endpoint of PHASE 3 is left out.

The consumer half deserves its own line in the publication, because it is the only endpoint of
this module a human never calls: `GET /api/v1/mdl/lookups` is the runtime load every other
module performs for every coded list it owns, and its response shape is the contract those
modules bind to. No other document, report, export or printable output is produced by this
module in v1.
<!-- PHASE:DOC:END -->

<!-- PHASE:INT-C:START traces=REQ-MDL-001,REQ-MDL-002 -->
## PHASE 5 — INT-C

One `XM-*` row, below the split threshold (1 < 5) — no SUB is opened. The db-script register
declares exactly this row, and this stage mints none: nothing written above is the first reader
of another module's data.

| XM | classification | target | interface | the thing the target publishes | status |
|---|---|---|---|---|---|
| XM-MDL-001 | SOFT-READ | SEC · ENT-SEC-004 (ModuleRegistry, table SEC_MODULE_REG) | injected in-process interface (`profile.conventions.module_interface: in_process`) | a module-registry read on SEC's own cross-module package — see the row below | ACTIVE |

<!-- XM:XM-MDL-001:START traces=REQ-MDL-001,REQ-MDL-002 -->
### XM-MDL-001 — is the owner module registered in SEC?
Target        : module SEC · entity ENT-SEC-004 (ModuleRegistry) · classification SOFT-READ
Interface     : an in-process interface published by SEC and injected into this module's
lookup-type service — `profile.conventions.module_interface` is `in_process`, so this platform
is ONE deployable: there is no HTTP client, no base path to SEC, no timeout and no network error
path, and therefore no ERROR CATALOG row describing a network failure that cannot occur. The
operation is the existence read behind `SecModuleRegistryApi` — the same shape as SEC's already
published `SecUserDirectoryApi`, injected the same way. The access is by module code alone and
carries no key: a SOFT-READ never becomes a foreign key (db-script §2.1). One thing is owed
elsewhere and is recorded rather than asserted: SEC's own P3.1 artifacts register only
`SecUserDirectoryApi` as an exposed surface, so the registry read named here is not yet written
down on SEC's side. That is SEC's artifact to correct on its own re-run; this plan does not edit
another module's, and ENT-SEC-004 — the entity this row depends on — is defined in SEC's
registry and resolves today.
Contract      : data required = whether the submitted code has a registry row in SEC; fallback
if absent = reject the registration with MDL-409-MODULE-NOT-REGISTERED (RULE-MDL-001) and store
no lookup type at all (AC-MDL-002); retry = none, and none is possible — a same-process call has
no transient failure mode to retry through; timeout = not applicable for the same reason;
idempotency = the call is read-only and naturally idempotent.
Blocks        : nothing is blocked and nothing is DEFERRED. SEC v1 is gated (pass-1 APPROVE) and
SEC_MODULE_REG exists, so this row is ACTIVE from the moment MDL v1 is created. No `DBF-*` and no
`API-*` waits on it: the column DBF-MDL-003 is stored either way, and only the create path
(API-MDL-002) consults SEC at all. Unblock condition: none outstanding.
<!-- XM:XM-MDL-001:END -->

**Inbound dependencies** — recorded for the cascade, never assigned here, because the consuming
module owns its own row: XM-FIN-001 (FIN → MDL, SOFT-READ, ACTIVE) reads this module's values
through API-MDL-011's interface for every lookup-backed code FIN owns. Every module from PRC
onward reaches the same surface the same way. The notation for a consumer not yet assigned is
`XM-INBOUND-STUB-<n>`, never TODO; none is outstanding for this version, since the one live
consumer already carries its own id.
<!-- PHASE:INT-C:END -->

<!-- PHASE:INT-R:START traces=REQ-MDL-001,REQ-MDL-002 -->
## PHASE 6 — INT-R

One status row, for the one contract INT-C places. This phase consumes that contract and does
not redefine it.

| XM | status | workaround / mock strategy |
|---|---|---|
| XM-MDL-001 | READY — the target is gated and the interface is injected in the same deployable | none needed: nothing is DEFERRED, MOCKED, SIMULATED, BLOCKED or in EXTERNAL_WAIT |

Nothing in this module is blocked on another module's delivery. Every endpoint of PHASE 3 can be
built, deployed and called with SEC present in the same deployment, and ten of the eleven do not
touch SEC at all — only the create path asks it a question.

What a test must do about it, stated here rather than discovered later: because the call is an
injected in-process interface and not a network hop, there is no HTTP-level way to simulate "SEC
unreachable", and no test should try to. The failure that exists is a registry row that is
absent, and it is exercised by submitting an unregistered code (AC-MDL-002).
<!-- PHASE:INT-R:END -->

<!-- PHASE:SEC-BE:START traces=REQ-MDL-001,REQ-MDL-005,REQ-MDL-011,REQ-MDL-013 -->
## PHASE 7 — SEC-BE

**Two screens, two page codes.** SCR-REQ-MDL-001 (اللوكبات العامة / Generic Lookups) is
composite — master and detail, search and entry on both levels are ONE screen — so it registers
exactly one page code, `MDL_LOOKUPS`, and mints four permission names from it. SCR-REQ-MDL-002
(سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner) is a read-only browse and
registers `MDL_TYPE_REGISTRY` with the gateway action alone. Two rows in `SEC_PAGES`, no more.

Every endpoint of PHASE 3 verifies its permission through the platform authorisation layer
before any processing, on the page code and the action the operation carries. No role name is
compared anywhere in this module's code: services see permissions, never roles. `VIEW` is the
gateway — a requester without it reaches no endpoint behind that page code, whatever else they
hold. The finer grain the SRS explicitly excludes is not invented here: there is no permission
per lookup type, because granting the screen is the whole of the precision v1 offers.

**Registration rows** — written at deployment into the platform registries, with the column
names taken from SEC's own script and never from here:
- one page row in `SEC_PAGES` with the page code `MDL_LOOKUPS`, its ar/en name
  (اللوكبات العامة / Generic Lookups) and its menu parent (ENT-SEC-005);
- one page row in `SEC_PAGES` with the page code `MDL_TYPE_REGISTRY`, its ar/en name
  (سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner) and the same menu parent;
- four action rows on the first page — VIEW, CREATE, UPDATE, DELETE — and one on the second
  (ENT-SEC-006), each named `PERM_<PAGE_CODE>_<ACTION>`;
- the module's own row in the registry this plan reads at create time (ENT-SEC-004): MDL
  registers itself there, exactly as it requires every other module to.

**Forbidden responses** map through `LocalizedException → {code, messageAr, messageEn}` with the
catalog row ACCESS_DENIED — the shared handler's row, not a module code, for the reason the
ERROR CATALOG states.

**Permission matrix** — one row per composite screen. A cell is marked only where the row also
names the endpoint that serves it and the permission for that action; where an action is not
granted, the cell is left as a dash rather than marked hopefully:

| Screen | ENT-* | API-* serving it | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|---|---|
| MDL_LOOKUPS | ENT-MDL-001, ENT-MDL-002 | API-MDL-001, API-MDL-005, API-MDL-011 (VIEW) · API-MDL-002, API-MDL-006 (CREATE) · API-MDL-003, API-MDL-007, API-MDL-009 (UPDATE) · API-MDL-004, API-MDL-008 (DELETE) | ✓ PERM_MDL_LOOKUPS_VIEW | ✓ PERM_MDL_LOOKUPS_CREATE | ✓ PERM_MDL_LOOKUPS_UPDATE | ✓ PERM_MDL_LOOKUPS_DELETE |
| MDL_TYPE_REGISTRY | ENT-MDL-001 | API-MDL-010 | ✓ PERM_MDL_TYPE_REGISTRY_VIEW | — | — | — |

The `DELETE` column of the first row is the soft deactivation of REQ-MDL-004 and REQ-MDL-009,
and it is marked because two endpoints really serve it; no hard-delete endpoint exists in this
module and none is implied by the word. The three dashes on the second row are the same fact
SRS §B3 and §B4 state twice: that screen creates, updates and deletes nothing.

### BOOTSTRAP DATA — MDL v1

The rows that must exist before any endpoint of this module can succeed. Every row names who
produces it, not only that it is needed.

| item | value | producer |
|---|---|---|
| LOOKUP KEY | none — SRS A6: this module owns no lookup key and consumes none, so no endpoint above reads a value of any module's lookup table. It is the store the other modules' keys live in, and each of those is registered and seeded by its owning module through API-MDL-002 and API-MDL-006 | seed source: not applicable — there is no key of this module's to seed, here or in another module's tables |
| PERMISSION | PERM_MDL_LOOKUPS_VIEW | grant target: role منسّق المنصة / Platform administrator, role مدير قوائم الوحدة المالكة / Owning-module lookup manager, and role الحساب الخدمي للوحدة المستهلكة / consuming-module service account — granted by the SEC role seed shipped with this module's deployment. This is the gateway: without the grant the name exists and authorises nobody, and every endpoint of PHASE 3 answers ACCESS_DENIED to every caller, the administrator included |
| PERMISSION | PERM_MDL_LOOKUPS_CREATE | grant target: role منسّق المنصة / Platform administrator and role مدير قوائم الوحدة المالكة / Owning-module lookup manager, granted in the same SEC role seed |
| PERMISSION | PERM_MDL_LOOKUPS_UPDATE | grant target: role منسّق المنصة / Platform administrator and role مدير قوائم الوحدة المالكة / Owning-module lookup manager, granted in the same SEC role seed |
| PERMISSION | PERM_MDL_LOOKUPS_DELETE | grant target: role منسّق المنصة / Platform administrator and role مدير قوائم الوحدة المالكة / Owning-module lookup manager, granted in the same SEC role seed |
| PERMISSION | PERM_MDL_TYPE_REGISTRY_VIEW | grant target: role منسّق المنصة / Platform administrator alone, granted in the same SEC role seed — SRS §B4 grants this screen to no other role |
| REGISTRATION | the two `SEC_PAGES` rows, the five action rows and this module's own registry row | seed source: this module's deployment seed, written into the platform registries (ENT-SEC-004, ENT-SEC-005, ENT-SEC-006). Without them the screens are absent from the menu and the module's own create path would reject its own code |

No required column of either table is seeded: every one of them is written by an endpoint of
PHASE 3 or by the platform — the keys from the two sequences, the audit columns by the
interceptor — so both tables start empty and the first create call fills them. That is the whole
reason this module has no data bootstrap of its own while carrying the platform's largest one:
every other module's lists live in these two tables, and each of those modules seeds its own.

The frontend stage references these permission names and never redeclares them.
<!-- PHASE:SEC-BE:END -->

<!-- PHASE:ALIGN-BE:START traces=REQ-MDL-001,REQ-MDL-011,REQ-MDL-013 -->
## PHASE 8 — ALIGN-BE

Every row below names the mechanical check that backs it, and there are no other rows. Each mark
is the analyze report's result for that check, copied — not an independent judgement.

```
ALIGN — MDL v1
row               backing check        mark                assertion
TRACEABILITY      traces               ✓                   every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF
COVERED           orphans              ✓                   every REQ is covered by ≥1 API or DBF
BINDING (§2A)     value-agreement      ✓                   every DBF names the same physical column here as the db-script declares for it
MANIFEST (§4)     count-agrees         ✓                   every total this plan states equals the rows it heads
WRITERS           required-writer      ✓                   every required column is written by an endpoint, or the row states why not
QRC (§5)          orphans              ✓                   every catalogued query is reached by ≥1 API
API (R3)          code-format          ✓                   every catalog code is an instance of the declared format and carries a status the platform can emit
RULE INPUTS       data-source          ✓                   every RULE enforced at runtime names where the data it READS comes from, or is deferred
CROSS-MODULE      registry-agree       ✓                   every registered XM is placed here, and every XM minted here is back-registered
FOREIGN IDS       xref-resolve         ✓                   every id of another module cited here is defined in that module's own registry
BOOTSTRAP DATA    bootstrap-complete   ✓                   every lookup key and every permission has a row naming who produces it
SECURITY (R7)     operation-resolves   ✓                   every declared entity operation resolves to an API, and every marked matrix cell names its API and its permission
DEMAND (SRS)      operation-resolves   ✓                   every operation an SRS screen names is built by an API, or the plan states why it is not
DECISIONS         refs-exist           ✓                   every ADR this plan cites exists on disk in analysis/decisions/MDL/
PATHS             paths-resolve        ✓                   every path the generated manifest and execution state emit resolves to something that exists
COVERAGE          (the report)         none                the clauses the analyze report lists as having examined nothing
RESULT            PASSED ✓ — 0 findings
```

**What this revision corrected, so a reader of the previous one is not misled.** Four statements
in the gated revision of this plan described a system that was not built, and none of them was a
new decision — each was the plan lagging behind an input it had itself been derived from:

- **PK generation.** Both BINDINGS lines read `GENERATED ALWAYS AS IDENTITY`. The profile
  declares `sequence`, the db-script builds two named sequences and no identity clause, and the
  deployed migrations agree with the db-script. Corrected in PHASE 1 and in both entity blocks,
  and made explicit as QR-MDL-016 and QR-MDL-017 so the allocation has an address rather than
  living in a sentence — db-script §4.2 asked this stage for exactly this.
- **Three read verbs.** The contract table carried `GET` collection reads for API-MDL-001,
  API-MDL-005 and API-MDL-010. The SRS §B5 and the published surface both say
  `POST …/search`; the plan was the only artifact that disagreed (ADR-MDL-002).
- **The cross-module mechanism.** XM-MDL-001's interface line named a REST call to a path on
  SEC's own base path, a shape the platform has nowhere to put:
  `profile.conventions.module_interface` is `in_process` and the whole platform is one
  deployable, so no HTTP call to another module exists or could. The old line is not reproduced
  here, because a path written out reads as an address even when the sentence around it says the
  opposite. Corrected in INT-C.
- **A catalog row nothing could raise.** The 503 row described "SEC unreachable during
  XM-MDL-001's validation call" — a network failure that cannot occur in one deployable, on a
  status `profile.stack.backend.api.http_statuses` does not declare. Struck, with the reason
  kept where the row was, so the deletion is legible.
<!-- PHASE:ALIGN-BE:END -->

## Coverage tables

**ENT / DBF → phases → QR → XM**

| ENT | DBF | phases | QR | XM |
|---|---|---|---|---|
| ENT-MDL-001 | DBF-MDL-001 | DATA-DOM, SVC-API | QR-MDL-016, QR-MDL-002, QR-MDL-003, QR-MDL-004 | — |
| ENT-MDL-001 | DBF-MDL-002 | DATA-DOM, SVC-API | QR-MDL-001, QR-MDL-002, QR-MDL-010, QR-MDL-013, QR-MDL-015 | — |
| ENT-MDL-001 | DBF-MDL-003 | DATA-DOM, SVC-API, INT-C | QR-MDL-001, QR-MDL-002, QR-MDL-010, QR-MDL-012 | XM-MDL-001 |
| ENT-MDL-001 | DBF-MDL-004 | DATA-DOM, SVC-API | QR-MDL-001, QR-MDL-002, QR-MDL-003, QR-MDL-010 | — |
| ENT-MDL-001 | DBF-MDL-005 | DATA-DOM, SVC-API | QR-MDL-001, QR-MDL-002, QR-MDL-003, QR-MDL-010 | — |
| ENT-MDL-001 | DBF-MDL-006 | DATA-DOM, SVC-API | QR-MDL-001, QR-MDL-002, QR-MDL-004, QR-MDL-010, QR-MDL-011, QR-MDL-015 | — |
| ENT-MDL-001 | DBF-MDL-007 | DATA-DOM, SVC-API | QR-MDL-002 | — |
| ENT-MDL-001 | DBF-MDL-008 | DATA-DOM, SVC-API | QR-MDL-002 | — |
| ENT-MDL-001 | DBF-MDL-009 | DATA-DOM, SVC-API | QR-MDL-003, QR-MDL-004 | — |
| ENT-MDL-001 | DBF-MDL-010 | DATA-DOM, SVC-API | QR-MDL-003, QR-MDL-004 | — |
| ENT-MDL-002 | DBF-MDL-011 | DATA-DOM, SVC-API | QR-MDL-017, QR-MDL-006, QR-MDL-007, QR-MDL-008, QR-MDL-009 | — |
| ENT-MDL-002 | DBF-MDL-012 | DATA-DOM, SVC-API | QR-MDL-005, QR-MDL-006, QR-MDL-009, QR-MDL-011, QR-MDL-014 | — |
| ENT-MDL-002 | DBF-MDL-013 | DATA-DOM, SVC-API | QR-MDL-005, QR-MDL-006, QR-MDL-011, QR-MDL-014 | — |
| ENT-MDL-002 | DBF-MDL-014 | DATA-DOM, SVC-API | QR-MDL-005, QR-MDL-006, QR-MDL-007, QR-MDL-011 | — |
| ENT-MDL-002 | DBF-MDL-015 | DATA-DOM, SVC-API | QR-MDL-005, QR-MDL-006, QR-MDL-007, QR-MDL-011 | — |
| ENT-MDL-002 | DBF-MDL-016 | DATA-DOM, SVC-API | QR-MDL-005, QR-MDL-006, QR-MDL-007, QR-MDL-009, QR-MDL-011 | — |
| ENT-MDL-002 | DBF-MDL-017 | DATA-DOM, SVC-API | QR-MDL-005, QR-MDL-006, QR-MDL-008, QR-MDL-011 | — |
| ENT-MDL-002 | DBF-MDL-018 | DATA-DOM, SVC-API | QR-MDL-006 | — |
| ENT-MDL-002 | DBF-MDL-019 | DATA-DOM, SVC-API | QR-MDL-006 | — |
| ENT-MDL-002 | DBF-MDL-020 | DATA-DOM, SVC-API | QR-MDL-007, QR-MDL-008, QR-MDL-009 | — |
| ENT-MDL-002 | DBF-MDL-021 | DATA-DOM, SVC-API | QR-MDL-007, QR-MDL-008, QR-MDL-009 | — |

**RULE → API → catalog code**

| RULE | API | catalog code |
|---|---|---|
| RULE-MDL-001 | API-MDL-002 | MDL-409-MODULE-NOT-REGISTERED |
| RULE-MDL-002 | API-MDL-006, API-MDL-007 | MDL-409-VALUE-DUP |
| RULE-MDL-003 | API-MDL-003 | none — enforced by DTO shape; no code path can raise one |
| RULE-MDL-004 | API-MDL-011 | MDL-404-TYPE-KEY |

**XM → status → blocks → workaround**

| XM | status | blocks | workaround |
|---|---|---|---|
| XM-MDL-001 | ACTIVE / READY | none — no DBF and no API waits on it | none needed |

## Decisions applied

| ADR | What | Status |
|---|---|---|
| ADR-MDL-002 (P3.2) | The three composite reads are `POST …/search`, and this plan's contract table was the artifact that lagged — corrected in PHASE 3 and PHASE 4 | ACCEPTED (non-breaking) — analysis/decisions/MDL/ADR-MDL-002.md |
| ADR-MDL-007 (P3.2) | API-MDL-011 is bound and called by no screen: a consuming module's backend calls it, and its authorisation is the VIEW grant on MDL_LOOKUPS | ACCEPTED (non-breaking) — analysis/decisions/MDL/ADR-MDL-007.md |
| ADR-MDL-009 (P2) | Index strategy for the two tables — which filter columns are indexed and why neither flag is | ACCEPTED (non-breaking), applied here |
| ADR-MDL-010 (P2) | String precisions for the six unsized text columns — the maxima every Request line above states | ACCEPTED (non-breaking), applied here |
| ADR-SEC-002 (SEC) | Structural error-catalog rows (duplicate, not found) and infrastructure rows sit under one PLATFORM-STD umbrella instead of a business rule each — cited, not re-derived, exactly as that decision's own consequence allows | ACCEPTED, applied here |
| ADR-FIN-001 (FIN) | A dependency on another module is an application read, never a cross-module foreign key — applied to XM-MDL-001 | ACCEPTED, applied here |

No new ADR was raised at this stage: every point that could have gone two ways was settled by an
input already in hand — the profile, the db-script, the SRS, or a decision already on the
platform's record. No BLOCKED ADR — the pass was not stopped. No question was raised.
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-backend-test-plan.md>>>
# BACKEND TEST PLAN — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module : MDL   Version : v1   Profile : erp   Scope : project (modules MDL, SEC)
Sources: srs-mdl.md v1 · backend-execution-plan-mdl.md v1 · registry-srs-mdl.md v1 · registry-db-mdl.md v1
Framework: agnostic. REDUCED: no. Open ADRs: 0 new.
TC count: 13 (module scope) · 1 (integration — XM-MDL-001, MDL declares → SEC)
══════════════════════════════════════════════════════════════════

<!-- PHASE:TEST-PLAN-BE:START traces=REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013 -->

<!-- SUB:RULE-SCENARIOS:START traces=REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-007,REQ-MDL-011,REQ-MDL-012 -->
### SUB — RULE-SCENARIOS

<!-- TC:TC-MDL-002:START traces=AC-MDL-002,REQ-MDL-002,API-MDL-002 -->
### TC-MDL-002 — reject a type for an unregistered owner module
Derived from : AC-MDL-002 (REQ-MDL-002)
Exercises    : API-MDL-002 POST /api/v1/mdl/lookup-types
Rule / code  : RULE-MDL-001 → MDL-409-MODULE-NOT-REGISTERED
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: an owner module code with no ModuleRegistry row in SEC
Steps        : 1. POST {key, ownerModuleCode: "ZZZ", nameAr, nameEn}
Expected     : 409 MDL-409-MODULE-NOT-REGISTERED; no LookupType created
Test data    : ownerModuleCode "ZZZ" (never registered in SEC)
<!-- TC:TC-MDL-002:END -->

<!-- TC:TC-MDL-003:START traces=AC-MDL-003,REQ-MDL-003,API-MDL-003 -->
### TC-MDL-003 — key is immutable after creation
Derived from : AC-MDL-003 (REQ-MDL-003)
Exercises    : API-MDL-003 PUT /api/v1/mdl/lookup-types/{id}
Rule / code  : RULE-MDL-003 → (enforced by DTO shape — no error code, `key` simply absent from the request schema)
Scenario     : STATE · data class VALID · language ALL
Preconditions: an existing LookupType
Steps        : 1. PUT {nameAr, nameEn} (no `key` field in the request DTO at all) — 2. attempt to also pass a `key` value and confirm it is ignored / rejected by schema validation
Expected     : 200; names updated, key unchanged; step 2 either fails schema validation (400) or is silently ignored depending on the consumer's DTO strictness — either way key never changes
Test data    : existing type key "ACCOUNT_TYPE"
<!-- TC:TC-MDL-003:END -->

<!-- TC:TC-MDL-004:START traces=AC-MDL-004,REQ-MDL-004,API-MDL-004 -->
### TC-MDL-004 — deactivating a type excludes its values from reads
Derived from : AC-MDL-004 (REQ-MDL-004)
Exercises    : API-MDL-004 DELETE /api/v1/mdl/lookup-types/{id}
Rule / code  : RULE-MDL-004 → (no error code — enforced on the read side, see TC-MDL-011)
Scenario     : STATE · data class VALID · language ALL
Preconditions: an active LookupType with active values
Steps        : 1. DELETE (deactivate) the type — 2. call API-MDL-011 for its key
Expected     : 1. 200, isActiveFl=false — 2. step 2 returns no values (REQ-MDL-011 no longer returns them)
Test data    : a test-only type with 2 active values
<!-- TC:TC-MDL-004:END -->

<!-- TC:TC-MDL-007:START traces=AC-MDL-007,REQ-MDL-007,API-MDL-006 -->
### TC-MDL-007 — reject a duplicate code within a type
Derived from : AC-MDL-007 (REQ-MDL-007)
Exercises    : API-MDL-006 POST /api/v1/mdl/lookup-types/{id}/values
Rule / code  : RULE-MDL-002 → MDL-409-VALUE-DUP
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a type already holding a value with code "ACTIVE"
Steps        : 1. POST a second value with code "ACTIVE" under the same type
Expected     : 409 MDL-409-VALUE-DUP; no second row created
Test data    : type USER_STATUS, code "ACTIVE" (already exists)
<!-- TC:TC-MDL-007:END -->

<!-- TC:TC-MDL-011:START traces=AC-MDL-011,REQ-MDL-011,API-MDL-011 -->
### TC-MDL-011 — read active values by key, ordered
Derived from : AC-MDL-011 (REQ-MDL-011)
Exercises    : API-MDL-011 GET /api/v1/mdl/lookups
Rule / code  : RULE-MDL-004 (positive path — active type, active values only)
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a type "PAYMENT_METHOD" with 2 active and 1 inactive value
Steps        : 1. GET ?type=PAYMENT_METHOD
Expected     : 200; exactly the 2 active values, ordered by sortOrder
Test data    : PAYMENT_METHOD with 3 values (2 active, 1 inactive)
<!-- TC:TC-MDL-011:END -->

<!-- TC:TC-MDL-012:START traces=AC-MDL-012,REQ-MDL-012,API-MDL-011 -->
### TC-MDL-012 — reject an unknown type key
Derived from : AC-MDL-012 (REQ-MDL-012)
Exercises    : API-MDL-011 GET /api/v1/mdl/lookups
Rule / code  : RULE-MDL-004 → MDL-404-TYPE-KEY
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no LookupType with key "NOT_A_REAL_KEY"
Steps        : 1. GET ?type=NOT_A_REAL_KEY
Expected     : 404 MDL-404-TYPE-KEY (never an empty-success)
Test data    : type "NOT_A_REAL_KEY"
<!-- TC:TC-MDL-012:END -->
<!-- SUB:RULE-SCENARIOS:END -->

  <!-- SUB:API-SCENARIOS:START traces=REQ-MDL-001,REQ-MDL-005,REQ-MDL-006,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,REQ-MDL-013 -->
  ### SUB — API-SCENARIOS

  <!-- TC:TC-MDL-001:START traces=AC-MDL-001,REQ-MDL-001,API-MDL-002 -->
  ### TC-MDL-001 — create a lookup type
  Derived from : AC-MDL-001 (REQ-MDL-001)
  Exercises    : API-MDL-002 POST /api/v1/mdl/lookup-types
  Rule / code  : — (happy path)
  Scenario     : HAPPY · data class VALID · language ALL
  Preconditions: unique key; registered owner module code
  Steps        : 1. POST {key: "TEST_TYPE", ownerModuleCode: "MDL", nameAr, nameEn}
  Expected     : 201; active LookupType created
  Test data    : key "TEST_TYPE", ownerModuleCode "MDL" (MDL owning its own test lookup type is a legitimate self-registration case — a module owning its own reference data is a real scenario, not a contrived one)
  <!-- TC:TC-MDL-001:END -->

  <!-- TC:TC-MDL-005:START traces=AC-MDL-005,REQ-MDL-005,API-MDL-005 -->
  ### TC-MDL-005 — select a type and list its values
  Derived from : AC-MDL-005 (REQ-MDL-005)
  Exercises    : API-MDL-005 POST /api/v1/mdl/lookup-types/values/search
  Rule / code  : — (happy path)
  Scenario     : HAPPY · data class VALID · language ALL
  Preconditions: a type with 3 values
  Steps        : 1. POST /search filtered by the type's lookupTypeId
  Expected     : 200; exactly those 3 values, ordered by sortOrder
  Test data    : type with 3 values
  <!-- TC:TC-MDL-005:END -->

  <!-- TC:TC-MDL-006:START traces=AC-MDL-006,REQ-MDL-006,API-MDL-006 -->
### TC-MDL-006 — create a lookup value
Derived from : AC-MDL-006 (REQ-MDL-006)
Exercises    : API-MDL-006 POST /api/v1/mdl/lookup-types/{id}/values
Rule / code  : — (happy path)
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a code not yet used within the type
Steps        : 1. POST {code, nameAr, nameEn, sortOrder}
Expected     : 201; active LookupValue created under the type
Test data    : new code "NEW_VAL" under an existing type
<!-- TC:TC-MDL-006:END -->

<!-- TC:TC-MDL-008:START traces=AC-MDL-008,REQ-MDL-008,API-MDL-007 -->
### TC-MDL-008 — edit a lookup value
Derived from : AC-MDL-008 (REQ-MDL-008)
Exercises    : API-MDL-007 PUT /api/v1/mdl/lookup-values/{id}
Rule / code  : — (happy path)
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an existing LookupValue
Steps        : 1. PUT {nameAr, nameEn, sortOrder}
Expected     : 200; names/sortOrder updated; code and lookupTypeId unchanged
Test data    : existing value, new nameEn
<!-- TC:TC-MDL-008:END -->

<!-- TC:TC-MDL-009:START traces=AC-MDL-009,REQ-MDL-009,API-MDL-008 -->
### TC-MDL-009 — deactivate a lookup value
Derived from : AC-MDL-009 (REQ-MDL-009)
Exercises    : API-MDL-008 DELETE /api/v1/mdl/lookup-values/{id}
Rule / code  : — (happy path)
Scenario     : STATE · data class VALID · language ALL
Preconditions: an active LookupValue
Steps        : 1. DELETE (deactivate) — 2. call API-MDL-011 for its type
Expected     : 1. 200, isActiveFl=false — 2. the value no longer returned
Test data    : a test-only value
<!-- TC:TC-MDL-009:END -->

<!-- TC:TC-MDL-010:START traces=AC-MDL-010,REQ-MDL-010,API-MDL-009 -->
### TC-MDL-010 — reorder lookup values
Derived from : AC-MDL-010 (REQ-MDL-010)
Exercises    : API-MDL-009 PATCH /api/v1/mdl/lookup-types/{id}/values/reorder
Rule / code  : — (happy path)
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: 3 values with sortOrder 1,2,3
Steps        : 1. PATCH {orderedValueIds: [v3, v1, v2]}
Expected     : 200; sortOrder persisted as 3,1,2 respectively; a subsequent API-MDL-011 call returns them in that order
Test data    : 3 values under one type
<!-- TC:TC-MDL-010:END -->

<!-- TC:TC-MDL-013:START traces=AC-MDL-013,REQ-MDL-013,API-MDL-010 -->
### TC-MDL-013 — browse the type registry grouped by owner
Derived from : AC-MDL-013 (REQ-MDL-013)
Exercises    : API-MDL-010 POST /api/v1/mdl/lookup-types/by-owner/search
Rule / code  : — (happy path)
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: types owned by SEC and by MDL
Steps        : 1. POST /search for the registry
Expected     : 200; types grouped under their respective owner-module headings
Test data    : existing SEC-owned and MDL-owned types
<!-- TC:TC-MDL-013:END -->
<!-- SUB:API-SCENARIOS:END -->
<!-- PHASE:TEST-PLAN-BE:END -->

<!-- PHASE:INT-XM:START traces=REQ-MDL-002,XM-MDL-001 -->
MDL declares one XM (XM-MDL-001, SOFT-READ → SEC's ModuleRegistry); SEC is in the current
selection, so this is a real linking atom.

<!-- TC:TC-MDL-014:START traces=XM-MDL-001,REQ-MDL-002,API-MDL-002 -->
### TC-MDL-014 — graceful degradation when SEC is unreachable during owner-module validation
Derived from : XM-MDL-001 (REQ-MDL-002)
Exercises    : API-MDL-002 POST /api/v1/mdl/lookup-types
Rule / code  : XM-MDL-001 (SOFT-READ) → (a defined error, never a 500/unhandled state)
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: SEC's registry-search endpoint (API-SEC-021) is made unreachable/times out
Steps        : 1. POST a new lookup-type registration while SEC is unreachable
Expected     : the request fails with a defined, documented error (not a raw 500/timeout
  leak) — MDL's own flow returns a controlled response rather than crashing
Test data    : any lookup-type payload; SEC endpoint simulated as down
<!-- TC:TC-MDL-014:END -->
<!-- PHASE:INT-XM:END -->

## TC TRACEABILITY INDEX
| AC | TC | REQ | API | RULE/code | XM |
|---|---|---|---|---|---|
| AC-MDL-001…013 | TC-MDL-001…013 (1:1) | REQ-MDL-001…013 (1:1) | see each TC's Exercises line | see each TC's Rule/code line | — |
| — | TC-MDL-014 | REQ-MDL-002 | API-MDL-002 | — | XM-MDL-001 |

## COVERAGE
AC covered 13/13 (0 gaps) · REQ covered 13/13 · API covered 11/11 · every selected-module
XM covered 1/1 (XM-MDL-001 → TC-MDL-014, no gap).
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-business-policies.md>>>
## BUSINESS POLICIES — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module   : MDL     Source of truth : new project/lookup-module-plan-en.md
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-MDL-001 — مركز واحد للقيم المرجعية / One central hub for reference data
  Statement (ar) : يجب على النظام أن يكون المخزن الوحيد على مستوى المنصة لقوائم القيم المُرمَّزة التي قد تحتاجها أكثر من وحدة.
  Statement (en) : The system shall be the sole platform-wide store of coded reference-value lists that more than one module may need.
  Pattern   : ubiquitous
  Trigger   : Any module needing a coded value list
  Rationale : منع انحراف القيم بين الوحدات (value drift)
  Source    : lookup-module-plan-en.md §2
  Status    : CONFIRMED

POL-MDL-002 — تسمية المالك لكل نوع لوكب / Owner namespacing per lookup type
  Statement (ar) : يجب على النظام تسجيل الوحدة المالكة لمعنى كل نوع لوكب.
  Statement (en) : The system shall record, for every lookup type, the module that owns its meaning.
  Pattern   : ubiquitous
  Trigger   : Lookup type registration
  Rationale : تخزين مركزي، لكن الملكية الدلالية تبقى للوحدة المُسجِّلة
  Source    : lookup-module-plan-en.md §2-§3
  Status    : CONFIRMED

POL-MDL-003 — رفض تسجيل نوع لوحدة غير مسجَّلة / Reject a type registered under an unregistered module
  Statement (ar) : إذا كان تسجيل نوع اللوكب يُسمّي وحدة غير مسجَّلة في وحدة الأمان، فيجب على النظام رفض التسجيل.
  Statement (en) : If a lookup type registration names a module that is not registered in the Security module, then the system shall reject the registration.
  Pattern   : unwanted
  Trigger   : Lookup type registration
  Rationale : سلامة مرجعية — لا نوع بلا مالك حقيقي
  Source    : lookup-module-plan-en.md §3; module-registry-mdl.md → SHARED ENTITIES CONSUMED
  Status    : CONFIRMED

POL-MDL-004 — شاشة عامة واحدة، لا شاشة لكل قائمة / One generic screen, never one per list
  Statement (ar) : يجب على النظام إدارة قيم كل أنواع اللوكب عبر شاشة عامة واحدة من نوع رئيسي-تفصيلي، دون أي شاشة مخصصة لقائمة بعينها.
  Statement (en) : The system shall manage the values of every lookup type through one generic master-detail screen, never a screen dedicated to one specific list.
  Pattern   : ubiquitous
  Trigger   : Any lookup-value management need, for any module
  Rationale : آلية واحدة تخدم كل الوحدات دون تكرار شاشات
  Source    : lookup-module-plan-en.md §3
  Status    : CONFIRMED

POL-MDL-005 — الحقول القياسية لقيمة اللوكب / Standard fields for a lookup value
  Statement (ar) : يجب على النظام تخزين رمز وتسميتين ثنائيتي اللغة وترتيب وحالة نشاط لكل قيمة لوكب.
  Statement (en) : The system shall store a code, bilingual labels, a sort order and an active flag for every lookup value.
  Pattern   : ubiquitous
  Trigger   : Lookup value creation
  Rationale : اتساق البنية عبر كل القوائم
  Source    : lookup-module-plan-en.md §3
  Status    : CONFIRMED

POL-MDL-006 — رفض تكرار الرمز ضمن النوع نفسه / Reject a duplicate code within the same type
  Statement (ar) : إذا شارك رمزا قيمتين نفس نوع اللوكب، فيجب على النظام رفض تسجيل الثانية.
  Statement (en) : If two lookup values under the same lookup type share the same code, then the system shall reject the second.
  Pattern   : unwanted
  Trigger   : Lookup value creation
  Rationale : قيمة موثوقة واحدة لكل مفهوم مُرمَّز — لا تضارب
  Source    : lookup-module-plan-en.md §6 "No contradiction: one authoritative value per coded concept"
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
| Lookup key | Added values | Source |
None — standard values apply (MDL introduces no domain-specific list of its own; see
module-registry-mdl.md → LOOKUPS OWNED).

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Hierarchical / tree-shaped lookup values | Not mentioned by the plan; flat code/label list only | explicit future request | lookup-module-plan-en.md §3 (no hierarchy mentioned) |
| Per-lookup-type fine-grained permission (beyond the shared screen's normal SEC gate) | Not mentioned by the plan — "subject to Security grants" means the normal screen-level gate, not per-type sub-permissions | explicit future request | lookup-module-plan-en.md §4 |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
None — no open question was raised for MDL; the plan is fully prescriptive for this stage's scope.
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-db-script.md>>>
# DATABASE — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module : MDL   Version : v1   Dialect : postgresql16   Schema prefix : none
Identifier transformation : SRS logical field name (camelCase) → physical column name
  (snake_case); table, sequence, constraint and index names are written in upper case.
  PostgreSQL folds every unquoted identifier to lower case and this script quotes no
  identifier, so exactly one spelling of each object exists in the catalog. `key` is a
  NON-RESERVED keyword in PostgreSQL 16 and is kept verbatim as the column name the SRS
  gives it (A3); no identifier reaches the 63-byte limit.
Type map addition : logical type `number` → `INTEGER` — declared once here (engine §4.1),
  since `profile.stack.db.syntax_map` carries no integer row and `sortOrder` is a whole
  number, not a decimal. Every other type is taken from the map unchanged.
PK generation : `sequence` (profile.stack.db.pk_generation) — one `SEQ_{TABLE}` per table
  in BLOCK 1, PK columns declared plain `BIGINT NOT NULL` with no identity clause and no
  sequence default; the application draws the key from the named sequence. No PK trigger.
Date : 2026-09-19
Counts : 2 tables · 21 DBF · 1 XM (XM-MDL-001 SOFT-READ → SEC)
══════════════════════════════════════════════════════════════════

## 1. DB FIELD TRACEABILITY MATRIX — MDL v1

This section is the canonical source of `DBF` → column → type → SRS origin. Downstream
artifacts reference these columns by DBF id only and never restate name, type or origin.

### Table MDL_LOOKUP_TYPE (ENT-MDL-001, kind master)
| DBF id | Column | Type (postgresql16) | Traces (ENT.field) | Traces (REQ) | Nullable | Default |
|---|---|---|---|---|---|---|
| DBF-MDL-001 | lookup_type_pk | BIGINT | ENT-MDL-001.lookupTypePk | REQ-MDL-001 | NOT NULL | — (drawn from SEQ_MDL_LOOKUP_TYPE by the application) |
| DBF-MDL-002 | key | VARCHAR(50) | ENT-MDL-001.key | REQ-MDL-001, REQ-MDL-003, REQ-MDL-011, REQ-MDL-012, REQ-MDL-013 | NOT NULL | — |
| DBF-MDL-003 | owner_module_code | VARCHAR(10) | ENT-MDL-001.ownerModuleCode | REQ-MDL-001, REQ-MDL-002, REQ-MDL-013 | NOT NULL | — |
| DBF-MDL-004 | name_ar | VARCHAR(200) | ENT-MDL-001.nameAr | REQ-MDL-001, REQ-MDL-003, REQ-MDL-013 | NOT NULL | — |
| DBF-MDL-005 | name_en | VARCHAR(200) | ENT-MDL-001.nameEn | REQ-MDL-001, REQ-MDL-003, REQ-MDL-013 | NOT NULL | — |
| DBF-MDL-006 | is_active_fl | BOOLEAN | ENT-MDL-001.isActiveFl | REQ-MDL-001, REQ-MDL-004, REQ-MDL-011, REQ-MDL-013 | NOT NULL | TRUE |
| DBF-MDL-007 | created_by | VARCHAR(100) | ENT-MDL-001.createdBy (profile: entity_defaults.master) | REQ-MDL-001 | NOT NULL | — |
| DBF-MDL-008 | created_at | TIMESTAMPTZ | ENT-MDL-001.createdAt (profile: entity_defaults.master) | REQ-MDL-001 | NOT NULL | — |
| DBF-MDL-009 | updated_by | VARCHAR(100) | ENT-MDL-001.updatedBy (profile: entity_defaults.master) | REQ-MDL-003, REQ-MDL-004 | NULL | — |
| DBF-MDL-010 | updated_at | TIMESTAMPTZ | ENT-MDL-001.updatedAt (profile: entity_defaults.master) | REQ-MDL-003, REQ-MDL-004 | NULL | — |

### Table MDL_LOOKUP_VALUE (ENT-MDL-002, kind lookup)
| DBF id | Column | Type (postgresql16) | Traces (ENT.field) | Traces (REQ) | Nullable | Default |
|---|---|---|---|---|---|---|
| DBF-MDL-011 | lookup_value_pk | BIGINT | ENT-MDL-002.lookupValuePk | REQ-MDL-006 | NOT NULL | — (drawn from SEQ_MDL_LOOKUP_VALUE by the application) |
| DBF-MDL-012 | lookup_type_id | BIGINT | ENT-MDL-002.lookupTypeId → ENT-MDL-001 (intra-module FK) | REQ-MDL-005, REQ-MDL-006, REQ-MDL-007, REQ-MDL-011 | NOT NULL | — |
| DBF-MDL-013 | code | VARCHAR(50) | ENT-MDL-002.code | REQ-MDL-006, REQ-MDL-007, REQ-MDL-011 | NOT NULL | — |
| DBF-MDL-014 | name_ar | VARCHAR(200) | ENT-MDL-002.nameAr | REQ-MDL-006, REQ-MDL-008, REQ-MDL-011 | NOT NULL | — |
| DBF-MDL-015 | name_en | VARCHAR(200) | ENT-MDL-002.nameEn | REQ-MDL-006, REQ-MDL-008, REQ-MDL-011 | NOT NULL | — |
| DBF-MDL-016 | sort_order | INTEGER | ENT-MDL-002.sortOrder | REQ-MDL-005, REQ-MDL-006, REQ-MDL-008, REQ-MDL-010, REQ-MDL-011 | NOT NULL | 0 |
| DBF-MDL-017 | is_active_fl | BOOLEAN | ENT-MDL-002.isActiveFl | REQ-MDL-006, REQ-MDL-009, REQ-MDL-011 | NOT NULL | TRUE |
| DBF-MDL-018 | created_by | VARCHAR(100) | ENT-MDL-002.createdBy (SRS A3 addition beyond entity_defaults.lookup) | REQ-MDL-006 | NOT NULL | — |
| DBF-MDL-019 | created_at | TIMESTAMPTZ | ENT-MDL-002.createdAt (SRS A3 addition beyond entity_defaults.lookup) | REQ-MDL-006 | NOT NULL | — |
| DBF-MDL-020 | updated_by | VARCHAR(100) | ENT-MDL-002.updatedBy (SRS A3 addition beyond entity_defaults.lookup) | REQ-MDL-008, REQ-MDL-009, REQ-MDL-010 | NULL | — |
| DBF-MDL-021 | updated_at | TIMESTAMPTZ | ENT-MDL-002.updatedAt (SRS A3 addition beyond entity_defaults.lookup) | REQ-MDL-008, REQ-MDL-009, REQ-MDL-010 | NULL | — |

Total: 21 DBF ids across 2 tables. Sequence continuous across the module, never per table.
Column order inside each table follows the matrix; inside MDL_LOOKUP_VALUE that is the SRS
A3 field order, which itself lists `lookupTypeId` second (§4 below).

Every column above is an SRS A3 field of its entity — no column is invented, and the four
audit columns of each table are the profile default set for the entity kind, carried by the
SRS itself (A3 notes on both entities). The `master` default field `code` is not a second
column here: `key` plays its role, by the SRS A3 note and the module vocabulary.

## 2. XM REGISTER — MDL v1

| XM id | Type | This table | Column / access | Target table | Target module | Traces (REQ) | Status |
|---|---|---|---|---|---|---|---|
| XM-MDL-001 | SOFT-READ | MDL_LOOKUP_TYPE | `owner_module_code` (DBF-MDL-003) — existence of the code is read by code at create time, no FK column and no constraint | SEC_MODULE_REG | SEC | REQ-MDL-001, REQ-MDL-002 | ACTIVE (target SEC v1 gated, pass-1 APPROVE) |

**XM-MDL-001** — SOFT-READ · MDL_LOOKUP_TYPE.owner_module_code → SEC_MODULE_REG (SEC, ENT-SEC-004) [REQ-MDL-001, REQ-MDL-002] · source: SRS A8 consumed entity ModuleRegistry, required by RULE-MDL-001 · status ACTIVE.

MDL consumes exactly one entity (SRS A8) and it is read, never joined: the check is an
application read by code, per the platform precedent that no physical cross-module FK
exists anywhere in this pipeline (project-registry DECISION INDEX #9, ADR-FIN-001). SEC is
Tier 0 like MDL, so the direction is permitted either way [KB:erp-domain-standards §5].
Audit columns are never XMs: `created_by` / `updated_by` hold a principal string, not a key
into SEC (engine §3; project-registry SHARED ENTITY DECLARATIONS row `User`).

### 2.1 SOFT-READ handling
```
-- XM-MDL-001 SOFT-READ — this module's lookup-type service reads SEC_MODULE_REG.module_code
-- from SEC without an FK, to refuse a lookup type whose owner module has no registry row
-- (RULE-MDL-001, on create only). Rationale: referential sanity for the namespacing rule
-- (POL-MDL-002, POL-MDL-003) without a cross-module constraint — SRS A8, [KB:erp-domain-
-- standards §5]. Risk: a module later unregistered in SEC does not invalidate the lookup
-- types it already owns (the SRS states this outcome in RULE-MDL-001 Test-Hint); changes to
-- SEC_MODULE_REG require impact assessment on REQ-MDL-001, REQ-MDL-002 and REQ-MDL-013.
```

No DEFERRED XM and no deferred FK: the only dependency is a SOFT-READ, which never carries
a constraint (§6 of the engine), and SEC is gated regardless.

**Inbound dependencies** (recorded here for the cascade, not assigned here — the consuming
module owns its own XM row): XM-FIN-001 reads this module's tables. Consumers resolve a
value by joining MDL_LOOKUP_VALUE to MDL_LOOKUP_TYPE on `lookup_type_id` and filtering
`MDL_LOOKUP_TYPE.key` — there is no key column on the value table.

## 3. FULL_DATABASE_SCRIPT

Copy-and-run against a clean schema of postgresql16, unedited. All objects unqualified —
no schema prefix anywhere, never mixed (S-7).

```sql
-- ════════════════════════════════════════════════════════════════
-- MDL — البيانات المرجعية / Master Data Lookup — v1 — postgresql16
-- Generated by P2 from srs-mdl.md (v1). 2 tables · 21 DBF · 1 XM.
-- PK generation: named sequences (profile.stack.db.pk_generation = sequence).
-- ════════════════════════════════════════════════════════════════

-- BLOCK 1 — SEQUENCES (one per table; count == table count)
CREATE SEQUENCE SEQ_MDL_LOOKUP_TYPE  START WITH 1 INCREMENT BY 1 CACHE 1 NO CYCLE;
CREATE SEQUENCE SEQ_MDL_LOOKUP_VALUE START WITH 1 INCREMENT BY 1 CACHE 1 NO CYCLE;

-- BLOCK 2 — PARENT TABLES (no FK dependencies)
CREATE TABLE MDL_LOOKUP_TYPE (
  lookup_type_pk     BIGINT        NOT NULL,
  key                VARCHAR(50)   NOT NULL,
  owner_module_code  VARCHAR(10)   NOT NULL,
  name_ar            VARCHAR(200)  NOT NULL,
  name_en            VARCHAR(200)  NOT NULL,
  is_active_fl       BOOLEAN       NOT NULL DEFAULT TRUE,
  created_by         VARCHAR(100)  NOT NULL,
  created_at         TIMESTAMPTZ   NOT NULL,
  updated_by         VARCHAR(100),
  updated_at         TIMESTAMPTZ
);

-- BLOCK 3 — CHILD TABLES (parent created above)
CREATE TABLE MDL_LOOKUP_VALUE (
  lookup_value_pk    BIGINT        NOT NULL,
  lookup_type_id     BIGINT        NOT NULL,
  code               VARCHAR(50)   NOT NULL,
  name_ar            VARCHAR(200)  NOT NULL,
  name_en            VARCHAR(200)  NOT NULL,
  sort_order         INTEGER       NOT NULL DEFAULT 0,
  is_active_fl       BOOLEAN       NOT NULL DEFAULT TRUE,
  created_by         VARCHAR(100)  NOT NULL,
  created_at         TIMESTAMPTZ   NOT NULL,
  updated_by         VARCHAR(100),
  updated_at         TIMESTAMPTZ
);

-- BLOCK 4 — COMMENTS (table + every column; each column comment cites its DBF id)
COMMENT ON TABLE MDL_LOOKUP_TYPE IS 'ENT-MDL-001 — نوع اللوكب / LookupType: سجل أنواع قوائم القيم المرمزة على مستوى المنصة، لكل نوع مفتاح فريد ووحدة مالكة / the platform-wide register of coded-list types, each with a unique key and an owning module.';
COMMENT ON COLUMN MDL_LOOKUP_TYPE.lookup_type_pk    IS 'DBF-MDL-001 — معرف نوع اللوكب / LookupType id. المفتاح الأساسي، يأخذه التطبيق من SEQ_MDL_LOOKUP_TYPE ولا يعرض كمرجع عمل / primary key, drawn by the application from SEQ_MDL_LOOKUP_TYPE, never shown as a business reference.';
COMMENT ON COLUMN MDL_LOOKUP_TYPE.key               IS 'DBF-MDL-002 — المفتاح / Key. فريد على مستوى المنصة وغير قابل للتعديل بعد الإنشاء (RULE-MDL-003)، وهو عقد القراءة لكل مستهلك (REQ-MDL-011) / platform-unique, immutable after creation, the read contract of every consumer.';
COMMENT ON COLUMN MDL_LOOKUP_TYPE.owner_module_code IS 'DBF-MDL-003 — رمز الوحدة المالكة / Owner module code. يتحقق من وجوده في SEC_MODULE_REG عند الإنشاء بقراءة تطبيقية بلا مفتاح أجنبي (XM-MDL-001, RULE-MDL-001) / validated against SEC_MODULE_REG on create by an application read, with no foreign key.';
COMMENT ON COLUMN MDL_LOOKUP_TYPE.name_ar           IS 'DBF-MDL-004 — الاسم (عربي) / Name (Arabic).';
COMMENT ON COLUMN MDL_LOOKUP_TYPE.name_en           IS 'DBF-MDL-005 — الاسم (إنجليزي) / Name (English).';
COMMENT ON COLUMN MDL_LOOKUP_TYPE.is_active_fl      IS 'DBF-MDL-006 — نشط / Active. التعطيل ناعم ولا حذف نهائي؛ النوع المعطل يحجب قيمه عن قراءة المستهلك (RULE-MDL-004) / soft deactivation, never a delete; an inactive type hides its values from consumer reads.';
COMMENT ON COLUMN MDL_LOOKUP_TYPE.created_by        IS 'DBF-MDL-007 — أنشأه / Created by. معرف الأصل المصادق، يملؤه النظام ولا يقبل من عميل / the authenticated principal string, filled by the platform, never accepted from a client.';
COMMENT ON COLUMN MDL_LOOKUP_TYPE.created_at        IS 'DBF-MDL-008 — تاريخ الإنشاء / Created at. UTC مخزنة وتعرض بتوقيت المستأجر / stored UTC, displayed in the tenant timezone.';
COMMENT ON COLUMN MDL_LOOKUP_TYPE.updated_by        IS 'DBF-MDL-009 — عدله / Updated by. يملأ عند أول تعديل / filled by the platform on the first update.';
COMMENT ON COLUMN MDL_LOOKUP_TYPE.updated_at        IS 'DBF-MDL-010 — تاريخ التعديل / Updated at. UTC مخزنة / stored UTC.';
COMMENT ON TABLE MDL_LOOKUP_VALUE IS 'ENT-MDL-002 — قيمة اللوكب / LookupValue: قيم كل نوع لوكب، مرتبة بالرتبة، والرمز هو ما تخزنه الوحدات المستهلكة / the values of each lookup type, ordered by sort order; the code is what consuming modules store.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.lookup_value_pk  IS 'DBF-MDL-011 — معرف القيمة / LookupValue id. المفتاح الأساسي، يأخذه التطبيق من SEQ_MDL_LOOKUP_VALUE / primary key, drawn by the application from SEQ_MDL_LOOKUP_VALUE.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.lookup_type_id   IS 'DBF-MDL-012 — نوع اللوكب / Lookup type. مفتاح أجنبي داخل الوحدة إلى MDL_LOOKUP_TYPE (FK_LOOKUP_VALUE_TYPE) / intra-module foreign key to MDL_LOOKUP_TYPE.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.code             IS 'DBF-MDL-013 — الرمز / Code. فريد ضمن النوع الواحد (RULE-MDL-002)، وهو ما تخزنه الوحدة المستهلكة عندها / unique within one type; the value a consuming module stores on its own row.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.name_ar          IS 'DBF-MDL-014 — الاسم (عربي) / Name (Arabic). التسمية المعروضة / the displayed label.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.name_en          IS 'DBF-MDL-015 — الاسم (إنجليزي) / Name (English). التسمية المعروضة / the displayed label.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.sort_order       IS 'DBF-MDL-016 — الترتيب / Sort order. ترتيب العرض تصاعديا، يضبط بالتحرير أو بإعادة الترتيب (REQ-MDL-010) / ascending display order, set by editing or by reorder.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.is_active_fl     IS 'DBF-MDL-017 — نشط / Active. القيمة المعطلة تغيب عن قراءة المستهلك وتبقى معروضة في شاشة الإدارة (REQ-MDL-009) / an inactive value is absent from consumer reads and still shown on the management screen.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.created_by       IS 'DBF-MDL-018 — أنشأها / Created by. معرف الأصل المصادق، يملؤه النظام / the authenticated principal string, filled by the platform.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.created_at       IS 'DBF-MDL-019 — تاريخ الإنشاء / Created at. UTC مخزنة / stored UTC.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.updated_by       IS 'DBF-MDL-020 — عدلها / Updated by. يملأ عند أول تعديل / filled by the platform on the first update.';
COMMENT ON COLUMN MDL_LOOKUP_VALUE.updated_at       IS 'DBF-MDL-021 — تاريخ التعديل / Updated at. UTC مخزنة / stored UTC.';

-- BLOCK 5 — CONSTRAINTS
-- 5a PRIMARY KEY
ALTER TABLE MDL_LOOKUP_TYPE  ADD CONSTRAINT PK_MDL_LOOKUP_TYPE  PRIMARY KEY (lookup_type_pk);
ALTER TABLE MDL_LOOKUP_VALUE ADD CONSTRAINT PK_MDL_LOOKUP_VALUE PRIMARY KEY (lookup_value_pk);

-- 5b UNIQUE
-- REQ-MDL-001: the key is unique across the platform.
ALTER TABLE MDL_LOOKUP_TYPE  ADD CONSTRAINT UQ_MDL_LOOKUP_TYPE_KEY        UNIQUE (key);
-- RULE-MDL-002: a code is unique within one type; the same code under another type is legal.
ALTER TABLE MDL_LOOKUP_VALUE ADD CONSTRAINT UQ_MDL_LOOKUP_VALUE_TYPE_CODE UNIQUE (lookup_type_id, code);

-- 5c CHECK
-- none: no SRS RULE states a value domain for any column; SRS A7 declares no status column
-- (both entities are two-state and carry a BOOLEAN flag, which is its own domain), and the
-- module owns no coded list of its own (SRS A6), so there is no code set to constrain.

-- 5d FOREIGN KEYS (intra-module only; parent PK created in 5a above)
ALTER TABLE MDL_LOOKUP_VALUE ADD CONSTRAINT FK_LOOKUP_VALUE_TYPE FOREIGN KEY (lookup_type_id) REFERENCES MDL_LOOKUP_TYPE (lookup_type_pk);

-- BLOCK 6 — TRIGGERS
-- none: no SRS RULE requires one. RULE-MDL-001 is a cross-module application read
-- (XM-MDL-001), RULE-MDL-003 is enforced by omission — the key is absent from the update
-- payload entirely (its SRS Test-Hint) — and RULE-MDL-004 is a read-time filter that
-- changes no stored flag. RULE-MDL-002 is structural, in BLOCK 5b. No PK-population
-- trigger and no sequence default on any PK column.

-- BLOCK 7 — INDEXES (non-PK)
-- Search/list filters of SRS PART B B2 on MDL_LOOKUP_TYPE:
CREATE INDEX IDX_MDL_LOOKUP_TYPE_OWNER   ON MDL_LOOKUP_TYPE (owner_module_code);
CREATE INDEX IDX_MDL_LOOKUP_TYPE_NAME_AR ON MDL_LOOKUP_TYPE (name_ar);
CREATE INDEX IDX_MDL_LOOKUP_TYPE_NAME_EN ON MDL_LOOKUP_TYPE (name_en);
-- The type key (B2 filter, and the consumer read of REQ-MDL-011/012) is served by the
-- implicit index of UQ_MDL_LOOKUP_TYPE_KEY — never duplicated.
-- The ordered detail read (REQ-MDL-005, REQ-MDL-011) and the FK column itself:
CREATE INDEX IDX_MDL_LOOKUP_VALUE_TYPE_SORT ON MDL_LOOKUP_VALUE (lookup_type_id, sort_order);
-- The value-code filter of B2 and the FK column are both led by lookup_type_id in
-- UQ_MDL_LOOKUP_VALUE_TYPE_CODE and in the index above — no third index over the same
-- leading column. Neither active flag is indexed (see ADR-MDL-009).

-- BLOCK 8 — LOOKUP SEED DATA
-- none, anywhere: MDL owns no lookup key and consumes none (SRS A6) — it is the mechanism
-- coded lists run on, not the owner of one. Every key belongs to the module that registers
-- it and is seeded by that module through this module's own API (API-MDL-002 for the type,
-- API-MDL-006 for the values), never by an INSERT written in another module's script.
COMMIT;

-- BLOCK 9 — VIEWS
-- none required by this SRS: the consumer read (REQ-MDL-011) is a two-table query with a
-- filter on both active flags, expressed at the API layer, not a stored view.

-- BLOCK 10 — FUNCTIONS / PROCEDURES
-- none required by this SRS.

-- BLOCK 11 — DEFERRED FK PATCH BLOCKS
-- none: this module's single cross-module dependency (XM-MDL-001) is a SOFT-READ, which
-- never becomes a constraint, and SEC is gated regardless. No live FK in this script
-- references another module's table.
```

## 4. DECISIONS APPLIED

| DEFAULT / ADR | What | Source | Override / status |
|---|---|---|---|
| PROFILE | PK generation is `sequence`: one `SEQ_{TABLE}` per table, PK columns plain `BIGINT NOT NULL`, no identity clause, no sequence default, no trigger | profile.stack.db.pk_generation + naming.sequence_pattern + syntax_map.sequence | Binding — one strategy per database. See the alignment note below |
| DEFAULT | Soft deactivation through `is_active_fl` on both tables; no delete column, no hard delete | [KB:erp-domain-standards §6 soft delete]; SRS Decisions applied | Platform-wide decision, not a module one |
| DEFAULT | The four audit columns on both tables — including the `lookup`-kind table, whose default set omits them — because the SRS A3 note carries them there deliberately | [KB:erp-domain-standards §6 audit trail]; SRS A3 | Dropping them would lose who changed a list everyone reads |
| DEFAULT | `created_by` / `updated_by` are a principal string (`VARCHAR(100)`), never a numeric FK into SEC; audit columns are never XMs | engine §3; project-registry SHARED ENTITY DECLARATIONS row `User` | non-breaking |
| DEFAULT | No database DEFAULT on `created_at` / `created_by`: the platform fills the audit columns, so exactly one writer exists. The two flags and `sort_order` do carry the defaults the SRS states (`true`, `0`) | engine §3 AUDIT COLUMNS; SRS A3 | non-breaking |
| DEFAULT | `TIMESTAMPTZ`, stored UTC, displayed in the tenant timezone | [KB:erp-domain-standards §6 dates] | Platform-wide |
| DEFAULT | `key` is kept verbatim as a column name — non-reserved in PostgreSQL 16 — and plays the role of the `master` default field `code` (SRS A3 note); no second column is created for `code` | SRS A3; engine §3 | non-breaking |
| DEFAULT | Inside MDL_LOOKUP_VALUE the DBF order follows SRS A3, which lists `lookupTypeId` second; the engine ordering rule "FK columns after the entity's own columns" governs FK columns the SRS does not itself declare as fields | engine §2; SRS A3; project-registry STRUCTURAL REGISTRY (range pinned) | non-breaking |
| DEFAULT | No CHECK constraint anywhere: no RULE states a value domain, A7 declares no status column, A6 declares no owned coded list | SRS A5, A6, A7 | non-breaking |
| DEFAULT | No seed data in this script and none in any other module's script: MDL owns no lookup key | SRS A6; engine §4.2 | non-breaking |
| ADR-MDL-009 | Index strategy: which filter columns get an index, which are left to the implicit index of a UNIQUE constraint, and why neither BOOLEAN flag is indexed | analysis/decisions/MDL/ADR-MDL-009.md | ACCEPTED (non-breaking) |
| ADR-MDL-010 | String precisions (`VARCHAR` lengths) for the six text columns the SRS leaves unsized | analysis/decisions/MDL/ADR-MDL-010.md | ACCEPTED (non-breaking) |

No BLOCKED ADR — the pass was not stopped. No question was raised.

### 4.1 RULE → constraint mapping (SRS A5)

| RULE | Mapped to | Where |
|---|---|---|
| RULE-MDL-001 | no database object — an application read of SEC_MODULE_REG by code (XM-MDL-001), on create only | §2.1; BLOCK 6 note |
| RULE-MDL-002 | `UQ_MDL_LOOKUP_VALUE_TYPE_CODE` UNIQUE (lookup_type_id, code) | BLOCK 5b |
| RULE-MDL-003 | no database object — the key is absent from the update payload (the RULE Test-Hint); never a trigger | BLOCK 6 note |
| RULE-MDL-004 | no database object — a read-time filter over DBF-MDL-006 and DBF-MDL-017; the rule changes no stored flag | BLOCK 6 note; BLOCK 9 note |

The platform-unique key of REQ-MDL-001 and the not-found of REQ-MDL-012 are error-catalog
rows under PLATFORM-STD, not business rules (project-registry DECISION INDEX #8,
ADR-SEC-002); the uniqueness half of that pair is nevertheless structural here, in
`UQ_MDL_LOOKUP_TYPE_KEY`.

### 4.2 Alignment note — PK generation and the existing P3.1 plan (engine §8)

`backend-execution-plan-mdl.md` (P3.1, gated) states `PK generation GENERATED ALWAYS AS
IDENTITY` in the BINDINGS line of both entities. That line predates the profile's current
`pk_generation: sequence` and does not describe what is deployed: the delivered migrations
build plain `BIGINT NOT NULL` PKs fed by named `SEQ_<TABLE>` sequences, and the repository's
entity contract mandates `GenerationType.SEQUENCE` — recorded for the sibling module in
`db-script-fin.md` §1 (migration V22). This script follows the profile and the deployed
reality; the two BINDINGS lines of the P3.1 plan are stale text to be corrected when that
stage is re-run, per engine §8. Nothing else in the plan is affected: it references columns
by DBF id, and no DBF id, column name, type or constraint name moved.

## 5. REGISTRY CONTENT
See `registry-db-mdl.md`.

## 6. DBF id definitions (cross-reference index — full detail in §1; `[traces]` = ENT + REQ)
**DBF-MDL-001** — MDL_LOOKUP_TYPE.lookup_type_pk [ENT-MDL-001, REQ-MDL-001]
**DBF-MDL-002** — MDL_LOOKUP_TYPE.key [ENT-MDL-001, REQ-MDL-001, REQ-MDL-003, REQ-MDL-011, REQ-MDL-012, REQ-MDL-013]
**DBF-MDL-003** — MDL_LOOKUP_TYPE.owner_module_code [ENT-MDL-001, REQ-MDL-001, REQ-MDL-002, REQ-MDL-013]
**DBF-MDL-004** — MDL_LOOKUP_TYPE.name_ar [ENT-MDL-001, REQ-MDL-001, REQ-MDL-003, REQ-MDL-013]
**DBF-MDL-005** — MDL_LOOKUP_TYPE.name_en [ENT-MDL-001, REQ-MDL-001, REQ-MDL-003, REQ-MDL-013]
**DBF-MDL-006** — MDL_LOOKUP_TYPE.is_active_fl [ENT-MDL-001, REQ-MDL-001, REQ-MDL-004, REQ-MDL-011, REQ-MDL-013]
**DBF-MDL-007** — MDL_LOOKUP_TYPE.created_by [ENT-MDL-001, REQ-MDL-001]
**DBF-MDL-008** — MDL_LOOKUP_TYPE.created_at [ENT-MDL-001, REQ-MDL-001]
**DBF-MDL-009** — MDL_LOOKUP_TYPE.updated_by [ENT-MDL-001, REQ-MDL-003, REQ-MDL-004]
**DBF-MDL-010** — MDL_LOOKUP_TYPE.updated_at [ENT-MDL-001, REQ-MDL-003, REQ-MDL-004]
**DBF-MDL-011** — MDL_LOOKUP_VALUE.lookup_value_pk [ENT-MDL-002, REQ-MDL-006]
**DBF-MDL-012** — MDL_LOOKUP_VALUE.lookup_type_id [ENT-MDL-002, ENT-MDL-001, REQ-MDL-005, REQ-MDL-006, REQ-MDL-007, REQ-MDL-011]
**DBF-MDL-013** — MDL_LOOKUP_VALUE.code [ENT-MDL-002, REQ-MDL-006, REQ-MDL-007, REQ-MDL-011]
**DBF-MDL-014** — MDL_LOOKUP_VALUE.name_ar [ENT-MDL-002, REQ-MDL-006, REQ-MDL-008, REQ-MDL-011]
**DBF-MDL-015** — MDL_LOOKUP_VALUE.name_en [ENT-MDL-002, REQ-MDL-006, REQ-MDL-008, REQ-MDL-011]
**DBF-MDL-016** — MDL_LOOKUP_VALUE.sort_order [ENT-MDL-002, REQ-MDL-005, REQ-MDL-006, REQ-MDL-008, REQ-MDL-010, REQ-MDL-011]
**DBF-MDL-017** — MDL_LOOKUP_VALUE.is_active_fl [ENT-MDL-002, REQ-MDL-006, REQ-MDL-009, REQ-MDL-011]
**DBF-MDL-018** — MDL_LOOKUP_VALUE.created_by [ENT-MDL-002, REQ-MDL-006]
**DBF-MDL-019** — MDL_LOOKUP_VALUE.created_at [ENT-MDL-002, REQ-MDL-006]
**DBF-MDL-020** — MDL_LOOKUP_VALUE.updated_by [ENT-MDL-002, REQ-MDL-008, REQ-MDL-009, REQ-MDL-010]
**DBF-MDL-021** — MDL_LOOKUP_VALUE.updated_at [ENT-MDL-002, REQ-MDL-008, REQ-MDL-009, REQ-MDL-010]
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-flow-diagram.md>>>
# FLOW DIAGRAM — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module : MDL   Version : v1   Profile : erp   Stage : P3.2 (Part A — UX design)
Sources: srs-mdl.md (functional ceiling — REQ/AC/SCR-REQ) · prd-mdl.md (priority and intent)
Screens: SCR-MDL-001, SCR-MDL-002 — two screens, four navigation paths
══════════════════════════════════════════════════════════════════

## The screens these flows move between

| SCR | الاسم / Name | Page code | SRS screen requirement |
|---|---|---|---|
| SCR-MDL-001 | اللوكبات العامة / Generic Lookups | MDL_LOOKUPS | SCR-REQ-MDL-001 |
| SCR-MDL-002 | سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner | MDL_TYPE_REGISTRY | SCR-REQ-MDL-002 |

Each is one screen with one page code, its sub-views included — the search and the entry of both
levels of the management screen are sub-views under `SCR-MDL-001`, not screens of their own
(`profile.conventions.composite_screen`; SRS Part B states the same in each `Composite` line).

Navigation into both starts at the platform menu entry البيانات المرجعية / Master Data Lookup →
اللوكبات / Lookups, which the SRS `Navigation` lines of both screen requirements name.

---

## FLOW — تسجيل نوع لوكب / Register a lookup type          traces=US-MDL-001,US-MDL-004,REQ-MDL-001,REQ-MDL-002,SCR-MDL-001
```
Screens   : SCR-MDL-001
Sequence  : القائمة الرئيسية / main menu → SCR-MDL-001 بحث الأنواع / type search
            → SCR-MDL-001 نموذج نوع جديد / new-type entry → حفظ / save
            → SCR-MDL-001 بحث الأنواع، والنوع الجديد ظاهر فيه / back to the type search, the new type in it
Trigger   : مُكامِل وحدة يسجّل قائمة قيم جديدة بيانات لا شيفرة /
            an integrator registers a new coded list as data, not as code
Priority  : HIGH (US-MDL-001, US-MDL-004)
```
الحفظ يرفض رمز وحدة مالكة لا صفّ لها في سجل وحدات الأمان (REQ-MDL-002)، فالمسار يعود إلى النموذج
نفسه برسالة الحقل لا إلى شاشة أخرى / a rejected owner module code (REQ-MDL-002) returns to the same
entry sub-view with the field message — it is not a navigation step.

## FLOW — تنقيح نوع قائم / Maintain an existing type       traces=US-MDL-001,REQ-MDL-003,REQ-MDL-004,SCR-MDL-001
```
Screens   : SCR-MDL-001
Sequence  : SCR-MDL-001 بحث الأنواع / type search → اختيار نوع / select a type
            → SCR-MDL-001 نموذج تعديل النوع / type edit sub-view → حفظ أو تعطيل / save or deactivate
            → SCR-MDL-001 بحث الأنواع / back to the type search
Trigger   : تغيّر تسمية قائمة، أو إخراجها من الخدمة /
            a list is renamed, or taken out of service
Priority  : HIGH (US-MDL-001)
```
المفتاح غير قابل للتعديل (RULE-MDL-003) والتعطيل لا رجعة له من هذه الشاشة (REQ-MDL-004) — كلاهما
يُقال في الشاشة لا في مسار تنقّل / the key is immutable and deactivation is one-way from this
screen; both are said on the screen, neither is a separate path.

## FLOW — إدارة قيم النوع / Manage a type's values         traces=US-MDL-002,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,SCR-MDL-001
```
Screens   : SCR-MDL-001
Sequence  : SCR-MDL-001 بحث الأنواع / type search → اختيار النوع / select the type
            → SCR-MDL-001 جزء القيم التفصيلي / the values pane of the selected type
            → SCR-MDL-001 نموذج قيمة (إضافة أو تعديل) / value entry sub-view (add or edit)
            → حفظ / save → جزء القيم / back to the values pane
Trigger   : صاحب قائمة يضيف قيمة، أو يعدّل تسمية، أو يعطّل قيمة، أو يعيد الترتيب /
            a list's owner adds a value, revises a label, deactivates a value, or reorders
Priority  : HIGH — الخطة تسمّي هذه الشاشة آلية الوحدة الجوهرية / the plan names this the module's
            core mechanism (US-MDL-002)
```
التعطيل وإعادة الترتيب إجراءان على الصفّ داخل جزء القيم ولا يفتحان سطحًا ثانيًا (REQ-MDL-009,
REQ-MDL-010) / deactivating a value and reordering are row actions inside the values pane; neither
opens a second surface.

## FLOW — تدقيق السجل حسب المالك / Audit the registry by owner   traces=US-MDL-005,REQ-MDL-013,SCR-MDL-002,SCR-MDL-001
```
Screens   : SCR-MDL-002, SCR-MDL-001
Sequence  : القائمة الرئيسية / main menu → SCR-MDL-002 السجل مجمّعًا حسب الوحدة المالكة /
            the registry grouped by owner module → اختيار نوع للفتح / pick a type to open
            → SCR-MDL-001 مع النوع مختارًا / SCR-MDL-001 with that type selected
Trigger   : منسّق المنصة يراجع قوائم وحدة بعينها في نظرة واحدة /
            a platform administrator reviews one module's lists at a glance
Priority  : MEDIUM (US-MDL-005)
```
المسار في اتجاه واحد: السجل يقرأ ولا يكتب (SRS §B3)، والانتقال منه إلى الشاشة العامة هو المكان
الوحيد الذي يُكتب فيه / the path is one-way: the registry reads and never writes, and the step into
the management screen is the only place anything is written. The target route carries its own
guard, so a reviewer who may browse but not manage is refused at the door rather than shown a
dead link.

---

## قصة بلا مسار تنقّل / A story with no navigation path

`US-MDL-003` — قراءة القيم الفعّالة بالمفتاح من أي وحدة مستهلكة / read active values by key from a
consuming module — carries **no flow**. Its caller is a consuming module's backend over the
platform's in-process interface, not a user at a screen: `REQ-MDL-011` and `REQ-MDL-012` are
written from the caller's side, and the SRS lists the operation under `SCR-REQ-MDL-002` §B5
because that is where the consumer contract is recorded, not because the registry screen calls
it (ADR-MDL-007). Inventing a "preview this type's values" screen for it would be inventing
navigation the SRS does not have — §A.2 forbids exactly that.

What a user can observe of that read is indirect and already on `SCR-MDL-001`: the values a
consumer receives, and the active flags that decide which of them are returned (RULE-MDL-004).

## تغطية القصص / Story coverage

| US | Flow | SCR |
|---|---|---|
| US-MDL-001 | تسجيل نوع لوكب · تنقيح نوع قائم / register, maintain | SCR-MDL-001 |
| US-MDL-002 | إدارة قيم النوع / manage a type's values | SCR-MDL-001 |
| US-MDL-003 | — بلا مسار: قراءة خادم لخادم / none: a server-to-server read (ADR-MDL-007) | — |
| US-MDL-004 | تسجيل نوع لوكب / register a lookup type | SCR-MDL-001 |
| US-MDL-005 | تدقيق السجل حسب المالك / audit the registry by owner | SCR-MDL-002 → SCR-MDL-001 |

أربعة مسارات، كلٌّ منها يبدأ بشاشة تقرّها الوحدةُ في SRS ويستشهد بقصة وبمتطلَّب / four flows, each
starting at a screen the SRS declares and each citing a story and a requirement. لا مسار بلا شاشة
مسنودة بالمواصفة / no flow rests on a screen the SRS does not have.
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-frontend-execution-plan.md>>>
# FRONTEND EXECUTION PLAN — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module    : MDL   Version : v1   Profile : erp   Track : frontend   Plan : exec
Framework : react-ts-vite · routing react-router · server-state tanstack-query ·
            forms react-hook-form · validation zod · state zustand ·
            one lazily-loaded chunk per composite screen (profile.stack.frontend)
Inputs    : srs (v1) · prd (v1) · api-docs (fetched from the backend repo, digest `bac95437…`,
            4 files) · registry-srs (v1) · registry-exec-be (v1)
Screens   : 2 — SCR-MDL-001, SCR-MDL-002 · UXD : 1 — UXD-MDL-001 · API published : 11, bound 11
Decisions : ADR-MDL-002 · ADR-MDL-003 · ADR-MDL-004 · ADR-MDL-005 · ADR-MDL-006 · ADR-MDL-007 ·
            ADR-MDL-011 · ADR-MDL-012 · ADR-MDL-013 — all ACCEPTED, all non-breaking
            (superseded, cited by them and not applied here: ADR-MDL-001, ADR-MDL-008)
══════════════════════════════════════════════════════════════════

## API SURFACE — MDL v1   (shapes: `_inputs/api-docs-mdl.md` — cited by API id, never restated)

The published document now carries a `Contract ID` line per endpoint and an API column in its
catalog — 11 of 11 served endpoints — so every block below cites its endpoint by the id the
api-docs themselves publish (ADR-MDL-011). Verb, path, request and response DTOs, the paging
envelope and the error catalog are read there and are not copied here.

```
BINDING   REQ → API — the binding only
  REQ-MDL-001, REQ-MDL-002  → API-MDL-002   create a lookup type
  REQ-MDL-001, REQ-MDL-003  → API-MDL-001   search lookup types (the list the registrar returns to)
  REQ-MDL-003               → API-MDL-003   update a lookup type
  REQ-MDL-004               → API-MDL-004   deactivate a lookup type
  REQ-MDL-005               → API-MDL-005   search the selected type's values
  REQ-MDL-006, REQ-MDL-007  → API-MDL-006   create a lookup value
  REQ-MDL-008               → API-MDL-007   update a lookup value
  REQ-MDL-009               → API-MDL-008   deactivate a lookup value
  REQ-MDL-010               → API-MDL-009   reorder a type's values
  REQ-MDL-013               → API-MDL-010   browse the type registry by owner
  REQ-MDL-011, REQ-MDL-012  → API-MDL-011   read active values by key — a consuming module's
                                            backend call, bound here and called by no screen
                                            (ADR-MDL-007)
UNMAPPED  REQ needing an endpoint that has none : none — all 13 requirements bind.
          Documented endpoint mapping to no REQ : none — all 11 endpoints bind.
          Operations the SRS names for which nothing is published: read one type by id · read
          one value by id. Neither is required by a REQ; both are omitted rather than faked,
          and the entry sub-views hydrate from the row the search query already holds
          (ADR-MDL-005). This SRS version names no `activate` action at either level (§B3:
          "لا إجراء تفعيل ولا محو نهائي"), so the half-toggle ADR-MDL-005 also covers is not a
          gap in this version — there is nothing to omit.
CODES     runtime error code → the RULE it carries, the link neither source states:
            MDL-409-MODULE-NOT-REGISTERED → RULE-MDL-001   (the owner module is registered in SEC)
            MDL-409-VALUE-DUP             → RULE-MDL-002   (no duplicate code within one type)
            MDL-404-TYPE-KEY              → RULE-MDL-004   (an inactive type hides its values)
            MDL-409-TYPE-DUP · MDL-404-TYPE · MDL-404-VALUE · MDL-400-REORDER-MISMATCH → no
            RULE of their own: platform-standard duplicate / not-found / validation rows under
            the ADR-SEC-002 umbrella the SRS §A5 names.
            RULE-MDL-003 carries no code at all: `key` is absent from the update request, so no
            code path can raise one. The form states the rule instead of waiting for a message.
          Platform rows the shared handler owns and this module does not mint:
            VALIDATION_ERROR (400) · ACCESS_DENIED (403) · METHOD_NOT_ALLOWED (405) ·
            DATA_INTEGRITY_VIOLATION (409) · INTERNAL_ERROR (500)
ENVELOPE  every response is wrapped in the published `ApiResponse<T>` — `success`, `data`,
          `error { code, message, fieldErrors[] { field, message } }`, `timestamp`. The
          published error object carries ONE message, not a bilingual pair: the client keys the
          text it displays on `error.code` against the module's catalog, which carries the ar
          and the en wording, so the language follows the user's locale; `error.message` is the
          fallback when a code is not in the catalog. `error.fieldErrors[].field` is what routes
          a validation message to a control.
PAGING    the published pagination envelope is `PageLookup<T>` (index.md), constraints from
          `PageableBuilder`: default page 0 · default size 20 · maximum size 200 — the same two
          numbers the SRS §B2 states. THREE response shapes travel in this module and the
          difference is load-bearing in every F2 block:
            · paginated  — API-MDL-001, API-MDL-005 only
            · bare array — API-MDL-009, API-MDL-010, API-MDL-011
            · one object — API-MDL-002, API-MDL-003, API-MDL-004, API-MDL-006, API-MDL-007,
                           API-MDL-008
SEARCH    the three reads of a collection are `POST …/search` carrying a
          `filters[] {field, operator, value}` envelope (operators EQUALS, NOT_EQUALS, LIKE,
          GREATER_THAN, GREATER_THAN_OR_EQUAL, LESS_THAN, LESS_THAN_OR_EQUAL, IN) — which is
          what the SRS §B5 tables state as well. Only the backend plan's contract summary still
          predicts GET for them, and that summary is never read as an API source (ADR-MDL-002).
LOOKUPS   none. MDL owns no lookup key and consumes none (SRS §A6): no field on either screen
          is backed by a list of values, no lookup hook exists anywhere in this plan, and no
          enum is modelled. The one constrained field, `ownerModuleCode`, binds to
          UXD-MDL-001's runtime-loaded list, which is another module's registry — not lookup
          data, and not read through API-MDL-011.
PERMS     declared by the backend and cited, never redeclared: PERM_MDL_LOOKUPS_VIEW (gateway) ·
          PERM_MDL_LOOKUPS_CREATE · PERM_MDL_LOOKUPS_UPDATE · PERM_MDL_LOOKUPS_DELETE ·
          PERM_MDL_TYPE_REGISTRY_VIEW. The api-docs state each endpoint's requirement, and the
          two deactivate endpoints require UPDATE, not DELETE — read there, not assumed here.
```

**Where the business codes are stated, and where they are not.** The api-docs publish this
module's business codes once, in the index's Known Error Codes table, and state per endpoint
only the structurally guaranteed answers (`ACCESS_DENIED`, and `VALIDATION_ERROR` where a body
is accepted). The per-block routing below therefore maps each code to the endpoint whose rule
scope can raise it — the mapping is this plan's reading, not a published per-endpoint list, and
no code appears below that the catalog does not publish.

### Reconciliation against the SRS — run once, before any F-content

- **Every REQ that needs an endpoint has one**, and every documented endpoint maps to a REQ —
  the BINDING block above is the whole mapping, with no gap in either direction.
- **Three reads are `POST …/search`** where the backend plan's contract summary predicts `GET`.
  The api-docs and the SRS §B5 tables agree with each other, so the backend plan is the one
  artifact that lags; `gov.py analyze` reports it there (C8.4). Recorded, not silently corrected
  (ADR-MDL-002).
- **The published ids are now the api-docs' own** — the generator emits the `Contract ID` line
  it emits for the other modules, so this plan cites `API-MDL-*` rather than the plan-local
  labels its previous revision used (ADR-MDL-011, superseding ADR-MDL-008).
- **Two by-id reads the SRS names are published nowhere**; both entry sub-views hydrate from the
  search cache and perform no second read (ADR-MDL-005).
- **`isActiveFl` is read-only at both levels**, which the SRS §B3 Editable column states as well;
  no published write DTO carries it, and the deactivate endpoints are what change it
  (ADR-MDL-006).
- **`ownerModuleCode` is a select over another module's registry**, cited as UXD-MDL-001 and
  never as a foreign path or a foreign API id (ADR-MDL-004); the foreign endpoint is named in
  `ui-ux-spec-mdl.md`, and the grant that must travel with the screen is ADR-MDL-013.
- **Action-level grants are readable from no published surface**, so no affordance is hidden on
  a guess; the page gate is the readable half and the server's 403 is the other (ADR-MDL-012).
- **Nothing is invented.** No value absent from the api-docs appears in this plan, and no
  permission name, route, component or field is derived from anything but the SRS, the api-docs
  and the profile's own stack.

## EXECUTION PLAN INDEX — MDL v1

| # | Phase | Split | Blocks |
|---|---|---|---|
| 1 | F1 — Models & Types | per screen — always | `SUB:F1-SCR-MDL-001`, `SUB:F1-SCR-MDL-002` |
| 2 | F2 — Data Hooks | per screen — always | `SUB:F2-SCR-MDL-001`, `SUB:F2-SCR-MDL-002` |
| 3 | F3 — Forms & Validators | per screen — always | `SUB:F3-SCR-MDL-001`, `SUB:F3-SCR-MDL-002` |
| 4 | F4 — Screens & Routes | per screen — always | `SUB:F4-SCR-MDL-001`, `SUB:F4-SCR-MDL-002` |
| 5 | SEC-FE | never split | level-1 only |
| 6 | ALIGN-FE | never split | level-1 only |

One `SUB` per screen in each of the four sub-bearing phases, at any screen count: what the split
exists to give is a per-screen address — an implementer takes one screen's F2 block, not "the F2
phase of a small module" — and that is worth the same at two screens as at twelve
(`profile.tracks.frontend.plans.exec`).

**SCREEN REGISTRY**

| SCR | الاسم / Name | Page code | Container pattern | Owning ENT |
|---|---|---|---|---|
| SCR-MDL-001 | اللوكبات العامة / Generic Lookups | MDL_LOOKUPS | TREE_MASTER_DETAIL | ENT-MDL-001 (+ ENT-MDL-002) |
| SCR-MDL-002 | سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner | MDL_TYPE_REGISTRY | FULL_PAGE — no entry sub-view (ADR-MDL-003) | ENT-MDL-001 |

<!-- PHASE:F1:START traces=SCR-MDL-001,SCR-MDL-002,UXD-MDL-001,REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013,AC-MDL-001,AC-MDL-003,AC-MDL-005,AC-MDL-006,AC-MDL-008,AC-MDL-010,AC-MDL-011,AC-MDL-013,API-MDL-001,API-MDL-002,API-MDL-003,API-MDL-004,API-MDL-005,API-MDL-006,API-MDL-007,API-MDL-008,API-MDL-009,API-MDL-010,API-MDL-011 -->
## PHASE 1 — F1 — Models & Types

Per entity and per screen: the source DTO with each property's type and its read-only /
system-only status, then the screen's search model, form model and container. Names are carried
per language (ar, en) wherever a label is modelled. Nothing is modelled that the api-docs do not
return, no internal identifier is invented, and **no enum and no lookup-backed field appears
anywhere in this phase** — MDL owns no coded list of its own (SRS §A6).

Field and DTO binding: see `_inputs/api-docs-mdl.md` — the published request and response shapes
for this module are the source and are not restated here. What is stated here is the shape's
consequence for the client: which properties a form may write, and which it may only display.

<!-- SUB:F1-SCR-MDL-001:START traces=SCR-MDL-001,UXD-MDL-001,REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,AC-MDL-001,AC-MDL-003,AC-MDL-005,AC-MDL-006,AC-MDL-008,AC-MDL-010,API-MDL-001,API-MDL-002,API-MDL-003,API-MDL-004,API-MDL-005,API-MDL-006,API-MDL-007,API-MDL-008,API-MDL-009 -->
### F1 · SCR-MDL-001 — اللوكبات العامة / Generic Lookups

#### F1-MODEL — ENT-MDL-001 — نوع اللوكب / LookupType
Source DTOs  : `LookupTypeResponse` (read) · `LookupTypeCreateRequest` · `LookupTypeUpdateRequest`
  lookupTypePk    : number · read-only (PK) · system-only · never shown as a business reference
  key             : string · maxLength 80 · required on create, **read-only on edit** —
                    RULE-MDL-003, and the update request does not carry it
  ownerModuleCode : string · maxLength 10 · required on create, **read-only on edit** — a plain
                    string holding the code, never an enum and never a union of literals; its
                    valid set is the security module's registry (UXD-MDL-001)
  nameAr          : string · required · maxLength 150
  nameEn          : string · required · maxLength 150
  isActiveFl      : boolean · read-only — flipped by API-MDL-004 alone (ADR-MDL-006)
  createdBy, createdAt, updatedBy, updatedAt : read-only · system-only (audit)

#### F1-MODEL — ENT-MDL-002 — قيمة اللوكب / LookupValue
Source DTOs  : `LookupValueResponse` (read) · `LookupValueCreateRequest` ·
               `LookupValueUpdateRequest` · `LookupValueReorderRequest`
  lookupValuePk : number · read-only (PK) · system-only
  lookupTypeId  : number · read-only — the path id of API-MDL-006, taken from the selected
                  parent, never typed
  code          : string · maxLength 50 · required on create, **read-only on edit** — the update
                  request does not carry it; unique within its type (RULE-MDL-002)
  nameAr        : string · required · maxLength 150
  nameEn        : string · required · maxLength 150
  sortOrder     : number · required on create **and** on update — and the same field the reorder
                  writes through `{ orderedValueIds[] }`; two paths, one field
  isActiveFl    : boolean · read-only — flipped by API-MDL-008 alone (ADR-MDL-006)
  createdBy, createdAt, updatedBy, updatedAt : read-only · system-only

#### F1-SCREEN — SCR-MDL-001
Search model : master — key : string · LIKE · ownerModuleCode : string · EXACT (options from the
               UXD-MDL-001 hook) · isActiveFl : boolean · EXACT · plus page, size, sortField,
               sortDirection, all inside the one request object of API-MDL-001
               detail — code : string · LIKE · lookupTypeId : number · EXACT (from the selected
               parent, not typed) · plus page, size, sortField, sortDirection, inside the one
               request object of API-MDL-005
Form model   : type · create — key, ownerModuleCode, nameAr, nameEn (all required)
               type · edit   — nameAr, nameEn (required); key and ownerModuleCode read-only
               value · create — code, nameAr, nameEn, sortOrder (all required)
               value · edit   — nameAr, nameEn, sortOrder (required); code read-only
               reorder        — the ordered list of value ids; not a per-row edit and not a form
               excluded system fields : both PKs · lookupTypeId · both isActiveFl · the audit four
Container    : TREE_MASTER_DETAIL — a master list of types and, beside it, the selected type's
               values; each of the two entry surfaces models its own record and nothing else
Both paged reads return the published pagination envelope; both write models drop every property
no published write DTO carries, so no form offers a field the server would ignore (ADR-MDL-006).
<!-- SUB:F1-SCR-MDL-001:END -->

<!-- SUB:F1-SCR-MDL-002:START traces=SCR-MDL-002,UXD-MDL-001,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013,AC-MDL-011,AC-MDL-013,API-MDL-010,API-MDL-011 -->
### F1 · SCR-MDL-002 — سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner

#### F1-MODEL — OwnerGroupResponse — المجموعة حسب المالك / Owner group
Source DTO   : `OwnerGroupResponse[]` — a bare array (API-MDL-010); this screen writes nothing
  ownerModuleCode : string · read-only — the group key, labelled through UXD-MDL-001
  types[]         : the same type projection as above, **every property read-only here** —
                    lookupTypePk, key, ownerModuleCode, nameAr, nameEn, isActiveFl and the audit
                    four. The type model of `F1-SCR-MDL-001` is reused, not re-declared; only its
                    writability differs, and on this screen there is none.

#### F1-MODEL — LookupValueResponse (read by key) — قيم المستهلك / the consumer's values
Source DTO   : `LookupValueResponse[]` — a bare array (API-MDL-011), active values only, ordered
               by `sortOrder`. Modelled for completeness of the published surface and bound in
               F2; **no component of this plan renders it** (ADR-MDL-007).

#### F1-SCREEN — SCR-MDL-002
Search model : ownerModuleCode : string · EXACT · key : string · LIKE. **No page, no size, no
               sort** — the request object of API-MDL-010 carries `filters` alone, so none of the
               three is modelled and none belongs in this screen's cache key
Form model   : none — a read-only browse (SRS §B3); no property of the response is writable
Container    : FULL_PAGE, no entry sub-view (ADR-MDL-003) — the owner → types hierarchy is the
               grouped list the endpoint returns, not a second pane with a form in it
The response is a bare array of groups and is modelled as one: reading it through a pagination
envelope would invent fields the endpoint does not send.
<!-- SUB:F1-SCR-MDL-002:END -->
<!-- PHASE:F1:END -->

<!-- PHASE:F2:START traces=SCR-MDL-001,SCR-MDL-002,UXD-MDL-001,REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013,AC-MDL-001,AC-MDL-002,AC-MDL-004,AC-MDL-005,AC-MDL-007,AC-MDL-009,AC-MDL-010,AC-MDL-011,AC-MDL-012,AC-MDL-013,API-MDL-001,API-MDL-002,API-MDL-003,API-MDL-004,API-MDL-005,API-MDL-006,API-MDL-007,API-MDL-008,API-MDL-009,API-MDL-010,API-MDL-011 -->
## PHASE 2 — F2 — Data Hooks

What each screen needs from the API — not hook code. Verb, path and request/response shape are
cited by the API id at each block's head and are read in the api-docs, never restated. Every read
query's cache key carries every filter that changes the response, page and size included **where
the endpoint is paged**; page and page size live inside the filter object and are never
independent state. Every mutation declares its invalidation. Components use the facade only; the
facade uses the declared queries only (server-state library: `tanstack-query`).

Error routing is uniform and is stated per block only where a business code makes it specific:
field validation → inline on the field `error.fieldErrors[].field` names · business rule → the
user message for that rule · unauthenticated → the platform's sign-in destination, discarding the
server-state cache · forbidden → the localized catalog message on the surface that attempted the
call · server → the generic message.

<!-- SUB:F2-SCR-MDL-001:START traces=SCR-MDL-001,UXD-MDL-001,REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,AC-MDL-001,AC-MDL-002,AC-MDL-004,AC-MDL-005,AC-MDL-007,AC-MDL-009,AC-MDL-010,API-MDL-001,API-MDL-002,API-MDL-003,API-MDL-004,API-MDL-005,API-MDL-006,API-MDL-007,API-MDL-008,API-MDL-009 -->
### F2 · SCR-MDL-001 — اللوكبات العامة / Generic Lookups

#### F2-QUERY — TYPE SEARCH — API-MDL-001      traces=API-MDL-001,REQ-MDL-001,REQ-MDL-003
Kind         : read query — a POST that mutates nothing (ADR-MDL-002); paginated response
Cache key    : `[lookup-types, filters]`, `filters` being the whole request object — key,
               ownerModuleCode, isActiveFl, sortField, sortDirection **and page, size**. Every
               one of them changes the response, so every one is in the key.
Errors       : `VALIDATION_ERROR` → inline on the offending filter · `ACCESS_DENIED` → the
               localized forbidden message · `INTERNAL_ERROR` → generic
Loading      : LOCAL — the SRS states nothing about this call being slow, so no global indicator
Cache policy : defaults
Invalidation : n/a (a read); refreshed by API-MDL-002, API-MDL-003 and API-MDL-004

#### F2-QUERY — TYPE CREATE — API-MDL-002      traces=API-MDL-002,REQ-MDL-001,REQ-MDL-002,AC-MDL-001,AC-MDL-002
Kind         : mutation
Errors       : `MDL-409-MODULE-NOT-REGISTERED` → the RULE-MDL-001 message, routed to the
               owner-module field — ar: «الوحدة المالكة غير مسجّلة في وحدة الأمان» ·
               en: "The owning module is not registered in the Security module" ·
               `MDL-409-TYPE-DUP` → inline on `key` (the platform duplicate row) ·
               `VALIDATION_ERROR` → inline per `error.fieldErrors[].field` ·
               `ACCESS_DENIED` → the localized forbidden message (ADR-MDL-012)
Loading      : LOCAL — on the submitting form
Invalidation : `[lookup-types, *]`, and `[lookup-types-by-owner, *]` — a new type changes what
               SCR-MDL-002's registry shows
Success      : ar: «تم حفظ نوع اللوكب.» · en: "The lookup type has been saved." (AC-MDL-001)

#### F2-QUERY — TYPE UPDATE — API-MDL-003      traces=API-MDL-003,REQ-MDL-003,AC-MDL-003
Kind         : mutation
Errors       : `MDL-404-TYPE` → user message · `VALIDATION_ERROR` → inline ·
               `ACCESS_DENIED` → the localized forbidden message
Loading      : LOCAL
Invalidation : `[lookup-types, *]`, `[lookup-types-by-owner, *]`
The request carries the two names and nothing else — RULE-MDL-003 expressed in the surface
itself: there is no key field to send, and none is sent.

#### F2-QUERY — TYPE DEACTIVATE — API-MDL-004  traces=API-MDL-004,REQ-MDL-004,AC-MDL-004
Kind         : mutation · no request body
Errors       : `MDL-404-TYPE` → user message · `ACCESS_DENIED` → the localized forbidden message
Loading      : LOCAL — on the confirmation
Invalidation : `[lookup-types, *]`, `[lookup-types-by-owner, *]` **and** `[lookup-values, *]` —
               RULE-MDL-004 makes an inactive type's values invisible to consumers, so a cached
               value list of that type is stale in meaning even though its rows did not change
Requires `PERM_MDL_LOOKUPS_UPDATE` per the api-docs, not DELETE — read there, not assumed.

#### F2-QUERY — VALUE SEARCH — API-MDL-005     traces=API-MDL-005,REQ-MDL-005,AC-MDL-005
Kind         : read query (ADR-MDL-002); paginated response
Cache key    : `[lookup-values, filters]` — lookupTypeId, code, sortField, sortDirection **and
               page, size**. The parent id is part of the key, so selecting another type is a
               different cache entry, not a refetch of the same one.
Errors       : `VALIDATION_ERROR` → inline on the offending filter · `ACCESS_DENIED` → the
               localized forbidden message · `INTERNAL_ERROR` → generic
Loading      : LOCAL — on the values pane alone; the master list does not blank while it loads
Cache policy : defaults
Invalidation : n/a (a read); refreshed by API-MDL-006, API-MDL-007, API-MDL-008, API-MDL-009
This read returns inactive values beside active ones — it is the manager's view, not the
consumer's, and the screen shows what API-MDL-011 hides (AC-MDL-005, AC-MDL-009).

#### F2-QUERY — VALUE CREATE — API-MDL-006     traces=API-MDL-006,REQ-MDL-006,REQ-MDL-007,AC-MDL-006,AC-MDL-007
Kind         : mutation · the parent type is the path id, taken from the selection
Errors       : `MDL-409-VALUE-DUP` → the RULE-MDL-002 message, routed inline to `code` —
               ar: «هذا الرمز مستخدم بالفعل ضمن هذا النوع» ·
               en: "This code is already used within this type" ·
               `MDL-404-TYPE` → user message (the parent type is gone) ·
               `VALIDATION_ERROR` → inline · `ACCESS_DENIED` → the localized forbidden message
Loading      : LOCAL
Invalidation : `[lookup-values, *]`
Success      : ar: «تم حفظ القيمة.» · en: "The value has been saved." (AC-MDL-006)

#### F2-QUERY — VALUE UPDATE — API-MDL-007     traces=API-MDL-007,REQ-MDL-008,AC-MDL-008
Kind         : mutation
Errors       : `MDL-404-VALUE` → user message · `MDL-409-VALUE-DUP` → inline on `code` if the
               server ever raises it here (RULE-MDL-002 is scoped to create and update alike) ·
               `VALIDATION_ERROR` → inline · `ACCESS_DENIED` → the localized forbidden message
Loading      : LOCAL
Invalidation : `[lookup-values, *]` — the submitted `sortOrder` may have moved the row

#### F2-QUERY — VALUE DEACTIVATE — API-MDL-008 traces=API-MDL-008,REQ-MDL-009,AC-MDL-009
Kind         : mutation · no request body
Errors       : `MDL-404-VALUE` → user message · `ACCESS_DENIED` → the localized forbidden message
Loading      : LOCAL — on the confirmation
Invalidation : `[lookup-values, *]`
The row stays in this screen's list afterwards, marked inactive: it leaves the consumer's read,
not the manager's (AC-MDL-009).

#### F2-QUERY — VALUE REORDER — API-MDL-009    traces=API-MDL-009,REQ-MDL-010,AC-MDL-010
Kind         : mutation · request `{ orderedValueIds[] }` · response a bare array in the
               persisted order — not a paginated envelope
Errors       : `MDL-400-REORDER-MISMATCH` → user message on the value list (the submitted set is
               not exactly that type's values) · `MDL-404-TYPE` → user message ·
               `ACCESS_DENIED` → the localized forbidden message
Loading      : LOCAL — on the list, with the dragged order held until the call settles
Invalidation : `[lookup-values, *]` — every row's `sortOrder` may have changed, and the response
               carries the persisted order the list then renders from
The whole ordered set is submitted, never one row's new position: the endpoint takes
`orderedValueIds[]`, and the mismatch code exists precisely because a partial set is wrong.

#### F2-LOOKUP — none
MDL owns no lookup key and consumes none (SRS §A6). The one option list on this screen is the
owner-module select, which is not lookup data: it is UXD-MDL-001, declared below.

#### F2-SCREEN-INIT — SCR-MDL-001
Permission read : `MDL_LOOKUPS` present in the caller's effective menu → VIEW, the gateway.
                  CREATE, UPDATE and DELETE are readable from no published surface; the server
                  answers them per call (ADR-MDL-012).
Lookups used    : none
Foreign data    : the owner-module select and the owner-module filter resolve through
                  UXD-MDL-001 — ONE shared hook, long-lived cache, shared with SCR-MDL-002. A
                  refused read leaves the select empty and disabled, and the create action
                  disabled behind it (ADR-MDL-013)
Entity by id    : none published at either level; both entry sub-views hydrate from the row their
                  own search query already holds, so opening an editor performs no second read
                  (ADR-MDL-005)

#### F2-FACADE — SCR-MDL-001
Composes     : API-MDL-001, API-MDL-005 (the two lists) · API-MDL-002, API-MDL-003, API-MDL-004,
               API-MDL-006, API-MDL-007, API-MDL-008, API-MDL-009 (the mutations) · the
               UXD-MDL-001 hook
State it owns: the type list and the selected type's value list, both derived from their queries'
               data and never copied into state; the selected type id, read from the route param;
               two filter objects, each carrying its own page and size; the pending drag order
               while a reorder is in flight; the open editor's identity, held in navigation state
               rather than in a boolean; and a derived loading flag over the calls in flight
Operations   : createType · updateType · deactivateType (confirmed, and the confirmation says
               consumer reads will stop returning this type's values — RULE-MDL-004) ·
               createValue · updateValue · deactivateValue (confirmed) · reorderValues (the whole
               ordered list, submitted once)
Ordered pairs: none. **No operation of this screen owns two calls** — a type's write carries no
               values, so nothing here updates a record and then replaces its child set, and no
               ordering or skip-if-unchanged rule is needed. Each operation is exactly one call.
There is no activateType and no activateValue: the SRS names no such action and no endpoint
exists for one (ADR-MDL-005). Components use the facade only; the facade uses the declared
queries only.
<!-- SUB:F2-SCR-MDL-001:END -->

<!-- SUB:F2-SCR-MDL-002:START traces=SCR-MDL-002,UXD-MDL-001,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013,AC-MDL-011,AC-MDL-012,AC-MDL-013,API-MDL-010,API-MDL-011 -->
### F2 · SCR-MDL-002 — سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner

#### F2-QUERY — REGISTRY BROWSE — API-MDL-010  traces=API-MDL-010,REQ-MDL-013,AC-MDL-013
Kind         : read query (ADR-MDL-002) · request carries `filters` alone — no sort, no paging ·
               response a bare array of owner groups
Cache key    : `[lookup-types-by-owner, filters]` — ownerModuleCode and key, and nothing more.
               No page or size belongs in this key because the endpoint accepts neither; adding
               them would key a variation the server cannot produce.
Errors       : `VALIDATION_ERROR` → inline on the offending filter · `ACCESS_DENIED` → the
               localized forbidden message · `INTERNAL_ERROR` → generic
Loading      : LOCAL
Cache policy : defaults
Invalidation : n/a (a read). It is refreshed by SCR-MDL-001's type mutations, which name this
               key family in their own invalidation lines — a type created, renamed or
               deactivated there changes what this registry shows.

#### F2-QUERY — CONSUMER READ BY KEY — API-MDL-011  traces=API-MDL-011,REQ-MDL-011,REQ-MDL-012,AC-MDL-011,AC-MDL-012
Kind         : read query · **bound, and called by no screen of this module** (ADR-MDL-007)
Caller       : a consuming module's backend, over the platform's in-process module interface —
               not a user at a screen. Stated here so the published surface is completely
               accounted for.
Errors       : `MDL-404-TYPE-KEY` [REQ-MDL-012, RULE-MDL-004] → answered to the calling module —
               ar: «لا يوجد نوع لوكب بهذا المفتاح» · en: "No lookup type exists with this key" —
               an unknown key is a not-found, never an empty success ·
               `ACCESS_DENIED` → answered to the calling module
Cache key    : n/a — no client of this plan calls it
Invalidation : n/a. What a user can observe of this endpoint is indirect: deactivating a value,
               or its type, on SCR-MDL-001 is what makes it stop being returned (RULE-MDL-004).

#### F2-LOOKUP — none
Same as the other screen, and for the same reason: MDL owns no lookup key and consumes none.

#### F2-SCREEN-INIT — SCR-MDL-002
Permission read : `MDL_TYPE_REGISTRY` present in the caller's effective menu → VIEW. This screen
                  has no other action to gate (SRS §B4).
Lookups used    : none
Foreign data    : the owner-module filter and the group headings resolve through UXD-MDL-001 —
                  the same shared hook SCR-MDL-001 uses, not a second call. A refused read
                  leaves the headings showing the bare code the browse already returns; browsing
                  is never blocked by it (ADR-MDL-013)
Entity by id    : none — each group carries its full type rows

#### F2-FACADE — SCR-MDL-002
Composes     : API-MDL-010 · the UXD-MDL-001 hook
State it owns: the groups derived from the query's data, and the filter object (owner module,
               key) mirrored from the route's search params, so a filtered registry view is
               shareable by address (ADR-MDL-002). No grouping and no count is composed here —
               both arrive in the response.
Operations   : none — this screen writes nothing. Opening a type in SCR-MDL-001 is a route
               change, not an operation.
Ordered pairs: none — there is no call to order.
<!-- SUB:F2-SCR-MDL-002:END -->
<!-- PHASE:F2:END -->

<!-- PHASE:F3:START traces=SCR-MDL-001,SCR-MDL-002,UXD-MDL-001,REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-010,REQ-MDL-011,REQ-MDL-013,AC-MDL-001,AC-MDL-002,AC-MDL-003,AC-MDL-004,AC-MDL-006,AC-MDL-007,AC-MDL-008,AC-MDL-013,API-MDL-001,API-MDL-002,API-MDL-003,API-MDL-005,API-MDL-006,API-MDL-007,API-MDL-010 -->
## PHASE 3 — F3 — Forms & Validators

One block per `RULE-*` a form enforces, plus the field constraints the published DTOs state. No
frontend-only validation the SRS does not state; every message is read from its catalog code
rather than hard-coded; the locale resolves session → browser → `ar` (`profile.languages.primary`);
and a caller without the write permission is answered by the server, not by a pre-emptively
disabled field (ADR-MDL-012). Schemas are written with `zod` and bound with `react-hook-form`.

**No option-set validator exists anywhere in this module.** MDL owns no coded list (SRS §A6), so
no field binds to a set of lookup values. The one field with a constrained set is
`ownerModuleCode`, whose set is another module's registry: its validator binds to the
runtime-loaded list of UXD-MDL-001, never to a static list of module codes.

<!-- SUB:F3-SCR-MDL-001:START traces=SCR-MDL-001,UXD-MDL-001,REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-010,AC-MDL-001,AC-MDL-002,AC-MDL-003,AC-MDL-004,AC-MDL-006,AC-MDL-007,AC-MDL-008,API-MDL-001,API-MDL-002,API-MDL-003,API-MDL-005,API-MDL-006,API-MDL-007 -->
### F3 · SCR-MDL-001 — اللوكبات العامة / Generic Lookups

Validation timing for this screen, declared once and holding for both entry surfaces: **on blur
for the unique key and the unique code, on submit for everything else.**

#### F3-FIELD — SCR-MDL-001 (type · create)
key             · REQUIRED · LENGTH (maxLength 80) · UNIQUE_CHECK · on blur
ownerModuleCode · REQUIRED · LENGTH (maxLength 10) · MEMBER_OF the UXD-MDL-001 list ·
                  BUSINESS_RULE (RULE-MDL-001) · on submit
nameAr, nameEn  · REQUIRED · LENGTH (maxLength 150) · on submit

#### F3-FIELD — SCR-MDL-001 (type · edit)
key, ownerModuleCode · read-only — not inputs at all; the update request carries neither
nameAr, nameEn       · REQUIRED · LENGTH (maxLength 150) · on submit

#### F3-FIELD — SCR-MDL-001 (value · create)
code            · REQUIRED · LENGTH (maxLength 50) · UNIQUE_CHECK within the selected type
                  (RULE-MDL-002) · on blur
nameAr, nameEn  · REQUIRED · LENGTH (maxLength 150) · on submit
sortOrder       · REQUIRED · integer · on submit

#### F3-FIELD — SCR-MDL-001 (value · edit)
code            · read-only — the update request does not carry it
nameAr, nameEn  · REQUIRED · LENGTH (maxLength 150) · on submit
sortOrder       · REQUIRED · integer · on submit

UNIQUE_CHECK    : async, on blur — the type's `key` through API-MDL-001 with an EQUALS filter;
                  the value's `code` through API-MDL-005 with EQUALS filters on **both**
                  `lookupTypeId` and `code`, so the check's scope is the rule's scope. Neither
                  blocks submit on its own: `MDL-409-TYPE-DUP` and `MDL-409-VALUE-DUP` from the
                  server are the authority, routed inline to the same field. On edit neither
                  field is an input, so neither check runs.

#### F3-VALIDATION — RULE-MDL-001   traces=REQ-MDL-002,AC-MDL-002
Statement : The system shall reject a lookup type registration whose owner module code has no
            ModuleRegistry row in the security module.
Message   : catalog code `MDL-409-MODULE-NOT-REGISTERED` —
            ar: «الوحدة المالكة غير مسجّلة في وحدة الأمان» ·
            en: "The owning module is not registered in the Security module"
Scope     : CREATE · Field : ownerModuleCode · kind BUSINESS_RULE · when submit
Shape     : the control is a select over the registered module codes loaded through UXD-MDL-001,
            so the common case cannot be typed wrong at all; the validator asserts that the
            submitted value is one the runtime-loaded list contains, never that it is one of a
            static set. The server stays the authority — a module deregistered between load and
            submit is caught there — and the catalog message routes to this field. When the
            foreign read is refused the select is empty and disabled and create is disabled
            behind it, rather than falling back to free text (ADR-MDL-013).

#### F3-VALIDATION — RULE-MDL-002   traces=REQ-MDL-007,AC-MDL-007
Statement : The system shall reject a lookup value whose code already exists under the same
            lookup type.
Message   : catalog code `MDL-409-VALUE-DUP` — ar: «هذا الرمز مستخدم بالفعل ضمن هذا النوع» ·
            en: "This code is already used within this type"
Scope     : CREATE (and UPDATE, per the rule's trigger) · Field : the value's code ·
            kind UNIQUE_CHECK · when blur, and again on submit by the server
Shape     : uniqueness is scoped to the parent type, never globally — the same code under
            another type is legitimate, and a global check would reject a value the server
            accepts. The async check is bound to the selected parent id. On edit the field is
            read-only, so the client check cannot fire and the rule is the server's alone.

#### F3-VALIDATION — RULE-MDL-003   traces=REQ-MDL-003,AC-MDL-003
Statement : The system shall prevent editing a lookup type's key after creation.
Message   : the rule's own text — ar: «لا يمكن تعديل مفتاح نوع اللوكب بعد إنشائه» ·
            en: "A lookup type's key cannot be changed after creation"
Scope     : UPDATE · Field : key · kind BUSINESS_RULE · not a form check at all
Shape     : there is **nothing to validate** — `key` is not an input on edit, because the update
            request does not carry it. The rule is expressed by the absence of the field rather
            than by a message on a control that would refuse. The form still states the rule
            beside the read-only key, so an editor learns why it cannot be changed instead of
            meeting a disabled control with no explanation.

#### F3-VALIDATION — RULE-MDL-004   traces=REQ-MDL-004,AC-MDL-004
Statement : While a lookup type is inactive, the system shall exclude its values from consumer
            reads.
Message   : the rule's own text — ar: «هذا النوع معطّل حاليًا» ·
            en: "This lookup type is currently inactive"
Scope     : the deactivate action (API-MDL-004) · Field : none — a row action ·
            kind BUSINESS_RULE · when submit
Shape     : **not a validation this form performs** — it is a consequence the deactivate
            confirmation names before the act: every consuming module stops receiving this
            type's values. The rule's text is also the state label on an inactive type row, so
            the same words explain the row and the warning. Nothing on this screen is hidden by
            it: the manager's value list still shows the values, which is the difference between
            this screen and a consumer.

Business-code fields: `key` and `code` are client-chosen strings, not platform-numbered, and both
are read-only after create per the two update DTOs. Neither is generated or predicted on the
client (SRS §3.3 numbering).
Locale : session → browser → `ar`.
Permission-driven behaviour: a caller without CREATE, UPDATE or DELETE receives `ACCESS_DENIED`
on submit and the form shows the localized message; fields are not pre-emptively disabled,
because no published surface tells this screen which actions its caller holds (ADR-MDL-012).
<!-- SUB:F3-SCR-MDL-001:END -->

<!-- SUB:F3-SCR-MDL-002:START traces=SCR-MDL-002,UXD-MDL-001,REQ-MDL-011,REQ-MDL-013,AC-MDL-013,API-MDL-010 -->
### F3 · SCR-MDL-002 — سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner

This screen has **no form**: SRS §B3 reads "read-only browse; no create/update here", and every
property of the response is read-only.

#### F3-FIELD — SCR-MDL-002 (filters — not a form)
ownerModuleCode · optional · the select offers the UXD-MDL-001 list, so only registered module
                  codes can be chosen; a code that is no longer registered but still owns types
                  is still shown in the results, because the grouping is the server's
key             · optional · LENGTH (maxLength 80) · a LIKE filter

#### F3-VALIDATION — none on this screen
No `RULE-*` is enforced here, because nothing is written. RULE-MDL-004's effect is visible — a
deactivated type leaves this registry's active set — but the rule fires on the consumer read
(API-MDL-011), not on this screen.
Shape  : filter validation only, written with `zod` over the route's search params, so an address
         someone shared is validated exactly as a typed filter is.
Locale : session → browser → `ar`.
Permission-driven behaviour: a caller without VIEW never reaches this screen — the navigation
guard of SEC-FE stops the route before any of this runs.
<!-- SUB:F3-SCR-MDL-002:END -->
<!-- PHASE:F3:END -->

<!-- PHASE:F4:START traces=SCR-MDL-001,SCR-MDL-002,UXD-MDL-001,REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013,AC-MDL-005,AC-MDL-009,AC-MDL-010,AC-MDL-013,API-MDL-001,API-MDL-002,API-MDL-003,API-MDL-004,API-MDL-005,API-MDL-006,API-MDL-007,API-MDL-008,API-MDL-009,API-MDL-010,API-MDL-011 -->
## PHASE 4 — F4 — Screens & Routes

One block per `SCR-*`: routes, chunk, guard, facade, components, composition and cross-module
citations. Routes are named by the container pattern — `TREE_MASTER_DETAIL` → one page hosting
the master list and the detail beside it, with the list route registered **before** any `:id`
route and every static segment registered before the parameterised ones; `FULL_PAGE` with no
entry sub-view → a single page and no entry route. One lazily-loaded chunk per composite screen.
Every `PERM_*` name below is the backend registry's, cited and never invented, and every route
sits under the module segment `/reference-data`.

<!-- SUB:F4-SCR-MDL-001:START traces=SCR-MDL-001,UXD-MDL-001,REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,AC-MDL-005,AC-MDL-009,AC-MDL-010,API-MDL-001,API-MDL-002,API-MDL-003,API-MDL-004,API-MDL-005,API-MDL-006,API-MDL-007,API-MDL-008,API-MDL-009 -->
### F4 · SCR-MDL-001 — اللوكبات العامة / Generic Lookups

#### F4-SCREEN — SCR-MDL-001
Routes       : base slug `lookups`, under `/reference-data` —
               `/reference-data/lookups` — the type list, registered **before** any `:id` route ·
               `/reference-data/lookups/new` — the type entry, a **static** segment registered
               before the parameterised ones ·
               `/reference-data/lookups/:typeId` — that type's values beside the list ·
               `/reference-data/lookups/:typeId/edit` — the type entry, edit ·
               `/reference-data/lookups/:typeId/values/new` — the value entry, create ·
               `/reference-data/lookups/:typeId/values/:valueId/edit` — the value entry, edit
Chunk        : one lazy chunk for this composite screen — both panes and both entry surfaces
               share it; neither entry is a chunk of its own
Guard        : every route element guarded by `PERM_MDL_LOOKUPS_VIEW`, evaluated as "`MDL_LOOKUPS`
               is in the caller's effective menu". The write routes carry the same guard, because
               CREATE, UPDATE and DELETE are readable from no published surface and the server's
               403 is the authority on the write itself (ADR-MDL-012)
Facade       : the SCR-MDL-001 facade of F2 — the page never calls a query directly
Components   : `LookupsPage` (route-level, TREE_MASTER_DETAIL) · `LookupTypeList`,
               `LookupTypeFilters`, `LookupTypeForm`, `LookupValuePane`, `LookupValueRow`,
               `LookupValueFilters`, `LookupValueForm`, `ValueReorderHandle`, `DeactivateConfirm`
               (presentational)
Mode         : CREATE | EDIT | VIEW resolved from the route match — `/new` and `/values/new` →
               CREATE, `/edit` → EDIT, `/:typeId` → VIEW — never from a parent's prop
Composition  : the spec's line resolved to components. Nothing is inline: `LookupValuePane` is not
               a control inside `LookupTypeForm`, and the type's write carries no values, so the
               type form has nothing of the values in it. Each value is a **summary row**
               (`LookupValueRow` — code, both names, order, state) and its editor is the **second
               level**: `LookupValueForm` is a SIBLING of `LookupValuePane`, never rendered inside
               its element; it is opened from the route (`/values/new`, `/values/:valueId/edit`),
               so back closes it, dismissing closes only it and a deep link opens it; it carries
               no scroll region of its own — the pane scrolls with the body it sits in
Saves        : ONE per open surface, and one surface at a time. `LookupTypeForm` submits once
               (API-MDL-002 or API-MDL-003); `LookupValueForm` submits once (API-MDL-006 or
               API-MDL-007) for its own record. The two are never open together: a value route is
               a state of the detail level, not a panel beside the open type form. **No action of
               this screen owns two calls**, so no ordered pair and no skip-if-unchanged rule
               arises. Deactivate and reorder are direct actions over a row and over the list;
               neither is a form and neither carries a second submit
Cross-module : UXD-MDL-001 — the owner-module select on the type entry, the owner-module filter
               on the master list, and the owner column of the list. The one field on this screen
               whose authoritative source is another module
The selected type is a route param, so a type's values are a linkable address and the browser's
back gesture returns to the list. Both levels offer Deactivate and neither offers an Activate: no
endpoint exists for the second half, and the confirmation says the act is not reversible from
this screen (ADR-MDL-005). The drag handle submits the whole ordered set through API-MDL-009
rather than writing one row's `sortOrder`.
<!-- SUB:F4-SCR-MDL-001:END -->

<!-- SUB:F4-SCR-MDL-002:START traces=SCR-MDL-002,UXD-MDL-001,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013,AC-MDL-013,API-MDL-010,API-MDL-011 -->
### F4 · SCR-MDL-002 — سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner

#### F4-SCREEN — SCR-MDL-002
Routes       : base slug `type-registry`, under `/reference-data` —
               `/reference-data/type-registry` — the only route. No `new`, no `:id`, no
               `:id/edit`: this screen addresses no record it could edit. The owner module and
               the key filter live in the route's search params, so the browse IS its address
Chunk        : one lazy chunk for this screen
Guard        : the route element guarded by `PERM_MDL_TYPE_REGISTRY_VIEW`, evaluated as
               "`MDL_TYPE_REGISTRY` is in the caller's effective menu"
Facade       : the SCR-MDL-002 facade of F2
Components   : `TypeRegistryPage` (route-level, FULL_PAGE) · `RegistryFilters`,
               `OwnerGroupSection`, `RegistryTypeTable` (presentational)
Mode         : not applicable — no CREATE, EDIT or VIEW mode to resolve; this screen writes
               nothing
Composition  : `none`, resolved: no picker, no child-row editor, no second level and no component
               opened over this page. `OwnerGroupSection` renders the groups the endpoint
               returns, in the endpoint's own grouping
Saves        : none — the screen submits nothing at all. Its one affordance is a link into
               SCR-MDL-001, which is a navigation, not a save
Cross-module : UXD-MDL-001 — here it is the grouping itself and the label on each group heading,
               not a field of a form
Each type row links to `/reference-data/lookups/:typeId`, which is SCR-MDL-001's own route and
carries its own guard: reviewing and managing are two steps of one task, and this screen does
neither half of the second. API-MDL-011 has no component and no route here (ADR-MDL-007).
<!-- SUB:F4-SCR-MDL-002:END -->
<!-- PHASE:F4:END -->

<!-- PHASE:SEC-FE:START traces=SCR-MDL-001,SCR-MDL-002,UXD-MDL-001,REQ-MDL-001,REQ-MDL-004,REQ-MDL-005,REQ-MDL-009,REQ-MDL-013 -->
## PHASE 5 — SEC-FE

The frontend half of the security model, per `SCR-*`: the navigation guard and the UI behaviour
per action. Permission names are the backend registry's and the SRS Access summary's, cited and
never redeclared. One mechanism gates both screens — the **menu gate**: the screen's page code is
present in the effective menu the security module serves this caller. Action-level grants are
published nowhere, so an action's affordance renders for a caller who holds the screen and the
server's `ACCESS_DENIED` is the authority, shown as its localized message (ADR-MDL-012). Never
split — level-1 only.

### SEC-FE · SCR-MDL-001 — اللوكبات العامة / Generic Lookups
Permissions      : `PERM_MDL_LOOKUPS_VIEW` (gateway) · `PERM_MDL_LOOKUPS_CREATE` ·
                   `PERM_MDL_LOOKUPS_UPDATE` · `PERM_MDL_LOOKUPS_DELETE`
Navigation guard : `MDL_LOOKUPS` must be in the caller's effective menu. A caller without it is
                   sent to the unauthorized destination, and every route of this screen — the
                   list, `new`, `:typeId`, `:typeId/edit` and both value routes — carries the
                   same guard.
Per action       : **VIEW** → the gate above, exact, and it covers both panes: a caller who holds
                   the screen sees types and values alike; without it no route of this screen
                   renders at all. **CREATE** (a type, and a value under it) → not readable
                   before the call; the affordance renders and a 403 is shown as the localized
                   message on the form that attempted it. **UPDATE** (edit at either level, and
                   the reorder) → the same. **DELETE** (which here means deactivate and nothing
                   else — no hard delete exists at either level) → the same.

**Registered, required by nothing.** The api-docs put `PERM_MDL_LOOKUPS_UPDATE` on **both** deactivate
endpoints, so no published endpoint of this module requires `PERM_MDL_LOOKUPS_DELETE`, which the
backend registry and the SRS Access summary both declare. The frontend neither checks it nor
depends on it: the guard it can evaluate is the page gate, and the authority on a write is the
server. Recorded, not corrected — the permission an endpoint requires is the backend's to state.

The screen-level grant is the whole granularity available. Per-lookup-type permissions — letting
a role manage one type but not another — are an explicit SRS scope exception (§A2), so no
per-type gate is drawn, attempted or hinted at in the UI.
Foreign grant    : a role granted `PERM_MDL_LOOKUPS_CREATE` needs `PERM_SEC_MODULE_REGISTRY_VIEW`
                   as well, or the owner-module select it must fill stays empty and disabled. The
                   grant is the security module's to make; this plan names it and mints nothing
                   (UXD-MDL-001, ADR-MDL-013).

### SEC-FE · SCR-MDL-002 — سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner
Permissions      : `PERM_MDL_TYPE_REGISTRY_VIEW`
Navigation guard : `MDL_TYPE_REGISTRY` must be in the caller's effective menu.
Per action       : **VIEW** → the gate above. There is no CREATE, UPDATE or DELETE on this
                   screen: the SRS Access summary gives it VIEW alone and the screen writes
                   nothing, so there is no affordance to hide and no submit to refuse. Its links
                   into SCR-MDL-001 render unconditionally; the target route's own guard stops a
                   caller who does not hold that screen, so a reviewer who may browse but not
                   manage sees the registry and is refused at the door of the editor rather than
                   shown a dead link.

**Across both screens.** A forbidden response is shown as its localized catalog message, never as
a silent no-op and never as a generic failure. An unauthenticated response returns the caller to
the platform's sign-in destination and discards the server-state cache, so no data of the
previous identity survives into the next. No screen composes a permission name and no screen
keeps a local copy of the caller's grants: the menu response is the single source, and a failure
to load it renders no entry of this module and grants no route of it — access narrows, never
widens.
<!-- PHASE:SEC-FE:END -->

<!-- PHASE:ALIGN-FE:START traces=SCR-MDL-001,SCR-MDL-002,UXD-MDL-001,REQ-MDL-001,REQ-MDL-002,REQ-MDL-003,REQ-MDL-004,REQ-MDL-005,REQ-MDL-006,REQ-MDL-007,REQ-MDL-008,REQ-MDL-009,REQ-MDL-010,REQ-MDL-011,REQ-MDL-012,REQ-MDL-013,AC-MDL-001,AC-MDL-002,AC-MDL-003,AC-MDL-004,AC-MDL-005,AC-MDL-006,AC-MDL-007,AC-MDL-008,AC-MDL-009,AC-MDL-010,AC-MDL-011,AC-MDL-012,AC-MDL-013,API-MDL-001,API-MDL-002,API-MDL-003,API-MDL-004,API-MDL-005,API-MDL-006,API-MDL-007,API-MDL-008,API-MDL-009,API-MDL-010,API-MDL-011 -->
## PHASE 6 — ALIGN-FE

The alignment self-check is this phase's content. Every row names the check that backs it, and
there are no other rows: a row nothing can falsify manufactures confidence, so a dimension with
no check behind it is not asserted here at all. The `RESULT` row is written by the orchestrator
from the analyze report. Never split — level-1 only.

```
ALIGN-FE — MDL v1
row           backing check   assertion
SCREENS       orphans         every SCR is referenced by a plan block — SCR-MDL-001 and
                              SCR-MDL-002 each carry a SUB in F1, F2, F3 and F4 and a block in
                              SEC-FE
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it
                              saves once — SCR-MDL-001: none inline, a summary row per value and
                              its editor as a second level, one submit per open surface;
                              SCR-MDL-002: none, and it submits nothing
UXD           orphans         every UXD is cited by a plan block — UXD-MDL-001 is cited by both
                              F2 SCREEN-INIT blocks, by both facades, by the F3 validator for
                              RULE-MDL-001, by both F4 Cross-module lines and by SEC-FE
TRACES        traces          every PHASE and SUB carries traces=; UXD-MDL-001 traces to its REQ
                              and its AC; every SCR traces to its REQ and its UXD
API           traces          every API this plan cites is defined in the fetched api-docs —
                              API-MDL-001..011, each carrying its own Contract ID line there
                              (ADR-MDL-011) — and never in the backend plan's contract summary.
                              No foreign module's API id is cited here at all
FOREIGN       xref-surface    every reference to another module's surface resolves in that
                              module's own artifacts. This plan writes no foreign path and no
                              foreign id: the one cross-module read is cited as UXD-MDL-001 and
                              named in ui-ux-spec-mdl.md, so this clause has no subject here
REGISTRY      registry-agree  the UXD and both SCR defined here are in registry-exec-fe-mdl.md,
                              and nothing else is
LANGUAGES     languages       labels and messages in ar + en
MARKERS       markers         the parser reports no structural or semantic error for this track
                              and plan
DECISIONS     refs-exist      every ADR this plan cites exists on disk in
                              analysis/decisions/MDL/ — ADR-MDL-002, ADR-MDL-003, ADR-MDL-004,
                              ADR-MDL-005, ADR-MDL-006, ADR-MDL-007, ADR-MDL-011, ADR-MDL-012,
                              ADR-MDL-013, and the two superseded ones they cite
COVERAGE      (the report)    none — the analyze report lists no clause as having examined
                              nothing. Two facts sit behind that word: every clause that counts
                              its subjects counted at least one here, and `xref-surface` reports
                              no count at all, so it appears in neither the coverage map nor that
                              list. It has no subject in this plan, by the decision the API row
                              above records.
RESULT        PASSED ✓ — 0 findings
```

### Operations coverage

| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| search lookup types | API-MDL-001 | SCR-MDL-001 · type search | `/reference-data/lookups` | ✓ |
| create lookup type | API-MDL-002 | SCR-MDL-001 · type entry, create | `/reference-data/lookups/new` | ✓ |
| update lookup type | API-MDL-003 | SCR-MDL-001 · type entry, edit | `/reference-data/lookups/:typeId/edit` | ✓ |
| deactivate lookup type | API-MDL-004 | SCR-MDL-001 · deactivate a type | `/reference-data/lookups/:typeId` | ✓ |
| search a type's values | API-MDL-005 | SCR-MDL-001 · the values pane | `/reference-data/lookups/:typeId` | ✓ |
| create lookup value | API-MDL-006 | SCR-MDL-001 · value entry, create | `/reference-data/lookups/:typeId/values/new` | ✓ |
| update lookup value | API-MDL-007 | SCR-MDL-001 · value entry, edit | `/reference-data/lookups/:typeId/values/:valueId/edit` | ✓ |
| deactivate lookup value | API-MDL-008 | SCR-MDL-001 · deactivate a value | `/reference-data/lookups/:typeId` | ✓ |
| reorder a type's values | API-MDL-009 | SCR-MDL-001 · drag to reorder | `/reference-data/lookups/:typeId` | ✓ |
| browse the registry by owner | API-MDL-010 | SCR-MDL-002 · the browse itself | `/reference-data/type-registry` | ✓ |
| read active values by key | API-MDL-011 | — a consuming module's backend call | — (ADR-MDL-007) | ✗ |
| read one type by id | — none published | hydrated from the search cache | — (ADR-MDL-005) | ✗ |
| read one value by id | — none published | hydrated from the search cache | — (ADR-MDL-005) | ✗ |

Ten of the eleven published endpoints carry a route and a ✓. Three rows carry a ✗ with the ADR
that explains it: one endpoint published for a caller that is not this frontend, and two
operations the SRS names for which nothing is published. No row is a ✗ for want of a decision,
and no row carries an empty route without one.
<!-- PHASE:ALIGN-FE:END -->

---

## Hand-off

The implementer reads the phases in profile order — F1 models, F2 hooks, F3 forms, F4 screens and
routes, SEC-FE guards — takes design intent from `ui-ux-spec-mdl.md`, and takes every request and
response shape from `_inputs/api-docs-mdl.md` at the commit this module version pins. No route,
component, permission or field that is not traceable to an F-block above is invented: a gap is an
ADR in `analysis/decisions/MDL/`, never an invention.

Three response shapes travel in this module and the difference is load-bearing: two paginated
reads, three bare arrays and six single objects. No block may be read through an envelope another
block declares.

The plan and its registry are split by the toolkit into the frontend execution package inside the
shared repo after the `gate:pass-2` verdict, and tagged `mdl-v1`. Nothing is copied anywhere: the
implementer reads it where it was written.
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-module-registry.md>>>
## MODULE REGISTRY — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module Code    : MDL   (profile.vocabulary.module_prefixes)
Bounded context: organization
Layer / Type   : L1 / reference hub     Execution tier : 1.3
Source         : NEW
Knowledge      : new project/lookup-module-plan-en.md; profiles/erp/knowledge/erp-domain-standards.md §2-§3
Readiness      : READY
══════════════════════════════════════════════════════════════════

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity (ar/en) | Kind (master / transactional / lookup / config / security) | PRIVATE / SHARED | Source |
|---|---|---|---|
| نوع اللوكب / LookupType | master | SHARED (owner) — every consuming module registers and reads its own types here | lookup-module-plan-en.md §2-§3 |
| قيمة اللوكب / LookupValue | lookup | SHARED (owner) — every consuming module reads/manages its own values here | lookup-module-plan-en.md §3 |

LOOKUPS OWNED    (value lists this module masters — MDL owns the MECHANISM only; see rule below)
| Lookup key | Description | Initial values (only those the user named) | Source |
None — MDL itself introduces no domain-specific coded list of its own; it is the generic
mechanism every OTHER module's lookup types run on top of. (MDL does not "consume itself.")
Rule (profile): all LOV values runtime-loaded from the lookup module; no hardcoded enums in APIs or field specs — this rule is MDL's own reason to exist.

LOOKUPS CONSUMED (from other modules)
| Lookup key | Owner code | READ-ONLY |
None.

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
| ModuleRegistry (ENT-SEC-004) | SEC | SOFT-READ | validate that a lookup type's declared owner module code is a real, registered platform module before accepting the registration — mirrors SEC's own RULE-SEC-004 pattern for screens, applied here to lookup types |

DEPENDENCIES
| Module code | HARD / SOFT / LOOKUP | What is consumed |
| SEC | SOFT | ModuleRegistry (owner-code validation only — see SHARED ENTITIES CONSUMED) |
ROOT: NO (has one SOFT dependency on SEC; still Tier 0 Foundation per KB §1 — a SOFT-READ
foundation dependency on another Tier-0 module does not change tiering)

AUTO-DECISIONS
AUTO: classified MDL's SEC dependency as SOFT-READ, not HARD-FK
  FROM: [KB:erp-domain-standards §5] "SOFT-READ: a read-only lookup by code — allowed in any direction"; a HARD-FK would make MDL's own schema depend physically on SEC's PK, which is heavier than the actual need (a one-time/per-write existence check)
  IF WRONG: promote to HARD-FK if a later requirement needs referential-integrity-level guarantees stronger than an application-level check — would need its own ADR.
AUTO: LookupType classified kind=master, LookupValue kind=lookup
  FROM: profiles/erp.yaml conventions.entity_defaults — LookupValue's fields (code, nameAr, nameEn, sortOrder, isActiveFl) match the `lookup` kind's default set exactly; LookupType (code, nameAr, nameEn, ownerModuleCode, isActiveFl) is closer to `master` (bilingual, coded, soft-deletable, not itself a coded VALUE of some other type)
  IF WRONG: none recommended — this is the plan's own vocabulary (§3 "a lookup is a type (master) + its values (detail)").
AUTO: tier/numbering 1.3 (Foundation, Tier 0)
  FROM: [KB:erp-domain-standards §1]
  IF WRONG: renumber if the platform later reprioritizes.

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
None — lookup-module-plan-en.md fully settles this module's P0 scope; no point required dialogue.

POLICIES OWNED (full text in business-policies-mdl.md)
POL-MDL-001, POL-MDL-002, POL-MDL-003, POL-MDL-004, POL-MDL-005, POL-MDL-006
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-platform-summary.md>>>
# PLATFORM SUMMARY — منصة تخطيط موارد المؤسسات (ERP Platform)
══════════════════════════════════════════════════════════════════
Profile : erp   Domain profile : v1   Registry : v1.1.0
══════════════════════════════════════════════════════════════════

## OVERVIEW
منصة ERP متعددة الوحدات، قائمة على مبدأ "كل ما يمكن أن يتغير = بيانات، لا شيفرة"،
تُبنى بـ Spring (خلفية) وReact (واجهة) على PostgreSQL كهدف بناء وحيد. الوحدة الأولى (SEC)
اكتملت (pass-1 APPROVE)؛ هذه الجلسة تُنشئ **الوحدة الثانية MDL** ثم تتبعها FIN.
[domain-profile §1-§2; project-registry PIPELINE/PROGRESS STATUS]

## MODULES
| #   | Code | Module (ar/en) | Bounded context | Layer | Type | Depends on | Status |
|-----|------|--------|-----------------|-------|------|------------|--------|
| 1.1 | ORG | الهيكل التنظيمي / Organization | organization | L1 | master data | ROOT | NEW (not this batch) |
| 1.2 | SEC | الأمان / Security | organization | L1 | security engine | ROOT | **EXCEPTION — pass-1 COMPLETE, read as-is** |
| 1.3 | MDL | البيانات المرجعية / Master Data Lookup | organization | L1 | reference | SEC (SOFT-READ, module validation) | NEW — this batch, second |
| 2.1 | PRC | المشتريات / Procurement | supply | L3 | transactional | SEC, MDL (not yet detailed) | NEW (not this batch) |
| 2.2 | FIN | الحسابات العامة / Finance (GL) | finance | L3 | transactional/reporting | SEC (HARD), MDL (HARD) | NEW (next, not this run) |
| 2.3 | INV | المخزون / Inventory | supply | L3 | transactional | SEC, MDL (not yet detailed) | NEW (not this batch) |
| 3.1 | SLS | المبيعات / Sales | commercial | L4 | transactional | SEC, MDL (not yet detailed) | NEW (not this batch) |
| 3.2 | CTR | العقود / Contracts | commercial | L4 | transactional | SEC, MDL (not yet detailed) | NEW (not this batch) |
| 3.3 | HR | الموارد البشرية / Human Resources | people | L4 | transactional | SEC, MDL (not yet detailed) | NEW (not this batch) |
Status: NEW (Phase 2 produces) · EXISTING (Phase 2 extends) · EXCEPTION (read as-is)
Numbering: [tier].[sequence within tier] — the user requests Phase 2 by this number.
This run's Phase 2 request: **1.3 MDL** (module convergence below).

## DEPENDENCY MAP
Build order: Tier 1 [ORG, SEC, MDL] → Tier 2 [PRC, FIN, INV] → Tier 3 [SLS, CTR, HR] → Tier 4 (reporting, none yet)
Key dependencies (one line each):
  MDL → SOFT-READ → SEC : validates a lookup type's owner module code against SEC's ModuleRegistry (new this run — identified during MDL's own convergence, not pre-listed in domain-profile §6, since it only becomes concrete once MDL's integration contract §4 is read against SEC's already-gated ModuleRegistry entity)
  FIN → HARD → SEC : identity + module/screen/action grants + SoD (unchanged, from SEC's own pass)
  FIN → HARD → MDL : payment methods, accounting event types, account types, period states, journal types (unchanged; MDL not yet built when this was first recorded — now concrete)

## DEFERRED (not in scope for this version)
| Item | Reason / activation trigger |
| ORG, PRC, HR, INV, SLS, CTR detailed analysis | user-scoped this batch to SEC → MDL → FIN only (GENERATION-INSTRUCTIONS.md §3) |
| Migrating SEC's USER_STATUS/SIGNUP_STATUS/AUDIT_EVENT_TYPE into MDL's shared lookup table | ADR-SEC-001 defers this to a SEC v2 delta version; out of scope for this batch's v1 passes |
| Workflow engine | profile: `forbidden` |
| Notifications / File Service redesign | ready external modules; consumed only on real need [lookup-module-plan-en.md §5] |

## RESOLVED DECISIONS (this phase)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | MDL → SEC dependency kind | SOFT-READ (validate module code only; no FK, no shared data ownership) | Yes — consistent with security-module-plan-en.md §7's "registers itself as data" pattern applied in reverse (MDL reads, doesn't own, SEC's registry) | [KB:erp-domain-standards §5 HARD-FK vs SOFT-READ] |
| 2 | Phase 2 request for this P0 run | Module 1.3 MDL (second of the batch order SEC→MDL→FIN) | Yes — GENERATION-INSTRUCTIONS.md §3 | GENERATION-INSTRUCTIONS.md §3 |

## OPEN ITEMS
None.

## NEXT STEP
Module 1.3 MDL converges below (module-registry-mdl.md, business-policies-mdl.md).
Reply with a plain instruction to adjust, or request module 2.2 (FIN) next per the
mandated order (FIN must wait until MDL's pass-1 gate as well, per GENERATION-
INSTRUCTIONS.md's dependency rule).

<<<END ARTIFACT>>>

<<<ARTIFACT: current-prd.md>>>
# PRD — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module          : MDL     Version : v1
Source artifacts: platform-summary, module-registry, business-policies
Stories         : 5   Policies covered : 6/6   Deferred : 0
Status          : DRAFT — awaiting prd-approval
══════════════════════════════════════════════════════════════════

## USER STORIES

US-MDL-001
  Title          : إدارة أنواع اللوكب / Manage lookup types
  Story          : As a platform administrator, I need to create, edit and deactivate a lookup type naming its owning module, so that every coded list has one authoritative, namespaced home.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-MDL-002, POL-MDL-003
  Source         : lookup-module-plan-en.md §2-§3
  Status         : DRAFT → APPROVED (by the PRD approval gate)

US-MDL-002
  Title          : إدارة قيم اللوكب عبر الشاشة العامة / Manage lookup values through the generic screen
  Story          : As a user authorized for a lookup type, I need one generic master-detail screen to pick the type and manage its values (code, bilingual labels, sort order, active flag), so that I never need a screen per list.
  Priority       : HIGH — plan names this the module's core mechanism (§3)
  Success metric : —
  Traces         : POL-MDL-004, POL-MDL-005, POL-MDL-006
  Source         : lookup-module-plan-en.md §3
  Status         : DRAFT → APPROVED (by the PRD approval gate)

US-MDL-003
  Title          : قراءة القيم من أي وحدة مستهلكة / Read values from any consuming module
  Story          : As a consuming module's backend, I need to read a lookup type's active values by key, so that I never hardcode a coded value list of my own.
  Priority       : HIGH — this is the module's entire reason to exist (§2)
  Success metric : —
  Traces         : POL-MDL-001
  Source         : lookup-module-plan-en.md §2, §4
  Status         : DRAFT → APPROVED (by the PRD approval gate)

US-MDL-004
  Title          : تسجيل نوع لوكب جديد كبيانات / Register a new lookup type as data
  Story          : As a consuming module's integrator, I need to register my own lookup type here as data (naming my module as owner), so that adding a new list never requires touching MDL's code.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-MDL-002, POL-MDL-003
  Source         : lookup-module-plan-en.md §4
  Status         : DRAFT → APPROVED (by the PRD approval gate)

US-MDL-005
  Title          : سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner
  Story          : As a platform administrator, I need to browse lookup types grouped by their owning module, so that I can find and audit a module's reference lists at a glance.
  Priority       : MEDIUM
  Success metric : —
  Traces         : POL-MDL-002
  Source         : lookup-module-plan-en.md §7 (screen inventory row 2)
  Status         : DRAFT → APPROVED (by the PRD approval gate)

## TRACEABILITY — story → policy
| US | Traces (POL) | Source |
|---|---|---|
| US-MDL-001 | POL-MDL-002, POL-MDL-003 | lookup-module-plan-en.md §2-§3 |
| US-MDL-002 | POL-MDL-004, POL-MDL-005, POL-MDL-006 | lookup-module-plan-en.md §3 |
| US-MDL-003 | POL-MDL-001 | lookup-module-plan-en.md §2, §4 |
| US-MDL-004 | POL-MDL-002, POL-MDL-003 | lookup-module-plan-en.md §4 |
| US-MDL-005 | POL-MDL-002 | lookup-module-plan-en.md §7 |
Every policy POL-MDL-001 … POL-MDL-006 appears in at least one row above.

## RESOLVED DECISIONS (dialogue)
| # | Question | Recommended | Confirmed by user | Sources |
None — lookup-module-plan-en.md left no story's scope, priority or role genuinely
ambiguous; no dialogue question was required.

## DEFERRED
| US | Reason | Activation trigger |
None — every capability named in lookup-module-plan-en.md is represented by a story in
this v1 PRD.

## APPROVAL
Approved by : PENDING   Date : PENDING
Once approved, no stage may raise a question; P1 onward self-resolve
per the ambiguity rule (shared/GOVERNANCE-CORE.md).
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-registry-db.md>>>
## REGISTRY — P2 — MDL v1
══════════════════════════════════════════════════════════════════

Tables
| Table | ENT id | Kind | DBF range |
|---|---|---|---|
| MDL_LOOKUP_TYPE | ENT-MDL-001 | master | DBF-MDL-001 … DBF-MDL-010 |
| MDL_LOOKUP_VALUE | ENT-MDL-002 | lookup | DBF-MDL-011 … DBF-MDL-021 |

Both names are the ones the platform structural registry already fixed for this module
(project-registry STRUCTURAL / IMPLEMENTATION REGISTRY, row MDL v1); neither is new here.

DB sequences (one per table, profile.stack.db.naming.sequence_pattern — carried into the
backend plan by P3.1): SEQ_MDL_LOOKUP_TYPE, SEQ_MDL_LOOKUP_VALUE. Every PK column is a plain
BIGINT NOT NULL fed from its sequence by the application — no identity clause anywhere.

DBF ids (full detail in db-script-mdl.md → §1): DBF-MDL-001, DBF-MDL-002, DBF-MDL-003,
DBF-MDL-004, DBF-MDL-005, DBF-MDL-006, DBF-MDL-007, DBF-MDL-008, DBF-MDL-009, DBF-MDL-010,
DBF-MDL-011, DBF-MDL-012, DBF-MDL-013, DBF-MDL-014, DBF-MDL-015, DBF-MDL-016, DBF-MDL-017,
DBF-MDL-018, DBF-MDL-019, DBF-MDL-020, DBF-MDL-021

XM index
| XM id | Type | From | To | Consumes | Status |
|---|---|---|---|---|---|
| XM-MDL-001 | SOFT-READ | MDL | SEC | ModuleRegistry (ENT-SEC-004, table SEC_MODULE_REG) — existence of a lookup type owner module code, RULE-MDL-001 | ACTIVE |

Lookups
| Key | Seeded values count | Owner |
|---|---|---|
| — | 0 | — |
MDL owns no lookup key and consumes none (SRS A6): it is the mechanism every other module
runs its coded lists on. No seed block exists in this script and none is owed to another
module's table; a consuming module registers and seeds its own keys through this module's
API (API-MDL-002, API-MDL-006).

Sequences
Last DBF: DBF-MDL-021 · Last XM: XM-MDL-001

Decisions
ADR-MDL-009 (ACCEPTED, non-breaking) — index strategy for this module's two tables.
ADR-MDL-010 (ACCEPTED, non-breaking) — string precisions for the six unsized text columns.
The ADR sequence continues from ADR-MDL-008 (raised at P3.2); nothing is renumbered, and no
decision is BLOCKED — the pass completed.

Cascade
No registry XM row anywhere in the platform targets MDL with status DEFERRED, so there is
nothing to resolve: the one inbound row, XM-FIN-001 (FIN → MDL), is a SOFT-READ already
recorded ACTIVE, and this script now gives it the structure it was already written against
(MDL_LOOKUP_TYPE, MDL_LOOKUP_VALUE, unchanged names and ranges). Outbound, XM-MDL-001
resolves to ACTIVE immediately: SEC v1 is gated (pass-1 APPROVE) and SEC_MODULE_REG exists.

Event
"P2 completed: MDL v1 — 2 tables, 21 DBF, 1 XM"
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-registry-exec-be.md>>>
## REGISTRY — P3.1 — MDL v1
══════════════════════════════════════════════════════════════════
Module : MDL (البيانات المرجعية / Master Data Lookup)   Version : v1   Profile : erp
Source : analysis/modules/MDL/P3_1/backend-execution-plan-mdl.md
══════════════════════════════════════════════════════════════════

ID RANGES
API-MDL-001 .. API-MDL-011 · QR-MDL-001 .. QR-MDL-017
Neither sequence restarts and nothing is renumbered: the API range and QR-MDL-001..015 continue
the assignment this module version already carries, and every downstream artifact that cites one
of them still resolves to the same operation. QR-MDL-016 and QR-MDL-017 are the only ids minted
at this run — the two sequence allocations the `sequence` PK strategy makes explicit.

API ids: API-MDL-001, API-MDL-002, API-MDL-003, API-MDL-004, API-MDL-005, API-MDL-006,
API-MDL-007, API-MDL-008, API-MDL-009, API-MDL-010, API-MDL-011

QR ids: QR-MDL-001, QR-MDL-002, QR-MDL-003, QR-MDL-004, QR-MDL-005, QR-MDL-006, QR-MDL-007,
QR-MDL-008, QR-MDL-009, QR-MDL-010, QR-MDL-011, QR-MDL-012, QR-MDL-013, QR-MDL-014,
QR-MDL-015, QR-MDL-016, QR-MDL-017

ENTITIES / TABLES bound
| ENT | Table | PK generation | Business code |
|---|---|---|---|
| ENT-MDL-001 | MDL_LOOKUP_TYPE | sequence SEQ_MDL_LOOKUP_TYPE | none — `key` is the registrar's business key |
| ENT-MDL-002 | MDL_LOOKUP_VALUE | sequence SEQ_MDL_LOOKUP_VALUE | none — `code`, unique within its type |

All 21 DBF ids (DBF-MDL-001 … DBF-MDL-021) are bound in the DB Alignment Manifest, status ✓ on
every row; no field is PENDING and no entity is PENDING DB. The PK generation recorded here is
the profile's `sequence`, which corrects the `IDENTITY` both entity blocks carried before this
run (db-script §4.2).

Lookups reused / new: none — MDL owns no lookup key and consumes none (SRS A6). It is the store
every other module's keys live in; each of those is registered and seeded by its owning module
through API-MDL-002 and API-MDL-006, never by this module and never by another module's script.

XM STATUS
open: none · deferred: none · active: 1 — XM-MDL-001 (SOFT-READ, MDL → SEC, ENT-SEC-004 /
SEC_MODULE_REG), placed in INT-C and resolved READY in INT-R. The db-script register declares
exactly this row and this stage minted none, so nothing is owed back to the register. The access
is an injected in-process interface (`profile.conventions.module_interface: in_process`), never
an HTTP call. One item is owed outside this module and is recorded, not asserted: SEC's own P3.1
artifacts register only `SecUserDirectoryApi` as an exposed cross-module surface, so the
module-registry read this row consumes is not yet written down on SEC's side — SEC's artifact to
correct on its own re-run.
Inbound: XM-FIN-001 (FIN → MDL, SOFT-READ, ACTIVE) consumes API-MDL-011; the id belongs to FIN.

CATALOG
10 error-catalog rows — 7 module codes, every one an instance of `{MOD}-{http}[-{SLUG}]` on a
status `profile.stack.backend.api.http_statuses` declares, and 3 platform rows carrying the
shared handler's own code strings (ACCESS_DENIED, VALIDATION_ERROR, INTERNAL_ERROR), which this
module does not mint and must not. Rule-backed: RULE-MDL-001 → MDL-409-MODULE-NOT-REGISTERED,
RULE-MDL-002 → MDL-409-VALUE-DUP, RULE-MDL-004 → MDL-404-TYPE-KEY. PLATFORM-STD under the
ADR-SEC-002 umbrella: MDL-409-TYPE-DUP, MDL-404-TYPE, MDL-404-VALUE, MDL-400-REORDER-MISMATCH
and the three platform rows.
Rules without a message: none — every RULE this plan enforces carries its ar and en text
character-perfect from the SRS. RULE-MDL-003 carries no row by construction: the key is absent
from the update request, so no code path can raise one.
Struck this run: the 503 row the previous revision carried for a network failure that cannot
occur in a single deployable, on a status the platform does not declare.

SECURITY
2 composite screens × 3 roles · 5 permissions:
PERM_MDL_LOOKUPS_VIEW (gateway), PERM_MDL_LOOKUPS_CREATE, PERM_MDL_LOOKUPS_UPDATE,
PERM_MDL_LOOKUPS_DELETE on page MDL_LOOKUPS; PERM_MDL_TYPE_REGISTRY_VIEW on page
MDL_TYPE_REGISTRY — each with a BOOTSTRAP DATA row naming its grant target, because registering
a page and minting a name is not a grant.

ALIGN
Verdict as stamped in backend-execution-plan-mdl.md → ALIGN → the verdict line (written by the
orchestrator from the analyze report). Findings fixed during the run: four statements of the
previous revision that described a system nobody built — the IDENTITY PK generation, the three
`GET` read verbs, the REST-call cross-module mechanism and the unraisable 503 catalog row. None
outstanding.

ADRs
No ADR raised at this stage. Applied, not re-assigned: ADR-MDL-002 and ADR-MDL-007 (P3.2),
ADR-MDL-009 and ADR-MDL-010 (P2), and the two platform precedents ADR-SEC-002 and ADR-FIN-001.
Every ADR-MDL-* this plan cites exists in analysis/decisions/MDL/. No BLOCKED ADR — the pass was
not stopped. No question was raised.

TRACEABILITY
REQ covered by ≥1 API/DBF: 13/13 — REQ-MDL-001 … REQ-MDL-013, each named by at least one API
block's traces and by the DBF records of db-script-mdl.md. Orphan REQ: none.
QR reached by ≥1 API: 17/17. Entity operations resolved to an API: 8/8. Screen operations the
SRS names, resolved to an API: 8/8 (search, create, read, update, deactivate, reorder on
SCR-REQ-MDL-001; search, browse on SCR-REQ-MDL-002) — none excluded and none deferred.

Event
"P3.1 completed: MDL v1 — 11 API, 17 QR, 1 XM, 10 catalog rows, 0 new ADRs"
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-registry-exec-fe.md>>>
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

<<<END ARTIFACT>>>

<<<ARTIFACT: current-registry-srs.md>>>
## REGISTRY — P1 — MDL v1
══════════════════════════════════════════════════════════════════
Module : MDL (البيانات المرجعية / Master Data Lookup)   Version : v1   Profile : erp
Source : analysis/modules/MDL/P1/srs-mdl.md
══════════════════════════════════════════════════════════════════

### Entities
| ENT id | Name (ar / en) | Kind | PRIVATE / SHARED | Status |
|---|---|---|---|---|
| ENT-MDL-001 | نوع اللوكب / LookupType | master | SHARED (owner) | REGISTERED |
| ENT-MDL-002 | قيمة اللوكب / LookupValue | lookup | SHARED (owner) | REGISTERED |

المعرّفان مُعاد استعمالهما كما هما من project-registry ENTITY OWNERSHIP — لا كيان جديد
ولا إعادة ترقيم (engine §1.1).

### Consumed (shared entities of other modules)
| Owner ENT id | Owner module | HARD-FK / SOFT-READ | Consumes |
|---|---|---|---|
| ENT-SEC-004 | SEC | SOFT-READ (no FK) | وجود رمز الوحدة المالكة عند إنشاء نوع لوكب — RULE-MDL-001 / سجل الوحدات / ModuleRegistry existence check |
مرشَّح XM واحد إلى SEC؛ المعرّف من إسناد P2. لا مفتاح أجنبي عابرًا للوحدات
(سابقة ADR-FIN-001 — project-registry DECISION INDEX #9).

### Lookups owned
| Key | ENT | Values count |
|---|---|---|
| — | — | 0 — الوحدة هي آلية القوائم ولا تملك قائمة قيم خاصة بها / the module owns the mechanism, not a list |

### Lookups consumed
| Key | Owner |
|---|---|
| — | — لا قيمة مُرمَّزة تُقرأ من أي وحدة / none consumed |

### Screens
| SCR-REQ id | Name (ar / en) | Page code |
|---|---|---|
| SCR-REQ-MDL-001 | اللوكبات العامة / Generic Lookups | MDL_LOOKUPS |
| SCR-REQ-MDL-002 | سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner | MDL_TYPE_REGISTRY |

### Requirements
| Atom | Count | Last sequence |
|---|---|---|
| REQ | 13 | REQ: 13 |
| AC | 13 | AC: 13 |
| ENT | 2 | ENT: 2 |
| RULE | 4 | RULE: 4 |
| SCR-REQ | 2 | SCR-REQ: 2 |

Requirements registered (REQ-MDL-001 … REQ-MDL-013):
REQ-MDL-001, REQ-MDL-002, REQ-MDL-003, REQ-MDL-004, REQ-MDL-005, REQ-MDL-006,
REQ-MDL-007, REQ-MDL-008, REQ-MDL-009, REQ-MDL-010, REQ-MDL-011, REQ-MDL-012,
REQ-MDL-013

Acceptance criteria registered (AC-MDL-001 … AC-MDL-013):
AC-MDL-001, AC-MDL-002, AC-MDL-003, AC-MDL-004, AC-MDL-005, AC-MDL-006,
AC-MDL-007, AC-MDL-008, AC-MDL-009, AC-MDL-010, AC-MDL-011, AC-MDL-012,
AC-MDL-013

Business rules registered (RULE-MDL-001 … RULE-MDL-004):
RULE-MDL-001, RULE-MDL-002, RULE-MDL-003, RULE-MDL-004

### Decisions
| ADR id | Subject | Status |
|---|---|---|
| — | لا قرار جديد في P1 / no ADR raised at P1 | — |
سابقتان مطبَّقتان لا مُنشأتان: ADR-SEC-002 (كتالوج الأخطاء البنيوية تحت مظلة PLATFORM-STD)
وADR-FIN-001 (تبعية الأمان قراءة تطبيقية لا مفتاحًا أجنبيًا) — project-registry
DECISION INDEX #8, #9. لا قرار بحالة BLOCKED / no BLOCKED ADR — the pass completed.

### Event
"P1 completed: MDL v1 — 2 entities, 13 requirements, 13 AC, 4 rules, 2 screen requirements, 0 ADR"
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-srs.md>>>
# SRS — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module : MDL   Version : v1   Profile : erp
Inputs : prd, domain-profile, project-registry (PRD approved 2026-09-10 — gate `prd-approval`)
Counts : REQ 13 · AC 13 · ENT 2 · RULE 4 · SCR-REQ 2 · ADR 0
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information

| Item | Value |
|---|---|
| الوحدة / Module | البيانات المرجعية / Master Data Lookup |
| رمز الوحدة / Module code | MDL |
| السياق المحدود / Bounded context | organization |
| النسخة / Version | v1 |
| التاريخ / Date | 2026-09-19 |
| الحالة / Status | COMPLETE — pass P1, round 1 |
| أعدّها / Prepared by | P1 (SRS engine), lane `analysis` |
| القرارات المطبَّقة / Decisions applied | 0 ADR + 6 DEFAULT + سابقتان منصّيتان (see STANDALONE) |
| المدخلات / Inputs | prd-mdl.md (APPROVED), domain-profile.md v1, project-registry.md v1.3.0, module-registry-mdl.md, business-policies-mdl.md |

## A2 — Functional context

**داخل النطاق / In scope**
كيانان اثنان (نوع اللوكب وقيمته) · تسجيل نوع لوكب باسم وحدة مالكة · إدارة قيم أي نوع عبر
شاشة عامة واحدة رئيسي-تفصيلي · قراءة القيم الفعّالة بالمفتاح من أي وحدة مستهلكة ·
تصفّح سجل الأنواع مجمّعًا حسب الوحدة المالكة · التعطيل الناعم على المستويين.

**خارج النطاق / Out of scope**
شاشة مخصَّصة لقائمة بعينها (POL-MDL-004) · قيم لوكب هرمية أو شجرية — القائمة مسطّحة
(business-policies SCOPE EXCEPTIONS) · صلاحية دقيقة لكل نوع لوكب على حدة، فالحدّ هو منح
الشاشة (business-policies SCOPE EXCEPTIONS) · قائمة قيم مُرمَّزة تملكها MDL لنفسها —
الوحدة هي الآلية لا صاحبة القوائم (module-registry-mdl.md LOOKUPS OWNED) · إعادة تفعيل
نوع أو قيمة معطَّلة: لا قصة معتمدة تطلبها · محرك سير عمل (domain-profile G12).

**وظيفة الوحدة / Module function**
تُعطي الوحدة المنصّةَ مخزنًا واحدًا لكل قائمة قيم مُرمَّزة: تُسجِّل كل وحدة أنواعها هنا
كبيانات لا كشيفرة، وتبقى الملكية الدلالية للوحدة المسجِّلة عبر رمزها المخزَّن مع النوع،
ويقرأ الجميع القيم الفعّالة بمفتاح النوع. إضافة قائمة جديدة لا تمسّ شيفرة MDL ولا تُنشئ
شاشة جديدة (POL-MDL-001, POL-MDL-002, POL-MDL-004).

**الوصف التفصيلي / Detailed description**
مُكامِل الوحدة يُسجّل نوع لوكب بمفتاح فريد على مستوى المنصة، ورمز وحدته المالكة، واسمين
عربي وإنجليزي؛ يرفض النظام التسجيل إن لم يكن للرمز صف في سجل وحدات الأمان
(POL-MDL-003). منسّق المنصة — أو مَن مُنح الشاشة من مديري قوائم الوحدة المالكة — يفتح
الشاشة العامة، يختار النوع من القائمة الرئيسية، فتُعرض قيمه في الجزء التفصيلي: يضيف قيمة
برمز وتسميتين ورتبة، يُنقّح تسمياتها ورتبتها، يُعيد ترتيب القيم، يُعطّل ما لم يعد مطلوبًا.
الوحدة المستهلكة لا تفتح شاشة إطلاقًا: تطلب قيم النوع بمفتاحه فتصلها القيم الفعّالة وحدها
مرتَّبة، وإن كان النوع نفسه معطَّلًا لم تصلها قيمة. الأدوار ثلاثة: منسّق المنصة، مدير قوائم
الوحدة المالكة، والحساب الخدمي للوحدة المستهلكة الذي لا يملك غير القراءة.

**الوضع الحالي / Current situation**
| الخطوة | الجهة | ملاحظة |
|---|---|---|
| تعريف قائمة قيم مُرمَّزة | كل وحدة على حدة | جدول lookup محلي أو ثابت مكتوب في الشيفرة |
| تعديل قيمة | مطوّر الوحدة | تغيير شيفرة ونشر، لا تغيير بيانات |
| مشاركة القائمة مع وحدة أخرى | نسخ يدوي | نسختان تفترقان مع أول تعديل |

**الصعوبات الحالية / Current difficulties**
انحراف القيم المرجعية بين الوحدات (reference-data drift): القيمة الواحدة لها تمثيلان
متضاربان أو أكثر، وكل قائمة جديدة تكلّف شاشة وشيفرة، ولا موضع واحد يُسأل عن المعنى.

**النظام المقترح ومنافعه / Proposed system and benefits**
مركز واحد على نمط MDM hub (domain-profile §9 R2): تخزين وشاشة مركزيان، وملكية دلالية
موزّعة بالتسمية (namespacing) على الوحدة المسجِّلة (G4, G5). إضافة قائمة صارت تسجيل
بيانات، والقيمة الواحدة صار لها مصدر واحد موثوق، والقراءة موحّدة بمفتاح واحد لكل مستهلك.

**ملاحظات عامة / General notes**
لا ترقيم مستندات: لا كيان من كياني الوحدة مستندٌ مُرقَّم ولا يُستخدم معرّفه خارج النظام
(§3.3 NUMBERING) · التعطيل ناعم بـ`isActiveFl` ولا حذف نهائي [KB:erp-domain-standards §6] ·
`key` في نوع اللوكب يؤدّي دور `code` في حقول النوع `master` القياسية (A3) · الوحدة لا
تستهلك من MDL شيئًا، فهي MDL نفسها.

## A3 — Entities and fields

**الحقول القياسية لكل نوع كيان (من الملف الشخصي) / Standard fields per entity kind**

| Kind | Default fields |
|---|---|
| master | nameAr, nameEn, code, isActiveFl, createdBy, createdAt, updatedBy, updatedAt |
| transactional | docNo, docDate, statusCode, fiscalYearId, periodId, createdBy, createdAt, updatedBy, updatedAt |
| lookup | code, nameAr, nameEn, sortOrder, isActiveFl |
| config | key, valueAr, valueEn, isActiveFl |

تُكتب مرة واحدة هنا ولا تُعاد لكل كيان. المفتاح الأساسي `{entity}Pk`؛ حقول العلم تنتهي بـ`Fl`؛
حقول التدقيق `createdBy, createdAt, updatedBy, updatedAt` يملؤها النظام ولا تُقبل من العميل
أبدًا. الأنواع هنا منطقية فقط — الأنواع الفيزيائية من إنتاج P2.

### ENT-MDL-001 — نوع اللوكب / LookupType

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master | SHARED (owner) — كل وحدة تُسجّل أنواعها هنا وتقرأها (project-registry SHARED ENTITY DECLARATIONS) | **لا / no** — المعرّف لا يُستخدم خارج النظام، ولا سياسة ولا قصة تطلب مرجعًا مقروءًا، وليس مستندًا مُرقَّمًا (§3.3 NUMBERING)؛ `key` مفتاح عمل يكتبه المُسجِّل لا رقم يولّده النظام | create · read · search · update · deactivate | `ownerModuleCode` يُتحقَّق من وجوده في سجل وحدات الأمان ENT-SEC-004 (SOFT-READ، بلا مفتاح أجنبي — A8) | module-registry-mdl.md ENTITIES OWNED; POL-MDL-002, POL-MDL-003 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| lookupTypePk | reference | yes | مولَّد من النظام / system-generated | المفتاح الأساسي، لا يُعرض كمرجع عمل | معرّف نوع اللوكب | LookupType id |
| key | text | yes | يكتبه المُسجِّل / supplied by the registrar | فريد على مستوى المنصة، وغير قابل للتعديل بعد الإنشاء — RULE-MDL-003؛ هو عقد القراءة لكل مستهلك (REQ-MDL-011) | المفتاح | Key |
| ownerModuleCode | text | yes | رمز وحدة مسجَّلة في ENT-SEC-004 / a module code registered in SEC | إلزامي عند الإنشاء — RULE-MDL-001؛ لا يُعدَّل بعد الإنشاء | رمز الوحدة المالكة | Owner module code |
| nameAr | text | yes | يكتبه المُسجِّل | الاسم العربي للنوع | الاسم (عربي) | Name (Arabic) |
| nameEn | text | yes | يكتبه المُسجِّل | الاسم الإنجليزي للنوع | الاسم (إنجليزي) | Name (English) |
| isActiveFl | flag | yes | `true` عند الإنشاء / `true` on creation | التعطيل الناعم — RULE-MDL-004 | نشط | Active |
| createdBy | text | yes | نظام / system | معرّف الأصل المُصادَق | أنشأه | Created by |
| createdAt | date-time | yes | نظام / system | UTC مخزَّنة، تُعرض بتوقيت المستأجر [KB:erp-domain-standards §6] | تاريخ الإنشاء | Created at |
| updatedBy | text | no | نظام / system | يُملأ عند أول تعديل | عدّله | Updated by |
| updatedAt | date-time | no | نظام / system | يُملأ عند أول تعديل | تاريخ التعديل | Updated at |

**ملاحظة على حقول `master` القياسية / note on the master default set** — الكيان يحمل
`nameAr`، `nameEn`، `isActiveFl` وحقول التدقيق الأربعة كاملةً؛ ودور `code` يؤدّيه `key`
باسمه الوارد في مفردات الوحدة (domain-profile §7.1 "نوع اللوكب") وفي module-registry-mdl.md،
لا حقلًا ثانيًا باسم آخر. و`ownerModuleCode` إضافة فوق المجموعة القياسية تفرضها قاعدة
التسمية بالمالك (POL-MDL-002).

### ENT-MDL-002 — قيمة اللوكب / LookupValue

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| lookup | SHARED (owner) — كل وحدة تقرأ قيمها الفعّالة هنا (project-registry SHARED ENTITY DECLARATIONS) | **لا / no** — القيمة تُعرَّف برمزها ضمن نوعها، ولا مرجع لها خارج النظام (§3.3 NUMBERING) | create · read · search · update · deactivate · reorder | لا شيء — الكيان داخلي التبعية، يرتبط بنوعه داخل الوحدة | module-registry-mdl.md ENTITIES OWNED; POL-MDL-005, POL-MDL-006 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| lookupValuePk | reference | yes | مولَّد من النظام / system-generated | المفتاح الأساسي | معرّف القيمة | LookupValue id |
| lookupTypeId | reference | yes | ENT-MDL-001 | النوع الذي تنتمي إليه القيمة — يُحدَّد باختيار النوع لا بكتابته | نوع اللوكب | Lookup type |
| code | text | yes | يكتبه المستخدم / supplied by the user | فريد ضمن النوع الواحد — RULE-MDL-002؛ هو ما تخزّنه الوحدات المستهلكة عندها | الرمز | Code |
| nameAr | text | yes | يكتبه المستخدم | التسمية العربية المعروضة | الاسم (عربي) | Name (Arabic) |
| nameEn | text | yes | يكتبه المستخدم | التسمية الإنجليزية المعروضة | الاسم (إنجليزي) | Name (English) |
| sortOrder | number | yes | `0` افتراضًا / `0` by default | ترتيب العرض تصاعديًا — REQ-MDL-010 | الترتيب | Sort order |
| isActiveFl | flag | yes | `true` عند الإنشاء / `true` on creation | التعطيل الناعم؛ القيمة المعطَّلة تغيب عن قراءة المستهلك (REQ-MDL-011) | نشط | Active |
| createdBy | text | yes | نظام / system | معرّف الأصل المُصادَق | أنشأها | Created by |
| createdAt | date-time | yes | نظام / system | UTC مخزَّنة [KB:erp-domain-standards §6] | تاريخ الإنشاء | Created at |
| updatedBy | text | no | نظام / system | يُملأ عند أول تعديل | عدّلها | Updated by |
| updatedAt | date-time | no | نظام / system | يُملأ عند أول تعديل | تاريخ التعديل | Updated at |

**إضافة فوق حقول `lookup` القياسية / addition beyond the lookup default set** — حقول
التدقيق الأربعة محمولة على هذا الكيان أيضًا رغم غيابها عن مجموعة النوع `lookup`
القياسية: القيمة هنا بيانات يحرّرها مستخدم عبر شاشة، لا صفٌّ ثابت يُزرع مرة واحدة،
فأثر التدقيق واجب عليها [KB:erp-domain-standards §6 audit trail].

## A4 — Functional requirements (EARS) and acceptance criteria

### REQ-MDL-001 — تسجيل نوع لوكب / Register a lookup type
  Pattern    : event
  Statement  : When a registrar submits a new lookup type carrying a key, an owner module code and Arabic and English names, the system shall store the lookup type with that key unique across the platform and shall mark it active.
  بالعربية   : عند إرسال المُسجِّل نوع لوكب جديد يحمل مفتاحًا ورمز وحدة مالكة واسمين عربي وإنجليزي، يخزّن النظام النوع بمفتاح فريد على مستوى المنصة ويجعله فعّالًا.
  Traces     : US-MDL-001, US-MDL-004
  Entities   : ENT-MDL-001
  Rationale  : إضافة قائمة جديدة تسجيلُ بيانات لا تعديلُ شيفرة (POL-MDL-001, POL-MDL-002)
  Source     : US-MDL-001; US-MDL-004; POL-MDL-002; lookup-module-plan-en.md §3-§4
  Priority   : HIGH

#### AC-MDL-001 — المسار السعيد لتسجيل النوع / register a type, happy path
  Traces : REQ-MDL-001
  Given  : منسّق ممنوح إجراء CREATE على شاشة اللوكبات العامة، ولا نوع على المنصة بالمفتاح `PAYMENT_METHOD`
  When   : يحفظ نوعًا مفتاحه `PAYMENT_METHOD` ووحدته المالكة `FIN` واسماه «طريقة الدفع» / "Payment method"
  Then   : يُخزَّن النوع بـ`key = PAYMENT_METHOD` و`ownerModuleCode = FIN` و`isActiveFl = true`، ويحمل `createdBy` و`createdAt` من النظام، وتظهر رسالة — ar: «تم حفظ نوع اللوكب.» · en: "The lookup type has been saved."

### REQ-MDL-002 — رفض وحدة مالكة غير مسجَّلة / Reject an unregistered owner module
  Pattern    : unwanted
  Statement  : If a lookup type registration names an owner module code that has no module registry row in the security module, then the system shall reject the registration and shall store no lookup type.
  بالعربية   : إذا سمّى تسجيلُ نوع لوكب رمزَ وحدة مالكة لا صفّ لها في سجل وحدات الأمان، فيرفض النظام التسجيل ولا يخزّن أي نوع.
  Traces     : US-MDL-001, US-MDL-004
  Entities   : ENT-MDL-001
  Rationale  : سلامة مرجعية — لا نوع بلا مالك حقيقي (POL-MDL-003)، وبغير الفحص تصير التسمية بالمالك حبرًا
  Source     : US-MDL-001; US-MDL-004; POL-MDL-003; module-registry-mdl.md SHARED ENTITIES CONSUMED
  Priority   : HIGH

#### AC-MDL-002 — رمز وحدة غير مسجَّل / an unregistered module code
  Traces : REQ-MDL-002
  Given  : منسّق في نموذج نوع جديد، والرمز `XYZ` بلا صفّ في سجل وحدات الأمان (ENT-SEC-004)
  When   : يحفظ نوعًا مفتاحه `SHIPPING_MODE` ووحدته المالكة `XYZ`
  Then   : يُرفض الحفظ برسالة RULE-MDL-001 — ar: «الوحدة المالكة غير مسجّلة في وحدة الأمان» · en: "The owning module is not registered in the Security module" — ولا يُخزَّن صفّ نوع البتة

### REQ-MDL-003 — إعادة تسمية نوع لوكب / Rename a lookup type
  Pattern    : event
  Statement  : When a registrar submits revised Arabic and English names for an existing lookup type, the system shall update that type's stored names with the submitted ones.
  بالعربية   : عند إرسال المُسجِّل اسمين عربي وإنجليزي مُنقّحين لنوع لوكب قائم، يحدّث النظام اسمَي النوع المخزَّنين بالمُرسَلين.
  Traces     : US-MDL-001
  Entities   : ENT-MDL-001
  Rationale  : التسمية تتغيّر، والمفتاح عقدٌ لا يتغيّر (RULE-MDL-003)
  Source     : US-MDL-001; lookup-module-plan-en.md §3
  Priority   : HIGH

#### AC-MDL-003 — تعديل الاسمين دون المفتاح / names change, key does not
  Traces : REQ-MDL-003
  Given  : نوع لوكب قائم مفتاحه `PAYMENT_METHOD` واسماه «طريقة الدفع» / "Payment method"
  When   : يُرسل المنسّق الاسمين «وسيلة الدفع» / "Payment means"
  Then   : يُحدَّث `nameAr` و`nameEn` بالقيمتين المُرسلتين، ويبقى `key = PAYMENT_METHOD` كما هو (RULE-MDL-003)، ويُملأ `updatedBy` و`updatedAt`

### REQ-MDL-004 — تعطيل نوع لوكب / Deactivate a lookup type
  Pattern    : event
  Statement  : When a registrar deactivates a lookup type, the system shall mark that lookup type inactive.
  بالعربية   : عند تعطيل المُسجِّل نوعَ لوكب، يجعل النظام النوع غير فعّال.
  Traces     : US-MDL-001
  Entities   : ENT-MDL-001
  Rationale  : إخراج قائمة من الخدمة تعطيلٌ ناعم لا محو — الصفوف تبقى وأثر الاستهلاك محفوظ
  Source     : US-MDL-001; DEFAULT [KB:erp-domain-standards §6 soft delete]
  Priority   : HIGH

#### AC-MDL-004 — أثر تعطيل النوع / the effect of deactivating a type
  Traces : REQ-MDL-004
  Given  : نوع فعّال مفتاحه `PAYMENT_METHOD` له ثلاث قيم فعّالة
  When   : يُعطّله المنسّق
  Then   : يصير `isActiveFl = false`، وتبقى قيمه الثلاث مخزَّنة كما هي، ولا تُعاد أيٌّ منها في قراءة المستهلك (RULE-MDL-004)

### REQ-MDL-005 — عرض قيم النوع المختار / List the selected type's values
  Pattern    : event
  Statement  : When a user selects a lookup type on the generic lookup screen, the system shall list that type's values ordered ascending by sort order.
  بالعربية   : عند اختيار المستخدم نوعَ لوكب على الشاشة العامة، يعرض النظام قيم ذلك النوع مرتَّبةً تصاعديًا بالترتيب.
  Traces     : US-MDL-002
  Entities   : ENT-MDL-001, ENT-MDL-002
  Rationale  : الشاشة العامة رئيسي-تفصيلي: اختيار النوع هو ما يحصر التفصيل (POL-MDL-004)
  Source     : US-MDL-002; POL-MDL-004; lookup-module-plan-en.md §3
  Priority   : HIGH

#### AC-MDL-005 — التفصيل محصور بالنوع المختار / the detail is confined to the selected type
  Traces : REQ-MDL-005
  Given  : النوع `PAYMENT_METHOD` له أربع قيم برُتب 1 و2 و3 و4، إحداها معطَّلة، ونوع آخر له قيمتان
  When   : يختار المستخدم `PAYMENT_METHOD` في القائمة الرئيسية للشاشة
  Then   : تُعرض قيم `PAYMENT_METHOD` الأربع كلها — الفعّالة والمعطَّلة — مرتَّبة تصاعديًا بـ`sortOrder`، ولا تظهر قيمة من النوع الآخر

### REQ-MDL-006 — إضافة قيمة لوكب / Add a lookup value
  Pattern    : event
  Statement  : When a user submits a new value under a selected lookup type carrying a code, Arabic and English labels and a sort order, the system shall store the value under that type and shall mark it active.
  بالعربية   : عند إرسال المستخدم قيمةً جديدة تحت نوع مختار تحمل رمزًا وتسميتين عربية وإنجليزية ورتبةً، يخزّن النظام القيمة تحت ذلك النوع ويجعلها فعّالة.
  Traces     : US-MDL-002
  Entities   : ENT-MDL-002
  Rationale  : الحقول القياسية لقيمة اللوكب منصوص عليها (POL-MDL-005)
  Source     : US-MDL-002; POL-MDL-005; lookup-module-plan-en.md §3
  Priority   : HIGH

#### AC-MDL-006 — المسار السعيد لإضافة قيمة / add a value, happy path
  Traces : REQ-MDL-006
  Given  : النوع المختار `PAYMENT_METHOD` لا يحمل قيمة رمزها `CASH`
  When   : يحفظ المستخدم قيمة رمزها `CASH` وتسميتاها «نقدًا» / "Cash" ورتبتها 1
  Then   : تُخزَّن القيمة تحت `PAYMENT_METHOD` بـ`code = CASH` و`sortOrder = 1` و`isActiveFl = true`، وتظهر رسالة — ar: «تم حفظ القيمة.» · en: "The value has been saved."

### REQ-MDL-007 — رفض رمز مكرَّر ضمن النوع / Reject a duplicate code within a type
  Pattern    : unwanted
  Statement  : If a submitted lookup value carries a code that another value already carries under the same lookup type, then the system shall reject the value and shall store nothing.
  بالعربية   : إذا حملت قيمةٌ مُرسَلة رمزًا تحمله قيمة أخرى تحت نوع اللوكب نفسه، فيرفض النظام القيمة ولا يخزّن شيئًا.
  Traces     : US-MDL-002
  Entities   : ENT-MDL-002
  Rationale  : قيمة موثوقة واحدة لكل مفهوم مُرمَّز — لا تضارب (POL-MDL-006)
  Source     : US-MDL-002; POL-MDL-006; lookup-module-plan-en.md §6
  Priority   : HIGH

#### AC-MDL-007 — رمز مكرَّر تحت النوع نفسه / a duplicate code under the same type
  Traces : REQ-MDL-007
  Given  : النوع `PAYMENT_METHOD` يحمل قيمة رمزها `CASH`
  When   : يحفظ المستخدم قيمة ثانية رمزها `CASH` تحت `PAYMENT_METHOD`
  Then   : يُرفض الحفظ برسالة RULE-MDL-002 — ar: «هذا الرمز مستخدم بالفعل ضمن هذا النوع» · en: "This code is already used within this type" — ويبقى عدد قيم النوع كما كان قبل الطلب

### REQ-MDL-008 — تعديل قيمة لوكب / Update a lookup value
  Pattern    : event
  Statement  : When a user submits revised Arabic and English labels and a revised sort order for an existing lookup value, the system shall update that value's stored labels and sort order with the submitted ones.
  بالعربية   : عند إرسال المستخدم تسميتين مُنقّحتين ورتبةً مُنقّحة لقيمة لوكب قائمة، يحدّث النظام تسميتَي القيمة ورتبتها المخزَّنة بالمُرسَلة.
  Traces     : US-MDL-002
  Entities   : ENT-MDL-002
  Rationale  : التسمية والرتبة تتغيّران، والرمز عقدُ القيمة عند مستهلكيها فلا يتغيّر
  Source     : US-MDL-002; POL-MDL-005
  Priority   : HIGH

#### AC-MDL-008 — تعديل التسميات دون الرمز / labels change, code does not
  Traces : REQ-MDL-008
  Given  : قيمة قائمة تحت `PAYMENT_METHOD` رمزها `CASH` ورتبتها 1
  When   : يُرسل المستخدم التسميتين «نقد» / "Cash payment" والرتبة 2
  Then   : تُحدَّث `nameAr` و`nameEn` و`sortOrder = 2` بالقيم المُرسَلة، ويبقى `code = CASH` كما هو، ويُملأ `updatedBy` و`updatedAt`

### REQ-MDL-009 — تعطيل قيمة لوكب / Deactivate a lookup value
  Pattern    : event
  Statement  : When a user deactivates a lookup value, the system shall mark that value inactive.
  بالعربية   : عند تعطيل المستخدم قيمةَ لوكب، يجعل النظام القيمة غير فعّالة.
  Traces     : US-MDL-002
  Entities   : ENT-MDL-002
  Rationale  : سحب قيمة من التداول دون فقد الصفوف التي تشير إليها عند المستهلكين
  Source     : US-MDL-002; DEFAULT [KB:erp-domain-standards §6 soft delete]
  Priority   : HIGH

#### AC-MDL-009 — القيمة المعطَّلة تغيب عن المستهلك وتبقى في الإدارة / inactive for consumers, visible in management
  Traces : REQ-MDL-009
  Given  : قيمة فعّالة رمزها `CHEQUE` تحت نوع فعّال مفتاحه `PAYMENT_METHOD`
  When   : يُعطّلها المستخدم
  Then   : تصير `isActiveFl = false`، ولا تُعاد في قراءة المستهلك للمفتاح `PAYMENT_METHOD`، وتبقى معروضة في الجزء التفصيلي للشاشة العامة

### REQ-MDL-010 — إعادة ترتيب قيم النوع / Reorder a type's values
  Pattern    : event
  Statement  : When a user submits a new display order for a lookup type's values, the system shall update each value's sort order to its position in the submitted order.
  بالعربية   : عند إرسال المستخدم ترتيب عرض جديدًا لقيم نوع لوكب، يحدّث النظام رتبة كل قيمة إلى موضعها في الترتيب المُرسَل.
  Traces     : US-MDL-002
  Entities   : ENT-MDL-002
  Rationale  : الترتيب حقل من الحقول القياسية (POL-MDL-005)، ومعناه أن يكون قابلًا للضبط دفعة واحدة
  Source     : US-MDL-002; POL-MDL-005
  Priority   : MEDIUM

#### AC-MDL-010 — الرتب تتبع الترتيب المُرسَل / sort orders follow the submitted order
  Traces : REQ-MDL-010
  Given  : نوع له ثلاث قيم `CASH` و`CHEQUE` و`TRANSFER` برُتب 1 و2 و3
  When   : يُرسل المستخدم الترتيب `TRANSFER`، `CASH`، `CHEQUE`
  Then   : تصير `sortOrder = 1` لـ`TRANSFER` و`2` لـ`CASH` و`3` لـ`CHEQUE`، وتُعاد القيم بهذا الترتيب في قراءة المستهلك

### REQ-MDL-011 — قراءة القيم الفعّالة بالمفتاح / Read active values by key
  Pattern    : event
  Statement  : When a consuming module requests a lookup type's values by that type's key, the system shall return the active values of the type ordered ascending by sort order.
  بالعربية   : عند طلب وحدة مستهلكة قيمَ نوع لوكب بمفتاحه، يعيد النظام القيم الفعّالة لذلك النوع مرتَّبةً تصاعديًا بالترتيب.
  Traces     : US-MDL-003
  Entities   : ENT-MDL-001, ENT-MDL-002
  Rationale  : سبب وجود الوحدة: لا وحدة تحتفظ بقائمة قيم مُرمَّزة خاصة بها (POL-MDL-001, G4)
  Source     : US-MDL-003; POL-MDL-001; lookup-module-plan-en.md §2, §4
  Priority   : HIGH

#### AC-MDL-011 — الفعّالة وحدها ومرتَّبة / active values only, in order
  Traces : REQ-MDL-011
  Given  : نوع فعّال مفتاحه `PAYMENT_METHOD` له قيمتان فعّالتان برتبتَي 1 و2 وقيمة ثالثة معطَّلة
  When   : تطلب وحدة مستهلكة قيم المفتاح `PAYMENT_METHOD`
  Then   : تُعاد القيمتان الفعّالتان وحدهما مرتَّبتين بـ`sortOrder`، وتحمل كل قيمة `code` و`nameAr` و`nameEn`، والقيمة المعطَّلة غائبة عن الرد

### REQ-MDL-012 — مفتاح غير معروف في قراءة المستهلك / An unknown key on a consumer read
  Pattern    : unwanted
  Statement  : If a consumer read names a key that no lookup type carries, then the system shall reject the read with a not-found error instead of an empty successful result.
  بالعربية   : إذا سمّت قراءةُ مستهلك مفتاحًا لا يحمله أي نوع لوكب، فيرفض النظام القراءة بخطأ «غير موجود» بدل نتيجة ناجحة فارغة.
  Traces     : US-MDL-003
  Entities   : ENT-MDL-001
  Rationale  : القائمة الفارغة تُخفي الخطأ البرمجي في المفتاح؛ التمييز بينهما شرط أن تُكتشف الأخطاء عند أول تكامل
  Source     : US-MDL-003; POL-MDL-001; PLATFORM-STD error catalog (project-registry DECISION INDEX #8 / ADR-SEC-002)
  Priority   : HIGH

#### AC-MDL-012 — مفتاح لا وجود له / a key that does not exist
  Traces : REQ-MDL-012
  Given  : لا نوع لوكب على المنصة بالمفتاح `NO_SUCH_KEY`
  When   : تطلب وحدة مستهلكة قيم المفتاح `NO_SUCH_KEY`
  Then   : يُرفض الطلب بخطأ «غير موجود» — ar: «لا يوجد نوع لوكب بهذا المفتاح» · en: "No lookup type exists with this key" — ولا يُعاد ردّ ناجح بقائمة فارغة

### REQ-MDL-013 — تصفّح سجل الأنواع حسب المالك / Browse the type registry by owner
  Pattern    : event
  Statement  : When a platform administrator browses the lookup type registry, the system shall return the active lookup types grouped by their owner module code.
  بالعربية   : عند تصفّح منسّق المنصة سجلَّ أنواع اللوكب، يعيد النظام الأنواع الفعّالة مجمّعةً حسب رمز وحدتها المالكة.
  Traces     : US-MDL-005
  Entities   : ENT-MDL-001
  Rationale  : التسمية بالمالك بلا عرضٍ حسب المالك لا تُراجَع ولا تُدقَّق (POL-MDL-002)
  Source     : US-MDL-005; POL-MDL-002; lookup-module-plan-en.md §7
  Priority   : MEDIUM

#### AC-MDL-013 — التجميع حسب الوحدة المالكة / grouping by owner module
  Traces : REQ-MDL-013
  Given  : ثلاثة أنواع فعّالة: اثنان مالكهما `FIN` وواحد مالكه `SEC`
  When   : يفتح منسّق المنصة سجلَّ الأنواع دون أي مُرشِّح
  Then   : تُعاد مجموعتان — `FIN` بنوعَيها و`SEC` بنوعه — ويحمل كل صف `key` و`nameAr` و`nameEn` و`ownerModuleCode`

## A5 — Business rules

### RULE-MDL-001 — الوحدة المالكة مسجَّلة في الأمان / The owner module is registered in SEC
  Scope      : ENT-MDL-001
  Trigger    : on create
  Statement  : The system shall reject a lookup type registration whose owner module code has no ModuleRegistry row in SEC.
  Message    : ar: «الوحدة المالكة غير مسجّلة في وحدة الأمان» · en: "The owning module is not registered in the Security module"
  Traces     : REQ-MDL-001, REQ-MDL-002
  Data source: ENT-MDL-001.ownerModuleCode
  Cross-module: وجود الرمز يُقرأ من ModuleRegistry (ENT-SEC-004، SOFT-READ — A8)، بلا مفتاح أجنبي
  Source     : POL-MDL-003; module-registry-mdl.md SHARED ENTITIES CONSUMED
  Test-Hint  : الفحص عند الإنشاء وحده؛ إلغاء تسجيل وحدة في الأمان لاحقًا لا يُبطل أنواعها القائمة

### RULE-MDL-002 — لا رمز مكرَّر ضمن النوع الواحد / No duplicate code within one type
  Scope      : ENT-MDL-002
  Trigger    : on create / on update
  Statement  : The system shall reject a lookup value whose code already exists under the same lookup type.
  Message    : ar: «هذا الرمز مستخدم بالفعل ضمن هذا النوع» · en: "This code is already used within this type"
  Traces     : REQ-MDL-006, REQ-MDL-007, REQ-MDL-008
  Data source: ENT-MDL-002.lookupTypeId, ENT-MDL-002.code
  Source     : POL-MDL-006; lookup-module-plan-en.md §6
  Test-Hint  : الرمز نفسه تحت نوع آخر مقبول — التفرّد ضمن النوع لا على مستوى الوحدة

### RULE-MDL-003 — مفتاح النوع لا يتغيّر بعد الإنشاء / A type's key is immutable after creation
  Scope      : ENT-MDL-001
  Trigger    : on update
  Statement  : The system shall prevent editing a lookup type's key after creation.
  Message    : ar: «لا يمكن تعديل مفتاح نوع اللوكب بعد إنشائه» · en: "A lookup type's key cannot be changed after creation"
  Traces     : REQ-MDL-003
  Data source: ENT-MDL-001.key
  Source     : DEFAULT — المفتاح عقد القراءة لكل مستهلك (POL-MDL-001, G4)؛ [KB:erp-domain-standards §2 rule 3]
  Test-Hint  : التعديل لا يُرفض برسالة فحسب — المفتاح غائب أصلًا عن حمولة التعديل

### RULE-MDL-004 — النوع المعطَّل يحجب قيمه عن المستهلك / An inactive type hides its values from consumers
  Scope      : ENT-MDL-001, ENT-MDL-002
  Trigger    : on evaluate — عند كل قراءة مستهلك
  Statement  : While a lookup type is inactive, the system shall exclude its values from consumer reads.
  Message    : ar: «هذا النوع معطّل حاليًا» · en: "This lookup type is currently inactive"
  Traces     : REQ-MDL-004, REQ-MDL-011
  Data source: ENT-MDL-001.isActiveFl, ENT-MDL-002.isActiveFl
  Source     : POL-MDL-001; engine §3.3 ARCH-5
  Test-Hint  : الحجب أثر قراءة لا كتابة — لا يُغيَّر علم النشاط على أي قيمة عند تعطيل نوعها

**ARCH-5** — الكيانان كلاهما SHARED (owner) ويقبلان التعطيل: أثر التعطيل على المستهلك
SOFT-READ منصوصٌ عليه بالمنع في RULE-MDL-004 للنوع، وفي REQ-MDL-011 للقيمة (الفعّالة
وحدها تُعاد). لا انتشار (cascade) ولا إشعار: الأثر أثر قراءة، والصفوف تبقى كما هي.

**قيود بلا قاعدة خاصة / constraints carried by the platform error catalog** — تفرّد
المفتاح على مستوى المنصة (REQ-MDL-001) و«غير موجود» لمفتاح مجهول (REQ-MDL-012) صفّان من
صفوف كتالوج الأخطاء البنيوية (duplicate، not-found) تُذكر تحت مظلة PLATFORM-STD الواحدة،
لا كقاعدة عمل لكل منها — سابقة معتمدة في project-registry DECISION INDEX #8 (ADR-SEC-002).
رسالتاهما ثنائيتا اللغة في AC-MDL-001 وAC-MDL-012.

## A6 — Lookups

لا تملك الوحدة أي مفتاح قائمة قيم ولا تستهلك أيًّا منه في هذه النسخة: MDL هي الآلية التي
تعمل عليها قوائم الوحدات الأخرى، ولا "تستهلك نفسها" (module-registry-mdl.md LOOKUPS OWNED).
علم النشاط `isActiveFl` قيمة منطقية باصطلاح المنصة لا نوع lookup، و`ownerModuleCode` رمز
وحدة يُقرأ من سجل وحدات الأمان لا مفتاح قائمة قيم (A8).
MDL owns no lookup key and consumes none; قاعدة الملف الشخصي "كل قيم LOV تُحمَّل زمن
التشغيل من وحدة اللوكب" هي وصف هذه الوحدة نفسها لا قيدًا تستهلكه.

## A7 — Status lifecycle

لا ينطبق / not applicable — لكل من الكيانين حالتان اثنتان لا أكثر (فعّال / معطَّل) وانتقال
واحد في هذه النسخة، وهو دون عتبة الثلاث حالات التي تستوجب مخطّطًا (§6):

```
فعّال (isActiveFl = true)  ──(تعطيل / deactivate)──▶  معطَّل (isActiveFl = false)
```
لا انتقال عكسي: إعادة التفعيل خارج النطاق (A2) — لا قصة معتمدة تطلبها.

## A8 — Module dependencies

| Consumed entity | Owner ENT id | Owner module | HARD-FK / SOFT-READ | XM candidate (assigned by P2) |
|---|---|---|---|---|
| ModuleRegistry | ENT-SEC-004 | SEC | SOFT-READ — فحص وجود رمز الوحدة المالكة عند الإنشاء (RULE-MDL-001) | نعم — مرشَّح XM، المعرّف من إسناد P2 |

الكيان أعلاه هو الكيان الوحيد الذي تستهلكه MDL. لا مفتاح أجنبي عابرًا للوحدات: الفحص
قراءة تطبيقية بالرمز [KB:erp-domain-standards §5]، وهو مسموح في أي اتجاه، ولا يغيّر كون
MDL وحدة تأسيسية Tier 0 [KB:erp-domain-standards §1].

| External service | Purpose | Integration kind |
|---|---|---|
| — | لا خدمة خارجية: لا إشعارات ولا خدمة ملفات ولا أي قائمة قيم مستهلكة | — |

# PART B — SCREEN REQUIREMENTS

## SCR-REQ-MDL-001 — اللوكبات العامة / Generic Lookups

### B1 — Definition
  Purpose      : أن يدير المستخدم أنواع اللوكب وقيمها كلها من شاشة واحدة، دون شاشة لكل قائمة
  Entities     : ENT-MDL-001, ENT-MDL-002
  Operations   : search · create · read · update · deactivate · reorder
  Users        : منسّق المنصة / Platform administrator · مدير قوائم الوحدة المالكة / Owning-module lookup manager
  Navigation   : البيانات المرجعية / Master Data Lookup → اللوكبات / Lookups → اللوكبات العامة / Generic Lookups ; from: القائمة الرئيسية / main menu ; to: سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner (SCR-REQ-MDL-002)
  Content shape: header + repeating lines — رئيسي (أنواع) + تفصيلي (قيم) قابل لإعادة الترتيب — تلميح لا تصميم
  Traces     : REQ-MDL-001, REQ-MDL-002, REQ-MDL-003, REQ-MDL-004, REQ-MDL-005, REQ-MDL-006, REQ-MDL-007, REQ-MDL-008, REQ-MDL-009, REQ-MDL-010
  Composite    : بحث + إدخال على المستويين (رئيسي + تفصيلي) = متطلَّب شاشة واحد (profile.conventions.composite_screen)

### B2 — Search / list
| Filter | Field (ENT) | Result column | Notes |
|---|---|---|---|
| المفتاح / Key | ENT-MDL-001.key | المفتاح / Key | تطابق جزئي (LIKE) |
| الوحدة المالكة / Owner module | ENT-MDL-001.ownerModuleCode | الوحدة المالكة / Owner module | تطابق تام (EXACT) |
| الاسم / Name | ENT-MDL-001.nameAr, ENT-MDL-001.nameEn | الاسم (عربي) · الاسم (إنجليزي) / Name (ar · en) | تطابق جزئي في اللغتين معًا [KB:erp-domain-standards §6 search] |
| الحالة / Active | ENT-MDL-001.isActiveFl | نشط / Active | تطابق تام |
| رمز القيمة / Value code | ENT-MDL-002.code | الرمز / Code | مُرشِّح الجزء التفصيلي، محصور بالنوع المختار (REQ-MDL-005) |

كل مُرشِّح يقابله عمود نتيجة. القائمة الرئيسية مُرقَّمة الصفحات على الخادم (20 افتراضًا،
200 حدًّا أقصى — [KB:erp-domain-standards §6 paging])، والجزء التفصيلي يُعرض كاملًا مرتَّبًا
بـ`sortOrder` لأنه محصور بنوع واحد. لا مفتاح قائمة قيم في أي مُرشِّح (A6).

### B3 — Input
| Field (ENT) | Editable | Notes |
|---|---|---|
| ENT-MDL-001.key | عند الإنشاء وحده / create-only | applies RULE-MDL-003 |
| ENT-MDL-001.ownerModuleCode | عند الإنشاء وحده / create-only | applies RULE-MDL-001 |
| ENT-MDL-001.nameAr, ENT-MDL-001.nameEn | yes | REQ-MDL-001, REQ-MDL-003 |
| ENT-MDL-001.isActiveFl | no | يتغيّر بإجراء التعطيل وحده (REQ-MDL-004) |
| ENT-MDL-002.code | عند الإنشاء وحده / create-only | applies RULE-MDL-002 |
| ENT-MDL-002.nameAr, ENT-MDL-002.nameEn | yes | REQ-MDL-006, REQ-MDL-008 |
| ENT-MDL-002.sortOrder | yes | يُضبط بالتحرير أو بإعادة الترتيب (REQ-MDL-010) |
| ENT-MDL-002.isActiveFl | no | يتغيّر بإجراء التعطيل وحده (REQ-MDL-009) |
| حقول التدقيق على الكيانين / audit fields | no | يملؤها النظام |

| Action (ar / en) | Operation | REQ | RULEs |
|---|---|---|---|
| حفظ نوع / Save type | create | REQ-MDL-001, REQ-MDL-002 | RULE-MDL-001 |
| حفظ تعديل النوع / Save type changes | update | REQ-MDL-003 | RULE-MDL-003 |
| تعطيل النوع / Deactivate type | deactivate | REQ-MDL-004 | RULE-MDL-004 |
| حفظ قيمة / Save value | create | REQ-MDL-006, REQ-MDL-007 | RULE-MDL-002 |
| حفظ تعديل القيمة / Save value changes | update | REQ-MDL-008 | RULE-MDL-002 |
| تعطيل القيمة / Deactivate value | deactivate | REQ-MDL-009 | — |
| إعادة الترتيب / Reorder | reorder | REQ-MDL-010 | — |

لا إجراء تفعيل ولا محو نهائي على أي من المستويين: إعادة التفعيل خارج النطاق (A2)،
والتعطيل ناعم [KB:erp-domain-standards §6].

### B4 — Access
| Page code | Action | Roles |
|---|---|---|
| MDL_LOOKUPS | VIEW (بوابة / gateway) | منسّق المنصة · مدير قوائم الوحدة المالكة · الحساب الخدمي للوحدة المستهلكة (قراءة B5 وحدها، بلا واجهة) |
| MDL_LOOKUPS | CREATE | منسّق المنصة · مدير قوائم الوحدة المالكة |
| MDL_LOOKUPS | UPDATE | منسّق المنصة · مدير قوائم الوحدة المالكة |
| MDL_LOOKUPS | DELETE (تعطيل ناعم / soft deactivate) | منسّق المنصة · مدير قوائم الوحدة المالكة |

صفٌّ واحد في SEC_PAGES لهذه الشاشة المركّبة. أسماء الصلاحيات تشتقّها وحدة الأمان من رمز
الصفحة ولا تُعدَّد هنا. منح الشاشة هو كل الدقّة المتاحة: الصلاحية لكل نوع لوكب على حدة
مستثناة صراحةً (business-policies SCOPE EXCEPTIONS)، ولا منطق تفويض مكتوب داخل الوحدة
(domain-profile G2, G3).

### B5 — API expectations
Base path : `/api/v1/mdl/lookup-types` و`/api/v1/mdl/lookup-values` — Response `ApiResponse<T>` ·
Paging `Page<T>` · Errors `LocalizedException → {code, messageAr, messageEn}`

| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search types | POST | `/api/v1/mdl/lookup-types/search` | key, ownerModuleCode, name, isActiveFl, paging | page of lookup types | — | REQ-MDL-001, REQ-MDL-003 |
| create type | POST | `/api/v1/mdl/lookup-types` | key, ownerModuleCode, nameAr, nameEn | the created type | RULE-MDL-001 | REQ-MDL-001, REQ-MDL-002 |
| update type | PUT | `/api/v1/mdl/lookup-types/{id}` | id, nameAr, nameEn | the updated type | RULE-MDL-003 | REQ-MDL-003 |
| deactivate type | DELETE | `/api/v1/mdl/lookup-types/{id}` | id | confirmation | RULE-MDL-004 | REQ-MDL-004 |
| search values of a type | POST | `/api/v1/mdl/lookup-types/values/search` | lookupTypeId, code | the type's values, ordered | — | REQ-MDL-005 |
| create value | POST | `/api/v1/mdl/lookup-types/{id}/values` | id, code, nameAr, nameEn, sortOrder | the created value | RULE-MDL-002 | REQ-MDL-006, REQ-MDL-007 |
| update value | PUT | `/api/v1/mdl/lookup-values/{id}` | id, nameAr, nameEn, sortOrder | the updated value | RULE-MDL-002 | REQ-MDL-008 |
| deactivate value | DELETE | `/api/v1/mdl/lookup-values/{id}` | id | confirmation | — | REQ-MDL-009 |
| reorder values | PATCH | `/api/v1/mdl/lookup-types/{id}/values/reorder` | id, القيم بترتيبها الجديد | confirmation | — | REQ-MDL-010 |

القراءات الثلاث تُطلب بـ`POST …/search` لأن مُرشِّحاتها مركَّبة، وهو اصطلاح المنصة للبحث؛
وباقي الأفعال على اصطلاح `profile.stack.backend.api`. لا قيمة مُرمَّزة مُعدَّدة في أي عملية
(A6). المفاتيح والرموز تُرسل كما كتبها المستخدم، ولا يولّد النظام أيًّا منها (§3.3 NUMBERING).

## SCR-REQ-MDL-002 — سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner

### B1 — Definition
  Purpose      : أن يجد منسّق المنصة قوائم أي وحدة ويُدقّقها في نظرة واحدة، مجمّعةً حسب مالكها
  Entities     : ENT-MDL-001
  Operations   : search · browse (قراءة فقط / read-only)
  Users        : منسّق المنصة / Platform administrator
  Navigation   : البيانات المرجعية / Master Data Lookup → اللوكبات / Lookups → سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner ; from: القائمة الرئيسية / main menu, اللوكبات العامة / Generic Lookups ; to: اللوكبات العامة / Generic Lookups (SCR-REQ-MDL-001)
  Content shape: true hierarchy (parent/child) — وحدة مالكة → أنواعها — تلميح لا تصميم
  Traces     : REQ-MDL-011, REQ-MDL-012, REQ-MDL-013
  Composite    : تصفّح مجمَّع بلا إدخال = متطلَّب شاشة واحد (profile.conventions.composite_screen)

### B2 — Search / list
| Filter | Field (ENT) | Result column | Notes |
|---|---|---|---|
| الوحدة المالكة / Owner module | ENT-MDL-001.ownerModuleCode | الوحدة المالكة / Owner module | تطابق تام (EXACT) — وهو عنوان المجموعة نفسه |
| المفتاح / Key | ENT-MDL-001.key | المفتاح / Key | تطابق جزئي (LIKE) |

كل مُرشِّح يقابله عمود نتيجة. المجموعة تُعاد كاملة بلا ترقيم صفحات — عدد الأنواع محكوم
بعدد الوحدات — والأنواع الفعّالة وحدها تدخل السجل (REQ-MDL-013)، فلا مُرشِّح حالة هنا.

### B3 — Input
تصفّح للقراءة فقط؛ لا إنشاء ولا تعديل هنا (الإدارة على SCR-REQ-MDL-001) /
Read-only browse; no create/update here (management happens on SCR-REQ-MDL-001).
الحقول المعروضة كلها من ENT-MDL-001 بالإشارة، وأيٌّ منها غير قابل للتحرير.

| Action (ar / en) | Operation | REQ | RULEs |
|---|---|---|---|
| فتح النوع في الشاشة العامة / Open in Generic Lookups | read | REQ-MDL-013 | — |

### B4 — Access
| Page code | Action | Roles |
|---|---|---|
| MDL_TYPE_REGISTRY | VIEW (بوابة / gateway) | منسّق المنصة / Platform administrator |

لا CREATE ولا UPDATE ولا DELETE على هذه الشاشة — وهو نصّ B3 نفسه مقروءًا من جهة التفويض.
صفٌّ واحد في SEC_PAGES، وأسماء الصلاحيات تشتقّها وحدة الأمان من رمز الصفحة.

### B5 — API expectations
Base path : `/api/v1/mdl/lookup-types` و`/api/v1/mdl/lookups` — Response `ApiResponse<T>` ·
Errors `LocalizedException → {code, messageAr, messageEn}`

| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| browse registry by owner | POST | `/api/v1/mdl/lookup-types/by-owner/search` | ownerModuleCode, key | الأنواع الفعّالة مجمّعة حسب المالك | — | REQ-MDL-013 |
| read values by key (consumer) | GET | `/api/v1/mdl/lookups` | مفتاح النوع / the type key | القيم الفعّالة مرتَّبة بـ`sortOrder` | RULE-MDL-004 | REQ-MDL-011, REQ-MDL-012 |

الصفّ الثاني عمليةٌ بلا سطح شاشة: تستدعيها خلفيّاتُ الوحدات المستهلكة لا مستخدمٌ
(US-MDL-003)، وتُدرج في هذا المتطلَّب لأن متطلَّبَيها (REQ-MDL-011, REQ-MDL-012) يخصّان
السجلَّ نفسه قراءةً بالمفتاح. تفويضها هو منح VIEW على MDL_LOOKUPS للحساب الخدمي للوحدة
المستهلكة (B4 في SCR-REQ-MDL-001)، لا منحٌ خاص بها.

# STANDALONE

## Traceability matrix

| P0.5 | REQ | AC | RULE | ENT | SCR-REQ |
|---|---|---|---|---|---|
| US-MDL-001 | REQ-MDL-001, REQ-MDL-002, REQ-MDL-003, REQ-MDL-004 | AC-MDL-001, AC-MDL-002, AC-MDL-003, AC-MDL-004 | RULE-MDL-001, RULE-MDL-003, RULE-MDL-004 | ENT-MDL-001 | SCR-REQ-MDL-001 |
| US-MDL-002 | REQ-MDL-005, REQ-MDL-006, REQ-MDL-007, REQ-MDL-008, REQ-MDL-009, REQ-MDL-010 | AC-MDL-005, AC-MDL-006, AC-MDL-007, AC-MDL-008, AC-MDL-009, AC-MDL-010 | RULE-MDL-002 | ENT-MDL-001, ENT-MDL-002 | SCR-REQ-MDL-001 |
| US-MDL-003 | REQ-MDL-011, REQ-MDL-012 | AC-MDL-011, AC-MDL-012 | RULE-MDL-004 | ENT-MDL-001, ENT-MDL-002 | SCR-REQ-MDL-002 |
| US-MDL-004 | REQ-MDL-001, REQ-MDL-002 | AC-MDL-001, AC-MDL-002 | RULE-MDL-001 | ENT-MDL-001 | SCR-REQ-MDL-001 |
| US-MDL-005 | REQ-MDL-013 | AC-MDL-013 | — | ENT-MDL-001 | SCR-REQ-MDL-002 |

كل قصة من القصص الخمس مغطّاة بمتطلَّب واحد على الأقل، وكل متطلَّب من الثلاثة عشر يتتبّع
قصةً واحدة على الأقل وله معيار قبول واحد، وكل قاعدة من الأربع تتتبّع متطلَّبًا، وكل
متطلَّب شاشة يتتبّع متطلَّبات مسمّاة. لا يتيم ولا مرجع معلّق. (US-MDL-005 قصة تصفّح
للقراءة فقط: لا قاعدة عمل تخصّها، والقيد الوحيد عليها — الأنواع الفعّالة وحدها — منصوصٌ
في نصّ REQ-MDL-013 نفسه.)

## Decisions applied

| DEFAULT / ADR | What | Source | Override / status |
|---|---|---|---|
| DEFAULT | التعطيل ناعم بـ`isActiveFl` على الكيانين، ولا حذف نهائي | [KB:erp-domain-standards §6 soft delete] | Override: لا يُغيَّر إلا بقرار منصّي يشمل الوحدات كلها |
| DEFAULT | حقول التدقيق الأربعة على الكيانين معًا، ومنها `lookup` رغم غيابها عن مجموعته القياسية | [KB:erp-domain-standards §6 audit trail] | Override: إسقاطها عن قيمة اللوكب يُفقد أثر مَن غيّر قائمةً يقرأها الجميع |
| DEFAULT | ترقيم صفحات قائمة الأنواع على الخادم: 20 افتراضًا، 200 حدًّا أقصى | [KB:erp-domain-standards §6 paging] | Override: رقمان قابلان للضبط دون أثر على أي متطلَّب |
| DEFAULT | البحث على الخادم بالمفتاح والاسم في اللغتين | [KB:erp-domain-standards §6 search] | Override: حصره بالمفتاح وحده إن طلب العميل |
| DEFAULT | التواريخ تُخزَّن UTC وتُعرض بتوقيت المستأجر | [KB:erp-domain-standards §6 dates] | Override: قرار منصّي لا قرار وحدة |
| DEFAULT | مفتاح النوع غير قابل للتعديل بعد الإنشاء (RULE-MDL-003) | POL-MDL-001; G4; [KB:erp-domain-standards §2 rule 3] | Override: السماح بالتعديل يوجب مسار ترحيل لكل مستهلك يقرأ بالمفتاح — قرار منصّي |
| سابقة / precedent — ADR-SEC-002 | تفرّد المفتاح و«غير موجود» صفّا كتالوج أخطاء بنيوية تحت مظلة PLATFORM-STD، لا قاعدة عمل لكل منهما | project-registry DECISION INDEX #8 | ACCEPTED — مطبَّقة كما هي، بلا قرار جديد |
| سابقة / precedent — ADR-FIN-001 | التبعية على الأمان قراءة تطبيقية لا مفتاحًا أجنبيًا عابرًا للوحدات | project-registry DECISION INDEX #9; [KB:erp-domain-standards §5] | ACCEPTED — مطبَّقة على RULE-MDL-001 وA8 |

لا قرار ADR جديد في هذه المرحلة: كل نقطة كانت تحتمل وجهين حسمها مدخلٌ قائم — سياسة، أو
سجلّ، أو مصدر معرفة، أو سابقة معتمدة في سجلّ القرارات. ولا قرار بحالة BLOCKED.

## Access summary

| Page code | Screen (ar / en) | Actions | Roles |
|---|---|---|---|
| MDL_LOOKUPS | اللوكبات العامة / Generic Lookups | VIEW (بوابة) · CREATE · UPDATE · DELETE (تعطيل) | منسّق المنصة · مدير قوائم الوحدة المالكة · الحساب الخدمي للوحدة المستهلكة (VIEW وحدها) |
| MDL_TYPE_REGISTRY | سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner | VIEW (بوابة) | منسّق المنصة |

صفّان في SEC_PAGES لا أكثر — شاشتان مركّبتان، لكل واحدة رمز صفحة واحد. المصدر هو B4 في
كلا متطلَّبَي الشاشة، وأسماء الصلاحيات تشتقّها وحدة الأمان من رمز الصفحة ولا تُعدَّد هنا.
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>

<<<ARTIFACT: current-ui-ux-spec.md>>>
# UI/UX SPEC — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module : MDL   Version : v1   Profile : erp   Stage : P3.2 (Part A — UX design)
Sources: srs-mdl.md (fields, rules, permissions — the functional ceiling) · prd-mdl.md (intent)
Mints  : SCR-MDL-001, SCR-MDL-002 · UXD-MDL-001
Scope  : design intent — final component names, routes and code are Part B's
══════════════════════════════════════════════════════════════════

هذه المواصفة لا تضيف حقلًا ولا قاعدة ولا صلاحية ليست في SRS، ولا تُسقط شيئًا منها؛ كل سطر أدناه
منقول عن مصدره ومنسوب إليه / This spec adds no field, rule or permission the SRS does not have and
omits none of them: every line below is copied from its source and attributed to it.

## SCR-MDL-001 — اللوكبات العامة / Generic Lookups
Traces            : REQ-MDL-001, REQ-MDL-002, REQ-MDL-003, REQ-MDL-004, REQ-MDL-005, REQ-MDL-006, REQ-MDL-007, REQ-MDL-008, REQ-MDL-009, REQ-MDL-010, AC-MDL-001, AC-MDL-002, AC-MDL-003, AC-MDL-004, AC-MDL-005, AC-MDL-006, AC-MDL-007, AC-MDL-008, AC-MDL-009, AC-MDL-010, UXD-MDL-001
Screen requirement: SCR-REQ-MDL-001 · page code MDL_LOOKUPS
UI pattern        : header + repeating lines — رئيسي (أنواع) + تفصيلي (قيم) قابل لإعادة الترتيب / master (types) + detail (values), reorderable — من SRS §B1 حرفيًا، غير مُبدَّل / verbatim from the SRS screen entry, not changed here
Sub-views         : Search (الأنواع / the types, مُرقَّم الصفحات على الخادم / server-paged) · Entry (نوع / a type) · Detail (قيم النوع المختار / the selected type's values) · Entry (قيمة / a value) — أربعة سطوح تحت SCR واحد / four surfaces under ONE SCR
Fields shown      : كل حقول ENT-MDL-001 وENT-MDL-002 كما في SRS §A3 — الجدولان أدناه، بتسمية لكل لغة وبعلم القراءة فقط / every field of ENT-MDL-001 and ENT-MDL-002 as SRS §A3 states them — the two tables below, label per language, read-only flags marked
Composition       : جزء القيم ليس تفصيلًا ثانويًا داخل نموذج النوع / the values are not secondary detail inside the type's form — inline: none · summary row + second level: صفّ لكل قيمة في جزء القيم، ومحرّرها يُفتح من ذلك الصفّ / a row per value in the values pane, its editor opened from that row · submits: one per open form, never two open at once
Permissions       : SRS §B4 — MDL_LOOKUPS: VIEW (بوابة / gateway) · CREATE · UPDATE · DELETE (تعطيل ناعم / soft deactivate) — reference only; names follow `PERM_<PAGE_CODE>_<ACTION>`
Cross-module data : `ownerModuleCode` → UXD-MDL-001 (owner module SEC)
States            : empty — لا نوع يطابق المرشِّحات / no type matches the filters · النوع المختار بلا قيم / the selected type has no values (رسالتان مختلفتان / two distinct messages) · loading — هيكل عظمي للقائمة، ومؤشّر موضعي لجزء القيم / a list skeleton and a local indicator on the values pane · error — لافتة برسالة الكتالوج المحلية / a banner carrying the localized catalog message (الرموز نفسها من نصيب الجزء ب / the codes themselves are Part B's) · offline — لا شيء: SRS لا تنصّ على عمل بلا اتصال / nothing: the SRS states no offline behaviour

**حقول ENT-MDL-001 — نوع اللوكب / LookupType**

| Field | التسمية (ar) | Label (en) | Read-only | من / from |
|---|---|---|---|---|
| lookupTypePk | معرّف نوع اللوكب | LookupType id | نعم / yes — لا يُعرض كمرجع عمل / never shown as a business reference | SRS §A3 |
| key | المفتاح | Key | عند التعديل / on edit — RULE-MDL-003 | SRS §A3, §B3 |
| ownerModuleCode | رمز الوحدة المالكة | Owner module code | عند التعديل / on edit | SRS §A3, §B3 |
| nameAr | الاسم (عربي) | Name (Arabic) | لا / no | SRS §A3 |
| nameEn | الاسم (إنجليزي) | Name (English) | لا / no | SRS §A3 |
| isActiveFl | نشط | Active | نعم / yes — يتغيّر بإجراء التعطيل وحده / changed by the deactivate action alone | SRS §B3 |
| createdBy · createdAt · updatedBy · updatedAt | أنشأه · تاريخ الإنشاء · عدّله · تاريخ التعديل | Created by · Created at · Updated by · Updated at | نعم / yes — يملؤها النظام / system-filled | SRS §A3 |

**حقول ENT-MDL-002 — قيمة اللوكب / LookupValue**

| Field | التسمية (ar) | Label (en) | Read-only | من / from |
|---|---|---|---|---|
| lookupValuePk | معرّف القيمة | LookupValue id | نعم / yes | SRS §A3 |
| lookupTypeId | نوع اللوكب | Lookup type | نعم / yes — يُحدَّد باختيار النوع لا بكتابته / set by selecting the type, never typed | SRS §A3 |
| code | الرمز | Code | عند التعديل / on edit — RULE-MDL-002 يحرس تفرّده ضمن النوع / its uniqueness within the type | SRS §A3, §B3 |
| nameAr | الاسم (عربي) | Name (Arabic) | لا / no | SRS §A3 |
| nameEn | الاسم (إنجليزي) | Name (English) | لا / no | SRS §A3 |
| sortOrder | الترتيب | Sort order | لا / no — يُضبط بالتحرير أو بإعادة الترتيب / set by editing or by reordering | SRS §A3, §B3 |
| isActiveFl | نشط | Active | نعم / yes — يتغيّر بإجراء التعطيل وحده / changed by the deactivate action alone | SRS §B3 |
| createdBy · createdAt · updatedBy · updatedAt | أنشأها · تاريخ الإنشاء · عدّلها · تاريخ التعديل | Created by · Created at · Updated by · Updated at | نعم / yes — يملؤها النظام / system-filled | SRS §A3 |

**المرشِّحات وأعمدة النتيجة / filters and result columns** — SRS §B2, one column per filter:
المفتاح (LIKE) · الوحدة المالكة (EXACT) · الاسم في اللغتين معًا (LIKE) · الحالة (EXACT) على القائمة
الرئيسية؛ ورمز القيمة (LIKE) على الجزء التفصيلي، محصورًا بالنوع المختار (REQ-MDL-005). القائمة
الرئيسية مُرقَّمة الصفحات على الخادم، والجزء التفصيلي يُعرض كاملًا مرتَّبًا بـ`sortOrder` لأنه محصور
بنوع واحد / the master list is server-paged; the detail is shown whole, ordered by `sortOrder`,
because it is confined to one type.

**الإجراءات / actions** — SRS §B3: حفظ نوع · حفظ تعديل النوع · تعطيل النوع · حفظ قيمة · حفظ تعديل
القيمة · تعطيل القيمة · إعادة الترتيب / save type · save type changes · deactivate type · save
value · save value changes · deactivate value · reorder. لا إجراء تفعيل ولا محو نهائي على أي من
المستويين / no activate and no hard delete at either level — SRS §B3 and §A2 (re-activation is out
of scope), which is why deactivation is stated as one-way where it is offered.

**لماذا هذا التوزيع / why this composition.** الشاشة تحرّر سجلّين من كيانين مختلفين، لا سجلًّا واحدًا
ذا مجموعة أبناء / this screen edits records of two different entities, not one record with a child
set: `LookupTypeCreateRequest` لا يحمل قيمًا أصلًا، وكل قيمة سجلّ قائم بذاته له عملياته / a type's
write carries no values at all, and each value is a record of its own. فالنوع لا "يحفظ قيمه" ولا
يمكن أن يحفظها / a type therefore does not — and cannot — save its values. جزء القيم صفّ ملخَّص لكل
قيمة (الرمز، التسميتان، الترتيب، الحالة) ومحرّر يُفتح منه كمستوى ثانٍ فوقه، وهو:
1. **يحفظ سجلّه هو بحفظ واحد** — القيمة كيان مستقل، لا حقل في نموذج النوع؛ ولا يُفتح محرّر قيمة ونموذج
   نوع معًا أبدًا، فلا يرى المستخدم زرَّي حفظ في وقت واحد / it saves its OWN record with ONE submit,
   and a value editor and a type form are never open at once, so two Save buttons never face the
   user together;
2. **حالة فتحه في حالة التنقّل** حيث تعيش بقية حالة التنقّل، فالرجوع يغلقه، والصرف يغلقه وحده،
   والرابط العميق يفتحه / its open state lives in navigation state: back closes it, dismissing
   closes only it, and a deep link opens it;
3. **لا منطقة تمرير خاصة به** — القائمة تتمرّر مع الجسم الذي تسكنه / it carries no scroll region of
   its own; the list scrolls with the body it sits in;
4. **يُرسم شقيقًا للمستوى الأول** لا داخل عنصره / it renders as a SIBLING of the first level, never
   inside its element.
إعادة الترتيب إجراء واحد على القائمة كلها — المجموعة المرتَّبة تُرسل مرة واحدة — لا تحريرًا لحقل في
نموذج / reordering is one action over the whole list: the ordered set is submitted once, not
edited field by field. ولا إجراء على هذه الشاشة يملك نداءين / no action of this screen owns two
calls, so nothing here needs an ordered pair.

## SCR-MDL-002 — سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner
Traces            : REQ-MDL-013, AC-MDL-013, UXD-MDL-001
Screen requirement: SCR-REQ-MDL-002 · page code MDL_TYPE_REGISTRY
UI pattern        : true hierarchy (parent/child) — وحدة مالكة → أنواعها / owner module → its types — من SRS §B1 حرفيًا / verbatim from the SRS screen entry
Sub-views         : Search (مرشِّحان / two filters) · Browse (المجموعات / the groups) — لا سطح إدخال البتة / no entry surface at all (SRS §B3: read-only browse)
Fields shown      : حقول ENT-MDL-001 المعروضة بالإشارة، كلها للقراءة / the displayed fields of ENT-MDL-001, every one read-only — الجدول أدناه / the table below
Composition       : none — سجل للقراءة فقط بلا تفصيل ثانوي ولا مُنتقٍ ولا مجموعة أبناء تُحرَّر / a read-only browse with no secondary detail, no picker and no editable child set · submits: none — الشاشة لا تكتب شيئًا / the screen writes nothing
Permissions       : SRS §B4 — MDL_TYPE_REGISTRY: VIEW (بوابة / gateway) وحدها؛ لا CREATE ولا UPDATE ولا DELETE / VIEW alone — reference only; names follow `PERM_<PAGE_CODE>_<ACTION>`
Cross-module data : `ownerModuleCode` → UXD-MDL-001 (owner module SEC) — هنا عنوان المجموعة نفسه / here it is the group heading itself
States            : empty — لا وحدة مالكة تطابق المرشِّحات / no owner module matches the filters (مجموعة بلا أنواع لا تُعاد أصلًا / a group with no types is not returned at all) · loading — هيكل عظمي للمجموعات / a skeleton over the groups · error — لافتة برسالة الكتالوج المحلية / a banner carrying the localized catalog message · offline — لا شيء: SRS لا تنصّ على عمل بلا اتصال / nothing: the SRS states no offline behaviour

| Field | التسمية (ar) | Label (en) | Read-only | من / from |
|---|---|---|---|---|
| ownerModuleCode | الوحدة المالكة | Owner module | نعم / yes — عنوان المجموعة / the group heading | SRS §B2, AC-MDL-013 |
| key | المفتاح | Key | نعم / yes | SRS §B2, AC-MDL-013 |
| nameAr | الاسم (عربي) | Name (Arabic) | نعم / yes | AC-MDL-013 |
| nameEn | الاسم (إنجليزي) | Name (English) | نعم / yes | AC-MDL-013 |

**المرشِّحان / the two filters** — SRS §B2: الوحدة المالكة (EXACT، وهو عنوان المجموعة نفسه) والمفتاح
(LIKE)، ولكلٍّ عمود نتيجة. المجموعة تُعاد كاملة بلا ترقيم صفحات، والأنواع الفعّالة وحدها تدخل السجل
(REQ-MDL-013)، فلا مرشِّح حالة هنا / the groups come back whole with no paging, and only active
types enter the registry, so there is no active-state filter on this screen.

**الإجراء الوحيد / the only action** — فتح النوع في الشاشة العامة / open the type in Generic
Lookups (SRS §B3): انتقال إلى SCR-MDL-001 بالنوع مختارًا، بحارس تلك الشاشة نفسه / a navigation to
SCR-MDL-001 with that type selected, under that screen's own guard.

---

## التبعيات العابرة للوحدات في طبقة العرض / Cross-module display dependencies

### UXD-MDL-001 — الوحدة المالكة تُقرأ من سجل وحدات الأمان / The owner module is read from the security module's registry
Traces      : REQ-MDL-001, REQ-MDL-002, REQ-MDL-013, AC-MDL-001, AC-MDL-002, AC-MDL-013
Screens     : SCR-MDL-001 (حقل إدخال عند الإنشاء، وعمود، ومرشِّح / an input on create, a column and a filter) · SCR-MDL-002 (عنوان المجموعة / the group heading)
Field       : `ENT-MDL-001.ownerModuleCode`
Owner       : SEC — `ModuleRegistry` (ENT-SEC-004)، وهو الكيان الوحيد الذي تستهلكه MDL (SRS §A8) / the one entity MDL consumes
Real API    : `POST /api/v1/sec/registry/search` (API-SEC-021) — سجل وحدات الأمان، شكله ومعرّفه من مواصفة تلك الوحدة لا من هنا / the security module's registry search; its shape and its id belong to that module's own artifacts
Grant       : كل دور يُمنح `PERM_MDL_LOOKUPS_CREATE` يلزمه `PERM_SEC_MODULE_REGISTRY_VIEW` أيضًا / every role granted `PERM_MDL_LOOKUPS_CREATE` must also hold `PERM_SEC_MODULE_REGISTRY_VIEW` — منحٌ تملكه وحدة الأمان، يُسمّى هنا ولا يُنشأ (ADR-MDL-013) / SEC's grant to make; named here, minted nowhere
Control     : قائمة اختيار على رموز الوحدات المسجَّلة، لا حقل نصّ حرّ (ADR-MDL-004) / a select over the registered module codes, never a free-text field
Degraded    : القراءة مرفوضة أو متعذّرة → القائمة فارغة ومعطَّلة برسالة تسمّي القراءة الناقصة، وإجراء الإنشاء معطَّل خلفها؛ ولا رجوع إلى النصّ الحرّ / a refused or failed read leaves the select empty and disabled with a message naming the missing read, and the create action disabled behind it — never a fall back to free text (ADR-MDL-013)

`UXD-MDL-001` هو حاجة في طبقة التطبيق لا قيد قاعدة بيانات: شاشة تملكها هذه الوحدة تعرض بيانات
مرجعها الموثوق وحدةٌ أخرى / an application-layer need, not a DB constraint: a screen this module
owns displays data whose authoritative source is another module's real API. لا يشترك في شيء مع
`XM-MDL-001` (وهو فحص السلامة المرجعية في الخادم عند الإنشاء — RULE-MDL-001) ولا يظهر في أي أثر
خلفي / it shares nothing with the backend's cross-module record, which is the server-side
existence check behind RULE-MDL-001, and it appears in no backend artifact.

واحد لا اثنان: التبعية نفسها والخطّاف نفسه يخدمان الشاشتين، فلا يُمنت `UXD-*` لكل شاشة / ONE, not
one per screen: the same dependency and the same shared hook serve both screens.

**لا تبعية عرض ثانية** / no second display dependency: MDL لا تملك مفتاح قائمة قيم ولا تستهلك أيًّا
منه (SRS §A6)، فلا حقل على أي من الشاشتين مسنود بقائمة قيم، ولا قيمة مُرمَّزة تُقرأ من وحدة أخرى /
MDL owns no lookup key and consumes none, so no field on either screen is backed by a list of
values and no coded value is read from another module.

---

## RECONCILIATION — MDL v1

```
RECONCILIATION — MDL v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)
   ✓ US-MDL-001, US-MDL-002, US-MDL-004, US-MDL-005 — each named by the flow that uses it and
     each carried by a REQ and a screen requirement of the SRS. US-MDL-003 is used by no flow:
     its caller has no screen (ADR-MDL-007), so no screen was invented for it.
B2 no RULE-* contradicts a flow or a spec outcome
   ✓ RULE-MDL-001 (owner module registered) → the create form's owner-module field and its
     server message · RULE-MDL-002 (no duplicate code within a type) → the value form's code
     field · RULE-MDL-003 (the key is immutable) → `key` read-only on edit, stated beside it ·
     RULE-MDL-004 (an inactive type hides its values from consumers) → said in the deactivate
     confirmation, and NOT applied to this screen's own value list, which is the manager's view
     and shows inactive rows (AC-MDL-005, AC-MDL-009). No contradiction; no ADR needed.
B3 every field and permission on a screen exists in the SRS
   ✓ extra: none — every field above is an SRS §A3 field of the owning entity and every
     permission is an SRS §B4 row. missing: none — no field of either entity is dropped, the
     audit four included, and they are marked system-filled rather than hidden.
B4 every screen entry of the SRS has exactly one SCR-* block
   ✓ SCR-REQ-MDL-001 → SCR-MDL-001 · SCR-REQ-MDL-002 → SCR-MDL-002. Two screen requirements,
     two SCR blocks, and the sub-views of each stay under their one SCR.
RESULT  reconciled 2 · reworked 0 · ADRs ADR-MDL-013 (new, the grant that travels with
        UXD-MDL-001); applied unchanged: ADR-MDL-003, ADR-MDL-004, ADR-MDL-005, ADR-MDL-006,
        ADR-MDL-007
```

لا سؤال في هذه المرحلة ولا قرار بحالة BLOCKED / no question is raised at this stage and no ADR is
BLOCKED: كل نقطة احتملت وجهين حسمها مدخلٌ قائم أو قرار مُسجَّل / every two-sided point was settled
by an existing input or a recorded decision.
══════════════════════════════════════════════════════════════════

<<<END ARTIFACT>>>
