# Frontend guide – HZ Semester 3 UVE (Microservices Frontend, SvelteKit)

This file is the **baseline for all frontend code** in this repository. It summarises the course
"MS – Frontend" (lessons 1–5: introduction/layout/routing, components, events & reactivity,
pages & data, forms & authentication) as rules and defaults. It complements the backend guide
(`.claude/rules/backend.md`).

## 0. Priority order (read first)

1. **The codebase wins.** Before writing code, inspect `code/frontend/` (or wherever the
   SvelteKit app lives): `package.json` (Svelte/SvelteKit versions, scripts), `svelte.config.js`,
   `vite.config.*`, `src/routes/`, `src/lib/`, `src/hooks.server.ts`, `src/app.d.ts`, existing
   components, any `docs/assignments/*.md` and the README. Follow the existing patterns (file
   names, folder layout, TypeScript usage, styling approach). This guide only fills gaps.
2. **Design documents next.** If `docs/ux/` exists, pages and components implement its user
   flows, wireflow and prototype (screens, states, copy/tone of voice, accessibility decisions). If
   `docs/fds/` exists, data shapes follow its API specs / Logical Data Objects. The backend's actual
   responses (through the API Gateway) are the final truth for field names.
3. **This guide.**

**Case-agnostic:** no case content here. Routes, components and data come from the current case,
the UX deliverables and the backend. `<angle brackets>` are placeholders. Never reuse the course's
example domain unless it is the current case.

**Learning context:** the assessment asks students to explain their code. Write readable,
conventional Svelte 5 / SvelteKit code, keep diffs small, don't rewrite working starter code,
and end every task with a short explanation of what changed and why (which load option, which
rune, why server-side, etc.).

---

## 1. Stack and repository layout

| Concern | Default |
|---|---|
| Runtime / tooling | Node.js LTS, Vite (dev server with HMR, Rollup production build) |
| Framework | **SvelteKit** (routing, SSR, load functions, form actions) on **Svelte 5** (runes) |
| Language | TypeScript (`<script lang="ts">`, `.ts` load/action files) |
| Backend | Express microservices behind an API Gateway (see backend guide) |
| API testing | Bruno (or Postman) |

Typical monorepo layout: `code/frontend` (SvelteKit) and `code/backend/<service>` or
`code/services/<service>` (Express). Keep that layout if it exists.

Svelte = the component compiler (UI components → JS + CSS). SvelteKit = the app framework around
it (router, SSR/CSR, load functions, form actions, build optimisations). By default SvelteKit uses
**SSR** for every page: HTML is rendered on the server, then **hydrated** in the browser. Don't
switch pages to CSR/SPA mode (`ssr = false`) unless there is a stated reason.

## 2. Routing & layout

- File-based routing under `src/routes/`; one folder per route, kebab-case:
  `src/routes/<route-name>/`. Dynamic segments: `[id]`. Route groups: `(group)` for a shared
  layout without a URL segment (e.g. `(protected)`).
- Per route, only the files needed:
  - `+page.svelte` – the page UI
  - `+layout.svelte` – shared UI for the route and its children; render children with
    `{@render children()}` (Svelte 5 snippet), not `<slot />`
  - `+page.ts` – universal `load` (runs on server and in the browser)
  - `+page.server.ts` – server-only `load` and **form actions**
  - `+layout.server.ts` / `+layout.ts` – data shared by all child routes (e.g. the current user)
- Global navigation lives in the root `+layout.svelte` as a component (e.g. `NavBar.svelte`).
  Highlight the active link by comparing `page.url.pathname` (`$app/state`; fall back to
  `$app/stores` only if the installed SvelteKit version lacks `$app/state`). Set
  `aria-current="page"` on the active link.
- Use plain `<a href>` for navigation (SvelteKit intercepts it); `goto()` only after
  programmatic actions.
- Every page sets a `<title>` via `<svelte:head>`.

## 3. Components (atomic design)

- Reusable components in `src/lib/components/`, import via `$lib`. Organise by atomic design
  if the repo has no other convention: `atoms/` (button, input, badge), `molecules/` (form field
  with label + error, card), `organisms/` (nav bar, list with filters, booking form). Pages
  (`+page.svelte`) compose organisms; they contain little markup of their own.
- One component per file, PascalCase file name. Props typed with an interface.
- Styles: scoped `<style>` per component; shared design tokens (colours, spacing, type scale from
  the UX style guide) as CSS custom properties in one global file. Use the UX contrast-checked
  colours only.
- Accessibility (from the UX course): semantic elements (`button` for actions, `a` for
  navigation, `label` for every input, headings in order), visible focus, targets ≥ 44px on touch,
  no colour-only meaning, alt text, respect `prefers-reduced-motion`.

