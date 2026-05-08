***
本指南已更新至1.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile/85802d2b7af91c5a3fd44e64a309b7cacbd171a2)
***

# 什么是弹幕？

在开始修改弹幕之前，您应该知道物品和弹幕之间的区别。物品是可以在物品栏中存储的对象，而弹幕则是从武器或敌人射出的对象等。

# 什么会使用弹幕？

Terraria中的许多物品通过弹幕实现其功能，包括枪械和弓（分别是子弹和箭矢）、激光、炸弹和其他投掷物品，以及大多数魔法武器。其他您可能想不到是弹幕的物品包括：抓钩、连枷、长矛、宠物、召唤物、钻头和悠悠球。许多敌人也会生成弹幕。

# 创建弹幕

要在Terraria中创建弹幕，您必须首先创建一个"继承"自`ModProjectile`的类。为此，请在模组的源目录（My Games\Terraria\tModLoader\ModSources\MyModName）中创建一个.cs文件，然后在文本编辑器中打开该文件。将以下内容粘贴到该文件中，将`NameHere`替换为您的物品内部名称，将`ModNamespaceHere`替换为您的模组文件夹名/命名空间。（一个常见错误是在内部名称中使用撇号或空格，不要这样做，计算机会无法理解。）

```c#
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;

namespace ModNamespaceHere
{
	public class NameHere : ModProjectile
	{
		
		public override void SetDefaults()
		{
			Projectile.arrow = true;
			Projectile.width = 10;
			Projectile.height = 10;
			Projectile.aiStyle = ProjAIStyleID.Arrow; // 或 1
			Projectile.friendly = true;
			Projectile.DamageType = DamageClass.Ranged;
			AIType = ProjectileID.WoodenArrowFriendly;
		}

		// 其他 hook/方法在这里。
	}
}
```
现在您有了一个.cs文件，将您的纹理文件（您制作的.png图像文件）放入包含此.cs文件的文件夹中。确保阅读[自动加载](https://github.com/tModLoader/tModLoader/wiki/Basic-Autoload)以了解如何满足计算机会的文件名和文件夹结构期望。

# 我找不到我的弹幕
请记住物品和弹幕是不同的。一个常见错误是模组制作者创建了弹幕但不理解他们需要创建使用该弹幕的东西。例如，对于飞刀武器，您需要同时创建物品和弹幕。弹药物品也需要一个与之关联的唯一弹幕。您并不总是需要物品和弹幕两者，例如如果弹幕是由NPC生成的。最简单的测试弹幕的方法是创建一个物品并将`Item.shoot`设置为该弹幕。例如，`Item.shoot = ModContent.ProjectileType<MyProjectile>();`。参见ExampleMod了解由物品生成的弹幕的许多示例，它们在不同的文件夹中，但很容易找到。

# SetDefaults
弹幕最重要的部分是`SetDefaults`方法。`SetDefaults`是您为弹幕设置值的地方，比如碰撞箱宽度和高度、弹幕是友方还是敌对、以及弹幕将使用哪种AI。查看[弹幕类文档](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation)了解通常在`SetDefaults`中设置的值意味着什么。您还可以通过访问[原版弹幕字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Projectile-Field-Values)查看原版弹幕的值。不同弹幕的许多示例可以在[ExampleMod.Content.Projectiles](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Content/Projectiles)中找到

## Projectile.damage
一个常见错误是在`SetDefaults`中设置`Projectile.damage`，这是不行的，因为弹幕拥有的伤害值总是在弹幕生成时传入`Projectile.NewProjectile`的值所覆盖。通常生成物品或NPC的物品会决定伤害。

## DrawOffsetX、DrawOriginOffsetY、DrawOriginOffsetX
这些是与正确将碰撞箱居中于精灵图相关的`ModProjectile`字段。阅读[绘制和碰撞](#绘制和碰撞)获取更多信息。

# 其他Hook/方法
[ModProjectile文档](https://docs.tmodloader.net/docs/stable/class_mod_projectile.html)列出了您可能需要使用的许多其他hook/方法来使您的弹幕独一无二。例如，如果您想在弹幕击中敌人时应用debuff，您可以使用`OnHitNPC`。要在弹幕击中瓷砖时执行某些操作，请使用`OnTileCollide`。查阅文档和ExampleMod中的用法以了解如何正确使用它们。

# 什么是AI
弹幕的AI是弹幕最重要的方面，它控制弹幕在生成后的移动和行为方式。对于新手模组制作者来说，最简单的方法是首先依赖其他原版弹幕已使用的AI代码，通过分配`Projectile.aiStyle = #;`和`AIType = ProjectileID.NameHere;`。您分配给`aiStyle`的数字必须与您用于`AIType`的弹幕使用的aiStyle数字相同。这被称为模仿原版弹幕。随着您对更高级移动方式的需求，您会意识到模仿原版弹幕AI非常有限。将在下面讨论模仿和自定义AI。

# 使用原版AI
我们可以使用原版AI来原型化我们的弹幕。让我们制作一个回旋镖。使用与像回旋镖一样移动的原版弹幕相同的`aiStyle`，我们可以制作一个回旋镖。您可以在[原版弹幕字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Projectile-Field-Values)中查找回旋镖弹幕，您会发现回旋镖都使用`aiStyle`为3：
![](https://i.imgur.com/RSaxV6T.png)

我们可以在代码中使用`Projectile.aiStyle = 3;`。（您可以将3改为`ProjAIStyleID.Boomerang`以使代码更具可读性。）为了使这个回旋镖更容易，我们可以使用`Projectile.CloneDefaults(ProjectileID.EnchantedBoomerang)`，这将复制所有其他默认值。这样做，您将获得一个与原版弹幕行为几乎相同的弹幕：
![](https://i.imgur.com/CL2MwaF.png)

您会注意到灰尘没有生成。我们可以通过使用`AIType`来修复这个问题。`AIType`用于进一步缩小`Projectile.aiStyle`。每个`aiStyle`在许多不同的弹幕之间共享。如果我们想使用特定类型弹幕的特定行为，我们需要设置`AIType`。这是我们的EnchantedBoomerang副本在分配`AIType`后的样子：
![](https://i.imgur.com/39KqXhc.png)

这是生成的代码。
```cs
public override void SetDefaults()
{
	Projectile.CloneDefaults(ProjectileID.EnchantedBoomerang);
	// projectile.aiStyle = 3; 这行不需要，因为CloneDefaults已经设置了。
	AIType= ProjectileID.EnchantedBoomerang;
}
```
那些灰尘很酷，但如果您想更改该灰尘的颜色或任何其他小东西，您不能依赖`aiStyle`和`AIType`。要更改内容，您需要查阅[原版代码适配](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption)指南来调整现有代码或继续阅读以了解如何从头编写AI代码。请记住，使用`projectile.aiStyle`和`AIType`是一个**原型工具**，模组中任何稍微有趣的内容都可能需要编写自己的AI代码或适配原版代码。

# 自定义AI
本节将讨论您可以融入AI的元素。请记住，如果您使用`Projectile.CloneDefaults`复制其他弹幕默认值，请将`Projectile.aiStyle`设置回0。所有自定义AI代码都放入`ModProjectile.AI`方法中。

## 计时器
许多弹幕使用计时器来延迟操作。通常我们使用`Projectile.ai[0]`或`Projectile.ai[1]`，因为这些值会自动同步，但我们也可以使用类字段。这里我们数到30，换句话说，半秒。

```cs
Projectile.ai[0] += 1f;
if (Projectile.ai[0] >= 30f)
{
	// 半秒已过。重置计时器等。
	Projectile.ai[0] = 0f;
	Projectile.netUpdate = true;
	// 在这里做些什么，也许切换到新状态。
}
```

## 重力
对于弹幕来说，重力实际上并不存在，每个受重力移动的弹幕只是在它们的AI中有代码。要实现重力，只需向`Projectile.velocity.Y`添加一个小值：
```cs
Projectile.velocity.Y = Projectile.velocity.Y + 0.1f; // 箭矢重力为0.1f，飞刀重力为0.4f
if (Projectile.velocity.Y > 16f) // 这个检查实现了"终端速度"。我们不希望弹幕继续越来越快。过去16f这个弹幕将穿过方块，所以这个检查很有用。
{
	Projectile.velocity.Y = 16f;
}
```

### 延迟重力
箭矢和飞刀弹幕都会等待几帧后才受重力影响：
```cs
Projectile.ai[0] += 1f; // 使用计时器等待15刻后再应用重力。
if (Projectile.ai[0] >= 15f)
{
	Projectile.ai[0] = 15f;
	Projectile.velocity.Y = Projectile.velocity.Y + 0.1f;
}
if (Projectile.velocity.Y > 16f)
{
	Projectile.velocity.Y = 16f;
}
```

## 风阻
通过减少`Projectile.velocity.X`的倍数，我们可以轻松实现风阻。结合计时器可以有条件地应用此效果。
```cs
Projectile.velocity.X = Projectile.velocity.X * 0.97f; // 滚动手雷速度减少为0.99f。尝试0.90f到0.99f之间的值
```

## 旋转
### 恒定旋转
我们可以在`AI`中增加`Projectile.rotation`来像回旋镖一样旋转。
```cs
Projectile.rotation += 0.4f * (float)Projectile.direction;
```

### 朝向移动方向
沿移动方向旋转通常用于像箭矢一样的弹幕。如果您的弹幕朝右，您不需要添加`MathHelper.PiOver2`（位于Microsoft.Xna.Framework中）。如果您的弹幕朝上，您将需要它。
```cs
Projectile.rotation = Projectile.velocity.ToRotation() + MathHelper.PiOver2; // 弹幕精灵朝上
// 或
Projectile.rotation = Projectile.velocity.ToRotation(); // 弹幕朝精灵朝右
```

### spriteDirection
如果您的精灵在向左射击时上下颠倒，您需要设置这个：`Projectile.spriteDirection = Projectile.direction;`有关说明和示例，请参阅[绘制和碰撞](#绘制和碰撞)。

## 灰尘
在AI中生成灰尘以获得视觉效果。随机化位置、`DustID`和频率会更有视觉美感。这是附魔回旋镖灰尘生成（aiStyle 3，AIType ProjectileID.EnchantedBoomerang）：
```cs
if (Main.rand.NextBool(5)) // 只有20%的时间生成
{
	int choice = Main.rand.Next(3); // 选择一个随机数：0、1或2
	if (choice == 0) // 使用该数字选择dustID：15、57或58
	{
		choice = 15;
	}
	else if (choice == 1)
	{
		choice = 57;
	}
	else
	{
		choice = 58;
	}
	// 生成灰尘
	Dust.NewDust(Projectile.position, Projectile.width, Projectile.height, choice, Projectile.velocity.X * 0.25f, Projectile.velocity.Y * 0.25f, 150, default(Color), 0.7f);
}
```
### 灰尘轨迹
通过每AI更新生成1个灰尘可以实现灰尘轨迹。

## 光照
模组制作者对光照有许多不同的定义。如果您想添加粒子，请参阅灰尘部分。如果您希望弹幕纹理不受黑暗影响，请参阅`ModProjectile.GetAlpha`。如果您希望弹幕发出白光，您可以在`SetDefaults`中设置`Projectile.light = 1f;`（或0到1之间的任何数字）。最后，如果您想发出彩色光而非来自生成的灰尘，这种光照可以照亮附近的瓷砖，请在`AI`方法中使用Lighting.AddLight：
```cs
Lighting.AddLight(Projectile.Center, 0.9f, 0.1f, 0.3f); // R G B值从0到1f。这是 Crimson Heart宠物的红色
```

## 声音
### 重复声音
字段`soundDelay`将每帧自动减少。检查它是否为0然后设置一个值并播放声音将导致重复声音。这个示例来自回旋镖aiStyle（3）。
```cs
if (Projectile.soundDelay == 0)
{
	Projectile.soundDelay = 8;
	Terraria.Audio.SoundEngine.PlaySound(SoundID.Item7, Projectile.position);
}
```

## 分裂/生成弹幕
水晶子弹和腐蚀之灾弹幕（EatersBite）都在死亡时生成新弹幕。我们通常在`OnKill`或`OnTileCollide`中看到生成弹幕，但我们也可以在`AI`中做这件事。生成弹幕时，我们需要注意多人游戏兼容性，并确保仅在`Main.myPlayer == Projectile.owner`为真时生成弹幕以防止问题。按比例缩小Projectile.damage是典型的。查阅[Projectile.NewProjectile](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation#public-static-int-newprojectilefloat-x-float-y-float-speedx-float-speedy-int-type-int-damage-float-knockback-int-owner--255-float-ai0--0f-float-ai1--0f-)了解考虑多人游戏的参数和用法。
```cs
// 这段代码在与弹幕相反的方向生成3个弹幕，速度有随机变化。
if (OptionallySomeCondition && Projectile.owner == Main.myPlayer)
{
	for (int i = 0; i < 3; i++)
	{
		// 为其他弹幕计算新速度。
		// 以40%到70%的速度反弹，加上-8到8之间的随机量
		float speedX = -Projectile.velocity.X * Main.rand.NextFloat(.4f, .7f) + Main.rand.NextFloat(-8f, 8f);
		float speedY = -Projectile.velocity.Y * Main.rand.Next(40, 70) * 0.01f + Main.rand.Next(-20, 21) * 0.4f; // 这是原版代码，有点难理解。这只是为了教您有时可以将原版代码转换为更易读的代码。

		// 生成弹幕。
		Projectile.NewProjectile(Projectile.GetSource_FromThis(), Projectile.position.X + speedX, Projectile.position.Y + speedY, speedX, speedY, ProjectileID.CrystalShard, (int)(Projectile.damage * 0.5), 0f, Projectile.owner, 0f, 0f);
	}
}
```

## 追踪
追踪弹幕的工作原理是找到目标，然后调整速度指向目标。要找到目标，通常遍历`Main.ActiveNPCs`以找到最近的敌人`NPC`。一旦确定目标，`Projectile.velocity`被调整指向目标的`NPC.Center`。根据所需的弹幕飞行特性，这种调整可以是渐进的或即时的。可以向追踪逻辑添加更多逻辑来影响追踪的准确性和 abrupt程度。[ExampleHomingProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleHomingProjectile.cs)展示了一个基本的追踪弹幕。

## 跟随鼠标
要跟随鼠标，我们在`AI`中编写代码检查`Main.MouseWorld`并调整`Projectile.velocity`向鼠标移动。此代码必须仅对弹幕所有者运行，使用`if(Main.myPlayer == projectile.owner)`检查。如果不这样做，将导致不同步，因为弹幕受每个用户本地鼠标的影响。其他客户端不知道弹幕所有者的鼠标位置，因此结果速度和位置更改使用`Projectile.netUpdate`同步。[MagicMissile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Old/Projectiles/MagicMissile.cs)尽管目前未更新为可在当前tModLoader上运行，但展示了正确实现跟随鼠标的弹幕所需的代码。

## 持有的弹幕
持有的弹幕是看起来像是在玩家手中持有的弹幕，类似于物品武器的持有方式。作为弹幕，自定义行为比物品更容易。最常见的持有弹幕示例是钻头，但许多其他武器如长矛、短剑、鞭子和连枷通常实现为持有的弹幕。

要实现持有的弹幕，您需要一个"发射"该弹幕的物品。那个物品需要设置`Item.channel = true;`。在弹幕中，代码将检查`player.channel`以查看物品是否仍在使用中。如果是，弹幕将被设置以匹配玩家位置和物品旋转。[ExampleDrillProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleDrillProjectile.cs)作为持有弹幕的示例，展示了实现持有弹幕所需的所有代码。

## 玩家所有者
如果弹幕是由玩家生成的，我们可以使用`Player player = Main.player[Projectile.owner];`来检索该`Player`。一旦我们这样做，我们就可以自由访问所需的任何玩家数据。并非每个弹幕都由玩家生成，确保仅在弹幕仅由玩家生成时访问玩家。错误使用`Projectile.owner`将导致问题，尤其是在多人游戏中。

## 淡入/淡出
许多子弹淡入，以便它们生成时不会与它们出现的枪口重叠。您可以在`SetDefaults`中设置弹幕以透明方式生成`Projectile.alpha = 255;`。然后，在`AI`中，您可以在每次更新时减少该透明度。
```cs
if (Projectile.alpha > 0)
{
	Projectile.alpha -= 15; // 减少alpha，增加可见度。
}
```
[`ExampleAdvancedAnimatedProjectile`](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleAdvancedAnimatedProjectile.cs)展示了在生成时淡入和在取消生成时淡出的用法。

## 动画/多帧
弹幕动画（切换要绘制的精灵帧）在`AI`中进行。首先确保在`SetStaticDefaults`中设置`Main.projFrames[Projectile.type] = #;`。您可以将`Projectile.frame`设置为您想要绘制的任何帧。不要尝试使用.gif文件作为纹理，那不起作用，这不是动画的做法。

### 循环/轮换
您可以使用`Projectile.frameCounter`和`Main.projFrames[Projectile.type]`来实现循环动画。示例：[`ExampleAdvancedAnimatedProjectile`](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleAdvancedAnimatedProjectile.cs)
```cs
// 循环4个动画帧，每个帧花费5刻。
if (++Projectile.frameCounter >= 5)
{
	Projectile.frameCounter = 0;
	if (++Projectile.frame >= Main.projFrames[Projectile.type])
	{
		Projectile.frame = 0;
	}
}
// 或者，更紧凑的写法：
if (++Projectile.frameCounter >= 5)
{
	Projectile.frameCounter = 0;
	Projectile.frame = ++Projectile.frame % Main.projFrames[Projectile.type];
}
```

## 示例
### AiStyle 1
弹幕AiStyle 1，也称为`ProjAIStyleID.Arrow`，用于游戏中许多简单的弹幕，有超过3000行代码。如果您尝试使用[高级原版代码适配](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption)指南来适配此AI，您可能会感到沮丧。这是该AiStyle的简要概述，不包含所有ProjectileID特定代码：
```cs
// 可选：如果弹幕应该淡入，淡入它：
if (Projectile.alpha > 0)
	Projectile.alpha -= 15;
if (Projectile.alpha < 0)
	Projectile.alpha = 0;

// 设置旋转以面向当前轨迹：
Projectile.rotation = (float)Math.Atan2((double)Projectile.velocity.Y, (double)Projectile.velocity.X) + 1.57f;
// 或者，这个版本更容易阅读：
Projectile.rotation = Projectile.velocity.ToRotation() + MathHelper.PiOver2;
// 限制向下速度，以防您为此弹幕添加重力
if (Projectile.velocity.Y > 16f)
	Projectile.velocity.Y = 16f;
```
正如您所看到的，不包含所有ProjectileID特定代码的Projectile AiStyle 1只有几行代码，与上面的淡入和旋转示例相符。

# 有限弹幕数量
大多数回旋镖武器限制它们一次可以"发出"的活动弹幕数量（通常仅为1）。这不是弹幕代码的一部分，而是生成弹幕的物品的行为。物品通常检查玩家拥有的弹幕在世界中有多少，并在大于所需限制时阻止物品使用。以下代码是`ModItem`生成特定`ModProjectile`时这种典型效果的示例：
```cs
public override bool CanUseItem(Player player) {
	return player.ownedProjectileCounts[Item.shoot] < 1;
}
```

# 弹跳和OnTileCollide
许多弹幕在与实心瓷砖碰撞时弹跳。从技术上讲，这种行为不是`AI`的一部分，因为它发生在名为`OnTileCollide`的方法中。默认情况下，当弹幕与瓷砖碰撞时，速度会迅速降低，使弹幕停止并被销毁。通过重写`ModProjectile.OnTileCollide`并返回`false`，我们可以避免该逻辑并实现我们自己的逻辑。如果我们返回`true`，我们可以在保留原版逻辑的同时添加额外逻辑。这最常见的用途是允许您的弹幕弹跳。一些弹幕通过失去一些速度逼真地弹跳，而其他弹幕则不合理地保持其原始速度沿新方向弹跳。一些弹幕有有限的弹跳次数，通常通过利用`Projectile.penetrate`来倒数弹跳次数。当重写`ModProjectile.OnTileCollide`时，销毁弹幕、生成碰撞灰尘和播放碰撞声音都是可能需要实现的事情。

## OnTileCollide示例

[ExampleBullet.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleBullet.cs#L37)展示了有限弹跳、瓷砖碰撞灰尘、瓷砖碰撞声音以及保持速度完全弹跳的用法。

[ExampleCloneProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleCloneProjectile.cs#L51)展示了多种随机碰撞声音并返回true以保留原始碰撞逻辑。`OnKill`展示了生成一小波次级弹幕。

[SparklingBall.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/SparklingBall.cs#L28)类似于ExampleBullet.cs，只是速度乘以`0.75f`，从而每次弹跳时减慢弹幕速度。

[ExampleAdvancedFlailProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleAdvancedFlailProjectile.cs#L315)展示了来自连枷状态和速度的灰尘和弹跳行为的更多动态示例。它还展示了生成火花视觉效果的额外行为。这种动态行为提供了连枷武器的标志性手感。

通过以上示例，您可以制作您想要的瓷砖碰撞行为。如果您尝试克隆原版弹幕行为，请搜索`Projectile.HandleMovement`以获取`ProjectileID`编号或弹幕`aiStyle`编号来找到相关代码。适配指南中的[Shadowbeam Staff Clone](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption#example-item-and-projectile-shadowbeam-staff-clone)示例展示了这一点以及查找原版代码片段所需的其他思考过程，这些片段不在`AI`代码范围内。

# 自定义绘制
有时默认绘制行为不是我们想要的。我们可以重写`PreDraw`或`PostDraw`来手动绘制弹幕以进一步自定义。

## 残影轨迹
"残影轨迹"是弹幕在其后方绘制自身的淡化副本。（另请参阅[灰尘轨迹部分](#灰尘轨迹)。）我们可以通过告诉游戏记住先前的弹幕位置然后手动在这些位置绘制弹幕来实现残影轨迹。以下代码是来自[ExampleBullet.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleBullet.cs)的相关代码：

```cs
public override void SetStaticDefaults() {
	ProjectileID.Sets.TrailCacheLength[Projectile.type] = 5; // 要记录的旧位置长度
	ProjectileID.Sets.TrailingMode[Projectile.type] = 0; // 记录模式
}

public override bool PreDraw(ref Color lightColor) {
	Texture2D texture = TextureAssets.Projectile[Type].Value;
	Vector2 drawOrigin = new Vector2(texture.Width * 0.5f, Projectile.height * 0.5f);
	for (int k = Projectile.oldPos.Length - 1; k > 0; k--) {
		Vector2 drawPos = (Projectile.oldPos[k] - Main.screenPosition) + drawOrigin + new Vector2(0f, Projectile.gfxOffY);
		Color color = Projectile.GetAlpha(lightColor) * ((Projectile.oldPos.Length - k) / (float)Projectile.oldPos.Length);
		Main.EntitySpriteDraw(texture, drawPos, null, color, Projectile.rotation, drawOrigin, Projectile.scale, SpriteEffects.None, 0);
	}

	return true;
}
```
在这段代码中，首先我们确保`ProjectileID.Sets.TrailCacheLength`和`ProjectileID.Sets.TrailingMode`有适当的值，查阅它们的文档获取更多信息。接下来，在`PreDraw`中，我们以相反顺序遍历`Projectile.oldPos`条目，并根据其"年龄"淡化颜色值绘制弹幕精灵。for循环确保不绘制第0个条目，因为那对应于当前位置，由于我们从此方法返回true，它将自动被绘制。反向迭代（从最旧到最新）很重要。它使重叠绘制看起来正确：

![image](https://github.com/user-attachments/assets/1eabb7d5-2a87-472f-9cc8-6a19d97bb2a4)

模组制作者可以尝试更改绘制比例。

### 带`spriteDirection`和`rotation`的残影轨迹
`Projectile.oldSpriteDirection`和`Projectile.oldRot`可用于促进需要`rotation`和`spriteDirection`信息的残影轨迹。

### 带`frame`的残影轨迹
没有`Projectile.oldFrame`来记住`Projectile.frame`的先前值，但通过一些数学，可以为循环动画计算先前的动画帧，如下所示：

```cs
for (int k = Projectile.oldPos.Length - 1; k > 0; k--) {
	// 计算过去此弹幕的帧值
	int frameCountOfPrevious = Projectile.frameCounter - k;
	int frameAdjustment = (int)MathF.Floor((float)frameCountOfPrevious / TicksPerAnimationFrame);
	int oldFrame = Utils.ModulusPositive(Projectile.frame + frameAdjustment, Main.projFrames[Type]);

	// 更改将被绘制的帧
	Rectangle drawRectangle = texture.Frame(1, Main.projFrames[Type], 0, oldFrame);

	Vector2 drawPos = (Projectile.oldPos[k] - Main.screenPosition) + drawOrigin + new Vector2(0f, Projectile.gfxOffY);
	Color color = Projectile.GetAlpha(lightColor) * ((Projectile.oldPos.Length - k) / (float)Projectile.oldPos.Length);
	Main.EntitySpriteDraw(texture, drawPos, drawRectangle, color, Projectile.rotation, drawOrigin, Projectile.scale, SpriteEffects.None, 0);
}
```

https://github.com/user-attachments/assets/2307462e-b0f1-4195-a14c-b52268bd8dfc

## 发光遮罩
名为`[TextureName]_Glow.png`的文件将自动用作此弹幕的发光遮罩纹理，这意味着它将以全亮度绘制在正常纹理上方。

## PreDraw/PostDraw示例
以下是一些可学习的自定义绘制示例：

[ExampleBullet.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleBullet.cs#L62)使用`PreDraw`绘制残影轨迹。

[MinionBossPetProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Pets/MinionBossPet/MinionBossPetProjectile.cs#L67)展示使用`PostDraw`在现有精灵上绘制额外细节。

[ExampleWhipProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleWhipProjectile.cs#L88)使用`PreDraw`绘制线条和单个鞭子段。

[ExampleAdvancedAnimatedProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleAdvancedAnimatedProjectile.cs#L102)使用`PreDraw`手动绘制弹幕纹理以避免与原版弹幕绘制怪异行为斗争。

[ExampleSwingingEnergySwordProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleSwingingEnergySwordProjectile.cs#L190)使用`PreDraw`绘制类似于1.4.4对暗影之魂、圣剑等所做的更改的"能量剑"效果。

# 绘制和碰撞
您可能会发现您的弹幕在不应该的时候击中墙壁，或者碰撞箱很奇怪。首先，值得重申的是，`Projectile.width`和`Projectile.height`对应于弹幕的碰撞箱，而不是精灵图。您几乎从不希望`width`或`height`不同，它应该是正方形。您也从不希望使用`Projectile.scale`，因为原版绘制代码并没有真正正确地考虑它。精灵的绘制尝试将碰撞箱覆盖在精灵上，这个精灵的绘制受`Main.DrawProj_DrawNormalProjs`方法中进行的各种数学计算影响。

## 垂直精灵示例
让我们通过这个示例来探索碰撞和绘制问题并努力解决它们。这是精灵，它是48x70像素：
![](https://i.imgur.com/y4OcJAv.png)
这个`ModProjectile`的重要部分如下：
```cs
// SetDefaults
Projectile.width = 8;
Projectile.height = 8;
// AI
Projectile.rotation = Projectile.velocity.ToRotation() + MathHelper.ToRadians(90f);
```
我们的目标是让这个弹幕的黄色部分成为碰撞箱。黄色区域是8x8像素，所以我们已经将`width`和`height`设置为8。`Projectile.rotation`代码将旋转设置为速度，同时添加90度旋转，因为我们使用的精灵恰好朝上而不是像游戏预期的那样朝右。在本指南中，我们将使用[模组制作者工具箱](https://steamcommunity.com/sharedfiles/filedetails/?id=2573569299)模组来可视化碰撞箱。这非常有用。

在这里我们看到碰撞箱（黄色方块）与我们的精灵尖端不匹配：

https://github.com/tModLoader/tModLoader/assets/4522492/8efa67d9-5565-4076-b0af-5e1688dfde84

原版代码执行的数学有点混乱，但基本上我们需要设置`DrawOffsetX`和`DrawOriginOffsetY`为某些值，以偏移精灵的绘制，尝试将精灵正确放置在碰撞箱上。如果您正在尝试这个，可以使用[模组制作者工具箱](https://steamcommunity.com/sharedfiles/filedetails/?id=2573569299)在游戏中更改偏移值，或者使用[编辑并继续](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#edit-and-continue)在游戏中调整值。另一种方法是在您的图形程序中直接测量：
![](https://i.imgur.com/m5DxkBm.png)
在这里我们看到使用模组制作者工具箱测试各种值。确保在您的`SetDefaults`代码中复制这些值：

https://github.com/tModLoader/tModLoader/assets/4522492/a21ae4df-e79f-4878-84ff-d8e30dd59583

经过一些实验或测量，我们知道在这个`ModProjectile.SetDefaults`中添加`DrawOffsetX = -20;`将修复绘制相对于碰撞箱的位置。

现在让我们尝试将碰撞箱放置在精灵的蓝色部分之上。这次，让我们使用[编辑并继续](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#edit-and-continue)来完成。在下面的剪辑中，您可以看到我们如何快速测试新值：

https://github.com/tModLoader/tModLoader/assets/4522492/d17b18ad-1d4b-46fb-90ee-dfa43789e484

正如您所看到的，我们添加了`DrawOriginOffsetY = -16;`以将碰撞箱放置在精灵下方。

### 修复上下颠倒的精灵问题
您可能已经注意到精灵在向左射击时上下颠倒。请记住，在我们的`AI`中，我们有这行代码：`Projectile.rotation = Projectile.velocity.ToRotation() + MathHelper.ToRadians(90f);`。如果我们将精灵向左旋转，那么它就上下颠倒了。我们可以用`spriteDirection`来修复这个问题。`spriteDirection`将水平翻转精灵的绘制。要实现这一点，只需在`Projectile.rotation = ...`行之后的`AI`代码中添加`Projectile.spriteDirection = Projectile.direction;`。

未修复：
![](https://i.imgur.com/sKUq94z.png)
已修复：
![](https://i.imgur.com/w3ALhDX.png)

## 水平精灵示例
如果您的精灵是水平方向的，事情会有一点不同。这是我们新的水平精灵，现在是70x48，水平指向右侧而不是像之前那样指向上：
![](https://i.imgur.com/etzbzs0.png)

再一次，我们可以看到碰撞箱不匹配：

https://github.com/tModLoader/tModLoader/assets/4522492/50312b6e-ecc8-46fe-b356-f50b9164290e

与垂直示例不同，这次我们直接设置`Projectile.rotation = Projectile.velocity.ToRotation();`而不是添加额外的90度。经过一些实验，我们得到以下关于尖端碰撞箱的代码：
```cs
DrawOffsetX = -62;
DrawOriginOffsetY = -20;
DrawOriginOffsetX = 31;
```
这些值有点奇怪，因为Terraria正在执行一些数学，所以这里是计算它们的算法：
```cs
DrawOffsetX = 预期碰撞箱左上角的负X像素位置
DrawOriginOffsetY = 预期碰撞箱左上角的负Y像素位置
DrawOriginOffsetX = 碰撞箱中心的X像素位置减去纹理宽度除以2
```
这是一个图表：
![](https://i.imgur.com/zQfxXM3.png)
如果您不喜欢与原版弹幕渲染代码斗争，您可以像在[ExampleAdvancedAnimatedProjectile](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleAdvancedAnimatedProjectile.cs#L101)中看到的那样自己绘制弹幕

### 再次修复上下颠倒的精灵问题
对于垂直精灵，使用`Projectile.spriteDirection`有效，因为它控制弹幕精灵的水平翻转。使用水平精灵，水平翻转会使精灵向后移动：
![](https://i.imgur.com/vfKrRzZ.png)
要修复这个问题，我们需要动态调整偏移，并根据条件向旋转添加180度或Pi。这是代码：
```cs
// 将direction和spriteDirection都设置为1或-1（分别为右和左）
// Projectile.direction在Projectile.Update中自动正确设置，但我们需要在这里设置它，否则纹理会在第1帧绘制不正确。
Projectile.spriteDirection = Projectile.direction = (Projectile.velocity.X > 0).ToDirectionInt();
// 如果朝左，向旋转添加Pi可以修正绘制
Projectile.rotation = Projectile.velocity.ToRotation() + (Projectile.spriteDirection == 1 ? 0f : MathHelper.Pi);
if (Projectile.spriteDirection == 1) // 朝右
{
	DrawOffsetX = -62; // 这些值与SetDefaults中的值匹配
	DrawOriginOffsetY = -20;
	DrawOriginOffsetX = 31;
}
else
{
	// 朝左。
	// 如果您在绘制程序中翻转精灵，可以找出这些值。
	DrawOffsetX = 0; // 现在为0，因为碰撞箱的左上角在最左边的像素上。
	DrawOriginOffsetY = -20; // 不变
	DrawOriginOffsetX = -31; // 数学计算结果是另一个值的负数。
}
```
![](https://i.imgur.com/FKfhtQ0.png)

希望这些答案可以帮助您解决弹幕碰撞箱和绘制问题。