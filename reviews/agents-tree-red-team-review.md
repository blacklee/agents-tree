# Agents Tree Skill Red-Team Review

Review date: 2026-06-02

Scope reviewed:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`
- `README.md`
- `README.zh.md`
- existing review notes in `reviews/`

Review goal: find scenarios where the current Agents Tree skill may cause an agent to do the wrong thing, waste tokens, delete human-maintained content, block work incorrectly, or generate stale project knowledge.

## Executive Summary

The current documentation has the right product boundaries: generated sections are managed, human sections are protected, conflict blocks exist, and stale knowledge is treated as dangerous. The main red-team finding is that several rules are still stated as principles rather than mechanical procedures. That leaves room for agents to improvise in exactly the places where the project needs determinism: marker parsing, freshness classification, pre-existing `AGENTS.md` migration, conflict boundaries, and evidence scope.

The highest-risk gaps are:

1. pre-existing or malformed `AGENTS.md` files can lead to accidental human-content deletion;
2. stale metadata can be advanced without enough evidence;
3. conflict blocks can over-block unrelated maintenance or under-block child refreshes;
4. agents may spend large token budgets rereading broad code areas because the bounded evidence procedure is underspecified.

## Failure Scenarios

### 1. Pre-existing `AGENTS.md` Has No Managed Markers

Scenario:

A target repository already has a normal `AGENTS.md` written by humans. It contains project rules, setup notes, and warnings, but it has no `agents-tree:generated` or `agents-tree:human` markers. The user asks the agent to "create an Agents Tree for this repo." The agent treats the existing file as a template target, adds front matter, and replaces the body with generated sections.

Current docs already prevent this?

Partially. The docs say human-maintained content must live inside human markers and generated content must live inside generated markers. They do not define a migration rule for existing unmarked human text.

What happens if not prevented:

- Human-written repository instructions may be deleted or moved into generated text.
- The next agent may treat old human rules as generated knowledge and rewrite them during refresh.
- Reviewers see a normal-looking generated diff instead of a clear "we are migrating existing human content" operation.

Rule to add:

If an existing `AGENTS.md` lacks managed markers, treat all existing body content as human-maintained by default. Preserve it byte-for-byte unless the user explicitly asks to normalize it. A create or refresh pass may only add missing managed sections around or after the preserved content after reporting the migration.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 2. Generated/Human Markers Are Malformed

Scenario:

An `AGENTS.md` file has duplicated `agents-tree:generated:start` markers, a missing `agents-tree:human:end`, or nested generated and human markers. The user asks for a refresh. The agent uses a naive text replacement and updates everything between the first generated start and the last generated end.

Current docs already prevent this?

No. The contract defines valid marker shapes but does not say what to do when markers are missing, duplicated, nested, or out of order.

What happens if not prevented:

- Human-maintained content can be overwritten.
- Generated and human ownership boundaries become ambiguous.
- The agent may falsely report a clean refresh even though the file contract is invalid.

Rule to add:

Before editing, parse section markers and require exactly one well-ordered generated section and at most one well-ordered human section. If markers are malformed, do not edit generated content. Classify the file as `INVALID`, report the marker problem, and require human repair or explicit normalization approval.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`

### 3. `last_verified_commit` Is Advanced Without Real Evidence

Scenario:

The agent updates prose in the generated section and sets `last_verified_commit` to current `HEAD`, but it only read the old `AGENTS.md` and one nearby file. It did not check recorded `critical_files`, `critical_symbols`, renamed files, or changed execution flows.

Current docs already prevent this?

Partially. The docs say metadata should be updated only after generated claims are checked, and stale knowledge is worse than missing knowledge. They do not define a minimum evidence checklist for advancing `last_verified_commit`.

What happens if not prevented:

- Stale or partially checked knowledge receives fresh-looking metadata.
- Later agents trust the file and skip source inspection.
- The project accumulates authoritative-looking but unverified architecture claims.

Rule to add:

Only advance `last_verified_commit` after checking every recorded critical file and symbol, plus any current diff or code-intelligence evidence that affects the generated claims. If any recorded evidence cannot be checked, do not mark the file `VALID` and do not advance the commit.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/references/file-contract.md`

### 4. Deleted Or Renamed Critical Evidence Is Silently Dropped

Scenario:

`critical_files` contains `src/payments/PaymentService.ts`. The file was renamed to `src/billing/PaymentService.ts`. During refresh, the agent cannot find the old path, removes it from metadata, writes a new summary from the renamed file, and marks the knowledge `VALID`.

Current docs already prevent this?

Partially. The docs say deleted or changed critical files can make knowledge `INVALID`, but they do not specify the required procedure for renames or missing critical evidence.

What happens if not prevented:

- The agent may hide evidence loss by rewriting metadata.
- A real scope move may be mistaken for a harmless rename.
- Parent and child `AGENTS.md` files may continue describing the old module boundary.

Rule to add:

If a critical file or symbol is missing, classify the file as `INVALID` until the agent verifies whether it was deleted, renamed, moved across module boundaries, or replaced by a new flow. Metadata may be changed only after the generated claims are re-evidenced against the new location and any scope change is handled.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 5. Conflict Block Over-Blocks Unrelated Work

Scenario:

`src/article/AGENTS.md` has an unresolved conflict. The user asks the agent to edit unrelated source code under `src/article/` after already providing the exact files and desired change. The agent refuses all code work because it interprets the conflict block as blocking the subtree entirely.

Current docs already prevent this?

Mostly, but not strongly enough. The docs say an unresolved conflict blocks knowledge-tree maintenance for that file and subtree, not unrelated code work. There are no concrete examples showing the distinction.

What happens if not prevented:

- Agents refuse valid code tasks.
- Users learn to remove conflict blocks just to keep work moving.
- The conflict mechanism becomes operationally expensive and loses trust.

Rule to add:

An unresolved conflict blocks relying on or refreshing that `AGENTS.md` file and descendant `AGENTS.md` files. It does not block ordinary code edits. During code work inside the conflicted subtree, the agent must ignore the conflicted AGENTS guidance and inspect source evidence directly.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 6. Conflict Block Under-Blocks Descendant Refresh

Scenario:

`src/payments/AGENTS.md` has an unresolved conflict about module ownership. A later agent refreshes `src/payments/refunds/AGENTS.md` because the child file itself has no conflict block.

Current docs already prevent this?

Mostly. The docs say an unresolved conflict blocks maintenance for the file and subtree. The risk is that the workflow does not require checking ancestors for conflict blocks before refreshing a child.

What happens if not prevented:

- A child `AGENTS.md` can be refreshed using a parent boundary that is explicitly unresolved.
- Descendant knowledge may become inconsistent with the pending human decision.
- Reviewers see the child diff without noticing the ancestor conflict.

Rule to add:

Before refreshing any `AGENTS.md`, read all applicable ancestor `AGENTS.md` files and stop if any ancestor has an unresolved conflict that covers the target path. Report the ancestor file and continue only with unrelated nodes.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 7. Parent And Child Instructions Conflict Without A Human Section

Scenario:

Root `AGENTS.md` says all API modules must use generated clients. A child generated section says this module should manually construct HTTP requests. Neither statement is in a human section, so the agent does not use the human-vs-generated conflict rule.

Current docs already prevent this?

Partially. `maintenance-workflow.md` lists parent/child incompatible instructions as conflicts, but `SKILL.md` and the conflict template primarily frame conflicts as human text versus generated knowledge.

What happens if not prevented:

- Agents follow whichever file they happened to read last.
- Refresh may rewrite one generated section without surfacing that the tree gives incompatible instructions.
- Future agents receive directory-scoped guidance that is locally clear but globally contradictory.

Rule to add:

Parent/child instruction mismatches are first-class conflicts even when both claims are generated. Use `conflict_type: parent_child_mismatch`, record both files, and block maintenance only for the affected node/subtree until the mismatch is resolved.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 8. Code-Intelligence Tool Missing Causes Broad Repo Scan

Scenario:

The skill says to use a code graph or code-intelligence tool for cross-module impact. The current environment has no such tool. The agent compensates by scanning the entire repository, reading many callers, package files, and tests before writing a small `AGENTS.md` update.

Current docs already prevent this?

Partially. The docs say to avoid broad scans and prefer focused reads, but they do not give a bounded no-code-intelligence procedure.

What happens if not prevented:

- The workflow wastes the same token budget it is supposed to save.
- The agent may summarize too much live dependency information into `AGENTS.md`.
- A small refresh becomes slower and less reviewable than ordinary source inspection.

Rule to add:

When code-intelligence tools are unavailable, use a bounded evidence sequence: current diff, recorded critical files, recorded critical symbols, nearest imports/exports, and focused textual references for named symbols only. Stop after enough evidence exists to classify freshness or report that validity cannot be established.

Files to change:

- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/SKILL.md`

