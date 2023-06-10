---
tip: translate by openai@2023-06-07 22:40:35
...
---
title: System Requirements
--------------------------

Welcome to the Yocto Project Reference Manual. This manual provides reference information for the current release of the Yocto Project, and is most effectively used after you have an understanding of the basics of the Yocto Project. The manual is neither meant to be read as a starting point to the Yocto Project, nor read from start to finish. Rather, use this manual to find variable definitions, class descriptions, and so forth as needed during the course of using the Yocto Project.

> 欢迎来到 Yocto 项目参考手册。本手册提供了 Yocto 项目当前版本的参考信息，最有效地使用是在您对 Yocto 项目的基础知识有了一定了解之后。本手册既不是作为 Yocto 项目的起点，也不是从头到尾阅读的，而是在使用 Yocto 项目过程中，根据需要查找变量定义、类描述等。

For introductory information on the Yocto Project, see the :yocto_home:[Yocto Project Website \<\>]{.title-ref} and the \"`overview-manual/development-environment:the yocto project development environment`{.interpreted-text role="ref"}\" chapter in the Yocto Project Overview and Concepts Manual.

> 对 Yocto 项目的介绍信息，请参见：Yocto 项目网站 <> 和 Yocto 项目概述与概念手册中的“开发环境：Yocto 项目开发环境”章节。

If you want to use the Yocto Project to quickly build an image without having to understand concepts, work through the `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"} document. You can find \"how-to\" information in the `/dev-manual/index`{.interpreted-text role="doc"}. You can find Yocto Project overview and conceptual information in the `/overview-manual/index`{.interpreted-text role="doc"}.

> 如果你想使用 Yocto Project 快速构建镜像而不必理解概念，请参考 `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"}文档。你可以在 `/dev-manual/index`{.interpreted-text role="doc"}中找到“如何”信息。你可以在 `/overview-manual/index`{.interpreted-text role="doc"}中找到 Yocto Project 的概览和概念信息。

::: note
::: title
Note
:::

For more information about the Yocto Project Documentation set, see the `ref-manual/resources:links and related documentation`{.interpreted-text role="ref"} section.

> 如果想要了解更多关于 Yocto Project 文档集的信息，请参阅 ref-manual/resources:links 和相关文档部分。
> :::

# Minimum Free Disk Space

To build an image such as `core-image-sato` for the `qemux86-64` machine, you need a system with at least &MIN_DISK_SPACE; Gbytes of free disk space. However, much more disk space will be necessary to build more complex images, to run multiple builds and to cache build artifacts, improving build efficiency.

> 要为 `qemux86-64` 机器构建一个像 `core-image-sato` 这样的镜像，你需要一个至少有&MIN_DISK_SPACE; G 字节的空闲磁盘空间的系统。但是，要构建更复杂的镜像，运行多个构建和缓存构建的工件，以提高构建效率，需要更多的磁盘空间。

If you have a shortage of disk space, see the \"`/dev-manual/disk-space`{.interpreted-text role="doc"}\" section of the Development Tasks Manual.

# Minimum System RAM {#system-requirements-minimum-ram}

You will manage to build an image such as `core-image-sato` for the `qemux86-64` machine with as little as &MIN_RAM; Gbytes of RAM on an old system with 4 CPU cores, but your builds will be much faster on a system with as much RAM and as many CPU cores as possible.

> 你可以在一台老旧的 4 核 CPU 系统上用最少&MIN_RAM;G 字节的内存来构建 `core-image-sato` 这样的镜像，但如果你有更多的内存和更多的 CPU 核心，你的构建速度会更快。

# Supported Linux Distributions {#system-requirements-supported-distros}

Currently, the Yocto Project is supported on the following distributions:

- Ubuntu 18.04 (LTS)
- Ubuntu 20.04 (LTS)
- Ubuntu 22.04 (LTS)
- Fedora 36
- Fedora 37
- AlmaLinux 8.7
- AlmaLinux 9.1
- Debian GNU/Linux 11.x (Bullseye)
- OpenSUSE Leap 15.3
- OpenSUSE Leap 15.4

::: note
::: title
Note
:::

- While the Yocto Project Team attempts to ensure all Yocto Project releases are one hundred percent compatible with each officially supported Linux distribution, you may still encounter problems that happen only with a specific distribution.

> 尽管 Yocto 项目团队尽力确保所有 Yocto 项目发行版完全兼容所有正式支持的 Linux 发行版，但您仍可能会遇到仅发生在特定发行版上的问题。

