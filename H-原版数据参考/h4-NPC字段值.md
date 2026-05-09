在学习创建模组 NPC 时，通过查看原版物品如何实现其预期效果来了解各种 NPC 属性的效果是有益的。以下列出了每个原版泰拉瑞亚 NPC 设置的属性。

例如，如果您希望您的模组 NPC 使用秃鹰被击中时发出的声音，您可以在下面的电子表格中找到该条目，并看到"Vulture"的"soundHit"为 28。现在，您可以在模组 NPC 的 SetDefaults 方法中编写 `npc.HitSound = SoundID.NPCHit28`，当玩家击中它时它应该发出相同的声音。

- [原版 NPC 字段值电子表格](http://bit.ly/TerrariaVanillaNPCFieldValues)
