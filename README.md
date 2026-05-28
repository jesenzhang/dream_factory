<!--
╔══════════════════════════════════════════════════════════════════════╗
║  DreamSeed 种梦计划 — AI创造者大赛  官方 README 模板                ║
║                                                                      ║
║  使用说明：                                                          ║
║  1. 将本模板放在参赛仓库根目录 README.md 的顶部                       ║
║  2. 头图使用 DreamField 官方公开活动图片地址                         ║
║  3. 请保留 DREAMFIELD_README_HEADER_START / END 标识                 ║
║  4. 分割线以下供创作者自由编写项目内容                               ║
╚══════════════════════════════════════════════════════════════════════╝
-->

<!-- DREAMFIELD_README_HEADER_START -->

<p align="center">
  <a href="https://www.dreamfield.top">
    <img src="https://www.dreamfield.top/dream-field/contest-readme/assets/dreamseed-readme-banner.png" alt="DreamSeed 种梦计划参赛作品" width="100%" />
  </a>
</p>

<!-- DREAMFIELD_README_HEADER_END -->
# Dream Factory

> Agent-native Dream Factoryligence platform for managing your own projects, analyzing reference repositories, and exporting model-ready context for coding agents.

**Dream Factory** 是一个面向 Agent 时代的软件项目知识资产管理工具。它用于管理自己的项目 Wiki、扫描任意参考项目、生成架构报告与代码知识图谱，并为 Codex、Claude Code、Cursor、OpenCode、Gemini CLI 等编码 Agent 输出可直接使用的上下文包。

项目目标不是再做一个普通文档站，而是构建一个：

```text
项目资产库 + 参考项目分析器 + Agent 上下文生成器 + 长期项目记忆层
```

---

## 1. Why Dream Factory?

在 Agent 辅助开发中，最大的问题往往不是模型不会写代码，而是模型缺少稳定、结构化、可持续更新的项目上下文。

常见问题包括：

- Agent 不知道项目结构和模块边界
- Agent 找不到某个功能的真实实现位置
- Agent 每次任务都从零开始探索代码
- 参考项目看过之后，很难沉淀成可复用开发资产
- 架构决策、失败经验、重构约束没有长期记忆
- `README.md` 面向人类，不能充分约束 Agent 的行为
- `AGENTS.md`、`CLAUDE.md`、MCP、项目 Wiki、架构报告分散且难以同步

Dream Factory 试图解决这些问题。

---

## 2. Core Idea

Dream Factory 把项目分为两类：

```text
Owned Projects      自己正在开发和长期维护的项目
Reference Projects  用于学习、拆解、借鉴的外部项目
```

对每个项目，Dream Factory 会生成和管理：

```text
.dream_factory/
├── profile.json                 # 项目元数据
├── wiki/                        # 可浏览 Wiki / 文档页
├── reports/                     # 架构报告、参考项目分析报告
├── graphs/                      # 代码知识图谱、业务流程图、依赖图
├── context/                     # Agent-ready context pack
├── memory/                      # 长期项目记忆、经验、决策记录
└── runs/                        # 扫描任务、日志、适配器输出
```

最终目标是让 Agent 可以基于这些资产回答：

```text
这个功能在哪里实现？
这个模块依赖哪些文件？
这个参考项目的架构有哪些可迁移设计？
我应该如何在当前项目中实现类似能力？
这次改动会影响哪些模块？
当前任务应该遵守哪些项目约束？
```

---

## 3. Current Status

> 当前项目处于设计和 MVP 实现规划阶段。

MVP 不追求一次性实现完整 DeepWiki 平台，而是优先验证以下闭环：

```text
注册项目
  ↓
扫描项目
  ↓
生成项目图谱与架构报告
  ↓
生成 Agent Context Pack
  ↓
让 Agent 基于上下文完成定位、分析和开发指导
```

---

## 4. MVP Architecture

MVP 默认采用以下组件组合：

| Component | Role |
|---|---|
| **Understand-Anything** | 默认项目理解引擎，负责代码扫描、知识图谱、Dashboard、问答、Diff Impact |
| **Litho / deepwiki-rs** | 架构报告生成器，负责 C4 文档、模块拆解、技术报告 |
| **TencentDB-Agent-Memory** | 长期项目记忆层，负责保存经验、决策、SOP、参考项目学习结论 |
| **Context Builder** | 自研上下文生成器，负责生成 `AGENTS.md`、`CLAUDE.md`、`feature-map.md` 等 |
| **Project Registry** | 自研项目资产注册中心，负责管理 Owned / Reference 项目 |
| **MCP Gateway** | 后续提供统一 MCP 入口，让 Agent 查询项目资产 |

可选 Adapter：

