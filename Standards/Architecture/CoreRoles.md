---
id: architecture.core-roles
title: 核心职责与单例规范
category: Architecture
scope: General
---

# 核心职责与单例规范

## 规范定位与适用范围

定义 Manager、System、Controller、Module、Initializer、Factory、State 等核心职责及协作边界。

本规范中的 Manager、System、Controller 等职责语义可跨项目复用；其中 `MonoBehaviour`、Inspector、Unity 场景级单例及 Unity 生命周期等具体实现仅适用于 Unity 项目。`Module` 的 Unity 挂载组件定义及其 GameObject、Prefab 引用约定同样仅适用于 Unity。非 Unity 项目应按目标引擎的组件、场景及对象生命周期和已有项目契约映射职责，不直接照搬 Unity 实现形式。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

## 相关规范

### 条件协作
- [架构设计与职责拆分规范](ArchitecturePrinciples.md)：进行整体职责拆分、跨框架协作或架构数据流设计时适用。
- [业务处理职责与基础处理实现规范](ProcessingRoles.md)
- [状态提交与 Committer 规范](StateCommit.md)
- [数据类型与传递契约规范](DataContracts.md)
- [EventSystem 与事件协作规范](EventSystem.md)
- [UI 表现层架构规范](UIArchitecture.md)

## 核心职责类型定义

1. `Manager` 用于承担项目级公共功能管理、全局运行管理、共享资源管理、表现管理及跨场景流程协调等职责，统一采用继承 `MonoBehaviour` 的可挂载项目运行级 Unity 组件单例，通常跨场景存续。Manager 可以独立承担完整的项目级公共功能，并在自身职责范围内拥有处理逻辑、权威运行状态、资源管理、配置入口和可视化调试能力。根据实际需求，充分利用 Unity Hierarchy 和 Inspector 提供参数配置、引用绑定和运行状态观察能力。典型示例包括 `UIManager`、`AudioManager`、`RedDotManager`、`SceneFlowManager`、`ConfigManager`。Manager 不得承担具体业务系统的核心职责，不得替代业务 System 组织领域规则、完整业务用例及权威业务状态更新。单场景流程协调、局部对象控制和纯业务计算不得命名为 `Manager`。 PoolManager 与 AssetManager 的资源管理职责及默认基础架构遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。
2. `System` 用于承担具有明确系统语义的业务系统或框架系统职责，其中业务系统为主要使用场景。统一采用不可挂载的项目运行级普通 C# 单例，不得继承 `MonoBehaviour`，不得依赖 Unity 组件单例机制。System 负责所属系统的生命周期、主要用例、统一对外入口、系统规则协调、内部功能组织及状态处理。调用方可以通过项目统一的单例入口直接调用其公开能力。具体规则和计算优先根据职责语义、稳定架构边界及实际需要组织到职责明确的内部类型中。System 可以直接使用已有的适用处理能力，也可以将具体处理全部委托给下层类型；不得仅因当前逻辑简单而否定合理拆分，也不得为了形式机械拆分。典型业务系统包括 `MissionSystem`、`InventorySystem`、`CombatSystem`、`SkillSystem`；典型框架系统包括 `EventSystem`、`ConfigSystem` 等。System 应明确所属状态的持有者，通过业务对象或 RuntimeData 等符合实际职责的状态承载方式组织状态读取、业务计算和结果提交。System 可以按照既有业务契约直接组织状态提交，也可以委托具有明确状态提交职责的 Committer 完成实际提交。System 始终负责整个业务用例的组织、提交契约的确定及跨业务 System 的协作，不因委托 Committer 而转移所属系统的业务责任。不得独立维护另一份能够决定同一业务事实的权威状态。System 不得依赖或调用 Manager，也不得仅因需要 Inspector 配置、Unity 对象引用或可视化调试而改变其系统职责及命名。单例实现及依赖方向遵循本规范。 存在异步、并发或重入需求时，System 负责组织符合所属业务契约的执行策略、状态一致性及完成边界，具体遵循[异步业务与状态一致性规范](../Runtime/StateConsistency.md)。 System 默认作为 public 类型提供明确的公开能力，不默认暴露其内部处理实例或可写权威状态；具体访问范围遵循[类的访问权限与公开接口规范](../Code/AccessControl.md)。 AssetSystem 属于新项目默认资源框架 System；PoolSystem 按实际共享复用框架需求引入。具体职责及依赖遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。 LogSystem 属于新项目默认日志框架 System，统一日志记录及输出而不接管业务异常处理；具体遵循[Unity 日志与异常处理规范](../Runtime/Logging.md)。
3. `Module` 只用于附属于一个已经明确存在的对象或所有者、负责一项独立功能能力的 Unity 挂载组件。它连接该能力所需的 Inspector 引用和 Unity 生命周期，但不代表所有者对象本身，不持有所有者的完整身份或核心权威状态，也不作为完整业务系统入口。
4. `Module` 必须能够明确说明“从属于哪个对象、提供什么独立能力”，名称应体现具体能力，例如 `GuideTargetInputModule`。禁止使用 `CharacterModule`、`StarModule`、`CardModule` 等只重复对象名称、没有说明独立能力的宽泛名称。
5. 仅仅因为类型继承 `MonoBehaviour`、挂载在 GameObject、Prefab 或场景对象上、连接 Inspector 引用或使用 Unity 生命周期，不得添加 `Module` 后缀。
6. 一个功能只有在能够独立拆卸、替换或复用，并且移除后不会改变所有者对象身份时，才考虑拆分为 `Module`；不得为了形式提前拆分。
7. `Controller` 用于协调所属场景中的多个对象、页面、Prefab 或具体流程，统一采用场景级 Unity 组件单例，生命周期跟随所属场景，不作为项目级跨场景单例。一个场景实例可以具有多个职责明确的不同类型 Controller，同类型 Controller 在同一场景实例内保持唯一。单例实现优先复用项目框架已有的场景级单例机制和稳定写法。
8. `Initializer` 用于明确范围内的唯一初始化入口，负责按规定顺序装配、注册或启动对象，不承担初始化完成后的持续业务处理。
9. `Factory` 用于创建和初始化某类运行时对象，不负责对象创建完成后的业务流程。 对象的实际创建、每次租用初始化、归还及最终销毁应区分；Factory 不得通过 PoolManager 形成 System 的间接反向依赖，具体遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。
10. `State` 用于状态机中的具体状态或具有明确生命周期的状态对象；普通数据类型不得仅因为保存数据而命名为 `State`。

