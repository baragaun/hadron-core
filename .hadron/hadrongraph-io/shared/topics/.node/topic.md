# Topics

Hadron uses "atomic" topic files that contain information on a single topic.

Each node is a directory containing a `.node/` folder with `node.yaml` (metadata) and
`topic.md` (content), located within the `.hadron/` folder hierarchy. The top level
directories represent the `domain`, which may map to an organization or project. If
organization, the next level usually maps to a project.

Each project has a directory tree that loosely maps to the module and submodule
structure of the source tree.

`.hadron/<domain>/<project>/[<module>/]<node-name>/.node/topic.md`.

**Examples:**
- `.hadron/micromentor-org/mmdata/services/channels/.node/topic.md`
- `.hadron/baragaun-com/bg-code-gen/code-generation/.node/topic.md`
- `.hadron/hadrongraph-io/shared/what-is-hadron/.node/topic.md`

Each file contains this:

```
# <Topic Title>

[## Terminology]
<Terms used in this topic>

<content>
```

We try to share only the context needed for a single task or chain of tasks to save tokens.
