# Hemia SSO Implementation Flow

## Table Of Contents

- Architecture Contract
- Backend Files
- Environment And Config
- Redis Session Store
- Auth Module
- Session Endpoint Shape
- Frontend Private Route Flow
- Downstream Hemia API Calls
- Logout
- Tests
- Common Bugs

## Architecture Contract

The browser must never receive OAuth tokens. The browser receives one opaque session cookie,
usually named `<app>_session`, with `HttpOnly`. Redis stores the full session keyed by that
opaque id.

Expected browser cookie:

```txt
Set-Cookie: <app>_session=<uuid>; HttpOnly; Path=/; SameSite=Lax; Max-Age=604800; Secure
```

Expected Redis value:

```ts
type SsoSession = {
  id: string;
  user: AuthenticatedUser & {
    internalUserId?: string;
  };
  accessToken: string;
  refreshToken?: string;
  expiresAt: string;
};
```

Key names used by `@hemia/auth` core:

- `sso:pkce:<state>` for transient PKCE state.
- `sso:session:<id>` for browser sessions.
- `sso:service-token` for optional client-credentials service token cache.

## Backend Files

Recommended NestJS structure:

```txt
apps/api/src/config/sso.config.ts
apps/api/src/config/redis.config.ts
apps/api/src/modules/auth/auth.module.ts
apps/api/src/modules/auth/redis.module.ts
apps/api/src/modules/auth/redis-session-store.ts
apps/api/src/main.ts
```

Optional app-specific session facade:

```txt
apps/api/src/modules/auth/session.controller.ts
apps/api/src/modules/auth/session.service.ts
```

Only add a facade when the product needs a custom response shape such as `workspaces`.

## Environment And Config

Use typed namespaced config, not raw `process.env` in services.

Required env:

```txt
SSO_ISSUER=https://id.hemia.example
SSO_CLIENT_ID=<app-client-id>
SSO_CLIENT_SECRET=<secret-if-confidential-client>
SSO_AUDIENCE=<app-audience>
SSO_REDIRECT_URI=https://api.example.com/auth/callback
SSO_FRONTEND_URL=https://app.example.com
SSO_SCOPE=openid profile email offline_access <app>.access
REDIS_URL=redis://localhost:6379
```

Config pattern:

```ts
import { registerAs } from '@nestjs/config';
import type { SsoConfig } from '@hemia/auth';

const withoutTrailingSlash = (value: string): string =>
  value.endsWith('/') ? value.slice(0, -1) : value;

export default registerAs('sso', (): SsoConfig => {
  const issuer = withoutTrailingSlash(process.env.SSO_ISSUER ?? 'http://localhost:3000');

  return {
    issuer,
    clientId: process.env.SSO_CLIENT_ID ?? '<app-client-id>',
    clientSecret: process.env.SSO_CLIENT_SECRET || undefined,
    audience: process.env.SSO_AUDIENCE ?? '<app-audience>',
    jwksUrl: process.env.SSO_JWKS_URL ?? `${issuer}/.well-known/jwks.json`,
    authorizationUrl: process.env.SSO_AUTHORIZATION_URL ?? `${issuer}/oauth/authorize`,
    tokenUrl: process.env.SSO_TOKEN_URL ?? `${issuer}/oauth/token`,
    revocationUrl: process.env.SSO_REVOCATION_URL ?? `${issuer}/oauth/revoke`,
    logoutUrl: process.env.SSO_LOGOUT_URL ?? `${issuer}/oauth/logout`,
    redirectUri: process.env.SSO_REDIRECT_URI ?? 'http://localhost:3001/auth/callback',
    frontendUrl: process.env.SSO_FRONTEND_URL ?? 'http://localhost:3000',
    scope: process.env.SSO_SCOPE ?? 'openid profile email offline_access <app>.access',
    cookieName: process.env.SSO_COOKIE_NAME ?? '<app>_session',
    sessionTtlSeconds: 7 * 24 * 3600,
    cookieSecure: process.env.NODE_ENV === 'production',
    authorize: (claims) => {
      const permissions = Array.isArray(claims.permissions) ? claims.permissions : [];
      return permissions.includes('<app>.access') && !permissions.includes('<app>.disabled');
    },
    onUserResolved: async (user) => {
      // Create or update the internal app user here.
      return { internalUserId: user.ssoUserId };
    },
  };
});
```

Do not hardcode `<app>` in final code. Replace it with the product namespace, for example
`docs`, `console`, or another agreed prefix.

## Redis Session Store

`@hemia/auth` needs a `SessionStore` with `get`, `set`, and `delete`.

```ts
import { Inject, Injectable, OnModuleDestroy } from '@nestjs/common';
import type { Redis } from 'ioredis';
import type { SessionStore } from '@hemia/auth';

export const REDIS = Symbol('REDIS');

@Injectable()
export class RedisSessionStore implements SessionStore, OnModuleDestroy {
  constructor(@Inject(REDIS) private readonly redis: Redis) {}

  async get<T>(key: string): Promise<T | null> {
    const raw = await this.redis.get(key);
    return raw === null ? null : (JSON.parse(raw) as T);
  }

  async set<T>(key: string, value: T, ttlSeconds: number): Promise<void> {
    await this.redis.set(key, JSON.stringify(value), 'EX', ttlSeconds);
  }

  async delete(key: string): Promise<void> {
    await this.redis.del(key);
  }

  async onModuleDestroy(): Promise<void> {
    await this.redis.quit();
  }
}
```

