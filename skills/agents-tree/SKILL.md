---
name: agents-tree
description: Use when creating, checking, refreshing, or reviewing directory-scoped AGENTS.md knowledge trees in a target project; when maintaining project knowledge, freshness metadata, generated sections, human-protected sections, or stale AGENTS.md guidance for coding agents.
---

# Agents Tree

## Purpose

Maintain a verified `AGENTS.md` knowledge tree inside the target project.

This skill is only the maintenance workflow. The knowledge tree belongs to the target repository, is committed there, and must remain useful without this skill installed.

## Operating Modes

- **Create**: add a minimal `AGENTS.md` tree where directory-level guidance will reduce repeated agent reasoning.
- **Check**: inspect existing metadata and code evidence, then report `VALID`, `STALE_WARNING`, or `INVALID`.
- **Refresh**: update generated sections after reviewing current code evidence.
- **Review**: evaluate whether proposed knowledge changes are accurate, scoped, and safe for future agents.

If the user only asks to check, inspect, analyze, or review, do not edit files unless they explicitly approve edits.

## Update Triggers

Consider updating the tree when work changes stable knowledge that future agents need:

- edited files or symbols listed in `critical_files` or `critical_symbols`
- changed directory responsibility, entry points, call flows, ownership boundaries, or verification steps
- added, removed, renamed, or moved an important module directory
- discovered an existing `AGENTS.md` claim that conflicts with current code evidence
- repeatedly scanned the same directory because useful local guidance was missing
- user explicitly asked to update, refresh, or record project knowledge

Do not update the tree for every code change. Update it only when the change affects durable guidance future agents should rely on.

## Workflow

1. Identify the target project and requested mode.
2. Read the nearest applicable `AGENTS.md` files first.
3. Use available code graph, code-intelligence, Git diff, language, or focused source-read tools to gather only the needed evidence.
4. Decide whether the target directory needs an `AGENTS.md`; do not create files just because a directory exists.
5. Preserve parent/child separation: root files route agents; leaf files hold concrete local knowledge.
6. Update only managed generated sections unless the user explicitly asks to edit human sections.
7. If generated knowledge contradicts a human section, write or preserve an unresolved conflict block instead of overwriting either side.
8. Summarize touched files, evidence reviewed, freshness status, and unresolved conflicts.

## Cross-Module Handoff

Keep each `AGENTS.md` cohesive to its own directory. Do not maintain complete cross-module dependency maps in the tree.

When a task touches critical symbols, APIs, data shapes, call flows, ownership boundaries, or other cross-module seams:

1. Use a code graph or code-intelligence tool to find real callers, callees, references, and impact.
2. If the result points to another module, read that module's nearest `AGENTS.md` before editing across the boundary.
3. Record only stable handoff guidance, such as when to inspect references before changing an interface.
4. Do not copy live dependency lists into `AGENTS.md`; those belong in graph/search tools.

## File Contract

Use YAML front matter plus managed sections.

Required metadata:

```yaml
knowledge_type: module
module: ExampleModule
last_verified_commit: abc123
critical_files: []
critical_symbols: []
confidence: medium
owner: ai-generated
```

Managed sections:

```md
<!-- agents-tree:generated:start -->
<!-- agents-tree:generated:end -->

<!-- agents-tree:human:start -->
<!-- agents-tree:human:end -->
```

Unresolved conflict blocks:

```md
<!-- agents-tree:conflict:start -->
status: unresolved
# Unresolved Agents Tree Conflict

This `AGENTS.md` file contains unresolved project-knowledge conflict.

Do not rely on this file as authoritative guidance for this directory or its subtree until the conflict is resolved.
<!-- agents-tree:conflict:end -->
```

If an unresolved conflict block exists, do not refresh that `AGENTS.md` file or its subtree unless the user explicitly asks to resolve the conflict.

Generated claims must be traceable to files, symbols, imports, execution flows, tests, or explicit human notes.

## Freshness Labels

- `VALID`: recorded evidence still supports the knowledge.
- `STALE_WARNING`: relevant evidence changed, but the main module shape appears intact.
- `INVALID`: critical files, symbols, ownership boundaries, or flows changed enough that agents must not trust the knowledge without rereading code.

Treat stale or invalid knowledge as worse than missing knowledge.

## Templates And Details

- Use `assets/root.AGENTS.md`, `assets/module.AGENTS.md`, or `assets/leaf.AGENTS.md` as starting templates.
- Read `references/file-contract.md` when exact metadata, section, and freshness rules matter.
- Read `references/maintenance-workflow.md` when deciding where to add files or how to handle conflicts.

Keep generated `AGENTS.md` files short. Do not turn them into project encyclopedias.
