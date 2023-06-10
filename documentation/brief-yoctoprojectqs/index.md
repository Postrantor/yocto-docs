---
tip: translate by openai@2023-06-09 19:22:08
...
---
title: Yocto Project Quick Build
---
# Welcome!


This short document steps you through the process for a typical image build using the Yocto Project. The document also introduces how to configure a build for specific hardware. You will use Yocto Project to build a reference embedded OS called Poky.

> 这篇简短的文档将引导您完成使用Yocto项目进行典型图像构建的过程。文档还介绍了如何为特定硬件配置构建。您将使用Yocto项目构建一个叫Poky的参考嵌入式操作系统。

::: note
::: title
Note
:::


- The examples in this paper assume you are using a native Linux system running a recent Ubuntu Linux distribution. If the machine you want to use Yocto Project on to build an image (`Build Host`{.interpreted-text role="term"}) is not a native Linux system, you can still perform these steps by using CROss PlatformS (CROPS) and setting up a Poky container. See the `dev-manual/start:setting up to use cross platforms (crops)`{.interpreted-text role="ref"} section in the Yocto Project Development Tasks Manual for more information.

> 本文中的示例假设您正在使用运行最新Ubuntu Linux发行版的本机Linux系统。如果您想要使用Yocto Project构建镜像（Build Host）的机器不是本机Linux系统，您仍然可以通过使用CROss PlatformS（CROPS）并设置Poky容器来执行这些步骤。有关更多信息，请参见Yocto Project Development Tasks Manual中的“dev-manual / start：设置使用跨平台（crops）”部分。

- You may use version 2 of Windows Subsystem For Linux (WSL 2) to set up a build host using Windows 10 or later, Windows Server 2019 or later. See the `dev-manual/start:setting up to use windows subsystem for linux (wsl 2)`{.interpreted-text role="ref"} section in the Yocto Project Development Tasks Manual for more information.

> 您可以使用Windows 10或更高版本、Windows Server 2019或更高版本的Windows子系统（WSL 2）版本2来设置构建主机。有关更多信息，请参阅Yocto项目开发任务手册中的“dev-manual/start：使用Windows子系统（WSL 2）”部分。
  :::

If you want more conceptual or background information on the Yocto Project, see the `/overview-manual/index`{.interpreted-text role="doc"}.

# Compatible Linux Distribution

Make sure your `Build Host`{.interpreted-text role="term"} meets the following requirements:


- At least &MIN_DISK_SPACE; Gbytes of free disk space, though much more will help to run multiple builds and increase performance by reusing build artifacts.

> 至少需要&MIN_DISK_SPACE;G字节的可用磁盘空间，虽然更多的空间可以帮助运行多个构建，并通过重复使用构建成果来提高性能。

- At least &MIN_RAM; Gbytes of RAM, though a modern modern build host with as much RAM and as many CPU cores as possible is strongly recommended to maximize build performance.

> 至少需要&MIN_RAM; G字节的内存，但强烈推荐使用尽可能多的内存和多核CPU的现代构建主机，以最大限度地提高构建性能。

- Runs a supported Linux distribution (i.e. recent releases of Fedora, openSUSE, CentOS, Debian, or Ubuntu). For a list of Linux distributions that support the Yocto Project, see the `ref-manual/system-requirements:supported linux distributions`{.interpreted-text role="ref"} section in the Yocto Project Reference Manual. For detailed information on preparing your build host, see the `dev-manual/start:preparing the build host`{.interpreted-text role="ref"} section in the Yocto Project Development Tasks Manual.

> 运行一个支持的Linux发行版（例如Fedora、openSUSE、CentOS、Debian或Ubuntu的最新版本）。要了解支持Yocto项目的Linux发行版，请参阅Yocto项目参考手册中的“参考手册/系统要求：支持的Linux发行版”部分。有关准备构建主机的详细信息，请参阅Yocto项目开发任务手册中的“开发手册/开始：准备构建主机”部分。
- - Git &MIN_GIT_VERSION; or greater

  > - tar &MIN_TAR_VERSION; or greater
  > - Python &MIN_PYTHON_VERSION; or greater.
  > - gcc &MIN_GCC_VERSION; or greater.
  > - GNU make &MIN_MAKE_VERSION; or greater
  >


