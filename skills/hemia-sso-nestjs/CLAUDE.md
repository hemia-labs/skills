# Hemia SSO NestJS

Use `SKILL.md` as the source of truth.

When the user asks to implement, review, or repair Hemia SSO in a NestJS app, follow this skill.

Read references in this order:

1. `SKILL.md`
2. `references/sso-implementation-flow.md` when code changes touch backend auth, Redis session store, frontend private routes, `/auth/session`, permissions, user resolution, logout, or downstream Hemia API calls.

Core rules:

- Use `@hemia/auth/nestjs` for `/auth/login`, `/auth/callback`, `/auth/session`, `/auth/logout`.
- Store session data in Redis through `SESSION_STORE`.
- Browser receives only an HttpOnly opaque session cookie.
- Never return `access_token` or `refresh_token` to the browser.
- Frontend must call `/auth/session` with `credentials: "include"`.
- Validate product access in `authorize`, for example require `<app>.access` and block `<app>.disabled`.
- Resolve/create internal users in `onUserResolved`.
- Downstream user-authenticated Hemia API calls must resolve `session.accessToken` server-side.

Validate backend with `bun run --cwd apps/api build` and auth tests when available. Validate frontend with `bun run --cwd apps/web build` when UI or session fetch code changes.
