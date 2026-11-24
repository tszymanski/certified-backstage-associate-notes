# Domain 2 — Backstage Infrastructure (22%)

Framework fundamentals, configuration, production deployment, and the
client-server architecture.

## Official curriculum objectives

- [ ] Understand the Backstage framework
- [ ] Configure Backstage
- [ ] Deploy Backstage to production
- [ ] Understand Backstage client-server architecture

## The three layers

| Layer | What it is |
|-------|-----------|
| **Core** | Base functionality built by the core open-source project |
| **App** | A deployed, customized Backstage instance (your portal) |
| **Plugins** | Add features on top of core (catalog, scaffolder, TechDocs, …) |

Backstage is a **single-page application (SPA)** that composes many plugins.

## Client–server architecture

- **Frontend** = React SPA (`packages/app`) running in the browser.
- **Backend** = Node.js service (`packages/backend`) exposing plugin APIs.
- They communicate over **HTTP API requests** (frontend fetches data from backend).
- A built-in **proxy** service forwards browser requests to third-party APIs to
  avoid **CORS** problems (e.g. CircleCI, Jenkins).

### Frontend building blocks

- **App instance** — root app that wires everything together (no features itself).
- **Extensions** — building blocks forming a tree; provided by app + plugins.
- **Frontend plugins** — deliver features; each mounts at a route (e.g. `/catalog`).
- **Utility APIs (Utility/Util APIs)** — TypeScript interfaces for plugin-to-plugin
  communication and integrator customization.
- **Routes** — let plugins link to each other without hardcoding URLs.

### Backend building blocks

- **Backend instance** — deployment unit wiring backend plugins together.
- **Backend plugins** — run like independent services; communicate **only over the
  network**, not by direct code calls.
- **Services** — shared utilities (logging, database, config, cache, auth, etc.).
- **Extension points** — plugin-specific hooks (e.g. catalog **Entity Providers**,
  scaffolder **Actions**) that modules extend.
- **Modules** — add features to a plugin via extension points; deployed in the same
  backend instance as their target plugin.

## Configuration

- Primary config file: **`app-config.yaml`**.
- Environment/stage overrides: `app-config.local.yaml`, `app-config.production.yaml`
  (later files override earlier ones).
- Reference **environment variables** with `${VAR}` — good for secrets/tokens.
- Config is validated against schemas provided by plugins.
- Common sections: `app`, `backend`, `organization`, `catalog`, `auth`, `integrations`,
  `techdocs`, `proxy`.

```yaml
app:
  title: My Company Catalog
  baseUrl: http://localhost:3000
backend:
  baseUrl: http://localhost:7007
  database:
    client: pg
    connection:
      host: ${POSTGRES_HOST}
      user: ${POSTGRES_USER}
      password: ${POSTGRES_PASSWORD}
integrations:
  github:
    - host: github.com
      token: ${GITHUB_TOKEN}
```

## Databases

- Built on the **Knex** query builder; **one logical database per plugin**
  (independent migrations).
- **SQLite** → development/testing (in-memory or file, ephemeral).
- **PostgreSQL** → **recommended for production**.
- Scale by running multiple backend instances against the same database.

## Caching

- Pluggable cache stores (via **Keyv**): in-memory (dev), **Memcache**, **Redis**,
  Valkey, Infinispan. Each plugin gets a logically separated cache.

## Production deployment

- **Containerize** frontend, backend, and database.
- Frontend is a **static bundle** (often served by the backend or a CDN/nginx).
- Backend runs as a Node service; can be **split across multiple containers** for
  scaling/feature isolation.
- Use **PostgreSQL**, real secrets via env vars, and set correct `baseUrl`s.
- Commonly deployed to **Kubernetes** (Helm charts / manifests exist).

## Key takeaways

- Core + App + Plugins; SPA frontend + Node backend over HTTP.
- Backend plugins are network-isolated micro-units; modules extend them via
  extension points.
- `app-config.yaml` with `${ENV}` substitution; layered config files.
- Knex, DB-per-plugin, SQLite (dev) vs PostgreSQL (prod).
- Proxy solves CORS for third-party integrations.
