<!-- source: PHASE:F4 / SUB:F4-SCR-MDL-002 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-MDL-011, AC-MDL-012, AC-MDL-013, REQ-MDL-011, REQ-MDL-012, REQ-MDL-013, SCR-MDL-002, UXD-MDL-001 -->
<!-- SUB:F4-SCR-MDL-002:START traces=REQ-MDL-011,REQ-MDL-012,REQ-MDL-013,AC-MDL-011,AC-MDL-012,AC-MDL-013,UXD-MDL-001,SCR-MDL-002 -->
### F4 · SCR-MDL-002 — سجل أنواع اللوكب حسب المالك / Lookup-type registry by owner

#### F4-SCREEN — SCR-MDL-002
Traces       : REQ-MDL-011, REQ-MDL-012, REQ-MDL-013, AC-MDL-011, AC-MDL-012, AC-MDL-013,
               UXD-MDL-001
Routes       : base slug `type-registry`, under `/reference-data` —
               `/reference-data/type-registry` — the only route; no `new`, no `:id`, no
               `:id/edit`, because this screen addresses no record it could edit. The owner
               module and the key filter live in the route's search params, so the browse IS
               its address
Chunk        : one lazy chunk for this screen
Guard        : the route element guarded by `PERM_MDL_TYPE_REGISTRY_VIEW`, evaluated as
               "`MDL_TYPE_REGISTRY` is present in the caller's effective menu"
Components   : `TypeRegistryPage` (route-level, FULL_PAGE) · `RegistryFilters`,
               `OwnerGroupSection`, `RegistryTypeTable` (presentational)
Mode         : not applicable — no CREATE, EDIT or VIEW mode exists to resolve; this screen
               writes nothing
Facade       : the SCR-MDL-002 facade of F2
Composition  : the spec's `Composition` line resolved to components — `none`. There is no form,
               no picker and no child-row editor: `OwnerGroupSection` renders the groups the
               server returns, `RegistryFilters` writes the route's search params, and no
               second level is opened from this screen. Its one affordance, the link into
               SCR-MDL-001, is a route change
Saves        : none — this screen writes nothing, so it renders no save affordance at all
Shared UI    : filter bar, select (the owner module), section headings, data table, localized
               message banner
Cross-module : UXD-MDL-001 — here it is the grouping itself, not a field of a form
Each type row links to `/reference-data/lookups/:typeId`, which is SCR-MDL-001's own route and
carries its own guard — reviewing and managing are two steps of one task, and this screen does
neither half of the second. The consumer read-by-key endpoint has no component and no route
here (ADR-MDL-007).
<!-- SUB:F4-SCR-MDL-002:END -->
