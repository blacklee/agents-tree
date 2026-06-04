# Agents Tree Decision-Guidance Red-Team Review

Review date: 2026-06-04

Review environment:

- Runtime: local Codex
- Agent: Codex
- Model: GPT-5.5
- Reasoning effort: high
- Skill workflow: Superpowers SKILL (`using-superpowers`) and `agents-tree`
- Requested rerun: `2C`

Prompt used:

- `reviews/v0.2-decision-guidance/prompts/agents-tree-red-team-review.md`

Scope reviewed:

- `README.md`
- `README.zh.md`
- `INSTALL.md`
- `INSTALL.zh.md`
- `AGENTS.md`
- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/agents/openai.yaml`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`
- previous result file `reviews/v0.2-decision-guidance/agents-tree-red-team-review.md`

Review goal: find scenarios where the current Agents Tree decision-guidance skill may cause an agent to do the wrong thing, waste tokens, delete human-maintained content, block work incorrectly, choose the wrong artifact strategy, or generate stale decision guidance.

## Regression Status From Previous Review

The updated skill closes most of the previous high-risk gaps. The current docs now explicitly cover sidecar pointer approval, sidecar repository-root discovery, native/sidecar ancestor overlap, proposed-diff review, unavailable code-intelligence tools, dirty working-tree evidence, unresolved placeholders, one-time `agents_tree_keep` exceptions, `agents_tree_skip` not hiding critical evidence, conflict status parsing, audience-boundary uncertainty, README over-generation wording, and the OpenAI manifest's Review Mode / human-section preservation surface.

The remaining risks are now less about missing product principles and more about executable edge cases: where metadata is stored, how existing unmarked files are migrated, how placeholders in protected areas behave, how agents know a code-intelligence tool is actually declared, and how to avoid stale-but-valid-looking guidance when the guidance artifact itself or its evidence model is ambiguous.

## Failure Scenarios

### 1. Existing `AGENTS.md` Has Non-Agents Front Matter

Scenario:

A target repository already has an unmarked root `AGENTS.md` with YAML front matter used by another tool, documentation generator, or local convention. The agent treats the file as eligible for Agents Tree creation, inserts Agents Tree fields into the existing front matter, or replaces the existing front matter with the Agents Tree template.

Current docs already prevent this?

Partially. The contract says existing unmarked `AGENTS.md` body text is human-maintained and must be preserved byte-for-byte. It also says not to add Agents Tree fields to human-facing docs or tool-specific docs that already have front matter. It does not explicitly cover existing non-Agents front matter in `AGENTS.md` itself.

What happens if not prevented:

- The agent corrupts a human-owned metadata block while believing it only added required Agents Tree metadata.
- Another tool may stop parsing the file correctly.
- The file may appear contract-compliant while the original human-maintained metadata was altered.

Rule to add or clarify:

