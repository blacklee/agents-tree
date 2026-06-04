# Agents Tree Skill Scenario Simulation

Review date: 2026-06-02

Simulation role: a coding agent with the `agents-tree` skill installed.

Scenario:

- `src/article/` has 30 files.
- `src/article/AGENTS.md` already exists.
- The human section says `ArticleService` only reads articles.
- Current code evidence shows `ArticleService` also owns recommendation ranking.
- `critical_files` includes `src/article/ArticleService.ts`.
- The current task changes the recommendation ranking output shape in `ArticleService`.
- The output is consumed by `src/feed/`.
- The project has `.gitignore` entries for `dist/` and `node_modules/`.
- Root `AGENTS.md` has `agents_tree_skip: ["vendor/"]`.

## Executive Judgment

The `src/article/AGENTS.md` file should not be treated as authoritative after this task.

The changed file is recorded as critical evidence, the changed behavior crosses a module boundary into `src/feed/`, and the human-maintained claim directly conflicts with current code evidence. The correct agents-tree action is to record an unresolved conflict in `src/article/AGENTS.md` and stop refreshing that file's generated knowledge until a human resolves the responsibility claim.

This is not a case for silently rewriting the human section, weakening the human claim, or adding broad fallback guidance. The normal logic is to surface the contradiction.

## 1. AGENTS.md Files I Would Read

Initial instruction and scope pass:

1. Root `AGENTS.md`
   - Reason: it applies to the whole repository and contains `agents_tree_skip: ["vendor/"]`.
   - It also acts as the tree index and may route module-specific knowledge.

2. `src/article/AGENTS.md`
   - Reason: it is the nearest applicable knowledge file for the edited critical file `src/article/ArticleService.ts`.
   - It contains the human-maintained claim that must be preserved unless a human explicitly asks to edit it.

Cross-module impact pass:

3. `src/feed/AGENTS.md`, if it exists.
   - Reason: code-intelligence evidence shows `src/feed/` consumes the recommendation ranking output shape changed by `ArticleService`.
   - The skill's cross-module handoff rule says to read the impacted module's nearest `AGENTS.md` before editing across the boundary or relying on cross-module assumptions.

If a `src/AGENTS.md` also exists in the target project, I would read it as an intermediate parent before the module files. The scenario only states that root and article AGENTS files exist, so the minimum concrete set is root plus `src/article/AGENTS.md`, then `src/feed/AGENTS.md` if present.

## 2. Tools I Would Use For Cross-Module Impact

I would use language-aware/code-intelligence tools rather than plain text guessing:

- TypeScript language server references for `ArticleService`, the recommendation ranking method, and the exported output type.
- Call graph or symbol reference lookup for callers and downstream consumers.
- Import graph lookup from `src/feed/` back to `src/article/ArticleService.ts` or its exported types.
- Structural search for the specific output fields if the shape is destructured or passed through adapters.
- Git diff for the current task to identify exactly which critical symbol and data shape changed.
- Focused source reads only after the graph/reference tools identify the relevant files.

I would not write a live list of all current callers into `AGENTS.md`. The stable guidance belongs in `AGENTS.md`; live dependency discovery belongs in code-intelligence/search tools.

## 3. Should `src/article/AGENTS.md` Be Updated?

Yes, but only to record the unresolved conflict.

The update trigger is clear:

- `src/article/ArticleService.ts` is listed in `critical_files`.
- The current task changed a durable output shape.
- The output is consumed by another module.
- Existing human-maintained guidance says `ArticleService` only reads articles, while code evidence says it also performs recommendation ranking.

However, this does not permit refreshing the generated section as if the new responsibility were settled knowledge. The conflict must be recorded first. While the conflict remains unresolved, maintenance of `src/article/AGENTS.md` and its subtree is blocked.

Freshness classification for `src/article/AGENTS.md`: `INVALID`.

## 4. Should I Read `src/feed/AGENTS.md`?

Yes, if it exists.

The changed recommendation ranking output is consumed by `src/feed/`, so the task crosses a module boundary. The skill requires reading the impacted module's nearest `AGENTS.md` before editing across that boundary or summarizing stable handoff guidance.

Reading `src/feed/AGENTS.md` does not mean copying feed's implementation details into article's AGENTS file. It means checking whether feed has local rules, contracts, verification steps, or human-maintained guidance that affect the output shape change.

## 5. Conflict Block I Would Write

I would write this block in `src/article/AGENTS.md`, preserving the existing human section byte-for-byte.

```md
<!-- agents-tree:conflict:start -->
status: unresolved
detected_at_commit: <current-commit-or-working-tree>
detected_by: agent
conflict_type: human_generated_mismatch
related_files:
  - src/article/ArticleService.ts
  - src/feed/

# Unresolved Agents Tree Conflict

This `AGENTS.md` file contains unresolved project-knowledge conflict.

Do not rely on this file as authoritative guidance for this directory or its subtree until the conflict is resolved.

Human action required:

- review the human-maintained section
- review the generated section
- update one or both sides
- remove this conflict block, or mark it resolved with a short note

## Human Claim

The human-maintained section says `ArticleService` only负责读取文章.

## Code Evidence

Current code evidence in `src/article/ArticleService.ts` shows `ArticleService` also performs recommendation ranking. The current task changes the recommendation ranking output structure, and that output is consumed by `src/feed/`.

## Required Resolution

Decide whether `ArticleService` is now allowed to own recommendation ranking, whether ranking should move elsewhere, or whether the human-maintained guidance should be rewritten to describe the current responsibility boundary. Until this is resolved, do not refresh generated article knowledge or rely on this file as authoritative guidance for `src/article/` or its subtree.
<!-- agents-tree:conflict:end -->
```

