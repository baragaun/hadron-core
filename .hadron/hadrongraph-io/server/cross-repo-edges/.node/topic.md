# Cross-Repo Edges

A node in one graph can reference a node in a different graph by its stable `id`.
The `Edge` model has no namespace constraint on `targetId` — any node can reference
any other node in the database.

## The Problem

Graphs are synced independently. If repo A is synced before repo B, edges from A to
B's nodes cannot be created yet (the FK constraint on `Edge.targetId` would fail).

## Solution: PendingEdge

When an edge's target is not yet in the database, the edge is stored as a `PendingEdge`:

```
PendingEdge {
  sourceId: String   ← FK → Node (cascade delete if source is removed)
  targetId: String   ← NOT a FK — just stored for later resolution
  label:    String?
}
```

No FK on `targetId` means the record can be created even when the target doesn't exist.

## Resolution

During every graph sync, after each node is upserted, the controller checks for
`PendingEdge` records with `targetId = node.id`:

```
for each newly upserted node:
  pending = db.pendingEdge.findMany({ where: { targetId: node.id } })
  for each pending:
    db.edge.upsert(sourceId, targetId, label)
    db.pendingEdge.delete(pending.id)
```

This means cross-repo edges are resolved automatically the first time the target
repo is synced, regardless of sync order.

## Cascade Delete

If the source node is deleted (e.g., the file is removed from the repo), its
`PendingEdge` records cascade-delete via the `sourceId` FK. No orphan records remain.

## Pitfalls

**Not a self-referencing edge.** An earlier design considered making cross-repo
edges self-referencing (`targetId = sourceId`) as a placeholder, but the
`@@unique([sourceId, targetId])` constraint on `Edge` would only allow one pending
edge per source node. The separate `PendingEdge` table avoids this limitation.
