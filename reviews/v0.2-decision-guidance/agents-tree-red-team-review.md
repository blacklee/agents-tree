# Agents Tree Decision-Guidance Red-Team Review

Review date: 2026-06-04

Review environment:

- Runtime: local Codex
- Agent: Codex
- Model: GPT-5.5
- Reasoning effort: high
- Skill workflow: Superpowers SKILL (`using-superpowers`) and `agents-tree`

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
- existing review notes in `reviews/v0.1-knowledge-tree/` and `reviews/v0.2-decision-guidance/`

Review goal: find scenarios where the current Agents Tree decision-guidance skill may cause an agent to do the wrong thing, waste tokens, delete human-maintained content, block work incorrectly, choose the wrong artifact strategy, or generate stale decision guidance.

## Failure Scenarios

### 1. Sidecar Pointer Requires Editing A Human-Owned Root `AGENTS.md`

Scenario:

A target project explicitly chooses sidecar mode because its root `AGENTS.md` is a strict human-owned instruction file. The sidecar contract requires a short root pointer to `decision-router.md`. The agent treats the sidecar choice as permission to edit the root `AGENTS.md`, inserts the pointer, and accidentally disturbs strict tool instructions or unmarked human content.

Current docs already prevent this?

Partially. The docs say sidecar mode requires a root pointer, and they also say human-owned or unmarked `AGENTS.md` content must be preserved. They do not state which rule wins when the required sidecar pointer must be added to a strict human-owned instruction file.

What happens if not prevented:

- The agent edits a human-owned instruction surface without explicit approval.
- A strict `AGENTS.md` may lose ordering, formatting, or emphasis that affects other agents.
- Sidecar mode becomes unsafe precisely for the projects that need it most.

Rule to add or clarify:

Selecting sidecar mode is not permission to edit a human-owned, strict, or unmarked root `AGENTS.md`. If the root pointer is missing, ask for explicit approval before adding it. If approval is not given, report that sidecar guidance exists but may not be reliably discovered.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `README.md`
- `README.zh.md`

### 2. Sidecar Discovery Uses The Wrong Repository Root

Scenario:

A workspace contains nested repositories or a monorepo with multiple package roots. The agent sees a parent `AGENTS.md` pointer to `decision-router.md` and follows sidecar files from the workspace root instead of the Git repository or package root that owns the target code. It then reads irrelevant parent guidance or misses the actual target sidecar.

Current docs already prevent this?

Partially. `file-contract.md` handles multi-repo `last_verified_commit`, but sidecar pointer discovery does not define the root selection rule for nested repos, package workspaces, or submodules.

What happens if not prevented:

- The agent applies the wrong decision guidance to the target directory.
- It may skip relevant source evidence because a parent workspace guide says the task belongs elsewhere.
- It may advance metadata using the right repository commit but the wrong sidecar guidance path.

Rule to add or clarify:

In sidecar mode, discover sidecar files from the repository root that contains the maintained artifact and target code. If multiple roots are plausible, report the ambiguity and do not classify sidecar guidance as `VALID` until the owner root is established.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `README.md`

### 3. Native And Sidecar Guidance Overlap Across Ancestor And Child Nodes

Scenario:

The root has native generated `AGENTS.md` decision guidance, while `src/payments/decision-router.md` contains sidecar guidance for a child module. The docs prohibit maintaining both native and sidecar guidance for the same directory, but this overlap is not in the same directory. The agent treats the hybrid tree as valid and applies both, even though their artifact strategies imply different discovery rules.

Current docs already prevent this?

Partially. The same-directory overlap rule is clear. The ancestor/descendant hybrid case is not explicit.

What happens if not prevented:

- Future agents do not know whether native or sidecar mode is authoritative for a subtree.
- A root native file may route agents away from the sidecar tree, or the sidecar tree may contradict the native parent.
- Refreshes may update one artifact strategy while leaving the other stale.

Rule to add or clarify:

Artifact strategy should be coherent for each covered subtree. If native and sidecar generated guidance overlap by ancestry, stop and ask whether the subtree is being migrated, intentionally split, or should use one strategy. Record the decision in the nearest authoritative guidance artifact before refresh.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 4. Review Mode Misses Destructive Changes In A Proposed Diff

Scenario:

A user asks the agent to review a pull request that changes `AGENTS.md`. The final file has valid markers, but the diff deleted unmanaged human text outside the managed sections and replaced several human notes with generated bullets. The Review Mode checklist focuses on whether the final file reduces decision cost, so the agent rates it `HIGH` and misses that the diff destroyed human-maintained content.

Current docs already prevent this?

Partially. The section-safety rules protect human text during refresh. Review Mode does not explicitly say to compare proposed diffs against the previous file and identify human-content deletion or ownership boundary changes.

