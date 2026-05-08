除了[原版泰拉瑞亚](https://terraria.wiki.gg/wiki/Server)提供的所有命令行和服务器配置设置外，tModLoader还添加了以下一些选项。

----
### TOBEADDEDLIST
1) -loadlib
2) -publish, -ciprep, -publishedmodfiles, -uploadfolder
3) -experimental
5) -steamworkshopfolder
6) -audiodriver -> 覆盖 SDL_AUDIODRIVER
7) -videodriver -> 覆盖 SDL_VIDEODRIVER
----

### savedirectory [pathToSaveDirectoryFolder]
服务器和客户端命令行参数
指定存档目录。默认存档目录列在[此处](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)，但不包括路径中的最后一个文件夹。`/tModLoader`（如果在1.3或1.4.3 tModLoader上，则为`/ModLoader`或`/tModLoader-1.4.3`）将自动附加到提供的路径。使用此参数可指定不同的存档目录。存档目录是世界目录和mod目录的父目录，因此您可以只使用此参数，而不必同时使用这两个参数。

### tmlsavedirectory [pathToSaveDirectoryFolder]
服务器和客户端命令行参数
与上面的`savedirectory`相同，只是`/tModLoader`不会自动附加到提供的路径。

### modpack [modpackname]
服务器和客户端命令行参数、服务器配置。
通过名称指定要使用的mod包。省略`.json`扩展名。mod包中指定的mod（仅限这些）将被加载。对于可能使用不同mod组合进行多个并发游戏的人来说，这可用于快速启动指定的mod集。

### modpath [pathToModsFolder]
服务器命令行参数、服务器配置。
指定查找`.tmod`文件的文件夹。对于托管服务器非常有用，因为默认文件夹位置通常在专用主机上无法访问。

### skipselect
服务器和客户端命令行参数
对于希望快速测试其mod的mod作者非常有用。此标志将自动选择列表中的第1个玩家和世界，允许mod作者直接进入游戏。从Visual Studio构建和启动时非常有用。