| Adapter | Status | Purpose |
|---|---|---|
| **CodeGraph** | Optional | 轻量本地代码图谱、符号关系、调用关系 |
| **DeepWiki-Open** | Optional | Wiki / RAG 平台增强 |
| **GitNexus** | Optional, Phase 2 | 多仓库 Agent 上下文、MCP、`AGENTS.md` / `CLAUDE.md` 生态 |
| **OpenDeepWiki** | Optional | 平台化代码知识库参考 |

---

## 5. System Overview

```text
┌──────────────────────────────────────────────────────────────┐
│                      Dream Factory CLI / UI                  │
│ add / scan / report / export-context / remember / serve-mcp  │
└──────────────────────────────────────────────────────────────┘
                              │
┌──────────────────────────────────────────────────────────────┐
│                       Project Registry                       │
│ owned projects / reference projects / tags / scan snapshots  │
└──────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
┌─────────────────┐  ┌─────────────────┐  ┌──────────────────────┐
│ Understand      │  │ Litho            │  │ Agent Memory          │
│ Anything Adapter│  │ Adapter          │  │ Adapter               │
│ graph / QA / UI │  │ C4 / reports     │  │ decisions / SOP / log │
└─────────────────┘  └─────────────────┘  └──────────────────────┘
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                              │
┌──────────────────────────────────────────────────────────────┐
│                    Unified Project Assets                    │
│ profile / wiki / graphs / reports / context / memory / runs  │
└──────────────────────────────────────────────────────────────┘
                              │
┌──────────────────────────────────────────────────────────────┐
│                    Agent Context Builder                     │
│ AGENTS.md / CLAUDE.md / project-profile / feature-map / MCP  │
└──────────────────────────────────────────────────────────────┘
```

---

## 6. Main Capabilities

### 6.1 Project Registry

管理自己的项目和参考项目。

```bash
dream_factory add ./my-app --type owned --name my-app
dream_factory add https://github.com/example/reference-app --type reference --name reference-app
dream_factory list
dream_factory info my-app
```

项目元数据示例：

```json
{
  "id": "my-app",
  "name": "my-app",
  "type": "owned",
  "repoUrl": "https://github.com/example/my-app",
  "localPath": "./repos/my-app",
  "tags": ["tauri", "rust", "video"],
  "purpose": "Main product repository",
  "lastIndexedCommit": "abc123",
  "createdAt": "2026-05-28T00:00:00Z"
}
```

---

### 6.2 Project Scan

扫描项目并生成代码知识图谱、架构报告、上下文资产。

```bash
dream_factory scan my-app
dream_factory scan reference-app --full
```

默认扫描流程：

```text
1. 检查项目路径和 Git 状态
2. 运行 Understand-Anything 生成知识图谱
3. 运行 Litho 生成架构报告
4. 汇总扫描产物
5. 生成 feature-map 和 implementation-locator
6. 更新项目 profile
7. 写入 memory 摘要
8. 生成 Agent Context Pack
```

---

### 6.3 Reference Project Report

针对参考项目生成可迁移设计报告。

```bash
dream_factory report reference-app --kind reference
```

报告包括：

```text
1. 项目定位
2. 技术栈
3. 核心模块地图
4. 关键功能实现位置
5. 架构模式
6. 可迁移设计
7. 不建议照搬的部分
8. 在新项目中的落地建议
9. 给 Agent 的开发指令
```

示例表格：

| Feature | Module | Files | Notes |
|---|---|---|---|
| Authentication | auth | `src/auth/*` | 登录、token、session 管理 |
| Video Playback | player | `src/player/*` | 播放状态、source 选择、控制逻辑 |
| Task Queue | worker | `src/jobs/*` | 后台任务、失败重试、调度 |

---

### 6.4 Agent Context Pack

为不同 Agent 输出模型可读上下文。

```bash
dream_factory export-context my-app --target codex
dream_factory export-context my-app --target claude
dream_factory export-context reference-app --target generic
```

输出结构：

```text
.agent-context/
├── AGENTS.md
├── CLAUDE.md
├── project-profile.json
├── architecture-summary.md
├── feature-map.md
├── implementation-locator.md
├── reference-patterns.md
├── constraints.md
├── memory-summary.md
└── next-actions.md
```

核心目标：

```text
让 Agent 不需要重新探索项目，就能知道：
- 当前项目是什么
- 核心模块在哪里
- 如何构建和测试
- 哪些目录不能乱改
- 关键功能实现位置
- 最近的重要决策
- 参考项目中哪些模式可借鉴
```

---

### 6.5 Memory Layer

使用长期记忆记录项目经验、决策和任务结果。

```bash
dream_factory remember my-app "播放器双 WebView 方案在 Android 上不可用，原因是 Android WebView 生命周期和媒体播放限制不同。"
dream_factory memory search my-app "WebView Android 播放问题"
```

记忆类型：