## Manager、System 与 Controller 的关系及单例约定

1. **Manager 与 System 具有明确的架构层级和单向依赖关系。** Manager 位于 System 上层，允许 Manager 调用 System，禁止 System 反向依赖或调用 Manager。System 不得通过单例访问、依赖注入、中转接口、委托、其他职责类型或其他方式变相调用 Manager。

2. **Manager 与 System 不构成强制配套关系。** Manager 可以独立承担完整的项目级公共功能，System 可以独立承担完整的业务系统或框架系统职责。不得为了架构形式强制创建同名 Manager 和 System，也不得仅为了转发调用、提供单例入口或增加架构层级而创建没有独立职责的类型。 从零设计项目时，按照[配置数据管理规范](../Runtime/Configuration.md)默认建立具有独立管理职责和框架职责的 ConfigManager 与 ConfigSystem，属于已明确的配置基础架构先行约定；不改变其他 Manager 与 System 不强制配套的原则，不授权已有项目自动补建或重构。 从零设计时，AssetManager 与 AssetSystem 同样属于已明确的资源基础架构先行约定；PoolManager 纳入默认基础架构，PoolSystem 按实际框架需求引入，不强制配套。具体遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)，不授权已有项目自动补建。 从零设计时，LogSystem 属于已明确的日志基础架构先行约定，LogManager 仅按独立管理职责引入，不强制配套，不授权已有项目自动补建或重构。

3. **Manager 与 System 的选择必须以实际职责为首要依据。** 承担明确业务系统职责的类型必须使用 System，不得以 Manager 替代。承担项目级公共功能管理、全局运行管理、表现管理、共享资源管理或跨场景协调职责，并适合作为 Unity 可视化管理入口的类型，优先使用 Manager。承担独立框架系统机制，向其他 System 或上层调用方提供系统能力的类型，可以使用 System；供 System 主动调用的框架系统能力不得以 Manager 作为调用入口。功能完整性、逻辑复杂度、代码数量、内部状态数量及是否具有统一入口，均不得单独作为 Manager 与 System 的选择依据。可挂载性、单例和跨场景存续是职责确定后的实现与作用域约束，不得单凭这些特征决定类型的职责归属。

4. **Manager 可以拥有自身职责范围内的完整逻辑和权威运行状态。** Manager 不仅负责资源持有、对象引用和执行操作，也可以承担自身管理职责所需的规则判断、状态汇总、调度、注册关系和生命周期管理。例如 RedDotManager 可以独立管理红点节点树、父子节点状态汇总、显示实例绑定及刷新流程，不要求仅因存在这些逻辑和状态而额外建立 RedDotSystem；但不得自行实现任务领奖、邮件领取等所属业务 System 的领域规则。

