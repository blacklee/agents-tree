# Prompt: Agents Tree Decision-Guidance Skill Review

You are an AI agent skill reviewer. Evaluate whether the `agents-tree` skill is complete, clear, safe, token-efficient, and executable for the current decision-guidance version.

Read these files:

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

Review goals:

1. Is the skill trigger condition clear for AGENTS.md-compatible decision guidance, including optional sidecar `decision-router.md` guidance?
2. Does an agent know when to create, check, refresh, or review maintained guidance?
3. Does `SKILL.md` stay concise enough while still telling agents when to read `references/`?
4. Are native `AGENTS.md` mode and sidecar mode clearly separated, with native mode remaining the default?
5. Are generated, human-maintained, and conflict sections clear enough to protect human text?
6. Is unresolved conflict behavior safe without blocking unrelated work?
7. Are keep/skip rules lightweight and respectful of existing ignore files?
8. Are cross-module relationships delegated to code graph or code-intelligence tools instead of being stored as durable guidance?
9. Do generated bullets have to change the next action for a future agent?
10. Does the skill explain how verified guidance can speed reasoning and reduce reasoning tokens without replacing code reading?
11. Are there contradictions, repeated rules, vague instructions, or non-executable rules?
12. Can an agent without this skill still understand conflict and freshness state from generated files?

Output format:

- Critical Issues
- Important Issues
- Minor Issues
- Missing Scenarios
- Suggested Edits With Exact File / Section
- Answers To Review Goals
- Overall Readiness Score: 1-10

Prefer concrete, actionable findings. Do not give generic praise.
