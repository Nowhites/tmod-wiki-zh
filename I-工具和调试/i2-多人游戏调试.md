本指南旨在帮助那些在使用tModLoader进行多人游戏时遇到麻烦的人。

# 我无法使用"主持并游戏"加入自己的服务器
## 验证您正在连接正确的服务器
### "您使用的版本与此服务器不同"

您正在尝试连接的服务器不是正确版本。您可能正在尝试连接到未修改的服务器，或者可能正在连接到过时的tModLoader服务器。

1. 打开tModLoader
2. 通过打开[安装文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)并双击`start-tModLoaderServer.bat`文件直接打开`start-tModLoaderServer.bat`
3. 验证客户端和服务器的tModLoader版本完全匹配。如果不匹配，请重新安装最新的tModLoader。
4. 您也可以验证文件大小

## 版本正常
如果版本相同，但客户端仍然报告版本不同，请检查主机是否也在同一端口（通常为7777）上运行着原版/未修改的服务器。
如果版本不是问题，请阅读下面的[我的连接正常，但我的朋友或我在"正在连接..."时卡住了](#我的连接正常但我的朋友或我在正在连接时卡住了)

## 在"找到服务器"时卡住
如果您使用ASUS计算机，似乎有一个名为Armory Crate的程序阻止了在正常7777端口上运行服务器。您可以关闭该程序，或者使用不同端口手动启动服务器以绕过此问题。确保连接到服务器时也指定新端口。

如果您没有该程序，您可以通过打开资源监视器、检查网络选项卡、展开TCP连接和监听端口、然后按端口对两者进行排序来检查其他占用端口的程序。查找使用默认7777端口的任何程序。

# 当我加入自己的服务器时，mods被禁用
这是由有问题的mods引起的。您必须查看`server.log`文件或使用可见的控制台启动服务器来识别问题。
* （注意：选项当前已损坏，请改用下一选项）将显示服务器控制台设置为开启：[image](https://i.imgur.com/nfhC1GH.png)
  * 当控制台出现时，寻找红色文本或任何看起来不合适的内容。阅读异常消息时，您应该会看到提到的mod名称，禁用这些mods。
* 通过在文本编辑器中从[日志文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#logs)打开`server.log`文件来查看。查找异常消息并停止使用导致错误的mods。

如果您是正在被禁用的mod的开发者，最常见的原因是代码访问仅在客户端分配但在服务器上为`null`的字段。另一个常见问题是尝试访问或初始化UI字段，这些字段在服务器上也没有初始化。`server.log`中的错误消息应包含错误的文件和平面号，但定位和修复这些问题的最简单方法是[直接在调试器中调试服务器](https://github.com/tModLoader/tModLoader/wiki/Learn-How-To-Debug#调试服务器)。

# 我可以加入自己的服务器，但我的朋友不能
## 确保您有正确的IP地址
1. 如果您给朋友的IP地址看起来像`192.168.x.x`，那么您有错误的IP地址。以192.168开头的IP地址是本地IP地址，只能与物理上与您连接在同一路由器上的朋友一起玩游戏。
2. 要找到您的互联网IP地址，您可以谷歌"我的IP地址是什么"或访问[canyouseeme.org](https://www.canyouseeme.org/)等网站。这个数字是您需要提供给朋友的地址。这个数字可能会改变，取决于您的互联网服务提供商。
## 验证您的服务器对互联网可见
1. 通过打开[安装文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)并双击`start-tModLoaderServer.bat`文件直接打开`start-tModLoaderServer.bat`。（如果您正在进行"主持并游戏"，可以通过将显示服务器控制台设置为开启来执行此操作：[image](https://i.imgur.com/nfhC1GH.png)）
2. 选择一个世界并启动服务器
3. 在运行服务器的计算机上转到[canyouseeme.org](http://canyouseeme.org/)，然后输入7777作为端口（如果更改了端口，则进行调整）
4. 点击检查端口
    * 如果网站显示错误，您的端口未打开。您可能需要在路由器上启用UPnP，在Windows防火墙上一并阻止`[安装文件夹]/dotnet/dotnet.exe`文件，或者在路由器上手动端口转发。对于防火墙，您需要点击详情并确保路径与modded服务器路径匹配。根据您安装tModLoader的方式，可能有4个单独的条目，因此您需要检查所有这些条目。您需要确保两个复选框都已选中。对于路由器问题，您只需谷歌一下并学习如何操作。
![](https://i.imgur.com/Ds6brPn.png)
    * 如果网站显示成功，您应该在服务器控制台上看到一条消息，如"Exception normal: Tried to send data to a client after losing connection"。这很好。现在我们知道服务器对互联网可见，所以问题可能在您朋友的计算机上。
![](https://i.imgur.com/xxPNTBN.png)
![](https://i.imgur.com/4Eo66NN.png)

## 我的连接正常，但我的朋友或我在"正在连接..."时卡住了
这就变得复杂了。这个错误可能有多种原因，其中大部分可能是由编程不当的mods引起的。最好的方法是手动启动服务器（不是"主持并游戏"）并观察输出中出现的错误消息。（如果您正在进行"主持并游戏"，可以通过将显示服务器控制台设置为开启来执行此操作：[image](https://i.imgur.com/nfhC1GH.png)）

首先，我建议使用新的玩家和世界文件并尝试连接到服务器。如果这有效，问题可能是mod在正确保存或加载其自定义数据时遇到麻烦。

为了找出哪个mod导致问题，您需要多次启动服务器，要么在mods列表中添加或删除mods，直到您发现可以再次加入服务器。我会从没有mods开始，然后逐步添加，每次添加几个mods。一旦您发现无法再连接，您就通过排除法找到了一个问题的mod。不幸的是，解决方案是不要使用该mod，除非制作该mod的modder可以使用您的mod列表或世界和玩家文件来诊断问题。

过程：
![](http://i.imgur.com/5nGP6Qi.png)
