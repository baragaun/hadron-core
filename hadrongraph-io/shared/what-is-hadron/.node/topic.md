# What Is Hadron?

Hadron empowers developers to use AI agents on complex, large codebases.

**Features**

- Analyzes large scale codebases
- Breaks down complex tasks into smaller steps
- Provides detailed instructions for AI agents to perform tasks
- Controls fine-grained context for fast launch at the start of a session and to minimize token
  usage
- Builds and manages project specific best-practice coding
- Takes over grunt work and automates boilerplate code generation with precision and consistency

## How Does Hadron Code Work?

Instead of building one massive context for an entire codebase, Hadron breaks this into
single-topic chapters that are maintained for the project, but can also be shared with other
projects. One or more topics can now be supplied to the AI agent when performing predefined tasks.
Tasks are as focused and specific as possible, and can then be chained for more complex work.

All topics and tasks are saved with the project's sources in a `.hadron` folder. This way they can
be versioned, updated and shared by the engineering team, with or without the Hadron application.

## Usage

At the start of the AI chat, or when the AI agent has been initialized, give it the following instructions:

```
Read `.hadron/instructions.md`
```

Now you are ready to ask the agent to perform various tasks. You can chain them, e.g.

```shell
run-task bg-code-gen.create-input-modeldef(serviceName: academy, modelName: AcyCertificate)
run-task mmdata.add-model-core-operations(serviceName: academy, modelName: AcyCertificate)
run-task mmdata.add-missing-js-imports(src/services/academy/types/models/AcyCertificateInput.ts)
run-task mmdata.sort-js-imports
```

## Adding New Tasks

Please help us to build a solid repository of atomic "task" and "learn" files.

Here are some strategies:

### Creating a new task with AI's help

1. Work with AI to write new code.
2. When done, ask AI to write a new task that would generate the new code. For this, use this task:

```shell
run-task hadrongraph-io.shared.create-task(path: "my-company.my-project.my-new-task", task_type: "generate")
```

This should create a new node with:

- `.hadron/<domain>/<project>/<task>/.node/topic.md` - the instructions for the task
- `.hadron/<domain>/<project>/<task>/.node/node.yaml` - the metadata for the task (e.g., arguments)

3. Verify that the instructions in the new task are indeed correct and complete.
4. If you think there is more context needed, add topic nodes and reference them in the task.

### Review

1. Ask AI run a task.
2. Then use this prompt:

```
Improve the task description in the task's topic.md file from your findings having run it just now.
```

3. Review the changes in the task file and correct as needed.