If an existing `AGENTS.md` has front matter that is not already an Agents Tree metadata block, treat that front matter as human-maintained. Do not merge, rewrite, or replace it without explicit approval. Prefer sidecar mode or a separate approved normalization step when Agents Tree metadata must be added.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/SKILL.md`

### 2. Initial Creation Adds YAML To A Strict Unmarked `AGENTS.md`

Scenario:

A project has a strict unmarked root `AGENTS.md` containing behavior instructions only. The user asks to "create an Agents Tree." The workflow says metadata belongs at the beginning of maintained artifacts and says adding metadata as front matter during unmarked migration is allowed if needed. The agent prepends YAML to the strict instruction file, changing the first content future agents see.

Current docs already prevent this?

Partially. Sidecar pointer edits to strict, human-owned, or unmarked root `AGENTS.md` require explicit approval. But initial native-mode creation still allows front matter insertion in an existing unmarked `AGENTS.md` without the same explicit approval gate.

What happens if not prevented:

- The strict instruction surface is changed even if the original body is byte-for-byte preserved below the YAML.
- Some agents may treat the YAML as instruction text or let it dilute the first visible rule.
- Native mode becomes risky for the exact projects that already have strict `AGENTS.md` files.

Rule to add or clarify:

Adding Agents Tree YAML front matter to an existing unmarked, strict, or human-owned `AGENTS.md` is a separate explicit edit, just like adding a sidecar pointer. Without approval, report that native generated guidance cannot be safely inserted into that file and recommend sidecar mode or a child artifact whose owner is clear.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 3. Template Human Placeholder Becomes Protected Forever

Scenario:

An agent copies `assets/module.AGENTS.md` and fills the generated section, but leaves this template text intact:

```md
<!-- agents-tree:human:start -->
Add human-maintained module notes here.
<!-- agents-tree:human:end -->
```

Later refreshes correctly preserve the human section byte-for-byte, so the fake placeholder becomes durable "human-maintained" content.

Current docs already prevent this?

No. Unresolved placeholders in front matter, generated sections, `Knowledge Status`, and `Evidence Notes` make a file invalid. The rule does not mention placeholders inside human sections, and the templates include human-section placeholder prose.

What happens if not prevented:

- Future agents preserve template filler because it is inside a protected human section.
- Reviewers may treat an empty human section as intentional.
- Cleanup later requires explicit human-section editing even though the text was never human-authored.

Rule to add or clarify:

Templates should not include human-section placeholder prose that can be mistaken for maintained human content. Either omit the human section by default, include it only as a commented template instruction outside managed markers, or classify known template filler inside human sections as invalid during creation before the file is considered maintained.

Files to change:

- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`
- `skills/agents-tree/references/file-contract.md`

### 4. Module Template Starts Medium Confidence With Empty Evidence

Scenario:

An agent creates a module guide from `assets/module.AGENTS.md`. The template has `confidence: medium`, empty `critical_files`, empty `critical_symbols`, and a generated `Knowledge Status` that also says medium. The agent fills only routing bullets and leaves critical evidence empty.

Current docs already prevent this?

Partially. The contract says new generated guidance with empty `critical_files` and `critical_symbols` should use low confidence unless Evidence Notes name another concrete checked source. The root and leaf templates are low confidence, but the module template still starts at medium.

What happens if not prevented:

- Freshly generated module guidance looks more trustworthy than its evidence supports.
- Future agents may treat a medium-confidence module file as usable even though there is no recorded re-check target.
- The template contradicts the contract and teaches the wrong default.

Rule to add or clarify:

Set the module template to `confidence: low` when recorded evidence is empty, or add explicit template evidence fields that must be replaced before medium confidence is allowed.

Files to change:

- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/references/file-contract.md`

### 5. Code-Intelligence Tool Declaration Is Undefined

Scenario:

A target project uses Graphify, GitNexus, Sourcebot, or CodeQL. The README lists these as companion tools, a parent `AGENTS.md` mentions one informally, and a generated section says "use the declared code-intelligence tool." During refresh, one agent treats the README companion list as a declaration and attempts multiple tools; another agent misses the informal note and uses only `rg`.

Current docs already prevent this?

Partially. The workflow repeatedly says declared code graph or code-intelligence tools must be attempted first, and it gives conservative behavior when tools are unavailable. It does not define where a declaration lives, what syntax makes it authoritative, or how examples differ from a project declaration.

What happens if not prevented:

- Agents waste tokens attempting optional tools that were merely mentioned.
- Other agents fail to attempt a tool the project actually depends on.
- Freshness classifications differ across agents because "declared" is interpreted differently.

Rule to add or clarify:

Define authoritative declaration locations and syntax. For example, allow only nearest applicable guidance metadata, a visible `Evidence Notes` declaration, or an explicit human instruction in the current task to require a tool. State that README companion-tool lists are examples, not tool declarations.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `README.md`
- `README.zh.md`

### 6. Ambiguous `critical_symbols` Resolve To The Wrong Symbol

Scenario:

A monorepo has `Config`, `Router`, and `createClient` symbols in several packages. A module guide records `critical_symbols: ["Config", "createClient"]`. During freshness review, the agent proves that a symbol with that name exists somewhere, but it checks the wrong package and marks the guidance `VALID`.

Current docs already prevent this?

Partially. The contract says every `critical_symbols` entry must resolve to a real code symbol and should not contain aliases or approximate labels. It does not say ambiguous symbol names are invalid or require module/path qualification.

What happens if not prevented:

- Freshness checks can pass against unrelated symbols.
- A changed boundary symbol in the real module is missed.
- Guidance looks verified because the metadata contains real, but ambiguous, names.

Rule to add or clarify:

`critical_symbols` must resolve unambiguously in the target module context. If a symbol name is duplicated across packages, languages, or generated sources, qualify it with its module path or pair it with a `critical_files` entry / Evidence Note that makes the resolution unique. Ambiguous symbol metadata is `INVALID` until disambiguated.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`

