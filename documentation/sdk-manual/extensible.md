---
tip: translate by openai@2023-06-07 21:04:55
...
---
title: Using the Extensible SDK
-------------------------------

This chapter describes the extensible SDK and how to install it. Information covers the pieces of the SDK, how to install it, and presents a look at using the `devtool` functionality. The extensible SDK makes it easy to add new applications and libraries to an image, modify the source for an existing component, test changes on the target hardware, and ease integration into the rest of the `OpenEmbedded Build System`{.interpreted-text role="term"}.

> 本章节描述可扩展的 SDK 以及如何安装它。信息涵盖了 SDK 的组件，如何安装它，以及使用 `devtool` 功能的一个概览。可扩展的 SDK 可以轻松地将新应用程序和库添加到图像中，修改现有组件的源代码，在目标硬件上测试更改，并且可以轻松地集成到 `OpenEmbedded Build System`{.interpreted-text role="term"}中。

::: note
::: title
Note
:::

For a side-by-side comparison of main features supported for an extensible SDK as compared to a standard SDK, see the `sdk-manual/intro:introduction`{.interpreted-text role="ref"} section.

> 要比较可扩展 SDK 和标准 SDK 的主要功能，请参阅 `sdk-manual/intro:introduction`{.interpreted-text role="ref"}部分。
> :::

In addition to the functionality available through `devtool`, you can alternatively make use of the toolchain directly, for example from Makefile and Autotools. See the \"`sdk-manual/working-projects:using the sdk toolchain directly`{.interpreted-text role="ref"}\" chapter for more information.

> 除了通过 `devtool` 提供的功能之外，您还可以直接使用工具链，例如从 Makefile 和 Autotools。有关更多信息，请参阅“sdk-manual / working-projects：直接使用 sdk 工具链”章节。

# Why use the Extensible SDK and What is in It?

The extensible SDK provides a cross-development toolchain and libraries tailored to the contents of a specific image. You would use the Extensible SDK if you want a toolchain experience supplemented with the powerful set of `devtool` commands tailored for the Yocto Project environment.

> 可扩展的 SDK 提供了一个跨开发工具链和针对特定图像内容设计的库。如果您想要一个工具链体验，并且需要为 Yocto Project 环境设计的强大 devtool 命令，那么您可以使用可扩展的 SDK。

The installed extensible SDK consists of several files and directories. Basically, it contains an SDK environment setup script, some configuration files, an internal build system, and the `devtool` functionality.

> 安装的可扩展 SDK 包括几个文件和目录。基本上，它包含一个 SDK 环境设置脚本、一些配置文件、一个内部构建系统以及 `devtool` 功能。

# Installing the Extensible SDK

## Two ways to install the Extensible SDK

Extensible SDK can be installed in two different ways, and both have their own pros and cons:

> 可扩展的 SDK 可以通过两种不同的方式安装，每种方式都有自己的优点和缺点。

#\. *Setting up the Extensible SDK environment directly in a Yocto build*. This avoids having to produce, test, distribute and maintain separate SDK installer archives, which can get very large. There is only one environment for the regular Yocto build and the SDK and less code paths where things can go not according to plan. It\'s easier to update the SDK: it simply means updating the Yocto layers with git fetch or layer management tooling. The SDK extensibility is better than in the second option: just run `bitbake` again to add more things to the sysroot, or add layers if even more things are required.

#\. *Setting up the Extensible SDK from a standalone installer*. This has the benefit of having a single, self-contained archive that includes all the needed binary artifacts. So nothing needs to be rebuilt, and there is no need to provide a well-functioning binary artefact cache over the network for developers with underpowered laptops.

## Setting up the Extensible SDK environment directly in a Yocto build

1. Set up all the needed layers and a Yocto `Build Directory`{.interpreted-text role="term"}, e.g. a regular Yocto build where `bitbake` can be executed.

> 設置所有所需的層和 Yocto `Build Directory`，例如可以執行 `bitbake` 的常規 Yocto 構建。

2.

> 二

```
Run:

:   \$ bitbake meta-ide-support \$ bitbake -c populate_sysroot gtk+3 (or any other target or native item that the application developer would need) \$ bitbake build-sysroots
```

## Setting up the Extensible SDK from a standalone installer

The first thing you need to do is install the SDK on your `Build Host`{.interpreted-text role="term"} by running the `*.sh` installation script.

> 首先你需要做的事情是在你的 `构建主机` 上运行 `*.sh` 安装脚本来安装 SDK。

You can download a tarball installer, which includes the pre-built toolchain, the `runqemu` script, the internal build system, `devtool`, and support files from the appropriate :yocto_dl:[toolchain \</releases/yocto/yocto-&DISTRO;/toolchain/\>]{.title-ref} directory within the Index of Releases. Toolchains are available for several 32-bit and 64-bit architectures with the `x86_64` directories, respectively. The toolchains the Yocto Project provides are based off the `core-image-sato` and `core-image-minimal` images and contain libraries appropriate for developing against that image.

> 您可以从适当的 :yocto_dl:[发布索引中的 toolchain \</releases/yocto/yocto-&DISTRO;/toolchain/\>]{.title-ref} 目录下载 tarball 安装程序，其中包括预构建的工具链、`runqemu` 脚本、内部构建系统 `devtool` 和支持文件。Yocto 项目提供的工具链支持几种 32 位和 64 位架构，分别在 `x86_64` 目录下。Yocto 项目提供的工具链是基于 `core-image-sato` 和 `core-image-minimal` 映像的，并包含与该映像相适应的库，用于开发。

The names of the tarball installer scripts are such that a string representing the host system appears first in the filename and then is immediately followed by a string representing the target architecture. An extensible SDK has the string \"-ext\" as part of the name. Following is the general form:

> 安装脚本的名称是这样的，文件名中首先出现一个表示主机系统的字符串，然后立即跟上一个表示目标架构的字符串。可扩展的 SDK 的名称中包含“-ext”字符串。以下是一般形式：

```
poky-glibc-host_system-image_type-arch-toolchain-ext-release_version.sh

Where:
    host_system is a string representing your development system:

               i686 or x86_64.

    image_type is the image for which the SDK was built:

               core-image-sato or core-image-minimal

    arch is a string representing the tuned target architecture:

               aarch64, armv5e, core2-64, i586, mips32r2, mips64, ppc7400, or cortexa8hf-neon

    release_version is a string representing the release number of the Yocto Project:

               &DISTRO;, &DISTRO;+snapshot
```

For example, the following SDK installer is for a 64-bit development host system and a i586-tuned target architecture based off the SDK for `core-image-sato` and using the current &DISTRO; snapshot:

> 以下为示例：SDK 安装程序适用于 64 位开发主机系统，基于 `core-image-sato` 的 SDK，并使用当前&DISTRO;快照，以 i586 优化的目标架构：

```
poky-glibc-x86_64-core-image-sato-i586-toolchain-ext-&DISTRO;.sh
```

::: note
::: title
Note
:::

As an alternative to downloading an SDK, you can build the SDK installer. For information on building the installer, see the `sdk-manual/appendix-obtain:building an sdk installer`{.interpreted-text role="ref"} section.

> 作为下载 SDK 的替代方案，您可以构建 SDK 安装程序。有关构建安装程序的信息，请参见 `sdk-manual/appendix-obtain:building an sdk installer` 部分。
> :::

The SDK and toolchains are self-contained and by default are installed into the `poky_sdk` folder in your home directory. You can choose to install the extensible SDK in any location when you run the installer. However, because files need to be written under that directory during the normal course of operation, the location you choose for installation must be writable for whichever users need to use the SDK.

> SDK 和工具链是独立的，默认会安装到您的主目录中的 `poky_sdk` 文件夹中。当您运行安装程序时，您可以选择在任何位置安装可扩展的 SDK。但是，由于在正常操作过程中需要在该目录下写入文件，因此您选择安装的位置必须对需要使用 SDK 的任何用户都可写。

The following command shows how to run the installer given a toolchain tarball for a 64-bit x86 development host system and a 64-bit x86 target architecture. The example assumes the SDK installer is located in `~/Downloads/` and has execution rights:

> 以下命令显示如何在 64 位 x86 开发主机系统和 64 位 x86 目标架构上运行安装程序，给定一个工具链 tarball。示例假设 SDK 安装程序位于 `~/Downloads/` 并具有执行权限：

```
$ ./Downloads/poky-glibc-x86_64-core-image-minimal-core2-64-toolchain-ext-2.5.sh
Poky (Yocto Project Reference Distro) Extensible SDK installer version 2.5
==========================================================================
Enter target directory for SDK (default: poky_sdk):
You are about to install the SDK to "/home/scottrif/poky_sdk". Proceed [Y/n]? Y
Extracting SDK..............done
Setting it up...
Extracting buildtools...
Preparing build system...
Parsing recipes: 100% |##################################################################| Time: 0:00:52
Initialising tasks: 100% |###############################################################| Time: 0:00:00
Checking sstate mirror object availability: 100% |#######################################| Time: 0:00:00
Loading cache: 100% |####################################################################| Time: 0:00:00
Initialising tasks: 100% |###############################################################| Time: 0:00:00
done
SDK has been successfully set up and is ready to be used.
Each time you wish to use the SDK in a new shell session, you need to source the environment setup script e.g.
 $ . /home/scottrif/poky_sdk/environment-setup-core2-64-poky-linux
```

::: note
::: title
Note
:::

If you do not have write permissions for the directory into which you are installing the SDK, the installer notifies you and exits. For that case, set up the proper permissions in the directory and run the installer again.

> 如果您没有安装 SDK 所需的目录的写入权限，安装程序会提示您并退出。在这种情况下，请在目录中设置正确的权限，然后再次运行安装程序。
> :::

# Running the Extensible SDK Environment Setup Script

Once you have the SDK installed, you must run the SDK environment setup script before you can actually use the SDK.

> 一旦安装了 SDK，在实际使用 SDK 之前，必须运行 SDK 环境设置脚本。

