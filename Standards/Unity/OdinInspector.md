---
id: unity.odin-inspector
title: Odin Inspector 分组规范
category: Unity
scope: Unity
---

# Odin Inspector 分组规范

## 规范定位与适用范围

定义 Odin Inspector 分组、字段说明及父子分组组织。

本规范是对应专业事项的规则主定义文档。规则适用范围、约束性质、优先关系及例外统一遵循 [规则优先级与冲突处理规范](../Governance/RulePriority.md)；项目修改、读取、实施及交付遵循 [AI 修改与交付规范](../Governance/AgentWorkflow.md)。

## 相关规范

### 基础依赖
- [Unity 项目约束](ProjectConstraints.md)

### 条件协作
- [代码注释规范](../Code/Comments.md)
- [类的访问权限与公开接口规范](../Code/AccessControl.md)

## Inspector 分组与校验规则

1. Inspector 顶层职责区域统一使用 `FoldoutGroup`；其内部的具体引用、配置或交互区域使用 `BoxGroup`。普通字段不得无依据创建三层以上嵌套分组。
2. Odin 分组路径中的 `/` 表示父子层级。任何使用 `父级/子级` 的 Group ID，都必须先在同一个声明类型中显式建立对应父级，禁止只声明子路径。
3. 推荐统一采用以下两层结构：
   - 顶层：`FoldoutGroup("业务名称")`
   - 子级：`BoxGroup("业务名称/具体职责")`
4. 分组常量必须从父级常量逐层组合，禁止分别硬编码容易失配的完整路径。例如：

   ```csharp
   // 任务星星的 Inspector 顶层职责分组标识。
   private const string INSPECTOR_GROUP_TASK_STAR =
       "任务星星";

   // 任务星星交互子分组标识，由顶层分组路径组成。
   private const string INSPECTOR_GROUP_TASK_STAR_INTERACTION =
       INSPECTOR_GROUP_TASK_STAR + "/交互";
   ```

5. 同一字段需要进入子分组时，必须先声明父级 Attribute，再声明子级 Attribute。例如：

   ```csharp
   // 任务星星的点击交互按钮。
   [SerializeField]
   [FoldoutGroup(INSPECTOR_GROUP_TASK_STAR)]
   [BoxGroup(INSPECTOR_GROUP_TASK_STAR_INTERACTION)]
   [LabelText("任务星星按钮")]
   [Tooltip("绑定当前星星用于接收点击的按钮。")]
   private Button taskButton;
   ```

6. 禁止以下缺少父级声明的写法：

   ```csharp
   [FoldoutGroup("任务星星/UI引用")]
   [BoxGroup("任务星星/UI引用/交互")]
   private Button taskButton;
   ```

7. 如果确实需要多层嵌套，必须逐级声明每个父级 Group，并优先沿用当前类型中已经存在的分组结构；不得直接跳级创建 `父级/子级/孙级`。
8. 修改或新增 Odin Inspector Attribute 后，Agent 必须在交付前进行静态分组检查：
   - 检查所有包含 `/` 的 Group ID；
   - 确认其顶层父级已经显式声明；
   - 确认父级 Attribute 写在子级 Attribute 之前；
   - 确认没有拼写不同但语义相同的重复分组；
   - 确认没有为了分组而改变字段序列化名称或现有引用。
