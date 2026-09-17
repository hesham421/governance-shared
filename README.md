# governance-shared

The single copy of everything that crosses repository boundaries between
`factory`, `backend` and `frontend`. Consumed as a **git submodule**, so each
repository still holds inside its own checkout everything it reads.

## Layout

```
platform/     written by factory  · read by all      rules, module registry, profile summary
backend/      modules/{MOD}/
                api-docs/         written by BACKEND · read by factory + frontend
                packages/         written by factory · read by backend
frontend/     modules/{MOD}/
                packages/         written by factory · read by frontend
```

## Two rules that make this work

**One writer per path.** `CODEOWNERS` enforces it. A path with two writers is a
path where drift is invisible.

**api-docs exist once.** `frontend/` holds no copy — the frontend reads
`backend/modules/{MOD}/api-docs/` directly, read-only. Two copies cannot diverge
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

Design: `factory/GOVERNANCE-SHARED-DESIGN.md`.
