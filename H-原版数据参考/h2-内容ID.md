# ID 指南

泰拉瑞亚使用数字来引用游戏中的各种内容。每个数字通常都有一个对应的 ID 名称。模组开发者可以使用这些 ID 名称轻松引用原版内容。例如，模组开发者可以输入 `Player.QuickSpawnItem(source, 22);` 在玩家位置生成一个铁锭。然而，`22` 很容易让人困惑，所以模组开发者可以改用 `Player.QuickSpawnItem(source, ItemID.IronBar);`。存在许多不同类型内容的 ID 类，如物品、弹射物、瓷砖等。使用任何 ID 类都需要在文件顶部添加 `using Terraria.ID;`。

请注意，这些 ID 的值可能会重叠。`ItemID.IronBar` 和 `TileID.Demonite` 的值都是 `22`。确保您正在使用正确的 ID 类来引用您尝试使用的内容类型非常重要。混淆这些将导致错误。例如，`Player.QuickSpawnItem(source, TileID.Demonite);` 会生成铁锭物品而不是矿石。

本页列出了使用不同类型 ID 的信息以及到官方泰拉瑞亚 wiki 实际值的链接。在这些列表中，通常有一个"内部名称"列，对应于模组开发者可以输入以获取数值实际 ID 名称。这些列表可以作为快速参考很有用，但在制作模组时不要浪费时间查找每个 ID。我们期望模组开发者使用 IDE 的自动完成功能来自动输入这些 ID。通常 ID 与物品的英文名称相同，因此自动完成应该足以满足模组制作的需求。在极少数情况下，您可能找不到您要查找的 ID，如果是这种情况，您可以使用 wiki 链接来发现实际的 ID 名称。另请注意，如果泰拉瑞亚最近更新而 tModLoader 尚未更新，则最近更新中添加的内容对应的新 ID 值将不存在于 tModLoader 中。

官方泰拉瑞亚 wiki 上的 Data IDs 页面包含许多内容 ID 列表的链接。本页的其余部分将深入介绍 tModLoader 模组制作中常用的内容 ID。

### 搜索网站
您可以使用 `Ctrl-f` 快捷键打开浏览器的查找功能。打开后，输入搜索词，匹配的结果将高亮显示。

### Count
大多数 ID 类都有一个 `Count` 字段，对应 ID 列表的结尾。如果您需要为每条内容运行一些代码，可以在 `for` 循环中从 `0` 迭代到 `SomethingID.Count`。

### Search
许多 ID 类有一个 `Search` 字段，其中包含可用于从相应数值获取 ID 名称的方法，反之亦然。这对于制作调试工具很有用。模组的内部名称也可以用相同的方式检索，但请记住模组 ID 可能在游戏重新加载之间发生变化。如果您尝试在游戏会话之间跟踪内容，请使用 `ItemDefinition` 等类。

### Sets
许多 ID 类中都包含一个名为 `Sets` 的内部类，这些类存储与内容对应的数据。例如，`TileID.Sets.HasOutlines` 集合跟踪哪些瓷砖有轮廓，提示游戏在悬停时加载和绘制轮廓。模组开发者通常在 `SetStaticDefaults` 方法中为这些 `Sets` 赋值。例如，模组开发者可以在其 `ModTile` 类的 `SetStaticDefaults` 方法中编写 `TileID.Sets.HasOutlines[Type] = true;` 来告诉游戏这个瓷砖有轮廓。请参阅 ExampleMod 了解其他 Sets 的各种用法。

# 物品 ID

