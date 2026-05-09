# 基础瓦片实体
本指南旨在解释所有瓦片实体共有的常见内容。
本指南假设您知道如何创建文件、创建类，以及理解一个类"继承"另一个类意味着什么。
[ExampleMod中的BasicTileEntity.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/TileEntities/BasicTileEntity.cs)文件展示了一个基础的瓦片实体。它可以与本指南一起作为所教概念的工作示例来学习。

# 什么是瓦片实体？
重要的是要认识到瓦片（Tile）和瓦片实体（Tile Entity）之间有区别。瓦片位于世界内的固定位置，必须通过`Main.tile`变量访问，它们不能包含额外数据，也不能在游戏更新期间运行代码。瓦片实体是通常附加到`Tile`上的额外数据，它们具有`Update`方法，它们通常与瓦片协同工作以使瓦片动态行为。瓦片实体可以在每个游戏更新时运行代码，而普通瓦片只有在世界更新游戏循环调用`RandomUpdate`时才有机会运行代码。

基本上，如果一个瓦片需要具有额外数据或可靠地运行代码，它需要有一个绑定到它的相应瓦片实体并与之协同工作。

# 制作瓦片实体
制作瓦片实体有三个组件。`ModItem`放置一个`ModTile`。那个`ModTile`在放置时附加一个`ModTileEntity`到自身。这三个类共同构成一个可工作的瓦片实体。

## 制作物品
首先，您需要一个放置`ModTile`的`ModItem`。
需要注意的行是在`ModItem.SetDefaults`中设置`Item.createTile`的那一行。请参阅[ExampleMod的可放置物品](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Content/Items/Placeable)。

## 制作瓦片实体
设置`ModTileEntity`需要使用`ModTileEntity`类中的一些钩子。

### IsTileValidForEntity(int x, int y)
此钩子在世界加载期间和放置服务器上的实体时运行。它用于在钩子返回`false`时自动销毁瓦片实体，表示它在无效位置。以下是`IsTileValidForEntity(int x, int y)`的标准用法：
```cs
public override bool IsTileValidForEntity(int x, int y)
{
    Tile tile = Main.tile[x, y];
    //MyTile类在后面展示
    return tile.HasTile && tile.TileType == ModContent.TileType<MyTile>();
}
```

