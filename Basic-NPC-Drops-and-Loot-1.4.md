# 基础NPC掉落和战利品
本指南将教您在敌人被击杀时掉落物品的基础知识。请注意，此页面仅适用于1.4 tModLoader。1.3 tModLoader中的NPC战利品完全不同，请参阅[基础NPC掉落和战利品](Basic-NPC-Drops-and-Loot)获取详细信息。

# 基础知识
Terraria维护着一个规则数据库，规定每个NPC类型掉落的物品。此数据库在模组加载期间填充。用户可以通过访问博物志来查看此数据。我们使用`ModifyNPCLoot`方法在我们的`ModNPC`类或我们的`GlobalNPC`类中注册规定敌人掉落物品的规则。此外，所有包含物品掉落的钱袋也通过`ModifyItemLoot`以类似方式构建。

## 为什么战利品从旧系统改变了？
1.3系统的战利品完全依赖代码来规定掉落，因此无法可靠地确定特定NPC的物品掉落结果。新系统维护一个规则数据库，有助于填充博物志功能，并促进模组调整物品掉落。例如，在1.3中，向一组物品掉落添加物品有可能破坏其他模组。新系统允许更可靠地调整物品掉落。

## IItemDropRule
每个物品掉落规则都是`IItemDropRule`类的实例。有多种`IItemDropRule`将在后面解释。每个规则都有规定物品掉落概率和堆叠大小的逻辑。

## ModNPC.ModifyNPCLoot() vs GlobalNPC.ModifyNPCLoot() vs GlobalNPC.ModifyGlobalLoot()
我们可以放置NPC战利品规则的3个地方。如果我们的模组添加了一个NPC并且我们想要该NPC的特定掉落，请将相关代码放在`ModNPC`类中的`ModifyNPCLoot` hook中。如果我们要向特定原版NPC添加掉落，将代码放在`GlobalNPC`类中的`ModifyNPCLoot` hook中。如果我们要向所有NPC添加掉落（如地牢箱子钥匙或灵魂的掉落方式），将代码放在`GlobalNPC.ModifyGlobalLoot`中。添加到所有NPC的掉落称为全局规则，不会显示在博物志中。记住，组织是模组可维护性的关键。

## 如何指定我的物品？
在下面的示例中，我们掉落一个原版物品：`ItemID.Shackle`。可以通过用`ModContent.ItemType<ItemName>()`替换它来换成您模组中的物品。

