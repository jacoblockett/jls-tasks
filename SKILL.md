---
name: tasks
description: Exhaustively compile a user-designated authoritative goal, specification, plan, export, or source corpus into a faithful, bite-sized task graph. Use when explicitly invoked as $tasks or when the user asks to turn source material into durable actionable tasks without losing requirements.
---

# Tasks

Tasks is a lossless compiler from authoritative source material to durable actionable work. It does not implement the resulting work.
Use this workflow only to derive, write, and verify tasks from the exact source scope the user designated.

Tasks currently uses Beads as its native task backend. The official `beads` skill and live `bd` CLI documentation remain authoritative for Beads mechanics. Tasks owns source fidelity, decomposition, provenance, coverage, repair, and review.

## Invariants

1. The user-designated source scope is authoritative for requirements in this transaction. Do not silently add requirements from chat memory, unrelated repository files, existing tracker state, or implementation preferences.
2. Relevant project files may be inspected as non-authoritative implementation context when needed to derive executable boundaries, integration work, or current technical reality. Context may inform how and where work must occur; it must never override, weaken, strengthen, or fabricate what the authoritative source requires.
3. Preserve meaning losslessly. Every materially relevant source element must be represented by actionable work, constrain actionable work, block actionable work, be explicitly deferred/out of scope, or be explicitly classified non-actionable with a reason. Nothing may silently disappear.
4. Source units are not task units. Do not create one issue per paragraph, node, bullet, or record unless that is naturally the correct executable boundary.
5. Decompose top-down: objective -> major capability or component -> narrow implementation responsibility -> leaf work.
6. Prefer small leaves. A leaf is acceptable only when one implementation agent can pick it up without first decomposing it into independently assignable changes.
7. Split independently assignable changes. If two changes can be implemented, reviewed, or reverted independently without violating one atomic invariant, they normally belong in separate leaf issues.
8. Parent issues provide structure and shared context. Leaf issues own executable responsibilities. Do not use a parent epic as a substitute for missing leaves.
9. Each executable issue must be self-contained enough to resume without the original conversation. Preserve necessary context, source anchors, constraints, dependencies, and objective acceptance conditions when they exist.
10. Preserve provenance. Each issue must identify the source anchors that justify it. Structured sources should retain native IDs when available. Unstructured sources should use stable path/section/line or equivalent anchors when practical.
11. Preserve dependencies that materially affect execution order. Add inferred technical dependencies only when genuinely required, and distinguish them from source-stated dependencies in issue context when useful.
12. Existing tracker items are context, not authority over the supplied source. Reuse or update a clearly equivalent existing issue instead of duplicating it, but do not let stale tracker state erase or rewrite authoritative source requirements.
13. Do not invent product decisions to make the task graph look complete. Material ambiguity that prevents faithful decomposition is an external blocker; defects in Tasks' own design are not.
14. Children run serially. Consume and close each child before spawning another. Children never spawn children.
15. Spawn prompts contain only dynamic source/project arguments, current transaction packets, and exact reviewer deficiencies. The specialist definition owns its semantic contract.
16. A reviewer finding omissions, weak provenance, incomplete constraint propagation, oversized/overlapping issues, missing integration work, bad dependencies, or other correctable design defects is an instruction to repair, not a reason to stop.
17. There is no fixed repair-attempt limit. Continue targeted design or durable-state repair while the remaining deficiencies can be resolved from the authorized source, available project context, current tracker state, and available tooling.
18. Never throw away the strongest recoverable design merely because review failed. Repair the current packet in place and preserve stable source IDs and issue keys wherever their meaning survives.
19. Return control because of review failure only for a genuine external blocker: required user authority/evidence is missing, a required specialist/backend/tool is unavailable, the authoritative source cannot be read, or the backend cannot faithfully represent/apply the reviewed work. Internal non-convergence alone is not an external blocker.
20. Do not mutate Beads until a design Reviewer returns PASS. Final-review repairs may mutate only the current transaction's created issues or explicitly mapped reused issues.
21. Do not implement, edit product code, or perform the work represented by the created tasks.
22. Every Beads issue newly created by Tasks must carry exact structured issue metadata `{"tasks-skill":"owned"}`. Never add this ownership marker to a pre-existing issue that Tasks only reuses or updates.

## Required specialists

Tasks requires two native specialists:

- `tasks-designer`
- `tasks-reviewer`

