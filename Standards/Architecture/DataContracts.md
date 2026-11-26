---
id: architecture.data-contracts
title: 数据类型与传递契约规范
category: Architecture
scope: General
---

# 数据类型与传递契约规范

## 规范定位与适用范围

定义 Config、RuntimeData、Snapshot、Request、Result、InputData 等数据语义及传递边界。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

## 相关规范

### 基础依赖
- [核心职责与单例规范](CoreRoles.md)

### 条件协作
- [状态提交与 Committer 规范](StateCommit.md)
- [EventSystem 与事件协作规范](EventSystem.md)
- [配置数据管理规范](../Runtime/Configuration.md)
- [异步业务与状态一致性规范](../Runtime/StateConsistency.md)

## 数据语义主定义与职责边界

本规范负责数据类型的通用命名、传递、只读性、所有权及边界约定。数据类型的具体业务语义由下表对应的专项规范主定义；本表用于定位，不重复定义各专项规则。

| 数据语义或类型 | 详细主定义 | 本规范保留的通用职责 |
|---|---|---|
| `Config` | Unity 项目：[配置数据管理规范](../Runtime/Configuration.md)；其他引擎：项目实际配置契约 | 配置类型的通用命名及数据传递约定 |
| `RuntimeData` | [状态提交与 Committer 规范](StateCommit.md) | 数据类型命名、公开传递及不可随意泄漏可写状态的约定 |
| `Snapshot` | 本规范 | 快照的只读数据契约；异步期间的有效时点及使用范围由[异步业务与状态一致性规范](../Runtime/StateConsistency.md)补充 |
| `InputData` | [业务处理职责与基础处理实现规范](ProcessingRoles.md) | 输入数据类型的通用命名、只读性及传递边界 |
| `DisplayData` | [UI 表现层架构规范](UIArchitecture.md) | 展示数据的通用命名、表现用途及传递边界；其不得成为独立权威业务状态的约束由[状态提交与 Committer 规范](StateCommit.md)定义 |
| `Request`、`Command`、`Action`、`Event` | [EventSystem 与事件协作规范](EventSystem.md) | 作为数据类型时的命名、只读性及传递约定；消息语义及事件发布边界由 EventSystem 主定义 |
| `Result` | [业务处理职责与基础处理实现规范](ProcessingRoles.md) | 结果数据的通用命名及传递约定；纯处理结果、提交结果和最终请求结果的阶段区别由[状态提交与 Committer 规范](StateCommit.md)补充 |
| `CommitResult`、`RequestResult` | [状态提交与 Committer 规范](StateCommit.md) | 结果类型的通用命名、只读性及传递边界 |
| `SaveData` | [状态提交与 Committer 规范](StateCommit.md)及项目实际持久化契约 | 持久化数据类型的通用命名及传递边界 |

同一类型可以在多个专项场景中被引用，但其详细语义、权威来源及维护责任应有明确主定义。不得因某份文档列出类型名称，就将该类型的所有专项语义都转移到本规范。

## 数据类型与参数包命名

1. 数据类型按照实际用途命名，必须体现所属业务。不得仅因类型保存数据，就统一添加 Model、State 或含义宽泛的 Data 后缀。

| 用途 | 命名形式 | 示例 |
|---|---|---|
| 独立运行时业务数据 | XxxRuntimeData | WalletRuntimeData |
| 只读状态快照 | XxxSnapshot | WalletSnapshot |
| 设计配置 | XxxConfig | CurrencyConfig |
| 独立持久化数据结构 | XxxSaveData | WalletSaveData |
| 界面展示数据 | XxxDisplayData | WalletDisplayData |
| 业务计算输入 | XxxInputData | CurrencySpendInputData |
| 业务请求 | XxxRequest | CurrencySpendRequest |
| 业务处理结果 | XxxResult | CurrencySpendResult |
| 状态提交结果 | XxxCommitResult | MissionProgressCommitResult |
| 最终请求处理结果 | XxxRequestResult | MissionProgressRequestResult |
| 独立事件数据 | XxxEvent | MissionCompletedEvent |

2. 业务对象本体继续按照对象名称命名，例如 Wallet、Inventory、Character。只有从对象中独立拆出的运行时数据结构，才使用 XxxRuntimeData，不要求为每个业务对象额外创建一份数据类。

3. RuntimeData 可以按照明确的提交契约受控更新；Snapshot 表达获取时的只读状态快照，不得通过可变成员或集合间接暴露原始权威状态的修改能力。 异步处理中使用的数据应明确为实时只读访问或对应时点的有效快照，不得以只读引用包装替代所需的稳定数据语义；具体遵循[异步业务与状态一致性规范](../Runtime/StateConsistency.md)。 Snapshot 及其他公开数据应具有符合实际契约的可访问范围和数据所有权，不能以可变原始对象的只读引用作为稳定快照。

4. 用于集中传递相关参数的纯数据类型，可以使用 Data 后缀；需要表达输入语义时使用 InputData，例如 MissionProgressInputData、InventoryTransferInputData。参数包只保存输入，不执行领域流程、不获取外部依赖，也不作为持续变化的运行时状态容器。

5. 参数包默认在创建后保持只读。处理结果通过独立 Result 返回，不得写回输入参数包。不得将参数包的只读约定机械套用于需要受控更新的 RuntimeData。

6. SaveData 表示独立持久化数据结构，不要求把项目已有 DTO、数据库映射类型或外部协议类型统一改名。既有名称、序列化和生成契约优先，调整仍须遵守AI 修改与交付规范中的修改授权及确认要求。

7. 普通业务数据不得仅因保存状态而使用 XxxState；State 继续遵循状态机及状态对象的既有命名约定。Model 仅在职责明确、已经确认的 UI 表现层架构或项目既有稳定契约中使用。

8. 通用命名规范不使用含义宽泛的 Context 表示普通参数集合；项目既有框架已经定义特定 Context 契约时，遵循其现有约定。

9. **允许为稳定的数据传递契约建立独立类型。** Request、Result、CommitResult、RequestResult、InputData、Snapshot 等具有明确业务语义、所有权及输入输出边界时，可以在当前字段较少、只有一个调用方时独立定义，不要求等到参数或返回信息增多。简单参数和明确返回值仍可按实际职责及既有契约直接表达；已对 Logic、Processor、Resolver、Committer 等规定具体输入输出边界的，继续遵守其专门规则。不得为了配齐数据类型而创造不存在的请求生命周期、业务状态、结果层级或失败条件。 具有独立数据契约的事件类型默认采用 XxxEvent，已有 EventArg、枚举及其他稳定事件数据契约继续沿用，事件数据的所有权、只读约束及有效期遵循[EventSystem 与事件协作规范](EventSystem.md)。 公开请求、结果、快照或事件类型时，应检查实际公开签名及数据的有效可见性，不因此机械公开所属内部实现。

10. Config 表示具有明确配置语义的数据结构，不限定普通 C# 类型、Serializable 类型、ScriptableObject、配置表生成类型或其他具体实现形式。不得仅因类型使用 ScriptableObject 就命名为 Config；已有配置生成类型及第三方契约继续沿用。配置载体、数据访问及所有权遵循[配置数据管理规范](../Runtime/Configuration.md)。