| Type | Purpose |
|---|---|
| Decision | 架构决策 |
| Lesson | 失败经验 |
| Pattern | 可复用模式 |
| Constraint | 项目约束 |
| Task Summary | 任务总结 |
| Reference Insight | 参考项目学习结论 |

---

### 6.6 MCP Gateway

后续阶段提供统一 MCP 服务，让编码 Agent 直接查询项目资产。

```bash
dream_factory serve-mcp
```

计划暴露资源：

```text
project://{id}/profile
project://{id}/architecture
project://{id}/feature-map
project://{id}/implementation-locator
project://{id}/context
project://{id}/memory
reference://{id}/patterns
reference://{id}/migration-guide
```

计划暴露工具：

```text
project_search
project_locate_feature
project_get_context
project_get_reference_patterns
project_remember
project_impact_analysis
```

---

## 7. Recommended Repository Structure

```text
dream_factory/
├── README.md
├── AGENTS.md
├── package.json
├── tsconfig.json
├── apps/
│   ├── cli/
│   │   └── src/
│   ├── mcp-server/
│   │   └── src/
│   └── web/
│       └── src/
├── packages/
│   ├── core/
│   │   └── src/
│   ├── registry/
│   │   └── src/
│   ├── adapters/
│   │   ├── understand-anything/
│   │   ├── litho/
│   │   ├── memory/
│   │   ├── codegraph/
│   │   ├── deepwiki-open/
│   │   └── gitnexus/
│   ├── context-builder/
│   │   └── src/
│   ├── report-builder/
│   │   └── src/
│   ├── mcp-gateway/
│   │   └── src/
│   └── shared/
│       └── src/
├── templates/
│   ├── AGENTS.md.hbs
│   ├── CLAUDE.md.hbs
│   ├── feature-map.md.hbs
│   ├── implementation-locator.md.hbs
│   ├── reference-report.md.hbs
│   └── memory-summary.md.hbs
├── storage/
│   ├── repos/
│   ├── assets/
│   └── cache/
├── docs/
│   ├── architecture.md
│   ├── adapters.md
│   ├── context-pack.md
│   └── roadmap.md
└── tests/
```

---

## 8. CLI Design

Planned CLI commands:

```bash
# Project registry
dream_factory add <path-or-url> --type owned|reference --name <name>
dream_factory list
dream_factory info <project-id>
dream_factory remove <project-id>

# Scan
dream_factory scan <project-id>
dream_factory scan <project-id> --adapter understand-anything
dream_factory scan <project-id> --adapter litho
dream_factory scan <project-id> --full

# Reports
dream_factory report <project-id> --kind overview
dream_factory report <project-id> --kind reference
dream_factory report <project-id> --kind migration

# Context
dream_factory export-context <project-id> --target codex
dream_factory export-context <project-id> --target claude
dream_factory export-context <project-id> --target generic

# Memory
dream_factory remember <project-id> "<content>"
dream_factory memory search <project-id> "<query>"
dream_factory memory summarize <project-id>

# MCP
dream_factory serve-mcp
```

---

## 9. Adapter Strategy

Dream Factory 不直接把所有开源项目源码合并为一个 monolith，而是通过 Adapter 编排外部工具。

每个 Adapter 需要遵守统一接口：

```ts
export interface DreamFactoryAdapter {
  name: string;
  check(): Promise<AdapterCheckResult>;
  scan(input: ScanInput): Promise<ScanResult>;
  collect(input: CollectInput): Promise<CollectedAsset[]>;
}
```

核心原则：

```text
不要强依赖某个外部工具的内部实现。
不要在 MVP 阶段合并外部项目源码。
优先使用 CLI / 文件产物 / API / MCP 方式集成。
所有外部工具输出都必须归档到 .dream_factory/runs/。
Dream Factory 自己维护统一数据模型。
```

---

## 10. MVP Scope

### In Scope

MVP 需要完成：

- 项目注册
- 本地项目和 Git URL 项目导入
- Understand-Anything Adapter
- Litho Adapter
- 最小 Memory Adapter
- Context Builder
- Reference Report Builder
- CLI
- 本地文件资产存储
- 最小 MCP Gateway

### Out of Scope

MVP 暂不实现：

- 多用户权限系统
- 云端 SaaS
- 完整 Web UI
- 全量 DeepWiki 平台
- 强制集成 GitNexus
- 强制集成 CodeGraph
- 复杂可视化编辑器
- 自动重构执行器
- Agent 自主长时任务调度系统

---

## 11. Roadmap

### Phase 0: Bootstrap

- 初始化 monorepo
- 创建 CLI
- 创建 Project Registry
- 定义统一 schema
- 创建模板目录

### Phase 1: Understand-Anything Integration

