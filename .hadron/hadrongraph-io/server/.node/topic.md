# Hadron Server

The Hadron server (also called the controller) is the central infrastructure component
that stores all organization graphs, exposes a GraphQL API, and records usage telemetry
from AI agents and developer tools.

## Topics

- [Controller](./controller/.node/topic.md) — what the server does and how it's structured
- [Configuration](./configuration/.node/topic.md) — `.hadron-controller.yaml` setup
- [Graph Sync](./graph-sync/.node/topic.md) — cloning/pulling repos and loading nodes into the DB
- [Database Model](./database-model/.node/topic.md) — Prisma schema (Node, Edge, PendingEdge, …)
- [Cross-Repo Edges](./cross-repo-edges/.node/topic.md) — how edges to not-yet-synced nodes are handled
- [GraphQL API](./graphql-api/.node/topic.md) — queries and mutations exposed by the server
