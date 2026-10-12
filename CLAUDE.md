# CLAUDE.md – UVE Project Week (HZ Semester 3) – car-auction

## Project context
- HZ University of Applied Sciences, ICT Semester 3, course **User Value Exploration (UVE)**.
- Project week: a case given by the teachers, built in a pair/team in one week.
- Deliverables: Functional Design Specification (FDS), UX design, microservices backend
  (Node/Express + API Gateway + auth), SvelteKit frontend, team portfolio.
- Assessment is **individual**: questions about the code (frontend + backend) and about UX + FDS.
  The student must be able to explain everything. Readable code, small diffs, and a short
  explanation after every task.
- The case changes every time. Never hard-code or reuse example domains from course slides.

## Language
- Talk to the user in **Dutch**.
- **All documentation in Dutch**: FDS, UX deliverables, `products/case-brief.md`, READMEs, portfolio
  products. This is the user's explicit request for Dutch as meant in the
  fds-builder and ux-builder skills. Course terms and diagram notation stay as taught where a
  translation would be unclear (e.g. Use Case, Basic Path, Exception Path, include/extend, DFD,
  ERD, persona, user flow, wireframe, MoSCoW, "The system SHALL" may become "Het systeem MOET").
- **Code in English**: identifiers, routes, API fields, database names, code comments.
- **GitHub in English, always**: issues (title + body), pull requests (title + description),
  branch names and commit messages.

## Where to find what

| What | Where |
|---|---|
| Teacher assignment / case description | `assignment/assignment.md` |
| Interviews (research input for UX + FDS) | `assignment/interviews.md` |
| Required products / deliverables | see "Deliverables" below (`assignment.md` is leading) |
| How to create GitHub issues | `ISSUES_CREATION.md` + `.github/ISSUE_TEMPLATE/` |
| Case summary (shared by FDS + UX) | `products/case-brief.md` (created by the skills) |
| FDS deliverables | `products/fds/` (`FDS.md`, `registry.yaml`, diagrams, API specs) |
| UX deliverables | `products/ux/` (`UX.md`, personas, flows, wireframes, prototype, test plan) |
| Backend rules (auto-loaded) | `.claude/rules/backend.md` |
| Frontend rules (auto-loaded) | `.claude/rules/frontend.md` |
| Skill: build the FDS | `.claude/skills/fds-builder/SKILL.md` → `/fds-builder` |
| Skill: build the UX deliverables | `.claude/skills/ux-builder/SKILL.md` → `/ux-builder` |
| Backend overview, run instructions | `code/backend/README.md`, `code/backend/docker-compose.yml` |

**Products live in `products/`, not `docs/`.** The teacher's `README.md` and `ISSUES_CREATION.md`
require analysis/design deliverables to be committed to `products/` as text (Markdown, Mermaid,
PlantUML). The skills and `.claude/rules/*.md` say `docs/`, `docs/fds/`, `docs/ux/`: read those as
`products/`, `products/fds/`, `products/ux/`.

## Deliverables
`assignment/assignment.md` lists 12 products; `products/products.md` lists only 9 (no journey
map, wireflow, activity diagram). **Decision: deliver all 12 from `assignment.md`.**

| # | Product | Notes |
|---|---|---|
| 1 | Persona | one issue per persona |
| 2 | Journey map | |
| 3 | System requirements | functional + non-functional, testable, acceptance criteria |
| 4 | 2 System Context Diagrams | Use Case Diagram + Level 0 DFD |
| 5 | Wireflow | |
| 6 | High fidelity prototype | explicitly checked against the (Nielsen) heuristics |
| 7 | Use Case specifications | **2 filled-in templates per student** |
| 8 | Activity diagram | **1 per student**, of one of their own use cases |
| 9 | Entity Relationship Diagram | 1 |
| 10 | UML Component diagram | 1, of the microservice architecture |
| 11 | API message design (Logical Data Objects) + API specification | per microservice |
| 12 | UXD testing | test script, questions and findings (separate issues for plan, script, results) |

