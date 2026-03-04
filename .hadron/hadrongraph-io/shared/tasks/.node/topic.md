# Tasks

You might be asked to perform tasks with the following format:

```
run-task <domain>.<project>[.<module>].<task>(<arg1>: <val1>, <arg2>: <val2>, ...)
```

You can then find instructions for this task in the node's `.node/` directory:
`.hadron/<domain>/<project>[/<module>]/<task>/.node/topic.md`.

The action arguments are defined in `.node/node.yaml` under `actions[0].args`.

The task instructions may contain a `Learn` section with references
to learning material. Read those before starting on the task.

**Example:**

```
run-task micromentor-org.mmdata.add-model-core-operations(serviceName: academy, modelName: AcyCertificate)
```

This `run-task` uses the instructions from `.hadron/micromentor-org/mmdata/add-model-core-operations/.node/topic.md`.

You may also see a chain of tasks in a prompt. Execute them in the order they are given.