5. **同一领域同时存在 Manager 与 System 时，必须明确独立职责。** 两者不得重复实现同一规则、重复组织同一完整用例，或分别保存同一份具有独立裁决能力的权威状态。只有确实存在独立的上层管理职责和系统职责时，才考虑同时建立两种类型。

6. **Manager 应发挥 Unity 可视化管理优势。** 根据实际管理需求，通过 Hierarchy、Inspector 和项目现有 Odin Inspector 机制提供配置、引用绑定及运行状态观察能力。Inspector 中展示的其他 System 的状态必须具有明确数据来源，不得成为另一份独立的权威业务状态。可视化需求不得成为将业务 System 改为 Manager 的理由，也不得为了展示状态而机械增加 Manager。Inspector 字段、分组、引用绑定和调试操作仍须遵守 Odin Inspector 规范、Unity 项目约束及相关变更授权规范。 资源及对象池运行观察应基于实际管理或框架状态，不维护重复权威数据，不为观察而无界持有对象、资源或历史记录。

7. **合法调用方可以直接调用 System。** Manager、Controller 及其他符合本文依赖规范的调用方，可以直接通过 System 的公开单例入口使用系统能力，不要求所有请求经过 Manager 转发。单例访问入口的名称和使用方式应沿用项目已有约定。 公开入口及其成员的可访问性不代表所有调用方均具备架构调用权限，具体遵循[类的访问权限与公开接口规范](../Code/AccessControl.md)。 ConfigManager 可以主动调用 ConfigSystem，业务 System 可以按照合法配置访问契约使用 ConfigSystem。ConfigSystem 及业务 System 不得反向依赖 ConfigManager。具体职责及默认配置架构遵循[配置数据管理规范](../Runtime/Configuration.md)。 AssetManager、PoolManager 可按职责调用 AssetSystem，业务 System 可使用合法资源及按需建立的 PoolSystem 框架能力，不得直接或间接依赖 AssetManager、PoolManager。

8. **System 可以通过业务事件通知上层，但不得形成反向依赖。** System 可以按照项目既有事件机制发布已经成立的业务事实。Manager 可以主动订阅事件，并根据事件组织自身管理职责内的后续操作。System 不得感知具体 Manager，不得通过定向事件或回调要求 Manager 完成所属系统的必要业务步骤，不得通过事件机制变相建立反向依赖。Manager 响应事件所产生的表现、管理及异步操作，不自动成为 System 完成其业务用例的前置条件；业务结果和前台流程的完成边界仍遵循各自既有契约。 EventSystem 执行订阅者主动注册的通用监听回调，属于框架事件分发机制，不等同于 System 对具体 Manager 的反向依赖。具体发布、订阅及分发边界遵循[EventSystem 与事件协作规范](EventSystem.md)。

9. **单例实现必须符合类型的职责、有效作用域和生命周期。**
   - Manager：采用继承 `MonoBehaviour` 的可挂载项目运行级 Unity 组件单例。同类型 Manager 在同一次项目运行期间，任一时刻只能存在一个有效实例，通常跨场景存续。
   - System：采用不可挂载的项目运行级普通 C# 单例。同类型 System 在同一次项目运行期间，任一时刻只能存在一个有效实例。不得继承 `MonoBehaviour`，不得采用 Unity 组件单例机制。
   - Controller：采用场景级 Unity 组件单例。同类型 Controller 在同一场景实例内保持唯一，生命周期跟随所属场景，不作为项目级跨场景单例。

   单例的实际实现必须与其规定的唯一性范围一致，不得将项目运行级单例与场景级单例混用。项目运行级唯一性不要求从项目启动至关闭始终存在同一个实例；实例创建、释放和重新初始化应符合实际生命周期约定，任一时刻不得存在多个同时有效的同类型实例。

   必须先检查项目框架及现有代码中是否已有符合对应职责、作用域和生命周期的单例基类、机制及稳定写法。存在适用方案时，应优先复用，并保持其继承方式、访问入口、初始化和释放约定，不得自行另起一套。Controller 应优先复用符合场景级生命周期的单例实现。确认没有适用实现后，再按照公共基础模块修改的报备与确认规则，统一补充可供相同单例机制的类型复用的基础能力，不得在各个 Manager、System 或 Controller 中分别建立不同的单例写法。新增或修改公共单例基础能力，仍须遵守 AI 修改与交付规范中关于公共基础模块修改的报备与确认要求。

