---
name: evc-office-frontend
description: Skill for Én Việt Office frontend development in React, TypeScript, and Vite.
---

# EVC Office Frontend Skill

Use this skill when working in `evc_ev_office_fe`, especially for:

- React + TypeScript + Vite frontend changes
- Pages, routes, layouts, providers, permissions, and sidebar configuration
- Feature modules under `src/features`
- Shared components, hooks, stores, services, schemas, types, helpers, utils, and config
- API integration with the backend through Axios and TanStack React Query
- Ant Design tables, forms, modals, messages, filters, and data-heavy office workflows
- SignalR, authentication, token refresh, file upload/download, and customer/HRM/workplace/accountant modules

Read `HOUSE_STYLE.md` before implementing or refactoring app, feature, API, hook, or shared UI code.

## Project Shape

- Framework: React 18, TypeScript, Vite 6.
- UI: Ant Design 5, `antd-style`, lucide icons, selected domain libraries such as AG Grid, FullCalendar, XYFlow, TinyMCE, and charts.
- Data: Axios wrapper in `src/services/httpRequest.ts`, TanStack React Query through shared custom hooks, Zustand stores for app state.
- Routing: React Router under `src/app/router.tsx`, `src/app/routes`, and pages under `src/app/pages`.
- Imports: `@` maps to `src`.
- Styling: global styles in `src/components/GlobalStyles`, Ant Design theme in `src/app/provider.tsx`, local CSS or existing component styling where nearby code uses it.

## Architecture Rules

- `src/app` may import from features and shared code.
- `src/features/<FeatureName>` may import shared code and files inside the same feature.
- Shared folders may import only other shared folders.
- Keep endpoint definitions out of components. Put them in `api`, `apis`, or `src/services/apis` modules according to the nearby feature.
- Keep React Query orchestration in hooks. Use existing `useCustomQuery`, `useCustomMutation`, `queryKeys`, and invalidation patterns.
- Keep reusable DTOs and model shapes in `src/types` or local feature `types` when the type is feature-owned.
- Put form/table column builders in local hooks when that is the surrounding pattern.

## Frontend Discipline

- Prefer small, typed components and hooks over large multipurpose files.
- Do not fetch data directly from render bodies or event handlers when an existing query/mutation hook is the right layer.
- Preserve permission checks and route/sidebar config behavior. Backend permission keys are surfaced in `src/config/layoutConfigs`.
- For tables, keep pagination/filter state compatible with `TableParams`, `defaultTableParams`, and `tableParamsToPaginationParams` when nearby tables use them.
- For forms, reuse existing Ant Design form conventions, validation schemas, options config, and modal/message stores.
- For auth and API errors, preserve `HttpRequest` token refresh, normalized response envelope, and error filtering behavior.
- For file/blob work, follow existing `getFile`, upload APIs, and response type conventions.
- For money, dates, statuses, or permissions displayed from the backend, do not invent semantics. Trace existing labels/options/mapping before editing.

## Working Pattern

1. Read README and nearby implementation first.
2. Locate the owner layer: app route/page, feature module, shared hook/component/service/type/config.
3. Reuse existing components, hooks, query keys, API wrappers, option maps, and table helpers.
4. Implement the smallest scoped change.
5. Run focused verification: lint, build, test, or targeted tests.
6. Summarize assumptions and verification.

## Verification Commands

Use the smallest useful scope:

```shell
npm run lint
npm run build
npm run test
```

Before commits, follow README cleanup:

```shell
npm run format:fix
npm run lint:fix
```
