___

**[我不想只玩模组，我想制作模组](tModLoader-guide-for-developers.md)**

___

**[我不想只玩模组，我想为tModLoader做贡献](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-contributors)**

___

## Steam安装
要在Steam上安装[tModLoader](https://store.steampowered.com/app/1281930/tModLoader)，您需要在Steam上拥有[Terraria](https://store.steampowered.com/app/105600/Terraria)。
只需在Steam上浏览[tModLoader](https://store.steampowered.com/app/1281930/tModLoader/)并安装它。
tModLoader安装将与原版安装并存，允许您同时玩原版和模组版，而不必麻烦地重新安装原版。
请注意，使用家庭共享安装Terraria的用户需要按照下面的[Steam家庭共享安装](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-players#steam-family-share-installation)说明进行操作。

默认情况下，Steam将安装当前稳定版的tModLoader。目前稳定版基于Terraria v1.4.4内容。

### 测试分支
tModLoader定期更新。大多数用户会希望使用默认版本，但用户可以使用Steam的测试分支功能切换到旧版或预览版tModLoader。旧版tModLoader对于玩尚未更新到最新稳定版tModLoader的模组很有用。预览版tModLoader对于测试新功能或测试Terraria更新中添加但尚未稳定的新内容很有用。

> **注意：** 如果您切换tModLoader版本以使用特定模组，请注意，您喜欢的其他模组可能不在您切换到的版本上，因此您可能无法同时使用所有想要的模组。这对于tModLoader的预览版尤其如此。

以下是可用选项：
* `default/None` - 这是我们期望玩家使用的稳定版本。它目前基于`Terraria 1.4.4.9`内容。
* `1.3-legacy` - 这是最新的1.3版本。有一些仅在1.3 tModLoader上可用的令人怀念的模组。
* `1.4.3-legacy` - 这是最新的1.4.3版本。
* `preview-v2026.X` - 此版本包含即将推出的更改。这是模组开发者测试新功能的地方。用户应注意，不建议在预览版上完整体验游戏。模组开发者可以使用即将推出的功能发布更新，为即将到来的稳定版做准备。

### 访问1.3（旧版tModLoader）和其他测试选项
请按照以下说明切换到Steam中的不同版本tModLoader。如果您是GOG用户或需要手动安装特定版本，请改用[手动安装](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-players#manual-installation)说明。

**视频说明：**

如果这些视频说明不清楚，请阅读下面的**书面说明**。

https://github.com/tModLoader/tModLoader/assets/4522492/4ac8e231-c409-48e9-a3e0-9024b78f2af1

<details><summary>书面说明：</summary><blockquote>

转到库：

![image](https://user-images.githubusercontent.com/59670736/169886058-3eb1b43c-a113-468b-8213-ef0bcccc8e01.png)

找到tModLoader：

![image](https://user-images.githubusercontent.com/59670736/169886128-43f95278-a2a4-4b13-bb7c-1b670c81b657.png)

右键点击tModLoader：

![image](https://user-images.githubusercontent.com/59670736/169886174-dfe0612b-75d9-4469-8f13-be3649fd35fc.png)

点击属性打开Steam游戏控制面板

![image](https://user-images.githubusercontent.com/59670736/169886269-f3a0e854-bbe6-4c2f-ab4c-6980406fea51.png)

选择Betas

![image](https://user-images.githubusercontent.com/59670736/169886307-e60be211-d331-443f-bcde-109f61c23323.png)

在下拉菜单中，选择您想要的任何测试版本

![image](https://user-images.githubusercontent.com/59670736/169886370-5a340164-ccfe-4520-a3f5-515fd81671cf.png)

关闭提示（无需代码）

![image](https://user-images.githubusercontent.com/59670736/169886435-b9cd7a18-eeb9-46f8-a41f-3dc450be8702.png)

</blockquote></details>

### 如何卸载？
只需右键点击tModLoader -> 管理 -> 卸载。
您的原版安装不会受损。

### Steam家庭共享安装
由于某些原因，如果您不拥有Terraria而是使用家庭共享的Terraria，tModLoader将无法启动并将带您进入Steam商店。这是Steam方面的限制，但是1.4.X和1.3版本都存在变通方法。
请注意，这些变通方法不允许Steam多人游戏，因此您需要使用`IP连接`选项进行多人游戏


在1.4.X版本上，变通方法是按照手动安装说明安装tModLoader，并使用文件中包含的`start-tModLoaderFamilyShare.bat/sh`启动它。或者，如果您能够让Steam在Steam客户端中下载文件，理论上也可以，但用户的成功率各不相同。
您也可以将此启动设置为库中的`非Steam游戏`，以启用游戏中的Steam覆盖。

在1.3版本上，变通方法是像平常一样通过steam安装tModLoader，然后将Terraria安装文件夹中的`steam_appid.txt`文件复制到tModLoader安装文件夹，覆盖现有文件。

## 手动安装
此安装适用于从GOG购买Terraria或希望安装特定版本tModLoader的玩家。每次要更新tModLoader时都应重复这些步骤。

安装tModLoader相对容易。

1. 前往**[发布页面](https://github.com/tModLoader/tModLoader/releases)**并下载您想要的tModLoader版本。
    1. `Default/None/1.4.4`：访问**[最新版本](https://github.com/tModLoader/tModLoader/releases/latest)**并下载`tModLoader.zip`。
    2. `较早的1.4.4稳定版`：访问**["stable"发布页面](https://github.com/tModLoader/tModLoader/releases?q=stable&expanded=true)**并找到您想要的版本。展开标有"Assets"的部分并下载`tModLoader.zip`。
    3. `1.3`：访问["1.3"发布页面](https://github.com/tModLoader/tModLoader/releases?q=%22v0.11.8%22)并找到最新条目。展开标有"Assets"的部分并下载与您的操作系统对应的`tModLoader.OS.Version.zip`文件
    4. `1.4.3`：访问["1.4.3"发布页面](https://github.com/tModLoader/tModLoader/releases?q=%221.4.3%22)并找到最新条目。展开标有"Assets"的部分并下载`tModLoader.zip`。
    5. `1.4.4-preview`：访问["preview"发布页面](https://github.com/tModLoader/tModLoader/releases?q=preview)并找到最新条目。展开标有"Assets"的部分并下载`tModLoader.zip`。如果您想跟上更改，需要定期手动安装新版本的1.4.4-preview。
2. 将下载的zip的**内容**解压到Terraria安装文件夹旁边或内部的名为`tModLoader`的文件夹中。
在1.4上，如果文件夹包含'Build'文件夹，您将需要删除这个中间文件夹并将内容上移一层。（GOG通常安装到`C:\GOG Games`，Steam安装到`C:\Program Files (x86)\Steam\steamapps\common\Terraria`。如果自定义了安装位置，请参见[此视频](https://github.com/tModLoader/tModLoader/assets/4522492/c49b8252-b48a-4cdd-b4cb-b032a22fa812)查找steam安装位置。）（如果您在Linux上且在GOG上拥有游戏，首选`Terraria\game`内的嵌套选项）如果您不知道如何解压zip文件，请找懂电脑的人帮助您。
    * **选项1，并排（推荐）：**
![](https://i.imgur.com/gmrBMSO.png)
    * **选项2，嵌套：**
![](https://i.imgur.com/YWaqZPO.png)
    * **请勿**将tModLoader文件直接安装到Terraria文件夹中。此选项在Mac上的GOG不支持。
3. [此步骤仅适用于1.3]根据您拥有的游戏版本删除或添加Steam文件：
    1. 如果您使用的是Terraria的GOG版本，请从刚将tModLoader解压到的文件夹中删除Steam文件（这些文件可能已从您下载的zip中删除）：
        * Windows：`steam_api.dll`
        * Linux：`lib/libsteam_api.so`和`lib64/libsteam_api.so`
        * Mac：`tModLoader.app/Contents/MacOS/osx/libsteam_api.dylib`
    2. 如果您使用的是Terraria的Steam版本，如果zip中缺少Steam文件，请从Terraria安装目录复制到tModLoader安装目录：
        * 复制`steam_appid.txt`，然后根据您的平台：
            * Windows：`steam_api.dll`和`CSteamworks.dll`
            * Linux：`lib/libsteam_api.so`、`lib/libCSteamworks.so`、`lib64/libsteam_api.so`和`lib64/libCSteamworks.so`
            * Mac：`tModLoader.app/Contents/MacOS/osx/libsteam_api.dylib`和`tModLoader.app/Contents/MacOS/osx/CSteamworks`
5. [仅限1.4] GOG用户需要安装Steam（如果尚未安装）。我们的模组浏览器使用一些Steam安装文件来访问Steam创意工坊。您不需要为此拥有一个账户/登录。
    1. Linux特殊说明：如果您的steam安装未被识别（错误"无法访问steam创意工坊"），请尝试通过终端从APT仓库卸载并重新安装Steam
    2. 如果由于某些原因无法在系统上安装Steam，tModLoader仍然可以工作，但无法在游戏中下载模组。模组必须手动安装。
6. [仅限Linux/Mac]使`start-tModLoader.sh`、`start-tModLoaderServer.sh`和`start-tModLoader-FamilyShare.sh`可执行。您可以在文件属性中执行此操作，也可以在目标安装目录中的终端运行`chmod +x start-tModLoader.sh`命令（对其他2个文件名执行相同操作）。
7. 完成。您现在可以为tModLoader创建桌面快捷方式并从中启动tModLoader。（文件`start-tModLoader.bat`启动游戏，这就是您可以创建快捷方式的目标。）

提示：这是一个找到Terraria文件位置简便方法：（[视频示例](https://github.com/tModLoader/tModLoader/assets/4522492/c49b8252-b48a-4cdd-b4cb-b032a22fa812)）

1. 在Steam游戏库中找到Terraria，右键点击它，然后点击'属性'
2. 转到'本地文件'选项卡，然后点击'浏览本地文件...'按钮
3. 您现在就在Terraria文件夹中（这就是您应该安装tModLoader的地方）

### 手动安装常见问题
仅限Windows 1.3：如果游戏根本无法启动，您可能没有安装.NET 4.5或XNA 4.0。下载并运行两个安装程序：
1. [Microsoft .NET Framework 4.5](https://www.microsoft.com/en-us/download/details.aspx?id=30653)
2. [Microsoft XNA Framework Redistributable 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=20914)

Linux/Mac：从Steam启动时，Steam显示游戏正在启动，然后按钮立即返回"播放"：这很可能是由于未使`.sh`文件可执行造成的。请重新访问上面[手动安装说明](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-players#manual-installation)的步骤6。

### 如何卸载？

1. 打开Steam，转到您的游戏库部分并找到Terraria。
2. 让Steam**[验证游戏文件的完整性](https://support.steampowered.com/kb_article.php?ref=2037-QEUH-3335)**，这将重新配置您的游戏文件以运行原版。
4. 完成。您可以像平常一样启动Terraria。

如果您使用GOG，只需删除之前创建的tModLoader文件夹即可。您的存档和玩家将被保存。

## 降级tModLoader
1.4 tModLoader每月更新一次。有时您使用的模组无法及时更新，将无法与最新的tModLoader 1.4版本一起使用。其他时候，影响您设置的罕见错误可能没有被发现。如果是这样，您可以手动降级。要手动降级，请在**[发布](https://github.com/tModLoader/tModLoader/releases)**页面上找到您以前使用的版本的最新发布并下载它。这是一个`tModLoader.zip`文件。要轻松找到上次更新的最新版本，请在搜索栏中输入"stable"并按回车键。这将筛选出所有"preview"版本。向下滚动找到您需要的版本。例如，如果您启动tModLoader并且它最近更新到`v2022.06+`，但它停止与一个重要模组一起工作，您可以找到最新的`v2022.05+`稳定版并下载它。

下载后，打开tModLoader安装目录并删除所有文件。确保您在安装文件夹中而不是存档文件夹中。要找到安装目录，右键点击tModLoader，点击`管理`，然后`浏览本地文件`。[此视频](https://github.com/tModLoader/tModLoader/assets/4522492/c49b8252-b48a-4cdd-b4cb-b032a22fa812)显示了此过程。删除原始文件后，您可以获取下载的.zip中的文件并将它们放入安装文件夹中。[此视频](https://github.com/tModLoader/tModLoader/assets/4522492/fd4fca84-725f-4b97-862d-8f659fc3abdb)显示了此过程。Linux和Mac用户还需要使文件可执行，请参见上面[手动安装说明](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-players#manual-installation)的步骤6。

一旦知道过时的模组已更新，您可以删除安装目录中的所有文件，并使用steam验证游戏完整性以升级回当前的tModLoader版本。

要使用1.3，只需在tModLoader测试菜单中选择`1.3-legacy`：[视频](https://github.com/tModLoader/tModLoader/assets/4522492/3b398cd6-8652-48eb-bd6f-99e8f89672fd)

## 双重安装 - 同时安装1.3和1.4 tModLoader
如果您利用Steam添加非Steam游戏的功能，可以同时保留1.3和1.4 tModLoader。为此，首先切换到`1.3-legacy`并确保下载完成。打开安装文件夹并复制所有文件。向上一级并创建一个tModLoader13文件夹。进入tModLoader13文件夹并粘贴文件。在Steam中，切换回tModLoader上的默认测试分支。接下来，点击`游戏`菜单并点击`将非Steam游戏添加到我的库`。点击`浏览...`并导航到tModLoader13文件夹，很可能这是"C:\Program Files (x86)\Steam\steamapps\common\tModLoader13"。点击"tModLoader.exe"，点击"打开"，然后点击"添加选定的程序"。最后，在库中右键点击第二个tModLoader条目并点击属性，然后将"tModLoader"改为"tModLoader 1.3"并关闭窗口。现在您在库中有了旧版tModLoader和自动更新的1.4 tModLoader。

## 从1.3迁移到1.4的所有内容
在大多数情况下，从1.3到1.4的过渡应该是全新的开始。这两个实际上是不同的游戏，可用的模组也不会匹配。但是，如果您愿意，可以迁移现有的模组、世界和玩家。
### 模组
请注意，您在1.3上使用的大多数模组可能不在1.4上。您可以在"下载模组"菜单中手动搜索模组，也可以使用模组包文件尝试一次性下载所有模组。首先，打开1.3存档文件夹并在Mod文件夹中找到enabled.json文件，这可能在`\Documents\My Games\Terraria\ModLoader\Mods\enabled.json`中。打开1.4 tModLoader，点击`创意工坊`，`模组包`，然后点击`打开模组包文件夹`。将您之前复制的enabled.json文件粘贴到此文件夹中。点击`返回`然后`模组包`刷新菜单。您应该会看到"enabled"的条目。点击`在模组浏览器中查看模组`，然后点击`下载全部`。您很可能收到一条消息，指出并非所有模组都在模组浏览器上找到。如果是这样，只需点击列表中每个模组上的下载按钮。

### 玩家
使用游戏内菜单迁移玩家。云玩家不会显示，因此如果希望复制它们，您需要切换到`1.3-legacy`并将其从云端取下。

### 世界
使用游戏内菜单迁移世界。云世界不会显示，因此如果希望复制它们，您需要切换到`1.3-legacy`并将其从云端取下。

## 安装后我的世界和角色消失了！
**tModLoader不使用您的原版世界和玩家文件。**
游戏内应该给您提供_复制_原始原版文件的选项。此选项不适用于您在Terraria 1.4中使用过的玩家和世界。
您不必担心您的原版存档被修改；它们将被复制用于模组游戏玩法。当您返回原版时，您将看到原始存档。

tModLoader使用单独的文件夹存储玩家(.plr)和社会(.wld)文件，主要是因为它会为其存储额外的数据。您的原版玩家和社会存储在：Windows的`%UserProfile%\Documents\My Games\Terraria`，分别在_Players_和_Worlds_文件夹中。

如果自动复制不起作用，请将`%UserProfile%\Documents\My Games\Terraria`中的"World"和"Player"文件夹复制到`%UserProfile%\Documents\My Games\Terraria\tModLoader`。

## 如何下载和玩模组？
tModLoader附带模组浏览器。请参阅[模组浏览器指南](Mod-Browser)了解如何下载和玩模组。

## 我的内存太少，无法运行多个模组！
Terraria和1.3 tModLoader是32位应用程序。简而言之，这意味着它们只能利用最多约4 GiB的RAM。使用大量模组时，您可能会耗尽内存。不幸比的是，解决方案是使用更少的模组。或者，您可以尝试使用非官方的1.3 tModLoader 64位版本。

1.4 tModLoader默认是64位的，这减轻了这个问题。

## 我使用macOS Catalina。我该怎么办？
如果遇到任何问题，请尝试使用tModLoader的64位版本或在Discord上与我们交谈。

## 如何在1.4中使用模组包？
1.4 tModLoader对模组包功能进行了重大改革，提供了多项附加功能。

首先，像平常一样保存您已启用的模组。
UI现在将显示很多按钮。让我们一起来看看。
![image](https://user-images.githubusercontent.com/59670736/180901414-3ca8f2f4-c053-4851-8de7-fa617f182db1.png)

首先，我将使用两个不同的术语：
模组包将指的是随时间不会更新的冻结模组副本。
模组集合将指的是始终最新的模组列表。

前两个按钮对"模组集合"样式进行操作。
仅启用此列表 - 禁用所有模组，然后仅加载集合中定义的模组
启用此列表 - 在任何现有已加载模组的基础上加载集合中定义的模组。用于堆叠集合

查看列表和在模组浏览器中查看模组允许您查看包/集合中的模组，并为您在模组浏览器中新鲜下载它们。

使用已启用更新列表 - 使用当前启用的模组集更新模组包或集合。将根据需要删除或添加到集合/包中

导入包（本地）- 告诉tModLoader检查模组包中要加载的冻结模组集。从包加载的任何模组（处于活动状态时）将覆盖您已下载的任何现有模组。当模组包处于活动状态时，它会在右上角显示。

删除包（本地）- 撤消导入包（本地）所做的更改

导出包实例 - 将ModConfigs和<ModPackName>/Mods文件夹的副本导出到InstallDirectory/<ModPackName>，以便您可以使用旧版本设置tModLoader的第二个实例，或使用该包快速设置服务器。检查InstallDirectory/<ModPackName>/SaveData/Mods中的install.txt文件，列出所有创意工坊发布ID和tmlversion.txt标签要使用的tml版本。

删除实例 - 删除由导出包实例创建的导出实例文件
