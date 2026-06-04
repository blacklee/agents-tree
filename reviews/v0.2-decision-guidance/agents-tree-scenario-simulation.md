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

The current v0.2 decision-guidance contract passes both scenario simulations when followed literally.

In native `AGENTS.md` mode, the article guidance cannot be treated as authoritative because human-maintained text says `ArticleService` only reads articles while current code evidence says it also owns recommendation ranking. The correct behavior is fail-closed guidance maintenance: preserve human text, preserve generated text, write or preserve an unresolved conflict block, avoid refreshing generated article guidance or child article guidance, and proceed with ordinary code work only from direct source and code-intelligence evidence.

In sidecar decision-router mode, generated decision guidance belongs in `decision-router.md`. The strict or human-owned `AGENTS.md` files remain instruction surfaces and must not receive Agents Tree metadata, managed generated sections, or generated routing rules. Native and sidecar generated guidance must not both be maintained for the same covered directory or overlapping ancestor/descendant subtree unless a human explicitly chooses migration, an intentional split, or overlap resolution and that decision is recorded before refreshing either side.

Token-saving value rating: `HIGH`.

The scenarios exercise the strongest current product contract: decision guidance is valuable only when it routes future agents toward current evidence without overwriting human-owned instructions, hiding stale evidence, or storing live dependency maps as durable knowledge.

## Scenario A: Native AGENTS.md Mode

### Step-By-Step Action Sequence

1. Read root `AGENTS.md` first for repository instructions, the decision index, and root metadata such as `agents_tree_skip`.
2. Read any applicable ancestor guidance such as `src/AGENTS.md` if it exists.
3. Read `src/article/AGENTS.md` because the task changes `src/article/ArticleService.ts`, which is recorded in `critical_files`.
4. Validate `src/article/AGENTS.md` front matter, owner, generated markers, human markers, unmanaged text preservation requirements, and any existing conflict block status before editing.
5. Apply project ignore files before evidence discovery, skipping `.gitignore` paths such as `dist/` and `node_modules/`.
6. Apply `agents_tree_skip` after project ignore files, skipping `vendor/` from root metadata.
7. Check recorded critical evidence even if it would otherwise match a skip rule; skip rules cannot hide freshness evidence.
8. Inspect current task evidence and current diff around `src/article/ArticleService.ts`.
9. Use code graph, language-server references, structural search, or another code-intelligence tool to inspect `ArticleService`, the recommendation ranking output shape, and current downstream references.
10. Follow the confirmed cross-module impact into `src/feed/`.
11. Read `src/feed/AGENTS.md` if it exists before editing across the feed boundary or writing any durable feed-related guidance claim.
12. Compare the human claim that `ArticleService` only reads articles with code evidence showing recommendation ranking ownership.
13. Classify `src/article/AGENTS.md` as `INVALID` for guidance authority.
14. Write or preserve an unresolved conflict block in `src/article/AGENTS.md`.
15. Do not refresh generated article guidance or child article guidance while the conflict is unresolved.
16. Continue ordinary code work, if needed, from direct source evidence rather than the conflicted guidance.

### Guidance Files To Read

- Root `AGENTS.md`: repository-level instructions, tree index, and root `agents_tree_skip`.
- `src/AGENTS.md`, if present: applicable ancestor guidance between root and `src/article/`.
- `src/article/AGENTS.md`: nearest maintained guidance for the changed critical file.
- `src/feed/AGENTS.md`, if present: nearest guidance for the impacted downstream module.

I would not read ignored or skipped directories during evidence discovery unless the human explicitly asks for a safe exact path in the current task.

### Cross-Module Tool Class

Use code graph, language-server references, structural search, or other code-intelligence tools before editing across the boundary or refreshing generated guidance.

Good query targets are:

- `ArticleService`
- the real recommendation ranking method or exported output type after confirming its symbol name
- current references to the ranking output shape
- current call/import paths from `src/feed/` to the article boundary

Focused `rg`, Git diff, source reads, and tests are supplementary evidence. They should not become a broad repo scan and should not be copied into durable guidance as a full caller or consumer list.

### Freshness Classification

`src/article/AGENTS.md`: `INVALID`.

Reasons:

- `src/article/ArticleService.ts` is recorded in `critical_files`.
- The current task changes a durable output shape owned by `ArticleService`.
- The changed output is consumed across the module boundary by `src/feed/`.
- Human-maintained guidance says `ArticleService` only reads articles.
- Current code evidence says `ArticleService` also owns recommendation ranking.

This is not a normal generated-section refresh. The file may receive a conflict block, but generated guidance should not be refreshed until a human resolves the responsibility contradiction.

`src/feed/AGENTS.md`, if present, is not automatically stale merely because feed consumes the changed shape. It should be read for local constraints and classified from its own metadata, critical evidence, and generated claims. Update it only if durable feed decision guidance actually changed.

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

