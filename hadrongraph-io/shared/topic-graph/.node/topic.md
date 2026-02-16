# Topic Graph

Nodes form a **knowledge graph** where users traverse from high-level concepts to
implementation details as needed.

## Graph Structure

### Nodes

Each node stores its data in a `.node/` subdirectory containing `node.yaml` (metadata)
and optionally `topic.md` (content), within the `.hadron/` folder tree.

The **directory structure** determines organizational hierarchy:

```
.hadron/
  baragaun-com/
    .node/
      node.yaml                         ← domain node (represents baragaun-com/)
    channels-service/
      .node/
        node.yaml                       ← project node (represents channels-service/)
      what-is-a-channel/
        .node/
          node.yaml                     ← content node
      channels/
        .node/
          node.yaml                     ← content node
  micromentor-org/
    .node/
      node.yaml                         ← domain node (represents micromentor-org/)
    mmdata/
      .node/
        node.yaml                       ← project node (represents mmdata/)
      models/
        .node/
          node.yaml                     ← content node
      services/
        .node/
          node.yaml                     ← module node (represents services/)
        service/
          .node/
            node.yaml                   ← content node
```

### Directory Nodes

Every directory that is a node contains a `.node/` folder with its metadata and content.
Child nodes are subdirectories alongside `.node/`.

Nodes closer to the root are broader; nodes nested deeper are more specific. No
explicit level field is needed — the directory depth carries this information.

### Edges: Node Relationships

Nodes reference other nodes via `nodes` in the metadata, with a `rel` field explaining
why the relationship exists:

- Each edge has a `loc` (dotted path to the target node) and `rel` (reason to traverse)
- Connections can cross domains (e.g., micromentor-org nodes referencing baragaun-com nodes)
- Multiple connections allowed between nodes
- Bidirectional traversal possible (broader ↔ more detailed)

**Example:**
```yaml
# node.yaml (models)
nodes:
  - loc: baragaun-com.secureid-service.secureid-service-hooks
    rel: Shows the exception pattern where hooks are used instead of app events
  - loc: micromentor-org.mmdata.services.service
    rel: Explains what a service is; models live within services
```

## Learning Flow

### From Actions to Topics

Action nodes reference **starting topics** needed to understand the work:

```yaml
# node.yaml for an action
nodes:
  - loc: micromentor-org.mmdata.models
    rel: How models are structured
  - loc: micromentor-org.mmdata.services.service
    rel: Service architecture
```

### User Traversal

Users follow node references to learn more:

```
Action → [Starting topics]
          ↓
       Follow `nodes` edges to related topics
          ↓
       User traverses until sufficient understanding
```

**Principle**: Users determine when they have enough context to proceed with the task.

## Node Metadata

```yaml
name: models
description: How business entity models are structured

tags:
  - model
  - database

nodes:
  - loc: micromentor-org.mmdata.services.service
    rel: Explains what a service is; models live within services
```

### Nodes Field

Lists connections to other nodes with annotated relationships. Each entry has a `loc`
(the full dotted path to the target node) and a `rel` field explaining why the
connection exists:

```yaml
nodes:
  - loc: micromentor-org.mmdata.services.service
    rel: Explains what a service is; models live within services
  - loc: baragaun-com.secureid-service.secureid-service-hooks
    rel: Shows the exception pattern where hooks are used instead of app events
```

The `rel` field helps AI agents and humans decide whether to traverse the edge — it
explains *why* consuming the related topic would be useful.

## Design Principles

1. **Directory = hierarchy**: Broader topics live closer to the domain root; detailed
   topics live deeper in the tree
2. **Multiple paths**: Same topic reachable from different starting points via `nodes`
3. **Flexible traversal**: Users choose their own learning depth
4. **Self-contained**: Each topic should be understandable with its references

## Benefits for LLMs

- **Context selection**: Load topics based on task requirements
- **Progressive loading**: Start with topics near the root, follow edges deeper as needed
- **Relationship awareness**: `nodes` edges explain which topics provide broader context
  vs deeper detail
- **Efficient learning**: Follow graph to exactly the depth needed

## Implementation Notes

- Nodes field is **optional** — not all nodes need explicit relationships
- Hierarchy is derived from the filesystem (directory depth = abstraction depth)
- Cross-organization references are supported (e.g., micromentor-org nodes referencing
  baragaun-com nodes)
- The `loc` path uses dots to separate segments: `domain.project.node-name`
