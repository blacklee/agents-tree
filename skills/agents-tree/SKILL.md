---
name: agents-tree
description: Use when creating, checking, refreshing, or reviewing directory-scoped AGENTS.md decision-compression trees in a target project; when maintaining project decision guidance, freshness metadata, generated sections, human-protected sections, or stale AGENTS.md guidance for coding agents.
---

# Agents Tree

## Purpose

Maintain a verified `AGENTS.md` decision-compression tree inside the target project.

This skill is only the maintenance workflow. The decision-compression tree belongs to the target repository, is committed there, and must remain useful without this skill installed.

Generated `AGENTS.md` files should compress future agent decisions, not summarize the directory. Their first job is to help the next agent decide what to inspect first, what to check with code-intelligence tools, which boundaries may be affected, which verification is relevant, and when this file can be skipped.

## Decision Compression Value

Create or refresh generated knowledge only when it reduces concrete future reasoning cost:

- first-hop reasoning: where the next agent should start for a known task shape
- code-intelligence targeting: which symbol, file, or flow to query first
- boundary reasoning: when a change must cross-check another module
- verification reasoning: which focused checks usually prove this area
- skip reasoning: when this `AGENTS.md` or subtree should not be read first

Use code graph, code-intelligence, structural search, or language-aware navigation for live facts. Use `AGENTS.md` for stable decision rules that route agents toward those facts. Do not store current caller lists, dependency inventories, consumer lists, or full API maps in generated sections unless the relationship is an intentionally stable contract.

Before creating a child `AGENTS.md`, state the specific decision cost it saves. If the answer is vague, do not create the file.

Every generated bullet must change the next action for a future agent: what to read, what to query, what boundary to check, what to skip, or how to choose verification. If a bullet does not change the next action, delete it.

`Skip This File When` must be concrete. Do not write vague skip rules such as "skip when the target is clear." Prefer "If the task is only X, do not read this file first; go to Y or query Z."

## Operating Modes

- **Create**: add a minimal `AGENTS.md` tree where directory-level guidance will reduce future decision branches.
- **Check**: inspect existing metadata and code evidence, then report `VALID`, `STALE_WARNING`, or `INVALID`.
- **Refresh**: update generated sections after reviewing current code evidence.
- **Review**: evaluate whether proposed knowledge changes are accurate, scoped, and safe for future agents.

If the user only asks to check, inspect, analyze, or review, do not edit files unless they explicitly approve edits.

## Mode Selection

- User asks to add missing guidance, start a tree, avoid repeated scans, or reduce task-routing decisions: **Create**.
- User asks whether current knowledge is trustworthy, stale, valid, or safe to use: **Check**.
- User asks to update, refresh, rewrite generated guidance, or record changed project decision guidance: **Refresh**.
- User asks to evaluate proposed `AGENTS.md` changes: **Review**.
- If wording is ambiguous, choose **Check** and recommend the next action instead of editing.

## Update Triggers

Consider updating the tree when work changes stable decision guidance that future agents need:

- edited files or symbols listed in `critical_files` or `critical_symbols`
- changed directory responsibility, entry points, call flows, ownership boundaries, or verification steps
- added, removed, renamed, or moved an important module directory
- discovered an existing `AGENTS.md` claim that conflicts with current code evidence
- repeatedly scanned the same directory because useful routing or skip guidance was missing
- user explicitly asked to update, refresh, or record project decision guidance

Do not update the tree for every code change. Update it only when the change affects durable guidance future agents should rely on.

## Workflow

1. Identify the target project and requested mode.
2. Read the nearest applicable `AGENTS.md` files first.
3. Validate ownership and section markers before editing.
4. Stop knowledge-tree maintenance for this file if `owner: human-maintained`, malformed markers, or an unresolved ancestor conflict blocks it.
5. If applicable project guidance declares a code graph or code-intelligence tool, **MUST** use it before creating, reviewing, or refreshing generated knowledge; use `grep`/`rg` only as supplementary evidence.
6. Decide whether the target directory needs an `AGENTS.md`; state the decision-compression reason before creating a child file.
7. Preserve parent/child separation: root files route agents; leaf files hold concrete local knowledge.
8. Write generated sections as routing rules: concrete skip rules, first-hop rules, cross-module checks, and verification-choice hints.
9. Update only managed generated sections unless the user explicitly asks to edit human sections.
10. If generated knowledge contradicts a human section, write or preserve an unresolved conflict block instead of overwriting either side.
11. Summarize touched files, evidence reviewed, freshness status, decision cost saved, and unresolved conflicts.

