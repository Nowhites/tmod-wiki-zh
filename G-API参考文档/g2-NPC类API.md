# NPC 类文档

此页面列出了与 `NPC` 类相关的方法和字段。此页面对于理解在 `ModNPC.SetDefaults` 和 `ModNPC.AI` 中使用什么很有用。

只列出了常用字段和方法。应查阅[完整文档](https://docs.tmodloader.net/docs/stable/class_n_p_c.html)以获取此处未列出的方法和字段的完整列表。一旦正确设置了 IDE，您也可以直接在您的 [IDE](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#documentation) 中查看这些方法和字段的名称和文档。

索引|
-----|
[字段](https://github.com/tModLoader/tModLoader/wiki/NPC-Class-Documentation#字段和属性)|
[方法](https://github.com/tModLoader/tModLoader/wiki/NPC-Class-Documentation#方法)|

# 字段和属性

您可以分配这些字段以为您的 `ModNPC` 提供各种值。通常，在为 `ModNPC.SetDefaults` 和 `ModNPC.AI` 编写代码时，您需要参考此页面。请务必查看[原版 NPC 字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-NPC-Field-Values)以查看原版 npc 对这些字段使用的值。除非另有说明，否则所有列出的字段都是 public。

| 字段 | 类型 | 默认值 | 描述 |
|----------|------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [width](#width) | int | 0 | NPC 碰撞箱的宽度 |
| [height](#height) | int | 0 | NPC 碰撞箱的高度 |
| [aiStyle](#aistyle) | int | 0（ModNPC 为 -1） | 确定运行哪个 AI 代码。许多 NPC 通过具有相同的 aiStyle 来共享相同的 AI 代码。ModNPC 除了 ModNPC.aiType 之外还可以利用原版 aiStyle 值来在某种程度上模仿原版行为。如果您希望进一步自定义 ModNPC 的 AI，您应该阅读[高级原版代码适配](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption)。如果您使用完全自定义的 AI，请将此设置为 -1，因为 aiStyle 为 0 会自动面向玩家，这可能会干扰您的逻辑。 |
| [damage](#damage) | int | 0 | 此 NPC 在碰撞时将造成的伤害量。当 NPC 在 AI 代码中使用 Projectile.NewProjectile 生成弹射物时，通常会按某个因子缩放 npc.damage。例如，传递 `(int)(npc.damage * 0.5f)` 作为 Damage 参数。 |
| [defDamage](#defdamage) | int | 0 | 在 SetDefaults 结束时存储 `damage` 的值。用于在 AI 代码中缩放伤害。 |
| [defense](#defense) | int | 0 | 它对伤害的抵抗能力。 |
| [defDefense](#defdefense) | int | 0 | 在 SetDefaults 结束时存储 `defense` 的值。用于在 AI 代码中缩放防御，例如史莱姆王在变小时会改变防御。 |
| [lifeMax](#lifemax) | int | 0 | 此 NPC 的最大生命值。 |
| [life](#life) | int | 0 | NPC 的当前生命值。在 SetDefaults 结束时自动分配给 lifeMax。 |
| [realLife](#reallife) | int | -1 | 此值用于多部分 npc，它们在维护独立碰撞箱的同时共享生命值。这对于蠕虫敌人很有用。该值表示 `Main.npc` 中具有此多部分 npc 实际生命的 npc 的索引。所有段都将指向同一个 npc，通常是 npc 的"头部"，头部和所有其他非多部分 npc 将仍然具有默认值 -1。 |
| [HitSound](#hitsound) | LegacySoundStyle | null | 此 npc 被击中时播放的声音。 |
| [DeathSound](#deathsound) | LegacySoundStyle | null | 此 npc 死亡时播放的声音。 |
| [alpha](#alpha) | int | 0 | 0 是不透明的，255 是透明的。这与计算机图形学中典型的 alpha 表示相反，因此请注意这一点。 |
| [color](#color) | Color | | |
| [value](#value) | float | 0f | NPC 死亡时将掉落多少铜币（100 铜币 = 1 银币等）。 |
| [rarity](#rarity) | int | 0 | NPC 对生命形式分析仪的稀有程度。Tim 和 模仿者 为 4，绑定的城镇 NPC 为 1。 |
| [immune](#immune) | int[] | 全部为 0 | 这决定了 NPC 是否可以被特定玩家拥有的物品或弹射物击中（它是一个数组，每个槽对应不同的玩家 (whoAmI)）。每次更新时它都会向 0 递减。近战物品将 immune[player.whoAmI] 设置为 player.itemAnimation，它从 item.useAnimation 开始向 0 递减。穿透弹射物通常将 immune 设置为 10，而非穿透弹射物不设置 immune。ExampleAnimatedPierce.cs 更多解释了 npc 和弹射物免疫的选项。 |
| [buffImmune](#buffimmune) | bool[] | 全部为 false | 使 NPC 对增益/减益免疫。示例：`npc.buffImmune[BuffID.OnFire] = true;` |
| [knockBackResist](#knockbackresist) | float | 1f | 它实际受到的击退量。1f：完全击退；0f：无击退。 |
| [scale](#scale) | float | 1f | 使 NPC 变大或变小。大于 1f = 更大。 |
| [townNPC](#townnpc) | bool | false | 如果为 true，npc 算作城镇 NPC。具有多种影响（即 npc 商店、住房、敌对生成率降低）。 |
| [noGravity](#nogravity) | bool | false | 如果为 true，npc 不会受到重力影响。恶魔眼和其他浮空 npc 使用此属性。 |
| [noTileCollide](#notilecollide) | bool | false | 如果为 true，npc 不会与瓷砖碰撞，使 npc 自由穿过瓷砖。流星头和蠕虫 npc 使用此属性。 |
| [npcSlots](#npcslots) | float | 1f | 表示此 npc 对 npc 生成限制的计数。小型生物如蠕虫或苍蝇的值约为 .1f 和 .25。比普通难度更大的敌人的值高于 1f，boss 和 mini boss 的值约为 6。适当的 npcSlots 值有助于平衡游戏并防止玩家被强大的敌人随机压倒。查看[原版 NPC 字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-NPC-Field-Values)电子表格以为您的 NPC 确定合适的值。 |
| [boss](#boss) | bool | false | 如果 NPC 是 boss 则设置为 true。防止离屏消失。 |
| [netAlways](#netalways) | bool | false | 如果为 true，确保此 NPC 始终在多人游戏中为新加入的玩家同步（如果是 boss 或 townNPC，也会发生相同情况）。 |

## 静态字段

静态字段通过类名访问，而不是实例访问。例如，我们编写 `NPC.downedPlantBoss` 来检查世纪之花是否已被击败。

| 字段 | 类型 | 默认值 | 描述 |
|----------|------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [killCount](#killcount) | int[] | 0 | 按 BannerID 索引。返回已击杀指定 BannerID 的击杀数量。许多 NPC 可以共享相同的 BannerID，许多 NPC 实际上根本没有 Banner ID。此代码将检索 NPC 的 bannerID，如果不存在则返回 -1：`int bannerID = Item.NPCtoBanner(npc.BannerID());` |
| [savedTaxCollector](#savedtaxcollector)、[savedGoblin](#savedgoblin)、[savedWizard](#savedwizard)、[savedMech](#savedmech)、[savedAngler](#savedangler)、[savedStylist](#savedstylist)、[savedBartender](#savedbartender) | bool | false | 这些值表示在野外找到了哪些 NPC。您可以在模组中的各种条件下使用这些值。 |
| [downedBoss1](#downedboss1)、[downedBoss2](#downedboss2)、[downedBoss3](#downedboss3)、[downedDeerclops](#downedDeerclops)、[downedQueenBee](#downedqueenbee)、[downedSlimeKing](#downedslimeking)、[downedGoblins](#downedgoblins)、[downedFrost](#downedfrost)、[downedPirates](#downedpirates)、[downedClown](#downedclown)、[downedPlantBoss](#downedplantboss)、[downedGolemBoss](#downedgolemboss)、[downedMartians](#downedmartians)、[downedFishron](#downedfishron)、[downedEmpressOfLight](#downedEmpressOfLight)、[downedHalloweenTree](#downedhalloweentree)、[downedHalloweenKing](#downedhalloweenking)、[downedChristmasIceQueen](#downedchristmasicequeen)、[downedChristmasTree](#downedchristmastree)、[downedChristmasSantank](#downedchristmassantank)、[downedAncientCultist](#downedancientcultist)、[downedMoonlord](#downedmoonlord)、[downedTowerSolar](#downedtowersolar)、[downedTowerVortex](#downedtowervortex)、[downedTowerNebula](#downedtowernebula)、[downedTowerStardust](#downedtowerstardust)、[downedTowers](#downedtowers)、[downedQueenSlime](#downedQueenSlime)、[downedMechBossAny](#... | bool | false | 这些值表示相应的 boss 是否已被击败。您可以在模组中的各种条件下使用这些值来确定游戏进度。 |

## 仅 tModLoader

| 字段 | 类型 | 默认值 | 描述 |
|----------|------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [modNPC](#modnpc) | ModNPC | null | 控制此 NPC 行为的 ModNPC 实例。如果这不是一个模组 NPC，则此属性为 null。 |
| [globalNPCs](#globalnpcs) | internal GlobalNPC[] | new GlobalNPC[0] | 不要触碰。使用 `NPC.GetGlobalNPC` |

# 方法

请记住，静态方法通过编写类名来调用，非静态方法使用实例名。`NPC.NewNPC(...)` vs `npc.CloneDefaults(...)`

### public static int NewNPC(IEntitySource spawnSource, int X, int Y, int Type, int Start = 0, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f, int Target = 255)

在世界生成 NPC。使用此方法从 boss 生成召唤物。有关 `spawnSource` 参数的信息，请参阅 [IEntitySource](https://github.com/tModLoader/tModLoader/wiki/IEntitySource)。
示例：`NPC.NewNPC((int)npc.position.X, (int)npc.position.Y, ModContent.NPCType<ExampleBossMinion>());` 如果需要用于特殊 AI 行为，可以使用 `ai` 参数初始化具有特定数据的 NPC。

### public static bool AnyNPCs(int Type)

如果世界中存在任何给定类型的存活 NPC，则返回 true。可用于 boss 生成物品或需要检查 NPC 是否存活的任何时候。
示例：`if (NPC.AnyNPCs(ModContent.NPCType<CaptiveElement2>())) {`

### public static int CountNPCS(int Type)

与 `AnyNPCs` 相同，只是返回给定类型的活跃 NPC 数量。

### public static int FindFirstNPC(int Type)

返回给定类型的第一个 NPC 的索引。如果未找到则返回 -1。用于索引到 `Main.npc` 以获取 NPC 实例。

### public static void SpawnOnPlayer(int plr, int Type)

在指定玩家的屏幕外某处生成 boss。这是生成 boss 最常见的方式。

## 仅 tModLoader

### public GlobalNPC GetGlobalNPC(Mod mod, string name)

获取与此 npc 实例关联的 GlobalNPC 实例（使用给定名称并由给定模组添加）。

### public T GetGlobalNPC\<T\>(Mod mod) where T : GlobalNPC

与其他 GetGlobalNPC 相同，但假定类名和内部名称相同。

### public T GetGlobalNPC\<T\>() where T : GlobalNPC

与其他 GetGlobalNPC 相同，但假定类名和内部名称相同，也假定 Mod。这是 99% 的情况下您应该使用的。

### public void CloneDefaults(int type)

允许您复制不同类型 NPC 的默认属性。
