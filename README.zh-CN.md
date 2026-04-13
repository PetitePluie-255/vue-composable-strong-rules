# Vue Composable Strong Rules

[English Version](./README.md)

`vue-composable-strong-rules` 是一个面向 Vue 架构边界的 skill，重点关注：

- 逻辑放置
- 边界纪律
- 特性内最小拆分

它适用于这样的问题：不仅要判断“功能能不能做出来”，还要判断“这段逻辑应该放在哪里”。

## 目标

这个 skill 的目标，是让 Vue 代码库持续保持强边界：

- view 只保留页面级 glue
- 可复用或持续增长的 workflow 逻辑下沉到 feature-local composable
- 已经过重的宿主不再继续承载新行为
- 最小合规架构优先于最小 diff

这个 skill 有意把 Vue SFC 的工程纪律拉向更严格的方向：更接近 React 式的关注点拆分，以及 Java 式的责任分层，而不是脚本式的持续堆积。

## 默认触发场景

默认情况下，下面这些任务应该触发这个 skill：

- Vue 实现任务
- Vue 重构任务
- Vue bugfix，但涉及行为、状态、异步或逻辑放置
- 旧页面上的 Vue 功能变更，即使改动很小

只有纯展示型改动可以跳过，例如：

- 静态模板调整
- 纯文案修改
- 图标替换
- 纯样式修改，且没有有意义的状态或 workflow 决策

## 这个 Skill 解决什么问题

这个 skill 用来帮助判断逻辑应该留在：

- view
- feature-local composable
- feature-local helper
- `provide/inject`
- store

它也会判断：当前宿主是否已经太重，以至于必须先拆分，再继续添加新行为。

## 核心立场

这个 skill 的核心架构立场是：

- Vue SFC 只是语法形式，不降低架构要求
- 现有本地写法不是健康边界的充分证据
- “代码已经写在这里了”不是继续保留漂移边界的正当理由
- 旧页面上的小功能变更，正是持续纠偏的关键时机

## 规则分组

这个 skill 的规则大致分为几组：

- State & Scope
  决定状态应该留在 view、composable、`provide/inject` 还是 store。
- Composable Design
  决定 composable 是否过重、是否应该拆、是否应该抽出共享业务单元。
- Lifecycle & Safety
  约束 watcher、timer、listener、长生命周期订阅等清理问题。
- Scope Discipline
  约束强边界默认值、轻量规划、宿主可承载性、最小合规架构和冲突处理。

## 关键规则

这个 skill 当前最重要的规则有：

- [strong-boundary-default](./rules/strong-boundary-default.md)
- [architecture-planning-gate](./rules/architecture-planning-gate.md)
- [current-host-viability](./rules/current-host-viability.md)
- [minimum-compliant-architecture-priority](./rules/minimum-compliant-architecture-priority.md)
- [boundary-first-minimality](./rules/boundary-first-minimality.md)
- [composable-weight-boundary](./rules/composable-weight-boundary.md)

## 决策流程

实现类任务的推荐流程是：

1. 先读取本地项目规则和仓库约定。
2. 先应用强边界默认值。
3. 做一个轻量的 feature-local 架构规划。
4. 判断当前宿主是否仍然可承载更多逻辑。
5. 比较最小 diff 和最小合规架构。
6. 如果宿主已经过重，或者当前 diff 会继续固化错误边界，就先做最小 feature-local 修正。

## 规划策略

这个 skill 默认包含轻量规划。

它**不会**要求所有 Vue 任务都输出到 `docs`。

只有以下情况才建议写正式计划文件：

- 用户明确要求写计划
- 任务已经大到应该切换到 `superpowers:writing-plans`
- 多人或多 agent 协作，需要一个持久化执行文档

## 维护建议

如果这个 skill 在真实使用里失败，建议按下面几类提 issue：

- false trigger
  纯展示任务却触发了重型架构判断。
- missed trigger
  明明是功能或行为变更，但 skill 没有足够介入。
- over-splitting
  本来应该留在 screen-specific glue 的逻辑，被过度抽离。
- under-splitting
  明明应该拆，却又往过重宿主里加了一层 concern。
- host-viability miss
  当前 component 或 composable 已经过重，但 skill 仍把它判成可继续承载。

## 文件说明

- [SKILL.md](./SKILL.md)
  主入口，定义触发条件、执行流程和输出要求。
- [rules/](./rules)
  主 skill 依赖的细粒度规则文件。
- [agents/openai.yaml](./agents/openai.yaml)
  skill 相关的 agent 配置文件。

## 使用说明

这个 README 是给维护者和使用者看的说明文档。

真正具有约束力的规范，仍然以以下文件为准：

- [SKILL.md](./SKILL.md)
- [rules/](./rules) 下的规则文件
