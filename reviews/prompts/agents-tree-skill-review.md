# Prompt: Agents Tree Skill Review

You are an AI agent skill reviewer. Evaluate whether the `agents-tree` skill is complete, clear, safe, and executable.

Read these files:

- `README.md`
- `README.zh.md`
- `AGENTS.md`
- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`

Review goals:

1. Is the skill trigger condition clear?
2. Does an agent know when to create, check, refresh, or review an `AGENTS.md` knowledge tree?
3. Are generated, human-maintained, and conflict sections clear enough to protect human text?
4. Is unresolved conflict behavior safe without blocking unrelated work?
5. Are keep/skip rules lightweight and respectful of existing ignore files?
6. Are cross-module relationships delegated to code graph or code-intelligence tools instead of being stored in `AGENTS.md`?
7. Are there contradictions, repeated rules, vague instructions, or non-executable rules?
8. Can an agent without this skill still understand conflict and freshness state from generated files?
9. Does the skill reduce future reasoning token cost, or does it risk creating documentation burden?

Output format:

- Critical Issues
- Important Issues
- Minor Issues
- Missing Scenarios
- Suggested Edits With Exact File / Section
- Answers To Review Goals
- Overall Readiness Score: 1-10

Prefer concrete, actionable findings. Do not give generic praise.
