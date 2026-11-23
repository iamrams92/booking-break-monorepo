# BookingBreak

A deliberately scoped high-concurrency ticket reservation system for hands-on software engineering R&D and a technical portfolio. The initial learning scope is about 2–3 weeks, prioritizing correctness over feature count.

This repository currently contains the Phase 0 AI development harness only. There are no application services, dependencies, build commands, or tests yet. The intended stack is a TypeScript monorepo with NestJS and PostgreSQL; the frontend, package manager, and workspace tooling remain undecided until needed.

## Start a change

Open this directory in either tool. Describe the problem and outcome in natural language:

- **Codex:** `$create-intent Help me clarify <problem> so that <desired outcome>.`
- **Claude Code:** `/create-intent Help me clarify <problem> so that <desired outcome>.`

The skill reads project context, asks necessary questions, and drafts `docs/changes/NNN-short-slug/intent.md`. Review the draft, resolve blocking questions, and explicitly accept that artifact before requesting Specification. Acceptance of Intent does not authorize implementation. Subsequent specification, planning, build, verification, review, and delivery follow the [engineering workflow](docs/engineering/README.md).

No first ticket is included. When ready, replace the angle-bracket text above with your actual problem; do not treat the invocation example as a requirement.

## Harness map

| File | Purpose |
| --- | --- |
| [AGENTS.md](AGENTS.md) | Shared project contracts for both providers |
| [CLAUDE.md](CLAUDE.md) | Claude's import of the shared instructions |
| [Product vision](docs/product/vision.md) | Purpose, learning goals, constraints, and open choices |
| [Engineering guide](docs/engineering/README.md) | Lifecycle, handoffs, delegation, and provider compatibility |
| [Change conventions](docs/changes/README.md) | Artifact names, states, acceptance, and history |
| [create-intent](.agents/skills/create-intent/SKILL.md) | Canonical Intent Discovery skill |
| `.claude/skills/create-intent` | Relative symlink to the canonical skill folder |
| [.gitignore](.gitignore) | Local secrets, credentials, and session/config exclusions |

Add `docs/architecture/` when there is a real architectural proposal or accepted decision to record. Avoid placeholder architecture and speculative rulebooks.

## Provider setup

Codex discovers the project skill in `.agents/skills/`; Claude Code uses `.claude/skills/`. Edit only the canonical skill. The Claude entry is a directory symlink, so use a checkout that preserves symlinks. On Windows this may require Developer Mode and Git's `core.symlinks` support. If unavailable, replace that entry locally with a small skill adapter instructing Claude to read `.agents/skills/create-intent/SKILL.md` from the repository root; do not maintain a second workflow copy.

Start a fresh session if the new skill is not visible. In Codex, check `/skills` or the skill picker; in Claude Code, type `/` and look for `create-intent`, and use `/memory` to inspect project instructions. See [compatibility and validation](docs/engineering/README.md#provider-compatibility-and-validation) for measured results and limits.

The bootstrap directory was not Git-initialized. The files are ready for version control; no commit, remote, personal provider settings, or execution permissions are configured by this harness.
# booking-break-monorepo
