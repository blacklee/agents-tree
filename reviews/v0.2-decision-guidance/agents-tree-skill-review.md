# Agents Tree Decision-Guidance Skill Review

Review date: 2026-06-04

Review environment:

- Runtime: local Codex
- Agent: Codex
- Model: GPT-5.5
- Reasoning effort: high
- Skill workflow: Superpowers SKILL (`using-superpowers`) and `agents-tree`
- Test variant: 2A

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

The updated v0.2 decision-guidance skill is coherent and executable for manual skill-driven use. The previous highest-risk gaps around sidecar pointer approval, declared-but-unavailable code-intelligence tools, unresolved placeholders, and proposed-diff review are now addressed in the main skill workflow, file contract, maintenance workflow, templates, and OpenAI manifest.

## Important Issues

1. **Public freshness wording is still more permissive than the executable contract.**
   - Evidence: `README.md` defines `VALID` as "no relevant evidence changed since `last_verified_commit`" and says the exact classifier should combine Git diffs with optional tools. `README.zh.md` has the same simplified framing. The stricter contract in `skills/agents-tree/references/file-contract.md` requires checking every recorded critical file and symbol, current diffs or code-intelligence evidence that affects generated claims, unresolved placeholders, ignored evidence, unavailable tools, and current references/flows before `VALID`.
   - Risk: A user or agent relying on the public README rather than the references may treat unchanged recorded files as enough for `VALID`, even though the skill explicitly says not to infer validity from unchanged filenames alone.
   - Suggested improvement: In both READMEs, change the `VALID` bullet to say that recorded critical evidence and relevant current diffs/flows were checked and still support the generated claims.

2. **Templates are safer than before, but still invite overfilled generated sections.**
   - Evidence: `skills/agents-tree/assets/root.AGENTS.md`, `module.AGENTS.md`, and `leaf.AGENTS.md` now warn that unresolved placeholders make guidance invalid. They still copy a full generated structure with `Decision Compression`, `Use`, `Skip`, `First Hop`, `Cross-Module`, `Verification`, and `Evidence Notes` plus multiple placeholder bullets.
   - Risk: The skill tells agents to delete unused headings, but weaker agents may fill every placeholder and create long guidance even when only one or two routing rules are justified.
   - Suggested improvement: Add a smaller minimal template variant, or move most optional headings into a commented example block so the default copied artifact starts with only `Knowledge Status`, one decision-cost line, and one or two generated sections.

3. **Install/update docs still do not make loaded-copy verification concrete enough for local review reruns.**
   - Evidence: `INSTALL.md` and `INSTALL.zh.md` say to restart Codex and confirm `agents-tree` is visible, but they do not tell reviewers how to verify whether Codex loaded the user-level copy, a project-level copy, or the current working-tree copy.
   - Risk: During skill review, an agent can accidentally evaluate an installed stale copy while the repository copy has changed, or vice versa. This matters because the skill is distributed by copying directories.
   - Suggested improvement: Add a post-update review note: when testing changes, explicitly read `skills/agents-tree/SKILL.md` from the repository under review, or reinstall and confirm the loaded skill path/version before running the prompt.

## Minor Issues

1. `README.md` still says "The exact classifier should combine Git diffs with optional code graph..." which can sound like an implementation promise, while the project is still skill-and-file-contract-first. "A reviewer may combine..." would better match the current stage.

2. `AGENTS.md` says generated guidance files "should use YAML front matter," while `file-contract.md` says maintained decision-guidance artifacts "must begin with YAML front matter." The stronger wording should be mirrored in project instructions.

3. The templates' default human sections say "Add human-maintained ... notes here." That is useful for scaffolding, but if not deleted it creates a fake human section. The placeholder invalid rule likely covers this in spirit, but the exact phrase is not listed in the invalid placeholder examples.

4. Conflict behavior is strong, but `maintenance-workflow.md` still includes the minimal conflict block example. It is acceptable, yet agents may copy only the minimal version even when related files or evidence links are known.

5. `README.zh.md` uses both "决策压缩层" and "决策指引"; this is understandable, but high-visibility wording would be cleaner if it consistently led with "AGENTS.md 兼容决策指引."

## Missing Scenarios

1. **Minimal valid root guidance with no code symbols**
   - The contract allows empty `critical_files` and `critical_symbols` only with concrete evidence notes and low confidence, but there is no small example of a root index that is valid based on repository structure or explicit human notes rather than symbols.

2. **Template scaffolding before evidence is available**
   - The docs classify placeholders as invalid, but they do not say whether a partially scaffolded draft should be left uncommitted only, marked `INVALID`, or avoided entirely until evidence exists.

3. **Resolving native/sidecar overlap**
   - The docs correctly stop and ask when both exist, but there is no example of how to record a human decision to migrate, intentionally split, or retire one artifact in the nearest authoritative guidance file.

