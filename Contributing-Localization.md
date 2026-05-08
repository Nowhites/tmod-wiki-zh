***
本指南适用于1.4.4版，即当前版本的tModLoader。如果您需要查看此wiki页面的1.4版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Contributing-Localization/5c2723d318427b2ed8ac35ef0ce4d8f9cc3f450f)
***

- **了解如何为您喜欢的mod或甚至[tModLoader本身](#本地化-tmodloader)贡献本地化（翻译）**
- **本指南还包含[Git版本控制系统](https://en.wikipedia.org/wiki/Git)的简要介绍，但如果您想要完整指南，您应该阅读[Git与mod管理](Intermediate-Git-&-mod-management)**

# 本地化工作流程
1. [选择一种方法](#本地化方法)
2. [获取模板](#本地化模板文件)
3. [翻译](#编辑本地化文件)
   * [占位符和替换](#占位符和替换)
   * [复数](#复数)
4. [发布翻译](#发布本地化文件)
5. [维护翻译](#维护本地化文件)

有关使用GitHub贡献本地化的信息，请参阅[面向本地化者的GitHub](#面向本地化者的github)部分。

如果您想本地化tModLoader，请参阅[本地化tModLoader](#本地化-tmodloader)部分。

# 本地化方法
有几种方法可以本地化一个mod。一些mod开发者欢迎翻译者直接通过GitHub或其他方式为他们的项目贡献翻译。其他mod开发者则希望翻译作为独立于原始mod的依赖mod来完成。无论采用哪种方法，本指南都将介绍如何本地化一个mod以及如何让您的本地化为其他用户所用。

### GitHub
在GitHub上托管其代码的mod最容易贡献翻译。翻译者可以下载mod的源代码并直接在其中工作。

### 联系Mod开发者
如果mod没有公开的GitHub，您可能需要考虑联系mod开发者。他们可能很乐意接受来自社区的直接翻译并将其整合到mod中。

### 新的翻译Mod
如果无法联系mod开发者或mod开发者不想处理翻译，最后的手段是制作一个包含该mod翻译的新mod。这种方法对用户来说不太方便，对翻译者来说也需要更多工作，但这是一个选择。（将来，我们计划在资源包中支持本地化。）

# 本地化模板文件
本地化一个mod的第一步是获取本地化文件。这些文件的扩展名是`.hjson`，可以用任何普通文本编辑器编辑。

***

### GitHub
如果您通过GitHub工作，请查找您希望使用的语言的`hjson`文件。如果该语言不存在文件，请参阅[添加新语言](https://github.com/tModLoader/tModLoader/wiki/Localization#添加新语言)部分以了解如何生成文件。

### 联系Mod开发者或新的翻译Mod
如果您在没有源代码的情况下工作，最简单的方法是制作一个新的翻译mod。按照[基础tModLoader Mod制作指南](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Modding-Guide#您的第一个mod)生成一个mod框架。我建议将mod命名为：`{原始Mod内部名称}{语言代码}`。例如，`ExampleMod`的法语翻译可能命名为`ExampleModFr`。显示名称应遵循类似的模式，也许`Example Mod Traduction française`。在制作mod框架时，通过将`BasicSword`条目留空来不生成模板剑物品。

现在，在您的mod的ModSources文件夹中找到`build.txt`，并用文本编辑器打开它。添加以下行：`modReferences = 原始Mod内部名称`，将`原始Mod内部名称`替换为适当的内部mod名称（mod的内部mod名称可以通过在[Mod文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#mods)中的`enabled.json`中找到该mod来查看）。接下来，添加另一行`translationMod = true`。保存`build.txt`。接下来，在`ModSources\您的Mod名称\Localization`中找到`en-US.hjson`并删除它，然后使用[语言缩写](https://github.com/tModLoader/tModLoader/wiki/Localization#culture)为您希望支持的语言制作一个`.hjson`文件。

现在您可以在游戏中构建mod了。确保您希望翻译的mod也已启用并重新加载mods。您的mod源代码文件夹现在将填充`.hjson`文件。请注意，文件将按照原始mod的布局组织，因此您可能需要在其中查找`.hjson`文件。如果您采用这种方法，请遵循`测试本地化文件`部分中的`新翻译Mod`步骤。

**如果您打算向原始mod开发者贡献翻译，一个替代方法是直接提取本地化文件。**这种方法很简单，但以后需要更多工作。首先，打开tModLoader并将游戏语言切换到您希望贡献的语言。接下来，导航到Mods菜单并确保mod已启用。如有必要，请重新加载mods。在Mods菜单中，点击`更多信息`按钮。右下角有一个`提取本地化`按钮，按下它。按下按钮后，文件资源管理器应该会打开到`Terraria\tModLoader\ModLocalization\Mod名称`。此文件夹现在包含与原始mod相同布局的所有本地化文件。不要移动或重命名此文件夹中的文件，因为这会使原始mod开发者更难以整合您的更改。您可能只会找到一个`.hjson`文件，这是正常的。如果您采用这种方法，请遵循`测试本地化文件`部分中的`联系Mod开发者`步骤。

***

# 编辑本地化文件

现在您有了您语言的`.hjson`文件或文件，用文本编辑器打开它。您应该会看到mod中内容的条目。作为翻译者，您只需要编辑格式为`key: translation`的行的右侧。保持类别名称和键不变（`：`左侧的文本）。例如：

```
Mods: {
	ExampleMod: {
		Config: {
			# 这是注释
			ExampleWingsToggle: {
				// Label: ExampleWings Toggle
				/* Tooltip: 
					'''
					启用或禁用ExampleWings物品
					第二行文本
					''' */
			}
		}

		Keybinds.RandomBuff.DisplayName: 随机增益
	}
}
```
此示例显示3个翻译条目：`Mods.ExampleMod.Config.ExampleWingsToggle.Label`、`Mods.ExampleMod.Config.ExampleWingsToggle.Tooltip`和`Mods.ExampleMod.Keybinds.RandomBuff.DisplayName`。`Label`和`Tooltip`条目被"注释掉"了，而`Keybinds.RandomBuff.DisplayName`条目已经被翻译成中文。`Label`条目使用单行`//`注释样式，而`Tooltip`条目使用多行`/* */`注释样式。翻译`Label`和`Tooltip`条目的右侧后，翻译者应该通过删除行首的`//`或删除围绕条目的`/*`和`*/`来取消注释该条目。

您还会看到以`# 注释在这里`形式的注释行。这些行不应被翻译，它们只是为了提供上下文和原始作者写的其他笔记。

翻译后，确保保存文件。

## 占位符和替换
您可能会在本地化文件中遇到`{0}`或`{$KeyHere}`等特殊文本。这些是本地化系统的高级用法，旨在简化翻译并减少文本重复。有关`{$KeyHere}`的信息请参阅[此处](https://github.com/tModLoader/tModLoader/wiki/Localization#替换)，有关`{0}`或其他数字的信息请参阅[此处](https://github.com/tModLoader/tModLoader/wiki/Localization#占位符)。

## 复数
在许多语言中，复数名词根据数量不同会有不同的形式。有关此功能的信息请参阅[复数部分](https://github.com/tModLoader/tModLoader/wiki/Localization#复数)。

# 测试本地化文件
如果有任何复杂或看起来模糊的本地化键，您可能需要测试您的编辑。

***

### GitHub
如果您直接在源代码上工作，您只需要在游戏中构建并重新加载您的mod。[了解如何构建Mod](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Modding-Guide#了解如何构建mod)部分可以教您如何做到这一点。[实时更新功能](https://github.com/tModLoader/tModLoader/wiki/Localization#实时更新)可用于在游戏中进一步测试更改，而无需重新构建和重新加载。

### 联系Mod开发者
由于您没有mod代码，您必须将此步骤留给mod作者，或者按照下面的`新翻译Mod`步骤制作一个临时mod来测试您的更改。如果这样做，请记住不要发布该mod。

### 新翻译Mod
要测试翻译mod，首先确保原始mod已启用，然后在游戏中构建并重新加载您的mod。您应该会看到您的翻译生效。[实时更新功能](https://github.com/tModLoader/tModLoader/wiki/Localization#实时更新)可用于在游戏中进一步测试更改，而无需重新构建和重新加载。

***

# 发布本地化文件
检查完所有内容并进行必要的更改后，您就需要发布您的翻译。此步骤也取决于您使用的本地化方法。

### GitHub
如果您熟悉GitHub，或者想学习如何使用，您可以进行拉取请求。有关使用GitHub的更多信息，请参阅下面的[]()部分。如果学习使用GitHub太麻烦，还有其他方式可以联系作者发送您的翻译。一种简单的方式是在他们的GitHub页面上提出一个Issue并在那里发布文件。如果您打算本地化很多mod，学习GitHub的基础知识可能是值得的。

### 联系Mod开发者
将完成的文件发送给mod开发者。然后mod开发者将使用您的文件。mod开发者可能希望在他们的英文翻译文件顶部跟踪翻译credits，以及在他们的创意工坊主页上。

### 新翻译Mod
如果愿意，为您的翻译mod制作一个图标。在允许的情况下，您可以修改原始mod的图标。[Mod框架内容](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Modding-Guide#mod框架内容)包含图标文件的尺寸信息。您还需要用有关翻译mod的信息编辑`description.txt`。一切准备就绪后，应该再次构建mod然后发布。这在Mod Sources菜单中完成。

# 维护本地化文件
您本地化的mod可能会更新新内容。如果发生这种情况，您可能需要更新翻译。

### GitHub
拉取或下载最新代码。模板文件应该已经用缺失的翻译更新了。如果没有，构建并重新加载mod。

### 联系Mod开发者
如果您制作了翻译mod，重新加载mods以更新本地化文件。找到新的未翻译条目，翻译它们，然后再次将新文件发送给mod开发者。

如果您改为提取本地化文件，请使用下载的更新mod按照之前的相同步骤操作。如果作者已将您之前的翻译整合到最新版本中，则新提取的本地化文件中这些条目应该已经被翻译了。

### 新翻译Mod
重新加载mods应该会更新本地化文件。找到新的未翻译条目，翻译它们，然后发布。

# 面向本地化者的GitHub
本节是GitHub和Git的简化指南，仅包含为mod贡献本地化所必需的信息。

## 术语
### Git和GitHub
大多数mod将使用git版本控制系统发布其源代码。GitHub，这个网站，为每个人提供了一个使用git贡献项目的全球平台。

### 仓库
仓库是特定的GitHub项目。这通常对应于单个mod的源代码。

### 提交
git中的提交是对项目的更改推送。每次对项目进行的提交都会更改项目中的某些文件。当您为mod创建了翻译时，这些将通过提交推送到项目。

### Fork
由于您可能不是项目的官方贡献者，您无法将提交推送到官方项目。这就是为什么您必须fork项目。Fork是您自己帐户下的项目副本。这允许您修改文件并向世界展示它们，但不能以官方项目的方式。

### 拉取请求
当您创建了翻译并将其提交到您的fork后，它们尚未进入官方项目。为了做到这一点，您必须向官方项目创建拉取请求。在您的fork的GitHub页面上应该提供一个按钮来提交拉取请求。

## 基本步骤概述
1. 登录您的GitHub帐户
    1. 如果您没有帐户，您可以在GitHub上免费注册自己[这里](https://github.com/join)。
1. 转到官方项目，fork项目到您的帐户：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/22ef73fa-3146-4fe2-a94f-c8f12644d066)
1. 创建翻译并将其提交到您的fork
1. 提交拉取请求

## 贡献本地化逐步示例
在这个例子中，我们将本地化[Recipe Browser](https://github.com/JavidPack/RecipeBrowser) mod。

首先，登录GitHub。接下来，打开您希望本地化的mod的GitHub主页。找到`Fork`按钮并点击它以创建您自己的GitHub仓库副本：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/5072a123-1480-4ef7-af02-e021762be9a8)

在下一页上，只需点击`Create fork`。等待页面刷新。

接下来，我们需要找到本地化文件。通过点击文件视图部分中的`Localization`文件夹来导航到它：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/1ac87917-a774-478f-9ac5-025dd1ff9f6b)

您应该能够找到`.hjson`文件的列表。请注意，不同mod的文件组织可能不同，因此您可能需要四处查找。找到与您希望贡献的语言对应的`.hjson`文件并点击它。（如果没有文件，您必须将mod的仓库克隆到您的计算机并本地工作。）

打开文件后，首先检查是否有任何缺失的翻译。如果有，那么就有工作要做了。点击`Edit this file`按钮：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/47c9f95e-e1a3-4996-a38a-9f88af24ab5e)

现在您可以编辑文件了。请记住从您翻译的未翻译条目中删除`//`。完成后，点击`Commit changes...`：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/f3b842c3-0be7-49ea-8585-c5b706a2bca9)

将会弹出一个窗口，可选择编辑提交消息和描述后点击`Commit changes`。

接下来，导航到`Code`选项卡：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/4ef0c202-1928-4ceb-89dc-e78b0166d747)

点击`Contribute`，然后点击`Open pull request`：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/bc081b14-1d2d-4683-98c3-0c015adb8e63)

现在您应该看到您所做的更改列表。仔细检查它们是否正确。如果一切看起来正确，请点击`Create pull request`：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/8d944a88-24c9-452d-bccf-28827376a134)

下一页允许您编辑拉取请求的标题和描述。点击`Create pull request`。

点击后，您的拉取请求就创建了。mod所有者将收到通知，并会在有空时审查并合并您的拉取请求。

恭喜！您现在已经为mod贡献了翻译。如果mod所有者接受您的拉取请求，翻译将出现在mod的下一个版本中。

# 本地化tModLoader
如果您想为tModLoader本身贡献翻译，这与为mod贡献有一些不同。

tModLoader不使用mod中看到的`.hjson`文件，而是使用`.json`文件，因此语法略有不同。过程大致相同，除了文件结构。导航到[tModLoader源代码文件夹](https://github.com/tModLoader/tModLoader/tree/1.4.4/patches/tModLoader/Terraria/Localization/Content)并找到您想要编辑的`.json`文件。打开文件并查找前面有`//`的条目。这些是缺失的翻译。按照与使用GitHub翻译mod相同的程序进行操作。Fork，编辑，然后创建拉取请求。如果您已登录，应该可以直接在GitHub.com上完成所有这些步骤。请记住只更改右侧，并从您本地化的行中删除`//`。与`.hjson`文件不同，需要引号（`"`），所以不要删除它们。

如果您想在游戏中测试您的更改，您需要按照[本指南](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-contributors)构建tModLoader。对于许多非技术用户来说，这是一个复杂的过程，所以这不是必需的。

如果您需要有关这些说明的任何指导，我们随时可以在[tModloader Discord服务器](http://discord.gg/tmodloader)上提供帮助。

### 本地化tModLoader Steam商店页面
除了游戏内文本外，我们还本地化了[tModLoader商店页面](https://store.steampowered.com/app/1281930/tModLoader/)本身。这些文件位于[SteamPageLocalization](https://github.com/tModLoader/tModLoader/tree/1.4.4/solutions/SteamWorkshopLocalization/SteamPageLocalization)文件夹中，缺失的语言可以通过在拉取请求中创建文件来提供。

### 本地化tModLoader Steam创意工坊页面
用户在访问[tModLoader Steam创意工庵页面](https://steamcommunity.com/app/1281930/workshop/)时可以看到点击的mod"标签"来自上述的`.hjson`文件。如果这些文件已更新但尚未在创意工坊页面上显示，请告知tModLoader开发者，我们会将其上传。

### 本地化tModLoader新闻公告
如果您想帮助翻译在Steam上发布的月度发布公告，请来到Discord上的[Steam公告本地化线程](https://discord.com/channels/103110554649894912/1163229998614855740)并告诉我们。

### 本地化ExampleMod
目前我们没有本地化[`ExampleMod`](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Localization)，存在的其他语言只是为了演示mod本地化过程如何工作。

### 本地化tModCodeAssist
[tModCodeAssist](https://github.com/tModLoader/tModLoader/wiki/tModCodeAssist)消息也可以本地化。编辑这些需要设置[tModLoader项目](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-contributors)，而且由于只有几条消息要本地化，通常更容易直接在Discord上ping tModLoader团队成员并发送翻译。

如果您想直接贡献，请阅读以下内容：

<details>
<summary>本地化tModCodeAssist详情</summary>

<blockquote>

首先，打开`tModLoader.sln`，然后在`tModCodeAssist`和`tModCoseAssist.CodeFixes`项目中打开`Resources.resx`文件。

![](https://github.com/user-attachments/assets/421ed1d5-e171-41e3-8276-fb82303445de)

在"Neutral Value"列中您会找到英文值。向右滚动找到您的语言，然后输入翻译。完成两个文件的编辑后，像往常一样进行提交和拉取请求。

![](https://github.com/user-attachments/assets/997b1197-31c4-4adf-848d-e91c2868e800)

此截图显示了向用户显示描述、标题和消息文本的位置，仅供参考。
![](https://github.com/user-attachments/assets/507dd9af-9633-400f-a29f-8061bf2c32aa)

</blockquote>
</details>
