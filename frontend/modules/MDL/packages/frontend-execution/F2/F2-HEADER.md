<!-- source: PHASE:F2 — preamble before the first SUB -->
<!-- traces: REQ-MDL-001, REQ-MDL-002, REQ-MDL-003, REQ-MDL-004, REQ-MDL-005, REQ-MDL-006, REQ-MDL-007, REQ-MDL-008, REQ-MDL-009, REQ-MDL-010, AC-MDL-001, AC-MDL-002, AC-MDL-003, AC-MDL-004, AC-MDL-005, AC-MDL-006, AC-MDL-007, AC-MDL-008, AC-MDL-009, AC-MDL-010, UXD-MDL-001, SCR-MDL-001, REQ-MDL-011, REQ-MDL-012, REQ-MDL-013, AC-MDL-011, AC-MDL-012, AC-MDL-013, SCR-MDL-002 -->
## PHASE F2 — Data Hooks

What each screen needs from the API — not hook code. Every read query's cache key carries
**every** filter that changes the response, page and size included **where the endpoint is
paged**; page and page size live inside the filter object and are never independent state.
Every mutation declares its invalidation. Components use the facade only; the facade uses the
declared queries only (server-state library: `tanstack-query`).

Each block below is headed by its label from the BINDING table and by the verb and path the
api-docs publish for it; the request and response shapes are read there and are not restated.

Only two of the eleven endpoints return a paged envelope — TYPE-SEARCH and VALUE-SEARCH.
VALUE-REORDER, REGISTRY-BY-OWNER and CONSUMER-READ-BY-KEY return bare arrays, so their keys
carry no page or size and their blocks say so rather than leaving a reader to assume the usual
envelope.

**Ordered calls.** No action on either screen owns two calls: the type form writes the type
alone, the value form writes one value, and the reorder submits one ordered list. The rule
that a single action owning two calls must send the second only after the first succeeded, and
not at all when its input is unchanged, therefore has nothing to order in this module — stated
because its absence is a decision, not an omission.
