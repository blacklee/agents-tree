# Agents Tree Decision-Guidance Skill Review

Review date: 2026-06-04

Review environment:

- Runtime: local Codex
- Agent: Codex
- Model: GPT-5.5
- Reasoning effort: high
- Skill workflow: Superpowers SKILL (`using-superpowers`) and `agents-tree`

Prompt used:

- `reviews/v0.2-decision-guidance/prompts/agents-tree-skill-review.md`

Scope reviewed:

- `README.md`
- `README.zh.md`
- `AGENTS.md`
- `INSTALL.md`
- `INSTALL.zh.md`
- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/agents/openai.yaml`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`

## Critical Issues

None found.

The v0.2 decision-guidance version resolves the most serious v0.1 execution gaps: mode selection is explicit, malformed markers are invalid for refresh, unmarked `AGENTS.md` content is preserved as human-maintained, visible `Knowledge Status` is required, missing critical evidence remains visible, and generated bullets must pass a next-action test.

## Important Issues

1. **Sidecar mode still needs a sharper rule for editing a strict human-owned root `AGENTS.md` pointer.**
   - Evidence: `skills/agents-tree/references/file-contract.md` says sidecar mode requires a short root `AGENTS.md` pointer. `skills/agents-tree/SKILL.md` and `file-contract.md` also say unmarked or human-owned `AGENTS.md` content must be preserved and not edited without explicit permission.
   - Risk: In the exact sidecar scenario this version introduces, an agent may need to add a discovery pointer to a strict root `AGENTS.md`, but that file may be human-owned and marker-free. Without a direct rule, agents may either modify it too freely or refuse sidecar setup even after the project has selected sidecar mode.
   - Suggested improvement: State that selecting sidecar mode is not itself permission to edit a human-owned root `AGENTS.md`; the agent must either ask for explicit approval to add the pointer or report that sidecar guidance will be hard to discover until the pointer is added.

2. **The code-intelligence requirement has a small contradiction between “MUST use” and the unavailable-tool path.**
   - Evidence: `SKILL.md` workflow step 5 says if applicable guidance declares a code graph or code-intelligence tool, the agent **MUST** use it before creating, reviewing, or refreshing generated knowledge. `maintenance-workflow.md` later says `grep`, `rg`, and raw reads may be used when the declared tool is unavailable or stale, and gives a bounded sequence for unavailable tools.
   - Risk: Agents may treat the bounded sequence as a full substitute and still mark guidance `VALID`, or they may stop completely even when a bounded `INVALID` / cannot-verify result would be useful.
   - Suggested improvement: Clarify that a declared tool must be attempted first. If it is unavailable or stale, the agent may use the bounded sequence only to classify conservatively, and should not report `VALID` unless the generated claims are fully evidenced without the missing tool.

3. **The templates still encourage placeholder-heavy generated sections unless agents aggressively delete them.**
   - Evidence: `assets/root.AGENTS.md`, `assets/module.AGENTS.md`, and `assets/leaf.AGENTS.md` include many headings and multiple placeholder bullets. They include comments telling agents to delete unused headings, but the copied starting point is still relatively large.
   - Risk: Less disciplined agents may fill every heading, turning a decision guide into a mini-encyclopedia or stale review checklist.
   - Suggested improvement: Consider adding a smaller “minimal generated block” template or make the current templates more visibly optional by grouping extra headings under a commented example block that should not be copied verbatim.

4. **`agents/openai.yaml` is too narrow compared with the v0.2 product contract.**
   - Evidence: `skills/agents-tree/agents/openai.yaml` default prompt says: “Use $agents-tree to create, check, or refresh this project's decision guidance.” It omits review mode, sidecar mode, human-section protection, and stale/invalid knowledge.
   - Risk: The manifest is not wrong, but it undersells the core safety contract and may train users to invoke only write-oriented workflows.
   - Suggested improvement: Include “review,” “sidecar when explicit,” and “preserve human-maintained sections” in the short description or default prompt if the manifest surface supports it.

