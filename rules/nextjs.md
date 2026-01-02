# Generic Next.js Project Structure

## Overview
Feature-Sliced Design (FSD) adapted for Next.js App Router.

## Development Environment
*   **Node Manager**: `nvm` (Node Version Manager).
    *   Ensure `.nvmrc` is present in the project root specifying the Node version.
    *   Run `nvm use` to sync the environment.

## Directory Layout & Responsibilities

1.  **`src/app/`**: Application Entry & Routing
    *   **Purpose**: Manages routing, global providers, `globals.css`, layouts, and metadata.
    *   **Rule**: Calls respective `*Screen.tsx` components from `screens/`.
    *   **Content**: `page.tsx`, `layout.tsx`, `loading.tsx`, `error.tsx`.

2.  **`src/screens/`**: Page Logic & Layout
    *   **Purpose**: Defines the layout and logic for a single screen/page.
    *   **Structure**: Composes `widgets/` and `features/`.
    *   **Naming**: Files and components must have the `Screen` suffix (e.g., `ProjectDetailScreen.tsx`).

3.  **`src/widgets/`**: Composition Layer
    *   **Purpose**: Composes meaningful, complex UI blocks from `features/`.
    *   **Naming**: Files and components must have the `Widget` suffix (e.g., `ProjectListWidget.tsx`).

4.  **`src/features/`**: Business Logic & Dumb Components
    *   **Purpose**: Holds domain-specific "dumb" components and logic.
    *   **Subfolders**:
        *   `components/`: Real components and their skeletons. Upper layers compose them.
        *   `hooks/`, `lib/`, `providers/`, `services/`, `types/`.
    *   **Rule**: Keep components pure and presentational where possible.

5.  **`src/shared/`**: Reusable Utilities
    *   **Purpose**: Project-agnostic, reusable code.
    *   **Structure**: Mirrors `features/` structure but for shared code.
    *   **Exports**:
        *   `index.ts`: Env-agnostic code.
        *   `client.ts`: Client-side specific exports.
        *   `server.ts`: Server-side specific exports.

## Naming Conventions
*   **Types**: `T` prefix (e.g., `TUser`).
*   **Interfaces**: `I` prefix (e.g., `IProject`).
*   **Enums**: `E` prefix (e.g., `EStatus`).

## Localization
*   See [Smart I18n Standards](i18n.md).
