# 条件

条件是指定配方何时可以制作的方式。它们允许你根据游戏状态、世界状态或玩家状态限制配方的可用性。

## 使用条件

条件通过`AddCondition`方法添加到配方中：

```csharp
Recipe.Create(ItemID.AlphabetStatueA)
    .AddIngredient(ItemID.StoneBlock, 10)
    .AddIngredient(ItemID.Chain)
    .AddTile(TileID.WorkBenches)
    .AddTile(TileID.Anvils)
    .AddCondition(Condition.NearWater) // 只在水源附近制作
    .Register();
```

## 内置条件

tModLoader提供了许多内置条件：

### 位置条件
- `Condition.NearWater`: 玩家附近有水
- `Condition.NearLava`: 玩家附近有岩浆
- `Condition.NearHoney`: 玩家附近有蜂蜜
- `Condition.NearShimmer`: 玩家附近有闪光

### 时间条件
- `Condition.Night`: 只在夜晚
- `Condition.Day`: 只在白天
- `Condition.BloodMoon`: 只在血月

### 世界条件
- `Condition.Hardmode`: 只在困难模式
- `Condition.NotExpert`: 只在普通模式（非专家模式）

### 专家模式条件
- `Condition.InExpert`: 只在专家模式
- `Condition.DownedBoss`: 特定Boss已被击败

## 自定义条件

你也可以创建自定义条件：

```csharp
public class MyCondition : IItemDropRuleCondition
{
    public bool CanDrop(DropAttemptInfo info) 
    {
        return Main.LocalPlayer.HasBuff(BuffID.Regeneration);
    }

    public bool CanShowItemDropInUI() => true;

    public string GetConditionDescription() => "玩家必须拥有再生效果";
}
```

然后在配方中使用：
```csharp
Recipe.Create(ItemID.Something)
    .AddIngredient(ItemID.DirtBlock)
    .AddCondition(new MyCondition())
    .Register();
```

## 组合条件

你可以通过多次调用`AddCondition`来组合多个条件：

```csharp
Recipe.Create(ItemID.BloodMoonStarter)
    .AddCondition(Condition.Night)
    .AddCondition(Condition.BloodMoon)
    .Register();
```

这将创建一个只在血月夜晚制作的配方。