10. **调用关系、实例生命周期与状态生命周期必须分别确定。**

    Manager 位于 System 上层，不代表 Manager 必须持有、创建、初始化、重置或释放所有 System。Manager 可以按自身职责持有和使用 System，但持有引用不代表其他调用方必须经过该 Manager，也不自动取得该 System 的生命周期所有权。

    单例实例的存在不代表其配置、依赖和业务数据已经完成初始化，也不代表其当前具备执行所有操作的条件。Manager、System 和 Controller 应根据实际职责及项目既有机制，明确实例创建、初始化、业务数据就绪、状态清理及实例释放的责任和时机。

    单例实例的生命周期与其管理的数据、对象、资源、事件订阅和异步操作的生命周期应分别确定。单例跨场景存续，不代表其内部所有状态和引用必须跨场景保留。状态有效范围及清理时机遵循实际职责和既有业务契约。 单例唯一性不代表业务请求串行执行；业务执行、调用方等待与状态有效范围的区别及一致性要求遵循[异步业务与状态一致性规范](../Runtime/StateConsistency.md)。 单例、跨场景对象及静态引用不自动要求资源永久驻留；应按实际资源所有权、借用及释放契约管理，具体遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。

    不得为了管理单例生命周期而机械新增 Manager、Initializer 或其他管理类型，也不得为不存在实际生命周期需求的类型强制增加初始化状态机。

11. **单例访问不得破坏其他职责类型的依赖边界。** 调用方仍须通过 System 的公开能力提交请求；直接访问单例不解除本文对 Processor、Logic、Resolver、Rules、Calculator、Evaluator、Provider 等内部职责类型的既有依赖限制。 不得通过公开成员暴露内部实现、可写权威状态或允许无关调用方替换单例；访问权限不改变单例的实际所有权与生命周期契约。

12. **单例约束仅适用于具有明确单例职责的类型，不得向下传递。**

    Manager、System 和 Controller 采用单例设计，不代表其管理、持有、创建或使用的对象也必须采用单例。业务对象、RuntimeData、Module、Processor、Logic、Resolver、Provider、Registry、Committer、Service、DataStorage 等类型，应根据实际职责、对象身份、所有权、复用方式及生命周期确定实例数量和创建方式，并继续遵守各自的职责及依赖约定。Committer 默认采用不可挂载的普通 C# 实例，符合其专门约定时允许静态实现。Operation、Formula、Algorithm 等基础处理实现类型及 Utility 不继承上层单例约束，默认优先采用静态实现，具体遵循下方专门章节。

    Initializer 的唯一性指所属装配范围内具有明确且唯一的初始化入口，不强制采用单例模式。

    System 可以统一处理多个具有独立身份和状态的业务对象，不得因为 System 采用单例而限制业务对象的数量，或将不同业务对象的状态强行合并。不得仅为了方便访问某个对象或处理类型，就将其设计为全局单例。

13. **实际需求与单例约定冲突时，必须优先检查职责归属和作用域。**

    当某个类型需要同时存在多个有效实例，而该类型原计划采用 Manager、System 或 Controller 命名时，必须先判断其实际职责是否符合对应类型的定义，以及多实例需求是否发生在该类型规定的唯一性范围内。

    如果该类型实际代表具体业务对象、游戏对象、局部能力、独立执行过程或其他非单例职责，应按照实际职责选择合适的类型名称和实例管理方式，而不是放宽 Manager、System 或 Controller 的单例约束。

    如果多实例需求可以通过区分业务对象、业务身份或状态有效范围解决，应优先保持 System 的统一单例入口，不得为每个业务对象分别创建 System 实例。

    如果类型的职责归属正确，但项目确实存在无法通过现有单例约定满足的需求，Agent 必须先说明冲突原因、实际实例需求、候选方案、依赖影响及生命周期影响，取得用户确认后才能调整架构。不得擅自取消单例约束、修改单例基类，或引入多例模式、依赖注入容器、作用域容器等额外架构。

14. **场景级单例的实现必须与实际场景运行方式一致。**

    当项目同一时间只存在一个具有对应 Controller 的有效场景实例时，可以沿用项目已有的场景级单例访问机制。

    当项目需要同时加载多个场景，并且不同场景实例中需要同时存在同类型 Controller 时，必须保证各 Controller 的实例获取、生命周期和状态能够按照所属场景实例正确区分。不得使用无法区分场景归属的全局静态实例入口，错误覆盖、销毁或访问其他场景实例中合法存在的 Controller。

    如果现有单例机制无法满足实际多场景并行需求，Agent 应先提出按场景获取实例、显式引用或其他符合项目架构的方案，并取得用户确认。不得在项目不存在相应需求时，为假设性的多场景并行情况提前建立复杂的场景注册、实例索引或作用域管理框架。

### Manager 与 System 职责判定参考

