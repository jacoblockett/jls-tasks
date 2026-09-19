<!-- Managed by JLS. Do not edit inside this block; reinstall/update replaces it. -->
## Tasks

Use `$tasks` when the user asks to convert an authoritative goal, specification, plan, structured export, or other designated source into a complete actionable task graph without silently losing requirements.

Tasks currently writes to Beads. Do not use Tasks for ordinary issue operations such as checking ready work, claiming an issue, adding notes, or closing completed work. Use the official `beads` skill and current `bd` CLI guidance for ordinary tracker operation.

Tasks never implements the generated work. It owns exhaustive source coverage, top-down decomposition, provenance, dependency preservation, fine-grained task design, convergent repair, and independent final review.

A Tasks transaction is not finished because a reviewer found defects. Reviewer `REPAIR` verdicts are internal work instructions: preserve the current packet/checkpoint, run the required repair specialist stage, and continue review automatically. Do not ask the user whether to continue merely because more internally repairable work remains. Return control for review failure only when the reviewer identifies a genuine external blocker requiring missing user authority or unavailable capability.

Substantive Tasks transactions maintain resumable JLS-owned recovery state under `.jls/tasks/`. Do not discard the strongest design packet on review failure. Delete transaction recovery state only after final durable-state review PASS or explicit user abandonment.

The Beads CLI remains authoritative for current backend command mechanics. Start substantive Tasks work with:

```text
bd --version
bd prime
```

Use `bd <command> --help` whenever exact current flags or semantics are needed.

JLS installs the required `tasks-designer` and `tasks-reviewer` specialists into the selected harness. Invoke those exact named specialists at the workflow stages required by the installed skill. Do not substitute a generic child or parent-thread judgment if a required specialist cannot run.
