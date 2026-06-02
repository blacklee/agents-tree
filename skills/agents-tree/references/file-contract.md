# Agents Tree File Contract

## Scope

This contract applies to `AGENTS.md` files maintained in a target project by the Agents Tree skill.

The skill repository is not the target knowledge tree. The target project owns the generated files.

## Front Matter

Every maintained `AGENTS.md` should begin with YAML front matter:

```yaml
---
knowledge_type: module
module: ExampleModule
last_verified_commit: abc123
critical_files:
  - src/example.ts
critical_symbols:
  - ExampleService
confidence: medium
owner: ai-generated
---
```

### Fields

- `knowledge_type`: `architecture`, `module`, `implementation`, or `workflow`.
- `module`: human-readable module or directory name.
- `last_verified_commit`: commit where the generated knowledge was last checked.
- `critical_files`: files whose changes may invalidate this knowledge.
- `critical_symbols`: functions, classes, types, modules, routes, jobs, or flows whose changes may invalidate this knowledge.
- `confidence`: `high`, `medium`, or `low`.
- `owner`: usually `ai-generated`; use `human-maintained` only when the whole file is intentionally human-owned.

## Managed Sections

Generated content must live inside:

```md
<!-- agents-tree:generated:start -->
...
<!-- agents-tree:generated:end -->
```

Human-maintained content must live inside:

```md
<!-- agents-tree:human:start -->
...
<!-- agents-tree:human:end -->
```

Agents may update generated sections after reviewing evidence. Agents must not rewrite human sections unless the user explicitly asks.

If generated knowledge conflicts with human text, report the conflict and ask for judgment.

## Recommended Sections

Root and module files should use a small subset of:

```md
# Module Overview
# Architecture
# Entry Points
# Common Tasks
# Rules
# Do Not
# Verification
```

Only include sections that contain useful information. Empty headings waste context.

## Freshness Review

Classify the file:

- `VALID`: recorded evidence still supports the generated claims.
- `STALE_WARNING`: related evidence changed, but claims appear mostly usable.
- `INVALID`: critical evidence changed enough that the file must not be trusted.

Use code-intelligence tools when available. If they are unavailable, use focused file reads and Git history. Do not infer validity from unchanged filenames alone.