Use the exact registered name. Do not replace a required specialist with a generic child or parent-thread semantic judgment. If a required specialist cannot run, that is a genuine external blocker.

## Live backend guidance

Tasks currently writes to Beads. Do not depend on memorized `bd` flags when the current CLI can answer directly.
At the start of a substantive transaction:

```text
bd --version
bd prime
```

Treat `bd prime` as the live AI-oriented source of truth for the current runtime. Before an unfamiliar or version-sensitive operation, use:

```text
bd <command> --help
```

The common command families this skill may need are:

```text
bd list
bd show <id>
bd create
bd update <id>
bd dep
bd ready
```

Use the current help output for exact flags, fields, dependency syntax, hierarchy support, metadata syntax, and JSON output. Prefer structured `--json` reads where supported.
Do not run `bd init` automatically. If no Beads database exists, report that prerequisite instead of silently initializing tracker state.

## Durable transaction recovery

Substantive Tasks work uses skill-owned recovery state under:

```text
<PROJECT_ROOT>/.tasks/
```

Create `.tasks/project.json` as the recovery-root marker when the recovery root is first needed, with identity such as `{"owner":"tasks-skill","kind":"recovery","version":1}`. Store each active transaction under `.tasks/transactions/<transaction-id>/`.
If `.tasks/` already exists without that matching marker identity, do not claim, modify, or delete it; stop before substantive work and report the recovery-path collision.
Write checkpoint replacements atomically where the environment permits so a failed write does not destroy the last complete revision.

Recovery state is not requirement authority. It is a resumable copy of work already derived from the authoritative source. At minimum persist:

- the resolved `GOAL`, `SOURCE_SCOPE`, and `CONTEXT_SCOPE`
- current stage
- every complete design revision and review ledger, plus a pointer to the current strongest packet
- stable deficiency IDs, per-deficiency progress classification, repair strategies, and no-progress streaks
- resolved deficiency IDs from prior revisions
- `APPLIED_MAPPING` once Beads mutation begins

Checkpoint before launching the next specialist stage and immediately after consuming its result. Large specialist packets should be written into this transaction directory rather than depending on chat context.

On a later invocation, inspect matching unfinished Tasks transactions before starting from scratch. Re-read the authoritative source and current project/tracker context; recovery state never overrides changed source reality.

Delete the transaction directory only after FINAL review PASS or explicit user abandonment. On a genuine blocker, retain the checkpoint and report the blocker plus resumable transaction location. Never delete the strongest packet merely because a review returned REPAIR.

## Start

1. Resolve the project root containing the target Beads database.
2. Resolve the exact authoritative source scope from the user's request. This may be inline text, files, directories, structured exports such as Map JSON, or another explicitly designated corpus.
3. Record the user's requested end result as `GOAL`. Do not expand it beyond the supplied evidence.
4. Set `CONTEXT_SCOPE` to the smallest relevant project implementation scope, or `AUTO_RELEVANT` when the Designer must discover it. Project context is never requirement authority.
5. Run `bd --version` and `bd prime`. Confirm the target Beads database can be read.
6. Inspect existing Beads only enough to identify overlaps, hierarchy, and dependencies relevant to the requested compilation.
7. Resume a matching unfinished Tasks transaction when one exists; otherwise initialize a new recovery transaction before Design.
8. If source authority is materially ambiguous or contradictory in a way Tasks cannot resolve from supplied evidence, stop with the recovery checkpoint intact and ask for the missing authority.

A Map export is ordinary structured input here. Do not invoke Map or reconstruct Map workflow semantics merely because the input came from Map.

## Design transaction

For a new transaction, spawn `tasks-designer` with:

```text
MODE: DESIGN
PROJECT_ROOT: <path>
GOAL: <exact requested end result>
SOURCE_SCOPE: <exact paths and/or exact inline source packet>
CONTEXT_SCOPE: <smallest relevant implementation scope or AUTO_RELEVANT>
EXISTING_BEADS_SCOPE: <relevant existing ids or AUTO>
DESIGN_PACKET: NONE
REVIEW_DEFICIENCIES: NONE
```

The Designer must independently inventory the authoritative source, understand the whole objective before leaf decomposition, inspect relevant implementation context and existing Beads, and return a complete `DESIGN_PACKET`.

The packet must contain:

