# Agents Tree

A skill for maintaining verified `AGENTS.md` guidance for faster agent decisions and fewer reasoning tokens.

Agents Tree is a skill for coding agents such as Codex, Claude Code, Cursor Agent, and opencode. The skill teaches agents how to create and maintain directory-scoped, reviewable, freshness-checked `AGENTS.md` decision guidance inside another project.

The goal is simple: let agents spend fewer tokens deciding where to inspect next, and more of their budget making correct changes.

> Status: early project. This repository currently defines the product direction, file contract, and skill workflow for maintaining AGENTS.md-compatible decision guidance inside target projects.

Installation guide: [`INSTALL.zh.md`](INSTALL.zh.md).

## Why

Large repositories make coding agents repeatedly pay for the same work:

- finding the relevant files
- inferring module responsibilities
- reconstructing historical design intent
- deciding whether old documentation is still trustworthy
- redoing architecture reasoning before each small change

Code graph, repo map, and semantic search tools reduce the cost of reading code. Agents Tree focuses on a different cost: repeated decision-making about where to inspect, what to query, which boundaries to check, and what to ignore first.

That is why short verified guidance can make reasoning faster and spend fewer reasoning tokens. A future agent can read a few decision rules before opening broad source context:

- start from the likely entry point instead of rediscovering it from filenames
- skip tempting files or subtrees that are usually irrelevant for a task shape
- query the right symbol, flow, or boundary in a code-intelligence tool first
- choose a focused verification path instead of guessing a test surface from scratch
- re-check stale guidance only when recorded evidence says it may no longer be valid

The guidance does not replace code reading. It reduces the number of reasoning turns needed before the agent knows which code to read and which evidence to trust.

It does that by guiding an agent to maintain a small decision-guidance layer across the target repository. By default that layer is a tree of `AGENTS.md` files: root files stay short and act like indexes, while lower-level files become more specific about local first hops, skip rules, boundary checks, and verification paths.

## Core Idea

Agents already know how to read `AGENTS.md`.

Agents Tree builds on that existing behavior instead of introducing a separate memory system. Each important directory in the target project can contain an `AGENTS.md` file that describes only the stable decision guidance relevant to that subtree.

```text
project/
├── AGENTS.md
├── src/
│   ├── AGENTS.md
│   ├── video/
│   │   ├── AGENTS.md
│   │   └── player/
│   │       └── AGENTS.md
│   └── socket/
│       └── AGENTS.md
```

Higher files answer "where should the agent go next?" Lower files answer "what first hop, boundary check, skip rule, or verification hint matters before editing here?"

The skill itself is not the target guidance layer. It is the maintenance workflow. The guidance belongs to the project being worked on, is committed with that project, and remains useful even when Agents Tree is not installed.

## Artifact Strategy

Use native `AGENTS.md` mode by default. It has the lowest learning cost because coding agents already know how to discover and apply `AGENTS.md` files.

Use a sidecar decision-router tree only when the target project intentionally wants to keep agent behavior instructions separate from decision guidance. For example, a project may already have strict `AGENTS.md`, `CLAUDE.md`, or `agents/claude.md` files for tool behavior, and may not want generated routing guidance mixed into those files.

In sidecar mode, use one consistent file name such as `decision-router.md` across the tree, and keep a short root `AGENTS.md` entry that tells agents when and how to read it:

```md
For Agents Tree decision guidance, read applicable `decision-router.md`
files from the repository root to the target directory before broad code inspection.
```

Sidecar mode is a trade-off. It keeps instruction files cleaner, but it depends on the root `AGENTS.md` pointer for discovery. Do not create both native `AGENTS.md` guidance and sidecar guidance for the same directory unless a human explicitly asks to migrate or resolve the overlap.

## What Makes It Different

Agents Tree is not meant to be another general-purpose agent memory store.

It is designed around six constraints:

- **Decision compression**: generated guidance should reduce future task-routing decisions.
- **Directory scope**: knowledge follows the same tree as the source code.
- **Freshness checks**: every generated knowledge file records what code evidence it was verified against.
- **Human review**: generated sections are reviewable diffs, and human-maintained sections are protected.
- **Agent compatibility**: the default output is ordinary `AGENTS.md`; optional sidecar mode still routes through a short root `AGENTS.md` pointer.
- **Skill-based maintenance**: the reusable part is the agent workflow; the generated knowledge stays inside the target repository.

Agents Tree may also report lightweight audience-boundary suggestions when human-facing docs such as `README.md` and agent-facing docs such as cross-agent `AGENTS.md` or tool-specific `CLAUDE.md` coexist in one directory. This is advisory: it helps keep human onboarding content and agent decision guidance in the right place, but it does not turn Agents Tree into a general README maintenance tool.

