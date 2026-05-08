***
本指南已更新至 1.4。如果您需要查看此 wiki 页面的旧 1.3 版本，请点击[这里](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ/8a86002d330cd4aa54d17012d31322c73bacd94a)
***

<!-- 使用 https://luciopaiva.com/markdown-toc/ 生成 -->

# 目录

- [基本故障排除信息](#基本故障排除信息)
  - [关于 tModLoader 64 位](#关于-tmodloader-64-位)
  - [关于盗版](#关于盗版)
- [一般故障排除步骤](#一般故障排除步骤)
  - [切换到 Stable tModLoader 或 Preview tModLoader](#切换到-stable-tmodloader-或-preview-tmodloader)
  - [验证游戏完整性](#验证游戏完整性)
  - [验证泰拉瑞亚游戏完整性](#验证泰拉瑞亚游戏完整性)
  - [全新安装](#全新安装)
    - [全新安装泰拉瑞亚](#全新安装泰拉瑞亚)
  - [禁用所有模组](#禁用所有模组)
  - [禁用所有资源包](#禁用所有资源包)
  - [重置 config.json](#重置-configjson)
  - [流程图](#流程图)
  - [阅读 client.log](#阅读-clientlog)
    - [简单说明](#简单说明)
    - [复杂说明](#复杂说明)
- [获取支持](#获取支持)
  - [tModLoader 支持](#tmodloader-支持)
    - [GitHub 问题跟踪器](#github-问题跟踪器)
    - [Discord 支持](#discord-支持)
  - [模组问题](#模组问题)
    - [检测到此模组是为 `tModLoader vX.Y` 构建的](#检测到此模组是为-tmodloader-vxy-构建的)
    - [JITException](#jitexception)
- [启动问题](#启动问题)
  - [hostpolicy.dll 错误](#hostpolicydll-错误)
  - [Windows 版本 N 和 KN 缺少某些功能 / 无法加载 mfplat.dll](#windows-版本-n-和-kn-缺少某些功能--无法加载-mfplatdll)
  - [出现黑色窗口然后关闭但没有其他反应](#出现黑色窗口然后关闭但没有其他反应)
    - [手动启动 tModLoader](#手动启动-tmodloader)
    - [全新安装](#全新安装)
    - [删除 `dotnet` 文件夹](#删除-dotnet-文件夹)
    - [检查 Natives.log](#检查-nativeslog)
    - [收集 Minidump 说明](#收集-minidump-说明)
    - [禁用 Proton](#禁用-proton)
    - [检查 launch.log](#检查-launchlog)
    - [禁用 RGB 键盘功能](#禁用-rgb-键盘功能)
    - [重启计算机](#重启计算机)
  - ["泰拉瑞亚已过时" 或 "泰拉瑞亚处于旧版本"](#泰拉瑞亚已过时-或-泰拉瑞亚处于旧版本)
  - [更新 tModLoader 时发生错误（缺少可执行文件）](#更新-tmodloader-时发生错误缺少可执行文件)
  - [无法启动 tModLoader 进程："系统找不到指定的路径"](#无法启动-tmodloader-进程系统找不到指定的路径)
  - [无法启动 tModLoader 进程："操作已成功完成。" 0x0](#无法启动-tmodloader-进程操作已成功完成-0x0)
  - [加载资源失败 (AssetLoadException)](#加载资源失败-assetloadexception)
  - [Texture2D 创建失败！错误代码：没有足够的内存资源来完成此操作。(0x8007000E)](#texture2d-创建失败错误代码没有足够的内存资源来完成此操作-0x8007000e)
  - [Texture2D 创建失败！错误代码：GPU 将不响应更多命令，很可能是因为调用应用程序传递了无效命令。(0x887A0006)](#texture2d-创建失败错误代码-gpu-将不响应更多命令很可能是因为调用应用程序传递了无效命令-0x887a0006)
  - [NoSuitableGraphicsDeviceException: 找不到 d3dcompiler_47.dll](#nosuitablegraphicsdeviceexception-找不到-d3dcompiler_47dll)
  - [NoSuitableGraphicsDeviceException: 无法创建交换链！错误代码：参数不正确。(0x80070057)](#nosuitablegraphicsdeviceexception-无法创建交换链错误代码参数不正确-0x80070057)
  - [System.DllNotFoundException: 无法加载 DLL 'vcruntime140.dll'或其依赖项之一：找不到指定的模块。(0x8007007E)](#systemdllnotfoundexception-无法加载-dll-vcruntime140dll或其依赖项之一找不到指定的模块-0x8007007e)
  - [System.BadImageFormatException: 尝试加载格式不正确的程序。(0x8007000B)](#systembadimageformatexception-尝试加载格式不正确的程序-0x8007000b)
  - [资源的多个扩展名](#资源的多个扩展名)
  - [System.Threading.SynchronizationLockException](#systemthreadingsynchronizationlockexception)
  - [启用模组时游戏冻结/设置控制不正确](#启用模组时游戏冻结设置控制不正确)
  - [磁盘写入错误](#磁盘写入错误)
  - [受控文件夹访问](#受控文件夹访问)
  - [System.UnauthorizedAccessException: 访问路径被拒绝。](#systemunauthorizedaccessexception-访问路径被拒绝)
  - [在 Mac 上无法加载共享库 libSDL2-2.0.0.dylib 或其依赖项之一](#在-mac-上无法加载共享库-libsdl2-200dylib-或其依赖项之一)
  - [没有日志文件夹](#没有日志文件夹)
- [音频问题](#音频问题)
    - [IAudioClient 解决方法](#iaudioclient-解决方法)
  - [音频故障排除](#音频故障排除)
- [多人游戏](#多人游戏)
  - [一般多人游戏故障排除步骤](#一般多人游戏故障排除步骤)
  - [延迟](#延迟)
- [无响应](#无响应)
  - [Minidump 说明](#minidump-说明)
  - [无法点击任何内容](#无法点击任何内容)
- [加载模组](#加载模组)
    - ["尝试打开 tModLoader 时某个模组崩溃"](#尝试打开-tmodloader-时某个模组崩溃)
    - [在成功调用 End 之前无法再次调用 Begin](#在成功调用-end-之前无法再次调用-begin)
    - [OutOfMemoryException](#outofmemoryexception)
- [玩家/世界](#玩家世界)
    - [加载失败！](#加载失败)
    - [玩家或世界上的"(LaterVersion)"](#玩家或世界上的laterversion)
    - [玩家上的"(Unknown error)"](#玩家上的unknown-error)
    - ["救命，我所有的玩家和世界都不见了！"](#救命我所有的玩家和世界都不见了)
    - ["云存储限制已达到，无法移动到云"](#云存储限制已达到无法移动到云)
- [模组浏览器](#模组浏览器)
    - ["模组浏览器离线"、"我无法下载模组"](#模组浏览器离线我无法下载模组)
    - [无法下载模组，模组下载进度条停留在 0.0 字节](#无法下载模组模组下载进度条停留在-00-字节)
    - [无法重新下载模组](#无法重新下载模组)
- [保存数据文件问题](#保存数据文件问题)
    - [Windows 10+ OneDrive](#windows-10-onedrive)
      - 云文件提供程序未运行
      - System.IO.IOException: 云操作不成功
      - System.IO.FileNotFoundException: 找不到文件 ...tModLoader\Mods
    - [无法找到我的 tModLoader 1.4.3 保存数据或自动迁移文件失败。](#无法找到我的-tmodloader-143-保存数据或自动迁移文件失败)
    - [Config.json 损坏](#configjson-损坏)
    - [尝试从 X 移植到 Y 中止，Z 文件已损坏。](#尝试从-x-移植到-y-中止z-文件已损坏)
  - [迁移失败](#迁移失败)
    - [重试](#重试)
    - [手动移植](#手动移植)
    - [非法文件名](#非法文件名)
- [Linux / Steam Deck / Mac](#linux--steam-deck--mac)
    - [兼容性选项](#兼容性选项)
    - [无法启动原生构建 - 点击 Steam 开始但没有任何反应](#无法启动原生构建---点击-steam-开始但没有任何反应)
    - [Flatpak、Steam Deck Steam 及相关沙盒应用程序](#flatpak-steam-deck-steam-及相关沙盒应用程序)
    - [Arm64 Linux 解决方法](#arm64-linux-解决方法)
    - [找不到系统 .NET SDK，没有沙盒](#找不到系统-net-sdk-没有沙盒)
- [其他问题](#其他问题)
    - [Steam 游戏录制](#steam-游戏录制)
    - [RGB 键盘错误 (端口 53664)](#rgb-键盘错误-端口-53664)
    - [低 FPS](#低-fps)
    - [幽灵鼠标](#幽灵鼠标)
    - [没有小地图](#没有小地图)

# 基本故障排除信息
本指南将帮助用户修复许多常见问题。请在此文档中搜索您遇到的错误。您可以使用 `ctrl-F` 来使用您的网络浏览器在此页面上搜索单词（例如异常名称或错误消息）。如果您找不到任何相关内容，请尝试[一般故障排除步骤](#一般故障排除步骤)中的所有建议。如果那里没有帮助，您可以在[获取支持部分](#获取支持)中追求直接支持。请注意，本指南主要涉及 tModLoader 本身的问题。如果您遇到特定模组或模组组合的问题，请参阅[模组故障排除步骤](#模组问题)

## 关于 tModLoader 64 位
请不要尝试将 "tModLoader 64 位" 安装到 tModLoader 中，它不再有用，会阻止游戏正确启动。如果您之前已安装它并遇到问题，您的第一个故障排除步骤是进行[全新安装](#全新安装)。您还需要按照[这些说明](#无法启动-tmodloader-进程系统找不到指定的路径)清除 `Launch Options`。

## 关于盗版
如果您盗版了泰拉瑞亚，我们无法帮助您。tModLoader 无法工作。请不要通过询问如何使其工作来打扰我们。

# 一般故障排除步骤
如果您的特定问题在本指南后面的部分中没有找到，应遵循这些步骤。

## 切换到 Stable tModLoader 或 Preview tModLoader
如果您使用的是 tModLoader 的 `preview` 版本，tModLoader 出现问题的可能性更大，因此您应该切换回 `stable` tModLoader（`None` beta 分支）看看是否能解决您的问题。相反，有时错误会在 `preview` 版本中修复，因此您可以从 `None` 切换到 `preview-v2025.X` 进行测试，看看即将发布的月度版本是否有针对您问题的修复。

<details><summary>切换 tModLoader 分支</summary><blockquote>

在 Steam 中，右键点击 `tModLoader`，选择 `Properties`，选择 `Betas`，然后在下拉菜单中选择 `preview-v2025.X` 或 `None`。忽略访问代码部分。关闭 Properties 窗口，您应该会看到 tModLoader 正在下载更新，一旦完成，您就可以启动它。
![image](https://i.imgur.com/aoy3HEp.png)

视频说明：

https://github.com/tModLoader/tModLoader/assets/4522492/bfcacae5-00e2-4488-a778-d2cf214299af

如果您切换到 `preview` 版本并且您的问题已解决，请记住 `preview` 主要面向模组制作者和测试人员，您可能会发现您使用的某些模组现在已损坏。`preview` 版本中正在测试的修复将在每月初到达 `stable` tModLoader（除非有扩展的 preview 窗口）。如果可以避免问题，等到下个月可能比停留在 `preview` 版本更好。

</blockquote></details>

## 验证游戏完整性
验证游戏完整性将把安装文件夹中 tModLoader 安装的所有文件恢复到原始状态。在 Steam 中右键点击库中的 `tModLoader`，然后点击 `Properties` 点击 `Local Files`。点击 `Verify integrity of game files...`。这将开始下载现在缺少的文件。完成后，再次尝试 tModLoader 看看您的问题是否已解决。如果没有，或者您在 GOG 上，请遵循[全新安装](#全新安装)说明。

<details><summary>验证游戏文件视频说明</summary><blockquote>

https://github.com/tModLoader/tModLoader/assets/4522492/650c43d3-1bac-49e9-aa5e-32aa91f076cf

</blockquote></details>

## 验证泰拉瑞亚游戏完整性
tModLoader 从泰拉瑞亚安装文件夹加载文件。对泰拉瑞亚执行上述相同步骤。如果您加入了 `v1.0.6.1 - Undeluxe Edition` 或 `v1.1.2 - First Final Update` 泰拉瑞亚 beta 分支，请确保首先切换回 `None` beta 分支。

## 全新安装
全新安装可以解决阻止游戏启动的许多问题。全新安装后，**不要**将任何其他文件放入安装目录。首先，打开[安装位置](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)。

确保您打开的是安装文件夹，而不是存档文件夹。存档文件夹有名为 "Players" 和 "Worlds" 的文件夹，而安装文件夹有 "tModLoader-Logs" 和 "LaunchUtils" 等文件夹。现在打开安装文件夹后，选择所有文件和文件夹并**删除它们全部**。接下来，重新安装 tModLoader：
* Steam 安装：在库中右键点击 `tModLoader`，然后点击 `Properties` 点击 `Local Files`。点击 `Verify integrity of game files...`。这将开始下载现在缺少的文件。在安装文件夹中，您应该会看到文件开始再次出现。
* GOG 安装：正常安装到与之前相同的文件夹。

### 全新安装泰拉瑞亚
tModLoader 从泰拉瑞亚安装文件夹加载文件。对泰拉瑞亚执行上述相同步骤。如果您加入了 `v1.0.6.1 - Undeluxe Edition` 或 `v1.1.2 - First Final Update` 泰拉瑞亚 beta 分支，请确保首先切换回 `None` beta 分支。

## 禁用所有模组
如果您遇到问题，确认问题是模组还是 tModLoader 造成的很有用。通常可以通过阅读游戏中的错误消息或[阅读 client.log](#阅读-clientlog) 来确定问题的原因，但如果不能，禁用所有模组并确认问题是否仍然发生或不再发生很有用。

首先，访问模组菜单并点击 `disable all`（如果您的问题阻止您进入主菜单，您可以在游戏启动时按住 shift 键跳过加载模组，或者在存档文件夹的 Mods 文件夹中找到 "enabled.json" 并删除它）。关闭 tModLoader 并再次启动。尝试重现您之前遇到的错误。如果错误仍然发生，那是 tModLoader 问题，您应该尝试[获取支持](#获取支持)。如果错误不再发生，您需要使用[流程图](#流程图)来识别导致问题的模组。

## 禁用所有资源包
资源包可能导致问题，禁用所有资源包然后再次测试可以排除资源包是问题所在。要禁用资源包，您可以访问 `Workshop->Use Resource Packs` 菜单并禁用所有包。如果您无法进入该菜单，关闭游戏，在文本编辑器中打开 `\Documents\My Games\Terraria\tModLoader\config.json`，找到 "ResourcePacks" 部分，然后将所有 true 改为 false，保存文件。

## 重置 config.json
`config.json` 保存用户偏好信息。有时其中的错误值可能导致问题。您可以删除或重命名该文件以将偏好重置为默认值，以测试它是否导致了您的问题。打开[存档文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)并找到 `config.json`。将 `config.json` 重命名为 `configOld.json`。重命名它可以让您以后在需要时恢复它。当您启动 tModLoader 时，您应该会看到 `Select Language` 菜单，如果没有，您可能编辑了错误的文件。如果这样做对您的问题没有任何改变，您可以关闭 tModLoader 并在删除新生成的 `config.json` 后将文件名改回 `config.json`。

您可以对 `input profiles.json` 文件执行相同的操作，如果您的问题与键盘、鼠标或游戏手柄问题相关。

## 流程图
有时某个模组导致问题，但您无法判断是哪个模组。使用此流程图来诊断和确定有问题的模组：
![ModIssueFlowchart](https://github.com/user-attachments/assets/0517bb4b-6b43-4035-b798-9f726ada2ae0)

## 阅读 client.log
### 简单说明
tModLoader 中的大多数错误都会作为错误记录到 `client.log` 文件中。阅读该文件以找到可能导致错误的模组。
* 关闭 tModLoader，然后重新打开 tModLoader 并重复触发错误所需的步骤。
* 在文本编辑器中打开 `client.log` 文件，例如记事本。`client.log` 文件位于[日志文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#logs)中
  * 要轻松打开文件夹，请使用 `ctrl-c` 将 `%UserProfile%\Documents\My Games\Terraria\tModLoader\Logs\` 复制到剪贴板，然后使用 `ctrl-v` 将其粘贴到文件资源管理器的地址栏中。按回车，文件资源管理器将转到该文件夹。
![](https://i.imgur.com/mdKWVXy.png)
  * 如果弹出窗口询问"您想如何打开此 .log 文件？"，在列表中找到记事本，点击它，点击 OK。
![](https://i.imgur.com/Q7HPPLK.png)
* 向下滚动，直到在文件中看到第一个异常。它应该看起来像下面的图像。查找缩进后跟 "at" 的行，这些行是异常的详细信息。日志中每个这样的异常都是由错误引起的。
![](https://i.imgur.com/5rA3yR6.png)
* 查看异常以找到您已启用的模组名称。在 "at" 后面一行的第一个单词和句点之前是参与异常的模组名称将显示的地方。像 "Terraria"、"Microsoft" 和 "MonoMod" 这样的词通常可以忽略。在这个示例中，名为 "GadgetGalore" 的模组在异常中，很可能是损坏的模组。
  * 如果单个堆栈跟踪中有多个模组名称，任一模组都可能有问题，或者两者都有，您需要进行一些测试。
* 在异常中找到模组后，下次启动 tModLoader 时禁用它，看看是否会触发相同的错误。如果有其他导致错误的模组，请重复上述步骤。
* 如果您在阅读 client.log 方面需要帮助，您可以在[tModLoader Discord 聊天](https://discord.gg/tmodloader)的 support-forum 频道寻求帮助。

### 复杂说明
您在玩 tModLoader 时可能遇到的许多错误都可以通过阅读 `client.log` 文件来识别。（如果您遇到的错误仅在多人游戏中发生，您需要同时阅读 `client.log` 和 `server.log`）。通过正确阅读日志文件，您可以识别问题是由 tModLoader 中的错误还是您使用的模组中的错误引起的。由于 tModLoader 每月都会更新到新版本，昨天可以工作的模组今天可能会损坏。无论如何，日志是找出问题的好地方。

如果您遇到错误，首先确保所有模组都是最新的，然后关闭 tModLoader 并重新打开它。这将重置日志，使其更容易阅读。一旦遇到错误，关闭 tModLoader 并打开日志文件。日志文件是位于[日志文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#logs)中的 `client.log` 文件。如果它没有文件关联，请在普通文本编辑器（如记事本）中打开该文件。

接下来，您需要浏览文件查找"error"或"exception"等单词。使用 `ctrl-f` 来使用文本编辑器的查找功能。您通常会希望关注日志文件中最早发现的错误。出现在早期错误之后的错误可能是由那些早期错误引起的，因此修复早期错误可能会修复后续错误。一旦找到"error"或"exception"这个词，您应该会看到类似于此的内容：
![](https://i.imgur.com/5rA3yR6.png)
在上面的 `client.log` 摘录中，我们可以看到一个错误被记录。错误从第 41 行开始，第 42 行显示蓝色摘要。错误摘要之后，会有一个"堆栈跟踪"，显示错误发生在代码的什么地方。注意堆栈跟踪行的缩进，以绿色显示。这种缩进对于快速找到日志文件中记录的错误很有用，而不是使用 `crtl-f`。接下来，您需要在堆栈跟踪中找到您已启用的模组的名称。在 "at" 后面一行的第一个单词和句点之前是您要查看的关键单词。像 "Terraria"、"Microsoft" 和 "MonoMod" 这样的词通常可以忽略，您想要找到错误中显示的模组或模组。在这个示例中，我们在堆栈跟踪中看到一个名为 "GadgetGalore" 的模组，可以假设它是错误的原因。如果单个堆栈跟踪中有多个模组名称，任一模组都可能有问题，或者两者都有，您需要进行一些测试。既然我们找到了一个出错的模组，您可以禁用该模组并重试。并非日志中记录的每个错误都是实际错误，因此可能需要一些实验。如果您在阅读 client.log 方面需要帮助，您可以在[tModLoader Discord 聊天](https://discord.gg/tmodloader)的支持频道中寻求帮助。

# 获取支持
如果本指南中的所有内容都无法解决您的问题，您可能需要直接支持。

支持问题分为两类：[特定于模组的问题](#模组问题) 和 [tModLoader 支持](#tmodloader-支持)

## tModLoader 支持
如果您需要基本支持来启动游戏，请参阅 [Discord 支持](#discord-支持)，否则从 [GitHub 问题跟踪器](#github-问题跟踪器) 开始

### GitHub 问题跟踪器
如果本指南没有解决您的问题并且您仍然需要帮助，首先检查[tModLoader 问题跟踪器](https://github.com/tModLoader/tModLoader/issues)并搜索您的问题，看看它是否已被报告。如果您找到您的问题，请对该问题添加 :thumbsup:  reaction 以帮助我们确定问题的优先级。要添加 :thumbsup: reaction，请点击顶部的微笑图标，然后点击 :thumbsup:: 

![](https://i.imgur.com/EDBwXtc.png)

添加包含 `client.log` 和您可以提供的其他有用信息的评论来帮助识别问题的原因也可能有用。评论中可能也有您可以遵循的变通方法。

### Discord 支持
如果您在问题跟踪器中找不到您的问题，您应该访问[tModLoader discord](https://discord.gg/tmodloader)上的 `#support-forum` 频道。Discord 是一项免费的聊天服务，您可以在浏览器中访问。我们不会在此处提供 GitHub 支持支持问题。一旦找到 `#support-forum` 频道，创建一个新帖子描述您的问题。您还需要通过打开[日志文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#logs)并将文件拖入聊天中来发布您的所有日志。那里的人应该能够为您提供帮助。

<details><summary>发布日志到 Discord 视频说明</summary><blockquote>

https://github.com/tModLoader/tModLoader/assets/4522492/3f4efa43-753f-4733-b630-ba85e711340c

</blockquote></details>

## 模组问题
如果特定模组导致您的问题，您应该访问[模组的工作室页面](https://steamcommunity.com/app/1281930/workshop/)。阅读 `Description` 并查看模组制作者是否有首选的错误联系方法，例如 `GitHub` 或 `Discord`。如果有，请使用它。如果没有，在工作室页面上发表评论并描述您的问题应该可以让创建者知道。如果错误是游戏破坏性的，您将不得不禁用该模组或等待作者修复。

### 检测到此模组是为 `tModLoader vX.Y` 构建的
此消息是一条模板消息，仅供参考。如果版本以 `v0.X` 开头，那么您正在尝试加载旧版模组，它无法工作，您需要删除该模组并在当前模组浏览器上找到它或它的替代品。如果版本以 `v202X` 开头，那么您应该查看错误消息中的下一段以获取实际错误。如果多个模组名称出现在错误中，可能是模组不兼容。作者将能够弄清楚。

### JITException
如果您看到此消息，该模组可能已过时。tModLoader 每月更新一次，有时这些更新会破坏模组。如果现在是月初，作者可能只是更新较慢。您将不得不禁用此模组直到它被修复。如果您手动安装了模组，该模组在 `Mods` 菜单上将是紫色的，您应该删除它并使用工作室版本。

# 启动问题
一些用户报告了当泰拉瑞亚或 tModLoader 安装在外置驱动器上时出现问题。如果您遇到下面未提及的问题，请尝试安装在正常的硬盘驱动器上。

## hostpolicy.dll 错误
请遵循[出现黑色窗口然后关闭但没有其他反应](#出现黑色窗口然后关闭但没有其他反应)部分中的说明。

## Windows 版本 N 和 KN 缺少某些功能 / 无法加载 mfplat.dll
您需要下载 Windows Media Pack，因为它没有随您的 Windows 安装一起提供。
请遵循此[指南](https://answers.microsoft.com/en-us/windows/forum/all/mfplatdll-is-missing-cant-install-media-feature/15964b8d-9e43-4ea9-b073-bd20794b6f4d)。您需要展开"回复"部分才能看到第 1 个答案。用户报告说，如果安装该功能无法解决问题，遵循同一帖子中提到的"方法 2"步骤，即"从提升的命令提示符启用媒体播放"说明，则有效。

## 出现黑色窗口然后关闭但没有其他反应
有多种问题可能导致此问题。如果这些建议都不起作用，请来到[tModLoader Discord 支持](#discord-支持)

### 手动启动 tModLoader
有时手动启动可以绕过某些启动问题。打开[tModLoader 安装文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)并双击 "start-tModLoader.bat"。如果这有效，您仍然应该尝试通过此处的其他步骤或[tModLoader 支持](#tmodloader-支持)来修复问题，因为这样启动不方便。

如果当您尝试此操作时，错误消息显示"Windows 找不到 '[InstallFolderHere]/start-tModLoader.bat'。请确保您输入的名称正确，然后重试。"，那么您的 Windows 某种方式已损坏，并且对 `.bat` 文件有错误的文件关联。此问题将阻止您的计算机运行任何 `.bat` 文件，这可能会影响其他程序，因此最好修复。用户报告说，遵循[这篇文章](https://www.winhelponline.com/blog/bat-files-do-not-run-when-double-clicked-fix-association/)中的步骤对他们有效。

如果窗口立即关闭，一个已报告的原因是命令提示符本身损坏。尝试从开始菜单打开命令提示符。如果您不能这样做，您的计算机处于损坏状态，需要在尝试其他任何事情之前进行修复。一位用户报告说他们的计算机在注册表中有一个错误的值，他们遵循了[这个 stackoverflow 答案](https://stackoverflow.com/questions/5373137/cmd-exe-closes-immediately-after-calling-win7-64/5374418#5374418)来删除错误的注册表值。如果不是这种情况，您需要搜索一些其他修复方法来启动命令提示符。

### 全新安装
请遵循[全新安装](#全新安装)和[全新安装泰拉瑞亚](#全新安装泰拉瑞亚)步骤。这可能看起来像很多工作，但它可以解决大部分问题。

### 删除 `dotnet` 文件夹
删除[tModLoader 安装文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)中的 `dotnet` 文件夹可能会有所帮助。如果问题持续存在，请检查您的杀毒软件是否正在隔离任何文件。`dotnet\shared\Microsoft.NETCore.App\8.0.0` 文件夹应该有 `184` 个项目。如果没有，请确保您的操作系统是 64 位，并在删除 `dotnet` 文件夹之前安装适合您操作系统的[依赖项](https://learn.microsoft.com/en-us/dotnet/core/install/windows?tabs=net80#additional-deps)，然后再试一次。

注意：如果您的控制台窗口表明在 GOG 上下载 dotnet 时遇到问题，您可以手动下载 [dotnet 8.0.0](https://dotnetcli.azureedge.net/dotnet/Runtime/8.0.0/dotnet-runtime-8.0.0-win-x64.zip) 并将其放入安装文件夹的 `LaunchUtils` 文件夹中。文件应命名为 `dotnet-runtime-8.0.0-win-x64.zip`。再次删除 `dotnet` 文件夹，然后重试启动。

如果您仍然遇到问题，并且 `dotnet\dotnet.exe` 或提到的 `184` 文件不全部存在，您可以手动安装包含的 dotnet。删除 `dotnet` 文件夹，然后再次创建 `dotnet` 文件夹并打开它。在另一个文件资源管理器窗口中，打开 `LaunchUtils\dotnet-runtime-8.0.0-win-x64.zip` 文件，您应该会看到其中包含 2 个文件夹和 3 个文件。将所有这些文件夹和文件拖放到您之前创建的 `dotnet` 文件夹中。

如果在重新安装后您仍然遇到 dotnet 问题，一些用户报告说在删除 `dotnet` 文件夹后安装 [.NET SDK](https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-8.0.204-windows-x64-installer) 可以解决此问题。我们的猜测是完整安装程序修复了一些阻止我们安装程序工作的问题。

如果在重新安装后您仍然遇到问题，请确保"以管理员身份运行此程序"未在 `dotnet.exe` 上设置。

<details><summary>检查"以管理员身份运行此程序"设置说明</summary><blockquote>

打开 `dotnet` 文件夹，右键点击 `dotnet.exe` 并选择 `Properties`。点击 `Compatibility` 选项卡。查找"以管理员身份运行此程序"复选框，并确保它未选中。点击 `OK` 并再次尝试运行游戏。

![image](https://github.com/tModLoader/tModLoader/assets/4522492/7b557d5c-17ec-4e0b-b67d-1c93acfe436c)

</blockquote></details>

### 检查 Natives.log
在[日志文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#logs)中，打开 `Natives.log`。
* 如果它说"在应用程序依赖项清单 (Microsoft.NETCore.App.deps.json) 中指定的程序集未找到"或类似内容，请仔细检查您是否遵循了上面 **删除 `dotnet` 文件夹** 建议的所有步骤。
* 如果它说"System.AccessViolationException: 尝试读取或写入受保护的内存。"，请遵循[全新安装](#全新安装)和[全新安装泰拉瑞亚](#全新安装泰拉瑞亚)步骤。
* 如果它说"Assertion failed: !FAILED(hr) && "Failed to find supported audio format!"" 或 "Assertion failed: !FAILED(hr) && "Failed to activate audio client!""，请遵循[音频故障排除](#音频故障排除)的**更改音频设备**部分。
* 如果它说"./LaunchUtils/ScriptCaller.sh: exec: line 83: C:/Program Files (x86)/Steam/steamapps/common/tModLoader/dotnet/dotnet.exe: Invalid argument"，请遵循[删除 dotnet 文件夹](#删除-dotnet-文件夹)步骤。
* 如果它说"Fatal error. System.AccessViolationException: 尝试读取或写入受保护的内存。这通常表明其他内存已损坏。
   at System.Globalization.CompareInfo.IcuCompareString(System.ReadOnlySpan'1<Char>, System.ReadOnlySpan'1<Char>, System.Globalization.CompareOptions)"，那么打开安装文件夹中的 `tModLoader.runtimeconfig.json` 并在 `DEFAULT_STACK_SIZE` 行之前添加 `"System.Globalization.UseNls": true,`，然后保存。
* 如果它说"Could not load ICU data. UErrorCode: 2"，请遵循上面 bullet 中的说明。
* 如果它说"Assertion failed: 0 && "Effect output format not supported", file C:\Games\Terraria\tModLoader\FNA\lib\FAudio\src\FAudio.c"，则禁用 TerrariaAmbiance 模组或将选定的输出设备输出速率调整为 48000 Hz 或 44100 Hz。
* 如果文件完全为空，您可能需要尝试本页上的许多不同建议。
  * 此问题的最新报告是通过重新安装或修复[上面 vcruntime140.dll 部分](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ#systemdllnotfoundexception-unable-to-load-dll-vcruntime140dll-or-one-of-its-dependencies-the-specified-module-could-not-be-found-0x8007007e)中提到的 `Microsoft Visual C++ 2015 Redistributable Update 3` 安装，然后重启计算机来解决的。
  * 此问题的另一个最新报告是由连接的 USB 控制器引起的。在这个用户的特定情况下，他们安装了 Xbox 360 Controller Emulator (x360ce)。问题来自 mayflash 的相关驱动程序，具体是位于 `C:/Windows/USB Vibration/7906` 的 `EZFRD64.dll` 驱动程序。断开控制器连接可以解决问题。可能可以更新此驱动程序，但尚未测试。
  * 如果其他方法都无效，您可能需要来到[我们的 Discord 支持论坛](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ#discord-support)并 ping jopojelly 并提供 minidump 文件。以下是相关说明。

### 收集 Minidump 说明
可以生成 minidump 文件以为 tModLoader 开发人员提供有关导致静默崩溃的错误的更多信息。静默崩溃是指在任何日志文件中没有错误的崩溃。对于在日志文件中报告错误的崩溃，无需遵循这些说明。

<details><summary>展开查看 Minidump 说明</summary><blockquote>

为此，下载 https://download.sysinternals.com/files/Procdump.zip 并将 zip 提取到 tModLoader 安装文件夹中。在安装文件夹中创建一个名为 `dump` 的文件夹。通过在文件资源管理器地址栏中输入 `cmd` 然后按回车，在 tModLoader 文件夹中打开命令提示符。
![image](https://github.com/user-attachments/assets/4de9dadb-c71d-4fe2-8b6d-880a66de3f85)
这将打开命令提示符，请确保它是您的安装文件夹：
![image](https://github.com/user-attachments/assets/f1ccdee8-23fe-4ec1-ae0f-e9687f15c05c)
在命令提示符中，键入 `procdump.exe -e -x .\dump dotnet\dotnet.exe tModLoader.dll -console` 然后按回车：
![image](https://github.com/user-attachments/assets/f31ff6a8-ad5d-4dcc-a814-f0d09238dbed)
游戏应该开始加载，然后在崩溃点崩溃。一旦崩溃，检查 `dump` 文件夹中是否有 `.dmp` 文件。这就是您需要上传到 Discord 论坛帖子的文件（您可以直接将文件拖放到 Discord 中）。还要确保 ping jopojelly，一位 tModLoader 开发人员，以获取有关此特定问题的帮助。如果您没有 `.dmp` 文件，那么我们可以在 Discord 上进一步调查。
![image](https://github.com/user-attachments/assets/4b6a5600-9cd6-4e50-aa17-fec9f2d93172)

</blockquote></details>

### 禁用 Proton
在 Linux 上不要使用 Proton，它无法工作。如果您这样做了，您将需要在禁用 tModLoader 的 Proton 后[删除 dotnet 文件夹](#删除-dotnet-文件夹)。

如果您在使用 Proton 启动时创建了玩家和世界，您需要将它们移动到正确的存档目录才能再次使用它们。为此，通过打开 `/home/[username]/.local/share/Steam/steamapps/compatdata/1281930/pfx/drive_c/users/steamuser/Documents/My Games/Terraria/tModLoader` 来找到 Proton 存档位置。您可以获取所有文件，或者只是您想要的文件，并将它们复制到[正确的存档文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)。

### 检查 launch.log
打开[日志文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#logs)中的 `launch.log`。如果您的日志中出现"FAudio"，您可能想尝试[IAudioClient-workaround.zip](https://github.com/tModLoader/tModLoader/issues/2863#issuecomment-1221975856)

如果应用解决方法后，您看到"Unable to load DLL 'FAudio' or one of its dependencies: The specified module could not be found. (0x8007007E)"，那么一种可能性是您没有遵循说明来替换 .zip 中所有文件，而只替换了 FAudio.dll 文件。所有文件都需要被复制。

### 禁用 RGB 键盘功能
即使您认为自己没有 RGB 键盘，这也可以提供帮助。请遵循[RGB 键盘错误 (端口 53664)](#rgb-键盘错误-端口-53664)说明。

### 重启计算机
重启计算机可以解决 tModLoader 用户面临的许多问题。这可能看起来是老生常谈，但它有效，试试看吧。

## "泰拉瑞亚已过时" 或 "泰拉瑞亚处于旧版本"
![dotnet_2024-04-24_13-31-39](https://github.com/tModLoader/tModLoader/assets/4522492/f7b8d92a-1c81-4662-a13d-a597efbf8914)

如果您收到消息说"泰拉瑞亚已过时，您需要在 Steam 中更新泰拉瑞亚"，请遵循[验证泰拉瑞亚游戏完整性](#验证泰拉瑞亚游戏完整性)步骤。

![dotnet_2024-04-24_13-33-41](https://github.com/tModLoader/tModLoader/assets/4522492/29e2e369-fcf5-4f3a-98f1-deb95c4c4ac7)

如果您收到消息说"泰拉瑞亚处于旧版本，您需要切换回 Steam 中的正常泰拉瑞亚版本才能加载 tModLoader"，则在切换回正常泰拉瑞亚 beta 分支后遵循[验证泰拉瑞亚游戏完整性](#验证泰拉瑞亚游戏完整性)步骤。为此，在 Steam 中右键点击泰拉瑞亚并选择 `Properties...`，点击 `Beta`，然后在 `Beta Participation` 下拉菜单中选择 `None`。

## 更新 tModLoader 时发生错误（缺少可执行文件）
![image](https://user-images.githubusercontent.com/4522492/194225008-48810974-7292-4b5b-974a-e03363b4383f.png)
这通常可以通过在确认您已切换到 tModLoader 的 `None` 分支并验证游戏完整性后重启计算机来解决，然后遵循下一节。

## 无法启动 tModLoader 进程："系统找不到指定的路径"
![image](https://user-images.githubusercontent.com/4522492/194225054-b0d9cac7-10fd-4864-87e9-0d16c9418ed1.png)
这是由之前安装 `tModLoader 64 bit` 引起的。首先，如果还没有，请进行[全新安装](#全新安装)，然后在 `Steam` 中右键点击 `tModLoader` 并选择 `Properties`。确保 `Launch Options` 完全为空，然后关闭窗口。
![image](https://user-images.githubusercontent.com/4522492/194367542-7f6d2700-542e-4a14-8074-68fb9a9c7677.png)

## 无法启动 tModLoader 进程："操作已成功完成。" 0x0
![failiuyre](https://github.com/tModLoader/tModLoader/assets/4522492/5b6c8c50-ec14-42d2-bc4c-32474b500e99)
当 `.bat` 文件的文件关联损坏时，我们看到有人报告了这个问题。打开安装文件夹并查看 `start-tModLoader.bat` 文件。图标应该看起来与此类似：
![image](https://github.com/tModLoader/tModLoader/assets/4522492/0e2e487e-de0b-4214-af13-b6a4f09fcc66)
如果不是，`.bat` 文件的文件关联已更改。这不应该发生，您可能需要扫描病毒。之后，您需要使用 Google 来弄清楚如何为您的 Windows 版本修复 `.bat` 文件关联。

## 加载资源失败 (AssetLoadException)
![image](https://user-images.githubusercontent.com/4522492/194373688-06a6aedc-ca27-4fe5-9587-7971a6e6c09e.png)
这通常是由您的泰拉瑞亚安装已过时引起的。首先，启动泰拉瑞亚并通过查看右下角确认它已更新到[最新版本](https://terraria.fandom.com/wiki/PC_version_history)。接下来，遵循[验证泰拉瑞亚游戏完整性](#验证泰拉瑞亚游戏完整性)说明。如果这不能解决问题，可能是模组导致此问题。

## Texture2D 创建失败！错误代码：没有足够的内存资源来完成此操作。(0x8007000E)
这意味着您的计算机已耗尽视频内存（VRAM）。这与可用磁盘空间或正常 PC 内存 (RAM) 不同，笔记本电脑上非独立显卡能够使用后者来弥补少量或不存在数量的 VRAM。

以下是一些可能有帮助的提示：
* 任务管理器的 `Performance -> GPU 0` 选项卡可以显示 VRAM 使用情况和其他图形信息。
* 关闭浏览器等程序，甚至可能是 Discord 作为极端措施，然后重试。
* 在游戏打开时按住左 Shift 跳过加载模组。

## Texture2D 创建失败！错误代码：GPU 将不响应更多命令，很可能是因为调用应用程序传递了无效命令。(0x887A0006)
这与[上面](#texture2d-创建失败错误代码没有足够的内存资源来完成此操作-0x8007000e)相同。据报道，遵循[低 FPS 部分](#低-fps)中的说明使用 OpenGL 或 Vulkan 可以解决此问题。

## NoSuitableGraphicsDeviceException: 找不到 d3dcompiler_47.dll
如果您使用的是 Windows 7，您需要安装[微软的 directX 包](https://support.microsoft.com/en-us/topic/update-for-the-d3dcompiler-47-dll-component-on-windows-server-2012-windows-7-and-windows-server-2008-r2-769c6690-ed30-4dee-8bf8-dfa30e2f8088)。运行完安装程序后重启计算机。

## NoSuitableGraphicsDeviceException: 无法创建交换链！错误代码：参数不正确。(0x80070057)
这与您的显卡能力有关。您可以尝试更新视频驱动程序，或者可以尝试使用 OpenGL 或 Vulkan，方法是遵循[低 FPS 部分](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ#low-fps)中的说明。

## System.DllNotFoundException: 无法加载 DLL 'vcruntime140.dll'或其依赖项之一：找不到指定的模块。(0x8007007E)
如果您有这个问题，[Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53587) 下载站点应该会在您的网络浏览器中自动打开。点击 `Download` 下载安装程序并运行它来安装这些库。这应该可以解决您的问题，您可能需要重启计算机。如果这不能解决问题，[下载并安装此更新版本](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist?view=msvc-170#visual-studio-2015-2017-2019-and-2022)并重试。您需要点击 X64 下载。（[直接链接](https://aka.ms/vs/17/release/vc_redist.x64.exe)）

## System.BadImageFormatException: 尝试加载格式不正确的程序。(0x8007000B)
此问题的大多数报告已通过重新安装或修复[上面 vcruntime140.dll 部分](#systemdllnotfoundexception-无法加载-dll-vcruntime140dll或其依赖项之一找不到指定的模块-0x8007007e)中提到的 `Microsoft Visual C++ 2015 Redistributable Update 3` 安装来解决。（正在进行的解决方案，正在调查更多。）

## 资源的多个扩展名
这是由不兼容的资源包引起的。当前某些资源包与 tModLoader 不兼容。要解决此问题，请在文本编辑器中打开 `\Documents\My Games\Terraria\tModLoader\config.json`，找到 "ResourcePacks" 部分，然后将所有 `true` 改为 `false`，保存文件。现在 tModLoader 应该可以再次启动。此问题最终会得到修复，对[问题](https://github.com/tModLoader/tModLoader/issues/2913)添加 thumbs up 以优先解决此问题。

## System.Threading.SynchronizationLockException
![](https://i.imgur.com/IkPqCo6.png)
解决方案。禁用 BitDefender 或禁用 Bit Defender 的安全文件功能。更多相关信息已在此处收集[here](https://www.bitdefender.com/consumer/support/answer/2700/)。

## 启用模组时游戏冻结/设置控制不正确
这很可能与您的杀毒软件阻止对 Documents 目录中的 `tModLoader` 文件夹的访问有关。请参阅[下面问题](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ#systemunauthorizedaccessexception-access-to-the-path-is-denied)的进程（但使用文件夹而不是 exe）

## 磁盘写入错误
如果您尝试通过 Steam 安装 tModLoader 并收到包含"磁盘写入错误"的消息，这通常是由 Avast 引起的。暂时禁用它并安装 tModLoader。

## 受控文件夹访问
![](https://i.imgur.com/Zn40Ohq.png)

受控文件夹访问是 Windows 10+ 安全功能，旨在防止勒索软件。这是一个有用的功能，但它会妨碍 tModLoader 保存到文档文件夹中的游戏存档文件通常安装的位置。如果您启用了此功能，您可以为 tModLoader 添加例外以允许它工作。您也可以完全禁用该功能，但除非您知道自己在做什么，否则不要这样做。

<details><summary>添加例外</summary><blockquote>

为 tModLoader 添加例外最简单的方法是在错误上点击 `OK`，然后点击出现的通知：
* 如果通知在 5 秒内没有出现，您可以访问屏幕右下角的操作中心并在那里找到它。如果不在那里，请在开始菜单中搜索"受控文件夹访问"，点击它，然后点击"阻止历史记录"
![image](https://user-images.githubusercontent.com/4522492/176323144-fa85ff75-8759-414c-aeff-22ac0be0bf69.png)

点击第一个结果：
![image](https://user-images.githubusercontent.com/4522492/176323195-14185202-e1be-4cf8-a62e-08b2c9a84a96.png)

当被问到"您是否允许此应用对您的设备进行更改"时点击 `Yes`：
![image](https://user-images.githubusercontent.com/4522492/176323236-8f69f573-78be-4335-af93-5cd05cc51d56.png)

您现在应该会看到解释 "dotnet.exe" 被阻止访问[存档文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)的信息（存档文件夹通常为 `"My Games\Terraria\tModLoader"`）：
![image](https://user-images.githubusercontent.com/4522492/176323400-53b3235e-7be1-4e62-8733-2de967532eb2.png)

点击 `Actions` 然后 `Allow on Device`：
![image](https://user-images.githubusercontent.com/4522492/176323452-28e6c171-796e-49a9-87d1-77cbf1579733.png)

您将再次被问到"您是否允许此应用对您的设备进行更改"，点击 `Yes`：
![image](https://user-images.githubusercontent.com/4522492/176323236-8f69f573-78be-4335-af93-5cd05cc51d56.png)

tModLoader 现在应该能够创建所需的存档文件。再次启动游戏。

</blockquote></details>

如果您遵循了方向但仍然无法解决问题，最后的手段是告诉 tModLoader 保存到[安装目录](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)而不是 `My Documents` 文件夹。您可以通过在[安装目录](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)中创建 `savehere.txt` 文件来实现。请参阅下面[savehere.txt 存档位置变通方法](#saveheretxt-存档位置变通方法)说明。

## savehere.txt 存档位置变通方法

如果您由于 OneDrive 问题、受控文件夹访问问题或任何其他文件系统问题而无法启动游戏，有一个最终的变通方法选项，可以告诉 tModLoader 保存到[安装目录](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)而不是 `My Documents` 文件夹。

通过在 `My Documents` 中保存，可以避免所有由此产生的并发症，但这意味着如果您依赖该功能，OneDrive 将不会同步和备份您的文件。Steam 云存档仍然有效（玩家和世界），但模组配置和其他杂项文件将无法像使用 OneDrive 那样备份。

要执行 `savehere.txt 存档位置变通方法`，请在[安装目录](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)中创建一个 `savehere.txt` 文件。下面的视频显示了如何操作。

<details><summary>savehere.txt 视频说明</summary><blockquote>

https://github.com/tModLoader/tModLoader/assets/4522492/3c925905-0a4a-4a55-a664-6ddd8dddd995

</blockquote></details>

## System.UnauthorizedAccessException: 访问路径被拒绝。
![](https://i.imgur.com/ZjhIvNo.png)

此问题可能由您的杀毒软件或 Windows 安全设置引起。如果您使用的是 Windows 安全（原 Windows Defender）并收到此错误，则需要将 "dotnet.exe" 添加到白名单，有关如何执行此操作的进一步说明，请继续阅读。

首先，遵循上面[受控文件夹访问](#受控文件夹访问)部分的说明，如果不起作用，则遵循以下步骤。

<details><summary>将 "dotnet.exe" 添加到白名单</summary>

![Right-Click and open security dashboard.](https://i.imgur.com/2Lj2Wrx.png)
右键点击 Windows 安全系统托盘图标并选择"查看安全仪表板"

![Select "Virus & Threat protection"](https://i.imgur.com/Uc5a5WL.png)
左键点击"病毒和威胁防护"

![](https://i.imgur.com/OJNFSd5.png)
从那里，左键点击"管理设置"下的"病毒和威胁防护设置"

![Scroll down.](https://i.imgur.com/2nhsK3a.png)
向下滚动直到找到"受控文件夹访问"部分，然后左键点击"管理受控文件夹访问"

![](https://i.imgur.com/DnT3LLQ.png)
左键点击"允许应用通过受控文件夹访问"

![](https://i.imgur.com/Az03a4f.png)
![](https://i.imgur.com/WLovfFc.png)
左键点击"添加允许的应用"，然后选择"最近阻止的应用"

![image](https://github.com/tModLoader/tModLoader/assets/4522492/78d94913-578e-4fbb-94de-138720efef86)

滚动列表直到找到 "dotnet.exe"，然后点击 + 然后关闭，之后您就完成了！（如果您在列表中找不到 "dotnet.exe"，则继续以下步骤）

![image](https://github.com/tModLoader/tModLoader/assets/4522492/0335069e-4058-4400-9ca9-0fdc61b50a14)

在"添加允许的应用"选择中，左键点击"浏览所有应用"

![image](https://github.com/tModLoader/tModLoader/assets/4522492/e2e5a8b0-738e-456f-af52-2e839979adef)

导航到[tModLoader 安装文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#install)，然后导航到并选择 "dotnet/dotnet.exe"，然后按 `Open`。这会将文件添加到您的白名单。完成了！

</details>

## 在 Mac 上无法加载共享库 libSDL2-2.0.0.dylib 或其依赖项之一
据报道，这对于手动下载 tModLoader zip 安装的 GOG 用户来说是个问题。在 Safari 上下载文件时，该文件会被操作系统标记为来自互联网。用户可以使用 `xarrt -d com.apple.quarantine` 命令来清除提到文件的标志。如果您对此特定修复有更多信息，请更新此部分。

## busybox64.exe 应用程序无法正确启动 (0xc0000142)
据报道，这可以通过重新安装或修复[上面 vcruntime140.dll 部分](#systemdllnotfoundexception-无法加载-dll-vcruntime140dll或其依赖项之一找不到指定的模块-0x8007007e)中提到的 `Microsoft Visual C++ 2015 Redistributable Update 3` 安装来解决。

## 没有日志文件夹
如果您遇到启动问题且找不到日志文件夹，据报道某些杀毒软件（如 Comodo）会导致日志文件夹无法生成。此问题与"SteamAPI.Init failed. Try logging out of Steam and restarting it."错误一起报告。另一种可能性是您有 2 个 tModLoader 安装文件夹而您在错误的地方寻找[日志文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#logs)。

# 音频问题
### IAudioClient 解决方法
尝试 [IAudioClient 解决方法](https://github.com/tModLoader/tModLoader/issues/2863#issuecomment-1221975856)。

## 音频故障排除
音频问题特别难以识别，如果您的问题不在此列表中，请尝试这些故障排除步骤。对于这些中的每一个，请确保在尝试这些更改时关闭 tModLoader：

**全新安装**
手动安装的 wave bank 可能导致问题，遵循[全新安装](#全新安装)和[全新安装泰拉瑞亚](#全新安装泰拉瑞亚)步骤将确保这些文件被删除。您可能不记得手动安装了 wavebank，但安装 `tModLoader 64 bit` 等其他操作可能会附带执行此操作。

**禁用所有资源包**
请遵循[禁用所有资源包](#禁用所有资源包)说明。

**更改音频设备**
例如，如果您使用的是耳机，请切换到扬声器。如果您没有扬声器，请尝试插入一些。尝试启动 tModLoader。

**检查音频输出速率**
尝试将音频输出速率更改为小于或等于 48000 Hz。最常见的输出速率是 44100 Hz 和 48000 Hz。高于 48000 Hz 的速率可能导致在启用某些模组时崩溃。为此，右键点击通知区域中的扬声器图标，点击"声音"，导航到"播放"选项卡，点击您当前的播放设备并点击属性，导航到"高级"选项卡，然后在"默认格式"部分调整输出速率。

**Voicemeeter**
如果您安装了 Voicemeeter 并看到"Failed to initialize audio client" 或 "WASAPI" 错误，请尝试将 A1-A5 更改为不同的设备。即使您将立体声输入更改为不同的设备，如果默认 A1 有问题，仍然会导致设备问题。

**拔掉控制器**
一些控制器内置扬声器，可能会导致我们的音频播放代码出现问题，请尝试拔掉它们然后启动 tModLoader。

**重置 config.json**
请遵循[重置 config.json](#重置-configjson)说明。

**尝试 IAudioClient 解决方法**
Windows：尝试 [IAudioClient 解决方法](#iaudioclient-解决方法)。

**Mac 无音频硬件 - 尝试用 vanilla Terraria 文件替换 Natives**
将 `Native/OSX` 中找到的所有文件替换为 vanilla 副本（至少 libFAudio、libSDL2、libSDL2-2.0.0）
从 `~/Library/Application Support/Steam/steamapps/common/Terraria/Terraria.app/Contents/MacOS/osx`
到 `~/Library/Application Support/Steam/steamapps/common/tModLoader/Libraries/Native/OSX`

**将音量设置为 0**
如果音频问题阻止游戏启动，请打开[存档文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)并找到 `config.json`，在文本编辑器中打开该文件。找到"VolumeMusic"、"VolumeAmbient"和"VolumeSound"。对于每一个，将数字更改为 `0`。保存文件。如果这有效，问题与音频相关，本指南中的其他步骤可能能够恢复音频，以便您可以再次调高音量。

# 多人游戏

## 一般多人游戏故障排除步骤
在在此处寻求支持之前，首先确保泰拉瑞亚本身的多人游戏正常工作。如果泰拉瑞亚多人游戏无法工作，您需要首先对该问题进行故障排除。官方泰拉瑞亚 wiki 上有 [LAN/Internet](https://terraria.wiki.gg/wiki/Guide:Setting_up_a_Terraria_server) 和 [Steam 邀请](https://terraria.wiki.gg/wiki/Guide:Setting_up_Steam_Multiplayer)多人游戏的指南，其中涵盖了端口转发、IP 地址以及其他必要步骤和术语。本 FAQ 仅列出特定于 tModLoader 的多人游戏问题，并期望任何常规网络问题已经解决。

此外，如果您正在寻找有关如何设置多人游戏服务器的信息，请阅读[本指南](https://github.com/tModLoader/tModLoader/blob/stable/patches/tModLoader/Terraria/release_extras/DedicatedServerUtils/README.md)。

最后，[调试多人游戏使用问题 wiki 页面](https://github.com/tModLoader/tModLoader/wiki/Debugging-Multiplayer-Usage-Issues)有大多数特定于多人游戏的说明，请先阅读该页面，然后如果您的问题仍未解决，请回到这里。

## 延迟
TODO：禁用模组，确认问题来自哪里。

* 一位用户报告说他们的杀毒软件"360 Security Guard"导致了极端延迟。他们禁用了它来修复延迟。

# 无响应
当游戏停止响应时，表明游戏逻辑陷入无限循环。这种问题可能非常难以诊断。有能力的程序员可以使用 minidump 文件来调查问题的原因。如果问题出在模组中，希望该模组制作者会修复他们的模组，如果是 tModLoader 中的问题，我们可以努力修复它。

## Minidump 说明
如果您的 tModLoader 停止响应并变白，您可以为我们提供 minidump 文件，这将帮助我们调试问题。
![unknown (2)](https://user-images.githubusercontent.com/4522492/179609389-3eafa688-1039-4448-a35d-c1aef6f3d037.png)

<details><summary>展开查看 Minidump 说明</summary><blockquote>

为此，下载 https://download.sysinternals.com/files/Procdump.zip 并提取 zip。在该文件夹中通过在文件资源管理器地址栏中输入 `cmd` 然后按回车来打开命令提示符。
![unknown (3)](https://user-images.githubusercontent.com/4522492/179609489-f7115dfc-1c27-4ec3-a945-1e1c5aa46f44.png)
这将打开命令提示符：
![unknown (4)](https://user-images.githubusercontent.com/4522492/179609511-d507817b-8734-4db2-9b1e-77f71342758c.png)
接下来，通过按 ctrl-shift-escape 打开任务管理器。点击 Details，然后向下滚动到 Not Responding 的 dotnet.exe：
![unknown (5)](https://user-images.githubusercontent.com/4522492/179609519-92cdfca1-0791-46ff-9bc2-9ae11d82325e.png)
记下 PID。在这个图像中，它是 20680，但您的会不同。回到命令提示符并输入 `procdump.exe -mm 20680`，只是将 20680 更改为您的数字。几秒钟后它就完成了：
![unknown (6)](https://user-images.githubusercontent.com/4522492/179609528-13ab0f1e-eec9-4606-8f05-5f4966aa79eb.png)
回到文件资源管理器，您可以看到 .dmp 文件：
![unknown (7)](https://user-images.githubusercontent.com/4522492/179609674-8ac1b5f4-27fd-48d6-a42d-1aad83e75de6.png)
找到一种将此文件上传到 Discord 支持频道的方法。我们有 nitro boosts，所以您应该能够直接将文件拖放到支持帖子中。

</blockquote></details>

## 无法点击任何内容
如果您发现自己无法点击任何内容，您的 `input profiles.json` 可能已损坏。请遵循[重置 config.json](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ#reset-configjson)中与 `input profiles.json` 相关的步骤。

# 加载模组
### "尝试打开 tModLoader 时某个模组崩溃"
您可以通过在 tModLoader 加载时按住 shift 来跳过加载模组，直到它到达主菜单。访问 `Workshop->Mods` 菜单来禁用或删除该模组。如果您需要直接删除模组，您可以在[工作室](https://steamcommunity.com/app/1281930/workshop/)上取消订阅。如果您需要删除手动安装的模组，请打开 tModLoader [模组文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#mods)并删除有问题的 .tmod 文件。

### 在成功调用 End 之前无法再次调用 Begin
（也适用于"无法访问已释放对象"错误）
![](https://i.imgur.com/jzbyghT.png)
此错误通常是由模组中未处理的错误引起的。这使用户很难知道哪个模组坏了很多时候，此错误只会在重新加载模组后发生（不正确地卸载 Texture2D 引用），但也可能由于其他错误而发生（例如，除以零。）作为模组用户，可能很难找出哪个模组导致了问题。要确定损坏的模组，请遵循[阅读 client.log](#阅读-clientlog)中的步骤。导致此崩溃的错误模组应该在最后一个或倒数第二个条目中。如果您在这些错误之一中找到提到的模组，那可能是导致错误的模组。如果有疑问，请在[tModLoader discord](https://discord.gg/tmodloader)上的 #support-forum 中询问。作为最后的手段，请使用[流程图](#流程图)。

### OutOfMemoryException
此错误意味着 tModLoader 没有足够的 RAM 来加载您尝试加载的所有模组。添加大量物品的大型模组是主要罪魁祸首。您可能必须减少同时加载的大型模组数量。您也可以尝试加载小或中世界而不是大世界。另一种可能性是您正在运行其他大型程序。如果可以关闭它们，请执行此操作。按 `Ctrl+Shift+Escape` 调出任务管理器。在任务管理器的"进程"选项卡中，寻找占用大量内存的进程。任何占用超过 100,000 K 的都是很好的候选。

tModLoader 将 6 GB RAM 列为最低系统要求，但该最低要求会根据启用的模组而增加。

查看使用大量内存的模组：
如果您好奇哪些模组正在使用您有限的 RAM，您可以启用 "Show Mod Memory Estimates" 选项在 `Settings->tModLoader Settings` 中。启用设置后，您必须退出到主菜单，然后关闭并重新打开游戏以使设置生效。访问 Mods 菜单，确保启用并重新加载您好奇的模组，您现在应该会看到顶部的彩色图表，显示每个模组正在使用多少内存。使用此信息来禁用与您喜欢该内容的程度相比占用太多 RAM 的模组。

![](https://i.imgur.com/3Pl6tG2.png)

# 玩家/世界
### 加载失败！
当世界加载失败时，通常是由有错误的模组引起的。要测试此问题，请禁用所有模组并查看世界是否加载。如果可以工作，则一次添加几个模组并尝试加载世界。最终世界将再次无法加载，然后您可以重新加载模组以隔离导致世界无法加载的模组。一旦弄清楚是哪个模组导致世界无法正常加载，您将需要从模组创建者那里寻求帮助，请参阅[模组问题](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-FAQ#mod-issues)以获取如何找到特定于模组的帮助的信息。

### 玩家或世界上的"(LaterVersion)"
![dotnet_2022-10-05_11-04-59](https://user-images.githubusercontent.com/4522492/194119673-44d0ad77-613b-4e6e-8198-20213d2b45c4.png)
这意味着您已将玩家或世界从比您版本的 tModLoader 构建的泰拉瑞亚版本更新的泰拉瑞亚版本迁移过来。当泰拉瑞亚更新时，tModLoader 需要一段时间来更新，您必须等待 tModLoader 更新才能使用该玩家或世界。

### 玩家上的"(Unknown error)"
![unknown (11)](https://user-images.githubusercontent.com/4522492/194117229-e0345d6f-7852-4f5d-916f-7a3a0e01b4d3.png)
这意味着 tModLoader 出于某种原因无法加载玩家。我们一直在研究此问题的原因。要解决此问题，您需要[从备份恢复玩家](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#world-and-player-backups)。如果您注意到您的玩家在恢复备份后始终损坏，则可能是模组损坏。您可以尝试[获取支持](#获取支持)，有人可能能够诊断问题。

### "救命，我所有的玩家和世界都不见了！"
tModLoader 存档与 vanilla 泰拉瑞亚存档保持分开。您可以在存档位置之间来回复制，但请注意，如果您使用 vanilla 中的 tModloader 世界/角色，您将丢失模组化的瓷砖和物品。

解决方案：要从泰拉瑞亚复制到 tModLoader，请使用 tModLoader 玩家和世界选择菜单中的 `Migrate individual players...` 按钮。请注意，如果 tModLoader 当前未与泰拉瑞亚同步更新，玩家和世界将太新而无法打开。您还需要注意，存储在云上的文件不会显示，您需要先从云上获取文件，请参阅下面。
![](https://i.imgur.com/0CJ9FKM.png)

要将玩家或世界从 tModLoader 迁移到泰拉瑞亚，您需要从[tModLoader 存档](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)文件夹复制到泰拉瑞亚存档文件夹。（泰拉瑞亚存档文件夹应该是 tModLoader 文件夹的父文件夹。）例如，将 `\Terraria\Worlds` 中的 .wld 文件复制到 `\Terrra\tModLoader\Worlds`，玩家和世界的 .plr 文件也是如此，对应的 `Players` 文件夹中也是如此。对于玩家，您还需要获取同名的文件夹，因为那些是地图。

<details><summary>从云获取玩家或世界</summary><blockquote>

您可能会注意到您的玩家或世界不在文件夹中，或者可能只有 .bak 文件在文件夹中。这意味着您已将该玩家或世界放到了云上。有两种方法可以获取文件。第一个选项是打开泰拉瑞亚或 tModLoader，然后只需点击 `Move off cloud` 按钮，然后按照上面的说明操作。第二个选项是从本地云文件副本中复制文件，steam 会保留这些文件并将它们放到各自的文件夹中。这些位于[本地云存档文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#cloud)</blockquote>

</details>

### "云存储限制已达到，无法移动到云"
tModLoader 与泰拉瑞亚共享云存储空间（总配额约为 950 MB）。超过泰拉瑞亚和 tModLoader 合计的此限制将使您无法将玩家和世界移动到云，直到有足够的存储空间可用。最简单的腾出空间的方法是"取消云化"世界，它们占用最多空间。您可以在 Steam 中检查使用了多少存储：右键 `tModLoader` -> `Properties`，在 `Steam Cloud` 部分下方显示可用存储量。在极少数情况下，如果您在 steam 发布前使用了 tModLoader 云存储功能（0.11.7），您将无法以正常方式摆脱这些"孤立"文件。尝试此方法：[视频，泰拉瑞亚 App ID 是 105600](https://youtu.be/JADsIv2RUSw)。删除孤立文件的另一种方法是下载 [Steam Cloud File Manager Lite](https://github.com/GMMan/SteamCloudFileManagerLite) 并使用它来删除"ModLoader"文件夹中的文件。

# 模组浏览器
### "模组浏览器离线"、"我无法下载模组"
![](https://i.imgur.com/JTtOMbq.png)

Steam 工作室有时会因维护而离线，请几小时或明天再试。

### 无法下载模组，模组下载进度条停留在 0.0 字节
造成此错误的原因有很多，但有一些您可以尝试的事情。首先，打开 `workshop_log.txt` 文件。该文件通常位于：`C:/Program Files (x86)/Steam/logs/workshop_log.txt`（Windows）、`~/Library/Application Support/Steam/logs/workshop_log.txt`（Mac）、`/home/user/.local/share/Steam/logs/workshop_log.txt`（Linux）。打开文件后，向下滚动到底部以查看最新的 steam 日志消息。您可能会看到与无法下载的模组相关的错误。如果可能，尝试理解错误并根据其含义采取行动。例如，它可能提到您硬盘空间不足，在这种情况下请清理一些文件或将 tModLoader 安装位置移动。

另一件可以尝试的事情是修复 Steam 库文件夹。为此，请导航到 `Steam->Settings->Storage`，然后从下拉菜单中选择 tModLoader 安装到的库位置。接下来，点击 `...` 图标并选择 `Repair Library`。此过程将尝试修复 steam 库文件夹中的任何权限或其他问题，应该需要一分钟左右。

一些用户报告说，当 tModLoader 安装在外置硬盘甚至默认驱动器以外的硬盘上时会出现问题。如果您处于这种情况，请尝试将 tModLoader 移动到主硬盘驱动器。为此，请在 Steam 库中右键点击 tModLoader 并选择 `Properties`。点击 `Installed Files`，然后 `Move install folder`，然后选择不同的位置。

### 无法重新下载模组
如果您发现在游戏中无法重新下载模组，您需要关闭游戏，然后访问[tModLoader 工作室](https://steamcommunity.com/app/1281930/workshop/)。在那里，找到您感兴趣的模组并订阅。如果您已经订阅，请取消订阅然后再次订阅。最后，通过完全关闭（Steam->Exit）来重启 Steam，然后再次启动它。您可能还需要遵循[验证游戏完整性](#验证游戏完整性)步骤来强制 steam 重新下载您订阅的模组。

如果前面的步骤没有解决，以下可能：
如果您得到 **Item is/was already installed** 消息和/或模组名称旁边的红色 `!`，如果 TML 提供的说明不起作用，请执行以下操作：
1) 首先在 Mods 菜单中删除模组。
2) 关闭游戏
3) 在 Steam 中取消订阅，订阅，取消订阅
4) 完全关闭 Steam
5) 在 SteamApps/Workshop 中删除 appworkshop_1281930.acf
6) 启动 steam
7) 启动游戏

# 保存数据文件问题
### Windows 10+ OneDrive
默认情况下，Microsoft 已将 OneDrive 强制到"我的文档"文件夹，这可能会在配置不当时弄乱某些游戏。

错误消息：云文件提供程序未运行
![252088854-2e17cd93-a44a-43f6-a169-c045d2c7f094](https://github.com/tModLoader/tModLoader/assets/4522492/add71bff-51ac-41d1-b2dd-bee57f9b68db)

也适用于错误消息：System.IO.IOException: 云操作不成功
![image](https://github.com/user-attachments/assets/5336c3d6-8e17-4779-8c0d-0d8e84937569)

也适用于错误消息：System.IO.FileNotFoundException: 找不到文件 ...tModLoader\Mods
![image](https://github.com/user-attachments/assets/68001138-6894-4182-9c69-0a182d45c1e7)

基本上，这些错误意味着游戏无法保存文件，因为 OneDrive 要么配置错误要么离线。解决方案通常是修复 OneDrive，但也可以卸载 OneDrive。请注意，禁用/卸载 OneDrive 意味着默认启用的自动备份和文件同步将不再处于活动状态。这是一个激烈的选择，不推荐，除非您知道自己在做什么。如果启用 OneDrive 无法解决您的问题且您不想卸载 OneDrive，[savehere.txt 存档位置变通方法](#saveheretxt-存档位置变通方法)是让游戏工作的另一个选择。

<details>
<Summary>解决方案 1：卸载 OneDrive。</Summary>

**注意：** 仅在您实际想要从计算机中删除 OneDrive 时才遵循这些说明。

* 在 Windows 搜索栏上，搜索程序，然后选择"添加或删除程序"
* 卸载 OneDrive。
* 问题解决了。

</details>

<details>
<summary>解决方案 2：使 OneDrive 正常运行</summary>

* 遵循[在 Windows 中将文件与 OneDrive 同步](https://support.microsoft.com/en-gb/office/sync-files-with-onedrive-in-windows-615391c4-2bd3-4aae-a42a-858262e42a49)中的说明以打开 OneDrive。
* 然后在管理访问菜单中关闭"文档"同步
![image](https://github.com/tModLoader/tModLoader/assets/59670736/bc96bafd-4f03-450a-9797-5fd322e8a4bf)
* 和/或开启"始终保留在此设备上"：
![image](https://github.com/tModLoader/tModLoader/assets/59670736/e2a24cec-5ec2-4a7b-adf5-cf638c720b5d)

</details>

### 无法找到我的 tModLoader 1.4.3 保存数据或自动迁移文件失败。
对于计算机上的文件，请执行此操作，参考从"default"复制到 1.4.3 的示例：
![image](https://github.com/tModLoader/tModLoader/assets/59670736/aced141b-3e5e-4df1-aff3-b6e8f097fa52)

对于 Steam 云相关文件，不幸的是，没有快速修复。
请根据 https://www.howtogeek.com/428491/how-to-download-your-save-games-from-steam-cloud/ 手动下载您的文件

### Config.json 损坏
### 尝试从 X 移植到 Y 中止，Z 文件已损坏。
![dotnet_2024-03-11_13-36-22](https://github.com/tModLoader/tModLoader/assets/4522492/8dab3fe7-fb53-4d43-96a8-0af4c0747156)

您基本上有一个损坏的 `config.json` 文件，这会导致 tModLoader 无法知道上次启动的是哪个版本的 tModLoader。

如果您是 tModLoader 的新手或玩过 tModLoader 1.4.4（自 2023 年 8 月以来的任何时间），只需删除[存档文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)中的 `config.json` 文件。

否则，如果您知道上次玩的 tModLoader 是 1.4.3 版本，请用这里提供的替换[存档文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)中的 `config.json`：
[config.zip](https://github.com/tModLoader/tModLoader/files/11992590/config.zip)。

之后，重启游戏。如果您仍然收到相同的错误，则说明您删除或替换了错误的文件。仔细检查文件夹路径，确保您修改的是错误消息中提到的正确的 `config.json` 文件，而不是 Terraria 文件夹中找到的那个。

文件位置示例
![image](https://github.com/tModLoader/tModLoader/assets/4522492/59670736/dbe1c30b-09eb-4776-a5a1-943984735d4f)

## 迁移失败
当 tModLoader 更新到新的主要版本时，有时玩家存档会从一个文件夹移动到另一个文件夹，以确保顺利过渡到下一个主要版本的 tModLoader。有时此过程会因多种原因之一而失败。在这种情况下，由用户移动文件或修复问题并重试。

首次启动 1.4.4 时，tModLoader 会将文件从 `tModLoader`（`源文件夹`）移动到 `tModLoader-1.4.3`（`目标文件夹`），保留 1.4.3 存档。然后 tModLoader 会将文件从 `tModLoader-preview`（`源文件夹`）移动到 `tModLoader`（`目标文件夹`），允许以前在 1.4.4 preview 版本上的用户继续在稳定版 1.4.4 上使用他们的玩家和世界。

### 重试
最常见的问题与 OneDrive 相关。如果您的 `My Documents` 文件夹在 OneDrive 中，请遵循[Windows 10+ Onedrive](#windows-10-onedrive)部分中的步骤以确保 OneDrive 正确设置和运行。之后，如果一切正常，请再次启动 tModLoader，这次迁移应该会成功。

### 手动移植
如果 tModLoader 仍然无法迁移文件，用户可以手动完成。首先，创建 `目标文件夹`，这将是 `tModLoader-1.4.3` 或 `tModLoader` [存档文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)，具体取决于错误消息中显示的文件路径。接下来，将 `源文件夹` 存档文件夹中的文件和文件夹复制到 `目标文件夹`。可以通过检查[迁移失败](#迁移失败)部分来确定应将哪个 `源文件夹` 复制到您刚创建的 `目标文件夹`。如果这失败了，您可能有一些需要删除的损坏文件。

### 非法文件名
很少见，tModLoader 存档文件夹可能包含无法以正常方式删除或移动的文件或文件夹。首先，重启计算机并重试。如果文件或文件夹仍然无法移动或删除，则它可能具有非法文件名。非法文件名的例子是以 `.` 结尾的文件或以空格结尾的文件夹。由于模组或 tModLoader 中的错误，这些文件很少会被创建。[stackoverflow 帖子的答案](https://stackoverflow.com/questions/4075753/how-to-delete-a-folder-that-name-ended-with-a-dot)中的说明都是删除这些文件和文件夹的选项。如果问题是 Players 文件夹中的文件夹以空格结尾，请借此机会将相应的 `.plr` 和 `.tplr` 文件重命名以删除其中的空格。如果您在处理此问题时需要帮助，请随时来到[我们的 Discord 支持](#discord-支持)。

# Linux / Steam Deck / Mac
tModLoader 旨在作为原生 Linux 应用程序运行。作为一个原生程序，它功能完整，因此不需要 Proton。
不幸的是，由于未知原因，tModLoader 无法与 Proton 配合使用。

### 兼容性选项
这是少数需要将兼容性选项设置为"无"的应用程序之一。
STEAM DECK：如果您没有看到无选项，您可以安装"Steam Play None"来强制添加选项。

### 无法启动原生构建 - 点击 Steam 开始但没有任何反应
如果本 FAQ 中删除 "dotnet" 文件夹的部分不能解决问题，据报道以下可能有用，通过 #3612：
从 nixpkgs 安装 `dotnet-sdk`，并创建从存储二进制文件到 TML 安装的符号链接。例如：`ln -s /run/current-system/sw/bin/dotnet /home/<user>/.local/share/Steam/steamapps/common/tModLoader/dotnet/dotnet`

### Flatpak、Steam Deck Steam 及相关沙盒应用程序
如果您想开发模组，这些会与之冲突。
您将需要在 Steam 外部启动游戏，使用"非 Steam 游戏"库选项或从终端启动。

<details><summary>在 Steam Deck 上构建模组</summary><blockquote>

由于 Steam Deck 的设置方式，构建模组的设置需要一点额外的努力。您需要按照以下说明将 dotnet 8 SDK 安装到您的系统。这些说明与典型的 Linux dotnet SDK 安装说明不同。这些说明已经过轻度测试，并因我们更多地了解如何正确使用 Steam Deck 而有待更新。Steam Deck OS 更新可能会潜在地恢复此安装，如果模组构建不再工作，则需要在 OS 更新后重新访问这些说明。

**通过 dotnet-install 脚本安装 dotnet SDK：**

注意：此方法会将 `dotnet 8.0 SDK` 安装在用户的主目录中。此方法应该会持续到 Steam Deck OS 更新，但它可能不会。

1. 确保您最近在此 Steam Deck 上启动过一次 tModLoader。
2. 在 Steam Deck 上，切换到桌面模式（Steam->Power->Switch to Desktop）。您现在应该处于桌面环境中。插入 USB 键盘现在将在接下来的步骤中提供帮助。
3. 打开 Steam 并右键点击 tModLoader，然后选择"管理"，然后"浏览本地文件"。这将在文件管理器中打开 tModLoader 安装文件夹。
![Screenshot_20231116_130439](https://github.com/tModLoader/tModLoader/assets/4522492/ac152bb9-0660-4324-9410-ebfa043cba06)
4. 导航到 `LaunchUtils` 文件夹。右键点击文件管理器中的任意位置，然后点击"在此打开终端"。这将打开 `Konsole`，默认终端程序。
![Screenshot_20231116_130644](https://github.com/tModLoader/tModLoader/assets/4522492/8d8ee052-f5df-4675-94a4-52c1f2877d47)
5. 接下来，我们将在系统上安装 `dotnet`。通过在我们刚刚打开的终端中运行 `./dotnet-install.sh -channel 8.0` 命令来执行此操作。这应该运行一会儿，然后显示"Installation finished successfully"。
![Screenshot_20231116_130858](https://github.com/tModLoader/tModLoader/assets/4522492/911672a0-7130-4aa8-b20f-08ad5ff19eca)
6. 安装了 `dotnet` 后，tModLoader 还无法访问它。为此，我们需要确保它被添加到我们的 `PATH`。我们通过编辑 bash 配置文件来实现。在文本编辑器中打开 `/home/deck/.bashrc`（默认情况下，这是一个名为 `Kate` 的程序），然后将以下 2 行添加到文件中间并保存文件：
    > export DOTNET_ROOT=$HOME/.dotnet
    > export PATH=$PATH:$DOTNET_ROOT:$DOTNET_ROOT/tools
    ![Screenshot_20231120_181643](https://github.com/tModLoader/tModLoader/assets/4522492/24505bf3-e50b-45de-b01a-cc2a596a233c)

    如果您找不到 `.bashrc` 文件，请打开 Dolphin 文件管理器并点击 `Home`。`Home` 与 `/home/deck/` 相同。接下来，查找以 `.` 开头的灰色文件，这些都是隐藏文件。如果没有，您需要在右上角点击选项下拉菜单并勾选`显示隐藏文件`。您现在应该会看到隐藏文件。
7. 接下来访问 tModLoader 的 Steam 属性。为此，在 Steam 中右键点击 tModLoader，选择"Properties"，然后找到"Launch Options"框。在"General"选项卡中。键入以下启动选项并关闭窗口：`DOTNET_ROOT=/home/deck/.dotnet %command%`（此步骤仅在为游戏模式支持 tModPorter 时是必要的，如果需要可以跳过。在 2025.01 及更高版本中这是不必要的。）
![20231120143425_1](https://github.com/tModLoader/tModLoader/assets/4522492/ee0fe72e-dc38-4593-9bcc-95c10b0998d2)
8. 启动游戏并访问 `Develop Mods` 菜单，您现在应该会看到模组列表（如果有的话），而不是提到安装 `dotnet SDK` 的说明。

此方法应该允许您在游戏模式、桌面模式下进行模组，甚至可以通过 `start-tModLoader.sh` 手动启动。

**通过 pacman 安装 dotnet SDK：**

为完整起见，还应该提到，理论上可以通过 `pacman` 在系统级别安装 dotnet SDK。此方法需要禁用操作系统的只读性质。这有点危险。此外，`pacman` 安装会导致不兼容的 SDK 版本，无法构建模组。这些步骤未在此处列出，因为它们目前不会产生可行的解决方案。

</blockquote></details>

### Arm64 Linux 解决方法
（此解决方法是 1.4.3 独有的，在 1.4.4 上不起作用）目前，tModLoader 使用的 Steamworks.NET 库在其托管代码中不支持 ARM。
请使用 [MikolajKolek](https://github.com/MikolajKolek) 的[此 fork](https://github.com/MikolajKolek/Steamworks.NET-arm64/releases/tag/20.1.0) 上的 20.1.0 Release build 下载替代 Steamworks.NET.dll。
请通过替换 Libraries/Steamworks.net/20.1.0/lib/netstandard2.1 中的现有 .dll 来安装 ARM64 Linux Steamworks.NET.dll

### 找不到系统 .NET SDK，没有沙盒
如果应用程序是沙盒化的，请参阅此处的适当部分。
如果没有，请尝试以下步骤：
1) 在终端中检查 "which dotnet" 命令结果。您应该会看到有关系统 dotnet 安装的信息，以及 "/my/path/dotnet" 的路径
2) 在路径 /etc/paths.d/dotnet 处创建内容为 "/my/path/dotnet" 的文本文件
再次尝试运行。
如果这不起作用，请在 Discord 上寻求进一步指导

# 其他问题
### Steam 游戏录制
[Steam 游戏录制](https://store.steampowered.com/gamerecording) 是一种无法开箱即用地与 tModLoader 配合使用的功能，因为它具有独特的启动顺序。作为解决方法，您可以使用"手动录制"设置以及开始/停止录制快捷键。关注[此问题](https://github.com/tModLoader/tModLoader/issues/4324)以获取任何进展。

### RGB 键盘错误 (端口 53664)
针对端口 `53664` 的网络问题（`SocketExceptionFactory`、`WebException`、`HttpRequestException`）是由 RGB 键盘支持错误引起的。您可以尝试修复键盘软件安装或禁用该功能。即使您认为自己没有 RGB 键盘，这也可以提供帮助。关闭 tModLoader。打开[存档文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)中的 `config.json`，在文本编辑器中找到它。找到"UseRazerRGB"、"UseCorsairRGB"、"UseLogitechRGB"和"UseSteelSeriesRGB"条目，并将每个的"true"改为"false"，然后保存文件，最后尝试打开 tModLoader。

### 低 FPS
您应该尝试很多事情：
1. 首先，确保您的模组没有在日志中抛出错误。请参阅[阅读 client.log](#阅读-clientlog)以了解如何检查模组的错误消息。
2. 第二，检查游戏是否在您的独立显卡上运行。由于某种原因，Windows 喜欢尝试在集成 GPU 上运行 tModLoader。
3. 接下来，确认即使启用 0 个模组也会发生问题。
4. 尝试更改"帧跳过"设置
5. 您可以尝试不同的图形选项。为此，关闭游戏，然后在 Steam 中右键点击 `tModLoader`，选择 `Properties`，然后找到 `Launch Options` 框。在框中输入 `/gldevice:OpenGL` 或 `/gldevice:Vulkan` 并关闭窗口。启动游戏并检查帧率。做同样的事情尝试另一个选项。如果这些没有效果，以相同的方式删除它们。之前 OpenGL 工作正常的问题？尝试删除它，看看默认设置现在是否更好。
![image](https://user-images.githubusercontent.com/4522492/194379953-0392ca08-8ac3-4610-8486-595620e3d1d7.png)
    1. 如果您使用的是游戏 GOG 版本，您可以编辑 `start-tModLoader.bat` 并将第 3 行更改为 `LaunchUtils/busybox-sh.bat ./LaunchUtils/ScriptCaller.sh /gldevice:Vulkan %*`，保存，然后通过启动文件进行测试。
6. 如果使用 Steam，您可以尝试以下操作：
    * 在好友列表设置中禁用动画头像
    * 在游戏内设置中禁用 steam 覆盖
    * 以"紧凑模式"启动 Steam（[教程](https://techverse.net/how-to-enable-small-mode-on-steam/))
7. 尝试主机和播放而不是单人游戏。
8. 尝试照明改造模组，看看是否能更好地利用您的计算机资源。
9. 尝试来自 Terraria Workshop 的 Optimizerarria 资源包
10. 尝试降低图形：禁用波浪、禁用背景、质量低
11. 如果使用复古或迷幻照明模式，尝试从 100% 放大到 102% 左右。在 100% 缩放时，复古和迷幻照明模式由于代码组织方式反而需要做更多工作，任何大于 100% 的值都可以。
12. 来到 [tModLoader Discord 支持](#discord-支持)。

### 幽灵鼠标
如果您的鼠标不可见但仍突出显示按钮，您可能遇到了"Monect Virtual Controller"的问题。如果您的 `client.log` 中有"Monect Virtual Controller"，请遵循[这些步骤](https://community.monect.com/d/39-fuk-monect-hid-device)来卸载它。据报道，插入单独的控制器也可以解决这个问题（如果您没有"Monect Virtual Controller"）。

另一种解决方案是关闭游戏，然后在文本编辑器中编辑[存档文件夹](https://github.com/tModLoader/tModLoader/wiki/Basic-tModLoader-Usage-Guide#saves)中的 `config.json`，并将"InvisibleCursorForGamepad"和"SettingBlockGamepadsEntirely"设置更改为 `false`，然后保存文件。

### 游戏无法启动，Webroot 杀毒软件
Webroot 杀毒软件对 tModLoader 中包含的 `Mono.Cecil.Rocks.dll` 文件有误报，您需要将其从 Webroot 杀毒软件隔离区中删除。

### 键盘延迟
据报道，通过在 Norton 杀毒软件中禁用"游戏优化"来修复。

### Windows 11 上控制台保持打开状态
如果控制台在您到达主菜单后仍保持打开状态：打开 Windows 设置 > 系统 > 高级 > 终端 > 将其设置为 Windows Console Host。

### 没有小地图
当游戏检测到与小地图相关的错误时会禁用小地图。这可能是由抛出错误的模组引起的。要恢复此功能，请转到 `Settings->General` 并将地图选项切换为 `Map Enabled`。如果问题经常发生，检查您的 `client.log` 并尝试找出是哪个模组导致错误可能值得。
