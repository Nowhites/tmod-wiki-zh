在学习创建模组物品时，通过查看原版物品如何实现其预期效果来了解各种物品属性的效果是有益的。以下列出了每个原版泰拉瑞亚物品设置的属性。请务必查看[物品类文档](https://github.com/tModLoader/tModLoader/wiki/Item-Class-Documentation)以了解每个字段代表什么。

例如，如果您希望您的模组物品使用"火焰之花"使用时发出的声音，您可以在下面的页面中找到该条目，并看到"火焰之花"的使用声音为 20。现在，您可以在模组物品的 SetDefaults 方法中说 `item.UseSound = SoundID.Item20`，它应该发出相同的声音。

- [原版物品字段值电子表格](https://terraria.wiki.gg/wiki/Item_IDs)
