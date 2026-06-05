# Agents Tree File Contract

## Scope

This contract applies to decision-guidance files maintained in a target project by the Agents Tree skill.

The default maintained artifact is native `AGENTS.md`. A project may explicitly choose sidecar mode and use a consistent file such as `decision-router.md` instead. The skill repository is not the target decision guidance. The target project owns the generated files.

## Artifact Strategies

### Native `AGENTS.md` Mode

Native mode is the default. Maintained guidance lives in directory-scoped `AGENTS.md` files, so existing coding agents can discover it with no extra protocol.

Use native mode unless there is a project-specific reason to keep behavior instructions and decision guidance separate.

### Sidecar Decision-Router Mode

Sidecar mode is optional and must be explicit. It is appropriate when a project has strict existing `AGENTS.md`, `CLAUDE.md`, or `agents/claude.md` instruction files and wants generated decision guidance in separate files.

In sidecar mode:

- Use one sidecar name consistently across the tree; prefer `decision-router.md` unless the human chooses another name.
- Discover sidecar files from the repository root that owns both the maintained artifact and target code. If nested repositories, submodules, or package roots make ownership ambiguous, report the ambiguity and do not classify sidecar guidance as `VALID` until the owner root is established.
- Keep a short root `AGENTS.md` pointer that tells agents to read applicable sidecar files from that repository root to the target directory before broad source inspection.
- If adding or changing the pointer would modify a human-owned, strict, or unmarked root `AGENTS.md`, ask for explicit approval before editing it. If approval is not given, report that sidecar guidance exists but may not be reliably discovered.
- Apply this same file contract to sidecar files: front matter, managed sections, freshness metadata, conflict blocks, and review rules.
- Do not maintain sidecar and native generated guidance for the same directory or overlapping ancestor/descendant subtree unless a human explicitly asks to migrate, intentionally split, or resolve the overlap. Record that decision in the nearest authoritative guidance artifact before refreshing either side.

Recommended root pointer:

```md
For Agents Tree decision guidance, read applicable `decision-router.md`
files from the repository root to the target directory before broad code inspection.
```

## Front Matter

Every maintained decision-guidance artifact must begin with YAML front matter:

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
- `last_verified_commit`: commit where the generated guidance was last checked.
- `critical_files`: files whose changes may invalidate this guidance.
- `critical_symbols`: real code symbols whose changes may invalidate this guidance, such as functions, classes, types, exported modules, route handlers, jobs, or flow entry points.
- `confidence`: `high`, `medium`, or `low`.
- `owner`: usually `ai-generated`; use `human-maintained` only when the whole file is intentionally human-owned.
- `agents_tree_keep`: short optional glob list for paths that should be considered even if ignore files would normally exclude them.
- `agents_tree_skip`: short optional glob list for extra paths to skip after applying project ignore files.

Keep `agents_tree_keep` and `agents_tree_skip` very small. Prefer existing `.gitignore`, `.ignore`, `.agentignore`, `.cursorignore`, or tool-specific ignore files for normal exclusions.

`agents_tree_keep` must not reopen secrets, credentials, dependency directories, build outputs, generated artifacts, or vendored code unless a human explicitly asks for that exact path in the current task. Broad keep globs are invalid.

Do not record a current-task ignore exception in durable `agents_tree_keep` unless the human explicitly asks to make that exact exception permanent and the path is safe for future tasks. Otherwise mention the one-time exception only in the current response or local evidence notes.

Recorded `critical_files` and `critical_symbols` must be checked even when they match `agents_tree_skip`. If recorded evidence is hidden by project ignore files or cannot be checked safely, report `INVALID` or cannot verify instead of treating the skipped evidence as trustworthy.

If `owner: human-maintained`, do not edit any part of the file unless the user explicitly asks to edit that human-owned file. Section markers do not override whole-file ownership.

If no usable commit SHA exists, use `last_verified_commit: unknown`, set `confidence: low`, and do not classify the file as `VALID`.

