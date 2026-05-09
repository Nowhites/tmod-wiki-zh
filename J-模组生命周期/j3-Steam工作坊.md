***
本指南仅适用于1.4版本。[Mod Browser](https://github.com/tModLoader/tModLoader/wiki/Mod-Browser)页面是您找到1.3版本tModLoader的相关信息的地方。
***

Workshop是tModLoader的一个在线组件，模组制作者可以在其中发布他们的模组。用户能够通过Workshop轻松找到、下载和安装他们感兴趣的模组。用户可以通过在主菜单访问`Workshop->Download Mods`直接在游戏中浏览Workshop。用户还可以在他们的Web浏览器或Steam中访问[Workshop](https://steamcommunity.com/app/1281930/workshop/)。

tModLoader还会加载用户从[Terraria Workshop](https://steamcommunity.com/app/105600/workshop/)订阅的Workshop项目。Terraria Workshop上的Workshop项目不是模组。Worlds和Resource Packs是Terraria workshop上唯一可以找到的内容类型。

***
**希望将模组发布到Mod Browser的模组制作者应继续阅读。**
***

## 入门
一旦你的模组工作正常并且功能完整，你可能希望与其他用户分享它。你需要一个Steam账户和一个可用的模组。

### Steam账户
希望发布模组的模组制作者必须拥有Steam版本的tModLoader。此外，Steam账户不能是[受限用户账户](https://help.steampowered.com/en/faqs/view/71D3-35C2-AD96-AA3A)。通常，这只是意味着该账户必须在Steam上花费至少5美元。

// TODO: 家庭共享可以发布吗？

### Steam订阅协议
模组制作者必须接受[Steam订阅协议](https://steamcommunity.com/sharedfiles/workshoplegalagreement)。在发布模组菜单上也有指向此页面的链接。

### 规则
此外，模组制作者应阅读规则。违反规则的模组将被移除：
* [Terraria玩家创作游戏增强：规则与指南](https://forums.terraria.org/index.php?threads/player-created-game-enhancements-rules-guidelines.286/)
* tModLoader规则（待定）

## 发布模组
在确保你的模组准备好发布后，访问`Workshop->Develop Mods`。你应该看到一个`Publish`按钮，如果没有，构建并重新加载你的模组，它应该会出现。按下`Publish`后，会出现一个带有一些附加选项的菜单。选择这些选项后，点击`Publish`，模组将被发布到workshop。然而，在发布之前，做以下事情：

### 设置版本
在你的build.txt中设置一个版本。像`0.1`这样的版本适合初始发布。请使用语义化版本：`major.minor.patch`你不允许在版本中使用字母。

### 填写description.txt
这是潜在用户在决定是否下载时会阅读的描述，写好它。

这是模组制作者在游戏中点击`More Info`按钮时看到的。它不像`description_workshop.txt`那样支持很多格式选项，但它确实支持聊天标签和彩色文本。

### 填写description_workshop.txt
这是潜在用户在决定是否下载时会阅读的描述，写好它。

此文件的内容将填充你的模组steam workshop页面描述。你可以使用[`bbcode`标记代码](https://steamcommunity.com/comment/ForumTopic/formattinghelp)来添加标题、链接和图片。模组制作者可以使用模组workshop页面上`Edit title & description`按钮来快速编辑和测试格式。如果你这样做，确保将直接在workshop上做的更改复制到`description_workshop.txt`，这样下次你发布模组更新时更改就不会被撤销。

### 测试
大量测试。你不希望你的模组因为糟糕的首次发布而被快速评价为不好。

### 制作主页
每个模组除了他们的Steam Workshop页面外都可以有一个主页。这可能是[泰拉瑞亚社区论坛](https://forums.terraria.org/index.php?forums/client-server-mods-tools.116/)上的一个线程、一个wiki、一个GitHub，甚至是一个Discord链接（不推荐）。由你决定。主页应该提供一种与用户互动的方式，这样他们可以提供bug报告、批评或赞扬。

一旦你有了主页，复制URL并将其粘贴到你的[build.txt的`homepage`属性](https://github.com/tModLoader/tModLoader/wiki/build.txt)中。保存build.txt。

### 构建
保存所有文件并在Mod Sources菜单中构建模组。

### Github
如果你有你的模组的Github仓库，推动所有更改。

### 可见性和标签
在发布菜单上，为与你的模组功能匹配的模组设置标签。如果你的模组准备好供任何人使用，将可见性设置为public。模组制作者可以使用Private或Friends Only来在发布前促进beta测试。

### 发布
你现在可以点击Publish按钮了。几分钟后，你的用户将自动拥有更新的模组。

## 更新模组
要更新你已发布的模组，首先做以下事情：

### 更新版本
增加你的build.txt中的版本。你的版本应该是2到4个用句点分隔的数字。不要在版本中添加字母，如"v"或"beta"。[阅读版本相关知识](https://msdn.microsoft.com/en-us/library/system.version(v=vs.110).aspx#Anchor_6)。注意：一些例子：0.1与0.001相同，0.0.0.10紧随0.0.0.9之后。

### 更新描述
用任何新的信息更新你的`description.txt`和`description_workshop.txt`，这些是你希望潜在用户知道的。最近更改的简要更新日志可能在这里很好。

### 更新changelog.txt
如果找到一个名为changelog.txt的文件，它将用于填充模组workshop页面的`Change Notes`部分。在发布更新之前，清除现有文本并输入自上次发布以来的更改。

### 更新模组图标
可以通过将80x80的`icon.png`添加到模组源文件夹的根目录来添加模组图标。这是向用户显示的图标。另外，workshop网站也支持更高分辨率的`icon_workshop.png`。这个文件可以最大512x512像素。如果你想保持80x80图标的像素艺术一致，你可以通过放大600%并使用最近邻放大选项来放大到480x480。

如果你想使用的话，这是一个模板：
![](https://i.imgur.com/uluOTmD.png)

### 测试
大量测试你的更改。通过加载你模组旧版本的旧存档来测试你的`ModPlayer`和`ModSystem`类中的保存和加载代码。

### 构建
保存所有文件并在`Workshop->Develop Mods`菜单中构建模组。

### Github
如果你有你的模组的Github仓库，现在推动所有更改，这样你的发布将与源代码保持一致。

### 发布
点击Mod Sources菜单中的Publish。几分钟后，你的用户现在将看到他们最喜欢的模组的更新可以下载。

### 更新网站
如果你有一个网站，现在是宣传更新和更新日志的好时机。

## 多个版本
模组可以同时为稳定版和预览版发布不同的模组构建。tModLoader将根据用户运行的tModLoader版本启用适当版本的模组，模组制作者不需要做任何事情。

## 取消发布模组
你可以通过访问你的模组的steam workshop页面并选择delete来取消发布一个模组。

## 协作者
在你的模组的steam workshop页面上，使用`Add/remove Contributors`来为你的模组添加协作者。用户必须是你的朋友。协作者可以向workshop发布模组更新，所以非常重要的是，你只添加你信任的协作者。为了实际协作代码，模组制作者通常使用GitHub一起处理代码。

## .tmod文件大小
如果你发现你的`.tmod`文件异常大，检查你的模组源文件夹中是否有不应该在那里的大文件。默认情况下，文件夹中的所有文件都将被打包到`.tmod`文件中。使用[build.txt中的`buildIgnore`](https://github.com/tModLoader/tModLoader/wiki/build.txt)来指定要忽略的文件夹或文件，或删除有问题的文件。常见的罪魁祸首包括photoshop或其他图像编辑项目文件。

## Github集成
模组制作者可以使用Github Actions来在每次提交时构建他们的模组。这可以用来确保模组不会破坏。

模组制作者也可以使用GitHub actions在每次build.txt更新时发布模组更新，但那不是我们有工作示例的东西，由用户自己决定。使用这种方法的人需要对其Steam凭证极其小心。

## 重命名模组
[build.txt](https://github.com/tModLoader/tModLoader/wiki/build.txt)中的`displayName`决定了用户在交互模组时看到的名称。这个名称可以随模组更改而更改。随意编辑它，你的更改将在下次发布时生效。如果你需要更改模组的内部名称，请继续阅读。

除了`displayName`，还有一个`internal name`。internal name是另一个与模组相关的名称，这个名称没有空格或特殊字符，并且在模组更新时不会更改。例如，[Begone, Evil! (Disable Evil Biome Spread)](https://steamcommunity.com/sharedfiles/filedetails/?id=2669670918)模组的`internal name`是"DisableCorruptionSpread"。tModLoader和其他模组可以在代码中使用"DisableCorruptionSpread"与此模组交互，如果`displayName`更改，连接不会被破坏。

`internal name`在workshop上必须是唯一的，所以你可能会发现你为模组选择的`internal name`在发布时已经被使用。或者，你可能因为其他原因想要重命名`internal name`。由于各种因素，不建议更改已发布的模组的`internal name`，但可以做到。如果你的模组很小，制作一个新的模组并复制更改可能比按照以下步骤重命名现有模组更容易。

### 重命名模组步骤
使用[workshop搜索](https://steamcommunity.com/app/1281930/workshop/)找到一个新的唯一`internal name`。搜索不按内部名称搜索，但它应该给你一个已经使用了什么的概念。如果你是在[tModLoader Discord](https://discord.gg/tmodloader)上，你可以在`#botworld`频道中输入`.mod ModNameHere`命令。我们的机器人将回复该模组的信息（如果存在的话）或"Mod with that name doesn't exist"（如果具有该`internal name`的模组不存在）。重复直到找到一个适合你的模组且未使用的名称。

在决定一个新的`internal name`后，首先重命名你的模组源文件夹。打开`ModSources`文件夹并重命名你的模组的文件夹。

接下来打开你的模组源文件夹并重命名`ModName.cs`、`ModName.csproj`，如果存在的话还有`ModName.csproj.user`。打开`ModName.cs`并重命名`Mod`类。打开文本编辑器中的`ModName.csproj`并重命名`AssemblyName`条目。

接下来我们需要更新你模组中每个`.cs`文件上的所有`namespace`条目。打开每一个并重命名每个`namespace ModName`条目。

确保你在每个文件上保存了所有更改，并尝试在游戏中构建模组。如果模组不加载或不构建，你可能错过了一些东西。错误消息应该帮助你找到并修复剩余的问题。

使用"在文件中查找"功能可以简化这个过程。例如，在notepad++中，"在文件中查找"工具可用于在特定目录中搜索所有文件。从那里每个结果都可以很容易地修复。

## 限制
### Terraria社区论坛规则
发布到Mod Browser的模组必须遵守[TCF模组规则](https://forums.terraria.org/index.php?threads/player-created-game-enhancements-rules-guidelines.286/)。值得注意的规则包括禁止从非PC版本的Terraria移植内容的规则和禁止剽窃的规则。违反这些将导致Mod Browser权限被撤销。

### 模组图标指南
模组图标中的任何裸体或不适当内容将导致特别严重的处理。
什么会被认为是不适当的？你可能可以猜到，但这里有一些例子：裸体/色情、脏话、对人类或动物的暴力/伤害，以及鼓励破坏、犯罪、恐怖主义、种族主义、饮食失调或自杀的图标等等。这个列表可以继续……请使用一个适当的图标。

### 恶意代码
不要这样做。你上传的模组带有你的Steam身份标记。你将被**永久禁止**使用浏览器，你的所有模组将被移除。我们还将通知Terraria和Steam工作人员关于你和你的恶意代码。

# 常见问题
// TODO: description.txt太长的错误

### 发布的项目的内容文件夹丢失了？！
未确认：如果saves目录中有Workshop文件夹，删除它。再次尝试发布。