- a complete source coverage ledger
- proposed issue hierarchy and issue contents
- source anchors/provenance
- dependencies
- mappings to reused existing issues where applicable
- explicit blocker/deferred/out-of-scope/non-actionable dispositions
- enough integration/API/application and bounded verification work to realize the source goal where current project reality makes those responsibilities necessary

Before returning, the Designer must self-audit the packet for coverage/dispositions, provenance/constraint propagation, leaf boundaries/overlap, dependency coherence, cold-start executability, and implementation/integration sufficiency.

Checkpoint the returned packet before Review.

## Design review and convergence

Spawn `tasks-reviewer` with:

```text
MODE: DESIGN
PROJECT_ROOT: <path>
GOAL: <exact requested end result>
SOURCE_SCOPE: <same authoritative source>
CONTEXT_SCOPE: <same implementation context rule>
DESIGN_PACKET: <current packet or recovery path>
APPLIED_MAPPING: NONE
PRIOR_DEFICIENCIES: <previous ledger or NONE>
```

The Reviewer independently re-reads the authoritative source and relevant project context. It must return exactly one verdict:

- `PASS`: design is ready to apply
- `REPAIR`: one or more internally correctable design defects remain
- `BLOCKED`: progress requires missing external authority/capability and cannot be resolved by further design work

For `REPAIR` or `BLOCKED`, each deficiency must have a stable ID and include:
- `ID`
- `CLASS: REPAIRABLE | EXTERNAL_BLOCKER`
- `PROGRESS: NEW | NARROWED | UNCHANGED | REGRESSED`
- `AREA`
- `EVIDENCE`
- `REQUIRED_CHANGE`

`PROGRESS` is relative to the prior ledger for the same stable deficiency ID. A correctable quality defect must never be classified `EXTERNAL_BLOCKER`.

On `REPAIR`, checkpoint the ledger, then spawn a fresh `tasks-designer` with:

```text
MODE: REPAIR_DESIGN
PROJECT_ROOT: <path>
GOAL: <same goal>
SOURCE_SCOPE: <same authoritative source>
CONTEXT_SCOPE: <same implementation context rule>
EXISTING_BEADS_SCOPE: <same rule>
DESIGN_PACKET: <current packet or recovery path>
REVIEW_DEFICIENCIES: <exact current REPAIR ledger>
REPAIR_HISTORY: <prior repair strategies and outcomes for active deficiency IDs or NONE>
```

The Designer must revise the current packet in place. Preserve accepted structure and stable keys where possible; split/add/remove/re-parent only where the deficiencies or source fidelity require it. Return a complete replacement packet plus a resolution mapping for every supplied deficiency ID.

Checkpoint the revised packet and run a fresh Design Review. Repeat REPAIR_DESIGN -> Review until PASS or the convergence guard below triggers.

Do not stop merely because the same area needs multiple repair cycles. If a deficiency persists, the Reviewer must keep the same stable ID when it is materially the same defect; the next Designer must receive its repair history and change strategy rather than repeat the prior edit. A reviewer may return BLOCKED only when it can identify the external authority/capability required to proceed.

## Convergence guard

Prevent infinite reviewer/designer loops without discarding work.

For each stable deficiency ID, maintain a `NO_PROGRESS_STREAK` in recovery state:
- `NEW` or `NARROWED`: reset the streak to 0.
- `UNCHANGED` or `REGRESSED` after a completed repair attempt: increment the streak.
- resolved deficiencies leave the active ledger but remain in history.

Before each repair, pass the active deficiency's prior repair strategies in `REPAIR_HISTORY`. The Designer must attempt a materially different correction when the same deficiency persists.

If any deficiency reaches a no-progress streak of 3:
1. Do not launch another automatic repair cycle for that deficiency.
2. Persist the current strongest design packet, every prior design/review revision, repair history, latest deficiency ledger, counters, and any `APPLIED_MAPPING`.
3. Mark the transaction `STALLED_DESIGN_REVIEW` or `STALLED_FINAL_REVIEW`.
4. Do not delete or roll back already-durable state. Before APPLY this means the recovery checkpoint remains and Beads is untouched; after APPLY, preserve all current transaction-created/reused Beads state and its mapping.
5. Report the exact stalled deficiency IDs and recovery transaction path. Do not present the stall as an external blocker and do not claim the work was discarded.