The changed recommendation ranking output is consumed by `src/feed/`. Before editing across that boundary or writing durable handoff guidance, inspect current feed references with the project code-intelligence tool and read [src/feed/AGENTS.md](src/feed/AGENTS.md) if it exists.

## Required Resolution

Decide whether `ArticleService` is allowed to own recommendation ranking, whether ranking ownership should move elsewhere, or whether the human-maintained guidance should be updated. Until this is resolved, do not refresh generated article guidance or rely on this file as authoritative guidance for `src/article/` or its subtree.
<!-- agents-tree:conflict:end -->
```

`<current-commit-or-working-tree>` must be replaced by a real commit hash or an explicit working-tree evidence marker. Do not invent a commit hash.

### Skipped Paths

- `dist/`: skipped because it is ignored by `.gitignore`.
- `node_modules/`: skipped because it is ignored by `.gitignore`.
- `vendor/`: skipped because root `AGENTS.md` declares `agents_tree_skip: ["vendor/"]`.

Project ignore files apply before `agents_tree_skip`. `agents_tree_keep` can reopen only rare, reviewed, safe exceptions and cannot reopen secrets, dependencies, build outputs, generated artifacts, or vendored code unless the human explicitly asks for that exact path in the current task.

### Content That Should Not Be Written

- Do not rewrite the human-maintained section.
- Do not refresh generated article guidance around the contradiction.
- Do not add generated text that contradicts the human claim without a conflict block.
- Do not write a complete current caller list, consumer list, import list, method inventory, API map, or dependency graph.
- Do not summarize all 30 files in `src/article/`.
- Do not store volatile implementation details of the new ranking output shape as durable guidance.
- Do not claim recommendation ranking ownership is settled until the conflict is resolved.
- Do not add guidance for ignored or skipped directories.
- Do not advance `last_verified_commit` or report `VALID` while the conflict is unresolved.

### Stable Guidance After Conflict Resolution

After a human resolves the responsibility conflict, a narrow generated rule may belong in `src/article/AGENTS.md`:

```md
## Cross-Module Checks

