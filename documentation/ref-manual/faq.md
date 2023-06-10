---
tip: translate by openai@2023-06-10 10:02:23
...
---
title: FAQ
----------

::: contents
:::

# General questions

## How does Poky differ from OpenEmbedded?

The term `Poky` refers to the specific reference build system that the Yocto Project provides. Poky is based on `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"} and `BitBake`{.interpreted-text role="term"}. Thus, the generic term used here for the build system is the \"OpenEmbedded build system.\" Development in the Yocto Project using Poky is closely tied to OpenEmbedded, with changes always being merged to OE-Core or BitBake first before being pulled back into Poky. This practice benefits both projects immediately.

> 术语“Poky”指的是 Yocto Project 提供的特定参考构建系统。 Poky 基于 OpenEmbedded-Core（OE-Core）和 BitBake。因此，这里使用的构建系统的通用术语是“OpenEmbedded 构建系统”。使用 Poky 开发 Yocto Project 与 OpenEmbedded 密切相关，始终将更改合并到 OE-Core 或 BitBake，然后再拉回 Poky。此做法立即使两个项目受益。

## How can you claim Poky / OpenEmbedded-Core is stable?

There are three areas that help with stability;

- The Yocto Project team keeps `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"} small and focused, containing around 830 recipes as opposed to the thousands available in other OpenEmbedded community layers. Keeping it small makes it easy to test and maintain.

> 团队维护的 OpenEmbedded-Core（OE-Core）保持小而集中，只包含大约 830 个配方，而不是其他 OpenEmbedded 社区层中的数千个配方。保持小规模有助于测试和维护。

- The Yocto Project team runs manual and automated tests using a small, fixed set of reference hardware as well as emulated targets.
- The Yocto Project uses an :yocto_ab:[autobuilder \<\>]{.title-ref}, which provides continuous build and integration tests.

## Are there any products built using the OpenEmbedded build system?