| 功能需求 | 默认命名 | 判定依据 |
|---|---|---|
| 全局 UI 管理 | `UIManager` | 界面资源、实例、层级及运行管理 |
| 全局音频管理 | `AudioManager` | 音频资源、播放对象、播放控制及配置 |
| 全局红点管理 | `RedDotManager` | 提示状态汇总、显示实例管理及运行观察 |
| 全局对象池管理 | `PoolManager` | 对象资源的分配、复用与回收 |
| 全局资源管理 | `AssetManager` | 资源来源、资产装配、管理流程及运行观察 |
| 场景切换管理 | `SceneFlowManager` | 跨场景运行流程协调 |
| 全局配置管理 | `ConfigManager` | 配置来源管理、资产装配及运行观察 |
| 背包系统 | `InventorySystem` | 物品状态、容量、堆叠及转移规则 |
| 任务系统 | `MissionSystem` | 任务进度、完成条件及奖励规则 |
| 战斗系统 | `CombatSystem` | 战斗规则及结算流程 |
| 技能系统 | `SkillSystem` | 技能释放、消耗及冷却规则 |
| 商店系统 | `ShopSystem` | 商品交易、购买条件及业务状态 |
| 事件系统 | `EventSystem` | 提供独立的框架事件机制 |
| 统一配置框架 | `ConfigSystem` | 配置加载组织、有效配置持有及统一查询 |
| 统一资源框架 | `AssetSystem` | 资源加载、访问、生命周期及释放机制 |
| 统一日志框架 | `LogSystem` | 统一日志记录、格式组织及底层输出，不接管业务异常处理 |
| 项目级日志管理（按需） | `LogManager` | 独立的日志配置装配、管理流程及运行观察 |
| 共享对象复用框架（按需） | `PoolSystem` | 为合法 System 提供独立的通用对象复用机制 |
| 集中更新管理（按需） | `UpdateManager` | Unity 更新注册及调度管理 |
| 属性系统 | `AttributeSystem` | 属性计算与数值关系 |

上述示例为缺少项目专用约定时的默认职责判定参考，不要求所有项目创建上述类型，也不要求对已有稳定类型进行批量重命名。实际设计仍应根据职责、依赖关系及项目已有架构确定。对现有代码的架构调整、命名重构及公共基础能力变更，仍须遵守 AI 修改与交付规范中的报备与确认要求。 ConfigManager 与 ConfigSystem 在新项目中的默认配套及已有项目保护，按照[配置数据管理规范](../Runtime/Configuration.md)的专门约定执行。 PoolManager、AssetManager 与 AssetSystem 的新项目默认基础架构，以及按需引入的 PoolSystem、UpdateManager，遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)的专门约定。 LogSystem 的新项目默认基础架构及按需引入的 LogManager，遵循[Unity 日志与异常处理规范](../Runtime/Logging.md)的专门约定。

依赖与事件协作示例：

```text
直接调用与依赖方向：

Manager → System     允许
System  → Manager    禁止

业务事件协作：

RedDotManager → EventSystem：主动订阅业务事件
AudioManager  → EventSystem：主动订阅业务事件
MissionSystem → EventSystem：发布已经成立的业务事实

RedDotManager 在收到业务事件后，主动查询相关 System，组织红点刷新。
AudioManager 在收到业务事件后，按已确认的表现约定执行音频播放。
MissionSystem 不感知具体 Manager，不依赖 Manager 完成任务业务。
```

### 单例作用域与多实例职责判定参考

| 实际需求 | 推荐设计 | 不推荐的设计 |
|---|---|---|
| 存在多个敌人，例如 100 个 | 多个 `Enemy` 对象 | 每个敌人分别使用一个 `EnemyController` |
| 每个敌人拥有独立移动能力，且需要独立拆分该能力 | 多个 `EnemyMovementModule` 实例 | 每个敌人分别使用一个移动 Controller |
| 场景内统一协调敌人生成 | 场景级 `EnemySpawnController` | 将单场景生成协调职责命名为 `EnemySpawnManager` |
| 存在多个背包 | 一个 `InventorySystem`、多个 `Inventory` 对象 | 为每个背包分别创建 `InventorySystem` 实例 |
| 同时处理多场独立战斗 | 一个 `CombatSystem`、多个具有独立身份与状态的 `Battle` 对象 | 默认创建多个 `CombatSystem` 实例 |
| 商店中存在多个商品条目 | 多个 `ShopItem` 对象 | 每个商品条目分别使用一个 `ShopItemController` |

上述示例用于判断职责归属和实例数量，不要求为了示例提前拆分类型，也不要求对已有稳定实现进行无关重命名或重构。实际设计应遵守本规范的职责命名与作用域规则，以及 AI 修改与交付规范中的变更授权要求。

## Initializer 的归属、装配与协作

