# Task "refine-task"

## Purpose

Refine an existing Hadron task file to optimally serve its primary purpose: providing clear, executable instructions to LLMs.

## Arguments

```yaml
args:
    - name: path
      type: string
      description: the full path of the task to refine (e.g., `mycompany.io.superapp.add-missing-imports`)
```

## Topics

- Read [hadron-node-path](../../hadron-node-path/.node/topic.md) to understand how to locate the node directory in the `.hadron` folder based on the `path` argument.

## Core Principles

Tasks are designed for **LLM execution**. When refining a task, optimize for:

1. **Clarity**: Steps are unambiguous and executable
2. **Conciseness**: No unnecessary explanations; reference topic files instead
3. **Specificity**: Concrete enough to execute; flexible enough for variations
4. **Structure**: Proper use of Purpose, Arguments, Prerequisites, Steps sections

## Steps

### 1. Read the current task

Read both the `topic.md` and `node.yaml` files for the task.

### 2. Evaluate against principles

Check if the task:
- ✅ Has a clear, one-sentence Purpose
- ✅ Uses numbered steps with descriptive titles
- ✅ References examples from actual code
- ✅ Defers to topic files for domain knowledge
- ✅ Specifies Prerequisites if task depends on prior work
- ❌ Contains inline explanations that belong in topic files
- ❌ Has overly rigid steps that don't allow for variations
- ❌ Includes too many conditional branches ("if X then Y, else Z")
- ❌ Duplicates information from other tasks

### 3. Apply refinements

**Purpose improvements:**
- Reduce to 1-2 sentences maximum
- Focus on what, not how (how is in Steps)
- Remove justifications (those belong in topic files)

**Arguments improvements:**
- Verify all referenced arguments are declared
- Use placeholder names consistently in steps
- Add descriptions that clarify expected format/value
- Use `(none)` if no arguments needed

**Prerequisites improvements:**
- Only include blockers (conditions that must be true)
- Move nice-to-have context to Purpose or Topics
- Be specific: "File X must exist" not "Project should be set up"

**Steps improvements:**
- Use `### N. Action verb` format (e.g., "### 1. Create the file")
- Reference code examples: "See X.ts for pattern" instead of showing full code
- Remove inline explanations; add topic references instead
- Break complex steps into sub-bullets
- Use `(none)` as step content only if truly no action needed

**Topics section improvements:**
- Add references to topic files instead of inline explanations
- Remove Topics section if task is self-contained
- Use descriptive link text: `[Model definitions](../../models/.node/topic.md)` not `[click here]`

### 4. Remove common anti-patterns

**Too much inline teaching:**
```markdown
<!-- BEFORE -->
## Steps
### 1. Create the service
Services in our codebase are organized under src/services/ and each
service has its own folder structure with types, operations, and tests...
```

```markdown
<!-- AFTER -->
## Topics
- [Service architecture](../../service/.node/topic.md)

## Steps
### 1. Create the service
Create `src/services/<serviceName>/` following service structure.
```

**Overly rigid steps:**
```markdown
<!-- BEFORE -->
### 1. Create the file
Create the file at src/services/myService/operations/myOp.ts with
exactly these imports: [long list]
```

```markdown
<!-- AFTER -->
### 1. Create the operation file
Create `src/services/<serviceName>/operations/<operationName>.ts`.
See existing operations in same service for import patterns.
```

**Hidden arguments:**
```markdown
<!-- BEFORE -->
### 1. Create the model
Create src/services/<serviceName>/types/models/<ModelName>.ts...
```

```markdown
<!-- AFTER -->
## Arguments
args:
    - name: modelName
      type: string
      description: PascalCase name of the model (e.g., "User")

## Steps
### 1. Create the model file
Create `src/services/<serviceName>/types/models/<modelName>.ts`...
```

### 5. Verify metadata

Update `node.yaml` file if needed:
- Ensure `description` matches refined Purpose
- Verify `args` under `actions[0].args` match Arguments section exactly
- Confirm tags are appropriate (e.g., `generate`, `clean`, `review`, `tool-call`)

### 6. Cross-reference check

Ensure task doesn't duplicate content from:
- Other tasks (combine or reference instead)
- Topic files (reference, don't inline)
- Code comments (reference files, don't copy)

## Examples

**Before (verbose):**
```markdown
## Purpose
This task helps you create a new model in the mmdata codebase. Models
are business entities that represent data stored in the database. They
are defined as TypeScript classes and have corresponding MongoDB schemas...
```

**After (concise):**
```markdown
## Purpose
Create a new model with TypeScript definition and MongoDB schema.

## Topics
- [Models](../../models/.node/topic.md)
```

**Before (too rigid):**
```markdown
### 1. Import dependencies
Add these exact imports at the top:
import { Field, ObjectType } from 'type-graphql'
import { IsOptional } from 'class-validator'
import { BaseModel } from '../../../models/types/classes/BaseModel.js'
```

**After (flexible):**
```markdown
### 1. Create model class
- Extend `BaseModel` from models service
- Add GraphQL decorators (`@ObjectType`, `@Field`)
- See existing models in service for import patterns
```

## Common Refinements

- **Remove**: Inline explanations, architecture discussions, over-specification
- **Keep**: Concrete steps, file paths, reference to examples
- **Simplify**: Conditional logic, error handling, edge cases
- **Clarify**: Argument usage, expected outcomes, reference locations
- **Reference**: Topic files for concepts, existing code for patterns