- Yocto Project releases are tested against the stable Linux distributions in the above list. The Yocto Project should work on other distributions but validation is not performed against them.

> Yocto 项目的发布版本已经在上述列表中的稳定的 Linux 发行版上进行了测试。Yocto 项目应该可以在其他发行版上运行，但不会对它们进行验证。

- In particular, the Yocto Project does not support and currently has no plans to support rolling-releases or development distributions due to their constantly changing nature. We welcome patches and bug reports, but keep in mind that our priority is on the supported platforms listed above.

> 特别是，Yocto 项目不支持并且目前没有计划支持滚动发行版或开发分发版，因为它们的性质不断变化。我们欢迎补丁和错误报告，但请记住我们的优先级是上述支持的平台。

- If your Linux distribution is not in the above list, we recommend to get the `buildtools`{.interpreted-text role="term"} or `buildtools-extended`{.interpreted-text role="term"} tarballs containing the host tools required by your Yocto Project release, typically by running `scripts/install-buildtools` as explained in the \"`system-requirements-buildtools`{.interpreted-text role="ref"}\" section.

> 如果您的 Linux 发行版不在上述列表中，我们建议您获取含有您的 Yocto Project 发行版所需的主机工具的 `buildtools`{.interpreted-text role="term"} 或 `buildtools-extended`{.interpreted-text role="term"} 归档文件，通常通过运行 `scripts/install-buildtools` 来实现，详情参见“`system-requirements-buildtools`{.interpreted-text role="ref"}”部分。

- You may use Windows Subsystem For Linux v2 to set up a build host using Windows 10 or later, or Windows Server 2019 or later, but validation is not performed against build hosts using WSL 2.

> 您可以使用 Windows Subsystem For Linux v2 在 Windows 10 或更高版本或 Windows Server 2019 或更高版本上设置构建主机，但不会对使用 WSL 2 的构建主机进行验证。

See the `dev-manual/start:setting up to use windows subsystem for linux (wsl 2)`{.interpreted-text role="ref"} section in the Yocto Project Development Tasks Manual for more information.

> 请参阅 Yocto Project 开发任务手册中的“dev-manual/start：使用 Windows 子系统（WSL 2）”部分，以获取更多信息。

- If you encounter problems, please go to :yocto_bugs:[Yocto Project Bugzilla \<\>]{.title-ref} and submit a bug. We are interested in hearing about your experience. For information on how to submit a bug, see the Yocto Project :yocto_wiki:[Bugzilla wiki page \</Bugzilla_Configuration_and_Bug_Tracking\>]{.title-ref} and the \"`dev-manual/changes:submitting a defect against the yocto project`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 如果您遇到问题，请访问：Yocto Project Bugzilla <>，并提交一个错误。我们期待您的体验。有关提交错误的信息，请参阅 Yocto Project Bugzilla wiki 页面 <Bugzilla_Configuration_and_Bug_Tracking> 和 Yocto Project 开发任务手册中的“提交针对 Yocto Project 的缺陷”部分。
> :::

# Required Packages for the Build Host

The list of packages you need on the host development system can be large when covering all build scenarios using the Yocto Project. This section describes required packages according to Linux distribution and function.

> 需要在主机开发系统上安装的软件包列表可能很长，当使用 Yocto Project 覆盖所有构建场景时。本节根据 Linux 发行版和功能描述所需的软件包。

## Ubuntu and Debian {#ubuntu-packages}

Here are the packages needed to build an image on a headless system with a supported Ubuntu or Debian Linux distribution:

```
$ sudo apt install &UBUNTU_HOST_PACKAGES_ESSENTIAL;
```

::: note
::: title
Note
:::

- If your build system has the `oss4-dev` package installed, you might experience QEMU build failures due to the package installing its own custom `/usr/include/linux/soundcard.h` on the Debian system. If you run into this situation, try either of these solutions:

> 如果您的构建系统安装了 `oss4-dev` 软件包，您可能会在 Debian 系统上由于该软件包安装自己的 `/usr/include/linux/soundcard.h` 而遇到 QEMU 构建失败的情况。如果遇到这种情况，请尝试以下任一解决方案：

```
$ sudo apt build-dep qemu
$ sudo apt remove oss4-dev
```

:::

Here are the packages needed to build Project documentation manuals:

```
$ sudo apt install make python3-pip inkscape texlive-latex-extra
&PIP3_HOST_PACKAGES_DOC;
```

## Fedora Packages

Here are the packages needed to build an image on a headless system with a supported Fedora Linux distribution:

