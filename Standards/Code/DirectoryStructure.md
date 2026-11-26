---
id: code.directory-structure
title: 代码目录组织与文件归属规范
category: Code
scope: Unity
---

# 代码目录组织与文件归属规范

## 规范定位与适用范围

定义 Unity 项目目录、领域归属、职责目录、文件拆分及生成产物保护。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

本节在既有架构先行、职责拆分、命名、访问权限、配置及资源管理约定的基础上，明确项目代码目录、职责所有者、独立文件和配置资产的物理组织方式。默认采用以职责所有者为核心的领域目录组织方式：项目级区分公共框架与游戏业务，游戏业务主要以业务 System 所代表的领域组织，领域内部按照已确定的职责层级进一步划分。目录表达职责归属，不改变实际调用、状态所有权、单例作用域及语言访问边界。

## 相关规范

### 基础依赖
- [代码命名规范](Naming.md)
- [类的访问权限与公开接口规范](AccessControl.md)

### 条件协作
- [配置数据管理规范](../Runtime/Configuration.md)
- [Unity 项目约束](../Unity/ProjectConstraints.md)
- [Unity Prefab 职责与封装规范](../Unity/Prefab.md)


## 适用范围与规则优先级

1. **用户明确指定的合法位置优先。** 用户明确指定文件、目录、配置资产、生成目标或其他存放位置时，应按其合法要求组织。用户未指定的部分，优先采用有效项目及局部专用约定、复用已有稳定目录、术语、文件归属和技术契约；缺少适用约定时采用本节默认方案。上述位置选择的优先关系不解除硬性职责、技术条件及变更授权，统一遵循[规则优先级与冲突处理规范](../Governance/RulePriority.md)。
2. **统一职责归属，不强制迁移已有目录。** 默认目录结构用于新项目、新建且缺少适用目录约定的业务，以及经确认的目录整理或架构重构。不得仅因已有目录名称、层级、分类方式或代码根目录与本节不同，就判定需要重构。
3. 用户指定位置或已有目录与实际 Unity 导入、资源路径、生成流程、程序集、职责及依赖约束发生冲突时，应说明具体冲突、调用及资源影响和候选方案，经用户确认后处理，不自行更换位置或改变架构契约。
4. 新增文件前应先识别真实职责所有者、现有实现及所属维护范围；不得在已有适用领域之外创建职责重复的平行目录或类型。实际缺陷、重复实现和归属冲突应按[AI 修改与交付规范](../Governance/AgentWorkflow.md)报告及处理，并遵循[规则优先级与冲突处理规范](../Governance/RulePriority.md)判断适用规则及冲突。 同时识别目标目录及其相关上层规范；修改文件时按其实际所属范围及维护来源识别有效规范，跨目录任务分别检查实际涉及的各范围及公共契约。
5. 本节不构成主动迁移、重命名、删除、批量整理已有文件，修改资产绑定、生成目标、程序集、命名空间或引入新运行机制的授权。目录组织不得成为新增校验、强制资源路径或运行时业务限制的依据。

## Unity 项目默认目录结构

缺少项目专用组织约定时，新建 Unity 项目默认采用以下代码及配置目录结构：

```text
UnityProject/
├─ Assets/
│  ├─ Scripts/
│  │  ├─ Framework/
│  │  └─ Game/
│  ├─ Resources/
│  │  └─ Configs/
│  │     ├─ ScriptableObjects/
│  │     ├─ Binary/
│  │     └─ Json/
│  ├─ Prefabs/
│  ├─ Scenes/
│  ├─ Art/
│  ├─ Audio/
│  ├─ Animations/
│  ├─ Shaders/
│  └─ Plugins/
├─ Excel/
├─ Packages/
├─ ProjectSettings/
└─ AGENTS.md
```

1. `Assets/Scripts/Framework/` 为项目公共框架及通用技术能力的默认代码根目录；`Assets/Scripts/Game/` 为具体游戏业务、游戏对象、项目专用管理和装配的默认代码根目录。
2. `Assets/Resources/Configs/` 为随游戏发布的本地配置资产默认目录；`Excel/` 为与 Assets 同级的项目根目录下原始 Excel 配置表默认目录。具体载体、生成产物及访问契约遵循本节相关约定和[配置数据管理规范](../Runtime/Configuration.md)。
3. Prefabs、Scenes、Art、Audio、Animations、Shaders、Plugins 等仅用于展示可能的项目资源组织，不在本节强制确立全部非配置资源的细分目录和命名体系。第三方插件及具有 Unity 技术语义的目录继续遵循真实技术契约。
4. 已有项目可以继续使用其稳定代码根目录、资源目录及包目录；默认结构不要求项目预先创建示例中的所有空目录，不授权迁移已有项目。

## 公共框架与游戏业务划分

