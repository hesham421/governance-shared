<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND EXECUTION PLAN — البيانات المرجعية / Master Data Lookup (MDL)
══════════════════════════════════════════════════════════════════
Module : MDL   Version : v1   Profile : erp   Track : frontend
Framework : react-ts-vite (profile.stack.frontend.framework) · routing react-router ·
            server-state tanstack-query · forms react-hook-form · validation zod ·
            state zustand · one lazy chunk per composite screen
Inputs : srs (v1, PRD-approved), prd (v1), api-docs (v1, re-fetched — the published surface),
         registry-srs (v1), registry-exec-be (v1)
Screens : 2 — SCR-MDL-001..002 · UXD : 1 — UXD-MDL-001 · endpoints bound : 11 / 11 published
ADRs : ADR-MDL-002 … ADR-MDL-008 (all ACCEPTED, all non-breaking; ADR-MDL-001 superseded
       by ADR-MDL-008)
══════════════════════════════════════════════════════════════════

## API SURFACE — MDL v1   (source: `_inputs/api-docs-mdl.md` — the ONLY endpoint source)

```
ENDPOINTS   11, bound by the identifiers the published document itself carries — the verb and
            the path (and, beside them, the generator's own operation id). It carries no
            governance `API-*` id: unlike the security and finance modules, whose published
            documents print a `Contract ID` line per endpoint, MDL's generator prints none, so
            no `API-*` id resolves in this document and none is cited in this plan
            (ADR-MDL-008, which supersedes ADR-MDL-001). The ids exist — the backend registry
            registers eleven of them — and the id ↔ endpoint binding is kept in
            `registry-exec-fe-mdl.md` and in ADR-MDL-008, where nothing reads it as a claim
            about the published surface.
ENVELOPE    every response is wrapped in `ApiResponse<T>` { success, data, error { code,
            message, fieldErrors[] }, timestamp }. Paging constraints (PageableBuilder):
            default page 0 · default size 20 · maximum size 200. THREE response shapes, and
            the difference matters to every F2 block:
              · `PageLookup<T>` — the two paged searches alone (types, values)
              · a bare array — the reorder response, the by-owner groups and the consumer read
              · a single object — create / update / deactivate at either level
            Two reads and one browse are POST `…/search` with a `filters[] {field, operator,
            value}` envelope rather than GET with query params (operators EQUALS, NOT_EQUALS,
            LIKE, GREATER_THAN, GREATER_THAN_OR_EQUAL, LESS_THAN, LESS_THAN_OR_EQUAL, IN) —
            which is what the SRS B5 tables state too; only the backend plan's contract table
            still predicts GET (ADR-MDL-002). The by-owner request carries `filters` alone,
            with no sort and no paging.
CODES       business codes, each already carrying its ar/en text in the module's catalog, and
            each linked here to the RULE it belongs to — the link neither the api-docs nor the
            SRS carries: MDL-409-MODULE-NOT-REGISTERED (409) → RULE-MDL-001 ·
            MDL-409-VALUE-DUP (409) → RULE-MDL-002 · MDL-404-TYPE-KEY (404) → REQ-MDL-012 ·
            MDL-409-TYPE-DUP (409) → the type key's uniqueness (no RULE-* states it; the
            surface does) · MDL-400-REORDER-MISMATCH (400) → REQ-MDL-010 · MDL-404-TYPE,
            MDL-404-VALUE (404) → a row that is gone. RULE-MDL-003 and RULE-MDL-004 have no
            runtime code of their own and cannot: the first is expressed by a field the update
            request does not carry, the second by what the consumer read leaves out.
            Framework codes: VALIDATION_ERROR (400) · DATA_INTEGRITY_VIOLATION (409) ·
            ACCESS_DENIED (403) · INTERNAL_ERROR (500). Routing is uniform across every F2
            block: field validation → inline · business rule → user message · unauthenticated
            → login · forbidden → the localized forbidden message · server → generic.
LOOKUPS     **none.** SRS §A6 records that MDL introduces no coded list of its own — it is the
            mechanism every other module's lists run on. No field on either screen is a
            lookup-backed code, no lookup hook exists anywhere in this plan, and no enum is
            modelled. The one cross-module dependency, UXD-MDL-001, is the owner-module field,
            whose valid set the security module owns (ADR-MDL-004) — not lookup data, and not
            read through the consumer read-by-key endpoint.
PERMISSIONS declared by the backend and read from the SRS Access summary and the published
            api-docs, never redeclared here: PERM_MDL_LOOKUPS_VIEW / _CREATE / _UPDATE /
            _DELETE · PERM_MDL_TYPE_REGISTRY_VIEW.
            No MDL endpoint publishes the caller's own permission set: the screen gate is the
            security module's effective menu (SEC-FE below), and no `PERM_*` name is composed
            at runtime.
```

