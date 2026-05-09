# 弹射物类文档

此页面列出了与 `Projectile` 类相关的方法和字段。此页面对于理解在 `ModProjectile.SetDefaults` 和 `ModProjectile.AI` 中使用什么很有用。

只列出了常用的字段和方法。应查阅[完整文档](https://docs.tmodloader.net/docs/stable/class_projectile.html)以获取此处未列出的方法和字段的完整列表。一旦正确设置了 IDE，您也可以直接在您的 [IDE](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#documentation) 中查看这些方法和字段的名称和文档。

索引|
-----|
[字段](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation#字段和属性)|
[方法](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation#方法)|

# 字段和属性

您可以分配这些字段以为您的 `ModProjectile` 提供各种值。通常，在为 `ModProjectile.SetDefaults` 编写代码时，您需要参考此页面。请务必查看[原版弹射物字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Projectile-Field-Values)以查看原版弹射物对这些字段使用的值。除非另有说明，否则所有列出的字段都是 public。

## 原版
| 字段 | 类型 | 默认值 | 描述 |
|----------|------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [type](#type) | int | 0 | 这是 ProjectileID，自动设置。 |
| [aiStyle](#aistyle) | int | 0 | 选择对 AI 方法使用哪个原版代码。模组制作者可以使用原版 aiStyle 来模仿游戏中已有的 AI 代码。请参阅 [ModProjectile.AIType](http://docs.tmodloader.net/docs/stable/class_mod_projectile.html#ad09901454c49ee3a5fbf3c5f5671ac73) 和 [ExampleCloneProjectile](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleCloneProjectile.cs) 了解如何使用原版 ai。如果您使用自定义 AI 代码，可以省略此行。 |
| [width](#width) | int | 0 | 弹射物碰撞箱的宽度（以像素为单位）。 |
| [height](#height) | int | 0 | 弹射物碰撞箱的高度（以像素为单位）。 |
| [friendly](#friendly) | bool | false | 如果为 True，此弹射物将伤害敌人 (!npc.friendly) |
| [hostile](#hostile) | bool | false | 如果为 True，此弹射物将伤害玩家和友好的 npc (npc.friendly) |
| [maxPenetrate](#maxpenetrate) | int | 1 | 在死亡前可以击中多少个 npc。（或瓷砖反弹）自动在 SetDefaults 结束时设置为 penetrate 的当前值。 |
| [penetrate](#penetrate) | int | 1 | 当前穿透值。 |
| [alpha](#alpha) | int | 0 | 此弹射物的绘制透明度。0 到 255。255 完全透明。[ExampleBullet](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleBullet.cs) 将此设置为 255，并且弹射物的 aiStyle 为 1 会自动在每个刻减少 alpha，让弹射物在生成后快速淡入。对于由于纹理重叠而在武器上最初生成时看起来奇怪的弹射物很有用。 |
| [timeLeft](#timeleft) | int | 3600 | 每次更新 timeLeft 减少 1。一旦 timeLeft 达到 0，弹射物将自然消失。默认值 3600 以刻为单位测量，通常每秒 60 刻，所以默认消失时间约为 60 秒。如果您希望弹射物提前消散而不是无限飞行，请调整此值。请注意，extraUpdates 将导致它比正常时间更快减少，因为 Update 被调用得更频繁。 |
| [tileCollide](#tilecollide) | bool | true | 弹射物将与瓷砖碰撞，通常根据 `ModProjectile.OnTileCollide` 反弹或破坏瓷砖。[ExampleBullet](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleBullet.cs#L37) 展示了如何反弹。 |
| [ignoreWater](#ignorewater) | bool | false | 弹射物不会受到水的影响。 |
| [extraUpdates](#extraupdates) | int | 0 | 每个刻的额外更新步骤。对于非常快的弹射物很有用，例如[暗影光束法杖](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption#example-item-and-projectile-shadowbeam-staff-clone)。 |
| [scale](#scale) | float | 1f | 缩放弹射物绘制，但不缩放碰撞箱，因此不适合用于影响碰撞箱。 |
| [frame](#frame) | int | 0 | 此弹射物将使用的精灵表中的帧号。示例：弹射物有 4 帧，那么 `frame` 可以在 0 到 3 之间取值 |
| [frameCounter](#framecounter) | int | 0 | 用作计时器以决定何时更改 `frame` |
| [rotation](#rotation) | float | 0f | 弹射物的旋转角度。弧度而非角度。如果您想将角度转换为弧度，请使用 `MathHelper`。0 面向右，pi/2 朝下，以此类推。 |
| [oldPos](#oldpos) | Vector2[] | | 保存上一次更新的 `Projectile.position` 值。用于绘制轨迹。必须与 `ProjectileID.Sets.TrailCacheLength` 和 `ProjectileID.Sets.TrailingMode` 一起使用。请参阅 [ExampleBullet](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExampleBullet.cs) |
| [oldRot](#oldrot) | float[] | | 保存上一次更新的 `Projectile.rotation` 值。见上文。 |
| [oldSpriteDirection](#oldspritedirection) | int[] | | 保存上一次更新的 `Projectile.spriteDirection` 值。见上文。 |
| [ai](#ai) | float[] | 0, 0 | 用于任何类型数据存储的数组，偶尔会同步到服务器。调用 [netUpdate](#netUpdate) 手动同步。 |
| [localAI](#localai) | float[] | 0, 0 | 像 `ai` 一样运作，但不会同步到服务器。 |
| [noDropItem](#nodropitem) | bool | false | 如果您不希望此物品有机会回收发射它的弹药物品，则设置为 true。例如，如果您发射木箭弹射物，它有时会掉落木箭物品。如果您的武器每消耗 1 个弹药发射多支箭，您可能需要考虑将此字段设置为 true 以防止无限弹药错误。 |
| [minion](#minion) | bool | false | 表示此弹射物是召唤物 |
| [minionSlots](#minionslots) | float | 0f | 在召唤物上设置为 1f 以使其计入召唤玩家的召唤物限制（视幻之眼法杖同时召唤两个召唤物，每个为 0.5f） |
| [spriteDirection](#spritedirection) | int | | |
| [hide](#hide) | bool | | 弹射物通常不绘制。请参阅 (TODO) [ExampleBehindTilesProjectile](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Old/Projectiles/ExampleBehindTilesProjectile.cs#L22) |
| [lavaWet](#lavawet) | bool | false | 表示此弹射物当前在熔岩中 |
| [wetCount](#wetcount) | | | |
| [wet](#wet) | bool | false | 表示此弹射物当前在水中 |
| [netImportant](#netimportant) | bool | false | 表示此弹射物将同步到加入的玩家（默认情况下，玩家加入前活跃的任何弹射物（除了宠物）都不会同步）。示例：荧光棒 |
| [netUpdate](#netupdate) | bool | false | 在 `ModProjectile.AI` 中手动设置为 true 以使其将当前的 ai[] 数组同步到服务器和其他客户端（取决于在设置此值为 true 的地方 Main.netMode 是什么） |
| [netUpdate2](#netupdate2) | bool | false | 内部用于检查发出垃圾 `netUpdate` 的弹射物。不要自己手动使用它 |
| [numUpdates](#numupdates) | | | |
| [identity](#identity) | int | | 弹射物的通用唯一标识符，在所有客户端和服务器上都相同。通常用于在多个客户端和/或服务器上找到相同的弹射物，例如 `Projectile match = Main.projectile.FirstOrDefault(x => x.identity == identity);` |
| [light](#light) | float | 0f | 设置为高于 0f 的值可使此弹射物发出白光（数字越高：光越强。1f 比火把更强） |
| [position](#position) | Vector2 | | |
| [velocity](#velocity) | Vector2 | Vector2.Zero | 此弹射物每个刻移动的坐标量 |
| [active](#active) | bool | | 如果此弹射物实际存在则为 True。`Main.projectile` 将包含大量垃圾数据。如果您正在迭代 `Main.projectile`，请务必检查 `active` 以确保弹射物仍然存活。例如，死亡的旧弹射物不会从数组中移除，它们只是将 active 设置为 false。 |
| [owner](#owner) | | | 拥有此弹射物的玩家的索引。在多人游戏中，客户端"拥有"它们发射的弹射物，而服务器"拥有"由 NPC 和世界生成的弹射物。对于在 `ModProjectile.AI` 和一些其他方法中丢弃物品或生成弹射物的事情，检查 `if (projectile.owner == Main.myPlayer)` 非常重要，因为 `AI` 在所有客户端和服务器上同时运行。此检查屏蔽了某些仅应在所有者计算机上运行的代码。[ExamplePaperAirplaneProjectile](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Projectiles/ExamplePaperAirplaneProjectile.cs#L142) 检查所有者以生成回收的物品。如果您不这样做，您将在模组中遇到同步错误。 |
| [damage](#damage) | int | 0 | 这将始终在 `Projectile.NewProjectile` 中基于武器伤害设置。不要以为在 `SetDefaults` 中设置它会起到任何作用。 |
| [originalDamage](#originaldamage) | int | 0 | 在原版中为召唤物和哨兵手动设置，并在 tML 在 `ContinuouslyUpdateDamage` 为 true 时自动设置。导致弹射物每个刻更新其伤害（与通常的"快照"伤害设计相反） |
| [knockBack](#knockback) | float | 0f | |
| [trap](#trap) | bool | false | 如果为 true，此弹射物是由陷阱瓷砖生成的。 |
| [npcProj](#npcproj) | bool | false | 如果为 true，此弹射物是由友好的城镇 NPC 生成的。 |
| [projUUID](#projuuid) | | | |

## tModLoader

| 字段 | 类型 | 默认值 | 描述 |
|----------|------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [ArmorPenetration](#armorpenetration) | int | 0 | 此弹射物可以忽略的防御点数。不能设置为负值。在生成时，如果此弹射物是由武器发射的，则此值会加上制造弹射物的武器的总护甲穿透值。 |
| [CritChance](#critchance) | int | 0 | 此弹射物的暴击几率修正。不能设置为负值。在生成时，如果此弹射物是由武器发射的，则此值会加上制造弹射物的武器的总暴击几率。 |
| [DamageType](#damagetype) | DamageClass | DamageClass.Default | 弹射物的伤害类别，与伤害加成和暴击相关。原版选项可以通过 `DamageClass` 字段分配，模组通过 `ModContent.GetInstance<CustomDamageClassNameHere>()` |
| [ContinuouslyUpdateDamage](#continuouslyupdatedamage) | bool | false | 如果设置，则 `damage` 将基于 `originalDamage`、`DamageType` 和拥有玩家重新计算，就像召唤物和哨兵一样。 |
| [ModProjectile](#modprojectile) | ModProjectile | null | 控制此弹射物行为的 ModProjectile 实例。如果这不是一个模组弹射物，则此属性为 null。 |
| [Globals](#globals) | RefReadOnlyArray<Instanced<GlobalProjectile>> | | 不要触碰。使用 `Projectile.GetGlobalProjectile` |

# 方法

此处仅记录最基本的方法。其他记录的方法在您的 [IDE](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#documentation) 中悬停时会显示文档。

## 原版

请记住，静态方法通过编写类名来调用，非静态方法使用实例名。`Projectile.NewProjectile(...)` vs `someProjectileInstance.Kill(...)`

### public static int NewProjectile(IEntitySource spawnSource, float X, float Y, float SpeedX, float SpeedY, int Type, int Damage, float KnockBack, int Owner = 255, float ai0 = 0f, float ai1 = 0f)

在世界生成弹射物。owner 变量基本上始终应设置为 `Main.myPlayer`。有关 `spawnSource` 参数的信息，请参阅 [IEntitySource](https://github.com/tModLoader/tModLoader/wiki/IEntitySource)。

考虑多人游戏的正确用法：
* 如果由玩家生成（通过配饰、击中时、使用时等），只有此弹射物的"所有者"应该生成它：
```c#
if (Main.myPlayer == player.whoAmI)
{
    // 如果此代码在弹射物中运行，则 player.whoAmI 可以是 Projectile.owner
    Projectile.NewProjectile(...);
}
```

* 否则，如果没有玩家"拥有"弹射物（例如友好的或敌对的 NPC 射击它，或者世界本身生成它），则在服务器（或单人游戏）上生成它
```c#
if (Main.netMode != NetmodeID.MultiplayerClient)
{
    Projectile.NewProjectile(...);
}
```

在这两种情况下，这保证了 `NewProjectile` 仅在游戏的一个实例上运行，这样就不会生成重复的弹射物。

### public static int GetByUUID(int owner, int uuid)

不要使用此方法基于其 Projectile.identity 在客户端上获取弹射物。相反，在您希望找到弹射物的客户端上循环遍历 Main.projectile[] 并检查是否有具有与您要查找的弹射物相同 Projectile.identity 的弹射物。

## tModLoader

### public T GetGlobalProjectile\<T\>(bool exactType = true) where T : GlobalProjectile

获取与此弹射物实例关联的 GlobalProjectile 实例（指定类型）。

### public void CloneDefaults(int type)

允许您复制不同类型弹射物的默认属性。

### public bool CountsAsClass\<T\>() where T : DamageClass

如果弹射物计入给定伤害类别（指定类型）的影响，则返回 true。

### public bool CountsAsClass(DamageClass damageClass)

如果弹射物计入给定伤害类别的影响，则返回 true。
