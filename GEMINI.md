# MEMORY INDEX

This file acts as a router for project-specific rules. 

## 📂 Rule Library
All detailed standards are located in C:\Users\sayat\.gemini\rules\.

### 1. [Next.js Standards](rules/nextjs.md)
*For all React/Next.js projects.*
*   **Architecture**: FSD, Thin App / Thick Core.
*   **Key Patterns**: Client/Server Service Splitting, Strategy Pattern (Mock/Real).
*   **Localization**: smart-i18next-cli workflow.

### 2. [Django Standards](rules/django.md)
*For all Python/Django projects.*
*   **Architecture**: Domain-driven pps/ directory.
*   **API**: Django Ninja (Fractal structure).
*   **Key Patterns**: Unified System Codes (pps/codes).

### 3. [Smart I18n Standards](rules/i18n.md)
*For projects using smart-i18next-cli.*
*   **Workflow**: 1-to-1 Namespacing, Gettext Keys.
*   **Features**: Auto-translation, Static Types.

### 4. [TypeScript Standards](rules/typescript.md)
*For all TypeScript projects.*
*   **Key Patterns**: T/I/E Naming, Explicit Returns.
*   **Structure**: Type organization.

## ⚠️ Global Instructions
*   **Always check the relevant rule file** at the start of a session based on the project type.
*   **Update the specific rule file** when new patterns are established, rather than this index.
