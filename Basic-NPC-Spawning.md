# 基础NPC生成
本指南将教您生成敌人的基础知识。

# 基本概念
首先您需要理解几个概念：

## 平衡
很难为ModNPC.SpawnChance的返回值猜到一个好的值。我们不希望我们的NPC与原版NPC相比生成得太频繁。通常0.1f或更小的值是好的，但您应该使用[模组制作者工具箱的](https://forums.terraria.org/index.php?threads/modders-toolkit-a-mod-for-modders-doing-modding.55738/) NPC生成工具来将您的NPC的生成率与原版和其他模组的NPC进行比较。

## Terraria生成机制
Terraria通过首先决定生成NPC的位置，然后询问每个NPC是否愿意在该位置生成来生成NPC。每次Terraria决定生成NPC时，它都会与一个Player对象（NPCSpawnInfo.Player）一起执行。在多人游戏中，服务器处理所有生成决策。如果您在模组中制作了一个自定义生物群落但注意到在多人游戏中生成不能正常工作，您需要正确实现ModPlayer.SendCustomBiomes和相关hook，以便服务器知道自定义生物群落布尔值的正确值，这样它才能做出正确的决策。

## 返回值
ModNPC.SpawnChance hook返回一个float。如果不理解可以谷歌一下。ModNPC.CanTownNPCSpawn hook返回一个bool。

## ModNPC.SpawnChance
这是本指南的主要焦点。所有自然生成的非boss、非城镇NPC的ModNPC类都应该覆盖这个hook：

```c#
public override float SpawnChance(NPCSpawnInfo spawnInfo)
{
	// 代码在这里
}
```

## ModNPC.CanTownNPCSpawn
仅适用于城镇NPC。请注意此类返回一个bool而不是float。使用此方法让您的城镇NPC在满足某些条件（如击败boss）后生成。

```c#
public override bool CanTownNPCSpawn(int numTownNPCs)
{
	// 代码在这里
}
```

## 条件（if-else）
生成NPC归根结底是做出是否生成我们的ModNPC的决定。请在此处阅读if-else[相关内容](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/if-else)。

### 非运算符（！）
请在此处阅读[相关内容](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/logical-negation-operator)。

```
if(Main.dayTime) // 如果是白天
if(!Main.dayTime) // 如果是夜晚
```

### 与和或（&&和||）
请阅读[&&](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-and-operator)和[||](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-or-operator)。请注意&&的优先级高于||。我们将使用它来组合条件。

### = vs ==
不要混淆这两个。`=`给变量赋值，`==`比较两个值。确保永远不要做像`if(Main.hardMode = true)`这样的事情，否则您会困惑为什么您的世界突然变成了困难模式。

### 三元运算符
if-else条件更紧凑的版本是三元运算符。请在此处阅读[相关内容](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-operator)。基本上它将：

```c#
if (condition)
{
	return .1f;
}
else
{
	return 0f;
}
```

变为：

```
return condition ? .1f : 0f;
```

# NPCSpawnInfo
NPCSpawnInfo是一个结构，包含Terraria希望生成NPC的生成位置的所有信息。请参阅[文档](https://docs.tmodloader.net/docs/stable/struct_n_p_c_spawn_info.html)获取字段。我们将使用此结构中的值来指导我们的逻辑并得出最终决定。

## 玩家生物群落
使用NPCSpawnInfo中传入的Player对象而不是Main.LocalPlayer来在生成逻辑中使用玩家生物群落。[区域布尔值列表](https://docs.tmodloader.net/docs/stable/struct_n_p_c_spawn_info.html#a7d41785192c2cd08b34bd2b684965dc0)

```cs
if(spawnInfo.Player.ZoneJungle) // 原版生物群落即区域
if(spawnInfo.Player.GetModPlayer<ExamplePlayer>().ZoneExample) // 模组生物群落
```

## 高度
当世界生成时，几个值会与世界一起保存以指定各种高度。Main.worldSurface是生成点下方几个瓦片，Main.rockLayer在其下方，岩石比泥土更普遍。Main.maxTilesY是Y在世界中的最大值，即最低点。我们可以使用这些值和数学来驱动我们的生成条件。我们也可以使用下图所示的预定义区域，而不是弄乱数学。
![](https://i.imgur.com/9rIgSMt.png)

在图像右侧，我们看到预定义给我们的区域，左侧是我们驱动这些区域的数学。例如，以下是等效的：
```c#
if(spawnInfo.Player.ZoneRockLayerHeight)
```
```c#
if(spawnInfo.SpawnTileY <= Main.maxTilesY - 200 && spawnInfo.SpawnTileY > Main.rockLayer)
```

我们可以使用简单的数学来制作更精确的基于高度的生成条件。例如，`if(spawnInfo.spawnTileY <= Main.maxTilesY - 200 && spawnInfo.SpawnTileY > (Main.rockLayer + Main.maxTilesY - 200) / 2)`可用于指定如上所示的ZoneRockLayerHeight下半部分的生成条件。预定义的高度区域易于使用，但请记住您可以使用数学来实现更具体的行为。还要记住Y坐标从天空中的0开始，随着您在世界中向下走而增加值。不要被WorldGen.lavaLine、WorldGen.waterLine、WorldGen.worldSurfaceHigh和其他未在此处提到的值所迷惑，它们没有保存在世界文件中，不适用于NPC生成。

## 横向位置
一些敌人很少使用X位置进行生成概率。例如，哥布林侦察兵和史莱姆王只在世界边缘生成。`Main.maxTilesX`可用于逻辑中来实现此目的。例如，`SpawnCondition.GoblinScout`内部使用以下逻辑仅在地图最左右六分之一处生成：`Math.Abs(spawnInfo.SpawnTileX - Main.spawnTileX) > Main.maxTilesX / 3`

# 其他值
除了NPCSpawnInfo，我们还可以在SpawnChance逻辑中使用其他字段：
* `Main.dayTime` - 白天为true，夜晚为false
* `NPC.downedGolemBoss`及[其他](https://github.com/tModLoader/tModLoader/wiki/NPC-Class-Documentation#downedboss1) - 如果该boss在此世界中已被击败则为true
* `Main.hardMode` - 如果在困难模式中为true
* `Main.expertMode` - 如果在专家模式中为true
* `Main.time` - 白天时为0（凌晨4:30）到54000（下午7:30）之间的值，夜晚时为0（下午7:30）到32400（凌晨4:30）之间的值。与`Main.dayTime`一起使用。`Main.time`通常每刻增量1。每个游戏内小时是3600刻。
  * 示例：`Main.dayTime && Main.time < 18000.0` - 凌晨4:30到上午9:30之间的早晨（因为18000/3600 == 5）
  * `Utils.GetDayTimeAs24FloatStartingFromMidnight`方法可用于简化基于世界时间的生成条件。示例：`Utils.GetDayTimeAs24FloatStartingFromMidnight() < 6.00f` - 早上6:00之前
* `Main.raining` - 如果当前下雨则为true
* `NPC.AnyNPCs(NPCID.IceGolem)` - 如果有任何冰巨人在世界中则为true。与!一起使用以防止mini-boss的重复生成。
  * `NPC.AnyNPCs(ModContent.NPCType<PartyZombie>())` - 相同，但用于模组NPC
* `NPC.CountNPCS(NPCID.AngryNimbus) < 2` - 如果世界中存在少于2个该NPC则为true
* `TileID.Sets.Conversion.Sand[spawnInfo.SpawnTileType]` - 如果生成瓦片是任何类型的沙瓦片则为true。还有其他TileID.Sets.Conversion可能有用
* `Math.Abs(spawnInfo.SpawnTileX - Main.spawnTileX) > Main.maxTilesX / 3` - 如果生成瓦片在地图外侧三分之一处则为true
* `NPC.waveNumber` - 活动期间的波数
* 需要更多？在Discord上向我们寻求帮助，我们可以将其添加到此列表。

# SpawnCondition
`SpawnCondition`是一个类，包含模仿各种原版NPC生成条件的现成可用字段集。请参阅[文档](https://docs.tmodloader.net/docs/stable/class_spawn_condition.html)获取可用的`SpawnCondition`。使用`SpawnCondition`字段可以简化您的`SpawnChance`逻辑。例如，白天史莱姆可以简单地这样实现：
```c#
return SpawnCondition.OverworldDaySlime.Chance * 0.1f;
```
而不是：
```c#
return Main.dayTime && spawnInfo.SpawnTileY <= Main.worldSurface ? 0.1f : 0f;
```

# 示例
以下每个示例都好像在ModNPC.SpawnChance方法内部：

```
public override float SpawnChance(NPCSpawnInfo spawnInfo)
{
	// 示例代码在这里
}
```

### 在我的ModTile上生成
```
return spawnInfo.SpawnTileType == ModContent.TileType<Tiles.CrystalBlock>() ? .1f : 0f;
```
### 如果玩家在自定义生物群落/区域中则生成
```
return spawnInfo.Player.GetModPlayer<CrystalPlayer>().ZoneCrystal ? .1f : 0f;
```
### 在丛林神庙中生成
```
return spawnInfo.SpawnTileType == TileID.LihzahrdBrick && spawnInfo.lihzahrd ? .1f : 0f;
// 或者
return SpawnCondition.JungleTemple.Chance * 0.1f;
```
### 日食期间生成
```
return spawnInfo.SpawnTileY <= Main.worldSurface && Main.dayTime && Main.eclipse
// 或者
return SpawnCondition.SolarEclipse.Chance * 0.05f; // 记住测试这个值以达到平衡
```
### 玩家站在Sunplate瓦片上
// 在这里我展示了将布尔值转换为int的2种方法。（False是0，True是1）
```
return (Main.tile[spawnInfo.PlayerFloorX, spawnInfo.PlayerFloorY].TileType == TileID.Sunplate).ToInt() * 0.2f;
// 或者
return Convert.ToInt32(Main.tile[spawnInfo.PlayerFloorX, spawnInfo.PlayerFloorY].TileType == TileID.Sunplate) * 0.2f; // 使用System;
```

# 组合代码片段
就像上面的示例一样，我们组合逻辑片段来构建我们的最终决定。请参阅上面的!、&&和||。

## 组合bool和float
SpawnCondition字段返回表示概率的浮点值，而许多其他条件只是bool。这可能导致一些棘手的代码。让我们尝试组合一个应该在蜘蛛洞穴但仅在夜晚生成的NPC的`SpawnChance`代码。我们可以为此使用SpawnCondition.SpiderCave和Main.dayTime。

### 简单语法
如果您不太了解c#，只需将bool和float分开。使用bool在if语句中根据需要使用!、&&和||，然后如果我们通过这些条件，在返回中使用`SpawnCondition`。如果条件失败，代码将返回0，意味着NPC将不会生成：
```cs
if(!Main.dayTime)
	return SpawnCondition.SpiderCave.Chance * 0.1f;
return 0;
```

### 中等语法
使用我们上面学习的三元运算符，我们可以使生成条件逻辑更紧凑。
```cs
return !Main.dayTime ? SpawnCondition.SpiderCave.Chance * 0.1f : 0;
```

### 复杂语法
如果您发现您的逻辑在将bool解释为false时为0、true时为1时更有意义，您可以这样做。这种方法并不常见，但可能有用：
```cs
return (!Main.dayTime).ToInt() * SpawnCondition.SpiderCave.Chance * 0.1f;
```

# 常见错误
### 为什么我的世界突然变成了困难模式？
许多新手程序员混淆`=`和`==`。`=`给变量赋值，`==`比较值。

如果您做`if (Main.hardMode = true)`，您正在给hardMode赋值true，本质上是将世界直接推进困难模式，这不是您想要的。确保做`if (Main.hardMode == true)`或更好，做`if (Main.hardMode)`。

### CS0161 '[ClassName].SpawnChance(NPCSpawnInfo)': 并非所有代码路径都返回值
这意味着您的代码有可能不返回值。例如。

```
if (spawnInfo.Granite)
	return 0.2f;
```
需要这样修复：

```
if (spawnInfo.Granite)
	return 0.2f;
return 0f;
```

### CS0029 无法隐式转换类型'bool'到'float'
这意味着您可能忘记使用逻辑来决定要返回的值。请参阅上文。

# 相关参考
* [ModNPC.SpawnChance文档](https://docs.tmodloader.net/docs/stable/class_mod_n_p_c.html#a9eb89f7c9fa0eef966bc36a29f848cd6)
* [NPCSpawnInfo文档](https://docs.tmodloader.net/docs/stable/struct_n_p_c_spawn_info.html)
* [SpawnCondition文档](https://docs.tmodloader.net/docs/stable/class_spawn_condition.html)

# 基础级别未涵盖的内容
* ModNPC.CheckConditions - 自定义城镇NPC房屋条件（如松露）
* Mod Boss布尔值 - 参阅ExampleMod了解正确的同步和使用
* 使用ModNPC.SpawnNPC - 在生成时操作NPC
* GlobalNPC.EditSpawnRate - 操作最大生成数和生成率（水蜡烛）
* GlobalNPC.EditSpawnRange
* GlobalNPC.EditSpawnPool - 在填充了选择后操作生成池
* GlobalNPC.SpawnNPC