### Chain of command: props down, callbacks up
- **Props** (`$props()`): inputs owned by the parent; the child reads them.
- **State** (`$state()`): local data owned by the component itself.
- Child → parent communication: **callback props** (`onselect`, `onchange`) – not
  `createEventDispatcher` (Svelte 4). Two-way binding only via `$bindable()` when the parent must
  see every edit.

```svelte
<!-- Child.svelte -->
<script lang="ts">
  interface Props { title: string; onselect?: (id: string) => void }
  let { title, onselect }: Props = $props();
  let open = $state(false);
</script>
<button onclick={() => (open = !open)} aria-expanded={open}>{title}</button>
```

## 4. Events & reactivity (Svelte 5 runes)

- Events are attributes: `onclick={handler}`, `onsubmit`, `oninput` – **not** `on:click`.
- Never use Svelte 4 reactivity (`$:` statements, `export let`) in new code.

**Which rune – decision order (stop at the first that fits):**
1. Can it be **derived** from other values? → `$derived(...)` (or `$derived.by(() => ...)`).
   Don't use `$state` at all.
2. Does the child mutate it on its own (e.g. a field pre-filled from a prop that the user edits)?
   → `$state(initialValueFromProp)` (a one-time copy).
3. Must the parent see the edits? → `$bindable()` (two-way binding), not state + callback.
4. Must local state reset when a prop changes but stay mutable in between? → `$state` + `$effect`.
   This is the exception, not the default.
`$effect` is for side effects (syncing with something outside Svelte), never as the default way to
compute values.

**Sharing state between components (in this order of preference):**

| Option | When |
|---|---|
| Props down + callback props up | Parent/child; easiest, most explicit |
| **Context API** (`setContext` / `getContext`) | Same component tree; scoped per tree instance and **safe under SSR** – default when unsure |
| Shared state module (`<name>.svelte.ts` exporting a `$state` object) | Genuinely global, non-personal state (theme, UI preferences). It is a **singleton per server process**, so under SSR it is shared between all users' requests: **never put user/personal data or tokens in it** |
| Svelte stores (`writable` etc.) | Legacy (pre-Svelte 5); last resort / only if the repo already uses them |

The underlying idea is publish/subscribe (signals): changing state notifies every subscriber.

## 5. Loading data from the backend

The SvelteKit app talks to the **API Gateway** only, never directly to individual microservices.
Base URL from env: `API_URL` via `$env/dynamic/private` (server code) – inside Docker this is the
gateway's compose service name, not `localhost`. Use `PUBLIC_API_URL` (`$env/dynamic/public`)
only if the browser itself must call the API.

Three options – choose deliberately and say which one you used:

| Option | File | Runs | Use for |
|---|---|---|---|
| 1 | `+page.server.ts` → `load: PageServerLoad` | Server only | **Default.** Anything needing a token/cookie, private env vars, or data you don't want the browser to call directly |
| 2 | `+page.ts` → `load: PageLoad` | Server (SSR) and browser | Public data from a public API, no secrets |
| 3 | `onMount` + `fetch` in a component | Browser only | Client-only, non-critical or user-triggered data (e.g. a dropdown filled after interaction) |

Options 1 and 2: the page receives the result via `let { data }: PageProps = $props();` with
`PageProps` from `./$types`. Shared data for several pages → `+layout.server.ts`.

```ts
// src/routes/<route>/+page.server.ts
import type { PageServerLoad } from './$types';
import { error } from '@sveltejs/kit';
import { env } from '$env/dynamic/private';

export const load: PageServerLoad = async ({ fetch, params, locals }) => {
  const res = await fetch(`${env.API_URL}/<resources>/${params.id}`, {
    headers: locals.token ? { Authorization: `Bearer ${locals.token}` } : {}
  });
  if (!res.ok) error(res.status, 'Could not load <resource>');
  const body = await res.json();          // backend shape: { data, meta }
  return { <resource>: body.data };
};
```

Rules:
- Always use the `fetch` passed into `load` (not the global one) in load functions.
- Check `res.ok`; map backend errors (`{ error: { code, message } }`) to `error()` in loads and
  to `fail()` in actions; show user-friendly messages in the UX tone of voice.
- Type responses: put shared TypeScript types matching the API's Logical Data Objects in
  `src/lib/types/`.
- Every data-driven UI has loading, empty and error states (option 3 especially: `$state` for
  `data`, `error`, `loading`). Use the empty/error copy from the UX deliverables.
- Put API helper functions in `src/lib/server/api.ts` (server-only, so they can never be
  bundled to the browser).

