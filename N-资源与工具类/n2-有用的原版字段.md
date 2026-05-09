这是最常见的原版字段的快速列表以及更改时其作用的小描述。

索引|
-----|
[物品字段](https://github.com/tModLoader/tModLoader/wiki/Useful-Vanilla-Fields#item-fields)|
[主字段](https://github.com/tModLoader/tModLoader/wiki/Useful-Vanilla-Fields#main-fields)|
[NPC 字段](https://github.com/tModLoader/tModLoader/wiki/Useful-Vanilla-Fields#npc-fields)|
[玩家字段](https://github.com/tModLoader/tModLoader/wiki/Useful-Vanilla-Fields#player-fields)|
[弹射物字段](https://github.com/tModLoader/tModLoader/wiki/Useful-Vanilla-Fields#projectile-fields)|
[世界生成字段](https://github.com/tModLoader/tModLoader/wiki/Useful-Vanilla-Fields#worldgen-fields)|

# 物品字段

请参阅[物品类文档](https://github.com/tModLoader/tModLoader/wiki/Item-Class-Documentation#fields-and-properties)

# 主字段

对于 `Main.tileSolid` 和其他瓷砖相关字段，请参阅[基础瓷砖](https://github.com/tModLoader/tModLoader/wiki/Basic-Tile)。

* expertMode; bool

如果为 true，则表示世界处于专家模式。

* hardMode; bool

如果为 true，世界当前处于困难模式。

* rain; bool

如果为 true，世界当前正在下雨。

* bloodMoon; bool

如果为 true，当前正在发生血月。

* spawnTileX, spawnTileY; int

世界生成瓷砖的 X 和 Y 坐标。

* npcShop; int

如果大于 0，玩家当前已打开 NPC 商店。

# NPC 字段

请参阅[NPC 类文档](https://github.com/tModLoader/tModLoader/wiki/NPC-Class-Documentation#fields-and-properties)

# 玩家字段

* armor; Item[]

表示玩家的主要装备槽（不包括位于单独页面上的宠物、坐骑和钩爪槽）。
0-2：护甲（头部、胸部、腿部），3-7 + 额外槽（最多 9）：配饰，10-12：时装护甲（头部、胸部、腿部），13-17 + 额外槽（最多 19）时装配饰。

* dash; int

根据变量值启用冲刺能力。1 = 忍者大师装备冲刺。2 = 克苏鲁之眼护盾冲刺。3 = 太阳护甲冲刺。

* endurance; float (%)

伤害减免修正。0 表示无减免，1 是最大减免（最少受到 1 点伤害）。

* immune; bool

给予玩家免疫。他不会因被击中而受伤。

* immuneAlpha; int

设置为 0 以在免疫时防止闪烁。

* immuneTime; int

表示玩家免疫的时间（以刻为单位）（与 immune bool 一起使用）。

* inventory; Item[]

表示物品栏，包括热键栏、硬币和弹药槽。硬币槽为 50-53，弹药槽为 54-57。Main.maxInventory = 总共 58 个物品

* lifeRegen; int

增加玩家的生命恢复。该数字在 2 秒内应用。

* lifeRegenTime; int

以不同方式增加玩家的生命恢复。

* magicCrit; int

玩家的魔法暴击几率。

* magicDamage; float (%)

玩家的魔法伤害。

* manaCost; float (%)

降低值以降低法术的法力消耗。您也可以增加它们以使它们消耗更多法力。

* manaRegen; int

增加玩家再生的法力量。该量在 2 秒内添加。

* manaRegenBonus; int

增加玩家再生法力的速度。

* maxMinions; int

允许的最大召唤物数量，默认值为 1。

* maxRunSpeed; float

玩家可以加速到的最大移动速度。

* meleeCrit; int

玩家的近战暴击几率。

* meleeDamage; float (%)

玩家的近战伤害。

* meleeSpeed; float (%)

玩家的近战挥动速度。

* minionDamage; float (%)

玩家的召唤物伤害。

* minionKB; float (%)

玩家的召唤物击退强度。

* moveSpeed; float

玩家的移动速度。上限为 1.6。

* noKnockback; bool

如果为 true，玩家不会受到击退影响。

* noFallDmg; bool

如果为 true，玩家不会受到坠落伤害。

* numMinions; int

当前召唤物数量。通过标记为召唤物的弹射物设置。

* rangedCrit; int

玩家的远程暴击几率。

* rangedDamage; float (%)

玩家的远程伤害。

* runAcceleration; float

玩家达到最大移动速度的速度。

* runSlowdown; float

玩家停止移动时减速的速度。

* slotsMinions; float

当前填充的召唤物槽。通过标记为召唤物的弹射物设置。一些召唤物弹射物只算作半个召唤物槽（想想 Retanimini 和 Spazmamini）。

* statDefense; int

玩家的防御。

* statLifeMax; int

最大生命的基础量。除非您知道自己在做什么，否则不要更改它，请改用 statLifeMax2。
例如，这是生命之心增加的变量。它是永久性的，会自动保存。

* statLifeMax2; int

玩家的临时最大生命。
例如，这用于配饰或药水之类的东西。它是临时性的，在世界加载时重置。

* statManaMax; int

类似于 statLifeMax，但修改法力而非生命。

* statManaMax2; int

类似于 statLifeMax2，但修改法力而非生命。

* thrownCrit; int

玩家的投掷暴击几率。

* thrownDamage; float (%)

玩家的投掷伤害。

* wings; int

设置为当前装备的翅膀物品的 wingSlot 值。主要用于视觉翅膀反馈（灰尘生成等）。

* wingsLogic; int

设置为当前装备的翅膀物品的 wingSlot 值。用于确定 wingTimeMax 和额外的翅膀机制。

* wingTime; int

当前翅膀时间。当玩家使用翅膀时从中减去。着陆或被抓住时默认为 wingTimeMax。

* wingTimeMax; int

确定玩家未骑乘时翅膀的最大飞行时间。默认为 0。

# 弹射物字段

请参阅[弹射物类文档](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation#fields-and-properties)

# 世界生成字段

* crimson; bool

如果为 true，则为世界生成了血腥。检查 false 以代替腐化。
