---
knowledge_type: architecture
module: PROJECT_NAME
last_verified_commit: COMMIT_SHA
critical_files: []
critical_symbols: []
confidence: low
owner: ai-generated
agents_tree_keep: []
agents_tree_skip: []
---

# Project Decision Index

<!-- agents-tree:generated:start -->
## Knowledge Status

- Last verified: `COMMIT_SHA`
- Confidence: low
- Critical evidence: update `critical_files` and `critical_symbols` in front matter.
- Re-check before trusting this file if those files, symbols, or related flows changed.

<!-- Before committing, replace or delete all placeholders such as COMMIT_SHA, TASK_SHAPE, DECISION_1, SymbolName, and path/to/file. Unresolved placeholders make this guidance invalid. Delete unused generated headings. Root files should route agents, not summarize the project. Delete any bullet that does not change the next action. -->

## Decision Compression

- This file exists to avoid broad root scans when choosing the right project area, code-intelligence target, or verification path.
- It saves reasoning about: `DECISION_1`, `DECISION_2`, `DECISION_3`.

## Use This File When

- Use this when deciding which top-level module or workflow owns a task.
- Use this when checking project-wide rules before entering a child subtree.

## Skip This File When

- If the task only changes `KNOWN_LEAF_SYMBOL`, do not read this file first; read `path/to/leaf/AGENTS.md`.
- If the task is only `LOCAL_IMPLEMENTATION_EDIT`, do not start from the root; go directly to `path/to/known/file`.

## First Hop Rules

- If task is `TASK_SHAPE`, start with `path/to/module` and query `SYMBOL_OR_FLOW` with the project code-intelligence tool.
- If task is `TASK_SHAPE`, skip `path/to/irrelevant-area` and go directly to `path/to/relevant-area`.

## Cross-Module Checks

- Before changing `PROJECT_WIDE_CONTRACT`, inspect current callers or consumers with the declared code-intelligence tool.

## Verification Hints

- For `TASK_SHAPE`, run or inspect `focused verification command or test path`.

## Evidence Notes

- Evidence type: code graph/context, source scan, tests, commit diff, or explicit human note.
- Symbol check: every `critical_symbols` entry resolves to a real code symbol.
<!-- agents-tree:generated:end -->

<!-- agents-tree:human:start -->
Add human-maintained project notes here.
<!-- agents-tree:human:end -->
