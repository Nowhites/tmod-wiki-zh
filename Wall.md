# 什么是墙？
墙相当简单，创建`ModWall`主要是一个遵循现有模式的过程。像瓷砖一样，墙由2部分组成：放置墙的`ModItem`和`ModWall`本身。

# 墙-物品配对
当`Item.createWall`被设置为`ModWall`的`WallType`时，`Item`将在特定`Wall`上放置一个地方。`ModWall`通常也会返回`ModItem`。这个过程对于由放置它的相同物品返回的墙是自动的，否则使用`ModWall.SetStaticDefaults`中的`RegisterItemDrop(ModContent.ItemType<ItemName>());`手动指定物品掉落。

# 基础示例
`ExampleMod`中的`ExampleWall`是一个基础示例。它有4个文件：
* [ExampleMod/Content/Walls/ExampleWall.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Walls/ExampleWall.cs)是实际的`ModWall`类
* [ExampleMod/Content/Walls/ExampleWall.png](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Walls/ExampleWall.png)是`ModWall`的纹理
* [ExampleMod/Content/Items/Placeable/ExampleWall.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Placeable/ExampleWall.cs)是放置`ExampleWall` `ModWall`的相应`ModItem`类
  * 注意：`ModWall.SetStaticDefaults`中的代码`Item.DefaultToPlacableWall(ModContent.WallType<Walls.ExampleWall>());`自动设置`Item.createWall`以及墙物品常见的所有其他属性
* [ExampleMod/Content/Items/Placeable/ExampleWall.png](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Placeable/ExampleWall.png)是`ExampleWall` `ModItem`的纹理

`ExampleMod`中的`ExampleWallUnsafe`也是一个基础示例，但只有2个文件，`ModWall`类和相应的纹理。`ExampleWallUnsafe`没有一个放置它的物品，所以使用`RegisterItemDrop`来注册物品掉落。

# 高级示例
请参阅[ExampleMod/Content/Walls/ExampleWallAdvanced.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Walls/ExampleWallAdvanced.cs)获取展示动画、自定义框架和照明的的高级示例。

# 纹理
墙的纹理有几个部分，用于根据邻居是否是相同墙类型在不同的位置绘制墙。注意每个方向有3个选项。中心方向是特殊的，实际上有15个选项，或5个部分的3个选项，允许更多变化以减少重复模式。这些部分被称为"style"，选项被称为"WallFrameNumber"。`ExampleWall`基于`Gemspark`墙，因为它很容易理解。请查阅其他[现有墙纹理](https://github.com/tModLoader/tModLoader/wiki/Intermediate-Prerequisites#vanilla-texture-file-reference)获取更详细的示例。
![](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Walls/ExampleWall.png)

如果使用了`Main.wallLargeFrames`，每个`style`会增加第4个`WallFrameNumber`选项以增加更多变化。那个模板如下所示：
![Wall_179](https://user-images.githubusercontent.com/4522492/191140372-60a897bf-f69c-4615-bab5-57cefc84066c.png)

这个模板展示了`Main.wallLargeFrames`值1（"Phlebas"）和2（"Lazure"）如何产生确定性模式而不是随机模式。
![image](https://user-images.githubusercontent.com/4522492/193192050-91e6502d-8f39-4c28-8221-297562ca0f51.png)

## 动画
如果墙是动画的，模式会重复。这仅适用于非`Main.wallLargeFrames`墙。
![Wall_225](https://user-images.githubusercontent.com/4522492/191140761-06536d43-3b87-4ffc-b036-d3b9cf1739f0.png)

# 墙属性
在`ModWall.SetStaticDefaults`中，可以分配各种数据来影响墙的行为。
### Main.wallHouse
如果为true，墙被认为是安全墙，用于各种事情。
### Main.wallDungeon
如果为true，墙被认为是地牢墙。
### Main.wallLight
如果为true，光从背景流入，如栅栏和玻璃。
### Main.wallLargeFrames
如果为1，使用"Phlebas"模式（镀板型墙）。如果为2，使用"Lazure"模式。这两者都使用第4个`WallFrameNumber`选项。
### Main.wallBlend
分配一个已知的`WallID`来与类似墙混合。例如，在一个墙的不安全变体中，你会写`Main.wallBlend[Type] = ModContent.WallType<MyWallSafe>();`。你不应该在安全变体上做相反的事情，只在一个墙上做。

# 动画
如上所示，动画墙的纹理有模板的多个副本。墙将根据`ModWall.AnimateWall`钩子中提供的时间和模式进行动画。

## 示例
这个示例循环播放7帧动画，每10帧切换一次帧。可以创建循环或更高级的模式。
```cs
public override void AnimateWall(ref byte frame, ref byte frameCounter) {
	frameCounter++;
	if (frameCounter >= 10) {
		frameCounter = 0;
		frame++;
		if (frame >= 7)
			frame = 0;
	}
	// 或者，更简洁地说：
	if (++frameCounter >= 10) {
		frameCounter = 0;
		frame = (byte)(++frame % 7);
	}
}
```

# 安全 vs 不安全
许多泰拉瑞亚墙有安全和不安全变体。安全墙变体是当玩家挖掘不安全墙时收到的物品放置的墙。不安全墙在世界生成期间放置，通常玩家不能放置。的不安全墙用于NPC生成计算。这种分离让玩家挖掘墙并使用它们进行装饰，而不会冒着特别是在他们的创作中生成各种敌人的风险。作为模组开发者，如果NPC生成逻辑考虑墙类型，这是个好模式。

# 自定义框架
使用`ModTile.WallFrame`，模组开发者可以实现任何他们想要的框架逻辑。这个来自[ExampleWallAdvanced](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Walls/ExampleWallAdvanced.cs#L48)的例子展示了比另外2个选项更权重第1个选项：
![image](https://user-images.githubusercontent.com/4522492/191853500-0208243b-68cd-4302-8984-79d5c2f81468.png)

```cs
public override bool WallFrame(int i, int j, bool randomizeFrame, ref int style, ref int frameNumber) {
	if (randomizeFrame) {
		// 这里我们使WallFrameNumber 0的机会非常罕见，只是为了视觉多样性：https://i.imgur.com/9Irak3p.png
		if (frameNumber == 0 && WorldGen.genRand.NextBool(3, 4)) {
			frameNumber = WorldGen.genRand.Next(1, 3);
		}
	}
	return base.WallFrame(i, j, randomizeFrame, ref style, ref frameNumber);
}
```
这里是这个例子和WallID.Cog的比较，它们有类似的纹理，注意WallFrameNumber 0是多么罕见：
![](https://i.imgur.com/9Irak3p.png)

## 不要用于游戏逻辑
确保不要将墙框架用于游戏逻辑，只用于视觉效果。墙框架不同步，每个玩家看到他们自己的墙框架模式。

# 相关参考
* [原版墙ID](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Content-IDs#wall-ids)
* [ModWall文档](https://docs.tmodloader.net/docs/stable/class_mod_wall.html)
* [ModBlockType文档](https://docs.tmodloader.net/docs/stable/class_mod_block_type.html) - 包含`ModTile`和`ModWall`都继承的其他方法和属性。
