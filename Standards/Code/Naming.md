---
id: code.naming
title: 代码命名规范
category: Code
scope: General
---

# 代码命名规范

## 规范定位与适用范围

定义 C# 命名格式、语义命名、对象命名及职责后缀索引。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

## 相关规范

### 基础依赖
- [核心职责与单例规范](../Architecture/CoreRoles.md)
- [业务处理职责与基础处理实现规范](../Architecture/ProcessingRoles.md)

### 条件协作
- [支持性职责规范](../Architecture/SupportingRoles.md)
- [EventSystem 与事件协作规范](../Architecture/EventSystem.md)
- [数据类型与传递契约规范](../Architecture/DataContracts.md)
- [UI 表现层架构规范](../Architecture/UIArchitecture.md)

## 适用范围

1. 本节新增的语义命名规则默认用于任务中新建的文件及其中的新类型和新成员，不要求为了统一形式主动批量修改已有文件、已有类型或已有成员的名称。
2. 在已有文件中继续开发时，应优先遵循该文件所属模块的现有稳定术语和代码风格，不得仅因本规范存在而扩大任务范围进行无关改名。 历史命名差异与真实职责冲突应按[规则优先级与冲突处理规范](../Governance/RulePriority.md)分别判断，新建类型仍遵守实际适用的职责及命名规则，不扩大历史违规。
3. 如果 Agent 发现已有名称职责明显不符、容易误导调用方，或者改名能够显著改善架构表达，必须先向用户报备原名称、建议名称、涉及文件、引用影响、Unity 序列化影响和修改理由，取得明确确认后才能修改。
4. 用户明确批准的命名重构任务不受第一条限制，但只能在已批准范围内执行。
5. 项目现有框架、第三方插件和稳定公共接口已经形成固定命名契约时，应保持原有名称，不得为了套用本规范进行无关改名。

## 命名空间使用范围

1. 普通游戏业务代码默认不新增命名空间，只有用户明确要求时才添加。不得仅因目录分层、模块划分或通用命名规范而自动为业务脚本添加命名空间。
2. 框架、公共基础包代码遵循所属框架已有的命名空间约定；不得将普通游戏业务代码自动视为框架代码。
3. 已经声明命名空间的脚本沿用现有结构，不得为了统一形式主动新增、删除或修改其命名空间。确需调整时，必须先说明涉及文件、引用影响和 Unity 序列化影响，并取得用户确认。
4. 命名空间的英文命名、PascalCase 和层级组织规范，仅约束需要使用命名空间的代码，不构成自动为业务脚本添加命名空间的依据。 程序集、文件目录、命名空间和业务职责边界不强制一一对应；程序集隔离遵循[类的访问权限与公开接口规范](AccessControl.md)。 领域目录及职责目录的建立不自动要求新增命名空间或程序集；文件归属及物理组织遵循[代码目录组织与文件归属规范](DirectoryStructure.md)。

## C# 基础命名格式