1. **Framework 与 Game 根据实际职责及依赖划分，而不是按照类型后缀划分。** Framework 承载不依赖具体游戏业务的公共框架、技术基础能力及通用管理机制；Game 承载当前游戏的具体领域、规则、业务对象、项目专用管理、场景协调及装配。
2. Framework 默认按完整框架功能组织，例如 Core、Event、Config、Asset、Pool、Log、Utilities 等。框架 System、通用 Manager 及其所属内部实现可以在同一功能目录协作；不默认建立全局 Managers、Systems 等目录集中存放所有同后缀类型。
3. Game 按具体领域及实际职责所有者组织。依赖具体游戏业务、专用配置、专用页面或业务装配的 Manager、Initializer 等，应归入 Game 的对应领域、Management 或 Bootstrap 等明确职责范围，不将具体游戏业务反向引入通用 Framework。
4. **通用 Framework 不依赖具体 Game 业务。** Game 可以按合法职责调用 Framework 提供的能力；框架可以通过明确的数据、通用契约和合法装配机制接收业务提供的信息，不需要依赖具体业务 System 或其内部实现。通用框架回调仍须遵守事件、装配及既有依赖规则，不得借回调形成禁止的反向依赖。
5. Framework 与 Game 的划分不解除 Manager 与 System 的单向依赖、纯处理层的显式依赖、状态提交及实际访问权限约束。业务 System 不得仅因某个 Manager 位于 Framework 就直接或间接调用它。
6. ConfigManager、AssetManager、PoolManager 承担通用管理职责时，可以与对应框架功能归类；具有当前游戏专用装配或管理职责时，归入 Game 的相应范围。ConfigSystem、AssetSystem、EventSystem、LogSystem 等承担通用框架职责时归入 Framework。LogSystem 默认归入 Framework/Log/；按需建立的 LogManager 根据真实管理职责归入通用框架功能或项目专用管理范围。不得为了目录组织重复建立 Manager、System 或更改已有职责。

默认框架组织参考（按已确定的职责建立）：

```text
Framework/
├─ Core/
│  └─ Singleton/
├─ Event/
│  ├─ EventSystem.cs
│  ├─ EventSubscriptionRegistry.cs
│  └─ EventDispatcher.cs
├─ Config/
│  ├─ ConfigManager.cs
│  └─ ConfigSystem.cs
├─ Asset/
│  ├─ AssetManager.cs
│  ├─ AssetSystem.cs
│  └─ Adapters/
├─ Pool/
│  ├─ PoolManager.cs
│  ├─ PoolRegistry.cs
│  └─ Pools/
├─ Log/
│  └─ LogSystem.cs
└─ Utilities/
```

上述组织以对应管理类型具备通用职责为前提，示例不改变 ConfigManager + ConfigSystem、AssetManager + AssetSystem、PoolManager 的默认基础架构及 EventSystem、PoolSystem、UpdateManager 等各自的实际引入条件，不强制建立所有示例内部类型或 Adapter。 LogSystem 纳入新项目默认基础架构，LogManager 按独立管理需求引入，具体遵循[Unity 日志与异常处理规范](../Runtime/Logging.md)。

## 领域目录与职责所有者

1. **以职责所有者为核心建立领域目录。** 游戏业务默认以业务 System 所代表的业务领域为主要组织单位；具有独立业务职责的 System 默认具有独立的领域目录，其所属内部处理、提交、数据访问及数据契约在该领域组织。
2. 业务 System 文件默认放在其所属领域根目录，例如 `Game/Inventory/InventorySystem.cs`、`Game/Combat/CombatSystem.cs`。具有独立完整框架功能的 System 或 Manager 同样默认位于对应功能入口所在范围内，而非按后缀集中。
3. **领域目录不强制与 System 一一对应。** Manager 所负责的独立管理功能、具有独立身份的业务或游戏对象、Controller 所负责的场景流程、Initializer 所负责的装配范围及公共技术能力，都可以按照实际所有者独立组织目录。不得为了建立目录而增加不存在实际职责的 System、Manager、Controller 或其他类型。
4. 同一个领域可以同时具有 System、业务对象、表现及其他独立职责类型；同一个 System 可以拥有多个子领域。具有独立归属的类型应归入其自然所有者，不因某个上层负责调用或装配就迁移到调用者目录。
5. 目录归属表示代码维护及职责范围，不等于运行时实例、资源或状态所有权。具体所有权、实例数量、生命周期及调用关系继续按照各职责的专门规范确定。

| 领域组织依据 | 主要职责所有者 | 默认位置示例 |
|---|---|---|
| 业务领域 | 业务 System | Game/Inventory/ |
| 公共框架 | 框架 System 或独立技术能力 | Framework/Event/ |
| 通用公共管理 | Manager | Framework/Pool/ |
| 项目专用管理 | Manager | Game/Management/RedDot/ |
| 游戏对象 | 对象本身 | Game/Character/ |
| 场景协调 | Controller | Game/Scenes/Battle/ |
| 项目装配 | Initializer 或实际装配入口 | Game/Bootstrap/ |
| 公共技术能力 | 具有明确职责的技术类型 | Framework/Utilities/ |

## 业务领域及子领域组织

1. 业务目录优先集中所属业务的实际职责，而不是将整个项目的全部 Logic、Calculator、Committer 等同后缀类型集中在全局目录。业务领域内部按照本节统一职责目录约定组织。
2. **存在明确的子领域或稳定能力边界时，允许先按子领域归类，再划分职责目录。** 例如 Combat 下的 Attack、Damage、Target、Settlement；子领域不要求拥有独立 System，仍可以由 CombatSystem 统一组织所属业务。
3. 子领域划分依据真实业务语义、归属及稳定架构边界，不规定固定文件数量、目录深度或复杂度作为拆分门槛；已确定的子领域即使目前文件较少也可以独立建立。
4. 跨多个子领域共同使用的类型按实际职责放在其共同所属领域或明确共享范围；不得在各子领域重复创建同一职责实现，也不得为了归类擅自提升内部能力的访问权限。子领域之间的依赖仍须符合所属 System 和内部职责类型的专门约定。
5. 目录层级用于稳定组织，不机械复刻每一次方法调用、实例持有或数据流关系。已有明确的职责目录不因某次调用链变化自动迁移。