If exact commit metadata is available, `<current-commit-or-working-tree>` should be replaced with the current `HEAD` SHA or a clear working-tree marker used by the project. I would not invent a commit hash.

## 6. Directories I Would Skip

The skip order is:

1. Apply project ignore files first.
2. Apply `agents_tree_skip` after project ignores.
3. Apply `agents_tree_keep` only for explicit reviewed exceptions.

For this scenario, skipped paths are:

- `dist/`
- `node_modules/`
- `vendor/`

`dist/` and `node_modules/` are skipped because `.gitignore` excludes them. `vendor/` is skipped because root `AGENTS.md` declares `agents_tree_skip: ["vendor/"]`.

I would not skip `src/feed/`, because it is a real downstream module implicated by the changed output shape.

## 7. Content That Should Not Be Written Into AGENTS.md

I would not write:

- A complete list of every current caller, import, reference, or consumer of `ArticleService`.
- A full cross-module dependency map between `src/article/` and `src/feed/`.
- Large summaries of all 30 files in `src/article/`.
- Implementation details of the new ranking output shape if they are still volatile or just part of the immediate change.
- Generated text that contradicts the human section without a conflict block.
- Any rewrite of the human-maintained section unless the user explicitly asks for that.
- Guidance for ignored directories such as `dist/`, `node_modules/`, or `vendor/`.
- Claims that recommendation ranking is stable article ownership unless the conflict is resolved and the claim is backed by current evidence.
- Test or verification claims that were not actually checked.

The stable handoff guidance that may belong in generated content after conflict resolution is narrow:

```md
Before changing `ArticleService` recommendation ranking output shapes, inspect downstream feed consumers with code-intelligence/reference tools.
```

That guidance is durable. A list of today's consumers is not.

## Concrete Step Sequence

1. Read root `AGENTS.md`.
2. Apply `.gitignore` exclusions for evidence discovery: skip `dist/` and `node_modules/`.
3. Apply root `agents_tree_skip`: skip `vendor/`.
4. Read `src/article/AGENTS.md`.
5. Parse front matter and section markers.
6. Notice `src/article/ArticleService.ts` is in `critical_files`.
7. Inspect the current Git diff for `src/article/ArticleService.ts`.
8. Use TypeScript/code-intelligence references for `ArticleService`, the ranking method, and its output type.
9. Follow real references into `src/feed/`.
10. Read `src/feed/AGENTS.md` if present.
11. Compare code evidence with the article human section.
12. Classify `src/article/AGENTS.md` as `INVALID` because human-maintained responsibility guidance conflicts with current code evidence.
13. Preserve human and generated sections.
14. Add the unresolved conflict block to `src/article/AGENTS.md`.
15. Do not refresh generated article knowledge or child AGENTS files under `src/article/` while the conflict is unresolved.
16. Report the conflict, evidence reviewed, skipped directories, and whether feed had relevant local guidance.

## Fitted `src/article/AGENTS.md` Fragment

This is the shape I would expect around the conflict. Existing generated and human content is represented only as placeholders because the skill must preserve real file content, especially the human section.

```md
---
knowledge_type: module
module: Article
last_verified_commit: <previous-verified-commit>
critical_files:
  - src/article/ArticleService.ts
critical_symbols:
  - ArticleService
confidence: medium
owner: ai-generated
agents_tree_keep: []
agents_tree_skip: []
---

<!-- agents-tree:generated:start -->
<!-- Existing generated content preserved while conflict is unresolved. -->
<!-- agents-tree:generated:end -->

<!-- agents-tree:human:start -->
<!-- Existing human-maintained content preserved byte-for-byte.
For this scenario, it includes the claim that ArticleService only负责读取文章. -->
<!-- agents-tree:human:end -->

<!-- agents-tree:conflict:start -->
status: unresolved
detected_at_commit: <current-commit-or-working-tree>
detected_by: agent
conflict_type: human_generated_mismatch
related_files:
  - src/article/ArticleService.ts
  - src/feed/

# Unresolved Agents Tree Conflict

This `AGENTS.md` file contains unresolved project-knowledge conflict.

Do not rely on this file as authoritative guidance for this directory or its subtree until the conflict is resolved.

Human action required:

- review the human-maintained section
- review the generated section
- update one or both sides
- remove this conflict block, or mark it resolved with a short note

## Human Claim

The human-maintained section says `ArticleService` only负责读取文章.

## Code Evidence

Current code evidence in `src/article/ArticleService.ts` shows `ArticleService` also performs recommendation ranking. The current task changes the recommendation ranking output structure, and that output is consumed by `src/feed/`.

## Required Resolution

Decide whether `ArticleService` is now allowed to own recommendation ranking, whether ranking should move elsewhere, or whether the human-maintained guidance should be rewritten to describe the current responsibility boundary. Until this is resolved, do not refresh generated article knowledge or rely on this file as authoritative guidance for `src/article/` or its subtree.
<!-- agents-tree:conflict:end -->
```

## Fitted `src/feed/AGENTS.md` Handling

I would not update `src/feed/AGENTS.md` just because it consumes the changed output shape.

I would update it only if the feed module has durable guidance affected by the change, such as a stable contract saying feed expects a specific recommendation ranking output. If it has no such durable local guidance, the correct action is to read it for constraints, update code/tests as needed, and leave feed's AGENTS file untouched.

Possible generated guidance after evidence review, only if this is a stable local rule:

```md
<!-- agents-tree:generated:start -->
## Cross-Module Checks

When changing feed recommendation rendering or ranking input handling, inspect the current `ArticleService` recommendation output contract with code-intelligence/reference tools before editing.
<!-- agents-tree:generated:end -->
```

This should not include a full article-to-feed dependency list.
