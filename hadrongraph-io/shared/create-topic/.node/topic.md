# Task "create-topic"

## Purpose

Create a new Hadron topic node in the folder `.hadron`.

## Arguments

```yaml
args:
  - name: path
    type: string
    description: the full path of the topic (e.g., `mycompany.io.superapp.user-authentication`)
```

## Topics

- Read [hadron-node-path](../../hadron-node-path/.node/topic.md) to understand how to determine the
  location of the node directory in the `.hadron` folder based on the `path` argument.
- Read [topic-graph](../../topic-graph/.node/topic.md) to understand how topics relate to each other
  as nodes in a knowledge graph.

## When to Create a Topic

The best topics are written **after** the AI has done real work on the codebase and been
corrected by a senior engineer. The process:

1. Work with AI on a coding task — assign work, review output, correct mistakes, explain why.
2. Once the work is correct and committed, ask AI to write a topic file capturing what it
   just learned about the concept, pattern, or convention involved.

Topics created this way capture **distilled experience** — not just what the code does, but
why it's structured that way, what the pitfalls are, and what patterns to follow. This is
fundamentally different from auto-generated documentation, which describes code without
understanding the decisions behind it.

A topic written by an AI that has struggled with the code, made mistakes, and received
corrections will be more accurate and more useful than one written from a cold reading of
the source files.

## Steps

### 1. Create the node directory

Create `.hadron/<folder(s)>/<topic-name>/.node/topic.md`.

**Directory nodes**: Every directory that is a node should have a `.node/` folder
(e.g., `services/.node/node.yaml`, `baragaun-com/.node/node.yaml`). This node
represents the directory itself and describes what it contains.

### 2. Write the topic.md file using this template

```markdown
# <Topic Title>

<Brief introduction — what this topic covers and why it matters.>

## <Section 1>

<Content explaining the first aspect of the topic.>

## <Section 2>

<Content explaining the second aspect of the topic.>

<Additional sections as needed.>

## Pitfalls

<Optional. Common mistakes or misconceptions related to this topic.
These should come from real experience — actual errors observed during development.>
```

Topic files are flexible and should contain whatever information is needed to understand
the topic. Common patterns:

- **Concept explanations**: What something is and how it works in this codebase
  (e.g., `service`, `what-is-hadron`)
- **Pattern guides**: How to implement something, with conventions and code examples
  (e.g., `channel-creation-flow`)
- **Terminology lists**: Simple bullet lists defining terms (e.g., `terminology`)
- **Constraint descriptions**: Rules and conventions that must be followed
  (e.g., "all enums live in `/types/enums`", "services must extend BaseService")

### 3. Guidelines

**Scope: one concept per topic.** A topic should cover a single concept, pattern, or
convention. If you find a topic growing to cover multiple distinct ideas, split it. A
topic about "models" should not also explain the service architecture — that's a separate
topic that can be linked via the `nodes` field in the metadata.

**Explain the why, not just the what.** Code itself shows *what* is done. Topics should
explain *why* — the design decisions, the constraints, the reasons a pattern was chosen
over alternatives. This is the knowledge that lives in engineers' heads and gets lost
when they leave the team.

**Include pitfalls.** The places where engineers (human or AI) stumble are the most
valuable knowledge in a topic. If there's a pattern that looks like it should work one
way but actually needs to work another way, say so explicitly. Frame these as warnings.

**Reference, don't duplicate.** When a topic relates to another topic, link to it via
the `nodes` field in the metadata (with a `rel` explaining the relationship) rather than
repeating its content. This keeps topics small, avoids drift between copies, and lets the
knowledge graph work as intended.

**Link from lower to higher.** When connecting topics, place the `nodes` reference on the
more specific (lower-level) topic, pointing up to the broader (higher-level) topic. For
example, `mm2-channel-lock` links to `channels`, not the other way around. This keeps
higher-level topics stable — they don't need updating when subtopics are added, moved, or
deleted. Hadron resolves links bidirectionally, so when reading a higher-level topic, the
AI is shown all lower-level topics that reference it as "more detailed topics available."
This lets the AI drill down when it needs deeper context.

**Keep it current.** A stale topic is worse than no topic — it will actively mislead the
AI. When you notice that code has changed in a way that contradicts a topic, update the
topic immediately.

**Write for AI consumption, but make it human-readable.** Topics are primarily consumed
by AI agents during task execution, but they should also make sense to a human engineer
browsing the knowledge base. Use clear headings, short paragraphs, and code examples
where they clarify the concept.

### 4. Common Pitfalls in Topic Writing

**Writing too broadly.** A topic called "backend architecture" that tries to explain
everything will be too long for efficient AI consumption and too vague to be actionable.
Break it into focused topics: "service structure", "middleware patterns",
"database conventions".

**Writing too shallowly.** A topic that merely states "we use MongoDB" without explaining
the conventions — how schemas are defined, where models live, which patterns to follow —
doesn't provide enough context for an AI to do correct work.

**Describing the code instead of the knowledge.** The AI can read the code. What it can't
read is the reasoning behind the code. "The `User` model has a `status` field" is
redundant — the AI can see that. "The `status` field uses a string enum rather than a
numeric enum because the values appear in API responses and must be human-readable" is
knowledge that prevents mistakes.

**Omitting the pitfalls.** If the AI made mistakes during the work that led to this
topic, those mistakes are precisely what the topic should warn about. They're the most
likely mistakes future AI agents will also make.

**Hardcoding specifics that will change.** File paths, exact import lists, and specific
code snippets make topics brittle. Prefer describing patterns and referencing existing
code as examples: "See `src/services/users/` for the canonical service structure."

### 5. Shareability

Topic files may be shared across teams, organizations, and projects. The domain-namespaced
folder structure (e.g., `.hadron/acme.com/`, `.hadron/framework.io/`) ensures that
knowledge graphs from different sources can merge without conflicts.

When writing a topic that could be useful beyond your team:

- Keep project-specific details in project-scoped topics; put reusable patterns in
  the `shared/` folder.
- Use relative paths for cross-references within the same domain.
- A well-written topic about a framework pattern or common convention can be shipped
  to other teams and dropped into their `.hadron/` folder as-is.

### 6. Create the metadata file

Create `.hadron/<folder(s)>/<topic-name>/.node/node.yaml` with the following content:

```yaml
name: <topic-name>
description: <Brief description of the topic>

tags:
  - <tag1>
  - <tag2>

nodes:
  - loc: <dotted-path-to-related-node>
    rel: <Why consuming this related topic is useful>
```

**Notes:**

- `name` should be just the node directory name, in kebab-case (e.g.,
  `service`, `models`, `what-is-hadron`)
- The full reference path (`loc`) is derived from the filesystem location using dots
  as separators (e.g., `baragaun-com.channels-service.channels`)
- Hierarchy comes from directory placement — broader topics live closer to the domain
  root, detailed topics live deeper in the tree (see [topic-graph](../../topic-graph/.node/topic.md))
- `tags` are optional but helpful for categorization and search
- `nodes` lists connections to other nodes; each entry has a `loc` (dotted path to the
  target node) and `rel` (why the relationship exists — the reason to also consume it)
