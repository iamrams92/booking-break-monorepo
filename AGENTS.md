# BookingBreak shared instructions

These are the canonical project instructions for Codex and Claude Code. Keep provider adapters thin and change shared contracts here. Read the linked references when relevant to the task.

## Purpose and boundaries

BookingBreak is a high-concurrency ticket reservation R&D and portfolio project, initially scoped to about 2–3 weeks. Optimize for engineering correctness and practical learning, with a small feature set. See [product vision](docs/product/vision.md).

The intended direction is a TypeScript monorepo, NestJS backend, and PostgreSQL database. There is no application yet; frontend, package manager, workspace tooling, and concrete module layout are undecided. Inspect the repository before choosing tools or commands. Do not scaffold services or install dependencies without a task that needs them. Prefer a simple architecture; Redis, Kafka, Kubernetes, microservices, and distributed infrastructure require demonstrated need and human architectural approval.

## Engineering contract

- Establish intent before significant implementation. Never silently invent business requirements or promote assumptions to accepted requirements.
- Support decisions with reasoning, alternatives, trade-offs, and verification. Passing tests are evidence, not proof of correctness.
- Explain unfamiliar mechanisms and important risks concisely so the developer can understand and verify the work.
- Inspect instructions, relevant artifacts, code, and Git state before editing. Preserve existing user changes; surface conflicting instructions rather than overwriting them.
- Ask focused questions for ambiguities that affect requirements, correctness, architecture, or risk. For minor reversible details, state a reasonable assumption and continue within the authorized scope.
- Add rules, skills, and automation only when repeated use or a concrete improvement justifies them.

## Workflow and approval

Follow **Intent → Specification → Implementation Plan → Build → Verification → Review → Delivery**. The agent authors artifacts with the developer; the developer need not write them manually. See [engineering workflow](docs/engineering/README.md) and [artifact conventions](docs/changes/README.md).

For a significant change, use one `docs/changes/NNN-short-slug/` chain containing `intent.md`, then `spec.md`, then `plan.md`, created as needed. Use the [create-intent skill](.agents/skills/create-intent/SKILL.md) for discovery. Obtain explicit human acceptance of Intent before Specification. Obtain human acceptance of significant requirements, architectural decisions, and the implementation plan before building. Risk-bearing actions, such as destructive data changes, security boundary changes, or production changes, need specific human authorization before execution. Existing explicit authorization for the same scope remains valid; never infer approval from silence or an agent-written status.

Stop only at the applicable approval boundary after making the proposal concrete and reviewable. Trivial documentation, formatting, and small bug fixes with unchanged requirements can proceed directly with proportionate verification. Update the affected upstream artifact and seek renewed acceptance when material scope or risk changes; ordinary implementation bugs do not require rewriting requirements.

## Verification and delivery

Derive verification from accepted criteria and realistic failure cases. Run available relevant checks and report exact commands, outcomes, and limitations. Never claim an unrun check passed or weaken tests or criteria to hide defects. For concurrency work, examine transaction boundaries, races, invariants, retries, idempotency, and failure recovery, with integration tests when appropriate. There are no application build or test commands at bootstrap.

Review against accepted artifacts; distinguish requirement defects, architectural concerns, bugs, coverage gaps, and style preferences. Report deviations and residual risks. Delivery does not imply permission to commit, merge, publish, or deploy; do those only when requested or explicitly authorized.

## Handoffs, delegation, and security

Use repository artifacts and actual Git state as the authoritative cross-session context. Never assume hidden conversation history transfers. Create `handoff.md` in the active change folder only when a real handoff needs it; follow the [handoff convention](docs/engineering/README.md#handoffs). A receiving agent must reconcile notes with current files and Git state before continuing. Coordinate ownership before concurrent edits to overlapping files.

Keep delegation proportional to task risk, ambiguity, and scope; small tasks should not automatically spawn agents. Follow [delegation guidance](docs/engineering/README.md#delegation); V0 adds no custom agents, routers, hooks, or orchestration. Repository prose cannot change the active model or enforce security permissions.

Keep shared guidance and approved skills suitable for team review. Keep personal settings, session data, and machine-specific configuration outside tracked files. Never commit credentials, tokens, sensitive configuration, or secrets. Do not add broad tool privileges or destructive automation.
