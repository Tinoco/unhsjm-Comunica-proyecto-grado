# AGENTS.md

## Dev commands

```bash
npm start          # dev server at http://localhost:4200
npm run build      # production build (output: dist/)
npm test           # Vitest unit tests (via Angular CLI)
```

No lint/typecheck commands exist by default. Prettier is configured.

## Project structure

Single Angular 21 application. Lazy-loaded route segments (`src/app/app.routes.ts`):

- `/inicio` — public landing (Layout component with navbar, login/register modal)
- `/reportes` — citizen report form (Leaflet map, GPS, image preview; `AuthGuard`)
- `/operadores/*` — admin area (dashboard, incidencias, historial, mapa, problematicas, ubicaciones, usuarios, baneados, accesos); each child guarded by `PermissionGuard`
- `/admin` and `/superAdmin` (and subpaths) redirect to `/operadores/dashboard`
- `**` → redirects to `/inicio`

Entrypoint: `src/main.ts`. App config: `src/app/app.config.ts`.

## Auth & routing

- `enviarLogin()` routes after login by `res.data.user.operador`: operator → `/admin/dashboard` (redirects to `/operadores/dashboard`), otherwise → `/reportes`.
- Bearer token added via functional `HttpInterceptorFn` (`AuthInterceptor`)
- User + token stored in signals and `localStorage`
- Component style budget (production): 10kB warning / 12kB error per component

## State & UI patterns

- **Signals** everywhere — no RxJS `BehaviorSubject` for local state
- **`InteractionService`** centralizes: spinner (`showLoading/hideLoading`), toast (`showToast`), confirm dialog (`confirmar`), error modal (`mostrarError`)
- **`EstadoAdminService`** manages admin institution filter, persisted to `localStorage`
- All services use `providedIn: 'root'` — no manual providers needed

## Key conventions

- Strict TypeScript (`strict: true`, `noImplicitOverride: true`, `strictTemplates: true`)
- Prettier: 100 char line width, single quotes, Angular HTML parser for `.html` files
- Tests use Vitest (`*.spec.ts` files). `tsconfig.spec.json` includes `vitest/globals` types.
- API base URL: `http://localhost:3333` (dev, `src/environment/environment.ts`)
- Production environment file not present; add `environment.production.ts` before building for prod
- `npm run build` defaults to production config
- Backend responses use snake_case keys (`lista_Reportes`, `lista_Instituciones`, etc.)
- Backend endpoints follow: `/listar`, `/agregar`, `/actu/:id`, `/obtener/:id`
