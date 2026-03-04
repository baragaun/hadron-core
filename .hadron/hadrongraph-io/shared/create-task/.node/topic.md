# Task "create-task"

## Purpose

Create a new Hadron task node in the folder `.hadron`.

## Arguments

```yaml
args:
  - name: path
    type: string
    description: the name of the task (e.g., `mycompany.io.superapp.add-missing-imports`)
  - name: task_type
    type: string
    description: the type of the task (e.g., `clean`, `generate`, `review`)
```

## Topics

- Read [hadron-node-path](../../hadron-node-path/.node/topic.md) to understand how to determine the
  location of the node directory in the `.hadron` folder based on the `path` argument.

## When to Create a Task

The best tasks are written **after** the work has been done at least once with AI assistance.
The process:

1. Work with AI on a real coding task — assign work, review output, correct mistakes, explain why.
2. Once the work is correct and committed, ask AI to write a task file based on what it just did.

Tasks created this way capture **distilled experience** — not just what to do, but the
nuances learned through actually doing it. A task written speculatively, before the work has
been done, tends to miss the pitfalls and edge cases that only surface during execution.

If you must write a task before doing the work, treat it as a draft. Run it, observe where
the AI stumbles, and refine the task based on what you learn.

## Steps

### 1. Create the node directory

Create `.hadron/<folder(s)>/<task-name>/.node/topic.md`.

### 2. Write the topic.md file using this template

```markdown
# Task "<task-name>"

## Purpose

<One or two sentences describing what this task accomplishes.>

## Topics

- [<topic-description>](<relative-path-to-topic.md>)

<Optional inline **Terminology** block for task-specific terms.>

## Arguments

\`\`\`yaml
args:
- name: <arg-name-1>
type: string
description: <Description of the argument>
- name: <arg-name-2>
type: string
description: <Description of the argument>
\`\`\`

## Prerequisites

<Optional. Conditions that must be true before starting. If any are not met, stop and alert the
user.>

## Steps

<Numbered steps describing what to do. Use `### N. Step title` for major steps.
Reference argument values using their placeholder names (e.g., `sampleService`).>

## Pitfalls

<Optional. Common mistakes the AI is likely to make when executing this task.
Frame as warnings: "Do not X" or "Y might seem right but Z is the correct approach."
These should come from actual experience — real mistakes observed during task execution.>
```

### 3. Guidelines

- **Purpose** should be concise — just enough to understand the goal.
- **Topics**: Include when the task depends on domain knowledge from a topic file. If you find
  yourself writing an explanation inside a step, that explanation probably belongs in a topic
  file instead. Extract it and reference it.
- **Arguments**: Required. Use `(none)` for tasks without arguments.
- **Prerequisites**: Optional. Include it when the task depends on prior work.
- **Steps** should be specific enough that an LLM can execute them without guessing, but not so
  rigid that they break on minor variations. Reference existing code as examples where possible.
- **Pitfalls**: Optional but recommended. Document the mistakes the AI actually made when the
  task was first developed. These are the most valuable part of a task — they prevent future
  AI agents from repeating the same errors.

### 4. Common Pitfalls in Task Writing

These are mistakes frequently made when writing task files:

**Inlining context that belongs in a topic.** If a step requires explaining *why* something
is done a certain way, that explanation should be a topic file. Tasks say *what* to do;
topics explain *why*. When context is inlined, it can't be reused by other tasks, and it
won't be updated when the codebase changes.

**Over-specifying code.** Listing exact imports, exact file contents, or exact code blocks
makes the task brittle. When the codebase evolves, the task breaks. Instead, reference
existing code as examples: "Follow the pattern in `<existing-file>`."

**Under-specifying intent.** The opposite problem: steps so vague that the AI has to guess.
"Set up the service" is too vague. "Create `src/services/<serviceName>/index.ts` exporting
the service class, following the pattern in the `users` service" is specific enough.

**Writing tasks speculatively.** A task written before doing the work lacks the corrections
and refinements that come from actual execution. If you haven't done the work yet, do it
with AI first, then write the task.

**Missing the pitfalls section.** The places where AI stumbles are the most valuable
knowledge. If you corrected the AI during the initial work, those corrections should
become pitfalls in the task. Without them, every future execution risks the same mistakes.

**Assuming context the AI won't have.** The AI starts each session fresh. It doesn't
remember previous runs. Any knowledge it needs must be provided via topic files or
explained in the task steps. If a step depends on understanding a codebase convention,
reference the topic that explains it.

### 5. Shareability

Task files may be shared across teams, organizations, and projects. Keep tasks
**self-contained and portable**:

- Reference topics by relative path for project-specific knowledge.
- Do not hardcode paths, names, or conventions that are specific to one codebase.
  Use arguments for anything that varies between projects.
- A well-written task with good topic references can be dropped into any `.hadron/`
  folder and work — provided the referenced topics exist or are shipped alongside it.

### 6. Create the metadata file

Create `.hadron/<folder(s)>/<task-name>/.node/node.yaml` with the following content:

```yaml
name: <task-name>
description: <Brief description of the task>

tags:
  - <task_type>

actions:
  - name: run
    description: <Brief description of the action>
    args:
      - name: <arg-name-1>
        type: string
        description: <Description of the argument>
      - name: <arg-name-2>
        type: string
        description: <Description of the argument>
```

**Notes:**

- `name` should be just the node directory name (e.g., `add-model-core-operations`,
  `sort-js-imports`)
- The full reference path is derived from the filesystem location
- The former `task_type` is now a tag (e.g., `generate`, `clean`, `review`, `tool-call`)
- `args` live under `actions[0].args`
