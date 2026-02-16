# Hadron

Hadron is a knowledge management system that organizes project context into topic
files and task files, stored in a `.hadron/` folder. These files form a knowledge
graph that AI agents traverse to understand codebases and execute tasks accurately.

Instead of building one massive context for an entire codebase, Hadron breaks it into
single-topic files that are maintained per project but can be shared across projects.
One or more topics are supplied to an AI agent when performing predefined tasks.
Tasks are focused and specific, and can be chained for more complex work.

## Key Concepts

- **Topics**: Atomic context files describing a single concept, pattern, or component.
  See [topics](../shared/topics/.node/topic.md).
- **Tasks**: Nodes with actions that tell an AI agent how to perform a specific piece of work.
  See [tasks](../shared/tasks/.node/topic.md).
- **Hadron folder**: The `.hadron/` directory tree that organizes nodes by
  domain, project, and module. See [hadron-folder](../shared/hadron-folder/.node/topic.md).
- **Topic graph**: Nodes reference each other via metadata, forming a navigable knowledge
  graph. See [topic-graph](../shared/topic-graph/.node/topic.md).