5. **Audience-boundary repeat suppression depends on comparing reviewed docs since `checked_at_commit`, but the procedure is not fully mechanical.**
   - Evidence: `file-contract.md` defines `audience_boundary_review.checked_at_commit`, `status`, and `files`; `maintenance-workflow.md` says not to repeat suggestions when all listed files have not changed since that commit.
   - Risk: In repositories with no usable commit SHA, shallow history, renamed docs, or sidecar-only guidance, agents may repeat suggestions or suppress them incorrectly.
   - Suggested improvement: Add a short rule: if `checked_at_commit` is `unknown` or cannot be compared, report the review state as uncertain and do not write `resolved` or suppress new suggestions unless the user confirms.

## Minor Issues

1. `README.md` uses “knowledge” language in several headings and paragraphs even though v0.2 is intentionally framed as “decision guidance.” This is understandable historically, but replacing the highest-visibility instances with “guidance” or “generated guidance” would reduce drift.

2. `README.zh.md` still says “决策压缩树” in install-related wording. That is close to the current concept, but “AGENTS.md 兼容决策指引” is more consistent with the v0.2 framing.

3. `file-contract.md` says “Every maintained decision-guidance artifact should begin with YAML front matter.” The rest of the contract treats metadata as required for maintained generated guidance. “Must begin” would be more executable than “should begin.”

4. `SKILL.md` says to use sidecar mode only when the target project explicitly wants separated guidance, but the “Mode Selection” section does not mention that artifact strategy is a separate decision from Create/Check/Refresh/Review. A one-line reminder would help.

5. Conflict examples are now strong, but the short conflict block in `maintenance-workflow.md` could point back to `file-contract.md` as the canonical expanded shape to avoid agents copying only the minimal block when richer evidence is available.

6. Installation docs say Codex supports user-level and project-level skills, but they do not mention how to verify which skill directory Codex actually loaded if multiple copies exist. This may matter during local development and review re-runs.

## Missing Scenarios

1. **Sidecar setup when root `AGENTS.md` is strict and human-owned**
   - The scenario prompt covers this in simulation, but the core docs should explicitly say whether adding the root sidecar pointer requires separate human approval.

2. **Declared code-intelligence tool is configured but stale, broken, or partially indexed**
   - The docs mention unavailable or stale tools, but they should say what status to report and when `last_verified_commit` may advance.

3. **A target project already has both native `AGENTS.md` generated guidance and sidecar `decision-router.md` generated guidance**
   - The docs say not to maintain both and to ask whether to migrate, merge, or leave separate. A conflict or overlap block example would help prevent agents from silently choosing one source as authoritative.

4. **Project has no usable Git commit but wants initial guidance**
   - `file-contract.md` says use `last_verified_commit: unknown`, `confidence: low`, and do not classify as `VALID`. A short initial-creation example would clarify whether generated guidance is allowed and what `Knowledge Status` should say.

5. **Audience-boundary metadata after file rename**
   - If `README.md` is renamed or split, it is unclear whether repeat suppression should be invalidated, migrated, or reported as uncertain.

6. **Review mode against a proposed diff rather than an existing target file**
   - `Review Mode` is good for an existing `AGENTS.md`, but agents also need to review pull-request diffs that add or change generated guidance before merge.

7. **Templates copied but placeholders not fully replaced**
   - The contract does not explicitly classify unresolved placeholders such as `TASK_SHAPE`, `COMMIT_SHA`, or `DECISION_1` as invalid.

8. **Multiple repositories inside one workspace with sidecar pointers**
   - `last_verified_commit` multi-repo handling is clear, but sidecar root pointer discovery across nested repos or monorepos could use one example.

## Suggested Edits With Exact File / Section

1. **`skills/agents-tree/references/file-contract.md` / `### Sidecar Decision-Router Mode`**
   - Add: “If the required root `AGENTS.md` pointer would modify a human-owned, strict, or unmarked `AGENTS.md`, ask for explicit approval before editing it. If approval is not given, report that sidecar guidance exists but may not be reliably discovered.”

2. **`skills/agents-tree/SKILL.md` / `## Artifact Strategy`**
   - Add a pointer-safety bullet: “Sidecar mode requires a discoverability pointer, but adding that pointer to an existing human-owned instruction file is a separate explicit edit.”

3. **`skills/agents-tree/SKILL.md` / `## Workflow` step 5**
   - Change the tool rule to: “If applicable project guidance declares a code graph or code-intelligence tool, MUST attempt it first before creating, reviewing, or refreshing generated knowledge. If it is unavailable or stale, follow the bounded no-tool sequence in `maintenance-workflow.md` and classify conservatively.”

