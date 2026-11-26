---
id: architecture.architecture-principles
title: 架构设计与职责拆分规范
category: Architecture
scope: General
---

# 架构设计与职责拆分规范

## 规范定位与适用范围

定义架构先行、职责拆分、整体数据流及跨框架协作原则。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

## 相关规范

### 基础依赖
- [核心职责与单例规范](CoreRoles.md)
- [业务处理职责与基础处理实现规范](ProcessingRoles.md)

### 条件协作
- [状态提交与 Committer 规范](StateCommit.md)
- [EventSystem 与事件协作规范](EventSystem.md)
- [配置数据管理规范](../Runtime/Configuration.md)
- [资源管理规范](../Runtime/ResourceManagement.md)
- [对象池与实例复用规范](../Runtime/ObjectPooling.md)

## 架构先行与职责拆分原则

1. **架构先行。** 项目优先确定稳定的职责边界、类型语义、依赖方向及代码归属，再根据实际业务需求实现对应功能。允许在业务逻辑尚未复杂时建立明确的架构层级，不要求等待代码规模增长后再进行职责拆分。 设计模式、抽象、继承与组合、算法和其他技术选型同样遵循架构先行原则，具体按照[技术实现方案设计与评估规范](../Design/TechnicalDesign.md)确定，不提前实现未经确认的功能。 架构先行的设计倾向与已经确定的硬性职责及依赖边界应分别判断，规则性质、默认选择及实际冲突遵循[规则优先级与冲突处理规范](../Governance/RulePriority.md)。

2. **鼓励细粒度职责拆分。** 具有明确业务语义、技术职责或稳定架构定位的能力，优先考虑通过独立类型表达。不得仅因代码行数较少、当前逻辑简单、只有一个调用方或尚未形成复用需求而否定其独立存在的价值。

3. **组织职责与具体实现可以分离。** 上层职责类型可以通过调用下层职责类型完成全部具体处理。上层负责统一入口、流程组织、能力协调或结果汇总，下层负责具体实现；实际分工必须符合各职责类型的专门定义。

4. **允许具有明确架构语义的委托层。** 一个类型即使当前仅负责参数传递、能力委托或结果返回，只要具有明确且稳定的架构职责、调用契约及依赖边界，就可以独立存在。不得仅因其当前实现简单、只有一层委托或没有直接执行具体计算而擅自合并、删除或绕过该类型。

5. **职责语义优先于代码数量。** 不以减少文件数量、类型数量、接口数量或调用层级作为架构设计的主要优化目标。优先考虑职责可定位性、架构一致性、依赖可预测性、修改影响范围及长期可维护性。职责清晰的局部实现细节仍可使用现有类型中的方法表达。 具体任务实施遵循[AI 修改与交付规范](../Governance/AgentWorkflow.md)中的最小化非必要影响原则，不机械追求最少修改文件、代码量或职责类型数量。

6. **提前建立架构不等于提前实现功能。** 可以提前确定职责归属、处理层级及稳定的输入输出契约，但不得为了充实某个类型的实现而擅自增加尚未确认的业务规则、队列、缓存、重试、限制、状态机或其他运行机制。不得虚构职责以配齐所有类型或层级。

7. **禁止缺乏职责依据的拆分。** 新增类型必须能够说明其职责语义、架构定位、依赖方向及所属范围。不得创建职责重复、归属不明或仅为增加层级而存在的类型。职责后缀用于表达实际职责或稳定架构边界，不是必须配齐的组件清单；允许按已确认架构预先建立合理边界，但不解除各类型的专门职责约束。

8. **保持既有架构稳定。** 已有项目形成稳定的职责分层和调用结构时，应优先沿用。不得以简化代码、减少调用层级或避免过度拆分为由，擅自合并、删除、绕过或重构已有职责类型。本节不授权主动批量重构、改变业务契约或引入新的公共框架；仍须遵守通用协作、公共基础模块报备、UI 分层确认、校验与附加限制等既有规则。 访问权限用于保护职责边界，不得以收紧可见性为由破坏已经确定的细粒度职责及合法协作关系。 不得仅以降低潜在 GC、减少方法调用或类型数量为由擅自合并、删除或绕过稳定职责边界；性能优化依据实际执行成本，具体遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。

