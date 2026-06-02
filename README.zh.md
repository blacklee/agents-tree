# Agents Tree

面向 Codex、Claude Code、Cursor Agent、opencode 等编码 Agent 的项目知识树维护 skill。

Agents Tree 的目标不是再造一个通用记忆系统，而是教 Agent 如何在其他项目里维护一棵 `AGENTS.md` 文件树。那棵文件树属于目标项目，和这个 skill 本身无关。

换句话说：`agents-tree` 是维护流程，`AGENTS.md` 文件树是被维护的项目知识层。

> 当前状态：早期项目。这个仓库先定义产品方向、文件契约，以及用 skill 在目标项目里维护知识树的工作流。

## 为什么需要它

大型项目里，Agent 的主要 token 成本往往不是写代码，而是反复做这些事：

- 找相关文件
- 理解模块职责
- 推断历史设计意图
- 判断旧文档是否可信
- 每次改动前重新做一轮架构推理

GitNexus、Graphify、repo map、语义搜索这类工具可以降低“读代码”的成本。Agents Tree 关注另一部分成本：重复推理。

它通过指导 Agent 在目标项目目录中维护一棵 `AGENTS.md` 文件树来解决这个问题。越靠近根目录，内容越像索引；越靠近代码叶子目录，内容越具体。

## 核心思路

Agent 已经知道怎么读 `AGENTS.md`。

Agents Tree 不要求 Agent 学一套新的记忆运行时，而是沿用现有机制：目标项目里的每个重要目录可以拥有自己的 `AGENTS.md`，只描述这个目录子树相关的知识。

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

上层文件回答“Agent 应该去哪里看”。下层文件回答“Agent 在这里动代码前必须知道什么”。

这个 skill 自己不是知识树。它只是维护知识树的方法。知识树保存在目标项目里，跟随目标项目提交、review 和演进；即使没有安装 Agents Tree skill，那些 `AGENTS.md` 文件仍然能被普通 Agent 读取。

## 和普通 Agent Memory 的区别

Agents Tree 不主打“记住所有东西”。

它主打四件事：

- **目录作用域**：知识结构和源码目录结构一致。
- **新鲜度检测**：每份生成知识都记录它基于哪些代码证据。
- **人类可审查**：知识更新体现为普通 Git diff。
- **现有 Agent 兼容**：输出就是普通 `AGENTS.md`，不需要新的运行时。
- **Skill 维护**：可复用的是维护流程；被维护的知识留在目标项目里。

简单说，它更像是：

```text
Verified AGENTS Tree
```

不是：

```text
Agent Memory Database
```

## 知识元数据

每个生成的 `AGENTS.md` 使用 YAML front matter 记录知识来源：

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
---
```

这些元数据让工具和 Agent 可以先问一句：

```text
这份知识现在还可信吗？
```

如果不可信，就不要盲信 `AGENTS.md`，而应该重新扫描代码。

## 新鲜度状态

Agents Tree 计划把知识状态分成三类：

- `VALID`：从 `last_verified_commit` 到当前提交，相关证据没有变化。
- `STALE_WARNING`：相关证据发生变化，但模块形态大体还在。
- `INVALID`：关键文件、符号、职责边界或执行流程变化较大，必须重新理解代码。

理想情况下，判断逻辑不只看 `git diff`，还可以接入 GitNexus、Graphify、静态 import 图、语言服务器等代码智能工具。

## 受管理区块

Agents Tree 区分自动生成内容和人工维护内容。

```md
<!-- agents-tree:generated:start -->
这里放自动生成的模块知识。
<!-- agents-tree:generated:end -->

