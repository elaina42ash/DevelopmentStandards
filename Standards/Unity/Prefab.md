---
id: unity.prefab
title: Unity Prefab 职责与封装规范
category: Unity
scope: Unity
---

# Unity Prefab 职责与封装规范

## 规范定位与适用范围

定义 Prefab 根节点、对象封装、内部引用及交付边界。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

## 相关规范

### 基础依赖
- [Unity 项目约束](ProjectConstraints.md)
- [代码目录组织与文件归属规范](../Code/DirectoryStructure.md)

### 条件协作
- [Unity UI 对象命名与层级规范](UIObjects.md)
- [UI 表现层架构规范](../Architecture/UIArchitecture.md)

## Prefab 职责与封装规则

1. **对象封装与交付。** Prefab 以一个可复用对象为单位，统一维护对象层级、组件、默认配置和内部引用。Prefab 可以作为不同岗位之间的对接交付物，使使用方能够按约定放置、配置和使用整个对象。

2. **根节点职责。** Prefab 根节点使用能够表达整体对象的业务名称，承接整体定位、必要配置和对外入口。根据对象的实际职责，可以在根节点挂载代表该对象的脚本，负责接收数据、处理本对象的交互及连接内部部件。

3. **具体表现放在子节点。** UI Prefab 中，`Image`、`RawImage`、TMP 等负责具体显示的组件统一放在子节点。只显示一张图的 UI Prefab 同样采用业务根节点和图片子节点。具体 UI 对象按照[Unity UI 对象命名与层级规范](UIObjects.md)命名。

4. **对接入口与内部引用。** Prefab 的制作方负责装配内部部件并维护默认配置和引用。使用方通过根节点上约定的对象脚本、接口或配置使用整个对象。对象脚本通过显式绑定的引用访问内部部件。

5. **Prefab 资产命名。** Prefab 文件名应清楚表达业务用途，并沿用项目已有的资产前缀约定。通过 Addressables 加载的资源，文件名、各级目录名和登记地址均使用无方括号的名称。

6. **适用范围与保护。** 本规范适用于新建 Prefab 和经确认的 Prefab 重构。调整已有 Prefab 的文件名、根节点名称、层级、组件或引用前，须说明对代码、动画、资源地址和引用的影响，并按项目变更授权流程取得确认。 Prefab 实例池化、资源持有及最终销毁按实际对象和资源生命周期契约组织，具体遵循[性能与内存管理规范](../Runtime/Performance.md)、[对象池规范](../Runtime/ObjectPooling.md)、[资源管理规范](../Runtime/ResourceManagement.md)。

层级示例

```text
人物卡片.prefab
└─ 人物卡片
   ├─ [Image]动态_头像
   └─ [Text (TMP)]动态_姓名
```

`人物卡片` 根节点挂载 `CharacterCard`，通过显式引用绑定头像和姓名。使用方通过 `CharacterCard` 提供的入口传入人物数据。
