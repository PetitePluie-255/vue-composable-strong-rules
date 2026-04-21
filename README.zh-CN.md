# Vue Composable Strong Rules

[English Version](./README.md)

`vue-composable-strong-rules` 现在不只是一个 Vue 架构边界 skill，它更准确地说是一个面向 Vue 实施任务的强规则实施者，重点关注：

- 先判断、后实施
- 逻辑放置
- 边界纪律
- 特性内最小拆分
- 高质量交付覆盖

它适用于这样的问题：不仅要判断“功能能不能做出来”，还要判断：

- “这段逻辑应该放在哪里”
- “在动手前，哪些理解、方案和风险必须先说清楚”
- “这次实现，哪些异常和边界必须明确处理”

## 目标

这个 skill 的目标，是让 Vue 代码库持续保持强边界，同时让实施行为本身更稳定：

- 编码前先给出 implementation judgment
- 存在歧义时先问，而不是猜
- 高影响改动先确认
- 行为类改动必须覆盖正常流程、异常处理、边界条件
- view 只保留页面级 glue
- 可复用或持续增长的 workflow 逻辑下沉到 feature-local composable
- 已经过重的宿主不再继续承载新行为
- 边界正确的实现路径优先于最小 diff

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

这个 skill 用来帮助判断：

- 当前需求到底在改什么行为
- 现在应该直接做、先问，还是先确认
- view
- feature-local composable
- feature-local helper
- `provide/inject`
- store

这些选项共同回答“逻辑应该留在哪里”。

它也会判断：

- 当前宿主是否已经太重，以至于必须先拆分，再继续添加新行为
- 这次实现里哪些异常路径和边界条件是 relevant
- 什么才是这次任务边界正确的最小实现路径

## 结构分层

这个 skill 现在建议按四层理解：

- `Identity`
  强规则、高质量、先判断后实施的 Vue 实施者。
- `Decision Protocol`
  编码前先说明理解、方案、风险；必要时先问或先确认。
- `Implementation Quality Contract`
  行为类改动必须覆盖正常流程、异常处理、边界条件。
- `Domain Rules`
  原有的 composable、state placement、boundary 规则。

## 关键规则

当前最重要的规则包括：

- [implementation-judgment-first](./rules/implementation-judgment-first.md)
- [decision-thresholds](./rules/decision-thresholds.md)
- [implementation-quality-contract](./rules/implementation-quality-contract.md)
- [architecture-planning-gate](./rules/architecture-planning-gate.md)
- [current-host-viability](./rules/current-host-viability.md)
- [minimum-compliant-architecture-priority](./rules/minimum-compliant-architecture-priority.md)

## 和其他 Skill 的关系

这个 skill 不会复制其他 skill 的完整流程，例如：

- `superpowers:brainstorming`
- `superpowers:test-driven-development`
- `superpowers:systematic-debugging`
- `superpowers:verification-before-completion`

它保留的是 Vue 实施现场的本地判断权：

- 这次是否该先问
- 这次是否该先确认
- 这次哪些异常和边界是 relevant
- 逻辑该放在哪里
- 边界正确的最小实现路径是什么

## 默认执行流

实现类任务的推荐流程是：

1. 先读取本地项目规则和仓库约定。
2. 编码前先输出 implementation judgment。
3. 决定下一步是直接实施、先问，还是先确认。
4. 再应用强边界默认值和轻量架构规划。
5. 判断当前宿主是否仍然可承载更多逻辑。
6. 比较最小 diff 和边界正确的最小实现路径。
7. 如果宿主已经过重，或者当前 diff 会继续固化错误边界，就先做最小 feature-local 修正。
8. 实施时显式覆盖质量合同。

## 规划策略

这个 skill 默认包含轻量规划。

它**不会**要求所有 Vue 任务都输出到 `docs`。

只有以下情况才建议写正式计划文件：

- 用户明确要求写计划
- 任务已经大到应该切换到 `superpowers:writing-plans`
- 多人或多 agent 协作，需要一个持久化执行文档

## 维护建议

如果这个 skill 在真实使用里失败，建议按下面几类提 issue：

- missing judgment
  还没把理解、方案、风险说清楚就开始编码。
- missed ask
  本来应该先问，结果直接猜着做了。
- missed confirmation
  跨了高影响阈值却没有先确认。
- quality gap
  只实现了 happy path，没有覆盖相关异常或边界。
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
  主入口，定义触发条件、判断优先的执行流程和输出要求。
- [rules/](./rules)
  主 skill 依赖的细粒度规则文件。
- [agents/openai.yaml](./agents/openai.yaml)
  skill 相关的 agent 配置文件。

## 使用说明

这个 README 是给维护者和使用者看的说明文档。

真正具有约束力的规范，仍然以以下文件为准：

- [SKILL.md](./SKILL.md)
- [rules/](./rules) 下的规则文件