9. **配置基础架构先行。** 从零设计项目时，默认提前建立 ConfigManager 与 ConfigSystem 的稳定职责边界。ConfigManager 负责项目级配置管理及装配，ConfigSystem 负责统一配置框架机制及有效配置访问。不得仅因当前配置数量较少而否定其独立架构价值。具体实现及内部拆分遵循[配置数据管理规范](../Runtime/Configuration.md)，不因此获得新增未经确认的运行机制或重构已有配置框架的授权。

10. **资源与对象池基础架构先行。** 从零设计项目时，默认提前建立 PoolManager、AssetManager 与 AssetSystem 的稳定职责边界；PoolManager 负责项目级对象复用管理，AssetManager 负责资源来源管理及装配，AssetSystem 负责统一资源加载、访问及释放机制。PoolSystem、UpdateManager 等按实际独立职责需求引入，不强制配套。具体遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)，不因此自动池化所有对象、实现预热缓存等运行机制或重构已有资源框架。

11. **目录组织与架构职责保持一致。** 项目默认按照公共框架、游戏业务及实际职责所有者组织代码，业务领域内部按照已确定的职责层级进一步划分。允许提前建立具有稳定架构语义的目录边界，不因目录内文件数量较少而否定其独立价值。具体目录组织遵循[代码目录组织与文件归属规范](../Code/DirectoryStructure.md)，不改变既有职责、单向依赖或变更授权。

12. **日志基础架构先行。** 从零设计项目时，默认提前建立 LogSystem 的稳定框架职责，负责统一日志记录、格式组织及底层输出，默认使用 Unity Debug API。LogManager 按实际独立管理需求引入，不强制配套。日志机制不接管业务异常处理及失败决策，纯处理层默认不主动输出日志。具体遵循[Unity 日志与异常处理规范](../Runtime/Logging.md)，不因此自动迁移已有日志框架或增加过滤、历史、持久化及上报机制。

## 默认职责数据流

新增业务流程类型时，优先依据本规范建立稳定的职责边界和代码归属，再实现当前已经确认的业务需求。以下职责关系是命名、架构组织及依赖方向参考。用户明确指定的合法方案、项目有效专用约定及已有稳定技术契约，优先于本节的默认组织方案和参考示例。上述优先关系不自动解除硬性架构约束、状态所有权、依赖方向及变更授权。实际冲突按照[规则优先级与冲突处理规范](../Governance/RulePriority.md)处理，不得为了套用示例强制重构已有实现。 物理文件归属及目录分层遵循[代码目录组织与文件归属规范](../Code/DirectoryStructure.md)，目录结构不将职责关系变为固定必经调用链。

### 职责组织与引入原则

1. **默认职责方向不是固定必经调用链。** 可以预先建立稳定的 System、Processor、Logic、Resolver、具体职责能力、基础处理实现及 Committer 等架构边界；不要求每个业务、查询或操作经过所有层级。是否经过某一层，取决于该层的实际职责和已确立的调用契约，不得以缩短调用链为由擅自绕过已有稳定入口。
2. **允许明确的架构委托。** Logic、Resolver、Calculator 等上层处理类型即使目前只有一个处理步骤，仍可作为稳定的流程、解析或计算入口，并将全部具体处理委托给下层。Committer 即使目前只组织一次状态写入，也可以作为稳定的提交入口并委托具体提交实现。Processor 的引入仍须符合工作单元处理及处理生命周期的职责语义，不因业务逻辑简单而否定已成立的边界，也不得为填充该层凭空增加队列、重试等机制。
3. **职责方向不改变状态和副作用边界。** System 负责组织业务用例及权威状态提交；Processor 通过明确的 Committer 接口或已有提交契约委托处理结果的提交；Committer 负责按契约组织实际状态提交及相关步骤。Logic、Resolver、具体职责能力及基础处理实现层保持各自的纯处理边界，不调用 Committer。Manager 可以独立承担自身管理职责并调用 System，但 System 不得直接或间接调用 Manager。查询、注册、持久化、装配及表现职责按各自专门约定处理，不是所有业务请求必须经过的线性层级。 事件通知是与纯业务处理及状态提交相协作的独立职责路径，不是第三个固定执行阶段。只有具有明确发布权限的职责承担者，才能在对应事实成立后按契约发布事件。 存在异步、并发、重入或多方状态提交需求时，各路径的衔接仍须满足实际业务一致性契约，具体遵循[异步业务与状态一致性规范](../Runtime/StateConsistency.md)。 各职责类型及成员的默认访问权限、公开能力和程序集边界遵循[类的访问权限与公开接口规范](../Code/AccessControl.md)；技术可访问性不解除本节的调用与依赖约束。 配置访问是与业务处理、状态提交及事件通知相协作的独立辅助路径，业务 System 默认通过 ConfigSystem 或合法 ConfigProvider 取得所需配置，再向纯处理层显式提供；具体遵循[配置数据管理规范](../Runtime/Configuration.md)，不强制每次业务调用均经过配置框架。 资源访问及对象复用同样属于独立辅助职责路径；公共资源默认通过 AssetSystem 或合法访问边界取得，池化及资源清理由实际所有者组织，具体遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)，不得由此绕过 Manager 与 System 的单向依赖。 日志记录属于独立诊断职责路径，合法非纯处理类型按实际责任使用 LogSystem 或已有日志入口；纯处理层只提供处理结果及必要诊断数据。日志输出不接管业务裁决、异常处理及状态提交，具体遵循[Unity 日志与异常处理规范](../Runtime/Logging.md)。

