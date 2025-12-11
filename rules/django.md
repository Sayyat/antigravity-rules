# Generic Django Project Structure

## Overview
This document outlines a modular **Django** project structure designed for scalability and maintainability. It emphasizes a strict separation of concerns using a custom `apps/` directory, a fractal API architecture using **Django Ninja**, and a centralized configuration setup.

## 1. 🏗️ Architectural Philosophy (Modular Monolith)

The project is built on the **Modular Monolith** principle. The codebase resides in a single repository but is logically divided into independent **apps**, each responsible for a specific business domain.

### 1.1. Directory Structure

```text
project_root/
├── apps/                 # Container for all modular Django apps
│   ├── users/            # Example domain app
│   ├── common/           # Shared code (utils, middleware, base classes)
│   ├── codes/            # Standardized API response handling
│   └── ... 
├── config/               # Django configuration
│   ├── settings/         # Split settings (base, dev, prod)
│   ├── urls.py           # Root URL router
│   └── api_urls.py       # Central API v1 router
├── docs/                 # Project documentation
├── media/                # User uploads (gitignored)
├── static/               # Collected static files (gitignored)
├── templates/            # Global HTML templates
├── .env                  # Environment variables
├── manage.py             # Django utility
└── docker-compose.yml    # Docker configuration
```

### 1.2. Key Principles
*   **App Isolation**: Each app in `apps/` should be maximally independent.
*   **Explicit Dependencies**: Imports between apps must be explicit (e.g., `from apps.users.services import UserService`).
*   **Clean Root**: The root directory contains only config files and top-level folders.

---

## 2. 🧩 API Architecture (`django-ninja-extra`)

The API is built using **`django-ninja-extra`**, focusing on cleanliness, type safety (Type Hinting), automatic documentation (OpenAPI), and Dependency Injection.

### 2.1. App-Level API Structure

Each app providing an API follows this strict structure:

```text
apps/domain_name/
├── api/
│   └── v1/                 # API Version
│       ├── controllers.py  # API Controllers (@api_controller)
│       ├── schemas.py      # Pydantic Schemas (Input/Output)
│       └── urls.py         # NinjaExtraAPI instance & controller registration
├── services.py             # Business Logic (Service Layer)
├── module.py               # Dependency Injection Configuration
└── ... 
```

### 2.2. Controllers (`controllers.py`)
*   Use `@api_controller` decorator.
*   **Thin Layer**: Responsible ONLY for HTTP handling (parsing, validation, calling services, responding).
*   **No Business Logic**: No direct ORM calls (`Model.objects...`) in controllers.
*   **Dependency Injection**: Receive services via `__init__` based on type hints.

### 2.3. Schemas (`schemas.py`)
*   Use **Pydantic** (`ninja.Schema`).
*   Define `Input` and `Output` structures.
*   Enforce automatic validation and serialization.

### 2.4. Dependency Injection (`module.py`)
*   Define a class inheriting from `injector.Module`.
*   Register services using `binder.bind(ServiceClass, scope=singleton)`.
*   Register modules globally in `settings.py` (`NINJA_EXTRA['INJECTOR_MODULES']`).

### 2.5. Routing Strategy (3-Level)
1.  **App Level** (`apps/x/api/v1/urls.py`): Creates `NinjaExtraAPI`, registers controllers, exports the API instance.
2.  **Central API Router** (`config/api_urls.py`): Imports app API instances and mounts them to paths (e.g., `path("users/", users_api.urls)`).
3.  **Root Router** (`config/urls.py`): Includes the central API router under a version prefix (`/api/v1/`).

---

## 3. 🛡️ Authentication & Authorization

### 3.1. Dual Authentication Strategy
For projects utilizing Telegram Mini Apps (TMA) alongside admin panels:
*   **`TMAAuth`**: Validates Telegram `init_data` for real users.
*   **`StaffSwaggerAuth`**: Allows `is_staff` users (logged into Django Admin) to access API docs and endpoints without TMA tokens.
*   **`api_with_tma` Wrapper**: Applies both auth methods to an API instance.

---

## 4. 💡 Unified System Codes (`apps/codes`)

To ensure **consistency**, all API responses follow a hybrid strategy using a central implementation in `apps/codes`.

### 4.1. The `CodeOut` Standard
A standard envelope for errors and empty success responses:
```python
class CodeOut(Schema):
    code: str
    message: str
    detail: Union[List[ValidationErrorDetail], str, Dict[str, Any], None] = None
```

### 4.2. Response Strategy
1.  **Success with Data (GET)** -> Return **Pydantic Schema** directly.
2.  **Success without Data (POST/PUT/DELETE)** -> Return **`CodeOut`** (e.g., `Successes2xx.OK`).
3.  **Errors (4xx/5xx)** -> Return **`CodeOut`** (e.g., `Errors4xx.NOT_FOUND`).

### 4.3. Middleware
*   **`Http422Middleware`**: Converts validation errors to `CodeOut`.
*   **`Http404Middleware`**: Converts standard 404s to `CodeOut`.
