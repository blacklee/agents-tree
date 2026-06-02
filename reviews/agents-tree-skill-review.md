# Agents Tree Skill Review

Review date: 2026-06-02

Scope reviewed:

- `README.md`
- `README.zh.md`
- `AGENTS.md`
- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`

## Critical Issues

1. **Freshness classification is directionally clear but not yet operational enough.**
   - Evidence: `skills/agents-tree/SKILL.md` defines `VALID`, `STALE_WARNING`, and `INVALID`, and `skills/agents-tree/references/file-contract.md` repeats the labels, but neither gives an executable decision procedure.
   - Risk: Agents may classify by intuition, especially around partial diffs, renamed files, deleted symbols, changed callers, or changed flows.
   - Why it matters: The project explicitly says stale knowledge is worse than missing knowledge. Without a stricter classifier, future agents may trust stale `AGENTS.md` files or rescan too broadly, both of which undermine the skill.

2. **Generated claims are required to be traceable, but the file contract does not provide a lightweight trace format.**
   - Evidence: `skills/agents-tree/SKILL.md` says generated claims must be traceable to files, symbols, imports, execution flows, tests, or explicit human notes. `AGENTS.md` says every generated claim should be traceable. The templates only provide prose sections and metadata lists.
   - Risk: Agents will either omit evidence, overstuff prose with citations, or repeatedly re-read code because the claim-to-evidence link is implicit.
   - Why it matters: This is the biggest gap between the product promise and actual token reduction. A small evidence convention is needed so future agents can verify only the right facts.

## Important Issues

1. **Create / Check / Refresh mode selection needs a sharper decision table.**
   - Current state: `skills/agents-tree/SKILL.md` lists four modes and update triggers, but the workflow starts with “Identify the target project and requested mode.”
   - Gap: Users will often say “看看这个模块需不需要 AGENTS.md” or “这个 AGENTS.md 还可信吗,” and the agent needs a deterministic way to choose Create vs Check vs Refresh vs Review.
   - Suggested improvement: Add a compact “Mode Selection” section to `skills/agents-tree/SKILL.md`.

2. **Conflict block variants are semantically aligned but structurally inconsistent.**
   - Current state: `README.md`, `README.zh.md`, `AGENTS.md`, and `SKILL.md` show the short conflict block. `file-contract.md` shows an expanded block with `detected_at_commit`, `detected_by`, `conflict_type`, `related_files`, `Human Claim`, `Code Evidence`, and `Required Resolution`.
   - Risk: Agents may create different conflict block shapes. That weakens machine-readability and makes review harder.
   - Suggested improvement: Make the expanded structure the canonical block in `file-contract.md`, and make the short block explicitly “minimum visible body” in overview docs.

3. **Unresolved conflict behavior is safe, but “subtree block” boundaries need one example.**
   - Current state: The docs correctly say unresolved conflicts block maintenance for that file and subtree, not unrelated code work or unrelated nodes.
   - Gap: There is no example for sibling directories, parent indexes, or code work inside the conflicted subtree that does not rely on the conflicted guidance.
   - Risk: Conservative agents may over-block, while aggressive agents may refresh descendants under a conflicted parent.

4. **Non-installed agents can understand conflict state, but not freshness state.**
   - Current state: Conflict block prose is visible and understandable without the skill installed. Freshness relies mostly on YAML fields such as `last_verified_commit`, `critical_files`, and `critical_symbols`.
   - Gap: A generic AGENTS-aware agent may not know whether an old `last_verified_commit` is stale, warning-level stale, or invalid.
   - Suggested improvement: Add a short visible generated subsection such as `## Knowledge Status` in templates, with plain-language guidance: last verified commit, confidence, and when to re-check.

