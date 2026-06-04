# Agents Tree Review Artifacts

This directory stores AI-assisted review artifacts for the Agents Tree skill.

These files are review records and pressure-test prompts, not the normative specification. The active specification lives in:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/*.AGENTS.md`

## Versioned Review Sets

### `v0.1-knowledge-tree/`

Historical review materials for the early "knowledge tree" framing.

- Date: 2026-06-02
- Environment: local Codex sessions
- Agent: Codex
- Model: GPT-5.5
- Reasoning effort: high
- Purpose: evaluate the original AGENTS.md knowledge-tree workflow

These artifacts are useful as design history, but they are no longer current validation evidence for the decision-guidance version.

### `v0.2-decision-guidance/`

Current prompt set for the AGENTS.md-compatible decision-guidance framing.

This version should validate:

- native `AGENTS.md` mode by default
- optional sidecar `decision-router.md` mode
- decision compression rather than directory summaries
- concrete skip guidance and first-hop rules
- freshness metadata and evidence checks
- human section protection and conflict behavior
- `SKILL.md` as a concise execution entrypoint with details delegated to `references/`

Generated review outputs should be saved in this directory with dates, for example:

- `2026-06-04-skill-review.md`
- `2026-06-04-scenario-simulation.md`
- `2026-06-04-red-team-review.md`

## How To Re-run

1. Start a fresh AI session so the reviewer does not inherit this development conversation.
2. Provide the repository files or a repository link.
3. Use one prompt from the latest version's `prompts/` directory.
4. Ask the reviewer to cite exact files and sections when proposing changes.
5. Save the output as a new dated review file after human review.

Do not tell the reviewer the expected answer. The point is to pressure-test the skill, not confirm prior decisions.