## Section Safety

- If an existing `AGENTS.md` has no managed markers, treat all existing body text as human-maintained. Preserve it unless the user explicitly asks to normalize it.
- During initial tree creation, unmarked existing body text may stay outside managed sections; preserve it byte-for-byte and add generated content in managed sections only.
- Require exactly one well-ordered generated section before refresh. Allow at most one well-ordered human section.
- In refresh mode, missing, duplicated, nested, or out-of-order markers make the file `INVALID`; do not edit generated content until repaired or explicitly normalized.
- Preserve unmanaged text outside managed sections as human-maintained content.
- `owner: human-maintained` blocks all edits to the file unless the user explicitly asks to edit that human-owned file.
- Before refreshing a child `AGENTS.md`, check applicable ancestors for unresolved conflict blocks that cover the target path.

## Cross-Module Handoff

Keep each `AGENTS.md` cohesive to its own directory. Do not maintain complete cross-module dependency maps in the tree.

When a task touches critical symbols, APIs, data shapes, call flows, ownership boundaries, or other cross-module seams:

1. Use a code graph or code-intelligence tool to find real callers, callees, references, and impact.
2. If the result points to another module, read that module's nearest `AGENTS.md` before editing across the boundary.
3. Record only stable handoff guidance, such as which code-intelligence target to inspect before changing an interface.
4. Do not copy live dependency lists, current callers, or consumer inventories into `AGENTS.md`; those belong in graph/search tools.

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

Use canonical conflict types from `references/file-contract.md`: `human_generated_mismatch`, `parent_child_mismatch`, `missing_critical_evidence`, or `scope_mismatch`.

Conflict blocks are an exception to normal module cohesion. Include the smallest cross-module context needed for a human to decide, such as impacted modules, relevant neighboring `AGENTS.md` claims, and code graph evidence. Keep ordinary generated sections cohesive and local.

Generated claims must be traceable to files, symbols, imports, execution flows, tests, or explicit human notes.

Before writing `critical_symbols`, verify each entry resolves to a real code symbol using declared code-intelligence tools or focused source evidence. Do not store route names, aliases, approximate labels, or task notes in metadata.

`critical_symbols` is not a function inventory. Include only durable entry points, boundary symbols, or symbols whose change would invalidate local guidance. Prefer about 8-15 symbols for a medium module unless there is a strong reason.

Use short `Evidence Notes` for generated files with multiple claims; name the evidence type used. Do not write live dependency lists.

Generated content should help future agents choose what to read or query next. Prefer task-routing rules, first-hop guidance, skip guidance, cross-module checks, and focused verification hints. Do not duplicate a full API index, method inventory, or dependency graph that search or code-intelligence tools can produce.

## Freshness Labels

- `VALID`: recorded evidence still supports the knowledge.
- `STALE_WARNING`: relevant evidence changed, but the main module shape appears intact.
- `INVALID`: critical files, symbols, ownership boundaries, or flows changed enough that agents must not trust the knowledge without rereading code.

Treat stale or invalid knowledge as worse than missing knowledge.

Decision checklist:

- Missing or malformed metadata or markers in a managed file: `INVALID`.
- Deleted, renamed, moved, or missing critical file or symbol: `INVALID` until re-evidenced; do not remove old metadata until the replacement evidence and scope are verified.
- Changed critical symbol signature, ownership boundary, entry point, scope, or execution flow: `INVALID`.
- Changed critical file with the same responsibility and key flows after focused review: `STALE_WARNING`.
- No relevant recorded evidence or current references/flows changed: `VALID`.
- Insufficient evidence: report `INVALID` or cannot verify; never report `VALID`.

Advance `last_verified_commit` only after checking every recorded critical file and symbol plus current diffs or graph evidence affecting generated claims.

After refresh, verify metadata consistency: every `critical_files` path exists, every `critical_symbols` entry resolves, generated claims fit the local scope, parent claims do not contradict this file, and `last_verified_commit` belongs to the target repository containing this `AGENTS.md`.

In Review mode, include a token-saving value rating: `HIGH`, `MEDIUM`, or `LOW`.

## Templates And Details

- Use `assets/root.AGENTS.md`, `assets/module.AGENTS.md`, or `assets/leaf.AGENTS.md` as starting templates.
- Read `references/file-contract.md` when exact metadata, section, and freshness rules matter.
- Read `references/maintenance-workflow.md` when deciding where to add files or how to handle conflicts.

Keep generated `AGENTS.md` files short. Do not turn them into project encyclopedias.
