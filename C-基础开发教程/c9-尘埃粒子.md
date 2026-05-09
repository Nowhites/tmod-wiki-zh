# 基础灰尘
灰尘是我们给那些为武器和其他东西添加视觉元素的小颗粒的名称。灰尘完全是视觉的，不应作为游戏元素使用。因为Terraria生成的灰尘量取决于计算机的性能。

每种灰尘都有一个图像和一个关联的行为。例如，一些灰尘受重力影响，一些则不受。一些灰尘像被风中飘雪一样随机移动，而其他则直线移动。这些行为都是编程到灰尘中的。

## 使用灰尘

要使用灰尘，只需调用灰尘生成方法之一。这些方法如下所列。

```c#
public static int NewDust(Vector2 Position, int Width, int Height, int Type, float SpeedX = 0f, float SpeedY = 0f, int Alpha = 0, Color newColor = default(Color), float Scale = 1f);
public static Dust NewDustDirect(Vector2 Position, int Width, int Height, int Type, float SpeedX = 0f, float SpeedY = 0f, int Alpha = 0, Color newColor = default(Color), float Scale = 1f);
public static Dust NewDustPerfect(Vector2 Position, int Type, Vector2? Velocity = null, int Alpha = 0, Color newColor = default(Color), float Scale = 1f);
```
`NewDust`最常用，但`NewDustPerfect`放弃了生成位置的随机化，可能很有用。另请注意，`NewDust`返回`Main.dust`数组中灰尘的索引，而其他2个直接返回` Dust`实例。

以下是一些示例：
```c#
// 在npc方法中生成模组灰尘
Dust.NewDust(npc.position, npc.width, npc.height, ModContent.DustType<Sparkle>());

// 在npc方法中生成原版灰尘，使用DustID
Dust.NewDust(npc.position, npc.width, npc.height, DustID.Granite);

// 在npc方法中生成原版灰尘，使用DustID数字（可读性较差）
Dust.NewDust(npc.position, npc.width, npc.height, 240);

// 在ModProjectile.AI中生成73号灰尘（DustID.PinkFairy），但只有1/6的时间（所以不那么频繁）。同时缩小速度。
if (Main.rand.Next(6) == 0)
{
	int dustnumber = Dust.NewDust(projectile.position, projectile.width, projectile.height, DustID.PinkFairy, 0f, 0f, 200, default(Color), 0.8f);
	Main.dust[dustnumber].velocity *= 0.3f;
}

// 使用for循环一次生成大量灰尘。这是魔法镜风格的灰尘生成。
for (int d = 0; d < 70; d++)
{
	Dust.NewDust(player.position, player.width, player.height, DustID.MagicMirror, 0f, 0f, 150, default(Color), 1.5f);
}
```

一些注意事项：`Position`、`Width`和`Height`定义了一个矩形，灰尘将从这里随机生成。生成原版灰尘和模组灰尘的唯一区别是替换第4个参数，这通常只是一个数字或`DustID`条目，用`ModContent.DustType<DustName>()`替换。如果需要，您可以省略可选参数。

## 特殊图案
使用您的[几何](https://github.com/tModLoader/tModLoader/wiki/Geometry)知识在同一次游戏更新中或跨越多个游戏更新生成多个灰尘，以使用灰尘制作特殊图案。

## 查找原版灰尘

要查找原版灰尘，请参阅下图。注意它是从左到右阅读，从0开始。每行有100种灰尘，每行实际上是相同灰尘的3帧，意味着总共有3行。

![](https://i.imgur.com/s4usYv4.png)

此图表应该有助于解释如何阅读图像。

![](https://i.imgur.com/n4iScrV.png)

也可以在[原版Terraria Wiki](https://terraria.wiki.gg/wiki/Dust_IDs)上找到所有灰尘的列表。一些原版灰尘没有官方名称，因此tModLoader为它们添加了名称。如果内部名称未在官方wiki上列出，请参阅[DustID.TML.cs](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ID/DustID.TML.cs)查找缺失灰尘的名称。

但是，尝试灰尘最简单的方法是下载[Modders Toolkit](https://forums.terraria.org/index.php?threads/modders-toolkit-a-mod-for-modders-doing-modding.55738/)并使用灰尘生成工具找到您需要的灰尘。

灰尘工具视频：

https://github.com/tModLoader/tModLoader/assets/4522492/0d42395f-3b38-43c6-9693-c096ce71ac7c

## ModDust

虽然原版灰尘都是10x30像素的图像，包含3帧动画，但`ModDust`可以是任何尺寸和任何帧数。请参阅ExampleMod了解许多不同的灰尘。