<!-- agents-tree:human:start -->
这里放人类维护的上下文。
工具不能自动覆盖这一段。
<!-- agents-tree:human:end -->
```

Agent 刷新知识时只能更新自动生成区。人工区默认保留。除非复查发现直接矛盾，否则不能动；发现矛盾时也应该报告冲突，让人类介入。

## 维护方式

Agents Tree 不需要专门的 CLI。

它的核心维护方式是 skill 驱动的对话和文件编辑：

- 人类让使用 Agents Tree skill 的 Agent 创建或更新目标项目里的 `AGENTS.md` 知识树。
- Agent 读取已有知识树，只检查必要的代码证据，然后提出局部修改。
- 人类 review 普通 Git diff。
- Agent 更新时必须保护人工维护区。

以后可以加自动化工具，但自动化只是可选能力。核心契约必须在“skill 说明 + 正常和 Agent 对话 + 手动编辑文件”的场景下就能成立。

## Skill 包结构

可复用的 skill 放在：

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

`SKILL.md` 保持短小，方便 Agent 低成本加载。详细规则放在 `references/`。可复制到目标项目的 `AGENTS.md` 模板放在 `assets/`。

## 什么时候新增文件

只在值得维护知识的目录新增 `AGENTS.md`。

适合生成的目录通常有这些特征：

- 文件很多
- 子目录较多
- 有独立业务职责
- 风险较高
- 有历史兼容逻辑
- Agent 经常访问

根目录文件应该保持短小，负责指路。叶子目录文件才适合承载更具体的实现知识。

## 新鲜度复查

新鲜度可以由人手动判断，也可以在日常对话中让 Agent 判断。

复查者读取元数据，对比记录过的代码证据和当前代码，然后给出状态：

- `VALID`：记录的代码证据仍然支持这份知识。
- `STALE_WARNING`：相关内容变过，知识可能需要局部更新。
- `INVALID`：不能继续信这份知识，必须重新检查代码。

Agent 可以用 Git diff、GitNexus、Graphify、语言服务器或直接读代码来完成复查。关键不在工具，而在于不能默默信任过期知识。

## 刷新流程

当知识需要更新时：

- 检查相关代码证据
- 只更新自动生成区
- 保留人工维护区
- 复查完成后推进 `last_verified_commit`
- 明确指出需要人类判断的冲突

## 更新触发时机

当一次改动影响未来 Agent 需要依赖的稳定知识时，Agent 才应该考虑更新知识树：

- 修改了 `critical_files` 或 `critical_symbols` 记录的文件或符号。
- 模块职责、入口文件、调用流程、职责边界或验证方式发生变化。
- 重要模块目录被新增、删除、重命名或移动。
- 发现现有 `AGENTS.md` 指引和当前代码事实冲突。
- Agent 反复扫描同一个目录，说明这里缺少有用的局部指引。
- 用户明确要求更新、刷新或记录项目知识。

不是每次代码修改都要更新知识树。普通实现细节改动如果没有改变稳定的项目理解，就应该保持知识树不变。

## 推荐的 `AGENTS.md` 结构

生成文件应该短、清晰、稳定：

```md
# Module Overview

# Architecture

# Entry Points

# Common Tasks

# Rules

# Do Not

# Verification
```

根目录文件应该像索引。叶子目录文件才适合写实现细节。

## 设计原则

- 宁可短而可信，也不要长而含糊。
- 根部知识抽象，叶子知识具体。
- 子文件不要重复父文件已经说过的内容。
- 自动生成的结论必须能追溯到文件、符号、调用流或人工说明。
- 过期知识比没有知识更危险。
- Agent 刷新知识时必须保护人工维护段落。
- 代码事实交给代码智能工具，`AGENTS.md` 负责给 Agent 提供行动指引。

## 和其他工具的关系

Agents Tree 可以和 repo map、Agent memory、代码图工具一起工作。

- 代码图工具回答“代码现在实际怎么跑”。
- Memory 工具保存跨会话事实和决策。
- Agents Tree 给 Agent 提供可验证、目录级的项目知识入口。

它不需要替代这些系统。更好的定位是：提供一个可复用的 Agent skill，让 Agent 能在任意目标项目里维护简单、可审查、可被继续读取的 `AGENTS.md` 知识树。

## License

暂未选择许可证。
