<div align="center">

# Cairn

**The knowledge base your engineering team actually writes in.**

*Every solved problem leaves a marker for whoever comes next.*

A production-grade single-page application for internal technical knowledge: long-form articles with runnable code blocks, role-based publishing, peer review through comments and ratings, and a design system that can be replaced under a feature flag without a rewrite.

[![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=white)](https://react.dev)
[![TypeScript](https://img.shields.io/badge/TypeScript-4.5-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![Redux Toolkit](https://img.shields.io/badge/Redux%20Toolkit-1.9-764ABC?logo=redux&logoColor=white)](https://redux-toolkit.js.org)
[![Feature Sliced Design](https://img.shields.io/badge/Architecture-Feature%20Sliced%20Design-FF6B6B)](https://feature-sliced.design)
[![Webpack](https://img.shields.io/badge/Webpack-5-8DD6F9?logo=webpack&logoColor=black)](https://webpack.js.org)
[![Vite](https://img.shields.io/badge/Vite-3-646CFF?logo=vite&logoColor=white)](https://vitejs.dev)
[![Storybook](https://img.shields.io/badge/Storybook-6.5-FF4785?logo=storybook&logoColor=white)](https://storybook.js.org)
[![Jest](https://img.shields.io/badge/Jest-unit%20%26%20RTL-C21325?logo=jest&logoColor=white)](https://jestjs.io)
[![Cypress](https://img.shields.io/badge/Cypress-e2e-17202C?logo=cypress&logoColor=white)](https://www.cypress.io)
[![Loki](https://img.shields.io/badge/Loki-visual%20regression-2C3E50)](https://loki.js.org)
[![i18next](https://img.shields.io/badge/i18n-EN%20%7C%20PL-26A69A?logo=i18next&logoColor=white)](https://react.i18next.com)

</div>

---

## The problem

Engineering knowledge inside a company has no home. Architecture decisions live in Slack threads that fall out of search, onboarding guides rot in a wiki nobody owns, and the one person who understands the payment pipeline explains it from scratch every quarter. Generic wikis fail for technical writing in three specific ways: they render code badly, they have no notion of *who is allowed to publish what*, and they give readers no way to signal that a document is still correct.

**Cairn** is named after the stacks of stones travellers leave on a trail so the next person knows the way. It is a knowledge platform built for engineers instead of adapted for them:

- **Structured articles, not documents.** Content is stored as a typed block tree — `TEXT`, `CODE`, `IMAGE` — so a snippet is a first-class object with syntax highlighting and copy-to-clipboard, not a screenshot of a terminal.
- **Publishing has an owner.** `USER`, `MANAGER` and `ADMIN` roles gate creation, editing and the admin area at the router level, so a knowledge base can be opened to the whole company without becoming unmaintainable.
- **Readers close the loop.** Ratings on articles and authors, threaded comments and a recommendation rail surface what is trusted and expose what has gone stale.
- **It survives redesign.** The entire UI kit exists in two implementations behind one feature flag — the platform can be re-skinned incrementally, in production, without freezing feature work.

The repository is also a reference implementation of a large-scale React codebase: strict Feature-Sliced Design enforced by custom lint rules, two interchangeable bundlers, four layers of automated tests and a code-generation toolkit for scaffolding and retiring features.

---

## Features

### Knowledge & content
- **Block-based article editor** — compose articles from text, code and image blocks; create and edit under `/articles/new` and `/articles/:id/edit`.
- **Article feed with two densities** — grid and list views, persisted per user.
- **Server-driven search, filtering and sorting** — full-text query, category tabs (`IT`, `Science`, `Economics`) and sorting by views, title or creation date, all synchronised to the URL so any view is shareable.
- **Infinite scroll** — pagination driven by an `IntersectionObserver` hook, with scroll position restored when returning to the feed.
- **Recommendations** — a related-articles rail on every article page, served through a dedicated RTK Query endpoint.
- **Comments and ratings** — per-article discussion plus a star-rating flow with optional written feedback, mirrored for author profiles.

### Access & identity
- **Username/password authentication** with session restore from `localStorage` on boot.
- **Role-based routing** — declarative `authOnly` and `roles` metadata per route, enforced by a `RequireAuth` guard that redirects to `/forbidden` rather than rendering a broken page.
- **Admin panel** reachable only by `ADMIN` and `MANAGER`.
- **Editable profiles** — inline edit mode with per-field validation, country and currency selectors, avatar, and cancel-to-restore.
- **Live notifications** — polled every 10s via RTK Query, rendered as a popover on desktop and a gesture-driven bottom drawer on mobile.

### Experience
- **Three themes** — light, dark and orange, persisted server-side in the user's `jsonSettings` so the choice follows the account across devices.
- **Two languages** — English and Polish, with namespaced translation files and lint rules that fail the build on hardcoded strings.
- **Adaptive rendering** — desktop and touch layouts render different component trees instead of hiding elements with CSS.
- **First-visit onboarding** — a one-time greeting on the articles page, tracked in user settings rather than local state.
- **Error boundaries and dedicated 403/404 pages** so a failing widget never blanks the app.

---

## Tech stack

| Area | Choice |
| --- | --- |
| UI | React 18, TypeScript, SCSS Modules |
| State | Redux Toolkit, RTK Query, async thunks, `EntityAdapter` normalisation |
| Routing | React Router v6, typed route config, route-level guards |
| Architecture | Feature-Sliced Design, enforced by custom ESLint rules |
| Build | Webpack 5 and Vite 3 — both fully wired |
| i18n | i18next, `react-i18next`, HTTP backend, language detector |
| Animation | `@react-spring/web` + `@use-gesture/react`, lazy-loaded |
| Docs | Storybook 6.5 with mocked network, themes and store decorators |
| Tests | Jest, React Testing Library, Loki, Cypress |
| Quality | ESLint (Airbnb), Stylelint, Prettier, Husky, lint-staged |

---

## Quick start

Requirements: **Node.js 17+** and npm.

```bash
npm install                 # install dependencies
npm run start:dev           # webpack dev server + mock API
# or
npm run start:dev:vite      # vite + mock API
```

| Service | URL |
| --- | --- |
| App | http://localhost:3000 |
| Mock API (HTTP) | http://localhost:8000 |
| Mock API (HTTPS) | https://localhost:8443 |
| Storybook | http://localhost:6006 (`npm run storybook`) |

The mock backend is a `json-server` instance seeded with 21 articles, 34 comments, users and notifications. It implements a real `POST /login`, rejects unauthenticated requests with `403`, and adds an artificial 800 ms latency so loading states are exercised during development.

**Demo accounts** (password `123` for all):

| Username | Role | Sees |
| --- | --- | --- |
| `admin` | `ADMIN` | everything, including `/admin` |
| `manager` | `MANAGER` | admin area, no destructive actions |
| `user` | `USER` | reading, commenting, rating |

---

## Architecture

The codebase follows [Feature-Sliced Design](https://feature-sliced.design). Layers are ordered by responsibility, and a module may only import from layers below it:

```
app        →  providers, global styles, router, error boundaries
pages      →  route-level composition only
widgets    →  self-contained blocks combining features and entities
features   →  one user action each (auth, rate, comment, switch theme)
entities   →  business objects (Article, User, Profile, Comment, Rating)
shared     →  UI kit, hooks, api, config — knows nothing about the domain
```

```
src/
├── app/            App root, providers (store, theme, router), global styles
├── pages/          10 lazily loaded route pages
├── widgets/        Navbar, Sidebar, Page, ArticlesFilters, ScrollToolbar, …
├── features/       17 slices — AuthByUsername, articleRating, addCommentForm, …
├── entities/       9 domain models with their own reducers and public API
└── shared/         ui/ (deprecated + redesigned kits), lib/, api/, const/, layouts/
```

### Layer rules are enforced, not documented

The project uses the `eslint-plugin-ulbi-tv-plugin` rule set so architectural drift fails CI instead of surviving code review:

| Rule | Guarantee |
| --- | --- |
| `path-checker` | No absolute imports inside a module — a slice stays relocatable. |
| `layer-imports` | No upward imports — a `feature` can never reach into a `widget`. |
| `public-api-imports` | Cross-module imports go through `index.ts` only, with autofix. |

A `CircularDependencyPlugin` fails the webpack build on any import cycle, and `fork-ts-checker-webpack-plugin` type-checks in a parallel process so dev rebuilds stay fast.

---

## Engineering decisions worth a look

**Two design systems behind one flag.** `shared/ui` contains both a `deprecated/` kit and a `redesigned/` one. Every consumer picks between them through the `<ToggleFeatures feature="isAppRedesigned" on={…} off={…} />` component or the `toggleFeatures()` helper, and flags are stored per user — so the new interface can be rolled out to a cohort, measured and reverted without a branch. When a flag has served its purpose, `npm run remove-feature <flagName> <on|off>` rewrites the AST with `ts-morph`, deletes the dead branch and removes the toggle. Migration debt is paid off by a script, not by hand.

**Bundle discipline.** Every page is code-split via `React.lazy`. Feature reducers are injected at mount and torn down at unmount by `DynamicModuleLoader`, so a slice's logic ships only when its route is opened. The animation libraries are loaded through an `AnimationProvider` that resolves `@react-spring/web` and `@use-gesture/react` on demand — the drawer's physics cost nothing to users who never open it.

**Data layer.** RTK Query owns server state, caching and polling; the auth token is attached in `prepareHeaders`. Reusable collections are normalised with `EntityAdapter`, and `buildSelector`/`buildSlice` factories cut the per-slice boilerplate down to the parts that actually differ.

**Two bundlers, on purpose.** Webpack drives production builds — content hashing, CSS extraction, locale copying, bundle analysis, HMR via React Refresh. Vite provides an instant dev loop. Both define the same `__IS_DEV__`, `__API__` and `__PROJECT__` globals, so the app cannot tell which one built it.

**Scaffolding.** `npm run generate:slice` generates a complete FSD slice — UI, model, types, public API, tests — from a template, so the tenth feature is laid out exactly like the first.

---

## Testing

Four layers, each covering what the layer below cannot:

| Layer | Tool | Command | Covers |
| --- | --- | --- | --- |
| Unit | Jest | `npm run test:unit` | reducers, selectors, thunks, helpers |
| Component | React Testing Library | `npm run test:unit` | rendering and interaction against a real store |
| Visual regression | Loki | `npm run test:ui` | 53 Storybook stories screenshotted on desktop and iPhone 7 |
| End-to-end | Cypress | `npm run test:e2e` | routing, profile editing, article details, ratings, comments |

Visual baselines are approved with `npm run test:ui:ok`; `npm run test:ui:report` produces a browsable HTML diff. Cypress ships custom commands (`login`, `createArticle`, `addComment`, `setRate`) and fixtures, so specs read as user stories rather than selector soup. Full details: [docs/tests.md](docs/tests.md).

---

## Storybook

Every component has stories next to it as `*.stories.tsx`. Decorators supply a Redux store, theme, router and translations, while `storybook-addon-mock` stubs network calls — components can be developed and reviewed without the backend running. See [docs/storybook.md](docs/storybook.md).

```bash
npm run storybook          # dev server on :6006
npm run storybook:build    # static build, also used by visual tests
```

---

## Scripts

<details>
<summary><b>All available commands</b></summary>

### Run
| Command | Description |
| --- | --- |
| `npm run start` | Frontend on the webpack dev server |
| `npm run start:vite` | Frontend on Vite |
| `npm run start:dev` | Webpack dev server + mock backend |
| `npm run start:dev:vite` | Vite + mock backend |
| `npm run start:dev:server` | Mock backend only |

### Build
| Command | Description |
| --- | --- |
| `npm run build:prod` | Production build |
| `npm run build:dev` | Development build (not minified) |

### Quality
| Command | Description |
| --- | --- |
| `npm run lint:ts` | Lint TypeScript |
| `npm run lint:ts:fix` | Lint and autofix TypeScript |
| `npm run lint:scss` | Lint SCSS |
| `npm run lint:scss:fix` | Lint and autofix SCSS |
| `npm run prettier` | Format the codebase |

### Tests
| Command | Description |
| --- | --- |
| `npm run test:unit` | Unit and component tests |
| `npm run test:e2e` | Open the Cypress runner |
| `npm run test:ui` | Visual regression run |
| `npm run test:ui:ok` | Approve new screenshots |
| `npm run test:ui:ci` | Visual regression against the static Storybook build |
| `npm run test:ui:report` | Generate the HTML diff report |

### Tooling
| Command | Description |
| --- | --- |
| `npm run generate:slice` | Scaffold a new FSD slice |
| `npm run remove-feature` | Retire a feature flag and its dead branch |
| `npm run storybook` | Storybook dev server |
| `npm run storybook:build` | Static Storybook build |

</details>

---

## CI/CD

The GitHub Actions pipeline in [.github/workflows](.github/workflows) runs the production build, the Storybook build, the unit suite and the visual regression suite on every push and pull request, then publishes the generated screenshot-diff report to GitHub Pages — so a UI change that shifts a pixel is visible in the PR rather than discovered after release. Locally, Husky's pre-commit hook runs Prettier and ESLint through `lint-staged`, so broken formatting never reaches a branch. `netlify.toml` and `.deploy/` (shell script + nginx config) cover SPA-friendly hosting on either Netlify or a plain server.

---

## Internationalisation

Translations live in `public/locales/{en,pl}` split into namespaces (`translation`, `main`, `about`, `profile`, `article-details`). `eslint-plugin-i18next` rejects hardcoded user-facing strings, and `babel-plugin-i18next-extract` keeps the locale files in sync with the code. Adding a language means adding a folder.

---

## Project structure at a glance

| | |
| --- | --- |
| TypeScript / TSX modules | 424 |
| SCSS modules | 76 |
| Storybook stories | 53 |
| Test files | 28 |
| Route pages | 10 |
| Feature slices | 17 |
| Domain entities | 9 |

---

## Configuration

```
config/
├── build/       webpack — loaders, plugins, resolvers, dev server
├── babel/       custom babel plugin that strips test props from production
├── jest/        test environment, module mapping, HTML reporter
└── storybook/   Storybook builder config and global decorators
scripts/
├── createSlice/     FSD slice generator
├── refactoring/     codemods for public APIs and imports
└── remove-feature.ts  AST-based feature-flag removal
```

The production build strips `data-testid` attributes through a custom Babel plugin, so test hooks never ship to users.
