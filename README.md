# Compound Engineering for OpenClaw

> AI 工程实践框架，让每一次工作都让下一次更容易。

[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Ready-blue.svg)](https://github.com/openclaw/openclaw)

## 理念

**每一次工程工作，都应该让下一次更容易，而不是更难。**

传统开发会累积技术债务，每一次功能都增加复杂性，代码库随着时间推移越来越难维护。

Compound Engineering 反转了这个逻辑：

- **80% 在规划和审查，20% 在执行**
- 规划充分后再写代码
- 审查捕获问题并沉淀经验
- 将知识固化以便复用
- 保持高质量，让未来的改动更容易

## 工作流

```
Brainstorm → Plan → Work → Review → Compound → Repeat
    ↑
  Ideate（可选 — 需要发现改进点时使用）
```

| 命令 | 用途 |
|------|------|
| `/ce:brainstorm` | 头脑风暴，通过对话探索需求，产出需求文档 |
| `/ce:ideate` | 发掘高价值的项目改进点（较少使用） |
| `/ce:plan` | 将需求文档转化为可执行的技术计划 |
| `/ce:work` | 按计划执行，使用 git worktree 管理任务 |
| `/ce:review` | 多角色 agent 并行代码审查，合并去重产出报告 |
| `/ce:compound` | 记录已解决的问题，知识积累复利 |

## 快速开始

### 安装

```bash
# 1. 安装 compound-engineering 插件
bunx @every-env/compound-plugin install compound-engineering --to openclaw

# 2. 启用插件
openclaw plugins enable compound-engineering

# 3. 重启 Gateway
openclaw gateway restart

# 4. 确认加载
openclaw skills list | grep ce:
```

### 基本使用

安装后，在 OpenClaw 对话中直接输入命令即可：

```bash
# 头脑风暴，探索需求
/ce:brainstorm

# 制定实施计划
/ce:plan

# 代码审查（审查当前分支）
/ce:review

# 记录刚解决的问题
/ce:compound
```

## 核心命令详解

### /ce:brainstorm — 需求探索

通过对话协作，将模糊的想法打磨成清晰的需求文档。

**使用场景：**
- 功能需求模糊，需要厘清方向
- 不确定要做什么，想先讨论
- 问题有多个解决方案，犹豫不决
- 听到「我们来头脑风暴一下」时

**输出：** 需求文档（需求文档/轻量PRD）

**特点：**
- 一次只问一个问题，不批量提问
- 解决产品决策（用户行为、边界、成功标准）
- 不涉及实现细节（不提前设计代码）
- 根据工作规模调整文档详细程度

**示例：**
```
你：帮我头脑风暴一下商品评论的 AI 总结功能
AI：好的，让我先了解一下现有系统... 你希望这个总结是自动生成还是用户触发？
你：自动
AI：理解了。那么触发时机是在评论发布后多久？1小时？24小时？
...
（继续深入，最终产出需求文档）
```

### /ce:plan — 技术计划

接收需求文档或功能描述，输出可执行的技术计划。

**使用场景：**
- 头脑风暴完成后，需要具体实施步骤
- 有明确的功能想法，想知道怎么实现
- 听到「怎么实现」「计划一下」「制定计划」时

**输入：** 需求文档路径，或功能描述
**输出：** 技术实施计划（含文件列表、依赖、风险、测试场景）

**特点：**
- 研究现有代码库模式后再制定计划
- 决策 + 理由，不是单纯的任务列表
- 给出测试场景，不只是实现步骤
- 计划应可独立存在，不依赖特定工具

**示例：**
```
你：/ce:plan docs/plans/product-review-summary.md
AI：（研究代码库 → 分析依赖 → 制定计划）
输出：商品评论总结功能的实施计划
- 影响范围：评论服务、通知服务、缓存层
- 需要新增文件：src/services/comment-summary.ts
- 关键依赖：AI API、缓存策略
- 测试场景：正常流程、API 超时、部分失败...
```

### /ce:work — 执行计划

按技术计划执行，使用 git worktree 隔离开发。

**使用场景：**
- 计划制定完成，可以开始编码时
- 需要并行处理多个任务时
- 听到「开始做」「执行计划」时

**特点：**
- 使用 git worktree 创建独立开发分支
- 任务进度跟踪
- 不偏离计划范围

### /ce:review — 多角色代码审查

使用多组并行 agent 从不同角度审查代码变更。

**使用场景：**
- 创建 PR 前
- 实现过程中需要反馈
- 听到「审查一下」「帮我看看代码」时

**审查维度（自动多角色并行）：**

| 角色 | 审查重点 |
|------|---------|
| 正确性审查 | 逻辑正确性、边界条件、错误处理 |
| 安全审查 | 注入、XSS、权限、敏感数据 |
| 性能审查 | 复杂度、数据库查询、缓存 |
| 一致性审查 | 与现有代码风格、架构的一致性 |
| 测试审查 | 测试覆盖、测试质量 |
| 可维护性审查 | 耦合度、复杂度、命名 |
| 架构审查 | 设计模式、依赖方向 |

**模式：**

| 模式 | 说明 |
|------|------|
| `mode:report-only` | 仅报告，不做任何修改 |
| `mode:autofix` | 自动修复安全可修复的问题，不交互 |
| `mode:headless` | 程序化调用，输出结构化文本 |

**示例：**
```
你：/ce:review mode:report-only
AI：启动多角色审查...
输出：综合审查报告（含各角色发现 + 严重程度评级）
```

### /ce:compound — 知识沉淀

记录刚解决的问题，让下次遇到同样问题时能快速解决。

**使用场景：**
- 刚解决了一个棘手的问题
- 学到了有价值的经验
- 听到「把这个记下来」「沉淀一下」时

**输出：** `docs/solutions/` 目录下的结构化文档（含 YAML frontmatter）

**为什么叫 Compound？**

> 第一次解决问题需要研究。把它记录下来，下次遇到同样问题只需几分钟。知识在积累中复利。

**示例：**
```
你：/ce:compound
AI：请问解决了什么问题？
你：修复了用户头像在 HTTPS 页面显示被浏览器阻止的问题
AI：（通过对话提取关键信息，生成结构化文档）
输出：docs/solutions/2026-04-04-fix-avatar-https-block.md
```

## Reviewer Agents（40+ 专项审查）

除了 CE 工作流命令，本插件还包含 40+ 个专项 reviewer agents，可独立使用：

| Agent | 用途 |
|-------|------|
| `agent-security-reviewer` | 综合安全审查 |
| `agent-security-lens-reviewer` | 安全视角专项审查 |
| `agent-security-sentinel` | 主动安全威胁检测 |
| `agent-performance-reviewer` | 性能问题审查 |
| `agent-performance-oracle` | 性能预测和建议 |
| `agent-architecture-strategist` | 架构战略分析 |
| `agent-native-architecture` | 原生架构模式 |
| `agent-testing-reviewer` | 测试覆盖和质量审查 |
| `agent-correctness-reviewer` | 正确性审查 |
| `agent-coherence-reviewer` | 代码一致性审查 |
| `agent-design-lens-reviewer` | 设计视角审查 |
| `agent-feasibility-reviewer` | 可行性评估 |
| `agent-product-lens-reviewer` | 产品视角审查 |
| `agent-maintainability-reviewer` | 可维护性审查 |
| `agent-reliability-reviewer` | 可靠性审查 |
| `agent-code-simplicity-reviewer` | 代码简洁性审查 |
| `agent-data-migrations-reviewer` | 数据迁移审查 |
| `agent-api-contract-reviewer` | API 契约审查 |
| `agent-schema-drift-detector` | 数据库模式漂移检测 |
| `agent-pattern-recognition-specialist` | 模式识别 |
| `agent-kieran-rails-reviewer` | Rails 专项（DHH 风格） |
| `agent-kieran-python-reviewer` | Python 专项 |
| `agent-kieran-typescript-reviewer` | TypeScript 专项 |
| `agent-julik-frontend-races-reviewer` | 前端竞态条件审查 |
| `agent-dhh-rails-reviewer` | DHH Rails 风格审查 |
| `agent-learnings-researcher` | 从历史经验中学习 |
| `agent-best-practices-researcher` | 最佳实践研究 |
| `agent-git-history-analyzer` | Git 历史分析 |
| `agent-issue-intelligence-analyst` | Issue 智能分析 |

## 工具类 Skills

| Skill | 用途 |
|-------|------|
| `git-worktree` | Git worktree 管理 |
| `git-commit` | 规范化 commit |
| `git-commit-push-pr` | Commit + Push + PR |
| `agent-browser` | 浏览器自动化 |
| `test-browser` | 浏览器测试 |
| `frontend-design` | 前端设计 |
| `gemini-imagegen` | 图片生成 |
| `test-xcode` | Xcode 测试 |
| `todo-create` | 创建任务 |
| `todo-triage` | 任务分类 |
| `todo-resolve` | 解决任务 |
| `orchestrating-swarms` | 多 Agent 协作编排 |
| `proof` | 数学/逻辑证明 |
| `dspy-ruby` | DSPy Ruby 集成 |
| `deploy-docs` | 文档部署 |
| `rclone` | 云存储管理 |

## 工作流循环

```
每次循环都会复利：
- 头脑风暴让计划更清晰
- 计划让未来的计划更准确
- 审查捕获更多问题
- 模式被记录复用
```

```
第1次：解决A问题 → 记录A
第2次：解决B问题 → 记录B（同时复用A的经验）
第3次：解决C问题 → 很快（因为有A+B的知识库）
...
n次后：你有了一个不断增长的最佳实践库
       新问题 → 搜索知识库 → 找到类似经验 → 快速解决
```

## 文件结构

```
compound-engineering/
├── index.ts                  # OpenClaw 插件入口
├── openclaw.plugin.json      # 插件配置（90个skills）
├── package.json
└── skills/
    ├── ce-brainstorm/        # 头脑风暴
    ├── ce-plan/              # 技术计划
    ├── ce-work/              # 执行计划
    ├── ce-review/            # 代码审查
    ├── ce-compound/          # 知识沉淀
    ├── ce-ideate/            # 改进点发掘
    ├── agent-*/              # 40+ 专项 reviewer agents
    └── [工具类skills]/
```

## 与 Claude Code 的关系

本插件来自 [EveryInc/compound-engineering-plugin](https://github.com/EveryInc/compound-engineering-plugin)，使用官方提供的 OpenClaw 适配器转换。

原版支持：Claude Code、Cursor、OpenCode、Codex、Pi、GitHub Copilot、Kiro、Windsurf、OpenClaw、Qwen Code。

## 进阶用法

### 组合使用

```bash
# 完整功能开发流程
/ce:brainstorm        # 探索需求
  → 产出需求文档

/ce:plan             # 制定技术计划
  → 产出实施计划

/ce:work             # 执行计划
  → 完成代码

/ce:review           # 代码审查
  → 修复审查发现

/ce:compound          # 沉淀经验
  → 知识库 +1
```

### 与其他工具配合

```bash
# 先用 git-worktree 创建隔离开发环境
/git-worktree add feature-x

# 在新分支上工作
/ce:brainstorm
/ce:plan
/ce:work
/ce:review

# 完成后
/git-commit-push-pr
```

### Reviewer Agents 独立使用

不需要完整 CE 工作流时，可直接调用单个 reviewer：

```
请用 agent-security-reviewer 审查 src/auth/login.ts
请用 agent-performance-reviewer 审查 src/api/users.py
```

## 许可

MIT License — 可自由使用、修改、分发。
