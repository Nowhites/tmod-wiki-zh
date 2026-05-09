___
[IL编辑新手？那么首先阅读我们关于IL编辑的其他指南，点击这里！](Expert-IL-Editing)

[本指南涉及IL编辑其他模组，点击这里了解更多](https://github.com/tModLoader/tModLoader/wiki/Patching-Other-Mods-Using-MonoMod)

- 在1.4.4中`HookEndpointManager`已过时，对detour使用`new Hook()`，对IL补丁使用`new ILHook()`，这些需要手动应用、卸载和存储。
___

还有另一种修改其他模组方法的方法。如果您查看使用MonoMod的HookGen生成的dll，您会看到它使用`HookEndpointManager`：
```cs
public static event ILContext.Manipulator Initialize
{
	add
	{
		HookEndpointManager.Modify<On.Wikithis.ItemWiki.hook_Initialize>(MethodBase.GetMethodFromHandle(methodof(global::Wikithis.ItemWiki.Initialize()).MethodHandle), value);
	}
	remove
	{
		HookEndpointManager.Unmodify<On.Wikithis.ItemWiki.hook_Initialize>(MethodBase.GetMethodFromHandle(methodof(global::Wikithis.ItemWiki.Initialize()).MethodHandle), value);
	}
}
```

您可以跳过使用HookGen创建中间dll并直接调用`HookEndpointManager`。但是，不可能（至少在C#10和.NET 6中）像HookGen那样创建完全相同的钩子，所以这样做需要一些使用反射的知识。

## 准备工作

首先获取您想要detour或IL编辑的方法的`MethodInfo`：
```cs
public class DetourManagerExample : Mod
{
    public override void Load()
    {
        if (ModLoader.HasMod("CalamityMod"))
        {
            // 目标方法所在的类
            Type calamityDetourClass = Type.GetType("CalamityMod.World.MiscWorldgenRoutines");

            // Public和Static绑定标志，因为PlaceRoxShrine是一个public static方法
            MethodInfo detourMethod = calamityDetourClass.GetMethod("PlaceRoxShrine", BindingFlags.Public | BindingFlags.Static);
        }
    }
}
```

## Detouring

如果方法有超过1-2个参数，或任何`ref`、`out`或`in`参数，添加原始方法的单独delegate：
```cs
public class DetourManagerExample : Mod
{
    private delegate void orig_PlaceRoxShrine();

    public override void Load()
    {
        ...
    }

    private static void PlaceRoxShrine_Detour(orig_PlaceRoxShrine orig)
    {
        // 您的补丁代码，与使用HookGen进行detouring相同
    }
}
```
`orig_PlaceRoxShrine` delegate与原始方法签名匹配（返回类型和参数）。

Detour方法（`PlaceRoxShrine_Detour`）与原始方法匹配，但添加了第一个参数`orig_PlaceRoxShrine` delegate。

<b>请注意，实例方法将第一个参数作为类实例：</b>

```cs
class MethodClass
{
    public int InstanceMethod(string instanceParameter) { ... }

    public static string StaticMethod(int instanceParameter) { ... }
}

class DetourClass
{
    private delegate int orig_InstanceMethod(MethodClass self, string instanceParameter);
    private int InstanceMethod_Detour(orig_InstanceMethod orig, MethodClass self, string instanceParameter) {  }

    private delegate string orig_StaticMethod(int instanceParameter);
    private string StaticMethod_Detour(orig_InstanceMethod orig, int instanceParameter) {   }
}
```

最后，调用`HookEndpointManager.Add`：
```cs
HookEndpointManager.Add(detourMethod, PlaceRoxShrine_Detour);
```

<b>tModLoader将确保所有detour和IL编辑在模组卸载期间卸载，无需手动取消注册它们</b>

### `Action`和`Func`

在某些场景中，自定义orig delegate可以替换为`Action`或`Func`，
例如如果方法有1个或更少的参数

```cs
public class DetourManagerExample : Mod
{
    public override void Load()
    {
        if (ModLoader.HasMod("CalamityMod"))
        {
            Type calamityDetourClass = Type.GetType("CalamityMod.World.MiscWorldgenRoutines");
            MethodInfo detourMethod = calamityDetourClass.GetMethod("PlaceRoxShrine", BindingFlags.Public | BindingFlags.Static);

            HookEndpointManager.Add(detourMethod, PlaceRoxShrine_Detour);
        }
    }

    private static void PlaceRoxShrine_Detour(Action orig)
    {
        // 您的补丁代码，与使用HookGen进行detouring相同
    }
}
```

更多Action/Func示例
```cs

// 可以用Action进行detour
public void VoidParameterlessMethod() { ... }

// 可以用Func进行detour
public string StringParameterlessMethod() { ... }

// 可以用Action<int>进行detour
public void VoidMethod(int integer) { ... }

// 可以用Func<int, string>进行detour
public string StringMethod(int integer) { ... }
```

在某些情况下不能使用Action或Func，例如，如果方法有任何`ref`、`out`或`in`参数

```cs
public int OutMethod(ref int refInt) { ... }

public int RefMethod(out int outInt) { ... }
```

## IL编辑

IL编辑比detouring更容易设置，只需创建一个返回`void`并具有单个参数 - `ILContext`来操作IL，然后调用`HookEndpointManager.Modify`。
```cs
public class ILEditManagerExample : Mod
{
    public override void Load()
    {
        if (ModLoader.HasMod("CalamityMod"))
        {
            Type calamityDetourClass = Type.GetType("CalamityMod.World.MiscWorldgenRoutines");
            MethodInfo detourMethod = calamityDetourClass.GetMethod("PlaceRoxShrine", BindingFlags.Public | BindingFlags.Static);

            HookEndpointManager.Modify(detourMethod, PlaceRoxShrine_ILEdit);
        }
    }

    private static void PlaceRoxShrine_ILEdit(ILContext il) 
    {
        // IL操作在这里，与使用HookGen或任何其他IL编辑相同
    }
}
```
