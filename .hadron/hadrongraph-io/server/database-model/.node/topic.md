# Database Model

The Prisma schema (`prisma/schema.postgresql.prisma`) defines seven models. A SQLite
variant (`prisma/schema.sqlite.prisma`) mirrors the same structure with string-encoded
arrays and JSON fields.

## Node

Represents a single node in a Hadron graph.

| Field | Type | Notes |
|-------|------|-------|
| `id` | String `@id` | UUIDv7 (no hyphens) from `node.yaml` — stable across moves |
| `loc` | String `@unique` | Dotted path, e.g. `myorg.myrepo.services` |
| `name` | String | Human-readable label |
| `alias` | String? | Alternative name |
| `description` | String? | One-liner |
| `content` | String? | Full `topic.md` text |
| `contentHash` | String? | SHA256(yaml + topic.md)[0:8] for change detection |
| `tags` | String[] | PostgreSQL native array; JSON string in SQLite |
| `namespace` | String | Graph-level namespace, e.g. `myorg.myrepo` |
| `ownerRepo` | String | GitHub repo slug, e.g. `myorg/myrepo` |

Indexes: `namespace`. Relations to `Edge` (outgoing, incoming), `PendingEdge`, `UsageEvent`.

## Edge

Directed relationship between two nodes.

| Field | Type | Notes |
|-------|------|-------|
| `sourceId` | String | FK → Node (cascade delete) |
| `targetId` | String | FK → Node (cascade delete) |
| `label` | String? | The `rel` field from `node.yaml` |

Unique constraint on `(sourceId, targetId)`.

## PendingEdge

An edge whose target node hasn't been synced yet. Stored without a FK on `targetId`.
Converted to a real `Edge` when the target arrives in any subsequent sync.

| Field | Type | Notes |
|-------|------|-------|
| `sourceId` | String | FK → Node (cascade delete) |
| `targetId` | String | Intended target id — **NOT a FK** |
| `label` | String? | The `rel` field from `node.yaml` |

Unique constraint on `(sourceId, targetId)`. Index on `targetId` for fast resolution lookup.

## Session

Tracks an AI agent or chatbot session from start to end.

Key fields: `id` (caller-generated), `type`, `clientId`, `repo`, `branch`,
`llmModel`, `inputTokens`, `outputTokens`, `turnCount`, `parentSessionId` (sub-agent),
`prevSessionId` (same thread), `outcome` (0.0–1.0), `outcomeRef`.

## UsageEvent

Every node access, action run, or tool call produces a record here.

Key fields: `type` (free-form, e.g. `h-view-node`), `nodeLoc` (stored directly —
survives node deletion), `nodeId` (FK → Node, nullable), `actionArgs` (Json),
`clientId`, `sessionId`.

## Client

Machine client that authenticates with an API key.

Key fields: `label` (unique), `keyHash` (bcrypt), `type` (WORKSTATION/CLOUD/IOT/AGENT),
`roles` (Role[]), `namespace` (optional write scope for CONTRIBUTOR).

## User

Human user who signed in via GitHub OAuth.

Key fields: `githubId` (unique, stable), `login`, `roles` (Role[]).

## Enums

```
Role:       ADMIN | CONTRIBUTOR | READER
ClientType: WORKSTATION | CLOUD | IOT | AGENT
```
