# Domain 4 — Customizing Backstage (32%)

The largest domain. Frontend vs backend plugins, modifying existing plugins,
editing React code, and using Material UI. Also covers Software Templates & TechDocs.

## Official curriculum objectives

- [ ] Understand frontend versus backend plugins
- [ ] Customizing Backstage plugins
- [ ] Make changes to React code in Backstage App
- [ ] Using Material UI components

> Note: the official curriculum lists only the four objectives above. Software
> Templates and TechDocs are included here as closely related customization topics
> that commonly appear in Backstage learning material — treat them as supporting,
> not core exam objectives.

## Plugins: the unit of extension

Backstage composes a single-page app from **plugins**. A plugin adds a feature:
a whole page, a tab, a card on the entity page, or backend APIs.

### Frontend vs backend plugins

| | Frontend plugin | Backend plugin |
|--|----------------|----------------|
| Runs in | Browser (React SPA) | Node.js backend |
| Provides | UI: pages, tabs, cards, components | APIs, data processing, integrations |
| Mounted at | A route (e.g. `/catalog`) | An HTTP route on the backend |
| Talks to | Backend via HTTP / Utility APIs | DB, external systems; other plugins via network |
| Package name | `@scope/plugin-<id>` | `@scope/plugin-<id>-backend` |

- **Modules** extend a *backend* plugin through its **extension points** (e.g. a
  new scaffolder action, a new catalog entity provider).
- Frontend plugins expose UI via **extensions**; backend plugins expose hooks via
  **extension points**.

### Creating a plugin

```bash
yarn new            # interactive: choose plugin / backend-plugin / etc.
# (older: yarn create-plugin)
```
- Scaffolds into the top-level **`plugins/`** directory.
- You then **wire it into the app**: import it in `packages/app/src/App.tsx`
  (frontend) or add it in `packages/backend/src/index.ts` (backend, e.g.
  `backend.add(import('@scope/plugin-x'))`).

### Plugin package structure (up to 5 packages)
- Frontend: `plugin-<id>` (+ `-react` library)
- Backend: `plugin-<id>-backend` (+ `-node` library)
- Shared isomorphic: `plugin-<id>-common`

## Modifying / customizing existing plugins

- **Integrate into the catalog** — add tabs/cards to the entity page by editing
  `packages/app/src/components/catalog/EntityPage.tsx` (compose `<EntityLayout>`,
  `<EntitySwitch>`, cards, grids).
- **Routing & layout** — edit `App.tsx` to add `<Route>`s and the sidebar
  (`packages/app/src/components/Root/`).
- **Utility APIs** — override or supply implementations via `apis.ts` to change
  behavior (e.g. custom auth, custom API client).
- **Theme** — provide a custom theme (see Material UI below).

## Editing React code

- The frontend is **React + TypeScript**. Components are `.tsx` files.
- Key files:
  - `packages/app/src/App.tsx` — app routes, plugin wiring.
  - `packages/app/src/components/catalog/EntityPage.tsx` — entity page layout.
  - `packages/app/src/components/Root/Root.tsx` — sidebar / nav.
  - `packages/app/src/apis.ts` — Utility API implementations.
- Backstage provides reusable components in `@backstage/core-components`
  (e.g. `Header`, `Page`, `Content`, `InfoCard`, `Table`).

## Material UI (MUI)

- Backstage's UI is built on **Material UI (MUI)** + its own core-components.
- Use MUI components (`Grid`, `Card`, `Typography`, `Button`, …) inside plugins.
- Styling uses MUI's `makeStyles` / `useStyles` (hooks-based CSS-in-JS).
- **Custom themes** are created and passed to the app to change colors, typography,
  and branding.

```tsx
import { Grid } from '@material-ui/core';
import { InfoCard, Header, Page, Content } from '@backstage/core-components';

export const MyPluginPage = () => (
  <Page themeId="tool">
    <Header title="My Plugin" />
    <Content>
      <Grid container spacing={3}>
        <Grid item xs={12}>
          <InfoCard title="Hello">Custom content here</InfoCard>
        </Grid>
      </Grid>
    </Content>
  </Page>
);
```

## Software Templates (Scaffolder)

Templates let users create new Components by automating scaffolding: they render a
skeleton, template in variables, and publish to a Git provider (GitHub/GitLab).

- Defined as a **`Template`** entity (`kind: Template`).
- Sections: **`parameters`** (form inputs, JSON-Schema driven, can be multi-step),
  **`steps`** (ordered actions), **`output`** (links shown on completion).
- **Actions** are the operations run in steps, e.g.:
  - `fetch:template` — fetch & template the skeleton
  - `publish:github` — create the repo
  - `catalog:register` — register the new component in the catalog
- **Custom action IDs use camelCase** (dashes break JS expressions).
- Uses **Nunjucks-style templating**: `${{ parameters.name }}`.
- Each run gets a **task ID** with streamed logs and success/failure feedback.

```yaml
apiVersion: scaffolder.backstage.io/v1beta3
kind: Template
metadata:
  name: react-ssr-template
  title: React SSR Template
spec:
  owner: team-artist
  type: website
  parameters:
    - title: Choose a name
      required: [name]
      properties:
        name:
          type: string
  steps:
    - id: fetch
      name: Fetch Base
      action: fetch:template
      input:
        url: ./skeleton
        values:
          name: ${{ parameters.name }}
    - id: publish
      name: Publish
      action: publish:github
      input:
        repoUrl: github.com?repo=${{ parameters.name }}&owner=acme
    - id: register
      name: Register
      action: catalog:register
      input:
        catalogInfoUrl: ${{ steps.publish.output.repoContentsUrl }}/catalog-info.yaml
  output:
    links:
      - title: Repository
        url: ${{ steps.publish.output.remoteUrl }}
```

## TechDocs

Docs-like-code: engineers write **Markdown** next to their code and get a rendered
doc site inside Backstage.

- Built on **MkDocs**; each docs source needs an **`mkdocs.yml`** and a `docs/` dir.
- A component opts in via the annotation
  **`backstage.io/techdocs-ref`** (e.g. `dir:.` to build from the repo).
- Three-part pipeline: **Generator** (Markdown → HTML) → **Publisher** → **Storage**
  (local filesystem for dev, or **AWS S3 / Google Cloud Storage / Azure Blob** for
  prod).
- Docs appear on the entity page's **Docs** tab; discoverable via Search.
- **TechDocs Addons** augment the read-time experience (e.g. report-issue).

## Key takeaways

- Frontend plugins = React UI at a route; backend plugins = Node services; modules
  extend backend plugins via extension points.
- Customize by editing `App.tsx`, `EntityPage.tsx`, `apis.ts`; wire plugins in.
- UI = Material UI + `@backstage/core-components`; theming for branding.
- Templates: `parameters` + `steps` (actions) + `output`; camelCase action IDs.
- TechDocs: MkDocs + `techdocs-ref` annotation; generator/publisher/storage.