A later Tasks invocation may resume a stalled transaction. Re-read current source/project/tracker state, preserve the repair history, reset the automatic no-progress streak for the explicit resume, and require a new repair strategy rather than replaying a prior one.

## Apply transaction

After Design Review PASS, spawn a fresh `tasks-designer` with:

```text
MODE: APPLY
PROJECT_ROOT: <path>
GOAL: <same goal>
SOURCE_SCOPE: <same source scope>
CONTEXT_SCOPE: <same implementation context rule>
DESIGN_PACKET: <Reviewer-PASSed packet>
REVIEW_DEFICIENCIES: NONE
```

The Designer must use current `bd prime` and command help, then create or update the approved Beads graph without re-planning it.
It may reuse clearly equivalent existing issues identified in the reviewed packet. It must preserve unrelated existing tracker state.
Every newly created issue must be created with exact structured metadata `{"tasks-skill":"owned"}` using the live supported Beads metadata syntax. Reused or pre-existing issues must not acquire that ownership marker merely because Tasks touches them.
It must read back every created/updated issue, verify ownership metadata on newly created issues, and verify relevant dependencies before returning.

The result must include an `APPLIED_MAPPING` from every proposed issue key to its durable Beads ID, plus any externally blocked operation.
Checkpoint `APPLIED_MAPPING` immediately.

Before the first Beads mutation, reconcile the reviewed packet against current tracker state and live Beads semantics. If faithful application now requires an internally resolvable design adjustment, return `REPAIR_REQUIRED` before mutating anything. Checkpoint the application deficiency, run `REPAIR_DESIGN` against the current packet and current tracker context, obtain a fresh Design Review PASS, then retry APPLY.

Once mutation begins, continue through the reviewed application and readback rather than voluntarily stopping mid-apply. If an external backend/tool failure prevents completion after safe retries, checkpoint every durable ID already created or updated and return the genuine external blocker; never discard that mapping.

## Final durable-state review and repair

Spawn `tasks-reviewer` with:

```text
MODE: FINAL
PROJECT_ROOT: <path>
GOAL: <same goal>
SOURCE_SCOPE: <same source scope>
CONTEXT_SCOPE: <same implementation context rule>
DESIGN_PACKET: <Reviewer-PASSed packet>
APPLIED_MAPPING: <durable ids from Designer>
PRIOR_DEFICIENCIES: <previous final-review ledger or NONE>
```

The Reviewer must inspect the actual Beads database using current `bd prime`/help and independently compare durable tracker state against both the authoritative source and reviewed design.

Its verdict is `PASS`, `REPAIR`, or `BLOCKED` under the same classification rule as Design Review.

On `REPAIR`, checkpoint the ledger and spawn `tasks-designer` with:

```text
MODE: REPAIR_APPLIED
PROJECT_ROOT: <path>
GOAL: <same goal>
SOURCE_SCOPE: <same source scope>
CONTEXT_SCOPE: <same implementation context rule>
DESIGN_PACKET: <Reviewer-PASSed packet>
APPLIED_MAPPING: <current durable mapping>
REVIEW_DEFICIENCIES: <exact current REPAIR ledger>
REPAIR_HISTORY: <prior repair strategies and outcomes for active deficiency IDs or NONE>
```

Repair only the identified durable-state defects. Mutate only issues created by the current transaction or pre-existing issues explicitly mapped by the reviewed design. An erroneous issue may be deleted only when it was created by this transaction and deletion is required by a reviewer deficiency. Never delete unrelated or pre-existing tracker state.

Read back repairs, checkpoint the updated mapping/result, and run FINAL review again. Repeat until PASS, a genuine external blocker is identified, or the same convergence guard reaches its no-progress threshold. A stalled final review preserves the current Beads graph and recovery mapping for later resume; never delete or roll it back merely because review did not converge.

## Completion boundary

Tasks is complete only when the final Reviewer returns PASS.

Completion means the authoritative source has a reviewed, durable task representation in which every material source element has an explicit disposition and every executable responsibility needed to realize the goal is represented by sufficiently small, self-contained work.

After PASS, delete the transaction recovery directory. If no other Tasks transactions remain, remove the `.tasks` recovery root and its marker as well. Report the created/updated issue count, reused issue count, blocked/deferred/non-actionable counts, and final review result. Do not implement the issues.

A `REPAIR` verdict is never a completion boundary and must not be surfaced to the user as "more work remains; what should I do?" Continue the workflow automatically.
