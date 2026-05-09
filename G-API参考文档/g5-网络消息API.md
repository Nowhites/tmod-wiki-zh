# 此页面已过时

此页面的某些内容包含过时的 1.3 信息。最新的信息包含在 [NetMessage 文档](https://docs.tmodloader.net/docs/stable/class_net_message.html) 和 [MessageID 文档](https://docs.tmodloader.net/docs/stable/class_message_i_d.html) 页面中。

# NetMessage 类文档

此页面列出了与 `NetMessage` 类相关的方法。您可以查阅此页面以更好地了解 `NetMessage` 的工作原理。如果您想为模组创建自定义网络代码，此页面不相关，请参阅 [基础网络代码](https://github.com/tModLoader/tModLoader/wiki/Basic-Netcode) 和 [中级网络代码](https://github.com/tModLoader/tModLoader/wiki/Intermediate-netcode)。

索引|
-----|
[字段](#字段)|
[方法](#方法)|
[NetMessage.SendData()](#netmessagesenddata)|

# 字段

由于大部分信息存储在 `MessageBuffer` 实例中，`NetMessage` 没有使用太多字段。

| 字段 | 类型 | 描述 |
|---------|------|-------------|
| [buffer](#buffer) | MessageBuffer[257] | 所有传出/传入网络代码数据存储的位置。索引 256 对应此客户端的 NetMessage 使用，其余索引直接绑定到 `Main.player[]` 中的每个玩家和 `Netplay.Clients[]` 中的每个客户端。 |
| [_currentPlayerDeathReason](#_currentPlayerDeathReason) | PlayerDeathReason | 在发送玩家受伤/死亡网络代码数据时使用。 |

# 方法

所有网络代码数据都通过 `SendData()` 方法发送。但是，`NetMessage` 确实有多个用于更特定目的的其他方法。

## public static void SendChatMessageToClient(NetworkText text, Color color, int playerId)

向给定 `playerId` 的客户端玩家发送文本数据包。

## public static void BroadcastChatMessage(NetworkText text, Color color, int excludedPlayer = -1)

类似于 `SendChatMessageToClient()`，但数据包发送到所有玩家，并可选择忽略某个特定玩家。

## public static void SendChatMessageFromClient(ChatMessage text)

从此客户端的玩家向服务器发送文本数据包。

## public static void SendData(int msgType, int remoteClient = -1, int ignoreClient = -1, NetworkText text = null, int number = 0, float number2 = 0f, float number3 = 0f, float number4 = 0f, int number5 = 0, int number6 = 0, int number7 = 0)

将网络代码数据从此客户端发送到服务器或反之。在[下一节](#netmessagesenddata)中有更详细的介绍。

## public static void SendObjectPlacment(int whoAmi, int x, int y, int type, int style, int alternative, int random, int direction)

`NetMessage.SendData(MessageID.PlaceObject, remoteClient, ignoreClient, null, x, y, type, style, alternative, random, direction);` 的简写。
如果此消息从服务器发送，则 `remoteClient` 为 -1，`ignoreClient` 为 `whoAmi`，反之如果此消息从客户端发送则相反。

## public static void SendTemporaryAnimation(int whoAmi, int animationType, int tileType, int xCoord, int yCoord)

`NetMessage.SendData(MessageID.TemporaryAnimation, whoAmi, -1, null, animationType, tileType, xCoord, yCoord, 0, 0, 0);` 的简写。

## public static void SendTileRange(int whoAmi, int tileX, int tileY, int xSize, int ySize, TileChangeType changeType = TileChangeType.None)

使用此方法更新一定区域的瓷砖。比 `SendTileSquare()` 对指定区域有更多控制。
`NetMessage.SendData(MessageID.TileSquare, whoAmi, -1, null, number, tileX, tileY, 0f, (int)changeType, 0, 0);` 的简写。
(tileX, tileY) 是瓷砖方块的**左上角**。
`number` 设置为 `xSize` 和 `ySize` 的最大值。

## public static void SendTileSquare(int whoAmi, int tileX, int tileY, int size, TileChangeType changeType = TileChangeType.None)

使用此方法更新一定区域的瓷砖。
`NetMessage.SendData(MessageID.TileSquare, whoAmi, -1, null, size, tileX - num, tileY - num, 0f, (int)changeType, 0, 0);` 的简写。
(tileX, tileY) 是瓷砖方块的**中心**。
`num` 设置为 `(size - 1) / 2`。
"中心"偏向瓷砖方块的左上角。例如，传入 `tileX, tileY, size` 的 `10, 10, 4` 将使方块左上角的瓷砖位于 (9, 9)。但是，传入 `10, 10, 5` 将使左上角位于 (8, 8)，这是合理的，因为方块具有奇数边。

## public static void SendTravelShop(int remoteClient)

通知特定客户端旅行商人的当前商店。
如果此游戏是服务器实例，此方法是 `NetMessage.SendData(MessageID.TravelMerchantItems, remoteClient, -1, null, 0, 0f, 0f, 0f, 0, 0, 0);` 的简写。否则，它什么也不做。

## public static void SendAnglerQuest(int remoteClient)

通知所有客户端或特定客户端当前渔夫任务。如果此游戏不是服务器实例，此方法什么也不做。

## public static void SendSection(int whoAmi, int sectionX, int sectionY, bool skipSent = false)

用于为多人游戏目的加载地图的特定部分。部分是 200 x 150 瓷砖的"块"。
如果此游戏不是服务器实例，此方法什么也不做。
否则，如果给定地图部分未为客户端 `Netplay.Clients[whoAmi]` 加载，则加载该部分，内部的所有 NPC 会将该部分的信息发送到该客户端。

## public static void sendWater(int x, int y)

对于每个客户端，如果 `Main.tile[x, y]` 处的瓷砖位于客户端加载的地图部分之一中，则通知该客户端该瓷砖的液体属性已更改。

# NetMessage.SendData()

### public static void SendData(int msgType, int remoteClient = -1, int ignoreClient = -1, NetworkText text = null, int number = 0, float number2 = 0f, float number3 = 0f, float number4 = 0f, int number5 = 0, int number6 = 0, int number7 = 0)

`NetMessage.SendData()` 是 NetMessage 的上帝方法，意思是它可以做任何事情来发送网络代码数据。以下是每个有效消息类型的文档，它们的名称以及发送的数据：

## MessageID.NeverCalled (0)

未使用。简单明了。

## MessageID.Hello (1)

客户端连接到服务器的第一步。
*客户端状态：0 -> 1*
原版发送版本字符串 `$"Terraria{Main.curRelease}"`。tModLoader 改为发送版本字符串 `$"{ModLoader.versionedName}"`。
如果客户端被禁止或者客户端的版本字符串与服务器的版本字符串不匹配，客户端会立即被踢出并显示相应消息。
如果服务器有密码，客户端会发送 [MessageID.RequestPassword](#messageidrequestpassword-37) 消息。如果服务器没有密码，客户端会改为发送 [MessageID.LoadPlayer](#messageidloadplayer-3) 消息。
如果服务器接受原版客户端且客户端发送了原版版本字符串，tModLoader 会将客户端标记为原版客户端并使用未修改的协议进行通信。
如果客户端未标记为原版客户端，客户端会发送 [MessageID.SyncMods](#messageidsyncmods-251) 消息而不是 [MessageID.LoadPlayer](#messageidloadplayer-3) 消息。

| 类型 | 大小 | 描述 |
|------|------|-------------|
| byte | 1 | MessageID.ClientHello |
| string | ? | ModLoader.versionedName |
| byte, string | ? | NetMessage.SendData，消息类型 [MessageID.Kick](#messageidkick-2)；仅在客户端被禁止、客户端版本号与服务器版本号不匹配或如果客户端使用原版客户端且 `ModNet.AllowVanillaClients` 为 false 时发送 |
| byte, short, ? | ? | NetMessage.SendData，消息类型 [MessageID.LoadPlayer](#messageidloadplayer-3)；仅在服务器不使用密码时发送 |
| ? | ? | NetMessage.SendData，消息类型 [MessageID.RequestPassword](#messageidrequestpassword-37)；仅在服务器使用密码时发送 |

## MessageID.Kick (2)

踢除 `player.whoAmI` 等于 `remoteClient` 的客户端玩家，并给出 `text` 作为原因。

| 类型 | 大小 | 描述 |
|------|------|-------------|
| byte | 1 | MessageID.Kick |
| byte, string | ? | 用于准备发送数据的 `BinaryWriter` 信息以及踢出的 `text` 原因 |

## MessageID.PlayerInfo (3)

客户端连接到服务器的下两步。
*客户端状态：1 -> 2*
客户端发送 [MessageID.SyncPlayer](#messageidsyncplayer-4)、[MessageID.ClientUUID](#messageidclientuuid-68)、[MessageID.PlayerHealth](#messageidplayerhealth-16)、[MessageID.PlayerMana](#messageidplayermana-42)、[MessageID.PlayerBuffs](#messageidplayerbuffs-50) 以及各种 [MessageID.SyncEquipment](#messageidsyncequipment-5) 消息。
最终的 [MessageID.SyncEquipment](#messageidsyncequipment-5) 消息用于玩家主物品栏、装甲/配饰、装备染料、杂项配饰、杂项染料、猪银行库存、保险箱库存、垃圾物品槽和守卫者熔炉库存。
tModLoader 然后调用 `PlayerHooks.SyncPlayer()`。
然后客户端发送 [MessageID.RequestWorldInfo](#messageidrequestworldinfo-6) 消息。
*客户端状态：2 -> 3*

| 类型 | 大小 | 描述 |
|------|------|-------------|
| `byte` | 1 | MessageID.LoadPlayer |
| `byte` | 1 | remoteClient；客户端的 `player.whoAmI` |
| ? | ? | NetMessage.SendData，消息类型 [MessageID.SyncPlayer](#messageidsyncplayer-4) |
| ? | ? | NetMessage.SendData，消息类型 [MessageID.ClientUUID](#messageidclientuuid-68) |
| ? | ? | NetMessage.SendData，消息类型 [MessageID.PlayerHealth](#messageidplayerhealth-16) |
| ? | ? | NetMessage.SendData，消息类型 [MessageID.PlayerMana](#messageidplayermana-42) |
| ? | ? | NetMessage.SendData，消息类型 [MessageID.PlayerBuffs](#messageidplayerbuffs-50) |
| `byte`、`short`、? | 7 + 模组数据 | NetMessage.SendData，消息类型 [MessageID.SyncEquipment](#messageidsyncequipment-5)；为客户端玩家主物品栏中的每个物品调用 |
| `byte`、`short`、? | 7 + 模组数据 | NetMessage.SendData，消息类型 [MessageID.SyncEquipment](#messageidsyncequipment-5)；为客户端玩家的装甲和配饰槽中的每个物品调用 |
| `byte`、`short`、? | 7 + 模组数据 | NetMessage.SendData，消息类型 [MessageID.SyncEquipment](#messageidsyncequipment-5)；为客户端玩家装备的染料槽中的每个物品调用 |
| `byte`、`short`、? | 7 + 模组数据 | NetMessage.SendData，消息类型 [MessageID.SyncEquipment](#messageidsyncequipment-5)；为客户端玩家的杂项配饰槽（宠物、灯光宠物、钩爪、矿车、坐骑）中的每个物品调用 |
| `byte`、`short`、? | 7 + 模组数据 | NetMessage.SendData，消息类型 [MessageID.SyncEquipment](#messageidsyncequipment-5)；为客户端玩家的杂项染料槽（用于杂项配饰）中的每个物品调用 |
| `byte`、`short`、? | 7 + 模组数据 | NetMessage.SendData，消息类型 [MessageID.SyncEquipment](#messageidsyncequipment-5)；为客户端玩家的猪银行库存中的每个物品调用 |
| `byte`、`short`、? | 7 + 模组数据 | NetMessage.SendData，消息类型 [MessageID.SyncEquipment](#messageidsyncequipment-5)；为客户端玩家的保险箱库存中的每个物品调用 |
| `byte`、`short`、? | 7 + 模组数据 | NetMessage.SendData，消息类型 [MessageID.SyncEquipment](#messageidsyncequipment-5)；用于客户端玩家的垃圾物品槽 |
| `byte`、`short`、? | 7 + 模组数据 | NetMessage.SendData，消息类型 [MessageID.SyncEquipment](#messageidsyncequipment-5)；为客户端玩家的守卫者熔炉库存中的每个物品调用 |
| none | 0 | NetMessage.SendData，消息类型 [MessageID.RequestWorldInfo](#messageidrequestworldinfo-6) |

## MessageID.SyncPlayer (4)

发送关于玩家的信息。
`number` 参数是要同步信息的玩家。

| 类型 | 大小 | 描述 |
|------|------|-------------|
| `byte` | 1 | `number`，要同步信息的 `player.whoAmI`，转换为 `byte` |
| `byte` | 1 | `player.skinVariant`，转换为 `byte` |
| `byte` | 1 | `player.hair`，转换为 `byte` |
| `string` | ? | `player.name` |
| `int` | 4 | `player.hairDye`，仅在允许原版客户端**时** |
| `byte` | 4 | `player.hairDye`，仅在不允许原版客户端**时**，转换为 `byte` |
| `ushort` | 2 | `player.hideVisibleAccessory`，通常是 `bool[]` 但使用位移操作作为 `ushort` 发送 |
| `BitsByte` | 1 | `player.hideMisc` |
| `byte` | 1 | `player.hideMisc` |
| `Color` | 3 | `player.hairColor`，作为颜色的 `.R`、`.G` 和 `.B` 值的 3 个 `byte` 写入 |
| `Color` | 3 | `player.skinColor`，作为颜色的 `.R`、`.G` 和 `.B` 值的 3 个 `byte` 写入 |
| `Color` | 3 | `player.eyeColor`，作为颜色的 `.R`、`.G` 和 `.B` 值的 3 个 `byte` 写入 |
| `Color` | 3 | `player.shirtColor`，作为颜色的 `.R`、`.G` 和 `.B` 值的 3 个 `byte` 写入 |
| `Color` | 3 | `player.underShirtColor`，作为颜色的 `.R`、`.G` 和 `.B` 值的 3 个 `byte` 写入 |
| `Color` | 3 | `player.pantsColor`，作为颜色的 `.R`、`.G` 和 `.B` 值的 3 个 `byte` 写入 |
| `Color` | 3 | `player.shoeColor`，作为颜色的 `.R`、`.G` 和 `.B` 值的 3 个 `byte` 写入 |
| `BitsByte` | 1 | `player.difficulty` 和 `player.extraAccessory` 作为 `BitsByte` 发送 |
| `BitsByte` | 1 | `player.UsingBiomeTorches`、`player.happyFunTorchTime`、`player.unlockedBiomeTorches`、`player.unlockedSuperCart` 和 `player.enabledSuperCart` 作为 `BitsByte` 发送。 |
| `BitsByte` | 1 | `player.usedAegisCrystal`、`player.usedAegisFruit`、`player.usedArcaneCrystal`、`player.usedGalaxyPearl`、`player.usedGummyWorm`、`player.usedAmbrosia` 和 `player.ateArtisanBread` 作为 `BitsByte` 发送。 |

## MessageID.SyncEquipment (5)

发送关于玩家物品栏数组中某个槽的信息。
要处理的槽由下表决定：

| 索引 | 物品栏 |
|------------|----------|
| `0..58` | `player.inventory`（主物品栏槽） |
| `59..78` | `player.armor`（原版装甲、配饰和虚荣槽） |
| `79..88` | `player.dye`（原版染料槽） |
| `89..93` | `player.miscEquips`（宠物、灯光宠物、钩爪、矿车、坐骑槽） |
| `94..98` | `player.miscDyes`（`miscEquips` 物品的原版染料槽） |
| `99..138` | `player.bank`（猪银行槽） |
| `139..178` | `player.bank2`（保险箱槽） |
| `179` | `player.trashItem`（垃圾槽） |
| `180..219` | `player.bank3`（守卫者熔炉槽） |
| `220..` | `player.bank4`（虚空仓库槽） |

`number` 参数是玩家 `whoAmI` 以从中检索信息，`number2` 参数是要同步的槽。

为方便使用，以下是可用于使此消息更易读的常量列表：
```cs
public const int SyncMainInventory = 0;
public const int SyncArmorAndEquips = 59;
public const int SyncArmorDyes = 79;
public const int SyncMiscEquips = 89;
public const int SyncMiscEquipDyes = 94;
public const int SyncPiggyBank = 99;
public const int SyncSafe = 139;
public const int SyncTraskSlot = 179;
public const int SyncDefendersForge = 180;
public const int SyncVoidVault = 220;
```

示例：
```cs
// 给定一个玩家 "player" ...
// ... 同步玩家主物品栏中的第 5 个物品
NetMessage.SendData(MessageID.SyncEquipment, number: player.whoAmI, number2: SyncMainInventory + 4);

// ... 同步玩家猪银行中的第 11 个物品
NetMessage.SendData(MessageID.SyncEquipment, number: player.whoAmI, number2: SyncPiggyBank + 10);
```

**注意：** 对于提供超过 260（`SyncVoidVault` + 40 容量）的槽没有边界检查，因此如果槽太大，此消息将抛出异常。
此外，此消息不支持同步模组配饰槽。

## MessageID.RequestWorldData (6)

// 待办

## MessageID.WorldData (7)

向服务器和/或其他客户端发送有关世界的信息。
每当世界发生变化时使用此 `MessageID`，包括但不限于时间和天气。

| 类型 | 大小 | 描述 |
|----------|------|------------
| `int` | 4 | `Main.time` |
| `byte` | 1 | 设置为 `Main.dayTime`、`Main.bloodMoon` 和 `Main.eclipse` 值的 `BitsByte` |
| `byte` | 1 | `Main.moonPhase` |
| `short` | 2 | `Main.maxTilesX` |
| `short` | 2 | `Main.maxTilesY` |
| `short` | 2 | `Main.spawnTileX` |
| `short` | 2 | `Main.spawnTileY` |
| `short` | 2 | `Main.worldSurface` |
| `short` | 2 | `Main.rockLayer` |
| `int` | 4 | `Main.worldID` |
| `string` | ? | `Main.worldName` |
| `byte[]` | ? | `Main.ActiveWorldFileData.UniqueId.ToByteArray()` 的返回值 |
| `ulong` | 8 | `Main.ActiveFileData.WorldGeneratorVersion` |
| `byte` | 1 | `Main.moonType` |
| `byte` | 1 | `WorldGen.treeBG` |
| `byte` | 1 | `WorldGen.corruptBG` |
| `byte` | 1 | `WorldGen.snowBG` |
| `byte` | 1 | `WorldGen.hallowBG` |
| `byte` | 1 | `WorldGen.crimsonBG` |
| `byte` | 1 | `WorldGen.desertBG` |
| `byte` | 1 | `WorldGen.oceanBG` |
| `byte` | 1 | `Main.iceBackStyle` |
| `byte` | 1 | `Main.jungleBackStyle` |
| `byte` | 1 | `Main.hellBackStyle` |
| `float` | 4 | `Main.windSpeedSet` |
| `byte` | 1 | `Main.numClouds` |
| `int` | 12 | `Main.treeX[]` 的内容 |
| `byte` | 4 | `Main.treeStyle[]` 的内容，每个转换为 `byte` |
| `int` | 12 | `Main.caveBackX[]` 的内容 |
| `byte` | 4 | `Main.caveBackStyle[]` 的内容，每个转换为 `byte` |
| `float` | 4 | `Main.maxRaining` |
| `byte` | 1 | 设置为 `WorldGen.shadowOrbSmashed`、`NPC.downedBoss1`、`NPC.downedBoss2`、`NPC.downedBoss3`、`Main.hardMode`、`NPC.downedClown` 和 `NPC.downedPlantBoss` 值的 `BitsByte` |
| `byte` | 1 | 设置为 `NPC.downedMechBoss1`、`NPC.downedMechBoss2`、`NPC.downedMechBoss3`、`NPC.downedMechBossAny`、`Main.cloudBGActive >= 1f`、`WorldGen.crimson`、`Main.pumpkinMoon` 和 `Main.snowMoon` 值的 `BitsByte` |
| `byte` | 1 | 设置为 `Main.expertMode`、`Main.fastForwardTime`、`Main.slimeRain`、`NPC.downedSlimeKing`、`NPC.downedQueenBee`、`NPC.downedFishron`、`NPC.downedMartian` 和 `NPC.downedAncientCultist` 值的 `BitsByte` |
| `byte` | 1 | 设置为 `NPC.downedMoonlord`、`NPC.downedHalloweenKing`、`NPC.downedHalloweenTree`、`NPC.downedChristmasIceQueen`、`NPC.downedChristmasSantank`、`NPC.downedChristmasTree`、`NPC.downedGolemBoss` 和 `BirthdayParty.PartyIsUp` 值的 `BitsByte` |
| `byte` | 1 | 设置为 `NPC.downedPirates`、`NPC.downedFrost`、`NPC.downedGoblins`、`Sandstorm.Happening`、`DD2Event.Ongoing`、`DD2Event.DownedInvasionT1`、`DD2Event.DownedInvasionT2` 和 `DD2Event.DownedInvasionT3` 值的 `BitsByte` |
| `sbyte` | 1 | `Main.invasionType` |
| ? | ? | 调用 `WorldIO.SendModData(BinaryWriter)` 的结果。`ModSystem.NetSend(BinaryWriter)` 在此处调用。 |
| `ulong` | 8 | 如果 `SocialAPI.Network` 不为 `null`，则写入 `SocialAPI.Network.GetLobbyId()` 的结果。否则，改为写入 `0`。 |
| `float` | 4 | `Sandstorm.IntendedSeverity` |

发送此网络消息有以下副作用：
- 如果 `Main.raining` 为 `false`，则在写入 `BinaryWriter` 之前 `Main.maxRaining` 设置为 `0f`。

## MessageID.SpawnTileData (8)

// 待办

## MessageID.StatusTextSize (9)

// 待办

## MessageID.TileSection (10)

// 待办

## MessageID.TileFrameSection (11)

已弃用。框架在 TileSection 发送后根据需要处理。

## MessageID.PlayerSpawn (12)

// 待办

## MessageID.PlayerControls (13)

// 待办

## MessageID.PlayerActive (14)

// 待办

## MessageID.Unused15 (15)

未使用。简单明了。

## MessageID.PlayerLifeMana (16)

// 待办

## MessageID.TileManipulation (17)

// 待办

## MessageID.SetTime (18)

未使用。简单明了。
不太确定为什么主菜单信息需要通过服务器发送。

## MessageID.ToggleDoorState (19)

// 待办

## MessageID.TileSquare (20)

// 待办

## MessageID.SyncItem (21)

发送掉落物品 `Main.item[number]` 的信息。如果目标物品不是 `active`，则此 MessageID 可用于通知其他客户端该物品已消失。
`number2` 参数在 `Item.NewItem()` 中从服务器发送消息时用于 `noGrabDelay` 是否为 true。否则，可以忽略。

示例：
```cs
int item = Item.NewItem(/* args */);

// 在此处直接修改 Main.item[item] 的代码...

if(Main.netMode != NetmodeID.Singleplayer)
    NetMessage.SendData(MessageID.SyncItem, number: item);
```

## MessageID.ItemOwner (22)

// 待办

## MessageID.SyncNPC (23)

// 待办

## MessageID.UnusedStrikeNPC (24)

未使用。简单明了。
最有可能未使用，因为它直接使用物品的伤害和击退，而不是通常的方差。

## MessageID.ChatText (25)

已过时。请改为使用 `NetMessage` 中的[聊天消息方法](#public-static-void-sendchatmessagetoclientnetworktext-text-color-color-int-playerid)或使用 `NetTextModule` 类。

## MessageID.HurtPlayer (26)

已过时。请改用 [MessageID.PlayerHurtV2](#messageidplayerhurtv2-117)。

## MessageID.SyncProjectile (27)

// 待办

## MessageID.DamageNPC (28)

// 待办

## MessageID.KillProjectile (29)

// 待办

## MessageID.TogglePVP (30)

// 待办

## MessageID.RequestChestOpen (31)

// 待办

## MessageID.SyncChestItem (32)

同步 `Main.chest[number].item[number2]` 处的物品。此消息通常在 `MessageID.ChestUpdates` 内发送，但它仍然可以正常工作。

示例：
```cs
Point16 position = /* 某个瓷砖位置 */;
//FindByGuessing 在以目标位置为中心的 3x3 区域内搜索附近的瓷砖
//调用将返回在此目标位置 2x2 瓷砖边界内具有宝箱的索引
int chest = Chest.FindByGuessing(position.X, position.Y);

if(chest > -1){
    Item item = Main.chest[chest].item[20] = new Item();
    item.SetDefaults(ItemID.DirtBlock);
    item.stack = 20;

    if(Main.netMode != NetmodeID.Singleplayer)
        NetMessage.SendData(MessageID.SyncChestItem, number: chest, number2: 20, number3: 0);
}
```

## MessageID.SyncPlayerChest (33)

// 待办

## MessageID.ChestUpdates (34)

// 待办

## MessageID.PlayerHeal (35)

// 待办

## MessageID.SyncPlayerZone (36)

// 待办

## MessageID.RequestPassword (37)

// 待办

## MessageID.SendPassword (38)

// 待办

## MessageID.ReleaseItemOwnership (39)

// 待办

## MessageID.SyncTalkNPC (40)

// 待办

## MessageID.ShotAnimationAndSound (41)

// 待办

## MessageID.PlayerMana (42)

// 待办

## MessageID.ManaEffect (43)

// 待办

## MessageID.KillPlayer (44)

已过时。请改用 [MessageID.PlayerDeathV2](#messageidplayerdeathv2-118)。

## MessageID.PlayerTeam (45)

// 待办

## MessageID.RequestReadSign (46)

// 待办

## MessageID.ReadSign (47)

// 待办

## MessageID.LiquidUpdate (48)

已过时。请改用 [MessageID.NetModules](#messageidnetmodules-82)。

## MessageID.InitialSpawn (49)

// 待办

## MessageID.PlayerBuffs (50)

// 待办

## MessageID.MiscDataSync (51)

// 待办

## MessageID.LockAndUnlock (52)

// 待办

## MessageID.AddNPCBuff (53)

// 待办

## MessageID.NPCBuffs (54)

// 待办

## MessageID.AddPlayerBuff (55)

// 待办

## MessageID.UniqueTownNPCInfoSyncRequest (56)

// 待办

## MessageID.TileCounts (57)

// 待办

## MessageID.InstrumentSound (58)

根据玩家位置持有的物品播放竖琴或铃声。`Main.harpNote = pitch` 用于音高。

## MessageID.HitSwitch (59)

// 待办

## MessageID.NPCHome (60)

// 待办

## MessageID.SpawnBossUseLicenseStartEvent (61)

// 待办

## MessageID.Dodge (62)

// 待办

## MessageID.PaintTile (63)

// 待办

## MessageID.PaintWall (64)

// 待办

## MessageID.TeleportEntity (65)

// 待办

## MessageID.SpiritHeal (66)

由幽灵兜帽护甲套装奖励的弹射物球体使用。增加玩家的生命并在其他客户端显示战斗文本。
`number` 是要治愈的玩家的 `Player.whoAmI`，`number2` 是要治愈玩家的量。

如果 `number2` 不大于零，则在接收客户端上不会发生任何事情。

示例：
```cs
// 此示例在弹射物的 AI 中展示弹射物治愈其所有者玩家：
int plr = Projectile.owner;
Player player = Main.player[plr];

if (plr == Main.myPlayer && !player.moonLeech) {
    int amount = 50;

    // Player.Heal() 不应在此处使用，因为它会广播额外的 HealEffect 调用
    player.statLife += amount;
    if (player.statLife > player.statLifeMax2)
        player.statLife = player.statLifeMax2;

    player.HealEffect(amount, broadcast: false);

    NetMessage.SendData(MessageID.SpiritHeal, number: plr, number2: amount);
}
```

**注意：** 此消息 ID 不限于弹射物。您也可以使用它来简单地同步玩家生命增加。

## MessageID.Unknown67 (67)

未使用。简单明了。

## MessageID.ClientUUID (68)

// 待办

## MessageID.ChestName (69)

// 待办

## MessageID.BugCatching (70)

// 待办

## MessageID.BugReleasing (71)

// 待办

## MessageID.TravelMerchantItems (72)

// 待办

## MessageID.RequestTeleportationByServer (73)

// 待办

## MessageID.AnglerQuest (74)

// 待办

## MessageID.AnglerQuestFinished (75)

// 待办

## MessageID.QuestsCountSync (76)

// 待办

## MessageID.TemporaryAnimation (77)

// 待办

## MessageID.InvasionProgressReport (78)

// 待办

## MessageID.PlaceObject (79)

// 待办

## MessageID.SyncPlayerChestIndex (80)

// 待办

## MessageID.CombatTextInt (81)

// 待办

## MessageID.NetModules (82)

// 待办

## MessageID.NPCKillCountDeathTally (83)

// 待办

## MessageID.PlayerSealth (84)

// 待办

## MessageID.QuickStackChests (85)

// 待办

## MessageID.TileEntitySharing (86)

尝试发送瓦片实体的数据（如果提供的 ID 存在）。数据通过 `TileEntity.Write(BinaryWriter, TileEntity, bool)` 发送，该方法调用 `ModTileEntity.NetSend(BinaryWriter, bool)`。

示例：
```cs
Point16 tilePosition = new Point16(i, j);
ModTileEntity entity = ModContent.GetInstance<SomeModTileEntity>();

// 如果给定位置没有实体，则放置一个并发送网络消息
if(entity.Find(tilePosition.X, tilePosition.Y) < 0){
    int id = entity.Place(tilePosition.X, tilePosition.Y);

    if(Main.netMode == NetmodeID.MultiplayerClient)
        NetMessage.SendData(MessageID.TileEntitySharing, remoteClient: -1, ignoreClient: Main.myPlayer, number: id);
}
```

如果发送消息的客户端中 ID 不再存在（通过 `TileEntity.ByID.Remove(int)`），但在其他客户端上存在，则该瓦片实体将在这些客户端上被移除。

示例：
```cs
Point16 tilePosition = new Point16(i, j);
if(TileEntity.ByPosition.TryGetValue(tilePosition, out TileEntity entity)){
    // 假设该 TileEntity 实际上是 ModTileEntity，如果您使用此代码则应该是这种情况
    ModTileEntity existing = entity as ModTileEntity;
    // 如果 (i, j) 处存在实体则杀死它
    existing.Kill(i, j);
    
    // 发送网络消息
    if(Main.netMode == NetmodeID.MultiplayerClient)
        NetMessage.SendData(MessageID.TileEntitySharing, remoteClient: -1, ignoreClient: Main.myPlayer, existing.ID);
}
```

## MessageID.TileEntityPlacement (87)

// 待办

## MessageID.ItemTweaker (88)

// 待办

## MessageID.ItemFrameTryPlacing (89)

// 待办

## MessageID.InstancedItem (90)

// 待办

## MessageID.SyncEmoteBubble (91)

// 待办

## MessageID.SyncExtraValue (92)

通过将其设置为 `number2` 并在客户端上在世界位置 `(number3, number4)` 播放"ping"声音来同步 `Main.npc[number].extraValue`。

示例：
```cs
// "npc" 是一个 NPC 实例，"position" 是一个 Vector2，其中应显示"拾取"视觉
int givenCoins = 50;  // 50 铜币
npc.extraValue += givenCoins;

if(Main.netMode == NetmodeID.Singleplayer)
    npc.moneyPing(position);
else
    NetMessage.SendData(MessageID.SendExtraValue, number: npc.whoAmI, number2: givenCoins, number3: position.X, number4: position.Y);
```

## MessageID.SocialHandshake (93)

// 待办

## MessageID.Deprecated1 (94)

未使用。简单明了。

## MessageID.MurderSomeoneElsesPortal (95)

// 待办

## MessageID.TeleportPlayerThroughPortal (96)

// 待办

## MessageID.AchievementMessageNPCKilled (97)

// 待办

## MessageID.AchievementMessageEventHappened (98)

// 待办

## MessageID.MinionRestTargetUpdate (99)

// 待办

## MessageID.TeleportNPCThroughPortal (100)

// 待办

## MessageID.UpdateTowerShieldStrengths (101)

// 待办

## MessageID.NebulaLevelupRequest (102)

// 待办

## MessageID.MoonlordHorror (103)

// 待办

## MessageID.ShopOverride (104)

// 待办

## MessageID.GemLockToggle (105)

// 待办

## MessageID.PoofOfSmoke (106)

// 待办

## MessageID.SmartTextMessage (107)

// 待办

## MessageID.WiredCannonShot (108)

// 待办

## MessageID.MassWireOperation (109)

// 待办

## MessageID.MassWireOperationPay (110)

// 待办

## MessageID.ToggleParty (111)

// 待办

## MessageID.SpecialFX (112)

// 待办

## MessageID.CrystalInvasionStart (113)

// 待办

## MessageID.CrystalInvasionWipeAllTheThingsss (114)

不，这不是拼写错误。常量实际上被称为 `CrystalInvasionWipeAllTheThingsss`。
// 待办

## MessageID.MinionAttackTargetUpdate (115)

// 待办

## MessageID.CrystalInvasionSendWaitTime (116)

// 待办

## MessageID.PlayerHurtV2 (117)

// 待办

## MessageID.PlayerDeathV2 (118)

// 待办

## MessageID.CombatTextString (119)

// 待办

## MessageID.Emoji (120)

// 待办

## MessageID.TEDisplayDollItemSync (121)

// 待办

## MessageID.RequestTileEntityInteraction (122)

// 待办

## MessageID.WeaponsRackTryPlacing (123)

// 待办

## MessageID.TEHatRackItemSync (124)

// 待办

## MessageID.SyncTilePicking (125)

// 待办

## MessageID.SyncRevengeMarker (126)

// 待办

## MessageID.RemoveRevengeMarker (127)

// 待办

## MessageID.LandGolfBallInCup (128)

// 待办

## MessageID.FinishedConnectingToServer (129)

// 待办

## MessageID.FishOutNPC (130)

// 待办

## MessageID.TamperWithNPC (131)

// 待办

## MessageID.PlayLegacySound (132)

// 待办

## MessageID.FoodPlatterTryPlacing (133)

// 待办

## MessageID.UpdatePlayerLuckFactors (134)

// 待办

## MessageID.DeadPlayer (135)

// 待办

## MessageID.SyncCavernMonsterType (136)

// 待办

## MessageID.RequestNPCBuffRemoval (137)

// 待办

## MessageID.ClientSyncedInventory (138)

// 待办

## MessageID.SetCountsAsHostForGameplay (139)

// 待办

## MessageID.SetMiscEventValues (140)

// 待办

## MessageID.RequestLucyPopup (141)

// 待办

## MessageID.SyncProjectileTrackers (142)

// 待办

## MessageID.CrystalInvasionRequestedToSkipWaitTime (143)

// 待办

## MessageID.RequestQuestEffect (144)

// 待办

## MessageID.SyncItemsWithShimmer (145)

与 MessageID.SyncItem (21) 相同，只是还同步 Item.shimmered 和 Item.shimmerTime

## MessageID.ShimmerActions (146)

用 3 个结果检查 (number)

0. `Item.ShimmerEffect(new Vector2(number2, number3));`
1. `Main.LocalPlayer.AddCoinLuck(new Vector2(number2, number3), number4);`
2. `Main.npc[number2].SetNetShimmerEffect();`

## MessageID.SyncLoadout (147)

// 待办

## MessageID.SyncItemCannotBeTakenByEnemies (148)

// 待办

## MessageID.Count (149)

原版中 MessageID 的总数。

## MessageID.InGameChangeConfig (249)

// 待办

## MessageID.ModPacket (250)

// 待办

## MessageID.SyncMods (251)

// 待办

## MessageID.ModFile (252)

// 待办
