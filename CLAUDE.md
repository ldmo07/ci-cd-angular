# CLAUDE.md

Repo independiente (`github.com/ldmo07/ci-cd-angular`), clonado dentro de `CI-CD/` pero ignorado por el repo raíz. Se commitea y pushea desde esta carpeta.

- App: Angular 22 (standalone, signals) en `src/app/`. Un componente lista personas desde `http://localhost:8083/personas` (API de `ci-cd-dotnet`, que tiene CORS habilitado). Sin router. Tailwind viene del `ng new` pero la UI usa CSS propio en `app.css`.
- Dockerfile multi-stage: `npm run build` (Node 24) y nginx sirve `dist/ci-cd-angular/browser`. Si cambia el nombre del proyecto en `angular.json`, actualizar esa ruta.
- `Jenkinsfile`: copia de `templates/Jenkinsfile.template` del repo raíz. Solo editar `APP_NAME` (`angular-example`), `HOST_PORT` (`8085`), `CONTAINER_PORT` (`80`).
- Push a `main` => Jenkins despliega en ~1–2 min y avisa a Discord. Un build roto conserva el contenedor anterior. `main` está protegida: rama → PR → check `test` en verde → merge.
- Tests con Vitest (`npm test -- --no-watch`), lint con `angular-eslint` (`npm run lint`). Ambos corren en `.github/workflows/test.yml` (solo en PRs; nunca build/deploy ahí).
- `.gitattributes` fuerza LF; CRLF en el `Jenkinsfile` rompe el pipeline.
- `.mcp.json` lo generó `ng new` (servidor MCP del Angular CLI para asistentes de IA); es opcional.

---

## Guía de Angular (generada por `ng new`)
You are an expert in TypeScript, Angular, and scalable web application development. You write functional, maintainable, performant, and accessible code following Angular and TypeScript best practices.

## TypeScript Best Practices

- Use strict type checking
- Prefer type inference when the type is obvious
- Avoid the `any` type; use `unknown` when type is uncertain

## Angular Best Practices

- Always use standalone components over NgModules
- Must NOT set `standalone: true` inside Angular decorators. It's the default in Angular v20+.
- Do NOT set `changeDetection: ChangeDetectionStrategy.OnPush` explicitly. `OnPush` is the default in Angular v22+.
- Use signals for state management
- Implement lazy loading for feature routes
- Do NOT use the `@HostBinding` and `@HostListener` decorators. Put host bindings inside the `host` object of the `@Component` or `@Directive` decorator instead
- Use `NgOptimizedImage` for all static images.
  - `NgOptimizedImage` does not work for inline base64 images.

## Accessibility Requirements

- It MUST pass all AXE checks.
- It MUST follow all WCAG AA minimums, including focus management, color contrast, and ARIA attributes.

### Components

- Keep components small and focused on a single responsibility
- Use `input()` and `output()` functions instead of decorators
- Use `model()` for two-way bound properties with `[(prop)]` syntax instead of pairing `input()` with `output()`
- Use `computed()` for derived state
- Use `linkedSignal()` for state derived from multiple reactive sources that must stay synchronized
- Prefer inline templates for small components
- Prefer Signal Forms (`@angular/forms/signals`) for new forms. They are stable in Angular v22+ and provide signal-based state, type-safe field access, and schema-based validation
- When not using Signal Forms, prefer Reactive forms instead of Template-driven ones
- Do NOT use `ngClass`, use `class` bindings instead
- Do NOT use `ngStyle`, use `style` bindings instead
- When using external templates/styles, use paths relative to the component TS file.

## State Management

- Use signals for local component state
- Use `computed()` for derived state
- Keep state transformations pure and predictable
- Do NOT use `mutate` on signals, use `update` or `set` instead

## Templates

- Keep templates simple and avoid complex logic
- Use native control flow (`@if`, `@for`, `@switch`) instead of `*ngIf`, `*ngFor`, `*ngSwitch`
- Use the async pipe to handle observables
- Do not assume globals like (`new Date()`) are available.

## Services

- Design services around a single responsibility
- Use the `providedIn: 'root'` option for singleton services
- Prefer the `@Service` decorator over `@Injectable({providedIn: 'root'})` for new singleton services (Angular v22+)
- Use the `inject()` function instead of constructor injection
