## 目录
* [许可证入门](#许可证入门)
* [关于版权](#关于版权)
* [关于商标和专利](#关于商标和专利)
* [关于tModLoader的许可证](#关于tmodloader的许可证)
* [如何为您的模组添加许可证](#如何为您的模组添加许可证)
* [不使用许可证（"无许可证"）或放弃版权](#不使用许可证无许可证或放弃版权)
* [版权侵权](#版权侵权)
* [来源](#来源)

# 许可证入门
当您创作一个作品（包括代码）时，该作品默认受独家版权保护。
除非您包含指定其他内容的许可证，否则其他人在不承担删除、敲诈或诉讼风险的情况下，不能使用、复制、分发或修改该作品。
一旦作品有了其他贡献者（每个都是版权持有者），"没有人"就开始包括您自己。
即使没有许可证文件，在某些情况下您也可能授予某些权利，当您在发布源代码的网站上接受服务条款时。
例如，如果您在GitHub上的公共仓库中发布源代码（如tModLoader），您已接受服务条款，允许其他人查看和分叉您的仓库。（[阅读更多](https://help.github.com/en/articles/licensing-a-repository#disclaimer)）
在其他人的特殊情况符合版权限制和例外时，他们可能不需要您的许可。
网站条款或特定司法管辖区的版权限制都不足以满足人们在公共代码托管上通常寻求的各种协作，例如开源许可证所促进的实验、修改和共享。许可证的简明定义是"许可人承诺不起诉被许可人"。这意味着如果没有许可证，任何第三方对知识产权的任何使用或开发都将构成复制或侵权。这种复制是不当的，如果知识产权所有者愿意，可以通过法律系统予以制止。


# 关于版权
版权是一项法律权利，存在于许多国家，授予原创作品创作者决定其他人是否可以在什么条件下使用该原创作品的专有权利。
这通常是有限期的。
专有权不是绝对的，而是受到版权法的限制和例外的限制，包括合理使用。
版权对创意的一个重要限制是版权仅保护创意的原始表达，而不保护其背后的创意本身。

# 关于商标和专利
版权**不等同于**专利或商标。
版权保护您创作的原创作品。
专利保护发明和有用发现。
商标保护独特的词语、短语、符号、声音和设计。
商标用于识别和区分一方的商品（和服务）与另一方的商品。

# 关于tModLoader的许可证
TML使用[MIT许可证](https://raw.githubusercontent.com/tModLoader/tModLoader/stable/LICENSE)，也称为Expat许可证。
MIT许可证是一种非常宽松的许可证，实际上它是为了让普通人阅读而设计的，而不仅仅是为律师和法官设计。

根据["理解开源和自由软件许可证"作者Andrew M. St. Laurent](http://oreilly.com/openbook/osfreesoft/book/)：
> 这些许可证，适用于原始许可的代码，允许该代码用于专有软件，不需要分发开源版本的代码。根据这些许可证创建或派生的代码，可能会"封闭"，并且可以在专有许可证下进行开发，从而丢失给开源社区。然而，出于同样的原因，这些许可证非常灵活，几乎与所有形式的开源许可证兼容。

为了更彻底地解释这意味着什么，这意味着您可以分发编译后的软件而无需包含MIT许可证，并且您可以分发使用MIT许可软件您自己的源代码，但您自己的源代码不必根据MIT许可证获得许可。MIT许可证仅保护其获得许可的代码。这意味着如果您创建TML的分叉，您可以自由选择另一个许可证来分发您的工作，但您仍然必须包含MIT许可证，因为TML是根据该许可证获得许可的。

该许可证还提供免责声明。从本质上讲，我们（TML的发布者）对使用软件对您或您的计算机造成的损害不承担责任。

# 如何为您的模组添加许可证
为您自己的模组添加许可证并不困难。如果您非常认真，可以咨询专业人士并设置您自己的许可证。不过，建议您查看涵盖您想要点的现有许可证。您可以使用[https://choosealicense.com](https://choosealicense.com)等网站来帮助您找到和选择一种。最常见的许可证是MIT许可证、Apache许可证和GNU许可证（但还有更多许可证）。

在Github（以及许多其他分发平台）上包含许可证很容易。在Github上，人们通常会在项目根目录包含一个名为"LICENSE"或"LICENSE.txt"的文件。Github也会尝试通过检查此文件来自动检测您的许可证。只需将您想要的许可证的内容包含在此类文件中，您的仓库站点就会包含该许可证。

**请注意**，您无法阻止其他人查看（除非您将项目设为私有）或分叉您的仓库。如果您在GitHub上的公共仓库中发布源代码，根据[服务条款](https://help.github.com/en/articles/github-terms-of-service)，其他GitHub用户有权在GitHub网站内查看和分叉您的仓库。如果您已经创建了一个公共仓库而不再希望用户访问它，您可以将其设为私有。当将公共仓库转换为私有仓库时，其他用户创建的现有分叉或本地副本仍会存在。有关更多信息，请参见["将公共仓库设为私有"。](https://help.github.com/en/articles/making-a-public-repository-private)（另见：[为仓库添加许可证](https://help.github.com/en/articles/licensing-a-repository)）

## 选择性许可
根据相关项目的性质，可能需要分部分对其进行许可。

考虑一下[Calamity Mod Music](https://github.com/CalamityTeam/CalamityModMusicPublic)。它包含大量受版权保护的资产和一些加载它们的代码。
鉴于与维护资产版权所有权和控制相关的重大价值，他们使用统一的保留所有权利版权。
相比之下，[Example Mod](https://github.com/tModLoader/tModLoader/tree/1.4.4/ExampleMod)包含大量供他人使用的代码示例。它使用统一的MIT许可证，所有包含的资产也根据MIT许可证获得许可，可根据这些条件使用。

您可以选择多个许可证。您需要确保明确标识哪些内容由哪个许可证覆盖。

因此，如果您考虑使模组源代码可见，建议考虑是否为代码和资产应用单独的许可证。如果这样做，请咨询适当的图像/音乐许可机构，并明确标识每个许可证下的文件范围。
通常，它看起来像是"所有文件保留所有权利，但C#源文件类型根据{此处您的许可证}获得许可"。建议咨询[LibreOffice](https://www.libreoffice.org/about-us/source-code/)或[Mozilla的Firefox](https://www.mozilla.org/en-US/foundation/licensing/)等示例。

# 不使用许可证（"无许可证"）或放弃版权
您没有义务使用许可证。如前所述，默认版权法适用，因此您保留对项目作品的所有权利，任何人不得复制、分发或从您的作品中创建衍生作品。（除非在GitHub网站上查看和分叉您的项目（如上所述，这仅在您使用Github时适用。请查阅您所使用的分发平台的服务条款以了解情况）。不使用许可证称为"无许可证"软件。无许可证软件是一种计算机软件，它没有明确处于公共领域，但软件所有者没有明确许可使软件根据伯尔尼公约完全受版权保护。（另见：[维基百科](https://en.wikipedia.org/wiki/License-free_software)）

如果您不太关心，不使用许可证也可以，但建议在选择许可证时做出明智的决定。

如果您想[放弃版权垄断利益](https://cr.yp.to/publicdomain.html)，"Unlicense"是另一个选择。您可以通过根据[Unlicense](https://unlicense.org/)许可您的工作来轻松做到这一点。
**但请注意**，Unlicense并不是非常"详细"，它相当简单，可能导致不同解释，在法律背景下可能导致责任问题。它可以被视为为人类"易于阅读"而写的，但不一定适合我们今天使用的法律系统。如果您想将您的工作置于公共领域，您可能对[Creative Commons Zero许可证](https://creativecommons.org/share-your-work/public-domain/cc0/)（CC0）更感兴趣，它是一个定义得更好的许可证。另请注意，Creative Commons（CC0）许可证非常受欢迎、非常成熟，用于许多产品，而不仅仅是代码。所有CC许可证都是不错的选择。
**Unilicense不是全球性的。** Unilicense在英联邦生态系统之外没有意义，**在某些国家（澳大利亚、新西兰等）的合法性不明确，在某些国家（如德国）甚至明确违法**。最后，某些条款很容易被解释为另一种或模糊的含义，因此如果您以某种方式上法庭，可能需要几次审判才能让法律团队为Unilicense提出一个客观定义。

**请理解**，在每个司法管辖区完全放弃版权持有是不可能的，但像CC0许可证这样的许可证是仍然为您的公共领域目标服务的最完整替代方案。如果我们追求这个目标，我们强烈推荐任何CC0许可证。

# 版权侵权
某人可能侵犯了您的版权，并可能因不遵守许可证条款而承担责任。作为一个非专业的、业余的模组制作团队，我们能做的有限。

我们拥有内部的模组浏览器。我们能为您提供的是，如果能提供证据，可以对有问题的被复制模组进行删除，但我们对其他平台（如Github）没有管辖权，对合作伙伴平台（如Steam）的管辖权也有限。

我们也无法为您提供法律建议。如果很重要，建议您咨询专业人士。如果您想联系我们，最好的方式是通过我们的[discord服务器](https://discord.gg/tmodloader)联系tmodloader开发团队（需要DiscordApp账户）

# 来源
除非另有说明，否则所有来源均在2019年4月26日查阅
* https://en.wikipedia.org/wiki/License-free_software
* https://choosealicense.com/no-permission/
* https://en.wikipedia.org/wiki/Copyright
* https://unlicense.org/
* https://help.github.com/en/articles/licensing-a-repository
* https://help.github.com/en/articles/github-terms-of-service
* https://choosealicense.com
* https://www.gnu.org/licenses/agpl.html
* https://creativecommons.org/share-your-work/public-domain/cc0/
* https://github.com/Dradonhunter11/tModLoader/tree/x64
* https://web.archive.org/web/20170301020915/https://lists.opensource.org/pipermail/license-review/2012-January/001386.html（于2019年7月29日查阅）
