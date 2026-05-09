***
本指南仅适用于1.3版本。[Workshop](https://github.com/tModLoader/tModLoader/wiki/Workshop)页面是您找到1.4版本tModLoader发布模组信息的地方。
***

模组浏览器是tModLoader的一个在线组件，模组制作者可以在此发布他们的模组。用户可以直接从tModLoader中轻松找到、下载和安装他们感兴趣的模组。强烈建议您访问网站和/或阅读要下载的模组描述。

### 直接下载
如果您在游戏中遇到问题，可以使用[直接模组下载列表](http://javid.ddns.net/tModLoader/DirectModDownloadListing.php)直接下载模组。将它们放置在`%userprofile%/Documents/My Games/Terraria/ModLoader/Mods/`

***
**想要将模组发布到模组浏览器的模组制作者应继续阅读。**
***

## 入门
首次尝试通过"Mod Sources"菜单中的"Publish Mod"按钮发布模组时，您将被引导至[模组浏览器注册页面](http://javid.ddns.net/tModLoader/register.php)，在那里您将通过Steam进行身份验证以确认您的身份。（您不需要拥有Steam上的Terraria，只需要一个账户）模组浏览器确认您的身份后，将提供一个"Passphrase"（密码短语），它将被粘贴到tModLoader中。现在您有了密码短语，就可以在模组浏览器上发布模组了。

> 注意：由于1.3 tModLoader已是旧版，不再可能进行注册。如果您想发布模组，请为当前版本的tModLoader制作模组。

## 发布模组
发布模组很简单，在确保您的模组已准备好发布后，只需在"Mod Sources"菜单中按"Publish"按钮。但是，在发布之前，请执行以下操作：

### 设置版本号
在build.txt中设置版本号。初始版本使用`0.1`这样的版本号很好。请使用语义化版本控制：`major.minor.patch`不允许在版本号中使用字母

### 填写description.txt
这是潜在用户在决定是否下载时会阅读的描述，请写好。

### 测试
要充分测试。您不希望自己的模组因为首次发布质量不佳而迅速受到差评。

### 制作网站
大多数模组会在[Terraria社区论坛](https://forums.terraria.org/index.php?forums/client-server-mods-tools.116/)上开设帖子。开设帖子是与用户互动的好方式。他们可以提供错误报告、批评或赞扬。开设帖子后，复制URL并将其粘贴到build.txt的`homepage`属性中。如果不确定build.txt文件的布局，请参阅ExampleMod。保存build.txt。

### 构建
保存所有文件并在Mod Sources菜单中构建模组。

### Github
如果您有模组的Github仓库，请推送所有更改。

### 发布
在Mod Sources菜单中点击"Publish"。几分钟后，您的用户将看到他们喜爱的模组有新版本可以下载了。

## 更新模组
要更新您已发布的模组，首先执行以下操作：

### 更新版本号
在build.txt中增加版本号。您的版本号应该是用句点分隔的2到4个数字。不要添加"v"或"beta"等字母。[阅读有关版本号的信息](https://msdn.microsoft.com/en-us/library/system.version(v=vs.110).aspx#Anchor_6)。注意：一些例子：0.1等同于0.001，0.0.0.10紧随0.0.0.9之后。

### 更新描述
用任何您希望潜在用户知道的新信息更新description.txt。在这里添加最近的更改日志可能是个好主意。

### 测试
充分测试您的更改。通过加载旧版本的模组的存档来测试ModPlayer和ModWorld类中的保存和加载代码。

### 构建
保存所有文件并在Mod Sources菜单中构建模组。

### Github
如果您有模组的Github仓库，现在请推送所有更改，以便您的发布与源代码保持一致。

### 发布
在Mod Sources菜单中点击"Publish"。几分钟后，您的用户将看到他们喜爱的模组有新版本可以下载了。

### 更新网站
如果您有网站，现在是宣传更新和更改日志的好时机。

## 取消发布模组
警告！此菜单中的按钮没有任何确认提示！您可以通过进入"Mod Sources"然后"Manage Published"并点击"Unpublish"来在游戏中取消发布模组。只有在模组质量较低时才执行此操作。

## .tmod文件大小限制
模组的默认大小限制为5 MB。如果您已将模组浏览器链接到您的GitHub仓库，则可以达到50 MB。如果您发现模组太大，请检查模组的源文件夹中是否有不应存在的大文件。默认情况下，文件夹中的所有文件都将被打包到.tmod文件中。使用build.txt中的buildIgnore来指定要忽略的文件夹或文件，或删除有问题的文件。常见的罪魁祸首包括photoshop或其他图像编辑项目文件。

## 模组图标
可以通过在模组源文件夹的根目录添加80x80的`icon.png`来为模组添加图标。作为使用GitHub并链接仓库的激励，您的图标将显示在模组浏览器菜单以及Mods菜单中。

如果您想使用模板：
![](https://i.imgur.com/uluOTmD.png)

## GitHub集成
模组浏览器使用大量带宽，超出了我们能承受的范围。为了缓解这些限制并鼓励开源软件，将其GitHub仓库链接到模组浏览器的模组将享受多项好处，包括更大的文件大小（从5 MB增加到50 MB）、旧版本的历史记录，以及在模组浏览器菜单中可见的模组图标。

要进行集成，请访问[模组浏览器注册页面](http://javid.ddns.net/tModLoader/register.php)，登录，点击"Manage Mods"，然后"Submit GitHub Authorization"。之后，请非常、非常仔细地按照说明进行操作。成功后，下次您发布模组时将享受这些好处。

### 高级GitHub集成
如果您按设计使用Github，即作为源代码仓库，您可以使用Github Actions自动化模组的发布。要做到这一点，请访问您的仓库并点击`Actions`按钮。然后，点击`Skip this and set up a workflow yourself`。将以下内容复制并粘贴到文本框中。
```
name: Push on Master jobs

on:
  push:
    branches: [ master ]

jobs:
  automaticpublish:
    name: Automatic Mod Browser Publish Integration
    runs-on: windows-latest
    steps:
      - uses: actions/checkout@v2
       
      # Mod Browser integration
      - uses: JavidPack/tModLoaderPublishIntegration@v0.11.8.8
        with:
          STEAMID64: ${{secrets.STEAMID64}}
          MODBROWSERPASSPHRASE: ${{secrets.MODBROWSERPASSPHRASE}}
          MODNAME: YourModNameHere
```
现在，用您的模组的内部名称替换`YourModNameHere`。提交此文件。接下来，访问`Settings->Secrets`并创建一个名为`STEAMID64`的仓库密码，包含您的steamid64，再创建一个名为`MODBROWSERPASSPHRASE`的密码，包含您当前的模组浏览器密码短语。完成这些步骤后，下次您将提交推送到master分支并且build.txt中的版本号增加时，模组将使用指定的tModLoader版本自动发布。随着tModLoader的更新，`tModLoaderPublishIntegration`也会更新，因此请通过将`JavidPack/tModLoaderPublishIntegration@v0.11.8.8`更改为您希望发布所使用的tModLoader版本来确保更新使用的版本。

## 重命名模组
[build.txt](https://github.com/tModLoader/tModLoader/wiki/build.txt)中的displayName决定了用户在交互模组时将看到的名称。内部名称是另一个与模组相关的名称。内部名称在创意工坊上必须是唯一的，因此您可能会发现您为模组选择的内部名称在发布时已被使用。或者，您可能出于其他原因想要重命名内部名称。由于各种因素，不建议更改模组的内部名称，但可以做到：

TODO：此处的步骤。

## 限制
### Terraria社区论坛规则
发布到模组浏览器的模组必须遵守[TCF模组规则](https://forums.terraria.org/index.php?threads/player-created-game-enhancements-rules-guidelines.286/)。需要注意的规则包括禁止从非PC版Terraria移植内容的规定以及禁止剽窃的规定。违反这些规定将导致模组浏览器权限被撤销。

### 模组图标指南
模组图标中的任何裸露或不适当内容将导致特别严重的处理。
什么会被视为不适当？你可能能猜到，但这里还是有一些例子：裸露/色情、粗口、对人类或动物的暴力/伤害，以及鼓励破坏公物、犯罪、恐怖主义、种族主义、饮食失调或自杀的图标。等等……请使用适当的图标。

### 恶意代码
不要这样做。您上传的模组带有您的Steam身份标记。您将**被永久禁止**使用浏览器，您的所有模组将被删除。我们还将就您和您的恶意代码通知Terraria和Steam工作人员。

## 常见问题（FAQ）
### 没有人能下载我的模组
如果您完成了github集成然后将仓库设为私有，就会发生这种情况。链接到模组的仓库必须是公开的，这样模组浏览器才能重定向到发布版本。

### 它不发布我的模组
请重新阅读[模组浏览器注册页面](http://javid.ddns.net/tModLoader/register.php)上的说明。

### 我无法注册/访问被拒绝
由于1.3 tModLoader已是旧版，不再可能进行注册。如果您想发布模组，请为当前版本的tModLoader制作模组。
