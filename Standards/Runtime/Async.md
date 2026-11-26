---
id: runtime.async
title: Unity 异步与 UniTask 规范
category: Runtime
scope: Unity
---

# Unity 异步与 UniTask 规范

## 规范定位与适用范围

定义 UniTask 使用条件、生命周期、取消令牌、异常观察及 Unity 执行阶段。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

## 相关规范

### 条件协作
- [异步业务与状态一致性规范](StateConsistency.md)：涉及业务请求、状态竞争、重入、取消、提交有效性或跨 System 一致性时适用。
- [日志与异常处理规范](Logging.md)
- [资源管理规范](ResourceManagement.md)
- [对象池与实例复用规范](ObjectPooling.md)
- [EventSystem 与事件协作规范](../Architecture/EventSystem.md)

## UniTask 使用规范

1. 开始异步实现前，必须先确认项目已经安装并可引用 `com.cysharp.unitask`，目标平台、程序集依赖和现有模块架构允许使用 UniTask。未安装时不得擅自添加包或创建新的异步框架，必须先向用户报备并取得确认。
2. 不需要等待或跨帧执行的逻辑，优先使用同步调用、事件或回调，不得为了形式强制改成 UniTask。
3. 新增的 Unity PlayerLoop、生命周期等待、延迟、UnityWebRequest、Addressables、动画时序和跨帧流程，默认优先使用 `UniTask` 或 `UniTask<T>`，避免新增 `System.Threading.Tasks.Task` 和协程实现。
4. 与第三方 API、JavaScript Bridge、公共接口或现有稳定契约对接时，可以保留 `Task`、`Task<T>`、`IEnumerator` 或回调；异步模型转换应限制在明确的适配边界内。
5. 依赖 Unity `YieldInstruction`、需要作为 `IEnumerator` 组合，或属于现有稳定协程调度框架的流程，可以继续使用协程，不得为了统一形式强制迁移。
6. 本规范默认约束新增代码和已经获得用户确认的异步重构，不要求主动批量迁移现有 Task、协程或回调代码。修改公共异步接口或跨模块调用契约前，必须按公共基础模块变更进行报备。 公开异步接口即使签名未变化，实际完成时机、等待与取消范围及行为发生变化时，仍应按公开调用契约变更处理。 已有合法异步技术契约的保护、规则允许的技术选型及真实冲突处理遵循[规则优先级与冲突处理规范](../Governance/RulePriority.md)，不改变本节规定的 UniTask 适用条件和迁移确认要求。
7. 每个持续或跨帧 UniTask 必须明确生命周期所有者和取消时机，并显式传递 `CancellationToken`。MonoBehaviour 销毁、禁用和普通 C# 对象释放必须分别采用与实际生命周期一致的取消策略。 取消令牌必须匹配真正的执行所有权与有效范围，不机械将外层页面或组件的生命周期作为所有业务的取消条件；业务取消与提交语义遵循[异步业务与状态一致性规范](StateConsistency.md)。 异步资源加载、池化对象及缓冲区的实际所有权和清理时机须分别确定，不得将取消等待视为底层资源已释放或对象可立即复用。
8. 返回的 UniTask 必须被 `await`、明确调用 `.Forget()` 或通过约定方式转交所有权；使用 `.Forget()` 时必须明确异常处理，不得产生无人观察的异步异常。 异常观察、预期取消、记录责任及异常传播遵循[Unity 日志与异常处理规范](Logging.md)；不因统一日志框架而省略实际任务所有权及结果处理。
9. 默认禁止新增 `async void`。只有 Unity、第三方或外部消息入口明确要求 `void` 签名时才允许保留，并应立即转交给返回 UniTask 的内部方法，同时显式处理异常。
10. 不得重复等待同一个 UniTask 实例；确实需要多次等待时必须说明所有权和复用原因，并使用符合项目当前 UniTask 版本的保留机制。
11. UniTask 不代表后台线程。调用 Unity API 前必须确保处于 Unity 主线程和正确的 PlayerLoop 阶段；物理、渲染帧和帧结束操作必须选择符合职责的执行时机。
12. WebGL 平台禁止使用 `Task.Run`、`UniTask.RunOnThreadPool`、`UniTask.SwitchToThreadPool` 或其他依赖线程池的实现。需要耗时计算时必须采用平台允许的分帧处理、主线程协作或项目已有平台方案。
13. 修改已有 Task、协程或回调实现前，必须说明原异步模型、调用方、取消规则、异常传播、平台差异和迁移收益，并取得用户确认。
14. 通过事件监听启动的异步任务，仍须明确执行所有者、取消时机及异常处理，并遵守本节的等待与任务所有权约定。普通事件分发结束和解除订阅不自动表示异步操作已经结束或取消。事件参数具有特定有效期时，异步监听必须遵守相应数据所有权及有效期契约；具体分发方式遵循[EventSystem 与事件协作规范](../Architecture/EventSystem.md)。 本节约束异步技术实现；业务请求调度、状态一致性、取消与结果有效范围另遵循[异步业务与状态一致性规范](StateConsistency.md)。