When using a SDK directly in a Yocto build, you will find the script in `tmp/deploy/images/qemux86-64/` in your `Build Directory`{.interpreted-text role="term"}.

> 当在 Yocto 构建中直接使用 SDK 时，您将在构建目录中的 `tmp/deploy/images/qemux86-64/` 中找到脚本。

When using a standalone SDK installer, this setup script resides in the directory you chose when you installed the SDK, which is either the default `poky_sdk` directory or the directory you chose during installation.

> 当使用独立的 SDK 安装程序时，该安装程序位于您安装 SDK 时选择的目录中，默认为“poky_sdk”目录或您安装时选择的目录。

Before running the script, be sure it is the one that matches the architecture for which you are developing. Environment setup scripts begin with the string \"`environment-setup`\" and include as part of their name the tuned target architecture. As an example, the following commands set the working directory to where the SDK was installed and then source the environment setup script. In this example, the setup script is for an IA-based target machine using i586 tuning:

> 在运行脚本之前，请确保它是与您正在开发的架构匹配的脚本。环境设置脚本以“environment-setup”字符串开头，并包含调整后的目标架构的名称。例如，以下命令将工作目录设置为 SDK 安装的位置，然后源自环境设置脚本。在此示例中，设置脚本用于基于 IA 的目标机器，使用 i586 调整：

```
$ cd /home/scottrif/poky_sdk
$ source environment-setup-core2-64-poky-linux
SDK environment now set up; additionally you may now run devtool to perform development tasks.
Run devtool --help for further details.
```

When using the environment script directly in a Yocto build, it can be run similarly:

> 当直接在 Yocto 构建中使用环境脚本时，可以类似地运行它：

> \$ source tmp/deploy/images/qemux86-64/environment-setup-core2-64-poky-linux

Running the setup script defines many environment variables needed in order to use the SDK (e.g. `PATH`, `CC`{.interpreted-text role="term"}, `LD`{.interpreted-text role="term"}, and so forth). If you want to see all the environment variables the script exports, examine the installation file itself.

> 运行设置脚本定义了许多环境变量，以便使用 SDK（例如 `PATH`、`CC`、`LD` 等）。如果要查看脚本导出的所有环境变量，请检查安装文件本身。

# Using `devtool` in Your SDK Workflow

The cornerstone of the extensible SDK is a command-line tool called `devtool`. This tool provides a number of features that help you build, test and package software within the extensible SDK, and optionally integrate it into an image built by the OpenEmbedded build system.

> 延展 SDK 的基石是一个名为“devtool”的命令行工具。该工具提供了许多功能，可以帮助您在延展 SDK 中构建、测试和打包软件，并可选择将其集成到由 OpenEmbedded 构建系统构建的映像中。

::: note
::: title
Note
:::

The use of devtool is not limited to the extensible SDK. You can use devtool to help you easily develop any project whose build output must be part of an image built using the build system.

> 使用 devtool 不仅限于可扩展的 SDK。您可以使用 devtool 来帮助您轻松开发任何必须使用构建系统构建的图像部分的项目。
> :::

The `devtool` command line is organized similarly to `overview-manual/development-environment:git`{.interpreted-text role="ref"} in that it has a number of sub-commands for each function. You can run `devtool --help` to see all the commands.

> `devtool` 命令行的组织方式与 `overview-manual/development-environment:git`{.interpreted-text role="ref"}类似，它具有许多子命令来执行每个功能。您可以运行 `devtool --help` 来查看所有命令。

::: note
::: title
Note
:::

See the \" devtool  Quick Reference \" in the Yocto Project Reference Manual for a devtool quick reference.

> 请参阅 Yocto Project 参考手册中的“devtool 快速参考”以获取 devtool 快速参考。
> :::

Three `devtool` subcommands provide entry-points into development:

> 三个 ` devtool` 子命令提供了进入开发的入口点：

- *devtool add*: Assists in adding new software to be built.
- *devtool modify*: Sets up an environment to enable you to modify the source of an existing component.

> - *devtool modify*：设置环境以使您能够修改现有组件的源代码。

- *devtool upgrade*: Updates an existing recipe so that you can build it for an updated set of source files.

> - *devtool upgrade*：更新现有食谱，使您可以为更新的源文件集构建它。

As with the build system, \"recipes\" represent software packages within `devtool`. When you use `devtool add`, a recipe is automatically created. When you use `devtool modify`, the specified existing recipe is used in order to determine where to get the source code and how to patch it. In both cases, an environment is set up so that when you build the recipe a source tree that is under your control is used in order to allow you to make changes to the source as desired. By default, new recipes and the source go into a \"workspace\" directory under the SDK.

> 随着构建系统，“配方”在 devtool 中代表软件包。当您使用 devtool add 时，将自动创建配方。当您使用 devtool modify 时，将使用指定的现有配方来确定获取源代码的位置以及如何补丁它。在这两种情况下，都会设置一个环境，以便在构建配方时使用您控制的源树，以允许您根据需要对源代码进行更改。默认情况下，新配方和源代码将放入 SDK 下的“工作区”目录中。

The remainder of this section presents the `devtool add`, `devtool modify`, and `devtool upgrade` workflows.

> 本节的其余部分介绍了 `devtool add`、`devtool modify` 和 `devtool upgrade` 工作流程。

## Use `devtool add` to Add an Application

The `devtool add` command generates a new recipe based on existing source code. This command takes advantage of the `devtool-the-workspace-layer-structure`{.interpreted-text role="ref"} layer that many `devtool` commands use. The command is flexible enough to allow you to extract source code into both the workspace or a separate local Git repository and to use existing code that does not need to be extracted.

> 命令 `devtool add` 基于现有源代码生成新的配方。这个命令利用了许多 `devtool` 命令使用的 `devtool-the-workspace-layer-structure`{.interpreted-text role="ref"}层。该命令足够灵活，允许您将源代码提取到工作区或单独的本地 Git 存储库中，并使用不需要提取的现有代码。

Depending on your particular scenario, the arguments and options you use with `devtool add` form different combinations. The following diagram shows common development flows you would use with the `devtool add` command:

> 根据您的特定情况，您使用 `devtool add` 的参数和选项形成不同的组合。 以下图表显示您使用 `devtool add` 命令的常用开发流程：

![image](figures/sdk-devtool-add-flow.png){width="100.0%"}

1. *Generating the New Recipe*: The top part of the flow shows three scenarios by which you could use `devtool add` to generate a recipe based on existing source code.

> 1. *生成新配方*：流程的上部显示了三种情况，可以使用 `devtool add` 根据现有源代码生成配方。

In a shared development environment, it is typical for other developers to be responsible for various areas of source code. As a developer, you are probably interested in using that source code as part of your development within the Yocto Project. All you need is access to the code, a recipe, and a controlled area in which to do your work.

> 在一个共享的开发环境中，其他开发人员通常负责各个源代码区域。作为一名开发人员，您可能有兴趣将该源代码作为 Yocto 项目的一部分使用。您所需要的只是访问代码，配方和一个受控的工作区。

Within the diagram, three possible scenarios feed into the `devtool add` workflow:

> 在图表中，有三种可能的情况可以进入“devtool add”工作流程：

- *Left*: The left scenario in the figure represents a common situation where the source code does not exist locally and needs to be extracted. In this situation, the source code is extracted to the default workspace \-\-- you do not want the files in some specific location outside of the workspace. Thus, everything you need will be located in the workspace:

> 图中的左侧场景代表一种常见情况，即源代码不存在于本地，需要提取。在这种情况下，源代码将提取到默认的工作区 - 你不想将文件放在工作区之外的某个特定位置。因此，您需要的一切都将位于工作区中。

```
```

$ devtool add recipe fetchuri

```

With this command, `devtool` extracts the upstream source files into a local Git repository within the `sources` folder. The command then creates a recipe named recipe and a corresponding append file in the workspace. If you do not provide recipe, the command makes an attempt to determine the recipe name.
```

- *Middle*: The middle scenario in the figure also represents a situation where the source code does not exist locally. In this case, the code is again upstream and needs to be extracted to some local area \-\-- this time outside of the default workspace.

> 图中的中等场景也代表一种情况，即源代码不存在于本地。在这种情况下，代码仍然是上游的，需要提取到某个本地区域 - 这次不是默认工作区。

```
 ::: note
 ::: title
 Note
 :::

 If required, devtool always creates a Git repository locally during the extraction.
 :::

 Furthermore, the first positional argument `srctree` in this case identifies where the `devtool add` command will locate the extracted code outside of the workspace. You need to specify an empty directory:

```

$ devtool add recipe srctree fetchuri

```

In summary, the source code is pulled from fetchuri and extracted into the location defined by `srctree` as a local Git repository.

Within workspace, `devtool` creates a recipe named recipe along with an associated append file.
```

- *Right*: The right scenario in the figure represents a situation where the `srctree` has been previously prepared outside of the `devtool` workspace.

> 图中右侧的场景代表的是在 `devtool` 工作区之外预先准备好了 `srctree` 的情况。

```
 The following command provides a new recipe name and identifies the existing source tree location:

```

$ devtool add recipe srctree

```

The command examines the source code and creates a recipe named recipe for the code and places the recipe into the workspace.

Because the extracted source code already exists, `devtool` does not try to relocate the source code into the workspace \-\-- only the new recipe is placed in the workspace.

Aside from a recipe folder, the command also creates an associated append folder and places an initial `*.bbappend` file within.
```

2. *Edit the Recipe*: You can use `devtool edit-recipe` to open up the editor as defined by the `$EDITOR` environment variable and modify the file:

> 2. *编辑食谱*: 你可以使用 `devtool edit-recipe` 来打开由 `$EDITOR` 环境变量定义的编辑器，并修改文件：

```

$ devtool edit-recipe recipe

> $ devtool 编辑配方食谱
```

From within the editor, you can make modifications to the recipe that take effect when you build it later.

> 从编辑器内部，您可以对配方进行修改，这些修改在稍后构建时生效。

3. *Build the Recipe or Rebuild the Image*: The next step you take depends on what you are going to do with the new code.

