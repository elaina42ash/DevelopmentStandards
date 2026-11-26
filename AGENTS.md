# Agent Rules

## 规范定位与适用范围

本文件是规范体系的核心治理入口，不承担全部专项技术规则的完整定义。通用规范由 GitHub `DevelopmentStandards` 仓库权威维护；当前实际 Codex Home 中的 `AGENTS.md` 与 `Standards/` 是本地部署副本。项目级及局部规则按实际作用域增量补充。

默认通用规范根目录为当前实际 `CodexHome/Standards/`。CodexHome、StandardsRoot、ProjectRoot 是逻辑路径名称，实际读取前必须核实真实路径及访问权限。规范部署、版本及同步细节见 [规范文档维护与演进规范](Standards/Governance/StandardsMaintenance.md)。

## 规范体系及文档关系

规范体系采用分层职责式组织：

1. 核心入口：本文件，声明核心约束、开发流程、规范索引及读取要求。
2. 共享专项规范：`Standards/` 下按 Governance、Architecture、Code、Design、Runtime、Unity 分类的30份文档。
3. 项目专用规范：由项目级 `AGENTS.md` 或按需建立的 `Standards/Project/` 承载。
4. 局部增量规范：位于实际适用的领域、目录或对象范围，优先使用就近 `AGENTS.md`。

项目事实、项目契约、项目专用规则及明确批准的规则例外分别判断。项目中已经存在某项实现，不自动使其成为通用规则；项目专用规则及局部规则不自动传播到其他项目或目录。

## 核心协作与授权约束

