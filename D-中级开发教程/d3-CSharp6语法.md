请在遵循本指南之前查看[前置条件](Intermediate-Prerequisites)。

## 什么是C#6？
C#6代表C#语言的第6个版本。默认情况下，你将使用C#第4版编译你的模组。但是，也可以使用C#第6版编译你的模组。本指南将介绍如何使用第6版编译，以及它可能包含哪些好处。

## 如何用C#6编译
要启用第6版编译，在你的build.txt文件中将languageVersion设置为6：
`languageVersion = 6`

### 编译方式
建议使用你的IDE编译和调试你的模组。你可以查看[使用Visual Studio开发指南](Developing-with-Visual-Studio)了解如何设置。你也可以使用游戏内菜单编译，但有时这会引入在使用IDE直接编译时不会遇到的额外问题。我们将在指南后面部分介绍这些问题。

## 为什么我应该使用C#6？
一般来说，使用最新版本通常是最好的。我们的语言版本也不例外，第6版比旧版本有很多改进。建议查看[微软官方C#6说明文章](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-6)了解所有新特性。一般来说，C#6有一些我们将在下面介绍的贴心QoL改进。以下是C#6引入的全部内容：
* 只读自动属性：
    * 你可以创建只能在构造函数中设置的只读自动属性。
* 自动属性初始化器：
    * 你可以编写初始化表达式来设置自动属性的初始值。
* 表达式-bodied函数成员：
    * 你可以使用lambda表达式编写单行方法。
* using static：
    * 你可以将单个类的所有方法导入当前命名空间。
* Null-条件运算符：
    * 你可以在检查null的同时简洁安全地访问对象成员。
* 字符串插值：
    * 你可以使用内联表达式而不是位置参数编写字符串格式化表达式。
* 异常过滤器：
    * 你可以根据异常的属性或其他程序状态捕获表达式。
* nameof表达式：
    * 你可以让编译器生成符号的字符串表示。
* await在catch和finally块中：
    * 你可以在以前不允许的位置使用await表达式。
* 索引初始化器：
    * 你可以为关联容器以及序列容器编写初始化表达式。
* 集合初始化器的扩展方法：
    * 集合初始化器除了成员方法外，还可以依赖可访问的扩展方法。
* 改进的重载解析：
    * 一些以前产生歧义方法调用的构造现在可以正确解析了。

请务必查看上面链接的文章，它详细解释了一切。这些QoL改进中的许多会缩短你的代码，并/或使其更易读（以及更容易使用！）

## 可以用C#7编译吗？
在_v0.10.1.5版本之后的版本中，可以使用C#7编译。要了解更多，请参见[C#7特定页面](Intermediate-modding-with-c%237)。

## 对模组开发者来说C#6最值得注意的代码改进
### 表达式-bodied函数成员
你有的任何方法，例如只返回一个值的重写钩子，现在都可以缩短为表达式-bodied函数成员（像指针的方法），例如。
```csharp
public override bool SomeHook() 
{
   return something() ? true : somethingAgain() : false;
}
```
可以缩短为
```csharp
public override bool SomeHook() => something() ? true : somethingAgain() : false
```

### 字符串插值
你不再需要使用string.Format，而是使用新的字符串插值：
```csharp
string s = string.Format("Hello {0}, how are you on {1}?", user.name, DateTime.Now);
```
可以缩短为
```csharp
string s = $"Hello {user.name}, how are you on {DateTime.Now}?";
```
## 用C#6编译时的大量问题列表

### !! 重要 !! 可能的问题
**注意：这些问题中的大部分（如果不是全部）自[v0.10.1.2](https://github.com/tModLoader/tModLoader/releases/tag/v0.10.1.2)以来已经解决。如果你由于某种原因仍在使用旧版本，你可以在下面看到各种问题和可能的修复方法。**

你需要在代码中以某种方式包含System.Core以避免bug。
```csharp
using System.Linq; // 显式使用

public static void RedundantFunc() // 如果using指令不够，尝试像这样
{
        var something = System.Linq.Enumerable.Range(1, 10);
}
```

### System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.IO.FileNotFoundException: Could not load file or assembly 'System.Collections.Immutable, Version=1.1.37.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The system cannot find the file specified.
如果你遇到这个问题，你需要将[System.Collections.Immutable.dll](https://cdn.discordapp.com/attachments/103115427491610624/352120158439079936/System.Collections.Immutable.dll)添加到你的\ModCompile\文件夹。一个新分发的RoslynWrapper.dll需要包含这个.dll才能解决这个问题。

### Metadata file 'path\FNA.dll\ could not be found 或者其他提到FNA.dll的错误
要修复这个问题，去你的\ModCompile\文件夹找到`FNA.dll`文件。_**复制**_这个文件到上级目录（到Terraria目录）。你可能通过游戏内菜单编译时遇到这个问题。确保也如上所述引用System.Core。

### 某个提到ReLogic.dll找不到或无法访问的错误
去你的Terraria文件夹找到`ReLogic.Native.dll`并_**复制**_这个文件到你的\ModCompile\文件夹，然后重命名为`ReLogic.dll`如果它现在开始提到Mac/Linux，再复制两份并分别命名为`ReLogicMac.dll`和`ReLogicLinux.dll`

### C#6问题："尝试从网络位置加载程序集..."
[参见此页面](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Modding-FAQ#c-6-issues-an-attempt-was-made-to-load-an-assembly-from-a-network-location)。
