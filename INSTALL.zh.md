# Agents Tree 安装指南

这份文档说明如何把 `agents-tree` 作为一个 Agent skill 安装到 Codex 或其他编码 Agent 中。

安装的是这个仓库里的 skill：

```text
skills/agents-tree/
```

安装后，skill 会指导 Agent 在目标项目里创建、检查或刷新 `AGENTS.md` 决策压缩树。目标项目里的决策压缩树仍然属于目标项目，和这个 skill 仓库不是同一件东西。

## 安装前确认

你需要拿到本仓库源码：

```bash
git clone <agents-tree-repo-url>
cd agents-tree
```

如果你是在本地开发这个仓库，也可以直接使用当前工作目录。

## 安装到 Codex

Codex 支持用户级和项目级 skill。

### 用户级安装

适合你希望在所有项目里都能使用 Agents Tree。

```bash
mkdir -p ~/.agents/skills
cp -R skills/agents-tree ~/.agents/skills/agents-tree
```

如果你已经安装过旧版本，可以先删除旧目录再复制：

```bash
rm -rf ~/.agents/skills/agents-tree
cp -R skills/agents-tree ~/.agents/skills/agents-tree
```

### 项目级安装

适合团队希望把这个 skill 随目标项目一起提交，让参与该项目的 Agent 都能发现它。

在目标项目根目录执行：

```bash
mkdir -p .agents/skills
cp -R /path/to/agents-tree/skills/agents-tree .agents/skills/agents-tree
```

然后把 `.agents/skills/agents-tree` 提交到目标项目仓库。

项目级安装会把 skill 放进目标项目；但它维护的 `AGENTS.md` 决策压缩树仍然是目标项目自己的决策压缩树。

## Codex 中如何调用

显式调用：

```text
$agents-tree 检查这个仓库是否适合创建 AGENTS.md 决策压缩树，只分析不改文件。
```

或者：

```text
$agents-tree 为 src/example 创建一个模块级 AGENTS.md，保护现有人工内容。
```

Codex 也可以根据 skill 的描述进行隐式调用。如果安装后没有出现在 skill 列表里，重启 Codex。

你也可以用 Codex 的 skill 列表或 `$` 提示确认 `agents-tree` 是否可见。

## 安装到其他 Agent

如果你的 Agent 支持 Agent Skills 或类似机制，把整个目录复制到它的 skill 目录：

```text
skills/agents-tree/
├── SKILL.md
├── agents/openai.yaml
├── references/
└── assets/
```

必须保持 `references/` 和 `assets/` 的相对路径不变，因为 `SKILL.md` 会引用这些文件。

如果 Agent 暂时没有 skill 机制，也可以手动使用：

1. 在对话中要求 Agent 读取 `skills/agents-tree/SKILL.md`。
2. 需要详细规则时，再让它读取 `references/file-contract.md` 或 `references/maintenance-workflow.md`。
3. 创建目标项目的 `AGENTS.md` 时，可使用 `assets/*.AGENTS.md` 作为模板。

## 不需要安装 Hooks

Agents Tree 的核心触发方式是 skill：

- 显式：用户用 `$agents-tree` 调用。
- 隐式：Agent 根据 skill 描述判断是否使用。

Hooks 更适合做强制检查、审计或团队策略，不是这个项目的 MVP 依赖。除非你明确需要 Codex 专属自动化，否则不要为了使用 Agents Tree 额外配置 hooks。

## 常见错误

- 不要只复制 `SKILL.md`；必须连同 `references/` 和 `assets/` 一起复制。
- 不要把整个 `agents-tree` 仓库当作 skill 目录；应复制里面的 `skills/agents-tree/`。
- 不要把 `assets/*.AGENTS.md` 直接当作目标项目的最终知识；它们只是模板，仍需 Agent 按代码证据生成内容。
- 不要为了触发 skill 先配置 hooks；先用显式 `$agents-tree` 调用验证。

## 安装后自检

可以用下面的提示测试安装是否生效：

```text
$agents-tree 只分析当前项目：哪些目录适合建立 AGENTS.md 决策压缩树？不要修改文件。
```

理想行为：

- Agent 会先读取 `SKILL.md`。
- Agent 会区分“检查模式”和“刷新/写入模式”。
- Agent 不会立刻全项目生成大量 `AGENTS.md`。
- Agent 会说明需要查看哪些证据，而不是无边界扫描整个仓库。

## 更新 skill

更新方式和安装方式相同：重新复制 `skills/agents-tree/` 到对应安装目录。

用户级更新：

```bash
rm -rf ~/.agents/skills/agents-tree
cp -R skills/agents-tree ~/.agents/skills/agents-tree
```

项目级更新：

```bash
rm -rf .agents/skills/agents-tree
cp -R /path/to/agents-tree/skills/agents-tree .agents/skills/agents-tree
```

更新后如果 Codex 没有识别新内容，重启 Codex。