5. **The skill says to use code graph / code-intelligence tools, but does not define what to do when they are available vs unavailable.**
   - Current state: Cross-module guidance correctly delegates callers, callees, references, and impact to code graph/code-intelligence tools.
   - Gap: The fallback path is “focused reads and Git history,” but there is no bounded procedure for missing tools.
   - Risk: Agents may either do broad scans or invent impact. This is not “兜底代码,” but the process still needs a safe no-tool review path.

## Minor Issues

1. `README.md` says “four constraints” but lists five bullets under “What Makes It Different.”
2. `README.zh.md` says “它主打四件事” but lists five bullets.
3. `README.md` and `README.zh.md` list `skills/agents-tree/agents/openai.yaml`, but that file is not present in the reviewed file list.
4. `README.md` metadata example omits `agents_tree_keep` and `agents_tree_skip`, while `AGENTS.md`, `file-contract.md`, and templates include them.
5. `skills/agents-tree/references/maintenance-workflow.md` places existing `AGENTS.md` before ignore files in evidence collection. That is fine for reading instructions, but for scanning candidate files the ignore files should be applied before path discovery.
6. Asset templates are useful but a little too heading-heavy. Empty headings are discouraged in `file-contract.md`, yet templates include many headings by default.

## Missing Scenarios

1. **No Git repository or shallow checkout**
   - What should `last_verified_commit` be when there is no usable commit SHA?
   - Should the file be allowed with `confidence: low`, or should creation/check be blocked?

2. **Initial creation when root `AGENTS.md` already exists**
   - How should the agent merge the managed sections into an existing normal `AGENTS.md`?
   - Where should human text go if it predates the markers?

3. **Human text outside managed human markers**
   - The contract says human-maintained content must live inside markers, but real repositories may already have unmarked `AGENTS.md` files.
   - The skill needs a safe migration rule: preserve existing text, wrap it, or ask before normalizing.

4. **Deleted or renamed critical files / symbols**
   - The docs mention this as invalidating evidence, but do not specify whether to keep the old metadata, remove stale entries, or write a conflict block.

5. **Parent / child instruction conflict**
   - `maintenance-workflow.md` lists this as a conflict, but the file contract mainly frames conflicts as human-vs-generated.
   - The conflict block should support `conflict_type: parent_child_mismatch`.

6. **Generated section absent or malformed**
   - The skill should say whether to repair markers, refuse refresh, or ask for human review when marker pairs are missing, nested, duplicated, or out of order.

7. **Refresh that changes scope**
   - If a module split/merge means the current `AGENTS.md` belongs at a different directory level, the workflow should say whether to mark invalid, move the file, create children, or ask for review.

8. **Evidence changed outside `critical_files` / `critical_symbols`**
   - The docs warn not to trust unchanged filenames, but the workflow needs an explicit path for dependency or flow changes found by code-intelligence tools.

## Suggested Edits With Exact File / Section

1. **`skills/agents-tree/SKILL.md` / after `## Operating Modes`**
   - Add a compact mode-selection table:
     - User asks to add missing guidance, repeated scans, or new tree: `Create`.
     - User asks whether current knowledge is trustworthy: `Check`.
     - User asks to update generated knowledge after evidence changed: `Refresh`.
     - User asks to evaluate proposed AGENTS changes: `Review`.
     - If wording is check/review/analyze only, report only unless the user explicitly asks to write.

2. **`skills/agents-tree/SKILL.md` / `## Freshness Labels`**
   - Add a decision checklist:
     - Missing/malformed metadata or markers: `INVALID`.
     - Deleted/renamed critical file or missing critical symbol: `INVALID`.
     - Changed critical symbol signature, ownership boundary, entry point, or execution flow: `INVALID`.
     - Changed critical file but same responsibility and same key flows after focused evidence review: `STALE_WARNING`.
     - No relevant evidence changed after checking recorded evidence and current references/flows where applicable: `VALID`.
     - Unknown due to insufficient evidence: report as `INVALID` or “cannot verify” rather than `VALID`.

