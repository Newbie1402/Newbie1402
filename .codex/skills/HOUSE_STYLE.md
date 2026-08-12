# EVC Frontend House Style

This file captures the implementation style already present in this React + TypeScript + Vite repo so future code matches it.

Source patterns are visible in files such as:

- `src/app/provider.tsx`
- `src/app/router.tsx`
- `src/app/routes/*`
- `src/services/httpRequest.ts`
- `src/features/DispatchWorkflowTemplate/*`
- `src/features/Auth/*`
- `src/hooks/*`
- `src/components/ui/*`
- `src/config/layoutConfigs/*`

## Repo Layout

### App layer

- `src/main.tsx` mounts `App` inside `GlobalStyles`.
- `src/app/index.tsx` composes `AppProvider` and `AppRouter`.
- `src/app/provider.tsx` owns global providers: Ant Design theme/locale, message and modal APIs, global loading, SignalR, React Flow, React Query, and designer context.
- `src/app/routes` owns route constants and route config.
- `src/app/pages` owns top-level pages that wire features into routes.

### Feature layer

Feature folders live under `src/features/<FeatureName>` and commonly contain:

- `api` or `apis` for endpoint wrappers
- `components` or `ui` for feature UI
- `hooks` for table columns, detail actions, mutations, queries, and page behavior
- `config` for tab items, options, labels, modal config, and mappings
- `types`, `schemas`, `utils`, `helpers`, `lib` when feature-owned
- `index.ts` for exports when the feature already uses one

### Shared layer

Shared code lives in folders such as:

- `src/components`
- `src/hooks`
- `src/services`
- `src/types`
- `src/utils`
- `src/helpers`
- `src/config`
- `src/lib`
- `src/stores`
- `src/schemas`
- `src/assets`

Shared code should stay domain-neutral or intentionally cross-feature.

## Import Boundaries

The ESLint boundaries config is part of the architecture:

- Shared can import shared.
- Feature can import shared and files from the same feature.
- App can import feature and shared.
- `src/main.tsx` may import app, feature, shared, and environment files.

Use `@/` for `src` imports. Avoid deep relative paths across folders when the alias is clearer. Do not bypass boundaries by moving feature-specific logic into shared folders unless it is genuinely reused.

## API Style

API wrappers should use the shared Axios wrapper:

```ts
import { HttpRequest } from '@/services/httpRequest'

const httpRequest = HttpRequest.getInstance()

export const exampleApi = {
  getById: (id: string) => {
    return httpRequest.get<Example>(`/Example/${id}`)
  },

  create: (data: Partial<Example>) => {
    return httpRequest.post<Example>('/Example', data)
  },
}
```

Preserve these patterns:

- Keep URLs and HTTP verbs in API modules.
- Keep generic response types explicit where useful.
- Let `HttpRequest` unwrap `response.data.result`.
- Do not duplicate token refresh, base URL, or error normalization logic.
- For blob/file endpoints, use existing response type and `getFile` conventions.

## Query And Mutation Style

Use existing custom hooks and query keys:

```ts
export function useExample(id?: string) {
  return useCustomQuery({
    queryKey: [...queryKeys.module.example.detail.gen(id!)],
    queryFn: () => exampleApi.getById(id!),
    enabled: !!id,
  })
}
```

For mutations:

- Use `useCustomMutation` where nearby code does.
- Set success messages in Vietnamese when the surrounding module does.
- Invalidate list and detail query keys with the same `exact` behavior as nearby hooks.
- Keep hook names descriptive: `useCreateX`, `useUpdateX`, `useDeleteX`, `useActivateX`, `useXDetailActions`, `useXTableColumns`.

## Component Style

### General

- Prefer function declarations for exported components when nearby code does.
- Keep props typed with `interface` or local types.
- Keep component files focused on rendering, state, and interaction.
- Move column definitions, action arrays, detail item builders, and larger behavior into local hooks when they grow.
- Reuse shared UI such as `TableWithTabsFilter`, modal stores, message stores, and existing form controls before creating new components.

### Tables

For data tables, match existing conventions:

- `TableParams` state initialized from `defaultTableParams`.
- Convert table params with `tableParamsToPaginationParams`.
- Update `pagination.total` from `data.totalCount`.
- Set stable row keys from backend IDs.
- Keep filter/tab config in `config` files.
- Keep column definitions in `hooks/useXTableColumns.tsx`.

### Forms And Modals

- Use Ant Design form patterns already present in the feature.
- Keep option lists, enum labels, and mappings in `config` when reused.
- Use existing modal/message stores for global feedback where the project already does.
- Do not hardcode permission-sensitive behavior in UI without tracing the existing permission model.

## Route And Permission Style

- Add route paths to the relevant `src/app/routes/*` module.
- Add page wiring under `src/app/pages` or the nearest route config pattern.
- Sidebar permission keys live in `src/config/layoutConfigs/index.ts` through `TSidebarItem.permissionKey`.
- A sidebar item is visible when the current user has the `view` permission for that backend-defined key.
- Preserve module route grouping: HRM, CRM, CMS, CS, Workplace, Accountant, Advance, System, Auth.

## TypeScript And Formatting

- TypeScript is strict/stylistic through ESLint.
- Prettier uses 2 spaces, single quotes, no semicolons, trailing commas, and print width 100.
- Avoid `any`; if nearby code uses it because an API is loose, keep the escape narrow and typed at the boundary as soon as practical.
- Do not add unused imports, unsorted imports, or broad eslint disables.
- `no-param-reassign` is enforced except known draft/state patterns.

## Vite And Assets

- Vite uses `@vitejs/plugin-react` and `vite-plugin-svgr`.
- SVGs may be imported as React components when the existing code does.
- Build chunking is intentionally tuned for charts, AG Grid, FullCalendar, TinyMCE, XYFlow, xlsx, React, Ant Design, and icons. Do not rewrite chunk strategy unless the task is specifically about build performance.

## Practical Rules

- Read neighboring files before creating new structure.
- Keep changes surgical and feature-owned.
- Use existing Vietnamese UI copy tone and terminology.
- Preserve backend response envelope assumptions.
- For risky workflow changes, check manual and table/detail flows for parity.
- Verify with the smallest useful command and report what ran.
