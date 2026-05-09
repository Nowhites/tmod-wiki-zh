# 中级先决条件
在查阅任何中级指南之前，请阅读以下内容，以便您为中级概念做好准备。请确保您已完成基础先决条件并学习了与您感兴趣的内容相关的基础级别教程。

## 从文本编辑器升级到IDE
在基础级别中，我们使用文本编辑器编写了一些简单的代码，但对于尝试中级指南中的任何内容的人来说，使用IDE（集成开发环境）是极其推荐的。

我们建议安装最新的[Visual Studio](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio)。它是免费且功能完整的。如果您不在Windows上，或更喜欢Visual Studio Code，我们建议安装[Visual Studio Code](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio-Code)

### 安装
按照上面链接的指南安装所选IDE和其他相应的先决条件。

### 智能感知
我们使用IDE的主要原因之一是利用智能感知。智能感知是指IDE在我们输入时向我们建议变量和方法名称的能力。这很重要，因为我们需要正确输入内容以便计算机理解我们。例如，如果我们想在代码中引用"云瓶"物品，我们需要精确地编写`ItemID.CloudinaBottle`。如果没有IDE，我们可能会假设正确的拼写和大小写，并输入`ItemID.CloudInABottle`。这将导致错误。我们的IDE将自动建议正确的变量名，节省输入和时间。
> ![image](https://github.com/tModLoader/tModLoader/assets/4522492/c62e6bdf-6af5-48ab-9caf-4d125ecf0099)

这是一个视频，展示了智能感知如何显示我们正在调用的方法的参数名称：

https://github.com/tModLoader/tModLoader/assets/4522492/0e682781-5094-4a1d-bb7f-40634f26816b

这是对智能感知的简短探索。请参阅[为什么使用IDE wiki页面中的智能感知部分](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#autocomplete--intellisense)获取更多信息和示例。智能感知非常重要。

### 使用
您所选IDE的指南（[Visual Studio](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio)或[Visual Studio Code](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio-Code)）有创建模组和正确打开模组源代码的部分。请按照这些指南正确打开模组的源代码。以这些指南中解释的方式打开模组源代码非常重要。如果未能正确打开模组源代码，将导致您的IDE仅作为普通文本编辑器有用。

### 高级
随着您制作模组，最终您需要学习更高级的技术。[高级先决条件](https://github.com/tModLoader/tModLoader/wiki/Advanced-Prerequisites)指南将通过教授如何访问和阅读Terraria源代码来为您做好准备。

[为什么使用IDE wiki页面](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE)是了解有用IDE功能（如编辑并继续、调试和断点）的另一个重要资源。

## 继承
请[阅读关于继承的内容](https://www.tutorialspoint.com/csharp/csharp_inheritance.htm)

继承在tModLoader模组制作中无处不在。如果您回顾我们在基础级别制作的剑，您会注意到我们的剑继承自`ModItem`。

## Hook
"Hook"是一种可供模组制作者使用的方法，以便他们能够为其模组实现特殊效果。Hook是虚拟方法，模组制作者如果想使用它们可以覆盖这些方法。

## 文档
学习阅读[文档](http://tmodloader.github.io/tModLoader/)对于模组制作至关重要。例如，[示例枪](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Weapons/ExampleGun.cs#L79)有一个覆盖`ModItem.Shoot`方法的示例。我们从方法签名中看到`Shoot`返回一个`bool`。我们返回的`bool`有特殊含义，但如果我们不查找它就不会知道。打开文档并搜索"Shoot"，然后点击结果，然后点击ModItem类的子结果。您将被带到描述。阅读描述，了解参数和返回值代表什么。回顾那个`ExampleGun`代码，您现在应该了解hook如何通过使用Shoot hook来阻止游戏的默认弹幕生成行为。
> ![](https://i.imgur.com/lQ5gAh2.png)

### 虚拟和覆盖
您可能注意到文档将所有hook列为virtual，但所有ExampleMod代码都有override。这是因为我们在继承时覆盖虚拟方法。如果您忘记了，请再次[阅读关于继承的内容](https://www.tutorialspoint.com/csharp/csharp_inheritance.htm)。

## 原版纹理文件参考
查看原版纹理文件并了解它们的布局方式非常有用，特别是当您开始为NPC制作动画时。Terraria将纹理存储在我们无法正常打开的`.xnb`文件中。最佳选择是只需将所有纹理文件提取为`.png`文件，并将它们保存在一个文件夹中，以便您随时可以查看。

说明：下载[TConvert](https://forums.terraria.org/index.php?threads/tconvert-extract-content-files-and-convert-them-back.61706/)并运行它。如有需要，修复`Terraria Content Folder`路径，然后点击`Use Terraria`。最后，使用`Output Folder`的文件夹图标指定要保存提取文件的位置。我建议在ModLoader文件夹中创建一个文件夹，以便您可以轻松找到它：`\Documents\My Games\Terraria\ModLoader\VanillaTextures\`。如果您愿意，可以取消选中`Images`以外的其他选项。现在，点击`Extract`并等待完成。从现在开始，如果您好奇NPC有多少帧，只需找到对应于NPCID的png文件并在图像查看器中查看它。

或者，您也可以在[The Spriters Resource](https://www.spriters-resource.com/pc_computer/terraria/)上找到Terraria精灵，但它们可能不是原始布局、尺寸或文件名。仅在需要快速参考时使用该网站，最佳选项是直接提取当前的Terraria纹理。