### 9. Live Caller Lists Are Written As Durable Knowledge

Scenario:

While refreshing a module, the agent finds 18 current callers of a function and writes them all into `AGENTS.md` as an "Architecture Map." A week later the caller list changes, but no one updates the file because the function itself did not change.

Current docs already prevent this?

Mostly. The docs repeatedly say not to store full cross-module dependency maps or live caller lists. The template still includes headings such as `Architecture`, `Architecture Map`, and `Cross-Module Checks`, which can tempt agents to fill them with current references.

What happens if not prevented:

- `AGENTS.md` becomes a stale dependency database.
- Agents trust outdated caller lists and miss real downstream impact.
- The root or module file grows into an encyclopedia.

Rule to add:

Generated sections must not list current callers, callees, imports, or references unless the relationship is an intentionally stable contract. Prefer guidance of the form "inspect current references before changing this interface" over enumerating today's references.

Files to change:

- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 10. `agents_tree_keep` Reopens Sensitive Or Ignored Paths

Scenario:

A generated root file contains `agents_tree_keep: ["secrets/**/*.json", "vendor/**"]` because an agent thought those files were useful evidence. Future agents read ignored secrets or huge vendored directories because the keep rule says they must remain visible.

Current docs already prevent this?

Partially. The docs say keep/skip lists should be small and project ignore files should be respected first. They do not explicitly forbid using keep rules to reopen sensitive paths or large ignored trees.

What happens if not prevented:

- Agents may inspect sensitive files.
- Token usage can explode on vendored or generated content.
- A local metadata field can accidentally override project-level safety intent.

Rule to add:

`agents_tree_keep` must not override ignore rules for secrets, credentials, generated artifacts, dependency directories, build outputs, or vendored code unless a human explicitly asks for that exact path in the current task. Broad keep globs are invalid.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 11. Freshness State Is Not Visible To Ordinary AGENTS-Aware Agents

Scenario:

An agent without the Agents Tree skill reads a generated `AGENTS.md`. The front matter has an old `last_verified_commit`, but the body looks authoritative. The ordinary agent follows stale guidance because it does not know the freshness protocol.

Current docs already prevent this?

No. Conflict blocks are visible and self-explanatory, but freshness status is mostly encoded in metadata and skill docs.

What happens if not prevented:

- Non-installed agents trust stale project knowledge.
- The compatibility goal is weakened because ordinary `AGENTS.md` readers cannot tell when to re-check.
- Stale knowledge stays quiet instead of being surfaced clearly.

Rule to add:

Generated files should include a short visible `Knowledge Status` section near the top of the generated block. It should state the last verified commit, confidence, critical evidence, and a plain-language instruction to re-check if those files, symbols, or flows changed.

Files to change:

- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`
- `skills/agents-tree/references/file-contract.md`

### 12. Templates Encourage Empty Headings And Token Bloat

Scenario:

The agent copies `module.AGENTS.md` and fills every heading, even when only two sections are useful. The result includes shallow prose under `Architecture`, `Entry Points`, `Common Tasks`, `Rules`, `Do Not`, and `Verification`.

Current docs already prevent this?

Partially. `file-contract.md` says empty headings waste context and `SKILL.md` says generated files should be short. The templates still present many headings as if they are expected.

What happens if not prevented:

- Root files become encyclopedias.
- Future agents spend tokens reading low-value filler.
- Weak generated claims become harder to verify because they are spread across many sections.

Rule to add:

Templates should mark optional sections explicitly and instruct agents to delete unused headings. Root files should be limited to routing/index guidance; leaf files may include implementation detail only when backed by stable evidence.

Files to change:

- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`
- `skills/agents-tree/references/file-contract.md`

