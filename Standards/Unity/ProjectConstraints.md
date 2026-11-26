---
id: unity.project-constraints
title: Unity 项目约束
category: Unity
scope: Unity
---

# Unity 项目约束

## 规范定位与适用范围

定义 Unity 工程修改、手动绑定、自动查找限制及交付清单。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

## 相关规范

### 基础依赖
- [代码目录组织与文件归属规范](../Code/DirectoryStructure.md)

### 条件协作
- [配置数据管理规范](../Runtime/Configuration.md)
- [资源管理规范](../Runtime/ResourceManagement.md)
- [Unity Prefab 职责与封装规范](Prefab.md)
- [Odin Inspector 分组规范](OdinInspector.md)

## Unity 项目操作约束

1. 修改 Unity 脚本、Prefab、场景、配置表或资源前，必须明确说明影响范围。 涉及文件移动、重命名、删除、目录创建及特殊目录时，同时遵守[代码目录组织与文件归属规范](../Code/DirectoryStructure.md)。
2. 手动编辑 Unity 序列化文件前，必须说明原因，并优先使用 Unity 编辑器或项目已有工具完成修改。
3. 表格、二进制配置、Addressables、Prefab 引用等运行时依赖变更后，必须提醒用户通过 Unity 执行对应生成或验证流程。 涉及配置表、生成代码、运行时配置文件及配置资产时，须先识别真实维护来源和已有生成流程，不得擅自直接修改自动生成产物、迁移配置载体或改变配置字段的序列化契约；具体遵循[配置数据管理规范](../Runtime/Configuration.md)，并明确需要用户手动完成的导表、生成及 Unity 验证。 涉及资源加载、对象池及释放机制的变更，须明确实际资源所有者、释放路径和用户手动验证事项，遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。
4. 不允许为了临时通过验证而加入兜底逻辑、兼容旧逻辑或静默默认值；需要保留兼容时必须先说明原因并获得确认。
5. 除非用户明确要求，Agent 生成或修改涉及 Inspector、Prefab、场景对象、`SerializeField`、资源引用、组件引用或拖拽引用的代码时，禁止添加任何自动绑定、自动查找、自动修复或静默兜底逻辑。
6. 禁止默认使用 `FindObjectOfType`、`FindAnyObjectByType`、`GameObject.Find`、`Transform.Find`、`GetComponentInChildren`、`GetComponentInParent`、`Resources.Load`、场景全局扫描、按名称查找对象、按 Tag 查找对象、运行时自动创建默认对象、`Reset` 自动赋值、`OnValidate` 自动赋值、编辑器脚本自动补引用等方式代替用户手动拖拽引用。 上述限制针对未经授权的自动查找、自动绑定、自动修复及缺失引用兜底，不构成对相关 API 合法用途的全面禁止。根据明确资源路径、配置标识及既有资源契约主动加载 Resources、Addressables 等资源，以及合法对象池租用，属于正常资源访问与对象复用行为，不等同于未经授权的自动查找或替代缺失的手动拖拽引用；仍须按实际资源、池化及装配契约处理。Resources 的资源加载职责、同步与异步选择、使用范围及资源所有权遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。不得借正常资源加载擅自改变已有 Inspector 引用及装配契约。
7. 对于必须由 Inspector、Prefab、场景或配置表提供的引用，代码必须进行显式空值检查，并由实际诊断责任边界使用 `Debug.LogError` 或统一日志入口的等价 Error 级别诊断输出所属对象、字段和失败操作等必要信息。引用缺失导致相关操作无法安全执行时，只跳过直接依赖该引用的当前操作；其他不依赖该引用的独立流程应继续运行。不得通过 `throw` 将此类错误沿业务调用链传播，也不得自行寻找替代引用、创建临时对象或使用静默默认值继续运行。本条不授权增加没有实际运行依赖的名称、层级、路径或归属限制；新增检查必须同时遵守[校验与附加限制授权规范](../Governance/ValidationAuthorization.md)。 日志权限、必要诊断与主要记录责任遵循[Unity 日志与异常处理规范](../Runtime/Logging.md)，不得借统一日志入口、省略重复日志或纯处理职责限制而省略必要运行时检查及错误记录。
8. 任务完成后，Agent 必须明确列出需要用户手动拖拽、绑定或配置的引用清单，包括字段名、所属脚本或对象、期望引用类型、建议绑定位置，以及该引用的用途。无法通过静态代码确认的引用，必须标记为“需要用户手动确认”。 该清单与用户手动验证步骤、实际修改及影响等一并按[AI 修改与交付规范](../Governance/AgentWorkflow.md)交付，不将待绑定或待验证事项表述为已经完成。
9. 如果用户明确要求实现自动绑定逻辑，Agent 必须在修改前说明自动绑定的必要性、触发时机、查找范围、失败处理方式、可能副作用和替代方案，并取得用户确认后才能实现。 满足本条专项授权条件属于正常规则执行，不是对手动绑定规范的永久废止或其他操作的授权。规则例外及优先关系遵循[规则优先级与冲突处理规范](../Governance/RulePriority.md)。
10. 资源加载、运行时创建、池化、借用及释放应具有明确的所有权、有效期与对应清理契约；不得为降低内存或帧时间擅自改变玩法、UI、资源质量、渲染管线、项目设置或已有资源加载契约。具体遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。
