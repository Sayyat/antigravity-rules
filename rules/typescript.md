# TypeScript Standards

## Naming Conventions
*   **Types**: `T` prefix (e.g., `TUser`).
*   **Interfaces**: `I` prefix (e.g., `IProject`).
*   **Enums**: `E` prefix (e.g., `EStatus`).

## Project Structure
*   **Types Directory**: `src/features/<feature>/types/` or `src/shared/types/`.
*   **Interfaces**: Place close to implementation or in shared types if reusable.

## Best Practices
*   **Strict Typing**: Avoid `any`. Use `unknown` or specific types.
*   **Explicit Returns**: Define return types for functions, especially exported ones.
