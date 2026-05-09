# 什么是精灵图？
"精灵图"指的是2D游戏中典型的2D图形。例如，金锭物品精灵图，`Item_19.png`，看起来像这样：![Item_19](https://user-images.githubusercontent.com/4522492/159594962-1a594c3c-cd6c-4741-b60e-f8409461d845.png) 。在制作模组时，您通常需要为您添加到模组中的每个视觉内容制作一个精灵图。

本指南将教您精灵图制作的基础知识以及精灵图如何与tModLoader相关。

## 精灵帧
许多精灵图在一个图像中有几个"帧"——通常称为精灵表单。这些通常用于动画或变化。例如，GiantBee弹幕看起来像这样：
![](https://i.imgur.com/jRhab1A.png)
您可以在提取的原版纹理副本中找到此纹理，方法是找到`Projectile_566.png`。程序员告诉游戏这个精灵图有4帧，所以游戏知道将96像素高的纹理分割成4个等大的24像素高的部分。游戏在绘制此弹幕时在这4帧之间循环

一些精灵帧是为了多样化。这里是金属棒的精灵图，`Tiles_239.png`，这个精灵图在所有金属棒瓦片之间共享：
![](https://camo.githubusercontent.com/9781684c4d69c2de30fc3495148a112bf50207246f4dbf1da36bb5a7afa28620/68747470733a2f2f2f692e696d6775722e636f6d2f62716f794c71542e706e67)
制作瓦片精灵图是一个完整的主题，请查阅[基础瓦片](https://github.com/tModLoader/tModLoader/wiki/Basic-Tile#framed-vs-frameimportant-tiles)指南，并在制作瓦片精灵图之前与您的程序员确认细节。

## 文件格式
每个tModLoader精灵图必须是.png文件。只要您不弄乱程序中的设置，保存为.png应该可以正常工作。

## 精灵制作工具
您**将需要**使用一个 capable 的艺术/精灵制作程序。MS Paint（Windows 11之前）对于您可能想要制作的大多数精灵不起作用，因为它无法保存透明像素。请参阅[](https://github.com/tModLoader/tModLoader/wiki/Basic-Prerequisites#drawing-program)找到合适程序的列表。本指南的其余部分将在示例图中使用Aseprite，但任何这些程序应该有类似的功能。

虽然完整版本需要花钱，但Aseprite确实提供了一个免费的可用版本，100%安全合法使用。您可以使用[本指南](https://github.com/aseprite/aseprite/blob/main/INSTALL.md)编译它。

其他推荐的精灵制作程序包括但不限于：
* [Piskel](https://www.piskelapp.com/)
* [GIMP](https://www.gimp.org/)
* [LibreSprite](https://libresprite.github.io/#!/)
* [paint.NET](https://www.getpaint.net/)

## 原版精灵资源
在开始之前，我们建议将原版精灵提取到您可以轻松访问的文件夹。在本指南中我们将使用`C:\Documents\My Games\Terraria\ModLoader\VanillaTextures`文件夹。为此，请按照[本指南](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Prerequisites#vanilla-texture-file-reference)进行操作。我们将使用这些纹理作为我们在模组中制作的类似内容的尺寸和方向指南。

如果您只需要参考各种原版纹理有多大，您可以查阅这个[文本文件](https://forums.terraria.org/index.php?attachments/sprite-information-spreadsheet-tsv.384515/)。

## 实时编辑精灵图
如果您需要对瓦片或护甲纹理进行轻微调整，反复进行制作精灵图更改、重新构建模组、重新加载模组、然后在游戏中测试更改是否正确的过程可能非常耗时。您可以使用一个名为"Modders Toolkit"的模组在游戏中实时编辑纹理。按照下面的视频查看如何操作。进行更改后，您需要重新构建模组以使更改持久化。

<details><summary>实时编辑精灵图展示视频</summary><blockquote>

**说明：**下载并启用[Modders Toolkit](https://steamcommunity.com/sharedfiles/filedetails/?id=2573569299)模组。在游戏中，点击左下角的小图标，然后找到标有"Click to toggle Texture Tool"的按钮并点击它。纹理工具应该会显示。在列表中点击您正在处理的模组，然后勾选"Watch Mod Sources"。使用您的图像编辑程序进行更改。当您在图像编辑程序中保存更改时，Modders Toolkit会注意到更改并为您更新游戏中的纹理。

https://github.com/tModLoader/tModLoader/assets/4522492/9da8b20d-3fa8-4fd8-9724-8e844364e868

</blockquote></details>

## 制作精灵图

### 2x2像素
Terraria使用像素艺术风格，其中每个像素实际上是2x2像素块。您不必遵循这种艺术风格，但请注意这个概念。如果您想遵循这种风格，您可以通过以正常像素绘制然后在使用"最近邻"选项调整大小时将生成的精灵图放大200%来简化工作。请注意，这也会将填充从2像素更改为1像素。

如果您的精灵图不知何故错误放大，您可能会看到被抛出的常见术语是混合像素。"混合像素"指的是精灵图中混合大小的像素，导致不一致性并使精灵图看起来凌乱。始终建议以1x1像素绘制，然后放大到2x2，而不是反过来或混合两者。

这个示例的左侧有不一致的像素大小，而右侧保持一致。右侧最终看起来更干净，因为它内部是一致的。
![](https://i.imgur.com/06pNr1r.png)

另一个问题是玩家可能倾向于使用`Item.scale`直接调整他们看起来太小的精灵图的尺寸。虽然从将1x1像素转换为2x2像素的意义上说这很有用，但对于将已经是2x2的像素转换为更大的尺寸（比如4x4）的情况，这当然不有用。您不使用它的原因是，以这种方式放大精灵图会导致混合像素（如前所述），并且不会与游戏中已经存在的2x2精灵图匹配。

## 尺寸
精灵图的尺寸指的是精灵图由高度和宽度组成。例如，20像素宽20像素高的精灵图将是20x20的精灵图。

在调整大小之前，精灵图的最大尺寸是2048x2048——所以在调整大小到2x2之前是1024x1024。

## 艺术工具

## 填充
当精灵图有多个帧时，我们使用填充来分隔各个帧。默认情况下，Terraria期望每个帧之间有2像素的填充。对于水平排列的帧，这意味着每个帧下方有2像素的填充，即使是最后一个。这里是带有洋红色填充绘制的GiantBee弹幕精灵图，以显示填充的位置。
![](https://i.imgur.com/CYsKgX3.png)

对于瓦片，填充放在每个帧的右侧和下方。

## 动画
动画是通过在精灵图中包含几个动画帧来完成的。确保帧均匀分布。

--这里有间距不正确然后用显示填充和均匀纹理分割的指导线修复的示例--

动画通常是循环的，但也可以自定义动画循环。动画速度和模式由您的程序员决定，因此详细信息在单独的指南中。
* [弹幕动画代码指南](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile#animationmultiple-frames)

## 朝向
Terraria中的许多东西有预期的朝向。例如，NPC精灵图通常朝左![](https://i.imgur.com/PhcRvIa.png)，箭矢弹幕指向上![](https://i.imgur.com/ogDFqEa.png)，法杖指向右上![](https://i.imgur.com/ax451LJ.png)。遵循这些约定很有用，因为绘制这些精灵图的代码期望精灵图以特定方式朝向。如果您不遵循这些约定，碰撞箱和绘制会变得很奇怪。请查阅最相似的原版精灵图以了解如何朝向您精灵图的好主意。

### 弹幕

### 发光遮罩
发光遮罩指的是以全亮度绘制的独立精灵图，无论光照如何。这通常用于武器上的发光灯光。例如，常规VortexDrill物品精灵图，![](https://i.imgur.com/Oa54l03.png)，只能在周围有光时看到，但相应的发光遮罩精灵图，![](https://i.imgur.com/dNTFMTM.png)，无论附近灯光如何都以全亮度绘制：
![](https://i.imgur.com/jjV8Txq.png) ![](https://i.imgur.com/hGEHjCu.png)

要制作发光遮罩精灵图，请制作原始精灵图的副本并删除所有不应"发光"的像素。然后您的程序员需要编写一些代码使其正确绘制。

# 示例
这里我们将为一把剑制作精灵图。首先，我将查看一个相似的原版剑精灵图，以了解我想制作多大。首先，我会在wiki上查找我感兴趣大小的武器。让我们选择[Muramasa](https://terraria.wiki.gg/wiki/Muramasa)。Wiki上的物品精灵图与实际精灵图不完全相同，所以不要使用那个精灵图。相反，通过在信息框中找到"Internal Item ID: ###"来查找ItemID。这里我们看到数字是155，所以打开您的提取原版精灵图副本并找到`Item_155.png`。在精灵图编辑器中打开此文件，然后使用"另存为"将此文件的副本保存到您保存精灵图的文件夹中。现在，在保持相同朝向和一般尺寸的同时，随意绘制一把剑。还要注意2x2像素。通过使用这种方法，您将制作一个看起来与原版武器尺寸相同的精灵图。在这种情况下，精灵图是58x58像素。随着时间的推移，您将学习各种实体尺寸的约定，并能够在不需要原版纹理指南的情况下制作精灵图。

# 制作好的像素艺术

本指南专注于精灵图如何构建以及如何将它们集成到模组中的技术细节。如果您发现制作精灵图困难，也许值得找一个艺术家朋友合作。然而，如果您想进一步提高您的艺术技能，网上有很多学习资源可以指导您。加入[tModLoader Discord服务器](https://discord.gg/tmodloader)并访问`#spriting`频道是获得其他精灵制作者建议和反馈的好方法。这里有一些其他学习资源的链接：
* [终极像素艺术教程（视频）](https://www.youtube.com/watch?v=lfR7Qj04-UA)

# 预乘精灵Alpha

在代码方面，Terraria的绘制通常配置为要求半透明精灵的RGB分量乘以其A分量。您可以在这里找到这个概念的更详细解释[这里](https://en.wikipedia.org/wiki/Alpha_compositing#Straight_versus_premultiplied)。基本上，如果您的半透明精灵在游戏中看起来奇怪，您需要预乘其Alpha。这个过程因您使用的精灵软件而异：
- 对于Paint.NET，您可以使用[一个插件](https://forums.getpaint.net/topic/113378-premultiply-alpha/)
- 对于Aseprite，您可以使用[这个脚本](https://pastebin.com/stujFj2Z)