### 13. User Says "Check" But Agent Refreshes Anyway

Scenario:

The user asks, "看看这个 AGENTS.md 还可信吗?" The agent finds stale generated knowledge and immediately edits the file to refresh it.

Current docs already prevent this?

Mostly. `SKILL.md` says if the user only asks to check, inspect, analyze, or review, do not edit files unless explicitly approved. The rule is strong, but it is worth duplicating in the detailed workflow because check and refresh are adjacent operations.

What happens if not prevented:

- The agent changes files during an analysis-only request.
- Human review expectations are violated.
- A stale finding gets mixed with a generated update, making the diff harder to audit.

Rule to add:

In check mode, stop after reporting `VALID`, `STALE_WARNING`, or `INVALID`, evidence reviewed, and recommended next action. Do not modify `AGENTS.md` or metadata unless the user explicitly requested refresh/update/write.

Files to change:

- `skills/agents-tree/references/maintenance-workflow.md`

### 14. Whole-File Human Ownership Is Ambiguous

Scenario:

Front matter says `owner: human-maintained`, but the file also contains a generated section. The user asks for refresh. The agent updates the generated section because markers exist, ignoring the whole-file owner field.

Current docs already prevent this?

No. `file-contract.md` says `owner` is usually `ai-generated` and `human-maintained` only when the whole file is intentionally human-owned, but the workflow does not define precedence between `owner` and section markers.

What happens if not prevented:

- A human-owned file can be edited by the agent.
- Ownership metadata becomes decorative instead of authoritative.
- Users lose trust in the protection model.

Rule to add:

If `owner: human-maintained`, do not edit any part of the file unless the user explicitly asks to edit that human-owned file. Section markers do not override whole-file ownership. Report the owner state and recommend a human-reviewed conversion if generated maintenance is desired.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`

### 15. Scope Move Is Treated As A Normal Refresh

Scenario:

A module split moves half of `src/video/` into `src/media/`. The agent refreshes `src/video/AGENTS.md` in place and adds a note that some behavior is now in `src/media/`, instead of recognizing that the tree shape itself changed.

Current docs already prevent this?

Partially. The docs mention added, removed, renamed, or moved important module directories as update triggers. They do not define when a scope move should create, move, invalidate, or retire an `AGENTS.md` file.

What happens if not prevented:

- Old nodes keep routing agents to the wrong subtree.
- Root indexes become misleading.
- Generated knowledge mixes old and new module responsibilities.

Rule to add:

If a module split, merge, rename, or move changes directory ownership, classify affected files as `INVALID` first. Then update the tree shape explicitly: retire, move, or create AGENTS nodes only after verifying the new scope. Do not hide a scope move inside a normal generated-section refresh.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/maintenance-workflow.md`

## Priority Fix List

1. Add marker validation and unmarked-file migration rules before any refresh behavior.
2. Add a minimum evidence checklist for advancing `last_verified_commit`.
3. Make conflict types and ancestor-conflict checks explicit.
4. Add a bounded no-code-intelligence evidence procedure.
5. Add visible freshness status to templates for ordinary AGENTS-aware agents.
6. Tighten `agents_tree_keep` so it cannot reopen sensitive or huge ignored paths by accident.

## Files Most Worth Editing First

1. `skills/agents-tree/SKILL.md`: add operational rules for mode selection, marker validity, owner precedence, freshness advancement, and ancestor conflict checks.
2. `skills/agents-tree/references/file-contract.md`: canonicalize malformed markers, owner precedence, conflict types, freshness visibility, and keep/skip constraints.
3. `skills/agents-tree/references/maintenance-workflow.md`: add concrete procedures for unmarked migration, bounded evidence collection, conflict boundaries, and scope moves.
4. `skills/agents-tree/assets/*.AGENTS.md`: reduce optional headings and add visible `Knowledge Status`.
