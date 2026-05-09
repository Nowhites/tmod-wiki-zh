2020年初，有两个。
Games/ModLoader (1.3)
Games/ModLoader/Beta (1.4 dev)

每个包含：
- Mod Sources
- Mod Config
- Mod Reader
- Mods
- Logs
- Players
- Worlds
- Config.json
- Achievements.dat
- input profiles.json
还有很多自定义模组文件夹/文件用于自定义存档数据。

我们还有，在Steam Cloud中，
Steam/ModLoader
Steam/ModLoader/Beta

包含：
- Players
- Worlds
- Achievements.dat


(PortOldSaveDirectories)
在1.4-Alpha发布开始时，我们执行了以下迁移：
- Documents/ModLoader/Beta重命名为tModLoader
- 在tModLoader文件夹中，删除Mod Sources、Mod Reader、Mod Config文件夹中的空格以提高Linux兼容性

我们在此阶段不包括任何steam cloud迁移

(新增1.4时代预览版、开发文件夹)
此外，我们添加了两个文件夹，取决于1.4-alpha迁移是否成功完成。
- tModLoader-preview
- tModLoader-dev
这两个文件夹是在使用BuildPurpose.Preview和BuildPurpose.Dev分别启动tML时创建的。

(PortCommonFiles)
此外，在首次启动时，preview/dev复制以下文件到新环境：
- config.json
- input profiles.json

(Port143FilesFromStable)
在启动tMod 1.4.4时代之前，我们需要保留1.4.3的玩家数据。
这需要步骤PortOldSaveDirectories首先完成。

核心目标是安全备份除ModSources和临时发布文件夹"Workshop"之外的所有内容。
备份将添加到新文件夹中，其作用与1.3的ModLoader相同。
新文件夹：Games/tModLoader-1.4.3

此迁移是最复杂的，涉及以下内容：
1) 如果Games/tModLoader-1.4.3文件夹不存在，则仅当存档数据对应于1.4.3或更早版本时才复制所有指定文件
2) 对Steam/tModLoader-1.4.3执行与1)相同的操作

鉴于我们实际上是在接触所有用户的数据，至关重要的是，我们在部分文件移动的情况下加入恢复逻辑。
因此，如果tModLoader-1.4.3文件夹确实存在，我们仍然必须从tModLoader复制比或不存在于tMod-1.4.3中的所有新文件，前提是这些文件适用于1.4.3或更早版本。
最后，为避免每次运行此逻辑（因为它很昂贵），我们在Ported143.txt中留下了一个完成标志，以告诉我们tml之前完全完成了。

tModLoader-1.4.3被决定与tModLoader共享ModSources文件夹，像dev和preview一样，与1.3不同。

(Maybe Port202306FilesFromPreview)
在1.4.4发布开始时，我们需要么
将现有的tModLoader-preview数据从preview迁移到`tModLoader`
或者
建立功能来扫描所有文件夹的数据，并提示用户选择使用哪个。

无论哪种方式，如果在1.4.4-stable启动时tModLoader目录存在，在步骤Port143FilesFromStable完全完成之前无法使用。

迁移中涉及的逻辑复制了Port143步骤，换出涉及的文件夹和2023.06而不是2022.09的版本。

（更多待添加）