What happens if not prevented:

- A review approves a diff that the skill itself should never have produced.
- Human-maintained instructions disappear while the final file still looks contract-compliant.
- The reviewer reports token-saving value but misses the larger safety regression.

Rule to add or clarify:

When reviewing proposed guidance changes, compare the diff against the previous file, not only the final artifact. Flag deletion, movement, wrapping, or rewriting of unmanaged text or human sections unless the diff includes explicit human approval.

Files to change:

- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/SKILL.md`
- `reviews/v0.2-decision-guidance/prompts/agents-tree-skill-review.md`

### 5. Declared Code-Intelligence Tool Is Missing, But Guidance Is Marked `VALID`

Scenario:

A target `AGENTS.md` says generated knowledge depends on a project code graph. The graph tool is unavailable in the current agent environment. The agent follows the bounded no-tool sequence, reads the current diff and a few critical files, then reports `VALID` because it found no obvious contradiction.

Current docs already prevent this?

Partially. `SKILL.md` says the declared tool **MUST** be used. `maintenance-workflow.md` allows `grep`, `rg`, and raw reads when the declared tool is unavailable or stale. The conservative classification rule is implied but not crisp.

What happens if not prevented:

- Guidance receives a fresh-looking `VALID` state without the evidence class it originally required.
- Later agents trust stale boundary or caller assumptions that only the missing code-intelligence tool would have exposed.
- The bounded no-tool sequence becomes a silent downgrade rather than an explicit limitation.

Rule to add or clarify:

The declared tool must be attempted first. If it is unavailable, stale, or partial, the bounded sequence may support `STALE_WARNING`, `INVALID`, or `cannot verify`; it may support `VALID` only when every generated claim is fully re-evidenced without the missing tool and the limitation is recorded in `Evidence Notes` or the review report.

Files to change:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/references/file-contract.md`

### 6. Dirty Working Tree Makes `last_verified_commit` Lie

Scenario:

The agent refreshes a guidance file while the working tree has uncommitted source edits affecting recorded `critical_files`. It sets `last_verified_commit` to `HEAD`, writes generated guidance based on the uncommitted code, and reports the file as fresh. Later the uncommitted code changes again or is discarded, but the guidance still claims it was verified at `HEAD`.

Current docs already prevent this?

Partially. The docs say to check current diffs before advancing `last_verified_commit`, but they do not define how to represent guidance verified against uncommitted working-tree state.

What happens if not prevented:

- `last_verified_commit` points to a commit that never contained the verified evidence.
- Reviewers cannot reconstruct what code state supported the generated claims.
- Future freshness checks may mark stale guidance `VALID` because the commit hash looks current.

Rule to add or clarify:

If generated guidance depends on uncommitted changes, do not advance `last_verified_commit` as though `HEAD` contains that evidence. Either wait until the source changes are committed, or record a visible working-tree evidence note and classify conservatively until a real commit can verify the claims.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`

### 7. Template Placeholders Survive Into Maintained Guidance

Scenario:

An agent copies `assets/module.AGENTS.md`, fills a few bullets, but leaves `COMMIT_SHA`, `TASK_SHAPE`, `DECISION_1`, `SymbolName`, and `path/to/file` in the generated section. The file has valid markers and metadata shape, so later agents read the placeholders as fuzzy guidance or waste tokens trying to resolve nonexistent symbols.

Current docs already prevent this?

Partially. Template comments say to delete unused headings and verify symbols, but unresolved placeholders are not explicitly classified as invalid.

What happens if not prevented:

- Placeholder text becomes durable project guidance.
- `critical_symbols` may look empty while generated bullets still name fake symbols.
- Future agents waste tokens interpreting placeholders or query nonexistent targets.

Rule to add or clarify:

Unresolved template placeholders in front matter, generated sections, `Knowledge Status`, or Evidence Notes make a maintained guidance artifact `INVALID`. Before committing or reporting refresh completion, replace or delete all placeholders such as `COMMIT_SHA`, `TASK_SHAPE`, `DECISION_1`, `SymbolName`, and `path/to/file`.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`

### 8. Root Template Starts With High Confidence And No Critical Evidence

Scenario:

An agent creates a root `AGENTS.md` from `assets/root.AGENTS.md`. The template starts with `confidence: high`, empty `critical_files`, and empty `critical_symbols`. The agent writes root routing guidance but forgets to add critical evidence. Later freshness checks see no recorded evidence and incorrectly treat the root as stable.

Current docs already prevent this?

Partially. The contract says generated claims should be traceable and freshness checks require recorded evidence. The root template still presents high confidence with empty evidence as a starting state.

What happens if not prevented:

- Root guidance appears more authoritative than its evidence supports.
- Future agents have no concrete paths or symbols to re-check before trusting root routing.
- README claims about verified guidance are weakened at the most visible node.