> 下一步你要怎么做取决于你将如何使用新代码。

If you need to eventually move the build output to the target hardware, use the following `devtool` command: :;

> 如果你需要最终将构建输出移至目标硬件，请使用以下 `devtool` 命令：::

> \$ devtool build recipe

>> 使用 devtool 编译配方
>>

>

>>

On the other hand, if you want an image to contain the recipe\'s packages from the workspace for immediate deployment onto a device (e.g. for testing purposes), you can use the `devtool build-image` command:

> 另一方面，如果你想要一张图片包含工作空间中的配方包以便立即部署到设备（例如用于测试目的），你可以使用 `devtool build-image` 命令：

```

$ devtool build-image image

> $ devtool 编译镜像 image
```

4. *Deploy the Build Output*: When you use the `devtool build` command to build out your recipe, you probably want to see if the resulting build output works as expected on the target hardware.

> 4. *部署构建输出*：当您使用 `devtool build` 命令构建菜谱时，您可能希望查看结果构建输出是否在目标硬件上按预期工作。

::: note
::: title

Note

> 注意
> :::

This step assumes you have a previously built image that is already either running in QEMU or is running on actual hardware. Also, it is assumed that for deployment of the image to the target, SSH is installed in the image and, if the image is running on real hardware, you have network access to and from your development machine.

> 这一步假设你已经构建了一个镜像，它已经在 QEMU 中运行或者在实际硬件上运行。此外，假设为了将镜像部署到目标，SSH 已经安装在镜像中，如果镜像在真实硬件上运行，你也有从开发机到目标的网络连接。
> :::

You can deploy your build output to that target hardware by using the `devtool deploy-target` command: \$ devtool deploy-target recipe target The target is a live target machine running as an SSH server.

> 你可以使用 `devtool deploy-target` 命令将构建输出部署到目标硬件：\$ devtool deploy-target recipe target 目标是一台运行 SSH 服务器的实时目标机器。

You can, of course, also deploy the image you build to actual hardware by using the `devtool build-image` command. However, `devtool` does not provide a specific command that allows you to deploy the image to actual hardware.

> 你当然也可以使用 `devtool build-image` 命令来部署你构建的镜像到实际硬件上。但是，`devtool` 没有提供一个特定的命令来允许你将镜像部署到实际硬件上。

5. *Finish Your Work With the Recipe*: The `devtool finish` command creates any patches corresponding to commits in the local Git repository, moves the new recipe to a more permanent layer, and then resets the recipe so that the recipe is built normally rather than from the workspace:

> 5. *使用配方完成工作*：`devtool finish` 命令会创建本地 Git 存储库中提交的任何补丁，将新配方移动到更持久的层，然后重置配方，以便配方正常构建，而不是从工作区构建：

```

$ devtool finish recipe layer

> 完成配方层的开发工具
```

::: note
::: title

Note

> 注意
> :::

Any changes you want to turn into patches must be committed to the Git repository in the source tree.

> 任何你想要转换成补丁的更改都必须提交到源树中的 Git 存储库中。
> :::

As mentioned, the `devtool finish` command moves the final recipe to its permanent layer.

> 按照提及，`devtool finish` 命令将最终配方移至其永久层。

As a final process of the `devtool finish` command, the state of the standard layers and the upstream source is restored so that you can build the recipe from those areas rather than the workspace.

> 作为 `devtool finish` 命令的最终步骤，标准层和上游源的状态将被恢复，这样您就可以从这些区域而不是工作区构建配方。

::: note
::: title

Note

> 注意
> :::

You can use the devtool reset command to put things back should you decide you do not want to proceed with your work. If you do use this command, realize that the source tree is preserved.

> 你可以使用 devtool 重置命令来把事情恢复原样，如果你决定不继续你的工作。如果你使用这个命令，要意识到源代码树会被保留下来。
> :::

## Use `devtool modify` to Modify the Source of an Existing Component

The `devtool modify` command prepares the way to work on existing code that already has a local recipe in place that is used to build the software. The command is flexible enough to allow you to extract code from an upstream source, specify the existing recipe, and keep track of and gather any patch files from other developers that are associated with the code.

> 命令 `devtool modify` 为开发现有代码做准备，现有的代码已经有一个本地的菜谱用于构建软件。该命令足够灵活，允许您从上游源提取代码，指定现有的菜谱，并跟踪和收集与代码相关的其他开发人员的补丁文件。

Depending on your particular scenario, the arguments and options you use with `devtool modify` form different combinations. The following diagram shows common development flows for the `devtool modify` command:

> 根据您的特定场景，您使用 `devtool modify` 的参数和选项形成不同的组合。 以下图表显示了 `devtool modify` 命令的常见开发流程：

![image](figures/sdk-devtool-modify-flow.png){width="100.0%"}

1. *Preparing to Modify the Code*: The top part of the flow shows three scenarios by which you could use `devtool modify` to prepare to work on source files. Each scenario assumes the following:

> 1. *准备修改代码*: 流程的上部显示了三种使用 `devtool modify` 准备进行源文件工作的情景。每种情景假设以下内容：

- The recipe exists locally in a layer external to the `devtool` workspace.

> 該食譜存在於 `devtool` 工作區外的一個外部層中。

- The source files exist either upstream in an un-extracted state or locally in a previously extracted state.

> 源文件可以存在于未解压状态的上游，或者已经解压状态的本地。

The typical situation is where another developer has created a layer for use with the Yocto Project and their recipe already resides in that layer. Furthermore, their source code is readily available either upstream or locally.

> 典型的情况是另一位开发人员已经为使用 Yocto 项目创建了一个层，而他们的食谱已经存在于该层中。此外，他们的源代码可以在上游或本地获得。

- *Left*: The left scenario in the figure represents a common situation where the source code does not exist locally and it needs to be extracted from an upstream source. In this situation, the source is extracted into the default `devtool` workspace location. The recipe, in this scenario, is in its own layer outside the workspace (i.e. `meta-` layername).

> 图中左侧的场景代表一种常见的情况，即源代码不存在本地，需要从上游源处提取。在这种情况下，源代码会被提取到默认的 `devtool` 工作区位置。在这种情况下，菜谱位于工作区之外的自己的层（即 `meta-` layername）中。

```
 The following command identifies the recipe and, by default, extracts the source files:

```

$ devtool modify recipe

```

Once `devtool` locates the recipe, `devtool` uses the recipe\'s `SRC_URI`{.interpreted-text role="term"} statements to locate the source code and any local patch files from other developers.

With this scenario, there is no `srctree` argument. Consequently, the default behavior of the `devtool modify` command is to extract the source files pointed to by the `SRC_URI`{.interpreted-text role="term"} statements into a local Git structure. Furthermore, the location for the extracted source is the default area within the `devtool` workspace. The result is that the command sets up both the source code and an append file within the workspace while the recipe remains in its original location.

Additionally, if you have any non-patch local files (i.e. files referred to with `file://` entries in `SRC_URI`{.interpreted-text role="term"} statement excluding `*.patch/` or `*.diff`), these files are copied to an `oe-local-files` folder under the newly created source tree. Copying the files here gives you a convenient area from which you can modify the files. Any changes or additions you make to those files are incorporated into the build the next time you build the software just as are other changes you might have made to the source.
```

- *Middle*: The middle scenario in the figure represents a situation where the source code also does not exist locally. In this case, the code is again upstream and needs to be extracted to some local area as a Git repository. The recipe, in this scenario, is again local and in its own layer outside the workspace.

> 图中的中间场景表示源代码也不存在本地的情况。在这种情况下，代码仍然是上游的，需要把它提取到本地作为 Git 存储库。在这种情况下，配方仍然是本地的，在工作区外的自己的层中。

```
 The following command tells `devtool` the recipe with which to work and, in this case, identifies a local area for the extracted source files that exists outside of the default `devtool` workspace:

```

$ devtool modify recipe srctree

```

::: note
::: title
Note
:::

You cannot provide a URL for srctree using the devtool command.
:::

As with all extractions, the command uses the recipe\'s `SRC_URI`{.interpreted-text role="term"} statements to locate the source files and any associated patch files. Non-patch files are copied to an `oe-local-files` folder under the newly created source tree.

Once the files are located, the command by default extracts them into `srctree`.

Within workspace, `devtool` creates an append file for the recipe. The recipe remains in its original location but the source files are extracted to the location you provide with `srctree`.
```

- *Right*: The right scenario in the figure represents a situation where the source tree (`srctree`) already exists locally as a previously extracted Git structure outside of the `devtool` workspace. In this example, the recipe also exists elsewhere locally in its own layer.

> 图中右侧的场景表示源树（`srctree`）已经以外部于 `devtool` 工作区之外的 Git 结构本地存在的情况。在这个例子中，配方也存在于自己的层中的其他地方。

```
 The following command tells `devtool` the recipe with which to work, uses the \"-n\" option to indicate source does not need to be extracted, and uses `srctree` to point to the previously extracted source files:

```

$ devtool modify -n recipe srctree

```

If an `oe-local-files` subdirectory happens to exist and it contains non-patch files, the files are used. However, if the subdirectory does not exist and you run the `devtool finish` command, any non-patch files that might exist next to the recipe are removed because it appears to `devtool` that you have deleted those files.

Once the `devtool modify` command finishes, it creates only an append file for the recipe in the `devtool` workspace. The recipe and the source code remain in their original locations.
```

2. *Edit the Source*: Once you have used the `devtool modify` command, you are free to make changes to the source files. You can use any editor you like to make and save your source code modifications.

> 2. *编辑源代码*：一旦你使用了 `devtool modify` 命令，你可以自由地修改源文件。你可以使用任何你喜欢的编辑器来进行和保存你的源代码修改。

3. *Build the Recipe or Rebuild the Image*: The next step you take depends on what you are going to do with the new code.

> 下一步你要做的取决于你将如何使用新代码。

If you need to eventually move the build output to the target hardware, use the following `devtool` command:

> 如果最终需要将构建输出移至目标硬件，请使用以下 `devtool` 命令：

```

