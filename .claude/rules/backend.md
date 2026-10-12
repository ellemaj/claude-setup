# Backend guide – HZ Semester 3 UVE (Microservices Backend)

This file is the **baseline for all backend code** in this repository. It summarises the course
"MS – Backend" (lessons 1–5: microservice architecture, REST, data & CRUD, API gateway,
authentication) as rules and defaults.

## 0. Priority order (read first)

1. **The codebase wins.** Before writing code, inspect the repository: folder structure,
   `README*.md`, `docker-compose.yml`, every `package.json`, existing services (e.g. a registry
   service, `api-gateway-service`, `auth-service`), middleware, Prisma schemas, error/response
   helpers, tests, Bruno/Postman collections and any `test-scenario.md`. Follow the existing
   patterns exactly (language JS vs TS, module style, naming, response and error shapes, folder
   layout). This guide only fills gaps.
2. **The design documents come next.** If `docs/fds/` exists, endpoints, resource names, data
   models and services must match its API specifications, Logical Data Objects, ERD and component
   diagram. If `docs/ux/` exists, the API must support the user flows. Report mismatches instead of
   silently diverging.
3. **This guide.**

**Case-agnostic:** this file contains no case content. The domain changes per project. All names
below in `<angle brackets>` are placeholders; resource names, entities and routes come from the
current case, the FDS and the existing code. Never reuse example domains from the slides.

**Learning context:** students must be able to explain every line. Prefer readable, conventional
code over clever code, keep diffs minimal, don't rewrite starter code that works, and end every
task with a short explanation of what changed and why. When an assignment explicitly asks for a
**design decision** (e.g. how the gateway verifies tokens, which routes need auth), and the README
does not already record one, present the options with trade-offs and ask the user to choose –
don't decide silently.

---

## 1. Tech stack (defaults – verify against the repo)

| Concern | Default |
|---|---|
| Runtime | Node.js v24 LTS |
| HTTP framework | Express (RESTful API per microservice; the gateway is also a microservice) |
| Language | Whatever the repo uses (starter auth/gateway code uses TypeScript, `.ts`) |
| ORM | Prisma (Prisma Client, Prisma Migrate, Prisma Studio) |
| Gateway proxy | `http-proxy-middleware` (`createProxyMiddleware`) |
| Auth | `jsonwebtoken` (JWT), password hashing as already used in `auth-service` |
| CORS | `cors` package |
| Containers | Docker Desktop + `docker compose` |
| API testing | Bruno (or Postman); collection kept in the repo |
| Frontend (other course) | Svelte, calls the API Gateway via AJAX/fetch |

Do not add new dependencies when an existing one already does the job. If a new one is needed,
say why.

## 2. Architecture rules

```
Client (Svelte frontend, AJAX)
        │  HTTP/JSON
        ▼
API Gateway  ── single entry point: routing/proxy, CORS, logging, authentication, (aggregation)
        │
        ├──► <service-a>  ── own database
        ├──► <service-b>  ── own database
        └──► auth-service ── issues & signs JWTs, own user store
```

- The frontend only talks to the **API Gateway**, never directly to a microservice.
- Each microservice has a **single capability** (one bounded context, DDD), is **individually
  deployable**, **replaceable**, and has its **own data store**. No service reads or writes
  another service's database; it references other services' data by **ID only** and calls
  their API when it needs more.