1. 命名空间、类、结构体、接口、方法、属性、事件和委托使用英文命名，并遵循项目统一的 C# 命名方式。
2. 需要使用命名空间时，使用英文命名和 PascalCase，并按照“项目或框架 + 业务模块 + 子领域”的顺序组织，例如 `PregnancyFramework.EventSystem`、`Game.Mission.Runtime`。
3. 类、结构体、方法、属性、事件和委托使用 PascalCase，例如 `EventCenter`、`EventArg`、`AddListener`、`CurrentMission`。
4. 接口使用 `I` + PascalCase，并表达调用方能够依赖的能力或契约，例如 `IListener`、`IMissionStateProvider`。 接口的引入及可见性按实际能力与稳定调用契约确定，不机械为每个实现类型建立配套接口，具体遵循[类的访问权限与公开接口规范](AccessControl.md)。
5. 方法参数、局部变量以及私有或受保护字段使用 camelCase，例如 `eventName`、`listenerId`、`eventTable`。
6. Inspector 序列化字段默认声明为私有字段并使用 camelCase；除非项目现有架构明确要求，不得为了 Inspector 显示而暴露公共字段。 Inspector 可见性与 C# 访问权限分别确定，字段、属性及序列化约束遵循[类的访问权限与公开接口规范](AccessControl.md)。
7. 常量使用英文 `UPPER_SNAKE_CASE`，例如 `INVALID_LISTENER_ID`、`DEFAULT_BUCKET_CAPACITY`。
8. 方法名称使用动词或动宾结构并表达实际行为，例如 `RegisterMission`、`ResolveTarget`、`RefreshMissionPanel`；不得使用 `Do`、`HandleData`、`ExecuteLogic` 等缺少业务语义的名称。
9. 布尔字段、属性和方法应体现判断语义，优先使用 `is`、`has`、`can`、`should` 或 `Try`，例如 `isInitialized`、`hasReward`、`CanAcceptMission`、`TryGetMission`。
10. 集合名称使用复数或明确映射语义，例如 `missions`、`activeMissionIds`、`missionById`；不得脱离业务上下文只命名为 `list`、`data` 或 `items`。
11. 异步方法只有在确实返回异步操作时才使用 `Async` 后缀，例如 `LoadMissionDataAsync`；同步方法不得添加 `Async`。
12. 泛型参数使用 `T` 或 `T` + 具体语义，例如 `TItem`、`TResult`、`TConfig`。
13. 事件名称描述已经发生的事实，例如 `MissionCompleted`、`SceneLoadStarted`；响应事件的方法使用 `Handle` + 事件语义，例如 `HandleMissionCompleted`。 具有独立数据契约的事件类型默认使用 `XxxEvent` 命名；已有事件框架和第三方接口的稳定命名及参数契约继续沿用，具体遵循[EventSystem 与事件协作规范](../Architecture/EventSystem.md)。
14. `OnXxx` 保留给类型内部触发对应事件的受保护方法，或者 Unity、框架规定的生命周期入口，不得同时混用多套事件命名方式。 事件响应及发布接口的命名不得成为擅自重命名已有事件框架的理由。
15. 枚举类型和枚举成员允许使用中文命名，但必须语义明确并在同一个枚举中保持语言一致；中文主要用于项目内部枚举、注释和 Inspector 显示文本。
16. 与第三方 API、网络协议、存档格式、代码生成工具或跨程序集公共契约对接的枚举，应遵循对应接口的既有语言和序列化约定。
17. 缩写仅允许使用项目已有且含义明确的统一写法，例如 `UI`、`ID`、`AI`；同一个缩写不得混用不同大小写形式。
18. 文件名必须与主要类型名一致。具有独立职责的类型应放入独立文件；仅服务于主要类型的私有或内部辅助类型可以保留在同一文件中。 独立接口、事件、数据契约、文件拆分及第三方和生成代码的具体归属遵循[代码目录组织与文件归属规范](DirectoryStructure.md)。
19. 移动或重命名 Unity 文件时，必须同时移动原 `.meta` 文件并保留其 GUID；涉及序列化类型时必须同步检查场景、Prefab、ScriptableObject 和配置资源引用。 移动或重命名 Unity 资产目录时同样须保留对应 .meta 与 GUID，并检查实际程序集、特殊目录、Resources 路径、Addressables 地址、生成流程和其他路径依赖；具体遵循[代码目录组织与文件归属规范](DirectoryStructure.md)。

## 语义命名原则

1. 命名必须优先表达业务语义，使阅读者能够从名称判断类型或成员“属于哪个领域、处理什么对象、承担什么职责”。
2. 只有当类型不是业务对象、游戏对象或领域实体本身，而是围绕对象承担一项独立职责时，才推荐采用“领域或所有者 + 具体业务对象或动作 + 职责后缀”的结构，例如 `MissionProgressRequestProcessor`、`MissionTargetResolver`。对象本身必须优先按照“对象类命名”直接命名，不得为了套用职责结构添加后缀。 设计模式用于描述实现结构及协作关系，职责命名优先；新增模式相关通用职责后缀及其归属遵循[技术实现方案设计与评估规范](../Design/TechnicalDesign.md)，不因采用模式自动改变已有命名。
3. 跨模块公开类型、公共接口、公共数据结构、序列化类型和稳定业务入口必须在名称中体现明确领域或所有者。模块内部类型只有在命名空间、目录和上下文已经能够明确表达归属时，才允许省略重复领域前缀。
4. 名称应使用完整、清晰的英文单词。除项目已经统一采用的缩写外，不得自行创造缩写、使用单字母业务名称或为了缩短名称牺牲可读性。
5. 同一个业务概念必须在目录、文件、类型、字段、方法和配置中使用一致术语，不得交替使用多个近义词。
6. 禁止新增 `CommonHelper`、`CommonUtility`、`GameLogic`、`MainProcessor`、`DataService` 等无法体现具体所有者和职责的宽泛业务名称。
7. 名称不得使用 `New`、`Old`、`Temp`、`V2`、`Legacy` 等实现阶段或迁移状态代替正式业务语义；确需版本并存或兼容迁移时，必须先说明生命周期和移除条件并取得用户确认。