职责协作参考：

```text
Manager → System（按管理职责调用，不要求配套）
Controller / 合法调用方 → System

System（组织业务用例）
    可以直接组织以下纯业务处理及状态提交两条路径；
    具有工作单元处理职责时，可以由 Processor 组织两条路径的衔接：

    ├─ 纯业务处理路径：
    │   → Logic
    │       → Resolver（明确的综合解析问题）
    │           → 具体职责能力（Rule / Rules / RuleSet / Calculator / Evaluator /
    │                             Matcher / Selector / Mapper / Normalizer）
    │               → 基础处理实现（Operation / Formula / Algorithm）
    │   ← 返回业务处理结果
    └─ 状态提交路径（需要提交且满足既有提交契约时）：
        → Committer（Processor 通过显式提交接口调用）
            ├─ 业务对象 / RuntimeData（受控状态更新）
            ├─ DataStorage / Service（按契约执行持久化或服务端提交等）
            └─ 按契约使用下方事件通知路径（事实成立时，按需）
        ← 返回实际提交结果

    由 System 或 Processor 按各自契约组织结果交付。

事件通知路径（独立协作路径，不是固定的第三个执行阶段）：
    System / Committer / 其他具有明确发布权限的职责承担者
        → EventSystem（沿用项目现有事件机制；对应事实成立时按需发布）
            ├─ Manager：按自身管理职责响应
            ├─ Controller / 合法表现订阅者：按职责响应
            └─ 其他业务 System：组织独立后续业务

    上述箭头表示事实通知流向，不表示发布方反向依赖具体订阅者；
    订阅者自主建立监听关系，不承担发布方未显式组织的必要业务步骤。

配置访问路径（独立辅助路径，不是固定业务执行阶段）：
    ConfigManager → ConfigSystem：配置来源管理及显式装配
    业务 System → ConfigSystem：按契约获取有效配置
    或 业务 System → 所属领域 ConfigProvider → ConfigSystem
    业务 System 或其他合法处理组织者 → 纯处理层：显式提供配置及业务输入

    ConfigSystem 不反向依赖 ConfigManager；
    纯处理层不得直接或间接访问全局配置系统。
    用户指定的配置载体及存放位置保持不变，局部配置按实际归属维护。

资源访问与对象复用路径（独立辅助路径，不是固定业务执行阶段）：
    AssetManager → AssetSystem：资源来源管理、装配及运行观察
    合法业务 System / 其他资源使用者 → AssetSystem：按契约取得及释放公共资源
    ConfigSystem → AssetSystem：按契约加载需要主动取得的配置资产
    PoolManager → AssetSystem：取得池所需资源
    PoolManager / 合法 System → PoolSystem：存在共享复用框架需求时使用
    Factory / 合法创建或复用实现：按契约创建、租用及初始化对象

    System 不直接或间接依赖 AssetManager、PoolManager；
    纯处理层只接收显式数据及合法工作缓冲区，不自行获取全局资源或对象池能力。

日志记录路径（独立诊断路径，不是固定业务执行阶段）：
    合法非纯处理职责类型 → LogSystem 或已有合法日志入口 → 底层日志输出
    LogManager（按需）→ LogSystem：管理调用、配置装配及运行观察
    纯处理层返回处理结果及必要诊断数据，由合法上层按实际责任判断和记录。
    日志框架不接管业务异常处理，不依赖具体业务 System 或 Manager。

Provider / Registry：按实际职责提供数据读取及注册查询能力
Service / DataStorage / Adapter：按既有规则提供底层技术或持久化能力
Utility：通用技术辅助能力，可按职责使用，不是固定必经层级
```