4. **Testing loaded skill copies during development**
   - The install docs lack a scenario for local reviewers who have both `skills/agents-tree/` in the repo and `~/.agents/skills/agents-tree` installed.

5. **Conflict block resolution note format**
   - The contract requires a short resolution note for `status: resolved`, but it does not show a compact resolved conflict example.

## Suggested Edits With Exact File / Section

1. **`README.md` / `## Freshness States`**
   - Change `VALID` from "no relevant evidence changed since `last_verified_commit`" to: "`VALID`: recorded critical evidence, relevant current diffs/flows, and generated claims were checked and still support the guidance."

2. **`README.zh.md` / `## 新鲜度状态`**
   - Mirror the stricter `VALID` wording: "`VALID`：已复查记录的关键证据、相关当前 diff/流程，以及自动生成结论，仍能支撑这份指引。"

3. **`AGENTS.md` / `## File Contract`**
   - Change "Generated native `AGENTS.md` files and explicit sidecar decision-guidance files should use YAML front matter" to "must begin with YAML front matter" to match `file-contract.md`.

4. **`skills/agents-tree/assets/*.AGENTS.md` / generated template body**
   - Add a short line near the human marker: "Delete this placeholder human section unless a human supplies real notes." Or include `Add human-maintained` in the unresolved-placeholder invalid examples.

5. **`skills/agents-tree/assets/*.AGENTS.md` / template shape**
   - Consider replacing the full default section list with a minimal generated block and a commented optional-section example.

6. **`INSTALL.md` and `INSTALL.zh.md` / `## Post-Install Check` and `## Update The Skill`**
   - Add a local review note: when rerunning skill-review prompts, verify whether the agent is using the repository copy or the installed copy; reinstall or explicitly point the prompt to the repository `skills/agents-tree/SKILL.md`.

7. **`skills/agents-tree/references/file-contract.md` / `## Conflict Sections`**
   - Add a compact `status: resolved` example with a required resolution note.

8. **`skills/agents-tree/references/maintenance-workflow.md` / `## Choose Artifact Strategy First`**
   - Add a short example of recording a human-approved native-to-sidecar migration or intentional split.

## Answers To Review Goals

1. **Trigger condition:** Clear. The skill description explicitly covers creating, checking, refreshing, and reviewing directory-scoped AGENTS.md-compatible decision guidance, native `AGENTS.md`, sidecar `decision-router.md`, freshness metadata, generated sections, human-protected sections, and stale guidance.

2. **Create / Check / Refresh / Review:** Clear. Operating modes and mode selection are actionable, and check-only/review-only requests are protected from edits.

3. **`SKILL.md` concision:** Strong. It stays concise while pointing agents to `file-contract.md` for exact metadata and freshness rules and `maintenance-workflow.md` for placement, conflicts, and refresh behavior.

4. **Native vs sidecar separation:** Strong. Native remains default; sidecar requires explicit project reason, owner root, discoverability pointer, and explicit approval before editing human-owned or unmarked root `AGENTS.md`.

5. **Generated / human / conflict sections:** Strong. Marker validation, byte-for-byte preservation, whole-file human ownership, unmanaged text protection, unresolved placeholder invalidation, and conflict blocks are clear.

6. **Unresolved conflict safety:** Strong. The docs block guidance maintenance for the affected file and subtree without blocking unrelated code work or unrelated tree nodes.

7. **Keep / skip:** Strong. Existing ignore files come first; keep/skip are small, safety-limited, and recorded evidence cannot be hidden by skip rules.

8. **Cross-module relationships:** Strong. Live callers, references, and dependency inventories are delegated to code graph or code-intelligence tools, while guidance stores only stable handoff rules.

9. **Generated bullets change next action:** Very clear. This rule appears in `SKILL.md`, `file-contract.md`, `maintenance-workflow.md`, and the templates.

10. **Review Mode proposed-diff safety:** Clear. `SKILL.md` and `maintenance-workflow.md` now tell agents to compare proposed diffs against the previous file and flag deletion, movement, wrapping, or rewriting of human/unmanaged text without explicit approval.

11. **Reasoning-token value:** Clear. The docs explain first-hop reasoning, code-intelligence targeting, boundary checks, verification choice, and concrete skip reasoning without claiming guidance replaces source reading.

12. **Contradictions / repetition / vague rules:** No blocking contradictions found. Remaining looseness is mostly in README-level freshness wording and template size, not in the executable contract.

13. **Non-installed agent readability:** Good. `Knowledge Status` and conflict blocks are visible and human-readable without the skill installed.

## Overall Readiness Score

**9 / 10**

The updated v0.2 skill is ready for realistic manual use and stronger fresh-session testing. The core safety contract is now explicit: native mode by default, sidecar only with approval-safe discoverability, no hidden stale evidence, no silent human-text rewrites, no durable caller inventories, and no placeholder-filled "valid" guidance. The remaining work is polish and pressure-test coverage rather than foundational repair.
