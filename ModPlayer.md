# ModPlayer 指南

`ModPlayer` 是用于向玩家添加数据和功能的类。每个玩家将自动拥有附加到其上的每个 `ModPlayer` 的实例，允许 `ModPlayer` 类作为原版 `Player` 类的扩展。

我们使用 `ModPlayer` 类来存储状态并使用该状态来应用逻辑以影响玩家的行为。

# 如果 X，则执行 Y

`ModPlayer` 最常见的用法是使玩家在满足某些其他条件时执行某些操作。换句话说，"如果 X，则执行 Y"。作为示例，我们将探索配饰如何给予玩家特定效果。虽然此示例是配饰，但相同的逻辑适用于增益、护甲、药水等。本节将浏览 [SimpleModPlayer](https://github.com/tModLoader/tModLoader/blob/stable/ExampleMod/Common/Players/SimpleModPlayer.cs) 的代码。

首先，我们需要向 `ModPlayer` 添加一个字段来跟踪效果是否在玩家身上处于活动状态：

```csharp
public bool FrostBurnSummon;
```

接下来，我们需要使用 `ResetEffects` 方法将其重置为默认值。游戏的设计方式是所有游戏效果和属性在每个游戏更新中都会被重新计算，此方法促进这一点：

```csharp
public override void ResetEffects()
{
    FrostBurnSummon = false;
}
```

接下来，我们需要使用字段来驱动一些游戏逻辑。这是"如果 X，则执行 Y"中的"执行 Y"。这可以是任何内容，例如当玩家受到伤害或伤害敌人时，或当玩家治疗时，或每个游戏更新时。这通常归结为找到与预期效果相关的特定 `ModPlayer` hook 并使用它。可以查阅 [ModPlayer 文档](https://docs.tmodloader.net/docs/stable/class_mod_player.html) 和各种 ExampleMod 示例来找到正确的方法。在此示例中，我们将使用 `ModPlayer.OnHitNPCWithProj` 方法：

```csharp
public override void OnHitNPCWithProj(Projectile proj, NPC target, NPC.HitInfo hit, int damageDone)
{
    if ((proj.minion || ProjectileID.Sets.MinionShot[proj.type]) && FrostBurnSummon && !proj.noEnchantments)
    {
        target.AddBuff(BuffID.Frostburn, 60 * Main.rand.Next(3, 6));
    }
}
```

最后，我们需要根据某些条件给予玩家我们的效果。这是"如果 X，则执行 Y"中的"如果 X"。由于此示例是配饰物品，正确位置在 `ModItem.UpdateAccessory` 中：

```csharp
public override void UpdateAccessory(Player player, bool hideVisual)
{
    player.GetModPlayer<SimpleModPlayer>().FrostBurnSummon = true;
}
```

就是这样。总结一下，`ModPlayer` 负责效果的实际逻辑，而配饰、增益、护甲等负责告诉玩家效果应该处于活动状态。

> [!注意]
> 您可能期望配饰包含其给予效果的相关代码，但游戏的设计并非如此。当您的模组引入升级配饰时，这尤其重要。在升级配饰物品中，您只需设置与原始配饰物品相同的 `ModPlayer` 字段，并可能添加一些额外效果。此设计避免了重复游戏逻辑代码。

# 多个 ModPlayer 类

模组可以有多个 `ModPlayer` 类，事实上，我们强烈建议这样做。特别是，我们建议 `ModPlayer` 类坚持单一"职责"。这意味着负责钓鱼调整的 `ModPlayer` 类，例如，也不会包含对最大生命值属性的调整。

# 更多信息

`ExampleMod` 包含各种 [`ModPlayer` 示例](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Common/Players)。每个示例都有文档记录，可以学习并应用于您自己的模组。

其他相关 wiki 页面：
* [使用 TagCompound 保存和加载](https://github.com/tModLoader/tModLoader/wiki/Saving-and-loading-using-TagCompound) - 阅读此页面了解如何实现持久效果，因为这些需要保存和加载。
* [ModPlayer 文档](https://docs.tmodloader.net/docs/stable/class_mod_player.html) - 通过搜索文档找到适合您预期游戏效果的适当 `ModPlayer` "hook"。