1. 任何修改项目文件、脚本、Prefab、场景、配置表或资源之前，必须先经过用户确认。 具体任务级授权、实施确认及有效范围遵循[AI 修改与交付规范](Standards/Governance/AgentWorkflow.md)。已确认任务范围内的正常实现细节无需逐文件或逐方法重复请示，不解除各专项变更授权。 规则性质、适用范围、优先关系、用户规则决策及例外，统一遵循前述[规则优先级与冲突处理规范](Standards/Governance/RulePriority.md)；明确规则决策不自动取得实施授权。
2. 修改前必须先说明修改范围、影响范围和修改原因，并给出计划或代码供用户审查。 按[AI 修改与交付规范](Standards/Governance/AgentWorkflow.md)先开展需求分析、项目调查及技术实现方案设计与评估，再提出具体修改方案；方案详细程度按实际任务规模和影响确定。 具体技术设计、外部研究、设计模式、算法、数据结构及方案评估遵循[技术实现方案设计与评估规范](Standards/Design/TechnicalDesign.md)。
3. **禁止 Agent 主动编译及执行编译、运行验证。** 在需求分析、项目调查、技术方案设计、实施、静态审查及交付的全部阶段，Agent 不得主动调用 Unity 编译、`dotnet`、`MSBuild`、Unity Console 编译诊断、语言服务器编译诊断或其他方式主动编译、检查编译错误、执行构建、运行项目或测试。正常且已授权的文件写入、资源修改等操作若被 Unity 自身被动触发自动编译，允许该附带行为；不得为取得编译结果而主动刷新、重新编译、构建或查询诊断。主动编译及实际运行验证由用户手动完成；Agent 仅分析用户提供的编译结果、错误信息及运行证据。静态审查、工具权限及被动 Unity 编译边界遵循[AI 修改与交付规范](Standards/Governance/AgentWorkflow.md)。
4. 新增或修改代码时，必须遵守代码命名规范和代码注释规范。 新增类型、成员及公开契约时，同时遵守[类的访问权限与公开接口规范](Standards/Code/AccessControl.md)。 新建文件及目录归属同时遵守[代码目录组织与文件归属规范](Standards/Code/DirectoryStructure.md)。 日志记录、失败结果及异常处理同时遵守[Unity 日志与异常处理规范](Standards/Runtime/Logging.md)。 中文注释的完整覆盖、XML 文档、关键步骤编号、职责区域及重要技术设计说明遵循[代码注释规范](Standards/Code/Comments.md)；不得借补注释扩大已确认的修改范围。
5. 不允许使用无依据的业务参数硬编码和不合理的魔法数字。应按照实际语义区分固定程序常量、设计配置、局部组件参数、运行时状态及正常数学或程序字面量。配置承载形式、存放位置及读取方式遵循用户明确要求、项目已有配置契约及[配置数据管理规范](Standards/Runtime/Configuration.md)。 配置及原始数据的默认文件目录同时遵循[代码目录组织与文件归属规范](Standards/Code/DirectoryStructure.md)，不因默认目录变更用户指定的配置形式或已有配置契约。
6. 代码结构和设计思路应优先复用项目已有适用且稳定的代码、架构及技术契约，不允许脱离现有架构擅自另起一套。已有技术实现差异与实际架构冲突应分别判断；不得因保护已有实现而扩大历史违规，也不得借落实规范擅自重构已有项目。具体遵循[规则优先级与冲突处理规范](Standards/Governance/RulePriority.md)。
7. 暴露在 Inspector 面板上的字段必须使用中文 `LabelText` 和完整 `Tooltip`，并按照[Odin Inspector 分组规范](Standards/Unity/OdinInspector.md)组织。不得为了满足形式要求而机械叠加 `FoldoutGroup`、`BoxGroup` 或其他 Group Attribute。 Inspector 的中文字段说明与源码注释应分别保留并保持语义一致，具体遵循[代码注释规范](Standards/Code/Comments.md)。
8. 修改现有公共基础模块、跨业务共享接口或全局配置前，必须提前向用户报备修改范围、修改原因、受影响调用方和潜在风险。业务模块内部修改在用户已经确认的任务范围内无需额外报备；如果修改会影响其他业务模块、公共数据结构、共享 Prefab、共享场景对象或全局运行行为，仍须按公共基础模块修改进行报备。新增供多个业务模块共同使用的公共基础能力时，也必须提前报备其职责、依赖关系和引入原因。“业务模块内部修改无需额外报备”仅适用于已经确认的修改范围，不构成对 UI 状态、交互方式或界面结构变更的默认授权。涉及这些变更时，必须遵守[UI 与交互变更授权规范](Standards/Governance/UIChangeAuthorization.md)。新增校验或附加限制时，还必须遵守[校验与附加限制授权规范](Standards/Governance/ValidationAuthorization.md)，不得将任务范围内的实现授权解释为自行新增限制的授权。 公开接口的访问范围、签名或实际行为契约发生变化时，即使名称及签名未变，也应按实际调用影响执行报备与确认，具体遵循[类的访问权限与公开接口规范](Standards/Code/AccessControl.md)。 配置载体迁移、权威来源变更、配置框架调整、生成契约及跨业务共享配置的修改，还须遵守[配置数据管理规范](Standards/Runtime/Configuration.md)，不得将用户对单项配置的授权扩大为其他配置或公共框架的改造授权。 性能优化不构成扩大任务授权的依据；涉及公共资源框架、对象池、调度、资源所有权、画质、物理及其他全局设置变化时，具体遵循[性能与内存管理规范](Standards/Runtime/Performance.md)、[对象池规范](Standards/Runtime/ObjectPooling.md)、[资源管理规范](Standards/Runtime/ResourceManagement.md)，仍须按实际影响报备并确认。
9. 新增、移动、重命名或删除文件及目录时，应明确实际职责归属、维护来源、目标位置及相关引用影响，并遵守[代码目录组织与文件归属规范](Standards/Code/DirectoryStructure.md)。目录规范不构成主动整理已有项目、迁移配置资源、改变生成目标或扩大任务范围的授权。 全部项目修改还须遵守[AI 修改与交付规范](Standards/Governance/AgentWorkflow.md)中的已有成果保护、多 Agent 协作、版本管理、工具权限、静态审查及交付要求。


### 核心授权与成果保护

