---
knowledge_type: implementation
module: LEAF_MODULE_NAME
last_verified_commit: COMMIT_SHA
critical_files: []
critical_symbols: []
confidence: low
owner: ai-generated
agents_tree_keep: []
agents_tree_skip: []
---

# Local Decision Guide

<!-- agents-tree:generated:start -->
## Knowledge Status

- Last verified: `COMMIT_SHA`
- Confidence: low
- Critical evidence: update `critical_files` and `critical_symbols` in front matter.
- Re-check before trusting this file if those files, symbols, or related flows changed.

<!-- Before committing, replace or delete all placeholders such as COMMIT_SHA, TASK_SHAPE, DECISION_1, SymbolName, and path/to/file. Unresolved placeholders make this guidance invalid. Delete unused generated headings. Include implementation details only when they reduce stable task decisions. Delete any bullet that does not change the next action. -->

## Decision Compression

- This file exists to avoid repeated reasoning about local first hops, files to ignore first, and focused verification.
- It saves reasoning about: `DECISION_1`, `DECISION_2`, `DECISION_3`.

## Use This File When

- Use this when editing stable behavior owned by this leaf directory.
- Use this when deciding which local file or test proves `TASK_SHAPE`.

## Skip This File When

- If the task only changes caller, wrapper, or transport behavior outside this leaf, do not read this file first; go to `path/to/caller-or-wrapper`.
- If the task only edits `KnownSymbol` internals, do not reread this guide; inspect `path/to/known/file` and focused tests.

## First Hop Rules

- If task is `TASK_SHAPE`, start with `path/to/file` or symbol `SymbolName`.
- If issue looks like `PATTERN`, do not start from `path/to/tempting-file`; query or inspect `SymbolName` first.

## Cross-Module Checks

- Before changing `LOCAL_BOUNDARY`, inspect current references with the declared code-intelligence tool.

## Verification Hints

- For `TASK_SHAPE`, run or inspect `focused verification command or test path`.

## Evidence Notes

- Evidence type: code graph/context, source scan, tests, commit diff, or explicit human note.
- Symbol check: every `critical_symbols` entry resolves to a real code symbol.
<!-- agents-tree:generated:end -->
