***
本指南针对tModLoader v1.4.4编写，这是最新版本的tModLoader。在旧版tModLoader中，本地化文件不会自动更新，指南中提到的某些方法也不存在。不过基本概念仍然适用。
***

[中文版 | Chinese version](https://github.com/tModLoader/tModLoader/wiki/zh‐Localization‐本地化)<br />
[Русская версия страницы | Russian version](https://github.com/tModLoader/tModLoader/wiki/ru-Localization-Локализация)

---

<!-- Generated with https://luciopaiva.com/markdown-toc/ -->
# 目录

- [什么是本地化？](#什么是本地化)
- [支持的语言](#支持的语言)
- [从1.4.3迁移到1.4.4](#从143迁移到144)
  - [在1.4.3上生成本地化文件](#在143上生成本地化文件)
  - [切换到1.4.4 tModLoader并构建Mod](#切换到144-tmodloader并构建mod)
- [本地化工作流程](#本地化工作流程)
  - [实时更新](#实时更新)
- [本地化工作原理](#本地化工作原理)
  - [本地化键](#本地化键)
  - [替换](#替换)
  - [字符串格式化](#字符串格式化)
  - [复数形式](#复数形式)
  - [聊天标签](#聊天标签)
  - [NetworkText类](#networktext类)
  - [时序注意事项](#时序注意事项)
- [自动本地化文件](#自动本地化文件)
  - [添加内容](#添加内容)
  - [HJSON语法](#hjson语法)
  - [注释](#注释)
  - [翻译文件名](#翻译文件名)
  - [多个文件](#多个文件)
- [添加新的翻译键](#添加新的翻译键)
  - [手动添加键](#手动添加键)
  - [添加可本地化属性](#添加可本地化属性)
- [添加新语言](#添加新语言)
- [覆盖现有的Terraria键](#覆盖现有的terraria键)
- [问题排查](#问题排查)

# 什么是本地化？

本地化使得使用不同语言的玩家也能游玩和享受模组的内容。每个玩家在游戏中看到的文本内容都包含在称为本地化文件的文本文件中。例如，ExampleMod中有一个物品在英文中叫"Paper Airplane"，但在俄语中叫"Бумажный самолётик"。通过使用本地化，俄语用户可以在不学习英语的情况下理解和享受ExampleMod的内容。

这些本地化文件易于操作，允许没有技术能力的用户翻译模组。这些翻译可以提供给模组作者或作为新模组发布，让更多人能够享受该模组。

**即使你不打算将自己模组本地化为其他语言，你仍然需要为所支持的那一种语言使用本地化文件。**

本 wiki 页面涵盖面向模组开发者的本地化主题。如果你对本地化现有模组或为tModLoader本身提供本地化感兴趣，请阅读[贡献本地化 wiki 页面](https://github.com/tModLoader/tModLoader/wiki/Contributing-Localization)。

# 支持的语言

目前，tModLoader支持以下语言区域。以下是相应的默认文件名。注意，如果使用[前缀](#翻译文件名)，文件名可能不同：

| 语言 | 缩写 | 文件名 |
|---------------------:|:-------------|---------------|
|                      |              |               |
| 英语              | en-US        | en-US.hjson   |
| 德语               | de-DE        | de-DE.hjson   |
| 意大利语              | it-IT        | it-IT.hjson   |
| 法语               | fr-FR        | fr-FR.hjson   |
| 简体中文   | zh-Hans      | zh-Hans.hjson |
| 西班牙语              | es-ES        | es-ES.hjson   |
| 俄语              | ru-RU        | ru-RU.hjson   |
| 巴西葡萄牙语 | pt-BR        | pt-BR.hjson   |
| 波兰语               | pl-PL        | pl-PL.hjson   |

# 从1.4.3迁移到1.4.4
如果你正在更新上次在1.4.3期间更新的模组，请展开本节并仔细阅读。

<details><summary>从1.4.3迁移到1.4.4的详细内容</summary>

从tModLoader v2023.01开始，所有本地化工作现在完全在`.hjson`文件中完成。在代码中声明翻译已不再支持。这一更改将大大简化本地化管理，使模组翻译更容易创建。如果你对了解更多关于这些更改的理由感兴趣，请参见[主要本地化更改功能提案](https://github.com/tModLoader/tModLoader/issues/3074)。

**如果你没有使用Git或某种形式的版本控制，建议在继续之前备份模组的源代码文件夹。**

## 在1.4.3上生成本地化文件
首先，我们需要使用旧版tModLoader导出本地化文件。你需要使用Steam切换到`1.4.3-legacy`分支。（[切换tModLoader版本说明](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ#switch-to-stable-tmodloader-or-to-preview-tmodloader)）

游戏版本正确后，打开tModLoader，启用模组，然后访问"Mod Sources"菜单。在列表中找到你的模组。你会看到一个绿色箭头，悬停时显示"Export 1.4.4+ localization files"，点击它。
![image](https://user-images.githubusercontent.com/4522492/210681409-a659670d-5908-4e5d-bd45-74c0545f4666.png)
现在导航到`"ModSources"`文件夹，然后导航到你的模组的本地化文件。如果你之前没有本地化文件，导航到模组文件夹的顶层。你应该会看到新生成的`.hjson.new`文件：
![image](https://user-images.githubusercontent.com/4522492/210681629-8aad2234-bd56-40c8-b03f-7e36b98d4486.png)
用文本编辑器打开新文件，确认它们看起来正确。应该包含当前`.hjson`文件中所有的条目，以及为模组中所有其他内容新生成的条目。如果看起来没问题，继续下一步。

### 更改的键
注意，许多键的模式已经更改。这些会在导出时自动调整，但任何自定义键或使用旧键模式的代码都需要由模组作者修复。例如，`Mods.{ModName}.ItemName.{ContentName}`现在是`Mods.{ModName}.Items.{ContentName}.DisplayName`。

<details><summary>更改的键模式</summary>

```
Mods.{ModName}.DamageClassName.{ContentName}		Mods.{ModName}.DamageClasses.{ContentName}.DisplayName
Mods.{ModName}.InfoDisplayName.{ContentName}		Mods.{ModName}.InfoDisplays.{ContentName}.DisplayName
Mods.{ModName}.BiomeName.{ContentName}			Mods.{ModName}.Biomes.{ContentName}.DisplayName
Mods.{ModName}.BuffName.{ContentName}			Mods.{ModName}.Buffs.{ContentName}.DisplayName
Mods.{ModName}.BuffDescription.{ContentName}		Mods.{ModName}.Buffs.{ContentName}.Description
Mods.{ModName}.ItemName.{ContentName}			Mods.{ModName}.Items.{ContentName}.DisplayName
Mods.{ModName}.ItemTooltip.{ContentName}		Mods.{ModName}.Items.{ContentName}.Tooltip
Mods.{ModName}.NPCName.{ContentName}			Mods.{ModName}.NPCs.{ContentName}.DisplayName
Mods.{ModName}.Prefix.{ContentName}			Mods.{ModName}.Prefixes.{ContentName}.DisplayName
Mods.{ModName}.ProjectileName.{ContentName}		Mods.{ModName}.Projectiles.{ContentName}.DisplayName
Mods.{ModName}.ResourceDisplaySet.{ContentName}		Mods.{ModName}.ResourceDisplaySets.{ContentName}.DisplayName
Mods.{ModName}.Containers.{ContentName}			Mods.{ModName}.Tiles.{ContentName}.ContainerName
Mods.{ModName}.MapObject.{ContentName}			Mods.{ModName}.Tiles.{ContentName}.MapEntry
Mods.{ModName}.Keybind.{ContentName}			Mods.{ModName}.Keybinds.{ContentName}.DisplayName
```

</details>

## 切换到1.4.4 tModLoader并构建Mod
使用Steam切换到"None"分支以返回1.4.4 tModLoader。（[切换tModLoader版本说明](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ#switch-to-stable-tmodloader-or-to-preview-tmodloader)）

启动tModLoader后，你会注意到你的模组（以及你启用的大多数其他模组）无法加载，这是预期的。访问Mod Sources菜单，按下"Run tModPorter"按钮。除了其他更改外，这还将移除所有使用旧本地化方法的代码。
![image](https://user-images.githubusercontent.com/4522492/210683375-43816104-2812-4db2-bac6-813ebb47a089.png)
之后，你需要找到那些`.hjson.new`文件，并用它们替换现有的`.hjson`文件。首先删除当前的`.hjson`文件（如果有的话），然后将`.hjson.new`文件重命名为`.hjson`扩展名。（如果你无法重命名文件扩展名，你需要[启用"文件扩展名"](https://github.com/tModLoader/tModLoader/assets/4522492/2f901fe9-db01-4bff-b930-449220419ae7)。）

现在，你可能需要打开Visual Studio并修复任何剩余的编译问题。修复完所有剩余问题后，你可以重新构建模组，它应该可以工作了。问题解决后，你可以搜索模组源代码中包含`// Tooltip.SetDefault("This is a modded Item.");`或`// DisplayName.SetDefault("Example Sword");`的行并删除它们。它们将不再被使用。（你可以在项目中搜索`.SetDefault(`来轻松找到这些行中的大多数。）

你可以使用以下正则表达式来帮助更轻松地找到这些调用（并将它们替换为空字符串）。两者都需要使用Notepad++之类的工具，并启用". matches newline"选项
- 单行注释：`\s+// [\w.]+SetDefault\(".+?;`
- 多行注释：`\s+/\*[\s\w.]+SetDefault\(".+?\*/`

Notepad++快速指南：将此应用于整个模组：打开任何文件与Notepad++，按ctrl + F，点击"Find in Files"选项卡，在底部选择"Regular Expression"和". matches newline"，然后在"Filters:"添加`*.cs`，对于"Directory:"点击右侧按钮导航到模组的根文件夹。最后，将正则表达式粘贴到"Find what:"中，并将"Replace with:"留空，然后按"Replace in Files"。如果犯了错误，只需按一次ctrl + Z即可还原所有更改。建议使用git之类的版本控制软件，以便在需要时能够手动还原，并检查结果。

</details>

# 本地化工作流程

本地化文件在模组加载结束时更新。这意味着模组开发者需要在添加内容后构建并加载模组，以便本地化文件更新。更新后，模组开发者可以编辑`.hjson`文件来添加翻译。完成后，可以再次构建并重新加载模组，使翻译显示在游戏中。

为避免丢失工作，请注意预期的工作流程：

1. 向模组添加新内容，例如新的`ModItem`
2. 构建并重新加载模组
3. 此时，`.hjson`文件将自动更新，添加新内容的条目。编辑`.hjson`文件，给新内容起一个英文名称
4. 构建并重新加载模组
5. 非英文的`.hjson`文件现在会更新为适当的占位符，翻译者或模组制作者也可以更新它们

如果翻译者向你发送更新的`.hjson`文件直接添加到你的模组中，请注意，如果模组加载且tModLoader检测到`.tmod`文件比`.hjson`文件新，它可能会被覆盖。在这种情况下，最好的选择是在加载之前构建模组，或者在启动tModLoader时按住Shift键跳过加载模组，然后立即访问Mod Sources菜单来构建模组。如果你忘记了这样做，发现tModLoader将新翻译的`.hjson`文件还原为旧内容，请将更新的`.hjson`文件再次复制到模组源文件夹，然后构建并重新加载。

## 实时更新

tModLoader会检测何时保存ModSources文件夹中的`.hjson`文件，并会在游戏中自动重新加载它们。通过使用这种方法，模组开发者不需要重新构建和重新加载模组来测试新值。如果你使用此功能，请记得在发布之前重新构建模组，以便所有更改都进入发布的模组中。

这里我们可以看到这个功能的实际效果。模组开发者编辑`en-US.hjson`文件，然后保存以更改`ExampleWings`物品的英文显示名称和提示。几秒钟后更改就会出现在游戏中：

https://user-images.githubusercontent.com/4522492/229942438-26604fd7-9073-436c-b2ab-d99b4f2efeb7.mp4

# 本地化工作原理

游戏中的每个文本片段，从物品名称到主菜单上的文字，都使用本地化。游戏中的每个文本实际上是一对数据：一个本地化键和一个本地化值。例如，当玩家创建一个小世界时，游戏使用`UI.WorldSizeSmall`的本地化键来查找当前加载语言的正确翻译值，如果选择了英语，则向用户显示"Small"一词。如果选择了另一种语言，游戏仍然查找`UI.WorldSizeSmall`，但本地化值会有所不同。由于Terraria的创建者用英语编写代码，大多数本地化键与其英语翻译值非常相似。

在tModLoader中，模组使用`.hjson`文件来清晰地包含本地化键和本地化值。每种语言都有自己的`.hjson`文件。如果你熟悉JSON，这些`.hjson`文件会让你感到熟悉。

这是一个简单的例子：

文件名：**tModLoader/ModSources/ExampleMod/en-US.hjson**
```
Mods: {
	ExampleMod: {
		Items: {
			ExampleItem: {
				DisplayName: Example Item
				Tooltip: This is a modded Item.
			}
		}
	}
}
```

在这个例子中，我们可以看到两个主要概念：本地化键和本地化翻译。本地化键由每层嵌套中`:`左侧的所有文本组合派生而成。`:`右侧的文本是本地化值。这个例子传达了两个本地化键及其对应的本地化值：`Mods.ExampleMod.Items.ExampleItem.Displayname`对应`Example Item`，`Mods.ExampleMod.Items.ExampleItem.Tooltip`对应`This is a modded Item.`。如果语法看起来复杂，不用担心，模组开发者不需要手动编辑这些文件，游戏会自动更新它们。

当这个模组加载时，tModLoader会找到与当前语言对应的所有本地化文件并将它们存储在内存中。当游戏需要向用户显示文本时，使用键来查询存储的数据并检索正确的文本。翻译作为`LocalizedText`对象存储在内存中，模组开发者可以使用`Language.GetText`方法从本地化键检索`LocalizedText`对象。可以使用`Value`属性从`LocalizedText`对象检索本地化值。或者，`Language.GetTextValue`方法直接从本地化键返回本地化值：

```cs
string hivePackDialogue = Language.GetTextValue("Mods.ExampleMod.Dialogue.ExampleTravelingMerchant.HiveBackpackDialogue");
或者
string hivePackDialogue = Language.GetText("Mods.ExampleMod.Dialogue.ExampleTravelingMerchant.HiveBackpackDialogue").Value;
或者
LocalizedText hivePackDialogueLocalizedText = Language.GetText("Mods.ExampleMod.Dialogue.ExampleTravelingMerchant.HiveBackpackDialogue");
string hivePackDialogue = hivePackDialogueLocalizedText.Value;
```

## 本地化键

tModLoader会自动为大多数内容分配翻译键。键的模式是`Mods.{ModName}.{Category}.{ContentName}.{DataName}`，其中`ModName`是模组的内部名称，`Category`由内容类型提供，`ContentName`是内容的内部名称（通常是类名），`DataName`指定类中的键。

例如，`ModItem`的`Category`设置为`Items`。它还有两个独立的数据：`DisplayName`和`Tooltip`。如果一个名为`ExampleMod`的模组添加了一个名为`ExampleItem`的`ModItem`类，则会生成两个键并添加到`.hjson`文件中：`Mods.ExampleMod.Items.ExampleItem.DisplayName`和`Mods.ExampleMod.Items.ExampleItem.Tooltip`。

**注意：** 避免在本地化键中使用空格和其他特殊字符。

## 替换

如果你的本地化文件中经常重复某些文本，或者如果你希望使用游戏中现有的文本，你可以使用替换来保持本地化文件的整洁和有序。替换采用`{$KeyHere}`的形式放在本地化值中。当游戏加载时，这些部分将被与提供的键对应的本地化文本替换。

例如，游戏已经有关于`Right Click To Open`文本的翻译，存储在`CommonItemTooltip.RightClickToOpen`键中。模组可以利用替换来重用该值。条目`Tooltip: "{$CommonItemTooltip.RightClickToOpen}"`最终会在用户语言中显示该物品的`Right Click To Open`文本。其他现有翻译（如物品名称和其他常见提示）也可用于此目的。

模组内部的翻译也可以使用。例如，在[ExampleMod的本地化文件](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Localization/en-US.hjson)中，`MapObject.ExamplePylonTile: "{$Mods.ExampleMod.ItemName.ExamplePylonItem}"`用于重用`Mods.ExampleMod.ItemName.ExamplePylonItem`键中包含的翻译。

许多替换中有`{0}`或`{1}`，这些是模组开发者可以提供的值的占位符。此功能在[字符串格式化部分](#字符串格式化)中说明。

### 现有的物品提示

在模组物品中使用游戏提供的现有物品提示是个好主意。使用一致的语言和现有翻译可以使你的模组对更多人更有吸引力。展开以下部分查看现有常见提示的列表。请记住，所有这些的键都将以`CommonItemTooltip.`开头。

<details><summary>CommonItemTooltip 键</summary>

```
// 由Terraria添加
"UsesLife": "使用 {0} 点生命",
"UsesMana": "消耗 {0} 点魔力",
"RestoresLife": "恢复 {0} 点生命",
"RestoresLifeRange": "恢复 {0} 到 {1} 点生命",
"RestoresMana": "恢复 {0} 点魔力",
"MinuteDuration": "持续 {0} 分钟",
"SecondDuration": "持续 {0} 秒",
"PlaceableOnXmasTree": "可放置在圣诞树上",
"String": "增加悠悠球范围",
"Counterweight": "用悠悠球击中敌人后投出配重",
"BannerBonus": "附近玩家获得对以下敌人的加成：",
"BannerBonusReduced": "附近玩家获得对以下敌人的小幅度加成：",
"SpecialCrafting": "用于特殊制作",
"DevItem": "'非常适合冒充开发者！'",
"FlightAndSlowfall": "允许飞行和缓慢下落",
"PressDownToHover": "按下向下键切换悬停\n按下向上键取消悬停",
"PressUpToBooster": "按住向上键加速！",
"RightClickToOpen": "<right> 打开",
"RightClickToClose": "<right> 关闭",
"MinorStats": "所有属性小幅提升",
"MediumStats": "所有属性中等提升",
"MajorStats": "所有属性大幅提升",
"TipsyStats": "近战属性小幅提升但防御降低",
"EtherianManaCost10": "防御Eternia水晶时每次使用消耗10点以太魔力",
"GolfBall": "可以用高尔夫球杆击打",
"Sentry": "召唤一个哨兵",
"GolfIron": "全能型球杆，适合中距离\n高尔夫球可以凭借不错的垂直仰角飞行相当距离",
"GolfPutter": "专用推杆，用于完成推杆\n高尔夫球在短距离内紧贴地面，适合精准击球",
"GolfWedge": "专用劈起杆，用于沙坑或高障碍物\n高尔夫球可以获得大量垂直仰角但飞行距离不远",
"GolfDriver": "强力球杆，用于远距离\n高尔夫球可以飞行很远，但垂直仰角很小",
"Kite": "有风的日子可以放风筝\n<right>收回",
"LavaFishing": "允许在岩浆中钓鱼",
"CreativeSacrificeNeeded": "再研究 {0} 个来解锁复制",
"CreativeSacrificeComplete": "解锁复制",
"TeleportationPylon": "当2个村民在附近时传送到另一个水晶塔\n每种类型只能放置一个，且需位于对应的生物群系中",
"Whips": "你的召唤物将集中攻击被击中的敌人",
"WizardHatDuringAnniversary": "最大召唤物数量增加1",
"MechSummonDuringEverything": "'套装的一部分'",
"MechdusaSummonNotDuringEverything": "'在这个世界上没有效果'",
"LuminiteVariant": "'来自远方的禁忌建筑材料'",

// 由tModLoader添加
"IncreasesMaxLifeBy": "最大生命增加 {0}",
"IncreasesMaxManaBy": "最大魔力增加 {0}",
"IncreasesMaxLifeByPercent": "最大生命增加 {0}%",
"IncreasesMaxManaByPercent": "最大魔力增加 {0}%",

"IncreasesBowDamageByPercent": "弓箭伤害增加 {0}%",
"IncreasesGunDamageByPercent": "枪械伤害增加 {0}%",
"IncreasesSpecialistDamageByPercent": "专家射手伤害增加 {0}%",

"IncreasesWhipRangeByPercent": "鞭子范围增加 {0}%",
"IncreasesMaxMinionsBy": "最大召唤物数量增加 {0}",
"IncreasesMaxSentriesBy": "最大哨兵数量增加 {0}",

"IncreasesFishingPowerBy": "钓鱼能力增加 {0}",

"PermanentlyIncreasesMaxLifeBy": "永久增加最大生命 {0}",
"PermanentlyIncreasesMaxManaBy": "永久增加最大魔力 {0}",

"ReducesDamageTakenByPercent": "受到伤害减少 {0}%",

"PercentChanceToSaveAmmo": "{0}% 几率节省弹药",
"PercentReducedManaCost": "魔力消耗减少 {0}%",

"PercentIncreasedMiningSpeed": "采矿速度增加 {0}%",
"PercentIncreasedMovementSpeed": "移动速度增加 {0}%",

"ArmorPenetration": "{0} 护甲穿透",
"PercentIncreasedDamage": "伤害增加 {0}%",
"PercentIncreasedCritChance": "暴击率增加 {0}%",
"PercentIncreasedDamageCritChance": "伤害和暴击率增加 {0}%",

"PercentIncreasedMagicDamage": "魔法伤害增加 {0}%",
"PercentIncreasedMagicCritChance": "魔法暴击率增加 {0}%",
"PercentIncreasedMagicDamageCritChance": "魔法伤害和暴击率增加 {0}%",

"PercentIncreasedMeleeDamage": "近战伤害增加 {0}%",
"PercentIncreasedMeleeCritChance": "近战暴击率增加 {0}%",
"PercentIncreasedMeleeDamageCritChance": "近战伤害和暴击率增加 {0}%",
"PercentIncreasedMeleeSpeed": "近战速度增加 {0}%",

"PercentIncreasedRangedDamage": "远程伤害增加 {0}%",
"PercentIncreasedRangedCritChance": "远程暴击率增加 {0}%",
"PercentIncreasedRangedDamageCritChance": "远程伤害和暴击率增加 {0}%",

"PercentIncreasedSummonDamage": "召唤伤害增加 {0}%",
"SummonTagDamage": "{0} 召唤标签伤害",
"PercentSummonTagCritChance": "{0}% 召唤标签暴击率"
```

</details>

### 其他现有翻译

除了`CommonItemTooltip.`翻译键外，模组开发者还可以引用游戏中的任何其他翻译键。例如，`NPCName.BlueSlime`是获取蓝史莱姆本地化名称的键。模组开发者可以通过下载[Terraria Workshop指南的高级语言包部分](https://forums.terraria.org/index.php?threads/the-ultimate-guide-to-content-creation-and-use-for-the-terraria-workshop.100652/#advancedlanguagepack)中提到的`.CSV`文件来查看所有翻译键。

请注意，模组翻译键与Terraria内容的键模式不同。例如，ExampleMod的`PartyZombie`的翻译键是`Mods.ExampleMod.NPCs.PartyZombie.DisplayName`。模组内容的翻译键不一定遵循默认模式，因此代码不应基于该假设编写。

### 范围简化

如果替换键与它们所使用的本地化键的值共享范围，则可以简化替换键。例如，在[ExampleMod的本地化文件](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Localization/en-US.hjson)中，`Mods.ExampleMod.Items.ExamplePetItem.DisplayName`键的值设置为`"{$Common.PaperAirplane}"`。在这种情况下，游戏知道检查当前范围内的键，从而找到并替换`Mods.ExampleMod.Common.PaperAirplane`键的值。使用这种技术，可以从替换键中省略`Mods.ModName`。替换键的更多文本可以省略，只要它与它所使用的值的键匹配得越多。

<details><summary>展开查看详细逻辑</summary><blockquote>

替换键被附加到当前键，然后是当前键没有最后一个后缀的形式，依此类推，直到找到现有的键。

键：`Mods.ExampleMod.Items.ExamplePetItem.DisplayName`
值：`Common.PaperAirplane`
检查键`Mods.ExampleMod.Items.ExamplePetItem.DisplayName.Common.PaperAirplane`，不存在。
检查键`Mods.ExampleMod.Items.ExamplePetItem.Common.PaperAirplane`，不存在。
检查键`Mods.ExampleMod.Items.Common.PaperAirplane`，不存在。
检查键`Mods.ExampleMod.Common.PaperAirplane`，存在，所以用它进行替换。

</blockquote></details>

### 覆盖内容本地化键

如果模组中的许多物品共享一个常用翻译，你可以让它们都指向同一个翻译键。为此，重写属性并返回使用你希望使用的翻译键的`Language.GetOrRegister`结果：
```cs
public override LocalizedText Tooltip => Language.GetOrRegister("Mods.ExampleMod.Common.SomeSharedTooltip");
```
如果你使用继承，你只需要在基类中执行此操作，如果特定子类需要不同的翻译，甚至可以在子类中再次重写它。参见[添加可本地化属性](#添加可本地化属性)，了解如何向你的内容添加额外的本地化（超出tModLoader提供的默认属性）。

模组开发者可以使用`public override LocalizedText Tooltip => LocalizedText.Empty;`来表示不应生成翻译键。使用它可以保持本地化文件更整洁。

## 字符串格式化

模组开发者可以使用[字符串格式化](https://learn.microsoft.com/en-us/dotnet/api/system.string.format?view=net-7.0#insert-a-string)在翻译中留出填充位置。这是C#的正常特性。模组开发者可以使用`string.Format`方法或`Language.GetTextValue`重载来使用字符串格式化。以下占位符部分有更多信息。

### 占位符

你可能会在翻译条目中看到类似`Missing mod: {0} required by {1}`的内容。`{0}`和`{1}`是占位符，文本将在使用时由游戏根据代码逻辑填充。这类似于普通C#编程中使用`string.Format`的方式。你可能需要在游戏中查看文本才能理解用法，从而进行准确的本地化。

### 将值绑定到本地化

许多翻译条目中有`{0}`或`{1}`等占位符，表示模组开发者可以在其中提供值的位置。例如，翻译键`CommonItemTooltips.IncreasesMaxMinionsBy`的值是`Increases your max number of minions by {0}`。为了在配饰物品中使用它，我们需要能够提供一个数字来替换`{0}`占位符。

首先，在`.hjson`文件中，我们为物品分配提示：
```
ExampleMinionBoostAccessory: {
	DisplayName: Minion Booster
	Tooltip: "{$CommonItemTooltip.IncreasesMaxMinionsBy}"
}
```

接下来，我们需要将我们打算的值"绑定"到这个提示。这个配饰旨在将最大召唤物增加3。为此，我们重写`Tooltip`属性并对原始提示调用`WithFormatArgs`方法。这将用提供的值填充占位符。我们建议在类中使用`static readonly int`字段来存储这些数值。在下面的例子中，`MaxMinionIncrease`在两个不同的地方使用。使用字段允许模组开发者同时更改行为和提示。字段为`readonly`可以防止在运行时意外尝试修改值，这在与`WithFormatArgs`一起使用时不起作用。这种方法可以防止可能导致的提示和行为不同步的拼写错误。

```cs
public class ExampleMinionBoostAccessory : ModItem
{
	public static readonly int MaxMinionIncrease = 3;

	public override LocalizedText Tooltip => base.Tooltip.WithFormatArgs(MaxMinionIncrease);

	public override void UpdateEquip(Player player) {
		player.maxMinions += MaxMinionIncrease; // 将玩家可以拥有的召唤物数量增加三个
	}
	
	// 其他代码...
}
```

### LocalizedText.Format方法

在上一节中，`LocalizedText.WithFormatArgs`方法用于将值绑定到`LocalizedText`实例。这对于一次性赋值很有用。然而，有时我们希望用会改变的值格式化`LocalizedText`，并且每次显示文本时都需要更新。为此，我们使用`LocalizedText.Format`方法，传入参数来填充占位符。然而，此方法直接返回最终的`string`，它不会将值绑定到`LocalizedText`。我们在游戏过程中执行的代码中使用`LocalizedText.Format`，例如`ModItem.ModifyTooltips`。

```cs
public class ExampleCustomResourceWeapon : ModItem
{
	private int exampleResourceCost; // 添加我们的自定义资源消耗

	public static LocalizedText UsesXExampleResourceText { get; private set; }

	public override void SetStaticDefaults() {
		UsesXExampleResourceText = this.GetLocalization("UsesXExampleResource");
	}

	public override void SetDefaults() {
		exampleResourceCost = 5; // 假设exampleResourceCost在此物品的其他逻辑中动态变化

		// ... 其他物品默认代码
	}

	public override void ModifyTooltips(List<TooltipLine> tooltips) {
		tooltips.Add(new TooltipLine(Mod, "ExampleResourceCost", UsesXExampleResourceText.Format(exampleResourceCost)));
	}
}
```

### 多个可格式化替换

当本地化引用多个替换时，可能存在重复占位符的问题。例如，一个使用`CommonItemTooltip.IncreasesMaxManaBy`和`CommonItemTooltip.IncreasesMaxMinionsBy`的配饰会发现它们都使用`{0}`占位符。尝试将值绑定到同时使用这两个文本替换的提示将无法工作，需要额外处理。模组开发者可以使用特殊语法来偏移特定替换键内的索引。在替换键后添加`@`后跟数字将增加该键内占位符的数量。简而言之，语法是`{$KeyHere@OffsetNumberHere}`。[ExampleBreastplate.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Armor/ExampleBreastplate.cs)是此功能的一个很好的例子：

**来自[tModLoader.json](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/Localization/Content/en-US/tModLoader.json)的现有CommonItemTooltip条目**
```
"CommonItemTooltip": {
	"IncreasesMaxManaBy": "最大魔力增加 {0}",
	"IncreasesMaxMinionsBy": "最大召唤物数量增加 {0}",
	// 等等
```

**ExampleMod/Localization/en-US.hjson**
```
ExampleBreastplate: {
	DisplayName: Example Breastplate
	Tooltip:
		'''
		这是一个模组身体护甲。
		免疫'着火！'
		{$CommonItemTooltip.IncreasesMaxManaBy}
		{$CommonItemTooltip.IncreasesMaxMinionsBy@1}
		'''
}
```

**ExampleMod/Content/Items/Armor/ExampleBreastplate.cs**
```cs
public class ExampleBreastplate : ModItem
{
	public static readonly int MaxManaIncrease = 20;
	public static readonly int MaxMinionIncrease = 1;

	public override LocalizedText Tooltip => base.Tooltip.WithFormatArgs(MaxManaIncrease, MaxMinionIncrease);

	public override void UpdateEquip(Player player) {
		player.buffImmune[BuffID.OnFire] = true; // 使玩家免疫火焰
		player.statManaMax2 += MaxManaIncrease; // 将玩家可以拥有的魔力点数增加20
		player.maxMinions += MaxMinionIncrease; // 将玩家可以拥有的召唤物数量增加一个
	}
}
```
从这个例子中，我们可以看到`Tooltip.WithFormatArgs(MaxManaIncrease, MaxMinionIncrease)`试图将`MaxManaIncrease`绑定到`{0}`，将`MaxMinionIncrease`绑定到`{1}`。因为在本地化条目中的`{$CommonItemTooltip.IncreasesMaxMinionsBy@1}`添加了`@1`，原始占位符`{0}`被解释为`{1}`，从而允许游戏将`MaxMinionIncrease`的值绑定到结果提示文本中的正确位置。

这可能看起来有点复杂，忽略文本替换和`WithFormatArgs`而直接在本地化文件中键入提示文本可能会更简单，但如果正确使用这种方法，好处是显著的。通过这种方法，你的模组的大部分内容将自动本地化为其他语言。这种方法也大大降低了以后因拼写错误导致混淆的可能性。

有关更复杂的例子，请参见[ExampleStatBonusAccessory.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Common/Configs/ModConfigShowcases/ModConfigShowcaseLabels.cs)及相应的[en-US.hjson](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Localization/en-US.hjson#L164)条目。

### 组合翻译以实现动态内容

在极少数情况下，你可能需要一个引用另一个模组内容名称的翻译，但由于组合是在运行时生成的，因此无法创建特定翻译。

**建议：**
> 避免仅仅因为英语总是遵循某种模式就创建组合翻译，因为并非所有语言都有合适的模式。相反，尽可能制作独特且详细的翻译。例如，你可能想制作`{0} damage`并代入伤害类名称（`melee`、`ranged`、`magic`等）。这在英语中可以工作，但其他语言可能需要更改短语结构或根据代入内容添加额外的语法。例如，`ranged damage`可能翻译为`remote damage`，但`melee damage`可能翻译为`damage in arms reach`，而类似`in arms reach damage`或`arms reach damage`的尝试可能没有意义。

一个典型的例子是一个自动为游戏中每个弹药物品添加无限弹药物品的模组。`WithFormatArgs`可以接受其他`LocalizedText`作为参数。你也可以重写`LocalizedText`属性来返回完全不同的`LocalizedText`（参见下面的例子中的`Tooltip`）
```
InfiniteAmmoItem.DisplayName: "Infinite {0}"
```

```cs
public class InfiniteAmmoItem : ModItem
{
    Item baseAmmoItem;
    
    public override LocalizedText DisplayName => base.DisplayName.WithFormatArgs(baseAmmoItem.DisplayName);

    public override LocalizedText Tooltip => baseAmmoItem.Tooltip;
}
```

### ModConfig使用

`ModConfig`元素的`Label`和`Tooltip`可以利用文本替换。通过`LabelArgs`和`TooltipArgs`属性，可以为相应的翻译提供值。请注意，以"$"开头的字符串被解释为翻译键。[ModConfigShowcaseLabels.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Common/Configs/ModConfigShowcases/ModConfigShowcaseLabels.cs)中的`InterpolatedTextA`、`InterpolatedTextB`和`InterpolatedTextC`示例展示了此功能。

## 复数形式

当使用数字占位符时，例如`{0} minutes ago`，在英语中当数字恰好为1时，你可能会遇到问题。当数字为1时，文本应该说"1 minute ago"而不是"1 minutes ago"。这个问题可以通过复数形式解决。

作为占位符的扩展，tModLoader还支持语言规则定义的基本复数形式。在英语中，名词有两种形式：一种是名词的单数计数，另一种是名词的复数计数。例如，"1 dog"和"3 dogs"。其他语言有不同的规则。为了支持这一点，使用特殊语法来为翻译提供正确复数化的能力。在这个例子中，`{0} {^0:mod;mods} filtered by enabled filter.`，`{0}`是将被填充数字的占位符，`{^0:mod;mods}`告诉游戏检查第0个占位符的值，并使用其值在`mod`和`mods`之间选择。

英语、德语、意大利语、西班牙语和葡萄牙语在值为1时使用第一种形式，在其他值时使用第二种形式。法语在值为0或1时使用第一种形式，在其他值时使用第二种形式。中文只有一种形式，不论数量。波兰语和俄语的复数规则更复杂，可以在[Unicode语言复数规则网页](https://www.unicode.org/cldr/charts/43/supplemental/language_plural_rules.html)上找到。这些图表中的基本形式顺序对应于tModLoader将使用的顺序。

## 聊天标签

可以使用[聊天标签](https://terraria.wiki.gg/wiki/Chat#Tags)向本地化值添加颜色和物品图标。在[ExampleMod的本地化文件](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Localization/en-US.hjson)中找到`ExampleTooltipsItem`作为例子。

## NetworkText类

在处理多人游戏中的文本时，希望文本以每个客户端选择的语言显示，而不是服务器或发送消息的客户端的语言。`NetworkText`类有助于实现这一目的。你会注意到处理网络消息的方法使用`NetworkText`参数来传入文本。`NetworkText`类可以使用本地化键或`LocalizedText`对象本身来正确地在多人游戏中发送消息。请参阅`NetworkText`文档和示例来学习模组。

## 时序注意事项

模组中包含的本地化值在模组加载的早期阶段不可用。例如，如果在模组加载过程的早期阶段使用`string text = Language.GetTextValue("MyKeyHere");`，将返回"MyKeyHere"而不是`.hjson`文件中包含的本地化值。同样，`Language.GetText`方法将为尚未注册的键返回一个虚拟的`LocalizedText`。

作为经验法则，模组开发者应确保仅在`SetupContent`阶段或之后访问本地化**值**。这意味着像`SetStaticDefaults`和`PostSetupContent`之类的方法是访问本地化值的合适位置（当然也可以检索`LocalizedText`实例）。另一方面，`LocalizedText`实例本身可以在`Load`期间尽早检索和缓存，假设它们存在于本地化文件中或使用`Language.GetOrRegister`检索。这与UI代码特别相关。出于这个原因，在`PostSetupContent`甚至游戏内初始化`UIState`很有用。

# 自动本地化文件

当新内容或翻译键被使用时，tModLoader会自动更新`.hjson`文件。英文文件将用作其他语言的模板，其他语言将自动继承注释和布局。

请注意，本地化文件只会在游戏认为合适的情况下更新，以提高效率。例如，模组必须存在于`ModSources`文件夹中。正在加载的模组也必须是本地构建的模组。本地化文件只会在文件修改时间戳比模组或模组引用的任何模组旧时更新。请注意，直接测试旧的`.tmod`文件可能会用旧内容覆盖你的`.hjson`文件，因此建议使用Git或备份模组源代码，以便在需要时还原文件。

## 添加内容

当模组开发者向模组添加新内容（如`ModItem`）时，该内容最初不会被本地化。模组开发者应构建并重新加载模组。一旦模组加载，`.hjson`文件将自动更新。英文文件现在将包含新内容的默认翻译条目。非英文文件将包含相同的条目，但会被注释掉。要本地化内容，模组开发者需要编辑.hjson文件，添加所需文本，保存，然后再次构建并重新加载模组。`.hjson`文件应使用UTF-8编码保存，如果文本编辑器询问的话。

## HJSON语法

`.hjson`文件包含Hjson数据。Hjson类似于JSON，但旨在人类可读。参见[Hjson网站](https://hjson.github.io/)了解Hjson语法的详细信息，但大多数模组开发者可以按照示例来熟悉语法。

### 多行

如果一行文本需要多行，请使用以下语法。确保缩进一致：
```
SomeKey: 
	'''
	This translation key has 2 lines.
	This is the 2nd line!
	'''
```
你也可以使用`\n`作为替代，但为可读性起见，不建议这样做。引号是必需的，以便将`\n`解释为换行符。请注意，当tModLoader更新`.hjson`文件时，会自动将其转换为上述语法：
```
SomeKey: "This translation key has 5 lines and low readability.\nThis is the 2nd line!\nThis is the 3rd line!\nThis is the 4th line!\nThis is the 5th line!"
```

### 特殊字符

如果翻译值需要以`{}[]:`或空白字符开头，你需要用引号括起翻译。在其他情况下可以省略引号。如果你的值需要字面的`"`，你可以使用多行语法：
```
ExamplePetBuff: {
	DisplayName: "{$Mods.ExampleMod.Common.PaperAirplane}"
	Description: '''"让这个宠物给你做个榜样！"'''
}
```

### tModLoader HJSON功能

#### 颜色

`[c/颜色:文本]`可以显示彩色文本。
`颜色`是十六进制颜色代码。

这是一个例子：
```
Yes: "[c/008000:yes]"
No: "[c/FF0000:no]"
```
显示时，"yes"将为绿色，"no"将为红色。

#### 物品

`[i:ItemID]`和`[i:ItemClassName]`可以在消息中显示物品。
`ItemID`是物品的`type`。由于模组物品没有固定的`type`，你可以使用`[i:ModName/ItemName]`代替。
`ModName`是你的模组的类名，`ItemName`是你的物品的类名。

`[i/pPrefixID:ItemID]`可以显示带有前缀的物品。
`PrefixID`是前缀的`type`。

`[i/sStack:ItemID]`可以显示指定堆叠数量的物品。
`Stack`是物品堆叠的数量。

这是一个例子：
```
Label: "[i:ImproveGame/StarburstWand] tIMBALoader"
Tooltip: "[i/p57:HiveBackpack] 是一个奇特的配饰，而 [i/s1145:2] 只是泥土"
```
在这个例子中，`Label`将显示`(星爆法杖图标) tIMBALoader`，`StarburstWand`是Quality of Life模组中的一个模组物品。
`Tooltip`将显示一个`残暴蜂箱背包`和1145个`泥土块`。

#### 键绑定

`<KeybindName>`可以显示键绑定的本地化名称。目前只支持`<left>`表示左键点击和`<right>`表示右键点击。
`KeybindName`是键绑定的名称。

这是一个例子：
```
Tip: "<right> to use it's special attack"
```
`<right>`将显示为"right click"的本地化名称。

## 注释

`.hjson`文件可以包含多种注释样式。tModLoader使用Hjson注释来表达两个独立的概念。

使用`#`在行首的注释是真正的注释，模组作者可以用它们来提醒自己有用的东西。这些注释应直接放在它们所涉及键的上方。如果不把注释放在物品上方，当tModLoader自动更新本地化文件时，注释将丢失或放错位置。

例子：
```
ExampleCanStackItem: {
	DisplayName: Example CanStack Item: Gift Bag
	# 引用一个在游戏语言中说"Right Click To Open"的语言键
	Tooltip: "{$CommonItemTooltip.RightClickToOpen}"
}
```

这些注释也会从英文文件复制到非英文文件中，在那里它们可以提醒翻译者自定义翻译键的使用位置，例如。

使用`/* */`或`//`样式的注释由tModLoader用来表示非英文翻译键尚未翻译。这是向模组开发者提示哪些语言缺少翻译的一种方式。翻译者可以将翻译值翻译成他们的语言并删除注释语法。模组开发者不应将此注释语法用于正常注释，因为当游戏自动更新`.hjson`文件时它们会丢失。

## 翻译文件名

tModLoader将尝试将模组中的所有`.hjson`文件作为本地化文件加载。因此，本地化文件可以放置在任何文件夹路径中，但按照惯例，我们建议将它们放置在模组源代码文件夹根目录中名为"Localization"的文件夹中。模组生成器遵循此约定，会在模组中生成`Localization/en-US.hjson`以开始。

文件名开头或包含文件夹名称中必须存在有效的文化代码，以确定语言。

### 文化

支持以下语言（也称为文化）：英语（"en-US"）、德语（"de-DE"）、意大利语（"it-IT"）、法语（"fr-FR"）、西班牙语（"es-ES"）、俄语（"ru-RU"）、中文（"zh-Hans"）、葡萄牙语（"pt-BR"）或波兰语（"pl-PL"）。这些代码用于指示`.hjson`文件涉及哪种语言。要开始支持新语言，请参见[添加新语言](#添加新语言)。

### 前缀

`.hjson`文件的前缀表示文件中所有本地化条目共享一个公共前缀。最常见的用法是从本地化文件中省略`Mods`和`ModNameHere`条目。通过省略这些，文件的缩进更少，对某些人来说更容易处理。绝大多数模组不会使用其模组前缀之外的本地化值。

例如，一个名为`Localization/en-US_Mods.ExampleMod.hjson`的文件将继承`Mods.ExampleMod`前缀，这意味着该文件可以直接以`Items`条目开头。

其模式如下：文件路径按文件夹分割，然后按下划线分割。找到文化代码后，下一个结果将用作前缀。以下都是表示文件适用于英语并应使用`Mods.ExampleMod`前缀的选项示例。

```
Localization/en-US_Mods.ExampleMod.hjson
Localization/en-US/Mods.ExampleMod.hjson
en-US_Mods.ExampleMod.hjson
en-US/Mods.ExampleMod.hjson
Localization/CoolBoss/en-US_Mods.ExampleMod.hjson
```

### 非本地化.hjson文件

文件名中没有文化的`.hjson`文件将不会被解释为本地化文件。模组开发者可以像使用任何其他"数据"文件一样使用这些文件，其目的可能是什么。文件名中有文化但与英文模板文件名不匹配的`.hjson`文件将被重命名为`{filename}.legacy`，其本地化条目将不会被加载。遇到此重命名的模组开发者应确保更新英文模板文件或将那些本地化条目迁移到其他现有本地化文件中。这是设计使然的，以保持所有本地化文件布局和文件名的一致性。

## 多个文件

模组开发者可以使用多个`.hjson`文件来组织翻译。例如，如果模组包含`en-US_Mods.ExampleMod.Items.hjson`和`en-US_Mods.ExampleMod.hjson`，则`en-US_Mods.ExampleMod.Items.hjson`文件可以包含所有物品本地化，而另一个文件包含其余的本地化条目。新内容将自动进入与翻译键最相似的现有`.hjson`文件中。

如果你分割了本地化文件，你只需要编辑英文文件，然后构建并重新加载模组。其他语言将自动调整以匹配相同的布局。

# 添加新的翻译键

新内容的条目将自动填充`.hjson`文件，但也可以向文件添加自定义翻译键。

**注意：** 避免在自定义本地化键中使用空格和其他特殊字符。

## 手动添加键

对于大多数自定义键，手动添加键不是推荐的方法，这样很容易出错，也无法充分利用本地化系统的强大功能。有关向模组添加新键的典型方法，请参见[添加可本地化属性](https://github.com/tModLoader/tModLoader/wiki/Localization#adding-localizable-properties)。然而，在某些情况下，手动添加的键可能很有用。

要添加自定义键，模组开发者可以遵循`.hjson`语法直接添加本地化条目。例如，ExampleMod有一个名为"Common"的类别，这些条目都是手动添加的，因为它们不是由tModLoader类直接使用的。

例如，让我们从以下`.hjson`文件开始：
```
Mods: {
	ExampleMod: {
    		Common: {
			PaperAirplane: Paper Airplane
		}
        
		Currencies.ExampleCustomCurrency: example currency
   	 }
}
```

我们可以通过添加一行并遵循`PaperAirplane`示例来为`Mods.ExampleMod.Common.NewKey`键添加新条目。我们可以通过遵循`Common`类别中显示的语法添加一个名为`Uncommon`的新类别。我们也可以通过遵循`Currencies.ExampleCustomCurrency`示例仅用一行指定类别来添加条目。以下显示了这三种方法：

```
Mods: {
	ExampleMod: {
    		Common: {
			PaperAirplane: Paper Airplane
        		HotDog: Hot dog
		}
        
 		Uncommon: {
            		Helicopter: Example Helicopter
       		}
        
		Currencies.ExampleCustomCurrency: example currency
        	Currencies.DirtCurrency: piles of dirt
    	}
}
```

请注意，当本地化文件自动更新时，tModLoader将决定如何组织和格式化文件，这将导致条目移动，但不会丢失数据。


## 添加可本地化属性

模组开发者可以向其类添加`LocalizedText`属性，用于各种目的。当正确实现时，这些属性将自动填充`.hjson`文件并准备好进行本地化工作。

[ExampleHealingPotion.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Items/Consumables/ExampleHealingPotion.cs)展示了一个这样的用例。`ExampleHealingPotion`使用名为`RestoreLifeText`的`LocalizedText`属性，用于动态提示。

基本方法如下：
1. 在类中添加静态`LocalizedText`属性
2. 在`SetStaticDefaults`中使用`this.GetLocalization`方法为该属性赋值
3. 在需要时通过访问该属性来检索本地化文本值

例如：
```cs
public class ExampleHealingPotion : ModItem
{
	// 步骤1：创建一个静态LocalizedText属性
	public static LocalizedText RestoreLifeText { get; private set; }

	public override void SetStaticDefaults() {
		// 步骤2：将RestoreLifeText赋值为GetLocalization的结果
		RestoreLifeText = this.GetLocalization(nameof(RestoreLifeText));
	}

	public override void ModifyTooltips(List<TooltipLine> tooltips) {
		TooltipLine line = tooltips.FirstOrDefault(x => x.Mod == "Terraria" && x.Name == "HealLife");

		if (line != null) {
			// 将文本更改为'恢复最大生命/2（快速治疗时为最大生命/4）'
			// 步骤3：检索本地化文本。此示例使用Format方法，因为它有要填充的占位符，否则可以使用Value属性
			line.Text = Language.GetTextValue("CommonItemTooltip.RestoresLife", RestoreLifeText.Format(Main.LocalPlayer.statLifeMax2 / 2, Main.LocalPlayer.statLifeMax2 / 4));
		}
	}
}
```

在上面的例子中，`.hjson`文件自动填充了`RestoreLifeText`条目，与现有的`DisplayName`和`Tooltip`条目一起。然后模组开发者用英文更新了它：
```
ExampleHealingPotion: {
	DisplayName: Example Healing Potion
	Tooltip: ""
	RestoreLifeText: "{0}（快速治疗时为 {1}）"
}
```

**注意**
`LocalizedText`实例旨在静态存储。理想情况下，你应该在加载期间注册和检索一次。`ExampleHealingPotion`示例在`SetStaticDefaults`中执行注册，并将检索到的`LocalizedText`缓存到`RestoreLifeText`属性中。如果缓存太麻烦，你可以在每次需要时访问该属性，这会有一点性能成本。请注意，为了使翻译自动填充到`.hjson`文件中，你需要在加载期间至少访问该属性一次。

### 从可本地化属性检索文本

在类中，`LocalizedText`属性可用于向用户显示本地化文本：

```cs
Main.NewText(SomeLocalizedTextProperty.Value);
```

如果文本有占位符，可以使用`Format`方法填充它们，该方法接受与占位符数量相同的参数：

```cs
Main.NewText(SomeLocalizedTextPropertyWithPlaceholders.Format(Main.LocalPlayer.statLifeMax2, Main.LocalPlayer.statManaMax2));
```

### 可继承的本地化属性

当使用继承时，不是静态属性，而是使用基类中的get-only属性或继承类中的非静态属性可以达到相同的效果。继承的特性允许逻辑和翻译被重用，保持代码和`.hjson`文件的整洁，避免不必要的重复。

例如，假设一个基类被模组中的多个物品共享。可以向基类添加一个属性来保存每个继承物品的`LocalizedText`。该属性必须在`SetStaticDefaults`期间访问才能自动出现在`.hjson`文件中。

**基类：MyBaseClass**
```cs
public LocalizedText SpecialMessage => this.GetLocalization(nameof(SpecialMessage));

public override void SetStaticDefaults() {
	_ = SpecialMessage;
}
```

如果类`ClassA`和`ClassB`都继承自`MyBaseClass`，则`.hjson`文件将自动填充`SpecialMessage`键的存根条目：

```
ClassA: {
	DisplayName: Class A 
	Tooltip: ""
	SpecialMessage: Mods.ExampleMod.Items.ClassA.SpecialMessage
}

ClassB: {
	DisplayName: Class B
	Tooltip: ""
	SpecialMessage: Mods.ExampleMod.Items.ClassB.SpecialMessage
}
```

如果`ClassA`或`ClassB`重写了`SetStaticDefaults`，确保保留`base.SetStaticDefaults()`以便执行原始代码。

`GetLocalization`生成的键的形式为`Mods.{ModName}.{LocalizationCategory}.{ContentName}.{suffix}`。如果需要默认模式之外的特定键，模组开发者可以使用`Language.GetOrRegister("Mods.ModName.Full.Key.Here");`或`Mod.GetLocalization("Full.Key.Here");`（`Mod.GetLocalization`是一种快捷方式，它会为键添加"Mods.ModName."前缀，与本指南其余部分使用的`ILocalizedModType.GetLocalization`相比，它更通用，因为它不包含`{LocalizationCategory}.{ContentName}`。）。请注意，由于C#的设计，必须用`this.`前缀调用`GetLocalization`，不能省略。通过在继承属性中使用完整键，共享的本地化可以存在于单个公共翻译键中，需要自己键的继承类可以重写属性并使用自己的键通过`this.GetLocalization`。

### 另一个例子

[ExampleChest.cs](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/Tiles/Furniture/ExampleChest.cs)作为使用自定义键的示例。默认情况下，tModLoader将为每个`ModTile`注册一个翻译键，形式为`Mods.{ModName}.Tiles.{ContentName}.MapEntry`。此键可以轻松地向瓦片添加地图条目。（地图条目控制在全屏地图上悬停瓦片时显示给用户的文本。）然而，`ExampleChest`需要2个地图条目。使用`GetLocalization`，可以轻松地向本地化文件添加新键：

```cs
AddMapEntry(new Color(200, 200, 200), this.GetLocalization("MapEntry0"), MapChestName);
AddMapEntry(new Color(0, 141, 63), this.GetLocalization("MapEntry1"), MapChestName);
```

这段代码的结果是本地化文件现在包含这些键，准备好本地化为其他语言：
```
ExampleChest: {
	MapEntry0: Example Chest
	MapEntry1: Locked Example Chest
}
```

在ExampleChest.cs的其他地方，这些本地化键使用`GetLocalization`动态检索：

```cs
public override LocalizedText DefaultContainerName(int frameX, int frameY) {
	int option = frameX / 36;
	return this.GetLocalization("MapEntry" + option);
}
```

此方法对动态本地化键很有用。

### ModType和ILocalizedModType

实现自定义`ModType`的模组可以实现`ILocalizedModType`来轻松促进本地化。这就像在类继承中添加`, ILocalizedModType`并通过添加`public string LocalizationCategory => "MyModTypeCategory";`来实现`LocalizationCategory`属性一样简单。对于自定义`ModType`类中的每个`LocalizedText`，你可以使用`public virtual LocalizedText DisplayName => this.GetLocalization(nameof(DisplayName), PrettyPrintName);`，允许它们与其他现有`ModType`类一样在`.hjson`文件中正确分类。

你还需要确保每个`LocalizedText`在模组加载期间被访问，以便它们自动填充到`.hjson`文件中。如果你的逻辑没有访问它们，只需将`_ = DisplayName;`添加到类的`ModType.SetupContent`方法中即可。

**深入了解：** `GetLocalization`是一种简化代码并避免拼写错误的辅助方法。`GetLocalization`等同于使用传入完整键调用`Language.GetOrRegister`。类似地，`GetLocalizedValue`等同于以相同方式调用`Language.GetTextValue`。如果需要，`GetLocalizationKey`可用于检索生成的键。

`GetLocalization`和`Language.GetOrRegister`有一个名为`makeDefaultValue`的可选第二个参数，它定义了一个函数，用于在本地化不存在时生成将采用的默认值。例如，传入`() => ""`，将导致默认值为空字符串而不是键。如果本地化是可选的，或者你有一个合理的默认值，模组开发者可以传入`PrettyPrintName`来实现典型行为，即获取内容的内部名称并在每个大写字母之间添加空格。

### LocalizedText[]

我们可以使用数组来存储许多相关的`LocalizedText`。例如，以下将加载"DrawMode_0"、"DrawMode_1"、"DrawMode_2"、"DrawMode_3"和"DrawMode_4"的条目到数组中：

```cs
public static LocalizedText[] DrawModeText { get; private set; }

public override void SetStaticDefaults() {
	DrawModeText = Enumerable.Range(0, 5).Select(i => this.GetLocalization($"DrawMode_{i}")).ToArray();
}
```

另一个选项是使用`Language.FindAll`加载所有共享公共前缀的本地化键。当条目不是编号时，这种方法很有用。它还会加载其他模组提供的键，使最终文本计数不确定。[ExampleTownPet.SetNPCNameList](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Content/NPCs/TownPets/ExampleTownPet.cs#L136)使用这种方法来加载不是编号而是命名的键。

```cs
LocalizedText[] Options = Language.FindAll(Lang.CreateDialogFilter(this.GetLocalizationKey("Options.")));
```

你可能会在原版代码中看到`Language.RandomFromCategory`方法。该方法从所有共享相同"category"/前缀的条目中选择并返回一个随机的`LocalizedText`。此方法不适合模组使用。模组开发者应改用`Lang.CreateDialogFilter`和`Language.SelectRandom`来实现相同的效果：

```cs
LocalizedText randomOption = Language.SelectRandom(Lang.CreateDialogFilter(this.GetLocalizationKey("Options.")));
```

# 添加新语言

默认情况下，tModLoader只会为已经出现在`.hjson`文件中的语言生成和更新本地化文件。要添加新语言，只需创建一个文本文件，并按照现有本地化文件的方式命名。你只需要创建一个。文件或文件夹路径需要包含语言的语言代码：英语（"en-US"）、德语（"de-DE"）、意大利语（"it-IT"）、法语（"fr-FR"）、西班牙语（"es-ES"）、俄语（"ru-RU"）、中文（"zh-Hans"）、葡萄牙语（"pt-BR"）或波兰语（"pl-PL"）。创建文件并具有正确的文件扩展名后，重新构建模组。该文件将更新为准备好翻译的条目。其他文件也将根据英文hjson文件的组织方式生成。

除英语外的本地化文件的注释和组织都继承自英文文件。如果你想为翻译者添加贡献，请在英文文件顶部的注释中添加它们，它们将从那里传播到非英文文件。

模组开发者可以随意组织英文本地化文件，其他语言文件将更新以匹配它们。如果你分割了本地化文件，你只需要编辑英文文件，然后构建并重新加载模组。其他语言将自动调整以匹配相同的布局。从英文文件中删除的键同样会导致该键从其他语言文件中删除。简而言之，模组开发者通常只需要处理英文文件，其他语言文件将自动调整。

# 覆盖现有的Terraria键

替换现有的Terraria本地化值也是可能的。语法与模组本地化条目完全相同，但请注意，由于Terraria键的本地化键没有以`Mods.ModNameHere`为前缀，本地化将不得不进入没有前缀的本地化文件，例如`en-US.hjson`。你需要手动将它们输入文件中。另请注意，你需要自己查找键。[官方资源包指南](https://forums.terraria.org/index.php?threads/the-ultimate-guide-to-content-creation-and-use-for-the-terraria-workshop.100652/#advancedlanguagepack)中的[All Localizations.csv](https://forums.terraria.org/index.php?attachments/all-localizations-csv.391802/)文件列出了所有现有的本地化键。[ExampleMod/Localization/en-US.hjson](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Localization/en-US.hjson)的最底部显示了编辑`UI.PlayerIsNotCreativeAndWorldIsCreative`键值的一个例子。

当多个模组编辑同一个键时，最后加载的模组优先。

# 问题排查

## 为什么我的本地化条目消失了？

这通常发生在你的模组在多个文件中有相同的本地化键时。当游戏更新本地化文件时，它将删除重复条目，使每个键在每种语言中只存在于一个本地化文件中。许多模组开发者没有意识到模组模板带有一个`Localization`文件夹，其中包含`Localization`文件夹中的`en-US_Mods.ModName.hjson`文件，因此他们在项目目录的其他地方错误地添加了自己的`.hjson`文件。为避免此问题，只需使用一个文件或另一个文件，然后删除另一个。

另一种可能性是你尝试向非英文本地化文件添加自定义翻译键，而没有同时将其添加到英文本地化文件中。当tModLoader加载模组时，它使用英文条目作为模板更新所有本地化文件。不存在于英文文件中的键将在游戏更新文件时被删除。请确保任何自定义翻译条目都存在于英文文件中。

## 为什么我的本地化文件被重命名为`filename.hjson.legacy`？

被tModLoader重命名为`hjson.legacy`的hjson文件表示tModLoader检测到该文件没有任何条目。这可能是由与上述"为什么我的本地化条目消失了？"部分中提到的类似情况引起的问题造成的。
