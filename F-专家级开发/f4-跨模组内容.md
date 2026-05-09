***
本指南已更新至 1.4。如果您需要查看此 wiki 页面的旧 1.3 版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content/838b9239b7030e2c7bf3654ef527b4d8b70a3e2d)
***

# 首先阅读

在与其他模组协作时，有几点需要注意。最需要注意的是，您希望与之交互的模组可能实际上并未加载。调用方法、访问字段或尝试使用未加载模组中的物品将导致问题。如果您使模组"强"依赖于另一个模组，则可以完全避免此问题，但这意味着没有另一个模组存在时，您的模组将无法被加载。强依赖的替代方案是弱依赖。弱依赖是最难正确实现的，但也是最强大的。第三种方案不如前两种强大，但更简单，依赖于 Mod.Call，这是一种模组之间传递消息的简单方法。此方案需要您希望与之交互的模组支持它。第四种方案是使用反射，这是不好的方法。第五种方案适用于配方等简单内容。这些方案从简单到强大的顺序在下面解释。

# 简单的跨模组内容。配方、物品和瓷砖（中级）

跨模组内容最简单的形式是在配方、商店或掉落中利用物品或瓷砖。首先要注意的是该模组可能不存在。为了确定它是否存在，我们首先向 tModLoader 请求 Mod 对象。
```cs
ModLoader.TryGetMod("ExampleMod", out Mod exampleMod);
```
`exampleMod` 对象现在要么是 `null`，要么是对 `ExampleMod` 的 `Mod` 类的有效引用。有两种方法可以确定使用 `ModLoader.TryGetMod` 时模组是否已加载，您可以使用方法返回的布尔值，也可以检查 `Mod` 实例是否为 null。在此示例中，检查方法是否返回 `true` 更容易。我们将向城镇 NPC 的商店添加一个物品。
```cs
public override void AddShops()
{
	// other code assigning npcShop

	// Find mod and then find item in that mod
	if (ModLoader.TryGetMod("ExampleMod", out Mod exampleMod)) {
		if (exampleMod.TryFind("ExampleWings", out ModItem exampleWings)) {
			npcShop.Add(exampleWings.Type);
		}
		// Add more items to the shop from Example Mod
	}

	// Alternate approach: Find item from mod directly
	if(ModContent.TryFind("ExampleMod", "ExampleTorch", out ModItem exampleTorch)) {
		npcShop.Add(exampleTorch.Type);
	}

	// other code and npcShop.Register();
}
```
正如您所看到的，我们可以使用 `exampleMod` 对象并调用 `TryFind` 方法来获取物品的 `ModItem`。然后我们使用 `ModItem.Type` 来检索该物品的类型。第二个示例展示了一步到位地使用 `ModContent.TryFind` 来获取 `ModItem`。请注意，`"ExampleWings"` 对应于物品的内部名称，因此可能需要询问其他模组制作者，以便您获得所需物品的正确内部名称。请参阅下面的[确定内部名称](#determining-internal-names)以获取更多方法。另请注意，如果您引用的模组更改了内部名称，您的模组将失效，直到您修复它。建议期望跨模组内容的模组避免更改其他模组期望保持一致的字段、方法和命名空间。

类似代码可用于 NPC 掉落和配方。

## 配方示例
配方示例请参阅[这里](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#cross-mod-recipes)。

## NPC 掉落示例
```cs
public override void ModifyNPCLoot(NPCLoot npcLoot) {
	if (ModContent.TryFind("ExampleMod", "ExampleSoul", out ModItem exampleSoul)) {
		npcLoot.Add(ItemDropRule.Common(exampleSoul.Type, 5));
	}
}
```

## 选择性加载内容示例
我们可以根据其他模组是否启用来选择性地加载或不加载内容。这对于避免内容重叠或促进跨模组内容很有用。为此，只需在 `IsLoadingEnabled` 方法中检查模组是否启用：
```cs
public override bool IsLoadingEnabled(Mod mod) {
	return ModLoader.HasMod("SomeOtherMod"); // Only load this content if the "SomeOtherMod" is enabled as well.
}
```

## 确定内部名称
如果您使用 [Helpful Hotkeys](https://steamcommunity.com/sharedfiles/filedetails/?id=2645058109) 模组并启用 `Show Developer Info` 设置，然后在游戏中悬停在模组实体上时使用 `Query Mod Origin` 热键，您可以确定该实体的内部名称。另一种方法是在 `ModLoader.GetMod` 方法调用后设置断点并检查 resulting `Mod` 对象。例如，您可以检查 `items` 字典以找到您感兴趣的 `ModItem` 的 `Name` 属性。

您可以使用的另一个模组是 ["Which Mod Is This From"](https://steamcommunity.com/sharedfiles/filedetails/?id=2563851005)：在其配置中启用所有内容，它会在您悬停时告诉您实体的内部名称。

# Call，也就是 Mod.Call（中级）

Call 是一个需要被调用模组和调用模组双方合作的方法。被调用模组将公布它们接受的各种消息的详细信息，希望与那些模组交互的调用模组将遵循消息格式以向被调用模组发送消息。

为了讲解这个概念，我们将与一个利用 Call 的流行模组进行交互：[Census - Town NPC Checklist](https://steamcommunity.com/sharedfiles/filedetails/?id=2687866031)（此模组让我们可以将模组的城镇 NPC 添加到扩展住房面板中，列出他们的条件。这对于玩家了解城镇 NPC 何时可以搬入非常有用）。首先，我们需要找到获取 Call 的地方。该模组有一个 [wiki](https://github.com/JavidPack/Census/wiki/Support-using-Mod-Call)，其中解释了各种 Call。阅读此页面后，我们发现我们可以向 Census 发送 1 条消息。该页面还指示我们在 `PostSetupContent` 中执行 `Call`，但这对于其他模组可能不同。我们现在使用与之前相同的技术调用 `ModLoader.TryGetMod` 来检查模组是否已加载。我们还必须确保完美遵循消息格式，否则可能会出现错误。现在让我们编写代码，就像我们想为 ExampleMod 添加 Census 支持一样：
```cs
public override void PostSetupContent()
{
    if (ModLoader.TryGetMod("Census", out Mod censusMod))
    {
        censusMod.Call("TownNPCCondition", ModContent.NPCType<Content.NPCs.ExamplePerson>(), $"Have either an Example Item [i:{ModContent.ItemType<Content.Items.ExampleItem>()}] or an Example Block [i:{ModContent.ItemType<Content.Items.Placeable.ExampleBlock>()}] in your inventory");
    }
}
```
现在，如果我们构建我们的模组，我们将看到我们的城镇 NPC 被添加到住房面板中，即使它尚未搬入。

`Mod.Call` 非常有用，而且是模组之间通信的非常简单的方式。有关如何实现接收 `Mod.Call` 以便其他模组可以与您的模组交互的信息，请参阅 Census 或其他开源模组的源代码。在 ExampleMod 中可以找到调用其他模组的 Mod.Calls 的完整注释展示（包括利用 Call 的最流行模组：Boss Checklist）以及如何组织它们可以找到[这里 (ModIntegrationsSystem)](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Common/Systems/ModIntegrationsSystem.cs)。

# 强引用，也就是 modReferences（专家级）

强引用很容易，但如果引用的模组未加载，它们会阻止您的模组被加载。强引用代表模组的层次结构，其中引用模组在引用的模组不存在的情况下启用是没有意义的。首先，假设我们要引用 ExampleMod。首先，在您的 `build.txt` 中添加一行 "modReferences = ExampleMod"。您可能还需要指定[最低版本](#strong-and-weak-references)。其次，确保 ExampleMod 已下载并启用。第三，我们可以正常编码，做任何您想做的事情。例如，让我们制作一个设置 Abomination 和 Purity spirit 为已击败的热键。首先，我们应该添加一个 using 语句：`using ExampleMod;`。然后，在我们的 `ModPlayer.ProcessTriggers` 中（热键应该被处理的地方），我们可以这样做：
```cs
public override void ProcessTriggers(TriggersSet triggersSet)
{
    if (MyMod.ToggleChecklistHotKey.JustPressed)
    {
         ExampleMod.ExampleWorld.downedAbomination = true;
         ExampleMod.ExampleWorld.downedPuritySpirit = true;
    // ...
```
此示例非常简单，但可以通过模组引用完成各种操作。调用方法、访问公共变量、使用 `ModContent.ItemType` 和 `ModContent.NPCType` 的泛型版本，等等。

### 访问修饰符
如果您发现无法访问特定类、方法或字段，但其他的使用正常，则该成员可能具有非公共访问修饰符。如果你不熟悉 private 和 public 等访问修饰符的含义，请阅读[访问修饰符（C# 编程指南）](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)来熟悉它。某些内容非公共可能是您所引用模组作者的有意设计决定，也可能是疏忽。如果这是疏忽，请询问模组作者更新模组以解决问题。

## Visual Studio 步骤

为了在 Visual Studio 中正确编码，VS 需要引用 `.tmod` 文件中包含的 `.dll` 文件。使用 tModLoader 中的菜单来提取模组并找到 `.dll` 文件（`Workshop`->`Manage Mods`->`More Info`->`Extract`）。如果没有 `.dll` 文件被提取，则模组作者选择不允许其被解包，所以请礼貌地询问他们。默认情况下，这些最终出现在[存档目录](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)的 `ModSources\ModAssemblies` 文件夹中，文件名为 `[ModName]_v[version].dll`。我们建议使用此文件夹来引用模组 `.dll` 文件，因为这将简化协作同时避免其他问题。不要将模组 `.dll` 文件放在模组源文件夹内，它最终会被打包到 `.tmod` 中并使文件膨胀。如果您不想使用推荐的位置并坚持将模组 `.dll` 保存在模组源文件夹中，您将需要使用 [build.txt](https://github.com/tModLoader/tModLoader/wiki/build.txt) 来忽略该文件或该文件所在的文件夹。确保该文件夹不是 `lib` 文件夹，否则您会遇到其他问题。通过右键单击解决方案资源管理器中的 `Dependencies` 来添加引用，然后单击 `Add Project Reference`，然后 `Browse`，然后 `Browse...` 按钮，然后导航到 .dll，最后单击 `Add` 和 `OK`。您还需要保存对项目的更改，`File->Save All` 应该可以做到这一点。

![image](https://github.com/user-attachments/assets/5c5ebe45-b0bd-453b-94b0-21e8250c7cda)
![image](https://github.com/user-attachments/assets/33db3383-a451-44d7-a349-d959b41b8a1f)

## Visual Studio Code 步骤
Visual Studio 步骤中的所有信息（关于如何实际添加引用的信息除外）也适用于 Visual Studio Code。要在 Visual Studio Code 中添加 .dll 引用，首先导航到解决方案资源管理器，然后单击 `.csproj` 文件条目。这将打开 `csproj` 文件进行编辑。在 `.csproj` 文件文本中，您应该看到嵌套在 `Project` 部分下的 `ItemGroup` 部分。如果没有，您可以手动输入如下所示的内容，或者使用 Mod Sources 菜单中的 `Upgrade .csproj file` 按钮重新生成 `.csproj` 文件。

![NVIDIA_Share_2025-06-10_12-11-04](https://github.com/user-attachments/assets/f6680bf7-6c6c-4d4f-8217-443e75ef90cd)

接下来，我们需要添加一个 `Reference` 部分。我们现在需要 .dll 文件的文件名，如果您不确定文件名，请在文件浏览器中打开 `ModSources\ModAssemblies` 文件夹并找到 .dll 文件。接下来，键入以下内容，适当替换模组名称、版本和文件路径：
```xml
	  <Reference Include="CheatSheet">
	    <HintPath>..\ModAssemblies\CheatSheet_v0.7.4.7.dll</HintPath>
	  </Reference>
```
最终文件应该看起来像这样。一定要保存此文件，然后通过尝试访问模组中的类并查看自动完成是否正常工作来验证它是否有效。您还可以通过查看自动完成是否会建议命名空间来验证。

![image](https://github.com/user-attachments/assets/104b81e3-ffb6-4e39-855f-90f045285eed)

![image](https://github.com/user-attachments/assets/86f5717f-2fb5-4917-a09e-97e8c4471d60)

![image](https://github.com/user-attachments/assets/abab2ca8-f44b-41c7-b030-f914866cd58f)

您还可以展开 `Dependencies->Assemblies` 部分并查找模组名称。它应该在那里，但如果有一个 ⚠️ 图标，则表示有需要修复的拼写错误。如果它在那里没有该图标，则应该正常工作。

![image](https://github.com/user-attachments/assets/39978c2c-c664-485b-9085-c61eac3ec735)

## 模组文档
如果您引用的模组包含文档，您在处理这些类时也应该能够看到这些文档。阅读[这个](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#mod-documentation)以获取更多信息。

# 弱引用，也就是 weakReferences（专家级）

弱引用具有与强引用相同的能力，但不受引用模组必须启用才能工作的限制。但是，它们需要更仔细的编程。过程基本相同，但不是在 `build.txt` 中使用 "modReferences = ExampleMod" 行，而是使用 "weakReferences = ExampleMod" 行。您可能还需要指定[最低版本](#strong-and-weak-references)。

弱引用需要更仔细的编程。例如，如果您有代码 `ExampleMod.Common.Systems.DownedBossSystem.downedMinionBoss = true;` 在一个被调用的方法中，但 ExampleMod 未加载，游戏将崩溃。使用弱引用时，您必须确保变量和类在 .Net 运行时运行 c# 代码时永远不会看到可能未加载的内容。一些例子：
```cs
npcShop.Add<ExampleWings>();

// now for a cross-content item
if (ModLoader.HasMod("ThoriumMod")) {
	if (ThoriumMod.ThoriumWorld.downedStarScouter) {
		npcShop.Add(ItemID.Gel);
	}
}
```
在此示例中，游戏将崩溃。您可能认为对 `thoriumLoaded` 为 `true` 的检查会阻止游戏崩溃，但发生的事情是 .Net 运行时会在调用方法时尝试理解此方法中提到的所有代码，并且由于它无法理解 `ThoriumMod.ThoriumWorld.downedStarScouter`，它将崩溃。

这是一个确实有效的解决方案，将可能无法解析的代码移动到属性中，有效地防止运行时在那个模组实际加载之前不得不了解 `ThoriumMod.ThoriumWorld.downedStarScouter`：
```cs
if (ModLoader.HasMod("ThoriumMod"))
{
    if (ThoriumModDownedStarScouter)
    {
        npcShop.Add(ItemID.Gel);
    }
}

[JITWhenModsEnabled("ThoriumMod")]
public bool ThoriumModDownedStarScouter => ThoriumMod.ThoriumWorld.downedStarScouter;
```

`JITWhenModsEnabled` 属性是必需的，在下面的 [JIT 注意事项部分](#jit-considerations) 中有解释。

弱引用很难，但可以做得很巧妙。但是，许多事情最好用 `Mod.Call` 处理。我希望本指南能帮助您选择跨模组内容的最佳方法。在这个具体示例中，ThoriumMod 提供了在其 [wiki](https://thoriummod.wiki.gg/wiki/Mod_Calls#GetDownedBoss) 上记录的 `Mod.Call`。

## 继承
当从被引用模组中的类继承时，必须使用 [`ExtendsFromMod` 属性](#extendsfrommod) 来注释类，以防止在引用的模组未加载时崩溃。

## Visual Studio 步骤
弱引用也需要 .dll 文件才能在 Visual Studio 中正常工作。阅读上面强引用部分中的 [Visual Studio 步骤](#visual-studio-steps) 说明。

## 测试
您可能错误地认为您的弱引用正在工作，因为您禁用了弱引用的模组并且您的模组仍然加载。这是假阳性。为了正确测试弱引用，您**必须**禁用被引用的模组，然后关闭并重新打开 tModLoader。

## 建议
最佳实践是将所有直接使用 weakReference（可能是可选的）的代码放在一个单独的类中。您可以使用 `[JITWhenModsEnabled("ReferencedModName")]` 来注释该类，以便整个类被排除在 JIT 过程之外。这可以简化逻辑并确保 JIT（即时）编译器在引用不可用时不必解析无法解析的引用，从而防止崩溃。[ItemChecklist's MagicStorageIntegration.cs](https://github.com/JavidPack/ItemChecklist/blob/1.4/MagicStorageIntegration.cs) 展示了这种方法。另请注意，此类的方法仅在 MagicStorage 加载时才会被调用。另一种用于自包含逻辑的方法是使用 `ExtendsFromMod` 的 `ModSystem` 类，以防止在引用的模组不存在时自动加载 `ModSystem`。使用 `JITWhenModsEnabled` 并重写 `IsLoadingEnabled` 和检查 `ModLoader.HasMod` 也是一种选择。

# 无引用，也就是反射（专家级）

使用[反射](https://github.com/tModLoader/tModLoader/wiki/Reflection)进行跨模组并不理想。一方面，反射依赖于字符串来访问目标模组的类和字段。随着目标模组的更新和变化，您的模组也会失效，除非您进行防御性编程。这个方案不协作，效率低下。这是一个糟糕的选择，但在避免依赖关系时很有用，并且在访问其他模组的私有成员时是必要的。如果您与其他模组的作者合作，让他们开放您想用您的模组进行的修改，那就更好了。使用 Github 一起工作是您最好的选择！

# JIT 注意事项
tModLoader 在加载时检查每个模组的所有类和方法。这将导致对弱引用其他模组成员的模组出现问题。为了避免这个问题，模组制作者需要用特殊的 `Attributes` 来注释成员。

### JITWhenModsEnabled
`[JITWhenModsEnabled(...)]` 在加载时 JIT 检查期间排除类/方法/属性，防止加载崩溃。[JIT Exception 弱引用](https://github.com/tModLoader/tModLoader/wiki/JIT-Exception#weak-references)页面有更多关于此主题的信息。

### ExtendsFromMod
如果您正在从您弱引用的模组中的基类继承，您可以使用 `[ExtendsFromMod(...)]` 属性来指定当模组检查其他模组时，不应自动加载或考虑该模组。示例：

```cs
[ExtendsFromMod("ExampleMod")]
public class ExampleItemSubclass : ExampleItem
{
}
```
如果您的 `ModX` 类依赖于另一个模组被加载，但不一定要从该模组中的类继承，您仍然可以使用 `ExtendsFromMod` 来防止它在引用的模组不存在时被加载。

如果您在使用反射迭代程序集中其他位置的类型时遇到问题，请确保使用 tModLoader 的 `AssemblyManager.GetLoadableTypes` 而不是直接使用 `someAssembly.GetTypes`。这可以阻止您的代码尝试加载可能未加载的类型。

# 最低引用版本
限制被引用模组到最低版本很有用。例如，如果被引用模组中您希望与之交互的内容是在 v2.0 中添加的，那么我们只会在用户加载该模组的 v2.0 或更高版本时才尝试访问该内容。尝试访问被引用模组中不存在的内容或类将导致加载错误或错误。

## 强引用和弱引用
对于强引用和弱引用，我们可以在 `build.txt` 中的 `modReferences` 或 `weakReferences` 行中添加最低版本。我们只需在模组名称后添加 `@` 然后是版本号。例如，"weakReferences = ExampleMod@2.0" 将指定 ExampleMod 必须是 v2.0 或更高版本。此版本限定符可防止您的模组与被引用模组的旧版本一起加载。每当您的模组引用被引用模组新版本中添加的功能时，请务必更新此值。

## Mod.Version
我们可以在代码中检查被引用模组的版本以获得更动态的行为。这对于手动为不使用强或弱依赖方法的模组实现最低版本检查特别有用。`Mod.Call` 有时也会在模组更新中更新，因此检查被引用模组的版本可确保您不会在用户加载该模组的旧版本时尝试使用不支持的 `Mod.Call`。

```cs
if (ModLoader.TryGetMod("ExampleMod", out Mod exampleMod)) {
	if (exampleMod.Version >= new Version(2, 0)) {
		// ExampleMod 2.0 specific logic here.
	}
}
```

# 依赖模组注意事项
作为依赖模组的模组作者应该记住几件事。如果您要制作库模组或期望其他模组扩展的内容模组，请阅读本节。

## 访问修饰符
声明适当的访问修饰符是准备模组作为其他模组的依赖使用的重要步骤。如果你不熟悉 private 和 public 等访问修饰符的含义，请阅读[访问修饰符（C# 编程指南）](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)来熟悉它。

模组应该使用 `public` 向其他模组公开类和方法，同时对不打算被访问的内容使用 `internal` 和 `private`。您还应该尝试保持 `public` 的类、字段和方法名称在更新中不变。`Type` 和参数也应该保持不变。原因是任何依赖您的模组的模组如果使用了任何这些更改的内容都将失效，您的用户将感到失望，因为他们再也无法继续他们的游戏了。有些人可能将这些公共成员称为模组的"公共 API"。另一方面，在更新中更改 `internal` 和 `private` 成员是完全可以的，因为没有模组应该直接访问它们，除非它们故意绕过您的模组运作方式的设计。

### 库模组
在制作库模组时，您只需要将那些其他模组应该使用的方法公开为 `public`。这使得其他作者更容易知道如何使用您的库。

如果您需要更改方法的参数，请考虑保留当前方法并用 `[Obsolete("Use X instead", error: true)]` 来注释它。`error` 参数为 `true` 将迫使依赖模组在下一次构建模组时更新到新方法，但只要旧方法仍然具有功能，模组本身仍然可以工作。tModLoader 也使用了这种方法，请参阅 [tModLoader 风格指南](https://github.com/tModLoader/tModLoader/wiki/tModLoader-Style-Guide#adding-a-parameter-to-a-hook)以获取更多关于将方法标记为已弃用的信息。

### 内容模组
对于内容模组，依赖模组可能希望直接访问所有内容类，例如 `ModContent.ItemType<YourModItem>()`。这将使使用您的物品制作配方和重用弹射物以及模组中的其他内容变得更加容易。因此，我们建议将内容类声明为 `public`。

## Mod.Version
`Mod.Version` 的目的是表明自上一次发布以来发生了多大的"变化"。如果您的模组需要彻底重新设计，它很可能应该伴随着 `Version` 的 `Major` 数字的更改。例如，`v1.3` 变为 `v2.0` 将表明模组发生了重大变化，可能有了新的"公共 API"。

## ModType
`ModType` 可以大大简化从其他模组加载内容。[自定义 ModType 示例 pull request](https://github.com/tModLoader/tModLoader/pull/4611) 是学习如何实现自定义 `ModType` 的好资源。

## Mod.Call
公开 `Mod.Call` API 很有用，请参阅[Call，也就是 Mod.Call（中级）部分](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content#call-aka-modcall-intermediate)中的信息。

## 文档
直接在模组中包含文档意味着模组制作者将在他们的 IDE 中看到该文档。请务必阅读[此 wiki 部分](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#mod-documentation)以了解如何在模组中包含文档。

## Wiki
面向开发者的 wiki 很有用，特别是对于库模组。Wiki 可以包括使用 `Mod.Call`、继承基类以及其他内容的信息。[Boss Checklist Github wiki](https://github.com/JavidPack/BossChecklist/wiki) 就是这样一个 wiki 的好例子。

## 继承
如果您公开了一个打算让其他模组继承的类，有几件小事需要注意，特别是如果您有其他模组可能继承的 `abstract` 类：

### ModType.Mod
`ModType.Mod` 指的是类所属的 `Mod`，当其他模组从您的模组中的内容继承时，这不一定是您的模组！

例如，调用 `Mod.GetLocalization("CommonMessage")` 将返回当在您模组的内容上调用时的 `Mods.MyMod.CommonMessage` 本地化条目，但当在从您的类继承的其他模组的内容上调用时将返回 `Mods.OtherMod.CommonMessage` 本地化条目。这种行为在某些情况下是需要的，在其他情况下是不需要的。如果您不是故意的，请务必在这些情况下使用 `ModContent.GetInstance<MyMod>().GetLocalization("CommonMessage")`。

作为依赖模组的作者，确保您的代码为您模组中的内容以及从您的类继承的其他模组的内容按预期工作是您的责任。
