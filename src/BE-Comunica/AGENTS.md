# AGENTS.md

## Framework & Stack
- **AdonisJS v7** (Node.js full-stack framework)
- **TypeScript** with ES modules
- **PostgreSQL** via AdonisJS Lucid ORM
- **Auth**: Built-in `@adonisjs/auth` with access tokens

## Developer Commands
```bash
npm run dev        # Dev server with HMR
npm run start      # Production server
npm run build      # Build for production
npm run test       # Run Japa tests
npm run lint       # ESLint
npm run format     # Prettier
npm run typecheck  # TypeScript check
```

## Architecture
- **Entrypoint**: `bin/server.ts`
- **Routes**: `start/routes.ts`
- **Middleware**: `start/kernel.ts` - registers named middleware `auth`, `permisosOperador`, `administrarPermisos`
- **Controllers**: `app/controllers/*.ts`
- **Models**: `app/models/*.ts`
- **Validators**: `app/validators/*.ts`
- **Database**: `config/database.ts` - PostgreSQL connection

## Auth & Permissions
- Named middleware: `auth` (requires login), `permisosOperador(['codigo.ver'])` (operator must hold **every** listed permission code), `administrarPermisos()` (super-admin only).
- Permission codes are strings such as `usuarios.ver`, `incidencias.crear`, `banned_users.actualizar`.
- Middleware is applied per route group. Open routes: `/`, `/auth/login`, and the `/public/*` group.

## Environment
Requires `.env` (copy `.env.example`, then run `node ace generate:key`). Vars: `DB_CONNECTION`, `DB_HOST/PORT/USER/PASSWORD/DATABASE`, `APP_KEY`, `APP_URL`, `SESSION_DRIVER`, `CLOUDINARY_CLOUD_NAME/API_KEY/API_SECRET`. Default `DB_PORT=5431` in the example.

## API Responses
- Custom serializer (`providers/api_provider.ts`) wraps serialized payloads in a `data` key and uses `snake_case`. Controllers use `serialize(...)` (injected from `HttpContext`) for the wrapped shape.

## Run Order
1. `npm run lint`
2. `npm run typecheck`
3. `npm run test`