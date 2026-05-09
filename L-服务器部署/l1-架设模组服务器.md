***
本页面已更新至1.4版。关于1.3的说明，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Starting-a-modded-server/dac6879dd891bfc74695d51a822379189d69f189)
***

***
本页面对于1.4.4专用服务器已过时。
请参阅我们的[DedicatedServerUtils自述文件](https://github.com/tModLoader/tModLoader/tree/stable/patches/tModLoader/Terraria/release_extras/DedicatedServerUtils)
***

-------

## 专用主机
### 如果您有自己的硬件，请跳过本节并继续进行自托管教程。

专用主机是一家提供服务器托管服务的公司。

编辑专用主机正在使用的服务器配置文件。并非每个主机都是相同的。您需要确保tModLoaderServer使用命令行参数或服务器配置行来设置Mods文件夹的路径。如果您希望使用与tModLoaderServer可执行文件相同文件夹中的Mods文件夹，我相信您可以使用相对路径，如"modpath=./Mods"。您还需要设置savedirectory，因为某些mod将mod特定数据存储在保存目录中。（savedirectory是仅限命令行的参数。）将`-savedirectory ./`添加到启动服务器的命令中，这通常可以在您的主机网站上找到。

### 已知可用的专用主机
NodeCraft - 按照[说明](https://nodecraft.com/support/games/terraria/tmodloader-server-guide)操作
    
GameServers - 将`-savedirectory ./`添加到服务器命令行 

[Akliz Hosting](https://www.akliz.net/games/terraria) - 开箱即用。 

[Citadel Servers Hosting Company](https://citadelservers.com/en-us/game-servers/terraria)

[AleForge](https://aleforge.net/games/terraria.html)

[WinterNode](https://winternode.com/games/terraria/)

-----

# 专用服务器设置前言

为1.4 tModLoader建立专用服务器有几种选择。
为简单起见，我们将仅介绍这些选项之一，但不要灰心去尝试！

所有这些信息都包含在任何tmodloader安装的DedicatedServerUtils文件夹中。

## 在Linux上设置专用服务器

如果您有一台闲置的旧计算机或整天开着计算机，您可以使用该计算机托管专用服务器。

## Docker与管理脚本
虽然Docker容器和管理脚本都可以安装和更新tModLoader及任何mod，但有几个关键区别。Docker将tModLoader与主机系统隔离并提高安全性。管理脚本允许直接访问您的服务器并因此增加控制。如果您是公共服务器运营商或更喜欢Docker，请选择[Docker容器](#使用docker容器)，否则请使用[管理脚本](#使用管理脚本)。

## 使用管理脚本
`manage-tModLoaderServer.sh`脚本可用于直接从GitHub发布版或从SteamCMD安装tModLoader。该脚本被设计为完全独立运行，因此只需将其下载到您的服务器并运行即可。不需要仓库中的其他文件。

### 安装tModLoader
#### 通过SteamCMD（推荐）
* 确保SteamCMD已安装并在PATH上。您可以从包管理器或[Valve的Wiki](https://developer.valvesoftware.com/wiki/SteamCMD)安装SteamCMD。
* 运行`./manage-tModLoaderServer.sh install --username your_steam_username`。
* 系统将提示您输入密码（如果适用，还包括2fa代码）。
* 默认情况下，tModLoader将安装到`~/Steam/steamapps/common/tModLoader`。要指定安装目录，请使用`--folder /full/path/from/root`

#### 从GitHub
* 运行`./manage-tModLoaderServer.sh install --github`。
* 默认情况下，tModLoader将安装到`~/tModLoader`。要指定安装目录，请使用`--folder /path/to/install`。
* 这将安装最新的GitHub发布版，与在Steam上发布的版本相同。

### 安装Mod
在tModLoader安装步骤期间mod将自动安装，但也可以使用`--mods-only`参数单独安装。只需将任何`.tmod`文件、`install.txt`（用于创意工坊mod）和`enabled.json`放入与脚本相同的目录。此外，您可以使用`--no-mods`参数避免更新或安装mod。

#### 获取install.txt
因为创意工坊不使用mod名称来识别mod，您必须创建一个mod包才能从创意工坊安装mod。要获取`install.txt`文件及其随附的`enabled.json`：
* 进入创意工坊
* 进入Mod包
* 点击`将已启用保存为新Mod包`
* 点击`打开Mod包文件夹`。
* 进入名称为您的mod包名称的文件夹

您可以将`Mods/enabled.json`和`Mods/install.txt`复制到脚本目录，下次运行脚本时将使用它们（运行`./manage-tModLoaderServer.sh --mods-only`立即安装mod）。
**您需要一个包含您想要启用的所有Mod的Mods/enabled.json。**

### 启动
要运行tModLoader，您只需导航到安装目录（对于GitHub为`~/tModLoader`，对于SteamCMD默认为`~/Steam/steamapps/common/tModLoader`），然后运行`./start-tModLoaderServer.sh`。还有一个`--start`参数可以启动游戏。

#### 自动选择世界
如果您想在启动时无需任何输入即可运行tModLoader（例如从初始化系统），您只需复制示例[serverconfig.txt](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/release_extras/serverconfig.txt)并根据您的喜好更改设置。其他选项可以在[Terraria wiki](https://terraria.wiki.gg/wiki/Server#Server_config_file)上找到。

## 更新
如果有可用的`manage-tModLoaderServer.sh`更新，将打印一条消息通知您可以使用`./manage-tModLoaderServer.sh --update-script`更新。过时的脚本可能包含错误或缺少功能，因此通常是个好主意。

使用`manage-tModLoaderServer.sh`时，可以使用`./manage-tModLoaderServer.sh update`执行tModLoader更新。使用GitHub安装时，使用`--github`。如果您的安装位于非标准位置，请使用`--folder`。mod也将更新。

使用Docker容器时，只需使用`docker-compose build`重建容器即可更新tModLoader。mod也将更新。

## 使用Docker容器
安装和运行容器：
* 确保已安装`docker`和`docker-compose`。它们可以从您的包管理器或[Docker的文档](https://docs.docker.com/engine/install/)安装

* 下载[docker-compose.yml](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/release_extras/DedicatedServerUtils/docker-compose.yml)和[Dockerfile](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/release_extras/DedicatedServerUtils/Dockerfile)。
* 在这些docker文件旁边，创建一个名为`Terraria`的文件夹，并将[enabled.json](#获取install.txt)、[install.txt](#获取install.txt)、[serverconfig.txt](#自动选择世界)、您的世界和任何`.tmod`文件放入其中。
* 使用您的GID和UID编辑`docker-compose.yml`。这些可以通过运行`id`找到。
* 运行`docker-compose build`
* 运行`docker-compose up`

服务器将在端口7777上可用。

要无交互地运行，使用`docker-compose up -d`，并在`Terraria`目录中包含[serverconfig.txt](#自动选择世界)。

## 启动时自动运行
使用管理脚本时，请参阅您的发行版文档。您可能可以使用初始化系统的启动脚本。

使用Docker容器时，在`docker-compose.yml`中的`services.tml`内添加`restart: always`，然后使用`docker-compose build`重建。

## 服务器配置

如果您缺少`serverconfig.txt`，则必须手动创建一个。您可以使用互联网上的配置预设或自己创建一个。

建议在此处包含的一些参数：

运行`./tModLoaderServer -config *configname*.txt`时自动加载世界，这一个对于其他一些参数生效是必需的

##### 设置在服务器上加载的世界

`world=path/to/WorldFile`

例如，`world=/home/<user>/.local/share/Terraria/ModLoader/Worlds/<worldname>.wld` **（必须是`.wld`而不是`.twld`！）**

##### 设置允许在服务器上的最大玩家数。该值必须在1到255之间。（默认=8）

`maxplayers=<number>`

##### 设置端口号（默认=7777）

`port=<number>`

##### 设置服务器密码

`password=<password>`

##### 减少敌人跳过但增加带宽使用。数字越小，跳过发生得越少，但发送的数据越多。0为关闭。

`npcstream=<number>`（建议在2-6左右，增加所需带宽但减少传送）

##### 链接mods文件夹（可能默认为此，因此可能无关）

`modpath=/home/<user>/.local/share/Terraria/ModLoader/Mods`

##### 为特定世界选择要加载的mod包。`.json`是可选的

`modpack=/home/<user>/.local/share/Terraria/ModLoader/Mods/ModPacks/<modpack name>/mods/enabled.json>`

完整列表请访问：https://terraria.wiki.gg/Server#Server_config_file

**注意**：Journey's End配置命令将无法工作，因为tModLoader运行在较旧版本的terria上！

### 端口转发

如果您希望其他人从另一个网络连接到您的服务器，则必须打开服务器将运行的端口。

#### 什么是端口？

您的计算机可以访问许多端口。当传入的网络数据包被您的互联网路由器接收时，它将附带相关的IP地址和端口号。如果IP与您的服务器IP匹配，它将被发送到您的服务器机器。端口号告诉计算机数据包**究竟**应该发送到哪个应用程序。这允许计算机同时运行许多应用程序并在网络之间进行通信。您可以在[这里](https://en.wikipedia.org/wiki/Port_(computer_networking))阅读更多内容。

#### 如何端口转发？

为了向其他网络开放端口，必须在网络路由器中明确告诉它开放。如果您在家，您需要与您的父母讨论并让他们允许您这样做。为terraria服务器打开特定端口将允许其他计算机通过terraria应用程序与您的计算机通信，**但由于terraria服务器应用程序将'消耗'端口：没有其他应用程序可以使用此端口，因此不会发生其他事情**。

#### 如何访问路由器

您典型的家庭IP以192.168开头，路由器通常位于192.168.1.1
您可以在互联网浏览器中输入此数字以导航到路由器网页。您将需要登录密码，通常显示在物理路由器设备上的某个贴纸上。建议尽可能更改此密码以提高安全性。如果您的互联网提供商为您做了这件事，您可能知道密码，如果您不知道密码，您将不得不打电话给他们讨论如何检索它。您可能需要重置路由器，因此请确保下次记住密码。

### 在Windows上启动服务器

[tModLoader安装](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)包含一个名为`start-tModLoaderServer.bat`的脚本。此脚本将在Windows上启动服务器实例。有几种方法可以使用此文件。

1. 直接启动`start-tModLoaderServer.bat`
    1. 使用`serverconfig.txt`启动服务器
    2. 将提示是否应启用Steam支持
    3. 如果`serverconfig.txt`未设置`world`属性，也将提示选择哪个世界
2. [创建快捷方式](https://github.com/tModLoader/tModLoader/wiki/Command-Line#windows-shortcut)到`start-tModLoaderServer.bat`
    1. 可以在"目标"中添加额外参数来自定义游戏启动，例如设置`-world`自动加载特定世界，`-config`使用特定配置文件，`-nosteam`跳过Steam支持提示，或任何其他支持的命令行参数。
3. 复制`start-tModLoaderServer.bat`的副本并使用与上述相同的命令行参数编辑副本
    1. 编辑`LaunchUtils/busybox-sh.bat ./start-tModLoaderServer.sh %*`行，在`%*`之前添加命令行参数。例如，`LaunchUtils/busybox-sh.bat ./start-tModLoaderServer.sh -nosteam %*`的行为将与正常行为完全相同，只是会跳过启用Steam多人游戏支持的提示。您可以创建指向此.bat文件的桌面快捷方式以方便使用。
4. 创建一个新的`.bat`文件
    1. 在文件中键入`start-tModLoaderServer.bat`，然后添加任何命令行参数。例如，`start-tModLoaderServer.bat -nosteam -world "C:\Documents\My Games\Terraria\tModLoader\Worlds\MyWorld.wld"`将启动加载`MyWorld.wld`的服务器，不支持Steam多人游戏。您可以创建指向此.bat文件的桌面快捷方式以方便使用。

### 在Linux或Mac上启动服务器

[tModLoader安装](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)包含一个名为`start-tModLoaderServer.sh`的脚本。此脚本将在Linux或Mac上启动服务器实例。此脚本可以与上述Windows脚本部分相同的方式启动和自定义，只需在这些说明中将`.bat`替换为`.sh`，如果您使用第3种方法，请将命令行参数添加到`launch_args="-server"`行。您可能还需要使新的`.sh`文件可执行。

可选：建议在screen或tmux会话中运行服务器。这样，当您需要关闭终端时，不必关闭服务器。这对于仅通过SSH连接的无头服务器特别有用。

### 无头启动服务器

默认情况下，服务器在实际启动前会询问几个问题。第一个问题是是否应启用Steam多人游戏支持。如果是，它将询问服务器是否应向朋友、朋友的朋友开放或设为私人。之后，mod将加载，服务器将询问选择哪个世界。选择世界后，将向用户显示关于最大玩家数、服务器端口、端口转发和密码的其他提示。

通过一些配置，可以跳过这些提示，服务器可以"无头"启动，换句话说，它将直接开始托管特定世界，无需任何交互。要无头启动服务器，我们需要至少指定2件事，Steam多人游戏服务器支持选择和要加载的世界。

要设置Steam多人游戏服务器模式，我们可以设置以下命令行参数之一：
1. `-nosteam` - 禁用Steam多人游戏支持
2. `-steam -lobby friends` - 启用Steam多人游戏支持，允许朋友直接加入。
3. `-steam -lobby friends -friendsoffriends` - 启用Steam多人游戏支持，允许朋友和朋友的朋友直接加入。
4. `-steam -lobby private` - 启用Steam多人游戏支持，仅限邀请。

要指定要加载的特定世界：
1. 可以提供`-world ".wld文件路径此处"`命令行参数。确保这是`.wld`文件的完整文件路径，而不是`.twld`文件。例如，`-world "C:\Documents\My Games\Terraria\tModLoader\Worlds\MyWorld.wld"`。
2. 可以在`serverconfig.txt`或正在使用的任何`-config`文件中设置`world=`条目。确保这是`.wld`文件的完整文件路径，而不是`.twld`文件。例如，`world=C:\Documents\My Games\Terraria\tModLoader\Worlds\MyWorld.wld`。确保保存配置文件。在这种情况下不应使用引号，并确保删除前面的`#`。

这些自定义命令行参数可以添加到`start-tModLoaderServer.bat/sh`的副本中，如[在Windows上启动服务器部分](#在windows上启动服务器)或[在Linux或Mac上启动服务器部分](#在linux或mac上启动服务器)所示。此时，启动新的脚本文件应该可以直接启动带有指定世界的服务器。可以进行进一步编辑以自定义端口、mod、mod包以及许多其他设置（如果需要）。这些更改可以是命令行参数，也可以是对`serverconfig.txt`或正在使用的任何`-config`文件的更改。

#### 其他服务器参数
##### 设置Steam创意工坊内容目录，对于使用SteamCMD将创意工坊项目安装到自定义目录很有用
`-steamworkshopfolder /home/<user>/<Custom Workshop Mod Folder>/steamapps/workshop/`