## 6. Forms & form actions

- Forms use SvelteKit **form actions**: `<form method="POST">` in `+page.svelte`, `actions` in
  `+page.server.ts`. Named actions with `action="?/<name>"` when a page has several forms.
- Add progressive enhancement with `use:enhance` (`$app/forms`) so the page doesn't fully reload;
  the form must still work without JavaScript.
- In the action: read `await request.formData()`, validate, call the backend, then
  `return fail(400, { <fields>, message })` on errors (re-populate the form; never return
  passwords) or `redirect(303, '<target>')` on success. Read the result in the page via
  `let { form }: PageProps = $props();`.

```ts
// src/routes/<route>/+page.server.ts
import type { Actions } from './$types';
import { fail, redirect } from '@sveltejs/kit';

export const actions = {
  default: async ({ request, fetch }) => {
    const data = await request.formData();
    const name = String(data.get('name') ?? '').trim();
    if (!name) return fail(400, { name, message: 'Please enter a name.' });
    // call the API Gateway ...
    redirect(303, '/<next-route>');
  }
} satisfies Actions;
```

- Inputs: `name` attributes match the action, `<label for>` on every field, `required`/`type`
  for native validation, error messages next to the field and announced (`aria-describedby`,
  `aria-invalid`).

## 7. Authentication (JWT in an httpOnly cookie)

Architecture: the browser never sees or stores the JWT. Only the **SvelteKit server** talks to the
API Gateway with the token.

```
Browser ──form POST──► SvelteKit server ──POST /auth/login──► API Gateway → auth-service
        ◄─Set-Cookie (httpOnly)──         ◄──── { token } ────
Browser ──GET /portal──► SvelteKit server (hooks: cookie → locals.token)
                         ──GET /<protected> + Authorization: Bearer <token>──► API Gateway
```

Files:
- `src/routes/login/+page.svelte` + `+page.server.ts` – login form + action: POST credentials to
  the gateway's login endpoint; on success `cookies.set('<cookie-name>', token, { path: '/',
  httpOnly: true, sameSite: 'lax', secure: !dev, maxAge: <seconds matching token expiry> })` and
  `redirect(303, '<portal-route>')`; on failure `fail(401, { email, message })`.
- `src/hooks.server.ts` – `handle`: read the cookie into `event.locals.token` (and optionally
  the decoded/fetched user into `event.locals.user`) for every request.
- `src/app.d.ts` – type `App.Locals` (`token?: string; user?: <User>`), and `App.Error` if needed.
- Protected routes – in `+layout.server.ts` of a `(protected)` group, or in each
  `+page.server.ts`: `if (!locals.token) redirect(303, '/login')`. Then fetch with
  `Authorization: Bearer ${locals.token}`.
- `src/routes/logout/+page.server.ts` – action that `cookies.delete('<cookie-name>', { path: '/' })`
  and redirects to the login page.
- If the backend answers 401 (expired/invalid token): delete the cookie and redirect to login.

Never: put the token in `localStorage`/`sessionStorage`, return it from a `load` function, render it
in the page, store it in a shared `.svelte.ts` module, or log it. Hiding a link in the UI is not
security – the backend/gateway still enforces auth on every protected route.

## 8. Running & checking

- Dev: `npm run dev` in the frontend folder (Vite + HMR); backend via `docker compose up`.
- Before finishing: `npm run check` (svelte-check / TypeScript), `npm run build`, and `npm run
  lint` / `npm run format` if configured. Fix all errors and warnings you introduced
  (accessibility warnings from the Svelte compiler included).
- Actually run the app and click through the affected user flow, including error, empty and
  logged-out states. Report what you tested; never claim something works untested.

## 9. Definition of done

- [ ] Follows existing repo patterns; matches UX flows/wireframes and the backend's real API.
- [ ] Svelte 5 syntax only: runes, `onclick`-style events, `{@render children()}`, callback props.
- [ ] Right rune (derived first, bindable second, state + effect last) and right sharing option
      (context over global module; no personal data in shared modules).
- [ ] Data loaded with a deliberately chosen option (server load by default), `res.ok` checked,
      loading/empty/error states present.
- [ ] Forms use actions + `use:enhance`, server-side validation, `fail`/`redirect`.
- [ ] Token only in an httpOnly cookie, read in `hooks.server.ts`, protected routes redirect
      when logged out, logout clears the cookie.
- [ ] Accessible markup (labels, headings, focus, contrast, `aria-current`), UX copy used.
- [ ] `npm run check` and `npm run build` pass; flow tested in the browser.
- [ ] Short explanation of the change written for the student.
