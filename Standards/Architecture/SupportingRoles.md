---
id: architecture.supporting-roles
title: 支持性职责规范
category: Architecture
scope: General
---

# 支持性职责规范

## 规范定位与适用范围

定义 Utility、Provider、Registry、Service、DataStorage 与 Adapter 等支持性职责。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

## 相关规范

### 基础依赖
- [核心职责与单例规范](CoreRoles.md)
- [数据类型与传递契约规范](DataContracts.md)

### 条件协作
- [配置数据管理规范](../Runtime/Configuration.md)
- [资源管理规范](../Runtime/ResourceManagement.md)
- [日志与异常处理规范](../Runtime/Logging.md)

## Utility

`Utility` 表示独立于具体业务领域的通用技术辅助能力，不属于固定业务层级。其职责、依赖方向、默认静态实现及状态约束遵循本节下方的具体规则。

1. `Utility` 表示不依赖具体游戏业务领域的通用技术辅助能力，名称必须体现具体技术用途，例如 `BitMaskUtility`、`NumericRangeUtility`、`PathNormalizationUtility`。
2. Utility 不属于固定业务处理层级，可以被符合职责及依赖规则的各层类型使用。默认优先采用不可挂载、无状态的静态实现；不因需要提供通用能力而设计成全局单例。确有独立实例配置或技术职责时，按实际需要确定实例方式，不引入隐式可变全局状态。
3. Utility 不得包含具体游戏业务规则、完整业务流程组织及权威业务状态管理，不得反向依赖使用它的上层类型、Manager 或具体业务 System。纯业务处理类型及基础处理实现层使用 Utility 时，仍须保持自身纯处理和无外部副作用约束，不得通过 Utility 间接执行上层操作。 不通过 Utility 使纯处理调用方直接或间接输出日志，具体诊断权限遵循[Unity 日志与异常处理规范](../Runtime/Logging.md)。
4. 禁止使用 `CommonUtility`、`GameUtility`、`MissionUtility` 等职责宽泛或以业务领域名称代替具体处理语义的名称。不得以 Helper 或 Utility 代替具有明确职责语义的 Calculator、Formula、Operation、Algorithm 等类型。
5. 已有 C#、Unity 或项目框架能力能够满足处理契约时，优先复用。可以为具有明确技术语义、稳定契约或隔离价值的通用能力建立独立 Utility，但不得为了更换名称或增加层级而建立没有明确职责依据的包装。

## `Provider`

1. `Provider` 表示向调用方提供已有数据、配置、状态快照、只读集合或环境信息的数据访问边界。
2. `Provider` 采用“业务对象或领域 + 所提供的数据 + Provider”命名，例如 `MissionProgressStateProvider`、`MissionConfigProvider`。
3. `Provider` 可以隐藏数据的具体存储位置和读取方式，对外提供稳定统一的读取接口，不再根据读取来源细分为 `Reader`、`Source`、`Lookup` 或 `Catalog` 等命名。
4. `Provider` 接收明确查询条件，并返回对应数据、状态快照、只读集合或明确查询结果。
5. `Provider` 不修改权威业务状态，不执行完整领域流程，不承担保存、事件发布、UI 更新或其他外部副作用。
6. 如果数据需要经过独立的解析、候选优先级或冲突判断才能确定，应由职责明确的 Resolver 完成解析，再由 Provider 对外提供结果；所属领域的规则及最终业务裁决仍遵循 System 的职责边界。简单的确定性读取属于 Provider 的职责，不需要为了形式额外建立解析类型。
7. 数据可能不存在时，必须使用 `TryGet` 或明确结果类型，不得静默创建默认数据、自动寻找替代来源或返回无业务依据的默认值。
8. 读取运行时业务状态时，应从明确的状态持有者获取只读信息或 Snapshot，不维护另一份独立变化的权威状态。需要读取持久化数据时，可以依赖 DataStorage；是否组合使用应按职责及稳定架构边界确定。具有明确数据访问契约的 Provider 即使当前仅转发一次读取或读取单个字段，也允许独立存在；不要求为每次简单读取额外建立无明确访问边界的类型。 在异步处理期间，应按照实际契约区分实时只读访问与稳定快照，并明确其有效期，具体遵循[异步业务与状态一致性规范](../Runtime/StateConsistency.md)。 对外读取应检查实际返回对象、底层集合及元素的可修改性，明确所有权及有效范围，不得通过只读接口泄漏可任意修改的权威状态；具体遵循[类的访问权限与公开接口规范](../Code/AccessControl.md)。 读取及缓存优化必须保持当前状态、只读视图和 Snapshot 的实际语义，遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)中的缓存及低分配数据传递约定。
9. 具有明确配置访问职责的 ConfigProvider，可以通过 ConfigSystem 的合法只读查询能力提供所属领域的有效配置，不强制直接读取原始文件、ScriptableObject 或配置表。Provider 不重复承担配置框架职责，不维护独立的权威配置；该约定不解除其他 Provider 的职责及依赖边界，也不得供纯处理层间接访问全局配置系统。具体遵循[配置数据管理规范](../Runtime/Configuration.md)。
10. 具有明确资源访问职责的 AssetProvider，可以按合法契约使用 AssetSystem 的资源访问能力，提供已有资源及只读资源信息。涉及加载、所有权取得或释放的操作，须按明确的资源执行契约组织；不得隐藏资源副作用、重复承担通用加载机制或使纯处理层间接访问全局资源框架。具体遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。

