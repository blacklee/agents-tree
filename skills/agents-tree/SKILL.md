---
name: agents-tree
description: Use when creating, checking, refreshing, or reviewing directory-scoped AGENTS.md-compatible decision guidance in a target project; when maintaining native AGENTS.md guidance, optional sidecar decision-router guidance, freshness metadata, generated sections, human-protected sections, or stale guidance for coding agents.
---

# Agents Tree

## Purpose

Maintain verified AGENTS.md-compatible decision guidance inside the target project.

This skill is the maintenance workflow, not the target guidance itself. Maintained guidance belongs to the target repository, is committed there, and must remain useful without this skill installed.

Generated guidance files should compress future agent decisions, not summarize the directory. Their first job is to help the next agent decide which child module owns a task, what to inspect first, what to check with code-intelligence tools, which boundaries may be affected, which verification is relevant, and when this file can be skipped.

## Decision Compression Value

Create or refresh generated guidance only when it reduces concrete future reasoning cost:

- first-hop reasoning: where the next agent should start for a known task shape
- module-responsibility reasoning: which child subtree owns a task shape
- current-design-intent reasoning: which current boundary or rule should guide the next action
- code-intelligence targeting: which symbol, file, or flow to query first
- boundary reasoning: when a change must cross-check another module
- verification reasoning: which focused checks usually prove this area
- skip reasoning: when this guidance file or subtree should not be read first

Use code graph, code-intelligence, structural search, or language-aware navigation for live facts. Use maintained guidance files for stable decision rules that route agents toward those facts. Do not store current caller lists, dependency inventories, consumer lists, or full API maps in generated sections unless the relationship is an intentionally stable contract.

Before creating a child guidance file, state the specific decision cost it saves. If the answer is vague, do not create the file.

Prefer parent guidance for comparing child module responsibilities. Parent files may include short child responsibility maps only when the map routes agents to the correct child subtree or tells them which child subtree to skip first. Child files should not repeat the parent map unless a local override is explicit and useful.

If current design intent is too ambiguous to support a routing rule, boundary check, or verification hint, ask the user to clarify it instead of generating vague guidance.

Every generated bullet must change the next action for a future agent: what to read, what to query, what boundary to check, what to skip, or how to choose verification. If a bullet does not change the next action, delete it.

`Skip This File When` must be concrete. Do not write vague skip rules such as "skip when the target is clear." Prefer "If the task is only X, do not read this file first; go to Y or query Z."

## Artifact Strategy

- Use native `AGENTS.md` mode by default.
- Use sidecar mode only when the target project explicitly wants decision guidance separate from agent behavior instructions.
- In sidecar mode, prefer one consistent file name such as `decision-router.md` and require a short root `AGENTS.md` pointer to applicable sidecar files.
- Adding the sidecar pointer to an existing human-owned, strict, or unmarked root `AGENTS.md` is a separate explicit edit; sidecar selection alone is not permission to change that file.
- Do not maintain native and sidecar guidance for overlapping generated guidance in the same covered subtree unless the user explicitly asks to migrate, split, or resolve overlap.
- Read `references/file-contract.md` for exact sidecar contract details.

## Operating Modes

- **Create**: add minimal directory-level guidance where it will reduce future decision branches.
- **Check**: inspect existing metadata and code evidence, then report `VALID`, `STALE_WARNING`, or `INVALID`.
- **Refresh**: update generated sections after reviewing current code evidence.
- **Review**: evaluate whether proposed guidance changes are accurate, scoped, and safe for future agents.

If the user only asks to check, inspect, analyze, or review, do not edit files unless they explicitly approve edits.

## Mode Selection

- User asks to add missing guidance, start a tree, avoid repeated scans, or reduce task-routing decisions: **Create**.
- User asks whether current guidance is trustworthy, stale, valid, or safe to use: **Check**.
- User asks to update, refresh, rewrite generated guidance, or record changed project decision guidance: **Refresh**.
- User asks to evaluate proposed guidance changes: **Review**.
- If wording is ambiguous, choose **Check** and recommend the next action instead of editing.

## Update Triggers

Consider updating guidance when work changes stable decision guidance that future agents need:

- edited files or symbols listed in `critical_files` or `critical_symbols`
- changed directory responsibility, entry points, call flows, ownership boundaries, or verification steps
- added, removed, renamed, or moved an important module directory
- discovered an existing guidance claim that conflicts with current code evidence
- repeatedly scanned the same directory because useful routing or skip guidance was missing
- user explicitly asked to update, refresh, or record project decision guidance

Do not update guidance for every code change. Update it only when the change affects durable guidance future agents should rely on.

## Workflow

