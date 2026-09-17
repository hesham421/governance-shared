# governance-shared

The single copy of everything that crosses repository boundaries between
`factory`, `backend` and `frontend`. Consumed as a **git submodule**, so each
repository still holds inside its own checkout everything it reads.

## Layout

One module, one place: everything about a module sits under one folder, and
inside it each writer has its own sub-path. `{profile_id}` is the factory's
active profile (`erp` today) — the factory resolves it from `factory.yaml →
paths`, so the tree carries no product name the tools would have to know.

```
platform/                          written by factory  · read by all
  modules-registry.json                the module registry (derived from the filesystem)
  profile-summary.json                 every factory fact a consumer needs to set a module up
  rules/                               governance rules, atom definitions, contracts
{profile_id}/                      the analysis partition — the factory's governance output
  domain-profile.md                    written by factory · read by all
  project-registry.md                  written by factory · read by all
  system-test-*.md                     written by factory · read by all
  decisions/{MOD}/                     written by factory · ADR stream (incl. dialogue decisions)
  modules/{MOD}/
    P0/ P0_5/ P1/ P2/ P3_1/ P3_2/      written by factory · the stage artifacts
    test_gen/  api_verify/             written by factory · standalone stages
    _state/  _inputs/                  written by factory · generated state, fetched inputs (+ .meta.json: the commit each input was read at)
    packages/                          written by factory · split output — the delivery each track reads
    manifest.json                      written by factory · the module's index + the factory's own execution state
    api-docs/                          written by BACKEND  · read by factory + frontend (generated from the running app)
    backend/                           written by BACKEND  · execution-state.json, test-api/
    frontend/                          written by FRONTEND · execution-state.json
```

## Two rules that make this work

**One writer per path.** `CODEOWNERS` enforces it. A path with two writers is a
path where drift is invisible.

**api-docs exist once.** The frontend holds no copy — it reads
`{profile_id}/modules/{MOD}/api-docs/` directly, read-only. Two copies cannot diverge
if there is only one. The isolation between partitions governs *writing*, not
reading, and two copies is the problem being solved.

## api-docs are derived, never edited

They are generated from the running backend (`/v3/api-docs`) by
`generate-api-docs`. An error in them is a defect in the code: fix the code and
regenerate. A hand edit here is erased by the next generation.

## Pinned on purpose

Each consumer pins a commit rather than tracking a branch, so a frozen module
version keeps the exact api-docs it was built against, and every upgrade is a
deliberate, reviewable change.

Each consumer narrows what it *sees* with `gov.py sparse --track <t>`: the
patterns are derived from the same declarations (`factory.yaml → repos.shared.partitions`,
`tracks`, the stages' `track`), never listed by hand.

Design: `factory/GOVERNANCE-SHARED-DESIGN.md` — the partitions, the one-writer
table (§3, enforced by `CODEOWNERS`) and the pinned-pointer rule (§4).