上图仅表达一种职责组织方向。System 可以直接调用适用的 Logic、Resolver 或 Calculator 等下层能力，并直接组织提交或调用所属 Committer，无需为此强制增加 Processor。Processor 通过显式提交接口组织状态提交。Logic、Resolver 和具体职责能力可以直接使用符合依赖规范的基础处理实现，但不得进入状态提交路径。图中的提交相关步骤并非固定执行顺序。各层仍须遵守显式输入输出、状态归属及禁止反向依赖等硬性约束。 事件通知路径可由 System 或 Committer 等合法发布方在对应事实成立时使用，不要求等待整个 CommitResult 或前台展示流程返回，也不要求所有业务发布事件。事件分发完成、监听者执行完成和业务结果交付分别遵守各自契约。局部事件、订阅生命周期及框架技术实现遵循[EventSystem 与事件协作规范](EventSystem.md)。 配置访问不改变原有职责路径及状态所有权；已有配置框架、特殊直接装配契约和从零开发的 ConfigManager + ConfigSystem 默认架构，遵循[配置数据管理规范](../Runtime/Configuration.md)。

### 示例一：具有请求处理生命周期的任务进度处理

以下保留完整的请求、纯业务处理、状态提交及结果交付示例，并明确基础处理实现层与 Committer 的职责边界。入队、顺序处理、持久化、事件发布等仅在对应业务契约要求时使用：

```text
外部调用方
    ↓ 请求 MissionSystem 处理进度变化

MissionSystem
    ↓ 创建 MissionProgressRequest
    ↓ 将请求提交给 MissionProgressRequestProcessor
    ↓ 等待请求最终结果

MissionProgressRequestProcessor
    ↓ 请求入队并按顺序取出
    ↓ 通过 MissionProgressStateProvider 读取状态持有者提供的只读信息或 MissionSnapshot
    ↓ 组装 MissionProgressInputData

MissionProgressLogic
    ↓ 编排一次纯领域业务流程

Resolver / Rules / Calculator / Evaluator
    ↓ 按明确职责使用具体处理能力，必要时调用基础处理实现

Operation / Formula / Algorithm（按实际处理职责组合）
    ↓ 执行具体操作、公式或算法并返回结果

Resolver / Rules / Calculator / Evaluator
    ↓ 返回解析、规则判断和计算结果

MissionProgressLogic
    ↓ 返回 MissionProgressResult

MissionProgressRequestProcessor
    ↓ 按业务契约通过 IMissionProgressCommitter 委托提交

MissionProgressCommitter（实现 IMissionProgressCommitter）
    ↓ 按已确认的提交契约组织状态写入及所需持久化、事实事件发布等步骤
    ↓ 返回实际 MissionProgressCommitResult

MissionProgressRequestProcessor
    ↓ 根据业务处理及实际提交结果生成 MissionProgressRequestResult
    ↓ 按契约记录请求成功、失败、取消、部分完成或结果尚未确认等必要状态
    ↓ 继续处理下一条请求

MissionSystem
    ↓ 向原始调用方返回 MissionProgressRequestResult
```

示例中的状态持有者可以是已有 Mission 业务对象，也可以是独立的 MissionRuntimeData。Provider 提供其只读信息或 Snapshot，Logic 根据输入计算结果，MissionProgressCommitter 按契约将结果应用到明确的状态持有者。状态更新、服务端提交与持久化的先后顺序，以及失败后的处理方式，遵循该业务已经确认的契约，不由通用示例统一规定。 存在竞争写入或异步等待时，应按实际处理时机及提交契约确认状态依据有效，示例的顺序处理并不自动保护其他不遵守该处理契约的写入路径。

