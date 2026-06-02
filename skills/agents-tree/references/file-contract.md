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
agents_tree_keep: []
agents_tree_skip: []
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
- `agents_tree_keep`: short optional glob list for paths that should be considered even if ignore files would normally exclude them.
- `agents_tree_skip`: short optional glob list for extra paths to skip after applying project ignore files.

Keep `agents_tree_keep` and `agents_tree_skip` very small. Prefer existing `.gitignore`, `.ignore`, `.agentignore`, `.cursorignore`, or tool-specific ignore files for normal exclusions.

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

## Conflict Sections

If generated knowledge conflicts with human text, record the conflict in the file instead of overwriting either side:

```md
<!-- agents-tree:conflict:start -->
status: unresolved
detected_at_commit: abc123
detected_by: agent
conflict_type: human_generated_mismatch
related_files:
  - src/example.ts

# Unresolved Agents Tree Conflict

This `AGENTS.md` file contains unresolved project-knowledge conflict.

Do not rely on this file as authoritative guidance for this directory or its subtree until the conflict is resolved.

Human action required:

- review the human-maintained section
- review the generated section
- update one or both sides
- remove this conflict block, or mark it resolved with a short note

## Human Claim

Summarize the human-maintained claim.

## Code Evidence

Summarize the current code evidence.

## Required Resolution

Ask a human to update the human section, update the generated section, or explain why both can coexist.
<!-- agents-tree:conflict:end -->
```

An unresolved conflict blocks knowledge-tree maintenance for that `AGENTS.md` file and its subtree. It does not block unrelated code work or unrelated tree nodes.

After a human resolves the conflict, remove the conflict block or change `status` to `resolved` with a short resolution note.

Conflict blocks must be self-explanatory. Humans and agents that have not installed Agents Tree should still understand that the local `AGENTS.md` guidance is not authoritative until the conflict is resolved.

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
