# Change artifacts

Keep one coherent change per folder: `NNN-short-slug`, with an ascending numeric ID padded to at least three digits and a lowercase hyphenated slug. Choose the next unused number after inspecting existing directories; do not reuse IDs or overwrite another change. Coordinate allocation if agents work concurrently. Split independently reviewable features into separate chains.

Create folders only for real requested changes. Bootstrap adds no first feature ticket. A typical chain is `001-short-slug/intent.md`, then `spec.md`, then `plan.md`; these are naming examples, not existing files. Optional `handoff.md` is created only for an actual handoff.

## Artifact responsibilities

- `intent.md`: problem, desired outcome, confirmed requirements, scope, non-goals, explicit constraints, success criteria, assumptions, and open questions. Describe what and why; technical choices belong here only when they are explicit constraints.
- `spec.md`: requirements, business rules/invariants, relevant system responsibilities, constraints, acceptance criteria, failure/edge cases, and important decisions. Link to the accepted Intent and flag unresolved design questions.
- `plan.md`: approach, affected modules/files, sequence, trade-offs, risk/compatibility, verification, and progress. Link upstream; append actual check results, review findings, deviations, and delivery evidence as work proceeds. Separate acceptance of the approach from later progress updates.

## States and human acceptance

Put this metadata near the top of each artifact, adapting the title and upstream links:

```markdown
Change: NNN-short-slug
Status: Draft
Accepted by: Pending
Accepted on: Pending
Acceptance evidence: Pending
```

| State | Meaning |
| --- | --- |
| Draft | Proposal or material revision awaiting acceptance; assumptions are not approved requirements |
| Accepted | A human explicitly accepted this artifact/version; record who, date, and evidence |
| In Progress | Work authorized by this accepted artifact is underway; acceptance evidence remains attached |
| Completed | The artifact's accepted scope is fulfilled; retain the record and evidence |

Record a human's explicit acceptance in `Acceptance evidence` as a review/issue link or a short attributed approval quote with date and artifact/version or section scope. A chat approval may be transcribed; an agent must not fabricate it. Use ISO dates (`YYYY-MM-DD`) and identify the accepted revision by commit when available, otherwise by dated revision note. Do not store private conversation dumps. Do not mark an artifact Accepted based on silence, generated metadata, or approval of an earlier stage.

Intent must be Accepted before drafting Specification. For a significant change, accept the Specification and Implementation Plan before Build; record human acceptance of architectural decisions in the relevant artifact or linked decision document. Additional authorization is required for risk-bearing actions. The developer can accept several concrete artifacts explicitly in one review; no ceremonial repetition is needed.

Status is per artifact: accepted Intent and Specification may stay Accepted while Plan is In Progress. At delivery, mark the chain Completed only when requirements, verification, and review are satisfied; record known limitations and any explicit human acceptance of deviations. An incomplete or blocked change stays in its actual state with the blocker noted; do not add states just to imply completion.

## Revisions and history

When a material requirement, scope, architectural choice, or risk changes, preserve the old acceptance record in a dated revision note, set the affected proposal to Draft, mark current acceptance fields Pending, and summarize the impact on downstream artifacts. Pause dependent work until the human accepts the revision. Progress updates and bug fixes within accepted scope do not invalidate the accepted approach.

Keep completed chains as historical records. Start a new chain for a subsequent feature or material follow-up, linking the earlier work; do not rewrite an old chain to look as if the later requirements were always intended.

Create `docs/architecture/` when an actual design needs a durable record. Use a short `NNN-short-slug.md` decision record with status (Proposed, Accepted, or Superseded), context, options, decision, rationale, consequences, and human acceptance evidence. Link it from affected changes. Keep current architecture descriptions accurate as accepted changes land; preserve superseded decisions with links rather than silently erasing history. Business requirements remain in change artifacts; implementation tasks remain in plans.
