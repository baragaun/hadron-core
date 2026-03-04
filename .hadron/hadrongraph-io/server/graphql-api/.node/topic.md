# GraphQL API

The controller exposes `POST /graphql` (Apollo Server 4) and lightweight REST
endpoints for high-frequency operations.

## GraphQL Queries

| Query | Auth | Description |
|-------|------|-------------|
| `node(loc)` | any | Single node by dotted loc |
| `nodes(namespace, tags, search, limit)` | any | Search/filter nodes |
| `subtree(prefix)` | any | All nodes whose loc starts with prefix |
| `nodeUsage(loc, limit)` | any | Recent usage events for a node |
| `sessions(type, repo, customerId, limit)` | any | List sessions |
| `session(id)` | any | Single session with events |
| `clients` | ADMIN | All API key clients |
| `users` | ADMIN | All registered users |
| `me` | authenticated | Current user from JWT |

## GraphQL Mutations

| Mutation | Auth | Description |
|----------|------|-------------|
| `upsertNode(input)` | CONTRIBUTOR | Create or update a node |
| `deleteNode(loc)` | CONTRIBUTOR | Delete a node |
| `replaceSubtree(namespace, ownerRepo, nodes, edges)` | CONTRIBUTOR | Atomically replace a namespace |
| `startSession(input)` | any | Start tracking a session |
| `endSession(id, summary, tokens, …)` | any | Mark session complete |
| `recordUsage(event)` | any | Record a usage event |
| `createClient(label, type, roles, …)` | ADMIN | Create API key (raw key returned once) |
| `revokeClient(label)` | ADMIN | Delete a client |
| `updateUserRole(userId, role)` | ADMIN | Change a user's role |

## REST Endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| `GET` | `/health` | none | Health check |
| `GET` | `/auth/github` | none | Start GitHub OAuth |
| `GET` | `/auth/github/callback` | none | OAuth callback — issues JWT, redirects |
| `GET` | `/auth/me` | JWT | Current user info |
| `POST` | `/usage` | none | Ingest a UsageEvent (no GraphQL overhead) |
| `POST` | `/sessions/:id/outcome` | none | Report session outcome (0.0–1.0) |
| `POST` | `/reload` | none | Re-sync all graphs from GitHub |

## Authentication

`Authorization: Bearer <token>`:
- **JWT** (contains dots) — GitHub OAuth user token
- **API key** (no dots) — machine client token

Token type detection is in `src/middleware/auth.ts`.

## SQLite Field Resolvers

Three models have field resolvers that transparently deserialize SQLite JSON strings:
- `Node.tags` — `String` → `string[]`
- `Session.outcomeMeta` — `String` → `object`
- `UsageEvent.actionArgs` — `String` → `object`

These are no-ops on PostgreSQL.