组织示例：

```text
Game/Combat/
├─ CombatSystem.cs
├─ Attack/
│  ├─ Processing/
│  │  └─ Logic/
│  │     └─ AttackLogic.cs
│  └─ Committers/
│     └─ AttackResultCommitter.cs
├─ Damage/
│  └─ Processing/
│     ├─ Capabilities/
│     │  └─ Calculators/
│     │     └─ DamageCalculator.cs
│     └─ Implementations/
│        └─ Formulas/
│           └─ CriticalDamageFormula.cs
├─ Target/
│  └─ Processing/
│     └─ Resolvers/
│        └─ CombatTargetResolver.cs
├─ Settlement/
├─ Data/
└─ Events/
```

## 职责类型与目录归属

以下为缺少项目专用约定时统一采用的职责目录名称及归属。表中相对路径均指实际所属领域或子领域，而不是要求在 Scripts 根目录分别建立相应目录。

| 职责类型 | 默认目录或归属 |
|---|---|
| Manager | 对应管理功能目录 |
| 框架 System | Framework 对应功能目录根部 |
| 业务 System | Game 对应领域目录根部 |
| 业务对象 | Objects/；独立对象领域的主类型可在所属领域根部 |
| Controller | 所属业务 Presentation/ 或所属场景目录 |
| Module | 所属对象或具体能力目录 |
| Initializer | 所属装配范围；领域内部可采用 Initializers/ |
| Factory | Factories/ |
| Processor | Processors/ |
| Logic | Processing/Logic/ |
| Resolver | Processing/Resolvers/ |
| Rule / Rules / RuleSet | Processing/Capabilities/Rules/ |
| Calculator | Processing/Capabilities/Calculators/ |
| Evaluator | Processing/Capabilities/Evaluators/ |
| Matcher | Processing/Capabilities/Matchers/ |
| Selector | Processing/Capabilities/Selectors/ |
| Mapper | Processing/Capabilities/Mappers/；专用表现或技术映射归入实际职责范围 |
| Normalizer | Processing/Capabilities/Normalizers/ |
| Operation | Processing/Implementations/Operations/ |
| Formula | Processing/Implementations/Formulas/ |
| Algorithm | Processing/Implementations/Algorithms/ |
| Committer | Committers/ |
| Provider | Providers/ |
| Registry | Registries/ 或具体框架注册职责所在范围 |
| Config | Data/Configs/ |
| RuntimeData | Data/Runtime/ |
| Snapshot | Data/Snapshots/ |
| Request | Data/Requests/ |
| Result / CommitResult / RequestResult | Data/Results/ |
| InputData | Data/Inputs/ |
| SaveData | Data/Save/ |
| DisplayData | 所属 Presentation/ 或其明确数据目录 |
| Event | 所属事实领域的 Events/ |
| DataStorage | Persistence/ |
| Interface | 所表达能力所在目录，或明确的 Contracts/ |
| View / Presenter / ViewModel / Model | 所属 Presentation/ 及已确认的表现分层 |
| Service | 所属技术能力目录 |
| Adapter | 所属适配能力目录，可采用 Adapters/ |
| Utility | 对应公共技术领域，如 Framework/Utilities/ |

1. 同一种职责使用统一术语及默认目录名称；不在缺少专用约定的新业务中随意混用多套同义目录结构。已有稳定组织继续沿用。
2. 实际所有者及技术契约优先于机械后缀分类。框架内部实现、对象专属能力及表现或持久化专用数据等，按其明确范围组织，不为了套用业务 System 模板打散原有功能边界。
3. 表中目录仅表达已存在或已确定的职责，不要求为配齐目录引入同名类型、接口、单例、程序集、状态或业务机制。 设计模式不自动产生新的职责后缀或平行目录体系；新增独立职责及目录须按[技术实现方案设计与评估规范](../Design/TechnicalDesign.md)明确语义、归属并取得确认。

## 纯业务处理及状态提交目录

1. **纯业务处理统一默认归入 Processing/。** Processing 是目录归类名称，不是 Processor 的别名，也不是新增职责类型或固定必经处理层。
2. Processing 下默认区分 Logic/、Resolvers/、Capabilities/、Implementations/，分别对应纯业务流程组织、综合解析、具体职责能力、基础处理实现。具体职责能力及基础处理实现继续按职责使用统一的子目录名称，不因当前业务简单或某个目录只有一个文件而擅自取消已确定的层级。
3. **Processor 与 Committer 独立于纯处理目录。** Processor 默认位于 Processors/，承担工作单元处理生命周期；Committer 默认位于 Committers/，承担实际状态提交。Provider、Registry、DataStorage 等有明确数据访问、注册及持久化职责的类型不默认放入 Processing/。
4. 纯处理目录中的代码仍须遵守显式输入输出、纯处理、无隐式可变全局状态及禁止反向依赖等硬性约束。目录名称不赋予合法访问权限，不将包含外部副作用的实现因物理位置而视为纯处理。
5. 同一业务可以直接使用符合职责及依赖规则的下层能力，不强制请求经过全部目录所表达的层级；状态提交、配置及资源访问、事实事件通知仍为各自明确的独立职责路径。

