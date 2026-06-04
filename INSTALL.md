# Agents Tree Installation Guide

This guide explains how to install `agents-tree` as an Agent skill for Codex or another coding agent.

The installed skill is this directory:

```text
skills/agents-tree/
```

After installation, the skill guides an agent to create, check, or refresh AGENTS.md-compatible decision guidance in a target project. That target project's guidance still belongs to the target project, not to this skill repository.

## Before Installing

Clone this repository:

```bash
git clone <agents-tree-repo-url>
cd agents-tree
```

If you are developing this repository locally, you can use the current working directory directly.

## Install For Codex

Codex supports user-level and project-level skills.

### User-Level Install

Use this when you want Agents Tree available across projects:

```bash
mkdir -p ~/.agents/skills
cp -R skills/agents-tree ~/.agents/skills/agents-tree
```

If an older version is already installed, remove it before copying:

```bash
rm -rf ~/.agents/skills/agents-tree
cp -R skills/agents-tree ~/.agents/skills/agents-tree
```

### Project-Level Install

Use this when a team wants to commit the skill into a target project so agents working on that project can discover it.

Run this from the target project root:

```bash
mkdir -p .agents/skills
cp -R /path/to/agents-tree/skills/agents-tree .agents/skills/agents-tree
```

Then commit `.agents/skills/agents-tree` to the target project repository.

Project-level installation places the skill inside the target project. The decision guidance it maintains still belongs to that target project.

## How To Invoke In Codex

Invoke explicitly:

```text
$agents-tree Check whether this repository should create AGENTS.md decision guidance. Analyze only; do not edit files.
```

Or:

```text
$agents-tree Create module-level AGENTS.md guidance for src/example while preserving existing human content.
```

Codex may also invoke the skill implicitly from its description. If the skill does not appear after installation, restart Codex.

You can also use Codex's skill list or `$` prompt to confirm that `agents-tree` is visible.

## Install For Other Agents

If your agent supports Agent Skills or a similar mechanism, copy the whole directory into that agent's skill directory:

```text
skills/agents-tree/
├── SKILL.md
├── agents/openai.yaml
├── references/
└── assets/
```

Keep the relative paths for `references/` and `assets/` unchanged because `SKILL.md` refers to those files.

If the agent has no skill mechanism yet, you can use the skill manually:

1. Ask the agent to read `skills/agents-tree/SKILL.md`.
2. When exact rules matter, ask it to read `references/file-contract.md` or `references/maintenance-workflow.md`.
3. When creating target-project `AGENTS.md` guidance, use `assets/*.AGENTS.md` as templates.

## Hooks Are Not Required

Agents Tree is primarily triggered as a skill:

- Explicitly: the user invokes `$agents-tree`.
- Implicitly: the agent decides to use it from the skill description.

Hooks are better suited for enforcement, auditing, or team policy. They are not an MVP dependency for this project. Unless you explicitly need Codex-specific automation, do not configure hooks just to use Agents Tree.

## Common Mistakes

- Do not copy only `SKILL.md`; copy `references/` and `assets/` too.
- Do not use the whole `agents-tree` repository as the skill directory; copy `skills/agents-tree/`.
- Do not treat `assets/*.AGENTS.md` as final target-project guidance; they are templates and still require evidence-based generation.
- Do not configure hooks just to trigger the skill; first verify it with explicit `$agents-tree` invocation.

## Post-Install Check

Use this prompt to test whether the installation works:

```text
$agents-tree Analyze only: which directories in this project are good candidates for AGENTS.md decision guidance? Do not modify files.
```

Expected behavior:

- The agent reads `SKILL.md` first.
- The agent distinguishes check mode from refresh/write mode.
- The agent does not immediately generate many `AGENTS.md` files across the whole project.
- The agent explains which evidence it needs instead of scanning the entire repository without boundaries.

## Update The Skill

Update the same way you installed it: copy `skills/agents-tree/` into the target install location again.

User-level update:

```bash
rm -rf ~/.agents/skills/agents-tree
cp -R skills/agents-tree ~/.agents/skills/agents-tree
```

Project-level update:

```bash
rm -rf .agents/skills/agents-tree
cp -R /path/to/agents-tree/skills/agents-tree .agents/skills/agents-tree
```

If Codex does not recognize the new content after updating, restart Codex.
