# NestJS Backend Architecture

Use `SKILL.md` as the source of truth.

When the user asks to create, review, or modify backend code in `apps/api/`, follow the NestJS + TypeORM architecture rules in this skill.

Read references in this order:

1. `SKILL.md`
2. `references/backend-guide.md` when the task touches architecture, entities, migrations, permissions, configuration, uploads, auth, database work, or backend review.

Core rules:

- Keep modules feature-based under `apps/api/src/modules/<feature>/`.
- Use DTOs with validation for request payloads.
- Keep business logic out of controllers.
- Use typed namespaced config with `registerAs` and `ConfigModule.forRoot({ load: [...] })`.
- Keep TypeORM `synchronize: false`.
- Do not generate or run migrations unless explicitly instructed.
- If a change requires a migration, report: `requiere migración manual`.

Validate with `bun run --cwd apps/api build`. If touching services, guards, auth, permissions, database queries, or business logic, also run `bun run --cwd apps/api test`.