示例中的业务事件表示对应业务事实已经按照该业务提交契约成立，不代表整个前台流程已经结束。业务事件的发布条件和时机由所属业务契约确定，不因前台展示尚未结束而统一延后。 同一次事实由明确的发布负责人发布，不得由 MissionSystem 和 MissionProgressCommitter 无意重复发布。

MissionProgressCommitResult 和 MissionProgressRequestResult 分别表示各自约定范围内的业务处理结果，应返回给上层用于继续组织后续步骤，不因前台流程完成通知的交付规则而延迟返回。整个前台流程向外部调用方交付的最终结果及完成通知，遵循项目已经确认的 UI 打开、业务请求与前台展示契约。

本约定不要求每个业务都新增事件，也不改变项目既有事件分发和订阅方式。发布业务事件本身，不自动表示所有监听者启动的后续异步操作均已完成；需要等待的后续步骤必须由对应流程显式组织。 事件分发中发生的监听者异常不自动撤销已经成立的业务事实，具体分发语义及异常归属遵循[EventSystem 与事件协作规范](EventSystem.md)。

### 示例二：简单任务进度计算与稳定委托层

即使当前任务进度计算仅包含一个数值公式，也可以建立明确的纯业务流程入口和计算能力入口：

```text
外部调用方
    → MissionSystem：受理业务请求，读取所属任务的有效状态
        → MissionProgressLogic：组织纯业务处理流程
            → MissionProgressCalculator：组织任务进度计算
                → MissionProgressIncrementFormula：执行明确的增量公式
                ← 返回计算结果
            ← 返回计算结果
        ← 返回 MissionProgressResult
    → MissionSystem：按业务契约组织状态提交
        → MissionProgressCommitter：应用已确定的结果
        ← 返回 MissionProgressCommitResult
    ← MissionSystem：向调用方返回明确的业务操作结果
```

例如，MissionProgressIncrementFormula 当前可以仅实现 `return progress + count;`。MissionProgressLogic 与 MissionProgressCalculator 即使都只委托下层处理，仍具有稳定的架构职责。MissionProgressCommitter 即使仅组织一个字段的受控写入，也可以独立存在；System 按已有契约直接组织提交同样允许。不需要为了充实这些类型而擅自增加倍率、上限、队列或其他规则。若已存在独立工作单元处理及生命周期职责，可以按 Processor 规范建立处理边界；若仅为简单状态查询，也不要求经过上述完整调用链。

### 示例三：战斗伤害处理与基础处理能力组合

```text
CombatSystem：组织战斗用例及状态提交
    → AttackLogic：编排纯攻击处理流程
        ├─ AttackRules：攻击条件判断
        │   └─ AttackRangeOperation：执行明确的范围处理
        ├─ CombatTargetResolver：综合目标解析
        │   └─ CombatTargetSelector：按明确条件选择候选项
        │       └─ TargetSelectionAlgorithm：执行具体选择算法
        └─ DamageCalculator：组织伤害计算
            ├─ BaseDamageFormula：基础伤害公式
            ├─ DefenseReductionFormula：防御减伤公式
            ├─ CriticalDamageFormula：暴击伤害公式
            └─ DamageClampOperation：按已确认条件处理伤害范围
    ← 返回纯攻击处理结果
    → AttackResultCommitter：按业务契约组织需要实际生效的状态更新
    ← 返回实际提交结果
```

在此架构中，修改攻击流程时优先检查 AttackLogic；修改攻击条件时优先检查 AttackRules；修改目标选择方式时优先检查 CombatTargetResolver、CombatTargetSelector 或具体算法；修改伤害计算组织方式时优先检查 DamageCalculator；修改防御减伤公式时优先检查 DefenseReductionFormula。Agent 仍须检查相关调用契约、依赖及受影响范围，不得仅凭类型名称假定具体实现或影响范围。

### 示例四：背包转移与多个状态持有者的提交

