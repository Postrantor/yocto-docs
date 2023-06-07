---
tip: translate by baidu@2023-06-07 17:08:26
...
---
title: Maintaining Build Output Quality
---------------------------------------

Many factors can influence the quality of a build. For example, if you upgrade a recipe to use a new version of an upstream software package or you experiment with some new configuration options, subtle changes can occur that you might not detect until later. Consider the case where your recipe is using a newer version of an upstream package. In this case, a new version of a piece of software might introduce an optional dependency on another library, which is auto-detected. If that library has already been built when the software is building, the software will link to the built library and that library will be pulled into your image along with the new software even if you did not want the library.

> 许多因素都会影响构建的质量。例如，如果您升级配方以使用上游软件包的新版本，或者尝试一些新的配置选项，则可能会发生细微的变化，您可能要等到以后才能检测到。考虑一下您的配方使用上游包的更新版本的情况。在这种情况下，一个软件的新版本可能会引入对另一个库的可选依赖，这是自动检测到的。如果在构建软件时已经构建了该库，则该软件将链接到构建的库，并且该库将与新软件一起被拉入您的映像中，即使您不想要该库。

The `ref-classes-buildhistory`{.interpreted-text role="ref"} class helps you maintain the quality of your build output. You can use the class to highlight unexpected and possibly unwanted changes in the build output. When you enable build history, it records information about the contents of each package and image and then commits that information to a local Git repository where you can examine the information.

> `ref classes buildhistory`｛.explored text role=“ref”｝类可帮助您保持生成输出的质量。您可以使用该类来突出显示生成输出中的意外更改和可能不需要的更改。当您启用构建历史记录时，它会记录有关每个包和映像内容的信息，然后将这些信息提交到本地 Git 存储库，您可以在那里检查这些信息。

The remainder of this section describes the following:

> 本节的其余部分介绍了以下内容：

- `How you can enable and disable build history <dev-manual/build-quality:enabling and disabling build history>`{.interpreted-text role="ref"}

> -`如何启用和禁用生成历史记录<dev manual/build quality:enabled and disabled build history>`｛.depredicted text role=“ref”｝

- `How to understand what the build history contains <dev-manual/build-quality:understanding what the build history contains>`{.interpreted-text role="ref"}

> -`如何理解构建历史记录中包含的内容<devmanual/build-quality:了解构建历史记录包含的内容>`｛.depredicted text role=“ref”｝

- `How to limit the information used for build history <dev-manual/build-quality:using build history to gather image information only>`{.interpreted-text role="ref"}

> -`如何限制用于生成历史记录的信息<dev manual/build quality:using build history to collect image information only>`｛.depreted text role=“ref”｝

- `How to examine the build history from both a command-line and web interface <dev-manual/build-quality:examining build history information>`{.interpreted-text role="ref"}

> -`如何从命令行和web界面检查生成历史记录<devmanual/build-quality:检查生成历史信息>`｛.depredicted text role=“ref”｝

# Enabling and Disabling Build History

Build history is disabled by default. To enable it, add the following `INHERIT`{.interpreted-text role="term"} statement and set the `BUILDHISTORY_COMMIT`{.interpreted-text role="term"} variable to \"1\" at the end of your `conf/local.conf` file found in the `Build Directory`{.interpreted-text role="term"}:

> 默认情况下禁用生成历史记录。若要启用它，请添加以下 `INHERIT`｛.depreted text role=“term”｝语句，并在 `Build Directory`｛.repreted text role=“term“｝中的 `conf/local.conf` 文件结尾处将 `BUILDSHISTORY_COMMIT`{.depreted textrole=”term“}变量设置为\“1\”：

```
INHERIT += "buildhistory"
BUILDHISTORY_COMMIT = "1"
```

Enabling build history as previously described causes the OpenEmbedded build system to collect build output information and commit it as a single commit to a local `overview-manual/development-environment:git`{.interpreted-text role="ref"} repository.