**BINDING — REQ → the published endpoint.** The binding only; verb, path, request and
response DTO, paging and envelope are read in `_inputs/api-docs-mdl.md`, not copied here. The
label in the first column is this plan's own short name for the endpoint and is used by every
F-block below; it is a reading aid, never an identifier the published surface would recognise.

| Label | Serves (REQ) | Published verb · path | operationId | Response shape |
|---|---|---|---|---|
| TYPE-SEARCH | REQ-MDL-001, REQ-MDL-013 | POST `/api/v1/mdl/lookup-types/search` | `search` | paged |
| TYPE-CREATE | REQ-MDL-001, REQ-MDL-002 | POST `/api/v1/mdl/lookup-types` | `create` | single |
| TYPE-UPDATE | REQ-MDL-003 | PUT `/api/v1/mdl/lookup-types/{id}` | `update_1` | single |
| TYPE-DEACTIVATE | REQ-MDL-004 | DELETE `/api/v1/mdl/lookup-types/{id}` | `deactivate_1` | single |
| VALUE-SEARCH | REQ-MDL-005 | POST `/api/v1/mdl/lookup-types/values/search` | `search_1` | paged |
| VALUE-CREATE | REQ-MDL-006, REQ-MDL-007 | POST `/api/v1/mdl/lookup-types/{id}/values` | `create_1` | single |
| VALUE-UPDATE | REQ-MDL-008 | PUT `/api/v1/mdl/lookup-values/{id}` | `update` | single |
| VALUE-DEACTIVATE | REQ-MDL-009 | DELETE `/api/v1/mdl/lookup-values/{id}` | `deactivate` | single |
| VALUE-REORDER | REQ-MDL-010 | PATCH `/api/v1/mdl/lookup-types/{id}/values/reorder` | `reorder` | bare array |
| REGISTRY-BY-OWNER | REQ-MDL-013 | POST `/api/v1/mdl/lookup-types/by-owner/search` | `browseByOwner` | bare array |
| CONSUMER-READ-BY-KEY | REQ-MDL-011, REQ-MDL-012 | GET `/api/v1/mdl/lookups` | `readByKey` | bare array |

UNMAPPED — a REQ needing an endpoint that has none: none of the thirteen. A documented
endpoint mapping to no REQ: none of the eleven. Operations the SRS *names* with no endpoint
published are a different list and are in ADR-MDL-005.

### Reconciliation against the SRS — run once, before any F-content

- **Every REQ that needs an endpoint has one.** REQ-MDL-001..013 map onto the eleven published
  endpoints with no gap; the mapping is the BINDING table above and the traces of the F2
  blocks below.
- **Every documented endpoint maps to a REQ.** All eleven are bound; none is unknown and none
  is used without a REQ behind it.
- **No governance id is published.** The document carries no `API-*` id, so this plan cites
  none: a cited id that resolves in no published document is a claim about a surface nobody
  published. Naming, not shape — nothing about the endpoints changes — so it is non-breaking
  and the run continues (ADR-MDL-008).
