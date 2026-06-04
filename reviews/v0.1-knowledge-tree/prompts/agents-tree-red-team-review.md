# Prompt: Agents Tree Red-Team Review

Act as a red-team reviewer for the `agents-tree` skill.

Goal: find scenarios where this skill may cause an agent to do the wrong thing, waste tokens, delete human-maintained content, block work incorrectly, or generate stale project knowledge.

Read these files:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`
- `README.md`
- `README.zh.md`

Construct at least 8 failure scenarios.

For each scenario, include:

- Scenario
- Whether current docs already prevent it
- What happens if not prevented
- Rule to add
- Files to change

Prioritize risks around:

- pre-existing or malformed `AGENTS.md`
- human-maintained text protection
- freshness classification
- `last_verified_commit`
- missing critical files or symbols
- conflict blocks
- parent/child instruction conflicts
- keep/skip abuse
- code graph tool absence
- token bloat

End with:

- Executive summary
- Priority fix list
- Files most worth editing first

Do not give generic advice. Focus on failure modes and precise fixes.