> 如前所述启用生成历史记录会导致 OpenEmbedded 生成系统收集生成输出信息，并将其作为单个提交提交到本地 `overview manual/development environment:git`{.depreted text role=“ref”}存储库。

::: note
::: title
Note
:::

Enabling build history increases your build times slightly, particularly for images, and increases the amount of disk space used during the build.

> 启用生成历史记录会略微增加生成时间，特别是对于映像，并增加生成过程中使用的磁盘空间量。
> :::

You can disable build history by removing the previous statements from your `conf/local.conf` file.

> 您可以通过从“conf/local.conf”文件中删除以前的语句来禁用构建历史记录。

# Understanding What the Build History Contains

Build history information is kept in `${``TOPDIR`{.interpreted-text role="term"}`}/buildhistory` in the `Build Directory`{.interpreted-text role="term"} as defined by the `BUILDHISTORY_DIR`{.interpreted-text role="term"} variable. Here is an example abbreviated listing:

> 生成历史记录信息保存在 `BUILDSHISTORY_DIR`｛.expected text role=“term”｝变量定义的 `Build Directory`｛.dexpected textrole=”term“｝中的 `${` TOPDIR `｛.respered text-role=”term”}`}/buildhistory` 中。以下是一个缩写列表示例：

![image](figures/buildhistory.png){.align-center width="50.0%"}

At the top level, there is a `metadata-revs` file that lists the revisions of the repositories for the enabled layers when the build was produced. The rest of the data splits into separate `packages`, `images` and `sdk` directories, the contents of which are described as follows.

> 在顶层，有一个“元数据 revs”文件，列出了生成构建时启用层的存储库的修订。其余数据分为单独的“包”、“图像”和“sdk”目录，其内容描述如下。

## Build History Package Information

The history for each package contains a text file that has name-value pairs with information about the package. For example, `buildhistory/packages/i586-poky-linux/busybox/busybox/latest` contains the following:

> 每个包的历史记录都包含一个文本文件，该文件具有与包相关信息的名称值对。例如，`buildhistory/packages/i586-poky-linux/busybox/bussybox/latest` 包含以下内容：

```none
PV = 1.22.1
PR = r32
RPROVIDES =
RDEPENDS = glibc (>= 2.20) update-alternatives-opkg
RRECOMMENDS = busybox-syslog busybox-udhcpc update-rc.d
PKGSIZE = 540168
FILES = /usr/bin/* /usr/sbin/* /usr/lib/busybox/* /usr/lib/lib*.so.* \
   /etc /com /var /bin/* /sbin/* /lib/*.so.* /lib/udev/rules.d \
   /usr/lib/udev/rules.d /usr/share/busybox /usr/lib/busybox/* \
   /usr/share/pixmaps /usr/share/applications /usr/share/idl \
   /usr/share/omf /usr/share/sounds /usr/lib/bonobo/servers
FILELIST = /bin/busybox /bin/busybox.nosuid /bin/busybox.suid /bin/sh \
   /etc/busybox.links.nosuid /etc/busybox.links.suid
```

Most of these name-value pairs correspond to variables used to produce the package. The exceptions are `FILELIST`, which is the actual list of files in the package, and `PKGSIZE`, which is the total size of files in the package in bytes.

> 这些名称-值对中的大多数对应于用于生成包的变量。例外情况是“FILELIST”和“PKGSIZE”，前者是包中文件的实际列表，后者是包中以字节为单位的文件的总大小。

There is also a file that corresponds to the recipe from which the package came (e.g. `buildhistory/packages/i586-poky-linux/busybox/latest`):

> 还有一个文件对应于该包的配方（例如“buildhistory/packages/i586 poky linux/bussybox/relatest”）：

```none
PV = 1.22.1
PR = r32
DEPENDS = initscripts kern-tools-native update-rc.d-native \
   virtual/i586-poky-linux-compilerlibs virtual/i586-poky-linux-gcc \
   virtual/libc virtual/update-alternatives
PACKAGES = busybox-ptest busybox-httpd busybox-udhcpd busybox-udhcpc \
   busybox-syslog busybox-mdev busybox-hwclock busybox-dbg \
   busybox-staticdev busybox-dev busybox-doc busybox-locale busybox
```

Finally, for those recipes fetched from a version control system (e.g., Git), there is a file that lists source revisions that are specified in the recipe and the actual revisions used during the build. Listed and actual revisions might differ when `SRCREV`{.interpreted-text role="term"} is set to \${`AUTOREV`{.interpreted-text role="term"}}. Here is an example assuming `buildhistory/packages/qemux86-poky-linux/linux-yocto/latest_srcrev`):

> 最后，对于那些从版本控制系统（例如 Git）获取的配方，有一个文件列出了配方中指定的源修订以及构建过程中使用的实际修订。当 `SRCREV`｛.depreced text role=“term”｝设置为\$｛`AUTOREV`｛.epreced textrole=”term｝时，列出的修订和实际修订可能会有所不同。下面是一个假设“buildhistory/packages/qemux86 poky linux/linux-yocto/latest_srcrev”）的示例：

```
# SRCREV_machine = "38cd560d5022ed2dbd1ab0dca9642e47c98a0aa1"
SRCREV_machine = "38cd560d5022ed2dbd1ab0dca9642e47c98a0aa1"
# SRCREV_meta = "a227f20eff056e511d504b2e490f3774ab260d6f"
SRCREV_meta ="a227f20eff056e511d504b2e490f3774ab260d6f"
```

You can use the `buildhistory-collect-srcrevs` command with the `-a` option to collect the stored `SRCREV`{.interpreted-text role="term"} values from build history and report them in a format suitable for use in global configuration (e.g., `local.conf` or a distro include file) to override floating `AUTOREV`{.interpreted-text role="term"} values to a fixed set of revisions. Here is some example output from this command:

> 您可以将“buildhistory collect srcrevs”命令与“-a”选项一起使用，从构建历史中收集存储的“SRCREV”｛.depredicted text role=“term”｝值，并以适合在全局配置中使用的格式（例如，“local.conf”或发行版包含文件）报告这些值，以将浮动的“AUTOREV”{.epredicted textrole=”term｝值重写为固定的修订集。以下是该命令的一些输出示例：

```
$ buildhistory-collect-srcrevs -a
# all-poky-linux
SRCREV:pn-ca-certificates = "07de54fdcc5806bde549e1edf60738c6bccf50e8"
SRCREV:pn-update-rc.d = "8636cf478d426b568c1be11dbd9346f67e03adac"
# core2-64-poky-linux
SRCREV:pn-binutils = "87d4632d36323091e731eb07b8aa65f90293da66"
SRCREV:pn-btrfs-tools = "8ad326b2f28c044cb6ed9016d7c3285e23b673c8"
SRCREV_bzip2-tests:pn-bzip2 = "f9061c030a25de5b6829e1abf373057309c734c0"
SRCREV:pn-e2fsprogs = "02540dedd3ddc52c6ae8aaa8a95ce75c3f8be1c0"
SRCREV:pn-file = "504206e53a89fd6eed71aeaf878aa3512418eab1"
SRCREV_glibc:pn-glibc = "24962427071fa532c3c48c918e9d64d719cc8a6c"
SRCREV:pn-gnome-desktop-testing = "e346cd4ed2e2102c9b195b614f3c642d23f5f6e7"
SRCREV:pn-init-system-helpers = "dbd9197569c0935029acd5c9b02b84c68fd937ee"
SRCREV:pn-kmod = "b6ecfc916a17eab8f93be5b09f4e4f845aabd3d1"
SRCREV:pn-libnsl2 = "82245c0c58add79a8e34ab0917358217a70e5100"
SRCREV:pn-libseccomp = "57357d2741a3b3d3e8425889a6b79a130e0fa2f3"
SRCREV:pn-libxcrypt = "50cf2b6dd4fdf04309445f2eec8de7051d953abf"
SRCREV:pn-ncurses = "51d0fd9cc3edb975f04224f29f777f8f448e8ced"
SRCREV:pn-procps = "19a508ea121c0c4ac6d0224575a036de745eaaf8"
SRCREV:pn-psmisc = "5fab6b7ab385080f1db725d6803136ec1841a15f"
SRCREV:pn-ptest-runner = "bcb82804daa8f725b6add259dcef2067e61a75aa"
SRCREV:pn-shared-mime-info = "18e558fa1c8b90b86757ade09a4ba4d6a6cf8f70"
SRCREV:pn-zstd = "e47e674cd09583ff0503f0f6defd6d23d8b718d3"
# qemux86_64-poky-linux
SRCREV_machine:pn-linux-yocto = "20301aeb1a64164b72bc72af58802b315e025c9c"
SRCREV_meta:pn-linux-yocto = "2d38a472b21ae343707c8bd64ac68a9eaca066a0"
# x86_64-linux
SRCREV:pn-binutils-cross-x86_64 = "87d4632d36323091e731eb07b8aa65f90293da66"
SRCREV_glibc:pn-cross-localedef-native = "24962427071fa532c3c48c918e9d64d719cc8a6c"
SRCREV_localedef:pn-cross-localedef-native = "794da69788cbf9bf57b59a852f9f11307663fa87"
SRCREV:pn-debianutils-native = "de14223e5bffe15e374a441302c528ffc1cbed57"
SRCREV:pn-libmodulemd-native = "ee80309bc766d781a144e6879419b29f444d94eb"
SRCREV:pn-virglrenderer-native = "363915595e05fb252e70d6514be2f0c0b5ca312b"
SRCREV:pn-zstd-native = "e47e674cd09583ff0503f0f6defd6d23d8b718d3"
```

::: note
::: title
Note
:::

Here are some notes on using the `buildhistory-collect-srcrevs` command:

> 以下是关于使用“buildhistory collect srcrevs”命令的一些注意事项：

- By default, only values where the `SRCREV`{.interpreted-text role="term"} was not hardcoded (usually when `AUTOREV`{.interpreted-text role="term"} is used) are reported. Use the `-a` option to see all `SRCREV`{.interpreted-text role="term"} values.

> -默认情况下，只报告“SRCREV”｛.explored text role=“term”｝未硬编码的值（通常在使用“AUTOREV”时）。使用“-a”选项可以查看所有“SRCREV”｛.explored text role=“term”｝值。

- The output statements might not have any effect if overrides are applied elsewhere in the build system configuration. Use the `-f` option to add the `forcevariable` override to each output line if you need to work around this restriction.

> -如果在构建系统配置的其他地方应用重写，则输出语句可能不会有任何效果。如果需要绕过此限制，请使用“-f”选项将“forcevariable”覆盖添加到每个输出行。

- The script does apply special handling when building for multiple machines. However, the script does place a comment before each set of values that specifies which triplet to which they belong as previously shown (e.g., `i586-poky-linux`).

> -当为多台机器构建时，脚本确实应用了特殊处理。然而，脚本确实在每一组值之前放置了一个注释，该注释指定了它们所属的三元组，如前所示（例如，“i586 poky linux”）。
> :::

## Build History Image Information

The files produced for each image are as follows:

> 为每个图像生成的文件如下：

- `image-files:` A directory containing selected files from the root filesystem. The files are defined by `BUILDHISTORY_IMAGE_FILES`{.interpreted-text role="term"}.

> -`image files:` 包含根文件系统中选定文件的目录。这些文件由 `BUILDSHISTORY_IMAGE_files`｛.explored text role=“term”｝定义。

- `build-id.txt:` Human-readable information about the build configuration and metadata source revisions. This file contains the full build header as printed by BitBake.

> -`build-id.txt:` 有关生成配置和元数据源修订的可读信息。此文件包含 BitBake 打印的完整构建标头。

- `*.dot:` Dependency graphs for the image that are compatible with `graphviz`.
- `files-in-image.txt:` A list of files in the image with permissions, owner, group, size, and symlink information.

> -`files-in-image.txt：` 图像中具有权限、所有者、组、大小和符号链接信息的文件列表。

- `image-info.txt:` A text file containing name-value pairs with information about the image. See the following listing example for more information.

> -`image-info.txt:` 一个文本文件，包含与图像信息成对的名称值。有关详细信息，请参见以下列表示例。

- `installed-package-names.txt:` A list of installed packages by name only.
- `installed-package-sizes.txt:` A list of installed packages ordered by size.
- `installed-packages.txt:` A list of installed packages with full package filenames.

::: note
::: title
Note
:::

Installed package information is able to be gathered and produced even if package management is disabled for the final image.

> 即使对最终映像禁用了程序包管理，也可以收集和生成已安装的程序包信息。
> :::

Here is an example of `image-info.txt`:

> 以下是“image info.txt”的示例：

```none
DISTRO = poky
DISTRO_VERSION = 3.4+snapshot-a0245d7be08f3d24ea1875e9f8872aa6bbff93be
USER_CLASSES = buildstats
IMAGE_CLASSES = qemuboot qemuboot license_image
IMAGE_FEATURES = debug-tweaks
IMAGE_LINGUAS =
IMAGE_INSTALL = packagegroup-core-boot speex speexdsp
BAD_RECOMMENDATIONS =
NO_RECOMMENDATIONS =
PACKAGE_EXCLUDE =
ROOTFS_POSTPROCESS_COMMAND = write_package_manifest; license_create_manifest; cve_check_write_rootfs_manifest;   ssh_allow_empty_password;  ssh_allow_root_login;  postinst_enable_logging;  rootfs_update_timestamp;   write_image_test_data;   empty_var_volatile;   sort_passwd; rootfs_reproducible;
IMAGE_POSTPROCESS_COMMAND =  buildhistory_get_imageinfo ;
IMAGESIZE = 9265
```

Other than `IMAGESIZE`, which is the total size of the files in the image in Kbytes, the name-value pairs are variables that may have influenced the content of the image. This information is often useful when you are trying to determine why a change in the package or file listings has occurred.

> 除了“IMAGESIZE”（图像中文件的总大小，单位为千字节）之外，名称-值对是可能影响图像内容的变量。当您试图确定包或文件列表发生更改的原因时，此信息通常很有用。

## Using Build History to Gather Image Information Only

As you can see, build history produces image information, including dependency graphs, so you can see why something was pulled into the image. If you are just interested in this information and not interested in collecting specific package or SDK information, you can enable writing only image information without any history by adding the following to your `conf/local.conf` file found in the `Build Directory`{.interpreted-text role="term"}:

> 正如您所看到的，构建历史会生成图像信息，包括依赖关系图，因此您可以了解为什么要将某些内容拉入图像中。如果您只对这些信息感兴趣，而对收集特定的包或 SDK 信息不感兴趣，您可以通过将以下内容添加到“构建目录”中的“conf/local.conf”文件中来启用只写图像信息而不写任何历史记录：

```
INHERIT += "buildhistory"
BUILDHISTORY_COMMIT = "0"
BUILDHISTORY_FEATURES = "image"
```

Here, you set the `BUILDHISTORY_FEATURES`{.interpreted-text role="term"} variable to use the image feature only.

> 在这里，您可以将 `BUILDTHISTORY_FATURE`｛.respered text role=“term”｝变量设置为仅使用图像功能。

## Build History SDK Information

Build history collects similar information on the contents of SDKs (e.g. `bitbake -c populate_sdk imagename`) as compared to information it collects for images. Furthermore, this information differs depending on whether an extensible or standard SDK is being produced.

> 与为图像收集的信息相比，构建历史收集关于 sdk 内容的类似信息（例如“bitbake-c populate_sdk imagename”）。此外，这些信息还因正在生成可扩展 SDK 还是标准 SDK 而有所不同。

The following list shows the files produced for SDKs:

> 以下列表显示了为 SDK 生成的文件：

- `files-in-sdk.txt:` A list of files in the SDK with permissions, owner, group, size, and symlink information. This list includes both the host and target parts of the SDK.

> -`files-in-sdk.txt:` sdk 中包含权限、所有者、组、大小和符号链接信息的文件列表。此列表包括 SDK 的主机部分和目标部分。

- `sdk-info.txt:` A text file containing name-value pairs with information about the SDK. See the following listing example for more information.

> -`sdk info.txt:` 一个文本文件，包含与 sdk 相关信息的名称值对。有关详细信息，请参见以下列表示例。

- `sstate-task-sizes.txt:` A text file containing name-value pairs with information about task group sizes (e.g. `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} tasks have a total size). The `sstate-task-sizes.txt` file exists only when an extensible SDK is created.

> -`sstate task sizes.txt:` 一个包含名称-值对的文本文件，其中包含有关任务组大小的信息（例如 `ref-tasks-populate_sysroot`{.depreted text role=“ref”}任务具有总大小）。“sstate task sizes.txt”文件仅在创建可扩展 SDK 时存在。

- `sstate-package-sizes.txt:` A text file containing name-value pairs with information for the shared-state packages and sizes in the SDK. The `sstate-package-sizes.txt` file exists only when an extensible SDK is created.

> -`sstate package sizes.txt:` 一个文本文件，包含名称-值对，以及 SDK 中共享状态包和大小的信息。“sstate-package-sizes.txt”文件仅在创建可扩展 SDK 时存在。

- `sdk-files:` A folder that contains copies of the files mentioned in `BUILDHISTORY_SDK_FILES` if the files are present in the output. Additionally, the default value of `BUILDHISTORY_SDK_FILES` is specific to the extensible SDK although you can set it differently if you would like to pull in specific files from the standard SDK.

> -`sdk files:` 一个文件夹，其中包含 `BUILDSHISTORY_sdk_files` 中提到的文件的副本（如果文件存在于输出中）。此外，“BUILDSHISTORY_SDK_FILES”的默认值是特定于可扩展 SDK 的，尽管如果您想从标准 SDK 中提取特定文件，可以进行不同的设置。

The default files are `conf/local.conf`, `conf/bblayers.conf`, `conf/auto.conf`, `conf/locked-sigs.inc`, and `conf/devtool.conf`. Thus, for an extensible SDK, these files get copied into the `sdk-files` directory.

> 默认文件是 `conf/local.conf`、`conf/bbayers.conf`、`conf/auto.conf`，`conf/ocked-sigs.inc` 和 `conf/devtool.conf`。因此，对于可扩展 SDK，这些文件会复制到 `SDK-files` 目录中。

- The following information appears under each of the `host` and `target` directories for the portions of the SDK that run on the host and on the target, respectively:

> -以下信息分别出现在主机和目标上运行的 SDK 部分的“主机”和“目标”目录下：

::: note
::: title

Note

> 笔记
> :::

The following files for the most part are empty when producing an extensible SDK because this type of SDK is not constructed from packages as is the standard SDK.

> 当生成可扩展 SDK 时，以下文件大部分是空的，因为这种类型的 SDK 不是像标准 SDK 那样由包构建的。
> :::

- `depends.dot:` Dependency graph for the SDK that is compatible with `graphviz`.
- `installed-package-names.txt:` A list of installed packages by name only.
- `installed-package-sizes.txt:` A list of installed packages ordered by size.
- `installed-packages.txt:` A list of installed packages with full package filenames.

Here is an example of `sdk-info.txt`:

> 以下是“sdk info.txt”的示例：

```none
DISTRO = poky
DISTRO_VERSION = 1.3+snapshot-20130327
SDK_NAME = poky-glibc-i686-arm
SDK_VERSION = 1.3+snapshot
SDKMACHINE =
SDKIMAGE_FEATURES = dev-pkgs dbg-pkgs
BAD_RECOMMENDATIONS =
SDKSIZE = 352712
```

Other than `SDKSIZE`, which is the total size of the files in the SDK in Kbytes, the name-value pairs are variables that might have influenced the content of the SDK. This information is often useful when you are trying to determine why a change in the package or file listings has occurred.

> 除了“SDKSIZE”（以 KB 为单位的 SDK 中文件的总大小）之外，名称-值对是可能影响 SDK 内容的变量。当您试图确定包或文件列表发生更改的原因时，此信息通常很有用。

## Examining Build History Information

You can examine build history output from the command line or from a web interface.

> 您可以从命令行或 web 界面检查生成历史记录输出。

To see any changes that have occurred (assuming you have `BUILDHISTORY_COMMIT`{.interpreted-text role="term"} = \"1\"), you can simply use any Git command that allows you to view the history of a repository. Here is one method:

> 要查看已发生的任何更改（假设您有 `BUILDSHISTORY_COMMIT`｛.depreted text role=“term”｝=\“1\”），您只需使用任何允许查看存储库历史记录的 Git 命令。以下是一种方法：

```
$ git log -p
```

You need to realize, however, that this method does show changes that are not significant (e.g. a package\'s size changing by a few bytes).

> 然而，您需要意识到，此方法确实显示了不显著的更改（例如，包的大小更改了几个字节）。

There is a command-line tool called `buildhistory-diff`, though, that queries the Git repository and prints just the differences that might be significant in human-readable form. Here is an example:

> 不过，有一个名为“buildhistory diff”的命令行工具可以查询 Git 存储库，并以人类可读的形式打印出可能重要的差异

```
$ poky/poky/scripts/buildhistory-diff . HEAD^
Changes to images/qemux86_64/glibc/core-image-minimal (files-in-image.txt):
   /etc/anotherpkg.conf was added
   /sbin/anotherpkg was added
   * (installed-package-names.txt):
   *   anotherpkg was added
Changes to images/qemux86_64/glibc/core-image-minimal (installed-package-names.txt):
   anotherpkg was added
packages/qemux86_64-poky-linux/v86d: PACKAGES: added "v86d-extras"
   * PR changed from "r0" to "r1"
   * PV changed from "0.1.10" to "0.1.12"
packages/qemux86_64-poky-linux/v86d/v86d: PKGSIZE changed from 110579 to 144381 (+30%)
   * PR changed from "r0" to "r1"
   * PV changed from "0.1.10" to "0.1.12"
```

::: note
::: title
Note
:::

The `buildhistory-diff` tool requires the `GitPython` package. Be sure to install it using Pip3 as follows:

> “buildhistory diff”工具需要“GitPython”包。请确保按照以下步骤使用 Pip3 进行安装：

```
$ pip3 install GitPython --user
```

Alternatively, you can install `python3-git` using the appropriate distribution package manager (e.g. `apt`, `dnf`, or `zipper`).

> 或者，您可以使用适当的分发包管理器（例如“apt”、“dnf”或“zipper”）安装“python3-git”。
> :::

To see changes to the build history using a web interface, follow the instruction in the `README` file :yocto\_[git:%60here](git:%60here) \</buildhistory-web/\>\`.

> 要使用 web 界面查看生成历史记录的更改，请按照 `README` 文件中的说明进行操作：yocto\_[git:%60here]（git:%60where）\</buildhistory web/\>\`。

Here is a sample screenshot of the interface:

> 以下是界面的示例屏幕截图：

![image](figures/buildhistory-web.png){width="100.0%"}
