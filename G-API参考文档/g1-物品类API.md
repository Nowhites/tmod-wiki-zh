# 物品类文档

此页面列出了与 `Item` 类相关的方法和字段。此页面对于理解在 `ModItem.SetDefaults` 中使用什么很有用。

只列出了常用的字段和方法。应查阅[完整文档](https://docs.tmodloader.net/docs/stable/class_item.html)以获取此处未列出的方法和字段的完整列表。一旦正确设置了 IDE，您也可以直接在您的 [IDE](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#documentation) 中查看这些方法和字段的名称和文档。

请注意，上面链接的完整文档和您的 IDE 中的文档将包含更多详细信息并且是最新的。此页面的信息是对重要字段的小部分选择，可能与更改不同步。

索引|
-----|
[字段](https://github.com/tModLoader/tModLoader/wiki/Item-Class-Documentation#字段和属性)|
[方法](https://github.com/tModLoader/tModLoader/wiki/Item-Class-Documentation#方法)|

# 字段和属性

您可以分配这些字段以为您的 `ModItem` 提供各种值。通常，在为 `ModItem.SetDefaults` 编写代码时，您需要参考此页面。请务必查看[原版物品字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Item-Field-Values)以查看原版物品对这些字段使用的值。除非另有说明，否则所有列出的字段都是 public。

| 字段 | 类型 | 默认值 | 描述 |
|----------|------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [value](#value) | int | 0 | Value 是物品价值多少铜币的数量（即从商人处购买的成本）。将其设置为 10462 意味着物品成本为 1 金币、4 银币和 62 铜币。物品的卖出价格是其价值的五分之一。Value 还会影响与地精工匠的重铸成本。为方便起见，您也可以使用 `Item.buyPrice()` 方法来设置值：`item.value = Item.buyPrice(0, 1, 4, 62);` 如果您希望以另一种方式考虑物品的值，也可以使用 `Item.sellPrice()` 方法。`Item.buyPrice(0, 0, 10, 55)` 和 `Item.sellPrice(0, 0, 2, 11)` 都会将值设置为 1055。 |
| [useStyle](#usestyle) | int | 0 | 物品的使用样式：<br>1 为挥动，<br>2 为饮用，<br>3 像短剑一样，<br>4 像生命水晶一样使用，<br>5 为使用法杖或枪 |
| [useTurn](#useturn) | bool | false | 玩家在使用动画进行时是否可以转身。 |
| [autoReuse](#autoreuse) | bool | false | 鼠标按钮按住时物品是否持续使用。 |
| [holdStyle](#holdstyle) | int | 0 | 物品的持有样式：<br>1 为持出（火把和荧光棒）<br>2 为向上持有（呼吸芦苇）<br>3 为不同版本的持出（魔法竖琴） |
| [useAnimation](#useanimation) | int | 100 | 物品使用动画的时间跨度。建议与 useTime 相同，因为这只是动画。方块使用 15。默认值为 100。泰拉瑞亚每秒运行 60 帧，所以 15 是四分之一秒。 |
| [useTime](#usetime) | int | 100 | 使用物品的时间跨度（以帧为单位）。方块使用 10。默认值为 100。武器通常具有相等的 useAnimation 和 useTime，这两个值不相等会导致每次点击多次攻击。请参阅 [ExampleGun.cs 的发条突击步枪示例](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Weapons/ExampleGun.cs#L105)。 |
| [reuseDelay](#reusedelay) | int | 0 | 在物品使用动画结束时添加的延迟（以帧为单位），在此期间玩家将无法使用任何物品。 |
| [consumable](#consumable) | bool | false | 物品在使用后是否被消耗。 |
| [rare](#rare) | int | 0 | 范围从 -1 到 13。<br>请查看 wiki 链接了解相应的颜色：https://terraria.wiki.gg/wiki/Rarity。为方便起见，您可以使用 `ItemRarityID`：[ItemRarityID.cs](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ID/ItemRarityID.cs)，[使用示例](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/ExampleHairDye.cs#L30) |
| [maxStack](#maxstack) | int | 1 | 单个堆叠中可以包含的最大物品数量。 |
| [width](#width) | int | 0 | 掉落物品碰撞箱的宽度（以像素为单位）。 |
| [height](#height) | int | 0 | 掉落物品碰撞箱的高度（以像素为单位）。 |
| [scale](#scale) | float | 1f | 物品在使用时精灵的大小乘数。也增加近战武器的范围。 |
| [createTile](#createtile) | int | -1 | 此物品在使用时放置的瓷砖的 ID。 |
| [placeStyle](#placestyle) | int | 0 | 正在放置的瓷砖的样式。用于根据用于放置它的物品而有不同外观的瓷砖。 |
| [createWall](#createwall) | int | -1 | 此物品在使用时放置的墙壁的 ID。 |
| [UseSound](#usesound) | LegacySoundStyle | null | 物品使用时发出的声音。<br>示例：`item.UseSound = SoundID.Item1;` |
| [damage](#damage) | int | -1 | 物品造成的 base 伤害。 |
| [knockBack](#knockback) | float | 0f | 击退的力量。最大值为 20。 |
| [shoot](#shoot) | int | 0 | 物品使用时发射的弹射物的 ID。如果此武器使用 useAmmo，则此值将被忽略，因为弹射物将由弹药物品决定，但按照惯例 shoot 应该仍然是 10。 |
| [shootSpeed](#shootspeed) | float | 0f | 物品发射的弹射物将具有的速度（以像素为单位）。实际速度取决于发射的弹射物。如果您的武器正在发射弹射物而它们是静止的，请将其更改为类似 10f 的值。飞刀使用 10f。像漩涡冲击者这样的持有弹射物使用 shootSpeed 来决定将弹射物保持在离玩家多远的地方。 |
| [noMelee](#nomelee) | bool | false | 如果为 true，物品的使用动画将不造成伤害。在大多数非剑武器上设置为 true。 |
| [accessory](#accessory) | bool | false | 物品是否是配饰。 |
| [melee](#melee)、[magic](#magic)、[ranged](#ranged)、[thrown](#thrown)、[summon](#summon) | bool | false | 物品造成的伤害类型（如果是武器）。 |
| [axe](#axe)、[pick](#pick)、[hammer](#hammer) | int | 0 | 这三个对应于 power，但 axe power 乘以 5，因此请相应调整。 |
| [defense](#defense) | int | 0 | 物品在装备时提供的防御量，无论是作为配饰还是护甲。 |
| [crit](#crit) | int | 0 | 此物品的基础暴击几率。请记住，玩家有 4 的基础暴击几率。 |
| [noUseGraphic](#nousegraphic) | bool | false | 如果为 true，物品的精灵在使用时将不可见。 |
| [useAmmo](#useammo) | int | AmmoID.None (0) | 此物品使用的弹药的 ID。请参阅[弹药指南](https://github.com/tModLoader/tModLoader/wiki/Basic-Ammo) |
| [ammo](#ammo) | int | AmmoID.None (0) | 此物品所属的弹药类别的 ID。请参阅[弹药指南](https://github.com/tModLoader/tModLoader/wiki/Basic-Ammo) |
| [notAmmo](#notammo) | bool | false | 如果为 true 且物品是弹药，该物品将不会对某些特定于弹药的行为计入弹药，例如 tooltip 提及物品是弹药，或者弹药物品在拾取时首先进入弹药槽。用于硬币物品、麦酒和导线。 |
| [mana](#mana) | int | 0 | 此物品使用时消耗的法力数量。 |
| [channel](#channel) | bool | false | 用于在按住攻击按钮时具有特殊行为的物品。 |
| [potion](#potion) | bool | false | 如果为 true，此物品将在使用时引发药水 sickness。还决定物品是否在玩家有 potion sickness 时无法使用，以及物品是否可以与快速治疗键一起使用。 |
| [healLife](#heallife) | int | 0 | 此物品在使用时治愈的生命数量。 |
| [healMana](#healmana) | int | 0 | 此物品在使用时治愈的法力数量。 |
| [buffType](#bufftype) | int | 0 | 此物品在使用时给予的增益的 ID。要让药水给予多个增益，请在此处分配一个增益，然后在 `ModItem.UseItem` 中调用 `player.AddBuff(buffID, time)` 以给予您希望给予的其他增益。确保也设置 [buffTime](#buffTime)，否则增益会立即消失。 |
| [buffTime](#bufftime) | int | 0 | 此物品在使用时给予的增益的持续时间（以刻为单位）。 |
| [expert](#expert) | bool | false | 表示此物品是专家模式物品。 |
| [bait](#bait)、[fishingPole](#fishingpole) | int | 0 | 分别对应于饵或鱼竿的钓鱼 power。 |
| [questItem](#questitem) | bool | false | 此物品的 tooltip 是否会提及它是任务物品。 |
| [mech](#mech) | bool | false | 此物品在持有时是否显示导线。 |
| [material](#material) | bool | false | 表示物品应被视为"材料"。这包括显示材料 tooltip 和支持将物品 shift-click 到工匠的"制作"菜单中。这将默认为 `ItemID.Sets.IsAMaterial` 的值，表明它至少用于一个未禁用的配方中。一些物品，例如硬币，手动将其设置为 false，以便物品不被视为材料。 |
| [backSlot](#backslot)、[balloonSlot](#balloonslot)、[bodySlot](#bodyslot)、[faceSlot](#faceslot)、[frontSlot](#frontslot)、[handOffSlot](#handoffslot)、[handOnSlot](#handonslot)、[headSlot](#headslot)、[legSlot](#legslot)、[neckSlot](#neckslot)、[shieldSlot](#shieldslot)、[shoeSlot](#shoeslot)、[waistSlot](#waistslot)、[wingSlot](#wingslot) | sbyte | -1 | 这些值表示配饰或护甲将显示的装备槽。这些槽对应于装备时显示的纹理。tModLoader 将根据 AutoloadEquip 属性自动为您的物品分配正确的值。 |

| 字段 | 类型 | 默认值 | 描述 |
|----------|------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [DD2Summon](#dd2summon) | bool | false | |
| [ToolTip](#tooltip) | ItemTooltip | null | |
| [active](#active) | bool | true | 如果为 false，此物品为空，无论设置了什么其他值。 |
| [alpha](#alpha) | int | 0 | |
| [buy](#buy) | bool | false | |
| [cartTrack](#carttrack) | bool | false | |
| [color](#color) | Color | Transparent | 使用彩色色调绘制物品精灵。凝胶和鲨鱼鳍使用此属性从相同的 ItemID 生成不同颜色的物品。如果不在 SetDefaults 中完成，则需要使用 `NetMessage.SendData(88, ...` 来同步此属性。 |
| [dye](#dye) | byte | 0 | |
| [expertOnly](#expertonly) | bool | false | 如果为 true，除非世界处于专家模式，否则配饰不会产生任何效果。 |
| [favorited](#favorited) | bool | false | 物品是否已被标记为在物品栏中收藏。 |
| [flame](#flame) | bool | false | |
| [glowMask](#glowmask) | short | -1 | |
| [hairDye](#hairdye) | short | -1 | |
| [instanced](#instanced) | bool | false | |
| [lavaWet](#lavawet) | bool | false | |
| [lifeRegen](#liferegen) | int | 0 | |
| [makeNPC](#makenpc) | short | 0 | 生成指定的 NPCID。 |
| [manaIncrease](#manaincrease) | int | 0 | |
| [mountType](#mounttype) | int | -1 | 指定在使用物品时装备哪个坐骑。请参阅 [ExampleMountItem](https://github.com/tModLoader/tModLoader/blob/1.4.4/ExampleMod/Content/Items/Mounts/ExampleMountItem.cs) |
| [netID](#netid) | int | 0 | 不要使用。 |
| [noWet](#nowet) | bool | false | |
| [paint](#paint) | byte | 0 | |
| [prefix](#prefix) | byte | 0 | |
| [release](#release) | int | 0 | |
| [sentry](#sentry) | bool | false | |
| [shopCustomPrice](#shopcustomprice) | int? | null | 在 `ModNPC/GlobalNPC.SetupShop` 中使用，为物品分配自定义价格，而不考虑 value 字段。与 `shopSpecialCurrency` 一起使用，以使用自定义货币而不是硬币。请参阅 [ExampleNPCShop.SetupShop](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Common/GlobalNPCs/ExampleNPCShop.cs#L17) |
| [shopSpecialCurrency](#shopspecialcurrency) | int | -1 | 与 [shopCustomPrice](#shopCustomPrice) 结合使用以指定自定义货币。请参阅 [ExampleMod.Load](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/ExampleMod.cs#L14) 和 [ExampleCustomCurrency](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Currencies/ExampleCustomCurrency.cs) |
| [stack](#stack) | int | 1 | 物品的当前堆叠数量。 |
| [tileBoost](#tileboost) | int | -1 | 指定工具的使用范围 |
| [tileWand](#tilewand) | int | 0 | |
| [type](#type) | int | 0 | 这是 ItemID，自动设置。 |
| [uniqueStack](#uniquestack) | bool | false | |
| [vanity](#vanity) | bool | false | 指定护甲是虚荣物品。 |
| [wet](#wet) | bool | false | |
| [wetCount](#wetcount) | int | 0 | |

## 静态字段

静态字段通过类名访问，而不是实例访问。例如，我们编写 `Item.staff[item.type]` 来检查物品是否被标记为法杖。在 `ModItem.SetStaticDefaults` 中设置这些值。

| 字段 | 类型 | 默认值 | 描述 |
|----------|------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [staff](#staff) | bool[] | false | 按 ItemID 索引。这使得 useStyle 作为法杖而不是枪进行动画处理。请参阅 [ExampleStaff](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Old/Items/Weapons/ExampleStaff.cs)。如果法杖不旋转，请确保 `Item.shootSpeed` 不为 0。 |

## 仅 tModLoader

| 字段 | 类型 | 默认值 | 描述 |
|----------|------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [modItem](#moditem) | ModItem | null | 控制此物品行为的 ModItem 实例。如果这不是一个模组物品，则此属性为 null。 |
| [globalItems](#globalitems) | internal GlobalItem[] | new GlobalItem[0] | 不要触碰。使用 `Item.GetGlobalItem` |

# 方法

请记住，静态方法通过编写类名来调用，非静态方法使用实例名。`Item.NewItem(...)` vs `item.CloneDefaults(...)`

### public static int NewItem(IEntitySource spawnSource, int X, int Y, int Width, int Height, int Type, int Stack = 1, bool noBroadcast = false, int pfix = 0, bool noGrabDelay = false, bool reverseLookup = false)

在世界生成物品。常见于 ModNPC.NPCLoot 中。X、Y、Width 和 Height 通常派生自 npc。有关 `spawnSource` 参数的信息，请参阅 [IEntitySource](https://github.com/tModLoader/tModLoader/wiki/IEntitySource)。不应在多人游戏客户端上调用此方法。如果您需要从客户端代码生成物品，请使用 `player.QuickSpawnItem`，它处理所需的多人游戏同步代码。
示例：`Item.NewItem((int)npc.position.X, (int)npc.position.Y, npc.width, npc.height, ModContent.ItemType<ExampleItem>());`

### public static int NewItem(IEntitySource spawnSource, Rectangle rectangle, int Type, int Stack = 1, bool noBroadcast = false, int prefixGiven = 0, bool noGrabDelay = false, bool reverseLookup = false)

此备用方法签名可以简化代码。
示例：`Item.NewItem(npc.getRect(), ModContent.ItemType<ExampleItem>());`

## 仅 tModLoader

### public GlobalItem GetGlobalItem(Mod mod, string name)

获取与此物品实例关联的 GlobalItem 实例（使用给定名称并由给定模组添加）。

### public T GetGlobalItem\<T\>(Mod mod) where T : GlobalItem

与其他 GetGlobalItem 相同，但假定类名和内部名称相同。

### public T GetGlobalItem\<T\>() where T : GlobalItem

与其他 GetGlobalItem 相同，但假定类名和内部名称相同，也假定 Mod。这是 99% 的情况下您应该使用的。

### public void CloneDefaults(int type)

允许您复制不同类型物品的默认属性。
