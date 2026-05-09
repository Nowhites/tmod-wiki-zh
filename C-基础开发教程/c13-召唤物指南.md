# 基础召唤物指南
在本指南中，我们将介绍召唤物、使其工作的原理，并展示大多数召唤物共享的基本AI代码片段。

## 介绍
召唤物首先是一个弹射物。
不要与Boss召唤物（如克苏鲁之脑的爬行怪）混淆，那是一个NPC。
这意味着你需要扩展tModLoader提供的`ModProjectile`类。
然后，你需要一把用来召唤它的武器，因此需要扩展`ModItem`。
最后是允许召唤物保持存活并能够被取消召唤的东西：
一个`ModBuff`。

总结：
* [ModProjectile](#modprojectile)
* [ModItem](#moditem)
* [ModBuff](#modbuff)

这听起来很多，但实际上并不是。绝大多数将与示例（后两个类）相同，你的大部分代码将在决定召唤物行为的`ModProjectile`中。
唯一"令人困惑"的部分是理解所有这些类如何组合在一起。
因此，在配套的[ExampleSimpleMinion](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Content/Projectiles/Minions)中，所有这些类都在同一个文件夹/文件中以便组织。
强烈建议使用[IDE](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE)来学习本指南，因为你会更容易看到类之间的关系。

## ModBuff
这个类负责召唤物实际被召唤并保持存活，以及能够通过右键点击图标取消召唤。
除此之外，Buff会自我重应用，保证无限持续时间。
如果你忘记实现`Update()`代码，可能会导致
* 取消Buff后你的召唤物仍然存活
* 你的召唤物未被召唤时Buff却处于激活状态

对于你将创建的大多数召唤物，这将是相同的代码，只需相应地替换示例中的`ExampleSimpleMinion`。
你可以在[这里](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/Minions/ExampleSimpleMinion.cs#L21)看到完整代码。

## ModItem
这是你用来召唤召唤物的武器。

### 必须项
它大部分与其他武器类似，以下是`SetDefaults()`的一些显著差异：
```csharp
// 这样武器在挥动时不会像剑一样造成伤害 
Item.noMelee = true;
// 这个武器的伤害类型
Item.DamageType = DamageClass.Summon;
Item.buffType = ModContent.BuffType<ExampleSimpleMinionBuff>();
Item.shoot = ModContent.ProjectileType<ExampleSimpleMinion>();
```
注意没有通常与下面两行相关的`Item.buffTime`和`Item.shootSpeed`。
因为Buff时间会显示在物品提示上（例如"持续1分钟"，这没有意义，因为召唤物持续时间是无限的），
而且召唤物有自己的移动代码，使shoot速度在大多数情况下无用。

```csharp
public override void ModifyShootStats(Player player, ref Vector2 position, ref Vector2 velocity, ref int type, ref int damage, ref float knockback) {
	position = Main.MouseWorld;
}

public override bool Shoot(Player player, EntitySource_ItemUse_WithAmmo source, Vector2 position, Vector2 velocity, int type, int damage, float knockback) {
	player.AddBuff(Item.buffType, 2);

	var projectile = Projectile.NewProjectileDirect(source, position, velocity, type, damage, knockback, Main.myPlayer);
	projectile.originalDamage = Item.damage;

	return false;
}

```
这是必需的，这样保持召唤物存活并允许你正确取消召唤的Buff才能正确应用。你可以在[这里](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/Minions/ExampleSimpleMinion.cs#L40)看到完整代码。

## ModProjectile
这是你实际的召唤物文件，你的大部分代码都在这里。建议阅读[基础弹射物指南](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile)以获取弹射物特定的问题或解答。

### 必须项
（关于必须右键目标锁定代码，参见[下面的中断部分](#intermission-targeting)）
首先，我们需要一堆代码来使弹射物被分类为召唤物。

#### 默认值
在`SetStaticDefaults()`中：
```csharp
// 设置这个召唤物在其精灵图上的帧数
Main.projFrames[Projectile.type] = 4;
// 这对于右键目标锁定是必需的
ProjectileID.Sets.MinionTargettingFeature[Projectile.type] = true;

Main.projPet[Projectile.type] = true; // 表示这个弹射物是一个宠物或召唤物

ProjectileID.Sets.MinionSacrificable[Projectile.type] = true; // 这需要让你的召唤物在召唤时正确生成，并在其他召唤物被召唤时正确替换
ProjectileID.Sets.CultistIsResistantTo[Projectile.type] = true; // 让邪教徒对这个弹射物有抗性，因为它对所有追踪弹射物都有抗性。
```
在`SetDefaults()`中：
```csharp 
// 只控制它是否在与敌人接触时造成伤害（更多内容见下文）
Projectile.friendly = true;
// 只决定伤害类型
Projectile.minion = true;
// 声明伤害类型（需要它才能造成伤害）
Projectile.DamageType = DamageClass.Summon; 
// 这个召唤物从玩家可用的总召唤物槽位中占用的槽位数（更多内容见下文）
Projectile.minionSlots = 1f;
// 需要这样召唤物在与敌人或方块碰撞时不会消失
Projectile.penetrate = -1;
```

#### 接触伤害
如果你的召唤物应该撞击敌人（造成接触伤害），你需要这两个钩子：
```csharp
// 在这里你可以决定你的召唤物是否破坏草或罐子之类的东西
// （在这个例子中返回false，因为设置为true可能会导致蜜蜂皇后幼虫被破坏并意外召唤Boss）
public override bool? CanCutTiles() {
	return false;
}

public override bool MinionContactDamage() {
	return true;
}
```
在`AI()`中，你需要将`Projectile.friendly`设置为表示召唤物是否有目标的布尔值（更多内容见下文）。
这是需要的，这样召唤物在空闲时不会伤害目标假人。

#### '存活检查'
在`AI()`中，你写的第一个代码应该总是这个，只需相应地替换`ExampleSimpleMinionBuff`：
```csharp
Player player = Main.player[Projectile.owner];
if (player.dead || !player.active) {
	player.ClearBuff(ModContent.BuffType<ExampleSimpleMinionBuff>());
}
if (player.HasBuff(ModContent.BuffType<ExampleSimpleMinionBuff>())) {
	Projectile.timeLeft = 2;
}
```

这在技术上是让你的召唤物现在正确召唤所需的全部。但它什么都不做，我们如何实际让它做些什么？

### 召唤物AI

召唤物有两种攻击方式：直接对敌人造成接触伤害，或向敌人发射其他弹射物。
召唤物也有两种移动方式：受重力影响，或不受。
`ExampleSimpleMinion`展示了最简单的召唤物类型：接触伤害+飞行（且不与方块碰撞，这使其更容易）。

即使是简单的召唤物也需要相当多的代码才能正确工作。大多数时候，它可以归结为相同的几个常见"操作"：
* 一般行为（确定空闲位置、与其他召唤物重叠等）
* 锁定目标（基于条件攻击敌人）
* 移动和攻击
* 动画和视觉效果

如果你的召唤物做更复杂的事情，你可能想考虑用状态来设计你的AI。
使用状态的NPC示例在[这里](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/NPCs/ExampleCustomAISlimeNPC.cs)，相同的概念可以应用于召唤物。

以下所有代码都写在`AI()`钩子中（`public override void AI()`的简写）。

#### 一般行为
属于此类别任务包括：
* 设置某些变量
* 与其他召唤物对齐（注意大多数召唤物如何在你身后重新排序）
* 如果玩家或召唤物彼此距离太远，则传送回玩家身边

你可能希望你的召唤物在不战斗时离你很近？那么你需要给它一个'空闲位置'
它在没有合适目标时前往的位置。这样的空闲位置可能是：
* 玩家上方
* 玩家身后，在召唤物队列中

以下是两者的混合：
```csharp
Vector2 idlePosition = player.Center;
idlePosition.Y -= 48f;
float minionPositionOffsetX = (10 + Projectile.minionPos * 40) * -player.direction;
idlePosition.X += minionPositionOffsetX;
```
`Projectile.minionPos`是它在召唤物列表中的位置。为了进一步移动，我们还将创建这些变量：
```csharp
Vector2 vectorToIdlePosition = idlePosition - Projectile.Center;
float distanceToIdlePosition = vectorToIdlePosition.Length();
```
更多可以在这里做的事情，查看`ExampleSimpleMinion's`的`General behavior`区域。

现在我们可以继续重要的部分：实际做些什么！

#### 锁定目标

注意：在1.4中，大多数情况下你可以简单地使用Minion_FindTargetInRange

```
int startAttackRange = 700f;
int attackTarget = -1;
Projectile.Minion_FindTargetInRange(startAttackRange, ref attackTarget, false);
```

你的召唤物想要攻击事物，所以它需要知道要攻击什么，最重要的是不要攻击什么。
我们首先创建几个需要的变量：
```csharp
// 起始搜索距离
float distanceFromTarget = 700f;
Vector2 targetCenter = Projectile.position;
bool foundTarget = false;
```

我们首先遍历世界中所有NPC，并检查它们是否能够被锁定：
```csharp
for (int i = 0; i < Main.maxNPCs; i++) {
	NPC npc = Main.npc[i];
	if (npc.CanBeChasedBy()) {
	/* 如果我们在这里，意味着我们找到了一个满足以下条件的NPC：
	* 活跃（存活）
	* 可追逐（例如不是邪教徒弓箭手）
	* 最大生命大于5（例如不是小动物）
	* 可以受到伤害（例如不是所有部分都被击杀后的月亮领主核心）
	* 敌对
	* 非不朽（例如不是目标假人）
	*/
	}
}
```

在里面，你可以写任何与NPC和你的召唤物相关的条件，例如：
```csharp
float between = Vector2.Distance(npc.Center, Projectile.Center);
bool closest = Vector2.Distance(Projectile.Center, targetCenter) > between;
bool inRange = between < distanceFromTarget;
bool lineOfSight = Collision.CanHitLine(Projectile.position, Projectile.width, Projectile.height, npc.position, npc.width, npc.height);
bool abovePlayer = player.Center.Y > npc.Center.Y;
```

你可以组合它们，例如：
```csharp
// !foundTarget检查是为了忽略任何范围检查
if (((closest && inRange) || !foundTarget) && lineOfSight)
```

一旦我们找到目标，我们更新搜索变量，以确保只搜索最近的NPC。
```csharp
distanceFromTarget = between;
targetCenter = npc.Center;
foundTarget = true;
```

找到目标后，如果你的召唤物造成接触伤害，你还需要正确设置它的友好状态。
结合`MinionContactDamage()`，这确保只在有目标时伤害目标。
```csharp
Projectile.friendly = foundTarget;
```

#### 中断：目标锁定
如果你的召唤武器应该支持右键目标锁定，你需要在常规目标查找循环之前添加以下代码：
```csharp
if (player.HasMinionAttackTargetNPC) {
	NPC npc = Main.npc[player.MinionAttackTargetNPC];
	float between = Vector2.Distance(npc.Center, Projectile.Center);
	// 合理的距离，这样它不会跨多个屏幕锁定目标
	if (between < 2000f) {
		distanceFromTarget = between;
		targetCenter = npc.Center;
		foundTarget = true;
	}
}
if (!foundTarget) {
	// 常规目标查找循环
}
```

在你的`SetStaticDefaults()`中加上这个：
```csharp
ProjectileID.Sets.MinionTargettingFeature[Projectile.type] = true;
```

最后，完整代码可以在`ExampleSimpleMinion's`的`Find target`区域看到。

#### 移动和攻击

召唤物理想情况下应该在空闲时跟随你移动，在能攻击时向敌人移动。我们怎么做？
如果你想从`start`飞到`end`，这里有一个简单的公式。对于在地面上移动的召唤物，这更复杂（更多内容在最后）。
```csharp
float speed = 8f;
float inertia = 40f;
Vector2 direction = end - start;
direction.Normalize();
direction *= speed;
Projectile.velocity = (Projectile.velocity * (inertia - 1) + direction) / inertia;
```
`speed`不言自明：它在直飞时的期望速度。
`inertia`是它向`direction`加速的"慢"程度。
更高的值意味着它转向更"慢"，更低的值意味着它的移动会更抖动。
`end`应该是其目的地（敌人目标或空闲位置）的`Vector2`，
而`start`应该永远是`Projectile.Center`。

我们可以简单地将其放入代码中，考虑`foundTarget`（然后我们使用`targetCenter`作为`end`，否则使用`vectorToIdlePosition`作为`direction`），
以及我们之前计算的距离的一些巧妙检查，这样我们的召唤物就不会"粘在"它的目的地。

对于以下移动技术（和射击），你需要计时器，[这个指南](https://github.com/tModLoader/tModLoader/wiki/Time-and-Timers)会帮助你。

如果你的召唤物应该冲刺（如死亡球），这意味着在特定方向上 timed、高速度增加，结合一定时间的额外减速。
由于`ExampleSimpleMinion`保持简单，这里没有涉及。

因为造成接触伤害的召唤物在移动时"攻击"，但不发射弹射物的召唤物不攻击，我们需要正确的射击代码。
这不在`ExampleSimpleMinion`中，但在`HoverShooter`的`AI()`末尾，[另一个召唤物示例](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Old/Projectiles/Minions/HoverShooter.cs)。（注意：`HoverShooter`尚未移植到1.4 ExampleMod。请相应调整代码。）

你可以在`ExampleSimpleMinion's`的`Movement`区域看到完整的移动代码。

#### 动画和视觉效果

你的召唤物在移动，但没有动画（假设你有它的精灵图），所以看起来很平淡。
这是一个简单的"以给定频率从上到下循环所有帧"的代码片段。
```csharp
int frameSpeed = 5;
Projectile.frameCounter++;
if (projectile.frameCounter >= frameSpeed) {
	Projectile.frameCounter = 0;
	Projectile.frame++;
	if (Projectile.frame >= Main.projFrames[Projectile.type]) {
		Projectile.frame = 0;
	}
}
```
`frameCounter`是游戏提供的可用于动画的变量（在下一次更改到另一帧之前计算的tick数）。
`frame`是精灵图中图像的编号（从上到下，从0开始）。

视觉效果可以是你想要的任何东西。有些比其他更难实现。这里有一些简单的：

**沿x轴方向倾斜：**
```csharp
Projectile.rotation = Projectile.velocity.X * 0.05f;
```

**创建光源：**
```csharp
Lighting.AddLight(Projectile.Center, Color.White.ToVector3() * 0.78f);
```

**创建尘埃：**
```csharp
if (Main.rand.NextBool(5)) {
Dust.NewDust(Projectile.position, Projectile.width, Projectile.height, DustID.Fire);
}
```

其他内容在[基础弹射物指南](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile)中提到。

你可以在[这里](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/Minions/ExampleSimpleMinion.cs#L100)看到完整代码。

## 常见问题

**我希望我的武器同时召唤两个召唤物，像透视杖一样**

1. 在你的`ModProjectile`中，设置`Projectile.minionSlots`为0.5f。
2. 在你的`ModItem`中，在`Shoot()`钩子中通过`Projectile.NewProjectile()`生成另一个弹射物。

**我希望召唤一个占用超过一个召唤物槽的弹射物，或超过一个召唤物（总召唤物槽大于1）**

如果适用，参见上面的答案。
另外，在你的`ModItem`的`SetStaticDefaults()`中，分配使用该武器单次将召唤的召唤物槽数：
```csharp
ItemID.Sets.StaffMinionSlotsRequired[Item.type] = 2f; // 以2f为例
```

在你的`CanUseItem()`钩子中：
```csharp
return player.maxMinions >= ItemID.Sets.StaffMinionSlotsRequired[Item.type];
```

**我的召唤物应该在地面上奔跑而不是飞行**

协调移动与召唤物状态（如靠近玩家、在敌人处跳跃、当离玩家太远时飞行等）更高级，不在本指南范围内。
考虑下面的要点。

**当敌人就在它下面时，我的召唤物不穿过平台**

如果你的召唤物不飞行，你想使用`TileCollideStyle()`钩子。你可能想让`foundTarget`、`targetCenter`等成为你`ModProjectile`中的字段，而不是在`AI()`中，这样你不需要在这里再次做目标查找代码（参见[目标锁定](#targeting)）。如果你这样做，确保在`AI()`中将它们重置为默认值，这样一切都会重置自己。
```csharp
if (foundTarget) {
	Vector2 toTarget = targetCenter - Projectile.Center;
	// 在这里我们检查NPC是否在召唤物下方且水平距离300/16 = 18.25格
	if (toTarget.Y > 0 && Math.Abs(toTarget.X) < 300) {
		fallThrough = true;
	}
	else {
		fallThrough = false;
	}
}
else {
	fallThrough = false;
}
return base.TileCollideStyle(ref width, ref height, ref fallThrough, ref hitboxCenterFrac);
```

**我想做与原版完全一样的东西/示例中未显示的东西**

最好的办法是自己查看原版源代码，指南在[这里](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption)。

**我想做类似星尘龙的东西**

它的一些代码（主要是替换/添加新段落的部分）是非常硬编码的。你最多可以一次召唤所有必要的段落。