1. **Initializer 的作用域由装配范围确定。** 同一装配范围内应有明确且唯一的初始化入口。装配时应明确依赖来源、就绪时机以及对象和资源的所有者。资源由实际所有者按其生命周期释放。 需要资源时应使用已有合法加载及装配契约，明确实际取得、借用、转移及释放责任，不重复实现 AssetSystem 已承担的通用资源机制。

2. **Controller 可以直接装配自己拥有的简单依赖。** 多个对象或 System 需要按明确顺序统一装配时，可以设置独立的 Initializer。两者同时存在时，可以在装配后显式启动 Controller，也可以由 Controller 等待初始化结果。依赖就绪后，由 Controller 协调所属场景的流程、输入和表现。

3. **Initializer 通过 Manager 的既有入口取得全局能力或共享资源。** Manager 按既有契约管理自己拥有的全局实例和资源。资源的取得、持有与释放责任应分别明确。

4. **System 的装配遵循其既有生命周期约定和单向依赖规则。** Initializer 可以按职责调用 System 已定义的初始化或注册入口。需要由 Manager 提供的配置、数据或资源引用时，可以由 Manager 或其他明确的上层装配入口取得后显式传入 System，并明确所有权和有效范围；不得将 Manager 实例、指向 Manager 的执行接口或委托作为 System 的依赖。System 负责所属业务系统或框架系统的用例、规则协调和状态处理。是否设立独立的 Initializer，由实际装配需求决定。 初始化、装配、注册及释放入口按实际生命周期所有权和合法调用范围确定可见性，不自动作为普通业务公开能力。 Initializer 可以根据明确装配范围组织配置准备、接收加载结果及装配配置依赖。具有统一配置框架时，应使用 ConfigSystem 已有的配置能力，不重复实现配置加载机制；配置就绪、System 实例创建和业务初始化完成条件分别确定，具体遵循[配置数据管理规范](../Runtime/Configuration.md)。

5. **Factory 负责创建对象，Initializer 负责装配对象。** 需要创建运行时对象时，Initializer 可以调用 Factory，再把创建好的对象和所需依赖提供给对应的所有者。 对于合法复用的对象，按本次租用契约完成装配及初始化，不将租用机械视为重新创建，不重复初始化或提交业务。

6. **Module 的依赖由明确的装配入口提供。** Initializer 可以通过 Inspector 引用、初始化参数或显式注册为 Module 提供依赖。Module 的能力边界和生命周期按「Module 的归属、装配与协作」约定确定。

例如，一个回合制场景需要创建玩家、注册本局参与者，再启动场景流程。

```text
Initializer
    → 调用 Factory 创建玩家
    → 向回合 System 注册本局参与者
    → 为输入 Module 提供依赖
    → 交付初始化结果

Controller
    → 在依赖就绪后协调开场、交互与结算展示
    → 需要推进回合时，向回合 System 提交请求并处理结果
```

## 跨 System 业务协作

1. **跨 System 的业务用例必须明确主导 System。** 主导 System 负责组织业务步骤、处理步骤之间的依赖，并向调用方返回整个用例的明确结果。优先由具有自然业务归属的现有 System 承接，例如任务领奖由 MissionSystem 主导；不得为每次跨系统调用机械新增 Manager、协调类或新的 System。

2. **各 System 保持各自领域的规则和状态边界。** 主导 System 通过其他 System 的公开业务能力或已经明确的提交契约完成协作。所属领域的规则判断和状态更新仍由对应 System 及其内部职责类型负责；主导 System 不得重复实现其他领域的规则，也不得绕过约定直接修改其他领域的权威状态。

3. **跨 System 协作可以沿用项目统一的单例入口直接调用。** 不强制增加接口、中转层或依赖注入框架。只有现有架构要求，或者存在明确的替换、隔离等需求时，才引入相应抽象；直接访问单例不改变职责边界。 具有明确稳定能力契约时，即使目前只有一个实现，也允许建立相应接口；不因抽取接口而改变既有依赖方向及业务调用边界。

4. **项目级管理、展示交互协调与业务用例协调应分别归属。** Manager、Controller、承担展示交互职责的独立 Presenter，以及 Module，可以在自身职责内查询多个 System，提交业务请求、处理系统返回结果，或将相互独立的操作分别提交给对应 System。具有业务前后依赖的跨系统状态变更，应提交给主导 System 组织，不得由 Manager、UI 或局部组件拼接成另一套完整业务流程，也不得绕过所属 System 修改权威业务状态。Manager 对多个 System 的状态进行汇总，并用于全局管理、表现更新或运行观察，不等同于接管跨系统业务流程。