完整业务领域候选目录模板：

```text
Domain/
├─ XxxSystem.cs
├─ Objects/
├─ Factories/
├─ Initializers/
├─ Processors/
├─ Processing/
│  ├─ Logic/
│  ├─ Resolvers/
│  ├─ Capabilities/
│  │  ├─ Rules/
│  │  ├─ Calculators/
│  │  ├─ Evaluators/
│  │  ├─ Matchers/
│  │  ├─ Selectors/
│  │  ├─ Mappers/
│  │  └─ Normalizers/
│  └─ Implementations/
│     ├─ Operations/
│     ├─ Formulas/
│     └─ Algorithms/
├─ Committers/
├─ Providers/
├─ Registries/
├─ Data/
│  ├─ Configs/
│  ├─ Runtime/
│  ├─ Snapshots/
│  ├─ Requests/
│  ├─ Results/
│  ├─ Inputs/
│  └─ Save/
├─ Events/
├─ Persistence/
├─ Contracts/
└─ Presentation/
```

Processing、Capabilities、Implementations 等为统一的目录术语，不确立新的通用职责后缀。上述模板不要求所有领域创建全部目录；已确定职责的目录可提前建立，其他候选目录按实际需求创建。已有稳定的纯处理组织及职责目录不得未经确认迁移。

## 公共代码与共享业务能力

1. **区分通用公共技术、项目内共享业务能力及领域内部实现。** 通用且不依赖具体游戏玩法的框架及技术代码默认归入 Framework 的对应技术领域；当前游戏内具有独立跨领域共享职责的能力可以归入 Game/Shared/ 下的明确功能目录；具有自然业务所有者的代码仍归入其所属领域。
2. **多个调用方不等于公共所有权。** 某一业务的规则、公式、内部处理类型或数据契约被其他领域使用，不自动成为 Framework 或 Game/Shared 的成员。外部需要使用业务能力时，应先通过所属 System 的公开能力或已明确的合法共享契约协作。
3. 确实具有独立共享语义和稳定调用契约的纯计算、数据契约、技术能力，可以按其真实职责独立组织，不以当前调用方数量为必要条件；不得仅为方便访问、减少路径或规避调用限制而提升为公共能力。
4. Game/Shared 不得成为未知归属代码的临时堆放目录，不依赖某个业务 System 的私有实现，不绕过所属系统的公开入口、权威状态及提交边界。新建或迁移公共共享能力仍须遵守公共基础模块报备及确认规则。
5. 不默认新增 Common、Misc、Helpers、Other 等缺乏明确职责范围的兜底目录，也不按相同后缀建立全局 Interfaces、Events、Modules 等目录打散已有明确所有者。已有稳定技术契约继续沿用。

## Interface、Event 及数据类型归属

1. **Interface 按其表达的能力及实际所有者归类。** 默认与对应职责实现放在同一功能或职责目录，例如 `Inventory/Committers/IInventoryTransferCommitter.cs` 与 `InventoryTransferCommitter.cs` 归入同一职责目录。具有独立公开或跨领域协作契约时，可以使用所属领域的 Contracts/ 或已明确的共享契约目录；不强制每个实现配套接口。
2. **Event 归入实际事实所属领域。** 例如 MissionCompletedEvent 归入 Mission/Events/，InventoryChangedEvent 归入 Inventory/Events/。Framework/Event/ 负责通用事件机制及自身技术契约，不集中保存所有游戏业务事件。不同订阅者的数量及位置不改变事件归属，发布权限与数据有效期继续遵守[EventSystem 与事件协作规范](../Architecture/EventSystem.md)。
3. RuntimeData、Snapshot、Request、Result、CommitResult、RequestResult、InputData、SaveData 和 Config 等按实际数据语义及所属领域组织。缺少专用约定时，默认在 Data/ 下按照本节确定的用途目录细分；专用于某个对象、能力、子领域、框架或外部接口的数据优先保持其自然归属及稳定契约。
4. DisplayData、专用展示映射和仅用于表现的局部状态应归入所属 Presentation/ 或其明确数据目录，不与权威业务状态混为同一维护范围；持久化数据的代码归属不改变实际存储位置及维护来源。
5. 手写 Config 类型属于其实际配置语义所在的业务或框架领域，默认使用 Data/Configs/。ScriptableObject 的代码类型定义与 .asset 资产分别组织；生成的配置类型按生成工具维护边界存放，不要求按手写职责目录拆散。
6. 数据类型、接口和事件的文件位置不决定其 public、internal、序列化能力、实际数据所有权或调用权限，继续遵守各自专门规范。不因建立 Contracts、Data 或 Events 目录而默认公开所有内部类型。

## 对象、Module、Controller 及表现代码归属

