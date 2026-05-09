- **本指南不涵盖[Visual Studio](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio)，只涵盖Visual Studio Code。**
- **Visual Studio Code占用的存储空间比Visual Studio少。**
- **Visual Studio Code可在Windows、Mac和Linux上运行。**
- **如果您在模组制作方面没有经验，并且您的计算机有空间并且在Windows上，强烈建议您获取Visual Studio。**
- **本指南不旨在学习C#基础知识**

# 先决条件

## 安装

1. 下载[Visual Studio Code](https://code.visualstudio.com/)。
1. 通过运行下载的安装程序安装Visual Studio Code。允许程序运行并接受许可协议（如常）。默认选项应该合适。
1. 通过下载安装程序、运行安装程序并按照说明进行操作来安装[.NET 8 SDK](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)。下载标记为"x64"的最新SDK安装程序（对应您的操作系统）。不要下载.NET 10.0或.NET 9.0版本，那些不起作用。([可视化指南](https://github.com/tModLoader/tModLoader/assets/4522492/75a9299b-0e1a-41cd-acd0-e6d8ffcfe591))
1. 启动Visual Studio Code。
1. 安装**C# Dev Kit**扩展。
    1. 您应该会看到类似于以下内容：[欢迎页面](https://i.imgur.com/YwNh3x6.png)
    1. 点击扩展按钮：[按钮位置](https://i.imgur.com/fqMhVKd.png)
    1. 在搜索栏中输入`C#`，点击"C# Dev Kit"扩展，然后点击安装按钮：[安装C#扩展步骤](https://i.imgur.com/KYNArmF.png)
    2. *可选*。如果您是经验丰富的用户，**IntelliCode for C# Dev Kit**将是一个有用的扩展。如有兴趣也可以安装。但是，如果您没有经验，这只会引起困惑，不建议安装：[扩展详情](https://i.imgur.com/1wxx5pg.png)
    3. 等待安装完成。输出窗口将显示其他扩展正在下载和安装，因为它与C# Dev Kit打包在一起：[已安装的扩展](https://i.imgur.com/9R1jazM.png)

# 创建模组

按照[基础tModLoader模组制作指南](Basic-tModLoader-Modding-Guide)中的说明进行操作

# 打开您的模组源代码

1. 非常重要的一点是您已安装上面[先决条件](#prerequisites)中列出的扩展和.NET 8 SDK。
1. 打开Visual Studio Code
1. 点击`打开文件夹`。如果您没有看到，点击`文件->打开文件夹`。导航到`%UserProfile%\Documents\My Games\Terraria\ModLoader\Mod Sources\TutorialMod`（如果您在Windows上），或导航到`~\Library\Application Support\Terraria\ModLoader\Mod Sources\TutorialMod`（如果您在Mac上），然后点击`选择文件夹`：[示例](https://i.imgur.com/lCaN4aP.png)
1. 您现在应该会看到各种可编辑的文件。打开`TutorialMod.cs`，您应该能够开始编写代码。
1. 您可以通过在Explorer窗格中的空白处右键点击来创建新文件夹和`.cs`文件：[新建文件](https://i.imgur.com/B6fh4JD.png)

# 构建您的模组

1. 构建模组很容易。只需从菜单栏打开`终端->新终端`：[位置](https://i.imgur.com/LnrCB0W.png)
2. 终端打开后，只需输入`dotnet msbuild`来构建您的模组。

## 启用热重载

此功能默认禁用。您需要打开设置并启用它。

1. 按键盘上的`Ctrl + ,`打开设置，然后点击右上角的图标：[位置](https://i.imgur.com/JwdkHte.png)
2. 在花括号内，添加以下行：
```
"csharp.experimental.debug.hotReload": true,
"csharp.debug.hotReloadOnSave": true
```
3. 按`Ctrl + S`保存。热重载应该已启用。

## 常见构建问题

* 如果您遇到与"找不到project.assets.json。请运行NuGet包还原以生成此文件。"相关的错误，请转到`终端->新终端`，在出现的终端中运行`dotnet restore`命令。现在您可以使用`dotnet msbuild`再次运行构建。
* 如果您遇到与"System.IO.IOException: 该进程无法访问文件TutorialMod.tmod，因为它正被另一个进程使用。"相关的错误，您需要在tModLoader中禁用模组并重新加载模组，以便可以编辑文件。在构建时完全关闭tModLoader也是一个选项。

# 为什么使用Visual Studio Code

请阅读[为什么使用IDE](Why-Use-an-IDE)页面。大多数功能都适用于Visual Studio Code。如果您没有看到错误被加下划线或看到建议的修复，您可能跳过安装了[C# Dev Kit扩展](#prerequisites)。

# 调试

调试可以通过使用VS Code的运行和调试功能来实现。

1. 点击`运行和调试`（或按`Ctrl + Shift + D`）：[位置](https://i.imgur.com/pUxWjVf.png)
2. 在主编辑器中打开与模组相关的`.cs`后，点击`运行和调试`按钮：[大按钮](https://i.imgur.com/FmKbi7z.png)
3. tModLoader应该会在启用模组当前状态的情况下启动。您可以从这里添加断点并在必要时和启用时热重载：[调试控制台](https://i.imgur.com/2LQIgLc.png)
4. 要添加断点，只需点击行号左侧的红点，如下所示：[红圈断点](https://i.imgur.com/1MlX9mN.png)

# 问题
## 自动完成、错误报告或智能感知不工作
假设您遵循了[先决条件](#prerequisites)中的每个步骤，此错误最常见的原因是未能正确打开模组代码。请遵循[打开您的模组源代码](#open-your-mod-source)部分。如果未能以这种方式打开模组，使用Visual Studio Code将适得其反。