$ devtool build recipe

> $ devtool 编译配方
```

On the other hand, if you want an image to contain the recipe\'s packages from the workspace for immediate deployment onto a device (e.g. for testing purposes), you can use the `devtool build-image` command: \$ devtool build-image image

> 另一方面，如果你想要一个图像包含工作区中的配方包供即时部署到设备（例如用于测试目的），你可以使用 `devtool build-image` 命令：\$ devtool build-image image

4. *Deploy the Build Output*: When you use the `devtool build` command to build out your recipe, you probably want to see if the resulting build output works as expected on target hardware.

> 4. *部署构建输出*：当您使用 `devtool build` 命令构建菜谱时，您可能希望查看生成的构建输出是否在目标硬件上按预期工作。

::: note
::: title

Note

> 注意
> :::

This step assumes you have a previously built image that is already either running in QEMU or running on actual hardware. Also, it is assumed that for deployment of the image to the target, SSH is installed in the image and if the image is running on real hardware that you have network access to and from your development machine.

> 这一步假设您已经构建了一个图像，该图像已经在 QEMU 中运行或在实际硬件上运行。另外，假设为了将图像部署到目标，图像中已安装 SSH，如果图像在真实硬件上运行，则您可以从开发机访问和访问网络。
> :::

You can deploy your build output to that target hardware by using the `devtool deploy-target` command:

> 你可以使用 `devtool deploy-target` 命令将你的构建输出部署到目标硬件：

```

$ devtool deploy-target recipe target

> $ devtool 部署目标配方目标
```

The target is a live target machine running as an SSH server.

> 目标是一台运行 SSH 服务器的活动目标机器。

You can, of course, use other methods to deploy the image you built using the `devtool build-image` command to actual hardware. `devtool` does not provide a specific command to deploy the image to actual hardware.

> 你当然可以使用其他方法来部署使用 `devtool build-image` 命令构建的镜像到实际硬件上。`devtool` 不提供将镜像部署到实际硬件的特定命令。

5. *Finish Your Work With the Recipe*: The `devtool finish` command creates any patches corresponding to commits in the local Git repository, updates the recipe to point to them (or creates a `.bbappend` file to do so, depending on the specified destination layer), and then resets the recipe so that the recipe is built normally rather than from the workspace:

> 5. *用配方完成你的工作*：`devtool finish` 命令会创建本地 Git 存储库中提交的任何补丁，更新配方以指向它们（或者根据指定的目标层创建 `.bbappend` 文件），然后重置配方，以便配方正常构建，而不是从工作区构建：

```

$ devtool finish recipe layer

> 完成配方层的开发工具
```

::: note
::: title

Note

> 注意
> :::

Any changes you want to turn into patches must be staged and committed within the local Git repository before you use the devtool finish command.

> 任何您想要转换为补丁的更改必须在使用 devtool finish 命令之前，先在本地 Git 存储库中暂存并提交。
> :::

Because there is no need to move the recipe, `devtool finish` either updates the original recipe in the original layer or the command creates a `.bbappend` file in a different layer as provided by layer. Any work you did in the `oe-local-files` directory is preserved in the original files next to the recipe during the `devtool finish` command.

> 由于不需要移动配方，`devtool finish` 命令既可以更新原始层中的原始配方，也可以根据层提供的不同层创建一个 `.bbappend` 文件。在 `devtool finish` 命令期间，`oe-local-files` 目录中的任何工作都会保留在配方旁边的原始文件中。

As a final process of the `devtool finish` command, the state of the standard layers and the upstream source is restored so that you can build the recipe from those areas rather than from the workspace.

> 作为 `devtool finish` 命令的最终过程，标准层的状态和上游源被恢复，因此您可以从这些区域而不是从工作区构建配方。

::: note
::: title

Note

> 注意
> :::

You can use the devtool reset command to put things back should you decide you do not want to proceed with your work. If you do use this command, realize that the source tree is preserved.

> 你可以使用 devtool 重置命令将事情恢复到原来的状态，如果你不想继续你的工作。如果你使用了这个命令，请记住源树是被保留的。
> :::

## Use `devtool upgrade` to Create a Version of the Recipe that Supports a Newer Version of the Software

The `devtool upgrade` command upgrades an existing recipe to that of a more up-to-date version found upstream. Throughout the life of software, recipes continually undergo version upgrades by their upstream publishers. You can use the `devtool upgrade` workflow to make sure your recipes you are using for builds are up-to-date with their upstream counterparts.

> `devtool upgrade` 命令可以将现有的配方升级到上游发现的更新版本。在软件的生命周期中，配方会不断地按照上游发布者的版本升级。您可以使用 `devtool upgrade` 工作流程来确保您用于构建的配方与上游对应的版本保持同步。

::: note
::: title
Note
:::

Several methods exist by which you can upgrade recipes -`devtool upgrade` happens to be one. You can read about all the methods by which you can upgrade recipes in the `dev-manual/upgrading-recipes:upgrading recipes`{.interpreted-text role="ref"} section of the Yocto Project Development Tasks Manual.

> 有几种方法可以用来升级食谱 - `devtool upgrade` 是其中之一。您可以在 Yocto 项目开发任务手册的 `dev-manual/upgrading-recipes:upgrading recipes`{.interpreted-text role="ref"}部分阅读有关升级食谱的所有方法。
> :::

The `devtool upgrade` command is flexible enough to allow you to specify source code revision and versioning schemes, extract code into or out of the `devtool` `devtool-the-workspace-layer-structure`{.interpreted-text role="ref"}, and work with any source file forms that the `bitbake-user-manual/bitbake-user-manual-fetching:fetchers`{.interpreted-text role="ref"} support.

> 命令 `devtool upgrade` 足够灵活，可以让您指定源代码修订版本和版本模式，将代码提取到 `devtool` 的 `devtool-the-workspace-layer-structure`{.interpreted-text role="ref"}中，或者从中提取出来，并且可以处理 `bitbake-user-manual/bitbake-user-manual-fetching:fetchers`{.interpreted-text role="ref"}支持的任何源文件形式。

The following diagram shows the common development flow used with the `devtool upgrade` command:

> 以下图表显示了使用“devtool upgrade”命令时常用的开发流程：

![image](figures/sdk-devtool-upgrade-flow.png){width="100.0%"}

1. *Initiate the Upgrade*: The top part of the flow shows the typical scenario by which you use the `devtool upgrade` command. The following conditions exist:

> 1. 启动升级：流程的顶部部分显示了使用 `devtool upgrade` 命令的典型场景。存在以下条件：

- The recipe exists in a local layer external to the `devtool` workspace.

> 配方存在于 `devtool` 工作空间之外的本地层。

- The source files for the new release exist in the same location pointed to by `SRC_URI`{.interpreted-text role="term"} in the recipe (e.g. a tarball with the new version number in the name, or as a different revision in the upstream Git repository).

> 源文件用于新版本存在于与配方中指向的同一位置（例如，一个带有新版本号的压缩包，或作为上游 Git 存储库中的不同修订版）。

A common situation is where third-party software has undergone a revision so that it has been upgraded. The recipe you have access to is likely in your own layer. Thus, you need to upgrade the recipe to use the newer version of the software:

> 常见情况是第三方软件已经进行了修订，以便进行升级。您可以访问的配方可能在您自己的层中。因此，您需要升级该配方以使用更新版本的软件：

```

$ devtool upgrade -V version recipe

> $ devtool升级 -V 版本配方
```

By default, the `devtool upgrade` command extracts source code into the `sources` directory in the `devtool-the-workspace-layer-structure`{.interpreted-text role="ref"}. If you want the code extracted to any other location, you need to provide the `srctree` positional argument with the command as follows:

> 默认情况下，`devtool upgrade` 命令将源代码提取到 `devtool-the-workspace-layer-structure`{.interpreted-text role="ref"}中的 `sources` 目录。如果您希望代码提取到任何其他位置，则需要使用以下命令提供 `srctree` 位置参数：

```

$ devtool upgrade -V version recipe srctree

> $ devtool升级 -V 版本配方srctree
```

::: note
::: title

Note

> 注意
> :::

In this example, the \"-V\" option specifies the new version. If you don\'t use \"-V\", the command upgrades the recipe to the latest version.

> 在这个例子中，“-V”选项指定新版本。如果不使用“-V”，命令将配方升级到最新版本。
> :::

If the source files pointed to by the `SRC_URI`{.interpreted-text role="term"} statement in the recipe are in a Git repository, you must provide the \"-S\" option and specify a revision for the software.

> 如果配方中的 `SRC_URI`{.interpreted-text role="term"}声明指向的源文件在 Git 存储库中，您必须提供“-S”选项并为软件指定一个修订版本。

Once `devtool` locates the recipe, it uses the `SRC_URI`{.interpreted-text role="term"} variable to locate the source code and any local patch files from other developers. The result is that the command sets up the source code, the new version of the recipe, and an append file all within the workspace.

> 一旦 `devtool` 定位到配方，它就会使用 `SRC_URI` 变量来定位来自其他开发者的源代码和任何本地补丁文件。结果是，该命令将源代码、新版本的配方和附加文件都设置在工作区中。

Additionally, if you have any non-patch local files (i.e. files referred to with `file://` entries in `SRC_URI`{.interpreted-text role="term"} statement excluding `*.patch/` or `*.diff`), these files are copied to an `oe-local-files` folder under the newly created source tree. Copying the files here gives you a convenient area from which you can modify the files. Any changes or additions you make to those files are incorporated into the build the next time you build the software just as are other changes you might have made to the source.

> 此外，如果您有任何非补丁本地文件（即在 SRC_URI 声明中使用 file://引用的，但不包括*.patch/或*.diff），则这些文件将被复制到新创建的源树下的 oe-local-files 文件夹中。将文件复制到此处可以方便地修改这些文件。您对这些文件所做的任何更改或添加都会与您可能对源代码所做的其他更改一样，在下次构建软件时被纳入构建中。