1. 修改项目文件前，必须先调查并提出具体修改方案，取得用户明确确认。讨论、审计、技术设计认可及工具可写权限，不自动构成项目文件修改授权。
2. 已确认方案范围内的正常实现细节可以自主完成，不逐文件、逐方法重复请示；新增业务行为、UI 变化、附加限制、公共框架、公开契约、配置/资源迁移及其他专项变更，仍遵守对应授权规范。
3. 保护用户、其他 Agent、第三方及已有生成产物的修改。不得为了清理工作区、减少冲突或规范整改而擅自覆盖、删除、回退、批量重构或迁移已有成果。
4. Git 状态变更、分支、提交、推送、合并、Tag、Release、远端同步及部署操作均须按具体授权执行。不得把规范修改授权自动解释为 Git 或发布授权。
5. 工具权限不替代用户授权。通过命令行、Unity MCP、编辑器、脚本、自动化或子 Agent 实施的变更，同样受本规范及项目专用规则约束。
6. 不得虚构项目事实、API、修改、工具执行、编译、测试、性能测量或交付结果。明确区分静态确认、推断、用户提供的验证结果及待验证事项。

## 核心架构约束

1. **Manager 位于 System 上层。** Manager 可以调用 System；System 不得直接或间接依赖、调用或通过接口、委托、事件等方式变相调用 Manager。
2. Manager 与 System 不强制同名配套。Manager 可以独立承担项目级公共管理职责；System 负责业务系统或框架系统职责。新项目配置架构默认规划 ConfigManager + ConfigSystem；已有项目优先保护适用实现，不自动补建。
3. **在 Unity 项目中**，Manager 默认是可挂载、项目运行级 Unity 组件单例；System 默认是不可挂载、项目运行级普通 C# 单例；Controller 默认是场景级 Unity 组件单例。上述挂载形式、MonoBehaviour 及 Unity 场景生命周期是 Unity 的默认实现，不直接套用到其他引擎；跨引擎项目按[核心职责与单例规范](Standards/Architecture/CoreRoles.md)中的职责语义，并结合目标引擎生命周期及项目契约映射。单例约束不向下传递，其他类型按职责、所有权和生命周期确定实例方式。
4. 权威业务状态必须具有明确所有者。System 负责组织所属业务用例；可按已确认契约直接提交，也可委托 Committer。Committer 负责实际提交，不接管所属 System 的业务用例或权威状态所有权。
5. Logic、Resolver、Rule、Calculator、Evaluator、Matcher、Selector、Mapper、Normalizer 及基础处理实现层遵守纯处理边界：使用显式输入，不隐式访问全局单例、配置、资源或日志，不直接或间接提交权威状态，不执行外部业务副作用。
6. Processor 负责明确工作单元的处理生命周期，不等同于所有业务的固定中间层。具体职责拆分依据实际语义，不以代码行数或调用数量机械决定。
7. Event 表达已经成立的事实，不替代具有业务前后依赖的必要调用步骤。事件发布权限、订阅生命周期、分发及异常遵循 EventSystem 专项规范。
8. Config、RuntimeData、Snapshot、Request、Result、Event 等数据类型应明确语义、所有权及有效期。数据契约与具体状态提交、配置、事件及异步一致性规则分别归口。
9. Framework 不反向依赖具体 Game 业务。纯处理层及下层能力不得通过 Provider、Utility、Adapter、事件或委托绕过既有依赖边界。

## 核心代码与工程约束