[官方泰拉瑞亚 wiki 上的物品 ID 页面](https://terraria.wiki.gg/wiki/Item_IDs)

一些名称不明显的常见物品：`Luminite Bar` 实际上是 `LunarBar`，`Luminite Bullet` 实际上是 `MoonlordBullet`，`Sky Dragon's Fury` 实际上是 `MonkStaffT3`。

提醒一下，您可以通过编写 `ItemID.Name` 或 just `Number` 来使用这些值，**不要**编写 `ItemID.Number`：
```cs
recipe.AddIngredient(ItemID.Gel);  // 正确
recipe.AddIngredient(23);          // 可以运行，但难以阅读
recipe.AddIngredient(ItemID.23);  // 错误
```

# 弹射物 ID

[官方泰拉瑞亚 wiki 上的弹射物 ID 页面](https://terraria.wiki.gg/wiki/Projectile_IDs)

弹射物 ID 通常用于 `Projectile.NewProjectile` 方法中生成弹射物到世界中。许多弹射物有多个版本，名称中带有 `Hostile` 或 `Friendly`。这些表示弹射物是[友好的](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation#friendly)还是[敌对的](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation#hostile)，请使用与您预期的伤害行为相匹配的那个。如果没有您需要的版本的弹射物，您可能需要将自己的版本制作成 `ModProjectile`。

# 弹射物 AI 样式 ID

[tModLoader GitHub 上的 ProjAIStyleID.cs](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ID/ProjAIStyleID.cs)

弹射物 AI 样式（用于 `Projectile.aiStyle`）没有官方名称。tModLoader 中包含了 `Terraria.ID.ProjAIStyleID` 类，它为每个 AI 样式提供了名称。

# NPC ID

[官方泰拉瑞亚 wiki 上的 NPC ID 页面](https://terraria.wiki.gg/wiki/NPC_IDs)

负 ID 表示 NPC 与具有正 NPC 的 NPC 共享 `type`。如果您需要区分这些变体，您需要检查 `netID` 来确定正在检查哪个变体。

# NPC AI 样式 ID

[tModLoader GitHub 上的 NPCAIStyleID.cs](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/ID/NPCAIStyleID.cs)
[官方泰拉瑞亚 wiki 上的 AI 页面](https://terraria.wiki.gg/wiki/AI)

NPC AI 样式（用于 `NPC.aiStyle`）没有官方名称。tModLoader 中包含了 `Terraria.ID.NPCAIStyleID` 类，它为每个提供了名称。官方 wiki 有另一个包含更详细描述的列表。结合两个页面来找到所需的信息。

# 瓷砖 ID

[官方泰拉瑞亚 wiki 上的瓷砖 ID 页面](https://terraria.wiki.gg/wiki/Tile_IDs)

瓷砖 ID 表示瓷砖的类型。瓷砖 ID 对应于给定坐标处的 `Tile.TileType`。许多家具瓷砖在"子 ID"列中有一个数字，这对应于基础瓷砖指南中解释的 `style` 概念。例如，游戏中的大多数宝箱列为瓷砖 ID 21，但具有不同的"子 ID"/`style` 值来区分它们。

wiki 没有列出内部名称，这些可以在[原版瓷砖 ID](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Tile-IDs) 页面上找到。

# 墙壁 ID

[官方泰拉瑞亚 wiki 上的墙壁 ID 页面](https://terraria.wiki.gg/wiki/Wall_IDs)

墙壁 ID 用于 `Tile.WallType`。许多墙壁有 2 个变体，一个是不安全变体，一个是安全变体。安全变体是玩家放置的墙壁，不具有与不安全变体相同的敌人生成属性。

# 增益 ID

[官方泰拉瑞亚 wiki 上的增益 ID 页面](https://terraria.wiki.gg/wiki/Buff_IDs)

在 `Player.AddBuff` 和 `NPC.AddBuff` 等方法中使用这些。请注意，许多增益只影响玩家或敌人，而不是两者。如果泰拉瑞亚没有给敌人增益的方法，它可能不会对 NPC 编程。同样，对于从未应用于玩家的增益也是如此。

# 灰尘 ID

由于灰尘的性质，它们很难识别，请使用[查找原版灰尘](https://github.com/tModLoader/tModLoader/wiki/Basic-Dust#finding-vanilla-dust)指南来查找灰尘 ID。

# 声音 ID

[官方泰拉瑞亚 wiki 上的声音 ID 页面](https://terraria.wiki.gg/wiki/Sound_IDs)

声音 ID 可用于在模组中播放特定的现有声音。[基础声音指南中的适配原版代码部分](https://github.com/tModLoader/tModLoader/wiki/Basic-Sounds#adapting-vanilla-code-or-code-from-past-tmodloader-versions)介绍了如何使用 wiki 上的声音 ID 信息来查找和播放特定的泰拉瑞亚声音。

# 成就标识符

[官方泰拉瑞亚 wiki 上的成就页面](https://terraria.wiki.gg/wiki/Achievements)列出了原版成就。在定位 `ModAchievement` 时，您需要内部名称来引用正确的原版成就。虽然该 wiki 页面没有列出它们的内部名称，但可以通过取成就的名称、删除标点符号、将所有字母大写并将空格替换为下划线来派生内部名称。例如，"Ooo! Shiny!" 将变为 "OOO_SHINY"。完整的列表如下所示。

<details><summary>原版成就标识符</summary><blockquote>

以下是按顺序列出的所有原版成就：

```
TIMBER
BENCHED
NO_HOBO
OBTAIN_HAMMER
OOO_SHINY
HEART_BREAKER
HEAVY_METAL
I_AM_LOOT
STAR_POWER
HOLD_ON_TIGHT
EYE_ON_YOU
SMASHING_POPPET
WORM_FODDER
MASTERMIND
WHERES_MY_HONEY
STING_OPERATION
BONED
DUNGEON_HEIST
ITS_GETTING_HOT_IN_HERE
MINER_FOR_FIRE
STILL_HUNGRY
ITS_HARD
BEGONE_EVIL
EXTRA_SHINY
HEAD_IN_THE_CLOUDS
LIKE_A_BOSS
BUCKETS_OF_BOLTS
DRAX_ATTAX
PHOTOSYNTHESIS
GET_A_LIFE
THE_GREAT_SOUTHERN_PLANTKILL
TEMPLE_RAIDER
LIHZAHRDIAN_IDOL
ROBBING_THE_GRAVE
BIG_BOOTY
FISH_OUT_OF_WATER
OBSESSIVE_DEVOTION
STAR_DESTROYER
CHAMPION_OF_TERRARIA
BLOODBATH
SLIPPERY_SHINOBI
GOBLIN_PUNTER
WALK_THE_PLANK
KILL_THE_SUN
DO_YOU_WANT_TO_SLAY_A_SNOWMAN
TIN_FOIL_HATTER
BALEFUL_HARVEST
ICE_SCREAM
STICKY_SITUATION
REAL_ESTATE_AGENT
NOT_THE_BEES
JEEPERS_CREEPERS
FUNKITOWN
INTO_ORBIT
ROCK_BOTTOM
MECHA_MAYHEM
GELATIN_WORLD_TOUR
FASHION_STATEMENT
VEHICULAR_MANSLAUGHTER
BULLDOZER
THERE_ARE_SOME_WHO_CALL_HIM
DECEIVER_OF_FOOLS
SWORD_OF_THE_HERO
LUCKY_BREAK
THROWING_LINES
DYE_HARD
SICK_THROW
FREQUENT_FLYER
THE_CAVALRY
COMPLETELY_AWESOME
TIL_DEATH
ARCHAEOLOGIST
PRETTY_IN_PINK
RAINBOWS_AND_UNICORNS
YOU_AND_WHAT_ARMY
PRISMANCER
IT_CAN_TALK
WATCH_YOUR_STEP
MARATHON_MEDALIST
GLORIOUS_GOLDEN_POLE
SERVANT_IN_TRAINING
GOOD_LITTLE_SLAVE
TROUT_MONKEY
FAST_AND_FISHIOUS
SUPREME_HELPER_MINION
TOPPED_OFF
SLAYER_OF_WORLDS
YOU_CAN_DO_IT
MATCHING_ATTIRE
DEFEAT_EMPRESS_OF_LIGHT
DEFEAT_QUEEN_SLIME
DEFEAT_DREADNAUTILUS
DEFEAT_OLD_ONES_ARMY_TIER3
GET_ZENITH
GET_TERRASPARK_BOOTS
FLY_A_KITE_ON_A_WINDY_DAY
FOUND_GRAVEYARD
GO_LAVA_FISHING
TURN_GNOME_TO_STATUE
TALK_TO_NPC_AT_MAX_HAPPINESS
PET_THE_PET
FIND_A_FAIRY
THROW_A_PARTY
DIE_TO_DEAD_MANS_CHEST
DEFEAT_DEERCLOPS
GET_GOLDEN_DELIGHT
DRINK_BOTTLED_WATER_WHILE_DROWNING
GET_CELL_PHONE
GET_ANKH_SHIELD
GAIN_TORCH_GODS_FAVOR
PLAY_ON_A_SPECIAL_SEED
ALL_TOWN_SLIMES
TRANSMUTE_ITEM
PURIFY_ENTIRE_WORLD
TO_INFINITY_AND_BEYOND
```

此外，`ModAchievement` 还可以选择性地添加到成就顾问中，这是 UI，显示库存旁边建议的下一个成就。成就顾问包含成就的一个子集，主要关注游戏进度。它们的顺序也与成就菜单顺序不同，如下所列。

```
TIMBER
BENCHED
OBTAIN_HAMMER
NO_HOBO
YOU_CAN_DO_IT
OOO_SHINY
HEAVY_METAL
MATCHING_ATTIRE
HEART_BREAKER
I_AM_LOOT
HOLD_ON_TIGHT
STAR_POWER
EYE_ON_YOU
SMASHING_POPPET
WHERES_MY_HONEY
STING_OPERATION
BONED
DUNGEON_HEIST
ITS_GETTING_HOT_IN_HERE
MINER_FOR_FIRE
STILL_HUNGRY
ITS_HARD
BEGONE_EVIL
EXTRA_SHINY
HEAD_IN_THE_CLOUDS
BUCKETS_OF_BOLTS
DRAX_ATTAX
PHOTOSYNTHESIS
GET_A_LIFE
THE_GREAT_SOUTHERN_PLANTKILL
TEMPLE_RAIDER
LIHZAHRDIAN_IDOL
ROBBING_THE_GRAVE
OBSESSIVE_DEVOTION
STAR_DESTROYER
CHAMPION_OF_TERRARIA
```

</blockquote></details>


# 其他内容...