If your build host does not meet any of these three listed version requirements, you can take steps to prepare the system so that you can still use the Yocto Project. See the `ref-manual/system-requirements:required git, tar, python, make and gcc versions`{.interpreted-text role="ref"} section in the Yocto Project Reference Manual for information.

> 如果您的构建主机不符合这三个列出的版本要求，您可以采取步骤来准备系统，以便仍然可以使用Yocto项目。有关信息，请参阅Yocto项目参考手册中的ref-manual/system-requirements：所需的git、tar、python、make和gcc版本部分。

# Build Host Packages

You must install essential host packages on your build host. The following command installs the host packages based on an Ubuntu distribution:

```
$ sudo apt install &UBUNTU_HOST_PACKAGES_ESSENTIAL;
```

::: note
::: title
Note
:::


For host package requirements on all supported Linux distributions, see the `ref-manual/system-requirements:required packages for the build host`{.interpreted-text role="ref"} section in the Yocto Project Reference Manual.

> 在 Yocto 项目参考手册中，参见“系统要求：构建主机所需的软件包”部分，以了解所有支持的 Linux 发行版上的主机软件包要求。
:::

# Use Git to Clone Poky


Once you complete the setup instructions for your machine, you need to get a copy of the Poky repository on your build host. Use the following commands to clone the Poky repository.

> 完成机器的设置指令后，您需要在构建主机上获取Poky存储库的副本。使用以下命令克隆Poky存储库。

```shell
$ git clone git://git.yoctoproject.org/poky
Cloning into 'poky'...
remote: Counting
objects: 432160, done. remote: Compressing objects: 100%
(102056/102056), done. remote: Total 432160 (delta 323116), reused
432037 (delta 323000) Receiving objects: 100% (432160/432160), 153.81 MiB | 8.54 MiB/s, done.
Resolving deltas: 100% (323116/323116), done.
Checking connectivity... done.
```


Go to :yocto_wiki:[Releases wiki page \</Releases\>]{.title-ref}, and choose a release codename (such as `&DISTRO_NAME_NO_CAP;`), corresponding to either the latest stable release or a Long Term Support release.

> 请访问:yocto_wiki:[发布wiki页面 \</Releases\>]{.title-ref}，然后选择一个发布代号（例如`&DISTRO_NAME_NO_CAP;`），它对应于最新的稳定版本或长期支持版本。

Then move to the `poky` directory and take a look at existing branches:

```shell
$ cd poky
$ git branch -a
.
.
.
remotes/origin/HEAD -> origin/master
remotes/origin/dunfell
remotes/origin/dunfell-next
.
.
.
remotes/origin/gatesgarth
remotes/origin/gatesgarth-next
.
.
.
remotes/origin/master
remotes/origin/master-next
.
.
.
```

For this example, check out the `&DISTRO_NAME_NO_CAP;` branch based on the `&DISTRO_NAME;` release:

```shell
$ git checkout -t origin/&DISTRO_NAME_NO_CAP; -b my-&DISTRO_NAME_NO_CAP;
Branch 'my-&DISTRO_NAME_NO_CAP;' set up to track remote branch '&DISTRO_NAME_NO_CAP;' from 'origin'.
Switched to a new branch 'my-&DISTRO_NAME_NO_CAP;'
```


The previous Git checkout command creates a local branch named `my-&DISTRO_NAME_NO_CAP;`. The files available to you in that branch exactly match the repository\'s files in the `&DISTRO_NAME_NO_CAP;` release branch.

> 上一次Git检出命令创建了一个名为`my-&DISTRO_NAME_NO_CAP;`的本地分支。在该分支中提供给您的文件与存储库中的`&DISTRO_NAME_NO_CAP;`发行分支中的文件完全匹配。

Note that you can regularly type the following command in the same directory to keep your local files in sync with the release branch:

```shell
$ git pull
```


For more options and information about accessing Yocto Project related repositories, see the `dev-manual/start:locating yocto project source files`{.interpreted-text role="ref"} section in the Yocto Project Development Tasks Manual.

> 对于获取Yocto项目相关存储库的更多选项和信息，请参阅Yocto项目开发任务手册中的dev-manual/start：定位Yocto项目源文件部分。

# Building Your Image

