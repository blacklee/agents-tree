# Agents Tree Project Instructions

## Project Purpose

This project builds a skill for maintaining verified `AGENTS.md` decision-compression trees in other projects.

The product should help agents reduce repeated task-routing and architecture reasoning by teaching them how to maintain directory-scoped decision guidance with freshness metadata.

The Agents Tree skill is not the target decision-compression tree. The skill is the reusable maintenance workflow. The `AGENTS.md` tree belongs to the target repository where the skill is applied.

Do not frame this project as a generic memory system. Its core identity is:

```text
Verified AGENTS Tree
```

## Current Stage

The repository is in the early design stage.

Before adding implementation code or automation, preserve the product contract described in `README.md`:

- a reusable skill for maintaining directory-scoped `AGENTS.md` files in target repositories
- metadata-backed freshness checks
- generated and human-maintained section boundaries
- compatibility with existing `AGENTS.md`-aware agents
- optional integration with code-intelligence tools
- generated content focused on task routing, first-hop rules, skip guidance, boundary checks, and verification hints
- advisory audience-boundary checks between human-facing docs and agent-facing docs, without turning this into a general README maintenance tool

## Architecture Direction

Prefer a skill-and-file-contract-first architecture.

The project must remain useful without a dedicated CLI. The primary workflow is:

- humans ask agents using this skill to create, check, or refresh the target project's tree
- agents inspect only the needed code evidence
- agents update generated sections through normal file edits
- humans review ordinary Git diffs
- manual editing remains a first-class maintenance path

Avoid adding servers, dashboards, embeddings, databases, background daemons, or required CLIs until the skill workflow and file contract are proven useful.

## Repository Layout

- `skills/agents-tree/SKILL.md`: concise operational workflow loaded by agents.
- `skills/agents-tree/references/`: detailed contract and maintenance guidance loaded only when needed.
- `skills/agents-tree/assets/`: templates copied into target projects.
- `README.md` and `README.zh.md`: public project explanation.

## File Contract

Generated `AGENTS.md` files should use YAML front matter for knowledge metadata.

Agents Tree front matter belongs only in maintained `AGENTS.md` files. Do not add Agents Tree metadata to `README.md`, `CLAUDE.md`, `agents/claude.md`, or other non-`AGENTS.md` docs.

Expected metadata fields:

```yaml
knowledge_type: module
module: ExampleModule
last_verified_commit: abc123
critical_files: []
critical_symbols: []
confidence: medium
owner: ai-generated
agents_tree_keep: []
agents_tree_skip: []
```

Generated content must be placed inside:

```md
<!-- agents-tree:generated:start -->
<!-- agents-tree:generated:end -->
```

Human-maintained content must be placed inside:

```md
<!-- agents-tree:human:start -->
<!-- agents-tree:human:end -->
```

Refresh logic must preserve human sections unless it detects a direct contradiction. In that case, report the conflict and require human review instead of overwriting the section.

Before editing any target `AGENTS.md`, validate section markers. Missing, duplicated, nested, or out-of-order managed markers make the file invalid for automated refresh.

If an existing target `AGENTS.md` has no managed markers, treat the existing body as human-maintained by default.

Conflicts must be recorded with:

```md
<!-- agents-tree:conflict:start -->
status: unresolved
# Unresolved Agents Tree Conflict

This `AGENTS.md` file contains unresolved project-knowledge conflict.

Do not rely on this file as authoritative guidance for this directory or its subtree until the conflict is resolved.
<!-- agents-tree:conflict:end -->
```

An unresolved conflict blocks maintenance of that `AGENTS.md` file and its subtree, not unrelated code work or unrelated tree nodes.

Conflict blocks must remain understandable to humans and agents that have not installed this skill.

Generated sections should include visible `Knowledge Status` text so ordinary `AGENTS.md` readers can see when to re-check the file.

Conflict blocks may include minimal cross-module context for human judgment; ordinary generated sections should remain cohesive to their directory.

Conflict blocks should use relative Markdown links for known files and related `AGENTS.md` nodes when doing so helps human review.

## Knowledge Rules

- Root `AGENTS.md` files should act as indexes, not encyclopedias.
- Child `AGENTS.md` files should not repeat parent guidance.
- Leaf files may include implementation details only when they are stable enough to be useful.
- Generated content should prioritize decision compression over directory summaries.
- Generated sections should say when to use the file, when to skip it, where to start, and which code-intelligence target or verification path matters.
- Every generated bullet should change the next action; delete bullets that only describe the directory.
- `Skip This File When` guidance should be concrete: if the task is only X, do not read this first; go to Y or query Z.
- When human-facing docs and agent-facing docs coexist in a directory, report audience-boundary suggestions once and record review state in the nearest managed `AGENTS.md` when edits are allowed.
- Every generated claim should be traceable to files, symbols, imports, execution flows, or explicit human notes.
- Stale knowledge should be surfaced clearly instead of silently trusted.
- `last_verified_commit` should advance only after all recorded critical evidence and relevant current diffs or graph evidence have been checked.

## Implementation Rules

- Keep the MVP dependency-light.
- Prefer plain files and Git diffs before adding persistent storage.
- Prefer deterministic scanners before LLM-generated summaries.
- Make LLM usage conversational, explicit, and reviewable.
- Keep agent-facing status messages concise enough to consume directly.
- Do not add fallback behavior that hides stale or invalid knowledge.

## Do Not

- Do not turn this into a general agent memory database.
- Do not overwrite human-maintained sections automatically.
- Do not generate large root-level knowledge dumps.
- Do not treat unchanged files as proof that dependent symbols or flows are unchanged.
- Do not claim knowledge is valid without checking its recorded evidence.
- Do not rewrite README, CLAUDE, or other non-`AGENTS.md` docs during audience-boundary review unless the user explicitly asks.

## Verification

When implementation or automation exists, verify changes with the narrowest relevant check first.

Expected future review paths:

- inspect the relevant `AGENTS.md` metadata
- compare recorded evidence with current code
- review generated-section diffs
- confirm human-maintained sections were preserved

If tests are added, keep fixtures small and focused on the file contract, freshness classification, and human-section preservation.