## Knowledge Metadata

Each generated native `AGENTS.md` starts with YAML front matter:

```yaml
---
knowledge_type: module
module: VideoPlayer
last_verified_commit: abc123
critical_files:
  - HPVideoPlayerController.h
  - HPVideoPlayerController.m
  - HPPlayerConfig.h
  - HPPlayerConfig.m
critical_symbols:
  - HPVideoPlayerController
  - HPPlayerConfig
confidence: medium
owner: ai-generated
agents_tree_keep: []
agents_tree_skip: []
---
```

The metadata gives agents and tools enough information to ask: "Is this knowledge still valid, or should I inspect the code again?"

Generated sections should also include a short visible `Knowledge Status` section so ordinary `AGENTS.md` readers can notice when to re-check.

Agents Tree metadata belongs in maintained decision-guidance artifacts: native `AGENTS.md` files, or explicit sidecar files such as `decision-router.md` when the project has chosen sidecar mode. Do not add Agents Tree YAML front matter to human-facing docs such as `README.md` or tool-specific agent docs such as `CLAUDE.md` unless the project already uses its own front matter there, and even then do not add Agents Tree fields.

## Freshness States

Agents Tree classifies knowledge into three states:

- `VALID`: no relevant evidence changed since `last_verified_commit`
- `STALE_WARNING`: relevant evidence changed, but the module shape appears mostly intact
- `INVALID`: critical files, symbols, ownership boundaries, or execution flows changed enough that the agent must rescan the code

The exact classifier should combine Git diffs with optional code graph, code-intelligence, static import graph, or language-server data.

## Managed Sections

Agents Tree treats generated knowledge and human notes differently.

```md
<!-- agents-tree:generated:start -->
Generated module knowledge lives here.
<!-- agents-tree:generated:end -->

<!-- agents-tree:human:start -->
Human-maintained context lives here.
Tools must not rewrite this section automatically.
<!-- agents-tree:human:end -->
```

Agent-driven refreshes may update generated sections. Human sections are preserved unless a review detects a direct contradiction and asks for judgment.

## Maintenance Workflow

Agents Tree does not require a dedicated CLI.

The primary workflow is skill-driven, conversational, and file-based:

- A human asks an agent using the Agents Tree skill to create or update the target project's decision guidance.
- The agent reads the existing native `AGENTS.md` tree or selected sidecar tree, inspects only the needed code evidence, and proposes focused changes.
- The human reviews the resulting Git diff.
- Human-maintained sections remain protected during agent updates.

Automation can be added later, but it should be optional. The core contract must remain useful with only normal agent conversations, the skill instructions, and manual editing.

## Skill Package

The reusable skill lives in:

```text
skills/agents-tree/
├── SKILL.md
├── agents/openai.yaml
├── references/
│   ├── file-contract.md
│   └── maintenance-workflow.md
└── assets/
    ├── root.AGENTS.md
    ├── module.AGENTS.md
    └── leaf.AGENTS.md
```

`SKILL.md` stays concise so agents can load it cheaply. References hold detailed rules. Assets provide native `AGENTS.md` templates that agents can copy into target projects.

## When To Add Files

Create a guidance file only where it earns its keep. In native mode this is an `AGENTS.md`; in sidecar mode this is the selected sidecar file, such as `decision-router.md`.

Good candidates include directories with:

- many files
- several child modules
- independent business responsibility
- high-risk code
- historical compatibility logic
- frequent agent access

Root files should stay short and point agents toward the right subtree. Leaf files may carry more concrete implementation knowledge.

## Freshness Review

Freshness can be reviewed manually or by an agent during normal work.

The reviewer reads metadata, compares the recorded evidence against the current code, and classifies the knowledge:

- `VALID`: the recorded evidence still supports the knowledge.
- `STALE_WARNING`: something changed and the knowledge may need a partial update.
- `INVALID`: the knowledge must not be trusted until the code is inspected again.

An agent may use Git diffs, code graph tools, code-intelligence tools, language-server data, or direct code reads to perform this review. The important part is not the tool; it is that stale knowledge is not silently trusted.

## Refresh Workflow

When knowledge needs an update:

- inspect the relevant code evidence
- update only the generated section
- preserve human-maintained sections
- advance `last_verified_commit` when the review is complete
- mention any conflicts that need human judgment

## Conflict Blocks

If generated knowledge conflicts with a human-maintained section, the agent should record the conflict in the file instead of overwriting either side:

```md
<!-- agents-tree:conflict:start -->
status: unresolved
# Unresolved Agents Tree Conflict

This `AGENTS.md` file contains unresolved project-knowledge conflict.

Do not rely on this file as authoritative guidance for this directory or its subtree until the conflict is resolved.
<!-- agents-tree:conflict:end -->
```

An unresolved conflict blocks knowledge-tree maintenance for that `AGENTS.md` file and its subtree. It does not block unrelated code work or unrelated tree nodes.

The marker is machine-readable, but the body must also be clear to humans and agents that have not installed Agents Tree.

Conflict blocks are allowed to include the minimal cross-module context needed for human judgment. This exception should not leak into ordinary generated sections.

Use relative Markdown links for known files and related `AGENTS.md` nodes inside conflict blocks so reviewers can jump to the evidence.

## Keep And Skip

Agents Tree should first respect existing ignore files such as `.gitignore`, `.ignore`, `.agentignore`, `.cursorignore`, and tool-specific ignore files.

`AGENTS.md` may add only a very small amount of local configuration:

```yaml
agents_tree_keep: []
agents_tree_skip: []
```

Use `agents_tree_skip` for extra paths to ignore, and `agents_tree_keep` for rare paths that must remain visible despite broad ignore patterns.

## Update Triggers

Agents should consider updating the tree when a change affects stable decision guidance future agents need:

- files or symbols recorded in `critical_files` or `critical_symbols` changed
- module responsibility, entry points, call flows, ownership boundaries, or verification steps changed
- important module directories were added, removed, renamed, or moved
- existing `AGENTS.md` guidance conflicts with current code evidence
- agents repeatedly scan the same directory because useful local guidance is missing
- the user explicitly asks to update, refresh, or record project decision guidance

Not every code change should update the tree. Small implementation edits should leave it untouched unless they change durable project understanding.

## Cross-Module Handoff

Each `AGENTS.md` should stay cohesive to its own directory. It should not try to store a complete dependency map.

When a task touches critical symbols, APIs, data shapes, call flows, or ownership boundaries, the agent should use a code graph or code-intelligence tool to find current callers, callees, references, and impact. If that points to another module, the agent should read that module's nearest `AGENTS.md` before editing across the boundary.

In short:

```text
AGENTS.md starts local reasoning.
Code graph tools reveal current cross-module impact.
The next AGENTS.md provides local context for the impacted module.
```

## Recommended `AGENTS.md` Shape

Generated files should stay short and decision-oriented:

```md
# Knowledge Status

# Decision Compression

# Use This File When

# Skip This File When

# First Hop Rules

# Cross-Module Checks

# Verification Hints

# Evidence Notes
```

Root-level files should behave like indexes. Leaf-level files may include implementation details.

## Design Principles

- Prefer trustworthy short context over exhaustive documentation.
- Keep root knowledge abstract and leaf knowledge concrete.
- Prefer task-routing and first-hop rules over directory summaries.
- Keep only generated bullets that change the next action.
- Make skip guidance concrete: if the task is only X, go to Y or query Z instead.
- Do not duplicate parent knowledge in child files.
- Record evidence for every generated claim.
- Treat stale knowledge as worse than missing knowledge.
- Preserve human-maintained notes during agent-driven refresh.
- Use code-intelligence tools for facts; use the selected guidance artifact for agent-facing decisions.

## Relationship To Other Tools

Agents Tree can work alongside repo-map and memory tools.

- Code graph tools explain what the code currently does.
- Memory tools preserve cross-session facts and decisions.
- Agents Tree routes agents through verified, directory-scoped decision guidance.

The intended role is not to replace those systems, but to give agents a reusable skill for maintaining simple, reviewable decision guidance inside any project. Native `AGENTS.md` mode is the default; sidecar decision-router mode exists only when a project explicitly wants that separation.

Common companion tools include:

- [GitNexus](https://github.com/nxpatterns/gitnexus): code intelligence and knowledge graph for agents.
- [Graphify](https://github.com/safishamsi/graphify): queryable knowledge graph for code and project materials.
- [Sourcebot](https://github.com/sourcebot-dev/sourcebot): self-hosted code search and navigation.
- [CodeQL](https://github.com/github/codeql): semantic code analysis and query engine.
- [ast-grep](https://github.com/ast-grep/ast-grep): AST-based structural search and rewrite.
- [Tree-sitter](https://github.com/tree-sitter/tree-sitter): parser foundation used by many code-intelligence tools.
- [Repomix](https://github.com/yamadashy/repomix): AI-friendly repository packing.
- [Gitingest](https://github.com/coderamp-labs/gitingest): prompt-friendly repository extraction.

## License

MIT License.
