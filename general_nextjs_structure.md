# General Scalable Next.js Project Structure

## Overview
This structure follows a **Feature-Sliced Design (FSD)** inspired approach, adapted for Next.js App Router. It emphasizes **vertical slicing** (grouping by feature) and **Environment Separation** (strict Client vs Server boundaries).

## core Principles
1.  **Thin App, Thick Core**: The `app` directory is for routing only. The application shell (Providers, Layouts, Global Styles) lives in `src/core`.
2.  **Isomorphic Safety**: Shared utilities and services are explicitly split into `.client.ts` and `.server.ts` where necessary to prevent leaking server logic to the client or vice versa.
3.  **Mock-First Development**: Services use a Strategy Pattern to switch between Real and Mock implementations seamlessly.

## Directory Layout

### Root Level
- `src/`: Source code root.
- `public/`: Static assets.
- `next.config.ts`: Next.js config (Standlone output recommended).
- `middleware.ts`: Auth & I18n Middleware.

### Source Directory (`src/`)

#### 1. App Layer (`src/app/`)
**Role**: Routing & Entry Points.
- `layout.tsx`: Root layout (Imports `CoreShell` from `core`).
- `page.tsx`: Route entry point.
- `globals.css`: Global styles (imported here).

#### 2. Core Layer (`src/core/`)
**Role**: Application Shell & Infrastructure.
- `components/`: Shell components (e.g., `CoreShell`, `Sidebar`).
- `providers/`: Context composition (`ClientProvidersWrapper`).
- `hooks/`: Global logical hooks (e.g., `useNavigationNodes`).

#### 3. Features Layer (`src/features/`)
**Role**: Business Logic Slices.
- **`services/` (The Strategy Pattern)**:
    - `client/`: Client-side data fetching (e.g., `real.ts`, `mock.ts`, `index.ts`).
    - `server/`: Server-side actions (e.g., `real.ts`, `mock.ts`, `index.ts`).
    - **Note**: `index.ts` determines which implementation (`real` vs `mock`) to export based on `env.USE_MOCK`.
- `types/`: Domain Models (`IUser`, `EUserRole`).

#### 4. Shared Layer (`src/shared/`)
**Role**: Reusable Primitives (No Business Logic).
- **`services/`**:
    - `api.ts`: Isomorphic Axios wrapper (Error handling, Response normalization).
    - `client.ts`: Client-specific API instance (Token rotation, Session sync).
    - `server.ts`: Server-specific API instance (Auth headers, Caching).
- **`lib/`**:
    - `urls.ts`: Isomorphic URL helpers.
    - `urls.client.ts`: Client-only URL helpers (Env aware).
    - `env/`: Environment schema validation (`client.ts` / `server.ts`).
- **`types/`**: Global DTOs (`IResponse`, `IPagination`).

## Configuration & Patterns

### Naming Conventions (Strict)
- **Interfaces**: `I` prefix (e.g., `IUserProfile`).
- **Types**: `T` prefix (e.g., `TValidationResult`).
- **Enums**: `E` prefix (e.g., `ENotificationCategory`).
- **Components**: PascalCase (e.g., `SubmitButton.tsx`).
- **Client Files**: `filename.client.ts` (if colocated).

### Dependency Rule
**`app` -> `core` -> `widgets` -> `features` -> `shared`**

1.  **Core** defines the App Shell.
2.  **Widgets** compose Features.
3.  **Features** hold Business Logic.
4.  **Shared** holds Utilities.
