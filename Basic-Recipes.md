***
本指南已更新至1.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-Recipes/f1a9c7a29f517a696631670bf58f75e7a7e4ccb9)
***

# 基础配方
配方可以在3个地方添加到游戏中：`ModItem.AddRecipes`、`GlobalItem.AddRecipes`和`ModSystem.AddRecipes`。您在哪里添加配方取决于您的组织偏好，但请注意`ModItem.CreateRecipe`方法不能像使用`Recipe.Create`那样到处使用。

配方由成分（制作结果消耗的物品）、工艺台（您需要站在旁边的瓷砖）和结果（创建的物品）组成。

"材料"提示会自动添加到在至少一个配方中作为成分使用的每个物品，不应手动添加。

# 基础配方结构
## 必需的Using语句
首先，确保在.cs文件中有以下using语句。这些using语句将让我们访问配方相关函数：
```cs
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;
```
## 创建配方并分配配方结果
要开始配方，我们创建`Recipe`类的实例。我们通过`Recipe.Create`方法或`ModItem.CreateRecipe`方法来做这件事。创建配方时，我们需要分配配方结果类型和结果堆叠。`ModItem.CreateRecipe`方法假定配方结果为当前`ModItem`，因此只需要堆叠大小。堆叠大小是可选的，默认为1：

在`GlobalItem`类中，我们输入"Recipe.Create"来使用`Recipe.Create`方法。以下是各种示例，展示原版和模组成分以及默认堆叠大小和自定义堆叠大小：
```cs
Recipe recipe = Recipe.Create(ItemID.AlphabetStatueZ);
Recipe recipe = Recipe.Create(ItemID.AlphabetStatueZ, 5);
Recipe recipe = Recipe.Create(ModContent.ItemType<Content.Items.ExampleItem>());
Recipe recipe = Recipe.Create(ModContent.ItemType<Content.Items.ExampleItem>(), 10);
```
在`ModSystem`类中，我们同样使用`Recipe.Create`：
```cs
Recipe recipe = Recipe.Create(ItemID.AlphabetStatueZ);
```
在`ModItem`类中，我们可以使用`Recipe.Create`创建不以这个`ModItem`为结果的配方：
```cs
Recipe recipe = Recipe.Create(ItemID.AlphabetStatueZ);
// ... 我们可以直接使用"CreateRecipe"来创建以这个ModItem为结果的配方。我们可以选择提供堆叠大小：
Recipe recipe = CreateRecipe();
Recipe recipe = CreateRecipe(10);
```
## 添加配方成分
现在，我们添加成分。这些是我们希望在配方制作时消耗的物品：

```cs
recipe.AddIngredient(ItemID.DirtBlock);
recipe.AddIngredient(ItemID.Ruby);
```
AddIngredient也接受一个可选参数来指定堆叠大小：