## `Registry`

1. `Registry` 表示一组已经存在的运行时对象与其唯一标识之间的显式注册关系，并提供注册、注销和确定性查询能力。
2. `Registry` 采用“被注册的业务对象或明确范围 + Registry”命名，例如 `MissionRegistry`、`ActiveMissionRegistry`、`CharacterRegistry`。
3. `Registry` 接收已经由明确所有者创建的对象，不负责创建、加载或销毁对象。
4. 重复注册、ID 冲突和注销不存在对象时必须返回明确结果，不得静默覆盖或假装成功。
5. `Registry` 不得通过场景扫描、反射、全局查找或自动绑定发现对象；所有注册和注销必须由对象所有者显式执行。
6. `Registry` 不作为全局服务定位器，不负责存档、业务规则、UI 更新或跨模块流程协调，也不因需要查询而默认设计成全局单例。 Registry 的查询及注册能力按实际所有权和合法调用范围提供，不默认公开其可变内部注册集合。 静态配置的查询及索引不默认归入运行时对象 Registry，已有明确配置注册契约或框架命名按其实际职责处理，具体遵循[配置数据管理规范](../Runtime/Configuration.md)。
7. 具有独立注册语义、明确对象归属及稳定注册查询契约时，可以建立 Registry，即使当前内部实现仅为简单字典、列表或委托调用。仅属于现有类型内部的局部集合实现且没有独立注册职责或架构边界时，不应为了形式额外创建 Registry。

## `Service`

1. `Service` 主要用于框架层、基础设施层、平台能力和第三方接口封装，表示一项可被上层模块调用的底层技术能力，例如 `CloudSyncService`、`PlatformAchievementService`、`FileStorageService`。
2. 上层业务类型默认不使用 `Service` 后缀，应优先使用项目业务语言直接命名对象、行为和职责，例如 `MissionSystem`、`MissionProgressLogic`、`MissionUnlockRules`、`Inventory`、`Character`。
3. 禁止使用 `MissionService`、`GameService`、`DataService`、`CommonService` 等无法说明具体业务职责或技术能力的宽泛名称。
4. `Service` 不作为完整业务模块入口，不持有上层业务权威状态，也不编排完整业务流程。
5. 依赖方向应保持为上层合法调用方（如业务 System、承担明确提交职责的 Committer）指向底层或框架 Service；`Service` 不得反向依赖具体业务 `System`、`Logic`、Committer 或 UI。Committer 仅在自身提交职责内使用相应技术能力。 涉及外部异步执行时，Service 应按实际技术契约提供可确认的执行、失败及取消信息，不得将通信失败或等待中断擅自解释为权威业务未执行；相关业务结果由合法上层按[异步业务与状态一致性规范](../Runtime/StateConsistency.md)组织。
6. 如果类型已经能够使用明确业务对象、业务动作或更具体职责后缀命名，不得为了体现架构层级额外添加 `Service`。

## `DataStorage`

1. `DataStorage` 表示某类业务对象、业务状态或业务数据的持久化读取和保存边界。
2. 通用命名规范不使用 `Repository` 后缀，统一使用语义更直白的 `DataStorage`。
3. `DataStorage` 采用“业务对象或业务数据 + DataStorage”命名，例如 `MissionDataStorage`、`PlayerProfileDataStorage`、`InventoryDataStorage`、`SaveSlotDataStorage`。
4. `DataStorage` 可以负责业务状态与持久化数据之间的转换，以及读取、保存、删除和存在性查询。
5. 读取、保存和删除操作必须返回明确结果。数据不存在、读取失败、保存失败或版本不兼容时，不得静默创建默认数据或继续使用无效数据。
6. `DataStorage` 不定义领域规则，不决定保存时机，不发布领域事件，不更新 UI，也不管理完整业务流程；相关流程由所属 `System` 负责，或由其明确委托的 Committer 按已确认的提交策略组织。
7. `DataStorage` 可以依赖底层文件、数据库、网络或云端存储能力，但不得将具体存储格式和技术细节泄漏给上层业务。
8. DataStorage 返回明确的读取、保存或删除结果。读取结果如何成为当前有效运行时状态，由所属 System 或其明确委托的 Committer 等提交实现按照既有业务契约处理；DataStorage 不擅自替换或修改现有权威状态实例。持久化格式、SaveData 或既有 DTO 与运行时业务数据之间的转换，应保持明确边界。 异步读写的目标归属、输入有效期、写入顺序或逻辑版本以及过期读取结果的应用边界，遵循[异步业务与状态一致性规范](../Runtime/StateConsistency.md)；不因此改变 System 的保存策略决定权。

## `Adapter`

1. `Adapter` 表示将第三方插件、外部系统、旧模块或不兼容接口适配为项目内部契约的组件。
2. `Adapter` 的名称应体现被适配来源或目标能力，例如 `SteamAchievementAdapter`、`RewiredInputAdapter`、`AddressablesAssetAdapter`、`LegacySaveDataAdapter`。
3. `Adapter` 可以转换接口调用、参数、枚举、回调和错误结果，但不得添加新的领域规则或完整业务流程。
4. `Adapter` 的依赖必须显式提供，不得自动查找、反射创建或使用静默默认实现。
5. 不得仅为套用适配器模式创建没有实际接口差异的 Adapter。