## Repository map
```
car-auction/
├── CLAUDE.md                     # this file
├── README.md                     # project README (read first)
├── ISSUES_CREATION.md            # how issues must be created (follow it)
├── .github/ISSUE_TEMPLATE/
│   ├── coding-issue.yml          # template for coding issues
│   └── product-issue.yml         # template for product (deliverable) issues
├── .claude/
│   ├── rules/backend.md          # backend conventions (auto-loaded)
│   ├── rules/frontend.md         # frontend conventions (auto-loaded)
│   └── skills/fds-builder/, ux-builder/
├── assignment/
│   ├── assignment.md             # the case + assignment from the teachers
│   └── interviews.md             # stakeholder interviews
├── products/
│   ├── products.md               # list of products (incomplete, see "Deliverables")
│   └── (to be created)           # case-brief.md, fds/, ux/
└── code/
    ├── backend/
    │   ├── docker-compose.yml    # runs all backend services
    │   ├── README.md             # backend instructions
    │   ├── api-gateway-service/  # API Gateway – port 3000; proxies /auth → auth-service,
    │   │                         #   /cars → car-service (any method), GET /health; no DB;
    │   │                         #   no gateway auth yet (README chooses local JWT verify)
    │   ├── auth-service/         # auth / JWT – port 4001; POST /auth/register,
    │   │                         #   POST /auth/login, GET /auth/me (Bearer), GET /health;
    │   │                         #   SQLite, model User (id Int, email, passwordHash, role)
    │   └── car-service/          # domain service – port 4000; GET /cars (list only),
    │                             #   GET /health; SQLite, model Car (id uuid, make, model,
    │                             #   year, color, mileage, price, createdAt); 5 seed cars
    └── frontend/                 # EMPTY (.gitkeep): SvelteKit app still to be created here
```
Per service (same layout everywhere): `src/server.ts`, `src/routes/`, `src/services/`,
`src/middleware/errorHandling.ts` (`asyncHandler` + error handler), `src/lib/prisma.ts`,
`prisma/schema.prisma` + `seed.ts`, `entrypoint.sh` (migrate deploy + optional seed),
`test-api.js`. Stack: Node 24, TypeScript, native ESM (imports end in `.js`), Express 4,
Prisma 7 + SQLite (better-sqlite3 adapter), Zod. Prisma client is generated into
`src/generated/prisma` (git-ignored).

**Testing:** no Bruno/Postman collection and no `test-scenario.md` yet; only a smoke test per
service (`npm run test:api` → `test-api.js`).

## Known issues in the starter code
Don't fix these silently; when a task touches them, propose the fix and keep the diff small.

**Inconsistencies**
- Error body: car-service uses `{ error: { code, message } }` (the course standard);
  auth-service, the gateway's 404 and the gateway's 502 proxy errors use `{ error: "<message>" }`.
- Auth responses (`{ token, user }`, `{ user }`) are not wrapped in `data`; car-service is.
- Gateway: `cors()` allows every origin (should be `CORS_ORIGIN` from env); `express.json()`
  runs globally, which works only because every proxy uses `fixRequestBody` – new proxies need it too.
- Gateway has no request logging (car-service has `requestLogger.ts`).
- `README.md` links to `code/setup.md`, which doesn't exist (teacher's file, can't be fixed by us).
- `README.md` says `main`; the repo's default branch is `master` (see Git below).

**Gaps compared to the case/interviews (scope for sprint 2)**
- No **auction service** at all (Erik names one; also no auctions/bids model anywhere).
- No **message broker** (Erik: Redis, at-least-once, for the car-delete → "active auctions?"
  check with a 5-second timeout; handlers must be idempotent). Not in `docker-compose.yml`.
- car-service is read-only (`GET /cars`); no detail, create, update or delete; no filters
  (make, year range, condition); no soft delete.
