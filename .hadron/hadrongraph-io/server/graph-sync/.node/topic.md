# Graph Sync

Graph sync keeps the Hadron database in sync with GitHub repos. It runs on server
startup and whenever `POST /reload` is called.

## Sync Flow

```
syncGraphFromGithub("myorg/myrepo")
  ↓
1. Look up config for this graph name
2. git pull  (if SOURCES_PATH/<path>/.git exists)
   git clone (otherwise)
  ↓
3. loadGraphFromFs(graphPath, graphName, ownerRepo)
   Walk all non-hidden directories for .node/ dirs
   (also walks .hadron/ — strips .hadron/ prefix from loc)
   For each .node/ dir: loadNodeFromFs(dir, graphRoot, ownerRepo)
     → read .node/node.yaml  (id, name, description, tags, edges)
     → read .node/topic.md   (content)
     → derive loc from path  (/ → .)
  ↓
4. DB transaction:
   a. Upsert each node by id (loc can change on rename; id is stable)
   b. After each node upsert, resolve any PendingEdges waiting for it
   c. Delete stale nodes: namespace + ownerRepo, id NOT IN upsertedIds
   d. For each edge: if target exists → real Edge; if not → PendingEdge
```

## Node Identity

Each node has a stable UUIDv7 `id` stored in its `node.yaml`. The id survives:
- Directory renames (loc changes, id stays the same)
- Moves within the repo
- Re-syncs (upsert-by-id preserves the DB record)

## Namespace

All nodes from a given graph sync share the same `namespace` in the DB,
set from `graphNameToNamespace(graphName)` (slashes → dots). Example:
`"myorg/myrepo"` → namespace `"myorg.myrepo"`.

The `namespace` is used by stale deletion to identify which nodes belong to
this graph: `DELETE WHERE namespace = X AND ownerRepo = Y AND id NOT IN upsertedIds`.

## Key Files

| File | Role |
|------|------|
| `src/config.ts` | `loadConfig()`, `SOURCES_PATH`, `graphNameToNamespace()` |
| `src/controller/startup.ts` | Iterates graphs and calls sync |
| `src/integrations/github/syncGraphFromGithub.ts` | git + load FS + DB upsert |
| `src/integrations/localFs/loadGraphFromFs.ts` | Directory walk → nodes + edges |
| `src/integrations/localFs/loadNodeFromFs.ts` | Parse single `.node/` directory |

## Trigger Points

| Trigger | Action |
|---------|--------|
| Server boot | `startup()` — syncs all configured graphs |
| `POST /reload` | Re-runs `startup()` (e.g., called by CI after a push) |