If generated guidance depends on uncommitted working-tree changes, do not set `last_verified_commit` as though `HEAD` contains that evidence. Wait for the source changes to be committed, or record a visible working-tree evidence note and classify conservatively until a real commit contains the verified evidence.

New generated guidance with empty `critical_files` and empty `critical_symbols` should use `confidence: low` unless `Evidence Notes` names another concrete checked source, such as explicit human notes or verified repository structure. Empty recorded evidence cannot support `VALID` by itself.

Before writing or refreshing `critical_symbols`, verify each entry resolves to a real code symbol using the project-declared code-intelligence tool or focused source evidence. Do not include route names, aliases, approximate labels, or task notes in metadata unless they are the actual symbol name. Put non-symbol notes in `Evidence Notes` instead.

`critical_symbols` is not a full function list. It should contain only durable high-value entry points, shared boundary functions, or symbols whose change would invalidate local guidance. For a medium module, prefer about 8-15 symbols unless the module has a strong documented reason for more.

### Optional Audience Boundary Metadata

When a directory contains both human-facing docs such as `README.md` and agent-facing docs such as cross-agent `AGENTS.md` or tool-specific `CLAUDE.md` / `agents/claude.md`, a managed guidance artifact may record that the audience-boundary review has already been suggested, dismissed, or resolved:

```yaml
audience_boundary_review:
  checked_at_commit: abc123
  status: suggested
  files:
    - README.md
    - AGENTS.md
    - CLAUDE.md
```

Allowed `status` values:

- `suggested`: the agent reported possible audience-boundary moves; do not repeat while reviewed files are unchanged.
- `dismissed`: a human declined or postponed the suggestion; do not repeat while reviewed files are unchanged.
- `resolved`: the audience boundary was reviewed and no active suggestion remains; re-check only after reviewed files change.

This metadata is advisory. It does not make the `AGENTS.md` stale or invalid by itself.

If `checked_at_commit` is `unknown`, cannot be compared, or any listed file was renamed, deleted, or replaced, treat repeat suppression as uncertain and re-run the advisory review. Do not rely on `resolved` suppression until the current file set has been reviewed.

### Front Matter Boundaries

Agents Tree YAML front matter belongs only in maintained decision-guidance artifacts: native `AGENTS.md` files, or explicit sidecar files such as `decision-router.md` when sidecar mode is selected.

Do not add Agents Tree front matter or Agents Tree metadata to human-facing docs such as `README.md` or tool-specific agent docs such as `CLAUDE.md` / `agents/claude.md`. If those files already use front matter for a site generator, documentation tool, or local convention, preserve it and do not add Agents Tree fields there.

If an audience-boundary review needs persistent state, record it in the nearest managed guidance artifact, not in the human-facing doc or tool-specific agent doc.

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

Before editing, parse section markers:

- exactly one generated section is required for refresh
- at most one human section is allowed
- starts and ends must be paired, ordered, and not nested
- malformed markers make the file `INVALID` for refresh
- text outside managed sections is human-maintained and must be preserved byte-for-byte
- unresolved template placeholders in front matter, generated sections, `Knowledge Status`, or `Evidence Notes` make the file `INVALID`

If an existing `AGENTS.md` lacks managed markers, treat all existing body text as human-maintained by default. Preserve it byte-for-byte unless the user explicitly asks to normalize it.

During initial tree creation, adding a generated section to an existing unmarked `AGENTS.md` is allowed only if the existing body remains byte-for-byte intact. Wrapping, reordering, trimming, or rewriting that body is normalization and requires explicit user approval.

## Knowledge Status

Generated files should include a visible status section near the top of the generated block:

```md
## Knowledge Status

- Last verified: `abc123`
- Confidence: medium
- Critical evidence: `src/example.ts`, `ExampleService`
- Re-check before trusting this file if those files, symbols, or related flows changed.
```

This section helps humans and agents that do not have Agents Tree installed notice when guidance should be re-checked.