2. *Resolve any Conflicts created by the Upgrade*: Conflicts could happen after upgrading the software to a new version. Conflicts occur if your recipe specifies some patch files in `SRC_URI`{.interpreted-text role="term"} that conflict with changes made in the new version of the software. For such cases, you need to resolve the conflicts by editing the source and following the normal `git rebase` conflict resolution process.

> 解决升级带来的冲突：在升级软件到新版本后可能会出现冲突。如果你的配方中指定的 `SRC_URI`{.interpreted-text role="term"} 与新版本软件中做出的更改发生冲突，则会发生冲突。对于这种情况，您需要通过编辑源代码并遵循正常的 `git rebase` 冲突解决过程来解决冲突。

Before moving onto the next step, be sure to resolve any such conflicts created through use of a newer or different version of the software.

> 在进入下一步之前，请务必解决使用较新或不同版本软件造成的任何冲突。

3. *Build the Recipe or Rebuild the Image*: The next step you take depends on what you are going to do with the new code.

> 下一步你要怎么做取决于你要如何使用新的代码。

If you need to eventually move the build output to the target hardware, use the following `devtool` command:

> 如果你需要最终将构建输出移动到目标硬件，请使用以下 `devtool` 命令：

```

$ devtool build recipe

> $ devtool 编译配方
```

On the other hand, if you want an image to contain the recipe\'s packages from the workspace for immediate deployment onto a device (e.g. for testing purposes), you can use the `devtool build-image` command:

> 另一方面，如果您想要一个图像来包含来自工作区的配方包以便立即部署到设备（例如用于测试目的），您可以使用 `devtool build-image` 命令：

```

$ devtool build-image image

> $ devtool 编译镜像图像
```

4. *Deploy the Build Output*: When you use the `devtool build` command or `bitbake` to build your recipe, you probably want to see if the resulting build output works as expected on target hardware.

> 4. *部署构建输出*：当您使用 `devtool build` 命令或 `bitbake` 来构建菜谱时，您可能希望看到结果构建输出是否在目标硬件上按预期工作。

::: note
::: title

Note

> 注意
> :::

This step assumes you have a previously built image that is already either running in QEMU or running on actual hardware. Also, it is assumed that for deployment of the image to the target, SSH is installed in the image and if the image is running on real hardware that you have network access to and from your development machine.

> 这一步假设你已经构建了一个图像，该图像已经在 QEMU 中运行或在实际硬件上运行。此外，假设为了将图像部署到目标，图像中安装了 SSH，如果图像在真实硬件上运行，则可以从开发机器访问和访问网络。
> :::

You can deploy your build output to that target hardware by using the `devtool deploy-target` command: \$ devtool deploy-target recipe target The target is a live target machine running as an SSH server.

> 你可以使用 `devtool deploy-target` 命令将构建输出部署到目标硬件：\$ devtool deploy-target recipe target 目标是一台运行 SSH 服务器的实时目标机器。

You can, of course, also deploy the image you build using the `devtool build-image` command to actual hardware. However, `devtool` does not provide a specific command that allows you to do this.

> 你当然也可以使用 `devtool build-image` 命令部署构建的镜像到实际硬件上。但是，`devtool` 没有提供一个允许你这样做的特定命令。

5. *Finish Your Work With the Recipe*: The `devtool finish` command creates any patches corresponding to commits in the local Git repository, moves the new recipe to a more permanent layer, and then resets the recipe so that the recipe is built normally rather than from the workspace.

> 使用配方完成工作：`devtool finish` 命令会根据本地 Git 仓库的提交创建任何补丁，将新配方移动到更永久的层，然后重置配方，使得该配方正常构建，而不是从工作区构建。

Any work you did in the `oe-local-files` directory is preserved in the original files next to the recipe during the `devtool finish` command.

> 任何你在 `oe-local-files` 目录中做的工作都会在 `devtool finish` 命令期间保留在原始文件中。

If you specify a destination layer that is the same as the original source, then the old version of the recipe and associated files are removed prior to adding the new version:

> 如果您指定的目标图层与原始源相同，则在添加新版本之前，将会删除旧版本的配方及其相关文件：

```

$ devtool finish recipe layer

> 完成配方层的开发工具
```

::: note
::: title

Note

> 注意
> :::

Any changes you want to turn into patches must be committed to the Git repository in the source tree.

> 所有你想转换成补丁的更改都必须提交到源树中的 Git 存储库中。
> :::

As a final process of the `devtool finish` command, the state of the standard layers and the upstream source is restored so that you can build the recipe from those areas rather than the workspace.

> 作为 `devtool finish` 命令的最终过程，标准图层和上游源的状态被恢复，这样您可以从这些区域而不是工作区构建配方。

::: note
::: title

Note

> 注意
> :::

You can use the devtool reset command to put things back should you decide you do not want to proceed with your work. If you do use this command, realize that the source tree is preserved.

> 你可以使用 devtool 重置命令来把事情恢复原状，如果你决定不继续你的工作。如果你使用这个命令，要知道源树会被保存。
> :::

# A Closer Look at `devtool add`

The `devtool add` command automatically creates a recipe based on the source tree you provide with the command. Currently, the command has support for the following:

> 命令 `devtool add` 根据您提供的命令中的源树自动创建配方。目前，该命令支持以下内容：

