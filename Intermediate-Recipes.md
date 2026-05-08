***
本指南已更新至1.4版本。如果您需要查看1.3版本的旧版指南，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes/c5e693d7cf96c9ff58167da11a5da30b759c427c)
***

# 前置条件
* [基础配方](Basic-Recipes)

# 配方组
配方组允许你创建一个可以接受指定材料列表中任意物品的配方。例如，所有品种的木头都在原版的"木头"组中。我们不需要创建8个单独的配方来使用每种原版木头，而是创建1个使用"木头"配方组的配方。模组也可以添加配方组来简化配方。

## 使用现有配方组
原版配方组包括："Wood"、"IronBar"、"PresurePlate"、"Sand"、"Fragment"、"Birds"、"Scorpions"、"Squirrels"、"Bugs"、"Ducks"、"Butterflies"、"Fireflies"、"Snails"、"Dragonflies"、"Turtles"和"Fruit"。要使用原版配方组，只需添加以下代码来代替类似的 `Recipe.AddIngredient` 行：
```csharp
// 选项1：组的名称作为字符串
recipe.AddRecipeGroup("Wood", 5); // 参数1是组名称，参数2是物品堆叠数量
// 选项2：使用RecipeGroupID值。
recipe.AddRecipeGroup(RecipeGroupID.IronBar, 10);
```
请注意，与 `Recipe.AddIngredient` 一样，提供显式堆叠数量的参数是可选的，默认为1。

## 新建配方组
你可能已经注意到，除了"Wood"和"IronBar"之外，没有很多有用的原版配方组。幸运的是，我们可以创建自己的配方组。假设我们想创建一个将魔法镜或冰镜作为材料的配方。如果没有配方组，我们只能这样做：
```csharp
Recipe recipe = CreateRecipe();
recipe.AddIngredient(ItemID.MagicMirror);
recipe.AddIngredient(ItemID.Gel, 10);
recipe.AddTile(TileID.Chairs);
recipe.AddTile(TileID.Tables);
recipe.Register();

recipe = CreateRecipe();
recipe.AddIngredient(ItemID.IceMirror);
recipe.AddIngredient(ItemID.Gel, 10);
recipe.AddTile(TileID.Chairs);
recipe.AddTile(TileID.Tables);
recipe.Register();
```
在这个例子中，情况还不算太糟，但想象一下你有10个或20个类似的物品想要用于你的配方——这很快就会变得难以管理。为了解决这个问题，首先，我们必须添加一个新的配方组。我们在一个 `ModSystem` 类中通过重写 `ModSystem.AddRecipeGroups` 方法来实现：
```csharp
// 需要使用 Terraria.Localization 命名空间指令（using Terraria.Localization;）来调用 Language 方法。
public override void AddRecipeGroups()
{
	RecipeGroup group = new RecipeGroup(() => $"{Language.GetTextValue("LegacyMisc.37")} {Lang.GetItemNameValue(ItemID.MagicMirror)}", ItemID.IceMirror, ItemID.MagicMirror);
	RecipeGroup.RegisterGroup(nameof(ItemID.MagicMirror), group);
}
```
如上所示，我们首先构建组，然后使用所需的名称调用 `RecipeGroup.RegisterGroup`。作为一个约定，对于主要涉及模组特定物品或用法的配方组，请使用 `"ModName:GroupName"`。如果你使用原版物品创建一个可以被其他模组用于相同目的的组，请使用 `nameof(ItemID.IconicItem)`，其中 `IconicItem` 是该组中最早添加到游戏的物品（最小的ItemID值）。例如，如果你要添加一个银锭和钨锭的组，使用银锭，因为它是第一个添加到游戏的物品。这样做可以允许多 个模组无缝协作。如果模组A添加了一个银锭组，模组B添加了类似的组但带有第三个银锭变体，游戏将无缝合并配方组，允许所有使用该组的配方都允许所有变体。

需要注意的是，`Language.GetTextValue("LegacyMisc.37")` 就是所选语言中的"任意"一词，需要在代码顶部添加 `Terraria.Localization` 命名空间指令。这里的语法对于新手来说有点困难（因为它使用了[lambda表达式](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-expressions)），所以请严格遵循它。

