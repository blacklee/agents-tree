---
knowledge_type: module
module: MODULE_NAME
last_verified_commit: COMMIT_SHA
critical_files: []
critical_symbols: []
confidence: medium
owner: ai-generated
agents_tree_keep: []
agents_tree_skip: []
---

# Module Decision Guide

<!-- agents-tree:generated:start -->
## Knowledge Status

- Last verified: `COMMIT_SHA`
- Confidence: medium
- Critical evidence: update `critical_files` and `critical_symbols` in front matter.
- Re-check before trusting this file if those files, symbols, or related flows changed.

<!-- Before committing, replace or delete all placeholders such as COMMIT_SHA, TASK_SHAPE, DECISION_1, SymbolName, and path/to/file. Unresolved placeholders make this guidance invalid. Delete unused generated headings. Keep only stable local decision guidance. Delete any bullet that does not change the next action. -->

## Decision Compression

- This file exists to avoid repeated reasoning about which local rule, entry layer, boundary, or verification path applies.
- It saves reasoning about: `DECISION_1`, `DECISION_2`, `DECISION_3`.

## Use This File When

- Use this when deciding where to start inside this module for `TASK_SHAPE`.
- Use this before changing local boundaries, exported data shapes, or durable rules.

## Skip This File When

- If the task only changes implementation inside `KnownSymbol`, do not read this file first; inspect `path/to/known/file`.
- If the task is `NEIGHBOR_TASK_SHAPE`, do not start in this subtree; read `neighboring/module/AGENTS.md`.

## First Hop Rules

- If task is `TASK_SHAPE`, start with `path/to/file` or symbol `SymbolName`.
- If issue looks like `PATTERN`, skip `path/to/tempting-file` and query references for `SymbolName` first.

## Cross-Module Checks

- Before changing `BOUNDARY_SYMBOL_OR_DATA_SHAPE`, inspect current callers, callees, or consumers with the declared code-intelligence tool.

## Verification Hints

- For `TASK_SHAPE`, run or inspect `focused verification command or test path`.

## Evidence Notes

- Evidence type: code graph/context, source scan, tests, commit diff, or explicit human note.
- Symbol check: every `critical_symbols` entry resolves to a real code symbol.
<!-- agents-tree:generated:end -->

<!-- agents-tree:human:start -->
Add human-maintained module notes here.
<!-- agents-tree:human:end -->
