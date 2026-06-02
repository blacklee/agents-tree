# Agents Tree Project Instructions

## Project Purpose

This project builds a skill for maintaining verified `AGENTS.md` trees in other projects.

The product should help agents reduce repeated architecture reasoning by teaching them how to maintain directory-scoped project knowledge with freshness metadata.

The Agents Tree skill is not the project knowledge tree. The skill is the reusable maintenance workflow. The `AGENTS.md` tree belongs to the target repository where the skill is applied.

Do not frame this project as a generic memory system. Its core identity is:

```text
Verified AGENTS Tree
```

## Current Stage

The repository is in the early design stage.

Before adding implementation code or automation, preserve the product contract described in `README.md`:

- a reusable skill for maintaining directory-scoped `AGENTS.md` files in target repositories
- metadata-backed freshness checks
- generated and human-maintained section boundaries
- compatibility with existing `AGENTS.md`-aware agents
- optional integration with code-intelligence tools

## Architecture Direction

Prefer a skill-and-file-contract-first architecture.

The project must remain useful without a dedicated CLI. The primary workflow is:

- humans ask agents using this skill to create, check, or refresh the target project's tree
- agents inspect only the needed code evidence
- agents update generated sections through normal file edits
- humans review ordinary Git diffs
- manual editing remains a first-class maintenance path

Avoid adding servers, dashboards, embeddings, databases, background daemons, or required CLIs until the skill workflow and file contract are proven useful.

## File Contract

Generated `AGENTS.md` files should use YAML front matter for knowledge metadata.

Expected metadata fields:

```yaml
knowledge_type: module
module: ExampleModule
last_verified_commit: abc123
critical_files: []
critical_symbols: []
confidence: medium
owner: ai-generated
```

Generated content must be placed inside:

```md
<!-- agents-tree:generated:start -->
<!-- agents-tree:generated:end -->
```

Human-maintained content must be placed inside:

```md
<!-- agents-tree:human:start -->
<!-- agents-tree:human:end -->
```

Refresh logic must preserve human sections unless it detects a direct contradiction. In that case, report the conflict and require human review instead of overwriting the section.

## Knowledge Rules

- Root `AGENTS.md` files should act as indexes, not encyclopedias.
- Child `AGENTS.md` files should not repeat parent guidance.
- Leaf files may include implementation details only when they are stable enough to be useful.
- Every generated claim should be traceable to files, symbols, imports, execution flows, or explicit human notes.
- Stale knowledge should be surfaced clearly instead of silently trusted.

## Implementation Rules

- Keep the MVP dependency-light.
- Prefer plain files and Git diffs before adding persistent storage.
- Prefer deterministic scanners before LLM-generated summaries.
- Make LLM usage conversational, explicit, and reviewable.
- Keep agent-facing status messages concise enough to consume directly.
- Do not add fallback behavior that hides stale or invalid knowledge.

## Do Not

- Do not turn this into a general agent memory database.
- Do not overwrite human-maintained sections automatically.
- Do not generate large root-level knowledge dumps.
- Do not treat unchanged files as proof that dependent symbols or flows are unchanged.
- Do not claim knowledge is valid without checking its recorded evidence.

## Verification

When implementation or automation exists, verify changes with the narrowest relevant check first.

Expected future review paths:

- inspect the relevant `AGENTS.md` metadata
- compare recorded evidence with current code
- review generated-section diffs
- confirm human-maintained sections were preserved

If tests are added, keep fixtures small and focused on the file contract, freshness classification, and human-section preservation.
