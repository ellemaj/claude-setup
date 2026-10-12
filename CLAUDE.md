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
- **All documentation in Dutch**: FDS, UX deliverables, `docs/case-brief.md`, READMEs, portfolio
  products, issue texts. This is the user's explicit request for Dutch as meant in the
  fds-builder and ux-builder skills. Course terms and diagram notation stay as taught where a
  translation would be unclear (e.g. Use Case, Basic Path, Exception Path, include/extend, DFD,
  ERD, persona, user flow, wireframe, MoSCoW, "The system SHALL" may become "Het systeem MOET").
- **Code in English**: identifiers, routes, API fields, database names, code comments, commit
  messages.

## Where to find what

| What | Where |
|---|---|
| Teacher assignment / case description | `assignment/assignment.md` |
| Interviews (research input for UX + FDS) | `assignment/interviews.md` |
| Required products / deliverables | `products/products.md` |
| How to create GitHub issues | `ISSUES_CREATION.md` + `.github/ISSUE_TEMPLATE/` |
| Case summary (shared by FDS + UX) | `docs/case-brief.md` (created by the skills) |
| FDS deliverables | `docs/fds/` (`FDS.md`, `registry.yaml`, diagrams, API specs) |
| UX deliverables | `docs/ux/` (`UX.md`, personas, flows, wireframes, prototype, test plan) |
| Backend rules (auto-loaded) | `.claude/rules/backend.md` |
| Frontend rules (auto-loaded) | `.claude/rules/frontend.md` |
| Skill: build the FDS | `.claude/skills/fds-builder/SKILL.md` → `/fds-builder` |
| Skill: build the UX deliverables | `.claude/skills/ux-builder/SKILL.md` → `/ux-builder` |
| Backend overview, run instructions | `code/backend/README.md`, `code/backend/docker-compose.yml` |

## Repository map
<!-- Claude: at the start of the first session, read the files below and complete the
"(to fill)" parts: per service its port, main routes, database and key files; whether a
Bruno/Postman collection or test-scenario exists. Keep it short and update it when the
structure changes. -->
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
│   └── products.md               # list/description of the products to deliver
├── docs/                         # (to be created) case-brief.md, fds/, ux/
└── code/
    ├── backend/
    │   ├── docker-compose.yml    # runs all backend services
    │   ├── README.md             # backend instructions
    │   ├── api-gateway-service/  # API Gateway – port (to fill), routes (to fill)
    │   ├── auth-service/         # auth / JWT – port (to fill), routes (to fill)
    │   └── car-service/          # domain service – port (to fill), routes (to fill)
    └── frontend/                 # EMPTY (.gitkeep): SvelteKit app still to be created here
```
- If `products/products.md` prescribes where or how products must be delivered, follow that
  instead of the default `docs/` locations used by the skills, and update the table above.

## Commands
<!-- Claude: fill in from package.json scripts, docker-compose.yml and READMEs. -->
_Not filled in yet._ Expected: backend from `code/backend/`: `docker compose up --build` /
`docker compose down`; Prisma migrate commands per service; frontend from `code/frontend/`
(after the SvelteKit app exists): `npm run dev`, `npm run check`, `npm run build`.

## Source of truth (when things conflict)
1. The **teacher's assignment instructions** and README files in the repo.
2. The **existing starter code** and its patterns.
3. The **case material** (description, interviews).
4. The **FDS and UX documents** in `docs/`.
5. The rules files in `.claude/rules/`.
Report conflicts to the user instead of silently choosing.

## Work order
0. Read `assignment/assignment.md`, `products/products.md`, `README.md` and `code/backend/README.md`.
1. `/fds-builder` with `assignment/` as input (creates `docs/case-brief.md` + FDS).
2. `/ux-builder` (personas, flows, wireframes, prototype, test plan; aligned with the FDS).
3. Backend: services, gateway, auth – endpoints and data models follow the FDS.
4. Frontend: create the SvelteKit app in `code/frontend/` (only when asked); screens follow the
   UX deliverables, data comes from the backend via the gateway.
After each step the user reviews the result before continuing.

## Working agreements
- Read the relevant assignment file before starting a task; do exactly what it asks.
- Issues: follow `ISSUES_CREATION.md` and the templates in `.github/ISSUE_TEMPLATE/`; only create
  issues on GitHub when the user asks.
- Ask the user when an assignment asks for a **design decision**; present the options and trade-offs.
- Keep `docs/` in sync: if an endpoint, data model or screen changes, update the FDS/UX documents
  (or list the needed change).
- Never invent research or usability-test results; never commit secrets (`.env` stays out of git,
  keep `.env.example` up to date).
- Run and test what you build (Docker, Bruno/curl, browser). Report what was tested.
- Don't rewrite working starter code; keep changes minimal and explain them.
