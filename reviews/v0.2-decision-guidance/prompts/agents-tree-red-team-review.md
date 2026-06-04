# Prompt: Agents Tree Decision-Guidance Red-Team Review

Act as a red-team reviewer for the current `agents-tree` decision-guidance skill.

Goal: find scenarios where this skill may cause an agent to do the wrong thing, waste tokens, delete human-maintained content, block work incorrectly, choose the wrong artifact strategy, or generate stale decision guidance.

Read these files:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`
- `skills/agents-tree/agents/openai.yaml`
- `README.md`
- `README.zh.md`
- `INSTALL.md`
- `INSTALL.zh.md`

Construct at least 10 failure scenarios.

For each scenario, include:

- Scenario
- Whether current docs already prevent it
- What happens if not prevented
- Rule to add or clarify
- Files to change

Prioritize risks around:

- pre-existing or malformed native `AGENTS.md`
- sidecar `decision-router.md` mode and root pointer discovery
- overlap between native guidance and sidecar guidance
- human-maintained text protection
- freshness classification and `last_verified_commit`
- missing critical files or symbols
- conflict blocks
- parent/child instruction conflicts
- keep/skip abuse
- code graph or code-intelligence tool absence
- token bloat from over-documentation
- `SKILL.md` being too terse after details moved into `references/`
- README claims about faster reasoning and fewer reasoning tokens

End with:

- Executive summary
- Priority fix list
- Files most worth editing first

Do not give generic advice. Focus on failure modes and precise fixes.
