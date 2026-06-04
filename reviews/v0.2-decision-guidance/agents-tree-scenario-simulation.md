# Agents Tree Decision-Guidance Scenario Simulation

Review date: 2026-06-04

Review environment:

- Runtime: local Codex
- Agent: Codex
- Model: GPT-5.5
- Reasoning effort: high
- Skill workflow: Superpowers SKILL (`using-superpowers`) and `agents-tree`

Prompt used:

- `reviews/v0.2-decision-guidance/prompts/agents-tree-scenario-simulation.md`

Scope read:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`

Simulation role: a coding agent with the current decision-guidance version of the `agents-tree` skill installed.

## Executive Judgment

The v0.2 decision-guidance contract handles both scenarios correctly when followed literally.

In native `AGENTS.md` mode, `src/article/AGENTS.md` must be treated as invalid for authority because human-maintained responsibility guidance conflicts with current code evidence. The right action is not to rewrite the human section and not to refresh generated guidance around the conflict. The agent should preserve existing sections, write an unresolved conflict block, use code-intelligence tools for the article-to-feed boundary, and read `src/feed/AGENTS.md` if that impacted module has one.

In sidecar decision-router mode, generated decision guidance belongs in `decision-router.md`, not in the human-owned `AGENTS.md`. The local `src/payments/AGENTS.md` may still be relevant as human instruction text, but it must not receive Agents Tree metadata, managed markers, or generated routing guidance. If native and sidecar generated guidance overlap in the same directory, the agent must stop and ask for migration or resolution rather than choosing one silently.

Token-saving value rating: `HIGH`.

The scenarios exercise the product's strongest v0.2 boundary: durable decision guidance is useful only when it routes future agents toward evidence without overwriting human instructions or storing live dependency maps.

## Scenario A: Native AGENTS.md Mode

### Step-By-Step Action Sequence

1. Read root `AGENTS.md`.
2. Apply project ignore files before evidence discovery, which skips `.gitignore` paths such as `dist/` and `node_modules/`.
3. Apply root `agents_tree_skip`, which skips `vendor/`.
4. Read any intermediate parent guidance such as `src/AGENTS.md` if it exists.
5. Read `src/article/AGENTS.md` because the task changes `src/article/ArticleService.ts`, which is recorded in `critical_files`.
6. Validate ownership, front matter, generated markers, human markers, and any existing conflict block.
7. Inspect the current diff or task evidence around `src/article/ArticleService.ts`.
8. Use language-aware/code-intelligence tools to inspect the `ArticleService` responsibility, the recommendation ranking output shape, and downstream references.
9. Follow real code-intelligence evidence into `src/feed/` because the changed output is consumed there.
10. Read `src/feed/AGENTS.md` if it exists before editing across that boundary or making any durable feed-related guidance claim.
11. Compare the human section's claim that `ArticleService` only reads articles with code evidence showing recommendation ranking ownership.
12. Classify `src/article/AGENTS.md` as `INVALID` for authority.
13. Preserve the generated and human sections as-is.
14. Write or preserve an unresolved conflict block in `src/article/AGENTS.md`.
15. Do not refresh `src/article/AGENTS.md` generated content or child guidance under `src/article/` while the conflict is unresolved.
16. Continue ordinary code work only from direct source evidence, not from the conflicted article guidance.

### Guidance Files To Read

- Root `AGENTS.md`: it supplies repository-level instructions, the tree index, and root `agents_tree_skip`.
- `src/AGENTS.md`, if present: it is an applicable ancestor between root and `src/article/`.
- `src/article/AGENTS.md`: it is the nearest applicable guidance for the changed critical file.
- `src/feed/AGENTS.md`, if present: the changed output shape crosses into feed consumers.

I would not read ignored or skipped directories as part of guidance or evidence discovery unless the human explicitly asks for an exact skipped path in the current task.

### Cross-Module Tool Class

Use code graph, language-server references, structural search, or other code-intelligence tools before making or refreshing generated knowledge. Good query targets are:

- `ArticleService`
- the recommendation ranking method or exported output type, after confirming its real symbol name
- current references and consumers of the ranking output shape
- imports or call paths from `src/feed/` back to the article boundary

Focused `rg`, Git diff, and source reads are supplementary evidence. They should not become a broad repo scan and should not be used to write a durable list of live consumers.

### Freshness Classification

`src/article/AGENTS.md`: `INVALID`.

Reasons:

- `src/article/ArticleService.ts` is recorded in `critical_files`.
- The current task changes a durable output shape owned by `ArticleService`.
- The output is consumed across the module boundary by `src/feed/`.
- The human-maintained section says `ArticleService` only reads articles, but current code evidence says it also owns recommendation ranking.

This is an invalid-authority state, not a normal refresh. The file can receive a conflict block, but generated guidance should not be refreshed until a human resolves the responsibility contradiction.

`src/feed/AGENTS.md`, if present, is not automatically stale merely because feed consumes the changed shape. It should be checked for relevant local constraints. Update it only if durable feed decision guidance actually changed.

### Conflict Block Draft

```md
<!-- agents-tree:conflict:start -->
status: unresolved
detected_at_commit: <current-commit-or-working-tree>
detected_by: agent
conflict_type: human_generated_mismatch
related_files:
  - "[src/article/ArticleService.ts](src/article/ArticleService.ts)"
