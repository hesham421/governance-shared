Write is disabled in this session too, so — as in round 1 — the merged scorecard is delivered directly here.

## Convergence — round 2 of 2 (final)

I confirmed all seven scores and all five round-1 findings. Three corrections and two additions:

**DECISION: all seven scores stand as round 1 set them** — `3 · 3 · 2 · 2 · 3 · 3 · 3`. `complete = 2` is right and not lower: the plan is exhaustive on its own surface (11/11 endpoints bound, 13/13 REQ mapped, `✗` rows each citing an ADR); the incompleteness is at the seams. `consistent = 2` is right and not higher: the pass is unusually rigorous about disclosing divergence rather than silently reconciling it, but G1/G6/G7 are where it didn't hold itself to that. TC-MDL-014's infeasibility is a backend-artifact defect and does not drag this track's `verifiable`/`feasible`.

**DECISION: G1, G2, G3 confirmed MAJOR — but round 1 routed their fixes outside this pass.** Round 1's fixes edit `backend-execution-plan-mdl.md`, `backend-test-plan-mdl.md` and SEC's P3.1 artifacts. None is in P3.2's writable set, so `merge-review-notes` cannot apply them as written. The pass already established the right mechanism: **PF-MDL-001** is a backend-track defect filed as a platform-findings row *in the frontend plan*, owner and evidence named, "not settled here". Same class, same route. Substance of all three confirmed unchanged.

**DECISION: G1's fix needs two things round 1 omitted.** `MDL-400-REORDER-MISMATCH`'s trigger reads "a submitted id does not belong to the type in the path" — reusing it for an incomplete set requires broadening that row, or the code is raised by a condition its own catalog row doesn't describe. And a size check alone misses **duplicate ids**: `[A,A,B]` on a 3-value type passes it and writes A twice. The check must be over the *distinct* submitted set.

**DECISION: G4 confirmed, wrong unwanted path.** Round 1's "empty result returns empty, not an error" restates a convention already stated platform-wide (QR-MDL-010 Notes; PHASE 1: "An empty result is success on every one of them"). The genuinely unspecified path: RULE-MDL-001 is create-time only, so a module deregistered from SEC keeps owning its types — and SCR-MDL-002 groups the registry by owner code, so an administrator auditing by owner sees a group headed by a module code that no longer exists, unmarked. `ADR-MDL-015` and `ui-ux-spec-mdl.md` both acknowledge this downstream; the SRS states it nowhere.

**DECISION: G5 needs no action and lowers no score.** The pass already recorded it as G8. Per §7, recording is never a reason to lower a score.

**DECISION: NEW — G6.** F3's UNIQUE_CHECK sends an **EQUALS** filter on `key` and `code`, but QR-MDL-001 binds `WHERE [key LIKE :key]` and QR-MDL-005 binds `[code LIKE :code]` unconditionally; PHASE 1 and SRS §B2 agree both are LIKE. Typing `PAYMENT` returns `PAYMENT_METHOD` and the blur check falsely reports the key as taken — on the one field that cannot be changed after creation. Inside this pass's writable set.

**DECISION: NEW — G7.** F1/F2 model `sortField` as a free variable and key the cache on it, but QR-MDL-001 declares one ordering (`ORDER BY key`) and PHASE 1 says "the module offers no free-form sort parameter". This is the pass's own G2 principle — "keying a variation the server cannot produce would fragment the cache for nothing" — not applied to itself. `sortDirection` survives; `sortField` does not.

### §5 traceability spot-checks

| # | Check | Result |
|---|---|---|
| 1 | design + exec plan → split (C9.*) | **PASS** — both SCR carry a SUB in F1/F2/F3/F4 + SEC-FE; composition declared for both |
| 2 | IDs ↔ stage registry, both directions | **PASS** — SCR-MDL-001/002, UXD-MDL-001, ADR-MDL-014/015; nothing extra either side |
| 3 | sequences continue, no gap, no reassignment | **PASS** — ADR-MDL-001…015 contiguous; 014/015 the only ids minted this run |
| 4 | upstream wins, no silent contradiction | **PASS** — every divergence disclosed (ADR-MDL-002, PF-MDL-002, the pagination correction) |
| 5 | XM state / DEFERRED / OPEN resolution | **PASS (qualified)** — form correct; "Unblock condition: none outstanding" is contestable while SEC has not registered the interface. See G3 |
| 6 | concurrency: a named guard, not "validated first" | **PASS** — exemplary; QR-MDL-013/014 *explicitly disclaimed* as non-guards. G1 is input validation, not concurrency, and is filed as such |
| 7 | cited APIs exist in api-docs; UXD/SCR referenced | **PASS** — all 11 carry a Contract ID; no foreign `API-*` cited |

