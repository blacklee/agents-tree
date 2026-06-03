# Agents Tree Maintenance Workflow

## When To Add `AGENTS.md`

Add a file only when it will reduce future agent reasoning cost.

Good candidates:

- directory has many files
- directory has several child modules
- directory owns an independent business or technical responsibility
- directory contains high-risk or historically fragile code
- directory has compatibility rules or non-obvious design constraints
- agents repeatedly inspect the directory during normal work

Before creating a child `AGENTS.md`, state the token-saving reason:

- repeated scans avoided
- high-complexity directory
- stable ownership boundary
- high-risk cross-module contract
- durable verification or compatibility rule

If none applies, do not create the file.

Do not add an `AGENTS.md` for tiny directories with obvious responsibilities.

## When To Update Existing Knowledge

Consider updating an existing `AGENTS.md` when current work changes stable knowledge future agents need:

- a changed file appears in `critical_files`
- a changed symbol appears in `critical_symbols`
- a module responsibility, boundary, entry point, call flow, or verification path changed
- an important directory was added, removed, renamed, or moved
- the agent found a mismatch between existing guidance and current code evidence
- the agent repeatedly had to scan the same directory because local guidance was missing
- the user explicitly asked to update, refresh, or record knowledge

Do not update the tree for every code change. Small implementation edits that do not change durable project understanding should leave the knowledge tree untouched.

## Tree Shape

- Root files act as indexes and route agents.
- Mid-level files describe module boundaries and common work.
- Leaf files may describe stable implementation details.
- Child files must not repeat parent guidance.
- Lower files may override parent rules only when the override is explicit and local.
- Each `AGENTS.md` should be cohesive to its directory and should not store a full cross-module dependency map.

Generated content should work as an entry map, not an encyclopedia. It should help future agents choose what to read next and avoid repeating stable reasoning. Do not duplicate a full API index, method inventory, or live dependency graph.

For flat directories with many large files, create one route-oriented `AGENTS.md` first. Do not create file-level `AGENTS.md` files unless the code is reorganized into real subdirectories. If the directory remains too broad for useful local guidance, recommend code-structure refactoring separately from knowledge-tree maintenance.

## Cross-Module Handoff

Use `AGENTS.md` to tell agents when cross-module reasoning is needed, not to store every relationship.

When work crosses a module boundary:

1. Start from the current module's nearest `AGENTS.md`.
2. Use a code graph, code-intelligence, structural search, or language-aware navigation tool to find current callers, callees, references, and impact.
3. Read the nearest `AGENTS.md` for any impacted module before editing across that boundary.
4. Update `AGENTS.md` only when the cross-module rule is durable, such as "inspect downstream consumers before changing this response shape."

Do not write lists of current callers, imports, or references into `AGENTS.md` unless they are intentionally stable architectural contracts.

## Evidence Collection

Separate instruction reading from evidence discovery:

1. Read applicable `AGENTS.md` files for instructions and local knowledge.
2. Apply project ignore files before discovering candidate evidence files.
3. Apply `agents_tree_skip` after project ignore files.
4. Apply `agents_tree_keep` only for rare reviewed exceptions.
5. **MUST** use project-declared code graph or code-intelligence tools before creating, reviewing, or refreshing generated knowledge.
6. Use focused source reads, relevant tests, Git diffs, and recent history only as needed.

Avoid broad source scans unless the existing knowledge is missing or invalid.

If project guidance or the nearest applicable `AGENTS.md` declares a code-intelligence tool, using it is mandatory for generated knowledge. Use `grep`, `rg`, and raw file reads only as supplementary evidence or when the declared tool is unavailable or stale.

Use ignore files before applying `agents_tree_skip`. Use `agents_tree_keep` only for a small number of paths that must remain visible despite broad ignore patterns.

`agents_tree_keep` must not reopen secrets, credentials, dependency directories, build outputs, generated artifacts, or vendored code unless a human explicitly asks for that exact path in the current task.

If code graph or code-intelligence tools are unavailable, use this bounded sequence:

1. current diff
2. recorded `critical_files`
3. recorded `critical_symbols`
4. nearest imports, exports, and type declarations around named symbols
5. focused textual references for named symbols only

Stop when there is enough evidence to classify freshness, or report that validity cannot be established.

## Critical Metadata Selection

Before writing or refreshing metadata:

- verify every `critical_files` path exists in the target repository
- verify every `critical_symbols` entry resolves to a real code symbol
- exclude route names, aliases, approximate labels, and task notes unless they are actual symbol names
- put non-symbol context in `Evidence Notes`, not metadata
- keep `critical_symbols` focused on durable entry points, boundary functions, exported types, jobs, or flow entry symbols

`critical_symbols` is not a complete function list. For a medium module, prefer about 8-15 high-value symbols unless a strong reason is recorded.

In multi-repo workspaces, compute `last_verified_commit` from the repository containing the target `AGENTS.md`. If a graph index reports a parent or aggregate repository commit, mention it in `Evidence Notes` but do not use it as `last_verified_commit`.

## Unmarked `AGENTS.md` Migration