## Decision Compression

Generated content should reduce future task-routing decisions. It should answer which child module owns a task, what current design boundary or rule should guide the next action, what to read or query first, when to skip this file, which stable boundaries require code-intelligence checks, and which focused verification usually proves a change.

Do not use generated sections as module summaries. Parent files may include short child responsibility maps when they help choose among child subtrees, but each responsibility bullet must change the next action. If current design intent is too ambiguous to support a concrete decision rule, report that it needs clarification instead of writing vague guidance. Keep directory scope context short and write decision rules that help future agents act:

```md
## Use This File When

- Read this before broad source inspection when deciding where permission rules live.

## Child Responsibility Map

- `path/to/child-module`: owns `TASK_SHAPE`; start here before reading other child subtrees.

## Skip This File When

- Skip this file for transport-only edits after the target handler is already known.

## First Hop Rules

- If the task is a permission bug, start with `src/permissions.ts`, then query references for `PermissionRule`.

## Cross-Module Checks

- Before changing the exported permission result shape, inspect current callers with the project code-intelligence tool.

## Verification Hints

- Permission rule changes are usually proved by `tests/permissions.test.ts`.
```

Negative guidance is allowed when it saves reasoning tokens, such as naming files or subtrees that should not be read first for a stable task shape. Do not add negative guidance as a broad prohibition; it must be scoped and evidence-backed.

Every generated bullet must change the next action for a future agent: what to read, what to query, what boundary to check, what to skip, or how to choose verification. Delete bullets that merely describe the directory without changing an action.

`Skip This File When` bullets must be concrete. Do not write vague bullets such as "skip when the target is clear." Use the shape: "If the task is only X, do not read this file first; go to Y or query Z."

## Evidence Notes

Generated claims should be easy to verify without rereading broad code areas.

Use a short visible section when a file contains more than a few generated claims:

```md
## Evidence Notes

- First-hop rule: supported by `src/example.ts` and `ExampleService`.
- Skip rule: supported by source scan showing `src/example/index.ts` only re-exports symbols.
- Cross-module check: inspect current references before changing `ExampleService` response shape.
```

Evidence notes should name the evidence type used, such as code graph query/context, source scan, tests, explicit human note, or commit diff. For generated claims involving flows, prefer code graph or process evidence when available.

When a generated rule depends on clarified current design intent, name the evidence as an explicit human note or user-provided context and keep the resulting rule action-oriented.

Keep evidence notes concise. Do not turn them into a citation table or live dependency list.

Generated content should route future agents toward the right evidence. It should not duplicate a full API index, method inventory, current caller list, consumer list, or live dependency graph that search and code-intelligence tools can produce.

## Conflict Sections

If generated guidance conflicts with human text, record the conflict in the file instead of overwriting either side:

```md
<!-- agents-tree:conflict:start -->
status: unresolved
detected_at_commit: abc123
detected_by: agent
conflict_type: human_generated_mismatch
related_files:
  - "[src/example.ts](src/example.ts)"
related_agents:
  - "[src/other-module/AGENTS.md](src/other-module/AGENTS.md)"
impacted_modules:
  - OtherModule

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

Summarize the current code evidence. Use relative Markdown links for known concrete files, for example `[src/example.ts](src/example.ts)`.

## Cross-Module Context

Summarize only the external module guidance needed for human judgment, including relevant neighboring guidance claims or graph evidence.

## Required Resolution

Ask a human to update the human section, update the generated section, or explain why both can coexist.
<!-- agents-tree:conflict:end -->
```

An unresolved conflict blocks guidance maintenance for that `AGENTS.md` file and its subtree. It does not block unrelated code work or unrelated tree nodes.

After a human resolves the conflict, remove the conflict block or change `status` to `resolved` with a short resolution note.

Before maintenance, parse conflict blocks as managed safety markers. Missing, duplicated, or unrecognized conflict `status` values make the file `INVALID` for guidance maintenance. Treat `status: resolved` as non-blocking only when a short resolution note is present; otherwise require human review.

