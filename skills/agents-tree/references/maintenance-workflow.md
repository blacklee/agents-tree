# Agents Tree Maintenance Workflow

## Choose Artifact Strategy First

Use native `AGENTS.md` mode by default. It preserves automatic discovery and keeps learning cost low.

Recommend sidecar mode only when the target project has a clear reason to separate decision guidance from agent behavior instructions:

- existing `AGENTS.md`, `CLAUDE.md`, or `agents/claude.md` files are strict tool instructions that should remain small and human-owned
- the team explicitly wants generated decision guidance in separate reviewable files
- the project is migrating from mixed instruction files and wants a clean boundary

If sidecar mode is selected, prefer `decision-router.md` as the consistent file name unless the human chooses another name. Ensure root `AGENTS.md` contains a short pointer telling agents to read applicable sidecar files from the repository root to the target directory before broad code inspection. Without that pointer, sidecar guidance is easy to miss.

Selecting sidecar mode is not permission to edit a human-owned, strict, or unmarked root `AGENTS.md`. If the pointer is missing, ask for explicit approval before adding it. Without approval, report that sidecar guidance may not be reliably discovered.

Discover sidecar files from the repository root that owns the maintained artifact and target code. In nested repositories, package workspaces, or submodules, establish that root before classifying sidecar guidance as `VALID`.

Do not create both native `AGENTS.md` guidance and sidecar guidance for the same directory or overlapping ancestor/descendant subtree. If both already exist, stop and ask whether to migrate, merge, intentionally split, or retire one artifact before refreshing either side.

## When To Add Guidance Files

Add a file only when it will reduce future agent decision cost.

Good candidates are directories where generated guidance can reliably answer at least one decision the next agent would otherwise have to reason through:

- which file, symbol, or flow to inspect first for a recurring task shape
- which code graph or code-intelligence query target to use first
- which boundary must be checked before changing an API, data shape, or ownership rule
- which focused verification usually proves the local change
- which nearby files or subtrees are tempting but usually irrelevant for a task shape

Directory size is only supporting evidence. A large directory without stable routing value should not get a child guidance file. A small directory may deserve one when it repeatedly forces agents to choose between entry-layer, rule-layer, transport-layer, or verification-layer evidence.

Before creating a child guidance file, state the decision-compression reason:

- first-hop guidance avoided
- code-intelligence target clarified
- stable boundary check captured
- irrelevant subtree or file skipped
- durable verification or compatibility rule captured

If none applies, do not create the file.

Do not add a guidance file for directories whose responsibilities and first-hop evidence are obvious from nearby names, tests, or existing parent guidance.

## When To Update Existing Knowledge

Consider updating an existing `AGENTS.md` when current work changes stable decision guidance future agents need:

- a changed file appears in `critical_files`
- a changed symbol appears in `critical_symbols`
- a module responsibility, boundary, first-hop rule, call flow, skip rule, or verification path changed
- an important directory was added, removed, renamed, or moved
- the agent found a mismatch between existing guidance and current code evidence
- the agent repeatedly had to decide where to start or what to ignore because local guidance was missing
- the user explicitly asked to update, refresh, or record guidance

Do not update the tree for every code change. Small implementation edits that do not change durable project understanding should leave the guidance tree untouched.

## Tree Shape

- Root files act as indexes and route agents to the next decision point.
- Mid-level files describe stable task-routing rules, boundaries, and common first hops.
- Leaf files may describe stable implementation decisions only when they reduce future task branching.
- Child files must not repeat parent guidance.
- Lower files may override parent rules only when the override is explicit and local.
- Each `AGENTS.md` should be cohesive to its directory and should not store a full cross-module dependency map.

Generated content should work as a decision map, not an encyclopedia. It should help future agents choose what to read or query next, what not to read first, and which checks prove a change. Do not duplicate a full API index, method inventory, current caller list, consumer list, or live dependency graph.

Every generated bullet must pass the next-action test: would this bullet change what the next agent reads, queries, cross-checks, skips, or verifies? If not, delete it.

For flat directories with many large files, create one route-oriented guidance file first. Do not create file-level guidance files unless the code is reorganized into real subdirectories. If the directory remains too broad for useful local guidance, recommend code-structure refactoring separately from guidance maintenance.

## Cross-Module Handoff

Use maintained guidance files to tell agents when cross-module reasoning is needed and which code-intelligence target to start from, not to store every relationship.

When work crosses a module boundary:

1. Start from the current module's nearest applicable guidance artifact.
2. Use a code graph, code-intelligence, structural search, or language-aware navigation tool to find current callers, callees, references, and impact.
3. Read the nearest applicable guidance artifact for any impacted module before editing across that boundary.
4. Update guidance only when the cross-module rule is durable, such as "inspect downstream consumers before changing this response shape."

