# 图鉴星星

游戏内图鉴中的星星或"评级"由多种因素控制。

以下是游戏用于确定任何给定`npc`的星星评级的一般公式（found in `Terraria.ID.ContentSamples.GetNPCBestiaryRarityStarsCount()`），有一些例外：

1. 从默认星星评级`1`开始。
2. 将`npc.rarity`添加到评级中。
3. 按顺序检查以下条件，只添加一次评级：
   1. `npc.rarity == 1`是否为真？如果是，评级加`1`。
   2. `npc.rarity == 2`是否为真？如果是，评级加`1.5`。
   3. `npc.rarity == 3`是否为真？如果是，评级加`2`。
   4. `npc.rarity == 4`是否为真？如果是，评级加`2.5`。
   5. `npc.rarity == 5`是否为真？如果是，评级加`3`。
   6. `npc.rarity > 0`是否为真？如果是，评级加`3.5`。（此条件适用于任何高于`5`的稀有度）
4. `npc.boss`是否为真？如果是，评级加`0.5`。
5. 计算`npc.damage + npc.defense + npc.lifeMax / 4`的总和。此处使用的属性是NPC在**普通模式**下的属性。
6. 使用该总和，按顺序检查以下条件，只添加一次评级：
   1. 总和是否大于`10000`？如果是，评级加`3.5`。
   2. 总和是否大于`5000`？如果是，评级加`3`。
   3. 总和是否大于`1000`？如果是，评级加`2.5`。
   4. 总和是否大于`500`？如果是，评级加`2`。
   5. 总和是否大于`150`？如果是，评级加`1.5`。
   6. 总和是否大于`50`？如果是，评级加`1`。
7. 将评级上限设为`5`，然后截断并返回。

## 如何为我的NPC添加自定义星星评级？

字典`ContentSamples.NpcBestiaryRarityStars`存储所有NPC（包括mod NPC）的星星评级。

要为NPC手动设置评级，请在`ModNPC/GlobalNPC.SetStaticDefaults()`中设置`ContentSamples.NpcBestiaryRarityStars[Type] = stars;`。

**示例：**

```csharp
public override void SetStaticDefaults() {
    // 将ExamplePerson的稀有度设置为5星
    ContentSamples.NpcBestiaryRarityStars[Type] = 5;
}
```

将NPC的评级设置为小于`0`被视为NPC评级为`0`。

将NPC的评级设置为大于`5`被视为NPC评级为`5`。

## v2025.06及更早版本

要为NPC手动设置评级，请在`GlobalNPC.SetBestiary()`中设置`ContentSamples.NpcBestiaryRarityStars[type] = stars;`。

**示例：**

```csharp
public override void SetBestiary(NPC npc, BestiaryDatabase database, BestiaryEntry bestiaryEntry){
    // 将ExamplePerson的稀有度设置为5星
    ContentSamples.NpcBestiaryRarityStars[ModContent.NPCType<ExamplePerson>()] = 5;
}
```
