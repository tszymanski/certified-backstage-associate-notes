# Domain 1 — Backstage Development Workflow (24%)

Building and running Backstage locally, dev workflows, TypeScript, dependency
management (npm/yarn), and containerizing with Docker.

## Official curriculum objectives

- [ ] Build and run Backstage projects locally
- [ ] Understand local development workflows
- [ ] Compile a Backstage project with TypeScript
- [ ] Download and install dependencies for a Backstage project with NPM/Yarn
- [ ] Use Docker to build a container image of a Backstage project

## Creating an app

```bash
npx @backstage/create-app@latest
```

- Runs a wizard; you name the app (e.g. `my-backstage-app`).
- **Node.js 22 or 24** recommended (`nvm install 22`). Use Active LTS.
- Uses **Yarn** as the package manager and **Yarn workspaces** (a monorepo).

## Running locally

```bash
cd my-backstage-app
yarn install      # install dependencies
yarn start        # runs frontend + backend together (dev)
```

- **Frontend** serves on **port 3000** → http://localhost:3000
- **Backend** serves on **port 7007**
- On success you'll see the bundler compile (e.g. "compiled successfully").
- `yarn dev` is also commonly used to run both concurrently.

### Useful commands

| Command | Purpose |
|---------|---------|
| `yarn install` | Install all workspace dependencies |
| `yarn start` / `yarn dev` | Run app (frontend + backend) in dev mode |
| `yarn tsc` | Type-check the TypeScript project |
| `yarn build` | Build all packages |
| `yarn build:backend` | Build the backend bundle (for Docker image) |
| `yarn test` | Run tests |
| `yarn lint` | Lint packages |
| `yarn new` | Scaffold a new plugin/package |

## Repository / folder structure

```
my-backstage-app/
├── app-config.yaml         # main configuration
├── app-config.production.yaml  # prod overrides
├── catalog-info.yaml       # the app's own catalog entity
├── package.json            # root (workspaces + scripts)
├── yarn.lock
└── packages/
    ├── app/                # FRONTEND (React SPA)
    └── backend/            # BACKEND (Node.js services)
plugins/                    # local/custom plugins live here
```

- `packages/` = Yarn workspaces. `packages/app` is the frontend, `packages/backend`
  is the backend (auth, catalog, scaffolder, TechDocs).
- Custom plugins scaffolded locally land in the top-level `plugins/` directory.

## TypeScript & dependencies

- Backstage is written in **TypeScript**; the whole monorepo compiles with `tsc`.
- Dependency management is done with **Yarn** (workspaces resolve shared deps).
- Plugin packages are npm packages named like `@<scope>/plugin-<plugin-id>`
  (e.g. `@backstage/plugin-catalog`).
- Backend plugin packages often carry a `-backend` suffix; shared library packages
  use `-react`, `-node`, or `-common`.

## Containerizing with Docker

Two common approaches:

1. **Host build** — build TypeScript on the host, then copy the bundle into a
   lightweight image (the default `packages/backend/Dockerfile`):
   ```bash
   yarn install --immutable
   yarn tsc
   yarn build:backend
   docker image build . -f packages/backend/Dockerfile --tag backstage
   docker run -it -p 7007:7007 backstage
   ```
2. **Multi-stage Docker build** — do the build entirely inside the Dockerfile.

- Typically you build **frontend, backend, and database** as separate containers,
  or serve the static frontend bundle from the backend.
- The frontend is a static bundle; the backend is a long-running Node service.

## Key takeaways

- `npx @backstage/create-app@latest` bootstraps a monorepo.
- Node 22/24, Yarn workspaces, TypeScript.
- Frontend :3000, Backend :7007.
- `yarn start` for dev; `yarn build:backend` + Dockerfile for images.
