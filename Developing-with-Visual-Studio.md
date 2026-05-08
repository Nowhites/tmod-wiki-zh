- **本指南支持VS 2019和VS 2022版本**
- **Visual Studio仅在Windows上运行。Mac和Linux用户应考虑使用[Visual Studio Code](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio-Code)或[Rider](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Rider)。**
- **本指南不涵盖[Visual Studio Code](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio-Code)。**
- **本指南不旨在学习C#基础知识**

# 先决条件
1. 下载[Visual Studio Community](https://visualstudio.microsoft.com/)。
    1. 社区版是免费使用的。
    1. 如果您使用的是**tml 1.3**，您需要下载并安装[2019版本](https://visualstudio.microsoft.com/vs/older-downloads/)（如果社区版不可用，请尝试[这里](https://docs.microsoft.com/en-us/visualstudio/releases/2019/release-notes)），否则您会收到下面提到的错误。如果愿意，之后您可以使用2022。
    1. 如果您使用的是**tml 1.4**，您需要2022。需要v17.8或更高版本，您可能需要[更新](https://learn.microsoft.com/en-us/visualstudio/install/update-visual-studio?view=vs-2022#use-the-visual-studio-installer-1)。
1. 在安装过程中，选中`.NET desktop development`工作负载。不需要选中其他内容。（[示例](https://i.imgur.com/Y8uZ14h.png)）
    1. 您可能很想取消选中`Individual components`选项卡中您认为自己不需要的项目以节省硬盘空间或带宽。我们目前尚未确定哪些单独组件可以取消选中以进行tModLoader模组制作。许多取消了选中内容但无法编译模组。如果您收到类似"The type 'Object' is defined in an assembly that is not referenced."的错误消息，您可能遇到此问题，应该进行完整工作负载安装。
    1. 如果您忘记此步骤，可以点击Tools->Get Tools and Features再次打开安装程序。（[示例](https://i.imgur.com/EmqEsmH.png)）
1. 通过访问[dotnet 8.0下载站点](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)，下载标记为"x64"的最新SDK安装程序（对应您的操作系统），然后运行安装程序完成安装来安装`.NET 8.0 SDK`。不要下载.NET 10.0或.NET 9.0版本，那些不起作用。安装后您可能需要重启Visual Studio或计算机。（[可视化指南](https://i.imgur.com/EzRCHdk.png)）

# 创建项目
首先，我们必须为模组创建一个新项目。为此，请按照[基础tModLoader模组制作指南](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Modding-Guide)中的说明生成模组框架，其中将包含项目文件。

## 为旧模组创建或更新现有项目
如果您正在更新1.4之前的模组，您可以使用模组源菜单中的"Upgrade .csproj file"按钮。这将为您创建一个完全可用的项目，您可以打开并继续工作。
![image](https://github.com/tModLoader/tModLoader/assets/4522492/12231152-f764-4bc3-9a39-472a38f149c8)

# 打开您的项目
从文件资源管理器打开`.csproj`文件非常重要。如果您打开单个`.cs`文件，Visual Studio将完全无用。要打开.csproj，请导航到`Documents\My Games\Terraria\tModLoader\ModSources\[ModName]\`并双击`[ModName].csproj`。确保您没有打开`[ModName].csproj.user`或`[ModName].cs`文件。如果您没有显示文件扩展名，就会发生这种情况。观看[这个](https://github.com/tModLoader/tModLoader/assets/4522492/7ae18fe2-f9cf-4bcf-b3af-67bfce7495f2)来了解如何切换文件扩展名。现在Visual Studio已打开，确保您看到以下内容：
![](https://i.imgur.com/NreIujY.png)
点击`Solution Explorer`按钮查看模组中的.cs文件。您必须从这里打开.cs文件才能正确使用Visual Studio。让我们确保自动完成功能正常工作。打开`Items\TutorialSword.cs`并在现有`item.autoReuse = true;`之后的新行上键入`item.`。您应该会看到自动完成弹出窗口出现：
![](https://i.imgur.com/2WOzFND.png)
恭喜，您已准备好为模组编写代码了。阅读下文获取有关在Visual Studio中构建和调试的信息。

# 使用Visual Studio构建
在Visual Studio中构建模组可让您快速确保您编写的代码没有任何构建错误。首先，确保tModLoader本身已关闭，或者至少要构建的模组已卸载。接下来，只需选择Build->Build [Modname]菜单项：
![](https://i.imgur.com/Afx2wtd.png)
如果存在错误，您将在错误列表中看到它们。您可以点击每个错误查看有问题的代码：
![](https://i.imgur.com/eO0w0cn.png)
如果没有错误，您应该在左下角看到Build Succeeded。如果打开输出面板（View->Output），您应该看到类似内容：
![](https://i.imgur.com/mrMpzB3.png)

**请记住，Visual Studio不遵守您的buildIgnore规则，您构建的.tmod文件将比使用游戏内构建选项更大。因此，在发布模组之前，务必使用游戏内菜单进行构建。**

# 调试
调试是Visual Studio区别于Visual Studio Code的主要功能。这可让您设置模组中的断点并在Visual Studio中检查变量。要进行调试，**首先确保tModLoader已关闭**，然后只需点击标记为"Terraria"的按钮或按F5：
![](https://i.imgur.com/7GUHYCS.png)
如果存在错误，将出现错误列表，否则，模组将构建，自动启用，然后Terraria将启动。（您可能会发现全屏模式使模组制作变得困难，我建议在制作模组时使用窗口模式。）如果Visual Studio告诉您存在构建错误并询问是否仍要启动，请说**否**！修复错误并重试。如果看到此内容，您就知道它正在工作：
![](https://i.imgur.com/9vK1rvF.png)
有关如何调试和调试的好处的更多信息，请阅读[为什么使用IDE](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#debug)页面，请阅读它，它非常有用。

## 使用家庭共享进行调试
如果您通过Steam上的家庭共享启动tModLoader，您需要执行以下修复，否则通过VS启动游戏会失败：
1. 在模组的根文件夹中打开`Properties`文件夹，并在文本编辑器中打开`launchSettings.json`。
2. 将
```json
"workingDirectory": "$(tMLSteamPath)"
```
   改为
```json
"workingDirectory": "$(tMLSteamPath)",
"environmentVariables": {
"SteamClientLaunch": "1"
}
```
3. 保存文件。

或者，您可以打开VS，导航到Debug -> \<ModName\> Debug Properties，并将`SteamClientLaunch=1`添加到"Environment variables"字段中（或者根据您的VS版本，在Name中添加`SteamClientLaunch`和在Value中添加`1`）。

# 编辑并继续
在调试时，您可以编辑源代码并在游戏中重新编译模组，而无需重新加载模组或重启游戏。[为什么使用IDE](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#edit-and-continue)页面有更多关于此主题的信息，请阅读它，它非常有用。***"下一步"和"常见问题"之间的任何内容都不是必需的，这是您在制作模组时"Create Mod"按钮设置的。如果您不完全了解自己在做什么，请不要触碰您的.csproj。***

# 下一步
现在您已设置好Visual Studio，您的模组开发速度可以大大提高。请阅读[为什么使用IDE](Why-Use-an-IDE)获取有关IDE（如Visual Studio）如何大大加快模组制作的更多提示。

## 其他推荐的扩展
为进一步改善模组制作体验，请考虑可添加到Visual Studio的这些扩展。

### HJSON
[Hjson扩展](https://github.com/Rijam/vs-hjson/releases)为.hjson文件添加了语法高亮。键和值被着色以帮助识别错误。

![Hjson_Highlighting](https://github.com/tModLoader/tModLoader/assets/4522492/170fbe6b-ed73-4570-8d30-bf3a7e4b8ed9)

### HLSL
[HLSL Tools扩展](https://github.com/tgjones/HlslTools)大大改善了Visual Studio中着色器文件的编辑。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/b298659f-69ac-48ba-be44-5a27eec55d5e)

# 手动设置
手动为模组设置项目是不推荐的。模组制作者应使用自动模组项目生成。本节中的信息仅供参考，可能仅适用于过时的tModLoader版本。

<details><summary>展开手动设置说明</summary><blockquote>

## 创建项目
1. 打开Visual Studio并转到New -> Project
1. 选择"Class Library"模板
1. 设置模组的_内部_名称。（不允许特殊字符）
1. 如果使用VS 2017，请取消选中"Create directory for solution"，如果使用VS 2019，请选中"Place solution and project in the same directory"。
1. 确保将位置设置为Mod Sources目录
1. 点击OK
    1. 您的输出应该类似于[这个](https://i.imgur.com/8JBLt1A.png)。请注意，您的窗口可能看起来不同，这是VS 2019的窗口。
1. 转到Solution Explorer选项卡，右键点击Solution -> ModName -> References并点击Add Reference...（[示例](https://i.imgur.com/oM30lfT.png)）
1. 点击Browse...并选择tModLoader可执行文件（可能是：`C:\Program Files (x86)\Steam\steamapps\common\tModLoader\tModLoader.exe`，如果您不在Windows上，请查看[这个视频](https://github.com/tModLoader/tModLoader/assets/4522492/74a6bc40-5dd0-46a6-a947-2a76fbd4135c)）
1. 在项目的根目录添加一个名为`build.txt`的新txt文件
1. `buildIgnore = *.csproj, *.user, obj\*, bin\*, .vs\*`添加到您的build.txt
1. 将XNA引用添加到您的项目。如果您可以玩Terraria，则应该有这些，否则请参阅[这里](https://www.microsoft.com/en-us/download/details.aspx?id=20914)
    1. 在好的文本编辑器（如[Notepad++](https://notepad-plus-plus.org/)）中打开csproj文件
    1. 找到引用部分并添加以下内容：
    ```
    <Reference Include="Microsoft.Xna.Framework" />
    <Reference Include="Microsoft.Xna.Framework.Game" />
    <Reference Include="Microsoft.Xna.Framework.Graphics" />
    ```
    1. VS会提示重新加载项目，请这样做。否则，只需重新打开项目即可。
1. 创建扩展`Terraria.ModLoader.Mod`的类

## 使用Visual Studio构建
这可让您从Visual Studio中构建模组，这样您就不必从游戏中重新构建，也不用将源文件放在Mod Sources文件夹中。

1. 在Solution Explorer中右键点击您的模组项目并点击Properties
1. 转到Build Events选项卡
1. 将以下内容添加到Post-build事件命令行
`"C:\Program Files (x86)\Steam\steamapps\common\tModLoader\tModLoaderServer.exe" -build "$(ProjectDir)\"`
    1. 确保路径被双引号包围，如果您在不同位置安装了tModLoader，请更改路径。

# 调试
这可让您设置模组中的断点并在VS中检查变量。如果您在其他位置安装了Terraria，请更改路径。

1. 在Solution Explorer中右键点击您的模组项目并点击Properties
1. 转到Debug选项卡
1. 将Start Action设置为Start external program：`C:\Program Files (x86)\Steam\steamapps\common\tModLoader\tModLoader.exe`
1. 将Working directory设置为`C:\Program Files (x86)\Steam\steamapps\common\tModLoader`
    1. 如果您想在异常堆栈跟踪中包含行号（对调试有用），请将行`includePDB = true`添加到您的build.txt。_但是它会增加模组的大小，应从发布版本中省略_。

# 设置编辑并继续
这可让您编辑源代码并在调试期间重新编译模组，而无需重启游戏。

1. 只需在Post-build事件命令行的末尾添加`-eac "$(TargetPath)"`

</blockquote></details>

# 常见问题
这些问题通常仅在您尚未升级到0.11 .csproj时适用。请首先在模组源菜单中更新您的csproj。

## 自动完成对我没有任何作用。
（还有：自动完成更改了我不希望它更改的内容，例如将`item`更改为`Items`。）
1. 检查您的.cs文件是否实际在项目中，以及您是否只是打开项目而不是单个.cs文件。您可以使用显示所有文件切换，然后右键点击 -> 包含在项目中以将缺失的源文件添加到项目。
[示例](http://i.imgur.com/dNMyROY.png)
2. 确保您不是从文件资源管理器打开.cs文件。您应该始终打开.csproj（或.sln）文件，然后一旦Visual Studio打开，从Solution Explorer打开您的.cs源文件。如果不是这样，您会注意到左上方显示Miscellaneous Files，这意味着该文件不属于任何项目。如果您看到Miscellaneous Files，请再次检查步骤2。
[示例](https://i.imgur.com/bw41Wt4.png)
修复后：
[示例](https://i.imgur.com/RY456Nb.png)
3. 确保Solution Explorer处于解决方案视图而非文件夹视图。[按钮位置](https://i.imgur.com/NIOP3fn.png)
4. 确保Visual Studio是v17.8或更高版本。[更新说明](https://learn.microsoft.com/en-us/visualstudio/install/update-visual-studio?view=vs-2022#use-the-visual-studio-installer-1)。

## 当前.NET SDK不支持面向.NET 8.0。请面向.NET 7.0或更低版本，或使用支持.NET 8.0的.NET SDK版本
这意味着您需要将Visual Studio更新到v17.8或更高版本。这是2024年5月1日v2024.03稳定版的要求。[更新说明](https://learn.microsoft.com/en-us/visualstudio/install/update-visual-studio?view=vs-2022#use-the-visual-studio-installer-1)。

如果您注意到所有依赖项都有⚠️图标覆盖，即使是`Frameworks->Microsoft.NETCore.App`条目，您也可能遇到此问题。

## 找不到指定的SDK'Microsoft.NET.Sdk'。
这意味着您跳过了本页顶部提到的安装`.NET desktop development`工作负载。[先决条件](#Prerequisites)

如果您确定已执行此操作，那么另一种可能是32位dotnet SDK在计算机的`PATH`上优先。遵循[此答案中的说明](https://stackoverflow.com/questions/67049414/windows-or-visual-studio-2022-cant-find-the-latest-installed-net-sdk-due-to-bi/67049415#67049415)查看这是否适用于您的情况以及如何修复。

## 项目不知道如何运行名为'Terraria'且命令'Executable'的配置文件
![unknown (8)](https://user-images.githubusercontent.com/4522492/193676280-b8d1cd7c-5b64-4a98-b7d8-d3ce7e936429.png)
这意味着您跳过了本页顶部提到的安装`.NET desktop development`工作负载。[先决条件](#Prerequisites)

## 错误 CS0246 找不到类型或命名空间名称'ReLogic'
确保您使用的是更新的.csproj，此错误将自动修复。请参阅[为旧模组创建或更新现有项目](#creating-or-updating-an-existing-project-for-an-old-mod)来更新您的csproj。

## 错误 CS0246 找不到类型或命名空间名称'Mod'（或'ModLoader'、'ModItem'等）
您已添加了对原版Terraria.exe的引用，请移除它并添加对模组化Terraria.exe的正确引用。

## CS5001 程序不包含适合入口点的静态'Main'方法
确保在项目属性中将"Output type"设置为"Class Library"。

## 对其他类的引用停止工作了！（例如：：ModItem、：ModProjectile等。）
只需重启Visual Studio，它应该会自行修复。
如果不行，您可以尝试删除模组源根目录中的.vs文件夹，然后重新加载项目。

## 加载预编译的编辑并继续dll失败 System.IO.FileNotFoundException: 找不到文件'C:\...\YYY.pdb'。
当项目不是用模组框架生成器创建时会发生此错误。要修复此错误，请转到项目的属性（在Solution Explorer中右键点击项目并点击`Properties`），转到`Build`选项卡，点击`Advanced`按钮，然后将`Debugging Information`的值更改为`Full`。

## VS 2019一直打开而不是VS 2022
如果您发现打开`.csproj`文件会打开VS 2019而您已安装2022，可以通过更新`.sln`文件来解决此问题。首先，打开VS 2022，然后使用`File->Open->Project/Solution...`直接打开`.csproj`。接下来，打开`Solution Explorer`面板并点击顶部的`Solution`行。点击`File->Save SolutionName.sln as...`，然后点击`Save`。这将更新绑定.csproj的.sln文件。现在您可以关闭VS并尝试双击.csproj以验证它现在打开VS 2022。

# 其他
## 从屏幕截图确定问题
如果您在网上帮助某人解决Visual Studio工作问题，这些视觉线索将帮助识别他们的问题：

### 错误的Visual Studio版本
![image](https://user-images.githubusercontent.com/4522492/194128001-f4b38ca6-bb45-4b60-bc2a-0c4458bb83f7.png)
`Save All`图标是识别他们是否使用错误Visual Studio版本的简单方法。请将他们定向到[先决条件](#prerequisites)以下载Visual Studio 2022。

### 错误的程序
![image](https://user-images.githubusercontent.com/4522492/194130930-c0bf7aef-a397-4666-9c49-858d7f91aad5.png)
Visual Studio中的准则是虚线，Visual Studio Code有实线，Notepad++有非常小点的虚线。还要注意Notepad++如何无法完成完整的语法高亮。请将他们定向到[先决条件](#prerequisites)以下载Visual Studio 2022。

### 未保存的更改
![image](https://github.com/tModLoader/tModLoader/assets/4522492/b8a4b584-de98-47e1-818b-16d2fc45af77)
所有编辑器在编辑器标签中都有一个小*号，表示文件有未保存的更改。在Visual Studio和Notepad++中 gutter区域的颜色表示尚未保存的行。在VS中，空心gutter标记是未保存的编辑或添加，而实心标记是最近保存的更改。在Notepad++中，橙色行是未保存的，绿色行是已保存的更改。

即使更改已保存，模组也可能没有重新构建。避免这些问题最简单的方法是始终直接从Visual Studio启动，而不是在编辑器中保存更改然后在游戏中构建模组。这种方法还有一个额外好处，允许热重载和构建并继续工作，大大加快测试速度。

### 其他文件
![image](https://user-images.githubusercontent.com/4522492/194131467-a47a0e90-692a-40c3-af8b-290566b6aabd.png)
如果您看到"Miscellaneous Files"而不是模组名称，则用户没有打开他们的`.csproj`，而是直接打开了`.cs`文件。其他线索包括类和方法调用的语法高亮缺失。请将他们定向到[打开您的项目](#opening-your-project)。