`工作中`
# 什么是C# 7？
C# 7是C#语言的第七个版本。它建立在Roslyn编译器的C# 6版本之上。要了解更多关于C# 6的信息，请参见[其专门页面](Intermediate-modding-with-c%236)。

# 如何使用C# 7构建？
**需要tML版本0.10.1.5或更高版本。**
从v0.11开始，C# 7是默认设置，你也可以将你的`languageVersion`设置为`6`，因为在C# 6和C# 7之间编译器内部没有变化。

# 最有用的特性
以下是C# 7提供的一些对模组制作最有用的特性。

## 嵌套函数（本地函数）
```c#
public void MyMethod() {
    void MyLocalFunc() {
        // 我做一些事情
    }

    MyLocalFunc(); // 调用我的嵌套函数
}
```
这是一个很棒的特性，允许你将较大的方法分解成较小的块。嵌套函数也可以在同一方法内重复使用。这些函数在其封闭块范围内。

## 内联out变量
```cs
public void PrintCoordinates(Point p) {
    p.GetCoordinates(out int x, out int y);
    mod.Logger.InfoFormat("({0}, {1})", x, y);
}
```
请注意，变量在其封闭块范围内，因此后续行可以使用它们。许多种类的语句不会建立自己的作用域，所以在其中声明的out变量通常会引入到封闭作用域中。

这也允许将"丢弃"作为out参数，形式为_，让你忽略你不在意的out参数：
```cs
p.GetCoordinates(out var x, out _); // 我只关心x
```

## 模式匹配
模式匹配在使用[多态](Polymorphism)时特别有用。你可以使用它们的继承树来识别对象，这意味着你也可以通过父类型来识别它们。
### 常量模式
例如：
```cs
if (obj is ModItem) {
    // obj是ModItem！
} else {
    // obj不是ModItem！
}
```

### 类型模式
例如：
```cs
if (obj is ModItem modItem) {
    // 我可以在这里使用modItem！
    modItem.item.damage = ....
}
```
或者：（像这样减少嵌套通常是有利的）
```cs
if (!(obj is ModItem modItem)) return;
// 我可以在这里使用modItem！
modItem.item.damage = ....
```

### switch语句中的模式
更多信息可在[这里](https://visualstudiomagazine.com/articles/2017/02/01/pattern-matching.aspx)找到。
```cs
switch(modItem)
{
    case ModItem a:
        // 我得到了一个ModItem！
        break;
    case ModItem b when (b.damage == 10):
        // 我得到了一个伤害为10的ModItem！
        break;
    case MyModItem c when (b.MyCustomType == MyCustomType.Something):
        // ....
        break;
    default:
        // ...
        break;
    case null:
        throw new ArgumentNullException(nameof(modItem));
}
```

## 元组类型和字面量
新的元组类型和字面量非常有用，让你避免定义自己的元组（形式为Tuple<x,y,z>并通过myType.Item1... Item2...等访问）或结构体，而是使用易于定义且可以解构的字面量。
```cs
(int, float, int) GetItemInfo(Item item) // 元组返回类型
{
    return (dmg: item.damage, kb: item.knockBack, proj: item.shoot); // 元组字面量
}
.....

// 解构
var itemInfo = GetItemInfo(myItem);
mod.Logger.Info(itemInfo.dmg.ToString());
// itemInfo.kb .... itemInfo.proj
```

也允许丢弃：

```cs
var (dmg, kb, _) = GetItemInfo(myItem);
// 我可以使用dmg、kb（变量）
// 与以下相同：
int dmg;
float kb;
(dmg, kb, _) = GetItemInfo(myItem);
// 或：
(int dmg, float kb, _) = GetItemInfo(myItem);
```
