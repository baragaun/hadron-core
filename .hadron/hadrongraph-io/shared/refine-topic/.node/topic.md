# Task "refine-topic"

## Purpose

Refine an existing Hadron topic file to optimally serve its primary purpose: providing focused, concise context to LLMs.

## Arguments

```yaml
args:
    - name: path
      type: string
      description: the full path of the topic to refine (e.g., `mycompany.io.superapp.user-authentication`)
```

## Topics

- Read [hadron-node-path](../../hadron-node-path/.node/topic.md) to understand how to locate the node directory in the `.hadron` folder based on the `path` argument.

## Core Principles

Topics are designed for **LLM consumption first, human readability second**. When refining a topic, optimize for:

1. **Conciseness**: Remove verbose explanations; keep only essential information
2. **Focus**: Single topic/concept per file; remove tangential content
3. **Context efficiency**: Assume topic may be loaded with others; avoid redundancy
4. **Information density**: More facts, fewer words

## Steps

### 1. Read the current topic

Read both the `topic.md` and `node.yaml` files for the topic.

### 2. Evaluate against principles

Check if the topic:
- ✅ Has a clear single focus
- ✅ Uses concise language (no fluff)
- ✅ Provides necessary technical details
- ✅ Avoids duplicating information from related topics
- ✅ Removes mentions of unrelated concepts
- ❌ Contains excessive best practices (limit to 3-5 key points)
- ❌ Has overly detailed examples (pseudo-code is often sufficient)
- ❌ Includes conversational/tutorial language

### 3. Apply refinements

**Structure improvements:**
- Combine related sections if they're too granular
- Remove sections that don't serve LLM understanding
- Reorder sections for logical flow (concept → details → usage)

**Content improvements:**
- Replace verbose explanations with bullet points
- Convert long paragraphs to structured lists
- Use code snippets only where they clarify (prefer TypeScript interfaces over full implementations)
- Remove "Why?" explanations unless they're architecturally important
- Trim best practices to 3-5 essential points

**Language improvements:**
- Remove conversational phrases ("Let's explore...", "It's important to note...")
- Use technical precision over readability
- Prefer active voice ("Hook updates data" not "Data is updated by hooks")
- Remove hedging language ("usually", "typically", "in most cases") when possible

### 4. Verify metadata

Update `node.yaml` file if needed:
- Ensure `description` is concise (one sentence)
- Verify `tags` are relevant and minimal (3-7 tags)

### 5. Cross-reference check

Ensure topic doesn't duplicate content from related topics:
- If overlap exists, keep information in most relevant topic
- Add cross-references instead of duplicating content
- Example: Don't explain hooks in models topic; reference secureid-service-hooks topic

## Examples

**Before (verbose):**
```markdown
The User model is special because user information is denormalized
across many collections (ChannelParticipant, etc.). When a user's
name or avatar changes, these updates must propagate immediately
and synchronously to maintain data consistency.
```

**After (concise):**
```markdown
`User` model uses hooks because:
- User info denormalized across collections
- Updates must be synchronous for consistency
```

**Before (over-detailed best practices):**
```markdown
1. Use sparingly: Hooks are only for special cases like the User model
2. Keep hooks fast: They run synchronously and can block operations
3. Test thoroughly: Ensure denormalized data stays in sync across all affected collections
4. Document clearly: Make it obvious which fields are kept in sync via hooks
5. Consider performance: Hooks that update many documents can be slow
6. Monitor execution time: Add logging to track hook performance
7. Handle errors gracefully: Ensure hooks don't break the save operation
```

**After (focused):**
```markdown
- Use only for User model (or similar extension-point models)
- Keep hooks fast (they block operations)
- Test denormalization sync thoroughly
- Document which collections have denormalized userInfo
```

## Common Refinements

- **Remove**: Tutorial language, excessive examples, redundant explanations
- **Keep**: Technical details, architecture rationale, code structure
- **Simplify**: Best practices, trade-off discussions, conceptual overviews
- **Clarify**: Architecture relationships, extension points, special cases