1. 新增及修改代码遵守[代码命名规范](Standards/Code/Naming.md)、[代码注释规范](Standards/Code/Comments.md)、[类的访问权限与公开接口规范](Standards/Code/AccessControl.md)及实际目录规范。中文注释、XML 文档、关键步骤编号及 Region 按 Comments.md 执行。
2. Inspector 可见字段使用中文 `LabelText` 和完整 `Tooltip`，并遵守 Odin 分组规范；不得为了形式机械叠加 Group Attribute。
3. Inspector、Prefab、场景及配置引用默认由明确装配和手动绑定提供。未经授权不得自动查找、自动绑定、自动修复或以静默默认值替代缺失引用；合法的明确路径资源加载遵循资源管理契约。
4. 不使用无依据的业务参数硬编码或不合理魔法数字。固定程序常量、设计配置、局部组件参数、运行时状态及数学字面量应按实际语义区分。用户明确指定的配置载体及存放位置优先于通用默认方案。
5. 默认配置目录：SO、二进制及 JSON 配置资产按既有约定放在 `Assets/Resources/Configs/` 对应目录；原始 Excel 放在项目根目录 `Excel/`。已有项目契约和用户明确指定优先，具体加载及资源所有权遵循[配置数据管理规范](Standards/Runtime/Configuration.md)与[资源管理规范](Standards/Runtime/ResourceManagement.md)。
6. 移动或重命名 Unity 资产时，保留 `.meta` 与 GUID，并检查场景、Prefab、ScriptableObject、Resources 路径、Addressables 地址、生成流程及其他引用影响。
7. 任何必要规则缺失、适用条件不明或存在实质冲突时，按[规则优先级与冲突处理规范](Standards/Governance/RulePriority.md)与[AI 修改与交付规范](Standards/Governance/AgentWorkflow.md)处理，不得凭经验虚构规则或在关键约束未掌握时实施受影响修改。

## 规则优先级与冲突处理

全部有效且适用的规则共同生效。规则性质、来源、作用域、用户决策、硬性约束、授权条件、项目契约、默认规则及例外按 [RulePriority.md](Standards/Governance/RulePriority.md) 裁决。不得仅凭目录更深、文件名含 override、读取顺序较晚、规则更严格或文本更详细就认定其可以解除上层硬性约束或授权条件。

项目专用规则可以细化通用规则，但只有具有明确依据及适用范围的用户决策才能调整项目内部硬性规则或批准例外。外部强制条件及实际技术可行性不能由项目规则解除。

## 默认开发流程

项目修改任务沿用既有七阶段流程：

1. 需求分析。
2. 项目调查。
3. 技术实现方案设计与评估。
4. 修改方案设计。
5. 用户确认。
6. 代码实施与静态审查。
7. 任务交付。

简单任务可以简洁表达，但不得省略必要调查、技术决策、实施授权、静态审查及真实交付。详细流程及权限见 [AgentWorkflow.md](Standards/Governance/AgentWorkflow.md)。

## 专项规范文档索引

以下为30份共享专项规范的唯一主定义位置。表中 `General` 表示跨项目、跨引擎可复用的规则，但仍受语言、框架及明确技术条件限制；`Unity` 表示仅适用于 Unity 工程。General 文档中的 Unity 条件性实现只在 Unity 条件成立时适用。适用范围不代表规则强度或无条件必读。任务索引是初始定位依据，不是封闭清单；实际任务涉及其他职责或技术机制时必须补充读取。

### Governance · 协作治理

| 文档 | 适用范围 | 主定义职责 |
|---|---|---|
| [RulePriority.md](Standards/Governance/RulePriority.md) | General | 规则性质、优先级、作用域、继承、例外及冲突裁决 |
| [AgentWorkflow.md](Standards/Governance/AgentWorkflow.md) | General | 七阶段开发流程、授权、项目调查、读取机制、静态审查及交付 |
| [UIChangeAuthorization.md](Standards/Governance/UIChangeAuthorization.md) | General | UI 状态、交互及界面结构变更授权 |
| [ValidationAuthorization.md](Standards/Governance/ValidationAuthorization.md) | General | 校验依据、额外限制、拦截、自动修正及兜底授权 |
| [StandardsMaintenance.md](Standards/Governance/StandardsMaintenance.md) | General | 规范体系、GitHub、版本、审批、部署、兼容及长期维护 |

### Architecture · 软件架构