需要注意的一点：对 `ModItem` 使用 `ModContent.ItemType<YourModItem>()` 而不是 `ItemID.ItemName`。

接下来，我们需要使用 `RecipeGroup`。我们就像使用原版配方组一样使用它，只是我们的配方组名称会有所不同：
```csharp
recipe.AddRecipeGroup("ModName:GroupName");
```
## 完整示例
```csharp
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;
using Terraria.Localization;

namespace ExampleMod
{
	public class ExampleRecipes : ModSystem
	{
		public override void AddRecipeGroups()
		{
			RecipeGroup group = new RecipeGroup(() => $"{Language.GetTextValue("LegacyMisc.37")} {Lang.GetItemNameValue(ItemID.MagicMirror)}", ItemID.IceMirror, ItemID.MagicMirror);
			RecipeGroup.RegisterGroup(nameof(ItemID.MagicMirror), group);
		}

		public override void AddRecipes()
		{
			Recipe recipe = Recipe.Create(ModContent.ItemType<Items.MinionControlRod>());
			recipe.AddIngredient(ItemID.SlimeStaff);
			recipe.AddRecipeGroup(nameof(ItemID.MagicMirror));
			recipe.AddTile(TileID.Chairs);
			recipe.AddTile(TileID.Tables);
			recipe.Register();

			recipe = Recipe.Create(ModContent.ItemType<Items.SummonersAssociationCard>());
			recipe.AddIngredient(ItemID.Gel, 10);
			// 这里，我们使用RecipeGroup来在一配方中指定所有类型的木头，而不是添加5个ItemID.Wood。
			recipe.AddRecipeGroup("Wood", 5);
			recipe.AddTile(TileID.Chairs);
			recipe.AddTile(TileID.Tables);
			recipe.Register();
		}
	}
}
```
请注意，`Recipe.AddRecipeGroup` 中使用的名称与注册我们的自定义 `RecipeGroup` 时定义的名称相同。

## 编辑原版配方组
在某些情况下，你可能想将自己的物品添加到现有的配方组中。在这个例子中，我们将添加另一种木头类型到"木头"配方组中。
```csharp
public override void AddRecipeGroups()
{
	if (RecipeGroup.recipeGroupIDs.ContainsKey("Wood"))
	{
		int index = RecipeGroup.recipeGroupIDs["Wood"];
		RecipeGroup group = RecipeGroup.recipeGroups[index];
		group.ValidItems.Add(ModContent.ItemType<MyWood>());
	}
}
```
请注意，检查 `if (RecipeGroup.recipeGroupIDs.ContainsKey(...))` 不是*必要的*，但如果某个其他模组由于某种原因完全删除了该配方组，这将防止错误。这也可用于[跨模组兼容性](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content)。