### 7. Native/Sidecar Overlap Resolution Has No Safe Place To Record The Decision

Scenario:

A repository has root native generated `AGENTS.md` guidance and a child `src/payments/decision-router.md`. The new rules correctly say not to refresh overlapping native and sidecar guidance unless a human asks to migrate, split, or resolve the overlap, and to record the decision in the nearest authoritative artifact before refresh. But the nearest artifact is itself human-owned, unmarked, malformed, or part of the overlap.

Current docs already prevent this?

Partially. The overlap rule is now clear. The recording rule does not say what to do when no safe authoritative artifact is editable.

What happens if not prevented:

- The agent may edit an unsafe artifact just to record the migration decision.
- Or it may block indefinitely even after the human has verbally chosen a strategy.
- Different agents may record the decision in different places, creating more overlap.

Rule to add or clarify:

If no safe authoritative artifact exists, do not force a record into an unsafe file. Report the selected migration/split decision in the review output, ask for explicit approval for the exact artifact that should record it, and keep overlap refresh blocked until that approved edit is made.

Files to change:

- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/SKILL.md`

### 8. Conflict Block Inside A Generated Section Gets Deleted

Scenario:

A previous agent wrote an unresolved conflict block inside `<!-- agents-tree:generated:start --> ... <!-- agents-tree:generated:end -->`. A later refresh validates the generated markers, replaces the generated section wholesale, and accidentally deletes the unresolved conflict.

Current docs already prevent this?

Partially. The docs require parsing conflict blocks and say unresolved conflicts block maintenance. They do not explicitly classify conflict markers nested inside generated or human sections as invalid placement.

What happens if not prevented:

- A real unresolved conflict disappears during a normal generated-section refresh.
- The subtree becomes authoritative-looking even though human review was required.
- The safety marker becomes vulnerable to exactly the edit operation it was supposed to block.

Rule to add or clarify:

Conflict blocks are managed safety blocks and must be outside generated and human sections. Any conflict marker nested inside a mutable generated section or protected human section makes the file `INVALID` for guidance maintenance until a human-approved normalization moves or resolves it. Refresh must preserve conflict blocks byte-for-byte unless explicitly resolving them.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 9. Review Mode Approves A Final Artifact Without A Baseline

Scenario:

A user pastes the final contents of a proposed `AGENTS.md` but not the previous file or Git diff. The final artifact has valid markers, plausible metadata, and concise routing rules. The reviewer assigns a `HIGH` token-saving rating but cannot see that the change deleted unmanaged human text from the old file.

Current docs already prevent this?

Partially. Review Mode says to compare proposed diffs against the previous file when a diff is available. It does not say that human-content preservation cannot be verified when the baseline is missing.

What happens if not prevented:

- A destructive change can be approved because only the final state was reviewed.
- The token-saving rating hides the safety limitation.
- Review Mode becomes weaker than Refresh Mode's own preservation rules.

Rule to add or clarify:

When reviewing a proposed guidance change without the previous file or diff, explicitly state that human-section and unmanaged-text preservation cannot be verified. Do not give an unqualified approval; include the limitation next to the token-saving value rating and request the baseline for safety review.

Files to change:

- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/SKILL.md`

### 10. Dirty Guidance Artifact Is Classified As `VALID`

Scenario:

The code evidence is clean and committed, but the maintained `AGENTS.md` itself has uncommitted generated-section edits. The agent checks `last_verified_commit`, sees the recorded critical files are unchanged, and reports `VALID` even though the current guidance text is not what existed at `last_verified_commit`.