- Before changing `ArticleService` recommendation ranking output shapes, inspect current downstream feed consumers with the project code-intelligence tool and read the nearest applicable feed guidance before editing `src/feed/`.
```

That rule changes the next action without freezing today's dependency list.

## Scenario B: Sidecar Decision-Router Mode

### Step-By-Step Action Sequence

1. Read root `AGENTS.md` first because it is the strict coding-agent behavior file and contains the sidecar discovery pointer.
2. Establish the repository root that owns both the maintained sidecar artifact and target code. If the owner root is ambiguous because of nested repositories, package roots, or submodules, report the ambiguity and do not classify sidecar guidance as `VALID` until it is resolved.
3. Follow the root pointer and read applicable `decision-router.md` files from that repository root toward `src/payments/`.
4. Read repository-root `decision-router.md` if it exists.
5. Read intermediate sidecar guidance such as `src/decision-router.md` if it exists.
6. Read `src/payments/decision-router.md` because it is the maintained Agents Tree decision-guidance artifact for the target module.
7. Read `src/payments/AGENTS.md` as human-owned local instruction text if normal instruction discovery or the root instructions make it relevant.
8. Treat `src/payments/AGENTS.md` as human-owned because the scenario describes it as project instruction text without Agents Tree markers.
9. Validate `src/payments/decision-router.md` metadata, generated markers, human markers, placeholders, and conflict block status before any generated guidance update.
10. Check whether native generated `AGENTS.md` guidance overlaps with the sidecar guidance in the same directory or by ancestry across the same covered subtree.
11. If overlap exists, stop before refreshing either side and ask the human whether to migrate, merge, intentionally split, or retire one artifact; record that decision in the nearest authoritative guidance artifact before continuing.
12. Use code-intelligence tools to inspect the payment authorization boundary shared by `src/api/`.
13. Read `src/api`'s nearest applicable guidance artifact before editing across the API boundary. In sidecar mode this means applicable `decision-router.md` files for `src/api/`, plus applicable `AGENTS.md` instruction files.
14. Update generated guidance only in `src/payments/decision-router.md` if the authorization change affects durable payments routing, first-hop, boundary, skip, or verification guidance and no conflict or overlap block remains.

### Files To Read First And Order

Minimum order:

1. Root `AGENTS.md`
2. Root `decision-router.md`, if it exists
3. Intermediate sidecar files on the path to payments, if they exist
4. `src/payments/decision-router.md`
5. `src/payments/AGENTS.md` for human-owned local instructions, not generated decision guidance
6. `src/api/decision-router.md` or nearest applicable API guidance if code-intelligence evidence confirms the shared boundary is impacted

The sidecar pointer governs decision-guidance discovery. `AGENTS.md` remains an instruction surface. Both may be read, but only the sidecar receives generated Agents Tree decision-guidance updates in this scenario.

### Artifact To Update

Generated decision-guidance updates should go to:

- `src/payments/decision-router.md`

Do not write generated decision guidance into:

- `src/payments/AGENTS.md`

That file is human-owned instruction text without Agents Tree markers. It should be preserved unless the user explicitly asks to edit that human-owned file.

### Freshness Classification

`src/payments/decision-router.md`: `STALE_WARNING`, `INVALID`, or cannot verify depending on actual evidence.

Classify as `INVALID` if the authorization change alters recorded critical files, critical symbols, ownership boundaries, exported API shape, execution flow, or shared authorization semantics enough that existing generated guidance should not be trusted.

Classify as `STALE_WARNING` only if related evidence changed but focused code-intelligence review shows the existing generated claims remain mostly usable.

Classify as cannot verify or `INVALID` if the owner repository root is ambiguous, required code-intelligence evidence cannot be checked, critical files or symbols are missing, placeholders remain, markers are malformed, or native/sidecar overlap is unresolved.

Do not classify as `VALID` until every recorded critical file and symbol plus the current authorization boundary evidence has been checked. Do not invent a commit hash or advance `last_verified_commit` without committed evidence.

`src/payments/AGENTS.md`: not an Agents Tree maintained artifact in this scenario. Do not classify it as generated decision guidance.

### What Must Not Be Added To `src/payments/AGENTS.md`

- Agents Tree YAML front matter.
- `agents-tree:generated`, `agents-tree:human`, or `agents-tree:conflict` sections for generated decision guidance.
- Generated first-hop, skip, cross-module, or verification routing rules.
- `last_verified_commit`, `critical_files`, `critical_symbols`, `agents_tree_keep`, or `agents_tree_skip` metadata.
- Sidecar discovery metadata.
- Any rewrite, wrapping, relocation, trimming, or normalization of human-owned instructions without explicit user approval.

### Native And Sidecar Overlap

If generated native `AGENTS.md` guidance and sidecar `decision-router.md` guidance overlap in the same directory, stop before refreshing either artifact. Ask for a human decision to migrate, merge, intentionally split, or retire one artifact, then record that decision in the nearest authoritative guidance artifact before continuing.

If generated native and sidecar guidance overlap by ancestry across the same covered subtree, apply the same rule. A root native generated decision guide plus a descendant sidecar generated guide is still an authority overlap unless the human has explicitly approved and recorded an intentional split or migration plan.

The agent must not silently choose which generated guidance is authoritative, and must not maintain both as authoritative generated guidance for the same covered subtree by default.

### Code-Intelligence Query Target

Before editing across the API boundary, query the real authorization boundary symbols and flows. Because the scenario does not name exact symbols, the targets should be discovered rather than invented:

- the payment authorization entry point in `src/payments/`
- exported payment authorization result or request/response types consumed by `src/api/`
- API route handlers, adapters, or service calls that invoke payment authorization
- references from `src/api/` to the confirmed payment authorization symbol or exported type

A durable generated rule may name the confirmed symbol after evidence review. It should not list all current API callers.

### Skipped Paths

The scenario does not declare specific ignored or skipped directories for sidecar mode. Apply the target project's ignore files first, then any `agents_tree_skip` from applicable decision-router metadata. Do not use sidecar mode as permission to scan ignored build outputs, dependencies, vendored code, generated artifacts, or secrets.

### Content That Should Not Be Written

- Do not add Agents Tree metadata to human-owned `AGENTS.md`.
- Do not treat sidecar selection as permission to edit strict root or local human-owned instruction files.
- Do not modify a strict, human-owned, or unmarked root `AGENTS.md` to add or change the sidecar pointer without explicit approval.
- Do not write live dependency maps between `src/payments/` and `src/api/`.
- Do not store all current API consumers as durable knowledge.
- Do not refresh generated guidance without validating section markers, conflict status, placeholders, metadata, and current evidence.
- Do not choose native or sidecar authority silently when both overlap.
- Do not mark guidance `VALID` if the owner root is ambiguous or the required code-intelligence evidence was unavailable, stale, or partial and the generated claims were not otherwise fully re-evidenced.

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

The rerun supports the current v0.2 framing and shows the latest skill update has closed the main sidecar ambiguity from the earlier simulation.

The current contract now gives an operational decision path for:

- choosing native mode by default and using sidecar mode only with an explicit project reason,
- requiring a discoverability pointer while protecting strict or human-owned root `AGENTS.md` files from unapproved edits,
- establishing the sidecar owner repository root before claiming validity,
- detecting same-directory and ancestor/descendant native-sidecar overlap,
- protecting human-owned instruction files from generated decision guidance,
- detecting human/code responsibility conflicts,
- blocking only affected guidance maintenance,
- using code-intelligence for cross-module impact,
- checking critical evidence even when skip rules would hide it,
- and keeping generated guidance focused on next actions rather than encyclopedic summaries.

No new scenario failure was found in this rerun. The main residual risk is operational rather than contractual: agents must actually perform the code-intelligence attempt and owner-root check instead of treating the written guidance as self-validating.