- Autotools (`autoconf` and `automake`)
- CMake
- Scons
- `qmake`
- Plain `Makefile`
- Out-of-tree kernel module
- Binary package (i.e. \"-b\" option)
- Node.js module
- Python modules that use `setuptools` or `distutils`

Apart from binary packages, the determination of how a source tree should be treated is automatic based on the files present within that source tree. For example, if a `CMakeLists.txt` file is found, then the source tree is assumed to be using CMake and is treated accordingly.

> 除了二进制包，如何处理源树的确定是基于源树中存在的文件自动完成的。例如，如果找到 `CMakeLists.txt` 文件，则源树被假定使用 CMake 并相应地进行处理。

::: note
::: title
Note
:::

In most cases, you need to edit the automatically generated recipe in order to make it build properly. Typically, you would go through several edit and build cycles until the recipe successfully builds. Once the recipe builds, you could use possible further iterations to test the recipe on the target device.

> 在大多数情况下，您需要编辑自动生成的配方，以便使其正确构建。通常，您将经历多次编辑和构建周期，直到配方成功构建。一旦配方构建，您可以使用可能的进一步迭代来在目标设备上测试配方。
> :::

The remainder of this section covers specifics regarding how parts of the recipe are generated.

> 本节的其余部分涵盖了有关如何生成食谱各部分的具体内容。

## Name and Version

If you do not specify a name and version on the command line, `devtool add` uses various metadata within the source tree in an attempt to determine the name and version of the software being built. Based on what the tool determines, `devtool` sets the name of the created recipe file accordingly.

> 如果在命令行中没有指定名称和版本，`devtool add` 将尝试使用源树中的各种元数据来确定正在构建的软件的名称和版本。根据工具确定的内容，`devtool` 将相应地设置创建的配方文件的名称。

If `devtool` cannot determine the name and version, the command prints an error. For such cases, you must re-run the command and provide the name and version, just the name, or just the version as part of the command line.

> 如果 `devtool` 无法确定名称和版本，该命令会打印一个错误。 对于这种情况，您必须重新运行该命令，并将名称和版本，仅名称或仅版本作为命令行的一部分提供。

Sometimes the name or version determined from the source tree might be incorrect. For such a case, you must reset the recipe:

> 有时从源树中确定的名称或版本可能不正确。 对于这种情况，您必须重置配方：

```
$ devtool reset -n recipename
```

After running the `devtool reset` command, you need to run `devtool add` again and provide the name or the version.

> 在运行 `devtool reset` 命令后，你需要再次运行 `devtool add`，并提供名称或版本号。

## Dependency Detection and Mapping

The `devtool add` command attempts to detect build-time dependencies and map them to other recipes in the system. During this mapping, the command fills in the names of those recipes as part of the `DEPENDS`{.interpreted-text role="term"} variable within the recipe. If a dependency cannot be mapped, `devtool` places a comment in the recipe indicating such. The inability to map a dependency can result from naming not being recognized or because the dependency simply is not available. For cases where the dependency is not available, you must use the `devtool add` command to add an additional recipe that satisfies the dependency. Once you add that recipe, you need to update the `DEPENDS`{.interpreted-text role="term"} variable in the original recipe to include the new recipe.

> 命令 `devtool add` 尝试检测构建时的依赖关系，并将它们映射到系统中的其他配方中。在此映射过程中，该命令将这些配方的名称填充到配方中的 `DEPENDS` 变量中。如果无法映射依赖项，`devtool` 会在配方中放置一个注释，指示此种情况。无法映射依赖项的原因可能是命名无法识别，或者是因为依赖项根本不可用。对于依赖项不可用的情况，您必须使用 `devtool add` 命令添加另一个满足依赖关系的配方。添加该配方后，您需要更新原始配方中的 `DEPENDS` 变量，以包括新配方。

If you need to add runtime dependencies, you can do so by adding the following to your recipe:

> 如果你需要添加运行时依赖，可以通过在你的配方中添加以下内容来实现：

```
RDEPENDS:${PN} += "dependency1 dependency2 ..."
```

::: note
::: title
Note
:::

The devtool add command often cannot distinguish between mandatory and optional dependencies. Consequently, some of the detected dependencies might in fact be optional. When in doubt, consult the documentation or the configure script for the software the recipe is building for further details. In some cases, you might find you can substitute the dependency with an option that disables the associated functionality passed to the configure script.

> devtool 命令通常无法区分必需的依赖和可选的依赖。因此，某些检测到的依赖可能实际上是可选的。如有疑问，可参考该食谱正在构建的软件的文档或配置脚本以获取更多详细信息。在某些情况下，您可能会发现可以用传递给配置脚本的禁用相关功能的选项来替换依赖项。
> :::

## License Detection

The `devtool add` command attempts to determine if the software you are adding is able to be distributed under a common, open-source license. If so, the command sets the `LICENSE`{.interpreted-text role="term"} value accordingly. You should double-check the value added by the command against the documentation or source files for the software you are building and, if necessary, update that `LICENSE`{.interpreted-text role="term"} value.

> 命令 `devtool add` 试图确定您要添加的软件是否能够根据通用的开源许可证进行分发。 如果是这样，则该命令会相应地设置 `LICENSE`{.interpreted-text role="term"}值。 您应该双重检查该命令添加的值，并且根据您正在构建的软件的文档或源文件进行更新，如有必要，请更新 `LICENSE`{.interpreted-text role="term"}值。

The `devtool add` command also sets the `LIC_FILES_CHKSUM`{.interpreted-text role="term"} value to point to all files that appear to be license-related. Realize that license statements often appear in comments at the top of source files or within the documentation. In such cases, the command does not recognize those license statements. Consequently, you might need to amend the `LIC_FILES_CHKSUM`{.interpreted-text role="term"} variable to point to one or more of those comments if present. Setting `LIC_FILES_CHKSUM`{.interpreted-text role="term"} is particularly important for third-party software. The mechanism attempts to ensure correct licensing should you upgrade the recipe to a newer upstream version in future. Any change in licensing is detected and you receive an error prompting you to check the license text again.

> 命令 `devtool add` 也会将 `LIC_FILES_CHKSUM` 变量设置为指向所有看起来像是与许可相关的文件。要知道许可声明通常会出现在源文件的顶部注释中或文档中。在这种情况下，命令不会识别这些许可声明。因此，如果存在，您可能需要修改 `LIC_FILES_CHKSUM` 变量以指向一个或多个注释。对于第三方软件，设置 `LIC_FILES_CHKSUM` 尤为重要。该机制旨在确保在将来升级食谱到新的上游版本时，正确的许可。检测到任何许可变更，您将收到一个错误提示，要求您再次检查许可文本。

If the `devtool add` command cannot determine licensing information, `devtool` sets the `LICENSE`{.interpreted-text role="term"} value to \"CLOSED\" and leaves the `LIC_FILES_CHKSUM`{.interpreted-text role="term"} value unset. This behavior allows you to continue with development even though the settings are unlikely to be correct in all cases. You should check the documentation or source files for the software you are building to determine the actual license.

> 如果 `devtool add` 命令无法确定许可证信息，`devtool` 会将 `LICENSE` 值设置为“CLOSED”，并将 `LIC_FILES_CHKSUM` 值保持未设置状态。此行为可让您继续开发，即使设置在所有情况下都不太可能正确。您应该检查文档或构建的软件源文件，以确定实际许可证。

## Adding Makefile-Only Software

The use of Make by itself is very common in both proprietary and open-source software. Unfortunately, Makefiles are often not written with cross-compilation in mind. Thus, `devtool add` often cannot do very much to ensure that these Makefiles build correctly. It is very common, for example, to explicitly call `gcc` instead of using the `CC`{.interpreted-text role="term"} variable. Usually, in a cross-compilation environment, `gcc` is the compiler for the build host and the cross-compiler is named something similar to `arm-poky-linux-gnueabi-gcc` and might require arguments (e.g. to point to the associated sysroot for the target machine).

> 使用单独的 Make 很常见于专有软件和开源软件中。不幸的是，Makefiles 通常不是考虑跨编译的情况而编写的。因此，`devtool add` 通常无法做很多事情来确保这些 Makefiles 正确构建。例如，通常会显式调用 `gcc` 而不是使用 `CC` 变量。通常，在跨编译环境中，`gcc` 是构建主机的编译器，而跨编译器被命名为类似 `arm-poky-linux-gnueabi-gcc` 的名称，可能需要参数（例如指向目标机器的关联 sysroot）。

When writing a recipe for Makefile-only software, keep the following in mind:

> 在为仅使用 Makefile 的软件编写菜谱时，请记住以下几点：

- You probably need to patch the Makefile to use variables instead of hardcoding tools within the toolchain such as `gcc` and `g++`.

> 你可能需要修补 Makefile，以使用变量而不是在工具链中硬编码工具，如 `gcc` 和 `g++`。

- The environment in which Make runs is set up with various standard variables for compilation (e.g. `CC`{.interpreted-text role="term"}, `CXX`{.interpreted-text role="term"}, and so forth) in a similar manner to the environment set up by the SDK\'s environment setup script. One easy way to see these variables is to run the `devtool build` command on the recipe and then look in `oe-logs/run.do_compile`. Towards the top of this file, there is a list of environment variables that are set. You can take advantage of these variables within the Makefile.

> 在 Make 运行的环境中，会设置各种标准变量来进行编译（例如 `CC`、`CXX` 等），类似于 SDK 环境设置脚本所设置的环境。要查看这些变量，可以在配方上运行 `devtool build` 命令，然后查看 `oe-logs/run.do_compile` 文件。在此文件的顶部，有一个设置的环境变量列表。你可以在 Makefile 中利用这些变量。

- If the Makefile sets a default for a variable using \"=\", that default overrides the value set in the environment, which is usually not desirable. For this case, you can either patch the Makefile so it sets the default using the \"?=\" operator, or you can alternatively force the value on the `make` command line. To force the value on the command line, add the variable setting to `EXTRA_OEMAKE`{.interpreted-text role="term"} or `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"} within the recipe. Here is an example using `EXTRA_OEMAKE`{.interpreted-text role="term"}:

> 如果 Makefile 使用“=”设置变量的默认值，则该默认值会覆盖环境中设置的值，这通常是不可取的。在这种情况下，您可以修改 Makefile，使其使用“? =”操作符设置默认值，或者可以在 `make` 命令行上强制使用该值。要在命令行上强制使用该值，请将变量设置添加到菜谱中的 `EXTRA_OEMAKE`{.interpreted-text role="term"}或 `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"}中。以下是使用 `EXTRA_OEMAKE`{.interpreted-text role="term"}的示例：

```
EXTRA_OEMAKE += "'CC=${CC}' 'CXX=${CXX}'"
```

In the above example, single quotes are used around the variable settings as the values are likely to contain spaces because required default options are passed to the compiler.

> 在上面的例子中，由于需要将默认选项传递给编译器，因此在变量设置周围使用单引号，因为值可能包含空格。

- Hardcoding paths inside Makefiles is often problematic in a cross-compilation environment. This is particularly true because those hardcoded paths often point to locations on the build host and thus will either be read-only or will introduce contamination into the cross-compilation because they are specific to the build host rather than the target. Patching the Makefile to use prefix variables or other path variables is usually the way to handle this situation.

> 在交叉编译环境中，在 Makefile 中硬编码路径通常会出现问题。这是特别有可能的，因为这些硬编码的路径通常指向构建主机的位置，因此将是只读的，或者会在交叉编译中引入污染，因为它们是特定于构建主机而不是目标的。通常的做法是修补 Makefile 以使用前缀变量或其他路径变量。

- Sometimes a Makefile runs target-specific commands such as `ldconfig`. For such cases, you might be able to apply patches that remove these commands from the Makefile.

> 有时，Makefile 会运行特定目标的命令，例如 `ldconfig`。 对于这种情况，您可能可以应用补丁以从 Makefile 中删除这些命令。

## Adding Native Tools

Often, you need to build additional tools that run on the `Build Host`{.interpreted-text role="term"} as opposed to the target. You should indicate this requirement by using one of the following methods when you run `devtool add`:

> 经常需要构建在构建主机上运行的额外工具，而不是目标。当您运行 `devtool add` 时，应使用以下方法之一来表明此要求：

- Specify the name of the recipe such that it ends with \"-native\". Specifying the name like this produces a recipe that only builds for the build host.

> 指定食谱的名称以“-native”结尾。按这种方式指定名称可以生成仅用于构建主机的食谱。

- Specify the \"\--also-native\" option with the `devtool add` command. Specifying this option creates a recipe file that still builds for the target but also creates a variant with a \"-native\" suffix that builds for the build host.

> 使用 `devtool add` 命令指定"--also-native"选项。指定此选项会创建一个用于目标的配方文件，同时也会创建一个带有"-native"后缀的变体，用于构建主机。

::: note
::: title
Note
:::

If you need to add a tool that is shipped as part of a source tree that builds code for the target, you can typically accomplish this by building the native and target parts separately rather than within the same compilation process. Realize though that with the \"\--also-native\" option, you can add the tool using just one recipe file.

> 如果你需要添加一个作为源树一部分运送的工具来构建针对目标的代码，你通常可以通过单独构建本地和目标部分而不是在同一个编译过程中来完成。不过要知道，可以使用“--also-native”选项，只用一个配方文件就可以添加工具。
> :::

## Adding Node.js Modules

You can use the `devtool add` command two different ways to add Node.js modules: 1) Through `npm` and, 2) from a repository or local source.

> 你可以使用 `devtool add` 命令以两种不同的方式添加 Node.js 模块：1）通过 `npm`，2）从存储库或本地源。

Use the following form to add Node.js modules through `npm`:

> 使用以下表格通过 `npm` 添加 Node.js 模块：

```
$ devtool add "npm://registry.npmjs.org;name=forever;version=0.15.1"
```

The name and version parameters are mandatory. Lockdown and shrinkwrap files are generated and pointed to by the recipe in order to freeze the version that is fetched for the dependencies according to the first time. This also saves checksums that are verified on future fetches. Together, these behaviors ensure the reproducibility and integrity of the build.

> 名称和版本参数是必需的。锁定和收缩包文件是根据第一次抓取生成的，并指向食谱，以冻结依赖关系的版本。这也保存了将在未来抓取时验证的校验和。这些行为一起确保构建的可重现性和完整性。

::: note
::: title
Note
:::

- You must use quotes around the URL. The `devtool add` does not require the quotes, but the shell considers \";\" as a splitter between multiple commands. Thus, without the quotes, `devtool add` does not receive the other parts, which results in several \"command not found\" errors.

> 你必须在 URL 周围使用引号。`devtool add` 不需要引号，但是 shell 把“;”视为多个命令的分隔符。因此，如果没有引号，`devtool add` 就不会接收其他部分，这就导致出现多个“command not found”错误。

- In order to support adding Node.js modules, a `nodejs` recipe must be part of your SDK.
  :::

As mentioned earlier, you can also add Node.js modules directly from a repository or local source tree. To add modules this way, use `devtool add` in the following form:

> 正如之前提到的，您也可以直接从存储库或本地源树中添加 Node.js 模块。要以这种方式添加模块，请使用以下形式的 `devtool add`：

```
$ devtool add https://github.com/diversario/node-ssdp
```

In this example, `devtool` fetches the specified Git repository, detects the code as Node.js code, fetches dependencies using `npm`, and sets `SRC_URI`{.interpreted-text role="term"} accordingly.

> 在这个例子中，`devtool` 获取指定的 Git 存储库，将代码检测为 Node.js 代码，使用 `npm` 获取依赖项，并相应地设置 `SRC_URI`。

# Working With Recipes

When building a recipe using the `devtool build` command, the typical build progresses as follows:

> 使用 `devtool build` 命令构建配方时，典型的构建过程如下：

1. Fetch the source

> 取得源代码

2. Unpack the source

> 2. 解压源代码

3. Configure the source

> 3. 配置源

4. Compile the source

> 4. 编译源代码

5. Install the build output

> 5. 安装构建输出

6. Package the installed output

> 6. 封装安装的输出

For recipes in the workspace, fetching and unpacking is disabled as the source tree has already been prepared and is persistent. Each of these build steps is defined as a function (task), usually with a \"do\_\" prefix (e.g. `ref-tasks-fetch`{.interpreted-text role="ref"}, `ref-tasks-unpack`{.interpreted-text role="ref"}, and so forth). These functions are typically shell scripts but can instead be written in Python.

> 在工作空间中的食谱中，由于源代码树已经准备好并且是持久的，因此禁用了获取和解压缩。每个构建步骤都定义为一个函数（任务），通常以“do_”前缀开头（例如 `ref-tasks-fetch`{.interpreted-text role="ref"}，`ref-tasks-unpack`{.interpreted-text role="ref"}等）。这些函数通常是 shell 脚本，也可以用 Python 编写。

If you look at the contents of a recipe, you will see that the recipe does not include complete instructions for building the software. Instead, common functionality is encapsulated in classes inherited with the `inherit` directive. This technique leaves the recipe to describe just the things that are specific to the software being built. There is a `ref-classes-base`{.interpreted-text role="ref"} class that is implicitly inherited by all recipes and provides the functionality that most recipes typically need.

> 如果您查看配方内容，您会发现配方并不包含构建软件的完整说明。相反，常见功能被封装在使用“继承”指令继承的类中。这种技术使配方仅描述与要构建的软件相关的事情。有一个 `ref-classes-base`{.interpreted-text role="ref"}类隐式继承于所有配方，并提供大多数配方通常需要的功能。

The remainder of this section presents information useful when working with recipes.

> 本节剩余部分提供了在处理食谱时有用的信息。

## Finding Logs and Work Files

After the first run of the `devtool build` command, recipes that were previously created using the `devtool add` command or whose sources were modified using the `devtool modify` command contain symbolic links created within the source tree:

> 在第一次运行 `devtool build` 命令后，使用 `devtool add` 命令创建的菜谱，或者使用 `devtool modify` 命令修改源代码的菜谱，会在源代码树中创建符号链接。

- `oe-logs`: This link points to the directory in which log files and run scripts for each build step are created.

> `-` oe-logs`：此链接指向一个目录，其中包含每个构建步骤的日志文件和运行脚本。

