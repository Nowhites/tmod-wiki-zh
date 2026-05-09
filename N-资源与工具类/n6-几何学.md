# 为什么学习几何学
许多视觉上有趣的效果以及模组制作者想要在模组中实现的有趣行为都需要一点几何知识。本指南将解释与tModLoader模组制作相关的几何学最基本用法。

例如，在弧形中生成灰尘或弹幕、让敌人向玩家射击以及编写追踪行为都利用了几何学。在教授基础知识后，本指南的后半部分将包含这些概念的示例。

# 先决知识
## 几何教育
作为程序员，特别是对电子游戏有兴趣的人，几何学是一项基本技能。由于Terraria是2D游戏，所需要的几何知识并不广泛，但对向量基础知识的熟悉是必不可少的。

## 坐标
Terraria使用不同的坐标集，X和Y的方向如果您以前没有从事过图形工作可能会让您惊讶。请参阅[坐标](https://github.com/tModLoader/tModLoader/wiki/Coordinates)并熟悉世界坐标以及正X和Y的方向。

## 旋转
旋转以弧度表示，而不是度。如果您想使用度，只需调用`MathHelper.ToRadians`方法。通常您只希望在某些行为的初始赋值时使用度，例如声明武器将以30度弧形射击。还要注意旋转0朝向右侧。旋转90度将直接向下指向，因为Y向下。

# Vector2
`Vector2`是一个结构，表示为几何中教导的2维向量。`Vector2`包含2个字段，`X`和`Y`，代表2维向量的X和Y分量的大小。在tModLoader中，Vector2主要用于两个目的。第一个目的是位置。`Vector2`用于表示许多游戏元素的位置，如`Player`、`Projectile`、`Dust`或`NPC`。`Vector2`也用于表示速度。速度是某物在X和Y方向上移动的速度。例如，如果玩家的位置是`3, 7`，并且玩家的速度是`4, 8`，那么每次游戏更新位置时玩家的位置将通过其速度移动。在这个例子中，1次更新后，玩家的新位置将是`7, 15`，因为`3 + 4 = 7`而`7 + 8 = 15`。如您所见，向量加法是通过将每个分量相加来完成的。如果向量A代表一个位置，向量B代表速度，那么经过X个单位时间后，新位置将等于`A + X * B`。下面是一些图表，在Terraria坐标系中教授这个概念。注意在视频中每次更新位置时如何按玩家的速度改变。记住游戏每秒运行60次更新，所以速度值是每次更新的世界空间单位，而不是每秒的世界空间单位。

![](https://i.imgur.com/GCqPyFc.png)

https://github.com/tModLoader/tModLoader/assets/4522492/e5c11e4c-b6d8-43c0-bbe5-9feb07105ced

我们也可以使用向量来表示两点之间的差异。一个常见的例子是敌人向玩家射弹。如果您想编程让敌人向玩家射击，您的代码需要知道要射击的方向。我们可以通过从npc.position减去player.position来计算那个方向。向量的减法与加法相同，X分量相互相减，向量的Y分量相互相减。从A到B的向量通过以下计算：`vector = B - A`。想象一个敌人在`14, 4`，一个玩家在`3, 12`。通过从敌人位置减去玩家位置，我们得到`-11, 8`的结果。在我们的代码中，我们不能直接使用这个向量，因为它代表位置的原始差异，而不是一个方向。要把这个向量变成一个方向并随后用它来生成弹射物，请阅读下面的Vector2.Normalize部分。下图显示从敌人到玩家的结果向量。

![](https://i.imgur.com/TT90bk0.png)

### 位置 vs 中心
在上面的图表中，您可能已经注意到箭头源自并指向实体的左上角。实际上，`Player.position`确实指向左上角，因为游戏就是这样设计的。这意味着我们实际上在代码中很少使用`Player.position`。相反，我们使用`Player.Center`，因为那个值指向实体的中心。（所有实体都可以被认为有一个矩形作为他们的碰撞箱。）对于`NPC.Center`和`Projectile.Center`也可以这样说。从现在起，指南将使用`.Center`因为这个位置更有意义。

## 加速度
当游戏更新某物（如弹射物）的位置时，它获取当前速度并将其添加到当前位置。这每秒发生60次，并以世界坐标运行。对于像子弹这样的弹射物，这就是需要发生的全部，但我们可以实现"加速度"来随时间影响速度，让我们的弹射物获得有趣的移动。这种加速度发生在各种`AI`方法中，也发生在各种碰撞方法中。加速度可用于模拟重力、空气阻力和追踪能力。

### 重力
重力通过每次更新向实体添加正Y速度来模拟。游戏默认实现玩家的重力。NPC受重力影响如果`NPC.noGravity`为false。灰尘也有一个`noGravity`字段。弹射物不受重力影响，所以如果模组制作者希望的话，必须在`ModProjectile.AI`中为弹射物添加重力力。[基础弹射物](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile#gravity)指南详细介绍了实现重力的各种细节。

```cs
Projectile.velocity.Y = Projectile.velocity.Y + 0.1f;
```

### 阻力或空气阻力
要模拟空气阻力，请阅读[基础弹射物](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile#wind-resistance)指南。本质上速度的一个分量乘以一个小于1的数字，所以慢慢减少它。

### 追踪
追踪最简单的形式基本上是向目标加速。请参阅[基础弹射物](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile#homing)。

### 碰撞和弹跳
当弹射物与实心瓦片碰撞时，速度会立即反转方向以允许弹射物弹跳。请参阅[基础弹射物](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile#bounce-and-ontilecollide)获取更多关于此的内容。

### 加速度可视化示例

在下面的视频中，我们可以看到上面大多数主题的总结。红色箭头代表速度，蓝色箭头代表加速度。在手里剑示例中，我们可以看到加速度力稍微指向左下方。左边的力是由空气阻力引起的，下方的力是由重力引起的。值得注意的是，在生成后立即的三分之一秒内，手里剑没有任何力作用在上面。这是通过类似于[延迟重力](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile#delayed-gravity)部分中所示的计时器来实现的，并给予手里剑有趣的移动行为。手雷没有相同的空气阻力力，所以我们只看到一个重力力。当手雷与瓦片碰撞时，我们看到一瞬间有一个大的力。这是碰撞力，逆转了弹射物速度。

https://github.com/tModLoader/tModLoader/assets/4522492/c6ee3ea3-1485-419b-b113-41cf2a23ea07

## Vector2.Normalize
在处理向量时，大多数时候向量的大小不相关，只有所代表的 direction 向量的大小是相关的。想象一个玩家离敌人100单位，另一个玩家离敌人1000单位但方向相同。如果我们计算从敌人到每个玩家的向量，那些向量将指向相同的方向，但一个将是另一个的10倍长。如果我们把这些向量原样用在我们的`AI`方法来生成向玩家射击的弹射物，第二个弹射物将飞行速度快10倍！我们不想要这个，我们希望敌人弹射物无论玩家多远都能以一致的速度飞行。（现在我们想象的是一个子弹式弹射物，如果您的敌人投掷的是手雷之类的东西，您会想要考虑距离一直到敌人打算的最大投掷速度，但那种高级AI行为不是我们这里要谈论的。）

解决这个问题的方法是"规范化"向量。规范化向量简单地说就是我们将向量缩放到长度为1，得到所谓的单位向量。您可以使用您在学校学习的毕达哥拉斯定理来做到这一点，但幸运的是`Vector2`类已经有了这个功能。然而，我们不想使用普通的Normalize方法，因为有可能除以零并导致游戏崩溃。相反，我们使用一个叫做`SafeNormalize`的方法：

```cs
// 这段代码可能位于ModNPC.AI方法中
// 注意player应该以某种方式在代码前面定义，可能是通过使用NPC.TargetClosest();和Player player = Main.player[NPC.target];
// 首先，通过以正确顺序减去两个向量来计算从npc到玩家的向量（从A到B的向量是B - A）
Vector2 vectorFromNpcToPlayer = player.Center - NPC.Center;
// 接下来，调用SafeNormalize将向量缩放到长度为1（也就是一个单位向量），这得到一个只代表方向的向量
Vector2 directionFromNpcToPlayer = vectorFromNpcToPlayer.SafeNormalize(Vector2.UnitX);
// 接下来，我们需要我们的npc有一些预期的射击速度。您会想要实验以找到一个好的速度。
float shootVelocity = 10;
// 最后，我们通过将向量乘以我们的shootVelocity来以预期方向和预期初始速度生成弹射物
Projectile.NewProjectile(source, NPC.Center, directionFromNpcToPlayer * shootVelocity, ProjectileID.BombSkeletronPrime, 5, 0, Main.myPlayer);
```

## Vector2.ToRotation
给定一个向量，无论是规范化的还是非规范化的，我们可以通过在该向量上调用`ToRotation`方法来计算旋转值。许多飞行敌人旋转面向他们的目标，如恶魔眼。您可以使用从敌人到玩家的向量来设置敌人旋转，或者您可以使用当前敌人速度来设置`NPC.rotation`。对于更高级的情况，您可能希望敌人NPC慢慢转向目标而不是立即转身面向玩家。

```cs
// 首先，计算一个指向您想要看的方向的向量
Vector2 vectorFromNpcToPlayer = player.Center - NPC.Center;
// 第二，使用ToRotation方法将该Vector2变成一个代表弧度旋转的float。
float desiredRotation = vectorFromNpcToPlayer.ToRotation();
// 现在我们可以做2件事之一。最简单的方法是直接使用旋转值
NPC.rotation = desiredRotation;
// 第二种方法是使用那个旋转值同时遵守最大旋转速度来转动npc。实验直到你得到一个好的值。
NPC.rotation = NPC.rotation.AngleTowards(desiredRotation, 0.02f);
```

## 向量乘法
我们可以通过简单地将它们乘以一个float来缩放向量。我们通常缩放规范化向量，如上面`Vector2.Normalize`示例中那样。在那个示例中，我们有一个代表指向玩家方向的单位向量，我们打算向那个方向射弹。通过将那个向量乘以我们预期的射击速度，我们得到一个与之前方向相同但更长的向量。因为我们在`Projectile.NewProjectile`方法的`velocity`参数中使用那个`Vector2`，结果是弹射物将以所需速度在所需方向生成。

您也可以出于其他原因乘以向量。例如，在[ExampleAdvancedFlailProjectile.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleAdvancedFlailProjectile.cs#L328)中，我们将弹射物速度向量乘以`0.2f`或`0.4f`（`bounceFactor`变量），有效地将其速度削减到原始速度的一小部分。这给予连枷在瓦片上弹跳时厚重的质感。

## Vector2 长度
我们可以很容易地确定向量的长度，而不诉诸毕达哥拉斯定理。计算向量的长度在许多情况下非常有用。一个例子是一个直到玩家进入特定范围内才会射击的敌人。通过检查从敌人到玩家的向量的长度，我们可以让敌人决定是否生成弹射物。

```cs
// 我们可以使用Vector2的Length方法来确定现有Vector2的长度
Vector2 vectorFromNpcToPlayer = player.Center - NPC.Center;
float distanceBetweenPlayerAndNpc = vectorFromNpcToPlayer.Length();
// 或者，我们可以使用Vector2.Distance方法来计算2个现有Vector2之间的距离
float distanceBetweenPlayerAndNpc = Vector2.Distance(player.Center, NPC.Center);
// 然后，在您的逻辑中使用那个值
if(distanceBetweenPlayerAndNpc < 300) {
    // 玩家在范围内，在这里生成弹射物
}
```

### Vector2 长度平方
如果您正在做一些计算密集型的事情，比如迭代许多实体以找到最近的一个，您应该知道使用长度平方方法更高效。如果您愿意，在这种情况下使用`Vector2.DistanceSquared`或`Vector2.LengthSquared`更高效。

## Vector2 旋转
旋转向量可用于多种目的。一个常见的例子是给武器不精确性。通过获取原始`Vector2`并在其上调用`RotatedByRandom`方法，我们可以计算一个新的`Vector2`，该向量已经以提供的弧度最多旋转。请参阅[ExampleShotgun.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Weapons/ExampleShotgun.cs#L38)查看实际运行情况。请注意，结果分布不均匀，这对这个效果很有效。

我们也可以以非随机量旋转向量。`RotatedBy`方法可以做到这一点。例如，我们可以将一个向量旋转`MathHelper.Pi / 2`或`MathHelper.ToRadians(90)`来计算一个与原始向量垂直的向量。我们可以使用那个向量来创建一个分裂弹射物。通过重复旋转一个向量，我们可以计算几个代表弧形的向量。请参阅[ExampleGun.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Weapons/ExampleGun.cs#L79)查看在吸血鬼刀示例中实际运行情况。

// 其他？

# 随机向量
利用随机向量是向视觉和行为效果添加多样性的简单方法。有很多方法可以生成随机向量。下面的图表 accompanying the following approaches 显示了分布以及一个游戏中生成几个使用该方法的灰尘的示例来说明它们的行为。

## 圆内的随机向量
这种方法在模组制作者想要制作随机向量时最常见。结果分布良好。

```cs
// 普通方法
Vector2 speed = Main.rand.NextVector2Circular(1f, 1f);
// 只在弧内生成向量的方法。
Vector2 speed = Main.rand.NextVector2Unit((float)MathHelper.Pi / 4, (float)MathHelper.Pi / 2) * Main.rand.NextFloat();
// NextVector2Circular允许提供宽度和高度半径，对于椭圆分布
Vector2 speed = Main.rand.NextVector2Circular(0.5f, 1f);
```

![](https://i.imgur.com/RJZuLoA.png)     ![](https://i.imgur.com/N6XF5CZ.png)    ![](https://i.imgur.com/wGW3hFn.png)

https://github.com/tModLoader/tModLoader/assets/4522492/07341fea-ad1f-4418-b464-c0a28093e96d

https://github.com/tModLoader/tModLoader/assets/4522492/d330ee58-8bda-4cc6-b37c-16b71daf321b

https://github.com/tModLoader/tModLoader/assets/4522492/80677a4e-6719-4a75-9d20-9665415b4a49

## 沿圆边缘的随机向量
通过生成一个到达边缘的随机向量，模组制作者可以生成具有一致长度或大小的随机向量。

```cs
// 普通方法
Vector2 speed = Main.rand.NextVector2Unit();
// 可选参数允许指定旋转范围。在这个例子中，开始旋转是MathHelper.Pi / 4，最多可以比那多MathHelper.Pi / 2。
Vector2 speed = Main.rand.NextVector2Unit((float)MathHelper.Pi / 4, (float)MathHelper.Pi / 2);
```

https://github.com/tModLoader/tModLoader/assets/4522492/ada40247-d17c-4dc0-a7cd-87b531e0cd74

https://github.com/tModLoader/tModLoader/assets/4522492/35c9caa2-a907-4e97-91c7-731e62a317b5

## 正方形内的随机向量
很多旧的Terraria代码使用一种奇怪的方法来随机化向量。每个分量，X和Y，以以下方式随机生成：

```cs
float xSpeed = Main.rand.NextFloat(-1f, 1f);
float ySpeed = Main.rand.NextFloat(-1f, 1f);
// 另一种方法
Vector2 speed = Utils.RandomVector2(Main.rand, -1f, 1f);
```

粗略一看，这似乎应该可以正常工作，但实际上这种方法有一个奇怪的分布，可能不受欢迎，它实际上可以生成比预期更长的向量，延伸到假想正方形的角落。在下面的图片和视频中，注意形成的奇怪形状。

![](https://i.imgur.com/rmPZlwk.png)

https://github.com/tModLoader/tModLoader/assets/4522492/5bd85be8-15a8-4a1f-bea3-8a5d4a9e6809
## 正方形边缘的随机向量
不太有用。

# 示例
现在我们有了几何学的基础知识，并看到了促进这些知识的各种`Vector2`方法，我们终于可以使用几何学来编程实现模组中有趣的行为了。

在这些示例中，您可能会看到使用Dust或Projectiles来说明技术，但它们可以互换。只要记住查阅您正在使用的方法的方法签名以了解每个参数的用途。

## 生成随机爆发/圆圈的东西
这是上面随机向量部分使用的代码。在这个示例中，我们使用for循环来生成50个灰尘，每个都有沿圆边缘的随机向量。请注意，我们乘以向量5来缩放它并使其更大，导致灰尘移动相当距离。

```cs
for (int i = 0; i < 50; i++) {
	Vector2 speed = Main.rand.NextVector2CircularEdge(1f, 1f);
	Dust d = Dust.NewDustPerfect(Main.LocalPlayer.Top, DustID.BlueCrystalShard, speed * 5, Scale: 1.5f);
	d.noGravity = true;
}
```

我们可以使用一些简单的几何学来将生成位置从同一点移开。通过将`speed * 32`添加到`Main.LocalPlayer.Top`，灰尘开始在一个小圆圈中，然后从那里向外扩展，而不是全部从同一点开始。

```cs
Dust d = Dust.NewDustPerfect(Main.LocalPlayer.Top + speed * 32, DustID.BlueCrystalShard, speed * 2, Scale: 1.5f);
```

速度已经减少以更容易可视化效果。

https://github.com/tModLoader/tModLoader/assets/4522492/9b9913f2-afaa-487d-ab7c-df3b81739fcb
## 向目标射击
在[ExampleWormHead](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/NPCs/ExampleWorm.cs#L73)中，显示了敌人向玩家射弹的基本模式。

基本上，要向目标射击，我们计算从源头到目标的向量，规范化它，然后将那个规范化向量按某个射击速度缩放。确保只在攻击冷却、距离和视线检查通过时才调用此代码，如链接示例所示。

```cs
// 向目标射击的几何学：
Vector2 direction = (target.Center - NPC.Center).SafeNormalize(Vector2.UnitX); // 规范化的指向目标的向量
Vector2 velocity = direction * 7f; // 射击速度
Projectile.NewProjectile(..., velocity, ...); // 在适当的地方在NewProjectile中使用那个速度
```

## 面向目标
您可以使用[Vector2.ToRotation](https://github.com/tModLoader/tModLoader/wiki/Geometry#vector2torotation)来面向目标。如果您的实体纹理不是朝向右侧，您可能需要添加`MathHelper.Pi / 2`来额外旋转90度。还要注意有时弹射物或NPC在向左时会翻转精灵。这是通过`spriteDirection`布尔完成的。如果这个布尔为true，您可能需要添加额外的180度旋转来补偿，通过添加`MathHelper.Pi`：

```cs
// 在ModProjectile.AI末尾
if (Projectile.spriteDirection == -1) { 
	Projectile.rotation += MathHelper.Pi;
}
```

// TODO: 解释自动发生了什么，在弹射物和npc的各种方向和spriteDirection代码放在哪里

# 了解更多
掌握本指南后，学习碰撞可能会很有用。
// TODO: 制作碰撞指南。