9. Agent 不得通过删除全部 Inspector 分组来规避 Odin 错误。若现有模块已经形成稳定分组风格，应优先沿用该模块的父子 Group 结构。
10. 对于必须由用户在 Inspector、Prefab 或场景中手动绑定，且缺失后对应功能无法正常执行的引用字段，必须添加 Odin `[Required]`，并提供能够明确说明所需引用和用途的中文错误信息。
11. `[Required]` 只用于真正必需的引用。可选引用、运行时注入引用、动态创建对象、允许为空的配置以及具有明确业务含义的空值，不得机械添加 `[Required]`。
12. `[Required]` 只提供编辑期提示，不能代替运行时显式空值检查、`Debug.LogError` 或统一日志入口的等价 Error 级别诊断，以及局部操作跳过逻辑。
13. `[Required]` 应写在对应的 Odin 分组 Attribute 之后，并与中文 `LabelText`、完整 `Tooltip` 同时使用；错误信息必须指出应绑定的对象及用途。
14. 集合字段除引用本身不能为空外，如果业务还要求至少包含一个元素，应使用符合项目现有 Odin 风格的额外校验；不得错误地认为 `[Required]` 能完整校验集合内容。
15. Inspector 校验必须区分错误、警告和普通提示。缺失后功能无法执行的必需引用使用 `[Required]`；配置仍可运行但可能产生异常表现、效果退化或使用风险时，才使用警告；不影响正确性的操作说明使用普通提示。
16. 真正必需的引用不得使用 `[Required(InfoMessageType.Warning)]` 降低严重程度，应保持 `[Required]` 的默认错误提示。
17. 单个字段自身的数值、范围、格式或内容存在可疑状态时，优先使用 `[ValidateInput]` 和 `InfoMessageType.Warning`；涉及多个字段组合、功能开关或上下文条件时，使用带显式条件方法的 `[InfoBox]` 和 `InfoMessageType.Warning`。
18. 警告必须按实际条件显示，不得为正常配置长期展示无意义的固定警告，也不得为了形式给所有序列化字段机械添加警告。
19. Odin 校验方法必须是无副作用的纯判断，不得在校验过程中修改字段、自动绑定引用、创建对象、扫描场景、记录日志或执行运行时业务。
20. 中文警告信息必须说明当前问题、可能影响和建议处理方式，不得只写“配置错误”“请检查”等宽泛内容。
21. Odin 错误、警告和普通提示只用于编辑期配置反馈，不能代替运行时参数检查、明确结果返回以及符合[Unity 日志与异常处理规范](../Runtime/Logging.md)的 `Debug.LogWarning`、`Debug.LogError` 或统一日志入口的等价等级诊断。

必需引用字段示例：

```csharp
// 接收当前任务星星点击操作所需的按钮引用。
[SerializeField]
[FoldoutGroup(INSPECTOR_GROUP_TASK_STAR)]
[BoxGroup(INSPECTOR_GROUP_TASK_STAR_INTERACTION)]
[Required("必须绑定用于接收任务星星点击的按钮。")]
[LabelText("任务星星按钮")]
[Tooltip("绑定当前任务星星用于接收玩家点击操作的按钮组件。")]
private Button taskButton;
```

`[Required]` 只负责在 Inspector 中提示缺失引用。运行时仍必须按照[Unity 项目约束](ProjectConstraints.md)进行显式空值检查，使用 `Debug.LogError` 或统一日志入口的等价 Error 级别诊断记录错误，并只跳过直接依赖该引用的局部操作。Odin `[Required]` 与 Unity `[RequireComponent]` 的职责不同：前者校验序列化字段是否已经绑定，后者声明同一 GameObject 必须具备的组件，不能用于替代字段赋值。

以下示例只展示警告校验写法；实际字段仍须按照本节前述规则添加完整的父子分组、中文 `LabelText` 和完整 `Tooltip`。

单字段警告示例：

```csharp
// 界面淡入时长，单位为秒。
[SerializeField]
[ValidateInput(
    nameof(IsFadeDurationReasonable),
    "淡入时长为 0，界面会立即出现；如非预期，请设置大于 0 的时长。",
    InfoMessageType.Warning)]
[LabelText("淡入时长")]
[Tooltip("控制界面完成淡入所需的秒数。")]
private float fadeDuration;

/// <summary>判断淡入时长是否满足当前示例的 Inspector 警告条件要求。</summary>
/// <param name="value">待检查的淡入时长，单位为秒。</param>
/// <returns>时长大于零时返回 true；否则返回 false，由 Odin 显示警告，不阻断运行。</returns>
private bool IsFadeDurationReasonable(float value)
{
    // 1. 零时长仍可运行，但需要在 Inspector 中提示可能的表现差异。
    return value > 0f;
}
```

跨字段条件警告示例：

```csharp
// 启用点击音效时使用的音频资源。
[SerializeField]
[InfoBox(
    "已启用点击音效，但未配置音效资源；点击操作仍可执行，但不会播放声音。",
    InfoMessageType.Warning,
    nameof(ShouldShowClickAudioWarning))]
[LabelText("点击音效")]
[Tooltip("启用点击音效时播放的音频资源。")]
private AudioClip clickAudioClip;

/// <summary>判断当前点击音效配置是否需要显示 Inspector 警告。</summary>
/// <returns>点击音效已启用且音频资源缺失时返回 true；否则返回 false。</returns>
private bool ShouldShowClickAudioWarning()
{
    // 1. 仅在启用点击音效且资源缺失时显示警告。
    return enableClickAudio && clickAudioClip == null;
}
```
