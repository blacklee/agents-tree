# Agents Tree Review Artifacts

This directory stores AI-assisted review artifacts for the Agents Tree skill.

These files are review records, not the normative specification. The active specification lives in:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/*.AGENTS.md`

## Review Context

- Date: 2026-06-02
- Environment: local Codex sessions
- Inputs: repository documentation, skill files, reference files, and templates
- External tools: no code graph or repository-analysis tool was required for the review prompts
- Purpose: evaluate whether the skill is clear, safe, token-efficient, and executable by future coding agents

The review outputs are advisory. Use them to find gaps, then update the skill specification directly.

## Files

- `agents-tree-skill-review.md`: general completeness and consistency review.
- `agents-tree-scenario-simulation.md`: simulated agent behavior for a cross-module conflict scenario.
- `agents-tree-red-team-review.md`: failure-mode and abuse-case review.
- `prompts/agents-tree-skill-review.md`: prompt used for general review.
- `prompts/agents-tree-scenario-simulation.md`: prompt used for scenario simulation.
- `prompts/agents-tree-red-team-review.md`: prompt used for red-team review.

## How To Re-run

1. Start a fresh AI session so the reviewer does not inherit this development conversation.
2. Provide the repository files or a repository link.
3. Use one prompt from `prompts/`.
4. Ask the reviewer to cite exact files and sections when proposing changes.
5. Save the output as a new dated review file or update the existing artifact after human review.

Do not tell the reviewer the expected answer. The point is to pressure-test the skill, not confirm prior decisions.