See :yocto_wiki:[Products that use the Yocto Project \</Project_Users#Products_that_use_the_Yocto_Project\>]{.title-ref} in the Yocto Project Wiki. Don\'t hesitate to contribute to this page if you know other such products.

> 请查看 Yocto 项目 Wiki 中的[产品使用 Yocto 项目\</Project_Users#Products_that_use_the_Yocto_Project\>]{.title-ref}。如果您知道其他此类产品，请不要犹豫，参与编辑此页面。

# Building environment

## Missing dependencies on the development system?

If your development system does not meet the required Git, tar, and Python versions, you can get the required tools on your host development system in different ways (i.e. building a tarball or downloading a tarball). See the \"`ref-manual/system-requirements:required git, tar, python, make and gcc versions`{.interpreted-text role="ref"}\" section for steps on how to update your build tools.

> 如果您的开发系统不符合所需的 Git、tar 和 Python 版本，您可以通过不同的方式在主机开发系统上获取所需的工具（例如构建一个 tarball 或下载一个 tarball）。有关如何更新构建工具的步骤，请参阅“ref-manual / system-requirements：required git，tar，python，make 和 gcc versions”部分。

## How does OpenEmbedded fetch source code? Will it work through a firewall or proxy server?

The way the build system obtains source code is highly configurable. You can setup the build system to get source code in most environments if HTTP transport is available.

> 系统构建获取源代码的方式非常可配置。如果可以使用 HTTP 传输，您可以设置构建系统以获取大多数环境中的源代码。

When the build system searches for source code, it first tries the local download directory. If that location fails, Poky tries `PREMIRRORS`{.interpreted-text role="term"}, the upstream source, and then `MIRRORS`{.interpreted-text role="term"} in that order.

> 当构建系统搜索源代码时，它首先尝试本地下载目录。 如果失败，Poky 会按照顺序尝试 `PREMIRRORS`{.interpreted-text role="term"}，上游源，然后是 `MIRRORS`{.interpreted-text role="term"}。

Assuming your distribution is \"poky\", the OpenEmbedded build system uses the Yocto Project source `PREMIRRORS`{.interpreted-text role="term"} by default for SCM-based sources, upstreams for normal tarballs, and then falls back to a number of other mirrors including the Yocto Project source mirror if those fail.

> 假设您的发行版是“poky”，OpenEmbedded 构建系统默认情况下使用 Yocto Project 源“PREMIRRORS”来源作为基于 SCM 的源，上游的普通 tarball，然后回退到其他包括 Yocto Project 源镜像的许多镜像，如果这些都失败了。

As an example, you could add a specific server for the build system to attempt before any others by adding something like the following to the `local.conf` configuration file:

> 举个例子，你可以在构建系统尝试其他任何服务器之前，通过将以下内容添加到 `local.conf` 配置文件中来添加一个特定的服务器：

```
PREMIRRORS:prepend = "\
    git://.*/.* &YOCTO_DL_URL;/mirror/sources/ \
    ftp://.*/.* &YOCTO_DL_URL;/mirror/sources/ \
    http://.*/.* &YOCTO_DL_URL;/mirror/sources/ \
    https://.*/.* &YOCTO_DL_URL;/mirror/sources/"
```

These changes cause the build system to intercept Git, FTP, HTTP, and HTTPS requests and direct them to the `http://` sources mirror. You can use `file://` URLs to point to local directories or network shares as well.

> 这些更改导致构建系统拦截 Git、FTP、HTTP 和 HTTPS 请求，并将它们重定向到 `http：//` 源镜像。您还可以使用 `file：//` URL 指向本地目录或网络共享。

Here are other options:

```
BB_NO_NETWORK = "1"
```

This statement tells BitBake to issue an error instead of trying to access the Internet. This technique is useful if you want to ensure code builds only from local sources.

> 这个声明告诉 BitBake 要发出一个错误，而不是尝试访问 Internet。如果你想确保代码只从本地源构建，这个技术是很有用的。

Here is another technique:

```
BB_FETCH_PREMIRRORONLY = "1"
```

This statement limits the build system to pulling source from the `PREMIRRORS`{.interpreted-text role="term"} only. Again, this technique is useful for reproducing builds.

> 这个声明限制了构建系统只能从 `PREMIRRORS` 中拉取源代码。同样，这种技术有助于重现构建。

Here is another technique:

```
BB_GENERATE_MIRROR_TARBALLS = "1"
```

This statement tells the build system to generate mirror tarballs. This technique is useful if you want to create a mirror server. If not, however, the technique can simply waste time during the build.

> 这个声明告诉构建系统生成镜像 tarballs。如果你想要创建一个镜像服务器，这种技术是有用的。但是，如果不这样做，这种技术可能会在构建过程中浪费时间。

Finally, consider an example where you are behind an HTTP-only firewall. You could make the following changes to the `local.conf` configuration file as long as the `PREMIRRORS`{.interpreted-text role="term"} server is current:

> 最后，考虑一个你在 HTTP-only 防火墙后面的例子。只要 `PREMIRRORS` 服务器是最新的，你可以对 `local.conf` 配置文件做出以下更改：

```
PREMIRRORS:prepend = "\
    git://.*/.* &YOCTO_DL_URL;/mirror/sources/ \
    ftp://.*/.* &YOCTO_DL_URL;/mirror/sources/ \
    http://.*/.* &YOCTO_DL_URL;/mirror/sources/ \
    https://.*/.* &YOCTO_DL_URL;/mirror/sources/"
BB_FETCH_PREMIRRORONLY = "1"
```

These changes would cause the build system to successfully fetch source over HTTP and any network accesses to anything other than the `PREMIRRORS`{.interpreted-text role="term"} would fail.

> 这些更改将导致构建系统成功地通过 HTTP 获取源，而对除 `PREMIRRORS` 以外的任何网络访问都将失败。

Most source fetching by the OpenEmbedded build system is done by `wget` and you therefore need to specify the proxy settings in a `.wgetrc` file, which can be in your home directory if you are a single user or can be in `/usr/local/etc/wgetrc` as a global user file.

> 大多数源代码的获取是通过 OpenEmbedded 构建系统完成的，因此您需要在 `.wgetrc` 文件中指定代理设置，如果您是单个用户，则可以在您的主目录中指定，如果是全局用户文件，则可以指定 `/usr/local/etc/wgetrc`。

Following is the applicable code for setting various proxy types in the `.wgetrc` file. By default, these settings are disabled with comments. To use them, remove the comments:

> 以下是在 `.wgetrc` 文件中设置各种代理类型的适用代码。默认情况下，这些设置被注释掉了。要使用它们，请删除注释：

```
# You can set the default proxies for Wget to use for http, https, and ftp.
# They will override the value in the environment.
#https_proxy = http://proxy.yoyodyne.com:18023/
#http_proxy = http://proxy.yoyodyne.com:18023/
#ftp_proxy = http://proxy.yoyodyne.com:18023/

# If you do not want to use proxy at all, set this to off.
#use_proxy = on
```

The build system also accepts `http_proxy`, `ftp_proxy`, `https_proxy`, and `all_proxy` set as to standard shell environment variables to redirect requests through proxy servers.

> 系统构建也接受 `http_proxy`、`ftp_proxy`、`https_proxy` 和 `all_proxy` 设置为标准 shell 环境变量，以通过代理服务器重定向请求。

The Yocto Project also includes a `meta-poky/conf/templates/default/site.conf.sample` file that shows how to configure CVS and Git proxy servers if needed.

> 项目也包括一个 `meta-poky/conf/templates/default/site.conf.sample` 文件，用于展示如何配置 CVS 和 Git 代理服务器（如果需要的话）。

::: note
::: title
Note
:::

You can find more information on the \":yocto_wiki:[Working Behind a Network Proxy \</Working_Behind_a\_Network_Proxy\>]{.title-ref}\" Wiki page.
:::

# Using the OpenEmbedded Build system

## How do I use an external toolchain?

See the \"`dev-manual/external-toolchain:optionally using an external toolchain`{.interpreted-text role="ref"}\" section in the Development Task manual.

> 见开发任务手册中的“dev-manual/external-toolchain：可选择使用外部工具链”部分。

## Why do I get chmod permission issues?

If you see the error `chmod: XXXXX new permissions are r-xrwxrwx, not r-xr-xr-x`, you are probably running the build on an NTFS filesystem. Instead, run the build system on a partition with a modern Linux filesystem such as `ext4`, `btrfs` or `xfs`.

> 如果你看到错误“chmod：XXXXX 新权限是 r-xrwxrwx，而不是 r-xr-xr-x”，你可能正在 NTFS 文件系统上运行构建。相反，请在具有现代 Linux 文件系统（如 ext4，btrfs 或 xfs）的分区上运行构建系统。

## I see many 404 errors trying to download sources. Is anything wrong?

Nothing is wrong. The OpenEmbedded build system checks any configured source mirrors before downloading from the upstream sources. The build system does this searching for both source archives and pre-checked out versions of SCM-managed software. These checks help in large installations because it can reduce load on the SCM servers themselves. This can also allow builds to continue to work if an upstream source disappears.

> 没有什么不对。OpenEmbedded 构建系统会在从上游源下载之前检查任何配置的源镜像。该构建系统会搜索源存档和 SCM 管理的软件的预检出版本。这些检查对于大型安装有帮助，因为它可以减少 SCM 服务器本身的负载。如果上游源消失，这也可以让构建继续工作。

## Why do I get random build failures?

If the same build is failing in totally different and random ways, the most likely explanation is:

- The hardware you are running the build on has some problem.
- You are running the build under virtualization, in which case the virtualization probably has bugs.

The OpenEmbedded build system processes a massive amount of data that causes lots of network, disk and CPU activity and is sensitive to even single-bit failures in any of these areas. True random failures have always been traced back to hardware or virtualization issues.

> 开放式嵌入式构建系统处理大量数据，导致大量网络、磁盘和 CPU 活动，并且对这些领域中的单位位故障非常敏感。真正的随机故障一直被追溯到硬件或虚拟化问题。

## Why does the build fail with `iconv.h` problems?

When you try to build a native recipe, you may get an error message that indicates that GNU `libiconv` is not in use but `iconv.h` has been included from `libiconv`:

> 当你尝试构建本地食谱时，你可能会得到一条错误消息，指出 GNU 的 `libiconv` 没有被使用，但是 `iconv.h` 已经从 `libiconv` 中被包含了。

```
#error GNU libiconv not in use but included iconv.h is from libiconv
```

When this happens, you need to check whether you have a previously installed version of the header file in `/usr/local/include/`. If that\'s the case, you should either uninstall it or temporarily rename it and try the build again.

> 当这种情况发生时，您需要检查/usr/local/include/中是否有以前安装的头文件版本。如果是这种情况，您应该卸载它或暂时重命名它，然后再次尝试构建。

This issue is just a single manifestation of \"system leakage\" issues caused when the OpenEmbedded build system finds and uses previously installed files during a native build. This type of issue might not be limited to `iconv.h`. Make sure that leakage cannot occur from `/usr/local/include` and `/opt` locations.

> 这个问题只是 OpenEmbedded 构建系统在本地构建过程中发现和使用已安装文件所造成的"系统泄漏"问题的一个表现。这种类型的问题可能不仅仅限于 `iconv.h`。确保 `/usr/local/include` 和 `/opt` 位置不会发生泄漏。

## Why don\'t other recipes find the files provided by my `*-native` recipe?

Files provided by your native recipe could be missing from the native sysroot, your recipe could also be installing to the wrong place, or you could be getting permission errors during the `ref-tasks-install`{.interpreted-text role="ref"} task in your recipe.

> 您本机配方提供的文件可能缺失于本机系统根目录，您的配方也可能安装到错误的位置，或者在配方中的'ref-tasks-install'任务期间可能会遇到权限错误。

This situation happens when the build system used by a package does not recognize the environment variables supplied to it by `BitBake`{.interpreted-text role="term"}. The incident that prompted this FAQ entry involved a Makefile that used an environment variable named `BINDIR` instead of the more standard variable `bindir`. The makefile\'s hardcoded default value of \"/usr/bin\" worked most of the time, but not for the recipe\'s `-native` variant. For another example, permission errors might be caused by a Makefile that ignores `DESTDIR` or uses a different name for that environment variable. Check the build system of the package to see if these kinds of issues exist.

> 这种情况发生在包使用的构建系统无法识别由 BitBake 提供的环境变量时。引发这个 FAQ 条目的事件牵涉到一个使用名为 BINDIR 的环境变量，而不是更标准的变量 bindir 的 Makefile。Makefile 的硬编码默认值“/usr/bin”在大多数情况下都有效，但不适用于配方的-native 变体。另一个例子，权限错误可能是由忽略 DESTDIR 或使用不同名称的 Makefile 引起的。检查包的构建系统，看看是否存在这类问题。

## Can I get rid of build output so I can start over?

Yes \-\-- you can easily do this. When you use BitBake to build an image, all the build output goes into the directory created when you run the build environment setup script (i.e. `structure-core-script`{.interpreted-text role="ref"}). By default, this `Build Directory`{.interpreted-text role="term"} is named `build` but can be named anything you want.

> 是的，您可以轻松地做到这一点。当您使用 BitBake 构建图像时，所有构建输出都会被放入运行构建环境设置脚本（即“structure-core-script”）时创建的目录中。默认情况下，此“构建目录”名为“build”，但可以按您的要求命名。

Within the `Build Directory`{.interpreted-text role="term"}, is the `tmp` directory. To remove all the build output yet preserve any source code or downloaded files from previous builds, simply remove the `tmp` directory.

> 在构建目录中，有一个 `tmp` 目录。要清除所有构建输出，同时保留任何先前构建中的源代码或下载文件，只需删除 `tmp` 目录即可。

# Customizing generated images

## What does the OpenEmbedded build system produce as output?

Because you can use the same set of recipes to create output of various formats, the output of an OpenEmbedded build depends on how you start it. Usually, the output is a flashable image ready for the target device.

> 因为你可以使用相同的一组配方来创建各种格式的输出，OpenEmbedded 构建的输出取决于你如何开始它。通常，输出是可刷入目标设备的可刷入图像。

## How do I make the Yocto Project support my board?

Support for an additional board is added by creating a Board Support Package (BSP) layer for it. For more information on how to create a BSP layer, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual and the `/bsp-guide/index`{.interpreted-text role="doc"}.

> 为添加额外的板支持，需要创建一个板支持包(BSP)层。有关如何创建 BSP 层的更多信息，请参阅 Yocto Project 开发任务手册中的“dev-manual/layers：理解和创建层”部分以及/bsp-guide/index 文档。

Usually, if the board is not completely exotic, adding support in the Yocto Project is fairly straightforward.

## How do I make the Yocto Project support my package?

To add a package, you need to create a BitBake recipe. For information on how to create a BitBake recipe, see the \"`dev-manual/new-recipe:writing a new recipe`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 要添加一个软件包，您需要创建一个 BitBake 配方。有关如何创建 BitBake 配方的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/new-recipe：编写新配方”部分。

## What do I need to ship for license compliance?

This is a difficult question and you need to consult your lawyer for the answer for your specific case. It is worth bearing in mind that for GPL compliance, there needs to be enough information shipped to allow someone else to rebuild and produce the same end result you are shipping. This means sharing the source code, any patches applied to it, and also any configuration information about how that package was configured and built.

> 这是一个很难的问题，您需要咨询律师给您的特定案件答案。值得记住的是，为了遵守 GPL，需要提供足够的信息来允许其他人重建并产生与您发货相同的最终结果。这意味着共享源代码，应用的任何补丁，以及有关如何配置和构建该软件包的任何配置信息。

You can find more information on licensing in the \"`overview-manual/development-environment:licensing`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual and also in the \"`dev-manual/licenses:maintaining open source license compliance during your product's lifecycle`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 您可以在 Yocto Project 概览和概念手册中的“概述手册/开发环境：许可”部分以及 Yocto Project 开发任务手册中的“开发手册/许可：在您的产品生命周期中保持开源许可合规”部分中找到更多有关许可的信息。

## Do I have to make a full reflash after recompiling one package?

The OpenEmbedded build system can build packages in various formats such as IPK for OPKG, Debian package (`.deb`), or RPM. You can then upgrade only the modified packages using the package tools on the device, much like on a desktop distribution such as Ubuntu or Fedora. However, package management on the target is entirely optional.

> 开放式嵌入式构建系统可以构建各种格式的包，如 OPKG 的 IPK，Debian 包（`.deb`）或 RPM。然后，您可以使用设备上的包工具仅升级修改过的包，就像在像 Ubuntu 或 Fedora 这样的桌面分发版上一样。但是，目标上的包管理完全是可选的。

## How to prevent my package from being marked as machine specific?

If you have machine-specific data in a package for one machine only but the package is being marked as machine-specific in all cases, you can set `SRC_URI_OVERRIDES_PACKAGE_ARCH`{.interpreted-text role="term"} = \"0\" in the `.bb` file. However, but make sure the package is manually marked as machine-specific for the case that needs it. The code that handles `SRC_URI_OVERRIDES_PACKAGE_ARCH`{.interpreted-text role="term"} is in the `meta/classes-global/base.bbclass` file.

> 如果您在一台机器上的软件包中有专门的机器数据，但该软件包被标记为专用机器，则可以在 `.bb` 文件中将 `SRC_URI_OVERRIDES_PACKAGE_ARCH` 设置为“0”。但是，请确保为需要它的情况手动标记该软件包为专用机器。处理 `SRC_URI_OVERRIDES_PACKAGE_ARCH` 的代码位于 `meta/classes-global/base.bbclass` 文件中。

## What\'s the difference between `target` and `target-native`?

The `*-native` targets are designed to run on the system being used for the build. These are usually tools that are needed to assist the build in some way such as `quilt-native`, which is used to apply patches. The non-native version is the one that runs on the target device.

> 本地*目标旨在在构建系统上运行。这些通常是为了协助构建的必要工具，如 quilt-native，用于应用补丁。非本地版本是在目标设备上运行的版本。

## Why do `${bindir}` and `${libdir}` have strange values for `-native` recipes?

Executables and libraries might need to be used from a directory other than the directory into which they were initially installed. Complicating this situation is the fact that sometimes these executables and libraries are compiled with the expectation of being run from that initial installation target directory. If this is the case, moving them causes problems.

> 可执行文件和库可能需要从安装的目录以外的目录使用。复杂的是，有时这些可执行文件和库是编译时期望从初始安装目标目录运行的。如果是这种情况，移动它们会导致问题。

This scenario is a fundamental problem for package maintainers of mainstream Linux distributions as well as for the OpenEmbedded build system. As such, a well-established solution exists. Makefiles, Autotools configuration scripts, and other build systems are expected to respect environment variables such as `bindir`, `libdir`, and `sysconfdir` that indicate where executables, libraries, and data reside when a program is actually run. They are also expected to respect a `DESTDIR` environment variable, which is prepended to all the other variables when the build system actually installs the files. It is understood that the program does not actually run from within `DESTDIR`.

> 这种情况是主流 Linux 发行版的软件包维护者以及 OpenEmbedded 构建系统的基本问题。因此，已经建立了一种解决方案。Makefile、Autotools 配置脚本和其他构建系统都应该遵循环境变量，例如 `bindir`、`libdir` 和 `sysconfdir`，这些变量指示程序实际运行时可执行文件、库和数据的位置。它们也应该遵循一个 `DESTDIR` 环境变量，当构建系统实际安装文件时，它会被添加到所有其他变量之前。但是，程序实际上不会从 `DESTDIR` 中运行。

When the OpenEmbedded build system uses a recipe to build a target-architecture program (i.e. one that is intended for inclusion on the image being built), that program eventually runs from the root file system of that image. Thus, the build system provides a value of \"/usr/bin\" for `bindir`, a value of \"/usr/lib\" for `libdir`, and so forth.

> 当 OpenEmbedded 构建系统使用配方来构建目标架构程序（即用于包含在正在构建的映像中的程序）时，该程序最终从该映像的根文件系统运行。因此，构建系统为“/usr/bin”提供“bindir”的值，为“/usr/lib”提供“libdir”的值，以及等等。

Meanwhile, `DESTDIR` is a path within the `Build Directory`{.interpreted-text role="term"}. However, when the recipe builds a native program (i.e. one that is intended to run on the build machine), that program is never installed directly to the build machine\'s root file system. Consequently, the build system uses paths within the Build Directory for `DESTDIR`, `bindir` and related variables. To better understand this, consider the following two paths (artificially broken across lines for readability) where the first is relatively normal and the second is not:

> 同时，`DESTDIR` 是 `Build Directory` 中的一个路径。但是，当配方构建本机程序（即旨在在构建机上运行的程序）时，该程序永远不会直接安装到构建机的根文件系统中。因此，构建系统使用 Build Directory 中的路径为 `DESTDIR`，`bindir` 和相关变量。要更好地理解这一点，请考虑以下两个路径（为了便于阅读而人为地拆分），其中第一个相对正常，第二个不正常：

```
/home/maxtothemax/poky-bootchart2/build/tmp/work/i586-poky-linux/zlib/
   1.2.8-r0/sysroot-destdir/usr/bin

/home/maxtothemax/poky-bootchart2/build/tmp/work/x86_64-linux/
   zlib-native/1.2.8-r0/sysroot-destdir/home/maxtothemax/poky-bootchart2/
   build/tmp/sysroots/x86_64-linux/usr/bin
```

Even if the paths look unusual, they both are correct \-\-- the first for a target and the second for a native recipe. These paths are a consequence of the `DESTDIR` mechanism and while they appear strange, they are correct and in practice very effective.

> 即使路径看起来不寻常，它们都是正确的——第一个用于目标，第二个用于本地配方。这些路径是 `DESTDIR` 机制的结果，虽然它们看起来很奇怪，但它们是正确的，实际上非常有效。

## How do I create images with more free space?

By default, the OpenEmbedded build system creates images that are 1.3 times the size of the populated root filesystem. To affect the image size, you need to set various configurations:

> 默认情况下，OpenEmbedded 构建系统创建的映像文件大小是填充根文件系统的 1.3 倍。要影响映像文件的大小，您需要设置各种配置。

- _Image Size:_ The OpenEmbedded build system uses the `IMAGE_ROOTFS_SIZE`{.interpreted-text role="term"} variable to define the size of the image in Kbytes. The build system determines the size by taking into account the initial root filesystem size before any modifications such as requested size for the image and any requested additional free disk space to be added to the image.

> 图像大小：OpenEmbedded 构建系统使用 `IMAGE_ROOTFS_SIZE` 变量来定义图像的大小，单位为 K 字节。构建系统会根据初始根文件系统的大小，以及图像所需的大小和额外的磁盘空间来确定大小。

- _Overhead:_ Use the `IMAGE_OVERHEAD_FACTOR`{.interpreted-text role="term"} variable to define the multiplier that the build system applies to the initial image size, which is 1.3 by default.

> 使用 `IMAGE_OVERHEAD_FACTOR` 变量定义建立系统对初始图像大小（默认为 1.3）的乘数。

- _Additional Free Space:_ Use the `IMAGE_ROOTFS_EXTRA_SPACE`{.interpreted-text role="term"} variable to add additional free space to the image. The build system adds this space to the image after it determines its `IMAGE_ROOTFS_SIZE`{.interpreted-text role="term"}.

> 使用 `IMAGE_ROOTFS_EXTRA_SPACE` 变量可以为镜像添加额外的空间。在确定 `IMAGE_ROOTFS_SIZE` 之后，构建系统会将这部分空间添加到镜像中。

## Why aren\'t spaces in path names supported?

The Yocto Project team has tried to do this before but too many of the tools the OpenEmbedded build system depends on, such as `autoconf`, break when they find spaces in pathnames. Until that situation changes, the team will not support spaces in pathnames.

> Yocto Project 团队曾尝试过这样做，但是 OpenEmbedded 构建系统依赖的许多工具，例如 `autoconf`，在发现路径名中有空格时会出现故障。在这种情况改变之前，团队不会支持路径名中的空格。

## I\'m adding a binary in a recipe. Why is it different in the image?

The first most obvious change is the system stripping debug symbols from it. Setting `INHIBIT_PACKAGE_STRIP`{.interpreted-text role="term"} to stop debug symbols being stripped and/or `INHIBIT_PACKAGE_DEBUG_SPLIT`{.interpreted-text role="term"} to stop debug symbols being split into a separate file will ensure the binary is unchanged.

> 最明显的改变是系统从中移除调试符号。设置 `INHIBIT_PACKAGE_STRIP` 以阻止调试符号被移除，或者设置 `INHIBIT_PACKAGE_DEBUG_SPLIT` 以阻止调试符号被分割到单独的文件中，以确保二进制文件不变。

# Issues on the running system

## How do I disable the cursor on my touchscreen device?

You need to create a form factor file as described in the \"`bsp-guide/bsp:miscellaneous bsp-specific recipe files`{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Packages (BSP) Developer\'s Guide. Set the `HAVE_TOUCHSCREEN` variable equal to one as follows:

> 你需要按照 Yocto Project Board Support Packages (BSP)开发者指南中"bsp-guide/bsp：其他 bsp 特定配方文件"部分中描述的创建一个形式因子文件。将 `HAVE_TOUCHSCREEN` 变量设置为 1，如下所示：

```
HAVE_TOUCHSCREEN=1
```

## How to always bring up connected network interfaces?

The default interfaces file provided by the netbase recipe does not automatically bring up network interfaces. Therefore, you will need to add a BSP-specific netbase that includes an interfaces file. See the \"`bsp-guide/bsp:miscellaneous bsp-specific recipe files`{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Packages (BSP) Developer\'s Guide for information on creating these types of miscellaneous recipe files.

> 默认的网络基础配方提供的接口文件不会自动启动网络接口。因此，您需要添加一个包含接口文件的特定于 BSP 的网络基础。有关创建此类杂项配方文件的信息，请参阅 Yocto 项目板级支持包（BSP）开发人员指南中的“ bsp-guide / bsp：杂项 bsp 特定配方文件”部分。

For example, add the following files to your layer:

```
meta-MACHINE/recipes-bsp/netbase/netbase/MACHINE/interfaces
meta-MACHINE/recipes-bsp/netbase/netbase_5.0.bbappend
```
