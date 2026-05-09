# 什么是JIT异常？

为了避免tModLoader频繁更新和mods变得"过时"导致的静默错误，tModLoader在加载每个mod时检查所有类和方法，确保它能正常工作。

tModLoader开发人员通过精心编程努力避免这些类型的问题，但有时这些问题是无法预见的或不可避免的。

# 玩家说明

mod坏了，您需要等待mod更新。可能作者不知道mod坏了或者不想继续更新mod。您可以检查mods的首选反馈位置，看是否能找到更多信息，或者如果没有其他人报告该问题，请通知作者。通常mods有GitHub问题跟踪器、Discord服务器或mod的Steam创意工坊页面作为其首选反馈位置。

# Modder说明

您需要更新您的mod。大多数时候这些错误发生是因为tModLoader更改了而您的mod还没有更新。例如，如果tModLoader方法更改了，您的mod将损坏，因为它试图使用一个不再存在的方法。另一种可能性是您的mod使用强引用或弱引用，而最近的tModLoader更改要求您需要进一步注释mod中的某些方法或类以适应。

## tModLoader更改了

您需要更改mod中的代码来修复现在存在的所有错误并发布版本。第一步是从ModSources菜单运行tModPorter。这应该能修复大多数错误。会有一些剩余的错误，tModPorter会添加新注释，您需要根据注释的说明修复那些剩余的错误。有些更改不会被tModPorter处理。[tModLoader Discord](https://discord.gg/tmodloader)上的`#preview-update-log`频道是我们跟踪在每个月`stable`更新的预期中引入的破坏性更改的地方。通常会有`Porting Notes`列出可能破坏现有mods的更改。[更新迁移指南](https://github.com/tModLoader/tModLoader/wiki/Update-Migration-Guide)也会偶尔更新。有一种可能性是Visual Studio中没有错误，但您原来发布的mod加载失败。在这种情况下，再次构建mod并发布应该可以修复问题。这通常是由为您之前使用的方法添加新的可选参数引起的。

### tModLoader更改示例

这是一个典型的`JITException`消息，这个例子将解释和修复这个特定错误：
![image](https://user-images.githubusercontent.com/4522492/168198434-efe69dc0-91f0-4de3-98e2-9c1a464c06f4.png)

首先，我们在顶部看到导致错误的mod叫做"BannerBonanza"。在下一节，我们看到实际的`JITException`消息，它说：
> In BannerBonanza.Items.BannerRackItem.SetDefaults, Method not found: 'Void Terraria.Item.DefaultToPlacableWall(UInt16)'."

从这里，我们知道在`BannerRackItem`类（位于`BannerBonanza.Items`命名空间中），`SetDefaults`方法就是发生这个特定错误的地方。错误说方法`Void Terraria.Item.DefaultToPlacableWall(UInt16)`不存在。构建这个mod时该方法确实存在，但显然现在已经不存在了。

此时，您可以检查[tModLoader Discord](https://discord.gg/tmodloader)上的`#preview-update-log`频道并查找谈论此事的消息。这是它的样子：
![image](https://user-images.githubusercontent.com/4522492/168232320-c63110a8-776f-4e35-9a57-bde54fa69f5d.png)

此时，打开Visual Studio并导航到错误的位置。您应该能够立即看到红色下划线表示错误：
![image](https://user-images.githubusercontent.com/4522492/168201563-48c70e41-95f2-4161-92ef-4b421faa3e6e.png)

使用更新日志消息或其他方法提供的信息，通过将`DefaultToPlacableWall`更改为`DefaultToPlaceableWall`来修复错误。（注意原来"Placeable"拼写中缺少的"e"。）

这个快速示例应该教您修复JIT异常的基本步骤，但如果您在弄清楚如何修复问题时有困难，请随时来到`#mod-programming-help`频道。有一种可能性是Visual Studio中没有错误，但您原来发布的mod加载失败。在这种情况下，再次构建mod并发布应该可以修复问题。这通常是由为您之前使用的方法添加新的可选参数引起的。

## 强引用

如果您通过强引用的mod收到此错误，很可能是那个mod更改了。从该mod中提取最新的.dll并在Visual Studio中引用较新的.dll而不是较旧的.dll。确保更新build.txt以使用`modReferences = TheOtherMod@0.2`引用至少那个版本号。接下来，修复错误并发布更新。

## 弱引用

`JITException`的另一个可能原因是弱引用到另一个mod。当游戏检查所有类寻找错误时，它会触发对无法解析的其他mods引用的`JITException`。您需要用特殊属性注释类，告诉tModLoader跳过检查这些类。`[JITWhenModsEnabled(...)]`排除类/方法/属性在加载时JIT。在任何直接引用可能不存在的mod类型的成员上使用此属性。提供所有需要加载才能允许检查的mod名称。以下是虚拟mod `ExampleModDepTest`中的一些示例：

```cs
public class ExampleModCalls
{
    [JITWhenModsEnabled("ExampleMod")]
    public static int property => ModContent.ItemType<ExampleItem>();

    [JITWhenModsEnabled("ExampleMod")]
    public ExampleModCalls()
    {
        ModContent.GetInstance<ExampleModConfig>().ExampleWingsToggle = true;
    }

    [JITWhenModsEnabled("ExampleMod")]
    public static void method()
    {
        ModContent.GetInstance<ExampleModConfig>().ExampleWingsToggle = true;
    }

    [JITWhenModsEnabled("ExampleMod")]
    public static Action lambda()
    {
        return [JITWhenModsEnabled("ExampleMod")] () => ModContent.GetInstance<ExampleModConfig>().ExampleWingsToggle = true;
    }
}

[JITWhenModsEnabled("ExampleMod")]
public class SeparateClass
{
    public ExampleItem item = ModContent.GetInstance<ExampleItem>();

    public static ExampleModConfig method() => ModContent.GetInstance<ExampleModConfig>();

    public static Func<ExampleModConfig> lambda() => () => ModContent.GetInstance<ExampleModConfig>();
}
```

如果您有特殊情况，可以在类/方法/属性上使用`NoJIT`属性来完全跳过JIT过程。如果您需要更多控制，您可以创建`MemberJitAttribute`的自定义子类或将`Mod.PreJITFilter`设置为自定义重写。请注意，`ShouldJIT`的基础实现检查`MemberJitAttribute`。

这些注释并不能免除您在[专家跨Mod内容](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content#weak-references-aka-weakreferences-expert)指南中所教的正确地保护对弱引用mods中类型的方法的调用。