4. **`skills/agents-tree/references/maintenance-workflow.md` / `## Evidence Collection`**
   - Add: “When a declared code-intelligence tool is unavailable or stale, record that limitation in `Evidence Notes` or the review report. Do not advance `last_verified_commit` to a fresh `VALID` state unless all generated claims are fully re-evidenced.”

5. **`skills/agents-tree/assets/*.AGENTS.md` / generated template comments**
   - Add an invalid-placeholder warning near the top: “Before committing, replace or delete all placeholders such as `COMMIT_SHA`, `TASK_SHAPE`, `DECISION_1`, and `SymbolName`; unresolved placeholders make this guidance invalid.”

6. **`skills/agents-tree/references/file-contract.md` / `## Freshness Review`**
   - Add unresolved template placeholders to the `INVALID` checklist.

7. **`skills/agents-tree/agents/openai.yaml`**
   - Broaden the default prompt to include review and safety:
     ```yaml
     default_prompt: "Use $agents-tree to create, check, refresh, or review this project's AGENTS.md-compatible decision guidance while preserving human-maintained sections."
     ```

8. **`skills/agents-tree/references/maintenance-workflow.md` / `## Review Mode`**
   - Add a short PR-diff variant: “When reviewing proposed guidance changes, compare the diff against the current file contract, recorded evidence, parent guidance, and generated/human section boundaries before rating token-saving value.”

9. **`skills/agents-tree/references/maintenance-workflow.md` / `## Audience Boundary Review`**
   - Add: “If `checked_at_commit` cannot be compared, or a listed file was renamed/deleted, report repeat-suppression state as uncertain and re-run the advisory check without writing `resolved` unless the user approves.”

10. **`README.md` and `README.zh.md` / high-visibility “knowledge” wording**
    - Keep metadata and freshness terms where useful, but prefer “decision guidance” for user-facing product identity.

## Answers To Review Goals

1. **Trigger condition:** Clear. The skill description strongly targets AGENTS.md-compatible decision guidance, sidecar guidance, freshness metadata, generated sections, and stale guidance.

2. **Create / Check / Refresh / Review:** Clear and much more executable than v0.1. The only remaining gap is separating operating mode from artifact-strategy approval in sidecar setup.

3. **`SKILL.md` concision:** Good. It is concise but not under-specified, and it points to `references/` for exact metadata, sidecar rules, markers, conflicts, and freshness.

4. **Native vs sidecar separation:** Directionally strong. Native remains default, sidecar is explicit, and overlap is prohibited. Pointer editing in human-owned root `AGENTS.md` needs one more safety rule.

5. **Generated / human / conflict sections:** Strong. Marker validation, byte-for-byte human preservation, unmarked migration, and whole-file human ownership are all clear.

6. **Unresolved conflict safety:** Strong. The docs correctly block guidance maintenance for the affected file/subtree without blocking unrelated code work or unrelated nodes, and they include examples.

7. **Keep / skip:** Strong. Existing ignore files come first; keep is small and cannot reopen sensitive or generated paths without explicit human request.

8. **Cross-module relationships:** Strong. The skill consistently delegates live callers, references, and impact analysis to code-intelligence or bounded focused evidence rather than durable guidance.

9. **Generated bullets change next action:** Very clear. This is repeated in the skill, file contract, workflow, and templates.

10. **Reasoning-token value:** Clear and credible. The docs explain that guidance reduces first-hop and routing reasoning without replacing code reading.

11. **Contradictions / repetition / vague rules:** No blocking contradictions. The main tension is the declared code-intelligence `MUST` versus unavailable-tool sequence. Some “knowledge” wording remains from v0.1, but it does not break execution.

12. **Non-installed agent readability:** Good. Conflict blocks and `Knowledge Status` are visible and understandable without the skill installed.

## Overall Readiness Score

**8.5 / 10**

The v0.2 decision-guidance version is ready for realistic manual use and fresh-session pressure testing. It has a coherent product boundary, strong human-section protection, concrete freshness rules, and a clear anti-encyclopedia stance. The remaining work is mostly edge-case hardening around sidecar discoverability, declared-but-unavailable code-intelligence tools, unresolved template placeholders, and review workflows for proposed diffs.