Conflict blocks must be self-explanatory. Humans and agents that have not installed Agents Tree should still understand that the local guidance is not authoritative until the conflict is resolved.

Conflict blocks are allowed to include cross-module context because they are human decision records, not ordinary module guidance. Include only what helps resolve the conflict. Do not use conflict blocks to create permanent dependency maps.

When writing conflict blocks, use relative Markdown links for concrete files, tests, and `AGENTS.md` nodes whenever the path is already known. Do not invent links or scan broadly just to add links.

Allowed `conflict_type` values:

- `human_generated_mismatch`
- `parent_child_mismatch`
- `missing_critical_evidence`
- `scope_mismatch`

Parent/child instruction mismatches are conflicts even when both claims are generated.

## Recommended Sections

Root and module files should use a small subset of:

```md
# Knowledge Status
# Decision Compression
# Use This File When
# Child Responsibility Map
# Skip This File When
# First Hop Rules
# Cross-Module Checks
# Verification Hints
# Do Not
# Evidence Notes
```

Only include sections that contain useful information. Empty headings waste context.

Delete any generated bullet that does not change the next action.

## Freshness Review

Classify the file:

- `VALID`: recorded evidence still supports the generated claims.
- `STALE_WARNING`: related evidence changed, but claims appear mostly usable.
- `INVALID`: critical evidence changed enough that the file must not be trusted.

Use code-intelligence tools when available. If they are unavailable, use focused file reads and Git history. Do not infer validity from unchanged filenames alone.

Decision checklist:

- Missing or malformed metadata or managed markers in a managed file: `INVALID`.
- Unresolved template placeholders such as `COMMIT_SHA`, `TASK_SHAPE`, `DECISION_1`, `SymbolName`, or `path/to/file`: `INVALID`.
- Deleted, renamed, moved, or missing critical file or symbol: `INVALID` until the new evidence is verified. Keep the old metadata while investigating; do not delete missing evidence entries merely to make the file pass freshness review.
- Recorded critical evidence hidden by `agents_tree_skip`, project ignore files, or unavailable tools: check it anyway when safe; otherwise report `INVALID` or cannot verify.
- Changed critical symbol signature, ownership boundary, entry point, scope, or execution flow: `INVALID`.
- Changed critical file with stable responsibility and key flows after focused review: `STALE_WARNING`.
- Empty `critical_files` and empty `critical_symbols` without concrete evidence notes: cannot be `VALID`.
- No relevant recorded evidence or current references/flows changed: `VALID`.
- Insufficient evidence: report `INVALID` or cannot verify; never report `VALID`.

Advance `last_verified_commit` only after checking every recorded critical file and symbol, plus current diffs or code-intelligence evidence that affects generated claims. Do not advance it when any recorded evidence cannot be checked.

If the declared code-intelligence tool is unavailable, stale, or partial, record the limitation in `Evidence Notes` or the review report. The bounded no-tool sequence may support `STALE_WARNING`, `INVALID`, or cannot verify; it supports `VALID` only when every generated claim is fully re-evidenced without the missing tool.

If generated claims are verified against uncommitted working-tree changes, keep `last_verified_commit` at the last committed evidence state and record the working-tree limitation visibly. Do not advance it to `HEAD` until `HEAD` contains the verified evidence.

After refresh, run a metadata consistency check:

- every `critical_files` path exists in the target repository
- every `critical_symbols` entry resolves to a real code symbol
- generated claims still fit this guidance file's directory scope
- parent guidance claims do not contradict this file
- `last_verified_commit` belongs to the target repository that contains this guidance artifact

In multi-repo workspaces, `last_verified_commit` must come from the repository containing the guidance artifact. If a code-intelligence index reports a parent, aggregate, or external repository commit, mention it in `Evidence Notes` but do not use it as `last_verified_commit`.