Redis module pattern:

```ts
import { Global, Module } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import Redis from 'ioredis';
import { REDIS, RedisSessionStore } from './redis-session-store';

@Global()
@Module({
  providers: [
    {
      provide: REDIS,
      inject: [ConfigService],
      useFactory: (config: ConfigService) =>
        new Redis(config.getOrThrow<string>('redis.url')),
    },
    RedisSessionStore,
  ],
  exports: [REDIS, RedisSessionStore],
})
export class RedisModule {}
```

## Auth Module

```ts
import { Module } from '@nestjs/common';
import { ConfigModule, ConfigService } from '@nestjs/config';
import { SsoModule, SESSION_STORE } from '@hemia/auth/nestjs';
import type { SsoConfig } from '@hemia/auth';
import { RedisModule } from './redis.module';
import { RedisSessionStore } from './redis-session-store';

@Module({
  imports: [
    SsoModule.forRootAsync({
      imports: [ConfigModule, RedisModule],
      inject: [ConfigService],
      store: { provide: SESSION_STORE, useExisting: RedisSessionStore },
      useFactoryConfig: (config: ConfigService): SsoConfig =>
        config.getOrThrow<SsoConfig>('sso'),
    }),
  ],
})
export class AuthModule {}
```

`main.ts`:

```ts
app.use(cookieParser());
app.useGlobalFilters(new SsoExceptionFilter());
```

Do not make `SsoAuthGuard` global when the app has public endpoints, webhooks, health checks,
or service-to-service APIs. Use `@UseGuards(SsoAuthGuard)` per controller or route.

## Session Endpoint Shape

Built-in `@hemia/auth` session endpoint returns:

```json
{
  "authenticated": true,
  "user": {}
}
```

If the frontend needs product data, wrap or extend with an app facade:

```json
{
  "authenticated": true,
  "user": {},
  "permissions": [],
  "workspaces": [],
  "activeWorkspaceId": null
}
```

Never include `accessToken`, `refreshToken`, `idToken`, raw claims with secrets, or provider
responses.

## Frontend Private Route Flow

Preferred client/session check:

```ts
async function getSession() {
  const response = await fetch(`${apiBaseUrl}/auth/session`, {
    credentials: 'include',
    headers: { Accept: 'application/json' },
  });

  if (response.status === 401) {
    const loginUrl = new URL('/auth/login', apiBaseUrl);
    loginUrl.searchParams.set('returnTo', window.location.pathname + window.location.search);
    window.location.assign(loginUrl.toString());
    return null;
  }

  if (!response.ok) throw new Error('Session check failed');
  return response.json();
}
```

Next.js proxy/middleware can pre-gate by checking whether the session cookie exists, but still
call `/auth/session` in the app to get user data and to allow refresh logic.

Middleware pattern:

```ts
if (!req.cookies.get(SESSION_COOKIE)?.value) {
  const loginUrl = new URL('/auth/login', backendUrl);
  loginUrl.searchParams.set('returnTo', `${pathname}${search}`);
  return NextResponse.redirect(loginUrl);
}
```

Use avatar, nav, workspace switcher, and permission-aware UI from `/auth/session`.

## Downstream Hemia API Calls

Do not forward the opaque session cookie to downstream APIs expecting OAuth credentials. Resolve
the session server-side, then call downstream with the user access token:

```ts
const session = await ssoCore.getSession(request.cookies?.[cookieName]);
await hemiaIdAdmin.request({
  path: '/some-user-scoped-resource',
  auth: { authorization: `Bearer ${session.accessToken}` },
});
```

If the downstream service specifically expects a cookie named `access_token`, create that header
server-side only. Do not set `access_token` in the browser.

## Logout

Logout should:

1. Call `core.logout(sessionId)` or built-in `/auth/logout`.
2. Revoke access/refresh token best-effort when `revocationUrl` exists.
3. Delete `sso:session:<id>` from Redis.
4. Clear session cookie with same path/domain options.
5. Clear non-auth auxiliary cookies such as `<app>_workspace_id`.
6. Return success or redirect frontend to public/login page.

## Tests

Backend e2e:

- No cookie -> `GET /auth/session` returns 401 and `Missing session`.
- `/auth/login?returnTo=/x` persists `sso:pkce:<state>` and redirects to SSO.
- `/auth/callback` with valid code/state sets only `<app>_session`.
- Callback never sets `access_token` or `refresh_token` cookies.
- Redis session contains access/refresh token server-side.
- Expiring session refreshes using refresh token.
- Missing `<app>.access` is rejected.
- `<app>.disabled` is rejected.
- Logout clears cookie and deletes Redis session.

Frontend:

- Private route without session redirects to `/auth/login`.
- Session fetch uses `credentials: "include"`.
- Avatar/nav renders from `session.user`.
- 401 session response clears local UI state and redirects.

## Common Bugs

- Frontend calls `/me` while the real contract is `/auth/session`.
- App expects `/api/v1/auth/session`, but Nest has no global prefix.
- Cookie name mismatch between backend config and frontend middleware.
- `Secure` cookie enabled in local HTTP, causing browser not to store it.
- `SameSite=None` without `Secure`.
- Redis session key expires before cookie max age.
- `authorize` missing, so any valid SSO user can enter app.
- Downstream Hemia API receives `<app>_session` instead of user OAuth token.
- Tests use `access_token` cookies while production only sets `<app>_session`.