1. **业务对象和游戏对象按照其实际身份归类。** 领域内的对象默认归入 Objects/；具有独立身份及稳定能力组成的对象，可以建立独立对象领域，以对象本身命名的主要类型位于该领域根部，不因可挂载性而更改对象命名或增设 System。
2. Module 优先归入所属对象或具体能力范围，例如 Character/Movement/、Character/Input/、Character/Animation/。当同一个对象具有多项 Module 能力时，按能力组织内部实现；不将全部 Module 集中在项目级 Modules 目录，也不因目录划分改变 Module 的所属关系、装配、事件及租用生命周期。
3. Controller 默认归入所属业务的 Presentation/ 或实际场景流程目录；跨领域的场景协调可以在 Game/Scenes/ 对应场景下组织。Controller 的文件归属不改变场景级单例及合法业务调用约束。
4. 具体业务 UI、展示数据、表现映射、View、Presenter、ViewModel 等归入所属业务表现范围；不因代码承担表现职责就自动归入通用 Framework/UI/。通用 UI 框架或通用管理功能可以归入对应框架领域，项目专用 UI 管理可以归入 Game/Management/UI/。
5. Initializer、Factory 及其他装配、创建能力按实际装配范围或对象所有者组织。例如项目级装配放入 Bootstrap/，场景专属装配位于所属场景，领域内部创建能力归入 Factories/。不得因文件位于某目录就要求改变已有装配关系、Prefab、场景、UI 层级或生命周期。
6. 目录规范不授权新增 UI 分层、将对象改为 View 或改变 Controller、Module 的职责。需要拆分独立表现层或迁移现有表现实现时，继续遵守既有 UI 及架构变更授权规范。

组织示例：

```text
Game/
├─ Character/
│  ├─ Character.cs
│  ├─ Movement/
│  │  ├─ CharacterMovementModule.cs
│  │  └─ Data/
│  │     └─ Configs/
│  │        └─ CharacterMovementConfig.cs
│  ├─ Input/
│  │  └─ CharacterInputModule.cs
│  └─ Animation/
│     └─ CharacterAnimationModule.cs
├─ Inventory/
│  ├─ InventorySystem.cs
│  └─ Presentation/
│     └─ InventoryPanelController.cs
└─ Scenes/
   └─ Battle/
      ├─ BattleSceneController.cs
      └─ BattleSceneInitializer.cs
```

## 配置资产及原始配置文件目录

1. **代码、配置资产、原始配置来源和生成产物分别组织。** 用户明确指定的配置载体、存放位置及已有稳定配置或导表契约优先。以下位置作为缺少专用约定时的默认方案，不构成主动迁移授权。

| 文件类别 | 默认存放位置 |
|---|---|
| SO 配置资产（.asset） | Assets/Resources/Configs/ScriptableObjects/所属领域/ |
| 随游戏发布的二进制配置（如 .bytes） | Assets/Resources/Configs/Binary/所属领域/ |
| 随游戏发布的 JSON 配置（.json） | Assets/Resources/Configs/Json/所属领域/ |
| 原始 Excel 配置表（如 .xlsx） | 项目根目录 Excel/所属领域/ |
| 手写 Config 类型定义（.cs） | 所属领域的 Data/Configs/ |
| 自动生成的业务配置代码 | Assets/Scripts/Game/Generated/Configs/（沿用实际生成目标） |
| 运行时下载数据及存档 | Application.persistentDataPath 或项目已有存储机制 |

