# 什么是 tModCodeAssist？
`tModCodeAssist` 是一个[自定义代码分析器](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/overview)，为模组项目提供代码修复。它包含在 tModLoader 中并自动启用。为了利用 `tModCodeAssist`，请确保您的 [IDE](Why-Use-an-IDE.md) 根据我们的指南正确配置。

`tModCodeAssist` 的目的是帮助模组制作者创建可读的代码。例如，在遵循[原版代码适配指南](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption)时，模组制作者可能会在源代码中得到 `int dust = Dust.NewDust(projectilePosition, 1, 1, 175, 0f, 0f, 0, default(Color), 1f);`。数字 `175` 对应于特定的灰尘，但仅从数字本身不可能知道是哪种灰尘。`tModCodeAssist` 不是在 `DustID.cs` 中查找数字，而是会建议一个代码修复，自动将 `175` 更改为 `DustID.SpectreStaff`，模组制作者可以选择将其应用到他们的代码中。

另一个目的是防止模组制作者混淆 ID。例如，新手模组制作者在为其物品制作配方时经常混淆 `ItemID` 和 `TileID`，导致模组无法工作且无法找出原因。`tModCodeAssist` 检测到这些误用的 ID 值，并向模组制作者报告错误。

所有这些问题都将在 IDE 的 `Error List` 选项卡中报告：
![Error List tab](https://github.com/user-attachments/assets/34422bb7-81db-4514-9b67-9be15fd570e9)

## 应用代码修复
要使用代码修复建议，只需悬停在绿色下划线的代码上，然后点击"Show potential fixes"，然后点击适当的建议。（或者，将光标放置，键入 `ctrl-.` 热键，然后点击适当的建议）这的工作方式与 [IDE 提供的代码修复](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#show-potential-fixes)完全相同。

<blockquote>

### **Show potential fixes:**
![Show potential fixes](https://github.com/user-attachments/assets/7e5c9397-8593-46ea-a820-28b2821e97e1)

### **Change magic number:**
![Change magic number](https://github.com/user-attachments/assets/698e3353-1379-488e-b719-390193a5eee1)
</blockquote>

在上面的示例中，`Item.rare = 3;` 被更改为 `Item.rare = ItemRarityID.Orange;`。您仍然可以悬停在 `ItemRarityID.Orange` 上查看它的值为 `3`。

## 对整个文件或项目应用代码修复

点击"Fix all occurrences in: Project"选项是对整个模组一次应用这些代码修复的绝佳选择。

<blockquote>

![Fix all occurrences](https://github.com/user-attachments/assets/26d03366-a946-4925-adb4-b8f488ba195d)
</blockquote>

# 诊断
以下是 tModCodeAssist 当前提供的诊断和代码修复。您可以通过点击 IDE 中 Error List 面板的 Code 列的超链接轻松导航到这些条目：

![](https://github.com/user-attachments/assets/0b23bd60-a5f6-4b38-a741-a24c26f42744)

## ChangeMagicNumberToID
严重性：⚠️ 警告

此诊断将警告["魔法数字"](https://en.wikipedia.org/wiki/Magic_number_(programming))并建议将它们更改为相应 ID 类中的适当值。例如，`Item.useStyle = 1;` 将被更改为 `Item.useStyle = ItemUseStyleID.Swing;`。由于模组制作者处理的是反编译的泰拉瑞亚，我们在[适配原版代码](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption)时通常会得到原始数字。此诊断和代码修复将为模组制作者恢复这些值的原始含义，使代码更具可读性。[PR](https://github.com/tModLoader/tModLoader/pull/4727)。

## BadIDType
严重性：⛔ 错误

此诊断将在错误的地方使用错误的 ID 类时报错。例如，`recipe.AddTile(ItemID.WorkBench)` 会报错，因为模组制作者错误地将 `ItemID` 值用于期望 `TileID` 的方法。由于 ID 值只是数字，如果没有错误，此代码将导致礼物瓷砖成为制作站而不是工作台，因为 `ItemID.WorkBench` 的值为 36，这与 `TileID.Presents` 相同。

要修复此问题，请更改 ID 类，让[自动完成/智能感知](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#autocomplete--intellisense)帮助您找到正确的条目。如果您无法在正确的 ID 类中找到该条目，请查阅[原版内容 ID](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Content-IDs)可能会有所帮助。有几个 ID 类条目与英文名称不匹配，因此您可能需要查找它们。另请注意，瓷砖可以在同一个 TileID 中有多种样式，例如 `TileID.MythrilAnvil` 对应秘银砧和山铜砧。[PR](https://github.com/tModLoader/tModLoader/pull/4742)。

## CommonCollisionName
严重性：⛔ 错误

如果命名空间或类名与几个常用泰拉瑞亚类之一匹配，此诊断将报错。例如，尝试创建名为 `MyMod.Item` 的命名空间会报错。当前保留的名称包括："Main"、"Mod"、"Player"、"Item"、"NPC"、"Projectile"、"Gore"、"Dust"、"Entity"、"Liquid"、"Mount"、"Tile"、"Recipe"、"ModPlayer"、"ModItem"、"ModNPC"、"ModProjectile"、"ModGore"、"ModDust"、"ModType"、"ModMount"、"ModTile" 和 "ModWall"。新手模组制作者经常创建 `Item` 命名空间，这导致编译器无法调和名称冲突的情况，并对像 `Item.value = Item.buyPrice(gold: 12);` 这样的代码行抛出错误。

我们建议模组制作者使用复数名词（如 `Items` 或 `Projectiles`）命名他们的命名空间，以防止与泰拉瑞亚类（通常是单数名词）的命名空间冲突。[PR](https://github.com/tModLoader/tModLoader/pull/4746)。

## SimplifyUnifiedRandom
严重性：ℹ️ 信息

此诊断建议将 `if (Main.rand.Next(3) == 0)` 更改为 `if (Main.rand.NextBool(3))`。这些逻辑上相同，但 `NextBool` 方法更能表达代码意图，并帮助新手模组制作者避免可能不理解原始代码在做什么的错误。[PR](https://github.com/tModLoader/tModLoader/pull/4727)。

## SimplifyLocalPlayer
严重性：ℹ️ 信息

此诊断建议将 `Main.player[Main.myPlayer]` 更改为 `Main.LocalPlayer`。这两个逻辑上相同，但 `Main.LocalPlayer` 更能表达代码意图。[PR](https://github.com/tModLoader/tModLoader/pull/4748)。

# 配置代码修复
在极少数情况下，模组制作者可能不需要代码修复建议。

<details><summary>详情</summary><blockquote>

模组制作者可以使用他们的 IDE 配置代码修复。可以使用提供的选项将代码修复抑制到不同的范围。

![Suppress](https://github.com/user-attachments/assets/80a61eb0-f147-43d9-8bdd-44fe17b4382a)

</blockquote></details>

# 为 tModCodeAssist 贡献新功能
我们欢迎社区为 `tModCodeAssist` 添加新功能的贡献。编写代码分析器代码与编写"普通" C# 代码完全不同，这个过程可能相当艰巨，但有简单的模板可以让模组制作者轻松地为现有代码修复添加功能。

## 添加到 `ChangeMagicNumberToID` 分析器
本节将教如何为 `tModCodeAssist` 的 `ChangeMagicNumberToID` 代码修复功能添加内容。这意味着如果某个字段或方法参数当前未被修复为 ID 类条目，我们可以添加它，然后它将按预期工作。本指南假设您已经知道如何[设置 tModLoader 源代码](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-contributors#getting-the-tmodloader-code-for-the-first-time)并提出拉取请求。如果不是这种情况，请参阅[tModLoader 贡献者指南](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-contributors)。

我们将以[这个拉取请求](https://github.com/tModLoader/tModLoader/pull/4747)作为本节其余部分的指南。打开"Files Changed"选项卡并跟着做：

1. 如果您想添加支持的 ID 类尚未包含在 `tModCodeAssist.csproj` 中，请按照示例中所示的方式手动编辑文件来添加它。
2. 通过遵循示例在 `MagicNumberBindings.cs` 中添加"绑定"。"绑定"只是字段（或方法参数）与其应使用的 ID 类之间的映射。如果 ID 类没有 `IdDictionary Search` 字段，您将需要提供与 ID 类中 `const` 字段的 `Type` 匹配的 `idType` 参数。否则，您不应提供该参数。
3. 在 `ChangeMagicNumberToIDUnitTest.cs` 中的现有测试添加新的代码修复演示行。在这个文件中，您会在每个测试中看到两个版本的代码片段。第一个是原始代码，第二个是应用此分析器的代码修复后的代码。添加一行来测试您的新绑定通常就足够了。请注意原始代码片段中的特殊 `[|number|]` 语法，表示应应用代码修复的位置。
4. 运行测试以确保它们工作：
    <details><summary>展开查看说明 </summary><blockquote>

    在解决方案资源管理器中右键点击 `tModCodeAssist.Tests` 项目，然后点击 `Run Tests`。
    ![Run Tests Button](https://github.com/user-attachments/assets/628dee98-c215-488f-bf43-e56c0c39a061)

    这将打开 `Test Explorer` 窗口。验证所有测试都通过了。您可能想故意破坏测试作为健全性检查，以确认它也按预期失败。
    ![Test Explorer window](https://github.com/user-attachments/assets/cdf97bde-1541-4222-a572-c9e20b4d3002)
    </blockquote></details>

5. 如果 ExampleMod 有任何缺失的 ID 值，我们希望修复这些并将其添加到我们的拉取请求中。为此，首先构建 `Terraria` 项目。这应该会导致 ExampleMod 看到更新后的 `tModCodeAssist.dll` 文件。您现在应该会看到修复建议。
6. 像往常一样提出拉取请求。

## 为 tModCodeAssist 添加全新功能
对于全新功能，您需要更熟悉代码分析器才能成功。[教程：编写您的第一个分析器和代码修复](https://learn.microsoft.com/en-us/dotnet/csharp/roslyn-sdk/tutorials/how-to-write-csharp-analyzer-code-fix)官方指南是开始学习的好地方。将您在该教程中学到的内容与 `tModCodeAssist` 代码进行比较，以更熟悉它的工作原理。另一个好的资源是 [RoslynQuoter](https://roslynquoter.azurewebsites.net/)，在这个网站上您可以将 C# 代码转换为相应的语法树表示。这将帮助您理解代码是如何表示的。同样，Visual Studio 有一个内置的语法可视化工具，可从 `View->Other Windows->Syntax Visualizer` 访问。您可以将其用于相同目的。如果语法可视化工具窗口丢失，通常意味着某些必需的 Visual Studio 组件未安装。您可以查看官方文档[使用 Visual Studio 中的 Roslyn 语法可视化工具浏览代码](https://learn.microsoft.com/en-us/dotnet/csharp/roslyn-sdk/syntax-visualizer?tabs=csharp)以获取安装步骤。

如果您有任何进一步的问题或想讨论，请来到[Discord 服务器](https://discord.gg/tmodloader)，那里进行开发，我们会很乐意指导您。