# 完整示例
此示例显示基本文件布局。实际规则将在下面的[典型物品掉落规则](#典型物品掉落规则)中教。
虽然以下示例为简洁起见排除了代码中通常存在的using语句，但掉落规则和掉落条件需要以下using：
```cs
using Terraria.GameContent.ItemDropRules;
```
这些示例可能还需要其他缺失的using语句，此处不会写出。

## 向ModNPC添加掉落
```cs
namespace MyMod
{
	public class MyNPC : ModNPC
	{
		// 此示例省略了其他代码
		public override void ModifyNPCLoot(NPCLoot npcLoot) {
			// 这是我们添加物品掉落规则的地方，这里是一个简单的示例：
			npcLoot.Add(ItemDropRule.Common(ItemID.Shackle, 50));
		}
	}
}
```

## 向原版NPC添加掉落
```cs
namespace MyMod
{
	public class MyGlobalNPC : GlobalNPC
	{
		public override void ModifyNPCLoot(NPC npc, NPCLoot npcLoot) {
			// 首先，我们需要检查npc.type以查看代码是否为我们想要更改的原版NPC运行
			if (npc.type == NPCID.VampireBat) {
				// 这是我们为VampireBat添加物品掉落规则的地方，这里是一个简单的示例：
				npcLoot.Add(ItemDropRule.Common(ItemID.Shackle, 50));
			}
			// 我们可以在这里使用其他if语句来调整其他原版NPC的掉落规则
		}
	}
}
```

### 特殊情况
一些原版boss需要特殊条件和规则来检测何时被击杀并准备掉落其战利品。

吞世者：
```c#
if (System.Array.IndexOf(new int[] { NPCID.EaterofWorldsBody, NPCID.EaterofWorldsHead, NPCID.EaterofWorldsTail }, npc.type) > -1)
{
	LeadingConditionRule leadingConditionRule = new(new Conditions.LegacyHack_IsABoss());
	leadingConditionRule.OnSuccess(/*您的规则放在这里*/);
	//leadingConditionRule.OnSuccess(/*其他规则作为新行*/);
	npcLoot.Add(leadingConditionRule);
}
```

双子：
```c#
if (npc.type == NPCID.Retinazer || npc.type == NPCID.Spazmatism)
{
	LeadingConditionRule leadingConditionRule = new LeadingConditionRule(new Conditions.MissingTwin());
	leadingConditionRule.OnSuccess(/*您的规则放在这里*/);
	//leadingConditionRule.OnSuccess(/*其他规则作为新行*/);
	npcLoot.Add(leadingConditionRule);
}
```

## 向所有NPC添加掉落（全局规则）
```cs
namespace MyMod
{
	public class MyGlobalNPC : GlobalNPC
	{
		public override void ModifyGlobalLoot(GlobalLoot globalLoot) {
			// 这是我们为所有NPC添加全局规则的地方，这里是一个简单的示例：
			globalLoot.Add(ItemDropRule.Common(ItemID.Shackle, 50));
		}
	}
}
```

# 预备知识
## 概率（分母和分子）
物品的概率通常向用户显示为诸如25%之类的东西，但在代码中，它表示为分数。分数由分子（顶部的数字）和分母（底部的数字）组成。例如，分数`1/4`对应25%。许多掉落规则只使用分母并假定分子为1，但应该有其他选项来构建具有非1分子的掉落规则。

## 幸运
// TODO：什么是幸运？它如何影响掉落？哪些规则受影响？

# 典型物品掉落规则
现在您知道在哪里放置物品掉落规则，现在我们将学习如何制作实际规则。许多规则有多种创建方式。最常见的方法是使用`ItemDropRule`类中的静态辅助方法，但您也可以使用底层类直接创建规则。

下面描述的每个规则都必须注册到战利品数据库。例如，如果您希望使用的规则是`ItemDropRule.Common(ItemID.BeeGun)`，那么您最终将编写的代码将是`npcLoot.Add(ItemDropRule.Common(ItemID.BeeGun));`。

## 掉落单个物品
`ItemDropRule.Common(int itemId, int chanceDenominator = 1, int minimumDropped = 1, int maximumDropped = 1)`
或者：`new CommonDrop(int itemId, int chanceDenominator, int amountDroppedMinimum = 1, int amountDroppedMaximum = 1, int chanceNumerator = 1)`

此规则将以给定参数的概率掉落物品。也可以控制掉落的堆叠大小：
```cs
ItemDropRule.Common(ItemID.BeeGun) // 始终掉落1把蜜蜂枪
ItemDropRule.Common(ItemID.BeeGun, 8) // 每8次掉落1把蜜蜂枪（12.5%概率）
ItemDropRule.Common(ItemID.Torch, 4, 10, 15) // 以1/4概率（25%概率）掉落10到15个火把的堆叠
new CommonDrop(ItemID.Torch, 5, 10, 15, 2) // 以2/5概率（40%概率）掉落10到15个火把的堆叠
```

## 掉落多个物品中的一个
`ItemDropRule.OneFromOptions(int chanceDenominator, params int[] options)`
或者`ItemDropRule.OneFromOptionsWithNumerator(int chanceDenominator, int chanceNumerator, params int[] options)`
或者`new OneFromOptionsDropRule(int chanceDenominator, int chanceNumerator, params int[] options)`

此规则将从物品选项中选择1个掉落。这通常是boss用来掉落其boss武器套装之一的方式。情况稍微复杂一些，因为boss通常有条件地掉落boss袋。请阅读LeadingConditionRule部分获取更多信息。
```cs
// 100%概率掉落这3个物品中的一个
ItemDropRule.OneFromOptions(1, ItemID.MagicDagger, ItemID.PhilosophersStone, ItemID.StarCloak)
// 100或1%概率掉落这3个物品中的一个。或者，每个实际上是0.33%概率。
ItemDropRule.OneFromOptions(100, ItemID.AncientCobaltHelmet , ItemID.AncientCobaltBreastplate , ItemID.AncientCobaltLeggings)
```

## 以不同的专家模式概率掉落单个物品
`ItemDropRule.NormalvsExpert(int itemId, int chanceDenominatorInNormal, int chanceDenominatorInExpert)`
或者`new DropBasedOnExpertMode(IItemDropRule ruleForNormalMode, IItemDropRule ruleForExpertMode)`

许多有用的物品在专家模式中掉落得更频繁。
```cs
ItemDropRule.NormalvsExpert(ItemID.BlessedApple, 40, 30) // 普通模式1/40（2.5%）概率。专家模式1/30（3.33%）概率。
```

## 条件性掉落物品
`ItemDropRule.ByCondition(IItemDropRuleCondition condition, int itemId, int chanceDenominator = 1, int minimumDropped = 1, int maximumDropped = 1, int chanceNumerator = 1)`
或者`new ItemDropWithConditionRule(int itemId, int chanceDenominator, int amountDroppedMinimum, int amountDroppedMaximum, IItemDropRuleCondition condition, int chanceNumerator = 1)`

通过传入`IItemDropRuleCondition`，可以构建仅在满足某些条件时才会掉落物品的物品掉落规则。
```cs
// 如果满足SoulOfLight条件，20%概率掉落1个灵魂之光
new ItemDropWithConditionRule(ItemID.SoulofLight, 5, 1, 1, new Conditions.SoulOfLight())
// 如果世界是血腥但不专家模式，掉落30到90个血腥矿石
ItemDropRule.ByCondition(new Conditions.IsCrimsonAndNotExpert(), ItemID.CrimtaneOre, 1, 30, 90)
```

## 如果其他规则未选中则掉落物品
请参阅[链接规则](#链接规则)获取有关分支规则的信息。

## 像双子一样掉落物品
这需要自定义条件，请参阅下面的相关内容。您可以查看`Conditions.MissingTwin`的代码作为如何实现的指南。

## 像月锭一样逐个掉落物品堆叠
`new DropOneByOne(int itemId, DropOneByOne.Parameters parameters)`
请参阅[MinionBossBody](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/NPCs/MinionBoss/MinionBossBody.cs#L202)获取使用`DropOneByOne`的示例。

## Boss袋
请参阅下面的[完整Boss示例](#完整boss示例)

## 实例化或每个玩家
// TODO

## 等等
// TODO：在此添加其他常见规则

# 条件（IItemDropRuleCondition）
许多掉落规则允许提供`IItemDropRuleCondition`。此对象规定掉落发生所需的条件。常见条件包括当前事件、世界中击败的boss、正常vs专家模式、当前生物群落等。
// TODO：生物群落、boss击败、专家

## 自定义条件
如果需要，您可以创建自己的条件。

请参阅ExampleMod中的`ExampleDropCondition`及其用法作为自定义条件的示例。

### 首次击败Boss条件
猪笼草保证掉落手榴弹发射器和50-149个火箭I。这是那段代码：
```cs
// 首先，创建一个非专家规则
LeadingConditionRule notExpertRule = new LeadingConditionRule(new Conditions.NotExpert());
npcLoot.Add(notExpertRule);

// 当非专家规则为真时，尝试这个firstTimeKillingPlanteraRule
LeadingConditionRule firstTimeKillingPlanteraRule = new LeadingConditionRule(new Conditions.FirstTimeKillingPlantera());
notExpertRule.OnSuccess(firstTimeKillingPlanteraRule);

// 此规则掉落手榴弹发射器和50-149个RocketI
IItemDropRule greanadeLauncherRule = ItemDropRule.Common(ItemID.GrenadeLauncher);
greanadeLauncherRule.OnSuccess(ItemOrderBase.Common(ItemID.RocketI, 1, 50, 150), hideLootReport: true);

// 当firstTimeKillingPlanteraRule成功时，greanadeLauncherRule将被尝试，掉落物品
firstTimeKillingPlanteraRule.OnSuccess(greanadeLauncherRule, hideLootReport: true);
// 否则，这7个规则中的一个将被尝试
firstTimeKillingPlanteraRule.OnFailedConditions(new OneFromRulesRule(1, greanadeLauncherRule, ItemDropRule.Common(ItemID.VenusMagnum), ItemDropRule.Common(ItemID.NettleBurst), ItemDropRule.Common(ItemID.LeafBlow), ItemDropRule.Common(ItemID.FlowerPow), ItemDropRule.Common(ItemID.WaspGun), ItemDropRule.Common(ItemID.Seedler)));
```
从这个设置中，规则规定当不在专家模式时，如果是第一次击杀猪笼草，则保证掉落手榴弹发射器。当不是第一次时，7个规则之一将掉落。重要的部分是`Conditions.FirstTimeKillingPlantera`条件。这段代码如下所示：
```cs
public class FirstTimeKillingPlantera : IItemDropRuleCondition, IProvideItemConditionDescription
{
	public bool CanDrop(DropAttemptInfo info) => !NPC.downedPlantBoss;
	public bool CanShowItemDropInUI() => true;
	public string GetConditionDescription() => null;
}
```
重要的部分是`CanDrop`方法，它简单地检查`!NPC.downedPlantBoss`（意味着当boss未被击败时可以掉落），这就是游戏跟踪世界中猪笼草是否被击败的方式。最关键的是，NPC掉落是在boss标志如`NPC.downedPlantBoss`设置为true之前计算的，这允许这个机制工作。

同样的方法可用于其他原版和模组boss。代码中只存在`FirstTimeKillingPlantera`，但模组制作者可以使用适当的[NPC.downedX布尔值](https://github.com/tModLoader/tModLoader/wiki/NPC-Class-Documentation#downedboss1)为任何其他boss创建`IItemDropRuleCondition`。模组boss也可以用相同的方式完成，只是使用[ModSystem类中的boss布尔值](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Common/Systems/DownedBossSystem.cs#L15)来跟踪

# 链接规则
规则可以链接在一起形成更复杂的物品掉落逻辑。例如，女王蜜蜂的掉落之一是4个选项中的1个。33%概率蜂巢魔杖，每个11%概率蜜蜂帽、蜜蜂衬衫和蜜蜂裤子。为此使用以下代码：
```cs
ItemDropRule.ByCondition(new Conditions.NotExpert(), ItemID.HiveWand, 3).OnFailedRoll(ItemDropRule.OneFromOptionsNotScalingWithLuck(2, ItemID.BeeHat, ItemID.BeeShirt, ItemID.BeePants))
```
首先，蜂巢魔杖以33%概率掉落，但仅当世界不是专家模式。通过`OnFailedRoll`方法链接到该物品掉落规则的是一个规则，以50%概率掉落3个蜜蜂虚荣套装物品之一。通过`OnFailedRoll`链接的规则仅在原始规则的条件满足但随机概率失败时使用。由于50%概率有66%的机会被尝试，最终概率是33%机会获得蜜蜂虚荣物品之一，或每个11%机会。

其他链接选项包括`OnSuccess`和`OnFailedConditions`。`OnSuccess`在原始规则成功时运行，`OnFailedConditions`在条件失败时运行。

使用名为`LeadingConditionRule`的规则，一组规则可以嵌套在单个规则下，而不是为每个规则重复相同的条件。这段代码定义了一个规则，其中`HiveWand`以25%概率掉落，10到30个蜜蜂炸弹以50%概率掉落，但仅当不在专家模式时。
```cs
// 此代码使用LeadingConditionRule将几个规则逻辑地嵌套在其下。
LeadingConditionRule leadingConditionRule = new LeadingConditionRule(new Conditions.NotExpert());
leadingConditionRule.OnSuccess(ItemDropRule.Common(ItemID.HiveWand, 4));
leadingConditionRule.OnSuccess(ItemDropRule.Common(ItemID.Beenade, 2, 10, 30));
npcLoot.Add(leadingConditionRule);

vs

// 此方法重复条件代码。使用任何适合您逻辑的方法
npcLoot.Add(ItemDropRule.ByCondition(new Conditions.NotExpert(), ItemID.HiveWand, 4));
npcLoot.Add(ItemDropRule.ByCondition(new Conditions.NotExpert(), ItemID.Beenade, 2, 10, 30));
```

其他常见模式是一次掉落全套护甲，或带有其特殊弹药的武器。方法类似。
```cs
//以20%概率掉落全套铜护甲。这是通过以20%概率掉落头盔，然后成功后以100%概率掉落其他部件来实现的。
IItemDropRule copperArmorRule = ItemDropRule.Common(ItemID.CopperHelmet, 5);
copperArmorRule.OnSuccess(ItemDropRule.Common(ItemID.CopperChainmail, 1));
copperArmorRule.OnSuccess(ItemDropRule.Common(ItemID.CopperGreaves, 1));
npcLoot.Add(copperArmorRule);

//以50%概率掉落雪球炮，并附带75-150个雪球。hideLootReport用于防止雪球出现在博物志中。这在这里是有意义的，因为雪球与雪球炮相关联
var snowballCannonRule = ItemDropRule.Common(ItemID.SnowballCannon, 2);
snowballCannonRule.OnSuccess(ItemDropRule.Common(ItemID.Snowball, 1, 75, 150), hideLootReport: true);
npcLoot.Add(snowballCannonRule);
```

### 常见错误
一个常见错误是意外地将链接规则添加为正常规则。这导致条件看似不工作。这是一个容易犯的错误。让我们以上面的`HiveWand`/`Beenade`示例为例。如果模组制作者编写以下内容，`NotExpert`条件看似被忽略：

```cs
LeadingConditionRule leadingConditionRule = new LeadingConditionRule(new Conditions.NotExpert());
npcLoot.Add(leadingConditionRule.OnSuccess(ItemDropRule.Common(ItemID.HiveWand, 4)));
npcLoot.Add(leadingConditionRule.OnSuccess(ItemDropRule.Common(ItemID.Beenade, 2, 10, 30)));
```

这里的问题是`leadingConditionRule`实际上没有作为掉落规则添加。`OnSuccess`方法返回被链接的规则，导致2个`ItemDropRule.Common`规则被意外直接添加，因此忽略了`NotExpert`条件。当使用`OnSuccess`、`OnFailedConditions`或`OnFailedRoll`时，确保将它们用作与`npcLoot.Add`分开的语句，并且只在所有链接规则链接到它之后才添加一次`LeadingConditionRule`。

# 完整Boss示例
在Terraria中，boss根据游戏模式掉落不同的物品。在专家模式中，为每个玩家掉落一个boss袋。在普通模式中，代替掉落的是原本会从boss袋中掉落的物品（专家专属物品除外）。此示例显示此模式。
您可以在[MinionBossBody](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/NPCs/MinionBoss/MinionBossBody.cs)上查看`ModifyNPCLoot`代码，在[MinionBossBag](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Consumables/MinionBossBag.cs)上查看相应的`ModifyItemLoot`代码，下面是一个最小示例来展示它：
```cs
//处理适当的难度掉落和boss袋的重要部分如下：
//1. Boss：
//1.1. 在ModifyNPCLoot hook中
npcLoot.Add(ItemDropRule.BossBag(ModContent.ItemType<MinionBossBag>())); // BossBag规则自动检查专家模式本身
//1.2. 放置任何基于"非专家"条件属于袋中的战利品
LeadingConditionRule notExpertRule = new LeadingConditionRule(new Conditions.NotExpert());
notExpertRule.OnSuccess(ItemDropRule.Common(ModContent.ItemType<MinionBossMask>(), 7));

//2. 袋子：
//2. 在ModItem中
//2.1. 在SetStaticDefaults中，设置ItemID.Sets.BossBag为true
ItemID.Sets.BossBag[Type] = true;
//2.2. 在CanRightClick中返回true
public override bool CanRightClick() {
	return true;
}
//2.3. 在ModifyItemLoot hook中，复制来自ModNPC.ModifyNPCLoot的掉落并添加专家专属掉落和金钱
public override void ModifyItemLoot(ItemLoot itemLoot) {
	itemLoot.Add(ItemDropRule.Common(ModContent.ItemType<MinionBossMask>(), 7)); // 复制来自ModNPC.ModifyNPCLoot的非专家掉落
	itemLoot.Add(ItemDropRule.Common(ItemID.JungleYoyo)); // 专家专属掉落示例
	itemLoot.Add(ItemDropRule.CoinsBasedOnNPCValue(ModContent.NPCType<MinionBossBody>()))); // 掉落金钱
}
```

# 查阅原版掉落代码
如果您好奇各种原版掉落概率如何对应代码，查阅反编译代码会很有益。所有原版掉落都在`Terraria.GameContent.ItemDropRules.ItemDropDatabase`类中找到。使用查找工具和您感兴趣的NPC的NPCID号来找到相关代码片段。

## 示例
// TODO：查找特定规则的示例

### 击杀NPC的玩家
// TODO：此部分仍是1.3方法
有时我们想为最后攻击NPC的玩家做点什么。NPC有一个lastInteraction字段，默认为255，意味着没有玩家伤害过npc。如果城镇NPC或陷阱造成NPC所有伤害，NPC可能会以lastInteraction仍为255的方式死亡。因此，通常可能看起来像这样为奖励或影响击杀NPC的玩家编写代码，如有必要，回退到FindClosestPlayer：

```c#
int playerIndex = npc.lastInteraction;
if (!Main.player[playerIndex].active || Main.player[playerIndex].dead)
{
	playerIndex = npc.FindClosestPlayer(); // 由于lastInteraction可能是无效玩家，回退到最近的玩家
}
Player player = Main.player[playerIndex];
// 其他影响玩家的代码。如果相关，可能需要ModPackets
```

# 编辑掉落
您也可以编辑掉落。请参阅[ExampleNPCLoot](https://github.com/tModLoader/tModLoader/blob/1.4/ExampleMod/Common/GlobalNPCs/ExampleNPCLoot.cs)了解更多。

# 基础级别未涵盖的内容
* 请告诉我们。
