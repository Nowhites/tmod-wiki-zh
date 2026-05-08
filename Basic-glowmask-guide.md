# 概述

本指南将介绍发光面具（Glowmask），包括它的定义以及如何应用它。

# 什么是"遮罩"，以及它在泰拉瑞亚中如何使用？

在学习发光面具之前，你需要先了解什么是"遮罩"。在艺术创作中，遮罩通常用于保护选定的区域在制作过程中不被改变，在泰拉瑞亚中我们的使用方式基本相同。通过在泰拉瑞亚中应用发光面具，我们并不是真正对原始精灵图进行遮罩（就像你在艺术创作中所做的那样），而是使用一个单独的精灵图来包含我们想要遮罩的像素。然后将这个精灵图绘制在原始精灵图的上方，它的颜色不受光线影响，从而产生发光效果。

# 泰拉瑞亚的发光面具长什么样？

以下是一个武器及其发光面具的示例：

| 普通精灵图 | 发光面具精灵图 |
|:-------------:|:-------------:|
| ![](https://i.imgur.com/8T2z1Qi.png) 	| ![](https://i.imgur.com/MxWHKGa.png) |

你可以看到，在发光面具中，我们只显示我们想要遮罩的像素，其他像素都被移除了。由于发光面具用于绘制不受光线影响的部分，所以在现实生活中通常是被照亮的部分（或者本身发光的部位）的像素会保留在发光面具精灵图中。

# 发光面具可以应用到哪些东西上？

发光面具可以应用到几乎任何你想要的东西上，无论是NPC、弹射物、物品，甚至是玩家穿戴的盔甲。对于装备物品（以及玩家手持的武器），你需要使用PlayerLayers，这超出了基础知识的范畴，因此不在本指南的讨论范围内。

# 我如何"应用"发光面具？

原版游戏有自己的发光面具"系统"，但是你**不能**使用这个系统，因为它不支持模组内容。此外，"应用"这个词应该宽松理解，因为你并不是真正将发光面具应用到原始精灵图上，而是独立于原始精灵图来绘制发光面具。

## 如何绘制发光面具？

要在原始精灵图上方绘制发光面具，你需要使用适当的 **Post**Draw 钩子。这可以确保发光面具绘制在原始精灵图的上方。你也可以选择完全手动绘制，重写相应的 **Pre**Draw 钩子，但这需要更多的手动工作，因此除非因为其他原因必须这样做，否则不建议使用。通常，精灵图命名约定是在名称后加上 _Glow 或 _Glowmask。以下是使用 PostDrawInWorld 为掉落在世界中的物品绘制发光面具的示例：

```csharp
public override void PostDrawInWorld(SpriteBatch spriteBatch, Color lightColor, Color alphaColor, float rotation, float  scale, int whoAmI) 	
{
	Texture2D texture = ModContent.Request<Texture2D>("YourModName/Items/MyItem_Glowmask", AssetRequestMode.ImmediateLoad).Value;
	spriteBatch.Draw
	(
		texture,
		new Vector2
		(
			item.position.X - Main.screenPosition.X + item.width * 0.5f,
			item.position.Y - Main.screenPosition.Y + item.height - texture.Height * 0.5f + 2f
		),
		new Rectangle(0, 0, texture.Width, texture.Height),
		Color.White,
		rotation,
		texture.Size() * 0.5f,
		scale, 
		SpriteEffects.None, 
		0f
	);
}
```

你不必_必须_使用这段代码，它只是一个示例。绘制发光面具时通常只需要遵循几个步骤：

- 确保你绘制的是发光面具精灵图，而不是原始精灵图
- 确保使用 Color.White 或至少是完全不透明的颜色进行绘制
  - 通过将 alpha 通道值增加到接近 255 来实现不透明（降低到接近 0 将实现透明）。请记住，XNA 的 Color 使用_字节_来表示 RGBA 通道，所以值范围在 0 到 255 之间
- 确保使用中心点作为原点（如果需要的话）
  - 你可以使用以下方式获取中心原点：`texture.Size() * 0.5f`
- 确保原始精灵图和发光面具精灵图具有相同的尺寸
- 最好缓存使用 ModContent.Request 请求的 Texture2D，例如提前请求并保存到变量中，然后使用该变量而不是每次都重新请求
