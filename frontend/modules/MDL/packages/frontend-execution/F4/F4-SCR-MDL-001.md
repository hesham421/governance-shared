<!-- source: PHASE:F4 / SUB:F4-SCR-MDL-001 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-MDL-005, AC-MDL-009, AC-MDL-010, API-MDL-001, API-MDL-002, API-MDL-003, API-MDL-004, API-MDL-005, API-MDL-006, API-MDL-007, API-MDL-008, API-MDL-009, REQ-MDL-001, REQ-MDL-002, REQ-MDL-003, REQ-MDL-004, REQ-MDL-005, REQ-MDL-006, REQ-MDL-007, REQ-MDL-008, REQ-MDL-009, REQ-MDL-010, SCR-MDL-001, UXD-MDL-001 -->
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
               403 is the authority on the write itself (ADR-MDL-012, PF-MDL-001)
Facade       : the SCR-MDL-001 facade of F2 — the page never calls a query directly
Components   : `LookupsPage` (route-level, TREE_MASTER_DETAIL) · `LookupTypeList`,
               `LookupTypeFilters`, `LookupTypeForm`, `LookupValuePane`, `LookupValueRow`,
               `LookupValueFilters`, `LookupValueForm`, `ValueReorderHandle`, `DeactivateConfirm`
               (presentational)
Mode         : CREATE | EDIT | VIEW resolved from the route match — `/new` and `/values/new` →
               CREATE, `/edit` → EDIT, `/:typeId` → VIEW — never from a parent's prop
Cold-load hydration (ADR-MDL-014) : the two edit routes and the two entry sub-views hydrate their
               form from the row their own search query already holds (ADR-MDL-005). No by-id
               read is published for either entity (PF-MDL-003: the search filter sets of
               API-MDL-001 and API-MDL-005 carry no id operator), so on a cold load — the
               hydrating row absent from cache, as when a link is opened directly rather than
               navigated to — the route redirects instead of rendering an empty form:
               `/reference-data/lookups/:typeId/edit` and `/reference-data/lookups/:typeId/values/new`
               and `/values/:valueId/edit` redirect to `/reference-data/lookups/:typeId`; if the
               type itself is not in cache either, to `/reference-data/lookups`. The destination
               shows the localized message — ar: «افتح السجل من القائمة» ·
               en: "Open the record from the list" — rather than a blank editor. This needs no
               unpublished endpoint and no invented filter, and it keeps every route addressable
               for the in-session case it was written for.
Composition  : the spec's line resolved to components. Nothing is inline: `LookupValuePane` is not
               a control inside `LookupTypeForm`, and the type's write carries no values, so the
               type form has nothing of the values in it. Each value is a **summary row**
               (`LookupValueRow` — code, both names, order, state) and its editor is the **second
               level**: `LookupValueForm` is a SIBLING of `LookupValuePane`, never rendered inside
               its element; it is opened from the route (`/values/new`, `/values/:valueId/edit`),
               so back closes it, dismissing closes only it and a deep link opens it, subject to
               the cold-load redirect above; it carries no scroll region of its own — the pane
               scrolls with the body it sits in
Saves        : ONE per open surface, and one surface at a time. `LookupTypeForm` submits once
               (API-MDL-002 or API-MDL-003); `LookupValueForm` submits once (API-MDL-006 or
               API-MDL-007) for its own record. The two are never open together: a value route is
               a state of the detail level, not a panel beside the open type form. **No action of
               this screen owns two calls**, so no ordered pair and no skip-if-unchanged rule
               arises. Deactivate and reorder are direct actions over a row and over the list;
               neither is a form and neither carries a second submit. The reorder handle is
               disabled whenever the detail pane's `code` filter is non-empty (G3, F2-QUERY VALUE
               REORDER)
Cross-module : UXD-MDL-001 — the owner-module select on the type entry, the owner-module filter
               on the master list, and the owner column of the list. The one field on this screen
               whose authoritative source is another module
The selected type is a route param, so a type's values are a linkable address and the browser's
back gesture returns to the list. Both levels offer Deactivate and neither offers an Activate: no
endpoint exists for the second half, and the confirmation says the act is not reversible from
this screen and that the code/key stays reserved (ADR-MDL-005, G7). The drag handle submits the
whole ordered set through API-MDL-009 rather than writing one row's `sortOrder`.
<!-- SUB:F4-SCR-MDL-001:END -->
