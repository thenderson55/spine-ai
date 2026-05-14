# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Monorepo Structure

pnpm + Turborepo monorepo. All tasks run through `turbo`; pnpm is the package manager.

**Apps:**
- `apps/mobile` — React Native / Expo app ("SpinAI"), expo-router file-based routing, React 19, RN 0.81, New Architecture enabled
- `apps/web` — Next.js 16 app, Tailwind CSS v4, App Router (`src/app/`)

**Packages:**
- `packages/ui` — shared React component library
- `packages/eslint-config` — shared ESLint configs (`base`, `next`, `react-internal`)
- `packages/typescript-config` — shared tsconfig presets (`base`, `nextjs`, `react-library`)

## Commands

From repo root:
```sh
pnpm dev              # start all apps in dev mode
pnpm build            # build all apps and packages
pnpm lint             # lint all packages
pnpm check-types      # typecheck all packages
pnpm format           # prettier format all .ts/.tsx/.md files
```

Filtering to a single app:
```sh
pnpm exec turbo dev --filter=mobile
pnpm exec turbo dev --filter=web
pnpm exec turbo build --filter=web
```

Inside `apps/mobile` directly:
```sh
pnpm start            # expo start
pnpm ios              # expo start --android
pnpm android          # expo start --android
```

## Mobile App Architecture (`apps/mobile`)

Uses **expo-router** for file-based routing. Route files live in `app/`:
- `app/_layout.tsx` — root layout: loads fonts (SpaceMono + FontAwesome), handles splash screen, sets up React Navigation `Stack`
- `app/(tabs)/` — tab group; `_layout.tsx` defines the `Tabs` navigator, each file is a tab screen
- `app/modal.tsx` — modal route presented over tabs
- `app/+html.tsx` — web-only HTML shell
- `app/+not-found.tsx` — 404 screen

Path alias `@/*` maps to the root of `apps/mobile` (configured in `tsconfig.json`).

**Themed components** (`components/Themed.tsx`): wraps RN `Text` and `View` to automatically apply light/dark colors from `constants/Colors.ts` via `useColorScheme`. Always use these instead of bare RN primitives when color-scheme awareness is needed.

**Platform-specific files**: `.web.ts` suffix overrides `.ts` for web targets (e.g. `useColorScheme.web.ts`, `useClientOnlyValue.web.ts`).

## Web App Architecture (`apps/web`)

Next.js 16 with App Router. **This version has breaking changes** from older Next.js — before writing any Next.js code, check `node_modules/next/dist/docs/` for current API conventions. Do not rely on training-data knowledge of Next.js APIs.

Styling: Tailwind CSS v4 via `@tailwindcss/postcss` (not the v3 plugin — config syntax differs).