1. Identify the target project and requested mode.
2. Read the nearest applicable `AGENTS.md` files first; if sidecar mode is selected, also read the applicable sidecar guidance files they point to.
3. Validate ownership and section markers before editing.
4. Stop guidance maintenance for this file if `owner: human-maintained`, malformed markers, or an unresolved ancestor conflict blocks it.
5. If applicable project guidance declares a code graph or code-intelligence tool, **MUST** attempt it before creating, reviewing, or refreshing generated guidance; if it is unavailable, stale, or partial, follow the bounded no-tool sequence in `maintenance-workflow.md` and classify conservatively.
6. Choose artifact strategy: native `AGENTS.md` by default; sidecar only with an explicit project reason, an owner repository root, and a discoverability pointer that is safe to edit or explicitly approved.
7. Decide whether the target directory needs a guidance file; state the decision-compression reason before creating a child file.
8. If the target directory contains both human-facing and agent-facing docs, run an Audience Boundary Review unless unchanged metadata says it was already suggested, dismissed, or resolved.
9. Preserve parent/child separation: parent files route agents and compare child responsibilities; leaf files hold concrete local guidance.
10. Write generated sections as routing rules: concrete skip rules, first-hop rules, cross-module checks, and verification-choice hints.
11. Update only managed generated sections unless the user explicitly asks to edit human sections.
12. If generated guidance contradicts a human section, write or preserve an unresolved conflict block instead of overwriting either side.
13. Summarize touched files, artifact strategy, evidence reviewed, freshness status, decision cost saved, audience-boundary suggestions, and unresolved conflicts.

## Section Safety

- If an existing guidance artifact has no managed markers, treat all existing body text as human-maintained.
- During initial creation, preserve unmarked existing body text byte-for-byte unless the user explicitly asks to normalize it.
- Require valid managed markers before refresh. Missing, duplicated, nested, or out-of-order markers make the file `INVALID`; do not edit generated content until repaired or explicitly normalized.
- Preserve unmanaged text outside managed sections as human-maintained content.
- `owner: human-maintained` blocks all edits to the file unless the user explicitly asks to edit that human-owned file.
- Before refreshing a child guidance file, check applicable ancestors for unresolved or malformed conflict blocks that cover the target path.
- Do not add Agents Tree YAML front matter to human-facing docs such as `README.md` or tool-specific agent docs such as `CLAUDE.md` / `agents/claude.md`. Agents Tree metadata belongs only in maintained decision-guidance artifacts: native `AGENTS.md` files, or explicit sidecar files such as `decision-router.md` when sidecar mode is selected.

## Audience Boundary Review

When human-facing docs such as `README.md` and agent-facing docs such as `AGENTS.md`, `CLAUDE.md`, or `agents/claude.md` coexist, report likely audience-boundary issues as suggestions only.

Do not move or rewrite audience-mismatched content by default. Avoid repeated suggestions by checking `audience_boundary_review` in the nearest managed guidance artifact. Read `references/maintenance-workflow.md` for the exact repeat-suppression rules.

## Cross-Module Handoff

Keep each guidance artifact cohesive to its own directory. Do not maintain complete cross-module dependency maps in the tree.

When a task touches critical symbols, APIs, data shapes, call flows, ownership boundaries, or other cross-module seams:

1. Use a code graph or code-intelligence tool to find real callers, callees, references, and impact.
2. If the result points to another module, read that module's nearest applicable guidance artifact before editing across the boundary.
3. Record only stable handoff guidance, such as which code-intelligence target to inspect before changing an interface.
4. Do not copy live dependency lists, current callers, or consumer inventories into guidance; those belong in graph/search tools.

## File Contract Pointers

Maintained guidance artifacts use YAML front matter plus managed generated/human sections. Exact metadata fields, marker validation, conflict blocks, sidecar contract rules, and freshness classification live in `references/file-contract.md`.

Before writing or refreshing generated content:

- verify `critical_files` paths exist and `critical_symbols` resolve to real symbols
- check recorded critical evidence even when it matches `agents_tree_skip`; skip rules cannot hide freshness evidence
- ensure generated claims are traceable to files, symbols, imports, execution flows, tests, or explicit human notes
- request clarification when current design intent is ambiguous and would affect generated guidance
- replace or delete template placeholders before treating a guidance artifact as valid
- treat stale or invalid guidance as worse than missing guidance
- advance `last_verified_commit` only after recorded evidence and relevant current diffs or graph evidence have been checked against committed code state

In Review mode, compare proposed diffs against the previous file when a diff is available, especially for human-section or unmanaged-text deletion, then include a token-saving value rating: `HIGH`, `MEDIUM`, or `LOW`.

## Templates And Details

- Use `assets/root.AGENTS.md`, `assets/module.AGENTS.md`, or `assets/leaf.AGENTS.md` as starting templates.
- Read `references/file-contract.md` when exact metadata, section, and freshness rules matter.
- Read `references/maintenance-workflow.md` when deciding where to add files or how to handle conflicts.

Keep generated guidance files short. Do not turn them into project encyclopedias.
