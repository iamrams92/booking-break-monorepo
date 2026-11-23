---
name: create-intent
description: Discover a requested BookingBreak feature or problem and draft its intent.md with scope, constraints, success criteria, and open questions. Use to start or refine Intent Discovery, not to specify, plan, or implement a change.
---

# Create Intent

All paths below are relative to the BookingBreak repository root, including when this skill is invoked through the Claude symlink. Read the canonical skill file when editing this workflow.

## Discover

1. Read `AGENTS.md`, `docs/product/vision.md`, `docs/engineering/README.md`, and `docs/changes/README.md`. Inspect relevant existing changes, architecture records, code, and Git status/diff if Git is initialized. Preserve unrelated work. Do not assume conversation history transfers across providers.
2. Identify the developer's problem, who is affected, and the desired observable outcome from the actual request. Separate explicit requirements and project constraints from assumptions. Existing code is evidence of behavior, not automatic approval of business intent.
3. Ask only questions whose answers materially affect scope, correctness, business rules, constraints, or success criteria. Group related questions; use known context first. Continue independent discovery while waiting. Never invent answers. If the developer cannot answer yet, record the uncertainty and whether it blocks acceptance.
4. Keep implementation choices out of Intent unless the developer or accepted project guidance explicitly makes them constraints. Do not select frontend tooling, reservation policies, infrastructure, schemas, APIs, or numeric load targets merely to fill gaps. Explain relevant trade-offs briefly when they help the developer decide.

## Draft

For an actual requested change, inspect existing change IDs, choose the next unused `NNN-short-slug`, and create only `docs/changes/NNN-short-slug/intent.md`. If refining a named existing draft, update it in place; if materially revising an accepted Intent, follow the revision and renewed-acceptance rules in `docs/changes/README.md`. Do not rewrite a completed chain for a new feature.

Use the following shape, omitting inapplicable details rather than inventing requirements:

```markdown
# Intent: <short title>

Change: NNN-short-slug
Status: Draft
Accepted by: Pending
Accepted on: Pending
Acceptance evidence: Pending

## Problem and context
Who is affected, what is happening, and why it matters.

## Desired outcome
Observable improvement, without prescribing an implementation.

## Confirmed requirements
What the developer or accepted project guidance actually states; identify the source.

## Scope
What this change covers.

## Non-goals
Explicit exclusions or proposed exclusions clearly marked for confirmation.

## Constraints
Explicit business, time, technology, or compatibility constraints and their sources.

## Success criteria
Observable, verifiable outcomes. Label proposed criteria; do not invent thresholds.

## Assumptions
Unconfirmed premises, their impact, and which need a human decision. Use None if empty.

## Open questions
Unresolved decisions, why they matter, and whether they block acceptance. Use None if empty.
```

For an explicit dry run or hypothetical example, present the draft in the response or an authorized temporary workspace. Do not allocate a real change ID or write a ticket into this repository. Apply the same distinction between confirmed facts and unknowns.

## Present and stop

Summarize the draft, assumptions, and unresolved decisions, linking the artifact when one was written. Request human acceptance of the specific Intent; acceptance-blocking questions must be resolved before recording acceptance. Preserve existing explicit acceptance for unchanged scope, and record new acceptance only when a human actually provides it, using the change conventions.

Stop after Intent Discovery. Do not generate `spec.md`, `plan.md`, a database schema, application code, or implementation scaffolding. Human acceptance of Intent permits Specification as a subsequent requested stage; it does not itself authorize Build.