- `oe-workdir`: This link points to the temporary work area for the recipe. The following locations under `oe-workdir` are particularly useful:

> `- oe-workdir`：此链接指向配方的临时工作区域。以下位于 `oe-workdir` 下的位置尤其有用：

- `image/`: Contains all of the files installed during the `ref-tasks-install`{.interpreted-text role="ref"} stage. Within a recipe, this directory is referred to by the expression `${``D`{.interpreted-text role="term"}`}`.

> - `image/`：包含在 `ref-tasks-install`{.interpreted-text role="ref"}阶段安装的所有文件。在食谱中，这个目录用表达式 `${``D`{.interpreted-text role="term"}`}` 来引用。

- `sysroot-destdir/`: Contains a subset of files installed within `ref-tasks-install`{.interpreted-text role="ref"} that have been put into the shared sysroot. For more information, see the \"`dev-manual/new-recipe:sharing files between recipes`{.interpreted-text role="ref"}\" section.

> - `sysroot-destdir/`：包含在 `ref-tasks-install`{.interpreted-text role="ref"}中安装的文件的子集，它们已被放入共享的 sysroot 中。有关更多信息，请参见“`dev-manual/new-recipe:sharing files between recipes`{.interpreted-text role="ref"}”部分。

- `packages-split/`: Contains subdirectories for each package produced by the recipe. For more information, see the \"`sdk-manual/extensible:packaging`{.interpreted-text role="ref"}\" section.

> - `packages-split/`：包含由配方生成的每个包的子目录。有关详细信息，请参阅“sdk-manual/extensible：packaging”部分。

You can use these links to get more information on what is happening at each build step.

> 您可以使用这些链接获取每个构建步骤发生的更多信息。

## Setting Configure Arguments

If the software your recipe is building uses GNU autoconf, then a fixed set of arguments is passed to it to enable cross-compilation plus any extras specified by `EXTRA_OECONF`{.interpreted-text role="term"} or `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"} set within the recipe. If you wish to pass additional options, add them to `EXTRA_OECONF`{.interpreted-text role="term"} or `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"}. Other supported build tools have similar variables (e.g. `EXTRA_OECMAKE`{.interpreted-text role="term"} for CMake, `EXTRA_OESCONS`{.interpreted-text role="term"} for Scons, and so forth). If you need to pass anything on the `make` command line, you can use `EXTRA_OEMAKE`{.interpreted-text role="term"} or the `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"} variables to do so.

> 如果您的配方正在构建的软件使用 GNU autoconf，那么将向其传递一组固定的参数以启用跨编译，以及由 `EXTRA_OECONF`{.interpreted-text role="term"}或 `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"}在配方中指定的任何额外参数。如果您想传递其他选项，请将它们添加到 `EXTRA_OECONF`{.interpreted-text role="term"}或 `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"}中。其他支持的构建工具也有类似的变量（例如，CMake 的 `EXTRA_OECMAKE`{.interpreted-text role="term"}，Scons 的 `EXTRA_OESCONS`{.interpreted-text role="term"}等等）。如果您需要在 `make` 命令行上传递任何内容，可以使用 `EXTRA_OEMAKE`{.interpreted-text role="term"}或 `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"}变量来执行此操作。

