# CBA Cheat Sheet — Last-Minute Review

## Facts
- Backstage: created by **Spotify**, donated to **CNCF** (**incubation** level).
- It's a **framework** for building **developer portals**, built around a **Software Catalog**.
- Written in **TypeScript**; frontend = **React SPA**; backend = **Node.js**.

## Commands
| Command | Does |
|---------|------|
| `npx @backstage/create-app@latest` | Scaffold a new app |
| `yarn install` | Install deps |
| `yarn start` / `yarn dev` | Run frontend + backend |
| `yarn new` | Scaffold a plugin/package |
| `yarn tsc` | Type-check |
| `yarn build:backend` | Build backend bundle for Docker |

## Ports & runtime
- Frontend **:3000** · Backend **:7007**
- **Node 22 / 24** · Yarn workspaces (monorepo)

## Layout
```
app-config.yaml   catalog-info.yaml   package.json
packages/app  (frontend)   packages/backend (backend)   plugins/ (custom)
```

## Config
- `app-config.yaml` (+ `.local`, `.production` overrides). Env vars via `${VAR}`.

## Databases
- **SQLite** = dev/test · **PostgreSQL** = production.
- **Knex**, one logical **DB per plugin**.

## Architecture
- Core + App + Plugins. SPA ↔ backend over **HTTP**.
- **Proxy** service solves **CORS** for 3rd-party APIs.
- Backend plugins talk **only over the network**; **modules** extend them via
  **extension points**; **utility APIs** connect frontend plugins.

## Catalog envelope
```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata: { name, namespace, title, labels, annotations, tags, links }
spec: { type, lifecycle, owner, system }
```
- **Component** requires: **type, lifecycle, owner**.
- `relations` & `status` are **read-only** (generated).
- `name`: ≤ **63 chars**, unique per kind+namespace.

## Kinds
Component · API · Resource · System · Domain · Group · User · Location · Template

Hierarchy: **Domain ⊃ System ⊃ Component/Resource/API**; Group ⊃ User.

- Component types: service / website / library
- API types: openapi / asyncapi / graphql / grpc
- lifecycle: experimental / production / deprecated

## metadata distinctions
- **labels** = identifying key/values (filter/select)
- **annotations** = non-identifying links to external tools (e.g. `backstage.io/techdocs-ref`)
- **tags** = free-form string list

## Ingestion
- Register: config **Location**, UI "Register existing component", or **discovery**
  (Entity Providers scan repos for `catalog-info.yaml`).

## Plugins
- **Frontend** = React UI at a route (`@scope/plugin-x`).
- **Backend** = Node service (`@scope/plugin-x-backend`).
- Wire frontend in `App.tsx`; backend in `backend/src/index.ts`.
- UI = **Material UI (MUI)** + `@backstage/core-components`.

## Software Templates (Scaffolder)
- `kind: Template` → **parameters** (form) + **steps** (actions) + **output**.
- Actions: `fetch:template`, `publish:github`, `catalog:register`.
- Templating: `${{ parameters.x }}`. **Custom action IDs = camelCase**.

## TechDocs
- **Docs-like-code**, Markdown + **MkDocs** (`mkdocs.yml`).
- Annotation: **`backstage.io/techdocs-ref`**.
- Pipeline: **Generator → Publisher → Storage** (local, or S3/GCS/Azure).
