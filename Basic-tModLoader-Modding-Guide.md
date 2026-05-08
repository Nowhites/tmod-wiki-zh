***
本指南已更新至1.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Modding-Guide/9c8ec569fb957e9ad34edb565e653a805811b5a6)
***

首先您必须决定要使用什么程序来编写代码。本质上有两种选择：**文本编辑器**或**IDE**。如果您完全不了解二进制逻辑和设计的新手，可能需要从文本编辑器开始，以掌握基础知识并能够在不感到被IDE界面和工具淹没的情况下处理简单代码。对于大多数其他情况，您需要使用的是**集成开发环境**。IDE是为特定编程语言（在我们的例子中是C#）设置便捷界面的软件。IDE可以做文本编辑器能做的任何事情，因为文本编辑器只是IDE的一个组件。

您还需要按照[这些说明](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-developers#net-sdk)安装".NET 8.0 SDK"。

本指南将让您熟悉tModLoader模组制作，并帮助您制作第一个模组。请阅读并遵循[基础先决条件](https://github.com/tModLoader/tModLoader/wiki/Basic-Prerequisites)

# 您的第一个模组
首先，我们将制作一个非常简单的模组，以让您熟悉tModLoader如何为模组制作。
## 生成模组框架
首先，我们将使用tModLoader生成一个基本的模组框架。打开tModLoader，然后打开"创意工坊"菜单，然后打开"开发模组"菜单，然后点击"创建模组"按钮。现在，填写输入框。我建议分别是`TutorialMod`、`Tutorial Mod`、`NewbieModder`和`TutorialSword`。最后，点击"创建"。如果出现消息，请修复问题，否则您将被带回模组源菜单。

> [!注意]
> 如果您要制作一个打算在创意工坊上发布的模组，它需要有一个唯一的`ModName`而不是`TutorialMod`。它需要一个在创意工坊上未被任何其他模组使用的名称，否则您将无法发布它。如果需要重命名现有模组，请参阅[重命名模组](https://github.com/tModLoader/tModLoader/wiki/Workshop#renaming-a-mod)。

![](https://i.imgur.com/X5x90vD.png)

## 模组框架内容
点击"开发模组"菜单上的"打开源"按钮，打开`ModSources`文件夹。您也可以通过导航到`Documents\My Games\Terraria\tModLoader\ModSources\`在文件浏览器中打开`ModSources`文件夹。接下来，打开模组的文件夹，如果您在跟随指南，文件夹应该叫做`TutorialMod`。此文件夹包含与此模组相关的所有代码。目前，应该有模组框架生成器添加的9个基本文件。以下是对每个文件的说明：
1. **[ModName].cs** - 这是`Mod`类。它是任何模组的中央文件。每个模组中只能有一个`Mod`类。对于简单的模组，这个文件将非常稀疏，但在这个类中可以发生各种全局事情。
2. **description.txt** - 包含模组描述的文本。点击模组菜单中的`更多信息`按钮可在游戏中查看。
3. **description_workshop.txt** - 包含在此模组的[Steam创意工坊网站](https://steamcommunity.com/app/1281930/workshop/)上显示的描述。此文件的内容可以使用额外的[BBCode格式化](https://steamcommunity.com/comment/ForumTopic/formattinghelp)进行格式化，以制作令人印象深刻的模组首页并吸引用户对其的兴趣。BBCode格式化可能实现的一个示例是[The Stars Above](https://steamcommunity.com/sharedfiles/filedetails/?id=2563862309)模组的创意工坊页面。
4. **build.txt** - 包含模组的版本、作者和显示名称。可以包含其他[值](https://github.com/tModLoader/tModLoader/wiki/build.txt)。必要文件。
5. **icon.png** - 将在游戏中显示的80x80图标。您可以为Steam创意工坊网站创建更详细或更高分辨率的`icon_workshop.png`版本。该文件最大可为512x512。
6. **icon_small.png** - 将在博物志模组源过滤器和模组配置列表中显示的30x30图标。为了与现有博物志图标的外观匹配，您可以以40%不透明度在右下角添加黑色投影。请参阅[ExampleMod的icon_small.png](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/icon_small.png)作为示例。如果未编辑，将不会使用它，博物志中将显示`?`图标。
7. **[ModName].csproj** - 用于Visual Studio的项目文件，用于调试您的模组。调试非常有用，但需要一些学习，不要删除它。
8. **Properties/launchSettings.json** - 与`[ModName].csproj`相关，包含用于调试的tModLoader文件路径。不要删除，您稍后获得经验时会需要它。
9. **Content/Items/[ItemName].cs** - 一个简单的剑物品。用作学习制作其他`ModItem`类的示例。
10. **Content/Items/[ItemName].png** - 对应的精灵图。
11. **Localization/en-US_Mods.[ModName].hjson** - 包含模组内容的英文文本。它目前包含生成的剑的显示名称和工具提示。此文件将自动更新您添加到模组的新内容的条目。请参阅[本地化wiki页面](https://github.com/tModLoader/tModLoader/wiki/Localization)了解有关本地化以及如何支持其他语言的更多信息。

![](https://i.imgur.com/tTmp0bq.png)

## 学习如何构建模组
我们现在有一个可以构建的现成模组。启动tModLoader并打开"创意工坊"菜单，然后打开"开发模组"菜单。您应该会看到您刚创建的模组的条目。现在点击"构建+重新加载"按钮。如果没有收到任何错误，您的模组将显示在模组菜单中！

![](https://i.imgur.com/unoLL8h.png)

现在进入游戏，制作一个工作台并挖掘10个泥土方块，您应该会看到您可以制作一把新剑！

![](http://i.imgur.com/UQb3tXq.png)

哇！惊人。但50伤害不够。我们现在将进行我们的第一次实际编程。在Notepad++中打开`ModSources\[ModName]\Content\Items\TutorialSword.cs`文件。找到带有`Item.damage = 50;`的行，将50改为100。现在，保存文件！记住不要弄乱您在基础先决条件课程中学到的语法。

接下来，同时在Notepad++中打开`ModSources\[ModName]\Localization\en-US_Mods.[ModName].hjson`文件。这是设置显示给用户的英文文本（如物品名称和工具提示）的地方。将`DisplayName: Tutorial Sword`更改为`DisplayName: My Tutorial Sword`，然后将`Tooltip`条目更改为`Tooltip: My first sword`，最后保存该文件。请注意，默认模组中的`Tooltip`条目使用多行条目（在此处阅读更多关于[多行语法](https://github.com/tModLoader/tModLoader/wiki/Localization#multiline)），这就是为什么它看起来与`DisplayName`条目如此不同的原因。因为我们只需要1行，所以整个条目（包括开头的`'''`和结尾的`'''`）需要替换为`My first sword`。

接下来，再次进入游戏，重新构建并重新加载模组，然后再次获取剑。您应该会看到新的伤害、新的物品名称和新的工具提示。

最后，您构建的模组被打包成一个名为`[ModName]`的`.tmod`文件，位于`tModLoader\Mods`中。如果您想让您的朋友玩您的模组但还不想发布，您可以发送他们此文件，让他们将其放入同一文件夹。（或者您可以发布到创意工坊并将其设置为"仅限好友"以保持一定程度的私密性。这种方法将允许模组在您发布更新时自动为您的朋友更新。）

## 稍微实验一下

您现在可以更多地实验，更改一些其他物品值。记住，您必须保存更改，然后运行`构建和重新加载`才能在游戏中看到更改。

## 下一步

现在您有了一个带有简单剑的简单模组，是时候拓展和学习其他技能了。慢慢来，尝试学习您想学的东西。如果您正在论坛或[Discord](https://discord.gg/tmodloader)上向其他模组制作者寻求帮助，最好用原版术语来表达您的问题。例如，如果您好奇熔岩之怒如何将木箭变为烈焰箭，明智的做法是问"熔岩之怒如何在仅使用木箭作为弹药时改变它发射的弹幕？"以这种方式表达您的问题是最有效的。

继续阅读以了解有关tModLoader以及如何做得更好的更多信息。

# 我如何在模组中引用其他文件中的内容？

模组制作者首先要做的其中一件事是从自定义武器发射自定义弹幕，或任何其他需要模组中的类引用其他类的类似行为。为此，我们使用`ModContent.SomethingType`方法，其中"Something"替换为"Item"、"Projectile"、"NPC"等。例如，如果您想让您的`ModItem`发射特定的`ModProjectile`，您将在`ModItem.SetDefaults`方法中编写`Item.shoot = ModContent.ProjectileType<MyProjectileClassName>();`。相反，从原始游戏发射弹幕将是`Item.shoot = ProjectileID.WoodenArrowFriendly;`或类似的。

请注意，当尝试引用模组类时，您需要添加引用该类命名空间的"using语句"。例如，如果您在类中编写`ModContent.ProjectileType<MyProjectile>()`，而`MyProjectile`类位于`MyMod.Content.Projectiles`命名空间中，您需要在.cs文件顶部添加`using MyMod.Content.Projectiles;`。一个好的IDE将[自动化这个过程](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#show-potential-fixes)为您服务。

## 更多引用模组内容的示例
以下是更多引用模组内容的示例。在所有这些情况中，我们执行相同的过程，通过适当的方法访问模组内容ID，并将其用于期望内容ID的地方。我们使用此值作为方法参数或分配变量。在所有这些情况中，也会有适当的using语句。不期望您理解下面的代码，它只是显示各种示例以让您了解模组内容如何在代码中被访问和使用。

```cs
Item.DefaultToStaff(ModContent.ProjectileType<Projectiles.SparklingBall>(), 16, 25, 12);
Item.shoot = ModContent.ProjectileType<ExamplePiercingProjectile>();
Projectile.NewProjectile(Projectile.GetSource_FromThis(), Projectile.Center, beamVelocity, ModContent.ProjectileType<ExampleLastPrismBeam>(), damage, knockback, Projectile.owner);
Banner = ModContent.NPCType<ExampleWormHead>();
itemLoot.Add(ItemDropRule.Common(ModContent.ItemType<ExampleItem>(), 1, 12, 16));
shop.Add<ExampleRocket>(Condition.NpcIsPresent(ModContent.NPCType<ExamplePerson>()));
Item.DefaultToPlaceableTile(ModContent.TileType<Tiles.ExampleBar>());
TileID.Sets.CloseDoorID[Type] = ModContent.TileType<ExampleDoorClosed>();
Item.createTile = ModContent.TileType<ExampleCritterCage>();
```

# tModLoader的"hook"如何工作？

首先，它们严格来说不是hook。我们简单称它们为hook，因为这样很容易。'hook'是您作为模组制作者可以使用的一个函数。可用的hook取决于您正在使用的类。例如您的`Mod`类有一个**[Load Hook](https://docs.tmodloader.net/docs/stable/class_mod.html#ae174f640b4281fa62e475400aad4dad6)**，这是一个特定于`Mod`类的函数。这些函数都是`virtual`的，这意味着函数有基本实现，但如果需要可以由模组制作者覆盖。这意味着当您想使用hook时，您_覆盖_它，这就是为什么`virtual`这个词被替换为`override`。由于_virtual_（部分）实现被覆盖，该实现将丢失。要查看hook的标准实现，您应该查看**[源文档](http://tmodloader.github.io/tModLoader/)**。

Hook基本上是模组运行代码的机会。每个hook在游戏的更新循环中的适当时间被"调用"。例如，有一个名为`ModItem.Shoot`的hook，每次游戏确定武器物品应该发射弹幕时会调用此hook。模组制作者通过编写代码来"覆盖""hook"，当武器物品发射弹幕时该代码将被执行。

## Hook使用示例
假设您想在物品在物品栏中时执行某些操作，类似于手机物品的工作方式。首先，我们打开源文档并搜索[ModItem文档](https://docs.tmodloader.net/docs/stable/class_mod_item.html)。找到此页面后，使用`ctrl-f`搜索与我们要查找的内容相关的页面。在这种情况下，搜索`inventory`并阅读结果，您可能会找到`UpdateInventory`方法。文档告诉我们这个"hook"让我们可以在物品在物品栏中时使事情发生，这正是我们想要的。要使用此hook，我们将方法签名复制到我们的ModItem类中。
```cs
virtual void Terraria.ModLoader.ModItem.UpdateInventory	(Player player)
```
如果您记得上面，我们都必须将"virtual"更改为"override"。我们还必须记住将方法名精简。这个代码在ModItem类中，所以我们可以去掉"Terraria.ModLoader.ModItem."。最后，确保告诉计算机这个`Player`类是什么。我们通过在.cs文件顶部添加`using Terraria;`来实现。添加开闭花括号后，我们终于有了准备好编写代码的hook：
```cs
override void UpdateInventory (Player player)
{
    // 我们在这里编写代码。
}
```
通过使用[Visual Studio](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#override)，覆盖hook的过程可以大大简化。
现在我们已经在`ModItem`类中有了一个hook，我们可以在其中添加代码。我们编写的代码将取决于我们希望实现的效果。ExampleMod和[高级原版代码适配](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption)指南可以帮助找到类似的代码片段进行实验。

# 索引和类型
有两个重要概念都对应于整数。这两种整数的用法可能会被新模组制作者无意中混合，导致代码可以编译但有bug。

在Terraria中，所有内容都有一个称为`Type`的唯一标识符。这是一个整数，让游戏可以唯一识别不同的内容位。例如，[[Shuriken弹幕]的类型是数字`3`。我们可以使用弹幕的`Type`来运行应该只影响该类型弹幕的代码。例如，如果我们使用`GlobalProjectile.OnHitNPC` hook，我们可以检查`if(projectile.type == ProjectileID.Shuriken)`，然后运行一些特定于手里剑的代码，例如向npc施加debuff。物品、NPC、弹幕、灰尘以及Terraria中更多种类的内容都使用`Type`标识符来识别内容的各种变体。

第二个概念是`Index`。在Terraria中，游戏使用各种数组来保存世界中所有活动内容。例如，`Main.projectile[]`数组保存当前游戏世界中所有弹幕的所有实例。每个`Projectile`通过`whoAmI`字段知道自己在该数组中的索引。

`Type`和`Index`都是整数，导致新模组制作者可能犯一个或另一个使用不当的大错误。例如，模组制作者可能犯错误认为`Projectile projectile = Main.projectile[ProjectileID.Shuriken]`会检索活动的手里剑弹幕，但它不会，它会返回该索引处的任何弹幕。随着模组制作者浏览不同的指南和示例，他们将看到`Type`和`Index`的正确用法。

此图说明了这一点，展示`Type`和`Index`如何是单独的概念，同时展示它们如何存储在数组中：
![ProjectileArrayExplaination](https://user-images.githubusercontent.com/4522492/184989136-c83e4bcf-18a1-4cb5-b798-60b190be5591.png)


# 模组类如何设置？

tModLoader提供了许多可用于创建自己的模组内容的类。您将通过使用所谓的`类派生`或`类继承`来基于这些类创建您自己的类。简而言之，这意味着您的类将使用我们的一个作为基类。例如，您的物品将基于ModItem：`MyItemClass : ModItem`，其中`: ModItem`表示它继承自Terraria.ModLoader命名空间中存在的ModItem类。（如果您来自Java，这与`extends ModItem`相同。）这意味着我们为`ModItem`所做的所有内容都可在您的类中使用，例如[SetDefaults](https://docs.tmodloader.net/docs/stable/class_mod_item.html#a10b52a61d301eca52d3b30c3c989d835) hook。请注意，您只能派生自一个类，因此`ModItem`不能是`ModProjectile`等等。

# 从Example Mod学习

Example Mod是tModLoader开发人员制作的模组，用于展示各种模组制作能力。明智的做法是启用Example Mod并玩一会儿。使用像[Cheat Sheet](https://steamcommunity.com/sharedfiles/filedetails/?id=2563784437)或[Dragon Lens](https://steamcommunity.com/sharedfiles/filedetails/?id=2939737748)这样的模组来生成其各种物品，找到它做的您想学习的东西。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/432af918-22ad-4cf7-b13b-1b265c66b73b)

接下来，下载Example Mod源代码。为此，下载[stable.zip](https://github.com/tModLoader/tModLoader/archive/refs/heads/stable.zip)，然后在其中找到ExampleMod文件夹。将该文件夹放在ModSources文件夹中，与TutorialMod文件夹相邻。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/f6899568-4e3d-4cfe-b867-a2aa3cd2876f)

您也可以通过直接在GitHub上浏览[stable ExampleMod](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod)来快速查找内容。屏幕左侧的"转到文件"搜索字段可用于快速找到特定文件。尝试输入"sword"进行搜索，您可以了解如何快速找到相关文件。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/26f8f518-3744-4bac-b99e-f593106d89d8)

找到您感兴趣的内容并尝试理解它。希望它很容易理解。您也可以在Example Mod的副本中更改内容并在游戏中构建Example Mod进行实验。

一旦您可以处理Example Mod或Tutorial Mod中的简单修改，您应该可以熟练掌握模组构建过程。随意探索其他指南或获取下一步的帮助。

## 分步学习

如果您是模组制作新手，您可能也是编程本身甚至C#语言的新手。建议从简单的事情开始，逐步提高难度。最简单的事情之一是制作一把可以挥动造成伤害的剑，如上所示，而最难的事情之一是创建一个完全功能的boss战。以下是一些入门建议：
* 首先，尝试熟悉tModLoader的工作方式。阅读"tModLoader的'hook'如何工作？"部分
* 接下来，尝试修改教程剑，造成更多伤害、更多击退、更快速度等。您还可以通过设置`Item.shoot`使其发射东西，并通过`Item.shootSpeed`控制速度
* 您需要理解模组制作就是编程，它是C#，它需要熟悉原版代码。这意味着您肯定应该遵循免费的在线课程以更好地理解C#语言或编程本身。也有用的资源可能是[快速Terraria特定C#速成课程](https://docs.google.com/document/d/1xRz3kFNbewb8DI29AKXuyi6O327IcxlgihZ7sdK_IuE/edit?usp=sharing)（不幸的是，由于链接损坏，本指南大部分已损坏。）。要更熟悉原版代码，请使用我们的[高级原版代码适配](Advanced-Vanilla-Code-Adaption)指南。
* 如果您有足够的信心，尝试制作自己的弹幕并让您的剑与其交互。
* 随着信心的增强，您可以开始尝试其他事情，例如制作一个敌人NPC。

**以下是对大多数模组制作者来说被认为是困难的事情，建议您在尝试这些之前从更容易的事情开始：**
* 完全功能的boss
* 全面的用户界面
* 代码抽象
* 实体模组信息，以及与其交互
* 完全自定义AI的弹幕、NPC、宠物...等等。

## 慢慢学习

有许多可用的资源可以帮助您成为更好的模组制作者。首先是这个wiki。在这个页面右侧，您可以找到wiki菜单，其中包含指向各种不同教程和指南的链接，可供您使用。这些都按难度级别分开，基础是最简单，专家是最难。另一个信息来源是[TCF上的模组制作部分](https://forums.terraria.org/index.php?forums/general-mod-discussion.121/)，但它不是很活跃。许多模组制作者在[tModLoader Discord](https://discord.gg/tmodloader)上合作和提问，如果您正在努力解决任何问题或有任何问题，请过来！最后，各种有用的工具和指南可在[此wiki的主页](Home)上找到。

## 模组制作提示和指南
### 命名约定
内部名称不支持空格，这意味着您需要命名`My Super Sword`但不能有空格。人们通常只是省略空格：`MySuperSword`但有时您也会看到`My_Super_Sword`。前者称为`pascal case`，后者称为`snake case`。`MySuperSword`/`pascal case`是首选，因为tModLoader自动填充本地化条目的方式。请记住使用简短且描述性的名称；如果您要制作一把剑，您可能在名称中使用'Sword'。命名约定主要取决于您与谁一起工作/为谁工作（如果您是被雇用等），如果您自己工作，显然取决于您的个人偏好。值得注意的是`camel case`类似于`pascal case`，但通常您会发现camel case的第一个字母可能是小写或大写，每个后续连接词大写，如`backColor`和`timeUtc`。

### 保持代码整洁有序
虽然这是一个更高级的主题（设计模式的一部分，可能稍后在专家教程中介绍），但确保您正确抽象代码是有用的。对于初学者，接受这个提示：在编程完东西并完成后，查看您的代码，询问每个地方发生了什么。现在您应该问代码是否整洁。尝试找到重复的代码部分并为其提供专用方法。接下来，尝试按逻辑分离代码的部分，为它们提供自己的方法，并提供有用的描述性名称（请参阅上一个提示），以便轻松识别该部分代码正在做什么。这是代码抽象部分的一个简要总结。

### 为您的代码添加注释
代码正在做什么并不总是很明显，或者有时您希望为自己或与您一起工作的其他模组制作者做笔记。您可以像这样制作单行注释`// 这是一个注释`，或者您可以像这样制作整个注释块：
```cs
/*
 * 这是一个注释块
 * 跨越多行！
*/
```
对于初学者，强烈建议添加尽可能多的注释，即使对于您认为自己已经很好理解的事情。从长远来看，这将帮助您，您可能会在几个月后回到代码并忘记它是如何工作的。对于有经验的模组制作者，挑战是使用尽可能少的注释；您的代码应该通过其行为对查看它的人来说是不言自明的。请注意许多语言使用不同的注释表示法，如`::`和`#`，对于C#使用上面显示的内容。

# 发布您的模组
一旦您的模组处于可工作状态，您可以将模组发布到Steam创意工坊。这将使您的模组可供所有tModLoader用户订阅。[创意工坊指南](https://github.com/tModLoader/tModLoader/wiki/Workshop)有更多相关信息。