```json
{
  "gate": "pass-2",
  "module": "MDL",
  "version": 1,
  "scores": { "unambiguous": 3, "verifiable": 3, "complete": 2, "consistent": 2, "singular": 3, "feasible": 3, "traceable": 3 },
  "extra_checks": [],
  "analyze_confirmed": [
    {"finding": "C5.16 feature-unwanted / US-MDL-005 / srs:47", "confirmed": true, "note": "Confirmed, and re-aimed. Round 1 proposed stating that an empty filter result is success — already stated platform-wide (QR-MDL-010 Notes; PHASE 1 Search contract). The unspecified path is the deregistered owner: see G4."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "artifact": "frontend-execution-plan-mdl.md (API SURFACE / platform findings)", "line": null,
     "clause": "P1 adversarial inversion · C7.20 operation-resolves · §7 platform findings",
     "problem": "QR-MDL-009 enforces only that each submitted id belongs to the type (lookup_type_id = :typeId). Nothing verifies the submitted set is the type's complete value set, contains no duplicate, and is non-empty. A subset re-ranks 1..n and collides with the ranks of every value left out; [A,A,B] on a 3-value type writes A twice. The frontend disables the drag affordance when the pane is filtered (G3) precisely because 'the server cannot distinguish a partial submission from a deliberate whole-set one' — so this pass identified a server-side invariant gap and settled it with a client-side convention instead of filing it, contrary to the PF-MDL-001 precedent it set for exactly this class.",
     "fix": "File a platform-findings row (PF-MDL-004) in frontend-execution-plan-mdl.md, owner MDL backend track (P3.1). Evidence: QR-MDL-009's statement and Result-shape lines, against frontend-execution-plan-mdl.md F2-QUERY VALUE REORDER's submission rule. Required backend change: reject in the API-MDL-009 orchestration, before the UPDATE loop and inside the same transaction, when the DISTINCT submitted id count does not equal the count of rows under lookup_type_id = :typeId (active and inactive both, matching QR-MDL-005's scope) — and broaden MDL-400-REORDER-MISMATCH's catalog trigger, which today reads only 'a submitted id does not belong to the type in the path', to cover an incomplete or duplicated set.",
     "adr": false},

    {"id": "G2", "severity": "MAJOR", "artifact": "frontend-execution-plan-mdl.md (platform findings) / backend-test-plan-mdl.md TC-MDL-014", "line": null,
     "clause": "P3 read the seams · XM-PROTOCOL §4-5 · consistency",
     "problem": "TC-MDL-014 makes 'SEC's registry-search endpoint (API-SEC-021) unreachable/times out' and expects a controlled non-500. Three artifacts under review contradict it: backend-execution-plan INT-R states 'there is no HTTP-level way to simulate \"SEC unreachable\", and no test should try to'; ALIGN-BE struck the 503 row for that same impossibility; and API-SEC-021 is the FRONTEND's HTTP read behind UXD-MDL-001, not the backend XM path at all, which is an injected in-process call. The test names the wrong surface AND an unconstructible failure mode, and duplicates TC-MDL-002's coverage.",
     "fix": "File a platform-findings row (PF-MDL-005) in frontend-execution-plan-mdl.md, owner MDL backend track. Evidence: TC-MDL-014's Preconditions line against backend-execution-plan-mdl.md PHASE 6 (INT-R) and the ALIGN-BE strike note. Required change: retarget TC-MDL-014 to the SOFT-READ's real untested edge — a module deregistered from SEC after its types were accepted must not invalidate them (RULE-MDL-001's own Test-Hint) — and drop the API-SEC-021 citation, which belongs to the frontend's UXD-MDL-001 read.",
     "adr": false},

    {"id": "G3", "severity": "MAJOR", "artifact": "frontend-execution-plan-mdl.md (platform findings) / backend-execution-plan-mdl.md XM-MDL-001", "line": null,
     "clause": "§7 platform findings · XM-PROTOCOL §4-5",
     "problem": "XM-MDL-001's contract states that SEC's P3.1 artifacts register only SecUserDirectoryApi, so the module-registry read this XM consumes is written down nowhere on SEC's side, and defers it to 'SEC's own re-run'. registry-exec-be repeats it. It is disclosed only as prose inside MDL's own artifacts and filed as no platform-findings row anywhere — the 'not mine to settle into the void' pattern §7 exists to stop. Meanwhile the XM is carried ACTIVE with 'Unblock condition: none outstanding', which is the claim the prose two paragraphs above contradicts.",
     "fix": "File a platform-findings row (PF-MDL-006) in frontend-execution-plan-mdl.md, owner SEC / P3.1 track. Evidence: registry-exec-be-mdl.md XM STATUS and backend-execution-plan-mdl.md INT-C XM-MDL-001 Interface paragraph. Statement: SEC v1's P3.1 artifacts must register SecModuleRegistryApi as an exposed cross-module surface alongside SecUserDirectoryApi. Distinct from API-SEC-021, which is the published HTTP read UXD-MDL-001 uses — the gap is specifically the in-process backend contract.",
     "adr": false},

    {"id": "G4", "severity": "MINOR", "artifact": "srs-mdl.md REQ-MDL-013", "line": null,
     "clause": "C5.16 feature-unwanted",
     "problem": "US-MDL-005's one requirement states no unwanted path. The real silent path is not the empty result (already a stated platform convention) but the deregistered owner: RULE-MDL-001 is create-time only, so a module removed from SEC keeps owning its types, and SCR-MDL-002 groups the registry by owner code — an administrator auditing by owner sees a group headed by a module code that no longer exists, unmarked. ADR-MDL-015 and ui-ux-spec-mdl.md both acknowledge it downstream; the SRS states it nowhere.",
     "fix": "Add to REQ-MDL-013 an unwanted-pattern statement: 'If a lookup type's owner module code is no longer registered in the security module, then the system shall still return that type under its stored owner module code, and shall neither remove nor alter the type.' This documents the behaviour RULE-MDL-001's existing Test-Hint already decided, so no new choice is taken. Outside this pass's writable set — file alongside the rows above, owner P1.",
     "adr": false},

    {"id": "G5", "severity": "MINOR", "artifact": "analysis/decisions/MDL/ADR-MDL-008.md", "line": null,
     "clause": "§7 decisions review",
     "problem": "The status header reads 'ACCEPTED (non-breaking) — supersedes ADR-MDL-001' with no record that ADR-MDL-011 supersedes it in turn.",
     "fix": "No action owed by this pass. Already recorded by it as G8 in registry-exec-fe-mdl.md, correctly noted as outside its writable file set; per §7 recording a finding never lowers a score. Carried for whichever stage next holds ADR-MDL-008.md.",
     "adr": false},

    {"id": "G6", "severity": "MAJOR", "artifact": "frontend-execution-plan-mdl.md (F3 · SCR-MDL-001, UNIQUE_CHECK)", "line": null,
     "clause": "P3 read the seams · C9.5 · consistent",
     "problem": "The UNIQUE_CHECK specifies 'the type's key through API-MDL-001 with an EQUALS filter' and 'the value's code through API-MDL-005 with EQUALS filters on both lookupTypeId and code'. Both queries bind those columns with LIKE unconditionally: QR-MDL-001 'WHERE [key LIKE :key]' (Filters: key: LIKE) and QR-MDL-005 'AND [code LIKE :code]' (Filters: lookupTypeId: EXACT · code: LIKE); PHASE 1's Search contract and SRS §B2 agree. An EQUALS operator therefore cannot produce an exact match — typing PAYMENT returns the existing PAYMENT_METHOD row and the blur check falsely reports the key as already taken, on the one field RULE-MDL-003 makes unchangeable afterwards. Capped below CRITICAL only because the plan states the check never blocks submit and the server's 409 is the authority.",
     "fix": "Rewrite the UNIQUE_CHECK line to request the LIKE filter the backend declares and then assert exact equality client-side over the returned rows before showing the inline message — for key on API-MDL-001, and for code scoped to lookupTypeId on API-MDL-005. This uses only the published surface, needs no new endpoint or operator, and is correct whether or not the service honours EQUALS. Inside this pass's writable set.",
     "adr": false},

    {"id": "G7", "severity": "MINOR", "artifact": "frontend-execution-plan-mdl.md (F1-SCREEN SCR-MDL-001; F2-QUERY TYPE SEARCH)", "line": null,
     "clause": "P4 error vs pattern · consistent",
     "problem": "The master search model carries sortField and sortDirection and the cache key carries both, but the backend declares a single ordering — QR-MDL-001 'ORDER BY key', PHASE 1 'key ASC — paged' and 'the module offers no free-form sort parameter'. This is the pass's own G2 correction unapplied to itself: 'keying a variation the server cannot produce would fragment the cache for nothing'.",
     "fix": "Drop sortField from the F1 search model and from the F2 TYPE SEARCH cache key, or constrain it to the single literal 'key'. Keep sortDirection, which PHASE 1's 'refused on any other field' wording leaves available on key itself. Inside this pass's writable set.",
     "adr": false}
  ],
  "adrs_reviewed": [
    {"id": "ADR-MDL-001", "status_ok": true, "note": "SUPERSEDED by ADR-MDL-008 — chain intact."},
    {"id": "ADR-MDL-002", "status_ok": true, "note": "ACCEPTED; correctly names the backend contract summary as the lagging artifact, not the implementation."},
    {"id": "ADR-MDL-003", "status_ok": true, "note": "ACCEPTED; refuses TREE_MASTER_DETAIL for a screen with nothing in its detail pane. Sound."},
    {"id": "ADR-MDL-004", "status_ok": true, "note": "ACCEPTED; mints UXD-MDL-001, one hook for two screens, no foreign API id in the plan."},
    {"id": "ADR-MDL-005", "status_ok": true, "note": "ACCEPTED. Self-tracked G9 (stale Context-table citation) open against a file outside this pass's writable set."},
    {"id": "ADR-MDL-006", "status_ok": true, "note": "ACCEPTED; matches the published write DTOs field for field."},
    {"id": "ADR-MDL-007", "status_ok": true, "note": "ACCEPTED; bound-but-uncalled is the honest accounting, and the req-ux ratio is correctly not inflated for it."},
    {"id": "ADR-MDL-008", "status_ok": false, "note": "Header does not record being superseded by ADR-MDL-011. See G5 — already disclosed by the pass, no action owed, no score effect."},
    {"id": "ADR-MDL-009", "status_ok": true, "note": "ACCEPTED (P2); applied unchanged."},
    {"id": "ADR-MDL-010", "status_ok": true, "note": "ACCEPTED (P2); correctly overrides the conflicting api-docs widths, with PF-MDL-002 filed rather than the divergence buried."},
    {"id": "ADR-MDL-011", "status_ok": true, "note": "ACCEPTED, supersedes ADR-MDL-008; consistent with the re-fetched api-docs' Contract ID lines, 11 of 11."},
    {"id": "ADR-MDL-012", "status_ok": true, "note": "ACCEPTED; correctly separates the readable menu gate from the unreadable action grants, and refuses to hide affordances on a guess."},
    {"id": "ADR-MDL-013", "status_ok": true, "note": "ACCEPTED; names the cross-grant dependency and mints nothing in SEC's registry."},
    {"id": "ADR-MDL-014", "status_ok": true, "note": "ACCEPTED, new this run; redirect rather than an empty editor, with no invented filter. Traces name the three affected routes."},
    {"id": "ADR-MDL-015", "status_ok": true, "note": "ACCEPTED, new this run; correctly distinguishes the two screens' degraded paths by the presence of a save."}
  ],
  "verdict": "REVISE"
}
```

**Verdict: REVISE.** All scores ≥ 2 and no CRITICAL, so ESCALATE is not reached — no BLOCKED ADR exists among the fifteen, and no finding repeats across rounds. Four MAJORs remain, and with the routing corrected every one has a fix `merge-review-notes` can apply inside this pass's writable set: G6 and G7 edit the frontend plan directly, and G1/G2/G3 become platform-findings rows in it, following the PF-MDL-001 precedent the pass itself set.

<!-- CONVERGED -->