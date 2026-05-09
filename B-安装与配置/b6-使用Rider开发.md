# 介绍性内容

Rider是由JetBrains开发的轻量级IDE。它类似于[Visual Studio](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio)，但布局不同，更类似于Intellij和PyCharm。如果您以前使用过其他JetBrains IDE，您使用Rider可能会比使用Visual Studio容易得多。Rider比Visual Studio更容易运行，同时仍然具有智能感知和代码调试功能。

重要的是，它可在Windows、Mac和Linux上运行，而[Visual Studio](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio)仅限Windows。它可免费用于非商业用途，学生可以获得[免费教育许可证](https://www.jetbrains.com/community/education/#students)。

## 目录
- [介绍性内容](#介绍性内容)
  * [开始](#开始)
  * [打开现有VS项目](#打开现有vs项目)
  * [创建新的Rider项目](#创建新的rider项目)
  * [Rider的功能](#rider的功能)
  * [添加项目引用](#添加项目引用)
  * [查看文件结构](#查看文件结构)
  * [最佳视图设置](#最佳视图设置)
  * [跟踪TODO](#跟踪todo)
  * [无干扰模式](#无干扰模式)
  * [代码分析、检查设置](#代码分析检查设置)
  * [拆分视图：同时处理多个文件](#拆分视图同时处理多个文件)
  * [调试](#调试)

## 开始
首先，安装Rider。Rider可免费用于非商业用途，可在此处下载：https://www.jetbrains.com/rider/

JetBrains的整个IDE套件也可通过[GitHub的教育包](https://education.github.com/pack)获得，只需要一个受认可的学校电子邮件即可。在[这里](https://education.github.com/benefits?type=student)注册。

[返回目录](#目录)

## 打开现有VS项目
启动Rider后，只需选择"打开解决方案或项目"。然后导航到您项目的`.csproj`或`.sln`文件并打开它。Rider将打开一个新窗口并加载项目。

**注意：**确保直接导航到项目文件，不要在路径上使用符号链接（符号链接）。否则，可能会导致问题，例如"Problems"视图中的文件不计入项目，或热重载不工作。

[返回目录](#目录)

## 创建新的Rider项目
建议您通过tModLoader本身创建项目文件。这可以通过按照[基础tModLoader模组制作指南](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Modding-Guide)中列出的步骤进行操作。通過tModLoader创建新的模组框架后，您的模组文件夹将位于模组源目录中，位于此处：`%userprofile%/Documents/My Games/Terraria/tModLoader/ModSources`按照上面的步骤将此路径粘贴到文件资源管理器中找到模组文件夹。您的项目将使用正确的构建设置生成，因此无需更改它们。

您现在可以按照[打开现有VS项目](#打开现有vs项目)开始处理您的模组。

[返回目录](#目录)

## Rider的功能
模组制作者使用像Rider这样的IDE是因为它有助于识别和修复编程错误。请参阅[为什么使用IDE](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE)获取IDE如何使模组制作者受益的基本信息。

[返回目录](#目录)

## 添加项目引用
生成的项目文件将自动应用所有适当的设置和引用。如果您需要引用唯一的.dll，例如[引用跨模组内容的模组](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content)，请按照以下说明进行操作。

在窗口左侧将有一个标为explorer的面板，这是您的解决方案资源管理器。顶部附近有一个下拉菜单。默认情况下，它将设置为解决方案，如果不是，请点击它并从下拉菜单中选择解决方案。现在explorer应该显示您的解决方案。从explorer中，右键点击带有绿色C#和您模组名称的顶部标签。应该会出现一个菜单。点击添加，然后点击添加引用。

![](https://i.imgur.com/E9Ky5nJ.png)

将出现一个新窗口，选择'从...添加'，应该会出现文件资源管理器。找到.dll文件并双击它以将其添加为项目引用。

[返回目录](#目录)

## 查看文件结构
要查看类似VS studio的文件结构（方法和下拉等）
转到`视图 -> 工具窗口 -> 结构`
这将打开一个结构视图窗口，如下所示：

![](https://i.imgur.com/hrNR8X3.png)

点击任何方法或字段将使您的编辑器滚动到该位置。

要返回常规文件资源管理器，只需选择左侧的Explorer标签。

[返回目录](#目录)

## 最佳视图设置
如果您计划处理大型代码（如tML），建议启用'查看空白字符'和'使用软换行'选项。转到`视图 -> 活动编辑器`并启用这些选项。查看空白字符（缩进）对于确保您使用4个宽度单位的制表符而不是空白字符很重要。

[返回目录](#目录)

## 跟踪TODO
Rider有一个有用的窗口，可以找到在注释中标记为TODO或抛出NotImplementedException()的内容。
要打开此窗口，请转到`视图 -> 工具窗口 -> TODO`
这将打开一个新窗口，如下所示：

![](https://i.imgur.com/JU5DiOX.png)

[返回目录](#目录)

## 无干扰模式
当您想在不受干扰的情况下编写代码时，这是一个有用的提示。
要使用此模式，请导航到`视图 -> 进入无干扰模式`
这将消除几乎所有侧边窗口，如下所示：

![](https://i.imgur.com/JMLXqOx.png)

[返回目录](#目录)

## 代码分析、检查设置
为了您自己的利益，建议启用代码分析并传播代码注释。

通过转到`文件 -> 设置`或使用`CTRL + ALT + S`快捷键打开设置。

导航到`编辑器 -> 检查设置`

在检查设置上，选中启用`'启用代码分析'`和`'启用解决方案范围分析'`

![](https://i.imgur.com/9UXxkvH.png)

接下来，要启用代码注释传播，请在同一下拉菜单中导航到`'代码注释'，并选中`'自动传播注释`'，如下所示：

![](https://i.imgur.com/zeaXVWe.png)

您可能还想禁用`'使用'var'（内置类型）'`提示，这可能会很烦人。

导航到`检查严重性 -> C#`

在搜索栏中输入`Use preferred 'var'`，然后取消选中它，如下所示：

![](https://i.imgur.com/XxnqDJG.png)

如果您也被`'Invert if to reduce nesting'`困扰，也可以禁用它：

![](https://i.imgur.com/537R8dV.png)

[返回目录](#目录)

## 拆分视图：同时处理多个文件
Rider中另一个有用的功能是拆分视图。

它看起来可能像以下内容：

![](https://i.imgur.com/gKgf9aa.png)

为此，右键点击您想要拆分的标签，然后选择"垂直拆分"或"水平拆分"，您可以多次执行此操作，但可读性会迅速下降。

[返回目录](#目录)

# 调试
要调试您的模组，请确保tModLoader已关闭，然后点击绿色的"错误"按钮以构建并启动带有附加调试器的模组。调试应该类似于在Visual Studio中的工作方式。本wiki上的指南使用Visual Studio显示截图和示例，但Rider中存在所有相同的选项。请参阅[学习如何调试指南](https://github.com/tModLoader/tModLoader/wiki/Learn-How-To-Debug#debugger-debugging)获取更多信息。

您还将获得"热重载"的访问权限，这是一个强大的工具，允许您在游戏已经运行时编辑代码。确保[正确打开项目](#打开现有vs项目)，否则它将不起作用。

[返回目录](#目录)