- 检查工具安装状态
- 执行项目扫描
- 收集 `knowledge-graph.json`
- 提取模块、文件、功能、关系摘要
- 生成 implementation locator 初版

### Phase 2: Litho Integration

- 执行架构报告生成
- 收集 C4 / Markdown / Mermaid 结果
- 生成 architecture summary
- 将 Litho 输出纳入 reference report

### Phase 3: Context Builder

- 生成 `AGENTS.md`
- 生成 `CLAUDE.md`
- 生成 `project-profile.json`
- 生成 `feature-map.md`
- 生成 `implementation-locator.md`
- 生成 `reference-patterns.md`

### Phase 4: Memory Layer

- 集成 TencentDB-Agent-Memory 或最小本地 memory store
- 保存 scan summary
- 保存 decision / lesson / pattern
- 支持 memory search

### Phase 5: Reference Project Report

- 基于图谱、架构报告、memory 生成参考项目分析报告
- 输出新项目迁移建议
- 输出给 Agent 的实现提示

### Phase 6: MCP Gateway

- 暴露 project resources
- 暴露 search / locate / context / memory tools
- 支持 Codex / Claude Code / Cursor 接入

### Phase 7: Optional Adapters

- CodeGraph Adapter
- DeepWiki-Open Adapter
- GitNexus Adapter
- OpenDeepWiki Adapter

---

## 12. Example Workflow

### Analyze a reference project

```bash
dream_factory add https://github.com/example/player-app --type reference --name player-app
dream_factory scan player-app --full
dream_factory report player-app --kind reference
dream_factory export-context player-app --target codex
```

Then ask the coding agent:

```text
Read .agent-context/reference-patterns.md and .agent-context/implementation-locator.md.
Summarize how the reference project implements video playback.
Then propose a simplified implementation plan for our current project.
Do not copy code directly. Extract architecture patterns and module boundaries only.
```

---

### Manage your own project

```bash
dream_factory add ./my-product --type owned --name my-product
dream_factory scan my-product
dream_factory remember my-product "Use Tauri for desktop shell and keep video playback isolated from overlay UI."
dream_factory export-context my-product --target codex
```

Then ask the coding agent:

```text
Use AGENTS.md and .agent-context/feature-map.md before making changes.
Locate the existing player module and add a minimal playback state machine.
Run the documented tests after changes.
```

---

## 13. Design Principles

Dream Factory follows these principles:

```text
1. Model-ready context over human-only documentation.
2. Project assets must be reproducible from source.
3. External tools are adapters, not the core system.
4. Owned projects and reference projects must be clearly separated.
5. Generated context should be versioned and inspectable.
6. Agent memory must cite or link back to evidence when possible.
7. MVP should prove usefulness before building a large UI.
8. Do not merge multiple upstream projects into one codebase too early.
```

---

## 14. Non-Goals

Dream Factory is not:

- A replacement for IDE / LSP
- A full autonomous coding agent
- A code hosting platform
- A generic note-taking app
- A documentation-only generator
- A monolithic fork of DeepWiki, Litho, GitNexus, or Understand-Anything

It is an orchestration and asset-management layer for Dream Factoryligence.

---

## 15. Related Projects

The following projects inspire or complement Dream Factory:

- Understand-Anything: https://github.com/Lum1104/Understand-Anything
- Litho / deepwiki-rs: https://github.com/sopaco/deepwiki-rs
- TencentDB-Agent-Memory: https://github.com/Tencent/TencentDB-Agent-Memory
- CodeGraph: https://github.com/colbymchenry/codegraph
- DeepWiki-Open: https://github.com/AsyncFuncAI/deepwiki-open
- OpenDeepWiki: https://github.com/AIDotNet/OpenDeepWiki
- GitNexus: https://github.com/abhigyanpatwari/GitNexus
- Microsoft Skills / wiki-agents-md: https://github.com/microsoft/skills/tree/main/.github/plugins/deep-wiki/skills/wiki-agents-md

---

## 16. License

TBD.

Before choosing a final license, verify compatibility with all integrated tools and confirm whether Dream Factory is distributed as:

```text
1. A standalone orchestration tool
2. A plugin collection
3. A hosted platform
4. A private internal development system
```

---

## 17. Development Notes for Agents

Before modifying this repository, coding agents should:

1. Read `README.md`
2. Read `AGENTS.md`
3. Inspect `docs/architecture.md`
4. Check existing adapters before adding new ones
5. Keep external integrations behind adapter interfaces
6. Avoid hardcoding provider-specific or tool-specific paths
7. Preserve local-first behavior
8. Add tests for registry, adapter execution, and context generation

Recommended first implementation task:

```text
Implement the Project Registry and CLI skeleton.
Do not integrate external tools yet.
Create the storage layout, schema definitions, and basic commands:
add, list, info, remove.
Then add tests for local path projects and Git URL projects.
```
