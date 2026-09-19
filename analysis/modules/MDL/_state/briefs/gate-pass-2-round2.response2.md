```json
{
  "gate": "pass-2",
  "module": "MDL",
  "version": 1,
  "scores": { "unambiguous": 2, "verifiable": 2, "complete": 1, "consistent": 1, "singular": 3, "feasible": 2, "traceable": 3 },
  "extra_checks": [],
  "analyze_confirmed": [
    {"finding": "C5.16 / srs:47 / feature-unwanted US-MDL-005", "confirmed": true,
     "note": "Not a false positive. REQ-MDL-013 is the story's only requirement and is an `event` statement; SRS B2 states the groups come back whole with no state filter, and no statement anywhere in the pass says what the registry returns when the owner filter names a code no active type carries. The fix belongs to the SRS (P1), outside this pass's editable scope; this pass's artifacts inherit it rather than cause it."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "artifact": "frontend-execution-plan", "line": null,
     "clause": "GOVERNANCE-CORE.md §1 (upstream wins)",
     "problem": "The client field constraints contradict the deployed precisions in six places: F1 and F3 state `key : maxLength 80` (SCR-MDL-001 create and the SCR-MDL-002 key filter) and `nameAr/nameEn : maxLength 150` on both entities, while db-script §1 and ADR-MDL-010 declare VARCHAR(50) for `key` and `code` and VARCHAR(200) for every name column, and backend-execution-plan API-MDL-002/003/006/007 state maximum 50 and maximum 200 on every Request line. The consequence runs both ways: a 51-80 character key passes client validation and is refused by the database, and a 151-200 character label the platform accepts is refused by the client. This is one rule breached six times, not six defects — F3's own preamble says the constraints are 'the field constraints the published DTOs state', and the numbers state something else.",
     "fix": "Set `key` and `code` to maxLength 50 and every `nameAr`/`nameEn` to maxLength 200 in F1-MODEL (both entities), F3-FIELD (all four blocks) and the SCR-MDL-002 key filter, sourcing each from ADR-MDL-010 by citation rather than a restated number. If `_inputs/api-docs-mdl.md` genuinely publishes 80/150, the numbers are a backend-repo defect: record an OPEN platform-findings row against the MDL api-doc generator with the db-script column widths as evidence, and bind the plan to the db-script meanwhile.",
     "adr": false},

    {"id": "G2", "severity": "MAJOR", "artifact": "frontend-execution-plan", "line": null,
     "clause": "C9.2 / GOVERNANCE-CORE.md §1",
     "problem": "The detail read is modelled as paginated and no upstream artifact says it is. F1-SCREEN gives the detail search model `page, size, sortField, sortDirection`; F2 heads API-MDL-005 'paginated response' and puts page and size in its cache key; registry-exec-fe lists API-MDL-005 under 'paginated (PageLookup<T>)'. Against that, SRS B2 states the detail is shown whole because it is confined to one type, backend-execution-plan API-MDL-005 states 'No paging: the result is bounded by one type and returned whole, ordered', and QR-MDL-005 records `Pagination: NO`. ADR-MDL-002 shows this pass knows how to record a divergence between the published surface and a plan; this one is recorded nowhere, so a reader cannot tell whether the api-docs page this endpoint or whether the shape was assumed from API-MDL-001's neighbour.",
     "fix": "Resolve against `_inputs/api-docs-mdl.md` and state the answer once. If the endpoint is unpaged, drop page/size/sort from the F1 detail search model, from the F2 cache key and from the registry's RESPONSE SHAPES table, and move API-MDL-005 to the bare-array row. If it is paged, record the divergence as an ADR in the form ADR-MDL-002 uses, naming SRS B2 and backend-execution-plan API-MDL-005 as the artifacts that lag, so the gate sees a declared diff rather than a silent one.",
     "adr": false},

    {"id": "G3", "severity": "MAJOR", "artifact": "frontend-execution-plan", "line": null,
     "clause": "QUALITY-RUBRIC.md §2 complete",
     "problem": "The reorder can submit a partial set, and the plan asserts the opposite is impossible. F2 says 'The whole ordered set is submitted, never one row's new position: the endpoint takes `orderedValueIds[]`, and the mismatch code exists precisely because a partial set is wrong.' MDL-400-REORDER-MISMATCH does not mean that: QR-MDL-009's predicate is `lookup_value_pk = :id AND lookup_type_id = :typeId`, so the code fires only when a submitted id belongs to another type. A subset of the correct type is accepted, each submitted id is written its position in the submitted list, and those ranks collide with the ranks of the values that were not submitted. The screen can produce exactly that subset: the detail pane carries a `code` LIKE filter (F1, F3) and, per G2, a page and size, so dragging inside a filtered or paged pane submits part of the type's values and re-ranks them from 1.",
     "fix": "State in F2-QUERY VALUE REORDER and in F4 that the submitted list is the type's complete value set, and disable the drag affordance whenever the pane shows less than that — a non-empty `code` filter, or a page other than the whole result — with the reason shown on the list. Add the counter-case as its own line: a reorder submitted from a filtered pane is refused by the client, because the server cannot distinguish it from a deliberate whole-set submission.",
     "adr": false},

    {"id": "G4", "severity": "MAJOR", "artifact": "frontend-execution-plan", "line": null,
     "clause": "QUALITY-RUBRIC.md §2 feasible",
     "problem": "The deep-linked entry routes cannot be built as specified. F4 registers `/reference-data/lookups/:typeId/edit`, `/values/new` and `/values/:valueId/edit` and states 'a deep link opens it'; ADR-MDL-005 states both entry sub-views 'hydrate from the row the search query already holds' and that no by-id read is published. On a cold load there is no search result in cache, and no read can produce one: the published filter set for API-MDL-001 is key, ownerModuleCode, name and isActiveFl, and for API-MDL-005 it is lookupTypeId and code — neither carries the record's own id, so even a one-row search cannot resolve `:typeId` or `:valueId`. The editor opens empty on the one entry path F4 explicitly promises.",
     "fix": "State the cold-load behaviour on each entry route: when the hydrating row is absent from cache, redirect to the parent surface — `/reference-data/lookups` for a type editor, `/reference-data/lookups/:typeId` for a value editor — and show the localized 'open the record from the list' message rather than rendering an empty form. Best practice over the alternatives because it needs no unpublished endpoint and no invented filter, and it keeps the route addressable for the in-session case F4 was written for. Record the underlying gap where its owner can act: an OPEN platform-findings row that the published search envelopes carry no id operator, which is what makes by-id hydration impossible for every module that omits a by-id read.",
     "adr": true},

    {"id": "G5", "severity": "MAJOR", "artifact": "frontend-execution-plan", "line": null,
     "clause": "REGISTRY-SCHEMA.md §4 (platform findings)",
     "problem": "The pass found a cross-artifact permission divergence and answered it with a paragraph instead of a row. SEC-FE states: 'The api-docs put `PERM_MDL_LOOKUPS_UPDATE` on both deactivate endpoints, so no published endpoint of this module requires `PERM_MDL_LOOKUPS_DELETE`' — against backend-execution-plan PHASE 7, whose matrix marks the DELETE column for API-MDL-004 and API-MDL-008 and whose BOOTSTRAP DATA seeds that permission with grant targets. The consequence is a privilege widening, not a naming detail: as built, a caller holding UPDATE alone can deactivate, and a caller granted DELETE alone cannot. 'Recorded, not corrected — the permission an endpoint requires is the backend's to state' is the correct scope judgement and the wrong disposal: a module-scoped worker saying 'not mine to settle' into prose is what the platform-findings category exists to stop.",
     "fix": "File an OPEN platform-findings row: owner = the MDL backend track (P3.1 plus the backend repo's method-level security annotations); evidence = backend-execution-plan PHASE 7 permission matrix and BOOTSTRAP DATA versus the api-docs' stated requirement on API-MDL-004 and API-MDL-008; impact = UPDATE grants deactivation and DELETE grants nothing, so the SRS B4 DELETE row is unenforceable as built. Keep the SEC-FE paragraph as the frontend's reading and cite the row's id from it.",
     "adr": false},

    {"id": "G6", "severity": "MAJOR", "artifact": "frontend-execution-plan", "line": null,
     "clause": "QUALITY-RUBRIC.md §2 complete",
     "problem": "The owner-module filter on SCR-MDL-002 has no stated behaviour when its source read fails. F3-FIELD SCR-MDL-002 makes it a select over the UXD-MDL-001 list; ADR-MDL-013 states the degraded path for that screen only as 'the group headings showing the bare code the browse already returns' and asserts 'browsing is never blocked by it'. Both hold for the headings and neither holds for the filter: a select over an empty list cannot be used, so the EXACT owner filter SRS B2 requires — the one filter the screen exists for — silently becomes unavailable while the plan says nothing is blocked. F2-SCREEN-INIT repeats the heading-only wording.",
     "fix": "State the filter's degraded source in F3-FIELD SCR-MDL-002 and in ADR-MDL-013: when the UXD-MDL-001 read is refused or fails, the owner select falls back to the distinct `ownerModuleCode` values present in the API-MDL-010 response, which the browse already returns on every group. Best practice over the alternatives because it needs no foreign grant, keeps the SRS B2 filter usable, and cannot offer a code the registry does not hold — narrower than free text, which would let a reviewer filter for a code no group carries.",
     "adr": true},

    {"id": "G7", "severity": "MAJOR", "artifact": "ui-ux-spec", "line": null,
     "clause": "QUALITY-RUBRIC.md §2 complete",
     "problem": "Deactivating a value permanently consumes its code under that type, and nothing in the pass says so. `UQ_MDL_LOOKUP_VALUE_TYPE_CODE` is (lookup_type_id, code) over every row, active or not (db-script BLOCK 5b), and no activate endpoint exists (ADR-MDL-005), so a code deactivated by mistake can never be re-created under its type through any surface this plan draws. The deactivate confirmation states only that the act 'is not reversible from this screen'; a user who then recreates the value is answered by MDL-409-VALUE-DUP routed inline to `code`, whose text — 'This code is already used within this type' — reads as a collision with a live value and offers no route out. The same closure question for a type is answered the same way: `key` is platform-unique forever and equally unrecoverable.",
     "fix": "Name the consequence where the act is taken and where it is met. In F4 and in ui-ux-spec SCR-MDL-001, the value deactivate confirmation states that the code stays reserved under this type and cannot be reused while no activate endpoint exists; in F2-QUERY VALUE CREATE, the MDL-409-VALUE-DUP inline routing adds that a deactivated value of the same code may be holding it, with the list's own inactive row as the evidence the user can see. Add the same sentence to the type deactivate confirmation for `key`.",
     "adr": false},

    {"id": "G8", "severity": "MINOR", "artifact": "ADR-MDL-008", "line": null,
     "clause": "GOVERNANCE-CORE.md §1 (ADR status)",
     "problem": "The status is stale. ADR-MDL-008's header reads 'Status: ACCEPTED (non-breaking) — supersedes ADR-MDL-001', while ADR-MDL-011's header and registry-exec-fe both record it as superseded ('Superseded, kept on disk and cited only by the decisions that replaced them: ADR-MDL-001, ADR-MDL-008'). A reader who opens the file alone is told a withdrawn decision is in force, and its content — plan-local labels, an ALIGN API row written '— examined nothing' — contradicts the plan as delivered.",
     "fix": "Rewrite the header the way ADR-MDL-001 already does it: 'Status : SUPERSEDED by ADR-MDL-011 (was ACCEPTED, non-breaking)', with the one-line note that the Contract ID lines it asked the backend for have since landed.",
     "adr": false},

    {"id": "G9", "severity": "MINOR", "artifact": "ADR-MDL-005", "line": null,
     "clause": "GOVERNANCE-CORE.md §1 (a decision follows its cited source)",
     "problem": "The Context table cites an SRS line the current SRS does not contain. It attributes 'Buttons: activate/deactivate per row at both levels' to B3; SRS B3 in this pass's state reads «لا إجراء تفعيل ولا محو نهائي على أي من المستويين» — no activate action at either level — and A2 puts reactivation out of scope. The frontend plan already notices this ('This SRS version names no `activate` action at either level … so the half-toggle ADR-MDL-005 also covers is not a gap in this version'), which leaves the ADR as the only artifact asserting the quotation.",
     "fix": "Drop the `activate` row from ADR-MDL-005's Context table, or mark it explicitly as a quotation from a superseded SRS revision, and keep the decision on the two by-id reads, which SRS B1 Operations still names. The plan's compensating sentence can then cite the corrected ADR instead of correcting it in passing.",
     "adr": false},

    {"id": "G10", "severity": "MINOR", "artifact": "frontend-execution-plan", "line": null,
     "clause": "QUALITY-RUBRIC.md §2 complete",
     "problem": "The optimistic reorder has no stated failure path. F2-QUERY VALUE REORDER holds 'the dragged order held until the call settles' and routes MDL-400-REORDER-MISMATCH to 'a user message on the value list', but never says what the list shows afterwards. The invalidation line is unconditional, so an implementer can read it as firing on failure too, and the user is left looking at a dragged order the server did not store.",
     "fix": "Add one line to the block: on any non-2xx answer the pending order is discarded and the list re-renders from the last persisted response; invalidation of `[lookup-values, *]` runs on success only.",
     "adr": false},

    {"id": "G11", "severity": "MINOR", "artifact": "frontend-execution-plan", "line": null,
     "clause": "QUALITY-RUBRIC.md §2 singular",
     "problem": "F1-SCR-MDL-002 models `LookupValueResponse[]` for the consumer read 'for completeness of the published surface', and states in the same block that no component renders it. The F2 block for API-MDL-011 already accounts for that surface, with its caller, its error answers and `Cache key : n/a` — so the F1 entry instructs an implementer to write a type nothing in the delivered frontend consumes.",
     "fix": "Remove the F1-MODEL for the consumer read and leave the accounting to the F2 block ADR-MDL-007 points at; if the model is kept, say in the block that it documents a foreign contract and is not to be emitted as a client type.",
     "adr": false}
  ],
  "adrs_reviewed": [
    {"id": "ADR-MDL-001", "status_ok": true, "note": "SUPERSEDED by ADR-MDL-008, stated in the header with the prior status preserved. Its reasoning — that an annex written into a generated input would be the factory certifying its own ids — is still sound and is why the chain ends where it does."},
    {"id": "ADR-MDL-002", "status_ok": true, "note": "ACCEPTED. Names the three affected API ids and the artifact that lags; SRS B5 and the api-docs agree with each other, so the backend plan's contract summary is correctly identified as the stale one. No downstream artifact ignores it — every F2 read block binds POST …/search."},
    {"id": "ADR-MDL-003", "status_ok": true, "note": "ACCEPTED. FULL_PAGE for a screen with nothing in its detail pane is followed by F1, F4 and registry-exec-fe without exception."},
    {"id": "ADR-MDL-004", "status_ok": true, "note": "ACCEPTED. The rule that the plan cites UXD-MDL-001 and never a foreign API id holds throughout; the foreign endpoint appears only in ui-ux-spec, as stated. Its degraded-read clause is incomplete for the SCR-MDL-002 filter — G6, filed against ADR-MDL-013, where that clause now lives."},
    {"id": "ADR-MDL-005", "status_ok": true, "note": "ACCEPTED, and the decision is right; its Context cites an SRS line that no longer exists (G9). Its hydration clause is what G4 breaks against on a cold route load."},
    {"id": "ADR-MDL-006", "status_ok": true, "note": "ACCEPTED. `isActiveFl` read-only at both levels is carried by F1, F3 and ui-ux-spec, and matches the published write DTOs and SRS B3."},
    {"id": "ADR-MDL-007", "status_ok": true, "note": "ACCEPTED. API-MDL-011 bound and drawn by no screen is applied consistently in F1, F2, F4, ALIGN-FE, registry-exec-fe and the flow diagram's 'a story with no navigation path'. The honest 11/13 req-ux ratio follows from it rather than being worked around."},
    {"id": "ADR-MDL-008", "status_ok": false, "note": "Header still reads ACCEPTED while ADR-MDL-011 and registry-exec-fe record it superseded — G8. Not BLOCKED, so the pass is not stopped."},
    {"id": "ADR-MDL-009", "status_ok": true, "note": "ACCEPTED (P2), applied not re-decided; touches no artifact of this pass."},
    {"id": "ADR-MDL-010", "status_ok": true, "note": "ACCEPTED (P2). It is the authority G1's precisions contradict: this pass cites it nowhere, which is how six local numbers drifted."},
    {"id": "ADR-MDL-011", "status_ok": true, "note": "ACCEPTED, raised this run. The re-fetched api-docs carrying a Contract ID per endpoint is verifiable in the plan's own digest line, and every F-block cites an API-MDL-* id accordingly. Supersession of ADR-MDL-008 is recorded on this side; the other side is G8."},
    {"id": "ADR-MDL-012", "status_ok": true, "note": "ACCEPTED, raised this run. The distinction between the readable page gate and the unreadable action grants is applied uniformly in F2, F3, F4 and SEC-FE, and access narrows on a failed menu load rather than widening."},
    {"id": "ADR-MDL-013", "status_ok": true, "note": "ACCEPTED, raised this run. Naming PERM_SEC_MODULE_REGISTRY_VIEW as a grant that travels with PERM_MDL_LOOKUPS_CREATE, and minting nothing, is the correct disposal of a cross-module grant dependency. Its degraded clause covers SCR-MDL-002's headings but not that screen's filter — G6."}
  ],
  "verdict": "REVISE"
}
```

<!-- CONVERGED -->