3. **`skills/agents-tree/references/file-contract.md` / `## Front Matter`**
   - Add a lightweight evidence convention, for example:
     ```yaml
     evidence:
       - claim: local_responsibility
         files: []
         symbols: []
         flows: []
     ```
   - Keep it optional but recommended for generated files with more than a few claims.

4. **`skills/agents-tree/assets/*.AGENTS.md` / generated section start**
   - Add `## Knowledge Status` near the top:
     - last verified commit
     - confidence
     - critical evidence summary
     - plain-language rule: “If these files, symbols, or flows changed, re-check before trusting this file.”

5. **`skills/agents-tree/references/file-contract.md` / `## Conflict Sections`**
   - Declare one canonical conflict shape.
   - Include allowed `conflict_type` values: `human_generated_mismatch`, `parent_child_mismatch`, `missing_critical_evidence`, `scope_mismatch`.
   - State that overview docs may show a shortened excerpt, but generated files should prefer the canonical shape.

6. **`skills/agents-tree/references/maintenance-workflow.md` / `## Conflict Handling`**
   - Add examples:
     - A conflict in `src/payments/AGENTS.md` blocks refreshing `src/payments/**/AGENTS.md`.
     - It does not block `src/search/AGENTS.md`.
     - It does not block unrelated code work, but agents must not rely on the conflicted AGENTS guidance.

7. **`skills/agents-tree/references/maintenance-workflow.md` / `## Evidence Collection`**
   - Split “instruction reading” from “file discovery”:
     - Read nearest existing `AGENTS.md` for instructions.
     - Apply ignore files before discovering candidate evidence files.
     - Apply `agents_tree_skip` after project ignore files.
     - Apply `agents_tree_keep` only for rare reviewed exceptions.

8. **`README.md` / `## What Makes It Different` and `README.zh.md` / `## 和普通 Agent Memory 的区别`**
   - Change “four constraints” / “四件事” to “five constraints” / “五件事,” or merge “Agent compatibility” and “Skill-based maintenance” into one bullet.

9. **`README.md` / `## Skill Package` and `README.zh.md` / `## Skill 包结构`**
   - Remove `agents/openai.yaml` from the tree unless the file is added.

10. **`skills/agents-tree/assets/*.AGENTS.md`**
    - Reduce default headings or mark some as optional placeholders.
    - This keeps templates aligned with `file-contract.md`, which says empty headings waste context.

## Answers To Review Goals

1. **Trigger conditions:** Mostly clear. The skill description and operating modes are good, but mode selection needs a table for ambiguous user phrasing.
2. **When to create/check/refresh:** Directionally clear, not yet deterministic enough for independent agents.
3. **Human/generated/conflict rules:** Clear in principle. Generated and human sections are strong; conflict shape needs canonical consistency.
4. **Unresolved conflict safety:** Good and appropriately scoped. Needs examples to prevent over-blocking or under-blocking.
5. **Keep/skip:** Good and lightweight. It correctly respects existing ignore files first.
6. **Cross-module relationships:** Good. The docs correctly assign live dependency impact to code graph/code-intelligence tools instead of `AGENTS.md`.
7. **Contradictions/repetition/abstraction:** No fatal contradiction. There is some duplication across README, SKILL, and references, plus minor count/package inconsistencies.
8. **Non-installed agent readability:** Conflict state is readable. Freshness state needs more visible plain-language status inside generated content.
9. **Token reduction vs doc burden:** Promising, but only if evidence traceability and freshness checks become more mechanical. Otherwise agents may spend extra tokens verifying vague documentation.

## Overall Readiness Score

**7 / 10**

The skill is conceptually strong and already has the right product boundaries: directory scope, protected human sections, conflict safety, lightweight keep/skip, and code-intelligence handoff. It is not yet “fully executable” because freshness classification and claim evidence are under-specified. Fixing those two areas would likely move it to 8.5+ without adding a CLI or heavyweight automation.