Current docs already prevent this?

Partially. The docs prevent advancing `last_verified_commit` when generated guidance depends on uncommitted source changes. They do not separately handle an uncommitted change to the guidance artifact itself.

What happens if not prevented:

- `VALID` is reported for text that is not represented by the recorded commit.
- Reviewers cannot reconstruct which generated claims were verified.
- A later reset or rewrite of the guidance artifact breaks the supposed freshness state.

Rule to add or clarify:

Freshness review must check the maintained guidance artifact's own diff. If the artifact has uncommitted generated, metadata, conflict, or human-section changes, report the artifact as dirty and do not classify the current text as committed `VALID`. Instead classify the committed version, or review the working-tree version with a visible limitation.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/SKILL.md`

### 11. Project-Installed Skill Files Become Target Guidance Candidates

Scenario:

A team installs Agents Tree into a target repo at `.agents/skills/agents-tree` as recommended by `INSTALL.md`. Later, an agent is asked to create or refresh decision guidance for the target repo. During candidate discovery it sees `.agents/skills/agents-tree/assets/root.AGENTS.md`, `.agents/skills/agents-tree/assets/module.AGENTS.md`, and the skill's own `SKILL.md`, then treats the installed skill package as project evidence or as candidate guidance to maintain.

Current docs already prevent this?

No. The installation docs explicitly allow project-level skill installation, but the maintenance workflow does not say to exclude installed skill packages from target guidance discovery unless the task is about the skill package itself.

What happens if not prevented:

- Agents waste tokens reviewing templates and skill internals as if they were target project guidance.
- The agent may create or refresh `AGENTS.md` files inside `.agents/skills`.
- The target project's guidance tree becomes polluted with the maintenance tool's own files.

Rule to add or clarify:

During target-project guidance discovery, ignore installed skill/plugin directories such as `.agents/skills/**`, `.codex/skills/**`, and known plugin cache directories unless the user explicitly asks to maintain the skill package itself. Project-level skill installation is tooling, not target decision guidance.

Files to change:

- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/SKILL.md`
- `INSTALL.md`
- `INSTALL.zh.md`

### 12. Broad `agents_tree_skip` Hides Candidate Evidence

Scenario:

A guidance file includes `agents_tree_skip: ["src/legacy/**", "src/generated/**", "packages/*/internal/**"]`. None of the current `critical_files` are inside those globs, so freshness checks pass. But a generated claim says "compatibility behavior lives outside legacy code," and the skipped legacy subtree now contains the actual compatibility implementation.

Current docs already prevent this?

Partially. The contract says keep skip lists small and says recorded critical evidence must still be checked even when it matches `agents_tree_skip`. It does not classify broad source-subtree skip rules as invalid when they can prevent discovery of missing or replacement evidence.

What happens if not prevented:

- The agent misses evidence that should have replaced stale critical metadata.
- A skip rule becomes a durable blind spot for scope moves.
- Guidance can remain apparently fresh while its evidence model is incomplete.

Rule to add or clarify:

Broad `agents_tree_skip` globs that exclude source subtrees should be invalid unless they are narrow, task-shaped, evidence-backed, and do not cover plausible ownership or replacement evidence. Skip rules should be reviewed as guidance claims, not just as scan configuration.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 13. Parent/Child Duplication Causes Token Bloat Without Contradiction

Scenario:

The root `AGENTS.md` says payment API shape changes require caller inspection. `src/payments/AGENTS.md` repeats the same rule with no added local first hop. `src/payments/refunds/AGENTS.md` repeats it again. No file contradicts another, so refresh passes, but future agents pay for the same generic instruction at every level.

Current docs already prevent this?

Partially. The docs say root files should act as indexes, child files should not repeat parent guidance, and generated bullets must change the next action. Review and refresh checks emphasize contradictions more than non-contradictory duplication.

What happens if not prevented:

- The tree saves less token budget than promised.
- Agents may overweight repeated generic guidance.
- More duplicated bullets must be kept fresh across multiple files.

Rule to add or clarify:

During review and refresh, compare generated child bullets with applicable parent bullets. Delete or rewrite repeated guidance unless the child adds a new local first hop, verification path, skip rule, or boundary detail that changes the next action inside that subtree.

Files to change:

- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/SKILL.md`
- `README.md`
- `README.zh.md`