- `Car` lacks case fields: condition, description, estimated value, photos, seller (owner) id.
  It has `price`, which in the case belongs to the auction (starting/reserve price).
- Roles are only `USER | ADMIN`; the case has buyer, seller and staff (design decision).
- Gateway does not verify JWTs and does not forward the user identity in headers; Erik says
  services trust identity headers from the gateway (matches the gateway README's option A).
- Timestamps are UTC; the UI must show Amsterdam time (Erik).

## Git
- Default branch is **`master`** (remote `origin` = `HZ-HBO-ICT-UVE-EXAMS/car-auction-assignment_team9`).
  Wherever `README.md` / `ISSUES_CREATION.md` say `main`, read `master`.
- Process (from `README.md`): issue → branch named after the issue → PR with `Closes #<n>` →
  review by a teammate → merge. No direct commits to `master`.

## Commands
**Backend – whole stack with Docker** (from `code/backend/`):
```bash
docker compose up --build    # gateway http://localhost:3000, car 4000, auth 4001
docker compose down          # stop (add -v to also delete the SQLite volumes)
```
Each container runs `prisma migrate deploy` on start. `SEED_DATABASE=true` (set in compose)
resets car-service to the 5 sample cars and recreates the demo users on every start.
Demo users: `student@example.com` / `admin@example.com`, password `supersecret1` (USER / ADMIN).
On Windows, file watching doesn't reach containers: after code changes run
`docker compose down` + `docker compose up --build`.

**Backend – per service locally** (from `code/backend/<service>/`, one terminal each):
```bash
cp .env.example .env
npm install                  # also runs prisma generate (postinstall)
npm run prisma:migrate       # prisma migrate dev (car-service, auth-service only)
npm run prisma:seed          # sample cars / demo users
npm run dev                  # tsx watch src/server.ts
npm run type-check           # tsc --noEmit
npm run build && npm start   # compile to dist/ and run
npm run test:api             # smoke test against the running service (in the gateway: end to end)
npm run prisma:generate      # after changing schema.prisma
npm run prisma:studio        # browse the database
```
New migration: `npx prisma migrate dev --name <change>` in the service folder.

**Frontend** (from `code/frontend/`, once the SvelteKit app exists): `npm run dev`,
`npm run check`, `npm run build`.

## Source of truth (when things conflict)
1. The **teacher's assignment instructions** and README files in the repo.
2. The **existing starter code** and its patterns.
3. The **case material** (description, interviews).
4. The **FDS and UX documents** in `products/`.
5. The rules files in `.claude/rules/`.
Report conflicts to the user instead of silently choosing.

## Work order
0. Read `assignment/assignment.md`, `assignment/interviews.md`, `README.md` and `code/backend/README.md`.
1. `/fds-builder` with `assignment/` as input (creates `products/case-brief.md` + FDS in `products/fds/`).
2. `/ux-builder` (personas, flows, wireframes, prototype, test plan in `products/ux/`; aligned with the FDS).
3. Backend: services, gateway, auth – endpoints and data models follow the FDS.
4. Frontend: create the SvelteKit app in `code/frontend/` (only when asked); screens follow the
   UX deliverables, data comes from the backend via the gateway.
After each step the user reviews the result before continuing.

## Working agreements
- Read the relevant assignment file before starting a task; do exactly what it asks.
- Issues: follow `ISSUES_CREATION.md` and the templates in `.github/ISSUE_TEMPLATE/`; only create
  issues on GitHub when the user asks.
- Ask the user when an assignment asks for a **design decision**; present the options and trade-offs.
- Keep `products/` in sync: if an endpoint, data model or screen changes, update the FDS/UX documents
  (or list the needed change).
- Never invent research or usability-test results; never commit secrets (`.env` stays out of git,
  keep `.env.example` up to date).
- Run and test what you build (Docker, Bruno/curl, browser). Report what was tested.
- Don't rewrite working starter code; keep changes minimal and explain them.
