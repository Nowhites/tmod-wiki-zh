# 基础NPC掉落和战利品

本指南将教您关于敌人被击杀时掉落物品的基础知识。请注意，本页仅适用于1.3 tModLoader。1.4 tModLoader中的NPC战利品完全不同，请参阅ExampleMod中的ModifyNPCLoot用法示例和[1.4基础NPC掉落和战利品](Basic-NPC-Drops-and-Loot-1.4)。

## 基础

我们使用ModNPC类或GlobalNPC类中的NPCLoot方法来指定敌人掉落的物品。

### ModNPC.NPCLoot()与GlobalNPC.NPCLoot()

我们可以在两个地方放置NPC掉落代码。如果我们的mod添加了一个NPC，我们想要该NPC有特定的掉落，请将相关代码放在该ModNPC类中。如果我们想为vanilla NPC添加掉落，将代码放在GlobalNPC类中。如果我们想为所有NPC添加掉落，比如地牢箱子钥匙或灵魂掉落，将代码放在GlobalNPC中。请记住，组织是mod可维护性的关键。

### Item.NewItem

在本指南中，您将看到`Item.NewItem`方法被调用。请参阅[有用的Vanilla方法](https://github.com/tModLoader/tModLoader/wiki/Useful-Vanilla-Methods#public-static-int-newitemint-x-int-y-int-width-int-height-int-type-int-stack--1-bool-nobroadcast--false-int-pfix--0-bool-nograbdelay--false-bool-reverselookup--false)查看参数。此方法在游戏世界中生成一个物品。物品根据参数指定为中心区域生成。

### 如何指定我的物品？

在下面的示例中，我们掉落一个vanilla物品：`ItemID.BeeGun`。可以通过用`ModContent.ItemType<ItemName>()`替换来换成您mod中的物品

### 始终掉落1个物品

以下显示最基本的示例。此代码将在每次击杀此ModNPC时掉落1个蜜蜂枪。

```csharp
public override void NPCLoot()
{
	Item.NewItem(npc.getRect(), ItemID.BeeGun);
}
```

### 额外掉落

我们可以通过添加其他代码行来添加额外掉落。

```csharp
Item.NewItem(npc.getRect(), ItemID.Beenade);
Item.NewItem(npc.getRect(), ItemID.HiveWand);
```

### 堆叠数量

如果我们想掉落多于1个物品的堆叠，请指定要掉落的数量。

```csharp
Item.NewItem(npc.getRect(), ItemID.Beenade, 20);
```

### 每玩家掉落或实例化

//TODO 每玩家

#### 实例化

如果您希望物品像boss袋一样掉落（每个玩家一个，客户端可见（其他玩家看不到属于其他玩家的掉落）），请使用`npc.DropItemInstanced`方法：

```csharp
npc.DropItemInstanced(npc.position, npc.Size, ItemID.Picksaw, 1, true);
```

最后两个参数是堆叠数量，以及是否需要NPC和玩家之间的交互才能掉落。

## 随机性

大多数时候，我们不希望物品始终掉落，而是以很小的几率掉落。我们可以使用随机数生成器让我们的物品有机会掉落。我们将使用`Main.rand.[METHODNAME]`来执行此操作，通常是`Main.rand.Next`。

### 随机几率

如果我们想要1/X的几率，建议使用以下代码。请注意，`Next(int max)`方法返回0到max-1之间的数字。在以下示例中，返回值的可能性是：0、1、2、3、4、5和6。（永远不会是7！）另外，不要更改0。

```csharp
if (Main.rand.Next(7) == 0)
	Item.NewItem(npc.getRect(), ItemID.Beenade, 20);
```

### 随机堆叠

请记住，Main.rand.Next(int)可能返回0且不返回最大值，因此请使用以下方法之一。

```csharp
Item.NewItem(npc.getRect(), ItemID.Beenade, 5 + Main.rand.Next(3)); // 5、6或7
Item.NewItem(npc.getRect(), ItemID.Beenade, Main.rand.Next(5, 8)); // 5、6或7
// 错误！不要使用，有机会掉落0：Item.NewItem(npc.getRect(), ItemID.Beenade, Main.rand.Next(5));
```

### 特定几率

有时候1/X的比例可能不是我们想要的。

```csharp
if(Main.rand.Next(7) < 2) // 2/7的几率
```

我们甚至可以计算特定的百分比。

```csharp
if (Main.rand.NextFloat() < .1323f) // 13.23%的几率
```

## 其他条件

有时我们想检查其他条件，例如专家模式或当前生物群系。

### 使用条件

我们可以使用逻辑运算符（如AND (&&)）向NPCLoot代码添加条件

```csharp
if (Main.rand.Next(7) == 0 && Main.expertMode)
	// 仅专家模式掉落。专家世界中有1/7的几率掉落。
```

### 双倍专家模式掉落

以下是双倍专家mod掉落的示例。您可以按照您想要的方式实现，只需确保逻辑合理。

```csharp
if(Main.rand.NextBool(Main.expertMode ? 2 : 1, 5))
```

### 不同的专家模式掉落

如果您想对专家模式条件或其他任何条件更具体，请使用if-else语句。

```csharp
// 专家模式掉落10-20，普通模式掉落20-30：
if(Main.expertMode)
	Item.NewItem(npc.getRect(), ItemID.Beenade, Main.rand.Next(20, 31));
else
	Item.NewItem(npc.getRect(), ItemID.Beenade, Main.rand.Next(10, 21));
```

### 生物群系或位置

以下显示vanilla如何掉落光之魂的代码。请注意，此示例更适合GlobalNPC类而不是NPCLoot类，因为它为在生物群系/区域死亡的所有NPC添加掉落。

```csharp
// 我们检查几件事来过滤boss和生物，以及NPC死亡的深度。
if (Main.hardMode && !npc.boss && npc.lifeMax > 1 && npc.damage > 0 && !npc.friendly && npc.position.Y > Main.rockLayer * 16.0 && npc.value > 0f && Main.rand.NextBool(Main.expertMode ? 2 : 1, 5))
{
	if (Main.player[Player.FindClosest(npc.position, npc.width, npc.height)].ZoneHoly)
	{
		Item.NewItem(npc.getRect(), ItemID.SoulofLight);
	}
}
```

### 自定义生物群系

将上述示例中的`.ZoneHoly`替换为`.GetModPlayer<ExamplePlayer>().ZoneExample`。

### 击杀NPC的玩家

有时我们想为最后攻击NPC的玩家做点什么。NPC有一个lastInteraction字段，默认为255，意味着没有玩家伤害过NPC。如果城镇NPC或陷阱造成所有伤害，NPC可能仍然以lastInteraction为255的方式死亡。因此，通常用于奖励或影响击杀NPC的玩家的代码可能看起来像这样，在需要时回退到FindClosestPlayer：

```csharp
int playerIndex = npc.lastInteraction;
if (!Main.player[playerIndex].active || Main.player[playerIndex].dead)
{
	playerIndex = npc.FindClosestPlayer(); // 由于lastInteraction可能是无效玩家，回退到最近的玩家。
}
Player player = Main.player[playerIndex];
// 其他影响玩家的代码。如果相关，可能需要ModPackets。
```

## 随机选择

很多时候我们想从一组选择中掉落一个随机物品。有一种简单的方法和一种更好的方法。

```csharp
int choice = Main.rand.Next(2);
if (choice == 0)
{
	Item.NewItem((int)npc.position.X, (int)npc.position.Y, npc.width, npc.height, ModContent.ItemType<PuritySpiritMask>());
}
else if (choice == 1)
{
	Item.NewItem((int)npc.position.X, (int)npc.position.Y, npc.width, npc.height, ModContent.ItemType<BunnyMask>());
}
```

上述代码也可以用switch语句替换。

一个更好的方法：

```csharp
using Terraria.Utilities;
// --------
var dropChooser = new WeightedRandom<int>();
dropChooser.Add(mod.ItemType<Items.Armor.PuritySpiritMask>());
dropChooser.Add(mod.ItemType<Items.Armor.BunnyMask>());
int choice = dropChooser;
Item.NewItem(npc.getRect(), choice);
```

第二种方法更容易维护。您也可以为不同的选择分配不同的权重。此用法可能是高级或专家级别，但我认为应该提一下。

另一种方法：

```csharp
int[] choices = [ModContent.ItemType<CarKey>(), ModContent.ItemType<ExampleLightPet>(), ItemID.PinkJellyfishJar];
int choice = Main.rand.Next(choices);
Item.NewItem(npc.getRect(), choice);
```

## Vanilla NPC呢？

如果您想从vanilla NPC掉落物品，所有相同的想法都适用，只是我们将代码放在ModNPC类中，而是放在GlobalNPC类中，并使用if语句过滤我们不想影响的NPC掉落

```csharp
class MyGlobalNPC : GlobalNPC
{
	public override void NPCLoot(NPC npc)
	{
		if(npc.type == NPCID.Frankenstein)
		{
			// 在这里放置针对Frankenstein的特定掉落
		}
		// 如果您想添加其他vanilla NPC的掉落，请在此添加其他if语句。
	}
}
```

### 特殊情况

一些vanilla boss需要特殊条件来检测它们何时被击杀并准备掉落战利品。

世界吞噬者：

```csharp
if (npc.boss && System.Array.IndexOf(new int[] { NPCID.EaterofWorldsBody, NPCID.EaterofWorldsHead, NPCID.EaterofWorldsTail }, npc.type) > -1)
```

双子：

```csharp
if (npc.type == NPCID.Retinazer && !NPC.AnyNPCs(NPCID.Spazmatism) || npc.type == NPCID.Spazmatism && !NPC.AnyNPCs(NPCID.Retinazer))
```

## 其他方法

### NextBool

如果您想使用NextBool方法而不是将随机数与0进行比较，可以使用它

```csharp
if (Main.rand.NextBool(7))
	Item.NewItem(npc.getRect(), ItemID.Beenade, 20);
```

# 基础级别未涵盖的内容

- BlockLoot
- 让我们知道。
