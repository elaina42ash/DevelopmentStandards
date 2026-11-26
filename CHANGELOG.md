# 规范变更记录

## v1.0.0 — 首个完整迁移基线（本地候选）

### 新增
- 建立核心治理入口与六类专项规范目录。
- 将原始单文件规则拆分为30份专项规范。
- 新增 `DataContracts.md`、`ObjectPooling.md` 与 `StandardsMaintenance.md` 的独立职责定义。
- 将规范读取机制整合至 `AgentWorkflow.md`。
- 建立规范文档体系、版本、部署、兼容及长期维护机制。

### 迁移
- 原始 AGENTS.md 的有效规则按职责归口迁移。
- 原有硬性约束、授权条件、默认方案、推荐原则、参考示例及项目保护要求均有迁移映射；语义一致性仍以持续审计结果为准。
- 原始52个代码及文本示例块逐块核对保留，目录示例随对应专业规范迁移。

### 结构审计修正
- 将正式规则正文从“相关规范”章节移出，恢复规范定位、引用和核心规则的清晰边界。
- 调整 Governance 与 Architecture 的基础依赖分类，消除基础依赖循环；将异步业务一致性改为按条件协作。
- 合并 StandardsMaintenance.md 重复章节，补充 `scope` 字段语义。
- 修正 AGENTS.md 空标题、编译限制表述及专项规范索引。
- 修复 Prefab 与 StateConsistency 中依赖文档排列位置的方向性引用。


### 第三轮语义审计修正（本地候选，未发布）
- 更新 `RulePriority.md` 的主定义映射，指向当前30份专项规范及核心入口的实际文件位置。
- 清理旧版单文件协作章节名称及其他过时文档名称引用，改为当前主定义链接。
- 明确 `General` 规范中 Unity 条件性实现的适用边界；将 UI 表现层职责及业务状态一致性规范的跨引擎适用范围写清楚。
- 在 `DataContracts.md` 增加数据语义主定义索引，明确 Config、RuntimeData、Snapshot、InputData、DisplayData、Request、Command、Result、CommitResult、RequestResult 与 SaveData 的归属。
- 统一 `EventSystem.md` 中 Request、Command、Action、Event 的语义定义，避免重复定义；明确新建事件框架的分发期间订阅、取消订阅及回调执行语义。
- 在 `AGENTS.md` 的新建业务 System 任务索引中补充权威状态与数据契约的条件读取要求。
- 增加关键规则级迁移追踪材料，辅助核对原始条款与最终主定义。

### 兼容性
- 本次规范文档迁移不自动修改任何 Unity 项目代码。
- 既有项目采用存量保护与增量治理；实际代码迁移须独立确认。

### 发布状态
- 本文件用于本地迁移候选基线记录。
- 尚未创建 Git Tag、GitHub Release 或执行 GitHub 推送。
