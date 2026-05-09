___

**[我不想创建模组，只想玩模组](tModLoader-guide-for-players)**

___

**[我不想创建模组，想以其他方式为tModLoader做贡献](tModLoader-guide-for-contributors)**

___


## 安装
如果您仍需要安装tModLoader，请参阅[tModLoader玩家指南](tModLoader-guide-for-players)。

## 使用tModLoader进行开发
在开始之前，您需要两样东西：".NET 8 SDK"和一个合适的IDE。安装这些后，您应该从[Basic tModLoader Modding Guide](Basic-tModLoader-Modding-Guide.md)开始。

### IDE
选择您喜欢的IDE来使用tModLoader制作模组。请参阅主页上的[开发部分](https://github.com/tModLoader/tModLoader/wiki#development)。为了获得最佳模组制作体验，强烈推荐使用Visual Studio。目前需要2022 v17.8或更高版本。

### .NET SDK
通过下载安装程序、运行它并按照说明安装[.NET 8 SDK](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)。下载标有"x64"的最新SDK版本的安装程序，对应您的操作系统。
不要下载标记为"Binaries"列中的链接。不要下载.NET 10.0或.NET 9.0版本，那些不起作用。

<details><summary>点击查看可视化说明</summary><blockquote>

![chrome_2024-05-02_11-55-52](https://github.com/tModLoader/tModLoader/assets/4522492/75a9299b-0e1a-41cd-acd0-e6d8ffcfe591)

</blockquote></details>

要验证".NET 8 SDK"是否正确安装，您可以打开一个新的命令提示符并输入`dotnet --list-sdks`然后按回车键。如果您看到以8开头的版本结果，则已安装。
![image](https://github.com/tModLoader/tModLoader/assets/4522492/ab93ade6-edb7-4088-881c-03f3871cd591)
显示的文件夹应该显示`Program Files`文件夹而不是`Program Files (x86)`文件夹。如果您看到86文件夹，则意味着您意外安装了32位SDK。您可以卸载它然后安装正确版本**或者**安装正确版本并[调整PATH变量](https://learn.microsoft.com/en-us/dotnet/core/install/windows?tabs=net80#no-net-sdk-was-found)，以便64位SDK显示出来。

如果tModLoader仍然抱怨找不到SDK，您可能需要重启计算机才能使更改生效。

## 我是模组新手
如果您是模组新手，建议从[入门指南](Basic-tModLoader-Modding-Guide)开始。然后您可以继续阅读[简单指南](home#easy-guides)。

以下链接可能对您有用：
1. [我们的网站](https://tmodloader.net)
1. [tModLoader文档](https://tmodloader.github.io/tModLoader/)
2. tModLoader wiki（有用信息和指南）-- **您现在就在这里。**
3. [加入我们的Discord服务器](https://discord.gg/tmodloader)（需要免费的Discord账户）
4. [Basic tModLoader Modding Guide](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Modding-Guide)
5. [Example Mod](https://github.com/tModLoader/tModLoader/tree/stable/ExampleMod) - 一个展示和教授各种tModLoader模组功能的模组
6. [tModLoader在TCF上的官方发布帖](http://forums.terraria.org/index.php?threads/.23726/)

## 我回归模组制作
如果您以前制作过tModLoader模组，并且想要更新过时的模组或制作新模组，tModLoader API很可能已更改。请阅读[更新迁移指南](https://github.com/tModLoader/tModLoader/wiki/Update-Migration-Guide)，了解您在阅读[tModPorter部分](https://github.com/tModLoader/tModLoader/wiki/Update-Migration-Guide#tmodporter)后错过的版本。如果有任何问题，您可以在Discord中提问。
