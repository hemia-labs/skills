---
name: hemia-sso-nestjs
description: Implementacion y revision del flujo SSO de Hemia en aplicaciones NestJS con @hemia/auth, Redis session store, cookies HttpOnly y frontend privado. Usar cuando se agregue, audite o repare login SSO, /auth/session, /auth/login, /auth/callback, logout, permisos de acceso, sesiones Redis, PKCE, refresh token server-side, guards NestJS o consumo frontend con credentials include.
---

# Hemia SSO NestJS

## Objetivo

Implementar o revisar SSO de Hemia en apps NestJS usando `@hemia/auth` como fuente del flujo
OAuth/OIDC, Redis como session store, cookies HttpOnly como unico estado del browser, y frontend
que consulta sesion con `credentials: "include"`.

Para detalles completos y snippets, leer [references/sso-implementation-flow.md](references/sso-implementation-flow.md).

## Principios No Negociables

- No mandar `access_token` ni `refresh_token` al browser, ni en JSON ni en cookies.
- Cookie principal = id de sesion opaco: `<app>_session` o nombre acordado.
- Tokens viven server-side en Redis dentro de la sesion.
- Frontend no lee la cookie HttpOnly; solo la envia automaticamente con `credentials: "include"`.
- Login privado inicia en frontend o proxy/middleware, pero la sesion se valida en backend.
- `/auth/session` es el endpoint "me" principal salvo que el proyecto tenga contrato distinto.
- Cualquier endpoint que necesite llamar APIs Hemia con el usuario debe resolver sesion -> `accessToken` server-side.
- Permisos de producto se validan durante callback con `authorize`.
- Mapeo/creacion de usuario interno se hace con `onUserResolved`.

## Flujo Esperado

1. Frontend carga ruta privada.
2. Frontend llama `GET /auth/session` o el proxy verifica cookie de sesion.
3. Si no hay cookie, backend responde `401 Missing session` o frontend redirige a `/auth/login?returnTo=/ruta`.
4. Backend crea `state`, `nonce`, PKCE `codeVerifier` y guarda `sso:pkce:<state>` en Redis.
5. Backend redirige a `SSO_AUTHORIZATION_URL` con `openid profile email offline_access <app>.access`.
6. Usuario autentica en SSO.
7. SSO redirige a `GET /auth/callback?code=...&state=...`.
8. Backend valida `state`, canjea `code` por `access_token`, `id_token`, `refresh_token`.
9. Backend valida JWT con JWKS, valida permisos, resuelve usuario interno.
10. Backend guarda sesion en Redis: `sso:session:<uuid>` con `accessToken`, `refreshToken`, `expiresAt`, `user`.
11. Backend setea cookie: `<app>_session=<uuid>; HttpOnly; Path=/; SameSite=Lax; Max-Age=7d`.
12. Backend redirige al frontend.
13. Frontend vuelve a llamar `GET /auth/session` con cookie automatica.
14. Backend lee cookie, carga Redis, refresca si expira pronto, responde datos de sesion.

## Implementacion Backend

Usar `@hemia/auth/nestjs`:

- Registrar `SsoModule.forRootAsync`.
- Proveer `SESSION_STORE` con una implementacion Redis.
- Cargar config tipada desde `ConfigModule`.
- Activar `cookieParser()`.
- Activar `SsoExceptionFilter`.
- Proteger rutas con `SsoAuthGuard` solo donde aplique, no globalmente si hay endpoints publicos o service-to-service.

Config minima:

- `issuer`
- `clientId`
- `clientSecret` si aplica
- `audience`
- `jwksUrl`
- `authorizationUrl`
- `tokenUrl`
- `revocationUrl`
- `logoutUrl`
- `redirectUri`
- `frontendUrl`
- `scope`
- `cookieName`
- `sessionTtlSeconds`
- `cookieSecure`
- `authorize`
- `onUserResolved`

## Implementacion Frontend

- Rutas privadas llaman `GET /auth/session` con `credentials: "include"`.
- Si recibe 401, redirigir a `/auth/login?returnTo=<path>`.
- Nunca buscar ni parsear `access_token` en JS.
- Mostrar avatar/nav desde `session.user`.
- Si hay workspace activo, usar cookie separada no auth, por ejemplo `<app>_workspace_id`; limpiarla en logout.

## Checklist De Revision

- `/auth/login`, `/auth/callback`, `/auth/session`, `/auth/logout` existen.
- Redis store persiste JSON con TTL.
- `state` PKCE expira rapido.
- Cookie de sesion tiene `HttpOnly`, `Path=/`, `SameSite=Lax`, `Max-Age`, `Secure` en prod.
- `scope` incluye `openid profile email offline_access` y permiso de app.
- `authorize` exige `<app>.access` y bloquea `<app>.disabled`.
- `onUserResolved` crea/actualiza usuario interno sin exponer tokens.
- `/auth/session` responde solo datos seguros: `authenticated`, `user`, `permissions`, `workspaces` si aplica.
- Logout revoca tokens best-effort, borra sesion Redis y limpia cookies auxiliares.
- Frontend usa `credentials: "include"`.
- APIs downstream usan access token server-side, no cookie opaca.

## Validacion

Backend:

```bash
bun run --cwd apps/api build
bun run --cwd apps/api test
```

Frontend:

```bash
bun run --cwd apps/web build
```

Si solo se crea documentacion o skill, validar estructura:

```bash
find skills/hemia-sso-nestjs -maxdepth 3 -type f | sort
```
