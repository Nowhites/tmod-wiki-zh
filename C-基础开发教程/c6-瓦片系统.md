***
本指南已更新至1.4.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-Tile/166add9cb77cce9848277575dc9c4f925ecfb69e)。1.4版本可以在[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-Tile/9223cb4451dbe651c893589d95da583913098eba)找到。
***

# 基础瓷砖
本指南旨在解释瓷砖的基础知识。

# 什么是瓷砖？
在脑海中清晰理解瓷砖很重要。刚开始时，您可能会混淆或合并瓷砖和物品。物品是您物品栏中的东西。瓷砖是世界中的方块。游戏中的许多物品会放置瓷砖，但除了放置瓷砖的物品和返回物品的瓷砖之外，物品与其放置的瓷砖之间没有强制性的连接。也就是说，大多数模组制作者添加的瓷砖将有1个或多个对应的物品。

令人困惑的是，瓷砖与墙壁和电线一起存在于`Tile`结构中。游戏世界中的每个瓷砖坐标对应一个`Tile`结构。`Main.tile`包含所有这些`Tile`。请记住，如果您直接使用`Tile`结构，它们包含的不仅仅是瓷砖数据。如果您对`Tile`结构本身感到好奇，或需要直接使用它，请参阅[瓷砖类文档](https://docs.tmodloader.net/docs/stable/struct_tile.html)

## 瓷砖-物品配对
当`Item.createTile`设置为`ModTile`的`TileType`时，物品将在放置特定瓷砖。如果瓷砖有多种样式，设置`Item.placeStyle`允许您指定该样式。有关更多详细信息，请参阅[多种样式](#multiple-styles)。`ModTile`在开采时也会返回`ModItem`。这个过程是自动化的，但如果需要，可以为特殊瓷砖进行自定义。模组制作者可以使用`ModTile.RegisterItemDrop`来手动注册特定样式的掉落。可以使用`ModTile.GetItemDrops`来完全控制掉落。可以使用`TileLoader.GetItemDropFromTypeAndStyle`来查询瓷砖类型和瓷砖样式的物品掉落。[ExampleTrap.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleTrap.cs)是一个使用自定义样式的瓷砖示例，因此使用了自定义物品掉落代码。

### 基础瓷砖放置物品代码
放置瓷砖的物品通常非常简单。以下是所需的大部分内容。添加[`配方`](https://github.com/tModLoader/tModLoader/wiki/Basic-Recipes)是添加到瓷砖放置物品的典型额外代码部分。
```cs
public class MyTable : ModItem
{
	public override void SetDefaults() {
		Item.DefaultToPlaceableTile(ModContent.TileType<MyMod.Tiles.Furniture.MyTable>(), 0);
		Item.value = 150;
	}
}
```

# 制作瓷砖
要在Terraria中添加瓷砖，我们首先必须创建一个"继承"自`ModTile`的"类"。要做到这一点，在模组的源目录（`My Games\Terraria\tModLoader\ModSources\MyModName`）中创建一个.cs文件，然后在文本编辑器中打开该文件。将以下内容粘贴到该文件中，将`NameHere`替换为您的瓷砖内部名称，将`ModNamespaceHere`替换为您的模组文件夹名/命名空间。（一个常见错误是在内部名称中使用撇号或空格，不要这样做，计算机会无法理解。）

```cs
using Microsoft.Xna.Framework;
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;

namespace ModNamespaceHere
{
	public class NameHere : ModTile
	{
		public override void SetStaticDefaults()
		{
			Main.tileSolid[Type] = true;
			Main.tileMergeDirt[Type] = true;
			Main.tileBlockLight[Type] = true;
			Main.tileLighted[Type] = true;
			DustType = DustID.Stone;
			AddMapEntry(new Color(200, 200, 200));
			// 在这里设置其他值
		}
	}
}
```

现在您有了.cs文件，带来您的纹理文件（您制作的.png图像文件）并将其放入与此.cs文件相同的文件夹中。确保阅读[自动加载](https://github.com/tModLoader/tModLoader/wiki/Basic-Autoload)以便您知道如何满足计算机会的文件名和文件夹结构的期望。

# 有框架瓷砖 vs 框架重要瓷砖
瓷砖有2种不同类型。一种类型是常规瓷砖，它们是1x1（宽度为1，高度为1），当您在它们旁边放置类似瓷砖时会自动调整自身。在本指南中，这些被称为"有框架（Framed）"瓷砖，但也称为"地形"瓷砖。另一种类型是不会自动更改的瓷砖，我们称之为"框架重要（FrameImportant）"瓷砖。这些瓷砖通常大于1x1，所以它们的另一个名称可能是"多瓷砖"。"家具"瓷砖也是这些瓷砖的另一个名称。

以下是Framed瓷砖精灵的示例：
![](https://i.imgur.com/vtH5d8n.png)

以下是FrameImportant瓷砖精灵的示例：
![](https://i.imgur.com/s9ZtC9n.png)

并非所有1x1瓷砖都是Framed瓷砖，这里是1x1 FrameImportant瓷砖的示例。如果您记得，这个`MetalBars`瓷砖在放置在与其他瓷砖旁边时不会像Framed瓷砖那样更改：
![](https://i.imgur.com/bqoyLqT.png)

您可能已经注意到，FrameImportant瓷砖可以有多个独特的瓷砖"样式"。您可能还注意到精灵图中的一些空白区域，这是填充。这些概念将在后面探讨。

# 坐标
瓷砖坐标是世界坐标的1/16。请记住这一点，如果您需要做任何涉及瓷砖和其他内容（如NPC、玩家或弹幕）的事情。请参阅[坐标wiki页面](https://github.com/tModLoader/tModLoader/wiki/Coordinates)了解更多信息。

# 填充
制作精灵时，遵守适当的尺寸很重要。在大多数情况下，每个单独的瓷砖将是16x16像素，右侧和下方各有2像素的填充，总计18x18。
如果您需要帮助填充精灵，您可以使用名为[tSpritePadder](https://forums.terraria.org/index.php?threads/tspritepadder-ready-to-use-sprites-for-terraria-tiles.96177/)的外部工具。

# SetStaticDefaults
现在我们已经完成了一些初步信息，让我们关注`SetStaticDefaults`及其内容。本节将解释`SetStaticDefaults`中的大多数常见项目。[文档](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#documentation)进一步扩展了这些设置以及本指南未涵盖的许多其他专业设置。`SetStaticDefaults`的目的是定义瓷砖的行为方式，例如它是实心的、东西是否可以站在上面、以及熔岩是否会杀死它。查阅您希望在ExampleMod中模仿的类似瓷砖可能比从头开始做更好。本指南中的许多行将某些内容设置为true，这意味着默认值是false。不要费心包含设置为默认值的行，它只会使您的代码混乱。

参考[`ExampleMod中的ModTile类`](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Content/Tiles)作为创建您自己的`ModTile`时的指南，特别是学习时。找到最接近您需要的`ModTile`并复制`SetStaticDefaults`代码，然后在需要时修改它。

对于本指南，许多gif将引用此瓷砖精灵：
![](https://i.imgur.com/b009P8f.png)

## MinPick
`MinPick`控制镐需要多少最小镐力量才能破坏瓷砖。例如，为瓷砖设置`MinPick`为50意味着镐需要高于50%的镐力量才能破坏它。`MinPick`默认为0，因此任何镐都可以破坏瓷砖。很少有原版瓷砖使用`MinPick`，因为它通常出于进度原因设置，例如，Lihzahrd Brick的`MinPick`为210，以阻止人们过早闯入丛林神庙。关于原版镐力量，请参阅原版wiki：[镐力量](https://terraria.wiki.gg/wiki/Pickaxe_power)、[镐](https://terraria.wiki.gg/wiki/Pickaxes)、[钻头](https://terraria.wiki.gg/wiki/Drills)。

## MineResist
`MineResist`通过使其具有更多生命值来控制瓷砖的开采难度，正常情况下为100生命值。`MineResist`是一个乘数，因此`MineResist`为2f的瓷砖生命值将翻倍至200，`MineResist`为0.5f的瓷砖生命值将减半至50。`MineResist`默认为1f，因此您的瓷砖生命值保持在100。`MineResist`通常设置在也设置`MinPick`值的瓷砖上。关于原版`MineResist`值，请参阅下表或[原版Wiki](https://terraria.wiki.gg/wiki/Pickaxe_power)。
| 瓷砖 | MineResist |
|-|-|
| 默认 | 1f |
| 泥土、沙子、黏土、泥浆、淤泥、灰烬、雪、稀泥 | 0.5f |
| 尖刺和木尖刺 | 0.5f |
| 黑檀石、血石、珍珠石和地牢砖 | 2f |
| 钴和钯 | 2f |
| 秘银和山铜 | 3f |
| 墓碑 | 通常3f，在"For The Worthy"世界中为4f |
| 精金和钛 | 4f |
| Lihzahrd砖 | 4f |
| 叶绿 | 5f |

## Main.tileSolid[Type] = true;
将此设置为true意味着瓷砖将是实心的。弹幕将与之碰撞，NPC和玩家可以站在上面。

| `false`（默认） | `true` |
| - | - |
| ![](https://github.com/tModLoader/tModLoader/assets/4522492/11d25d26-0d2d-4a8f-bab5-7092e87ea122) | ![](https://github.com/tModLoader/tModLoader/assets/4522492/b973894a-1d4f-470c-9104-adc5e6052af7) |

## Main.tileSolidTop[Type] = true;
许多瓷砖，如放置的锭、桌子、铁砧等可以站立但不是实心的。所有这些瓷砖都设置了这个值。
`true`:
![tileSolidTop true](https://github.com/tModLoader/tModLoader/assets/4522492/e608ddf4-faa0-48c7-b8cd-f48d82d834b6)

## Main.tileTable[Type] = true;
一些瓷砖，如瓶子，只能放置在"桌子"上。请务必为扁平顶部家具瓷砖设置此项。

| `false`（默认） | `true` |
| - | - |
| ![tileTable false](https://github.com/tModLoader/tModLoader/assets/4522492/d66d85e0-40a6-42a8-8880-7f2eda41a4c4) |  ![tileTable true](https://github.com/tModLoader/tModLoader/assets/4522492/5b063bac-cf0d-47f2-b626-7e39e1d9a4e3) |

## Main.tileMergeDirt[Type] = true;
这个地形瓷砖将使用精灵图中提供的额外精灵与泥土合并。

| `false`（默认） | `true` |
| - | - |
| ![](https://i.imgur.com/6UjS77f.png) | ![](https://i.imgur.com/4RvCh3p.png) |

## Main.tileSpelunker[Type] = true;
## Main.tileShine[Type] = true;
## Main.tileShine2[Type] = true;
## Main.tileValue[Type] = true;
这些与矿石探测器和矿石发光有关。请参阅[ExampleOre](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleOre.cs)

## Main.tileBlockLight[Type] = true;
如果设置为`true`，光线会被这个瓷砖阻挡，光线穿过时会减少。
![](https://i.imgur.com/BVIl2Fl.png)

## Main.tileLighted[Type] = true;
如果为`true`，则瓷砖会产生光线。您可以使用`ModifyLight` hook为瓷砖设置光线颜色。

## Main.tileLavaDeath[Type] = true;
如果您希望瓷砖被熔岩击中时死亡，请设置为`true`。
![tileLavaDeath true](https://github.com/tModLoader/tModLoader/assets/4522492/61610046-f13b-4d53-9654-c65f57b042d2)

## Main.tileWaterDeath[Type] = true;
如果您希望瓷砖被水击中时死亡，请设置为`true`。

## Main.tileNoAttach[Type] = true;
防止瓷砖附加到此瓷砖。

| `false`（默认） | `true` |
| - | - |
| ![tileNoAttach false](https://github.com/tModLoader/tModLoader/assets/4522492/7de72b32-7696-4d54-b946-97a3512509c5) | ![tileNoAttach true](https://github.com/tModLoader/tModLoader/assets/4522492/5c31bd62-9872-4add-aaf2-451000fe78fd) |

## Main.tileCut[Type] = true;
瓷砖可以被武器破坏。

## Main.tileLargeFrames[Type] = 1 or 2;
请参阅下面的[自定义瓷砖变化](#custom-tile-variation)。

## 其他
这些较少使用，本指南不会解释。如果您需要这些提示，请参阅[文档](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#documentation)或原版源代码。
### Main.tileBouncy[Type] = true;
### Main.tileAlch[Type] = true;
### Main.tileStone[Type] = true;
### Main.tileAxe[Type] = true;
### Main.tileHammer[Type] = true;
### Main.tileNoSunLight[Type] = true;
### Main.tileDungeon[Type] = true;
### Main.tileRope[Type] = true;
### Main.tileBrick[Type] = true;
### Main.tileMoss[Type] = true;
### Main.tileNoFail[Type] = true;
### Main.tileObsidianKill[Type] = true;
### Main.tilePile[Type] = true;
### Main.tileBlendAll[Type] = true;
### Main.tileGlowMask[Type] = true;
### Main.tileContainer[Type] = true;
### Main.tileSign[Type] = true;
### Main.tileMerge[Type][otherType] = true;
### Main.tileSand[Type] = true;
### Main.tileFlame[Type] = true;
### Main.tileFrame[Type] = true;
### Main.tileFrameCounter[Type] = true;

## Main.tileFrameImportant[Type] = true;
这表示瓷砖是FrameImportant瓷砖。框架重要（Frame Important）这个名字的框架部分表明框架很重要，但什么是框架？框架是精灵图中当前瓷砖应绘制的坐标。对于Framed瓷砖，框架从不保存，因为Framed瓷砖的坐标框架是在加载世界时计算的。对于FrameImportant瓷砖，世界需要保存那些坐标，因此是"重要的"。对于模组制作者，只需在制作使用`TileObjectData`的瓷砖时将此设置为`true`，或者基本上所有不像泥土、矿石或其他基本建筑瓷砖的瓷砖。请参阅下面的[TileObjectData](#tileobjectdata)获取详细信息。

## ModTile属性：DustType、AdjTiles等
这些在[文档](https://docs.tmodloader.net/docs/stable/class_mod_tile.html#properties)中解释。您需要展开"从[ModBlockType](https://docs.tmodloader.net/docs/stable/class_mod_block_type.html)继承的属性"部分才能看到继承的属性，如`DustType`和`HitSound`。

## AddToArray(ref TileID.Sets.RoomNeeds.????);
用于使ModTile作为光源、椅子或桌子用于住房目的。一些示例：
```cs
AddToArray(ref TileID.Sets.RoomNeeds.CountsAsTable);
AddToArray(ref TileID.Sets.RoomNeeds.CountsAsDoor);
AddToArray(ref TileID.Sets.RoomNeeds.CountsAsTorch);
AddToArray(ref TileID.Sets.RoomNeeds.CountsAsChair);
```

## AddMapEntry
`AddMapEntry`用于设置在地图上查看瓷砖时与其关联的颜色和可选文本。浏览[ExampleMod示例](https://github.com/search?q=repo%3AtModLoader%2FtModLoader+AddMapEntry+path%3AExampleMod&type=Code)和文档获取更多信息。

<a name="terraintile"></a>
# 地形或有框架瓷砖
地形瓷砖构成了世界的大部分，如泥土、沙子、木材、石头等。绝大多数有框架瓷砖是实心的，将设置`Main.tileSolid[Type] = true;`。这些瓷砖被称为"有框架"瓷砖，因为游戏将根据附近瓷砖自动调整要使用的精灵图框架。例如，一些框架对应一个独立瓷砖，另一个可能对应上方有瓷砖且下方有瓷砖的瓷砖。

在这个图表中，我们可以看到几个[`ExampleBlock`](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleBlock.cs)框架在游戏中放置时的样子。
![image](https://github.com/user-attachments/assets/43c07625-6263-4b1b-a1cb-2c635ee6fa9c)

## 模板
这里有几个模板可以使用。这些模板中的颜色对应于本指南后面解释的`TileFrameNumber`变化。第一个模板不与泥土合并，而另一个合并并设置`Main.tileMergeDirt[Type] = true;`。

模板1：
![merge_template_2x2](https://github.com/user-attachments/assets/85654015-ccb4-40f9-8703-3d9ee9525fc8)
模板2：
![dirt_merge_template_2x2](https://github.com/user-attachments/assets/6c8393fd-d14d-4727-8e07-0eb064377a53)

另一个模板选项是Gemspark布局。此选项通过考虑所有8个邻居而不是仅4个邻居来进行框架，并具有用于角落中没有邻居的布局的其他额外框架。这些额外选项允许边缘线条正确平铺。

<img width="324" height="90" alt="DiamondGemsparkTemplate" src="https://github.com/user-attachments/assets/04278cad-c7ff-4370-a268-9195de190753" />

使用此选项，您需要添加以下内容才能正确工作：
```cs
public override bool TileFrame(int i, int j, ref bool resetFrame, ref bool noBreak) {
	Framing.SelfFrame8Way(i, j, Main.tile[i, j], resetFrame);
	return false;
}
```
请参阅[ExampleGemsparkBlock.cs](https://github.com/tModLoader/tModLoader/blob/1.4.4/ExampleMod/Content/Tiles/ExampleGemsparkBlock.cs)获取完整示例。

也可以使用其他[ExampleMod示例](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Content/Tiles)或[Terraria瓷砖纹理](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Prerequisites#vanilla-texture-file-reference)作为模板。

## 变化
默认情况下，游戏在放置瓷砖时会自动在3个不同选项之间随机选择。这是`Tile.TileFrameNumber`值。这将需要做更多的美术工作来创建工作的精灵图，但变化使瓷砖看起来更自然。

在这个示例中，每个颜色属于不同的变化，注意它们是如何被随机使用的。

![image](https://github.com/user-attachments/assets/1de7fda1-ba13-4806-858f-5341915534c2)

## 自定义框架
可以自定义瓷砖框架逻辑以与其他瓷砖合并、完全自定义框架逻辑以及调整瓷砖变化。

[ExampleCustomFramingTile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleCustomFramingTile.cs)展示了如何正确实现与其他非泥土地形瓷砖的合并。

## 自定义瓷砖变化
随机变化并不总是适合瓷砖的理想外观。一些瓷砖，如Lunar Rust Brick和Smooth Marble Block具有自定义瓷砖变化代码，以呈现一致的图案。[ExampleCustomFramingTile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleCustomFramingTile.cs)展示了一种逐行交替的自定义瓷砖变化图案。

### `Main.tileLargeFrames[Type] = 2;` 或2x2平铺图案
Lunar Rust Brick有相互连接的线条，它设置`Main.tileLargeFrames[Type] = 2;`以使用自定义瓷砖变化图案，强制瓷砖变化以平铺2x2图案重复。此图案使用4个变化而不是3个，因此精灵图有第二组精灵来包含第4个变化。第5个和第6个变化实际上并未使用，只是第4个选项的重复。

![tileLargeFrames2](https://github.com/tModLoader/tModLoader/assets/4522492/ac9a0dba-f0e2-45ce-9192-e0f2702e51f3)

### `Main.tileLargeFrames[Type] = 1;` 或3x4瓷砖图案
Smooth Marble Block有一个大型3x4平铺图案，它设置`Main.tileLargeFrames[Type] = 1;`以使用此瓷砖变化图案。

![image](https://github.com/tModLoader/tModLoader/assets/e56b5211-3d5a-4fa6-9e5a-783b89904667)

同样，第5个和第6个变化未使用，只是在精灵图中重复以简化。

![Tiles_357](https://github.com/tModLoader/tModLoader/assets/cb5a3447-f0d2-48bd-98fc-ef199f65f65b)

## 动画
进行动画的地形瓷砖在垂直方向上复制整个精灵图1次或多次。[ExampleLivingFireTile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleLivingFireTile.cs)展示了所需的代码，[ExampleLivingFireTile.png](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleLivingFireTile.png)展示了精灵图的预期布局。

<a name="tileobjectdata"></a>
# TileObjectData或FrameImportant/多瓷砖
如果`ModTile`不是Framed瓷砖，它必须有`Main.tileFrameImportant[Type] = true;`和`SetStaticDefaults`中的`TileObjectData`。FrameImportant瓷砖可以是任意大小，从1x1到任何更大的尺寸。它们也可以有许多不同的"样式"。每个样式也可以有"交替"，这是特定样式的交替放置。

对于本指南，许多gif将引用此瓷砖精灵：
![](https://i.imgur.com/b009P8f.png)

## 多种样式
您可以利用瓷砖样式来简化代码并避免代码重复。使用此功能，您可以拥有1个`ModTile`文件来放置几种样式。每个放置此瓷砖的物品将具有相同的`Item.createTile`但将具有不同的`Item.placeStyle`来区分要放置的样式。`Item.DefaultToPlaceableTile`方法将为瓷砖放置物品设置所有必需的属性，并将分配`Item.createTile`和`Item.placeStyle`。

可以将特定于瓷砖样式的代码添加到各种`ModTile`方法中，以自定义特定瓷砖样式行为。例如，[ExampleTorch.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleTorch.cs)对于瓷砖的2种样式具有不同的液体放置属性、光线颜色和悬停图标。

有关瓷砖样式如何在精灵图中组织的更多信息，请参阅下面的[StyleHorizontal](#stylehorizontal)、[StyleMultiplier](stylemultiplier)和[StyleWrapLimit](#stylewraplimit)。

![](https://i.imgur.com/O923oDq.png)

## 基本TileObjectData.newTile结构
在`SetStaticDefaults`中，我们使用`TileObjectData.newTile`来定义瓷砖的属性。有几种方法可以做到这一点。我们可以从`TileObjectData`模板复制、从现有瓷砖复制`TileObjectData`，或从头创建`TileObjectData`。

从模板或现有瓷砖复制可以大大简化代码，适合继承通用家具设置。例如，从`Style2x2`复制会创建一个典型的2x2瓷砖，可以放置在地面上且不受熔岩影响。然而，有时我们可能会继承意外行为。例如`Style4x2`创建一个4x2瓷砖，但该瓷砖设置为有左右放置。这对床很有用，但可能不是预期的。

使用`CopyFrom`对常见家具类型很方便，但有时直接制作`TileObjectData`更容易。对于尺寸不在现有模板中的瓷砖尤其如此。一些现有模板可能具有意想不到的行为，如熔岩免疫或左右放置，因此直接操作可以确保瓷砖的行为完全符合预期。

这些方法在下面的章节中更详细地探讨。

设置`TileObjectData`的基本结构如下：

```
如需要，使用TileObjectData.newTile.CopyFrom
在TileObjectData.newTile上调整设置
如需要，在TileObjectData.newAlternate上调整设置（如后面解释）
如需要，在TileObjectData.newSubTile上调整设置（如后面解释）
TileObjectData.addTile(Type);
```

最重要记住的是`addTile`必须是最后一条，如果使用`CopyFrom`，则必须是第一条。以错误顺序执行此操作将导致错误。

## 从头创建TileObjectData

要直接创建`TileObjectData`，请按照以下模板进行操作。根据您想要的宽度和高度调整值，然后根据您想要的行为进行任何其他调整。

```cs
TileObjectData.newTile.UsesCustomCanPlace = true;
TileObjectData.newTile.StyleHorizontal = true;
TileObjectData.newTile.Width = 2;
TileObjectData.newTile.Height = 2;
TileObjectData.newTile.CoordinateWidth = 16;
TileObjectData.newTile.CoordinateHeights = [16, 16];
TileObjectData.newTile.CoordinatePadding = 2;
TileObjectData.newTile.AnchorBottom = new AnchorData(AnchorType.SolidTile | AnchorType.SolidWithTop | AnchorType.Table | AnchorType.SolidSide, TileObjectData.newTile.Width, 0);
// 这里的其他编辑，如熔岩免疫、交替放置和子瓷砖
TileObjectData.addTile(Type);
```

## CopyFrom
Terraria包含几个现有的`TileObjectData`模板用作基础。如果其中一个模板匹配所需行为，您可以使用`CopyFrom`轻松为瓷砖设置`TileObjectData`。名称通常是自我解释的。
现有模板包括：
```cs
StyleSwitch
StyleTorch
Style4x2 // 床，有左右放置
Style2x2
Style1x2
Style1x1
StyleAlch
StyleDye
Style2x1
Style6x3
StyleSmallCage
StyleOnTable1x1 // 仅可放置在桌子上，如瓶子
Style1x2Top // "悬挂"在附加到上方瓷砖
Style1xX
Style2xX
Style3x2
Style3x3
Style3x4
Style3x3Wall
```

通常，您需要从复制模板开始，然后根据需要进行修改。
例如，[ExampleChair.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/Furniture/ExampleChair.cs)首先执行：
`TileObjectData.newTile.CopyFrom(TileObjectData.Style1x2);`
...然后进行如下调整：
```cs
TileObjectData.newTile.CoordinateHeights = [16, 18]; // 默认是16, 16
TileObjectData.newTile.CoordinatePaddingFix = new Point16(0, 2); // 我们添加了2个像素
```
...最后调用：
```TileObjectData.addTile(Type);```

## CopyFrom TileID
我们可以从特定现有瓷砖复制，而不是从模板之一复制。从现有瓷砖复制意味着复制更具体的行为。例如，从`Style1x2`复制将产生典型的1x2瓷砖，但从`TileID.Chair`复制将产生具有左右放置样式的1x2瓷砖。请注意，使用`CopyFrom`时，不会复制特定于瓷砖样式的行为，例如一些黑曜石家具的熔岩防护。`FullCopyFrom`方法将额外复制样式特定信息。

## Width
修改瓷砖在瓷砖坐标中的宽度：
`TileObjectData.newTile.Width = 3;`
![](http://i.imgur.com/ZjvSjOV.png)
`TileObjectData.newTile.Width = 2;`
![](http://i.imgur.com/4Qqx0mC.png)
`TileObjectData.newTile.Width = 1;`
![](http://i.imgur.com/8dudSXH.png)

请注意，如果您修改`Width`，您很可能也需要在后面的语句中修改`AnchorBottom`。

## Height
修改瓷砖在瓷砖坐标中的高度：
`TileObjectData.newTile.Height = 3;`
![](http://i.imgur.com/ZjvSjOV.png)
`TileObjectData.newTile.Height = 2;`
![](http://i.imgur.com/IqzM18z.png)
`TileObjectData.newTile.Height = 1;`
![](http://i.imgur.com/ypcuohX.png)

请注意，如果您修改`Height`，您很可能也需要在后面的语句中修改`CoordinateHeights`。

## Origin
修改瓷砖上以鼠标为中心的部分，以瓷砖坐标表示，从右上角开始：
`TileObjectData.newTile.Origin = new Point16(0, 0); // 默认`
![TileObjectData Origin 0 0](https://github.com/tModLoader/tModLoader/assets/4522492/c4c5cb05-ce17-40b1-ab63-cd54f6acfc8a)
`TileObjectData.newTile.Origin = new Point16(2, 0); // 向右2个瓷砖`
注意光标如何使用标记为"3"的区域放置瓷砖，该区域向右2个：
![TileObjectData Origin 2 0](https://github.com/tModLoader/tModLoader/assets/4522492/301a6441-879b-4cba-a37b-a1f81dc8f1c2)

## CoordinateHeights
这个int数组定义瓷砖内每个单独瓷砖行的高度。此数组必须与`Height`的值具有完全相同的元素数量，否则会发生错误。请注意，这些值不包括填充像素。

基本上，在大多数情况下所有值都应该是16。在底部使用18，以便纹理可以稍微延伸到下方的地面。这里是纹理的特写，注意那里有白色，这是为了说明为什么我们有时在底部瓷砖使用18。
![](http://i.imgur.com/XMHqvfy.png)
`TileObjectData.newTile.CoordinateHeights = [16, 16, 18]; // 延伸到草瓷砖。`

在这里我们看到白色像素从草后面窥出。草瓷砖不能完全覆盖其16x16区域，留下微小的孔。正确执行此操作将帮助瓷砖看起来真的在那里并坐在土壤中，显然模组制作者应该正确绘制精灵而不是白色。（注意，似乎solid必须为false才能实现此效果。）
![](http://i.imgur.com/CDzrin7.png)
`TileObjectData.newTile.CoordinateHeights = [16, 16, 16]; // 不要延伸到草中。`
注意草如何不完全覆盖其区域，因此我们的瓷砖看起来有点漂浮。
![](http://i.imgur.com/PMBuMum.png)

也可以使用[DrawYOffset](#drawyoffset)将整个瓷砖向下移动以实现类似效果。

### 非16或18的值
可以使用16或18以外的值，但非常罕见，通常仅用于1x1瓷砖（因为更大的高度只会为更大的瓷砖相互绘制）。例如，珊瑚瓷砖是24x26（每个样式，不包括填充）。代码定义此效果如下：
```cs
TileObjectData.newTile.CoordinateHeights = [26];
TileObjectData.newTile.CoordinateWidth = 24;
TileObjectData.newTile.DrawYOffset = -8;
```

![](https://i.imgur.com/65wwveE.png)
![](https://i.imgur.com/ku0wQH7.png)

## DrawYOffset
在Y方向上偏移此瓷砖的绘制。可用于将瓷砖绘制在比默认位置更高或更低的位置。

最常见的用法是将瓷砖向下推入地面一点，使其看起来像是坐在土壤中而不是漂浮在上面。（这也可以通过使用[CoordinateHeights](#coordinateheights)在底部瓷砖使用18像素高度来实现，但如果您的艺术已经是16像素高度，这是另一个选项。）`TileObjectData.newTile.DrawYOffset = 2;`将瓷砖向下移动2像素。另一个常见用法是悬挂瓷砖如横幅和灯笼，这些使用负值。如[ExampleWideBannerTile](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/Furniture/ExampleWideBannerTile.cs)这样的横幅瓷砖也使用[交替放置](#newalternate---alternate-placement-styles)来根据是挂在平台下方还是不是，将绘制偏移-2或-10像素。

此图像显示了几个示例，注意这些瓷砖如何全部推入泥土中。此截图中的唯一例外是[ExampleClock](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/Furniture/ExampleClock.cs)，它根本不使用`DrawYOffset`，因此看起来悬浮。背景可以通过草下面的间隙看到。

![image](https://github.com/user-attachments/assets/2c843023-abea-4e55-a93f-c784b72acf2a)

## DrawXOffset
类似于`DrawYOffset`，但适用于X方向。这唯一的示例是开关，如上图所示，它们推入放置的瓷砖中以看起来更自然。此示例还使用交替放置为每个方向应用不同的`DrawXOffset`值。

## CoordinateWidth
与`CoordinateHeights`不同，瓷砖中的所有瓷砖共享相同的宽度，因此这是`int`而不是`int数组`。如果您不复制样式，请确保将其设置为16。
`TileObjectData.newTile.CoordinateWidth = 16;`

## CoordinatePadding
这是瓷砖精灵图中瓷砖之间的填充。在每个区域的右侧和下方添加2像素的填充。按照惯例，坚持使用2。执行此操作，否则会出现奇怪的伪影。如果您不复制现有TileObjectData，请确保将其设置为2。
`TileObjectData.newTile.CoordinatePadding = 2;`

## AnchorBottom/AnchorLeft/AnchorRight/AnchorTop
锚点定义有效放置瓷砖所需的相邻瓷砖。最常见的锚点是锚定到实心瓷砖的`AnchorBottom`，占瓷砖的全部宽度，但可以创建更复杂的锚点，例如仅覆盖瓷砖侧面的一半或锚定到左侧或右侧。当您使用`CopyFrom`时，通常会免费获得适当的锚点，但请注意，如果更改`TileObjectData`的宽度或高度，并且该宽度或高度用于锚点，您将需要修复锚点：
```cs
TileObjectData.newTile.Width = 3; // 这必须在分配AnchorBottom的代码之上
TileObjectData.newTile.AnchorBottom = new AnchorData(AnchorType.SolidTile, TileObjectData.newTile.Width, 0);
```

这是一个自定义`AnchorBottom`的示例。`AnchorData`构造函数的第二个变量是宽度，第三个是所需锚点的瓷砖开始。如果下方第1个或第2个方块被破坏，瓷砖也将被破坏，但如果第3个方块被破坏则不会。第一个变量是一个BitMask，描述锚点有效的瓷砖类型。
```cs
TileObjectData.newTile.AnchorBottom = new AnchorData(AnchorType.SolidTile, TileObjectData.newTile.Width - 1, 0);
```
![AnchorBottom 2 of 3](https://github.com/tModLoader/tModLoader/assets/4522492/1001a752-3f23-445d-937d-58872e58e9dd)

这是一个`AnchorTop`的示例，要求上方的瓷砖为空。在珊瑚上方放置瓷砖，您会看到珊瑚因此代码而破碎：
```cs
TileObjectData.newTile.AnchorTop = new AnchorData(AnchorType.EmptyTile, TileObjectData.newTile.Width, 0);
```

默认情况下，所有锚点都是空的，但如果您使用`CopyFrom`从现有瓷砖复制，您可能需要清除您继承的锚点。要清除锚点，将锚点设置为`AnchorData.Empty`：
```cs
TileObjectData.newTile.AnchorBottom = AnchorData.Empty;
```

### 多个AnchorType
`AnchorData`构造函数的第一个参数可以通过使用逻辑"OR"运算符（`|`）组合多个`AnchorType`来提供。当组合多个`AnchorType`时，瓷砖的锚点将被满足，只要匹配的提供`AnchorType`中的任何一个。

例如，[ExampleWideBannerTile](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/Furniture/ExampleWideBannerTile.cs)使用`TileObjectData.newTile.AnchorTop = new AnchorData(AnchorType.SolidTile | AnchorType.SolidSide | AnchorType.SolidBottom | AnchorType.PlanterBox, TileObjectData.newTile.Width, 0);`。这允许瓷砖锚定到实心瓷砖、被锚定面上不平斜的地形瓷砖、具有实心底部的瓷砖和种植箱。`ExampleWideBannerTile`还使用[交替放置](#newalternate---alternate-placement-styles)来定义仅锚定到`AnchorType.PlatformNonHammered`的另一种放置。它使用此方法为专门在平台下方放置的样式提供自定义`DrawYOffset`值。

## Direction
此瓷砖将有资格放置的方向。默认设置为`None`，意味着当玩家面向任一方向时都可以放置瓷砖。当与交替放置一起使用时，可以为玩家面向左侧放置瓷砖和面向右侧放置瓷砖时给瓷砖不同的精灵。这是椅子和床瓷砖通常的工作方式。按照惯例，正常放置通常是左侧，交替是右侧。

```cs
TileObjectData.newTile.CopyFrom(TileObjectData.Style1x2);
TileObjectData.newTile.Direction = TileObjectDirection.PlaceLeft;
TileObjectData.newAlternate.CopyFrom(TileObjectData.newTile);
TileObjectData.newAlternate.Direction = TileObjectDirection.PlaceRight;
TileObjectData.addAlternate(1); // 朝右将使用第二个纹理样式
TileObjectData.addTile(Type);
```

![ExampleChair](https://github.com/tModLoader/tModLoader/assets/4522492/66cd9905-d4e9-4f4b-9d2f-ffbab99dbf3e)

https://github.com/tModLoader/tModLoader/assets/4522492/fb2c9892-6e4f-4426-a1df-40379dd1dbf6

## StyleHorizontal
默认情况下，瓷砖样式在精灵图上垂直排列：
![](https://i.imgur.com/nx4asBg.png)

更方便的是将它们水平放置。事实上，大多数`TileObjectData`模板都是水平排列的。方法是：
`TileObjectData.newTile.StyleHorizontal = true;`
![](https://i.imgur.com/MzfnM3l.png)

## StyleWrapLimit
如果您制作很多样式，您应该考虑使用`StyleWrapLimit`来保持精灵图文件整齐排列。这可以使处理比真正长或高的图像更容易。`StyleWrapLimit`使图像环绕到下一行/列（取决于`StyleHorizontal`）以继续放置样式。在横幅瓷砖精灵（Tiles_91.xnb）中，`TileObjectData.newTile.StyleWrapLimit = 111;`意味着样式0到110在第一行，样式111到221在下一行，依此类推。如果您没有其他样式的行，则不需要将此设置为任何内容。请注意，此值按放置样式计数，如果使用交替或随机样式，这不一定等于瓷砖样式。
![](https://i.imgur.com/pdI4S2N.png)

## StyleMultiplier
用于在精灵图中为交替放置和随机样式留出空间。此空间沿样式布局方向添加，即如果`StyleHorizontal`为true则水平添加。这应该设置为交替放置样式和`RandomStyleRange`的乘积，以确保每个交替放置和随机样式放置都被解释为相同的瓷砖样式。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/85fe2692-6171-4bca-b217-14b99ebb1b96)

一些瓷砖同时使用`StyleMultiplier`和`StyleWrapLimit`将每个样式放在自己的行上，仅与其交替放置。例如，火炬将`StyleMultiplier`和`StyleWrapLimit`都设置为6，同时将`StyleHorizontal`设置为true。最终结果是每个瓷砖样式在新行上，而不是与其他瓷砖样式的放置样式在同一行上：

![image](https://github.com/tModLoader/tModLoader/assets/4522492/3b925c97-3fd5-4c8c-887a-3c80a7b3223a)

## StyleLineSkip
类似于`StyleMultiplier`，但在达到`StyleWrapLimit`后在样式行之间添加空间，而不是在样式行内添加。如果`StyleHorizontal`为true，这意味着在达到`StyleWrapLimit`后在样式行之间添加额外的行。此额外空间可用于瓷砖状态，如"开"或"关"状态以及生长状态。也可以用于瓷砖动画。这应该被设置，即使未使用`StyleWrapLimit`以正确计算瓷砖掉落。

## RandomStyleRange
允许在放置时选择随机选择的放置样式。例如，珊瑚瓷砖使用`RandomStyleRange`值6，允许在放置时选择6种不同的瓷砖放置样式。与`StyleMultiplier`一起使用，以确保所有放置的瓷砖被解释为相同的样式，以使瓷砖掉落按预期工作。
`TileObjectData.newTile.RandomStyleRange = 6;`
![](https://i.imgur.com/y23Gc7T.png)

## UsesCustomCanPlace
应该始终为`true`。如果您复制了模板，它已经是`true`，但如果您不复制自模板，请确保设置它。

## 电线、开关、更改框架、更改状态
有时我们使用精灵图中的额外框架来允许我们的瓷砖在关闭和打开之间切换。额外精灵的放置取决于`StyleLineSkip`和`StyleHorizontal`。如果正确设置，这些额外的瓷砖"状态"仍应是相同的瓷砖样式。请参阅[ExampleLamp.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleLamp.cs)查看`HitWire`如何更改`TileFrameX`以更改绘制的精灵。
![](https://i.imgur.com/Xq13Slr.png)

## DrawFlipHorizontal和DrawFlipVertical
如果为true，瓷砖放置预览将在偶数X（或Y）瓷砖坐标处水平（或垂直）翻转绘制此瓷砖。必须在`ModTile.SetSpriteEffects`中复制此效果才能使放置的瓷砖正常工作。请参阅[ExampleLamp](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleLamp.cs#L66)获取示例。

```cs
public override void SetSpriteEffects(int i, int j, ref SpriteEffects spriteEffects) {
	if (i % 2 == 0) {
		spriteEffects = SpriteEffects.FlipHorizontally;
	}
}
```

https://github.com/tModLoader/tModLoader/assets/4522492/7d57d23a-68ab-46f6-a53a-5d371b9e626e

## 其他
还有更多本指南尚未解释的内容。其中许多在[文档](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#documentation)或[ExampleMod示例](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Content/Tiles)中解释。反编译Terraria并在`TileObjectData.Initialize`中查找以弄清楚它们是如何使用的：
```cs
AnchorWall
AnchorValidTiles
AnchorInvalidTiles
AnchorAlternateTiles
AnchorValidWalls
WaterDeath
LavaDeath
WaterPlacement
LavaPlacement
HookCheck
HookPostPlaceEveryone
HookPostPlaceMyPlayer
HookPlaceOverride
FlattenAnchors
CoordinatePaddingFix
DrawStepDown
```

## addTile(Type);
请务必调用此方法，否则您的模组将无法正确加载。这应该是`SetStaticDefaults`中处理`TileObjectData`的最后一行代码。
`TileObjectData.addTile(Type);`

## newAlternate - 交替放置样式
一些瓷砖有交替放置样式。例如床和椅子有面向左或面向右的交替放置样式。火炬有交替放置样式，用于锚定到下方瓷砖、左侧、右侧或锚定到墙壁。此外，有时会为瓷砖添加额外的放置选项，以允许使用多个原点放置瓷砖，例如闭合门可以通过点击门框中的任意位置来放置。这些交替放置使用`TileObjectData.newAlternate`注册。我们可以修改`TileObjectData.newAlternate`为每个交替放置分配不同的属性。

`newAlternate`的基本模式是从当前`TileObjectData.newTile`进行`CopyFrom`，对`TileObjectData.newAlternate`进行样式特定更改，然后调用`TileObjectData.addAlternate(placementStyleHere);`完成。应在任何`TileObjectData.newSubTile`更改之前和`TileObjectData.addTile(Type);`之前完成。使用交替放置时，请务必设置[`StyleMultiplier`](https://github.com/tModLoader/tModLoader/wiki/Basic-Tile#stylemultiplier)以确保瓷砖样式的交替放置仍被解释为相同的瓷砖样式。

请参阅[方向部分](https://github.com/tModLoader/tModLoader/wiki/Basic-Tile#direction)获取左右放置的示例。
请参阅[ExampleTorch.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleTorch.cs)和[ExampleSign.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleSign.cs)获取使用不同锚点的交替放置示例。
请参阅[ExampleDoorClosed.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/Furniture/ExampleDoorClosed.cs)获取使用`TileObjectData.addAlternate`注册相同放置样式的多个放置原点的示例。

## newSubTile - 瓷砖样式
具有多种瓷砖样式的瓷砖有时受益于特定瓷砖样式的自定义行为。这通过`TileObjectData.newSubTile`完成。

### 条件行为
您可能已经注意到`Main.tileWaterDeath`等按瓷砖类型索引。您可能还记得诅咒火炬和琥珀火炬可以在水下工作，触摸水时不会被破坏。如果您在代码中查看，您会发现诅咒火炬和琥珀火炬与其他火炬是相同的瓷砖类型。这怎么可能？这是通过`TileObjectData.newSubTile`实现的。我们可以修改`TileObjectData.newSubTile`以允许将不同属性应用于相同瓷砖类型的不同"瓷砖样式"。可以通过研究源代码了解`newSubTile`的更高级用法。

`newSubTile`的基本模式是从当前`TileObjectData.newTile`进行`CopyFrom`，对`TileObjectData.newSubTile`进行样式特定更改，然后调用`TileObjectData.addSubTile(styleNumberHere);`完成。如果使用交替放置，还需要使用`TileObjectData.newSubTile.LinkedAlternates = true;`。这应该在`TileObjectData.addTile(Type);`之前以及完成`TileObjectData.newTile`和`TileObjectData.newAlternate`更改之后完成。

此示例显示基本模式，并显示自定义瓷砖样式1以可在熔岩和水中放置。本示例可以在[ExampleTorch](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleTorch.cs)中的完整代码中看到。

```cs
// 之前的代码 TileObjectData.newTile 和 TileObjectData.newAlternate 代码

TileObjectData.newSubTile.CopyFrom(TileObjectData.newTile);
TileObjectData.newSubTile.LinkedAlternates = true;
TileObjectData.newSubTile.WaterDeath = false;
TileObjectData.newSubTile.LavaDeath = false;
TileObjectData.newSubTile.WaterPlacement = LiquidPlacement.Allowed;
TileObjectData.newSubTile.LavaPlacement = LiquidPlacement.Allowed;
TileObjectData.addSubTile(1);

// 下面的其他 TileObjectData.newSubTile 和 TileObjectData.addTile(Type); 代码
```

# 从`Tile`检索瓷砖样式
我们可以使用`TileObjectData.GetTileStyle(Tile)`方法检索放置的`Tile`的瓷砖样式。这主要用于促进各种`ModTile`方法中的瓷砖样式特定行为。例如，在[`ExampleTorch.cs`](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleTorch.cs)中，`TileObjectData.GetTileStyle`方法在`ModifyLight`中用于自定义`ExampleTorch`和`ExampleWaterTorch`样式发出的光线：

```cs
// 在ModifyLight中
int style = TileObjectData.GetTileStyle(Main.tile[i, j]);
if (style == 0) {
	r = 0.9f;
	g = 0.9f;
	b = 0.9f;
}
else if (style == 1) {
	r = 0.5f;
	g = 1.5f;
	b = 0.5f;
}
```

我们还使用它在悬停瓷砖时设置适当的光标图标。此代码使用`TileLoader.GetItemDropFromTypeAndStyle`方法检索相应瓷砖类型和样式的物品类型：

```cs
// 在MouseOver中
int style = TileObjectData.GetTileStyle(Main.tile[i, j]);
player.cursorItemIconID = TileLoader.GetItemDropFromTypeAndStyle(Type, style);
```

# 动画
不要更改瓷砖的`TileFrameX`或`TileFrameY`进行动画。瓷砖及其值应在动画时保持不变。[ExampleAnimatedGlowmaskTile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleAnimatedGlowmaskTile.cs)展示了更改状态和为瓷砖制作动画。[ExampleAnimatedTile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleAnimatedTile.cs)展示了更多动画瓷砖选项。[ExampleLivingFireTile.png](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleLivingFireTile.png)展示了动画地形瓷砖。

# 完整示例
## 有框架瓷砖
* [ExampleBlock.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/ExampleBlock.cs) - 典型地形方块

## FrameImportant瓷砖
* [ExampleTable.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/Furniture/ExampleTable.cs) - 典型家具瓷砖
* [TileObjectDataShowcase.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/TileObjectDataShowcase.cs) - 展示许多高级`TileObjectData`选项。作为交替放置、瓷砖样式、动画、瓷砖状态和随机样式如何组合在一起的示例。

## 相关参考
* [原版瓷砖ID](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Content-IDs#tile-ids)
* [ModTile文档](https://docs.tmodloader.net/docs/stable/class_mod_tile.html)
* [ModBlockType文档](https://docs.tmodloader.net/docs/stable/class_mod_block_type.html) - 包含`ModTile`和`ModWall`都继承的其他方法和属性。
* [TileObjectData文档](https://docs.tmodloader.net/docs/stable/class_tile_object_data.html) - 包含各种`TileObjectData`字段的其他文档。