```
$ sudo dnf install &FEDORA_HOST_PACKAGES_ESSENTIAL;
```

Here are the packages needed to build Project documentation manuals:

```
$ sudo dnf install make python3-pip which inkscape texlive-fncychap
&PIP3_HOST_PACKAGES_DOC;
```

## openSUSE Packages

Here are the packages needed to build an image on a headless system with a supported openSUSE distribution:

```
$ sudo zypper install &OPENSUSE_HOST_PACKAGES_ESSENTIAL;
```

Here are the packages needed to build Project documentation manuals:

```
$ sudo zypper install make python3-pip which inkscape texlive-fncychap
&PIP3_HOST_PACKAGES_DOC;
```

## AlmaLinux Packages

Here are the packages needed to build an image on a headless system with a supported AlmaLinux distribution:

```
$ sudo dnf install &ALMALINUX_HOST_PACKAGES_ESSENTIAL;
```

::: note
::: title
Note
:::

- Extra Packages for Enterprise Linux (i.e. `epel-release`) is a collection of packages from Fedora built on RHEL/CentOS for easy installation of packages not included in enterprise Linux by default. You need to install these packages separately.

> 针对企业 Linux 的额外软件包（即 `epel-release`）是从 Fedora 构建在 RHEL / CentOS 上的一组软件包，可以轻松安装企业 Linux 默认不包含的软件包。您需要单独安装这些软件包。

- The `PowerTools/CRB` repo provides additional packages such as `rpcgen` and `texinfo`.
- The `makecache` command consumes additional Metadata from `epel-release`.
  :::

Here are the packages needed to build Project documentation manuals:

```
$ sudo dnf install make python3-pip which inkscape texlive-fncychap
&PIP3_HOST_PACKAGES_DOC;
```

# Required Git, tar, Python, make and gcc Versions {#system-requirements-buildtools}

In order to use the build system, your host development system must meet the following version requirements for Git, tar, and Python:

- Git &MIN_GIT_VERSION; or greater
- tar &MIN_TAR_VERSION; or greater
- Python &MIN_PYTHON_VERSION; or greater
- GNU make &MIN_MAKE_VERSION; or greater

If your host development system does not meet all these requirements, you can resolve this by installing a `buildtools`{.interpreted-text role="term"} tarball that contains these tools. You can either download a pre-built tarball or use BitBake to build one.

> 如果您的主机开发系统不满足所有这些要求，您可以通过安装包含这些工具的 `buildtools`{.interpreted-text role="term"}归档文件来解决此问题。您可以下载预先构建的归档文件，也可以使用 BitBake 来构建一个。

In addition, your host development system must meet the following version requirement for gcc:

- gcc &MIN_GCC_VERSION; or greater

If your host development system does not meet this requirement, you can resolve this by installing a `buildtools-extended`{.interpreted-text role="term"} tarball that contains additional tools, the equivalent of the Debian/Ubuntu `build-essential` package.

> 如果您的主机开发系统不满足此要求，您可以通过安装包含额外工具的 `buildtools-extended` tarball 来解决此问题，这相当于 Debian/Ubuntu 的 `build-essential` 软件包。

For systems with a broken make version (e.g. make 4.2.1 without patches) but where the rest of the host tools are usable, you can use the `buildtools-make`{.interpreted-text role="term"} tarball instead.

> 对于具有损坏的 make 版本（例如未补丁的 make 4.2.1）但其余主机工具可用的系统，您可以使用 `buildtools-make`{.interpreted-text role="term"}压缩包。

In the sections that follow, three different methods will be described for installing the `buildtools`{.interpreted-text role="term"}, `buildtools-extended`{.interpreted-text role="term"} or `buildtools-make`{.interpreted-text role="term"} toolset.

> 在接下来的部分中，将描述三种不同的方法来安装 `buildtools`{.interpreted-text role="term"}、`buildtools-extended`{.interpreted-text role="term"}或 `buildtools-make`{.interpreted-text role="term"}工具集。

## Installing a Pre-Built `buildtools` Tarball with `install-buildtools` script

The `install-buildtools` script is the easiest of the three methods by which you can get these tools. It downloads a pre-built `buildtools`{.interpreted-text role="term"} installer and automatically installs the tools for you:

> `install-buildtools` 脚本是您可以获得这些工具的三种方法中最简单的一种。它会下载一个预构建的 `buildtools` 安装程序，并自动为您安装工具：