# 自定义条件
在[基础配方指南的条件部分](https://github.com/tModLoader/tModLoader/wiki/Basic-Recipes#conditions)中，介绍了如何在配方中使用现有条件。模组中也可以使用自定义条件。

模组最常见的自定义条件可能是检查玩家是否处于自定义 `ModBiome` 中的条件。为此，请使用 `AddCondition` 方法并传入一个 `Condition` 类的新实例。在 `Condition` 类构造函数中，需要提供一个本地化键和一个没有参数且返回 bool 的方法。在这种情况下，我们将使用 `InModBiome` 方法来查询本地玩家是否在我们的 `ModBiome` 中。这个示例仅在玩家处于 `ExampleSufaceBiome` 时才允许制作配方：
```cs
Recipe.Create(ItemID.AlphabetStatueG)
	.AddIngredient(ItemID.StoneBlock, 3)
	.AddCondition(new Condition("Mods.ExampleMod.Conditions.InExampleBiome", () => Main.LocalPlayer.InModBiome<ExampleSurfaceBiome>()))
	.Register();
```
这种方法适用于在单个配方中使用的效果，但将相同的代码添加到许多不同的配方中会变得混乱且容易出错。解决方案是重用 `Condition`。建议将所有 `Condition` 实例放在一个适当命名的静态类中，以便在整个模组中任何需要使用它的配方中轻松引用。

带有共享 `Condition` 实例的静态类：
```cs
namespace ExampleMod.Content
{
	public static class ExampleConditions
	{
		public static Condition InExampleBiome = new Condition("Mods.ExampleMod.Conditions.InExampleBiome", () => Main.LocalPlayer.InModBiome<ExampleSurfaceBiome>());

		// 其他Condition实例可以放在这里。
	}
}
```
使用静态类中的 `Condition`。只要使用了正确的 using 语句，此代码可以在任何文件中的任何配方中使用：
```cs
Recipe.Create(ItemID.AlphabetStatueG)
	.AddIngredient(ItemID.StoneBlock, 3)
	.AddCondition(ExampleConditions.InExampleBiome)
	.Register();
```
如果用户从指南的配方帮助或类似[配方浏览器](https://steamcommunity.com/sharedfiles/filedetails/?id=2619954303)的模组中查看配方，将向用户显示条件描述。
![image](https://github.com/tModLoader/tModLoader/assets/4522492/e365694c-0405-4fe1-8f11-711b367bbb21)

# 微光拆解
默认情况下，产生某个物品的第一个配方将是用于拆解该物品的配方。可以使用条件自定义此行为。 [ShimmerShowcase.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/ShimmerShowcase.cs) 中的注释和示例教授了如何自定义微光拆解功能。

# 自定义物品消耗
配方并不总是消耗用作材料的所有物品。例如，所有使用 `TileID.Bottles` 作为合成站的配方都将受到[炼金桌效果](https://terraria.wiki.gg/wiki/Alchemy_Table)的影响。所有材料有1/3的几率不被消耗。请注意，模组开发者仍然需要全部数量的材料来制作物品，只是这些材料可能不会被消耗。

模组开发者可以创建自己的自定义配方物品消耗规则，并将其应用于单个配方。这是通过 `AddConsumeItemCallback` 方法完成的。模组开发者提供了一个委托（委托是 `ConsumeItemCallback` 类型），当制作物品时将对每个材料运行该委托。该委托能够调整每个被消耗物品的数量。

```cs
Recipe.Create(ItemID.AlphabetStatueB)
	.AddIngredient(ItemID.StoneBlock, 10)
	.AddIngredient(ItemID.Chain)
	.AddConsumeItemCallback((Recipe recipe, int type, ref int amount) => {
		if (type == ItemID.Chain) {
			amount = 0;
		}
	})
	.AddTile(TileID.HeavyWorkBench)
	.Register();
```
在这个例子中，我们添加了一个不会消耗 `ItemID.Chain` 物品的配方。这是通过添加一个 `ConsumeItemCallback` 委托来实现的，该委托将在检查的成分是 `ItemID.Chain` 时将数量设置为0。这种方法适用于在单个配方中使用的效果，但将相同的代码添加到许多不同的配方中会变得混乱且容易出错。解决方案是重用委托。建议将所有 `ConsumeItemCallback` 委托放在一个适当命名的静态类中，以便在整个模组中任何需要使用它的配方中轻松引用。

带有共享 `ConsumeItemCallback` 委托的静态类：
```cs
namespace ExampleMod.Content
{
	public static class ExampleConsumptionRules
	{
		public static void DontConsumeChain(Recipe recipe, int type, ref int amount) {
			if (type == ItemID.Chain) {
				amount = 0;
			}
		}

		// 其他ConsumeItemCallback委托可以放在这里。
	}
}
```
使用 `ConsumeItemCallback` 委托。只要使用了正确的 using 语句，此代码可以在任何文件中的任何配方中使用：
```cs
Recipe.Create(ItemID.AlphabetStatueC)
	.AddIngredient(ItemID.StoneBlock, 10)
	.AddIngredient(ItemID.Chain)
	.AddConsumeItemCallback(ExampleConsumptionRules.DontConsumeChain)
	.AddTile(TileID.HeavyWorkBench)
	.Register();
```

炼金桌效果使用了一些高级逻辑。首先，炼金桌瓦片本身使用 [AdjTiles](https://github.com/tModLoader/tModLoader/wiki/Basic-Recipes#making-an-upgraded-vanilla-tile) 来充当 `TileID.Bottles`。这意味着它将满足需要 `TileID.Bottles` 的配方的工作台要求。代码还将 `Player.alchemyTable` 布尔值设置为 true，表示玩家处于炼金桌效果下。最后，应用到每个药水配方上的 `ConsumeItemCallback` 代码执行以下操作来检查 `alchemyTable` 布尔值，并为每个物品提供1/3不被消耗的机会：
```cs
public static void Alchemy(Recipe recipe, int type, ref int amount) {
	if (!Main.LocalPlayer.alchemyTable) {
		return;
	}

	int amountUsed = 0;

	for (int i = 0; i < amount; i++) {
		if (!Main.rand.NextBool(3)) {
			amountUsed++;
		}
	}

	amount = amountUsed;
};
```
为了适应模组化情况，模组开发者可能需要使用 `ModPlayer` 布尔值或类似 `Main.LocalPlayer.adjTile[TileID.HeavyWorkBench]` 的东西作为条件，而不是 `alchemyTable` 布尔值。

如上所示，模组开发者可以在 `ConsumeItemCallback` 代码中执行高级逻辑。请记住，除非您告知用户，否则他们不会知道这些效果。例如，炼金桌物品提示告诉玩家"33%的几率不消耗药水制作材料"。

# 自定义配方制作行为
我们可以使用 `AddOnCraftCallback` 方法在配方制作完成后运行代码。原版泰拉瑞亚内容没有这样做，但它对模组添加的高级功能很有用。类似于展示使用 [AddConsumeItemCallback](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#custom-item-consumption) 的方法，我们希望静态存储这些方法以便轻松重用。下面的例子是一个简单的例子，但应该能给你一个关于可以做什么的想法。这个例子在配方制作时随机生成烟花弹射物和五彩纸屑物品到玩家的物品栏中。你可以通过在ExampleMod中制作物品来测试这个：
```cs
Recipe.Create(ItemID.AlphabetStatueZ)
	.AddIngredient(ItemID.StoneBlock, 10)
	.AddIngredient(ItemID.Chain)
	.AddOnCraftCallback(ExampleRecipeCallbacks.RandomlySpawnFireworks)
	.AddTile(TileID.HeavyWorkBench)
	.Register();
```
```cs
namespace ExampleMod.Common
{
	public static class ExampleRecipeCallbacks
	{
		public static void RandomlySpawnFireworks(Recipe recipe, Item item, List<Item> consumedItems, Item destinationStack) {
			if (Main.rand.NextBool(3)) {
				int fireworkProjectile = ProjectileID.RocketFireworksBoxRed + Main.rand.Next(4);
				Projectile.NewProjectile(Main.LocalPlayer.GetSource_FromThis(), Main.LocalPlayer.Top, new Microsoft.Xna.Framework.Vector2(0, -Main.rand.NextFloat(2f, 4f)).RotatedByRandom(0.3f), fireworkProjectile, 0, 0, Main.myPlayer);

				Main.LocalPlayer.QuickSpawnItem(Main.LocalPlayer.GetSource_FromThis(), ItemID.Confetti, 5);
			}
		}
	}
}
```

# 配方排序
配方可以放置在特定配方之前或之后，以便将它们与类似的配方分组在一起。为此，请使用 `SortBeforeFirstRecipesOf`、`SortBefore`、`SortAfterFirstRecipesOf` 或 `SortAfter` 方法。默认情况下，配方将放置在配方列表的末尾。

`SortBeforeFirstRecipesOf` 和 `SortAfterFirstRecipesOf` 接受一个 `ItemID`，使用起来很简单：
```cs
CreateRecipe()
	.AddIngredient(ItemID.Terrarium)
	.AddIngredient(ModContent.ItemType<NPCs.ExampleCritterItem>())
	.SortAfterFirstRecipesOf(ItemID.FrogCage) // 将配方放置在原版青蛙笼配方之后。
	.Register();
```

`SortBefore` 和 `SortAfter` 接受一个特定的 `Recipe`，允许更复杂的逻辑。一种可能的用途是将一个配方放置在两个具有相同结果的配方之间。
```cs
CreateRecipe()
	.AddIngredient<ExampleItem>()
	.AddTile<Tiles.Furniture.ExampleWorkbench>()
	.SortBefore(Main.recipe.First(recipe => recipe.createItem.wingSlot != -1)) // 在任何翅膀之前放置此配方，以便每个翅膀在合成菜单中保持在一起。
	.Register();
```
![image](https://github.com/user-attachments/assets/8a9223ae-8863-41ca-ae0f-604ec0ddbb1d)
![image](https://github.com/user-attachments/assets/e52a6050-735d-4013-847b-0e9504575590)

# 编辑配方
我们可以从 `ModSystem.PostAddRecipes` 方法中编辑原版配方或其他模组的配方基本上，我们遍历配方以找到我们想要调整的配方，然后修改材料、瓦片，甚至完全禁用配方。

## 完整示例
此代码从所有原版配方中移除"Chain"物品。这个例子的第二部分找到并禁用了一个精确的配方。不要从 `Main.recipe` 中删除条目，这将破坏很多东西。
```csharp
public override void PostAddRecipes() {
	for (int i = 0; i < Recipe.numRecipes; i++) {
		Recipe recipe = Main.recipe[i];

		if (recipe.TryGetIngredient(ItemID.Chain, out Item ingredient)) {
			recipe.RemoveIngredient(ItemID.Chain);
		}

		if(recipe.HasRecipeGroup("IronBar") && recipe.HasTile(TileID.Anvils) && recipe.HasResult(ItemID.Chain) && recipe.createItem.stack == 10) {
			recipe.DisableRecipe();
		}
	}
}
```

# 跨模组配方
如果你想在配方中使用其他模组的物品或瓦片，你需要提前计划。阅读[专家级跨模组内容](https://github.com/tModLoader/tModLoader/wiki/Expert-Cross-Mod-Content)了解你需要考虑的一些事项。例如，你引用的模组可能在更新中更改了物品名称，从而在过程中破坏了你的模组。另一个问题是计划其他模组未启用的情况。在这种情况下，你可能需要完全跳过配方或计划一个替代配方。下面的示例展示了替代材料的方法。

```cs
Recipe recipe = Recipe.Create(ItemID.Wood, 999);
if (ModLoader.TryGetMod("ExampleMod", out Mod exampleMod) && exampleMod.TryFind<ModItem>("ExampleWings", out ModItem ExampleWings)) {
	recipe.AddIngredient(ExampleWings.Type);
}
else {
	recipe.AddIngredient(ModContent.ItemType<MyItem>());
	recipe.AddIngredient(ItemID.EnchantedSword);
}
recipe.Register();
```
如你所见，如果 `ExampleMod` 已启用且该模组中存在 `ExampleWings`，则此配方使用 `ExampleMod` 的 `ExampleWings` 物品；如果 `ExampleMod` 未启用，则使用当前模组的 `MyItem` 和原版的 `EnchantedSword`。如果你已选择仅在其他模组启用时才加载你的模组，你可以简化逻辑，因为使用 `modReferences` 时其他模组将始终可用。

# 常见错误

### `MyModItem.AddRecipeGroups()`: 找不到可重写的合适方法
`ModSystem.AddRecipeGroups` 必须放在 `ModSystem` 类中，而不是你的 `ModItem` 类中。

# 相关参考
* [原版物品ID](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Content-IDs#item-ids)
* [原版瓦片ID](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Content-IDs#tile-ids)
* [配方文档](http://docs.tmodloader.net/docs/stable/class_recipe.html)
* [ModSystem文档](http://docs.tmodloader.net/docs/stable/class_mod_system.html)
* [ModItem文档](http://docs.tmodloader.net/docs/stable/class_mod_item.html)
* [GlobalItem文档](http://docs.tmodloader.net/docs/stable/class_global_item.html)