```cs
recipe.AddIngredient(ItemID.Chain, 10);
```
前面的示例通过引用ItemID类向配方添加了原版物品。有了像Visual Studio这样的IDE，您会发现自动完成和智能感知非常有用，但您也可以[在这里查找ItemID名称或值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Content-IDs#item-ids)。

我们也可以添加此模组添加的模组物品。我们有几种方法可以做到这一点。您可以选择您喜欢的方法。所有这些方法都可以添加堆叠参数。这些示例都指向`ExampleMod.Content.Items`命名空间中的`ExampleItem`类：

```cs
recipe.AddIngredient<Content.Items.ExampleItem>();
recipe.AddIngredient<Content.Items.ExampleItem>(10);
recipe.AddIngredient(ModContent.ItemType<Content.Items.ExampleItem>());
recipe.AddIngredient(ModContent.GetInstance<Content.Items.ExampleItem>());
recipe.AddIngredient(Mod, "ExampleItem");
```

如果您在`ModItem`类中，您也可以直接在配方中使用该ModItem：
```cs
recipe.AddIngredient(this, 5);
```

## 添加合成工艺台
接下来，我们可以指定合成工艺台。这遵循与物品相同的模式。您可以[在这里查找TileID](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Tile-IDs)。

如果您希望物品手工合成，您应该跳过此步骤。

```cs
recipe.AddTile(TileID.WorkBenches);
recipe.AddTile(TileID.Anvils);
recipe.AddTile<Content.Tiles.Furniture.ExampleWorkbench>();
recipe.AddTile(ModContent.TileType<Content.Tiles.Furniture.ExampleWorkbench>());
recipe.AddTile(ModContent.GetInstance<Content.Tiles.Furniture.ExampleWorkbench>());
recipe.AddTile(Mod, "ExampleWorkbench");
```
## 注册配方
最后，我们需要告诉tModLoader我们的Recipe已完成并将其添加到游戏中：
```cs
recipe.Register();
```

## 使用原版vs模组成分和瓷砖
作为总结，原版物品和瓷砖使用`TileID`和`ItemID`类，而模组物品使用`ModContent.TileType`和`ModContent.ItemType`方法：

```cs
recipe.AddTile(TileID.WorkBenches); // 原版瓷砖
recipe.AddTile(ModContent.TileType<Content.Tiles.Furniture.ExampleWorkbench>()); // 模组瓷砖
recipe.AddIngredient(ItemID.Meowmere); // 原版物品
recipe.AddIngredient(ModContent.ItemType<Content.Items.ExampleItem>()); // 模组物品
```

## 完整基础配方示例
这里有几个完整的基础配方示例。第一个示例位于ModSystem类中。配方需要1个铁链和10个石头块，在工作台和铁砧合成，结果物品是1个字母雕像A。

```cs
Recipe recipe = Recipe.Create(ItemID.AlphabetStatueA);
recipe.AddIngredient(ItemID.StoneBlock, 10);
recipe.AddIngredient(ItemID.Chain);
recipe.AddTile(TileID.WorkBenches);
recipe.AddTile(TileID.Anvils);
recipe.Register();
```

这个示例位于`ModItem`类中。这个配方用5个`ExampleItem`合成3个ModItem：

```cs
Recipe recipe = CreateRecipe(3);
recipe.AddIngredient<Content.Items.ExampleItem>(5);
recipe.Register();
```

# 链式语法
本指南中的代码相当冗长。使用链式语法，我们可以减少代码的冗长度。这种方法可能对模组制作者更令人满意。只有最后一行有分号。
```cs
Recipe.Create(ItemID.AlphabetStatueA)
	.AddIngredient(ItemID.StoneBlock, 10)
	.AddIngredient(ItemID.Chain)
	.AddTile(TileID.WorkBenches)
	.AddTile(TileID.Anvils)
	.Register();
```


# 配方组
配方组允许单个成分从一组相似物品中选择。例如，铁锭或铅锭都可以用于合成配方。配方组在[中级配方](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#recipegroups)中讨论

# 条件
除了成分和合成工艺台，配方还可以有条件。每个条件必须满足才能制作配方。

## 水、蜂蜜、岩浆、闪光
水、蜂蜜、岩浆和闪光在技术上不是瓷砖，因此要使配方需要站在这些旁边，请使用以下之一：

```cs
recipe.AddCondition(Condition.NearWater);
recipe.AddCondition(Condition.NearLava);
recipe.AddCondition(Condition.NearHoney);
recipe.AddCondition(Condition.NearShimmer);
```
请注意，`NearWater`也被水池满足，所以不要单独添加水池瓷砖。

## 其他原版条件
[Conditions.cs](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/Condition.cs)页面列出了所有其他原版条件。以类似方式使用它们。

## 自定义条件
模组也可以使用自定义条件，这些在[中级配方指南的自定义条件部分](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#custom-conditions)中讨论。

# 多个配方
在同一个AddRecipes中有多个配方时，确保不要重新声明您的变量名。以下将导致错误：

```cs
Recipe recipe = Recipe.Create(ItemID.AlphabetStatueA);
// 其他代码
Recipe recipe = Recipe.Create(ItemID.AlphabetStatueB);
// 其他代码
```
您可以将变量命名为recipe1、recipe2等，但更干净的方法是重用同一个变量：

```cs
Recipe recipe = Recipe.Create(ItemID.AlphabetStatueA);
// 其他代码
recipe = Recipe.Create(ItemID.AlphabetStatueB);
// 其他代码
```

如果您使用[链式语法](#chain-syntax)，那么只需对每个后续配方执行相同方法即可。

# 制作"升级版"原版瓷砖
另外，您可能希望您的ModTile算作，比如说，工作台或铁砧。为此，请在您的`ModTile.SetStaticDefaults`中添加以下内容：

```cs
AdjTiles = [TileID.WorkBenches];
```

# 完整示例
这里有2个完整示例，一个展示在`ModItem`类中添加配方，更适合涉及该`ModItem`的配方，另一个展示在`Mod`类中添加配方，更适合涉及原版物品的配方。从技术上讲，配方可以放在任一位置，但出于组织目的，有时将配方放在ModItem类中会很好。
## ModItem示例
```cs
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;

namespace ExampleMod.Content.Items.Accessories
{
	public class ExampleShield : ModItem
	{
		// 其他方法和字段在这里...

		public override void AddRecipes()
		{
			// 这个示例展示了更现代的配方创建"链式风格"。
			// 有关更简单的示例，请参阅下面的ModSystem代码。
			CreateRecipe()
				.AddIngredient<ExampleItem>()
				.AddTile<ExampleWorkbench>()
				.Register();
		}
	}
}
```

## ModSystem示例
请记住，在`ModSystem`中，我们必须传入配方结果物品类型。
```cs
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;

namespace ExampleMod.Content
{
	public class ExampleRecipes : ModSystem
	{
		// 其他方法和字段在这里...

		public override void AddRecipes()
		{
			// 这是配方的示例。
			Recipe recipe = Recipe.CreateRecipe(ItemID.Wood, 999);
			recipe.AddIngredient<Content.Items.ExampleItem>();
			recipe.Register();

			// 这里我们重用'recipe'，这意味着我们不需要重新声明它是Recipe
			recipe = Recipe.CreateRecipe(ItemID.PumpkinPie, 2);
			recipe.AddIngredient(ItemID.BlueBerries, 20);
			recipe.AddTile(TileID.WorkBenches);
			recipe.Register();
		}
	}
}
```

# 常见错误
### Error CS0117 'ItemID' (or TileID) does not contain a definition for 'MyModItem'
您尝试使用原版物品语法添加ModItem，请重新阅读本教程。
### Error CS0103 The name 'recipe' does not exist in the current context
您忘记将第一个配方声明为`Recipe`。确保代码中的第一个配方以`Recipe recipe = ...`开头。
### Error CS0128 A local variable named 'recipe' is already defined in this scope
请阅读上面的`多个配方`部分。
### My recipes aren't in game
检查您的AddRecipes方法是否有override而不是virtual。
### No suitable method to override
确保您只在Mod、ModSystem或ModItem中重写AddRecipes。
### The crafting station or ingredient shown in game doesn't match my recipe code
这很可能是因为您错误地将`ItemID`传入了`AddTile`或将`TileID`传入了`AddIngredient`。通过使用错误的ID类，您无意中引用了错误的ID。

# 相关参考
* [原版物品ID](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Content-IDs#item-ids)
* [原版瓷砖ID](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Content-IDs#tile-ids)
* [配方文档](https://docs.tmodloader.net/docs/stable/class_recipe.html)
* [ModSystem文档](http://docs.tmodloader.net/docs/stable/class_mod_system.html)
* [ModItem文档](http://docs.tmodloader.net/docs/stable/class_mod_item.html)
* [GlobalItem文档](http://docs.tmodloader.net/docs/stable/class_global_item.html)

# 基础级别未涵盖的内容
配方还有其他方面将在更高级的指南中介绍：
* [配方组](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#recipe-groups) -- 中级 -- 允许单个成分为一大组中的任意一个，就像大多数涉及木材的配方可以使用白桦木或珍珠木。（"任意木材"）
* [编辑配方](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#editing-recipes) -- 中级 -- 编辑现有配方或禁用现有配方
* [排序配方](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#ordering-recipes) -- 中级 -- 自定义配方在合成列表中的显示位置。
* [闪光分解](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#shimmer-decrafting) -- 中级 -- 自定义通过闪光液体分解时使用的配方。
* [自定义条件](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#custom-conditions) -- 中级 -- 允许配方需要满足自定义条件才能制作。
* [自定义物品消耗](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#custom-item-consumption) -- 中级 -- 允许配方有条件地消耗比平时更少的成分，就像炼金台所做的那样。
* [自定义配方制作行为](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#custom-recipe-craft-behavior) -- 中级 -- 允许在配方制作完成后运行代码。
* [跨模组内容](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Recipes#cross-mod-recipes) -- 中级 -- 在您的配方中使用其他模组的物品或瓷砖。
