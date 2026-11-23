# Engineering workflow

[Shared contracts](../../AGENTS.md) apply to both providers. [Change conventions](../changes/README.md) define the artifact states and acceptance record. Keep this guide technology-independent until actual code establishes useful conventions.

## Lifecycle

Use the full chain for significant features and changes. Small documentation fixes, formatting changes, or small bug fixes with unchanged requirements can go directly from the request to implementation, appropriate verification, and review. Explain scope when the distinction matters; avoid paperwork that adds no value.

| Stage | Agent work and evidence | Boundary |
| --- | --- | --- |
| Intent | Read project goals and relevant context; clarify the problem, outcome, scope, constraints, and observable success criteria; draft `intent.md` with assumptions and questions | Human explicitly accepts Intent before Specification |
| Specification | From accepted Intent, draft `spec.md` with functional requirements, business rules/invariants, relevant frontend/backend/database responsibilities, system constraints, acceptance criteria, failure/edge cases, and important design questions or decisions | Human accepts significant requirements and decisions; unknowns remain explicit |
| Implementation Plan | Inspect the repository; draft `plan.md` covering approach, affected modules/files, sequence, alternatives/trade-offs, risks, compatibility, and verification | Human reviews and accepts the significant plan and architectural choices before Build |
| Build | Implement the accepted scope in reviewable increments; explain unfamiliar mechanisms and important choices concisely | Seek specific authorization before risk-bearing actions; pause for material requirement/architecture changes |
| Verification | Run available checks against acceptance criteria and relevant failures; record commands, results, environment limits, and gaps in the plan | Do not substitute a green test suite for reasoning about correctness |
| Review | Compare implementation with accepted artifacts and inspect the diff; classify requirement defects, architecture concerns, bugs, test gaps, and style preferences separately | Resolve material defects or explicitly report unresolved risks for human disposition |
| Delivery | Summarize behavior, evidence, deviations, and remaining risks in the plan and delivery message; mark completed only when the accepted scope is actually done | Commit, merge, publish, and deploy require applicable authorization |

Acceptance of one stage does not accept later artifacts. Do not begin implementation while a significant requirement or architectural decision remains unaccepted. If an upstream decision materially changes, update it and its affected dependents, record the impact, and seek renewed acceptance. Fix implementation bugs directly when the approved requirements still hold. The workflow is iterative; keep feedback local to the artifact or code that is wrong.

## Verification proportional to risk

Use the tools and commands actually present in the repository. Record unrun, unavailable, or failed checks plainly. Never weaken accepted criteria or tests solely to make a result pass.

For future reservation/concurrency changes, reason about transaction boundaries and invariants, explore competing requests and partial failures, and verify idempotency, retries, and recovery as relevant. Use real PostgreSQL integration tests when database semantics matter; do not infer database correctness from mocks. Specify measurable load goals with the developer before making performance claims.

For harness/documentation edits, inspect references, skill metadata, adapter consistency, and the diff. No application test or build commands exist at V0.

## Handoffs

When another session or provider needs context that artifacts and the diff do not already make clear, add `handoff.md` inside the active change directory. Include:

- Current change ID, progress, and base branch/commit if available; describe uncommitted changes and file ownership.
- Accepted artifacts with acceptance evidence and any pending approvals.
- Completed tasks, pending tasks, and important decisions with rationale or links.
- Executed verification commands and actual results, including failures and unrun checks.
- Known issues, deviations, unresolved risks, and a concrete recommended next action.

Do not include secrets, raw session dumps, or personal configuration. The receiving agent reads project instructions and artifacts, inspects code and Git status/diff, and reconciles stale or conflicting handoff notes before continuing. A handoff never grants additional approval. Avoid overlapping concurrent writes unless ownership is explicitly coordinated.

## Delegation

Choose delegation and reasoning capacity by risk, ambiguity, and scope. Mechanical, well-defined work may suit faster models. Business invariants, concurrency, and architectural decisions need sufficient reasoning and independent evidence; security-sensitive work needs appropriate verification. Small tasks should not automatically spawn subagents. Coordinate bounded tasks and nonoverlapping ownership if delegation is explicitly chosen.

V0 provides no specialized agents, model router, external orchestrator, hooks, or automatic model switches. Verify current tool support before choosing a model or reasoning setting; repository instructions do not change the active session model. Add automation only after recurring work demonstrates its value.

## Provider compatibility and validation

The canonical skill lives in `.agents/skills/create-intent/SKILL.md`. `.claude/skills/create-intent` links to the same folder; `CLAUDE.md` contains only `@AGENTS.md`. Relative paths inside the skill are explicitly repository-root-relative, so invocation through either entry uses the same references. A symlink-preserving checkout is required for this adapter.

Official references for discovery and imports:

- [Codex skills](https://developers.openai.com/codex/skills): project `.agents/skills` discovery, metadata, and invocation.
- [Claude Code skills](https://code.claude.com/docs/en/skills): project `.claude/skills` discovery, symlinked skill folders, and slash invocation.
- [Claude Code memory/imports](https://code.claude.com/docs/en/memory): `@path` imports, including the shared `@AGENTS.md` arrangement.

Bootstrap checks on 2026-11-23:

| Check | Observed result |
| --- | --- |
| Installed tooling | `codex --version`: `codex-cli 0.166.0`; `claude --version`: `2.1.150 (Claude Code)` |
| Codex discovery | `codex debug prompt-input` with a temporary, isolated `CODEX_HOME` exited 0; the generated prompt contained the shared `AGENTS.md` instructions and a `create-intent` catalog entry resolving to the canonical project skill |
| Claude discovery | CLI stream-JSON initialization with temporary provider state, project setting sources, no MCP servers, and no enabled tools returned success and listed `create-intent` as a project command; no user/model prompt was submitted. The probe used the initialization protocol in the [official Python SDK source](https://github.com/anthropics/claude-agent-sdk-python/blob/main/src/claude_agent_sdk/_internal/query.py) without installing the SDK |
| Skill metadata | The bundled skill-creator `quick_validate.py` reported `Skill is valid!` for both the canonical path and the Claude symlink path |
| References and adapters | All 17 local Markdown links/anchors resolved; `CLAUDE.md` was exactly the shared import; the relative skill symlink resolved to the canonical folder with identical content |
| Intent dry run | Manual walkthrough of a synthetic reservation request produced only a Draft Intent in temporary storage, kept unspecified policies as blocking questions, and stopped for human acceptance; a before/after content snapshot confirmed no repository changes |
| Scope and security | Inspected every addition; no application code, dependencies, real change folder, credentials, personal paths, or provider permissions were introduced; credential-pattern checks found no matches |
| Git review | The directory had no Git metadata. Reviewed all additions, including the symlink, with `git diff --no-index` against an empty temporary directory; `--check` found no whitespace errors. No Git initialization or commit was performed |

Limits: neither provider ran a model-driven Intent session. The dry run was a manual workflow exercise, not an independent agent evaluation. Claude's shared import was checked against the file, local target, and official documentation; its expanded model context was not inspected. A symlink-preserving clone on another OS and remote/cloud discovery were not tested. Initial Codex help/version probes emitted a sandbox warning about optional PATH aliases; the isolated prompt/discovery probe succeeded without that warning.

After code or tooling is introduced, replace bootstrap-only descriptions and document the actual commands here. Recheck compatibility when adapters or provider behavior change; do not treat these dated results as a permanent guarantee.