- **Naming and shape differences** — three reads are POST `…/search` where the backend plan's
  contract table predicted GET. The api-docs and the SRS B5 tables agree with each other; the
  backend plan is the one artifact that lags, and `gov.py analyze` reports it there as C8.4.
  The frontend binds to the published shape (ADR-MDL-002).
- **Operations the SRS names with no published endpoint** — `activate` at either level, and
  the by-id reads of a type and of a value. None is required by a `REQ-*`, so none is
  breaking; each is omitted rather than faked (ADR-MDL-005).
- **Endpoints published but not called by this frontend** — CONSUMER-READ-BY-KEY alone, the
  read a consuming module's backend performs over the platform's in-process module interface
  (ADR-MDL-007). It is bound and blocked out in F2 below.
- **One screen's form differs from its SRS Part B input list** — SCR-MDL-001 renders
  `isActiveFl` read-only at both levels although B3 lists it among the detail inputs; no
  published write DTO accepts it and the deactivate endpoints are what change it
  (ADR-MDL-006).
- **Nothing is invented.** No value absent from the api-docs appears in this plan, and no
  permission name, route or component is derived from anything but the SRS, the api-docs and
  the profile's own stack.

## EXECUTION PLAN INDEX — MDL v1 — frontend-execution-plan-mdl.md

| # | Phase | Split | Blocks |
|---|---|---|---|
| 1 | F1 — Models & Types | always — one SUB per screen | 2 SUB |
| 2 | F2 — Data Hooks | always — one SUB per screen | 2 SUB |
| 3 | F3 — Forms & Validators | always — one SUB per screen | 2 SUB |
| 4 | F4 — Screens & Routes | always — one SUB per screen | 2 SUB |
| 5 | SEC-FE | never split | level-1 only |
| 6 | ALIGN-FE | never split | level-1 only |

**SCREEN REGISTRY**

| SCR | Name (ar / en) | Page code | Container pattern | Composition | Owning ENT |
|---|---|---|---|---|---|
| SCR-MDL-001 | اللوكبات العامة / Generic Lookups | MDL_LOOKUPS | TREE_MASTER_DETAIL | owner select inline · value collection at a second level · one save per entry | ENT-MDL-001 نوع اللوكب / LookupType (+ ENT-MDL-002 قيمة اللوكب / LookupValue) |
| SCR-MDL-002 | سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner | MDL_TYPE_REGISTRY | FULL_PAGE (no entry sub-view — ADR-MDL-003) | none — a read-only browse, no save | ENT-MDL-001 نوع اللوكب / LookupType |

Two screens, and every `sub_bearing` phase still splits per screen — one `SUB` per `SCR-*`,
at any screen count. The four phases below therefore carry two SUB blocks each, and every SUB
id is phase-qualified: `SUB:F1-SCR-MDL-001` and `SUB:F2-SCR-MDL-001` are distinct blocks for
the same screen under different phases.













---

## Hand-off

The implementer reads the phases in profile order — F1 models, F2 hooks, F3 forms, F4 screens
and routes, SEC-FE guards — takes design intent from `ui-ux-spec-mdl.md`, and takes every
request and response shape from `_inputs/api-docs-mdl.md`, matching each F2 block to its
endpoint by the verb and path in the block's own heading. No route, component, permission or
field that is not traceable to an F-block above is invented: a gap is an ADR in
`analysis/decisions/MDL/`, never an invention. Three response shapes travel in this module and
the difference is load-bearing — two paged reads, three bare arrays, six single objects — so no
block may be read through an envelope another block declares. The plan and its registry are
split by the toolkit into `/frontend-execution/` inside the shared repo after the `gate:pass-2`
verdict, then tagged `mdl-v1`; nothing is copied anywhere, and the implementer reads it where
it was written, at the commit its own repo pins.

══════════════════════════════════════════════════════════════════
