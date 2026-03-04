# Hadron Controller

The Hadron Controller is the central server for the Hadron graph system. It maintains
synchronized copies of all configured organization graphs, exposes a GraphQL API for
querying and mutating graph data, and records usage telemetry from AI agents and
developer tools.

## Technology Stack

- **Runtime**: Node.js (ESM), TypeScript
- **HTTP**: Express 4
- **GraphQL**: Apollo Server 4 + `expressMiddleware`
- **Database ORM**: Prisma 6 (PostgreSQL in production, SQLite for dev)
- **Auth**: `jose` (JWT), `bcrypt` (API key hashing)
- **Config parsing**: `yaml`

## Architecture

```
                        ┌─────────────────────────────────────────┐
                        │           hadron-controller              │
  Browser / UI  ──────► │  /auth/github  →  GitHub OAuth          │
  AI agents     ──────► │  POST /graphql  (Apollo Server)         │
  MCP clients   ──────► │  POST /usage    (REST, no auth)         │
  CI tools      ──────► │  POST /reload   (trigger sync)          │
                        │                                         │
                        │  controller/                            │
                        │    startup()   ← runs on boot           │
                        │    syncGraphFromGithub()                 │
                        │    findGraph / createNode / …            │
                        │           │                             │
                        │    integrations/github/   localFs/      │
                        │    (git clone/pull)  (parse .node/)     │
                        │           └──── integrations/db/ ───    │
                        │              (Prisma + PG/SQLite)       │
                        └─────────────────────────────────────────┘
```

## Directory Structure

```
src/
  index.ts               Entry point — boots server, triggers startup()
  server.ts              Express app, routes, Apollo middleware
  config.ts              Reads .hadron-controller.yaml, exports SOURCES_PATH
  auth/
    github.ts            GitHub OAuth flow
    jwt.ts               signJwt / verifyJwt (jose, HS256, 7-day expiry)
  middleware/
    auth.ts              resolveAuthContext — JWT vs API key detection
  api/graphql/schema/
    typeDefs.ts          GraphQL schema (SDL)
    resolvers.ts         All query and mutation resolvers
  controller/
    startup.ts           Syncs all configured graphs on boot
    operations/          findGraph, createNode, updateNode, createUsageEvent
  integrations/
    db/                  Prisma client + PostgreSQL/SQLite provider abstraction
    localFs/             loadNodeFromFs, loadGraphFromFs, saveNodeToFs, …
    github/              syncGraphFromGithub
```

## Startup & Sync

On boot, `controller.startup()` reads `.hadron-controller.yaml` and syncs each
configured graph. Sync can also be triggered at any time via `POST /reload`.

## Authentication

Two token types are accepted via `Authorization: Bearer <token>`:
- **JWT** (contains dots) — issued after GitHub OAuth login
- **API key** (hex string) — created for machine clients (agents, CI, IoT)

Roles: `READER < CONTRIBUTOR < ADMIN`. Contributors may be scoped to a namespace.

## Dual Database

Runs on PostgreSQL in production and SQLite for local development.
The `src/integrations/db/provider.ts` module abstracts the differences
(native arrays vs JSON strings, enum types, case-insensitive search mode).
