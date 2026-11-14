# TypeScript and framework practices

Consult this while you implement. The framework is the one recorded in the product's ADR. If no framework is recorded yet, go back to step 1 of the SKILL workflow.

## TypeScript (every framework)

- **Compiler settings.** Turn on `strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes` and `noImplicitOverride`, and turn on ESLint's `typescript-eslint` strict-type-checked rules.
- **Model the domain with types.** Use discriminated unions for state (`{ status: 'loading' } | { status: 'error'; error: AppError } | { status: 'success'; data: T }`) instead of a cluster of booleans. Use branded types for IDs when mixing them up would be a bug.
- **Parse untrusted data at the boundary.** Data from APIs, storage, URLs or forms goes through a schema (Zod or Valibot). Infer the TS types from the schema, so there's one source of truth.
- **Types and interfaces.** Use `type` for unions and compositions, and `interface` for object contracts meant to be extended. Pick one convention per codebase and follow it.
- **Avoid escape hatches.** `any`, non-null `!`, `as` casts and enums are out. Use `unknown` plus narrowing, `satisfies`, and union literals or `as const` objects.
- **Make exports explicit.** Each feature has a public `index.ts`, and everything else stays internal.

## Clean architecture on the frontend

Use whichever style the ADR chose (see the shared ARCHITECTURE.md). In every style:

- **Domain and application logic are plain TS modules** with no framework imports. Examples are pricing rules, validation and state machines. Unit-test them directly.
- **API access goes through typed client or repository modules** (ports). Components never call `fetch` inline.
- **Keep server state and client state apart.** Server state lives in a cache library (TanStack Query, RTK Query, Apollo, Angular resource/HTTP plus signals, Nuxt `useFetch`). Client state stays local first. A global store (Zustand, Redux Toolkit, Pinia, NgRx/signals store) holds only truly shared client state.
- **Components only render and delegate events.** Logic moves into hooks, composables or services.

## React

- Use function components and hooks. Custom hooks (`useXxx`) hold reusable logic.
- Derive state instead of syncing it. Use `useEffect` only to sync with external systems, never for data you could compute.
- Use stable keys for lists, never the array index for dynamic lists.
- Memoize (`memo`, `useMemo`, `useCallback`) only after profiling, or for stable identities passed to memoized children. The React Compiler, when enabled, handles most of this.
- Use error boundaries per route or feature, and Suspense for loading.
- Build forms with React Hook Form plus a schema resolver.

## Next.js (App Router)

- Server Components are the default. Add `'use client'` only at the leaves that need interactivity, browser APIs or state.
- Fetch data on the server close to where it is used, and make caching and revalidation choices explicit for each fetch or route.
- Use Server Actions for mutations, with schema validation on the server. Never trust the client.
- Each route segment gets a `loading.tsx` and an `error.tsx`, plus `not-found.tsx` where it applies. Use the Metadata API for SEO.
- Secrets live only in server-only modules (`import 'server-only'`). Only `NEXT_PUBLIC_*` variables reach the client.
- Use `next/image` and `next/font`.

## Angular

- Use standalone components, signals for state, and `computed` and `effect` sparingly. Use `OnPush` change detection everywhere.
- Use the new control flow (`@if`, `@for` with `track`, `@defer` for lazy chunks).
- Inject dependencies with `inject()`. Services hold logic and HTTP. Components stay thin.
- Use typed reactive forms. Lazy-load each feature through routes.
- Each RxJS stream is subscribed once, using the `async` pipe or `takeUntilDestroyed`, and never as a nested subscribe.

## Vue (3, and Nuxt)

- Use the Composition API with `<script setup lang="ts">`, and `defineProps` and `defineEmits` with type-based declarations.
- Composables (`useXxx`) hold reusable logic. Use Pinia for shared state.
- Use `computed` for derived state and `watch` only for side effects. Don't mutate props.
- In Nuxt, use `useFetch` or `useAsyncData` for SSR-safe data, server routes for the backend-for-frontend, and keep runtime config secrets server-side.

## Astro

- Ship zero JS by default. Interactive islands use the lowest-cost `client:*` directive that works (`client:visible` or `client:idle` before `client:load`).
- Use content collections with Zod schemas for typed content.
- Islands can be React, Vue or another framework. Use one framework per product unless the ADR says otherwise.
- Share state between islands through nanostores, not through prop drilling across `.astro` files.

## Testing stack

- **Unit and component tests:** Vitest (or Jest) plus Testing Library. In Angular, use TestBed with Testing Library.
- **Network mocking:** MSW, at the network boundary.
- **E2E and visual tests:** Playwright for critical flows, and visual regression on design-system stories.
- **Accessibility:** run axe checks in component tests and in e2e tests.
