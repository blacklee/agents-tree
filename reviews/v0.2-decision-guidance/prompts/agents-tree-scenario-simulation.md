# Prompt: Agents Tree Decision-Guidance Scenario Simulation

Simulate being a coding agent with the `agents-tree` skill installed.

Use the current decision-guidance version of the skill, not the older knowledge-tree framing.

Read these files before answering:

- `skills/agents-tree/SKILL.md`
- `skills/agents-tree/references/file-contract.md`
- `skills/agents-tree/references/maintenance-workflow.md`
- `skills/agents-tree/assets/root.AGENTS.md`
- `skills/agents-tree/assets/module.AGENTS.md`
- `skills/agents-tree/assets/leaf.AGENTS.md`

## Scenario A: Native AGENTS.md Mode

- `src/article/` has 30 files.
- `src/article/AGENTS.md` already exists and has valid managed markers.
- The human section says `ArticleService` only reads articles.
- Current code evidence shows `ArticleService` also owns recommendation ranking.
- `critical_files` includes `src/article/ArticleService.ts`.
- The current task changes the recommendation ranking output shape in `ArticleService`.
- The output is consumed by `src/feed/`.
- The project has `.gitignore` entries for `dist/` and `node_modules/`.
- Root `AGENTS.md` has `agents_tree_skip: ["vendor/"]`.

Answer:

1. Which guidance files would you read?
2. Which class of tools would you use to inspect cross-module impact?
3. Should `src/article/AGENTS.md` be updated, checked only, or marked invalid?
4. Should you read `src/feed/AGENTS.md`?
5. If the human section conflicts with code evidence, what conflict block would you write?
6. Which directories are skipped and why?
7. What content must not be written into maintained guidance?

## Scenario B: Sidecar Decision-Router Mode

- The target project has a strict root `AGENTS.md` used only for coding-agent behavior rules.
- Root `AGENTS.md` contains a pointer telling agents to read applicable `decision-router.md` files from the repository root to the target directory before broad code inspection.
- `src/payments/decision-router.md` exists and has valid Agents Tree metadata and managed sections.
- `src/payments/AGENTS.md` also exists, but it is human-owned project instruction text without Agents Tree markers.
- The current task changes payment authorization behavior shared by `src/api/`.

Answer:

1. Which files would you read first, and in what order?
2. Which artifact should receive generated decision-guidance updates?
3. What must not be added to `src/payments/AGENTS.md`?
4. What should happen if native and sidecar guidance overlap in the same directory?
5. What should happen if native and sidecar generated guidance overlap by ancestry across the same covered subtree?
6. Which code-intelligence query target would you seek before editing across the API boundary?

Output:

- Executive judgment
- Step-by-step action sequence
- Freshness classification
- Conflict block draft if needed
- Skipped paths
- Content that should not be written
- Stable guidance that may belong in generated content after conflict resolution

Do not rewrite human-maintained text. Do not invent commit hashes. Do not list live dependency maps as durable knowledge.