## 对象类命名

1. 表示明确业务对象、游戏对象或领域实体的类型，默认直接使用对象本身命名，例如 `Mission`、`Character`、`Enemy`、`InventoryItem`，不得机械添加 `Model`、`Object`、`Entity` 或 `View` 后缀。
2. 如果一个类型同时承担对象身份、核心状态、对象行为和规模合理的视觉表现，它应首先命名为对象本身，而不是命名为 `XxxView`。
3. 表示业务对象或领域实体时，`View` 不得用于代替对象本身。不得只创建 `XxxView` 作为某个业务对象的唯一实现，却不存在对应的 `Xxx` 对象类。经用户确认采用 UI 表现层分层后，View、Presenter、ViewModel、Model 等类型按照实际职责命名，不要求为了名称配对额外创建没有独立职责的 `Xxx` 对象类。
4. 业务对象的独立表现拆分，可以基于独立表现职责、稳定架构边界、视觉表现复杂度、复用、替换需求或项目现有架构。UI 页面的表现层拆分，还可以基于数据绑定、交互状态、异步流程、生命周期协调等明确职责及稳定分层需要，不要求仅因当前实现简单而否定拆分；具体遵循[UI 表现层架构演进](../Architecture/UIArchitecture.md)约定。
5. 独立 `XxxView` 必须从属于已经存在的 `Xxx` 对象或明确所属 Controller，负责视觉表现；UI View 还可以采集并上报界面交互事件。View 不保存权威业务状态、不实现核心业务规则，也不作为完整业务模块入口。
6. Agent 判断需要新增 `View`、`Presenter`、`ViewModel`、Model/View 分层或 MVP、MVVM、MVC 等表现层结构时，必须先说明拆分原因、类型职责、所有权、依赖方向、Prefab、场景和 Inspector 引用影响，以及保持现有结构不拆分的替代方案，并取得用户明确确认。
7. 简单 UI 页面默认由具体 `PanelController` 或页面 `Controller` 负责页面生命周期、用户输入、业务系统调用和 UI 显示更新，不强制创建 View 或 Presenter。存在明确的独立表现职责或稳定分层需要时，可以按照已确认方案建立或拆分 UI 表现层职责，不要求等到页面复杂后才分层。无论是否分层，Controller 都遵循场景级单例约定，页面生命周期的组织职责不改变其场景级作用域。
8. `Controller` 可以根据已经确定的业务状态或业务结果更新表现，但不得在 UI 层重新实现领域规则、修改权威业务状态或绕过业务 `System`。
9. 场景对象、Prefab 根对象或 GameObject 所代表的主要业务对象，即使继承 `MonoBehaviour`、使用 Unity 生命周期、连接 Inspector 引用并控制规模合理的表现，也必须优先使用对象本身命名，例如 `FusionStar`、`PregnancyMemoryCard`、`CalendarDayItem`。
10. `MonoBehaviour`、可挂载性、Inspector 引用、Prefab 归属和 Unity 生命周期都是技术实现方式，不构成添加 `Module`、`Controller`、`View` 或其他职责后缀的理由。
11. 如果一个类型是某个对象在项目中的唯一实现或主要实现，不得将其命名为 `XxxModule` 而不提供对应的 `Xxx` 对象类。
12. 判断名称时必须先回答“这个类型代表谁”，再回答“它承担什么附加职责”。能够明确回答为某个业务对象时，直接使用对象名；只有无法作为对象本身成立时，才继续选择职责后缀。

## 职责后缀命名总则

1. 职责后缀必须与实际职责一致，不得为了让名称看起来完整而随意添加后缀，也不得使用 `MissionSystemManager`、`AudioManagerController` 等含义重复的组合。