You can use the `devtool configure-help` command to help you set the arguments listed in the previous paragraph. The command determines the exact options being passed, and shows them to you along with any custom arguments specified through `EXTRA_OECONF`{.interpreted-text role="term"} or `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"}. If applicable, the command also shows you the output of the configure script\'s \"\--help\" option as a reference.

> 你可以使用 `devtool configure-help` 命令来帮助你设置前面段落中列出的参数。该命令确定传递的确切选项，并将它们显示给你，同时还显示通过 `EXTRA_OECONF` 或 `PACKAGECONFIG_CONFARGS` 指定的任何自定义参数。如果适用，该命令还会向你显示配置脚本的“--help”选项的输出，作为参考。

## Sharing Files Between Recipes

Recipes often need to use files provided by other recipes on the `Build Host`{.interpreted-text role="term"}. For example, an application linking to a common library needs access to the library itself and its associated headers. The way this access is accomplished within the extensible SDK is through the sysroot. There is one sysroot per \"machine\" for which the SDK is being built. In practical terms, this means there is a sysroot for the target machine, and a sysroot for the build host.

> 食谱通常需要使用由“构建主机”上的其他食谱提供的文件。例如，链接到公共库的应用程序需要访问库本身及其关联的头文件。在可扩展 SDK 中实现此访问的方式是通过 sysroot。每个“机器”都有一个 sysroot，用于构建 SDK。实际上，这意味着有一个 sysroot 用于目标机器，还有一个 sysroot 用于构建主机。

Recipes should never write files directly into the sysroot. Instead, files should be installed into standard locations during the `ref-tasks-install`{.interpreted-text role="ref"} task within the `${``D`{.interpreted-text role="term"}`}` directory. A subset of these files automatically goes into the sysroot. The reason for this limitation is that almost all files that go into the sysroot are cataloged in manifests in order to ensure they can be removed later when a recipe is modified or removed. Thus, the sysroot is able to remain free from stale files.

> 食谱不应该直接将文件写入 sysroot。相反，应在 `${D}` 目录中的 `ref-tasks-install` 任务期间将文件安装到标准位置。其中一小部分文件会自动进入 sysroot。这个限制的原因是几乎所有进入 sysroot 的文件都会被记录在清单中，以确保以后可以在修改或删除食谱时删除它们。因此，sysroot 可以保持清洁，没有过时的文件。

## Packaging

Packaging is not always particularly relevant within the extensible SDK. However, if you examine how build output gets into the final image on the target device, it is important to understand packaging because the contents of the image are expressed in terms of packages and not recipes.

> 打包并不总是特别相关于可扩展的 SDK。然而，如果您检查构建输出如何进入目标设备上的最终映像，了解打包就很重要，因为映像的内容表示为包而不是食谱。

During the `ref-tasks-package`{.interpreted-text role="ref"} task, files installed during the `ref-tasks-install`{.interpreted-text role="ref"} task are split into one main package, which is almost always named the same as the recipe, and into several other packages. This separation exists because not all of those installed files are useful in every image. For example, you probably do not need any of the documentation installed in a production image. Consequently, for each recipe the documentation files are separated into a `-doc` package. Recipes that package software containing optional modules or plugins might undergo additional package splitting as well.

> 在 `ref-tasks-package`{.interpreted-text role="ref"}任务期间，在 `ref-tasks-install`{.interpreted-text role="ref"}任务期间安装的文件被分割成一个主要的包，它几乎总是以和食谱相同的名字，以及其他几个包。这种分离存在，因为这些安装的文件并不是每个映像都有用。例如，您可能不需要在生产映像中安装的任何文档。因此，对于每个食谱，文档文件被分割成一个 `-doc` 包。打包含有可选模块或插件的软件的食谱可能会进行额外的包分割。

After building a recipe, you can see where files have gone by looking in the `oe-workdir/packages-split` directory, which contains a subdirectory for each package. Apart from some advanced cases, the `PACKAGES`{.interpreted-text role="term"} and `FILES`{.interpreted-text role="term"} variables controls splitting. The `PACKAGES`{.interpreted-text role="term"} variable lists all of the packages to be produced, while the `FILES`{.interpreted-text role="term"} variable specifies which files to include in each package by using an override to specify the package. For example, `FILES:${PN}` specifies the files to go into the main package (i.e. the main package has the same name as the recipe and `${``PN`{.interpreted-text role="term"}`}` evaluates to the recipe name). The order of the `PACKAGES`{.interpreted-text role="term"} value is significant. For each installed file, the first package whose `FILES`{.interpreted-text role="term"} value matches the file is the package into which the file goes. Both the `PACKAGES`{.interpreted-text role="term"} and `FILES`{.interpreted-text role="term"} variables have default values. Consequently, you might find you do not even need to set these variables in your recipe unless the software the recipe is building installs files into non-standard locations.

> 在构建配方后，您可以通过查看 `oe-workdir/packages-split` 目录来查看文件去向，该目录包含一个子目录，每个子目录对应一个包。除了一些高级情况外，`PACKAGES`{.interpreted-text role="term"}和 `FILES`{.interpreted-text role="term"}变量控制分割。 `PACKAGES`{.interpreted-text role="term"}变量列出要生成的所有包，而 `FILES`{.interpreted-text role="term"}变量通过使用覆盖来指定要包含在每个包中的文件。例如，`FILES:${PN}` 指定要放入主包（即主包与配方同名，`${``PN`{.interpreted-text role="term"}`}` 评估为配方名称）的文件。 `PACKAGES`{.interpreted-text role="term"}的顺序是重要的。对于每个安装的文件，`FILES`{.interpreted-text role="term"}值与该文件匹配的第一个包就是该文件所在的包。 `PACKAGES`{.interpreted-text role="term"}和 `FILES`{.interpreted-text role="term"}变量都有默认值。因此，除非配方构建的软件将文件安装到非标准位置，否则您可能发现您甚至不需要在配方中设置这些变量。

# Restoring the Target Device to its Original State

If you use the `devtool deploy-target` command to write a recipe\'s build output to the target, and you are working on an existing component of the system, then you might find yourself in a situation where you need to restore the original files that existed prior to running the `devtool deploy-target` command. Because the `devtool deploy-target` command backs up any files it overwrites, you can use the `devtool undeploy-target` command to restore those files and remove any other files the recipe deployed. Consider the following example:

> 如果您使用 ` devtool deploy-target` 命令将食谱的构建输出写入目标，并且您正在编辑系统的现有组件，那么您可能会遇到需要恢复运行 ` devtool deploy-target` 命令之前存在的原始文件的情况。由于 ` devtool deploy-target` 命令会备份所有被覆盖的文件，因此您可以使用 ` devtool undeploy-target` 命令恢复这些文件并删除食谱部署的任何其他文件。请考虑以下示例：

```
$ devtool undeploy-target lighttpd root@192.168.7.2
```

If you have deployed multiple applications, you can remove them all using the \"-a\" option thus restoring the target device to its original state:

> 如果您已部署了多个应用程序，您可以使用“-a”选项将它们全部删除，从而将目标设备恢复到原始状态：

```
$ devtool undeploy-target -a root@192.168.7.2
```

Information about files deployed to the target as well as any backed up files are stored on the target itself. This storage, of course, requires some additional space on the target machine.

> 在目标机器上部署的文件信息以及备份的文件都存储在目标机器上。当然，这种存储需要在目标机器上额外的空间。

::: note
::: title
Note
:::

The devtool deploy-target and devtool undeploy-target commands do not currently interact with any package management system on the target device (e.g. RPM or OPKG). Consequently, you should not intermingle devtool deploy-target and package manager operations on the target device. Doing so could result in a conflicting set of files.

> devtool deploy-target 和 devtool undeploy-target 命令当前不会与目标设备上的任何包管理系统（例如 RPM 或 OPKG）交互。因此，您不应在目标设备上混合使用 devtool deploy-target 和包管理器操作。这样做可能会导致一组冲突的文件。
> :::

# Installing Additional Items Into the Extensible SDK

Out of the box the extensible SDK typically only comes with a small number of tools and libraries. A minimal SDK starts mostly empty and is populated on-demand. Sometimes you must explicitly install extra items into the SDK. If you need these extra items, you can first search for the items using the `devtool search` command. For example, suppose you need to link to libGL but you are not sure which recipe provides libGL. You can use the following command to find out:

> SDK 可扩展性的默认配置只包含少量的工具和库。最小的 SDK 几乎是空的，需要按需加载。有时候你必须显式的安装额外的项目到 SDK 中。如果你需要这些额外的项目，你可以使用 `devtool search` 命令来搜索它们。例如，假设你需要链接到 libGL，但是你不确定哪个食谱提供了 libGL。你可以使用以下命令来查找：

```
$ devtool search libGL mesa
A free implementation of the OpenGL API
```

Once you know the recipe (i.e. `mesa` in this example), you can install it.

> 一旦你知道配方（例如这里的 `mesa`），你就可以安装它。

## When using the extensible SDK directly in a Yocto build

In this scenario, the Yocto build tooling, e.g. `bitbake` is directly accessible to build additional items, and it can simply be executed directly:

> 在这种情况下，Yocto 构建工具，例如 `bitbake`，可以直接访问以构建其他项目，可以直接执行：

> \$ bitbake mesa \$ bitbake build-sysroots

## When using a standalone installer for the Extensible SDK

> \$ devtool sdk-install mesa

By default, the `devtool sdk-install` command assumes the item is available in pre-built form from your SDK provider. If the item is not available and it is acceptable to build the item from source, you can add the \"-s\" option as follows:

> 默认情况下，`devtool sdk-install` 命令假定该项目可以从 SDK 提供商处以预构建形式获得。如果该项目不可用且可以从源代码构建，您可以添加“-s”选项，如下所示：

```
$ devtool sdk-install -s mesa
```

It is important to remember that building the item from source takes significantly longer than installing the pre-built artifact. Also, if there is no recipe for the item you want to add to the SDK, you must instead add the item using the `devtool add` command.

> 重要的是要记住，从源构建项目比安装预构建的项目需要更长的时间。此外，如果没有要添加到 SDK 的项目的配方，则必须使用 `devtool add` 命令来添加项目。

# Applying Updates to an Installed Extensible SDK

If you are working with an installed extensible SDK that gets occasionally updated (e.g. a third-party SDK), then you will need to manually \"pull down\" the updates into the installed SDK.

> 如果你正在使用一个可扩展的安装式 SDK，它会不时地更新（例如第三方 SDK），那么你需要手动将更新“拉下来”到已安装的 SDK 中。

To update your installed SDK, use `devtool` as follows:

> 更新已安装的 SDK，请使用 `devtool`，如下所示：

```
$ devtool sdk-update
```

The previous command assumes your SDK provider has set the default update URL for you through the `SDK_UPDATE_URL`{.interpreted-text role="term"} variable as described in the \"`sdk-manual/appendix-customizing:Providing Updates to the Extensible SDK After Installation`{.interpreted-text role="ref"}\" section. If the SDK provider has not set that default URL, you need to specify it yourself in the command as follows: \$ devtool sdk-update path_to_update_directory

> 上一个命令假设您的 SDK 提供商已经通过“SDK_UPDATE_URL”变量（参见“sdk-manual/appendix-customizing：在安装后向可扩展 SDK 提供更新”部分）为您设置了默认更新 URL。 如果 SDK 提供商没有设置该默认 URL，则需要在命令中自行指定，如下所示：$ devtool sdk-update path_to_update_directory

::: note
::: title
Note
:::

The URL needs to point specifically to a published SDK and not to an SDK installer that you would download and install.

> URL 需要指向一个已发布的 SDK，而不是一个你可以下载并安装的 SDK 安装程序。
> :::

# Creating a Derivative SDK With Additional Components

You might need to produce an SDK that contains your own custom libraries. A good example would be if you were a vendor with customers that use your SDK to build their own platform-specific software and those customers need an SDK that has custom libraries. In such a case, you can produce a derivative SDK based on the currently installed SDK fairly easily by following these steps:

> 您可能需要生成一个包含自己定制库的 SDK。一个好的例子是，如果您是一个供应商，客户使用您的 SDK 来构建自己的特定平台的软件，而这些客户需要一个拥有定制库的 SDK。在这种情况下，您可以很容易地基于当前安装的 SDK 生成一个衍生的 SDK，方法是：

1. If necessary, install an extensible SDK that you want to use as a base for your derivative SDK.

> 如果需要，安装一个你想要用作派生 SDK 基础的可扩展 SDK。

2. Source the environment script for the SDK.

> 设置 SDK 的环境脚本。

3. Add the extra libraries or other components you want by using the `devtool add` command.

> 使用 `devtool add` 命令添加您想要的额外库或其他组件。

4. Run the `devtool build-sdk` command.

> 运行 `devtool build-sdk` 命令。

The previous steps take the recipes added to the workspace and construct a new SDK installer that contains those recipes and the resulting binary artifacts. The recipes go into their own separate layer in the constructed derivative SDK, which leaves the workspace clean and ready for users to add their own recipes.

> 前面的步骤将添加到工作区的菜谱提取出来，构建一个新的 SDK 安装程序，其中包含这些菜谱和最终的二进制文件。菜谱被放到构建的衍生 SDK 的单独层中，这样工作区就干净整洁，用户可以添加自己的菜谱。
