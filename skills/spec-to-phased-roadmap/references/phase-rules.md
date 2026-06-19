# Phase Rules

Start with `Fase 00 - Alineacion inicial`.

Use phase numbering with two digits:

- `Fase 00`
- `Fase 01`
- `Fase 02`

Default phase order for software projects:

1. Alignment and decisions.
2. Base configuration and architecture.
3. Shared domain primitives.
4. Data model and entities.
5. Core modules and services.
6. External providers or integrations.
7. Public/internal APIs.
8. Events, webhooks, queues, or background processing.
9. Access, permissions, security, or policy enforcement.
10. Audit, reporting, observability, or support.
11. Hardening, tests, docs, migration/seeds, and release readiness.

Adapt order to the spec. Keep dependencies earlier than consumers.

Each phase must be implementable by an agent in one focused coding session when possible. If a phase is too large, split it.

Each phase must state:

- Objective.
- Tasks.
- Deliverable.
- Mandatory progress registration.

Add acceptance-oriented wording to deliverables. Prefer concrete outputs over broad goals.

Do not put future stretch work inside MVP phases. Add a later phase or a "Pendiente futuro" item.

