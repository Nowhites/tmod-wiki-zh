# 为什么使用IDE
希望本页面能说服您放弃在Notepad中制作模组。务必点击下面的链接观看视频。所有呈现的信息对应于[Visual Studio](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio)，但其他IDE如[Rider](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Rider)和[Visual Studio Code](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio-Code)具有类似的功能。

# 语法错误检查
当您犯语法错误时，您会在错误代码下方看到红色下划线，类似于拼写检查。
![](https://i.imgur.com/dwskGkr.png)
您可以将鼠标悬停在错误上查看解释。
![](https://i.imgur.com/a80jrK8.png)
看到上面的解释，我们可以通过将`20.5f`更改为只是`20`来轻松修复代码。

## 简单拼写错误
Visual Studio也可以修复简单的拼写错误。

https://github.com/tModLoader/tModLoader/assets/4522492/5bab82f0-84dd-4b7d-8623-68c70275e8a4

## 显示潜在修复
有时Visual Studio可以猜测如何修复您的错误。例如，下面我们忘记将`using Terraria.ID;`添加到我们的ModItem类中。
![](https://i.imgur.com/dq7v87C.png)
点击`Show potential fixes`或按热键会显示以下选项。
![](https://i.imgur.com/BivfnXZ.png)
点击第一个选项会将适当的using语句添加到我们的代码中。我们可以在不尝试在游戏中构建的情况下修复错误。

https://github.com/tModLoader/tModLoader/assets/4522492/9055c84c-fe13-4cb9-bf54-6a201cc5db31

# 自动完成/智能感知

## 字段名称
新模组制作者的一个常见问题是他们不知道所有Terraria变量名或方法名。例如，各种生物群落/区域的名字并不总是您期望的。您仍然需要一点经验来知道在哪里查找这些变量，但智能感知有很大帮助。在下面的视频中，我们看到智能感知建议可用的区域和被击败的boss。注意Plantera被击败是`downedPlantBoss`。这就是为什么智能感知对自给自足的模组制作者如此有价值的原因。
![](https://i.imgur.com/CkGOuta.png)

https://github.com/tModLoader/tModLoader/assets/4522492/8565bfed-b085-416c-841a-b5c409b0339c

### ItemID、NPCID、ProjectileID等...
虽然您可以浪费时间在我们的[参考页面](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Content-IDs)中查找各种原版ID，但让自动完成为您完成工作要容易得多！只需开始输入您对ID名称的猜测，您就会快速看到建议。您可以选择一个建议来自动完成它。这不是很好吗？
![](https://i.imgur.com/8VpUJFF.png)

## 方法名称和参数
方法名称也以相同的方式建议。此外，您可以看到方法参数，包括它们的名称和类型。例如，我想在用剑击中敌人时向他添加一个debuff。输入`target.AddBuff(`会带来参数，帮助我完成对AddBuff方法的调用。
![](https://i.imgur.com/Oml4sUU.png)

https://github.com/tModLoader/tModLoader/assets/4522492/df0ed4f6-80b2-4158-b440-178b2fbfa340

## 覆盖
制作模组时一个极其常见的任务是覆盖各种hook，如`SetDefaults`或`UpdateAccessory`。记住这些hook的参数有时很难。通过使用Visual Studio，您可以轻松地写"override"，然后加一个空格，然后输入您想覆盖的方法的几个字母，然后回车轻松覆盖hook。作为奖励，任何缺失的using引用都将自动添加到您的代码中。
**之前：**
![](https://i.imgur.com/uOQMDHx.png)
**之后：**
![](https://i.imgur.com/KpQARfo.png)

https://github.com/tModLoader/tModLoader/assets/4522492/2715244e-b526-46b2-bfc9-1cdd82d15c46

## 跨模组
通过使用提取模组功能，`[Modname]_v[Modversion].dll`（如果存在，还有一个可选的`[Modname]_v[Modversion].xml`文档文件，请参阅下面的[模组文档](#模组文档)部分）将被放置在`Terraria\tModLoader\ModSources\ModAssemblies`中。如果您的模组对另一个模组有强引用或弱引用（您仍然需要build.txt条目），则可以将此文件作为dll引用添加到您的.csproj。此路径是一致的，可从Mod Sources文件夹访问，意味着添加到.csproj的引用对于模组的所有贡献者都是可移植的，前提是他们提取了计算机上引用的模组。通过这样做，您也可以享受所引用模组提供的API的自动完成。请在[这里](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content)阅读更多内容。

# 构建
您可以从IDE构建模组。在VS中，找到`Build->Build [ModName]`菜单来构建模组。请注意，这通常不如直接调试有用，但可能有其用途。还要注意，在打开的客户端中加载模组时尝试构建模组将不起作用。

# 调试
使用IDE（如VS），您可以调试模组。调试是精确定位模组问题并解决它们的最佳方式。在调试时，您可以设置"断点"，这将使应用程序在到达该点时"停止"并暂停自身。在此暂停期间，您可以通过例如查看范围内变量的值来检查正在发生的事情。您可以快速恢复应用程序，或单步执行代码以尝试找到问题的原因。要了解更多关于调试的信息，请参阅其[自己的指南](Learn-How-To-Debug)。

## 编辑并继续
使用编辑并继续（或eac），您可以在命中断点后编辑代码，并立即在游戏中看到编辑效果，而不必手动重新构建模组。这对于定位弹幕生成或调整物品默认值非常有用。务必阅读[编辑并继续](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio#edit-and-continue)以使用此功能。请注意，`ModItem.SetStaticDefaults`、`ModTile.SetDefaults`或其他代码等内容可能仅在模组加载时运行，因此无法从eac中受益。

### 简单示例
在这个视频中，我们看到生成了2个Example Gun。生成第一个后，在SetDefaults中设置一个断点。一旦命中，游戏暂停，Visual Studio进入前台。然后，`item.damage`从40更改为80。我们按继续，我们可以将鼠标悬停在2个物品上，看到第二个物品以80伤害生成。

https://github.com/tModLoader/tModLoader/assets/4522492/34bba15f-23d0-4484-bee2-b8f5e8de8533

### 另一个示例
在[基础弹幕：绘制和碰撞](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile#drawing-and-collision)指南中，展示了一个使用断点测试`ModProjectile.SetDefaults`值的示例：[视频](https://user-images.githubusercontent.com/4522492/265848754-d17b18ad-1d4b-46fb-90ee-dfa43789e484.mp4)

# 文档
tModLoader将自动提供文档XMLdoc文件，当您将鼠标悬停其上时可以看到方法文档。这与[在线文档](http://tmodloader.github.io/tModLoader/)中包含的信息相同，只是更容易访问。例如，当`override`菜单处于活动状态时，点击一个条目将显示该方法的文档，您可以在其中查看该方法的目的和潜在用法：
![image](https://user-images.githubusercontent.com/4522492/184468884-b177fc6f-9050-4ae5-bff1-e9fab0140a7c.png)

将鼠标悬停在现有覆盖方法上也会显示文档：
![image](https://user-images.githubusercontent.com/4522492/184468903-31fa6b84-a1d4-4754-b6d0-9aacf226a00c.png)

将鼠标悬停在方法参数上可查看参数作用的详细信息：
![image](https://user-images.githubusercontent.com/4522492/184468932-a82c9cc9-6562-4ffd-be0c-0bed8e9ebecb.png)

将鼠标悬停在字段或方法上可查看详细信息：
![image](https://user-images.githubusercontent.com/4522492/184469232-b8333cff-a678-46e4-a33f-707cbbb63318.png)
![image](https://user-images.githubusercontent.com/4522492/184469241-39eb4c8e-aeb9-48c4-8bc2-1c21c78d2747.png)

请注意，文档的数量和分布是由模组制作者反馈驱动的。如果您发现需要文档的内容，请随时在Discord聊天中建议。

## 为您自己的模组编写文档
为模组编写文档可能是有用的，一旦模组变得复杂。尝试记录重要的事情，但不要觉得有压力要记录所有内容，这都是为了您自己的利益（除非您打算让其他模组使用这些方法，请参阅下一节）。毕竟，编写可读的代码比记录无意义的代码更好。有关编写文档的更多信息，请参阅[XML文档](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/xmldoc/)。记录方法的最简单方法是在方法上方的行上键入`///`，Visual Studio将自动完成模板，您可以输入您的笔记。

## 模组文档
为其他模组制作带有API的模组的模组制作者可以将其`[ModName].xml`文件包含在模组中，它们将自动与其`.dll`文件一起提取并放置在`Terraria\tModLoader\ModSources\ModAssemblies`中以便轻松协作（前提是`hideCode`不为true，这会对期望被其他模组引用的模组产生反效果）。模组的XMLdoc文件可以在常规构建期间在Visual Studio或Rider中生成（任何使用`csproj`格式的当代IDE都应该可以），应命名为[ModName].xml并放置在模组源文件夹的根目录中。

要使您的IDE生成文档，请将以下内容添加到`.csproj`文件中的`PropertyGroup`部分：

```xml
<PropertyGroup>
	<GenerateDocumentationFile>True</GenerateDocumentationFile>
	<DocumentationFile>$(AssemblyName).xml</DocumentationFile>
</PropertyGroup>
```

要编辑`.csproj`文件，请在IDE中右键单击项目并点击`Edit Project file`或直接在文本编辑器中打开文件。

您会想要记录您期望模组制作者使用的任何公共方法和字段。为此，在字段或方法上方的行上键入`///`。这将导致您的IDE生成文档存根，之后您可以将任何您想要的信息添加到`summary`部分。您可以查看[`ModItem`源代码](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ModLoader/ModItem.cs)来了解有用的文档应该是什么样子。如果您发现"Missing XML comment for publicly visible type or member X"警告很烦人，请随意将`dotnet_diagnostic.CS1591.severity = none`添加到您的`.editorconfig`文件，或将`<NoWarn>1591</NoWarn>`与添加到`PropertyGroup`部分的其他新行一起添加以忽略此警告。

每当您更新文档时，请记住在发布前至少从您的IDE构建一次以更新`.xml`文档文件。然后在游戏中构建将使用该`.xml`文件并将其包含在模组中。

要使用其他模组的文档，只需按照[专家跨模组内容指南](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content#visual-studio-steps)中的步骤操作。如果`[Modname]_v[Modversion].xml`文件出现在`ModAssemblies`文件夹中，则包含文档。请记住，我们建议将引用的`.dll`文件保留在`ModSources\ModAssemblies`文件夹中而不是复制到模组源代码文件夹中，但如果您确实移动了`.dll`文件，请确保也复制`.xml`文件。现在，像往常一样将鼠标悬停在任何有文档的成员上，如果模组作者已记录该成员，您应该会看到文档显示。许多模组在其主页或GitHub wiki上维护文档，请务必检查这些位置（如果模组中未包含`.xml`文档文件）。

# 查找所有引用（Ctrl-K，R）
查找所有引用可让您轻松找到整个项目中变量的所有用法。只需右键点击变量并点击`Find all references`。这对于查看您分配和使用变量的位置非常有用。
![](https://i.imgur.com/QCUdJdc.png)

https://github.com/tModLoader/tModLoader/assets/4522492/e37f0e9a-2bf7-4704-8e90-a5bf0a26d1ce

# 转到定义（F12，Ctrl-左键单击）
通过点击变量或类名，您可以按F12（或Ctrl-左键单击），Visual Studio将直接带您到该类或变量声明的位置。如果您在代码之外的类（如Terraria类`Dust`或XNA类`Rectangle`）上执行此操作，您将被引导到一个页面，其中仅显示变量和方法，而不是实际代码。能够直接读取Terraria代码非常有用。请按照下面的"转到反编译代码"部分来启用导航到反编译代码的功能，让您也可以查看该代码。

https://github.com/tModLoader/tModLoader/assets/4522492/a19265ee-8f0c-4e33-a1c0-554c3b6bab75

## 转到反编译代码
默认情况下，`Go To Definition`命令不允许您查看tModLoader/Terraria源代码。我们可以启用此功能以使模组制作更加高效。在Visual Studio中，使用搜索（Ctrl-Q）搜索"decompile source"。点击"Enable navigation to decompiled sources"，然后选中复选框并点击"OK"：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/45b1ccc1-bcdd-444f-b507-18d4d5c227e6)
![image](https://github.com/tModLoader/tModLoader/assets/4522492/1b1fd419-38cd-4fb8-a85b-670486bf947a)

从现在开始，您应该能够使用`Go To Definition`命令查看反编译的Terraria代码。现在就尝试一下，输入`Main.npc[0].CanBeChasedBy();`并在`CanBeChasedBy`上使用`Go To Definition`命令。在Visual Studio反编译tModLoader之后经过一段延迟后，您应该看到`NPC.CanBeChasedBy`方法的代码：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/9a94f9f2-7192-45d6-bd0c-027309536919)

# 重命名（F2）
想象一下，您为一个变量或类命名了一些愚蠢的东西并想更改它。没有IDE，您将不得不手动检查变量使用的每个文件并编辑文本。即使是"查找和替换"也会容易出错（如果您对其他变量使用类似的名称）。使用重命名功能，您可以轻松重命名类和变量。请注意，如果您使用的是`mod.ItemType("Name")`等方法而不是`ModContent.ItemType<Name>()`，这将不起作用。这也是使用<>类型方法用于ItemType和NPCType的一个很好的理由，这样维护起来更容易。在视频中，我们将一个名为`examplePet`的变量重命名为更好的东西，`airflowerPet`。
![](https://i.imgur.com/PInjsqm.png)

https://github.com/tModLoader/tModLoader/assets/4522492/88a34158-cbfc-4eb6-8f19-81d1eaa9cc79

# 参数信息（Ctrl-Shift-Space）
一些Terraria方法有很多很多参数。很难记住这些参数的顺序和类型。如果您想查看类似Item.NewItem或Projectile.NewProjectile的内容的参数，只需将光标放在方法内并按`Ctrl-Shift-Space`。您可以左右导航并观看粗体参数变化。按上下可在同一方法的重载之间切换。
![](https://i.imgur.com/D0f2NM6.png)

https://github.com/tModLoader/tModLoader/assets/4522492/61c8ca1e-fe95-40c7-93f2-d8460edeb97a

https://github.com/tModLoader/tModLoader/assets/4522492/571f8868-7094-4815-ad23-d0250dce15b5

# 转到（Ctrl-,)
这是一个非常有用的快捷方式，转到允许您快速导航到代码中的任何类、方法或字段。只需按`Ctrl-,`，转到全部窗口将弹出。输入名称或部分名称并选择项目以快速导航到代码。您也可以输入名称中每个单词的首字母来找到它。例如，输入`eqf`会建议`ExampleQuestFish`。Visual Studio也会巧妙地处理拼写错误和部分匹配。视频显示了此功能可以有多快，以及在代码中快速导航到其他位置是多么容易。
![](https://i.imgur.com/TUthjgE.png)

https://github.com/tModLoader/tModLoader/assets/4522492/24e0cb32-6c07-4635-9e90-f692dfc54534

# 快速操作和重构（Ctrl-.）
这个上下文相关的热键在几种情况下很有用。建议取决于光标位置或选中的文本。以下是一些有用的操作：

## 为所有出现引入局部变量
如果您在通过字段和方法重复访问相同对象的代码中重复很多，有时您的代码可能会变得有点重复。创建一个局部变量并重用该变量会使代码更清晰。如果您突出显示重复对象访问的第一个出现，您可以使用此操作快速引入一个局部变量，然后您可以为其指定一个适当的名称。

https://github.com/tModLoader/tModLoader/assets/4522492/7f8be194-6862-4056-b91d-70e611320cab

# 格式化文档（Ctrl-E，D或Edit->Advanced->Format Document）
正确格式化的代码是可读性的关键。在寻求代码帮助之前，务必运行此命令，因为没有什么比格式不佳的代码更烦人的了。

https://github.com/tModLoader/tModLoader/assets/4522492/03e49732-da87-454f-8ab8-5b71c578f752

# 结构指导线
有时，特别是在Projectile和NPC AI中，您可能会在代码中迷路。对于嵌套if语句尤其如此。将鼠标悬停在虚线上可快速查看您当前在代码中的位置。例如，在这里我可以一眼看出我的AI逻辑中当前处于哪个位置。
![](https://i.imgur.com/nVR628X.png)

# 代码折叠
通过按`-`或`+`按钮折叠代码可能很有用，如果您需要更广泛地了解您的代码。
![](https://i.imgur.com/BcgwF5H.png)

# 转到括号（Ctrl-]）
使用此热键在有大代码块的括号内导航原版代码时非常有用。

https://github.com/tModLoader/tModLoader/assets/4522492/86ea2d85-b84a-4e11-b4f3-427b222b4dad

# 启动配置文件
可以设置启动配置文件以简化模组开发和测试。例如，可以设置一个配置文件以直接进入世界启动游戏，跳过玩家和世界选择菜单。直接将调试器启动到游戏可以节省时间和点击。其他启动配置文件用途包括测试特定模组包和维护特定于模组的保存目录以进行组织。请参阅[使用命令行参数进行调试](https://github.com/tModLoader/tModLoader/wiki/Command-Line#using-command-line-arguments-for-debugging)以了解如何使用此功能。

# C#交互式
您知道C#中的`3/2`是`1`而不是`1.5`吗？有时您可能想尝试一些C#语法，但不想仅仅为了测试代码而制作一个模组。C#交互式窗口对此非常有用。您可以在C#交互式中做任何您想做的事情，每条命令在您按回车后会执行它。可以使用`Alt-UpArrow`调出之前的语句。
![](https://i.imgur.com/F6YABM2.png)

https://github.com/tModLoader/tModLoader/assets/4522492/9e1f8d4f-c000-47d5-bb8c-61e0b5b90276

# Git集成
Git集成允许您直接在IDE中与GitHub仓库交互。您可以直接在IDE中查看差异并进行提交。我们建议在Google上找到指南以了解更多。

# Visual Studio Live Share
此功能可让您邀请某人进入您的项目并协作处理代码。可用于指导模组团队中的其他开发人员或获得棘手问题的帮助。与Discord聊天配对以进行语音聊天。