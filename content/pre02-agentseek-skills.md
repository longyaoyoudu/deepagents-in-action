# AgentSeek CLI（下）— 用 skills 命令安装开发辅助技能

## Skills 是什么

在 AgentSeek 体系中，**Skill（技能）** 是一种教会 AI 编码助手如何执行特定任务的知识包。每个 Skill 是一个 `SKILL.md` 文件，包含经过验证的工程经验——当你在 Claude Code、Codex、Cursor 等工具中开发时，助手会自动读取并应用这些知识。

目前我们给大家重点提供了两个核心技能：

- **`langchain-dev-guide`** — LangChain / LangGraph / DeepAgents 开发中的踩坑经验和解决方案（20+ 条）
- **`langsmith-trace`** — 利用 LangSmith CLI 拉取和分析 Trace，帮助调试 Agent 执行流程

## 查看内置技能列表

```bash
agentseek skills list
```

```
  AgentSeek Skills (ob-labs/agentseek)

    langsmith-trace
      LangSmith CLI setup, tracing, and trace debugging for AgentSeek backends

    langchain-dev-guide
      LangChain / LangGraph / DeepAgents pitfalls and fixes,
      plus CN model integration (DeepSeek, Qwen, GLM)

    github-repo-cards
      Generate visual repo cards for documentation and social sharing

  Install:
    agentseek skills add --all --global        # all skills
    agentseek skills add --skill <name> -g     # one skill
```

## 安装技能

安装单个技能：

```bash
agentseek skills add --skill langchain-dev-guide -g
agentseek skills add --skill langsmith-trace -g
```

一次安装全部：

```bash
agentseek skills add --all --global
```

> `-g` / `--global` 会将技能安装到项目的 `.agents/skills/` 目录，并自动为支持的编码助手创建符号链接（如 `.claude/skills/`）。

安装完成后，你会看到类似输出：

```
◇  Installed 2 skills
│
│  ✓ .agents/skills/langchain-dev-guide
│    universal: Codex, Cursor, Gemini CLI, Warp, Amp +12 more
│    symlinked: Claude Code
│  ✓ .agents/skills/langsmith-trace
│    universal: Codex, Cursor, Gemini CLI, Warp, Amp +12 more
│    symlinked: Claude Code
│
└  Done!
```

## langchain-dev-guide 技能

这个技能覆盖了 LangChain / LangGraph / DeepAgents 开发中最常遇到的问题，目前包含 20+ 条经过验证的踩坑经验。每条都遵循 **症状 → 原因 → 解决方案 → 经验总结** 的结构。

### 覆盖场景

| 分类 | 涉及问题 |
|------|----------|
| Deep Agents | 模型选择、文件系统后端、禁用默认子 Agent、长期记忆 |
| 模型接入 | OpenAI 兼容接口踩坑、Reasoning 模型集成 |
| 国产模型集成 | DeepSeek / Qwen / GLM / Moonshot 接入（内置 CN Model 模板） |
| Middleware | 执行顺序、state_schema 合并、HITL resume 值 |
| Streaming | stream_events vs stream 选择、多 LLM Token 区分 |
| 多 Agent 编排 | subagents vs handoffs、工具分发模式 |

### 使用示例

安装此技能后，你在编码助手中提问：

```
我想接入通义千问模型，应该怎么配置？
```

助手会查阅技能中的国产模型集成指南，给出两种方案：
1. 通过 OpenAI 兼容接口（改 `OPENAI_API_BASE` 和 `OPENAI_API_KEY`）
2. 通过内置的 CN Model 模板生成自定义 LangChain 集成类

再比如：

```
我想给 Deep Agent 添加一个自定义中间件
```

助手会找到 middleware 相关的指南，推荐装饰器和自定义中间件两种写法，并提醒执行顺序的注意点。

## langsmith-trace 技能

这个技能专注于利用 LangSmith CLI 进行 Trace 调试。它帮助 AI 编码助手学会：拉取线上 Trace 数据，分析执行链路中的瓶颈和错误。

### 工作流程

当你觉得 Agent 的某次运行有问题时，用自然语言描述你的需求：

```
帮我分析上一次运行哪个环节比较慢
```

助手会按照技能中的调试流程：

1. 使用 `langsmith project list` 找到对应项目
2. 使用 `langsmith trace list` 拉取最近的 Trace
3. 使用 `langsmith trace get <id>` 获取详细层级
4. 分析各节点耗时，生成时间线，给出优化建议

### 使用前提

- 项目已开启 LangSmith Tracing（`.env` 中 `LANGSMITH_TRACING=true`）
- 已安装 LangSmith CLI：`curl -sSL https://raw.githubusercontent.com/langchain-ai/langsmith-cli/main/scripts/install.sh | sh`
- 已配置 `LANGSMITH_API_KEY` 环境变量

## 推荐的编码助手

视频中推荐使用 **Claude Code** 或 **Codex** 来配合这些技能，它们对 Skill 的执行最为积极。如果使用 Cursor 等 IDE，遇到技能未被自动调用的情况，可以在提问时直接提及技能名称来强制触发。

## 更新技能

技能会随着社区反馈持续更新。更新已安装的技能：

```bash
npx skills update
```

## 常用命令速查

```bash
# 查看内置技能列表
agentseek skills list

# 安装单个技能
agentseek skills add --skill langchain-dev-guide -g

# 安装全部技能
agentseek skills add --all --global

# 更新已安装技能
npx skills update
```

## 小结

本章你学会了：

1. 使用 `agentseek skills list` 查看可用技能
2. 使用 `agentseek skills add` 安装技能到项目
3. `langchain-dev-guide` — 开发阶段的踩坑指南（模型接入、中间件、流式输出等）
4. `langsmith-trace` — 调试阶段的 Trace 分析（拉取 Trace、分析瓶颈、定位问题）

遇到问题欢迎在 [GitHub Issues](https://github.com/ob-labs/agentseek/issues) 中反馈或直接提 PR，我们会持续补充到技能中形成正循环。