Do not write lists of current callers, imports, references, or consumers into `AGENTS.md` unless they are intentionally stable architectural contracts.

## Generated Content Shape

Prefer decision-compression sections over module-summary sections:

- `Use This File When`: task shapes where this file should be read before broad source inspection.
- `First Hop Rules`: stable rules of the form "if task is X, start with Y."
- `Skip This File When`: concrete task shapes where another file, subtree, or code graph query should come first.
- `Cross-Module Checks`: stable boundary checks and the code-intelligence targets to inspect before editing across them.
- `Verification Hints`: focused tests, commands, or review steps that usually prove this area.
- `Evidence Notes`: concise evidence types behind the generated claims.

Keep scope context short. A one-line ownership snapshot is useful when it explains why the routing rules apply, but directory responsibilities should not dominate the generated section.

Allow negative guidance when it saves reasoning tokens, such as "do not start from `__init__.py` for permission bugs" or "transport-only changes usually do not require reading the full services subtree." Negative guidance must be evidence-backed and scoped to stable task shapes.

`Skip This File When` is required to be specific when present. Do not write vague bullets such as "skip this when the target is already clear" or "skip for unrelated changes." Use the form "If the task is only X, do not read this file first; go to Y or query Z."

## Audience Boundary Review

Run this lightweight advisory check when the target directory contains both:

- human-facing docs, such as `README.md`, install docs, usage docs, or contribution docs
- agent-facing docs, such as cross-agent `AGENTS.md`, tool-specific `CLAUDE.md` / `agents/claude.md`, or other coding-agent instruction files

Review only the reader boundary:

- Human-facing docs should explain the project, onboarding, install, usage, contribution, and human-readable architecture context.
- Agent-facing docs should guide agent actions: first hops, code-intelligence requirements, skip rules, boundary checks, verification choice, and links to human docs when context is needed.
- Shared facts may appear in both places only when expressed for different readers.

Report likely misplaced information as suggestions. Do not move or rewrite human-facing docs such as `README.md` or tool-specific agent docs such as `CLAUDE.md` / `agents/claude.md` unless the user explicitly asks for that edit.

Avoid repeated suggestions:

1. Check the nearest managed guidance artifact for `audience_boundary_review`.
2. If `status` is `suggested`, `dismissed`, or `resolved`, and all listed files have not changed since `checked_at_commit`, do not repeat the same suggestion.
3. Re-run the review when any listed file changed since `checked_at_commit`, the reviewed file set changed, or the user explicitly asks.
4. In check-only mode, report suggestions without writing metadata unless the user explicitly asks to record the review state.
5. In create or refresh mode, record or update `audience_boundary_review` only in the nearest managed guidance artifact.

If `checked_at_commit` is `unknown`, cannot be compared, or any listed file was renamed, deleted, or replaced, report the repeat-suppression state as uncertain and re-run the advisory check. Do not write or rely on `resolved` suppression for the new file set unless it has been reviewed.

Use `status: suggested` after reporting active suggestions, `status: dismissed` when the user declines, and `status: resolved` when no active suggestion remains after review or cleanup.

Do not add YAML front matter to non-`AGENTS.md` docs for this review. If those files already have project-specific front matter, preserve it and leave Agents Tree metadata out of it.

## Evidence Collection

Separate instruction reading from evidence discovery:

1. Read applicable `AGENTS.md` files for instructions and local guidance; in sidecar mode, also read the sidecar guidance files they point to.
2. Apply project ignore files before discovering candidate evidence files.
3. Apply `agents_tree_skip` after project ignore files.
4. Apply `agents_tree_keep` only for rare reviewed exceptions.
5. **MUST** attempt project-declared code graph or code-intelligence tools before creating, reviewing, or refreshing generated guidance.
6. Use focused source reads, relevant tests, Git diffs, and recent history only as needed.

Avoid broad source scans unless the existing guidance is missing or invalid.

If project guidance or the nearest applicable guidance artifact declares a code-intelligence tool, attempt it first for generated guidance. Use `grep`, `rg`, and raw file reads only as supplementary evidence or when the declared tool is unavailable or stale.

Use ignore files before applying `agents_tree_skip`. Use `agents_tree_keep` only for a small number of paths that must remain visible despite broad ignore patterns.

`agents_tree_keep` must not reopen secrets, credentials, dependency directories, build outputs, generated artifacts, or vendored code unless a human explicitly asks for that exact path in the current task.

Do not write a current-task keep exception into durable `agents_tree_keep` unless the human explicitly asks to make that exact safe path permanent. A one-time exception belongs in the current response or task evidence notes.

Recorded `critical_files` and `critical_symbols` must be checked even when they match `agents_tree_skip`. If project ignore files or safety rules prevent checking recorded evidence, report `INVALID` or cannot verify instead of treating skipped evidence as unchanged.

If code graph or code-intelligence tools are unavailable, use this bounded sequence:

