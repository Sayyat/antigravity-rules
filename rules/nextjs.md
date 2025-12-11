# Generic Next.js Project Structure

## Overview
Feature-Sliced Design (FSD) adapted for Next.js App Router (Thin App, Thick Core).

## Core Principles
1.  **Thin App, Thick Core**: `src/app` for routing only. `src/core` for Providers/Layouts.
2.  **Screens Layer**: Intermediate layer (`src/screens`) for assembling widgets/features into page views, allowing dynamic imports in Core.
3.  **Isomorphic Safety**: Split utils into `.ts` (Shared/Iso) and `.client.ts` (Client-only).
4.  **Strategy Pattern**: Use `services/{client|server}/index.ts` to switch Real vs Mock based on Env.

## Directory Layout
- `src/app`: Routes (`page.tsx`, `layout.tsx`).
- `src/core`: App Shell (`CoreShell`, `Providers`, `useNavigationNodes`).
- `src/screens`: Page assemblies (Lazy loaded by `core/hooks`).
- `src/widgets`: Composition (`BottomNavBar`, `ScheduleWidget`).
- `src/features`: Business Logic (`Authentication`, `Payment`).
- `src/shared`: Reusable (`api.ts`, `urls.client.ts`, `cn`).

## Architectural Flow
**App** (Route) -> **Core** (Shell/Nav) -> **Screens** (Lazy Assembly) -> **Widgets** (UI Blocks) -> **Features** (Logic) -> **Shared** (Utils)

## Naming Conventions
- **Interfaces**: `I` prefix.
- **Types**: `T` prefix.
- **Enums**: `E` prefix.
- **Client Files**: `*.client.ts`.

## Localization
See [Smart I18n Standards](i18n.md) for full details on `smart-i18next-cli`.