Use the following steps to build your image. The build process creates an entire Linux distribution, including the toolchain, from source.

::: note
::: title
Note
:::


- If you are working behind a firewall and your build host is not set up for proxies, you could encounter problems with the build process when fetching source code (e.g. fetcher failures or Git failures).

> 如果您正在防火墙后面工作，而您的构建主机没有设置代理，您在获取源代码时（例如获取器失败或Git失败）可能会遇到构建过程中的问题。

- If you do not know your proxy settings, consult your local network infrastructure resources and get that information. A good starting point could also be to check your web browser settings. Finally, you can find more information on the \":yocto_wiki:[Working Behind a Network Proxy \</Working_Behind_a\_Network_Proxy\>]{.title-ref}\" page of the Yocto Project Wiki.

> 如果您不知道您的代理设置，请咨询您的本地网络基础架构资源并获取该信息。一个好的起点也可以是检查您的网页浏览器设置。最后，您可以在Yocto项目维基网站上的“工作在网络代理后面”页面上找到更多信息。
  :::


1. **Initialize the Build Environment:** From within the `poky` directory, run the ``ref-manual/structure:\`\`oe-init-build-env\`\` ``{.interpreted-text role="ref"} environment setup script to define Yocto Project\'s build environment on your build host.

> 从`poky`目录中运行`ref-manual/structure:oe-init-build-env`环境设置脚本，以在构建主机上定义Yocto Project的构建环境。

   ```shell
   $ cd poky
   $ source oe-init-build-env
   You had no conf/local.conf file. This configuration file has therefore been
   created for you with some default values. You may wish to edit it to, for
   example, select a different MACHINE (target hardware). See conf/local.conf
   for more information as common configuration options are commented.

   You had no conf/bblayers.conf file. This configuration file has therefore
   been created for you with some default values. To add additional metadata
   layers into your configuration please add entries to conf/bblayers.conf.

   The Yocto Project has extensive documentation about OE including a reference
   manual which can be found at:
       https://docs.yoctoproject.org

   For more information about OpenEmbedded see their website:
       https://www.openembedded.org/

   ### Shell environment set up for builds. ###

   You can now run 'bitbake <target>'

   Common targets are:
       core-image-minimal
       core-image-full-cmdline
       core-image-sato
       core-image-weston
       meta-toolchain
       meta-ide-support

   You can also run generated QEMU images with a command like 'runqemu qemux86-64'

   Other commonly useful commands are:
    - 'devtool' and 'recipetool' handle common recipe tasks
    - 'bitbake-layers' handles common layer tasks
    - 'oe-pkgdata-util' handles common target package tasks
   ```


   Among other things, the script creates the `Build Directory`{.interpreted-text role="term"}, which is `build` in this case and is located in the `Source Directory`{.interpreted-text role="term"}. After the script runs, your current working directory is set to the `Build Directory`{.interpreted-text role="term"}. Later, when the build completes, the `Build Directory`{.interpreted-text role="term"} contains all the files created during the build.

> 脚本还会创建“构建目录”，在这种情况下它的名字是“build”，位于“源目录”中。脚本运行后，当前的工作目录就会被设置为“构建目录”。稍后，当构建完成之后，“构建目录”中就会包含构建过程中创建的所有文件。

2. **Examine Your Local Configuration File:** When you set up the build environment, a local configuration file named `local.conf` becomes available in a `conf` subdirectory of the `Build Directory`{.interpreted-text role="term"}. For this example, the defaults are set to build for a `qemux86` target, which is suitable for emulation. The package manager used is set to the RPM package manager.

> 检查本地配置文件：当您设置构建环境时，会在构建目录的conf子目录中提供一个名为local.conf的本地配置文件。对于本例，默认设置是为qemux86目标构建，这适用于模拟。使用的包管理器设置为RPM包管理器。

   ::: tip
   ::: title
   Tip
   :::


   You can significantly speed up your build and guard against fetcher failures by using `overview-manual/concepts:shared state cache`{.interpreted-text role="ref"} mirrors and enabling `overview-manual/concepts:hash equivalence`{.interpreted-text role="ref"}. This way, you can use pre-built artifacts rather than building them. This is relevant only when your network and the server that you use can download these artifacts faster than you would be able to build them.

> 您可以通过使用镜像和启用哈希等价性来显著加快构建速度并防止获取器故障。这样，您可以使用预先构建的工件而不是构建它们。这仅在您的网络和您使用的服务器可以比您构建它们更快地下载这些工件时才有意义。

   To use such mirrors, uncomment the below lines in your `conf/local.conf` file in the `Build Directory`{.interpreted-text role="term"}:

   ```
   BB_SIGNATURE_HANDLER = "OEEquivHash"
   BB_HASHSERVE = "auto"
   BB_HASHSERVE_UPSTREAM = "hashserv.yocto.io:8687"
   SSTATE_MIRRORS ?= "file://.* https://sstate.yoctoproject.org/all/PATH;downloadfilename=PATH"
   ```

   :::
3. **Start the Build:** Continue with the following command to build an OS image for the target, which is `core-image-sato` in this example:

   ```shell
   $ bitbake core-image-sato
   ```


   For information on using the `bitbake` command, see the `overview-manual/concepts:bitbake`{.interpreted-text role="ref"} section in the Yocto Project Overview and Concepts Manual, or see `bitbake-user-manual/bitbake-user-manual-intro:the bitbake command`{.interpreted-text role="ref"} in the BitBake User Manual.

> 要了解如何使用`bitbake`命令，请参阅Yocto项目概述和概念手册中的“overview-manual/concepts:bitbake”部分，或参阅BitBake用户手册中的“bitbake-user-manual/bitbake-user-manual-intro:the bitbake command”部分。

4. **Simulate Your Image Using QEMU:** Once this particular image is built, you can start QEMU, which is a Quick EMUlator that ships with the Yocto Project:

> 4. **使用QEMU模拟您的镜像：**一旦构建了这个特定的镜像，您就可以启动QEMU，它是Yocto Project附带的快速EMUlator：

   ```shell
   $ runqemu qemux86-64
   ```


   If you want to learn more about running QEMU, see the `dev-manual/qemu:using the quick emulator (qemu)`{.interpreted-text role="ref"} chapter in the Yocto Project Development Tasks Manual.

> 如果你想了解更多关于运行 QEMU 的信息，请参考 Yocto 项目开发任务手册中的 dev-manual/qemu:using the quick emulator (qemu) 章节。
5. **Exit QEMU:** Exit QEMU by either clicking on the shutdown icon or by typing `Ctrl-C` in the QEMU transcript window from which you evoked QEMU.

# Customizing Your Build for Specific Hardware


So far, all you have done is quickly built an image suitable for emulation only. This section shows you how to customize your build for specific hardware by adding a hardware layer into the Yocto Project development environment.

> 到目前为止，您所做的只是快速构建一个仅适用于模拟的映像。本节将向您展示如何通过将硬件层添加到Yocto项目开发环境中来为特定硬件定制构建。


In general, layers are repositories that contain related sets of instructions and configurations that tell the Yocto Project what to do. Isolating related metadata into functionally specific layers facilitates modular development and makes it easier to reuse the layer metadata.

> 一般来说，层是包含相关指令和配置集的存储库，它们告诉Yocto项目该做什么。将相关元数据隔离到功能特定的层中，可以促进模块化开发，并使得重用层元数据变得更加容易。

::: note
::: title
Note
:::

By convention, layer names start with the string \"meta-\".
:::

Follow these steps to add a hardware layer:


1. **Find a Layer:** Many hardware layers are available. The Yocto Project :yocto\_[git:%60Source](git:%60Source) Repositories \<\>[ has many hardware layers. This example adds the \`meta-altera \<[https://github.com/kraj/meta-altera](https://github.com/kraj/meta-altera)\>]{.title-ref}\_\_ hardware layer.

> 找到一层：可用的硬件层很多。Yocto Project :yocto\_[git:%60Source](git:%60Source) Repositories \<\>[有很多硬件层。这个例子添加了`meta-altera \<[https://github.com/kraj/meta-altera](https://github.com/kraj/meta-altera)\>]{.title-ref}\_\_ 硬件层。

2. **Clone the Layer:** Use Git to make a local copy of the layer on your machine. You can put the copy in the top level of the copy of the Poky repository created earlier:

> 2. **克隆层：**使用Git在您的机器上创建层的本地副本。您可以将副本放在先前创建的Poky存储库的顶层：

   ```shell
   $ cd poky
   $ git clone https://github.com/kraj/meta-altera.git
   Cloning into 'meta-altera'...
   remote: Counting objects: 25170, done.
   remote: Compressing objects: 100% (350/350), done.
   remote: Total 25170 (delta 645), reused 719 (delta 538), pack-reused 24219
   Receiving objects: 100% (25170/25170), 41.02 MiB | 1.64 MiB/s, done.
   Resolving deltas: 100% (13385/13385), done.
   Checking connectivity... done.
   ```


   The hardware layer is now available next to other layers inside the Poky reference repository on your build host as `meta-altera` and contains all the metadata needed to support hardware from Altera, which is owned by Intel.

> 现在在您的构建主机上的Poky参考存储库中，硬件层面旁边还有其他层次，作为`meta-altera`，包含所有支持Intel旗下Altera硬件所需的元数据。

   ::: note
   ::: title
   Note
   :::


   It is recommended for layers to have a branch per Yocto Project release. Please make sure to checkout the layer branch supporting the Yocto Project release you\'re using.

> 建议每个Yocto Project发行版本都有一个分支。请确保检出支持您正在使用的Yocto Project发行版本的层分支。
   :::

3. **Change the Configuration to Build for a Specific Machine:** The `MACHINE`{.interpreted-text role="term"} variable in the `local.conf` file specifies the machine for the build. For this example, set the `MACHINE`{.interpreted-text role="term"} variable to `cyclone5`. These configurations are used: [https://github.com/kraj/meta-altera/blob/master/conf/machine/cyclone5.conf](https://github.com/kraj/meta-altera/blob/master/conf/machine/cyclone5.conf).

> 3. **更改配置以构建特定机器：** `local.conf` 文件中的 `MACHINE`{.interpreted-text role="term"} 变量指定了构建的机器。 对于此示例，将 `MACHINE`{.interpreted-text role="term"} 变量设置为 `cyclone5`。 使用以下配置：[https://github.com/kraj/meta-altera/blob/master/conf/machine/cyclone5.conf](https://github.com/kraj/meta-altera/blob/master/conf/machine/cyclone5.conf)。

   ::: note
   ::: title
   Note
   :::

   See the \"Examine Your Local Configuration File\" step earlier for more information on configuring the build.
   :::

4. **Add Your Layer to the Layer Configuration File:** Before you can use a layer during a build, you must add it to your `bblayers.conf` file, which is found in the `Build Directory`{.interpreted-text role="term"} `conf` directory.

> 在构建过程中使用层之前，您必须将其添加到`bblayers.conf`文件中，该文件位于`构建目录`的`conf`目录中。

   Use the `bitbake-layers add-layer` command to add the layer to the configuration file:

   ```shell
   $ cd poky/build
   $ bitbake-layers add-layer ../meta-altera
   NOTE: Starting bitbake server...
   Parsing recipes: 100% |##################################################################| Time: 0:00:32
   Parsing of 918 .bb files complete (0 cached, 918 parsed). 1401 targets,
   123 skipped, 0 masked, 0 errors.
   ```


   You can find more information on adding layers in the ``dev-manual/layers:adding a layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"} section.

> 你可以在``dev-manual/layers:adding a layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}部分找到更多关于添加层的信息。


Completing these steps has added the `meta-altera` layer to your Yocto Project development environment and configured it to build for the `cyclone5` machine.

> 完成这些步骤后，您已将`meta-altera`层添加到Yocto Project开发环境中，并配置其为`cyclone5`机器进行构建。

::: note
::: title
Note
:::


The previous steps are for demonstration purposes only. If you were to attempt to build an image for the `cyclone5` machine, you should read the Altera `README`.

> 上一步仅用于演示目的。如果您想要为`cyclone5`机器构建图像，应该阅读Altera的`README`。
:::

# Creating Your Own General Layer


Maybe you have an application or specific set of behaviors you need to isolate. You can create your own general layer using the `bitbake-layers create-layer` command. The tool automates layer creation by setting up a subdirectory with a `layer.conf` configuration file, a `recipes-example` subdirectory that contains an `example.bb` recipe, a licensing file, and a `README`.

> 也许你需要隔离一个应用或特定的行为集。你可以使用`bitbake-layers create-layer`命令创建自己的通用层。该工具通过设置一个包含`layer.conf`配置文件、`recipes-example`子目录（包含`example.bb`配方）、许可文件和`README`的子目录来自动创建层。

The following commands run the tool to create a layer named `meta-mylayer` in the `poky` directory:

```shell
$ cd poky
$ bitbake-layers create-layer meta-mylayer
NOTE: Starting bitbake server...
Add your new layer with 'bitbake-layers add-layer meta-mylayer'
```


For more information on layers and how to create them, see the ``dev-manual/layers:creating a general layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"} section in the Yocto Project Development Tasks Manual.

> 要了解更多关于层的信息以及如何创建它们，请参阅Yocto Project开发任务手册中的“dev-manual / layers：使用“bitbake-layers”脚本创建一个通用层”部分。

# Where To Go Next


Now that you have experienced using the Yocto Project, you might be asking yourself \"What now?\". The Yocto Project has many sources of information including the website, wiki pages, and user manuals:

> 现在你已经体验了使用Yocto项目，你可能会问自己“接下来怎么办？” Yocto项目有很多信息来源，包括网站、维基页面和用户手册：


- **Website:** The :yocto_home:[Yocto Project Website \<\>]{.title-ref} provides background information, the latest builds, breaking news, full development documentation, and access to a rich Yocto Project Development Community into which you can tap.

> 网站：Yocto项目网站提供背景信息、最新版本、突发新闻、完整的开发文档以及进入丰富的Yocto项目开发社区的访问权限。

- **Video Seminar:** The [Introduction to the Yocto Project and BitBake, Part 1](https://youtu.be/yuE7my3KOpo) and [Introduction to the Yocto Project and BitBake, Part 2](https://youtu.be/iZ05TTyzGHk) videos offer a video seminar introducing you to the most important aspects of developing a custom embedded Linux distribution with the Yocto Project.

> - **视频研讨会：**[Yocto项目和BitBake介绍第1部分](https://youtu.be/yuE7my3KOpo)和[Yocto项目和BitBake介绍第2部分](https://youtu.be/iZ05TTyzGHk)视频研讨会为您介绍使用Yocto项目开发自定义嵌入式Linux发行版最重要的方面。

- **Yocto Project Overview and Concepts Manual:** The `/overview-manual/index`{.interpreted-text role="doc"} is a great place to start to learn about the Yocto Project. This manual introduces you to the Yocto Project and its development environment. The manual also provides conceptual information for various aspects of the Yocto Project.

> **Yocto 项目概览和概念手册：** `/overview-manual/index`{.interpreted-text role="doc"}是了解Yocto项目的绝佳起点。本手册将向您介绍Yocto项目及其开发环境。手册还提供了Yocto项目的各个方面的概念信息。

- **Yocto Project Wiki:** The :yocto_wiki:[Yocto Project Wiki \<\>]{.title-ref} provides additional information on where to go next when ramping up with the Yocto Project, release information, project planning, and QA information.

> - **Yocto 项目维基：** :yocto_wiki:[Yocto 项目维基 \<\>]{.title-ref} 提供了有关使用 Yocto 项目进行构建后的下一步操作、发布信息、项目规划和质量保证信息的其他信息。

- **Yocto Project Mailing Lists:** Related mailing lists provide a forum for discussion, patch submission and announcements. There are several mailing lists grouped by topic. See the `ref-manual/resources:mailing lists`{.interpreted-text role="ref"} section in the Yocto Project Reference Manual for a complete list of Yocto Project mailing lists.

> - **Yocto Project 邮件列表：** 相关邮件列表提供了讨论、补丁提交和公告的论坛。它们按主题分组。详情请参阅Yocto Project 参考手册中的“ref-manual/resources:mailing lists”部分，以获取Yocto Project 邮件列表的完整列表。

- **Comprehensive List of Links and Other Documentation:** The `ref-manual/resources:links and related documentation`{.interpreted-text role="ref"} section in the Yocto Project Reference Manual provides a comprehensive list of all related links and other user documentation.

> **全面的链接和其他文档列表：**Yocto项目参考手册中的`ref-manual/resources:links and related documentation`{.interpreted-text role="ref"}部分提供了所有相关链接和其他用户文档的全面列表。
