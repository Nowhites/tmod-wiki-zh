***
本指南已更新至1.4版本。如果您需要查看1.3版本的旧版指南，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Saving-and-loading-using-TagCompound/a03f9a9c9530ff209853233efd9e6dbbfea879d7)
***

# 什么是TagCompound

`TagCompound` 是使用 tModLoader 保存自定义数据的数据格式。我们在 `ModSystem`、`ModItem`、`ModPlayer`、`ModNPC`、`GlobalItem`、`GlobalNPC` 和 `ModTileEntity` 中使用 `TagCompound`。如果你熟悉这些概念，可以把 `TagCompound` 想象为一个可嵌套的字典或 JSON，这非常接近。像字典一样，我们提供字符串键和任何支持类型的值。

请使用 [NBTExplorer](https://github.com/jaquadro/NBTExplorer/releases/tag/v2.8.0-win) 通过它打开 .twld 或 .tplr 文件来可视化 TagCompounds。通过这种方式查看数据可以帮助你验证数据是否以合理的方式保存。

# 关键概念
以下是使用 `TagCompound` 时需要记住的一些事项。

##在哪里使用 `TagCompound`
我们在 `ModSystem`、`ModItem`、`ModPlayer`、`ModNPC`、`GlobalItem`、`GlobalNPC` 和 `ModTileEntity` 中使用 `TagCompound`。我们在 `SaveData` 方法中保存数据，在 `LoadData` 方法中加载数据（除了 `ModSystem` 我们在 `SaveWorldData` 和 `LoadWorldData` 中保存和加载而不是）。不要在字段中存储 `TagCompound`，它们不打算在加载和保存过程之外存在。

## 兼容的数据类型
所有原始数据类型以及 `byte[]`、`int[]` 和其他支持数据类型的 `List` 都受支持。通常我们使用 `GetInt` 或 `GetBool` 等方法，但对于没有定义特定方法的类型，我们可以使用 `Get<Type>`。请参阅 [TagCompound文档](http://docs.tmodloader.net/docs/stable/class_tag_compound.html) 或依靠你的IDE的[自动完成](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#autocomplete--intellisense)来找到你需要的方法。除了方法名称建议的类型外，还支持 `ushort`、`uint`、`ulong`、`Vector2`、`Vector3`、`Item`、`Color`、`Point16`、`Rectangle` 和 `EntityDefinition`。还可以通过实现 `TagSerializable` 接口、创建继承自 `TagSerializer` 的类或手动嵌套 `TagCompound` 来实现其他支持。

## 模组版本更新
使用 `TagCompound` 可以帮助模组开发者顺利更新模组。例如，如果模组的 v1.0 只保存了 `a`，但 v2.0 同时保存了 `a` 和 `b`，在大多数情况下，模组开发者不需要额外检查验证 `b` 的值或存在性。他们只需要在 `b` 具有非默认值时做额外的工作。例如，如果值不存在于 `TagCompound` 中，`b = tag.GetInt("b");` 将返回 int 的默认值，而 int 的默认值是0。如果0是模组对缺失条目的期望值，这很好。如果模组期望缺失条目具有非默认值，可以使用以下方法：

```cs
public override void Initialize()
{
	QuestsLeft = 10;
}

public override void LoadData(TagCompound tag)
{
	if (tag.ContainsKey("QuestsLeft"))
		QuestsLeft = tag.GetInt("QuestsLeft");
}
```

请注意 `Initialize` 如何将值设置为10，这是我们模组期望的默认值。然后我们检查标签是否有"QuestsLeft"的条目，如果有，我们就检索该值。如果没有这个检查，如果键不存在，`tag.GetInt` 将返回0。设计你的变量使得默认值与你期望的默认值相对应，如果你想避免检查 `ContainsKey`，这可能很有用。这个例子使用了 `ModPlayer` 中的 `Initialize` 方法来展示这个概念，但其他类有类似的方法适合初始化数据。例如，对于 `ModItem`，初始值可以在 `SetDefaults` 中设置。

### 更新数据类型
如果在版本更新中保存的值改变了`类型`，例如从 `float` 改为 `int`，必须格外小心以避免遇到异常。

<details><summary>数据类型更新详情</summary><blockquote>

当 `TagCompound` 中某个键的值是 `float` 时，尝试使用 `tag.GetInt(key)`、`tag.Get<int>(key)` 或 `tag.TryGet<int>(key, out int value))` 将抛出异常。使用新键将导致现有用户在更新到模组最新版本时丢失他们的数据。如果不可避免，以下代码显示了在数据类型改变时保留旧数据的示例：

```cs
if (tag.ContainsKey("MyKey")) { 
	object MyKeyData = tag["MyKey"];

	if (MyKeyData is float MyKeyFloat)
		MyKey = (int)MyKeyFloat;

	if (MyKeyData is int MyKeyInt)
		MyKey = MyKeyInt;
}
```

</blockquote></details>

### 重命名类
如果在版本更新中你更改了类的名称，例如从 `MyWorldModSystem` 改为 `BossDownedModSystem`，它会被当作一个新东西，从玩家的角度来看，就好像数据被重置了一样。为了让它继承旧数据，你需要在该类上方添加 `[LegacyName("OldNameOfClass")]` 属性。对于像 `ModItem` 这样的东西，这样做的好处是不会将旧物品变成"已卸载物品"。这是一个例子：

```cs
[LegacyName("MyWorldModSystem")]
internal class BossDownedModSystem : ModSystem
{
//...
```

## 初始化
确保在适当的方法、构造函数或字段初始值设定项中初始化值。这包括创建数据结构并在适当时用默认值填充它们。这是因为如果之前没有为此实体保存过 `TagCompound`，则不会调用 `LoadData`。例如，确保在 `ModSystem.ClearWorld` 中重置 `ModSystem` 值，如果你不这样做，数据会跨世界交叉，因为玩家进出世界时。同样，确保在 `ModPlayer.Initialize` 中对 `ModPlayer` 数据执行相同的操作。这是一个例子：

```cs
internal class MyWorldModSystem : ModSystem
{
	public static bool MySpecialBool;

	public override void ClearWorld()
	{
		MySpecialBool = false;
	}

	public override void LoadWorldData(TagCompound tag)
	{
		MySpecialBool = tag.GetBool("MySpecialBool");
	}

	public override void SaveWorldData(TagCompound tag)
	{
		tag["MySpecialBool"] = MySpecialBool;
	}
}
```

在上面的例子中，我们确保在 `ModSystem.ClearWorld` 中将 `MySpecialBool` 设置为 false。如果我们忘记这样做，可能会发生以下情况：玩家进入世界A，`MySpecialBool` 由于某些事件（例如击败Boss）被设置为 true，玩家退出世界A并进入世界B，世界B还没有 `TagCompound` 数据，所以不调用 `LoadWorldData`，`MySpecialBool` 仍然是 true，尽管 `MySpecialBool` 所代表的事情从未在世界B中发生过。请记住，总是要在适当的方法、构造函数或字段初始值设定项中将值设置为默认值。

# 示例
以下是 ExampleMod 和其他模组中的各种示例，按复杂度排序：
* [ExampleStatIncreasePlayer](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Common/Players/ExampleStatIncreasePlayer.cs#L53) - ModPlayer - 简单示例，1个表示生命提升物品消耗的数字和1个表示魔法Boss物品消耗的数字
* [DownedBossSystem](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Common/Systems/DownedBossSystem.cs#L25) - ModSystem - 保存布尔值，指示ExampleMod中的哪些Boss已在世界中被打败
* [ExamplePerson](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/NPCs/ExamplePerson.cs#L382) - ModNPC - 保存一个 int 来表示这个城镇NPC的对话活动
* [ExampleInstancedItem](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/ExampleInstancedItem.cs#L62) - ModItem - 保存一组与物品特定实例相关的 Color
* [ExampleCanStackItem](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Consumables/ExampleCanStackItem.cs#L73) - ModItem 保存一个字符串
* [ExampleGlobalNPC](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/NPCs/ExampleGlobalNPC.cs#L41) - GlobalNPC - 保存一个布尔值（可选，出于效率考虑）
* [DisableCorruptionSpreadModWorld](https://github.com/JavidPack/DisableCorruptionSpread/blob/1.4/DisableCorruptionSpreadModWorld.cs#L193) - ModSystem - 展示 `nameof` 和 `tag.ContainsKey` 的用法
* [AutoTrashPlayer](https://github.com/JavidPack/AutoTrash/blob/1.4/AutoTrashPlayer.cs#L31) - ModPlayer - 保存和加载 `Item` 列表
* [BossChecklist](https://github.com/JavidPack/BossChecklist/blob/1.4.4/DataManager.cs) - TagSerializable - 展示实现TagSerializable的几个例子，包括嵌套
* [Item](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/Item.TML.cs)（和 [ItemIO](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ModLoader/IO/ItemIO.cs) - TagSerializable - 展示实现TagSerializable
* [TagSerializer](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ModLoader/IO/TagSerializer.cs#L186) - TagSerializer - 展示继承自TagSerializer以促进序列化现有类

# 简单示例

让我们从 ExampleLifeFruitPlayer 开始：

```cs
public override void SaveData(TagCompound tag) {
	tag["exampleLifeFruits"] = exampleLifeFruits;
}

public override void LoadData(TagCompound tag) {
	exampleLifeFruits = tag.GetInt("exampleLifeFruits");
}
```

这里我们看到了保存和加载 int 变量的一个非常简单的例子。在 `SaveData` 中，我们向提供的 `TagCompound` 添加数据。在 `LoadData` 中，我们得到一个名为 tag 的 `TagCompound` 并从中检索值。我们必须使用与存储数据类型匹配的适当 Load 方法。

# 列表示例
模组开发者使用 `TagCompound` 时的一个常见错误是将数据列表作为 `TagCompound` 中的单独条目保存。例如，以下是一个不好的方法：

```cs
for (int i = 0; i < stats.Count; i++)
{
	tag.Add("stats_" + i, stats[i]); // 错误示例，请勿使用！
}
```

像这样保存单个条目不是我们使用 `TagCompound` 的方式。以下是这种方法在 NBTExplorer 中的样子：
![](https://i.imgur.com/puTAMHM.png)

`TagCompound` 支持兼容数据类型的列表，以下是正确的方法：

```cs
// SaveData
tag.Add("stats", stats);

// LoadData
stats = tag.GetList<int>("stats");
// 或者
stats = tag.Get<List<int>>("stats");
```
![](https://i.imgur.com/dBLlbVu.png)

注意：如果某个条目缺失，`GetList<>` 或 `Get<List<>>` 将返回空列表而不是 null。

# 字典示例
保存和加载字典是可以的，但需要一点努力。一种方法是将键和值保存为列表，然后使用 `Zip` 方法重建 `Dictionary`：

```cs
// 这个代码可以在 ExampleMod 的 TEScoreBoard 中找到：https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Old/Tiles/TEScoreBoard.cs
// 注意：TEScoreBoard 尚未更新到1.4.4，链接的示例是1.3代码。
using System.Linq;

internal Dictionary<string, int> scores = new Dictionary<string, int>();

public override void SaveData(TagCompound tag)
{
	tag["scoreNames"] = scores.Keys.ToList();
	tag["scoreValues"] = scores.Values.ToList();
}

public override void LoadData(TagCompound tag)
{
	var names = tag.Get<List<string>>("scoreNames");
	var values = tag.Get<List<int>>("scoreValues");
	scores = names.Zip(values, (k, v) => new { Key = k, Value = v }).ToDictionary(x => x.Key, x => x.Value);
}
```

如果你专门保存一个以 `string` 为键的 `Dictionary`，还有另一种方法。使用以 `string` 为键的 `Dictionary` 是非常典型的，所以这个方法值得学习。这种方法利用了 `TagCompound` 实际上在内部充当以 `string` 为键的 `Dictionary` 的事实，所以我们可以直接将键和值保存到 `TagCompound` 中，而不是之后从两个单独的列表重建 `Dictionary`。
```cs
internal Dictionary<string, int> scoresAlternate = new Dictionary<string, int>();

public override void SaveData(TagCompound tag) {
	TagCompound scoresAlternateTag = new TagCompound();
	foreach (var scoreAlternate in scoresAlternate) {
		scoresAlternateTag[scoreAlternate.Key] = scoreAlternate.Value;
	}
	tag["scoresAlternate"] = scoresAlternateTag;
}

public override void LoadData(TagCompound tag) {
	foreach (var item in tag.GetCompound("scoresAlternate")) {
		scoresAlternate[item.Key] = (int)item.Value;
	}
}

```
两种方法都可行，但结果数据存储方式不同。如果按 `string` 编制索引，使用替代方法可能更有用，这样如果出现问题，用户更容易修改他们的存档。这样存储也更容易阅读和调试问题。下面的屏幕截图中蓝色高亮部分对应替代方法，而黄色部分对应保存2个单独列表的方法。

![NBTExplorer_2024-04-04_11-58-07](https://github.com/tModLoader/tModLoader/assets/4522492/7bc48c73-162a-4972-9720-22cd3e33501b)

这是一个更复杂的 `Dictionary<ulong, Tuple<string, int>>` 示例。使用 `Zip` 来做这件事会有点困难，所以我们在这里采用另一种方法。我们不是将键和值分别存储为列表，而是在列表中构造一个 `TagCompound` 列表，该列表中的每个 `TagCompound` 代表 `Dictionary` 中的一个条目：

```cs
public Dictionary<ulong, Tuple<string, int>> complexDictionary;

public override void Initialize()
{
	complexDictionary = new Dictionary<ulong, Tuple<string, int>>();
}

public override void SaveData(TagCompound tag)
{
	var list = new List<TagCompound>();
	foreach (var item in complexDictionary)
	{
		list.Add(new TagCompound() {
			{ "pid", item.Key },
			{ "name", item.Value.Item1 },
			{ "deaths", item.Value.Item2 },
		});
	}
	tag["complexDictionary"] = list;
}

public override void LoadData(TagCompound tag)
{
	var list = tag.GetList<TagCompound>("complexDictionary");
	foreach (var item in list)
	{
		ulong pid = item.Get<ulong>("pid");
		string name = item.GetString("name");
		int deaths = item.GetInt("deaths");
		complexDictionary[pid] = new Tuple<string, int>(name, deaths);
	}
}
```

![](https://i.imgur.com/Ecu3gc3.png)

# 嵌套示例

**TODO：完成本节示例。**

# 物品示例
模组开发者的一个常见任务是保存一个 `Item`。不要试图通过保存 itemid 或 item name，然后在 `LoadData` 期间尝试恢复它来破解你自己的方法。`Item` 类是 `TagCompound` 原生支持的。卸载的物品将按预期通过模组卸载和加载来保留。

请注意，不支持在 `Item` 的列表或数组中保存 `null` 条目。每个条目必须是一个实例化的 `Item`。缺失的标签也将作为 `new Item()` 加载。

```
public Item SingleItem = new Item();
public List<Item> ItemsToSave = new List<Item>();

public override void SaveData(TagCompound tag) {
	tag[nameof(SingleItem)] = SingleItem;
	tag[nameof(ItemsToSave)] = ItemsToSave;
}

public override void LoadData(TagCompound tag) {
	SingleItem = tag.Get<Item>(nameof(SingleItem));
	ItemsToSave = tag.Get<List<Item>>(nameof(ItemsToSave));
}
```

如果你只需要保存物品的身份，最好（更高效且更容易使用）保存和加载一个 `ItemDefinition` 而不是。`ItemDefinition` 是一个特殊的类，可用于安全地存储和恢复物品类型。不要直接存储 `Item.type` 值，如果模组或模组列表被更改或更新，模组物品类型可能会改变，这将不起作用。`ItemDefinition` 也可以在模组卸载时保留物品身份。

# 自定义TagCompound示例

**TODO：完成本节示例。**

# TagSerializer和TagSerializable示例
创建继承自 `TagSerializer` 的类或实现 `TagSerializable` 接口的类是简化将自定义数据保存和加载到 `TagCompound` 的另一种方法。我们可以为不属于我们模组控制的类创建 `TagSerializer`，而对于在我们模组中定义的类，实现 `TagSerializable` 更好。

## TagSerializer示例
我们为不在我们模组控制下的类创建 `TagSerializer`。添加 `TagSerializer` 允许我们在 `TagCompound` 中直接保存和加载类。这是一个例子：

```cs
public class RectangleSerializer : TagSerializer<Rectangle, TagCompound>
{
	public override TagCompound Serialize(Rectangle value) => new TagCompound
	{
		["x"] = value.X,
		["y"] = value.Y,
		["width"] = value.Width,
		["height"] = value.Height
	};

	public override Rectangle Deserialize(TagCompound tag) => new Rectangle(tag.GetInt("x"), tag.GetInt("y"), tag.GetInt("width"), tag.GetInt("height"));
}
```

我们现在可以像使用原生支持的数据类型一样自由使用 `Rectangle`：
```cs
public override void SaveData(TagCompound tag)
{
	tag["rectangle"] = rectangle;
	tag["rectangles"] = rectangles;
}

public override void LoadData(TagCompound tag)
{
	rectangle = tag.Get<Rectangle>("rectangle");
	rectangles = (List<Rectangle>)tag.GetList<Rectangle>("rectangles");
}
```
![](https://i.imgur.com/dBpWSf0.png)

请注意，`RectangleSerializer` 已经在 tModLoader 中原生实现，任何为 `Rectangle` 注册 `TagSerializer` 的尝试都将导致你的模组无法加载。其他一些常见类已经有了为他们制作的 `TagSerializer`，如 `Vector2`、`Vector3`、`Color`、`Point16` 和 `Rectangle`。由于为同一类创建多个 `TagSerializer` 会导致错误，如果你为一个可能对其他模组开发者有用的类实现 `TagSerializer`，请联系我们，我们可以将它直接添加到下一个 tModLoader 版本中。

## TagSerializable示例
如果一个类是在你的模组中定义的，最好直接在类上实现 `TagSerializable`。为此，请在类后面添加 ` : TagSerializable`，并添加 `public static readonly Func<TagCompound, ClassName> DESERIALIZER = LoadMethodNameHere;`。下面是利用 `TagSerializable` 的完整示例，代码清晰简洁。这个例子还展示了保存 `List` 以及保存包含其他对象的对象如何轻松完成。示例还在 `NestedData` 类中展示了紧凑语法以及 `nameof` 操作符的使用（[见下文](#nameof操作符)）。你还会注意到 `MyData` 包含一个 `Rectangle`，展示了该类的已实现 `TagSerializer` 如何被自动利用。简而言之，这个例子涵盖了很多本指南的内容。

```cs
using Microsoft.Xna.Framework;
using System;
using System.Collections.Generic;
using Terraria.ModLoader;
using Terraria.ModLoader.IO;

namespace ExampleMod
{
	class MyModPlayer : ModPlayer
	{
		List<MyData> listOfMyData;
		MyData specialMyData;

		public override void Initialize()
		{
			listOfMyData = new List<MyData>();
			specialMyData = new MyData();

			// 测试数据。实际上你会在模组中某个适当的地方更改这个数据。
			/*
			specialMyData.number = 1;
			specialMyData.rectangle = new Rectangle(1, 2, 3, 4);

			var listItem = new MyData();
			listItem.nested = new NestedData();
			listItem.nested.A = 55;
			listItem.nested.B = 66;
			listItem.number = 77;
			listOfMyData.Add(listItem);
			*/
		}

		public override void SaveData(TagCompound tag)
		{
			tag[nameof(listOfMyData)] = listOfMyData;
			tag[nameof(specialMyData)] = specialMyData;
		}

		public override void LoadData(TagCompound tag)
		{
			listOfMyData = tag.Get<List<MyData>>(nameof(listOfMyData));
			specialMyData = tag.Get<MyData>(nameof(specialMyData));
		}
	}

	class MyData : TagSerializable
	{
		public static readonly Func<TagCompound, MyData> DESERIALIZER = Load;

		public int number;
		public Rectangle rectangle;
		public NestedData nested = new NestedData();

		public TagCompound SerializeData()
		{
			return new TagCompound
			{
				["number"] = number,
				["rectangle"] = rectangle,
				["nested"] = nested,
			};
		}

		public static MyData Load(TagCompound tag)
		{
			var myData = new MyData();
			myData.number = tag.GetInt("number");
			myData.rectangle = tag.Get<Rectangle>("rectangle");
			myData.nested = tag.Get<NestedData>("nested");
			return myData;
		}
	}

	class NestedData : TagSerializable
	{
		public static readonly Func<TagCompound, NestedData> DESERIALIZER = Load;
		public int A;
		public int B;
		public TagCompound SerializeData() => new TagCompound { [nameof(A)] = A, [nameof(B)] = B };
		// 对象初始化语法
		public static NestedData Load(TagCompound tag) => new NestedData() { A = tag.GetInt(nameof(A)), B = tag.GetInt(nameof(B)) };
	}
}
```
这是为此示例保存的数据：
![](https://i.imgur.com/ZcUDRuO.png)

# 其他主题
## GlobalItem优化
在 `GlobalItem` 中保存数据会迅速增加玩家和世界存档的文件大小。确保只在有非默认数据要保存时才向 `TagCompound` 赋值。例如，如果你要追踪一个物品被重铸的次数，如果该物品的重铸次数为0，就根本不要保存该值。

## nameof操作符
`nameof` 操作符可以简化一些事情，但要注意。（参见 [nameof文档](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/nameof)）`nameof` 操作符可以帮助避免在 `LoadData` 和 `SaveData` 之间（或在 `ModSystem` 类中的 `LoadWorldData` 和 `SaveWorldData` 之间）出现拼写错误。但是，要注意在模组中重命名变量时（Visual Studio中的F2命令）。例如，如果你重命名了一个使用 `nameof` 保存的 `ModPlayer` 变量，你的模组将在用户更新模组时丢失数据。见下面的例子。请注意，我们不需要写 `"CorruptionSpreadDisabled"` 来指定键，这有拼写错误的风险，而且很难发现。

```cs
public override void LoadWorldData(TagCompound tag)
{
	CorruptionSpreadDisabled = tag.GetBool(nameof(CorruptionSpreadDisabled));
}

public override void SaveWorldData(TagCompound tag)
{
	tag[nameof(CorruptionSpreadDisabled)] = CorruptionSpreadDisabled;
}
```
