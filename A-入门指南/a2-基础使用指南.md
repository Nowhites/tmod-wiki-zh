- **为玩家和模组制作者学习tModLoader的基础知识**
- 如果遇到问题，请参阅[使用常见问题](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ)

# 安装tModLoader
[阅读我们的安装说明](tModLoader-guide-for-players)

# 安装模组
- 模组是带有".tmod"扩展名的文件。获取模组最常见的方式是通过游戏内Steam创意工坊菜单。这也适用于GOG用户。从主菜单，点击`创意工坊`，然后点击`下载模组`。您可以搜索并找到要下载的模组。或者您可以直接访问[tModLoader Steam创意工坊](https://steamcommunity.com/app/1281930/workshop/)并在该网站上订阅模组。
    - 强烈建议您访问网站和/或阅读您要下载的模组描述。许多模组会更改游戏机制，因此熟悉这些更改将有助于避免困惑。
- 如果您手动下载模组，可以将其放入[模组](#模组)文件夹中。请注意，此模组不会自动更新，会在模组菜单中显示为紫色，以提醒您它是手动安装的。手动安装模组主要用于帮助测试模组的用户，普通用户应始终使用创意工坊方法。

# 启用模组
使用`创意工坊->管理模组`菜单设置您希望使用的模组，然后点击返回重新加载。

# 与非tModLoader用户一起玩
**tModLoader不会连接到非tModLoader的Terraria服务器。**
如果您想一起玩，您必须使用Terraria，或者您的朋友必须安装并启动tModLoader。将它们视为2个完全独立的游戏。

您可以在[这里](Starting-a-modded-server)了解更多关于运行模组服务器的信息。

# 世界和玩家备份
模组游戏本质上容易出现bug，而bug可能导致您的世界或玩家无法游玩。因此，tModLoader会维护世界和玩家文件的多重备份。玩家备份位于`Terraria\TMLSAVEDIRECTORYHERE\Players\Backups`，世界备份位于`Terraria\SAVEFOLDERNAMEHERE\Worlds\Backups`。（将`SAVEFOLDERNAMEHERE`替换为与您的tModLoader版本对应的[保存目录](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)。）如果您发现无法再加载世界或玩家，可以通过从这些备份之一恢复文件来回滚到之前的存档。您将丢失自上次保存以来的进度，但总比丢失一切好。

## 恢复备份
在备份文件夹中，您会发现许多zip文件。文件的命名方式是备份日期后跟玩家或世界的名称。这些说明将展示恢复世界备份的过程，但对于玩家备份，除了文件夹和文件名外，过程完全相同。例如，如果您的`Terraria\SAVEFOLDERNAMEHERE\Worlds\Backups`文件夹中有文件`2021-02-21-CoolTown.zip`和`2021-02-19-CoolTown.zip`，那么您有2个"CoolTown"世界的备份，一个是2月21日，一个是2月19日。如果当前的"CoolTown"在tModLoader中无法加载，您可以尝试按反向时间顺序恢复备份，直到最终成功。首先，让我们备份当前无法工作的世界文件。这样做是因为问题可能是您使用的损坏模组，如果是这样，您会希望能够恢复到最新存档。找到`WorldName.twld`和`WorldName.wld`，复制并粘贴到安全位置，类似于`Terraria\SAVEFOLDERNAMEHERE\TempWorldBackup\`的位置就可以了。确保"文件扩展名"可见，这样您可以确定您备份的是`.twld`和`.wld`文件而不是`.twld.bak`和`.wld.bak`文件。如果您找不到这些文件，您可能在云上有世界，如果是这样，先将世界从云上移下来，然后按照步骤操作。
![](https://i.imgur.com/4m69DnM.png)
现在您已备份了最新存档，从`Terraria\SAVEFOLDERNAMEHERE\Worlds\Backups`文件夹中打开您希望恢复的世界备份的最新.zip文件，并将文件复制到剪贴板。导航回Worlds文件夹并粘贴。当被询问时，请确保说"替换目标中的文件"。
![](https://i.imgur.com/kesLiid.png)
![](https://i.imgur.com/8QSH6sF.png)
现在，打开tModLoader并尝试加载世界。如果成功，很好，如果失败，请尝试使用第二新的备份。您可以使用作弊模组如[Cheat Sheet](https://steamcommunity.com/sharedfiles/filedetails/?id=2563784437)或[HEROs Mod](https://steamcommunity.com/sharedfiles/filedetails/?id=2564645933)来恢复丢失的物品（如果您愿意的话）。如果您无法加载备份，您的问题可能是损坏的模组。您可以[阅读client.log](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ#reading-clientlog)以获取有关哪个模组出错的提示，或者来[Discord聊天](https://discord.gg/tmodloader)寻求帮助。

# 路径
以下是一些有用的默认路径：

## 安装
在Steam中右键点击库中的`tModLoader`，然后将鼠标悬停在`管理`上并点击`浏览本地文件`。打开的文件夹就是安装文件夹。

<details><summary>视频说明</summary><blockquote>

https://github.com/tModLoader/tModLoader/assets/4522492/0dac7f15-520e-434e-b180-5c584583ddf3

</blockquote></details>

如果您使用GOG，游戏安装在与安装Terraria相同的任何位置。安装说明指示用户在Terraria安装文件夹旁边安装tModLoader。默认位置列在下一个子部分中。如果您有桌面或开始菜单快捷方式，可以右键点击它并选择`打开文件位置`直接打开安装目录位置。

### 默认安装位置
**Steam:**
Windows安装：`C:\Program Files (x86)\Steam\steamapps\common\tModLoader`
Linux安装：`~/.local/share/Steam/steamapps/common/tModLoader`或`~/.steam/steam/steamapps/common/tModLoader`
Mac安装：`Library/Application Support/Steam/steamapps/common/tModLoader`

**GOG:**
**注意：**这些位置只有在Terraria文件夹存在于同一位置时才适用，因为tModLoader必须安装在Terraria旁边。
Windows安装：`C:\Program Files (x86)\GOG Galaxy\Games\tModLoader`或`C:\GOG Games\tModLoader`
Linux安装：`$HOME/GOG Games/tModloader`
Mac安装：`/Applications/tModLoader`

## 保存
每个tModLoader主要版本都使用自己的保存文件夹。在下一节中将`SAVEFOLDERNAMEHERE`替换为与您当前版本匹配的版本。
* `1.3` - `ModLoader`
* `1.4.3` - `tModLoader-1.4.3`
* `1.4.4` - `tModLoader`
* `1.4.4 Preview` - `tModLoader-preview`

Windows保存：`%UserProfile%\Documents\My Games\Terraria\SAVEFOLDERNAMEHERE`（通常位于`C:\Documents\`）
Linux保存：`~/.local/share/Terraria/SAVEFOLDERNAMEHERE/`或`$XDG_DATA_HOME/Terraria/SAVEFOLDERNAMEHERE/`
Mac保存：`~/Library/Application support/Terraria/SAVEFOLDERNAMEHERE/`

### 云
Steam在计算机上保留云数据的本地副本。如果需要将云玩家或世界从Terraria或tModLoader复制到本地保存，这会很有用。tModLoader数据将在tModLoader子文件夹中：
Windows云保存：`C:\Program Files (x86)\Steam\userdata\[some number here]\105600\remote`

浏览器云访问：[Steam远程存储](https://store.steampowered.com/account/remotestorage)

## 模组
您手动安装的模组存储在[保存](#保存)文件夹的名为`/Mods`的子文件夹中。创意工坊模组存储在`C:\Program Files (x86)\Steam\steamapps\workshop\content\1281930`，但您不应该触摸该文件夹，而应该在steam中取消订阅。

## 日志
日志位于[安装](#安装)文件夹中名为`/tModLoader-Logs`的子文件夹中。如果您为支持游戏破坏性问题而共享日志，请发布所有日志文件。如果问题发生在游戏中，只需`client.log`和/或`server.log`（如果相关）就足够了。