| 文档 | 适用范围 | 主定义职责 |
|---|---|---|
| [ArchitecturePrinciples.md](Standards/Architecture/ArchitecturePrinciples.md) | General | 架构先行、职责拆分、整体数据流及跨框架协作 |
| [CoreRoles.md](Standards/Architecture/CoreRoles.md) | General | Manager、System、Controller、Module、Initializer、Factory 等 |
| [ProcessingRoles.md](Standards/Architecture/ProcessingRoles.md) | General | Logic、Processor、Resolver、Calculator、Evaluator 及基础处理实现 |
| [StateCommit.md](Standards/Architecture/StateCommit.md) | General | 权威状态所有权、提交职责及 Committer |
| [DataContracts.md](Standards/Architecture/DataContracts.md) | General | 数据类型通用命名、只读性、所有权及传递边界；具体数据语义按主定义归口 |
| [SupportingRoles.md](Standards/Architecture/SupportingRoles.md) | General | Utility、Provider、Registry、Service、DataStorage、Adapter |
| [EventSystem.md](Standards/Architecture/EventSystem.md) | General | EventSystem、事件语义、发布订阅及分发 |
| [UIArchitecture.md](Standards/Architecture/UIArchitecture.md) | General | Controller、View、Presenter、ViewModel 等表现层架构 |

### Code · 代码工程

| 文档 | 适用范围 | 主定义职责 |
|---|---|---|
| [Naming.md](Standards/Code/Naming.md) | General | C# 命名格式、语义命名、对象命名及职责后缀索引 |
| [AccessControl.md](Standards/Code/AccessControl.md) | General | 访问修饰符、封装、公开接口、集合及程序集边界 |
| [DirectoryStructure.md](Standards/Code/DirectoryStructure.md) | Unity | Unity 项目目录、领域归属、文件拆分及生成产物保护 |
| [Comments.md](Standards/Code/Comments.md) | General | 中文注释、XML、关键步骤、Region 及技术说明 |

### Design · 技术设计

| 文档 | 适用范围 | 主定义职责 |
|---|---|---|
| [TechnicalDesign.md](Standards/Design/TechnicalDesign.md) | General | 软件设计原则、设计模式、算法、数据结构、研究及技术选型 |

### Runtime · 运行时框架

| 文档 | 适用范围 | 主定义职责 |
|---|---|---|
| [Configuration.md](Standards/Runtime/Configuration.md) | Unity | 配置分类、载体、ConfigManager、ConfigSystem 及读取生命周期 |
| [ResourceManagement.md](Standards/Runtime/ResourceManagement.md) | Unity | 资源所有权、Resources、Addressables、AssetManager、AssetSystem |
| [ObjectPooling.md](Standards/Runtime/ObjectPooling.md) | Unity | PoolManager、PoolSystem、租用、归还、重置及容量 |
| [Logging.md](Standards/Runtime/Logging.md) | Unity | 日志、业务失败、运行错误、异常传播及诊断责任 |
| [Async.md](Standards/Runtime/Async.md) | Unity | UniTask、异步生命周期、取消令牌及异常观察 |
| [StateConsistency.md](Standards/Runtime/StateConsistency.md) | General | 跨引擎可复用的业务并发、重入、幂等、状态有效性、取消及跨 System 一致性 |
| [Performance.md](Standards/Runtime/Performance.md) | Unity | GC、数据结构、执行频率、缓存、渲染、物理及并行计算 |

### Unity · Unity 专项

| 文档 | 适用范围 | 主定义职责 |
|---|---|---|
| [ProjectConstraints.md](Standards/Unity/ProjectConstraints.md) | Unity | Unity 工程操作、手动绑定及自动查找限制 |
| [OdinInspector.md](Standards/Unity/OdinInspector.md) | Unity | Inspector 分组及字段说明 |
| [Prefab.md](Standards/Unity/Prefab.md) | Unity | Prefab 根节点、对象封装及内部引用 |
| [UIObjects.md](Standards/Unity/UIObjects.md) | Unity | UI GameObject 命名及层级组织 |
| [CharacterMovement.md](Standards/Unity/CharacterMovement.md) | Unity | 人物控制、输入、时间域、运动后端及相机 |

## 任务与规范读取对应关系

以下清单用于形成初始阅读集合。核心入口及授权规则始终适用；最终读取范围由任务性质、职责类型、技术机制、文件及资源类型、实际影响与授权五个维度共同决定。