### 14. Public Freshness Wording Encourages Diff-Only Validation

Scenario:

A reader uses only the README, not the full skill references. The README freshness states say `VALID` means "no relevant evidence changed since `last_verified_commit`." The agent checks Git diff for recorded file paths, sees no changes, and marks the file `VALID` without resolving symbols, checking guidance-artifact dirtiness, or re-evidencing generated claims.

Current docs already prevent this?

Mostly in the references, but not in the public-facing simplified wording. The detailed contract is stricter: stale guidance must not be trusted, missing or ambiguous evidence is invalid, and `VALID` requires recorded evidence still supports generated claims.

What happens if not prevented:

- Agents using the README as the main contract perform shallow freshness checks.
- README claims about verified guidance are weaker than the actual file contract.
- A file can be treated as valid because filenames did not change, not because claims were rechecked.

Rule to add or clarify:

Align README freshness wording with the contract: `VALID` requires recorded evidence and current relevant diffs or code-intelligence evidence to still support the generated claims. It is not a filename-only or diff-only status.

Files to change:

- `README.md`
- `README.zh.md`
- `skills/agents-tree/references/file-contract.md`

## Executive Summary

The updated Agents Tree skill is materially safer than the previous review target. Most previous red-team findings were directly addressed in `SKILL.md`, `file-contract.md`, `maintenance-workflow.md`, templates, README wording, and the OpenAI manifest.

The remaining high-value fixes are about hardening the boundary between human-owned instruction surfaces and maintained decision guidance. The most serious residual issues are initial creation in existing unmarked `AGENTS.md` files, template text inside protected human sections, ambiguous code-intelligence declarations, and freshness checks that do not account for dirty guidance artifacts or ambiguous symbol metadata.

The skill should keep failing closed when authority is unclear. If the existing file has non-Agents front matter, if a strict root `AGENTS.md` would need YAML prepended, if overlap cannot be recorded safely, if a conflict block is nested in a generated section, or if a review lacks the previous file, the agent should report the limitation rather than producing fresh-looking guidance.

## Priority Fix List

1. Add explicit approval gates for adding or merging Agents Tree front matter into existing unmarked, strict, human-owned, or non-Agents-front-matter `AGENTS.md` files.
2. Remove protected human-section placeholder prose from templates, and set the module template to low confidence while evidence is empty.
3. Define authoritative code-intelligence declaration syntax and distinguish project declarations from README companion-tool examples.
4. Require unambiguous `critical_symbols`, with path/module qualification when names collide.
5. Treat dirty maintained guidance artifacts as a separate freshness limitation.
6. Classify nested conflict blocks and baseline-less reviews conservatively.
7. Exclude project-installed skill/plugin directories from target guidance discovery by default.
8. Review broad `agents_tree_skip` globs as guidance claims that can create evidence blind spots.
9. Add a parent/child duplication check to Review and Refresh mode.
10. Align README freshness wording with the stricter file contract.

## Files Most Worth Editing First

1. `skills/agents-tree/references/file-contract.md`: front matter ownership, human placeholder invalidity, ambiguous symbols, dirty guidance artifacts, nested conflict markers, broad skip validity.
2. `skills/agents-tree/references/maintenance-workflow.md`: initial creation gates, no-baseline Review Mode, installed skill directory exclusion, overlap decision recording, parent/child duplication review.
3. `skills/agents-tree/assets/*.AGENTS.md`: remove fake human notes and fix module-template confidence.
4. `skills/agents-tree/SKILL.md`: concise reminders for unsafe existing `AGENTS.md` migration, dirty artifact checks, and installed skill package exclusion.
5. `README.md` and `README.zh.md`: clarify freshness is claim-evidence validation, not diff-only validation; clarify companion tools are examples unless declared in target guidance.
6. `INSTALL.md` and `INSTALL.zh.md`: warn that project-level skill installation is tooling and should be ignored during target guidance discovery unless the skill package itself is the target.
