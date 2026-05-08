# 多态指南

`WIP`（正在进行的工作）

多态是面向对象编程的三大支柱之一（另外两个是封装和继承）。多态允许对象以多种形式呈现。在 tModLoader 模组制作中，多态主要体现在以下几个方面：

# 继承中的多态

当一个类继承自另一个类时，它可以获得父类的行为，同时也可以重写这些行为以适应自己的需求。这就是多态的基本形式。

## 虚方法（Virtual Methods）

虚方法是可以在子类中被重写的方法。在 tModLoader 中，许多方法被设计为虚方法，允许模组开发者自定义行为。

```csharp
// 基类
public class BaseClass
{
    public virtual void DoSomething()
    {
        // 默认实现
    }
}

// 派生类
public class DerivedClass : BaseClass
{
    public override void DoSomething()
    {
        // 自定义实现
    }
}
```

## 抽象方法（Abstract Methods）

抽象方法是没有默认实现的方法，必须在派生类中实现。

```csharp
public abstract class BaseClass
{
    public abstract void DoSomething();
}

public class DerivedClass : BaseClass
{
    public override void DoSomething()
    {
        // 必须实现
    }
}
```

# 接口中的多态

接口定义了一组方法签名，实现该接口的类必须提供这些方法的具体实现。

```csharp
public interface IDoSomething
{
    void DoSomething();
}

public class MyClass : IDoSomething
{
    public void DoSomething()
    {
        // 实现接口方法
    }
}
```

# 在 tModLoader 中的应用

## ModType 中的多态

tModLoader 中的许多类如 `ModItem`、`ModProjectile`、`ModNPC` 等都利用了多态。模组开发者通过继承这些类并重写其方法来自定义游戏内容。

```csharp
public class MyCustomItem : ModItem
{
    public override void SetDefaults()
    {
        // 自定义物品属性
    }
    
    public override bool CanRightClick()
    {
        // 自定义右键点击行为
        return true;
    }
}
```

## Hook 系统中的多态

tModLoader 的 hook 系统也利用了多态。不同的 hook 用于在游戏的不同阶段执行自定义代码。

```csharp
public override void SetDefaults()
{
    // 在物品设置默认属性时调用
}

public override void UpdateInventory(Player player)
{
    // 当物品在玩家物品栏中更新时调用
}
```

# 实际应用示例

## 根据条件改变行为

多态允许根据不同的条件让对象表现出不同的行为：

```csharp
public class Weapon : ModItem
{
    public override void UpdateInventory(Player player)
    {
        if (player.HasBuff(BuffID.Rage))
        {
            // 在愤怒状态下增加伤害
            Item.damage = (int)(Item.damage * 1.5f);
        }
    }
}
```

## 状态模式

多态可以用于实现状态模式，让对象根据内部状态改变行为：

```csharp
public class MyNPC : ModNPC
{
    private enum NPCState
    {
        Idle,
        Attacking,
        Fleeing
    }
    
    private NPCState currentState;
    
    public override void AI()
    {
        switch (currentState)
        {
            case NPCState.Idle:
                // 空闲状态行为
                break;
            case NPCState.Attacking:
                // 攻击状态行为
                break;
            case NPCState.Fleeing:
                // 逃跑状态行为
                break;
        }
    }
}
```

# 多态的最佳实践

1. **保持继承层次扁平**：过深的继承层次会使代码难以理解和维护。
2. **使用接口而非继承**：当类需要多种不相关的行为时，使用接口比使用继承更灵活。
3. **重写方法时调用基类实现**：在适当的情况下调用 `base.MethodName()` 以保留基类的行为。
4. **使用 override 关键字**：确保正确重写基类方法，而不是创建新方法。
