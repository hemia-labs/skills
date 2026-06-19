# Update Rules

Use these rules when coding from a roadmap or updating progress after work.

## Before Work

1. Read `docs/roadmap/roadmap_desarrollo_<project>.md`.
2. Read `docs/roadmap/<project-slug>-development-progress.md`.
3. Identify current incomplete phase.
4. Continue pending work before starting the next phase.
5. If user asks for a specific phase, work on that phase and note skipped dependencies.

## After Work

Update only the relevant phase section unless multiple phases changed.

Record:

- Files created or modified.
- Entities, DTOs, services, controllers, modules, scripts, configs, migrations, and seeds implemented.
- Endpoints added or changed.
- Tests run and exact command names.
- Tests not run and why.
- Migration status.
- Seed status.
- Pending work.
- Decisions and blockers.
- Exact roadmap reference.

## Completion Criteria

Mark `completada` only when:

- Phase deliverable is implemented.
- Build/typecheck/test or suitable validation ran.
- Remaining work is either no aplica or moved to a later explicit phase.
- Progress doc includes evidence.

If validation cannot run, keep phase `en progreso` or explain why completion is still acceptable.

## Consistency

- Match existing progress document style.
- Preserve prior phase history.
- Do not rewrite unrelated sections.
- Do not remove blockers or notes unless resolved.
