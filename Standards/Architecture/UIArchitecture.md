---
id: architecture.ui-architecture
title: UI 表现层架构规范
category: Architecture
scope: General
---

# UI 表现层架构规范

## 规范定位与适用范围

定义 UI Controller、View、Presenter、ViewModel 等表现层职责及演进。表现层职责语义可跨项目、跨引擎复用；本规范中 Controller 的场景级 Unity 组件单例、Prefab 及 Unity 生命周期实现仅适用于 Unity。其他引擎按其场景、页面及对象生命周期映射，不强制采用 Unity 单例形式。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

## 展示数据与业务状态边界

1. `DisplayData` 表示为界面展示准备的数据形态，可以由 Presenter、Controller 或符合职责的数据映射能力根据业务查询结果、只读状态或 Snapshot 组织。其字段和结构服务于页面显示，不因包含业务数值而成为权威业务状态。
2. View 按明确传入的 DisplayData 更新显示，不自行获取全局权威状态或修改业务状态。DisplayData 的创建、更新及有效期应与对应页面和展示流程一致，不要求每个 View 都建立独立 DisplayData 类型。
3. DisplayData 与 Snapshot 不等同：Snapshot 表达获取时点的只读业务状态快照；DisplayData 表达面向具体表现需求的数据结构，可以包含格式化、组合或派生后的展示字段。DisplayData 不得成为另一份独立裁决同一业务事实的权威状态，相关状态所有权约束遵循[状态提交与 Committer 规范](StateCommit.md)。

## 相关规范

### 基础依赖
- [核心职责与单例规范](CoreRoles.md)

### 条件协作
- [UI 与交互变更授权规范](../Governance/UIChangeAuthorization.md)
- [Unity UI 对象命名与层级规范](../Unity/UIObjects.md)：仅 Unity 项目涉及 UI GameObject 命名及层级时适用。
- [Unity Prefab 职责与封装规范](../Unity/Prefab.md)：仅 Unity 项目涉及 Prefab 封装及引用时适用。

## UI 表现层架构演进

1. **UI 页面可以按明确职责与稳定架构边界分层。** 当数据绑定、显示更新、交互状态、异步流程、生命周期协调等具有独立职责，或者存在稳定分层、复用、替换或独立验证需求时，可以提出 UI 分层方案，不要求等待页面复杂后才建立边界。仍须按AI 修改与交付规范中的用户确认流程取得用户确认。

2. **UI 分层强调职责划分，不强制每种职责独立成类。** 页面入口、展示交互协调和页面显示应具有清晰边界。Controller 可以同时承担 Presenter 职责，保留原有 Controller 命名，不要求额外创建独立 Presenter 类。确有独立拆分需要时，也可以由 Controller 装配独立 Presenter，具体按照已确认方案执行。

3. **在 Unity 项目中，Controller 默认遵循场景级单例约定。** 无论是否承担 Presenter 职责，Controller 都保留页面入口、装配和生命周期协调职责。可以通过实现接口或兼容现有继承结构的方式承担、复用 Presenter 职责，但必须保持项目已有的场景级单例机制和作用域。采用 MVP、MVC、MVVM 等 UI 架构，不要求将 Controller 改名为 Presenter，也不改变其场景级单例身份。其他引擎中的 Controller 仍承担相应表现层协调职责，但实例数量及生命周期按目标引擎和项目既有契约确定，不自动要求单例。

4. **View 负责页面显示和交互事件上报。** 根据明确传入的展示数据更新文字、图片、数值等界面内容，并向承担 Presenter 职责的实现上报用户交互事件；该实现可以是 Controller，也可以是独立 Presenter。View 不自行执行完整业务用例或决定领域业务结论。

5. **Presenter 表示展示交互协调职责。** 负责响应 View 上报的交互事件，调用业务 System，处理返回结果，并转换为 View 的显示更新。无论该职责由 Controller 承担还是由独立 Presenter 实现，都不得重新实现领域规则、绕过 System 修改权威业务状态，或重复保存同一份权威业务状态。

6. **分层方案必须明确职责承接、依赖关系和生命周期。** 在 Unity 项目中，Controller 兼任 Presenter 时，由同一个场景级单例实例承担两种职责；存在独立 Presenter 时，由 Controller 按已确认方案完成装配和生命周期协调。其他引擎按对应的场景或页面生命周期组织。 同一业务操作应具有明确的处理负责人，避免 Controller 与 Presenter 重复响应交互、重复提交请求或重复处理结果。Unity Controller 的场景级单例要求不自动延伸到独立 Presenter、View、ViewModel 或 Model，其他独立类型按实际职责和所有权确定实例方式。

Unity 实现参考：

方式一：Controller 同时承担 Presenter 职责。

```text
SettingsPanelController   场景级单例，负责页面入口、生命周期及展示交互协调
SettingsPanelView         负责页面显示和交互事件上报
```

如采用接口表达 Presenter 契约，可以由 SettingsPanelController 实现 ISettingsPanelPresenter；接口名称不改变 Controller 的类型名称。

方式二：确有独立拆分需要时，由 Controller 装配独立 Presenter。

```text
SettingsPanelController   场景级单例，负责页面入口、装配和生命周期协调
SettingsPanelView         负责页面显示和交互事件上报
SettingsPanelPresenter    负责展示交互协调、调用 System 和更新 View
```
