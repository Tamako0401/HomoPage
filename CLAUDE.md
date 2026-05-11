# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

HomoPage is a full-stack web application with a Vue 3 frontend (`client/`) and a NestJS backend (`server/`), backed by a PostgreSQL 16 database. This is a monorepo using **pnpm** for the client and **npm** for the server (separate `node_modules`).

## Prerequisites

- Node.js `^20.19.0 || >=22.12.0`
- pnpm (client)
- Docker (for PostgreSQL)

## Starting the Database

```bash
docker compose up -d
# PostgreSQL 16 on :5432, DB: navdb, user: postgres, password: 123456
```

## Client (`client/`)

Vue 3 + TypeScript + Vite, with Pinia for state management and Vue Router.

| Command | Description |
|---------|-------------|
| `pnpm dev` | Start Vite dev server (HMR) |
| `pnpm build` | Type-check then production build |
| `pnpm type-check` | Run `vue-tsc --build` only |
| `pnpm lint` | Run oxlint then ESLint (both auto-fix) |
| `pnpm format` | Prettier format `src/` |

**Import alias**: `@/` maps to `src/` (configured in both `vite.config.ts` and `tsconfig.app.json`).

**Key stack**: Vue 3.5 (Composition API, `<script setup>`), Pinia 3, Vue Router 5, Vite 8, TypeScript 6.0.

Two `tsconfig` references exist: `tsconfig.app.json` (app code, extends `@vue/tsconfig`) and `tsconfig.node.json` (build/config files, extends `@tsconfig/node24`).

## Server (`server/`)

NestJS (Express) with TypeScript. Standard module/controller/service architecture.

| Command | Description |
|---------|-------------|
| `npm run start:dev` | Start with watch mode (port 3000 by default) |
| `npm run build` | `nest build` to `dist/` |
| `npm run start:prod` | Run compiled `dist/main` |
| `npm test` | Jest unit tests (`*.spec.ts` in `src/`) |
| `npm run test:e2e` | Jest e2e tests (`test/*.e2e-spec.ts`) |
| `npm run test:cov` | Jest with coverage |
| `npm run lint` | ESLint on `src/`, `apps/`, `libs/`, `test/` |
| `npm run format` | Prettier on `src/**/*.ts` and `test/**/*.ts` |

**Key stack**: NestJS 11, TypeScript ~5.7, Jest 30, `ts-jest` transformer, ESLint flat config (`eslint.config.mjs`) with TypeScript-typed linting.

## Architecture

- **Server entry**: `server/src/main.ts` — bootstraps `AppModule` via `NestFactory.create()`, listens on `process.env.PORT ?? 3000`
- **Client entry**: `client/src/main.ts` — creates Vue app, installs Pinia + Router, mounts to `#app`
- **Client routing**: `client/src/router/index.ts` — two routes: `/` (HomeView, eager) and `/about` (AboutView, lazy-loaded)
- **Client state**: Pinia stores in `client/src/stores/` (currently a single `counter` store using Setup Store syntax)

The `AppModule` (`server/src/app.module.ts`) currently has a single `AppController` and `AppService` — this is a starter template with no custom business logic yet.

## Testing

- **Server unit tests**: `server/src/**/*.spec.ts` — uses `@nestjs/testing` `Test.createTestingModule()`
- **Server e2e tests**: `server/test/*.e2e-spec.ts` — uses `supertest` against a real NestJS app instance
- Jest config is embedded in `server/package.json` (`jest` key)

## Code Style

- Prettier is the sole formatter for both client and server (VS Code configured to format on save)
- Client: semicolons off, single quotes, 100 char width
- Server: semicolons on, single quotes, trailing commas (per ESLint Prettier plugin)
- ESLint flat config used in both projects
- Client uses `@vue/eslint-config-typescript` + `eslint-plugin-oxlint` for correctness linting
- Server uses `typescript-eslint` strict typed checks with `projectService: true`