```text
InventorySystem：组织物品转移用例
│
├─ InventoryTransferLogic
│   ├─ InventoryTransferRules
│   └─ InventoryStackCalculator
│       ├─ InventoryStackSplitOperation
│       └─ InventoryStackMergeOperation
│
│   ← 返回 InventoryTransferResult
│
└─ InventoryTransferCommitter
    ├─ 玩家 Inventory（受控状态更新）
    ├─ 仓库 Inventory（受控状态更新）
    ├─ InventoryDataStorage（按业务契约）
    └─ EventSystem（按业务契约）

    ← 返回 InventoryTransferCommitResult
```

InventorySystem 负责根据业务处理结果组织提交，并按业务契约交付整个操作的明确结果。InventoryTransferCommitter 可以组织多个状态持有者的提交，但不得自行改变业务处理结果，也不得违反已确认的整体成功、部分成功或其他一致性契约。上述状态写入、保存及事件发布不代表固定执行顺序。

所有职责数据流示例均用于说明架构边界和代码归属，不表示项目必须具有相应战斗规则、公式、限制、数据类型或处理机制。

## 配置、资源与对象池协作

1. **区分配置数据、资源资产及运行时对象实例。** ConfigSystem 组织并提供有效配置，AssetSystem 组织资源加载与生命周期，PoolManager 或合法池实现组织已创建实例的复用，Factory 负责具体对象创建及初始化，各业务 System 组织相应业务的使用。它们不属于所有业务固定必经的线性调用链。
2. ConfigManager 负责配置来源管理和装配，AssetManager 负责资源来源管理和装配；允许合法共用已有资源来源与引用，不得分别维护同一配置或资源状态的独立权威来源。配置载体及存放位置仍以用户明确指定为先，不因统一资源访问而擅自迁移配置资产或 Inspector 引用。
3. ConfigSystem 需要主动加载配置资产或运行时配置资源时，可以按照既有契约使用 AssetSystem 的合法资源能力，或沿用现有配置框架明确的加载契约。ConfigSystem 负责有效配置及其使用语义，AssetSystem 负责对应资源取得及释放机制；须明确数据有效期、加载所有权和资源依赖。启动所需依赖应由明确装配入口组织，避免 ConfigSystem 与 AssetSystem 相互等待或形成初始化循环，不假定固定启动顺序。 使用 Resources 存放本地 SO、二进制或 JSON 配置时同样遵守上述职责关系；固定绑定资产无需机械重新加载，原始 Excel 及生成产物位置遵循目录和配置规范。
4. PoolManager 可以使用 AssetSystem 取得对象池所需 Prefab 或其他资源；PoolSystem 如已建立，也可按框架职责使用符合依赖规范的资源机制。池应维持池内实例所依赖的资源有效性，并在实际池生命周期结束时按资源所有权契约处理释放。资源加载不等同于已创建实例，池归还也不等同于资源释放。
5. 业务 System 可以使用 ConfigSystem、AssetSystem 及已确认的合法框架能力，不能直接或间接调用 ConfigManager、AssetManager、PoolManager。Factory、执行实现及 Provider 不得成为绕过单向依赖的中转层；需要上层管理操作时，由明确所有者主动组织，具有业务结果依赖的步骤仍通过合法执行契约处理，不以普通事件隐式替代。
6. 纯业务处理及基础处理实现层只使用显式提供的配置、状态、计算输入及符合其职责的数据或工作缓冲区，不因配置、资源与对象池框架存在而自行访问全局入口、加载资源、租用 Unity 对象或执行业务副作用。

职责示例：

```text
ConfigManager → ConfigSystem：配置来源装配
AssetManager → AssetSystem：资源来源装配
ConfigSystem → AssetSystem：按契约取得需要主动加载的配置资产
PoolManager → AssetSystem：按契约取得池所需资源
PoolManager → PoolSystem：存在独立公共复用框架时使用（非必需）

合法敌人生成流程（按实际职责组织，不是固定调用链）：
    取得 EnemyConfig：配置访问
    取得 Enemy Prefab：资源访问
    创建或租用 Enemy：对象创建或复用
    按本次身份初始化 Enemy：对象装配
    组织敌人相关业务：所属业务 System
```

上述资源和对象获取步骤应由具有对应职责及合法依赖的调用方组织，不表示业务 System 可以直接调用 PoolManager，也不表示资源加载及池化是所有敌人生成的前置条件。