related_agents:
  - "[src/article/AGENTS.md](src/article/AGENTS.md)"
  - "[src/feed/AGENTS.md](src/feed/AGENTS.md)"
impacted_modules:
  - Feed

# Unresolved Agents Tree Conflict

This `AGENTS.md` file contains unresolved project-knowledge conflict.

Do not rely on this file as authoritative guidance for this directory or its subtree until the conflict is resolved.

Human action required:

- review the human-maintained section
- review the generated section
- update one or both sides
- remove this conflict block, or mark it resolved with a short note

## Human Claim

The human-maintained section says `ArticleService` only reads articles.

## Code Evidence

Current code evidence in [src/article/ArticleService.ts](src/article/ArticleService.ts) shows `ArticleService` also owns recommendation ranking. The current task changes the recommendation ranking output shape.

## Cross-Module Context

The changed recommendation ranking output is consumed by `src/feed/`. Before editing across that boundary or writing durable handoff guidance, inspect the current feed references with the project code-intelligence tool and read [src/feed/AGENTS.md](src/feed/AGENTS.md) if it exists.

## Required Resolution

Decide whether `ArticleService` is allowed to own recommendation ranking, whether ranking ownership should move elsewhere, or whether the human-maintained guidance should be updated. Until this is resolved, do not refresh generated article guidance or rely on this file as authoritative guidance for `src/article/` or its subtree.
<!-- agents-tree:conflict:end -->
```

The placeholder `<current-commit-or-working-tree>` must be replaced with a real current commit or an explicit working-tree marker. Do not invent a commit hash.

### Skipped Paths

- `dist/`: skipped because it is ignored by `.gitignore`.
- `node_modules/`: skipped because it is ignored by `.gitignore`.
- `vendor/`: skipped because root `AGENTS.md` declares `agents_tree_skip: ["vendor/"]`.

The skip order is project ignore files first, then `agents_tree_skip`, with `agents_tree_keep` only for rare explicit reviewed exceptions. Nothing in the scenario justifies reopening skipped paths.

### Content That Should Not Be Written

- Do not rewrite the human-maintained section.
- Do not add generated text that contradicts the human claim without a conflict block.
- Do not write a complete current caller list, consumer list, import list, or dependency map.
- Do not summarize all 30 files in `src/article/`.
- Do not store volatile implementation details of the new ranking output shape as durable guidance.
- Do not claim recommendation ranking is settled article ownership until the conflict is resolved.
- Do not add guidance for ignored or skipped directories.
- Do not advance `last_verified_commit` or report `VALID` while the conflict is unresolved.

### Stable Guidance After Conflict Resolution

After a human resolves the responsibility conflict, a narrow generated rule may be useful:

```md
Before changing `ArticleService` recommendation ranking output shapes, inspect current downstream feed consumers with the project code-intelligence tool.
```

That rule changes the next action and avoids a stale dependency map.

## Scenario B: Sidecar Decision-Router Mode

### Step-By-Step Action Sequence

1. Read root `AGENTS.md` first because it is the strict coding-agent behavior file and contains the sidecar discovery pointer.
2. Follow the pointer and read applicable `decision-router.md` files from the repository root toward `src/payments/`.
3. Read repository-root `decision-router.md` if it exists.
4. Read intermediate sidecar guidance such as `src/decision-router.md` if it exists.
5. Read `src/payments/decision-router.md` because it is the maintained Agents Tree decision-guidance artifact for the target module.
6. Read `src/payments/AGENTS.md` as applicable human-owned instruction text if normal AGENTS discovery or the root instructions make it relevant.
7. Treat `src/payments/AGENTS.md` as human-owned because it has no Agents Tree markers and is described as project instruction text.
8. Validate `src/payments/decision-router.md` metadata and managed sections before any generated guidance update.
9. Use code-intelligence tools to inspect the payment authorization boundary shared by `src/api/`.
10. Read `src/api`'s nearest applicable guidance artifact before editing across the API boundary. In sidecar mode this means `src/api/decision-router.md` if present, plus applicable `AGENTS.md` instruction files.
11. Update generated guidance only in `src/payments/decision-router.md` if the authorization change affects durable payments routing, first-hop, boundary, or verification guidance.
12. If native generated guidance and sidecar generated guidance overlap in the same directory, stop and ask whether to migrate, merge, or leave them separate.

### Files To Read First And Order

Minimum order:

1. Root `AGENTS.md`
2. Root `decision-router.md`, if it exists
3. Intermediate sidecar files on the path to payments, if they exist
4. `src/payments/decision-router.md`
5. `src/payments/AGENTS.md` for human-owned local instructions, not generated decision guidance
6. `src/api/decision-router.md` or nearest applicable API guidance if code-intelligence evidence confirms the shared boundary is impacted

The key point is that the sidecar pointer governs decision-guidance discovery, while `AGENTS.md` remains an instruction surface. Both may be read, but only the sidecar receives generated Agents Tree updates.

### Artifact To Update

Generated decision-guidance updates should go to:

- `src/payments/decision-router.md`

Do not write generated decision guidance into:

- `src/payments/AGENTS.md`

That file is human-owned instruction text without Agents Tree markers. It should be preserved unless the user explicitly asks to edit that human-owned file.

### Freshness Classification

`src/payments/decision-router.md`: likely `STALE_WARNING` or `INVALID`, depending on evidence.

Classify as `INVALID` if the authorization change alters recorded critical files, critical symbols, ownership boundaries, exported API shape, execution flow, or shared authorization semantics enough that existing generated guidance should not be trusted.

Classify as `STALE_WARNING` only if related evidence changed but focused code-intelligence review shows the existing generated claims remain mostly usable.

Do not classify as `VALID` until the recorded critical files, critical symbols, and relevant current authorization boundary evidence have been checked. Do not invent a commit hash or advance `last_verified_commit` without that evidence.

`src/payments/AGENTS.md`: not an Agents Tree maintained artifact in this scenario. Do not classify it as generated decision guidance.

### What Must Not Be Added To `src/payments/AGENTS.md`

- Agents Tree YAML front matter.
- `agents-tree:generated`, `agents-tree:human`, or `agents-tree:conflict` sections for generated decision guidance.
- Generated first-hop, skip, cross-module, or verification routing rules.
- `last_verified_commit`, `critical_files`, `critical_symbols`, `agents_tree_keep`, or `agents_tree_skip` metadata.
- Sidecar discovery metadata.
- Any rewrite that weakens, relocates, or normalizes human-owned instructions without explicit user approval.

### Native And Sidecar Overlap

If generated native `AGENTS.md` guidance and sidecar `decision-router.md` guidance overlap in the same directory, stop and ask for a human decision:

- migrate native guidance into sidecar mode,
- merge sidecar guidance back into native mode,
- keep them separate for an explicit reason, or
- retire one artifact.

The agent must not maintain both as authoritative generated guidance for the same directory by default.

### Code-Intelligence Query Target

Before editing across the API boundary, query the real authorization boundary symbols and flows. Because the scenario does not name exact symbols, the target should be discovered rather than invented:

- the payment authorization entry point in `src/payments/`
- exported payment authorization result or request/response types consumed by `src/api/`
- API route handlers, adapters, or service calls that invoke payment authorization
- references from `src/api/` to the confirmed payment authorization symbol or exported type

A durable generated rule may name the confirmed symbol after evidence review. It should not list all current API callers.

### Skipped Paths

The scenario does not declare specific ignored or skipped directories for sidecar mode. Apply the target project's ignore files first, then any `agents_tree_skip` from applicable decision-router metadata. Do not use sidecar mode as permission to scan ignored build outputs, dependencies, vendored code, or secrets.

### Content That Should Not Be Written

- Do not add Agents Tree metadata to human-owned `AGENTS.md`.
- Do not treat sidecar selection as permission to edit strict root or local human-owned instruction files.
- Do not write live dependency maps between `src/payments/` and `src/api/`.
- Do not store all current API consumers as durable knowledge.
- Do not refresh generated guidance without validating section markers and current evidence.
- Do not choose native or sidecar authority silently when both overlap.
- Do not mark guidance `VALID` if the declared code-intelligence tool was required but could not be used and the generated claims were not otherwise fully evidenced.

### Stable Guidance After Evidence Review

Possible generated content for `src/payments/decision-router.md`, only after real symbol evidence and conflict/overlap checks:

```md
## Cross-Module Checks

- Before changing payment authorization behavior or exported authorization result shapes, query current references for the confirmed authorization boundary symbol and read the nearest applicable API guidance before editing `src/api/`.
```

```md
## First Hop Rules

- If the task changes payment authorization semantics, start from the confirmed payments authorization entry point, then inspect API adapters that consume its result shape.
```

These bullets route future agents to evidence. They do not freeze today's caller list.

## Overall Assessment

The scenario simulation supports the v0.2 framing. The skill is no longer just a documentation review tool; it gives an operational decision path for:

- choosing native versus sidecar artifacts,
- protecting human-owned instruction files,
- detecting human/code responsibility conflicts,
- blocking only affected guidance maintenance,
- using code-intelligence for cross-module impact,
- skipping ignored and explicitly skipped paths,
- and keeping generated guidance focused on next actions rather than encyclopedic summaries.

The remaining edge worth tightening in the docs is sidecar pointer editing: if the root `AGENTS.md` is strict and human-owned but lacks the sidecar pointer, selecting sidecar mode should not itself imply permission to modify that file. The agent should ask for explicit approval or report that discoverability is incomplete.
