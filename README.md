# governance-shared — the ERP project repo

The project repo of the **ERP Platform**, driven by the governance factory
(`hesham421/factory`, a pure tool that carries no project). Everything generated
or project-variable lives here; every consumer (backend, frontend) mounts this
repo as a **git submodule** and pins a commit, so each still reads inside its
own checkout what the factory wrote once.

## Layout

```
project.yaml                    this project's facts: the active profile id, the consumer repos (user-edited; the factory never writes it)
profiles/                       the domain profile(s) — <id>.yaml + <id>/knowledge/ (validated by the factory's schema)
analysis/                       written by the factory
  domain/domain-profile.md          the entry gate
  platform/                         project-registry.md · system tests · PROJECT-OVERVIEW.md (rendered: profile summary + phase tables)
  modules/{MOD}/[vN/]               every stage artifact · _state/ (generated current state, gate + analyze records, briefs) · _inputs/ (+ .meta.json: the commit each input was read at) · manifest.json (the module's index + the factory's execution state)
  decisions/{MOD}/                  the ADR stream, dialogue decisions included
backend/modules/{MOD}/
  api-docs/                     written by BACKEND  · read by factory + frontend (generated from the running app, never edited)
  packages/                     written by factory  · the delivered backend execution + test packages, versioned like the module
  execution-state.json, …       written by BACKEND  · its own execution progress (the factory reads it: gov.py feedback)
frontend/modules/{MOD}/
  packages/                     written by factory  · the delivered frontend packages
  execution-state.json, …       written by FRONTEND
platform/                       written by factory  · rules, modules-registry.json, profile-summary.json (read by all)
history/  _archive-v5/          this project's history (pre-v6 artefacts, loaded by nothing)
```

## Two rules that make this work

**One writer per path.** `CODEOWNERS` enforces it, most specific wins — the
same reading the factory uses when it decides what a regeneration may delete.
A path with two writers is a path where drift is invisible.

**api-docs exist once.** The frontend holds no copy — it reads
`backend/modules/{MOD}/api-docs/` directly, read-only. Two copies cannot diverge
if there is only one. The isolation between partitions governs *writing*, not
reading, and two copies is the problem being solved.

## api-docs are derived, never edited

They are generated from the running backend (`/v3/api-docs`) by
`generate-api-docs`. An error in them is a defect in the code: fix the code and
regenerate. A hand edit here is erased by the next generation.

## Pinned on purpose

Each consumer pins a commit rather than tracking a branch, so a frozen module
version (`{mod}-vN`, tagged **here**) keeps the exact api-docs it was built
against, and every upgrade is a deliberate, reviewable change. `gov.py
fetch-inputs` records which commit it read a track's publication at.

Each consumer narrows what it *sees* with `gov.py sparse --track <t>`: the
patterns are derived from the factory's declarations (`factory.yaml →
project.partitions`, `tracks`, the stages' `track`), never listed by hand.

Driving it: `export GOV_PROJECT_CHECKOUT=/path/to/this/repo` in the shell the
factory runs from (the factory's README has the operator steps).

Design: `factory/GOVERNANCE-SHARED-DESIGN.md` — the partitions, the one-writer
table (§3, enforced by `CODEOWNERS`) and the pinned-pointer rule (§4).
