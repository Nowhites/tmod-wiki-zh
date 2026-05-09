* **警告：本指南很久以前编写，与当前tModLoader无关。请不要遵循本指南。**
* **我们推荐在Mac和Linux上开发mods使用[Visual Studio Code](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Visual-Studio-Code)或[Rider](https://github.com/tModLoader/tModLoader/wiki/Developing-with-Rider)。**
* **Mac和Linux不再需要任何特定于操作系统的说明。**

# 这些说明尚不适用

目前，说明在[这里](https://forums.terraria.org/index.php?threads/1-3-tmodloader-a-modding-api.23726/post-1001200)。

下面所有的内容可能与tModLoader v0.11相关，但说明尚不起作用。

### 注意：工作进行中，说明尚未完全工作或测试。目前还没有实现在Linux/Mac上反编译Terraria的功能。这意味着您需要运行`Setup`工具在Windows下反编译Terraria。希望这也能很快在Linux和Mac上实现。

# 在Mac或Linux上开发
tModLoader需要安装Microsoft C#编译器（`mcs`）。
在Windows上，它随.NET安装本身捆绑。在Mac和Linux上，它需要一个可能未在系统上预安装的单独软件包。

您可以通过打开终端并执行命令`mcs`来检查它是否存在，您应该会看到类似这样的内容：
```
error CS2008: No files to compile were specified
Compilation failed: 1 error(s), 0 warnings
```

或者您可以尝试编译并查找`mcs not found`错误。

有关如何安装所需软件包的说明在下一节中。

# 在tModLoader中构建mods

tModLoader能够构建在Terraria本身内没有外部依赖项/引用的mods。
如果您不熟悉mod制作或不知道外部依赖项是什么，这很可能就是您想要的。

如果您想使用允许更多"花哨"功能的外部依赖项，并且您知道自己在做什么，您可以看看[在tModLoader外构建](#在tmodloader外构建)

## 安装mono
安装Mono框架允许您直接在tModLoader中的`Mod Sources`菜单选项下构建mods。

### Linux

在大多数Linux发行版上，软件包称为`mono-complete`或简单的`mono`。

如果您在安装软件包时遇到麻烦，可以前往mono项目网站的[安装说明](https://www.mono-project.com/download/stable/#download-lin)页面获取进一步帮助。

### Mac

访问Mono网站并按照Mac的[安装说明](https://www.mono-project.com/download/stable/#download-mac)进行操作。

显然这个安装不会将适当的工具添加到Path中，因此如果仍然无法在游戏中构建，您可能需要遵循这些说明。如果您有任何相关信息，请更新此页面。

编辑：可以在这个[Stackoverflow问题](https://stackoverflow.com/questions/32542535/how-to-install-mono-on-macos-so-mono-works-in-terminal)的答案部分找到一些进一步说明。

未经测试。

# 在tModLoader外构建

如果您想拥有更灵活且通常更好的开发环境，您应该考虑在tModLoader外构建mods。这意味着使用IDE（如Visual Studio Code（常规Visual Studio在Linux上不支持，尽管在Mac上支持））。

Xamarin/MonoDevelop（Visual Studio for Mac基于此）是最简单的入门方法。

以下说明可用于启动简单的环境：[Xamarin/MonoDevelop说明](https://forums.terraria.org/index.php?threads/1-3-tmodloader-a-modding-api.23726/page-525#post-1001200)

## Visual Studio for Mac的工作说明
使用游戏中的骨架生成器，可以对生成的.csproj进行以下更改以使其在Visual Studio for Mac上工作。这些结果是一个modder尝试使其工作的结果。我们将努力在进一步更新中改进工作流程。与此同时，这是编辑后的csproj：
```xml
<?xml version="1.0" encoding="utf-8"?>
<Project Sdk="Microsoft.NET.Sdk">
    <Import Project="..\..\references\tModLoader.targets" />
    <PropertyGroup>
        <AssemblyName>test</AssemblyName>
        <TargetFramework>net45</TargetFramework>
        <PlatformTarget>x86</PlatformTarget>
        <LangVersion>latest</LangVersion>
    </PropertyGroup>
    <Target Name="BuildMod" AfterTargets="Build">
        <Exec Command="&quot;$(tMLBuildServerPath)&quot; -build $(ProjectDir) -eac $(TargetPath)" />
    </Target>
    <PropertyGroup Condition=" '$(RunConfiguration)' == 'Terraria' ">
        <StartAction>Program</StartAction>
        <StartProgram>$(tMLPath)</StartProgram>
        <StartWorkingDirectory>$(TerrariaSteamPath)</StartWorkingDirectory>
        <EnvironmentVariables>
            <Variable name="DYLD_LIBRARY_PATH" value="$(TerrariaSteamPath)/osx" xmlns="" />
        </EnvironmentVariables>
    </PropertyGroup>
</Project>
```

## Debian上MonoDevelop编译的调整
（2020-05-31，Debian 10/4.19.118，MonoDevelop 7.8.4，Mono 6.8.0.123，Steam tModLoader v0.11.7.4）

tModLoader看起来想要

* `<projectname>.XNA.dll`
* `<projectname>.XNA.pdb`
* `<projectname>.FNA.dll`
* `<projectname>.FNA.pdb`

构建mod时。

大部分按照上面链接中的说明，使用提供的空白Linux csproj，但有以下值得注意的例外。

在打开项目之前：
* 安装MonoDevelop时，将仓库添加到apt缓存，更新所有mono*包，然后安装。
* 从https://github.com/tModLoader/tModLoader/releases/tag/v0.11.4获取ModCompile zip并在Terraria安装文件夹中的新ModCompile文件夹中组合它们。
* 在项目附近创建指向tModLoader和Terraria安装的符号链接以便于路径设置。
* 在项目文件中将`<Import Project="$(MSBuildToolsPath)/Microsoft.CSHARP.targets" />`更改为`<Import Project="$(MSBuildToolsPath)/Microsoft.CSharp.targets" />`。

在MonoDevelop中，打开项目后：
* 删除所有引用的程序集，添加回`tModLoader`文件夹中的`FNA.dll`和`tModLoader.exe`。可能需要根据代码要求添加其他程序集，当有人可以测试Windows端的构建时，所以写下删除的程序集。
* 在项目选项/配置下，将`Windows`重命名为`MyMod.XNA`。
* 在项目选项/配置下，将`Mono`重命名为`MyMod.FNA`。
* 在项目选项/编译器下，将两个配置中的调试信息从`None`更改为`Symbols only`。
* 在项目选项/自定义命令下，添加后期构建步骤`cp bin/${ProjectConfigName}/${ProjectName}.pdb ${ProjectConfigName}.pdb`到两个配置。

然后能够编译两个配置，运行`tModLoaderServer -build <path_to_mymod>`成功，简单的mod（1个瓦片，1个物品）在游戏中有效。需要用Windows测试。

<details>
<summary>mymod.csproj</summary>

```
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="4.0" DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <Configuration Condition=" '$(Configuration)' == '' ">Debug</Configuration>
    <Platform Condition=" '$(Platform)' == '' ">AnyCPU</Platform>
    <ProjectGuid>{7773BDD8-037E-46DB-B842-B0EE41E74873}</ProjectGuid>
    <OutputType>Library</OutputType>
    <NoStandardLibraries>false</NoStandardLibraries>
    <AssemblyName>MyMod</AssemblyName>
    <TargetFrameworkVersion>v4.0</TargetFrameworkVersion>
    <FileAlignment>512</FileAlignment>
  </PropertyGroup>
  <PropertyGroup>
    <RootNamespace>MyMod</RootNamespace>
  </PropertyGroup>
  <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|AnyCPU' ">
    <AllowUnsafeBlocks>true</AllowUnsafeBlocks>
  </PropertyGroup>
  <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|AnyCPU' ">
    <AllowUnsafeBlocks>true</AllowUnsafeBlocks>
  </PropertyGroup>
  <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'MyMod.FNA|AnyCPU' ">
    <DebugSymbols>true</DebugSymbols>
    <DebugType>pdbonly</DebugType>
    <Optimize>true</Optimize>
    <OutputPath>bin\MyMod.FNA</OutputPath>
    <DefineConstants>TRACE</DefineConstants>
    <ErrorReport>prompt</ErrorReport>
    <WarningLevel>4</WarningLevel>
    <CustomCommands>
      <CustomCommands>
        <Command>
          <type>AfterBuild</type>
          <command>cp bin/${ProjectConfigName}/${TargetName} ${ProjectConfigName}.dll</command>
        </Command>
        <Command>
          <type>AfterBuild</type>
          <command>cp bin/${ProjectConfigName}/${ProjectName}.pdb ${ProjectConfigName}.pdb</command>
        </Command>
      </CustomCommands>
    </CustomCommands>
    <AllowUnsafeBlocks>true</AllowUnsafeBlocks>
  </PropertyGroup>
  <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'MyMod.XNA|AnyCPU' ">
    <DebugSymbols>true</DebugSymbols>
    <DebugType>pdbonly</DebugType>
    <Optimize>false</Optimize>
    <OutputPath>bin\MyMod.XNA</OutputPath>
    <WarningLevel>4</WarningLevel>
    <CustomCommands>
      <CustomCommands>
        <Command>
          <type>AfterBuild</type>
          <command>cp bin/${ProjectConfigName}/${TargetName} ${ProjectConfigName}.dll</command>
        </Command>
        <Command>
          <type>AfterBuild</type>
          <command>cp bin/${ProjectConfigName}/${ProjectName}.pdb ${ProjectConfigName}.pdb</command>
        </Command>
      </CustomCommands>
    </CustomCommands>
    <AllowUnsafeBlocks>true</AllowUnsafeBlocks>
  </PropertyGroup>
  <ItemGroup>
  </ItemGroup>
  <ItemGroup>
    <Content Include="build.txt" />
    <Content Include="description.txt" />
  </ItemGroup>
  <ItemGroup />
  <ItemGroup>
    <Compile Include="Tiles\MyTile.cs" />
    <Compile Include="Items\MyItem.cs" />
    <Compile Include="MyMod.cs" />
  </ItemGroup>
  <ItemGroup>
    <Folder Include="Tiles\" />
    <Folder Include="Items\" />
  </ItemGroup>
  <ItemGroup>
    <Reference Include="FNA">
      <HintPath>..\..\tModLoader\FNA.dll</HintPath>
    </Reference>
    <Reference Include="tModLoader">
      <HintPath>..\..\tModLoader\tModLoader.exe</HintPath>
    </Reference>
  </ItemGroup>
  <Import Project="$(MSBuildToolsPath)/Microsoft.CSharp.targets" />
  <ProjectExtensions>
    <VisualStudio AllowExistingFolder="true" />
  </ProjectExtensions>
</Project>
```

</details>

# 已知问题
## Mac
### 'System.Drawing.GDIPlus'的类型初始值设定项抛出了异常
在**Mac**上按照本指南操作后，您可能会在尝试构建包含png文件的mod时遇到此错误。

![](https://cdn.discordapp.com/attachments/103115427491610624/540334979343974410/Screen_Shot_2019-01-30_at_6.55.40_PM.png)

要修复此问题，请下载[此zip文件](https://cdn.discordapp.com/attachments/103115427491610624/540387967915655188/system.drawing_for_mac.zip)并将其内容提取到您安装tModLoader的位置（通常在`Library/Application Support/Steam/steamapps/common/Terraria/Terraria.app/Contents/MacOS`），如果被要求则覆盖/合并任何文件夹/文件。