1. Execute the `install-buildtools` script. Here is an example:

   ```
   $ cd poky
   $ scripts/install-buildtools \
     --without-extended-buildtools \
     --base-url &YOCTO_DL_URL;/releases/yocto \
     --release yocto-&DISTRO; \
     --installer-version &DISTRO;
   ```

   During execution, the `buildtools`{.interpreted-text role="term"} tarball will be downloaded, the checksum of the download will be verified, the installer will be run for you, and some basic checks will be run to make sure the installation is functional.

> 在执行期间，会下载 `buildtools`{.interpreted-text role="term"}压缩包，校验下载的校验和，为您运行安装程序，并进行一些基本检查以确保安装功能正常。

To avoid the need of `sudo` privileges, the `install-buildtools` script will by default tell the installer to install in:

```
/path/to/poky/buildtools
```

If your host development system needs the additional tools provided in the `buildtools-extended`{.interpreted-text role="term"} tarball, you can instead execute the `install-buildtools` script with the default parameters:

> 如果您的主机开发系统需要 `buildtools-extended` 压缩包中提供的其他工具，您可以使用默认参数执行 `install-buildtools` 脚本：

```
$ cd poky
$ scripts/install-buildtools
```

Alternatively if your host development system has a broken `make` version such that you only need a known good version of `make`, you can use the `--make-only` option:

> 如果您的主机开发系统的 `make` 版本有问题，以至于您只需要一个已知的良好版本的 `make`，您可以使用 `--make-only` 选项：

```
$ cd poky
$ scripts/install-buildtools --make-only
```

2. Source the tools environment setup script by using a command like the following:

   ```
   $ source /path/to/poky/buildtools/environment-setup-x86_64-pokysdk-linux
   ```

   After you have sourced the setup script, the tools are added to `PATH` and any other environment variables required to run the tools are initialized. The results are working versions versions of Git, tar, Python and `chrpath`. And in the case of the `buildtools-extended`{.interpreted-text role="term"} tarball, additional working versions of tools including `gcc`, `make` and the other tools included in `packagegroup-core-buildessential`.

> 经过源脚本设置后，工具将添加到 PATH 和其他运行工具所需的环境变量中。结果是 Git、tar、Python 和 chrpath 的可用版本。对于 buildtools-extended 压缩包，还包括 gcc、make 和 packagegroup-core-buildessential 中包含的其他工具的可用版本。

## Downloading a Pre-Built `buildtools` Tarball

If you would prefer not to use the `install-buildtools` script, you can instead download and run a pre-built `buildtools`{.interpreted-text role="term"} installer yourself with the following steps:

> 如果您不想使用 `install-buildtools` 脚本，您可以自行下载并运行预先构建的 `buildtools` 安装程序，并按照以下步骤操作：

1. Go to :yocto_dl:[/releases/yocto/yocto-&DISTRO;/buildtools/]{.title-ref}, locate and download the `.sh` file corresponding to your host architecture and to `buildtools`{.interpreted-text role="term"}, `buildtools-extended`{.interpreted-text role="term"} or `buildtools-make`{.interpreted-text role="term"}.

> 1. 前往:yocto_dl:[/releases/yocto/yocto-&DISTRO;/buildtools/]{.title-ref}，找到并下载与您的主机架构和 `buildtools`{.interpreted-text role="term"}、`buildtools-extended`{.interpreted-text role="term"}或 `buildtools-make`{.interpreted-text role="term"}对应的 `.sh` 文件。

2. Execute the installation script. Here is an example for the traditional installer:

   ```
   $ sh ~/Downloads/x86_64-buildtools-nativesdk-standalone-&DISTRO;.sh
   ```

   Here is an example for the extended installer:

   ```
   $ sh ~/Downloads/x86_64-buildtools-extended-nativesdk-standalone-&DISTRO;.sh
   ```

   An example for the make-only installer:

   ```
   $ sh ~/Downloads/x86_64-buildtools-make-nativesdk-standalone-&DISTRO;.sh
   ```

   During execution, a prompt appears that allows you to choose the installation directory. For example, you could choose the following: `/home/your-username/buildtools`

> 在执行过程中，会出现一个提示，让您选择安装目录。例如，您可以选择以下：`/home/your-username/buildtools`

3. As instructed by the installer script, you will have to source the tools environment setup script:

   ```
   $ source /home/your_username/buildtools/environment-setup-x86_64-pokysdk-linux
   ```

   After you have sourced the setup script, the tools are added to `PATH` and any other environment variables required to run the tools are initialized. The results are working versions versions of Git, tar, Python and `chrpath`. And in the case of the `buildtools-extended`{.interpreted-text role="term"} tarball, additional working versions of tools including `gcc`, `make` and the other tools included in `packagegroup-core-buildessential`.

