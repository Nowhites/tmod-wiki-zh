本页面包含将代码迁移到新版tModLoader的新方法和功能的指南。当tModLoader更新需要重写代码时，我们将在此处提供信息。本指南涵盖0.10、0.11和v2022.X (1.4.3)版本的过时迁移。当前版本的迁移信息，请参阅[更新迁移指南](Update-Migration-Guide)

<!-- Generated with https://luciopaiva.com/markdown-toc/ -->
# 目录

- [v2022.X (1.4.3)](#v2022x-143)
  - [重命名或移动的成员](#重命名或移动的成员)
  - [重大变更概念](#重大变更概念)
  - [Hook更改](#hook更改)
  - [tModLoader .NET升级](#tmodloader-net升级)
  - [Terraria 1.4+ 原版更改](#terraria-14-原版更改)
- [v0.11.7.5](#v01175)
  - [反射](#反射)
- [v0.11.7 (Steam发布)](#v0117-steam发布)
- [v0.11.5](#v0115)
  - [Mod.XType](#modxtype)
  - [Mod.GetModX和Instance字段](#modgetmodx和instance字段)
  - [Player.GetModPlayer<T>(Mod)](#playergetmodplayertmod)
  - [ModTile.RightClick](#modtilerightclick)
  - [遍历Buff](#遍历buff)
  - [反射](#反射)
- [v0.11](#v011)
- [v0.10](#v010)

# v2022.X (1.4.3)
v2022.X将tModLoader更新到Terraria 1.4.3.6。此更新改变了所有内容。在开始之前，您需要安装[.NET 6 SDK](https://github.com/tModLoader/tModLoader/wiki/tModLoader-guide-for-developers#net-6-sdk)。接下来，您需要将模组源复制到`My Games/Terraria/tModLoader/ModSources/`文件夹。接下来，在游戏中访问Workshop->Develop Mods菜单，然后点击"upgrade .csproj"按钮。之后，点击"Run tModPorter"按钮。完成后，您就可以打开Visual Studio并开始处理tModPorter无法移植或留下带有说明的注释的代码部分（要查找所有注释，首先，转到工具 -> 选项 -> 环境 -> 任务列表，并添加`tModPorter`作为自定义标记。然后关闭它，打开视图 -> 任务列表将列出所有注释，您也可以使用搜索栏过滤特定注释）。在大tModLoader更新后，您可以多次"运行tModPorter"，它会定期更新并处理1.4之间的更改。

以下是最相关的更改。

## 重命名或移动的成员

### 命名空间/类
* `World.Generation` -> `WorldBuilding`
* `ItemText` -> `PopupText`

### 静态方法
* `ItemText.NewText(Item, ...)` -> `PopupText.NewText(PopupTextContext, Item, ...)`
* `GameContent.Generation.WorldGenLegacyMethod(GenerationProgress)` -> , `GameContent.Generation.WorldGenLegacyMethod(GenerationProgress, GameConfiguration)` （这影响`GameContent.Generation.PassLegacy`构造函数）（并注意`GenerationProgress`命名空间的更改）
* `Lighting.BlackOut` -> `Lighting.Clear`
* `Main.NewText` -> 不变，但现在无论何时使用它（如果它不是仅客户端上下文），您都需要`Main.netMode != NetmodeID.Server`检查，否则它将崩溃
* `Main.NPCAddHeight(int)` -> `Main.NPCAddHeight(NPC)`
* `Main.PlaySound` -> `SoundEngine.PlaySound` -> 在`Terraria.Audio`命名空间中
* `Main.IsTileSpelunkable(Tile)` -> `Main.IsTileSpelunkable(int, int)`
* `Main.PlaySoundInstance(SoundEffectInstance)` -> 完全移除
* `TileDrawing.IsTileDangerous(Player, Tile, ushort)` -> `TileDrawing.IsTileDangerous(int, int, Player)`
* `NetMessage.BroadcastChatMessage` -> `Chat.ChatHelper.BroadcastChatMessage`

### 静态字段/常量/属性
* `ID.ItemUseStyleID`已重命名字段（`SwingThrow` -> `Swing` (1), `EatingUsing` -> `EatFood` (2), `Stabbing` -> `Thrust` (3), `HoldingUp` -> `HoldUp` (4), `HoldingOut` -> `Shoot` (5)）还有更多可用样式
* `Main.ActivePlayersCount` -> `Main.CurrentFrameFlags.ActivePlayersCount`
* `Main.font*` -> `FontAssets.*.Value`<br/>
**正则表达式：** `Main.font(\w+)]` -> `FontAssets.$1.Value`
* `Main.*Texture[i]` -> `TextureAssets.*[i].Value`<br/>
**物品正则表达式：** `Main.itemTexture\[([^\]]*)\]` -> `TextureAssets.Item[$1].Value`
* `Main.blackTileTexture` -> `TextureAssets.BlackTile.Value`
* `Main.magicPixel` -> `TextureAssets.MagicPixel.Value`
* `Main.fishingLineTexture` -> `TextureAssets.FishingLine.Value`
* `Main.campfire`及类似环境标志现在位于`Main.SceneMetrics`中并略有重命名，例如`Main.SceneMetrics.HasCampfire`
* `Main.jungleTiles`及类似周围瓦片计数现在位于`Main.SceneMetrics`中并略有重命名，例如`Main.SceneMetrics.JungleTileCount`
* `Main.dresserX/Y` -> `Main.interactedDresserTopLeftX/Y`
* `Main.GlobalTime` -> `Main.GlobalTimeWrappedHourly`
* `Main.itemLockoutTime` -> `Main.timeItemSlotCannotBeReusedFor`
* `Main.maxInventory` -> `Main.InventorySlotsTotal`
* `Main.quickBG` -> `Main.instantBGTransitionCounter`
* `Main.SmartCursorEnabled` -> `Main.SmartCursorIsUsed`
* `Main.tileValue` -> `Main.tileOreFinderPriority`
* `Main.worldRate` -> `Main.desiredWorldTilesUpdateRate`
* `Main.expertDebuffTime` -> `Main.GameModeInfo.DebuffTimeMultiplier`
* `Main.expertNPCDamage ` -> `Main.GameModeInfo.TownNPCDamageMultiplier`
* `Main.expertLife ` -> `Main.GameModeInfo.EnemyMaxLifeMultiplier`
* `Main.expertDamage ` -> `Main.GameModeInfo.EnemyDamageMultiplier`
* `Main.expertKnockBack ` -> `Main.GameModeInfo.KnockbackToEnemiesMultiplier`
* `Main.knockBackMultiplier ` -> `Main.GameModeInfo.KnockbackToEnemiesMultiplier`
* `Main.damageMultiplier ` -> `Main.GameModeInfo.EnemyDamageMultiplier`
* `ReLogic.OS.Platform.Current.Clipboard` -> `ReLogic.OS.Platform.Get<IClipboard>().Value`
* `Tile.Liquid_Water/Liquid_Honey/Liquid_Lava` -> `ID.LiquidID.Water/Honey/Lava`
* `Tile.Type_Solid` -> `(int)BlockType.Solid`
* `Tile.Type_Halfbrick` -> `(int)BlockType.HalfBlock`
* `Tile.Type_SlopeDownRight` -> `(int)BlockType.SlopeDownLeft`
* `Tile.Type_SlopeDownLeft` -> `(int)BlockType.SlopeDownRight`
* `Tile.Type_SlopeUpRight` -> `(int)BlockType.SlopeUpLeft`
* `Tile.Type_SlopeUpLeft` -> `(int)BlockType.SlopeUpRight`
* `Lighting.lightMode` -> `Graphics.Light.LegacyLighting.Mode`（可通过`Lighting.LegacyEngine.Mode`访问）
* `Localization.GameCulture.*` -> `Localization.GameCulture.CultureName.*`
**替换ModTranslation.AddTranslation使用的正则表达式：** `\bGameCulture\.([^,]+)+` -> `GameCulture.FromCultureName(GameCulture.CultureName.$1)`
* `NPCID.Sets.TechnicallyABoss` -> `NPCID.Sets.ShouldBeCountedAsBoss`
* `ProjectileID.Sets.Homing` -> `ProjectileID.Sets.CultistIsResistantTo`
* `Utils.InverseLerp` -> `Utils.GetLerpValue`
* `Utils.PerLinePoint` -> `Utils.TileActionAttempt`
* `WorldGen.CopperTierOre` -> `WorldGen.SavedOreTiers.Copper`
* `WorldGen.IronTierOre` -> `WorldGen.SavedOreTiers.Iron`
* `WorldGen.SilverTierOre` -> `WorldGen.SavedOreTiers.Silver`
* `WorldGen.GoldTierOre` -> `WorldGen.SavedOreTiers.Gold`
* `WorldGen.oreTier1` -> `WorldGen.SavedOreTiers.Cobalt`
* `WorldGen.oreTier2` -> `WorldGen.SavedOreTiers.Mythril`
* `WorldGen.oreTier3` -> `WorldGen.SavedOreTiers.Adamantite`

### 非静态方法
* `Player.GetItem(int, Item, bool, bool)` -> `Player.GetItem(int, Item, GetItemSettings)`（`GetItemSettings`类包含可用于最后一个参数的各种静态实例）
* `Player.Spawn` -> `Player.Spawn(PlayerSpawnContext)`
* `Player.SporeSac` -> `Player.SporeSac(Item)`
* `void Player.PickAmmo(Item, ref int, ref float, ref bool, ref int, ref float, bool)` -> `bool Player.PickAmmo(Item, out int, out float, out bool, out int, out float, out int, bool)`，返回表示先前的`canShoot`
* `Item.IsTheSameAs` -> 移除，直接使用`item.type == compareItem.type`
* `Item.IsNotTheSameAs` -> `Item.IsNotSameTypePrefixAndStack`
* `public override void GenPass.Apply(GenerationProgress)` -> `protected override void GenPass.ApplyPass(GenerationProgress, GameConfiguration)`（仅覆盖，调用仍通过`Apply`，但带有新参数）
* `Main.instance.DrawPlayer(...)` -> `Main.PlayerRenderer.DrawPlayer(Main.Camera, ...)`

### 非静态字段/常量/属性
* `Main.Rasterizer`现在是静态的
* `GenBase._worldWidth/_worldHeight/_random/_tiles`现在是静态的
* `UIElement.Id` -> `UIElement.UniqueId`<br/>
（从字符串更改为自动分配的自动递增int）
* `Player.hideVisual` -> `Player.hideVisibleAccessory`
* `Item.prefix` -> `byte` 到 `int`（相关方法也有很多更改）
* `Item.dye` -> `byte` 到 `int`（相关方法也有很多更改）
* `Item.hairDye` -> `short` 到 `int`（相关方法也有很多更改）
* `Item.owner` -> `Item.playerIndexTheItemIsReservedFor`
* `ObjectData.TileObjectData.HookCheck` ->  `ObjectData.TileObjectData.HookCheckIfCanPlace`
* `Player.showItemIcon` -> `Player.cursorItemIconEnabled`
* `Player.showItemIcon2` -> `Player.cursorItemIconID`
* `Player.showItemIconText` -> `Player.cursorItemIconText`
* `Player.ZoneHoly` -> `Player.ZoneHallow`
* `Player.activeNPCs` -> `Player.nearbyActiveNPCs`
* `Player.hasBanner` -> `Main.SceneMetrics.hasBanner`
* `Player.NPCBannerBuff` -> `Main.SceneMetrics.NPCBannerBuff`
* `Player.doubleJumpCloud`及其他跳跃 -> `Player.hasJumpOption_Cloud`等
* `Player.dash` -> `Player.dashType`。Player.dash现在用于其他目的。
* `Player.bee`及类似产生抛射物的配饰标志 -> `Player.honeyCombItem`等。检查它们是否启用：`X != null && !X.IsAir`；启用它们：为您自己的配饰分配它。
* `Player.talkNPC = X;` -> `Player.SetTalkNPC(X);`（由于图鉴更改由原版更改）。获取`Player.talkNPC`的值没有更改，只有设置它被更改。
* `Player.flyingPigChest = -1;` -> `Player.piggyBankProjTracker.Clear(); Player.voidLensChest.Clear();`（由于新库存访问抛射物由原版更改）。设置现在使用相应tracker上的`Set`方法完成。
* `Player.extraAccessorySlots` -> `Player.GetAmountOfExtraAccessorySlotsToShow()`
* `StructureMap.AddStructure` -> `StructureMap.AddProtectedStructure`，仅用于1.3 -> 1.4，因为AddStructure仍然存在但用于不同目的。

### 其他更改
* 注册为`MiscShaderData`的着色器现在需要`float4 uShaderSpecificData;`作为参数
* 护甲着色器现在需要`float2 uTargetPosition`、`float4 uLegacyArmorSourceRect`和`float2 uLegacyArmorSheetSize`参数

### tModLoader更改
以下包含对tModLoader成员的较小规模更改。更详细的更改（即围绕IEntitySource的内容）在下面单独处理

_此处列出的所有ModX内容也适用于GlobalX_

* 所有小写属性现在都大写了（例如`ModX.mod`、`ModProjectile.aiType`和`ModPlayer.player` -> `ModX.Mod`、`ModProjectile.AIType`、`ModPlayer.Player`）
* `ModLoader.ModWorld` -> `ModLoader.ModSystem`（并添加了`Mod`的一些添加内容。`ModWorld.Load/Save/Initialize`已更改以适应世界上下文：`ModSystem.LoadWorldData/SaveWorldData/OnWorldLoad+PreWorldGen`。如果您直接移植到1.4.4，`Initialize -> ClearWorld`）
* `ModLoader.ModWorld.TileCountsAvailable(int[])` -> `ModLoader.ModSystem.TileCountsAvailable(ReadOnlySpan<int>)`（需要`using System;`）
* 许多方法从`ModLoader.Mod`移到了`ModLoader.ModSystem`：`ModifyTransformMatrix, UpdateUI, PreUpdateEntities, PostUpdateEverything, ModifyInterfaceLayers, ModifySunLightColor, ModifyLightingBrightness, PostDrawFullscreenMap, PostUpdateInput, PreSaveAndQuit, PostDrawInterface`
* `ModLoader.Mod.MidUpdateX` hooks已移至`ModLoader.ModSystem`并拆分，名称略有不同：`Pre/PostUpdatePlayers, Pre/PostUpdateNPCs, Pre/PostUpdateGores, Pre/PostUpdateProjectiles, Pre/PostUpdateItems, Pre/PostUpdateDusts, Pre/PostUpdateTime, Pre/PostUpdateInvasions`
* `ModLoader.Mod.HotKeyPressed` -> 移除，使用`ModLoader.ModPlayer.ProcessTriggers`
* `ModLoader.Mod.AddEquipTexture` -> 移除，使用`ModLoader.EquipLoader.AddEquipTexture`，Mod作为第一个参数
* `ModLoader.Mod.GetEquipSlot` -> 移除，使用`ModLoader.EquipLoader.GetEquipSlot`，Mod作为第一个参数
* `ModLoader.Mod.GetAccessorySlot` -> 移除，使用`ModLoader.EquipLoader.GetEquipSlot`，Mod作为第一个参数，必要时强制转换为`sbyte`
* `ModLoader.MusicPriority` -> `ModLoader.SceneEffectPriority`
* `ModLoader.PlayerHooks` -> `ModLoader.PlayerLoader`
* `ModLoader.ModHotKey` -> `ModLoader.ModKeybind`
* `ModLoader.SpawnCondition` -> `ModLoader.Utilities.SpawnCondition`
* `ModLoader.PlayerDrawInfo` -> `DataStructures.PlayerDrawSet`
* `ModLoader.SoundType` -> 移除，模组声音不再分类
* `ModLoader.ModSound` -> 移除
* `ModLoader.ModMountData` -> `ModLoader.ModMount`
* `ModLoader.ModMountData.JumpHeight/JumpSpeed`没有`Player`参数 -> 移除/弃用
* `ModLoader.ModContent.TextureExists(string)` -> `ModLoader.ModContent.HasAsset(string)`
* `ModLoader.ModContent.GetTexture(string)` -> `ModLoader.ModContent.Request<Texture2D>(string)`，其他资产如`Effect`类似
**正则表达式：** `ModContent\.GetTexture\(([^)]+).` -> `ModContent.Request<Texture2D>($1)`
* `ModLoader.Mod.GetTexture(string)` -> `ModLoader.Mod.Assets.Request<Texture2D>(string)`，其他资产如`Effect`类似
**正则表达式：** `mod\.GetTexture\(([^)]+).` -> `Mod.Assets.Request<Texture2D>($1).Value`
* `ModLoader.ModContent.GetEffect(string)` -> `ModContent.Request<Effect>(string).Value`（`Request`的第二个参数必须是`ReLogic.Content.AssetRequestMode.ImmediateLoad`）
* `ModLoader.Mod.GetEffect(string)` -> `ModLoader.Mod.Assets.Request<Effect>(string).Value`（`Request`的第二个参数必须是`ReLogic.Content.AssetRequestMode.ImmediateLoad`）
* `ModLoader.ModLoader.GetMod(string)`现在如果模组未加载则抛出异常，使用`ModLoader.ModLoader.TryGetMod(string, out Mod)`
* `ModLoader.Mod.AddBossHeadTexture(string, int)`现在返回`int`，即头部纹理槽。
* `ModLoader.Mod.AddTranslation(ModTranslation)` -> `ModLoader.LocalizationLoader.AddTranslation(ModTranslation)`
* `ModLoader.Mod.CreateTranslation(string)` -> `ModLoader.LocalizationLoader.CreateTranslation(Mod, string)`
* `ModLoader.Mod.GetLegacySoundSlot(ModLoader.SoundType, string)` -> 移除
* `ModLoader.Mod.RegisterHotKey(string, string)` -> `ModLoader.KeybindLoader.RegisterKeybind(Mod, string, string)`
* `ModLoader.ModGore.GetGoreSlot`-> `ModLoader.ModContent.GetGoreSlot`
* `ModLoader.ModGore.DrawBehind`-> 移除，使用`ID.GoreID.Sets.DrawBehind[Type]`在`SetStaticDefaults`中
* `ModLoader.ModGore.OnSpawn(Gore)`-> `ModLoader.ModGore.OnSpawn(Gore, IEntitySource)`（`using Terraria.DataStructures;`）
* `ModLoader.ModPlayer.CatchFish(Item, Item, int, int, int, int, int, ref int)` -> `ModLoader.ModPlayer.CatchFish(FishingAttempt, ref int, ref int, ref AdvancedPopupRequest, ref Vector2)`
* `ModLoader.ModPlayer.DrawEffects(PlayerDrawInfo, ...)` -> `ModLoader.ModPlayer.DrawEffects(PlayerDrawSet, ...)`
* `ModLoader.ModProjectile.CanDamage` -> 返回类型从`bool`更改为`bool?`，考虑返回`null`而不是`true`
* `ModLoader.ModProjectile.TileCollideStyle(ref int, ref int, ref bool)` -> `ModLoader.ModProjectile.TileCollideStyle(ref int, ref int, ref bool, ref Vector2)`
* `ModLoader.ModProjectile.PreDraw(SpriteBatch, Color)` -> `ModLoader.ModProjectile.PreDraw(ref Color)`，`ModLoader.ModProjectile.PostDraw(SpriteBatch, Color)` -> `ModLoader.ModProjectile.PostDraw(Color)`，以及`PreDrawExtras(SpriteBatch)` -> `PreDrawExtras()`，因此使用`Main.EntitySpriteDraw`而不是`spriteBatch.Draw`（使用相同参数，除了最后一个是float -> int，应该保持为0）。
* `ModLoader.ModNPC.PreDraw(SpriteBatch, Color)` -> `ModLoader.ModNPC.PreDraw(SpriteBatch, Vector2, Color)`，`ModLoader.ModNPC.PostDraw(SpriteBatch, Color)` -> `ModLoader.ModNPC.PostDraw(SpriteBatch, Vector2, Color)`，这意味着您应该使用新参数而不是`Main.screenPosition`，以便在图鉴中正确绘制。
* `ModLoader.ModNPC.NPCLoot` -> `ModLoader.ModNPC.OnKill`（战利品现在必须在`ModifyNPCLoot`中添加，请参阅[图鉴](#图鉴)<a name="Bestiary"></a>部分）
* `ModLoader.ModNPC.PreNPCLoot` -> `ModLoader.ModNPC.PreKill`
* `ModLoader.ModNPC.SpecialNPCLoot` -> `ModLoader.ModNPC.SpecialOnKill`
* `ModLoader.ModNPC.bossBag` -> 移除，通过`npcLoot.Add(ItemDropRule.BossBag(type))`将宝藏袋与其他战利品一起生成
* `ModLoader.ModItem.Clone` -> `ModLoader.ModItem.Clone(Item)`
* `ModLoader.ModItem.CloneNewInstances` -> 从`public`更改为`protected`
* `ModLoader.ModItem.NetRecieve` -> `ModLoader.ModItem.NetReceive`（拼写错误）
* `ModLoader.ModItem.NewPreReforge` -> `ModLoader.ModItem.PreReforge`
* `ModLoader.ModItem.UseItem` -> 返回类型从`bool`更改为`bool?`，考虑返回`null`而不是`false`
* `ModLoader.ModItem.HoldStyle(Player)` -> `ModLoader.ModItem.HoldStyle(Player, Rectangle)`
* `ModLoader.ModItem.UseStyle(Player)` -> `ModLoader.ModItem.UseStyle(Player, Rectangle)`
* `ModLoader.ModItem.DrawX` -> 现在使用`ArmorIDs.X.Sets.Draw/Hide/etc[equipSlotID] = true/false`来指定装备纹理的这些属性。
* `ModLoader.ModItem.DrawHair` -> 移除。移植：`drawAltHair = true` -> `ArmorIDs.Head.Sets.DrawHatHair[Item.headSlot] = true`，`drawHair = true` -> `ArmorIDs.Head.Sets.DrawFullHair[Item.headSlot] = true`（在`SetStaticDefaults`中）
* `ModLoader.ModItem.UpdateVanity` -> `ModLoader.ModItem.EquipFrameEffects`，UpdateVanity hook仍然存在，只是用于不同目的
* `ModLoader.ModPlayer.SetupStartInventory(IList<Item>)` -> 移除/弃用
* `ModLoader.ModPlayer.SetupStartInventory(IList<Item>, bool)` -> `ModLoader.ModPlayer.AddStartingItems(bool)`，返回`IEnumerable<Item>`。如需要修改，使用ModifyStartingInventory
* `ModLoader.ModPlayer/ModItem.GetWeaponDamage` -> 移除/弃用
* `ModLoader.ModPlayer/ModItem.GetWeaponCrit(..., ref int)` -> `ModLoader.ModPlayer/ModItem.ModifyWeaponCrit(..., ref float)`
* `ModLoader.ModPlayer/ModItem.GetWeaponKnockback(..., ref bool)` -> `ModLoader.ModPlayer/ModItem.ModifyWeaponKnockback(..., ref StatModifier)`
* `ModLoader.ModPlayer/ModItem.ModifyWeaponDamage(..., float, float)` -> 移除/弃用
* `ModLoader.ModPlayer/ModItem.ModifyWeaponDamage(..., float, float, float)` -> `ModLoader.ModPlayer/ModItem.ModifyWeaponDamage(..., ref StatModifier)`
* `ModLoader.ModTile/ModWall.drop` -> `ModLoader.ModTile/ModWall.ItemDrop`
* `ModLoader.ModTile/ModWall.soundType` -> `ModLoader.ModTile/ModWall.HitSound`
* `ModLoader.ModTile/ModWall.soundStyle` -> 移除/集成到`HitSound`
* `ModLoader.ModTile.DrawEffects(int, int, SpriteBatch, ref Color, ref int)` -> `ModLoader.ModTile.DrawEffects(int, int, SpriteBatch, ref TileDrawInfo)`
* `ModLoader.ModTile.NewRightClick` -> `ModLoader.ModTile.RightClick`
* `ModLoader.ModTile.Dangersense` -> `ModLoader.ModTile.IsTileDangerous`（`GlobalTile`变体是`bool?`而不是`bool`）
* `ModLoader.ModTile.HasSmartInteract` -> `HasSmartInteract(int i, int j, SmartInteractScanSettings settings)`（`SmartInteractScanSettings`需要`using Terraria.GameContent.ObjectInteractions;`）
* `ModLoader.ModTile.disableSmartCursor` -> `TileID.Sets.DisableSmartCursor[Type]`
* `ModLoader.ModTile.disableSmartInteract` -> `TileID.Sets.DisableSmartInteract[Type]`
* `ModLoader.ModTile.dresser` -> `ContainerName.SetDefault("Dresser Name")`，也考虑`TileID.Sets.BasicChest`
* `ModLoader.ModTile.chest` -> `ContainerName.SetDefault("Chest Name")`，也考虑`TileID.Sets.BasicDresser`
* `ModLoader.ModTile.bed` -> `TileID.Sets.CanBeSleptIn[Type]`
* `ModLoader.ModTile.sapling` -> `TileID.Sets.TreeSapling[Type]`，也考虑`TileID.Sets.CommonSapling`
* `ModLoader.ModTile.torch` -> `TileID.Sets.Torch[Type]`
* `ModLoader.ModPrefix.AutoDefaults` -> `ModLoader.ModPrefix.AutoStaticDefaults`
* `ModLoader.ModPrefix.GetPrefix(byte)` -> `ModLoader.PrefixLoader.GetPrefix(int)`
* `ModLoader.BuffLoader.CanBeCleared(int)` -> 移除
* `ModLoader.ModBuff.canBeCleared` -> `BuffID.Sets.NurseCannotRemoveDebuff[Type]`，逻辑反转
* `ModLoader.ModBuff.longerExpertDebuff` -> `BuffID.Sets.LongerExpertDebuff[Type]`
* `ModLoader.ModWaterStyle.Type` -> `ModLoader.ModWaterStyle.Slot`
* `ModLoader.ModWaterfallStyle.Type` -> `ModLoader.ModWaterfallStyle.Slot`
* `ModLoader.EquipTexture.mod` -> 移除
* `ModLoader.EquipTexture.UpdateVanity` -> `ModLoader.EquipTexture.FrameEffects`（仅用于初始1.3移植，UpdateVanity hook仍然存在，只是用于不同目的）
* `ModLoader.ModX.Load(TagCompound)` -> `ModLoader.ModX.LoadData(TagCompound)`
* `ModLoader.ModX.Save()` -> `ModLoader.ModX.SaveData(TagCompound)` - 现在返回`void`，这意味着您应该将数据分配给传入的标签。


## 重大变更概念

### 资产
每个资产（`Texture2D`、`DynamicSpriteFont`、`Effect`等）现在都包装在`Asset<T>`中。您需要使用`.Value`访问实际资产。例如，而不是`Texture2D test = ModContent.GetTexture("Test");`，您将编写`Texture2D test = ModContent.Request<Texture2D>("Test").Value;`（`Mod`方法是`Mod.Assets.Request<Texture2D>("Test")`）。从技术上讲，您也可以`Texture2D test = (Texture2D)GetTexture("Test");`，根据您的风格，这可能更容易查看。它与`.Value`做完全相同的事情，即加载纹理。
此外，tModLoader默认异步加载纹理。这意味着在首次请求资产时，关联的值可能尚未分配。这通常不是问题（对于纹理，tModLoader在真实资产加载之前提供虚拟纹理），但对于需要在构造时获取纹理尺寸的UI事物（例如`UIImageButton`）可能会出现问题。然后，在`Request<T>`中将`AssetRequestMode.ImmediateLoad`指定为第二个参数。

纹理/资产路径现在也略有更改，因此使用如下内容：`"Terraria/Item_" + ItemID.IronPickaxe;`，必须更改为：`"Terraria/Images/Item_" + ItemID.IronPickaxe;`

最后，在召唤原版纹理时，确保在`TextureAssets.Item[type].Value`等情况下调用正确版本的`Main.instance.LoadItem(type)`，以避免空错误。

Gores（`ModGore`）现在从路径中包含"Gores"的任何文件夹自动加载（而不是必须位于模组根文件夹的"Gores"文件夹中）。

### 声音
声音现在大大简化了，只有一个播放声音的方法，一个代表声音的对象。有关详细信息，请参阅更新的[基本声音](https://github.com/tModLoader/tModLoader/wiki/Basic-Sounds)指南。特别注意[调整原版代码或过去tModLoader版本的代码](https://github.com/tModLoader/tModLoader/wiki/Basic-Sounds#adapting-vanilla-code-or-code-from-past-tmodloader-versions)部分，它将教您更改什么。要迁移现有代码，请使用指南中教的`SoundEngine.PlaySound`、`SoundID`字段和`new SoundStyle(pathtosoundwithoutextension)`。有关更多信息和一些示例，请参阅相应的[Pull Request](https://github.com/tModLoader/tModLoader/commit/1f8670de56611833db64bb7082bbd2f17b9c0877)。

### 配方
配方已完全重新设计（不要惊慌，请阅读下文）。配方现在可以使用流式API语法，而不是创建`ModRecipe`（现在是`Recipe`）并调用其方法。如果您不知道这是什么，这里有一个之前的例子：

```
// this would be in your item
public override void AddRecipes()
{
    var recipe = new ModRecipe(mod);
    recipe.AddIngredient(ItemID.Wood, 5);
    recipe.SetResult(this);
    recipe.AddRecipe();
}
```
这已被替换为：
```
// still in your item
public override void AddRecipes()
{
    CreateRecipe()
        .AddIngredient(ItemID.Wood, 5)
        .Register();
}
```
您甚至可以为此使用表达式：
```
public override void AddRecipes() => CreateRecipe()
        .AddIngredient(ItemID.Wood, 5)
        .Register();
```
在[ExampleMod/Content/ExampleRecipes.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/ExampleRecipes.cs)中有更详细的解释。_请记住，链接方法是可选的，您仍然可以使用旧模式。_

#### RecipeFinder和RecipeEditor
这两个类已被移除。现在只需在任何`PostAddRecipes` hook（如`ModSystem`中）中使用，并手动迭代`Main.recipe`来查找和编辑您需要的内容。要删除配方，请对其调用`DisableRecipe()`。

#### 自定义ModRecipe类/扩展
由于ModRecipe已消失，而Recipe是密封的，您不能再从中继承。任何利用自定义类及其方法的1.3模式都需要使用新方法。
方法移植说明（委托可以与方法直接传递互换）：
* `ConsumeItem`：将您的代码写为`AddConsumeItemCallback`的方法：
```cs
.AddConsumeItemCallback(delegate (Recipe recipe, int type, ref int amount) {
	//Code here
})
```

* `OnCraft`：将您的代码写为`AddCraftItemCallback`的方法：
```cs
.AddOnCraftCallback(delegate (Recipe recipe, Item item) {
	//Code here
})
```

* `RecipeAvailable`：将您的代码写为`AddCondition`的方法（您在ExampleRecipes中找到更多相关信息，以及如何使用原版条件）：
```cs
var conditionDescription = NetworkText.FromKey("Mods.MyMod.MyConditionKey"); //You are encouraged to localize your conditions properly (it would be <MyConditionKey: My Condition> in the localization file). If you don't want that, use NetworkText.FromLiteral("My Condition")
.AddCondition(conditionDescription , recipe => {
	//Code here
})
```

### Mod.XType，内容获取
基于字符串的模组内容获取（如`Mod.ItemType("ItemName")`或`Mod.ProjectileType("ProjectileName")`)已被替换并统一为`Mod.Find<ModX>("XName").Type`（很少`.Type`是`.Slot`）。它不再在找不到所述内容时返回0，而是抛出异常，鼓励使用编译时安全的泛型方法（仍然存在，如`ModContent.ItemType<ItemName>()`），或者如果无法使用泛型方法（如跨模组），则使用`Mod.TryFind<ModX>("XName", out var baseObj)`（然后如果方法返回`true`则使用`baseObj.Type`）。
同样的方法也存在于`ModContent`，字符串参数期望格式为"ModName/XName"（顺便说一句，这是`baseObj.FullName`将返回的格式）。

**重要：不要替换/移除`<ModX>`部分！**

统一为新方法的其他方法：
* `Mod.GetGoreSlot`和`ModGore`（重要说明：如果在服务器端调用，这将出错，因为`ModGore`现在是客户端类型，因此检查`Main.netMode != NetmodeID.Server`）（另外重要：Find/TryFind现在**不再**需要在`"Gores/GoreName"`中的`Gores/`）

示例：
```cs
// Replacement for recipe.AddIngredient(Mod.ItemType("ItemName")) assuming it exists in your mod, but will crash if said item was removed/renamed:
recipe.AddIngredient(Mod.Find<ModItem>("ItemName").Type);

// The same but safe:
if (Mod.TryFind<ModItem>("ItemName", out var modItemName)) {
	recipe.AddIngredient(modItemName.Type);
}

// The same but using a different mod (cross mod):
if (ModLoader.TryGetMod("OtherMod", out var otherMod)) {
	if (otherMod.TryFind<ModItem>("ItemName", out var modItemName)) {
		recipe.AddIngredient(modItemName.Type);
	}
}

// The same but shortering it by using ModContent:
if (ModContent.TryFind<ModItem>("OtherMod/ItemName", out var modItemName)) {
	recipe.AddIngredient(modItemName.Type);
}

// This would also work, first parameter split in two:
if (ModContent.TryFind<ModItem>("OtherMod", "ItemName", out var modItemName)) {
	recipe.AddIngredient(modItemName.Type);
}
```

### 伤害类别
`Item.melee`、`Projectile.ranged`等被tModLoader自己的`DamageClass`实现取代，该实现利用`StatModifier`来统一访问基础/加成/乘法/固定奖励。这意味着`item.ranged = true`变为`Item.DamageType = DamageClass.Ranged;`，`if (item.ranged)`变为`if (Item.CountsAsClass(DamageClass.Ranged))`。您也可以通过此系统创建自己的自定义类。有关更多信息，请访问`ExampleMod/Content/DamageClasses/ExampleDamageClass.cs`及其中的物品和抛射物。

召唤物和哨兵抛射物必须具有`Projectile.DamageType = DamageClass.Summon;`（对于召唤物，在`Projectile.minion = true;`之外）。

随着投掷伤害的加入，投掷武器/伤害类别奖励将从`thrown`变为`Throwing`，例如`Player.GetCritChance(DamageClass.Throwing)`。

给予伤害奖励的配饰从`player.minionDamage += 0.1f;`更改为`Player.GetDamage(DamageClass.Summon) += 0.1f;`。
固定增加通过`Player.GetDamage(DamageClass.Summon).Flat += 4;`在应用乘数之后进行，或`.Base += 4;`在应用之前进行。

使用`Player.GetTotalDamage`获取伤害类别的有效统计修饰符（包括继承，如`Generic`）

其他移植更改：
* `Player.allDamage` -> `Player.GetDamage(DamageClass.Generic)`（当使用+=时）
* `Player.allDamageMult` -> `Player.GetDamage(DamageClass.Generic)`（当使用*=时）
* `Player.armorPenetration` -> `Player.GetArmorPenetration(DamageClass.Generic)`
* `Player.meleeSpeed` -> `GetAttackSpeed(DamageClass.Melee)`
* `Player.arrowDamage/bulletDamage/rocketDamage` -> 类型更改为`StatModifier`
* `Item.thrown` -> 虽然在1.4中通常被移除，但通过伤害类别由tML重新实现（详见下文）。
* `Projectile.thrown` -> 与`Item.thrown`相同

### XItem.Shoot
`ModPlayer/GlobalItem/ModItem.Shoot`已拆分为三个方法：`CanShoot`、`ModifyShootStats`和`Shoot`。现在允许射击和更改射击相关参数与抛射物的创建分开。`CanShoot`简单地控制物品是否可以射击抛射物。`ModifyShootStats`包含所有作为`ref`的参数，而`Shoot`不是。
`float speedX/Y`参数也已合并为`Vector2 velocity`。如果您滥用`Shoot`即更改伤害但不实际生成任何抛射物，然后返回true，则需要切换（或移动该代码）到`ModifyShootStats`。

更改的小概述：
* `ModPlayer/GlobalItem/ModItem.Shoot` -> `ModPlayer/GlobalItem/ModItem.CanShoot`（用于允许或防止在抛射物中生成）
* `ModPlayer/GlobalItem/ModItem.Shoot` -> `ModPlayer/GlobalItem/ModItem.ModifyShootStats`（用于更改射击相关参数）
* `ModPlayer/GlobalItem/ModItem.Shoot` -> `ModPlayer/GlobalItem/ModItem.Shoot`（用于生成抛射物和控制是否生成默认抛射物）

### 玩家绘制
以前使用`PlayerLayer`和`ModPlayer.ModifyDrawLayers`的模组制作者现在必须适应替换：`PlayerDrawLayer`、`ModPlayer.HideDrawLayers`和`ModPlayer.ModifyDrawLayerOrdering`。

现在，您可以实例化自定义`PlayerLayer`对象，然后通过使用列表中的索引将其添加到现有图层列表中，您可以使用自包含的`PlayerDrawLayer`类（就像任何其他`ModX`类一样使用它），它通过`GetDefaultPosition`覆盖指定其绘制顺序，方法是返回定义**无条件**顺序/层次的对象（`new Between(layer1, layer2)`、`new BeforeParent(layer)`、`new AfterParent(layer)`（`layer`通常是在`PlayerDrawLayers`类中找到的原版图层）。`ModPlayer` hooks不再用于插入。要隐藏图层（以前通过将Visible设置为false来完成），您可以调用它们的`Hide`方法，这也会隐藏附加到此图层的任何子图层。

（等效）更改的小概述：
* `ModLoader.PlayerLayer` -> `ModLoader.PlayerDrawLayer`（用于创建您的图层）
* `ModLoader.PlayerLayer` -> `DataStructures.PlayerDrawLayers`（用于引用原版图层）
* 通过`ModLoader.PlayerDrawLayerLoader.Layers`（不是按绘制顺序排列的！）访问所有图层，而不是`ModPlayer.ModifyDrawLayers`中的`layers`参数
* `ModLoader.ModPlayer.ModifyDrawLayers` -> `ModLoader.ModPlayer.HideDrawLayers`（_仅_用于隐藏（如果您需要访问所有图层，请参阅上文））
* `ModLoader.ModPlayer.ModifyDrawLayers` -> `ModLoader.ModPlayer.ModifyDrawLayerOrdering`（_仅_用于更改顺序）

移植主要包括将插入代码从`ModifyDrawLayers`移动到`GetDefaultPosition`（必须是无条件的，如果您需要基于条件更改顺序，请使用新hook），将即时绘制条件移入`GetDefaultVisibility` hook，实际绘制代码移入`Draw`，并将创建的`DrawData`添加到`drawInfo.DrawDataCache`。请参阅ExampleMod中的示例以了解实现。

### 瓦片
* `Tile`类型不再是引用类型的`class`，而是充当键的`readonly struct`，实际考虑到计算机处理器和内存板的工作方式。对用户来说，使用类型保持某种相似性。
* 内存使用已大幅减少。在大型世界的情况下，32位和64位上下文将分别少使用242/484兆字节的数据。
* 在某些情况下，性能提升了_**高达30%**_。
* 为了将来发展的好处，`LiquidType`现在是6位整数，范围为[0, 64]。
* `Tile`不再可为空。所有`tile == null`检查都是无用的，将始终返回false。`Tile tile = default;`将产生一个指向[0,0]瓦片的键，而不是空值。如果您需要可空性 - 使用`Tile?`（`Nullable<Tile>`的C#简写）。
* 您可以通过声明实现标记`ITileData`接口的struct来添加自定义瓦片数据。
它也将与原版数据一起复制和移除。但是，它不会自动保存或同步。
瓦片数据结构必须是`unmanaged`，也就是说，它们只能包含值数据。
瓦片数据可以通过`ref T Tile.Get<T>() where T : unmanaged, ITileData`方法或通过`TileMap.GetData<T>()`访问和修改。
* 大多数瓦片get/set方法现在是内部的，为了一致性和易用性而替换为属性。tModPorter将处理所有这些。
  * 除了`isTheSameAs(Tile)`。此方法仅被原版用于网络压缩，可能不是您想要的。您应该直接比较您关心的字段。如果您正在做类似原理图的事情，请查看`Get<TileWallWireStateData>().NonFrameBits`
* `Main.tile`类型从`Tile[,]`更改为`TileMap`。

### ModBiome和ModSceneEffect
`ModSceneEffect`现在处理选择场景效果而不是单独的hooks，这样tML可以给予适当的指定优先级。它有一个`IsSceneEffectActive`返回方法和一个关联的`Priority`属性。在添加由以下任何hooks控制的场景效果时，应直接派生它，但此类已经派生的`ModType`（如`ModBiome`）除外。可能需要多个小的派生类来完成相同的功能。在撰写本文时，此更改不影响当时的现有ModNPC音乐实现。

关于`ChooseStyle` Hooks已进行以下更改，可能需要多个`ModSceneEffect`类来完全替换Hook（除非另有说明，`ModSceneEffect`也算作`ModBiome`，因为后者继承自前者）：

* `ModWorld.ChooseWaterStyle()` -> `ModSceneEffect.WaterStyle`
* `Mod.UpdateMusic()` -> `ModSceneEffect.Music and .Priority`，以及`ModSceneEffect.IsSceneEffectActive()`
* `ModSurfaceBgStyle.ChooseStyle()` -> `ModSceneEffect.SurfaceBackgroundStyle`
* `ModUgBgStyle.ChooseStyle()` -> `ModSceneEffect.UndergroundBackgroundStyle`

同样，随着`ModBiome`的引入，`UpdateBiomes`和`UpdateBiomeVisuals` hooks已集成到`ModBiome`类中。
* `ModPlayer.UpdateBiomes()` -> `ModBiome.IsBiomeActive()`
* `ModPlayer.UpdateBiomeVisuals()` -> `ModSceneEffect.SpecialVisuals()`

## Hook更改
* `(ModItem/GlobalItem).UseTimeMultiplier`：现在接受实际的乘数而不是除数。通过用1.0除以它们来反转您的值。
* `(ModItem/GlobalItem).MeleeSpeedMultiplier`：替换为`UseAnimationMultiplier`和`UseSpeedMultiplier`。如果您想增加`itemAnimation`值（冒增加使用次数/射击次数的风险），请使用前者；如果您只想安全地加快物品/武器的速度，同时保持`itemAnimation`和`itemTime`之间的比率相同，请使用后者。
* `(GlobalTile/GlobalWall/ModTile/ModWall/ModBuff/ModDust/ModMount/ModPrefix).SetDefaults` -> `(X).SetStaticDefaults`。现在所有`ModType`都有这样的方法。
* 弹药更改：
    * `(ModItem/GlobalItem).PickAmmo`将`ref int damage`更改为`ref StatModifier`，但有注意事项。在此处阅读更多[内容](https://github.com/tModLoader/tModLoader/pull/2288)
    * 没有`Item weapon`参数的`(ModItem/GlobalItem).PickAmmo` -> 移除/弃用
    * `ConsumeAmmo`的适当变量名和附加上下文（澄清`item`用法以指定`weapon`和`ammo`，并根据上下文添加每个缺失的）
    * `ModPlayer.ConsumeAmmo` -> `ModPlayer.CanConsumeAmmo`
    * `(ModItem/GlobalItem).ConsumeAmmo` -> `(ModItem/GlobalItem).CanConsumeAmmo`，现在仅在武器上调用。
    * 弹药的新hook：`(ModItem/GlobalItem).CanBeConsumedAsAmmo`。
    * `(ModItem/GlobalItem).OnConsumeAmmo`：现在仅在武器上调用。
    * 弹药的新hook：`(ModItem/GlobalItem).OnConsumedAsAmmo`。
    * 弹药**选择**的新hooks：`(ModItem/GlobalItem).CanChooseAmmo`和`(ModItem/GlobalItem).CanBeChosenAsAmmo`
* `(ModNPC/GlobalNPC).OnCatchNPC`：现在有附加上下文，不再用于修改捕获的NPC所变成的物品。要修改捕获的NPC的物品，请使用`OnSpawn`并检查源是否为`EntitySource_CatchEntity`。

### 自动加载
tML中类的自动加载和类型结构已更改。实现`ILoadable`的类现在是自动加载的。最明显的实现是`ModType`类，它用于所有ModX和GlobalX类，统一各种行为。旧的`Autoload` hooks已被`IsLoadingEnabled(Mod)`和`Autoload`属性替换：
* `IsLoadingEnabled`允许简单的"可以加载与否"行为（即配置）
* `Autoload`属性用于类，可以提供`true/false`（允许您选择使用`Mod.AddContent`在其他地方手动添加和控制内容）和`ModSide`（允许创建特定 стороны的内容，即`ModGore`，这是客户端的）
* `ModLoader.Mod.AddItem(string, ModItem)`、`ModLoader.Mod.AddProjectile(string, ModProjectile)`和其他类似方法 -> `ModLoader.Mod.AddContent(ILoadable)`，名称现在通过`ILoadable`上的`Name`属性指定
* `Mod.Properties`和`ModProperties`已移除。直接分配`ContentAutoloadingEnabled, GoreAutoloadingEnabled, MusicAutoloadingEnabled, and BackgroundAutoloadingEnabled`

//TODO有关旧Autoload hook中参数的更详细移植说明，以及现在如何手动添加内容

## tModLoader .NET升级
{关于.NET5和AnyCPU目标的一些信息}
### .NET 5安装和Visual Studio设置
{}
### .NET 5教程链接
{}

## Terraria 1.4+ 原版更改
{1.4包括几个后端更改，我们应该指出}
### IEntitySource
各种实体创建方法（`Item.NewItem`、`Projectile.NewProjectile`、`NPC.NewNPC`、`Player.QuickSpawnItem` `Player.QuickSpawnClonedItem`（仅tML）、`Gore.NewGore`）都收到了一个新参数表示其来源，在此处阅读更多[内容](https://github.com/tModLoader/tModLoader/wiki/IEntitySource)。

### 装备纹理和护甲表
护甲某些方面的精灵样式已更改 - 即`EquipType.HandsOn/HandsOff/Body`（后者也影响1.3 Arm和FemaleBody纹理）。迁移到新护甲纹理格式的工具和指南可以在[护甲纹理迁移指南](../wiki/Armor-Texture-Migration-Guide)中找到。

![](https://i.imgur.com/0iw2Tw2.png)

### 召唤物生成
召唤伤害（召唤物、哨兵和召唤物/哨兵发射的抛射物）现在在生成时动态缩放，而不是固定值。模组制作者现在必须在其创建后将`Projectile.originalDamage`分配给基础伤害（通常是`Item.damage`）（不是在`SetDefaults`中，`在生成它的物品的Shoot`中是合适的位置）。这里有两种最常见的方法：

```cs
//1: 主要用于哨兵（与`Player.FindSentryRestingSpot`结合使用）
int index = Projectile.NewProjectile(parameters);
Main.projectile[index].originalDamage = Item.damage;

//2: 自动设置originalDamage，主要用于召唤物
Player.SpawnMinionOnCursor(parameters); //Make sure to pass Item.damage for the damage parameter
```

### 图鉴
每个模组在图鉴中都有其自己的过滤器，默认为"？"图标。您可以通过在根文件夹中提供30x30的`icon_small.png`来更改它。

要向NPC添加掉落，您现在必须使用`Mod/GlobalNPC.ModifyNPCLoot`（和`GlobalNPC.ModifyGlobalLoot`）hooks（而不是1.3的`NPCLoot`、`OnKill`类似物，它们用于非掉落例如标记boss被击败、生成矿石或抛射物）。在此处阅读更多[内容](https://github.com/tModLoader/tModLoader/wiki/Basic-NPC-Drops-and-Loot-1.4)。

您可以使用`ModNPC.SetBestiary` hook自定义NPC的图鉴条目，并通过将数据添加到`NPCID.Sets.NPCBestiaryDrawOffset`来自定义NPC在预览和完整图像中的外观。

//TODO最佳组合与自定义预览图像、动画、掉落规则等的集成。

### NPC Buff免疫
设置NPC buff免疫的旧方法不再有效（提醒：在`SetDefaults`中：`npc.buffImmune[type] = true;`）。
新方法将其移到`SetStaticDefaults`并使用新语法。示例：

```cs
// Add these to the top of your file
using Terraria.DataStructures;
using Terraria.ID;

// Specify the debuffs it is immune to
NPCDebuffImmunityData debuffData = new NPCDebuffImmunityData {
	SpecificallyImmuneTo = new int[] {
		BuffID.Confused, // Most NPCs have this
		BuffID.Poisoned,
		ModContent.BuffType<MyDebuff>(), // Modded buffs
	}
};
NPCID.Sets.DebuffImmunitySets[Type] = debuffData;
```

如果您想给NPC免疫所有debuffs（如破坏者），请使用此：
```cs
new NPCDebuffImmunityData {
	ImmuneToAllBuffsThatAreNotWhips = true,
	ImmuneToWhips = true
}
```

### 翅膀
翅膀数据现在在加载时通过`ModItem.SetStaticDefaults`中的`ArmorIDs`集分配，如下所示：`ArmorIDs.Wing.Sets.Stats[Item.wingSlot] = new WingStats(wingTimeMax, speed, acceleration);`（检查其他构造函数以进行更精细的调整）。仅在需要动态调整时分配`player.wingTimeMax`或使用`ModItem.HorizontalWingSpeeds`。未能添加前一个代码将导致玩家在飞行时无法水平移动。

### 其他
* 标记boss被击败不再需要手动同步（`MessageID.WorldData`将在`OnKill` hook之后发送），如果您使用此方法，它也会触发灯笼夜：
`NPC.SetEventFlagCleared(ref myDownedBool, -1);`

# v0.11.7.5

## 反射
一如既往，您的反射可能已损坏，因此请仔细检查。特别注意`UICommon`类中的`Texture2D`字段，请参阅[此提交](https://github.com/tModLoader/tModLoader/commit/57de6f9650aa4f05e36735a93683df0522e42422)

# v0.11.7 (Steam发布)
必须使用新的Terraria v1.4安装。
tModLoader从现在开始_不得_安装到Terraria文件夹中，但在手动安装的情况下应安装到单独的文件夹中。
模组不需要其他更改。

# v0.11.5
v0.11.5引入了`ContentInstance`，这是一种更快、更简单的访问模组类ID和实例的方法。

### 我需要更新吗
不需要，所有模组仍然可以工作，但如果您想在v0.11.5上发布，则需要更新。

### Mod.XType
如果您以前使用过泛型`Mod.XType<T>()`方法，则需要更改。`mod.ItemType<ExampleBlock>()`和`this.ItemType<ExampleBlock>()`现在只是`ItemType<ExampleBlock>()`，前提是您在每个.cs文件顶部写入`using static Terraria.ModLoader.ModContent;`。如果您不这样做，它将是`ModContent.ItemType<ExampleBlock>()`。对于位于`Mod`类中的方法调用，您需要保留`ModContent.XType`方法，因为否则会有命名空间冲突。如果您仍在使用字符串版本，则不需要更新，但新方法更快，泛型方法更不容易出错。[示例](https://github.com/tModLoader/tModLoader/commit/4bb65940e7928af8b6bf8fee30f935d37683505c)

### 正则表达式自动修复
您可以使用以下在Visual Studio中查找和替换命令轻松迁移整个模组。确保启用`使用正则表达式`。

查找：` (this|mod)\.(.*)Type<(.*)>\(\)`
替换：`ModContent.$2Type<$3>()`
示例：
![](https://i.imgur.com/DGycKCu.png)

如果您更喜欢`using static Terraria.ModLoader.ModContent;`方法，请改用以下查找和替换命令。这些也需要启用`使用正则表达式`。您需要手动修复`Mod`类中的调用以使用`ModContent.XType<>()`：

查找：`using Terraria.ModLoader;`
替换：`using Terraria.ModLoader;\nusing static Terraria.ModLoader.ModContent;`
查找：`mod\.(.*)Type<(.*)>\(\)`
替换：`$1Type<$2>()`

### Mod.GetModX和Instance字段
与上述类似，泛型`Mod.GetModX<T>()`方法（如`GetModWorld`）现在不再从`Mod`类实例调用。将`mod.GetModWorld<ExampleWorld>();`替换为`GetInstance<ExampleWorld>();`，前提是您在每个.cs文件顶部写入`using static Terraria.ModLoader.ModContent;`。如果您不这样做，它将是`ModContent.GetInstance<ExampleWorld>()`。此外，静态实例变量不再推荐。删除类似`public static ExampleConfigServer Instance;`的内容，改为调用`GetInstance<ExampleConfigServer>()`。另一个示例：`ExampleMod.Instance` -> `GetInstance<ExampleMod>()`。[示例](https://github.com/tModLoader/tModLoader/commit/4bb65940e7928af8b6bf8fee30f935d37683505c)

### Player.GetModPlayer<T>(Mod)
诸如`player.GetModPlayer<ExamplePlayer>(mod)`的语法现已弃用，使用`Player.GetModPlayer<T>()`即移除括号中的模组实例：`player.GetModPlayer<ExamplePlayer>()`。

### ModTile.RightClick
`ModTile.RightClick`现在是`ModTile.NewRightClick`。`NewRightClick`返回一个指示是否发生交互的bool，防止武器使用。要迁移，将`public override void RightClick(int i, int j)`替换为`public override bool NewRightClick(int i, int j)`，如果发生交互则添加`return true;`。[示例](https://github.com/tModLoader/tModLoader/commit/e47dcfb91811a5d1df96ec9d3d598e828adac577)

### 遍历Buff
v0.11.5添加了`Player.MaxBuffs`。如果您以前使用`for (int n = 0; n < 22; n++)`遍历buffs，您将希望将代码更新为`for (int n = 0; n < Player.MaxBuffs; n++)`。

### 反射
一如既往，您的反射可能已损坏，因此请仔细检查。许多内部类和字段的命名空间和标识符已更改。

例如，这是反射到加载模组进度条所需的更改。
```cs
//var type = assembly.GetType("Terraria.ModLoader.Interface");
//FieldInfo loadModsField = type.GetField("loadModsProgress", BindingFlags.Static | BindingFlags.NonPublic);
//Type UILoadModsProgressType = assembly.GetType("Terraria.ModLoader.UI.DownloadManager.UILoadModsProgress");

var type = assembly.GetType("Terraria.ModLoader.UI.Interface");
FieldInfo loadModsField = type.GetField("loadMods", BindingFlags.Static | BindingFlags.NonPublic);
Type UILoadModsProgressType = assembly.GetType("Terraria.ModLoader.UI.UILoadMods");
```

# v0.11

v0.11引入了许多更改。这是[迁移指南](https://docs.google.com/document/d/127Gmexzj533xMBC3ISmvWUz1yy6RZYjVjO8H2ZotwGM/edit?usp=sharing)。

# v0.10

这是[迁移指南](https://docs.google.com/document/d/1GY6Jyj0IkqfvQlXJUwXg60d2V8tIzumoNVgh5OWzGIc/edit?usp=sharing)
