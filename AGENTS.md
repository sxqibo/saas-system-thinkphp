# AGENTS.md

## Cursor Cloud specific instructions

### Architecture Overview
This is a multi-tenant SaaS admin platform with a PHP (ThinkPHP 8) backend and a Vue 3 frontend (separate repo).
- **Backend**: ThinkPHP 8 — PHP multi-app framework with 3 apps: `admin` (platform), `tenant`, `api`
- **Frontend**: Vue 3 + Element Plus + Vite — lives in `web/` directory (cloned from `https://github.com/saas-system/saas-system-vue3.git`)
- **Database**: MySQL 8, **Cache/Queue**: Redis

### Services & Ports
| Service | Command | Port | Working Directory |
|---------|---------|------|-------------------|
| Backend | `php think run --host=0.0.0.0 --port=8000` | 8000 | `/workspace` |
| Frontend | `pnpm dev` | 1818 | `/workspace/web` |

### Starting Services (must be done each session)
1. **Start MySQL**: `sudo bash -c 'mysqld --user=mysql &'` — wait ~2s, verify with `mysqladmin ping`
2. **Start Redis**: `sudo redis-server --daemonize yes --requirepass 123456`
3. **Start Backend**: `cd /workspace && php think run --host=0.0.0.0 --port=8000 &`
4. **Start Frontend**: `cd /workspace/web && pnpm dev &`

### Credentials
- **MySQL**: root / 123456 (host: 127.0.0.1, database: `saas-system`)
- **Redis**: password 123456 (port 6379)
- **Platform Admin**: http://localhost:1818/#/platform — username: `admin`, password: `123123`

### Gotchas
- The `web/` directory is gitignored; it must be cloned separately from `https://github.com/saas-system/saas-system-vue3.git`.
- MySQL starts via `mysqld --user=mysql` directly (systemd/service commands don't work in this container).
- The `.env` file must exist (copy from `.env-example` if missing).
- `eslint-plugin-prettier` is required by the frontend ESLint config but not listed in `package.json` upstream; install it with `pnpm add -D eslint-plugin-prettier` in `web/`.
- The `tests/WalletTest.php` depends on an external package (`Sxqibo\FastWallet`) not included in `composer.json`; it cannot be run without that dependency.
- TypeScript typecheck (`pnpm typecheck`) has ~27 pre-existing errors in the codebase.
- ESLint (`pnpm lint`) has ~500 pre-existing prettier formatting warnings.

### Lint/Test/Build (see README.md for standard commands)
- **Frontend lint**: `cd web && pnpm lint`
- **Frontend typecheck**: `cd web && pnpm typecheck`
- **Frontend build**: `cd web && pnpm build`
- **Backend run**: `php think run`
- **Migrations**: `php think migrate:run`
- **Seeds**: `php think seed:run`
