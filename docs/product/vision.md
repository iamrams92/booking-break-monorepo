# Product vision

BookingBreak is a high-concurrency ticket reservation R&D project and technical portfolio, not a commercial product or startup. Its purpose is to strengthen fullstack engineering understanding through real implementation challenges and documented trade-offs.

## Learning outcomes

- Backend architecture, business logic, API design, and fundamental design principles.
- Relational modeling, SQL, indexing, transactions, and database behavior.
- Concurrency, race conditions, data consistency, and invariants.
- Idempotency, retries, failure handling, and recovery.
- Automated and integration testing that exercises meaningful failure cases.
- Observability, deployment, and DevOps fundamentals as the application needs them.
- Evidence-based architectural choices and the ability to explain their trade-offs.

## Scope and constraints

Keep the initial implementation suitable for approximately 2–3 weeks of a small hands-on project. Prioritize correctness, understandable design, and learning over feature quantity. The exact feature scope and success criteria will be established through accepted change artifacts.

Technology direction: TypeScript, a NestJS backend, PostgreSQL, and a monorepo. These are project constraints, not evidence that any application or tooling already exists. Frontend technology, package manager, workspace tooling, and application layout are open choices; confirm them when relevant to a change.

Prefer a simple architecture. Do not introduce microservices, Redis, Kafka, Kubernetes, distributed infrastructure, or abstractions merely because high-concurrency systems sometimes use them. Any added dependency or infrastructure must address a demonstrated requirement.

## Unspecified business behavior

Ticket inventory rules, reservation lifetime, payment behavior, authentication, allocation policy, and concurrency/load targets are not yet requirements. Discover relevant behavior with the developer before specifying it. Keep business intent separate from architectural decisions and implementation mechanics.

Phase 0 establishes only the project harness. It does not start a feature ticket or authorize application development.