| 任务示例 | 初始专项规范 |
|---|---|
| 新建业务 System | ArchitecturePrinciples、CoreRoles、ProcessingRoles、AccessControl、Naming、Comments、DirectoryStructure、TechnicalDesign；涉及权威状态持有或提交时补 StateCommit、DataContracts；涉及配置、事件、异步、资源及日志时按实际机制补充对应规范 |
| 新增伤害计算公式 | ProcessingRoles、DataContracts、Naming、Comments、DirectoryStructure、TechnicalDesign；涉及配置或高频计算时补 Configuration、Performance |
| 新增业务事件 | EventSystem、CoreRoles、DataContracts、StateCommit、Naming、Comments；涉及异步或异常时补 Async、StateConsistency、Logging |
| 新建状态提交 | StateCommit、CoreRoles、DataContracts、ProcessingRoles、AccessControl、TechnicalDesign；涉及并发时补 StateConsistency |
| 开发对象池 | ObjectPooling、ResourceManagement、CoreRoles、Performance、AccessControl、DirectoryStructure；涉及异步时补 Async、StateConsistency |
| 开发配置加载 | Configuration、DataContracts、ResourceManagement、DirectoryStructure、Async、Logging |
| 修改 UI 交互 | UIChangeAuthorization、UIArchitecture、UIObjects、Prefab、ProjectConstraints；涉及限制或异步时补 ValidationAuthorization、StateConsistency、Async |
| 修改 Prefab / Inspector | Prefab、OdinInspector、ProjectConstraints、DirectoryStructure、AccessControl、Comments |
| 优化高频战斗计算 | TechnicalDesign、ProcessingRoles、Performance、DataContracts；实际提交或并发时补 StateCommit、StateConsistency |
| 修改人物移动 | CharacterMovement、CoreRoles、ProcessingRoles、StateConsistency、Performance、ProjectConstraints |
| 修改公共框架或公开接口 | AgentWorkflow、RulePriority、CoreRoles、AccessControl、对应框架专项规范及实际调用方规则 |
| 修改规范文档 | RulePriority、AgentWorkflow、StandardsMaintenance、目标规范及其关联主定义 |

## 规范读取与动态补读要求

1. Codex 原生加载的用户级及项目级规则是初始入口，不假定它已自动加载 `Standards/` 全部文件或目标子目录中的局部规范。
2. 明确目标后检查项目根目录至目标目录之间的适用 `AGENTS.md` / `AGENTS.override.md`。同目录有效 override 替代普通 AGENTS.md，不自动合并；局部规则不得仅凭目录层级解除上层硬性约束。
3. 选定专项规范默认完整读取；明确局部任务可以定向读取，但必须掌握完整适用条件、约束、例外及必要依赖。基础依赖必须掌握，条件协作在触发时读取，参考关联按需读取。
4. 新增职责、技术机制、文件范围或实际影响时动态补读。上下文压缩、任务恢复、规范来源或内容变化时，恢复无法确认的必要规则，不机械重读全部规范。
5. 子 Agent 必须掌握自身任务适用规则、项目契约、局部规范及授权范围；主 Agent 的阅读只有在实际内容、来源和适用范围明确传递时才能复用。
6. 必要规范缺失或无法读取时，先尝试允许的只读恢复；仍无法掌握时，阻塞受影响实施，允许继续独立且不受影响的已授权工作。不得虚构规则、混用版本或绕过访问权限。
7. 默认不引入 Hooks、自动加载程序、依赖数据库或持久化读取记录。工具级保障按实际问题经确认后引入，不替代用户授权、语义判断及静态审查。

## 规范维护与交付要求

通用规范由 GitHub `DevelopmentStandards` 仓库权威维护；本地 Codex Home 是受控部署副本。规范修改、Git 操作、版本发布及本地部署分别遵循授权。规范更新不自动触发已有项目代码迁移；存量保护、增量遵守、项目固定版本及迁移边界见 [StandardsMaintenance.md](Standards/Governance/StandardsMaintenance.md)。

项目任务交付必须真实说明修改文件、核心实现、实际影响、手动绑定及验证步骤、静态审查和剩余风险。Agent 不得主动编译、查询编译错误、运行项目或执行测试；用户手动完成实际验证。交付要求详见[AI 修改与交付规范](Standards/Governance/AgentWorkflow.md)。