When creating an Agents Tree in a directory that already has an unmarked `AGENTS.md`:

1. Treat the existing body as human-maintained.
2. Preserve the existing body byte-for-byte, including headings, comments, whitespace, and ordering.
3. Add metadata as front matter only if needed for the managed generated section.
4. Place generated content in managed sections without wrapping, reordering, trimming, or rewriting the existing body.
5. Report that the file now contains preserved human text outside managed sections.

Do not wrap existing text in `agents-tree:human` markers unless the user explicitly asks to normalize the file. Normalization is a human-reviewed formatting step, not part of default creation or refresh.

On later refreshes, continue preserving unmanaged text outside managed sections as human-maintained content.

## Missing Critical Evidence

If a recorded `critical_files` path or `critical_symbols` entry cannot be found:

1. Classify the `AGENTS.md` as `INVALID`.
2. Keep the old metadata while investigating; do not delete or replace missing entries first.
3. Check whether the evidence was deleted, renamed, moved across module boundaries, or replaced by a new flow.
4. Use code graph or code-intelligence evidence when available; otherwise use Git history and focused references for the recorded path or symbol.
5. Update metadata only after the generated claims are re-evidenced against the new location or replacement flow.

If the missing evidence reflects a module split, merge, rename, or ownership move, handle it as a scope move before refreshing generated content.

If the replacement cannot be established, preserve or write a conflict block with `conflict_type: missing_critical_evidence` and leave the stale evidence visible for human review.

## Refresh Discipline

When refreshing:

- verify current code evidence first
- edit only the generated section
- preserve human sections byte-for-byte unless the user asks otherwise
- write or preserve conflict blocks when human and generated knowledge disagree
- update metadata only after the generated claims have been checked
- run a metadata consistency check before reporting completion
- list any unresolved conflicts in the final response

If the user asks for a check-only pass, report findings without editing files.

In check mode, stop after reporting freshness status, evidence reviewed, and recommended next action. Do not modify `AGENTS.md` or metadata unless the user explicitly requested refresh, update, or write.

After refresh, verify:

- every `critical_files` path exists
- every `critical_symbols` entry resolves
- generated claims still fit the module scope
- parent `AGENTS.md` claims do not contradict this file
- `last_verified_commit` matches the target repository, not an unrelated parent or aggregate repository

## Review Mode

When reviewing an `AGENTS.md`, assess whether it reduces future reasoning cost:

- Does it route agents to fewer files?
- Does it prevent repeated discovery of stable boundaries?
- Does it avoid duplicating searchable details?
- Are critical files and symbols precise enough for freshness checks?
- Does it use declared code-intelligence evidence when that evidence is available?

Return a concise token-saving value rating: `HIGH`, `MEDIUM`, or `LOW`.

## Conflict Handling

Treat these as conflicts:

- human text says a module owns one responsibility, but code evidence shows another
- generated text would reverse or weaken a human rule
- a critical file or symbol disappeared
- parent and child `AGENTS.md` files give incompatible instructions
- a module split, merge, rename, or move changes the tree scope

Do not resolve conflicts silently. Record the exact file, competing claims, and code evidence in an `agents-tree:conflict` block.

```md
<!-- agents-tree:conflict:start -->
status: unresolved
# Unresolved Agents Tree Conflict

This `AGENTS.md` file contains unresolved project-knowledge conflict.

Do not rely on this file as authoritative guidance for this directory or its subtree until the conflict is resolved.
<!-- agents-tree:conflict:end -->
```

If a file already has an unresolved conflict block, do not refresh that file or any child `AGENTS.md` under its directory. Continue with unrelated files when they do not depend on the conflicted subtree.

Write conflict blocks so they are understandable without this skill installed. The marker is for tools; the visible Markdown body is for humans and ordinary agents.

Conflict blocks may include cross-module context when needed for human judgment. This is an explicit exception to normal `AGENTS.md` cohesion. Include impacted modules, relevant neighboring `AGENTS.md` claims, and current graph/search evidence only when they help resolve the conflict.

Use relative Markdown links in conflict blocks for known files, tests, and `AGENTS.md` nodes so humans can jump directly to evidence. Do not invent links or broaden the scan only to add links.

Examples:

- An unresolved conflict in `src/payments/AGENTS.md` blocks refreshing `src/payments/**/AGENTS.md`.
- It does not block refreshing `src/search/AGENTS.md`.
- It does not block ordinary code edits under `src/payments/`, but agents must inspect source evidence directly instead of relying on the conflicted guidance.

Before refreshing any child `AGENTS.md`, read applicable ancestor `AGENTS.md` files and stop if an unresolved ancestor conflict covers the target path.

## Scope Moves

If a module split, merge, rename, or move changes directory ownership, classify affected `AGENTS.md` files as `INVALID` first.

Update the tree shape explicitly after verifying the new scope:

- retire old nodes that no longer own useful knowledge
- move or recreate nodes at the new directory boundary
- create child nodes only where they reduce future reasoning cost

Do not hide a scope move inside a normal generated-section refresh.
