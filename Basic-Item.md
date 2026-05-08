***
本指南已更新至1.4.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-Item/f240adbd4e8629200547bd80057207a0182382d4)
***

# 基础物品
本指南旨在解释所有物品共有的常见内容。

# 什么是物品？
重要的是要意识到物品(Items)、弹幕(Projectiles)和瓷砖(Tiles)之间的区别。刚开始时，如果您不清楚它们如何不同，可能会无意中混淆概念。例如，有些人在想要添加工作台瓷砖时，却错误地将其添加到配方中。同样重要的是要意识到许多像回旋镖武器这样的东西由物品和弹幕两部分组成。虽然这是一个简单的概念，但请尽量记住。

# 创建物品
要在Terraria中添加物品，我们必须首先创建一个"类"，该类"继承"自ModItem。为此，请在模组的源目录（`My Games\Terraria\tModLoader\ModSources\MyModName`）中创建一个.cs文件，然后在文本编辑器中打开该文件。将以下内容粘贴到该文件中，将`NameHere`替换为您的物品内部名称，将`ModNamespaceHere`替换为您的模组文件夹名/命名空间。（一个常见错误是在内部名称中使用撇号或空格，不要这样做，计算机会无法理解。）
```cs
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;

namespace ModNamespaceHere
{
    public class NameHere : ModItem
    {
        public override void SetDefaults()
        {
            Item.width = 20;
            Item.height = 20;
            Item.maxStack = 9999;
            Item.value = 100;
            Item.rare = ItemRarityID.Blue;
            // 在这里设置其他 Item.X 值
        }

        public override void AddRecipes()
        {
            // 配方在这里。参见基础配方指南
        }
    }
}
```
现在您有了一个`.cs`文件，将您的纹理文件（您制作的`.png`图像文件）放入包含此`.cs`文件的文件夹中。确保阅读[自动加载](https://github.com/tModLoader/tModLoader/wiki/Basic-Autoload)以了解如何满足计算机会的文件名和文件夹结构期望。

# SetDefaults
物品最重要的部分是`SetDefaults`。`SetDefaults`是您为物品设置值的地方，比如物品使用什么弹药、物品有多大、物品放置在哪个瓷砖上。查看[物品类文档](https://github.com/tModLoader/tModLoader/wiki/Item-Class-Documentation)了解通常在`SetDefaults`中设置的值意味着什么。您还可以通过访问[原版物品字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Item-Field-Values)查看原版物品的值。不同物品的许多示例可以在[ExampleMod.Content.Items](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod/Content/Items)中找到

# 本地化
构建模组后，本地化文件（通常位于`My Games\Terraria\tModLoader\ModSources\MyModName\Localization\en-US_Mods.MyModName.hjson`）将填充新添加物品的条目。对于物品，您会看到`DisplayName`和`Tooltip`的条目。`DisplayName`默认为类名，单词首字母大写分隔。`Tooltip`默认为空。您可以编辑此文件来自定义这些本地化：

```
Items: {
	NameHere: {
		DisplayName: 物品名称
		Tooltip: 这是一个模组物品。
	}
}
```

如果您想要多行Tooltip，请阅读[本地化wiki页面中的多行部分](https://github.com/tModLoader/tModLoader/wiki/Localization#multiline)以了解正确的语法。