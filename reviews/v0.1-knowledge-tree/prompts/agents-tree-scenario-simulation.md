# Prompt: Agents Tree Scenario Simulation

Simulate being a coding agent with the `agents-tree` skill installed.

Scenario:

- `src/article/` has 30 files.
- `src/article/AGENTS.md` already exists.
- The human section says `ArticleService` only reads articles.
- Current code evidence shows `ArticleService` also owns recommendation ranking.
- `critical_files` includes `src/article/ArticleService.ts`.
- The current task changes the recommendation ranking output shape in `ArticleService`.
- The output is consumed by `src/feed/`.
- The project has `.gitignore` entries for `dist/` and `node_modules/`.
- Root `AGENTS.md` has `agents_tree_skip: ["vendor/"]`.

Using the `agents-tree` skill, answer:

1. Which `AGENTS.md` files would you read?
2. Which class of tools would you use to inspect cross-module impact?
3. Should `src/article/AGENTS.md` be updated, checked only, or marked invalid?
4. Should you read `src/feed/AGENTS.md`?
5. If the human section conflicts with code evidence, what conflict block would you write?
6. Which directories are skipped and why?
7. What content must not be written into `AGENTS.md`?

Output:

- Executive judgment
- Step-by-step action sequence
- Freshness classification
- Conflict block draft
- Skipped paths
- Content that should not be written
- Any stable guidance that may belong in generated content after conflict resolution

Do not rewrite human-maintained text. Do not invent commit hashes. Do not list live dependency maps as durable knowledge.