2. **配置资产默认按载体形式优先、所属业务领域其次组织。** SO、二进制和 JSON 分别位于 Resources/Configs/ 下的对应目录，内部使用与代码一致的领域或框架功能术语；Excel 位于与 Assets 同级的 Excel/ 目录，内部按实际表的领域组织，默认不作为 Unity 运行时资产直接导入。共用表、Schema 及其他工具输入按照真实维护来源及工具契约组织，不为形式拆散已有生成体系。
3. 手写 ScriptableObject 类型仍位于所属代码领域，实际 SO 配置资产位于默认配置资产目录。非配置用途的 ScriptableObject、JSON、二进制文件、工具输出、项目设置及玩家存档，不因扩展名或技术类型相同而强制进入 Resources/Configs/。
4. 生成的二进制、JSON 及业务配置代码按照实际工具输出契约及默认目录组织。人工维护与自动生成的文件存在批量覆盖或清理风险时，须隔离生成目标与手工维护范围，可以在对应载体或领域下使用明确的 Manual、Generated 等目录；不强制提前建立无实际维护需求的目录。直接维护的 JSON 不因其扩展名或所在载体目录而被视为生成产物。
5. **配置目录不决定数据权威来源、覆盖关系、加载方式及生命周期。** ConfigManager 管理来源及装配，ConfigSystem 组织有效配置，合法业务调用方按既有契约访问。存放在 Resources 的 SO 仍可通过 Inspector 显式装配，不强制改为动态加载；需要主动取得资源时默认使用 AssetSystem 或其合法访问边界，具体遵循[配置数据管理规范](../Runtime/Configuration.md)和[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。
6. 同一配置含义可以存在原始 Excel、生成二进制或 JSON、生成代码等不同表示；须明确真实维护来源、生成关系及当前有效配置，不得仅因多个文件同时存在就认定多个独立权威来源，不擅自制定文件形式之间的优先级或自动合并策略。
7. 仅将具有明确随游戏发布需求且适合本地 Resources 机制的配置放入默认目录；已有 Addressables、StreamingAssets、远端配置及其他专用加载契约继续沿用。不得因目录规范要求所有配置和其他资产进入 Resources，或把运行时持久化文件写回 Unity 项目资产。

配置组织参考：

```text
UnityProject/
├─ Assets/
│  ├─ Scripts/
│  │  └─ Game/
│  │     ├─ Combat/
│  │     │  └─ Data/
│  │     │     └─ Configs/
│  │     │        └─ CombatConfig.cs
│  │     └─ Generated/
│  │        └─ Configs/
│  └─ Resources/
│     └─ Configs/
│        ├─ ScriptableObjects/
│        │  └─ Combat/
│        │     └─ CombatConfig.asset
│        ├─ Binary/
│        │  └─ Combat/
│        │     └─ Enemy.bytes
│        └─ Json/
│           └─ Mission/
│              └─ MissionReward.json
└─ Excel/
   ├─ Combat/
   │  └─ Enemy.xlsx
   └─ Mission/
      └─ MissionReward.xlsx
```

示例不规定每个配置必须同时具有 Excel、SO、JSON、二进制及对应生成代码，不要求新增导表框架、配置解析器或配置加载方式。已有配置文件及加载路径须受变更授权保护。

## 文件拆分及主要类型约定

1. **文件名与主要类型名一致，独立职责类型默认独立文件。** 业务对象、职责实现、具有稳定能力契约的接口、独立事件、配置、请求、结果及其他具有独立语义的数据类型，应按照实际职责组织独立文件。不得仅因代码行数较少或只有一个调用方而合并已确定的独立类型。
2. 仅服务于主要类型、没有独立职责或稳定外部协作契约的私有或内部辅助类型、局部枚举及嵌套类型，可以保留在所属文件。不得为实现一文件一类型而强制拆分所有局部细节，也不得借辅助类型的名义在 System 文件中堆积独立的 Logic、Committer 等职责实现。
3. Unity 可挂载脚本及 ScriptableObject 类型按实际 Unity 脚本导入、类型识别及序列化契约组织文件。具有不同独立主要类型的脚本不为减少文件数量而混合，已有合法框架和生成契约不机械改写。
4. Interface 可以与实现位于同一职责目录，但独立的稳定接口默认使用自己的文件。目录归属及文件数量不构成增加接口、数据类型或公开成员的依据。
5. partial class 只用于明确的生成代码、现有框架或实际类型组织需求，按照真实维护契约处理多个文件，不作为普通职责类型拆分的默认替代方式；不因文件拆分引入重复权威状态或破坏类型命名、序列化及生成约定。
6. 第三方及自动生成代码遵循其实际文件结构、主要类型及命名契约；已有代码不为统一文件形式自动拆分、合并或移动。

## 目录命名与术语统一

1. **新建代码目录统一使用英文 PascalCase 及明确业务或职责术语。** 领域目录优先使用与所属对象、系统和业务概念一致的名称，例如 Inventory、Combat、WorldGeneration；职责目录使用本节确定的名称，如 Processing、Capabilities、Implementations、Calculators、Formulas、Committers、Data、Presentation。
2. 保持同一概念在目录、文件、类型及配置中的稳定术语，不在缺少专用约定的新业务中交替使用 Calculator、Calculators、Calculation 等同义归类。固定目录名称的单复数及拼写按本节示例和归属表执行，不要求所有目录机械使用单数或复数。
3. 不默认使用数字前缀表示架构层级或排序，例如 01_Systems、02_Logic；用户明确指定排序规则或已有稳定命名契约时继续遵循。不得使用含义宽泛的 Other、Temp、New 等名称掩盖正式业务归属。
4. Unity 特殊目录、包名、第三方插件目录及生成目标的固定拼写和大小写按真实技术契约处理。代码目录英文命名不改变 UI GameObject 中文命名、中文枚举及已有资产命名规范；Addressables 相关无方括号命名规则继续有效。
5. 发现目录术语、类型名称或已有路径冲突时，应检查实际调用、资源加载及序列化引用，先报告问题和方案再修改，不为统一大小写或术语执行无关迁移。

## 架构先行与目录创建

1. **目录组织同样遵循架构先行。** 已经确定的公共基础框架、业务领域、子领域和稳定职责层级，可以在代码规模尚小时提前规划及建立相应目录，不以当前文件数量、调用方数量或复杂度为必要条件。
2. 新项目默认的 Config、Asset、Pool、Log 等基础框架目录可按已确认的架构方案提前建立；具体 Manager、System 及内部能力的引入仍遵循原有专门约定，不因目录建立自动配齐类型或实现运行机制。
3. 具有明确职责的目录即使目前只有一个文件也允许存在；不得仅以减少目录层级、文件数量或点击次数为由，擅自合并、移动或取消稳定职责边界。
4. 已确定的目录边界可以先建立，尚不存在的业务及尚未确定的职责目录不机械生成，不以空目录数量衡量架构是否完整。目录模板是统一组织参考，不是每个业务必须配齐的文件夹清单。
5. 新建及调整目录仍须处于用户已确认的任务范围内；不为准备未来需求而自行创建无归属的代码、配置、程序集、资源及运行机制。已有适用目录时优先复用，不建立重复结构。

## Unity 特殊目录及程序集关系

1. **普通目录与具有引擎技术语义的目录必须区分。** 使用 Resources、Editor、StreamingAssets、Plugins、Packages 等位置时，必须检查当前 Unity 版本、项目设置、包及程序集的实际行为，不将其视为普通归类名称或仅靠目录名称推定具体技术效果。

| 目录或位置 | 技术语义及注意事项 |
|---|---|
| Resources | 本地资源加载路径及构建包含范围，遵守 Resources 资源管理规范 |
| Editor | 编辑器专用代码组织；检查实际程序集及平台配置，不仅凭目录名判断 |
| StreamingAssets | 原始文件随构建交付及对应平台的读取方式 |
| Plugins | 插件导入、平台及实际编译契约 |
| Packages | 包来源、包管理及更新契约 |
| Library | Unity 生成的本地导入与缓存数据，不作为手写代码或权威配置维护位置 |
| Generated | 本文件中的普通组织目录，按实际生成工具契约管理，不是 Unity 特殊目录 |

2. Editor 工具代码按实际功能归属和编辑器程序集契约组织。存在 asmdef、asmref 时，应检查真实编译归属及平台设置；不得仅凭位于 Editor 目录就认定代码一定不进入运行时程序集。运行时代码不得依赖只可在编辑器使用的实现。
3. 测试代码按项目已有测试组织、程序集及平台契约存放；测试目录名不自动提供编译隔离或内部访问权限。不得为目录组织而擅自增加测试框架、友元程序集或运行验证机制。
4. **文件目录、命名空间、程序集及架构职责是不同维度。** 普通目录不提供 C# 名称隔离、internal 可见性隔离或业务访问授权；同程序集同命名空间的同名类型仍可能发生冲突。Internal/、Framework/ 或 Processing/ 等目录不自动改变语言权限或实际职责。
5. 业务目录和职责目录不强制与 namespace、asmdef、asmref 一一对应。普通游戏业务代码继续遵循原有默认不新增命名空间约定；程序集组织及隔离沿用[类的访问权限与公开接口规范](AccessControl.md)。不得为统一目录擅自新增命名空间、拆分程序集、修改引用或放宽类型访问权限。
6. 涉及移动特殊目录、改变程序集归属、资源打包及加载范围时，应按照真实技术契约评估影响并取得确认；不得借文件移动擅自改变资源加载机制、平台、项目设置或第三方包。

## 第三方代码及生成产物保护

1. **按真实维护来源区分自研代码、第三方代码和自动生成代码。** 项目自行维护的框架、业务和适配代码按其实际职责组织；第三方插件、包及外部框架保留原有目录、名称、接口、许可证及更新契约，不为统一目录将外部类型拆散至项目职责目录。
2. 项目自行维护的 Adapter、Service 等第三方对接实现归入项目自己的对应技术或领域目录，与第三方源码的维护边界分别确定。不得为适配目录形式修改插件源码或生成无实际接口差异的包装。
3. 自动生成的 C#、配置数据及其他产物使用既有生成目标和维护流程，缺少专用约定时采用本节默认生成目录。实际输出范围、清理规则及生成工具契约须明确，不将手写代码放入可能被自动清理的范围。
4. 不得仅凭文件扩展名、Generated 目录或文件名称推断其真实维护来源。需要变更生成代码或数据时，优先按已确认的原始配置、Schema、模板或工具配置维护真实来源；生成产物默认不直接手工修改，特殊情况继续遵守[配置数据管理规范](../Runtime/Configuration.md)。
5. 新增或调整导表工具、生成目标、原始 Excel 位置、生成目录覆盖范围或外部插件目录，必须说明实际来源、调用、依赖和迁移影响并取得确认，不得使工具覆盖或删除非所属的手工维护文件。
6. 用户需要手动执行的导表、生成、Unity 导入、引用配置及验证，在交付时明确列出；不以整理目录或生成代码为由主动执行编译、编译错误检查或运行验证。

## 文件移动、重命名及删除保护

1. **目录规范不构成自动整理已有项目的授权。** 移动、重命名、拆分、合并、删除已有文件或目录前，必须说明原位置、目标位置、职责归属、原因、受影响引用及风险，按[AI 修改与交付规范](../Governance/AgentWorkflow.md)中的公共基础模块报备及确认要求执行。已确认的具体迁移范围无需重复请示，不得扩大授权。 移动及重命名前同时检查原位置和目标位置的有效规则及专用约定，发现规则冲突时遵循[规则优先级与冲突处理规范](../Governance/RulePriority.md)。
2. 移动或重命名 Unity 资产、脚本及已存在的 Unity 资产目录时，必须一并保留对应原 .meta 文件及 GUID；不得通过删除后重建、重新生成 .meta 或复用其他资产 GUID 代替迁移。删除资产时应按确认的范围处理对应 .meta 和依赖，不保留错误引用或误删仍在使用的文件。
3. **保留 GUID 不等于所有契约不受影响。** 必须同时检查实际序列化类型、场景、Prefab、ScriptableObject、UnityEvent、配置数据、引用绑定、资源路径、Addressables 地址及外部工具依赖。仅移动文件不自动授权改名类型、替换资源、修改字段、重建 Prefab 或自动修复绑定。
4. 涉及目录迁移时，应检查 .asmdef、.asmref、Editor 及 Plugins 等实际编译和平台归属、命名空间和访问权限、包依赖及生成文件。不得擅自通过增加程序集引用、扩大可见性或改变业务依赖来解决文件迁移带来的问题。
5. Resources 路径、Addressables 地址、StreamingAssets 路径、配置表及生成工具输入输出位置、编辑器工具和其他字符串路径可能独立于 GUID。按实际引用方检查并在批准的范围内同步调整相应契约；不自行引入路径兼容层、自动扫描或替代资源兜底。
6. 删除已有类型、脚本、配置、资产或目录前，应识别实际调用、引用、生成及维护关系，确认不会在授权之外移除其他业务或共享资源。无法静态确认的引用、反射、外部工具或动态加载风险必须明确说明，不仅因未找到直接代码引用就判定可安全删除。
7. 迁移应按项目已有工具及版本管理方式组织，保留无关文件和变更，不擅自还原用户已有修改。手动编辑 Unity 序列化文件、需要 Unity 导入或资产迁移验证时，继续遵守[Unity 项目约束](../Unity/ProjectConstraints.md)。 所有文件修改均须保护用户及其他 Agent 的现有成果，具体遵循[AI 修改与交付规范](../Governance/AgentWorkflow.md)中的工作区保护、并行修改及 Git 操作约定。
8. 交付时应列出实际新增、移动、重命名、删除的文件及相关引用变更，说明 .meta、GUID、程序集及资源地址的静态检查情况和需用户手动验证的事项。不得宣称未经验证的运行结果，不得主动编译、检查编译错误或运行验证。

## Agent 开发前检查与默认设计方案

1. 新增、修改或整理文件前，应按当前任务的相关范围识别：
   - 用户明确指定的位置、已有代码根目录、原位置和目标位置的相关上层及局部有效规则、稳定术语及真实维护来源；跨领域任务的相关公共协作契约。
   - 实际职责所有者、Framework 与 Game 的归属、所属业务领域、子领域、对象、场景或装配范围。
   - 已存在的相同职责类型、适用目录及合法共享能力，避免重复建立实现和目录。
   - 新增文件的职责后缀、独立类型、正式契约及目录分层，实际调用方和依赖方向。
   - 配置是原始来源、手工维护资产、生成代码、运行时配置还是其他持久化数据；实际生成范围及资源访问契约。
   - Unity 特殊目录、程序集及命名空间归属、序列化引用、.meta、GUID、资源路径、Addressables 地址及外部工具影响。
2. 任务方案中应明确新增或调整文件的职责及目标路径、是否复用已有目录、需要迁移的实际原因与影响。已有约定明确且任务范围已确认时，按实际需求组织，不要求每次输出完整目录审计；发现真实归属、依赖、资源或生成契约冲突时，先报告并取得确认。
3. 交付时说明实际文件及目录变更、维护来源、迁移及相关调用或资源影响，并列出需要用户手动完成的拖拽、配置、导表、生成、Unity 导入及验证事项。静态审查只能确认相应文本及依赖信息，不得主动编译、检查编译错误或运行验证。

默认设计参考：

| 设计维度 | 默认推荐或约束 |
|---|---|
| 规则优先级 | 用户明确指定的合法位置优先，其次有效专用约定及已有稳定契约，缺少约定时使用本节默认结构；实际规则、技术及职责冲突按[规则优先级与冲突处理规范](../Governance/RulePriority.md)处理 |
| 项目代码根目录 | Assets/Scripts/ 下区分 Framework 与 Game |
| 核心划分依据 | 实际职责所有者，业务主要以 System 所代表的领域组织 |
| 公共框架 | 按完整框架功能组织，不依赖具体游戏业务 |
| Manager / System | 所属功能或领域组织，不默认全局按后缀集中 |
| 业务 System | 所属领域根目录 |
| 业务领域内部 | 按职责组织；存在明确子领域时允许子领域优先 |
| 纯业务处理 | Processing/Logic、Resolvers、Capabilities、Implementations |
| 具体职责能力 | Processing/Capabilities/ 对应职责目录 |
| 基础处理实现 | Processing/Implementations/ 对应职责目录 |
| Processor / Committer | Processors/、Committers/ 独立于纯处理目录 |
| 对象及 Module | 实际对象或能力归属 |
| Controller 及表现 | 所属业务 Presentation/ 或场景范围 |
| Interface / Event | 所属能力、契约或事实领域 |
| 公共与共享能力 | Framework 对应功能；独立共享游戏业务可归入 Game/Shared/ |
| 数据类型 | 所属领域 Data/ 按用途细分，专用数据按实际职责及技术契约组织 |
| SO 配置资产 | Assets/Resources/Configs/ScriptableObjects/ |
| 二进制配置 | Assets/Resources/Configs/Binary/ |
| JSON 配置 | Assets/Resources/Configs/Json/ |
| 原始 Excel 配置表 | 项目根目录 Excel/ |
| 配置资产目录 | 载体优先、所属领域其次；不决定权威来源及加载方式 |
| 手写 Config 类型 | 所属领域 Data/Configs/ |
| 生成配置代码 | Assets/Scripts/Game/Generated/Configs/，优先沿用实际生成目标 |
| 独立类型及稳定契约 | 独立文件；局部辅助类型可保留在所属文件 |
| 目录命名 | 英文 PascalCase，统一领域及职责术语，保留特殊目录及已有契约 |
| 目录先行 | 允许提前建立确定的稳定目录；不机械创建所有候选目录 |
| 命名空间及程序集 | 不与目录强制对应，保持原有访问及程序集规范 |
| 第三方及生成代码 | 按实际维护及更新契约保护 |
| 文件迁移及删除 | 按具体影响取得确认，保留需要保持的 .meta、GUID 及引用契约 |

上述默认方案不要求项目具备所有示例功能，不以目录数量或层级深度判断架构完整性，不授权自动迁移已有文件、生成产物、配置资产或修改加载机制。目录用于定位职责、维护来源和合法协作范围，始终受原有职责划分、依赖方向、访问权限、状态所有权、资源生命周期及修改授权规则约束。