Rule to add or clarify:

New generated guidance with empty `critical_files` and empty `critical_symbols` must default to low confidence and cannot be classified `VALID` unless Evidence Notes name another concrete checked source, such as an explicit human note or verified repository structure. The root template should not default to high confidence with empty evidence.

Files to change:

- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 9. One-Time `agents_tree_keep` Exception Becomes A Durable Reopen Rule

Scenario:

A human asks the agent in the current task to inspect one exact ignored generated file because it is relevant to a bug. The agent records that file in `agents_tree_keep`. Future agents now treat the ignored path as a durable exception and keep reopening generated or sensitive-adjacent content for unrelated tasks.

Current docs already prevent this?

Partially. The docs say `agents_tree_keep` must not reopen secrets, dependencies, build outputs, generated artifacts, or vendored code unless a human explicitly asks for that exact path in the current task. They do not say that current-task exceptions should usually stay out of durable metadata.

What happens if not prevented:

- A one-time permission becomes persistent metadata.
- Token usage grows because ignored paths are repeatedly reopened.
- Generated artifacts or sensitive-adjacent files become easier for agents to inspect accidentally.

Rule to add or clarify:

Do not record current-task ignore exceptions in durable `agents_tree_keep` unless the human explicitly asks to make the exception permanent and the path is safe for future tasks. Otherwise mention the exception only in the current response or Evidence Notes for that task.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 10. Skip Rules Hide Critical Evidence During Freshness Checks

Scenario:

A parent guidance file has `agents_tree_skip: ["src/legacy/**"]` because legacy code is usually irrelevant. A child or module guidance file records `src/legacy/compat.ts` as a critical file for a compatibility boundary. During freshness review, the agent applies the parent skip first and never checks the critical file.

Current docs already prevent this?

Partially. Evidence collection says apply ignore files, then `agents_tree_skip`, then `agents_tree_keep`. It does not explicitly define precedence when recorded critical evidence is inside a skipped path.

What happens if not prevented:

- A critical file changes but the freshness review misses it.
- The agent may classify guidance as `VALID` after intentionally skipping required evidence.
- Skip rules become a way to hide stale knowledge.

Rule to add or clarify:

Recorded `critical_files` and `critical_symbols` must be checked even if they match `agents_tree_skip`. If they are skipped by project ignore files or cannot be checked safely, report `INVALID` or cannot verify instead of silently trusting the guidance. `agents_tree_skip` should not suppress recorded evidence.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/SKILL.md`

### 11. Conflict Block Status Is Malformed Or Ambiguous

Scenario:

An `AGENTS.md` contains a conflict block with `status: unresolved` buried under prose, duplicated status lines, or `status: resolved` without a resolution note. The agent only checks for the marker pair, assumes the conflict is resolved or irrelevant, and refreshes a child guidance file.

Current docs already prevent this?

Partially. The docs define conflict blocks and say unresolved conflicts block maintenance. They do not require parsing conflict-block status mechanically or classify malformed conflict blocks.

What happens if not prevented:

- A malformed unresolved conflict may fail to block descendant refresh.
- A resolved-looking conflict without human resolution may be treated as safe.
- Agents may disagree about whether a subtree is authoritative.

Rule to add or clarify:

Before maintenance, parse conflict blocks as managed safety markers. Missing, duplicated, or unrecognized conflict `status` values make the file `INVALID` for guidance maintenance. Treat `status: resolved` as non-blocking only when a short resolution note is present; otherwise require human review.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 12. Audience-Boundary Suppression Hides A New Misplaced Instruction

Scenario:

The nearest managed guidance artifact has `audience_boundary_review.status: resolved` at `checked_at_commit: abc123`. A human later renames `README.md` to `docs/README.md` and adds new agent-specific instructions to it. The agent cannot compare the old file path cleanly, treats the review as resolved, and suppresses the audience-boundary suggestion.

Current docs already prevent this?

Partially. The workflow says to re-run the review when listed files changed or the reviewed file set changed. It does not define the conservative behavior when `checked_at_commit` cannot be compared, files were renamed, or history is shallow.

What happens if not prevented:

- Agent-specific instructions remain hidden in human-facing docs.
- Future agents miss or duplicate behavior guidance.
- The repeat-suppression metadata becomes a stale silencer.

Rule to add or clarify:

If `checked_at_commit` cannot be compared, or any reviewed file was renamed, deleted, or replaced, report the audience-boundary state as uncertain and re-run the advisory check. Do not write or rely on `resolved` suppression until the new file set is reviewed.

Files to change:

- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 13. README Candidate List Encourages Too Many Guidance Files

Scenario:

The README says good candidates include directories with many files, several child modules, high-risk code, historical compatibility logic, or frequent agent access. An enthusiastic agent interprets the list as a creation checklist and creates `AGENTS.md` files across most top-level and mid-level directories before proving a decision-compression reason for each.

Current docs already prevent this?

Mostly in the skill and workflow, but not in the public-facing README wording. `maintenance-workflow.md` says directory size is only supporting evidence and a file must reduce future decision cost. The README list is easier to misread.

What happens if not prevented:

- The guidance tree becomes larger than the source areas it was meant to route.
- Future agents spend extra tokens reading low-value files.
- More metadata has to be kept fresh, increasing the stale-knowledge surface.

Rule to add or clarify:

In README candidate guidance, state that these traits are only prompts for investigation, not reasons to create a file. A new guidance file requires a specific decision cost saved before creation.

Files to change:

- `README.md`
- `README.zh.md`
- `skills/agents-tree/references/maintenance-workflow.md`

### 14. Agent Manifest Underspecifies Review And Human Protection

Scenario:

An agent surface shows only `skills/agents-tree/agents/openai.yaml` metadata. The default prompt says to "create, check, or refresh" decision guidance. The user asks for a safety review, but the agent assumes the skill is write-oriented and refreshes generated content instead of running Review Mode.

Current docs already prevent this?

Partially. `SKILL.md` includes Review Mode and the human-section rules. The manifest, which may be the user's first contact with the skill, omits review, sidecar, stale/invalid handling, and human-maintained section protection.

What happens if not prevented:

- Users invoke the skill with write-oriented expectations.
- Agents may skip Review Mode or understate human-content safety.
- The skill's most important safety contract is not visible in the installation surface.

Rule to add or clarify:

Broaden the manifest default prompt and description to include create, check, refresh, and review, while explicitly preserving human-maintained sections. If the surface permits only one sentence, make safety part of that sentence.

Files to change:

- `skills/agents-tree/agents/openai.yaml`
- `INSTALL.md`
- `INSTALL.zh.md`

## Executive Summary

The v0.2 decision-guidance version is much safer than the v0.1 knowledge-tree version. The major v0.1 hazards are now explicitly addressed: unmarked `AGENTS.md` files are human-maintained by default, malformed markers block refresh, missing critical evidence is invalid, conflict blocks are visible, and generated bullets must change the next action.

The remaining red-team risks are mostly edge conditions where two correct rules collide. Sidecar mode needs stronger pointer and root-discovery rules. Review Mode needs to assess diffs, not only final files. Freshness needs a precise dirty-working-tree rule. Tool-required evidence needs a conservative unavailable-tool classification. Templates should make unresolved placeholders invalid, and README/manifest wording should not encourage over-generation or write-oriented use.

The most important theme is that Agents Tree should fail closed when authority is ambiguous. A missing pointer, ambiguous sidecar root, stale code-intelligence tool, unresolved placeholder, skipped critical file, or malformed conflict status should not produce fresh-looking guidance.

## Priority Fix List

1. Clarify sidecar pointer approval, sidecar root discovery, and ancestor/child native-sidecar overlap.
2. Add Review Mode rules for proposed diffs, especially human-content deletion and ownership-boundary changes.
3. Define conservative freshness behavior for unavailable code-intelligence tools and dirty working trees.
4. Classify unresolved template placeholders and empty-evidence generated guidance as invalid or low-confidence.
5. Tighten `agents_tree_keep` and `agents_tree_skip` precedence so one-time exceptions do not become durable reopen rules and skip rules cannot hide critical evidence.
6. Make conflict-block status parsing mechanical enough to avoid accidental descendant refresh.
7. Adjust README and manifest wording so the public contract emphasizes decision cost saved, review mode, and human-section protection.

## Files Most Worth Editing First

1. `skills/agents-tree/references/file-contract.md`: canonicalize sidecar root rules, placeholder invalidity, dirty-working-tree freshness, conflict status validity, and skip/critical-evidence precedence.
2. `skills/agents-tree/references/maintenance-workflow.md`: add procedures for sidecar pointer approval, Review Mode diff checks, unavailable code-intelligence classification, audience-boundary uncertainty, and one-time keep exceptions.
3. `skills/agents-tree/SKILL.md`: keep the concise workflow aligned with sidecar safety, diff review, tool-attempt semantics, and critical evidence not being suppressed by skip rules.
4. `skills/agents-tree/assets/*.AGENTS.md`: remove high-confidence empty-evidence defaults, warn that unresolved placeholders make guidance invalid, and keep templates smaller.
5. `README.md` and `README.zh.md`: reduce wording that can be read as "create files for large/high-risk directories" and reinforce that each file needs a stated decision-compression reason.
6. `skills/agents-tree/agents/openai.yaml`: include Review Mode and human-maintained section preservation in the visible invocation surface.
