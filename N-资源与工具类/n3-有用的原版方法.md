这是模组制作时有用的原版方法集合。此列表旨在作为最常用方法的快速参考，但对于更高级的模组，强烈建议将 Terraria 添加到 IDE 引用中，因为此列表并非详尽无遗。

## 物品
请参阅[物品类文档](https://github.com/tModLoader/tModLoader/wiki/Item-Class-Documentation#methods)

## 弹射物
请参阅[弹射物类文档](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation#methods)

## 灰尘
### public static int NewDust(Vector2 Position, int Width, int Height, int Type, float SpeedX = 0f, float SpeedY = 0f, int Alpha = 0, Color newColor = default(Color), float Scale = 1f)
在世界生成灰尘。

## 照明
### public static void AddLight(int i, int j, float R, float G, float B)
在提供的瓷砖坐标处向世界添加光照。

## 玩家
### public void QuickSpawnItem(int item, int stack = 1)
直接在玩家身上生成物品，是 Item.NewItem 的简单替代方案。
### public bool HasItem(int type)
确定玩家物品栏中是否有指定物品。示例：`if(player.HasItem(ModContent.ItemType<SpectreGun>())){...}`
### public void AddBuff(int type, int time1, bool quiet = true)
向玩家添加增益。
### public int FindBuffIndex(int type)
如果玩家没有该增益则返回 -1，如果有则返回索引（0 到 21）。

## NPC
### public static int NewNPC(int X, int Y, int Type, int Start = 0, float ai0 = 0f, float ai1 = 0f, float ai2 = 0f, float ai3 = 0f, int Target = 255)
在世界生成 NPC。示例：`NPC.NewNPC(i, j, ModContent.NPCType<PuritySpirit>());`
### public void AddBuff(int type, int time, bool quiet = false)
向 NPC 添加增益。示例：`Main.npc[i].AddBuff(BuffID.Ichor, 60);`
### public int FindBuffIndex(int type)
如果 NPC 没有该增益则返回 -1，如果有则返回索引（0 到 4）。示例：`if (npc.FindBuffIndex(BuffID.Cursed) >= 0){...}`

## 主类
### public static void PlaySound(int type, int x = -1, int y = -1, int Style = 1)
播放声音。type 是类别，style 是该类别内的声音。x 和 y 为声音提供位置，但可以保留为 -1 表示中心。
### public static void NewText(string newText, byte R = 255, byte G = 255, byte B = 255, bool force = false)
向控制台打印文本消息。

## 世界生成
### public static void TileRunner(int i, int j, double strength, int steps, int type, bool addTile = false, float speedX = 0f, float speedY = 0f, bool noYChange = false, bool overRide = true)
在世界生成期间生成矿石有用。此方法有很多原版瓷砖特定代码，但仍然有效。i 和 j 是坐标，type是要放置的瓷砖。

### public static void OreRunner(int i, int j, double strength, int steps, ushort type)
TileRunner 的更安全版本。原版将此方法用于困难模式矿石。只有 TileID.Sets.CanBeClearedDuringOreRunner 或 Main.tileMoss 数组中的瓷砖会被替换，保持宝箱、树和特殊瓷砖不被覆盖。
