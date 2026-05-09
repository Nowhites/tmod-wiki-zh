***
本指南已更新至1.4版本。如果您需要查看此wiki页面的1.3旧版本，请点击[此处](https://github.com/tModLoader/tModLoader/wiki/build.txt/c4f06914c8f2547d8f0d176663b6dfd7cb8c485d)
***

## 关于
您可以在mod源代码的顶层目录中创建build.txt文件，以为其指定构建属性。这些属性在mod的构建过程中使用，包含有关mod的信息。此文件中的属性可能会让您想起.ini文件，其中每个键都有_名称_和_值_，由等号（`=`）分隔。

```.ini
property = value
otherProperty = value1, value2, value3
```
**注意：此文件中的大小写很重要！大小写不正确将导致构建过程出错。**

空白字符不重要。但是，每个属性必须单独成行，列表项必须用逗号分隔，属性名称必须在等号的左侧。

## 可用属性

| 属性 | 描述 | 默认值 |
| --: | :-- | :-- |
| displayName | 显示mod时使用的名称 | |
| author | 显示mod时使用的作者<br><br><details><summary>贡献者（可以发布更新的其他mod作者）</summary><blockquote>请注意，作者字段只是一个显示值，它实际上不控制任何内容。如果您与其他mod作者合作，并希望让他们为此mod发布更新到Steam创意工坊，您可以在mod发布到创意工坊后，点击创意工坊页面上的"添加/删除贡献者"来添加他们。在添加贡献者时要格外小心，只能添加您绝对信任的贡献者。<br>![image](https://github.com/user-attachments/assets/3b13aafe-9096-421a-9a0e-e9f2b9995ec5)</blockquote></details> |
| version | 显示mod时使用的版本。<br>必须是2到4个用句点分隔的数字。<br>值必须采用以下格式：`<主版本>.<次版本>.<构建>.<修订>`（例如1.2.3.4）<br>**这些不是小数**，例如`1.02`与`1.2`相同。另外，`1.101`大于`1.2`，因为`101 > 2`。`1`和`1.2.3.4.5`都不允许，因为需要2到4个数字。 |
| dllReferences | mod除其他mod的dll外所依赖的dll引用列表。请勿在此处包含mod。<br>按文件名（不带扩展名）引用。<br>您必须将dll文件放在mod源代码顶层目录的`lib/`文件夹中。 |
| modReferences | mod所依赖的其他mod列表。此mod将要求启用依赖的mod才能启用此mod。<br>按文件名（不带扩展名）引用mod。请参阅[跨Mod内容专家指南](f4-跨模组内容.md)了解如何正确使用此功能。 |
| weakReferences | mod将构建并可以本地交互的mod列表，但不一定需要启用。本质上是可选的modReferences，但需要mod作者在编程时更加小心以避免绑定错误。请参阅[跨Mod内容专家指南](f4-跨模组内容.md)了解如何正确使用此功能。 |
| noCompile | mod在构建时是否应编译源代码，还是使用mod作者预编译的.dll文件。如果您不包含此属性，默认为false。预编译的dll文件必须命名为"ModName.dll"，其中`ModName`是您的mod名称。 | false |
| homepage | mod主页的链接。这将与Steam创意工坊页面一起列在"更多信息"菜单中。 | |
| hideCode | 如果为true，则使用提取mod选项生成的解包mod将不包含.dll文件（编译代码）、.pdb文件（有助于错误消息）或.xml文件（文档）。这些文件对于允许跨mod功能至关重要，因此如果您打算允许mod正确地进行互操作，此选项不应为true。 | false |
| hideResources | 如果为true，则使用提取mod选项生成的解包mod将不包含资源文件，如.png文件和.wav文件。如果您希望让其他mod作者自由查看您的声音和图像，请使用此选项。如果使用`includeSource`，这也将包含那些.cs文件。 | false |
| includeSource | 如果为true，则使用提取mod选项生成的解包mod将包含.cs文件，前提是`hideResources`仍为false。如果您希望自由分享您的代码，请使用此选项。如果您真的希望获得mod的社区支持，请考虑使用[GitHub](d2-Git与模组管理.md)允许其他人为您mod贡献代码。 | false |
| buildIgnore | 构建和打包过程中要忽略的文件模式列表。要排除单个文件，只需写文件名：`Template.psd`、`Items/TestItem.cs`。要排除文件夹，请写`FolderName/*`。 | 默认排除`build.txt`、以`.`开头的文件和文件夹、`bin/*`、`obj/*`和`Thumbs.db`。如果`includeSource`为`false`，则.csproj、.cs和.sln文件也将被排除。 |
| side | 控制此mod如何在客户端和服务器之间同步的ModSide。请参阅[ModSide](http://tmodloader.github.io/tModLoader/docs/1.4-stable/namespace_terraria_1_1_mod_loader.html#a1c82c6b1930a8ee5c45efb091a036b06)了解更多信息。 | Both |
| sortAfter | 此mod将被强制在其他mod之后加载的mod列表。按字母顺序排序时，此mod将在列表中最后一个mod之后加载。<br>仅在mod正常会在那些其他mod之前加载时影响加载顺序。<br>与`modReferences`列表中列出的mod自动发生。<br>按文件名（不带扩展名）引用mod。 |
| sortBefore | 此mod将被强制在其他mod之前加载的mod列表。此属性可以被认为是`sortAfter`属性的相反。<br>按文件名（不带扩展名）引用mod。 |
| playableOnPreview | 如果为false，如果从创意工坊加载，mod将拒绝在预览版tModLoader上加载。这可用于希望避免预览用户错误报告或希望在不影响的情况下发布到预览版的mod。大多数mod不需要使用此选项。 | true |
| translationMod <a name="translationmod"></a> | 如果为true，此mod将被视为为另一个mod提供翻译的mod。`modReferences`中列出的mod是此mod翻译的mod。[贡献本地化wiki页面](m2-参与本地化.md#contact-mod-developer-or-new-translation-mod)更详细地解释了这一点。当一起加载时，翻译mod将自动使用被翻译mod的本地化条目进行更新，从而使翻译mod作者能够轻松地将翻译更新到最新版本。这还将导致在Mods菜单中显示特殊图标，并添加一个[自定义创意工坊标签](https://steamcommunity.com/workshop/browse/?appid=1281930&requiredtags[]=Translation)，让用户更容易找到翻译mod。 | false |
| modSource | tModLoader内部使用，允许mod源代码存在于正常`ModSources`文件夹之外。请参阅[此页面](https://github.com/tModLoader/tModLoader/pull/4548)的"如何使用"部分了解如何此功能。 |
