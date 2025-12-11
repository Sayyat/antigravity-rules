# Generic CI/CD Pipeline (GitHub Actions)

This workflow automates building, testing, and deploying generic Django projects to a self-hosted server.

## 1. Workflow Architecture (`.github/workflows/dev.yml`)

*   **Trigger**: `push` to `dev` branch.
*   **Runner**: `self-hosted` (Execute build and deploy on the target server to avoid registry transfers).
*   **Strategy**: Cancel-in-progress enabled.

## 2. Pipeline Jobs

### 2.1. `notify_start`
*   **Runner**: `ubuntu-latest`
*   **Task**: Send Telegram notification that deployment started (Commit info, Author).

### 2.2. `build`
*   **Runner**: `self-hosted`
*   **Task**: Build Docker images.
    *   Checkout code.
    *   `docker compose -f docker-compose.yml -f docker-compose.dev.yml build`
    *   Use Docker BuildKit for caching.

### 2.3. `deploy`
*   **Runner**: `self-hosted`
*   **Depends On**: `build`
*   **Steps**:
    1.  **Env Setup**: Create `.env` file from GitHub Secrets.
    2.  **Network**: Ensure docker network exists.
    3.  **Restart**: 
        *   `docker compose down --remove-orphans`
        *   `docker compose up -d`
    4.  **Post-Deployment ops** (Crucial: Run AFTER container start to ensure DB availability):
        *   `migrate`: `docker compose exec -T <backend_service_name> python manage.py migrate --no-input`
        *   `collectstatic`: `docker compose exec -T <backend_service_name> python manage.py collectstatic --no-input`
    5.  **Cleanup**: `docker system prune` (Optional).

### 2.4. `notify_result`
*   **Runner**: `ubuntu-latest`
*   **Condition**: Always run (Success or Failure).
*   **Task**: Send Telegram notification with status and total duration.

## 3. Best Practices

*   **Self-Hosted Runners**: For single-server deployments, building on the runner avoids the need for a private Docker registry and speeds up deployment.
*   **Dynamic Config**: Never commit `.env`. Inject secrets via CI/CD.
*   **Safety**: Run `migrate` and `collectstatic` inside the container after it is healthy/running, not as a build step.