5. **内部处理类型继续遵守既有依赖方向。** 跨 System 协作不解除 Processor、Logic、Resolver、Rules、Calculator、Evaluator 及其他具体职责能力、基础处理实现层的职责限制。纯领域处理通过显式输入数据和明确结果参与协作，不得自行获取其他 System 单例或通过 Utility 等类型间接访问上层能力；Processor 仍通过显式提交接口提交结果，不依赖具体业务 System。Committer 只组织自身提交范围内的步骤，不反向依赖所属业务 System，不默认调用其他业务 System，不得替代主导 System 组织跨系统用例或直接修改其他系统的权威状态。需要使用 EventSystem 等框架能力时，遵循状态提交层的专门约定。 事件发布及订阅不得突破上述内部职责边界，具体遵循[EventSystem 与事件协作规范](EventSystem.md)。 同一程序集内的 internal 类型仍可能被其他业务访问，技术可访问性不解除上述架构访问限制。

6. **同一次业务请求的处理责任必须清晰。** 被调用 System 返回自身步骤的明确结果，由主导 System 根据结果和已确认的业务契约决定后续步骤。不得将同一次请求在多个 System 之间循环转发，导致重复执行或责任不明。不同业务用例可以分别由不同 System 主导，不因两个 System 在不同用例中互相提供能力而直接判定为违规。

7. **整体结果必须反映真实执行情况。** 主导 System 应保留各步骤已经完成、失败、取消或结果尚未确认的事实，不得将部分完成或结果未确认统一当作全部成功或全部失败。状态提交顺序、持久化时机、部分成功处理、重试和补偿方式，按具体业务已经确认的契约执行，不得自行添加自动回滚、重复提交或静默恢复策略。 跨系统顺序执行不自动保证整体原子性；实际一致性范围、部分完成和未知结果的处理遵循[异步业务与状态一致性规范](../Runtime/StateConsistency.md)。

8. **区分跨 System 的直接调用与独立事实响应。** 允许业务 System 订阅其他 System 发布的事实事件，并在自身职责范围内组织独立后续业务。具有必要业务前后依赖、明确结果要求或整体成功条件的跨系统操作，仍由主导 System 显式组织，不得通过普通事件监听关系隐藏业务执行依赖，不得以监听者注册或分发完成代替实际业务结果。

Manager 与 System 协作边界示例：

| 行为 | 是否符合规范 |
|---|---|
| `RedDotManager` 查询任务、邮件、背包的提示状态 | 允许 |
| `RedDotManager` 汇总红点节点的状态并更新显示 | 允许 |
| `AudioManager` 根据已成立的业务事件及已确认的表现约定播放音效 | 允许 |
| `MissionSystem` 直接或通过中转接口调用 `AudioManager` | 禁止 |
| Manager 代替 `MissionSystem` 组织任务领奖与背包奖励提交 | 禁止 |
| `InventorySystem` 为了显示红点而调用 `RedDotManager` | 禁止 |

Committer 协作边界示例：

| 行为 | 是否符合规范 |
|---|---|
| `MissionSystem` 调用所属 `MissionRewardCommitter` | 允许 |
| Processor 通过显式提交接口调用 Committer | 允许 |
| Committer 调用 DataStorage 完成既定保存步骤 | 允许 |
| Committer 通过 EventSystem 发布已成立的业务事实 | 允许 |
| `MissionRewardCommitter` 自行组织完整的背包奖励发放业务 | 禁止 |
| `MissionProgressLogic` 调用 `MissionProgressCommitter` | 禁止 |
| Controller 绕过 System 直接调用内部 Committer | 禁止 |
| Committer 调用 `AudioManager` 播放提交成功音效 | 禁止 |

## Module 的归属、装配与协作