1. current diff
2. recorded `critical_files`
3. recorded `critical_symbols`
4. nearest imports, exports, and type declarations around named symbols
5. focused textual references for named symbols only

Stop when there is enough evidence to classify freshness, or report that validity cannot be established. Record unavailable, stale, or partial tool evidence in `Evidence Notes` or the review report. Do not report `VALID` unless every generated claim is fully re-evidenced without the missing tool.

## Critical Metadata Selection

Before writing or refreshing metadata:

- verify every `critical_files` path exists in the target repository
- verify every `critical_symbols` entry resolves to a real code symbol
- exclude route names, aliases, approximate labels, and task notes unless they are actual symbol names
- put non-symbol context in `Evidence Notes`, not metadata
- keep `critical_symbols` focused on durable entry points, boundary functions, exported types, jobs, or flow entry symbols

`critical_symbols` is not a complete function list. For a medium module, prefer about 8-15 high-value symbols unless a strong reason is recorded.

In multi-repo workspaces, compute `last_verified_commit` from the repository containing the target `AGENTS.md`. If a graph index reports a parent or aggregate repository commit, mention it in `Evidence Notes` but do not use it as `last_verified_commit`.

If generated guidance depends on uncommitted working-tree changes, do not advance `last_verified_commit` to `HEAD` as though the commit contains that evidence. Wait for the source changes to be committed, or record a visible working-tree limitation and classify conservatively.

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
- write or preserve conflict blocks when human and generated guidance disagree
- update metadata only after the generated claims have been checked
- run a metadata consistency check before reporting completion
- list any unresolved conflicts in the final response

If the user asks for a check-only pass, report findings without editing files.

In check mode, stop after reporting freshness status, evidence reviewed, and recommended next action. Do not modify `AGENTS.md` or metadata unless the user explicitly requested refresh, update, or write.

After refresh, verify:

- every `critical_files` path exists
- every `critical_symbols` entry resolves
- generated claims still fit the module scope
- parent guidance claims do not contradict this file
- `last_verified_commit` matches the target repository, not an unrelated parent or aggregate repository

## Review Mode

When reviewing an `AGENTS.md`, assess whether it reduces future decision cost:

- Does it route agents to the right first file, symbol, flow, or code-intelligence query?
- Does it say concretely when the file should be skipped and where to go instead?
- Does every generated bullet change the next action?
- Does it prevent repeated discovery of stable boundaries?
- Does it avoid duplicating searchable details, current caller lists, or dependency inventories?
- Are critical files and symbols precise enough for freshness checks?
- Does it use declared code-intelligence evidence when that evidence is available?

When reviewing a proposed diff, compare the diff against the previous file, not only the final artifact. Flag deletion, movement, wrapping, or rewriting of unmanaged text or human sections unless the diff includes explicit human approval.

Return a concise token-saving value rating: `HIGH`, `MEDIUM`, or `LOW`.

## Conflict Handling

Treat these as conflicts:

- human text says a module owns one responsibility, but code evidence shows another
- generated text would reverse or weaken a human rule
- a critical file or symbol disappeared
- parent and child guidance files give incompatible instructions
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

If a file already has an unresolved conflict block, do not refresh that file or any child guidance file under its directory. Continue with unrelated files when they do not depend on the conflicted subtree.

Before maintenance, parse conflict-block status mechanically. Missing, duplicated, or unrecognized `status` values make the file `INVALID` for guidance maintenance. Treat `status: resolved` as non-blocking only when a short resolution note is present.

Write conflict blocks so they are understandable without this skill installed. The marker is for tools; the visible Markdown body is for humans and ordinary agents.

Conflict blocks may include cross-module context when needed for human judgment. This is an explicit exception to normal guidance-file cohesion. Include impacted modules, relevant neighboring guidance claims, and current graph/search evidence only when they help resolve the conflict.

Use relative Markdown links in conflict blocks for known files, tests, and `AGENTS.md` nodes so humans can jump directly to evidence. Do not invent links or broaden the scan only to add links.

Examples:

- An unresolved conflict in `src/payments/AGENTS.md` blocks refreshing `src/payments/**/AGENTS.md`.
- It does not block refreshing `src/search/AGENTS.md`.
- It does not block ordinary code edits under `src/payments/`, but agents must inspect source evidence directly instead of relying on the conflicted guidance.

Before refreshing any child guidance file, read applicable ancestor guidance files and stop if an unresolved ancestor conflict covers the target path.

## Scope Moves

If a module split, merge, rename, or move changes directory ownership, classify affected guidance files as `INVALID` first.

Update the tree shape explicitly after verifying the new scope:

- retire old nodes that no longer own useful guidance
- move or recreate nodes at the new directory boundary
- create child nodes only where they reduce future reasoning cost

Do not hide a scope move inside a normal generated-section refresh.
