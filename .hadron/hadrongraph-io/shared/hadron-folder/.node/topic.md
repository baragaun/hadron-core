# Hadron Folder

The Hadron folder contains an organized collection of "nodes".

Each node stores its data in a `.node/` subdirectory containing `node.yaml` (required)
and `topic.md` (optional). A node can represent:

- **domain** (top level directory)
- **project** (top or second level directory)
- **(sub-) module** (directory under project)
- **knowledge** (a node with a `topic.md` describing a concept, pattern, or component)
- **action** (a node with `actions` in `node.yaml` and instructions in `topic.md`)

The directory structure under a project may loosely map to the module and submodule structure of
the source tree, as organized by the owner of the source code.

Every directory that is a node contains a `.node/` folder with its metadata and content.
Child nodes are subdirectories alongside `.node/`.

Nodes can reference other nodes, building a graph structure.

Domain and project graphs can be merged to serve a codebase that may contain sources
from other domains or projects, e.g. as libraries.

Example structure:

```
.hadron/
  mycompany-io/                       ← domain node: "mycompany-io"
    .node/
      node.yaml                       ← metadata for node "mycompany-io"
      topic.md                        ← topic for node "mycompany-io"
    superapp/                         ← project node: "mycompany-io.superapp"
      .node/
        node.yaml                     ← metadata for node "mycompany-io.superapp"
        topic.md                      ← topic for node "mycompany-io.superapp"
      features/
        .node/
          node.yaml                   ← metadata for node "mycompany-io.superapp.features"
          topic.md                    ← topic for node "mycompany-io.superapp.features"
      add-missing-imports/
        .node/
          node.yaml                   ← metadata for node "mycompany-io.superapp.add-missing-imports"
          topic.md                    ← instructions for this action node
```

This tree has the nodes:

- `mycompany-io` (domain)
- `mycompany-io.superapp` (project)
- `mycompany-io.superapp.features` (knowledge node)
- `mycompany-io.superapp.add-missing-imports` (action node)