### LoadData/SaveData/NetSend/NetReceive
虽然从技术上讲，制作可工作的瓦片实体不需要这些，但瓦片实体几乎总是需要存储和同步自定义数据，因为这是它们的主要目的之一。[保存和加载数据](https://github.com/tModLoader/tModLoader/wiki/Saving-and-loading-using-TagCompound)以及[通过网络同步数据](https://github.com/tModLoader/tModLoader/wiki/Basic-Netcode#modtileentity)的基本概念在其他指南中有讲解。如果您还不熟悉这些概念，请查阅它们。通常，值得保存和加载的任何内容也值得同步。未能正确同步数据将导致客户端在交互瓦片实体时看不到正确的值。

[BasicTileEntity](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/TileEntities/BasicTileEntity.cs#L58)示例是一个简单的示例，展示了存储和同步单个`int`值。

重写这些方法，但暂时保持不变。

### Update
`Update`是可以在每帧执行瓦片实体逻辑的地方。这也不是技术上必需的，但一旦您向`ModTileEntity`添加功能，您最终将使用它，所以也重写它，但暂时保持不变。

`ModTileEntity`中存在的其他钩子是可选的，本指南不会涉及。

## 制作瓦片
在制作了一个普通的`frameImportant`多重瓦片（有关更多信息，请参阅[基础ModTile指南](https://github.com/tModLoader/tModLoader/wiki/Basic-Tile)）后，您只需要添加几行代码即可使其自动放置瓦片实体。

### SetDefaults() / SetStaticDefaults()
在`TileObjectData.addTile(Type);`行之前，添加以下内容：
```cs
// MyTileEntity指的是上一节提到的瓦片实体
TileObjectData.newTile.HookPostPlaceMyPlayer = ModContent.GetInstance<MyTileEntity>().Generic_HookPostPlaceMyPlayer;

// 这是实际调用钩子所必需的。
TileObjectData.newTile.UsesCustomCanPlace = true; // 如果使用CopyFrom克隆现有TileObjectData模板，这将已经被设置。
```

### KillMultiTile(int i, int j, int frameX, int frameY)
在此钩子中添加以下行：
```cs
// ModTileEntity.Kill()负责检查瓦片实体是否存在，如果它存在于世界中则销毁它
// 瓦片坐标参数已经指的是多重瓦片的左上角
ModContent.GetInstance<MyTileEntity>().Kill(i, j);
```

## 基础模板
这是所需的`ModItem`、`ModTileEntity`和`ModTile`的最小示例。
```cs
public class MyItem : ModItem
{
	public override void SetDefaults() {
		Item.DefaultToPlaceableTile(ModContent.TileType<MyTile>());
	}

	public override void AddRecipes() {
		CreateRecipe().AddIngredient(ItemID.Wood).Register();
	}
}

public class MyTileEntity : ModTileEntity
{
	public override bool IsTileValidForEntity(int x, int y) {
		Tile tile = Main.tile[x, y];
		return tile.HasTile && tile.TileType == ModContent.TileType<MyTile>();
	}

	public override void SaveData(TagCompound tag) {
	}

	public override void LoadData(TagCompound tag) {
	}

	public override void NetSend(BinaryWriter writer) {
	}

	public override void NetReceive(BinaryReader reader) {
	}

	public override void Update() {
	}
}

public class MyTile : ModTile
{
	public override void SetStaticDefaults() {
		Main.tileFrameImportant[Type] = true;

		TileObjectData.newTile.CopyFrom(TileObjectData.Style2x2);
		TileObjectData.newTile.CoordinateHeights = [16, 18];
		TileObjectData.newTile.StyleHorizontal = true;

		// 这是重要的行！
		TileObjectData.newTile.HookPostPlaceMyPlayer = ModContent.GetInstance<MyTileEntity>().Generic_HookPostPlaceMyPlayer; 

		TileObjectData.addTile(Type);
	}

	public override void KillMultiTile(int i, int j, int frameX, int frameY) {
		ModContent.GetInstance<MyTileEntity>().Kill(i, j);
	}
}
```

# 其他
## 从ModTile访问ModTileEntity
玩家将通过与`ModTile`交互来与`ModTileEntity`交互。`TileEntity.TryGet`将检索存在于提供坐标处的`ModTileEntity`。您将希望在`ModTile.RightClick`、`ModTile.SetDrawPositions`、`ModTile.MouseOver`等方法中使用`TileEntity.TryGet`。

通过访问瓦片实体实例，您可以打开UI、生成物品、显示消息、显示工具提示、自定义瓦片视觉效果等等。[BasicTileEntityTile](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/TileEntities/BasicTileEntity.cs#L96)方法展示了几种正确使用`TryGet`的示例。

这是一个基本示例，注意`TryGet`如何在`if`语句中使用。极少数情况下`ModTileEntity`可能不存在，所以一定要写这样的防御性代码来考虑这种情况：

```cs
// 这个钩子放在你的ModTile中
public override bool RightClick(int i, int j)
{
	if (TileEntity.TryGet(i, j, out MyTileEntity entity))
	{
		// 在这里对你的实体做事情
	}
}
```

## 可视化瓦片实体
瓦片实体是不可见的，它们通常只有在所属的`ModTile`使用它们进行自定义视觉效果时才具有视觉效果。这在模组制作时可能使验证`ModTileEntity`的存在变得困难。

模组开发者可以添加`Dust.QuickDust(Position.X, Position.Y, Color.Red);`到`ModTileEntity.Update`作为一种快速验证`ModTileEntity`存在的方法：

![dotnet_2025-05-19_14-37-07](https://github.com/user-attachments/assets/2f883cef-f818-493f-a549-a6e7eb122eb5)    

另一个选择是像[Modders Toolkit](https://steamcommunity.com/sharedfiles/filedetails/?id=2573569299)这样的模组，它提供了一个切换开关来可视化所有瓦片实体位置：

![image](https://github.com/user-attachments/assets/9e3012a0-b9d9-4f84-835d-d8e87fde42f3)    



# 示例
- [BasicTileEntity.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/TileEntities/BasicTileEntity.cs)是瓦片实体的主要示例。它展示了大 部分所需功能，同时足够简单以便阅读和理解。
- [SimplePylonTileEntity](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/TileEntities/SimplePylonTileEntity.cs)和[AdvancedPylonTileEntity](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/TileEntities/AdvancedPylonTileEntity.cs)是`TEModdedPylon`类。`TEModdedPylon`类本身继承自`ModTileEntity`并自行实现所需的逻辑。虽然作为学习`ModTileEntity`钩子的示例不太有用，但重要的是要认识到水晶球是瓦片实体。