- Service boundaries follow the business domain: group use cases that belong together
  (matches the FDS "functional grouping"). If boundaries are unclear, prefer fewer, larger
  services (Fowler's "Monolith First" reasoning) over many tiny ones.
- The gateway holds no business data. Business rules live in the owning service.
- Each service: unique port, configured through environment variables; inside Docker use the
  compose service names as hosts (`http://<service-name>:<port>`), never `localhost`.

### Layering inside a service (MVC-style)

```
src/
  server.ts | app.ts        # express app, global middleware, mounts routers, listen(PORT)
  routes/<resource>.ts      # router: URI + method → handler; thin, no business logic
  services/<resource>Service.ts  # business logic + Prisma calls (the "model" side)
  middleware/               # logging, error handler, authenticate, validation
  utils/ or lib/            # prisma client singleton, asyncHandler, response/error helpers
prisma/schema.prisma        # this service's data model + migrations/ + seed
```
Routers call services; services call Prisma. Wrap async handlers (e.g. `asyncHandler`) so errors
reach the central error handler. One Prisma Client instance per service.

## 3. REST design rules

The six REST principles apply: uniform interface, client–server, **stateless** (every request is
self-contained; no server-side sessions), cacheable, layered system, code on demand (optional).

**Resource naming**
- Plural **nouns**, lowercase, hyphenated: `/<resources>`, `/<resources>/:id`.
- Nesting for ownership, max one level: `/<parents>/:parentId/<children>`.
- Optional version prefix if the repo uses it: `/api/v1/...`.
- **Never verbs in the URI** and never `POST` for reads. Wrong: `POST /getAll<Resources>`.
  Right: `GET /<resources>`.
- Filtering, sorting and paging via the query string: `GET /<resources>?<field>=<VALUE>&limit=20&offset=0&sort=<field>`.

**Methods and status codes**

| Action | Method | Endpoint | Success |
|---|---|---|---|
| Create | POST | `/<resources>` | **201 Created** (+ created object; optionally `Location` header) |
| Read all | GET | `/<resources>` | 200 |
| Read one | GET | `/<resources>/:id` | 200 |
| Replace | PUT | `/<resources>/:id` | 200 |
| Partial update | PATCH | `/<resources>/:id` | 200 |
| Delete | DELETE | `/<resources>/:id` | **204 No Content** (no body) |

Errors: 400 invalid input, 401 not authenticated (missing/invalid/expired token), 403
authenticated but not allowed, 404 not found (also for soft-deleted records), 409 conflict
(duplicate / business-rule violation), 500 unexpected. Never return 200 with an error inside.
Don't use the new `QUERY` method unless explicitly asked.

**Response shapes** (match the repo if it already defines them):
```json
{ "data": [ { "id": 1, "url": "/<resources>/1", "...": "..." } ],
  "meta": { "count": 1, "limit": 20, "offset": 0, "url": "/<resources>?limit=20&offset=0" } }
```
- `data` = payload (object for one item, array for a collection); `meta` = metadata (count,
  pagination, request url, title/date where useful). This matches the FDS Meta/Data split.
- Include a link (`url`) to each item's own resource (hypermedia).
- Error body, everywhere (gateway and services):
```json
{ "error": { "code": "<UPPER_SNAKE_CODE>", "message": "<human readable explanation>" } }
```
For each resource, think through: URI, which methods to expose, representation accepted from the
client, representation served to the client, links to related resources, error conditions.

**Input** – read `req.params`, `req.query`, `req.body` explicitly; validate required fields,
types and enum values before touching the database; respond 400 with a clear message.

**CORS** – the browser enforces the Same-Origin Policy; CORS headers relax it. Configure `cors`
on the gateway (the browser's only contact point) with an explicit origin from env
(e.g. `CORS_ORIGIN=http://localhost:<frontend-port>`), not `*` beyond quick local tests.

## 4. Data & CRUD (Prisma)

- Each service has its own `prisma/schema.prisma` and database. Derive models from the FDS ERD:
  same entities, keys and cardinalities. Use `@@id([...])` for composite keys and
  `@@unique([...])` for business constraints – don't blindly add an `id` that removes a
  constraint without adding a unique rule.
- Change the schema only through migrations: `npx prisma migrate dev --name <change>`, then
  regenerate the client. Keep a seed script with realistic dummy data if the repo has one.
- Queries: use `select`/`include` deliberately (only what the response needs), `orderBy` for
  stable lists, `where` built conditionally (spread operator for optional filters).
- **Soft delete** is the default for domain records: fields `deleted Boolean @default(false)` and
  `deletedAt DateTime?` (follow the repo's naming, e.g. `deleted_at`). `DELETE` sets them; every
  read filters `deleted: false`. Only hard-delete when explicitly required.
- **History/audit data** (e.g. consent or status changes that must be provable over time):
  append-only records with a timestamp and a version field, one-to-many to the owning entity;
  never overwrite them.
- Choosing SQL vs NoSQL for a new service: justify in that service's README (relations and
  consistency → SQL; flexible documents/scale → NoSQL; CAP: under a network partition you choose
  consistency or availability).
- Connection strings come from env (`DATABASE_URL`), never hard-coded.

## 5. API Gateway

- Proxy with `http-proxy-middleware`:
  ```ts
  app.use('/<resources>', createProxyMiddleware({ target: process.env.<SERVICE>_URL, changeOrigin: true }));
  ```
  Use `pathRewrite` only when the external path differs from the service's internal path.
- Middleware order: `cors` → request logging → `authenticate` (only on protected routes) →
  proxy → 404 fallback → error handler.
- **Gotcha:** `express.json()` placed before the proxy consumes the request body, so proxied
  POST/PUT/PATCH requests arrive empty or hang. Don't parse bodies globally in the gateway, or use
  `fixRequestBody` from `http-proxy-middleware`.
- Proxy `/auth` to `auth-service` like any other route.
- The gateway may aggregate (combine several service calls into one response) only where a user
  flow clearly benefits; document it.
- Benefits to keep in mind (and mention in READMEs when relevant): simple client interface,
  security/authentication in one place, versioning, aggregation, caching, logging, load
  balancing. An API gateway is not a load balancer (which spreads load over identical instances).

## 6. Authentication & authorization (JWT)

- JWT (RFC 7519) = `header.payload.signature`, signed with a secret (HS256) or a key pair
  (RS256). The payload is only base64-encoded, **not encrypted**: put only `sub` (user id), role(s)
  and `exp` in it – never passwords or personal data. Always set an expiry (`expiresIn`).
- `auth-service` registers/logs in users (passwords hashed, never stored or logged in plain text)
  and issues tokens; it also exposes `GET /auth/me` for the current user.
- This enables **stateless session management** and **single sign-on** across services: no
  server-side session, every request carries `Authorization: Bearer <token>`.
- **Gateway verification – design decision (record it in `api-gateway-service/README.md`,
  section "Authentication"):**
  - Option A – local verification: gateway verifies with `jsonwebtoken` and a shared
    `JWT_SECRET`. Fast, no network call; two services must keep the same secret in sync.
  - Option B – ask auth-service: gateway calls `GET /auth/me` with the token on every protected
    request and only proxies on success. Secret stays in one service; extra round trip and a hard
    dependency on auth-service availability.
  If the README already records a choice, implement that. Otherwise present both and ask.
- **`authenticate` middleware** (gateway: `src/middleware/authenticate.ts`, using
  `auth-service/src/middleware/authenticate.ts` as reference):
  1. Read `Authorization: Bearer <token>`.
  2. Valid → attach the decoded payload/user to `req` (e.g. `req.user`) and call `next()`.
  3. Missing, malformed, wrong signature or expired → `401` with the standard error body and a
     specific code (e.g. `TOKEN_MISSING`, `TOKEN_INVALID`, `TOKEN_EXPIRED`).
  4. Role checks (authorization) are a separate middleware returning `403`.
- **Protect per route, not all-or-nothing.** Decide route by route and document it in the gateway
  README as a table: Method | Path | Auth required | Role | Reason. Apply `authenticate` only to
  those routes (mount it before the proxy for specific path patterns, or filter inside the
  middleware by method + path). Typical reasoning: public reads of non-personal data may be
  open; anything personal, any write and any delete requires a token.
- Verify the middleware before wiring it in: valid token passes; missing token, token signed
  with the wrong secret and a truncated token are rejected with 401.
- Secrets live in `.env` (strong, long `JWT_SECRET`), are loaded via env, and are **never
  committed**. Keep an up-to-date `.env.example` without real values, and make sure `.env` is in
  `.gitignore`.

## 7. Docker & running

- Every service and database is a service in `docker-compose.yml` with its own port; fix port
  collisions in compose and env, not in code.
- Start: `docker compose up --build`; stop: `docker compose down`. On Windows, file-watch reload
  (nodemon) often doesn't work inside Docker: restart with `docker compose down` +
  `docker compose up` after changes (or configure polling) and mention this to the user.
- Run migrations inside the right container or against the right `DATABASE_URL`.

## 8. Testing

- Maintain a Bruno collection (or Postman, whichever the repo uses) with: per endpoint the happy
  path + main error cases (400/401/403/404), an auth flow (register → login → store token as an
  environment variable → call protected route) and requests through the **gateway** (not only
  directly to services). If `test-scenario.md` exists, the collection must cover it.
- After implementing, actually run the stack and exercise the endpoints (curl or Bruno CLI if
  available). Report what was tested and the results; never claim something works untested.
- Use the existing unit-test setup if the repo has one; add tests for middleware and services.

## 9. Code conventions

- async/await everywhere, errors go to a central error handler (via `asyncHandler`), no
  unhandled promise rejections, no `console.log` of tokens or passwords.
- Request logging middleware (method, path, status, duration; user agent if requested).
- Configuration only through env variables with clear names (`PORT`, `DATABASE_URL`,
  `JWT_SECRET`, `<SERVICE>_URL`, `CORS_ORIGIN`); fail fast at startup if a required one is missing.
- English names; resources plural in routes, singular in models (`<Resource>` model,
  `/<resources>` route).
- TypeScript (if used): strict typing, no `any` unless unavoidable; extend Express' `Request`
  type for `req.user` instead of casting everywhere.
- Small, focused functions; comments explain *why*, not *what*.

## 10. Definition of done (check before finishing a task)

- [ ] Matches existing repo patterns and the FDS API spec / ERD / component diagram (or the
      mismatch is reported).
- [ ] REST rules: nouns, correct methods, correct status codes, `data`/`meta` and
      `{ error: { code, message } }` shapes.
- [ ] Input validated; soft delete respected in reads; no cross-service database access.
- [ ] Routed through the gateway; CORS correct; protected routes require a valid JWT; route
      protection table updated in the README.
- [ ] No secrets in code or git; `.env.example` updated; migrations created.
- [ ] Stack runs in Docker; endpoints tested (Bruno/curl) including error cases; results reported.
- [ ] README of each changed service updated (routes, env vars, design decisions).
- [ ] Short explanation of the change written for the student.