1. **Module 表达对象的能力组成，不是业务数据流中的固定层级。** 不得为了架构形式，要求所有请求都经过 Module，或者在 System 与 Processor 之间固定增加 Module。
2. **每个 Module 必须具有明确的所有者和能力边界。** 所有者可以是业务对象、场景或具体 Controller。所有者负责组织和使用能力；Module 不代表所有者本身，也不承担完整业务系统入口。拆除或替换 Module 后，所有者的对象身份应保持成立。
3. **Module 可以实现本能力范围内的局部行为，并保存必要的执行状态。** 例如输入订阅、动画播放进度、异步取消句柄和自己创建的资源；不得重复保存所有者的核心权威状态，也不得重新实现所属业务系统的规则。
4. **Module 的依赖必须由明确的装配入口提供。** 根据项目已有架构，由所有者、Controller、Initializer 或 Factory 通过 Inspector 引用、初始化参数或显式注册完成装配。不得依靠场景扫描、全局查找或静默创建依赖补齐配置，也不得为每个 Module 默认创建一套独立 System。 Module 的类型与成员可见性按挂载、装配及真实能力契约确定，不因可挂载而公开所有字段和方法。
5. **接收外部交互的 Module，可以向所属 System 提交业务请求。** Module 负责采集输入、转换交互参数和接收处理结果；涉及完整业务用例、业务规则或权威状态提交时，必须遵循所属 System 的处理边界。简单的对象局部能力可以由所有者直接调用，不强制额外建立 System。
6. **一个 Module 可以显式依赖并调用多个 System，不要求一对一关系。** 所有调用必须服务于该 Module 明确的能力职责，例如查询只读信息，或者将相互独立的交互分别提交给对应系统。具有业务前后依赖的跨系统状态变更，应按“跨 System 业务协作”交由主导 System 组织；各领域规则和权威状态仍由所属 System 及其内部职责类型处理。Controller 可以协调场景和展示交互步骤，但不得因此承担领域规则或绕过 System 修改权威状态。
7. **提供执行能力的 Module，可以接受 System 或应用层提交、执行实现的明确调用。** 例如播放动画、控制特效或更新对象表现。需要替换实现、复用能力或隔离 Unity 依赖时，应通过职责明确的能力接口调用。调用参数应包含执行所需的信息，Module 不自行推断业务结论，也不通过反向查询完整 System 隐式补齐执行条件。System 发起的此类调用仍须遵守 Manager 与 System 的单向依赖规则，不得通过 Module、能力接口或应用层提交、执行实现变相调用 Manager。Committer 不因 Module 具备执行能力而接管与状态提交无关的 UI、动画、音效等通用表现流程；相关操作仍由明确的上层或执行实现按既有契约组织。
8. **纯业务处理类型不得依赖具体 Module。** Logic、Resolver、Rules、Calculator、Evaluator 及其他纯业务处理类型、基础处理实现层只接收显式业务数据及符合各自约束的依赖并返回结果，不得通过具体 Module、Committer 或能力接口间接执行外部副作用；Unity 组件操作和其他外部副作用，由应用层提交或执行实现协调。Processor 通过显式提交或执行接口委托相关操作，不直接承担具体 Module 的执行细节。输入类与执行类是两种协作方式，不要求每个 Module 同时承担两种职责，也不得形成相互转发的调用循环。 纯业务处理类型也不得通过 EventSystem、局部事件或回调间接执行外部业务副作用，不作为独立的全局业务事件监听者。
9. **Module 必须明确自身生命周期和资源清理责任。** 停用、销毁或解除绑定时，应按照该能力的生命周期约定注销监听、取消自身异步操作并释放自己拥有的资源。只有明确拥有 System 生命周期的宿主，才负责释放该 System；普通能力 Module 不得擅自释放共享 System 或其他对象的资源。 订阅解除与异步操作取消分别按照实际所有权及生命周期组织，不得假设解除订阅会自动取消已经启动的异步操作。 池化 Module 应区分实例生命周期与每次租用生命周期，按实际契约清理本次租用的订阅、任务、状态和独占资源，保留仍有效的稳定依赖；不得提前释放共享资源或将旧结果应用到新的租用身份。
10. **Module 提供异步能力时，必须明确完成、失败、取消和清理边界。** 调用方需要等待实际执行完成时，返回的异步操作必须覆盖约定的执行与必要清理过程；不得只启动后台任务便向调用方报告完成。失败与取消应提供明确结果，不得吞掉错误或将未完成的操作报告为成功。 局部执行取消、调用方等待取消与关联业务取消应分别遵守其实际契约，不得因 Module 停用或销毁而自动撤销已经生效的业务效果，具体遵循[异步业务与状态一致性规范](../Runtime/StateConsistency.md)。
11. **Module 的事件协作必须服务于自身能力职责。** 可以使用局部事件，或订阅与其职责相关的全局事实事件；确有全局通知职责时，可以在自身能力范围内发布已经成立的事实。不得通过事件直接修改其他业务系统的权威状态、绕过所属 System 或变相调用 Manager。订阅、解除订阅及异步响应遵循[EventSystem 与事件协作规范](EventSystem.md)，并与实际能力生命周期一致。

关系示例如下，各路径按实际职责使用，不要求每个功能全部具备：

```text
归属关系：

业务对象 / 场景 / Controller
    └─ Module：提供某项独立能力


请求进入业务系统：

外部交互
    → 输入类 Module：采集输入、转换参数、转发请求
    → System
    → 原有业务处理数据流


执行已经确定的业务结果：

System 或应用层提交、执行实现
    → 能力接口（需要隔离或替换时）
    → 执行类 Module
    → Unity 组件或该能力拥有的资源
```