mod作者应阅读下面的[使用命令行参数进行调试部分](#使用命令行参数进行调试)来了解如何在使用此参数时轻松进行调试。

如果您想指定特定的玩家和世界，可以使用`-skipselect "MyPlayer:MyWorld"`。两者都可以省略，如果缺失则默认为第一个玩家或世界。例如，`-skipselect ":MyWorld"`将选择第一个玩家和指定的世界。这些参数根据世界名称（在游戏中看到的名称）而不是文件路径。

对于服务器，[原版泰拉瑞亚](https://terraria.wiki.gg/wiki/Server)提供的`-world`参数类似。它使用世界文件路径作为参数而不是世界名称。

### build [pathToModSource]
服务器客户端命令行参数
从源代码构建指定的mod，对mod作者有用。自动生成的csproj默认使用此参数。

### publish [modname]
服务器命令行参数
用于通过命令行发布mod。必须与`-server`和`-steam`一起使用才能正常工作。目前仅可用于发布[服务端](https://docs.tmodloader.net/docs/1.4-stable/namespace_terraria_1_1_mod_loader.html#a1c82c6b1930a8ee5c45efb091a036b06)mod。

示例：
```
-server -steam -publish MyServersideMod -publishtags Library&Content -publicity 2
```

### publishtags [modpackname]
服务器命令行参数
与`-publish`一起使用。设置此参数将覆盖当前为mod在创意工坊上设置的标签。每个标签应用`&`分隔。标签列表可在[`SteamedWraps.InitializeModTags`方法](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/Social/Steam/SteamedWraps.cs#L685)中找到。标签可以通过多种方式指定，例如对于`AddModTag("tModLoader.TagsQoL", "Quality of Life");`，可以使用"QoL"或"QualityofLife"。大小写也不重要，但必须删除空格。请注意，如果语言标签低于某个阈值，即使它们被传递到`-publishtags`，也会自动被删除。

### publicity [WorkshopItemPublicSettingId]
服务器命令行参数
与`-publish`一起使用。这将更改mod在创意工坊上的可见性。对于新mod，如果未分配此项，则默认为公共。可能的值如下：
```
Private - 0
FriendsOnly - 1
Public - 2
Unlisted - 3
```
### eac
服务器客户端命令行参数（仅限Windows）
启用"编辑并继续"功能，对mod作者有用。自动生成的csproj默认使用此参数。

### gldevice
客户端命令行参数
tModLoader使用[FNA-XNA框架](https://github.com/FNA-XNA/FNA)，这意味着FNA的启动选项可用。最适用的选项是`/gldevice:`，它将切换图形驱动程序。可用选项包括：
* `/gldevice:D3D11`（Windows上的默认选项）
* `/gldevice:OpenGL`
* `/gldevice:Vulkan`

有关FNA启动选项的更多信息，请参见[FNA文档](https://fna-xna.github.io/docs/7%3A-FNA-Environment-Variables/)。

### testservermodloading
服务器命令行参数
这在tModLoader内部用于在进入发布菜单之前测试服务器端mod。

-----------

# 使用方法
对于专用服务器配置，请按照[tModLoader安装目录](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#default-install-locations)中`serverconfig.txt`文件中的示例进行操作。（例如，"modpath=/mymods/"。基本上，[设置名称]=[设置值]）

您还可以在安装目录中创建一个`cli-argsConfig.txt`文件，其中包含的命令行参数将像正常使用一样被加载。

否则，使用脚本（Windows上的`.bat`，Linux/Mac上的`.sh`）来指定命令行参数（例如"start-tModLoader.bat -modpack friendsplaythrough"）。Windows用户还可以通过在"目标"末尾添加参数来向快捷方式文件添加参数。您还可以将命令行参数添加到Steam中游戏的"启动选项"中，这些参数仅在从Steam启动时适用。

# 示例
## cli-argsConfig.txt
在安装目录中创建一个`cli-argsConfig.txt`文件，并按您想要的方式进行编辑。参数可以在不同的行上，也可以在同一行上。这些参数甚至在调试或从Steam启动时也会加载，这使得它对于您想始终应用的选项非常方便。例如：

```
-tmlsavedirectory TestSaveDirectory
-skipselect
```

## Windows快捷方式
通过复制然后粘贴快捷方式，或通过右键拖动文件到另一个位置并选择"在这里创建快捷方式"，创建到`start-tModLoader.bat`文件的快捷方式。接下来，右键单击属性并适当编辑"目标"。

### 使用特定Mod包启动Windows客户端
请参见图片：
![](https://github.com/user-attachments/assets/eb1fea0b-81f4-4539-921d-e64f9b618ffc)


------

## 使用命令行参数进行调试
### 在Visual Studio中添加命令行参数
本节假设您已按照[使用Visual Studio开发](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio)指南完成了工作开发环境设置。

要添加调试参数，请打开mod项目的调试属性。为此，请单击"调试目标"按钮旁边的小黑色箭头。"调试目标"按钮有一个绿色箭头，此时可能标记为"Terraria"。在下拉菜单中，选择"[ModName]调试属性"：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/085c5d55-79ee-420b-b784-362e9896fc40)

这将打开一个名为"启动配置文件"的窗口。在此窗口中找到"命令行参数"部分。这是分配调试时使用的命令行参数的地方。默认值应为`$(tMLPath)`。可以将更多参数添加到此条目。最有用添加的参数是`-skipselect`（有关更多详细信息，请参见上面的[skipselect部分](#skipselect)）。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/129048fe-099c-4a34-b781-dc2acb9ef524)

如果您正在调试服务器而不是客户端，请确保选择`TerrariaServer`启动配置文件，然后更改"命令行参数"。服务器启动配置文件的默认值应为`$(tMLServerPath)`。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/981643b7-ad2b-4255-aa99-8d66a577bcea)

### 启动配置文件
默认情况下，您的mod项目将附带2个启动配置文件：`Terraria`和`TerrariaServer`。这些将分别启动和调试客户端和服务器。在上一节中，我们向其中一个默认启动配置文件添加了`-skipselect`，以通过绕过玩家和世界选择菜单来简化调试。这很有用，但我们可能并不总是想要这种行为，编辑启动配置文件以添加或删除命令行参数可能会很繁琐。

为避免这种情况，我们可以创建一个具有新行为的新启动配置文件。当我们想要新行为时，只需从下拉菜单中选择启动配置文件，然后单击它即可开始使用该配置文件进行调试。

首先，再次打开"启动配置文件"菜单：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/b53d3a71-df77-49f8-89ff-34fdcdccdb06)

选择要复制的启动配置文件（客户端为`Terraria`，服务器为`TerrariaServer`），然后单击"复制所选配置文件"：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/d401a316-f51a-4e62-bbbc-3a60b245660d)

接下来，单击"重命名所选配置文件"并为其指定合适的名称。在这个例子中，我们将配置文件命名为`TerrariaMyTestWorld`，因为我们将使用`-skipselect`参数启动一个名为"MyTestWorld"的世界。接下来，编辑"命令行参数"部分并添加您想要的参数。在这个例子中，添加了`-skipselect ":MyTestWorld"`。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/e351d327-8716-40d4-b147-5c0e5e023a24)

关闭"启动配置文件"窗口。从现在开始，您可以使用"调试目标"下拉菜单选择一个启动配置文件，然后单击它以使用该配置文件开始调试。
![image](https://github.com/tModLoader/tModLoader/assets/4522492/91285ccf-d6f8-4a08-8b19-a9fbe5a4d481)

启动配置文件存储在[`Properties/launchSettings.json`文件](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Modding-Guide#mod-skeleton-contents)中。如果更方便的话，可以手动编辑该文件，只需确保遵循json语法即可。

### 启动配置文件 ideas
使用启动配置文件来简化mod开发可以节省时间。以下是有关如何使用启动配置文件的更多 ideas：

* 使用[`tmlsavedirectory`](#tmlsavedirectory-pathtosavedirectoryfolder)使用单独的存档位置。这可用于组织或测试您不想影响正常tModLoader客户端的特定设置。
* 使用[`modpack`](#modpack-modpackname)使用特定mod包启动游戏。这可用于隔离测试mod或针对特定可选依赖项测试mod。
