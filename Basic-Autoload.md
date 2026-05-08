***
本指南已更新至1.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-Autoload/26fd108fd8ec4948a8134ffa35c3ee6bb90a1e7c)
***

自动加载是一种机制，tModLoader对我们的模组做出一些假设，以简化模组制作者在模组中需要做的工作。在本基础指南中，我们将专注于纹理如何自动加载，因为这是初级模组制作者的主要关注点。

物品、弹幕和NPC都需要纹理才能正常工作。任何没有关联纹理的这些东西将导致您的模组无法加载。tModLoader不能神奇地知道您希望哪个纹理文件与哪个东西关联，但它可以根据它知道的模式做出有根据的猜测。

这是模式：

取Mod Sources路径：
`C:\Documents\My Games\Terraria\tModLoader\ModSources`

添加类的命名空间，将'.'替换为'\\'：
`namespace ExampleMod.NPCs`
`C:\Documents\My Games\Terraria\tModLoader\ModSources\ExampleMod\NPCs`

添加类的类名，然后附加'.png'：
`public class FlutterSlime : ModNPC`
`C:\Documents\My Games\Terraria\tModLoader\ModSources\ExampleMod\NPCs\FlutterSlime.png`

更简洁地说，在由类的命名空间指定的文件夹中查找与事物类名匹配的文件名的.png文件。

基本上，只需确保.cs文件和.png文件存在于同一目录中，并且.cs文件中的类与.png文件的文件名匹配。确保.cs文件名也与类名和.png文件名匹配也是一个好主意。此外，您必须确保从Mod Sources文件夹到文件夹的路径与命名空间匹配（其中'.'在文件夹结构中变为'\\'）。

可视化解释：
![](https://i.imgur.com/9z50wHh.png)

### 额外纹理

除了内容可能需要的常规纹理外，还有其他纹理将为特定类型的内容自动加载。以下是这方面最常见的示例。

具有在玩家身上绘制的纹理的配饰物品会在类上使用`[AutoloadEquip(EquipType.Something)]`注释。这些物品需要一个以后缀装备类型命名的单独纹理，例如`_Back`或`_Shield`。

使用`[AutoloadHead]`和`[AutoloadBossHead]`注释的城镇NPC和Boss NPC类将分别自动加载以后缀`_Head`和`_Head_Boss`命名的纹理。这些是绘制在小地图上的纹理。

设置了`TileID.Sets.HasOutlines`的瓷砖将自动加载以后缀`_Highlight`命名的纹理，其中包含智能交互轮廓纹理。

坐骑和矿车将需要纹理。以后缀`_Back`（在玩家背后绘制）、`_Front`（在玩家面前绘制）或[其他选项](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ModLoader/MountTextureType.cs)命名的额外纹理将被自动加载。至少需要一个才能使坐骑正常工作。

# 音乐
音乐文件需要位于名为"Music"的文件夹或子文件夹中才能作为音乐自动加载。对于不符合该条件的音乐文件，模组制作者可以使用`MusicLoader.AddMusic`。

# 背景
背景纹理文件需要位于名为"Backgrounds"的文件夹或子文件夹中才能作为背景纹理自动加载。对于不符合该条件的纹理文件，模组制作者可以使用`BackgroundTextureLoader.AddBackgroundTexture`。