> 在您源自设置脚本后，工具将添加到 PATH 和任何其他运行工具所需的环境变量中进行初始化。结果是 Git、tar、Python 和 chrpath 的可用版本。在 buildtools-extended 压缩包的情况下，还提供了包括 gcc、make 和 packagegroup-core-buildessential 中包含的其他工具的附加可用版本。

## Building Your Own `buildtools` Tarball

Building and running your own `buildtools`{.interpreted-text role="term"} installer applies only when you have a build host that can already run BitBake. In this case, you use that machine to build the `.sh` file and then take steps to transfer and run it on a machine that does not meet the minimal Git, tar, and Python (or gcc) requirements.

> 构建和运行自己的 `buildtools` 安装程序仅适用于您具有可以运行 BitBake 的构建主机的情况。在这种情况下，您使用该机器构建 `.sh` 文件，然后采取步骤将其传输并在不满足最低 Git、tar 和 Python（或 gcc）要求的机器上运行它。

Here are the steps to take to build and run your own `buildtools`{.interpreted-text role="term"} installer:

1. On the machine that is able to run BitBake, be sure you have set up your build environment with the setup script (`structure-core-script`{.interpreted-text role="ref"}).

> 在可以运行 BitBake 的机器上，请确保使用 setup 脚本（`structure-core-script`{.interpreted-text role="ref"}）设置好构建环境。

2. Run the BitBake command to build the tarball:

   ```
   $ bitbake buildtools-tarball
   ```

   or to build the extended tarball:

   ```
   $ bitbake buildtools-extended-tarball
   ```

   or to build the make-only tarball:

   ```
   $ bitbake buildtools-make-tarball
   ```

   ::: note
   ::: title
   Note
   :::

   The `SDKMACHINE`{.interpreted-text role="term"} variable in your `local.conf` file determines whether you build tools for a 32-bit or 64-bit system.

> 变量 `SDKMACHINE` 在您的 `local.conf` 文件中决定您是为 32 位系统还是 64 位系统构建工具。
> :::

Once the build completes, you can find the `.sh` file that installs the tools in the `tmp/deploy/sdk` subdirectory of the `Build Directory`{.interpreted-text role="term"}. The installer file has the string \"buildtools\" or \"buildtools-extended\" in the name.

> 一旦构建完成，您可以在构建目录的 tmp/deploy/sdk 子目录中找到安装工具的 `.sh` 文件。安装程序文件的名称中包含“buildtools”或“buildtools-extended”字符串。

3. Transfer the `.sh` file from the build host to the machine that does not meet the Git, tar, or Python (or gcc) requirements.
4. On this machine, run the `.sh` file to install the tools. Here is an example for the traditional installer:

   ```
   $ sh ~/Downloads/x86_64-buildtools-nativesdk-standalone-&DISTRO;.sh
   ```

   For the extended installer:

   ```
   $ sh ~/Downloads/x86_64-buildtools-extended-nativesdk-standalone-&DISTRO;.sh
   ```

   And for the make-only installer:

   ```
   $ sh ~/Downloads/x86_64-buildtools-make-nativesdk-standalone-&DISTRO;.sh
   ```

   During execution, a prompt appears that allows you to choose the installation directory. For example, you could choose the following: `/home/your_username/buildtools`

> 在执行过程中，会出现一个提示，允许您选择安装目录。例如，您可以选择以下目录：`/home/your_username/buildtools`

5. Source the tools environment setup script by using a command like the following:

   ```
   $ source /home/your_username/buildtools/environment-setup-x86_64-poky-linux
   ```

   After you have sourced the setup script, the tools are added to `PATH` and any other environment variables required to run the tools are initialized. The results are working versions versions of Git, tar, Python and `chrpath`. And in the case of the `buildtools-extended`{.interpreted-text role="term"} tarball, additional working versions of tools including `gcc`, `make` and the other tools included in `packagegroup-core-buildessential`.

> 在您源自设置脚本之后，工具将添加到 `PATH` 和其他运行工具所需的环境变量中初始化。结果是 Git、tar、Python 和 `chrpath` 的可用版本。而对于 `buildtools-extended`{.interpreted-text role="term"} tarball，还包括 `gcc`、`make` 以及 `packagegroup-core-buildessential` 中包含的其他工具的可用版本。
