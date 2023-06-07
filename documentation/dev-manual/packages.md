---
tip: translate by baidu@2023-06-07 17:14:43
...
---
title: Working with Packages
----------------------------

This section describes a few tasks that involve packages:

> 本节介绍了一些涉及软件包的任务：

- `dev-manual/packages:excluding packages from an image`{.interpreted-text role="ref"}
- `dev-manual/packages:incrementing a package version`{.interpreted-text role="ref"}
- `dev-manual/packages:handling optional module packaging`{.interpreted-text role="ref"}
- `dev-manual/packages:using runtime package management`{.interpreted-text role="ref"}
- `dev-manual/packages:generating and using signed packages`{.interpreted-text role="ref"}
- `Setting up and running package test (ptest) <dev-manual/packages:testing packages with ptest>`{.interpreted-text role="ref"}

> -`设置和运行包测试（ptest）<devmanual/packages:testing packages with ptest>`｛.depredicted text role=“ref”｝

- `dev-manual/packages:creating node package manager (npm) packages`{.interpreted-text role="ref"}
- `dev-manual/packages:adding custom metadata to packages`{.interpreted-text role="ref"}

# Excluding Packages from an Image

You might find it necessary to prevent specific packages from being installed into an image. If so, you can use several variables to direct the build system to essentially ignore installing recommended packages or to not install a package at all.

> 您可能会发现有必要防止将特定的程序包安装到映像中。如果是这样的话，您可以使用几个变量来指导构建系统基本上忽略安装推荐的包，或者根本不安装包。

The following list introduces variables you can use to prevent packages from being installed into your image. Each of these variables only works with IPK and RPM package types, not for Debian packages. Also, you can use these variables from your `local.conf` file or attach them to a specific image recipe by using a recipe name override. For more detail on the variables, see the descriptions in the Yocto Project Reference Manual\'s glossary chapter.

> 下面的列表介绍了可以用来防止程序包安装到映像中的变量。这些变量中的每一个仅适用于 IPK 和 RPM 包类型，而不适用于 Debian 包。此外，您可以使用“local.conf”文件中的这些变量，也可以使用配方名称覆盖将它们附加到特定的图像配方中。有关变量的更多详细信息，请参阅 Yocto 项目参考手册词汇表章节中的描述。

- `BAD_RECOMMENDATIONS`{.interpreted-text role="term"}: Use this variable to specify \"recommended-only\" packages that you do not want installed.

> -`BAD_RECommendaTION`｛.explored text role=“term”｝：使用此变量可以指定不希望安装的“仅推荐”程序包。

- `NO_RECOMMENDATIONS`{.interpreted-text role="term"}: Use this variable to prevent all \"recommended-only\" packages from being installed.

> -`NO_RECOMMENTATIONs`｛.depredicted text role=“term”｝：使用此变量可阻止安装所有“仅推荐”的程序包。

- `PACKAGE_EXCLUDE`{.interpreted-text role="term"}: Use this variable to prevent specific packages from being installed regardless of whether they are \"recommended-only\" or not. You need to realize that the build process could fail with an error when you prevent the installation of a package whose presence is required by an installed package.

> -`PACKAGE_EXCLUDE`｛.explored text role=“term”｝：使用此变量可以防止安装特定的程序包，无论它们是否“仅推荐”。您需要意识到，当您阻止安装已安装程序包所需的程序包时，生成过程可能会失败并出现错误。

# Incrementing a Package Version

This section provides some background on how binary package versioning is accomplished and presents some of the services, variables, and terminology involved.

> 本节提供了有关如何实现二进制包版本控制的一些背景知识，并介绍了所涉及的一些服务、变量和术语。

In order to understand binary package versioning, you need to consider the following:

> 为了理解二进制包版本控制，您需要考虑以下内容：

- Binary Package: The binary package that is eventually built and installed into an image.
- Binary Package Version: The binary package version is composed of two components \-\-- a version and a revision.

> -二进制包版本：二进制包版本由两个部分组成——一个版本和一个修订版。

::: note
::: title

Note

> 笔记
> :::

Technically, a third component, the \"epoch\" (i.e. `PE`{.interpreted-text role="term"}) is involved but this discussion for the most part ignores `PE`{.interpreted-text role="term"}.

> 从技术上讲，涉及第三个组成部分，即“epoch\”（即 `PE`｛.解释文本角色=“术语”｝），但本讨论在很大程度上忽略了 `PE`{.解释文字角色=“词汇”｝。
> :::

The version and revision are taken from the `PV`{.interpreted-text role="term"} and `PR`{.interpreted-text role="term"} variables, respectively.

> 版本和修订分别取自 `PV`{.depreted text role=“term”}和 `PR`{.deploted text role=“term”}变量。

- `PV`{.interpreted-text role="term"}: The recipe version. `PV`{.interpreted-text role="term"} represents the version of the software being packaged. Do not confuse `PV`{.interpreted-text role="term"} with the binary package version.

> -`PV`｛.explored text role=“term”｝：配方版本 `PV `｛.explored text role=“term”｝表示正在打包的软件的版本。不要将 `PV`｛.explored text role=“term”｝与二进制包版本混淆。

- `PR`{.interpreted-text role="term"}: The recipe revision.
- `SRCPV`{.interpreted-text role="term"}: The OpenEmbedded build system uses this string to help define the value of `PV`{.interpreted-text role="term"} when the source code revision needs to be included in it.

> -`SRCV`｛.depreted text role=“term”｝：OpenEmbedded 构建系统使用此字符串来帮助定义 `PV`｛.repreted text role=“term“｝的值，当源代码修订版需要包含在其中时。

- :yocto_wiki:\`PR Service \</PR_Service\>\`: A network-based service that helps automate keeping package feeds compatible with existing package manager applications such as RPM, APT, and OPKG.

> -：yocto_wiki:\`PR Service\</PR_Service\>\`：一种基于网络的服务，有助于自动保持包提要与现有包管理器应用程序（如 RPM、APT 和 OPKG）兼容。

Whenever the binary package content changes, the binary package version must change. Changing the binary package version is accomplished by changing or \"bumping\" the `PR`{.interpreted-text role="term"} and/or `PV`{.interpreted-text role="term"} values. Increasing these values occurs one of two ways:

> 无论何时二进制程序包内容发生更改，二进制程序包版本都必须更改。更改二进制程序包版本是通过更改或“碰撞”`PR`｛.depredicted text role=“term”｝和/或 `PV`｛.epredicted textrole=”term“｝值来完成的。增加这些值的方式有两种：

- Automatically using a Package Revision Service (PR Service).
- Manually incrementing the `PR`{.interpreted-text role="term"} and/or `PV`{.interpreted-text role="term"} variables.

> -手动递增 `PR`｛.depredicted text role=“term”｝和/或 `PV`｛.epredicted textrole=”term“｝变量。

Given a primary challenge of any build system and its users is how to maintain a package feed that is compatible with existing package manager applications such as RPM, APT, and OPKG, using an automated system is much preferred over a manual system. In either system, the main requirement is that binary package version numbering increases in a linear fashion and that there is a number of version components that support that linear progression. For information on how to ensure package revisioning remains linear, see the \"`dev-manual/packages:automatically incrementing a package version number`{.interpreted-text role="ref"}\" section.

> 考虑到任何构建系统及其用户的主要挑战是如何维护与现有包管理器应用程序（如 RPM、APT 和 OPKG）兼容的包馈送，使用自动化系统比手动系统更可取。在这两种系统中，主要要求都是二进制包版本号以线性方式增加，并且有许多版本组件支持这种线性进展。有关如何确保包修订保持线性的信息，请参阅\“`dev manual/packages:automatically incremental a package version number`｛.depredicted text role=”ref“｝\”一节。

The following three sections provide related information on the PR Service, the manual method for \"bumping\" `PR`{.interpreted-text role="term"} and/or `PV`{.interpreted-text role="term"}, and on how to ensure binary package revisioning remains linear.

> 以下三节提供了有关公共关系服务的相关信息，“碰撞”`PR`｛.depredicted text role=“term”｝和/或 `PV`｛.epredicted textrole=”term“｝的手动方法，以及如何确保二进制包修订保持线性。

## Working With a PR Service

As mentioned, attempting to maintain revision numbers in the `Metadata`{.interpreted-text role="term"} is error prone, inaccurate, and causes problems for people submitting recipes. Conversely, the PR Service automatically generates increasing numbers, particularly the revision field, which removes the human element.

> 如前所述，试图在“元数据”中维护修订号｛.explored text role=“term”｝容易出错、不准确，并会给提交食谱的人带来问题。相反，公共关系服务会自动生成越来越多的数字，尤其是删除人为因素的修订字段。

::: note
::: title
Note
:::

For additional information on using a PR Service, you can see the :yocto_wiki:[PR Service \</PR_Service\>]{.title-ref} wiki page.

> 有关使用 PR 服务的其他信息，您可以查看：yocto_wiki:[PR Service\</PR_Service\>]｛.title-ref｝wiki 页面。
> :::

The Yocto Project uses variables in order of decreasing priority to facilitate revision numbering (i.e. `PE`{.interpreted-text role="term"}, `PV`{.interpreted-text role="term"}, and `PR`{.interpreted-text role="term"} for epoch, version, and revision, respectively). The values are highly dependent on the policies and procedures of a given distribution and package feed.

> Yocto 项目按照优先级递减的顺序使用变量，以便于修订编号（即，历元、版本和修订分别为 `PE`｛.解释文本角色=“术语”｝、`PV`｛..解释文本角色 ＝“术语”}和 `PR`｛.Spredicted 文本角色 ＝”术语“｝）。这些值高度依赖于给定分发和包提要的策略和程序。

Because the OpenEmbedded build system uses \"`signatures <overview-manual/concepts:checksums (signatures)>`{.interpreted-text role="ref"}\", which are unique to a given build, the build system knows when to rebuild packages. All the inputs into a given task are represented by a signature, which can trigger a rebuild when different. Thus, the build system itself does not rely on the `PR`{.interpreted-text role="term"}, `PV`{.interpreted-text role="term"}, and `PE`{.interpreted-text role="term"} numbers to trigger a rebuild. The signatures, however, can be used to generate these values.

> 由于 OpenEmbedded 构建系统使用“`signatures<overview manual/concepts:checksums（signatures）>`{.depredicted text role=“ref”}\”，这对于给定的构建是唯一的，因此构建系统知道何时重建包。给定任务的所有输入都由签名表示，签名可以在不同时触发重建。因此，构建系统本身不依赖于 `PR`｛.解释的文本角色=“术语”｝、`PV`｛.explored 文本角色=”术语“｝和 `PE`｛.Expered 文本角色 ＝”术语“}数字来触发重建。但是，签名可以用于生成这些值。

The PR Service works with both `OEBasic` and `OEBasicHash` generators. The value of `PR`{.interpreted-text role="term"} bumps when the checksum changes and the different generator mechanisms change signatures under different circumstances.

> PR 服务同时使用“OEBasic”和“OEBasicHash”生成器。当校验和发生变化并且不同的生成器机制在不同的情况下更改签名时，`PR`｛.explored text role=“term”｝的值会发生变化。

As implemented, the build system includes values from the PR Service into the `PR`{.interpreted-text role="term"} field as an addition using the form \"`.x`\" so `r0` becomes `r0.1`, `r0.2` and so forth. This scheme allows existing `PR`{.interpreted-text role="term"} values to be used for whatever reasons, which include manual `PR`{.interpreted-text role="term"} bumps, should it be necessary.

> 如所实现的，构建系统将来自 PR 服务的值包括在 `PR`｛.explored text role=“term”｝字段中，作为使用“`.x`\”形式的加法，因此 `r0` 变为 `r0.1`、`r0.2` 等等。该方案允许出于任何原因使用现有的“PR”｛.解释的文本角色=“术语”｝值，其中包括必要时手动的“PR’｛.理解的文本角色 ＝“术语”}碰撞。

By default, the PR Service is not enabled or running. Thus, the packages generated are just \"self consistent\". The build system adds and removes packages and there are no guarantees about upgrade paths but images will be consistent and correct with the latest changes.

> 默认情况下，PR 服务未启用或未运行。因此，生成的包只是“自洽的”。生成系统添加和删除程序包，不能保证升级路径，但映像将与最新更改保持一致和正确。

The simplest form for a PR Service is for a single host development system that builds the package feed (building system). For this scenario, you can enable a local PR Service by setting `PRSERV_HOST`{.interpreted-text role="term"} in your `local.conf` file in the `Build Directory`{.interpreted-text role="term"}:

> PR 服务最简单的形式是针对构建包提要（构建系统）的单个主机开发系统。在这种情况下，您可以通过在“构建目录”中的“local.conf”文件中设置“PRSERV_HOST”｛.depreted text role=“term”｝来启用本地 PR 服务：

```
PRSERV_HOST = "localhost:0"
```

Once the service is started, packages will automatically get increasing `PR`{.interpreted-text role="term"} values and BitBake takes care of starting and stopping the server.

> 一旦服务启动，包将自动获得增加的 `PR`｛.explored text role=“term”｝值，BitBake 负责启动和停止服务器。

If you have a more complex setup where multiple host development systems work against a common, shared package feed, you have a single PR Service running and it is connected to each building system. For this scenario, you need to start the PR Service using the `bitbake-prserv` command:

> 如果您有一个更复杂的设置，其中多个主机开发系统针对一个通用的共享包提要工作，那么您有一台运行的 PR 服务，它连接到每个建筑系统。对于这种情况，您需要使用“bitbake prserv”命令启动 PR 服务：

```
bitbake-prserv --host ip --port port --start
```

In addition to hand-starting the service, you need to update the `local.conf` file of each building system as described earlier so each system points to the server and port.

> 除了手动启动服务外，您还需要更新每个构建系统的“local.conf”文件，如前所述，以便每个系统都指向服务器和端口。

It is also recommended you use build history, which adds some sanity checks to binary package versions, in conjunction with the server that is running the PR Service. To enable build history, add the following to each building system\'s `local.conf` file:

> 还建议您将构建历史记录与运行 PR 服务的服务器结合使用，该历史记录会为二进制包版本添加一些健全性检查。要启用构建历史记录，请将以下内容添加到每个构建系统的“local.conf”文件中：

```
# It is recommended to activate "buildhistory" for testing the PR service
INHERIT += "buildhistory"
BUILDHISTORY_COMMIT = "1"
```

For information on build history, see the \"`dev-manual/build-quality:maintaining build output quality`{.interpreted-text role="ref"}\" section.

> 有关生成历史记录的信息，请参阅\“`dev manual/build quality:maintaining build output quality`｛.depreted text role=“ref”｝\”一节。

::: note
::: title
Note
:::

The OpenEmbedded build system does not maintain `PR`{.interpreted-text role="term"} information as part of the shared state (sstate) packages. If you maintain an sstate feed, it\'s expected that either all your building systems that contribute to the sstate feed use a shared PR Service, or you do not run a PR Service on any of your building systems. Having some systems use a PR Service while others do not leads to obvious problems.

> OpenEmbedded 构建系统不将 `PR`｛.explored text role=“term”｝信息作为共享状态（sstate）包的一部分进行维护。如果您维护一个 sstate 提要，那么您的所有建筑系统都应该使用共享的 PR 服务，或者您没有在任何建筑系统上运行 PR 服务。有些系统使用公关服务，而另一些系统则不会导致明显的问题。

For more information on shared state, see the \"`overview-manual/concepts:shared state cache`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> 有关共享状态的更多信息，请参阅 Yocto 项目概述和概念手册中的\“`overview manual/concepts:shared state cache`｛.depreced text role=”ref“｝\”一节。
> :::

## Manually Bumping PR

The alternative to setting up a PR Service is to manually \"bump\" the `PR`{.interpreted-text role="term"} variable.

> 设置 PR 服务的另一种选择是手动“碰撞”`PR`｛.explored text role=“term”｝变量。

If a committed change results in changing the package output, then the value of the `PR`{.interpreted-text role="term"} variable needs to be increased (or \"bumped\") as part of that commit. For new recipes you should add the `PR`{.interpreted-text role="term"} variable and set its initial value equal to \"r0\", which is the default. Even though the default value is \"r0\", the practice of adding it to a new recipe makes it harder to forget to bump the variable when you make changes to the recipe in future.

> 如果提交的更改导致更改包输出，则作为提交的一部分，需要增加（或“bump”）`PR`｛.explored text role=“term”｝变量的值。对于新配方，您应该添加 `PR`｛.explored text role=“term”｝变量，并将其初始值设置为默认值\“r0\”。尽管默认值是“r0\”，但将其添加到新配方的做法会使您在将来更改配方时更难忘记更改变量。

Usually, version increases occur only to binary packages. However, if for some reason `PV`{.interpreted-text role="term"} changes but does not increase, you can increase the `PE`{.interpreted-text role="term"} variable (Package Epoch). The `PE`{.interpreted-text role="term"} variable defaults to \"0\".

> 通常，版本增加只发生在二进制包中。但是，如果由于某种原因 `PV`｛.depredicted text role=“term”｝发生变化但没有增加，则可以增加 `PE`｛.epredicted textrole=”term“｝变量（Package Epoch）。`PE`｛.explored text role=“term”｝变量默认为\“0\”。

Binary package version numbering strives to follow the [Debian Version Field Policy Guidelines](https://www.debian.org/doc/debian-policy/ch-controlfields.html). These guidelines define how versions are compared and what \"increasing\" a version means.

> 二进制包版本编号努力遵循 [Debian 版本字段策略指南](https://www.debian.org/doc/debian-policy/ch-controlfields.html)。这些指南定义了如何比较版本以及“增加”版本的含义。

## Automatically Incrementing a Package Version Number

When fetching a repository, BitBake uses the `SRCREV`{.interpreted-text role="term"} variable to determine the specific source code revision from which to build. You set the `SRCREV`{.interpreted-text role="term"} variable to `AUTOREV`{.interpreted-text role="term"} to cause the OpenEmbedded build system to automatically use the latest revision of the software:

> 获取存储库时，BitBake 使用 `SRCREV`｛.explored text role=“term”｝变量来确定要构建的特定源代码修订版。您将 `SRCREV`｛.depredicted text role=“term”｝变量设置为 `AUTOREV`｛.epredicted textrole=”term“｝，以使 OpenEmbedded 构建系统自动使用软件的最新版本：

```
SRCREV = "${AUTOREV}"
```

Furthermore, you need to reference `SRCPV`{.interpreted-text role="term"} in `PV`{.interpreted-text role="term"} in order to automatically update the version whenever the revision of the source code changes. Here is an example:

> 此外，您需要在 `PV`｛.depredicted text role=“term”｝中引用 `SRCV`｛.epredicted textrole=”term“｝，以便在源代码的修订发生更改时自动更新版本。以下是一个示例：

```
PV = "1.0+git${SRCPV}"
```

The OpenEmbedded build system substitutes `SRCPV`{.interpreted-text role="term"} with the following:

> OpenEmbedded 构建系统用以下内容替换“SRCPV”｛.explored text role=“term”｝：

```none
AUTOINC+source_code_revision
```

The build system replaces the `AUTOINC` with a number. The number used depends on the state of the PR Service:

> 生成系统将“AUTOINC”替换为一个数字。使用的号码取决于公关服务的状态：

- If PR Service is enabled, the build system increments the number, which is similar to the behavior of `PR`{.interpreted-text role="term"}. This behavior results in linearly increasing package versions, which is desirable. Here is an example:

> -如果启用了 PR 服务，构建系统会增加数字，这类似于 `PR`｛.explored text role=“term”｝的行为。这种行为导致软件包版本线性增加，这是合乎需要的。以下是一个示例：

```none
hello-world-git_0.0+git0+b6558dd387-r0.0_armv7a-neon.ipk
hello-world-git_0.0+git1+dd2f5c3565-r0.0_armv7a-neon.ipk
```

- If PR Service is not enabled, the build system replaces the `AUTOINC` placeholder with zero (i.e. \"0\"). This results in changing the package version since the source revision is included. However, package versions are not increased linearly. Here is an example:

> -如果未启用 PR 服务，则生成系统会将“AUTOINC”占位符替换为零（即“0\”）。这将导致更改包版本，因为包含了源修订版。但是，软件包版本并不是线性增加的。以下是一个示例：

```none
hello-world-git_0.0+git0+b6558dd387-r0.0_armv7a-neon.ipk
hello-world-git_0.0+git0+dd2f5c3565-r0.0_armv7a-neon.ipk
```

In summary, the OpenEmbedded build system does not track the history of binary package versions for this purpose. `AUTOINC`, in this case, is comparable to `PR`{.interpreted-text role="term"}. If PR server is not enabled, `AUTOINC` in the package version is simply replaced by \"0\". If PR server is enabled, the build system keeps track of the package versions and bumps the number when the package revision changes.

> 总之，OpenEmbedded 构建系统不会为此目的跟踪二进制包版本的历史记录 `在这种情况下，AUTOINC` 相当于 `PR`｛.explored text role=“term”｝。如果 PR 服务器未启用，包版本中的“AUTOINC”将简单地替换为“0\”。如果启用了 PR 服务器，则构建系统会跟踪软件包版本，并在软件包版本更改时更改编号。

# Handling Optional Module Packaging

Many pieces of software split functionality into optional modules (or plugins) and the plugins that are built might depend on configuration options. To avoid having to duplicate the logic that determines what modules are available in your recipe or to avoid having to package each module by hand, the OpenEmbedded build system provides functionality to handle module packaging dynamically.

> 许多软件将功能划分为可选模块（或插件），构建的插件可能取决于配置选项。为了避免重复决定配方中可用模块的逻辑，或者避免手工打包每个模块，OpenEmbedded 构建系统提供了动态处理模块打包的功能。

To handle optional module packaging, you need to do two things:

> 要处理可选模块封装，您需要做两件事：

- Ensure the module packaging is actually done.
- Ensure that any dependencies on optional modules from other recipes are satisfied by your recipe.

> -确保您的配方满足对其他配方中可选模块的任何依赖。

## Making Sure the Packaging is Done

To ensure the module packaging actually gets done, you use the `do_split_packages` function within the `populate_packages` Python function in your recipe. The `do_split_packages` function searches for a pattern of files or directories under a specified path and creates a package for each one it finds by appending to the `PACKAGES`{.interpreted-text role="term"} variable and setting the appropriate values for `FILES:packagename`, `RDEPENDS:packagename`, `DESCRIPTION:packagename`, and so forth. Here is an example from the `lighttpd` recipe:

> 为了确保模块打包真正完成，您可以在配方中的 `populate_packages'Python函数中使用` do_split_packages'函数。“do_split_packages”函数搜索指定路径下的文件或目录模式，并通过将其附加到“packages”｛.explored text role=“term”｝变量并设置“files:packagename”、“RDEPENDS:packagename”和“DESCRIPTION:packagename’”等的适当值，为找到的每个文件或目录创建一个包。以下是“lighttpd”配方中的一个示例：

```
python populate_packages:prepend () {
    lighttpd_libdir = d.expand('${libdir}')
    do_split_packages(d, lighttpd_libdir, '^mod_(.*).so$',
                     'lighttpd-module-%s', 'Lighttpd module for %s',
                      extra_depends='')
}
```

The previous example specifies a number of things in the call to `do_split_packages`.

> 上一个示例在对“do_split_packages”的调用中指定了许多内容。

- A directory within the files installed by your recipe through `ref-tasks-install`{.interpreted-text role="ref"} in which to search.

> -您的配方通过 `ref tasks install` 安装的文件中的一个目录{.depreted text role=“ref”}进行搜索。

- A regular expression used to match module files in that directory. In the example, note the parentheses () that mark the part of the expression from which the module name should be derived.

> -用于匹配该目录中的模块文件的正则表达式。在示例中，请注意括号（），这些括号标记了应该从中派生模块名称的表达式部分。

- A pattern to use for the package names.
- A description for each package.
- An empty string for `extra_depends`, which disables the default dependency on the main `lighttpd` package. Thus, if a file in `${libdir}` called `mod_alias.so` is found, a package called `lighttpd-module-alias` is created for it and the `DESCRIPTION`{.interpreted-text role="term"} is set to \"Lighttpd module for alias\".

> -“extra_depends”的空字符串，它禁用对主“lighttpd”包的默认依赖项。因此，如果在 `${libdir}` 中找到名为 `mod_alias.so` 的文件，则会为其创建名为 `lighttpd module alias` 的包，并将 `DESCRIPTION`{.depreted text role=“term”}设置为\“lighttpd module for alias\”。

Often, packaging modules is as simple as the previous example. However, there are more advanced options that you can use within `do_split_packages` to modify its behavior. And, if you need to, you can add more logic by specifying a hook function that is called for each package. It is also perfectly acceptable to call `do_split_packages` multiple times if you have more than one set of modules to package.

> 通常，封装模块与前面的示例一样简单。但是，您可以在“do_split_packages”中使用更高级的选项来修改其行为。而且，如果需要，可以通过指定为每个包调用的钩子函数来添加更多的逻辑。如果要打包多个模块集，那么多次调用“do_split_packages”也是完全可以接受的。

For more examples that show how to use `do_split_packages`, see the `connman.inc` file in the `meta/recipes-connectivity/connman/` directory of the `poky` `source repository <overview-manual/development-environment:yocto project source repositories>`{.interpreted-text role="ref"}. You can also find examples in `meta/classes-recipe/kernel.bbclass`.

> 有关如何使用“do_split_packages”的更多示例，请参阅“poky `”源存储库<overview-manual/development-environment:yocto项目源存储库>`{.interplated-text role=“ref”}的“meta/recipes-connectivity/connman/`目录中的“connman.inc` 文件。您也可以在“meta/classes recipe/kernel.bbclass”中找到示例。

Following is a reference that shows `do_split_packages` mandatory and optional arguments:

> 以下是显示“do_split_packages”强制参数和可选参数的引用：

```
Mandatory arguments

root
   The path in which to search
file_regex
   Regular expression to match searched files.
   Use parentheses () to mark the part of this
   expression that should be used to derive the
   module name (to be substituted where %s is
   used in other function arguments as noted below)
output_pattern
   Pattern to use for the package names. Must
   include %s.
description
   Description to set for each package. Must
   include %s.

Optional arguments

postinst
   Postinstall script to use for all packages
   (as a string)
recursive
   True to perform a recursive search --- default
   False
hook
   A hook function to be called for every match.
   The function will be called with the following
   arguments (in the order listed):

   f
      Full path to the file/directory match
   pkg
      The package name
   file_regex
      As above
   output_pattern
      As above
   modulename
      The module name derived using file_regex
extra_depends
   Extra runtime dependencies (RDEPENDS) to be
   set for all packages. The default value of None
   causes a dependency on the main package
   (${PN}) --- if you do not want this, pass empty
   string '' for this parameter.
aux_files_pattern
   Extra item(s) to be added to FILES for each
   package. Can be a single string item or a list
   of strings for multiple items. Must include %s.
postrm
   postrm script to use for all packages (as a
   string)
allow_dirs
   True to allow directories to be matched -
   default False
prepend
   If True, prepend created packages to PACKAGES
   instead of the default False which appends them
match_path
   match file_regex on the whole relative path to
   the root rather than just the filename
aux_files_pattern_verbatim
   Extra item(s) to be added to FILES for each
   package, using the actual derived module name
   rather than converting it to something legal
   for a package name. Can be a single string item
   or a list of strings for multiple items. Must
   include %s.
allow_links
   True to allow symlinks to be matched --- default
   False
summary
   Summary to set for each package. Must include %s;
   defaults to description if not set.
```

## Satisfying Dependencies

The second part for handling optional module packaging is to ensure that any dependencies on optional modules from other recipes are satisfied by your recipe. You can be sure these dependencies are satisfied by using the `PACKAGES_DYNAMIC`{.interpreted-text role="term"} variable. Here is an example that continues with the `lighttpd` recipe shown earlier:

> 处理可选模块封装的第二部分是确保您的配方满足对其他配方中可选模块的任何依赖。您可以通过使用 `PACKAGES_DYNAMIC`｛.explored text role=“term”｝变量来确保这些依赖关系得到满足。下面是一个继续前面显示的“lighttpd”配方的示例：

```
PACKAGES_DYNAMIC = "lighttpd-module-.*"
```

The name specified in the regular expression can of course be anything. In this example, it is `lighttpd-module-` and is specified as the prefix to ensure that any `RDEPENDS`{.interpreted-text role="term"} and `RRECOMMENDS`{.interpreted-text role="term"} on a package name starting with the prefix are satisfied during build time. If you are using `do_split_packages` as described in the previous section, the value you put in `PACKAGES_DYNAMIC`{.interpreted-text role="term"} should correspond to the name pattern specified in the call to `do_split_packages`.

> 正则表达式中指定的名称当然可以是任何名称。在本例中，它是“lighttpd module-”，并被指定为前缀，以确保在构建时满足以前缀开头的包名称上的任何“RDEPENDS”｛.explored text role=“term”｝和“RRECOMMENDS”{.explered text rol=“term“｝。如果如前一节所述使用 `do_split_packages'，则在` packages_DYNAMIC `｛.depredicted text role=“term”｝中输入的值应与对` do_ssplt_packages` 的调用中指定的名称模式相对应。

# Using Runtime Package Management

During a build, BitBake always transforms a recipe into one or more packages. For example, BitBake takes the `bash` recipe and produces a number of packages (e.g. `bash`, `bash-bashbug`, `bash-completion`, `bash-completion-dbg`, `bash-completion-dev`, `bash-completion-extra`, `bash-dbg`, and so forth). Not all generated packages are included in an image.

> 在构建过程中，BitBake 总是将配方转换为一个或多个包。例如，BitBake 采用“bash”配方并生成许多包（例如“bash’、“bash-bashbug”、“bash completion”、“bash completion dbg”、“bath completion dev”、“pash completion extra”、“巴什 dbg”等）。并非所有生成的包都包含在图像中。

In several situations, you might need to update, add, remove, or query the packages on a target device at runtime (i.e. without having to generate a new image). Examples of such situations include:

> 在某些情况下，您可能需要在运行时更新、添加、删除或查询目标设备上的包（即，无需生成新映像）。此类情况的例子包括：

- You want to provide in-the-field updates to deployed devices (e.g. security updates).
- You want to have a fast turn-around development cycle for one or more applications that run on your device.

> -您希望在设备上运行的一个或多个应用程序有一个快速的开发周期。

- You want to temporarily install the \"debug\" packages of various applications on your device so that debugging can be greatly improved by allowing access to symbols and source debugging.

> -您希望在设备上临时安装各种应用程序的“debug”包，以便通过允许访问符号和源调试来大大改进调试。

- You want to deploy a more minimal package selection of your device but allow in-the-field updates to add a larger selection for customization.

> -您希望部署设备的最小软件包选择，但允许现场更新以添加更大的选择进行自定义。

In all these situations, you have something similar to a more traditional Linux distribution in that in-field devices are able to receive pre-compiled packages from a server for installation or update. Being able to install these packages on a running, in-field device is what is termed \"runtime package management\".

> 在所有这些情况下，您有一种类似于更传统的 Linux 发行版的东西，即现场设备能够从服务器接收预编译的包以进行安装或更新。能够在运行中的现场设备上安装这些软件包就是所谓的“运行时软件包管理”。

In order to use runtime package management, you need a host or server machine that serves up the pre-compiled packages plus the required metadata. You also need package manipulation tools on the target. The build machine is a likely candidate to act as the server. However, that machine does not necessarily have to be the package server. The build machine could push its artifacts to another machine that acts as the server (e.g. Internet-facing). In fact, doing so is advantageous for a production environment as getting the packages away from the development system\'s `Build Directory`{.interpreted-text role="term"} prevents accidental overwrites.

> 为了使用运行时包管理，您需要一台主机或服务器机器来提供预编译的包以及所需的元数据。您还需要在目标上使用包操作工具。构建机器很可能是充当服务器的候选者。但是，该机器不一定必须是包服务器。构建机器可以将其工件推送给另一台充当服务器的机器（例如面向互联网）。事实上，这样做对生产环境是有利的，因为将包从开发系统的“构建目录”｛.explored text role=“term”｝中移除可以防止意外的覆盖。

A simple build that targets just one device produces more than one package database. In other words, the packages produced by a build are separated out into a couple of different package groupings based on criteria such as the target\'s CPU architecture, the target board, or the C library used on the target. For example, a build targeting the `qemux86` device produces the following three package databases: `noarch`, `i586`, and `qemux86`. If you wanted your `qemux86` device to be aware of all the packages that were available to it, you would need to point it to each of these databases individually. In a similar way, a traditional Linux distribution usually is configured to be aware of a number of software repositories from which it retrieves packages.

> 只针对一个设备的简单构建会生成多个包数据库。换言之，构建产生的包根据目标的 CPU 架构、目标板或目标上使用的 C 库等标准被分为几个不同的包分组。例如，以“qemux86”设备为目标的构建会生成以下三个包数据库：“noarch”、“i586”和“qemu x86”。如果您希望您的“qemux86”设备了解所有可用的软件包，则需要将其分别指向这些数据库中的每一个。以类似的方式，传统的 Linux 发行版通常被配置为知道从中检索包的许多软件存储库。

Using runtime package management is completely optional and not required for a successful build or deployment in any way. But if you want to make use of runtime package management, you need to do a couple things above and beyond the basics. The remainder of this section describes what you need to do.

> 使用运行时包管理是完全可选的，并且不需要以任何方式成功构建或部署。但是，如果您想利用运行时包管理，您需要做一些基础之外的事情。本节的其余部分将介绍您需要做的操作。

## Build Considerations

This section describes build considerations of which you need to be aware in order to provide support for runtime package management.

> 本节介绍了为了提供对运行时包管理的支持而需要注意的构建注意事项。

When BitBake generates packages, it needs to know what format or formats to use. In your configuration, you use the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable to specify the format:

> 当 BitBake 生成包时，它需要知道要使用什么格式。在您的配置中，您使用 `PACKAGE_CLASES`｛.explored text role=“term”｝变量来指定格式：

1. Open the `local.conf` file inside your `Build Directory`{.interpreted-text role="term"} (e.g. `poky/build/conf/local.conf`).

> 1.打开“构建目录”中的“local.conf”文件{.depreced text role=“term”}（例如“poky/Build/conf/local.conf”）。

2. Select the desired package format as follows:

> 2.选择所需的包格式，如下所示：

```

> ```

PACKAGE_CLASSES ?= "package_packageformat"

> PACKAGE_CLASSES？=程序包类别“package_packageformat”

```

> ```
> ```

where packageformat can be \"ipk\", \"rpm\", \"deb\", or \"tar\" which are the supported package formats.

> 其中 packageformat 可以是“ipk”、“rpm\”、“deb”或“tar”，它们是支持的包格式。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

Because the Yocto Project supports four different package formats, you can set the variable with more than one argument. However, the OpenEmbedded build system only uses the first argument when creating an image or Software Development Kit (SDK).

> 由于 Yocto 项目支持四种不同的包格式，因此可以使用多个参数设置变量。但是，OpenEmbedded 构建系统在创建映像或软件开发工具包（SDK）时仅使用第一个参数。

:::

> ：：：

If you would like your image to start off with a basic package database containing the packages in your current build as well as to have the relevant tools available on the target for runtime package management, you can include \"package-management\" in the `IMAGE_FEATURES`{.interpreted-text role="term"} variable. Including \"package-management\" in this configuration variable ensures that when the image is assembled for your target, the image includes the currently-known package databases as well as the target-specific tools required for runtime package management to be performed on the target. However, this is not strictly necessary. You could start your image off without any databases but only include the required on-target package tool(s). As an example, you could include \"opkg\" in your `IMAGE_INSTALL`{.interpreted-text role="term"} variable if you are using the IPK package format. You can then initialize your target\'s package database(s) later once your image is up and running.

> 如果您希望您的映像从包含当前版本中的包的基本包数据库开始，并在目标上提供用于运行时包管理的相关工具，则可以在 `image_FATURE`｛.depreted text role=“term”｝变量中包含\“包管理\”。在此配置变量中包含“包管理”可确保为目标组装映像时，映像包括当前已知的包数据库以及在目标上执行运行时包管理所需的特定于目标的工具。然而，这并不是绝对必要的。您可以在没有任何数据库的情况下启动映像，但只包括所需的目标软件包工具。例如，如果您使用的是 IPK 包格式，则可以在 `IMAGE_INSTALL`｛.respered text role=“term”｝变量中包含\“opkg\”。然后，一旦映像启动并运行，您就可以稍后初始化目标的包数据库。

Whenever you perform any sort of build step that can potentially generate a package or modify existing package, it is always a good idea to re-generate the package index after the build by using the following command:

> 无论何时执行任何可能生成包或修改现有包的生成步骤，最好在生成后使用以下命令重新生成包索引：

```
$ bitbake package-index
```

It might be tempting to build the package and the package index at the same time with a command such as the following:

> 使用以下命令同时构建包和包索引可能很诱人：

```
$ bitbake some-package package-index
```

Do not do this as BitBake does not schedule the package index for after the completion of the package you are building. Consequently, you cannot be sure of the package index including information for the package you just built. Thus, be sure to run the package update step separately after building any packages.

> 不要这样做，因为 BitBake 不会在您正在构建的包完成后为其安排包索引。因此，您无法确定包索引是否包含刚构建的包的信息。因此，请确保在构建任何包之后分别运行包更新步骤。

You can use the `PACKAGE_FEED_ARCHS`{.interpreted-text role="term"}, `PACKAGE_FEED_BASE_PATHS`{.interpreted-text role="term"}, and `PACKAGE_FEED_URIS`{.interpreted-text role="term"} variables to pre-configure target images to use a package feed. If you do not define these variables, then manual steps as described in the subsequent sections are necessary to configure the target. You should set these variables before building the image in order to produce a correctly configured image.

> 您可以使用 `PACKAGE_FEED_ARCHS`｛.explored text role=“term”｝、`PACKAGE_EFEED_BASE_PATHS`{.explered text role=“term”｝和 `PACKAGE_IFED_URIS`｛.explored textrole=”term“｝变量预先配置目标图像以使用包馈送。如果您没有定义这些变量，则需要按照后续章节中描述的手动步骤来配置目标。您应该在构建图像之前设置这些变量，以便生成正确配置的图像。

When your build is complete, your packages reside in the `${TMPDIR}/deploy/packageformat` directory. For example, if `${``TMPDIR`{.interpreted-text role="term"}`}` is `tmp` and your selected package type is RPM, then your RPM packages are available in `tmp/deploy/rpm`.

> 当您的构建完成时，您的包位于“$｛TMPDIR｝/deploy/packageformat”目录中。例如，如果`$｛`TMPDIR`｛.explored text role=“term”｝`｝` 是 `tmp`，并且您选择的程序包类型是 RPM，那么您的 RPM 程序包在 `tmp/deploy/RPM` 中可用。

## Host or Server Machine Setup

Although other protocols are possible, a server using HTTP typically serves packages. If you want to use HTTP, then set up and configure a web server such as Apache 2, lighttpd, or Python web server on the machine serving the packages.

> 尽管其他协议也是可能的，但使用 HTTP 的服务器通常提供包。如果要使用 HTTP，请在提供包的机器上设置和配置 web 服务器，如 Apache 2、lighttpd 或 Python web 服务器。

To keep things simple, this section describes how to set up a Python web server to share package feeds from the developer\'s machine. Although this server might not be the best for a production environment, the setup is simple and straight forward. Should you want to use a different server more suited for production (e.g. Apache 2, Lighttpd, or Nginx), take the appropriate steps to do so.

> 为了简单起见，本节介绍如何设置 Python web 服务器以共享来自开发人员机器的包提要。尽管此服务器可能不是生产环境的最佳服务器，但设置简单而直接。如果您想使用更适合生产的不同服务器（例如 Apache2、Lighttpd 或 Nginx），请采取适当的步骤。

From within the `Build Directory`{.interpreted-text role="term"} where you have built an image based on your packaging choice (i.e. the `PACKAGE_CLASSES`{.interpreted-text role="term"} setting), simply start the server. The following example assumes a `Build Directory`{.interpreted-text role="term"} of `poky/build` and a `PACKAGE_CLASSES`{.interpreted-text role="term"} setting of \"`ref-classes-package_rpm`{.interpreted-text role="ref"}\":

> 在“构建目录”｛.depreced text role=“term”｝中，您已经根据您的打包选择构建了一个映像（即 `PACKAGE_CLASES`｛.deverced text role=”term“｝设置），只需启动服务器即可。以下示例假定 `poky/Build` 的 `Build Directory`｛.expected text role=“term”｝和\“`ref-CLASSES-PACKAGE_rpm`｛.rexpected text-role=“ref”｝\”的 `PACKAGE_CLASES`｛.depreted text-lole=“term“｝设置：

```
$ cd poky/build/tmp/deploy/rpm
$ python3 -m http.server
```

## Target Setup

Setting up the target differs depending on the package management system. This section provides information for RPM, IPK, and DEB.

> 根据包管理系统的不同，设置目标会有所不同。本节提供有关 RPM、IPK 和 DEB 的信息。

### Using RPM

The `Dandified Packaging <DNF_(software)>`{.interpreted-text role="wikipedia"} (DNF) performs runtime package management of RPM packages. In order to use DNF for runtime package management, you must perform an initial setup on the target machine for cases where the `PACKAGE_FEED_*` variables were not set as part of the image that is running on the target. This means if you built your image and did not use these variables as part of the build and your image is now running on the target, you need to perform the steps in this section if you want to use runtime package management.

> `Dantified Packaging＜DNF_（软件）>`{.depreced text role=“wikipedia”}（DNF）执行 RPM 包的运行时包管理。为了使用 DNF 进行运行时包管理，必须在目标计算机上执行初始设置，以防“package_FEED_*”变量未设置为目标上运行的映像的一部分。这意味着，如果您构建了映像，但没有将这些变量作为构建的一部分，并且映像现在正在目标上运行，那么如果您希望使用运行时包管理，则需要执行本节中的步骤。

::: note
::: title
Note
:::

For information on the `PACKAGE_FEED_*` variables, see `PACKAGE_FEED_ARCHS`{.interpreted-text role="term"}, `PACKAGE_FEED_BASE_PATHS`{.interpreted-text role="term"}, and `PACKAGE_FEED_URIS`{.interpreted-text role="term"} in the Yocto Project Reference Manual variables glossary.

> 有关 `PACKAGE_FEED_*` 变量的信息，请参阅 Yocto 项目参考手册变量词汇表中的 `PACKAGE.FEED_ARCHS`｛.explored text role=“term”｝、`PACKAGE_BEED_BASE_PATHS`｛.explored textrole=”term“｝和 `PACKAGE_E_FEED_RIS`｛..explored extrole=。
> :::

On the target, you must inform DNF that package databases are available. You do this by creating a file named `/etc/yum.repos.d/oe-packages.repo` and defining the `oe-packages`.

> 在目标上，您必须通知 DNF 包数据库可用。您可以通过创建一个名为“/etc/yum.repos.d/oe-packages.repo”的文件并定义“oe-packages”来完成此操作。

As an example, assume the target is able to use the following package databases: `all`, `i586`, and `qemux86` from a server named `my.server`. The specifics for setting up the web server are up to you. The critical requirement is that the URIs in the target repository configuration point to the correct remote location for the feeds.

> 例如，假设目标能够使用名为“my.server”的服务器上的以下包数据库：“all”、“i586”和“qemux86”。设置 web 服务器的具体内容由您决定。关键要求是目标存储库配置中的 URI 指向提要的正确远程位置。

::: note
::: title
Note
:::

For development purposes, you can point the web server to the build system\'s `deploy` directory. However, for production use, it is better to copy the package directories to a location outside of the build area and use that location. Doing so avoids situations where the build system overwrites or changes the `deploy` directory.

> 出于开发目的，您可以将 web 服务器指向构建系统的“deploy”目录。但是，对于生产使用，最好将包目录复制到构建区域之外的某个位置并使用该位置。这样做可以避免生成系统覆盖或更改“deploy”目录的情况。
> :::

When telling DNF where to look for the package databases, you must declare individual locations per architecture or a single location used for all architectures. You cannot do both:

> 当告诉 DNF 在哪里查找包数据库时，必须为每个体系结构声明单独的位置，或者为所有体系结构声明单个位置。您不能同时执行以下操作：

- *Create an Explicit List of Architectures:* Define individual base URLs to identify where each package database is located:

> -*创建体系结构的显式列表：*定义各个基本 URL 以标识每个包数据库的位置：

```none
[oe-packages]
baseurl=http://my.server/rpm/i586  http://my.server/rpm/qemux86 http://my.server/rpm/all
```

This example informs DNF about individual package databases for all three architectures.

> 此示例通知 DNF 关于所有三种体系结构的各个包数据库。

- *Create a Single (Full) Package Index:* Define a single base URL that identifies where a full package database is located:

> -*创建单个（完整）包索引：*定义单个基本 URL，用于标识完整包数据库的位置：

```
[oe-packages]
baseurl=http://my.server/rpm
```

This example informs DNF about a single package database that contains all the package index information for all supported architectures.

> 此示例通知 DNF 关于单个包数据库的信息，该数据库包含所有支持的体系结构的所有包索引信息。

Once you have informed DNF where to find the package databases, you need to fetch them:

> 一旦您通知 DNF 在哪里可以找到包数据库，您就需要获取它们：

```none
# dnf makecache
```

DNF is now able to find, install, and upgrade packages from the specified repository or repositories.

> DNF 现在可以从指定的一个或多个存储库中查找、安装和升级软件包。

::: note
::: title
Note
:::

See the [DNF documentation](https://dnf.readthedocs.io/en/latest/) for additional information.

> 参见 [DNF 文档](https://dnf.readthedocs.io/en/latest/)以获取更多信息。
> :::

### Using IPK

The `opkg` application performs runtime package management of IPK packages. You must perform an initial setup for `opkg` on the target machine if the `PACKAGE_FEED_ARCHS`{.interpreted-text role="term"}, `PACKAGE_FEED_BASE_PATHS`{.interpreted-text role="term"}, and `PACKAGE_FEED_URIS`{.interpreted-text role="term"} variables have not been set or the target image was built before the variables were set.

> “opkg”应用程序执行 IPK 包的运行时包管理。如果尚未设置 `PACKAGE_FEED_ARCHS`｛.explored text role=“term”｝、`PACKAGE_BEED_BASE_PATHS`｛..explored textrole=”term“｝和 `PACKAGE_EFED_RIS`｛.explored extrole=“term”｝变量，或者在设置变量之前构建了目标映像，则必须在目标计算机上执行 `opkg` 的初始设置。

The `opkg` application uses configuration files to find available package databases. Thus, you need to create a configuration file inside the `/etc/opkg/` directory, which informs `opkg` of any repository you want to use.

> “opkg”应用程序使用配置文件查找可用的包数据库。因此，您需要在“/etc/opkg/”目录中创建一个配置文件，该文件将通知“opkg”您要使用的任何存储库。

As an example, suppose you are serving packages from a `ipk/` directory containing the `i586`, `all`, and `qemux86` databases through an HTTP server named `my.server`. On the target, create a configuration file (e.g. `my_repo.conf`) inside the `/etc/opkg/` directory containing the following:

> 例如，假设您通过名为“my.server”的 HTTP 服务器从包含“i586”、“all”和“qemux86”数据库的“ipk/”目录中提供包。在目标上，在“/etc/opkg/”目录内创建一个配置文件（例如“my_repo.conf”），其中包含以下内容：

```none
src/gz all http://my.server/ipk/all
src/gz i586 http://my.server/ipk/i586
src/gz qemux86 http://my.server/ipk/qemux86
```

Next, instruct `opkg` to fetch the repository information:

> 接下来，指示“opkg”获取存储库信息：

```none
# opkg update
```

The `opkg` application is now able to find, install, and upgrade packages from the specified repository.

> “opkg”应用程序现在可以从指定的存储库中查找、安装和升级软件包。

### Using DEB

The `apt` application performs runtime package management of DEB packages. This application uses a source list file to find available package databases. You must perform an initial setup for `apt` on the target machine if the `PACKAGE_FEED_ARCHS`{.interpreted-text role="term"}, `PACKAGE_FEED_BASE_PATHS`{.interpreted-text role="term"}, and `PACKAGE_FEED_URIS`{.interpreted-text role="term"} variables have not been set or the target image was built before the variables were set.

> “apt”应用程序执行 DEB 包的运行时包管理。此应用程序使用源列表文件来查找可用的包数据库。如果尚未设置 `PACKAGE_FEED_ARCHS`｛.explored text role=“term”｝、`PACKAGE_BEED_BASE_PATHS`｛..explored textrole=”term“｝和 `PACKAGE.FEED_RIS`｛.explored extrole=“term”｝变量，或者在设置变量之前构建了目标映像，则必须在目标计算机上执行 `apt'的初始设置。

To inform `apt` of the repository you want to use, you might create a list file (e.g. `my_repo.list`) inside the `/etc/apt/sources.list.d/` directory. As an example, suppose you are serving packages from a `deb/` directory containing the `i586`, `all`, and `qemux86` databases through an HTTP server named `my.server`. The list file should contain:

> 要通知“apt”您要使用的存储库，您可以在“/etc/apt/sources.list.d/`”目录中创建一个列表文件（例如“my_repo.list”）。例如，假设您通过名为“my.server”的 HTTP 服务器从包含“i586”、“all”和“qemux86”数据库的“deb/”目录提供包。列表文件应包含：

```none
deb http://my.server/deb/all ./
deb http://my.server/deb/i586 ./
deb http://my.server/deb/qemux86 ./
```

Next, instruct the `apt` application to fetch the repository information:

> 接下来，指示“apt”应用程序获取存储库信息：

```none
$ sudo apt update
```

After this step, `apt` is able to find, install, and upgrade packages from the specified repository.

> 在此步骤之后，“apt”能够从指定的存储库中查找、安装和升级包。

# Generating and Using Signed Packages

In order to add security to RPM packages used during a build, you can take steps to securely sign them. Once a signature is verified, the OpenEmbedded build system can use the package in the build. If security fails for a signed package, the build system stops the build.

> 为了为构建过程中使用的 RPM 包添加安全性，您可以采取措施对其进行安全签名。一旦验证了签名，OpenEmbedded 构建系统就可以在构建中使用该包。如果签名包的安全性失败，生成系统将停止生成。

This section describes how to sign RPM packages during a build and how to use signed package feeds (repositories) when doing a build.

> 本节介绍如何在构建过程中对 RPM 包进行签名，以及在进行构建时如何使用签名的包提要（存储库）。

## Signing RPM Packages

To enable signing RPM packages, you must set up the following configurations in either your `local.config` or `distro.config` file:

> 要启用对 RPM 包的签名，必须在“local.config”或“distro.config”文件中设置以下配置：

```
# Inherit sign_rpm.bbclass to enable signing functionality
INHERIT += " sign_rpm"
# Define the GPG key that will be used for signing.
RPM_GPG_NAME = "key_name"
# Provide passphrase for the key
RPM_GPG_PASSPHRASE = "passphrase"
```

::: note
::: title
Note
:::

Be sure to supply appropriate values for both [key_name]{.title-ref} and [passphrase]{.title-ref}.

> 请确保为[key_name]｛.title-ref}和[passphrase]｛.title-ref｝提供适当的值。
> :::

Aside from the `RPM_GPG_NAME` and `RPM_GPG_PASSPHRASE` variables in the previous example, two optional variables related to signing are available:

> 除了前面示例中的“RPM_GPG_NAME”和“RPM_GPS_PASSPHRASE”变量外，还有两个与签名相关的可选变量：

- *GPG_BIN:* Specifies a `gpg` binary/wrapper that is executed when the package is signed.
- *GPG_PATH:* Specifies the `gpg` home directory used when the package is signed.

## Processing Package Feeds

In addition to being able to sign RPM packages, you can also enable signed package feeds for IPK and RPM packages.

> 除了能够签署 RPM 包，您还可以为 IPK 和 RPM 包启用已签署的包提要。

The steps you need to take to enable signed package feed use are similar to the steps used to sign RPM packages. You must define the following in your `local.config` or `distro.config` file:

> 您需要采取的步骤来启用已签名的程序包提要的使用与用于签名 RPM 程序包的步骤类似。您必须在“local.config”或“distro.config”文件中定义以下内容：

```
INHERIT += "sign_package_feed"
PACKAGE_FEED_GPG_NAME = "key_name"
PACKAGE_FEED_GPG_PASSPHRASE_FILE = "path_to_file_containing_passphrase"
```

For signed package feeds, the passphrase must be specified in a separate file, which is pointed to by the `PACKAGE_FEED_GPG_PASSPHRASE_FILE` variable. Regarding security, keeping a plain text passphrase out of the configuration is more secure.

> 对于已签名的程序包提要，必须在一个单独的文件中指定密码短语，该文件由 `package_FEED_PG_passphrase_file` 变量指向。关于安全性，将纯文本密码短语排除在配置之外更安全。

Aside from the `PACKAGE_FEED_GPG_NAME` and `PACKAGE_FEED_GPG_PASSPHRASE_FILE` variables, three optional variables related to signed package feeds are available:

> 除了 `PACKAGE_FEED_GPG_NAME` 和 `PACKAGE.FEED_PG_PASSPHRASE_FILE` 变量外，还有三个与签名包提要相关的可选变量：

- *GPG_BIN* Specifies a `gpg` binary/wrapper that is executed when the package is signed.
- *GPG_PATH:* Specifies the `gpg` home directory used when the package is signed.
- *PACKAGE_FEED_GPG_SIGNATURE_TYPE:* Specifies the type of `gpg` signature. This variable applies only to RPM and IPK package feeds. Allowable values for the `PACKAGE_FEED_GPG_SIGNATURE_TYPE` are \"ASC\", which is the default and specifies ascii armored, and \"BIN\", which specifies binary.

> -*PACKAGE_FEED_PG_SIGNATURE_TYPE:*指定“GPG”签名的类型。此变量仅适用于 RPM 和 IPK 程序包馈送。`PACKAGE_FEED_PG_SIGNATURE_TYPE` 的允许值为\“ASC\”和\“BIN\”，前者是默认值，指定 ascii 铠装，后者指定二进制。

# Testing Packages With ptest

A Package Test (ptest) runs tests against packages built by the OpenEmbedded build system on the target machine. A ptest contains at least two items: the actual test, and a shell script (`run-ptest`) that starts the test. The shell script that starts the test must not contain the actual test \-\-- the script only starts the test. On the other hand, the test can be anything from a simple shell script that runs a binary and checks the output to an elaborate system of test binaries and data files.

> 包测试（ptest）针对 OpenEmbedded 构建系统在目标计算机上构建的包运行测试。ptest 至少包含两项：实际测试和启动测试的 shell 脚本（“run ptest”）。启动测试的 shell 脚本不能包含实际测试\-该脚本仅启动测试。另一方面，测试可以是任何东西，从运行二进制文件并检查输出的简单 shell 脚本到复杂的测试二进制文件和数据文件系统。

The test generates output in the format used by Automake:

> 测试以 Automake 使用的格式生成输出：

```
result: testname
```

where the result can be `PASS`, `FAIL`, or `SKIP`, and the testname can be any identifying string.

> 其中结果可以是“通过”、“失败”或“跳过”，测试名称可以是任何标识字符串。

For a list of Yocto Project recipes that are already enabled with ptest, see the :yocto_wiki:[Ptest \</Ptest\>]{.title-ref} wiki page.

> 有关已使用 ptest 启用的 Yocto 项目配方的列表，请参阅：Yocto_wiki:[ptest\</ptest\>]｛.title-ref｝wiki 页面。

::: note
::: title
Note
:::

A recipe is \"ptest-enabled\" if it inherits the `ref-classes-ptest`{.interpreted-text role="ref"} class.

> 如果配方继承了 `ref classes ptest`｛.explored text role=“ref”｝类，则该配方为“ptest enabled”。
> :::

## Adding ptest to Your Build

To add package testing to your build, add the `DISTRO_FEATURES`{.interpreted-text role="term"} and `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} variables to your `local.conf` file, which is found in the `Build Directory`{.interpreted-text role="term"}:

> 若要将包测试添加到您的生成中，请将 `DISTRO_FEATURE`｛.expected text role=“term”｝和 `EXTRA_IMAGE_FEATURES`｛.dexpected textrole=”term“｝变量添加到 `local.conf` 文件中，该文件位于 `build Directory`｛.rexpected text role=

```
DISTRO_FEATURES:append = " ptest"
EXTRA_IMAGE_FEATURES += "ptest-pkgs"
```

Once your build is complete, the ptest files are installed into the `/usr/lib/package/ptest` directory within the image, where `package` is the name of the package.

> 构建完成后，ptest 文件将安装到映像中的“/usr/lib/package/ptest”目录中，其中“package”是包的名称。

## Running ptest

The `ptest-runner` package installs a shell script that loops through all installed ptest test suites and runs them in sequence. Consequently, you might want to add this package to your image.

> “ptest runner”包安装一个 shell 脚本，该脚本在所有已安装的 ptest 测试套件中循环并按顺序运行。因此，您可能希望将此程序包添加到您的映像中。

## Getting Your Package Ready

In order to enable a recipe to run installed ptests on target hardware, you need to prepare the recipes that build the packages you want to test. Here is what you have to do for each recipe:

> 为了使配方能够在目标硬件上运行已安装的 ptests，您需要准备用于构建要测试的包的配方。以下是您对每个食谱必须做的操作：

- *Be sure the recipe inherits the* `ref-classes-ptest`{.interpreted-text role="ref"} *class:* Include the following line in each recipe:

> -*确保配方继承* `ref classes ptest`｛.explored text role=“ref”｝*class:*在每个配方中包括以下行：

```
inherit ptest
```

- *Create run-ptest:* This script starts your test. Locate the script where you will refer to it using `SRC_URI`{.interpreted-text role="term"}. Here is an example that starts a test for `dbus`:

> -*创建运行 ptest:*此脚本启动测试。使用 `SRC_URI`｛.explored text role=“term”｝找到要引用的脚本。下面是一个启动“dbus”测试的示例：

```
#!/bin/sh
cd test
make -k runtest-TESTS
```

- *Ensure dependencies are met:* If the test adds build or runtime dependencies that normally do not exist for the package (such as requiring \"make\" to run the test suite), use the `DEPENDS`{.interpreted-text role="term"} and `RDEPENDS`{.interpreted-text role="term"} variables in your recipe in order for the package to meet the dependencies. Here is an example where the package has a runtime dependency on \"make\":

> -*确保满足依赖项：*如果测试添加了包通常不存在的构建或运行时依赖项（例如要求\“make\”运行测试套件），请在配方中使用 `DEPENDS`｛.explored text role=“term”｝和 `RDEPENDS`{.explered text rol=“term“｝变量，以便包满足依赖项。以下是一个包在运行时依赖于\“make\”的示例：

```
RDEPENDS:${PN}-ptest += "make"
```

- *Add a function to build the test suite:* Not many packages support cross-compilation of their test suites. Consequently, you usually need to add a cross-compilation function to the package.

> -*添加一个函数来构建测试套件：*没有多少包支持交叉编译它们的测试套件。因此，您通常需要向包中添加一个交叉编译函数。

Many packages based on Automake compile and run the test suite by using a single command such as `make check`. However, the host `make check` builds and runs on the same computer, while cross-compiling requires that the package is built on the host but executed for the target architecture (though often, as in the case for ptest, the execution occurs on the host). The built version of Automake that ships with the Yocto Project includes a patch that separates building and execution. Consequently, packages that use the unaltered, patched version of `make check` automatically cross-compiles.

> 许多基于 Automake 的软件包通过使用单个命令（如“make-check”）编译和运行测试套件。然而，主机“make-check”在同一台计算机上构建和运行，而交叉编译要求包在主机上构建，但针对目标体系结构执行（尽管通常，如 ptest 的情况，执行发生在主机上）。Yocto 项目附带的 Automake 的构建版本包括一个将构建和执行分离的补丁。因此，使用“make-check”的未更改、修补版本的程序包会自动交叉编译。

Regardless, you still must add a `do_compile_ptest` function to build the test suite. Add a function similar to the following to your recipe:

> 无论如何，您仍然必须添加一个“do_compile_ptest”函数来构建测试套件。在您的食谱中添加类似以下的功能：

```
do_compile_ptest() {
    oe_runmake buildtest-TESTS
}
```

- *Ensure special configurations are set:* If the package requires special configurations prior to compiling the test code, you must insert a `do_configure_ptest` function into the recipe.

> -*确保设置了特殊配置：*如果在编译测试代码之前包需要特殊配置，则必须在配方中插入“do_configure_ptest”函数。

- *Install the test suite:* The `ref-classes-ptest`{.interpreted-text role="ref"} class automatically copies the file `run-ptest` to the target and then runs make `install-ptest` to run the tests. If this is not enough, you need to create a `do_install_ptest` function and make sure it gets called after the \"make install-ptest\" completes.

> -*安装测试套件：*`ref classes ptest`｛.explored text role=“ref”｝类自动将文件 `run ptest` 复制到目标，然后运行 make `Install ptest` 来运行测试。如果这还不够，您需要创建一个“do_install_ptest”函数，并确保在“make-install-ptest”完成后调用它。

# Creating Node Package Manager (NPM) Packages

`NPM <Npm_(software)>`{.interpreted-text role="wikipedia"} is a package manager for the JavaScript programming language. The Yocto Project supports the NPM `fetcher <bitbake-user-manual/bitbake-user-manual-fetching:fetchers>`{.interpreted-text role="ref"}. You can use this fetcher in combination with `devtool </ref-manual/devtool-reference>`{.interpreted-text role="doc"} to create recipes that produce NPM packages.

> `NPM<NPM_（软件）>`{.depreced text role=“wikipedia”}是 JavaScript 编程语言的包管理器。Yocto 项目支持 NPM `fetcher<bitbake用户手册/bitbake使用手册fetchers>`{.depredicted text role=“ref”}。您可以将此 fetcher 与 `devtool</ref manual/devtool-reference>`{.depredicted text role=“doc”}结合使用，以创建生成 NPM 包的配方。

There are two workflows that allow you to create NPM packages using `devtool`: the NPM registry modules method and the NPM project code method.

> 有两个工作流允许您使用“devtool”创建 NPM 包：NPM 注册表模块方法和 NPM 项目代码方法。

::: note
::: title
Note
:::

While it is possible to create NPM recipes manually, using `devtool` is far simpler.

> 虽然可以手动创建 NPM 配方，但使用“devtool”要简单得多。
> :::

Additionally, some requirements and caveats exist.

> 此外，还存在一些要求和注意事项。

## Requirements and Caveats

You need to be aware of the following before using `devtool` to create NPM packages:

> 在使用“devtool”创建 NPM 包之前，您需要注意以下几点：

- Of the two methods that you can use `devtool` to create NPM packages, the registry approach is slightly simpler. However, you might consider the project approach because you do not have to publish your module in the [NPM registry](https://docs.npmjs.com/misc/registry), which is NPM\'s public registry.

> -在可以使用“devtool”创建 NPM 包的两种方法中，注册表方法稍微简单一些。但是，您可能会考虑项目方法，因为您不必在[NPM 注册表中发布模块([https://docs.npmjs.com/misc/registry](https://docs.npmjs.com/misc/registry))，是 NPM 的公共注册中心。

- Be familiar with `devtool </ref-manual/devtool-reference>`{.interpreted-text role="doc"}.
- The NPM host tools need the native `nodejs-npm` package, which is part of the OpenEmbedded environment. You need to get the package by cloning the :oe\_[git:%60meta-openembedded](git:%60meta-openembedded) \</meta-openembedded\>[ repository. Be sure to add the path to your local copy to your ]{.title-ref}[bblayers.conf]{.title-ref}\` file.

> -NPM 主机工具需要本机的“nodejs-NPM”包，它是 OpenEmbedded 环境的一部分。您需要通过克隆：oe\_[git:%60meta openembeded]（git:%60meta openembedded）\</meta openembeded\>[存储库来获取包。请确保将本地副本的路径添加到您的]｛.title-ref｝[bblayers.conf]｛.title-ref｝\`文件中。

- `devtool` cannot detect native libraries in module dependencies. Consequently, you must manually add packages to your recipe.

> -“devtool”无法在模块依赖项中检测本机库。因此，您必须手动将程序包添加到您的配方中。

- While deploying NPM packages, `devtool` cannot determine which dependent packages are missing on the target (e.g. the node runtime `nodejs`). Consequently, you need to find out what files are missing and be sure they are on the target.

> -在部署 NPM 包时，“devtool”无法确定目标上缺少哪些依赖包（例如，节点运行时“nodejs”）。因此，您需要找出丢失的文件，并确保它们在目标上。

- Although you might not need NPM to run your node package, it is useful to have NPM on your target. The NPM package name is `nodejs-npm`.

> -尽管您可能不需要 NPM 来运行您的节点包，但在您的目标上安装 NPM 是很有用的。NPM 程序包名称为“nodejs-NPM”。

## Using the Registry Modules Method

This section presents an example that uses the `cute-files` module, which is a file browser web application.

> 本节介绍了一个使用“可爱文件”模块的示例，该模块是一个文件浏览器 web 应用程序。

::: note
::: title
Note
:::

You must know the `cute-files` module version.

> 你必须知道“可爱的文件”模块的版本。
> :::

The first thing you need to do is use `devtool` and the NPM fetcher to create the recipe:

> 您需要做的第一件事是使用“devtool”和 NPM fetcher 来创建配方：

```
$ devtool add "npm://registry.npmjs.org;package=cute-files;version=1.0.2"
```

The `devtool add` command runs `recipetool create` and uses the same fetch URI to download each dependency and capture license details where possible. The result is a generated recipe.

> “devtool add”命令运行“recipetool create”，并使用相同的 fetch URI 下载每个依赖项，并在可能的情况下捕获许可证详细信息。结果是生成了一个配方。

After running for quite a long time, in particular building the `nodejs-native` package, the command should end as follows:

> 在运行了很长一段时间后，特别是构建了“nodejs native”包，该命令应该以如下方式结束：

```
INFO: Recipe /home/.../build/workspace/recipes/cute-files/cute-files_1.0.2.bb has been automatically created; further editing may be required to make it fully functional
```

The recipe file is fairly simple and contains every license that `recipetool` finds and includes the licenses in the recipe\'s `LIC_FILES_CHKSUM`{.interpreted-text role="term"} variables. You need to examine the variables and look for those with \"unknown\" in the `LICENSE`{.interpreted-text role="term"} field. You need to track down the license information for \"unknown\" modules and manually add the information to the recipe.

> 配方文件相当简单，包含“recipetool”找到的每个许可证，并包括配方的“LIC_FILES_CHKSUM `”变量中的许可证。您需要检查变量，并在` LICENSE`｛.explored text role=“term”｝字段中查找带有“unknown”的变量。您需要追踪“未知”模块的许可证信息，并手动将信息添加到配方中。

`recipetool` creates a \"shrinkwrap\" file for your recipe. Shrinkwrap files capture the version of all dependent modules. Many packages do not provide shrinkwrap files but `recipetool` will create a shrinkwrap file as it runs.

> `recipetool 为您的食谱创建一个“shrinkwrap”文件。收缩包装文件捕获所有相关模块的版本。许多程序包不提供包络处理文件，但“recipetool”会在运行时创建一个包络处理文件。

::: note
::: title
Note
:::

A package is created for each sub-module. This policy is the only practical way to have the licenses for all of the dependencies represented in the license manifest of the image.

> 为每个子模块创建一个包。此策略是获得映像的许可证清单中表示的所有依赖项的许可证的唯一实用方法。
> :::

The `devtool edit-recipe` command lets you take a look at the recipe:

> 使用“devtool edit recipe”命令可以查看配方：

```
$ devtool edit-recipe cute-files
# Recipe created by recipetool
# This is the basis of a recipe and may need further editing in order to be fully functional.
# (Feel free to remove these comments when editing.)

SUMMARY = "Turn any folder on your computer into a cute file browser, available on the local network."
# WARNING: the following LICENSE and LIC_FILES_CHKSUM values are best guesses - it is
# your responsibility to verify that the values are complete and correct.
#
# NOTE: multiple licenses have been detected; they have been separated with &
# in the LICENSE value for now since it is a reasonable assumption that all
# of the licenses apply. If instead there is a choice between the multiple
# licenses then you should change the value to separate the licenses with |
# instead of &. If there is any doubt, check the accompanying documentation
# to determine which situation is applicable.

SUMMARY = "Turn any folder on your computer into a cute file browser, available on the local network."
LICENSE = "BSD-3-Clause & ISC & MIT"
LIC_FILES_CHKSUM = "file://LICENSE;md5=71d98c0a1db42956787b1909c74a86ca \
                    file://node_modules/accepts/LICENSE;md5=bf1f9ad1e2e1d507aef4883fff7103de \
                    file://node_modules/array-flatten/LICENSE;md5=44088ba57cb871a58add36ce51b8de08 \
...
                    file://node_modules/cookie-signature/Readme.md;md5=57ae8b42de3dd0c1f22d5f4cf191e15a"

SRC_URI = " \
    npm://registry.npmjs.org/;package=cute-files;version=${PV} \
    npmsw://${THISDIR}/${BPN}/npm-shrinkwrap.json \
    "

S = "${WORKDIR}/npm"

inherit npm

LICENSE:${PN} = "MIT"
LICENSE:${PN}-accepts = "MIT"
LICENSE:${PN}-array-flatten = "MIT"
...
LICENSE:${PN}-vary = "MIT"
```

Here are three key points in the previous example:

> 以下是上一个示例中的三个关键点：

- `SRC_URI`{.interpreted-text role="term"} uses the NPM scheme so that the NPM fetcher is used.
- `recipetool` collects all the license information. If a sub-module\'s license is unavailable, the sub-module\'s name appears in the comments.

> -“recipetool”收集所有许可证信息。如果子模块的许可证不可用，则子模块的名称将显示在注释中。

- The `inherit npm` statement causes the `ref-classes-npm`{.interpreted-text role="ref"} class to package up all the modules.

> -“inherit npm”语句导致“ref classes npm”｛.explored text role=“ref”｝类打包所有模块。

You can run the following command to build the `cute-files` package:

> 您可以运行以下命令来构建“可爱的文件”包：

```
$ devtool build cute-files
```

Remember that `nodejs` must be installed on the target before your package.

> 请记住，“nodejs”必须在包之前安装在目标上。

Assuming 192.168.7.2 for the target\'s IP address, use the following command to deploy your package:

> 假设目标的 IP 地址为 192.168.7.2，请使用以下命令部署包：

```
$ devtool deploy-target -s cute-files root@192.168.7.2
```

Once the package is installed on the target, you can test the application to show the contents of any directory:

> 一旦包安装在目标上，就可以测试应用程序以显示任何目录的内容：

```
$ cd /usr/lib/node_modules/cute-files
$ cute-files
```

On a browser, go to `http://192.168.7.2:3000` and you see the following:

> 在浏览器上，转到 `http://192.168.7.2:3000` 您会看到以下内容：

![image](figures/cute-files-npm-example.png){width="100.0%"}

You can find the recipe in `workspace/recipes/cute-files`. You can use the recipe in any layer you choose.

> 您可以在“工作区/食谱/可爱的文件”中找到食谱。你可以在你选择的任何图层中使用该配方。

## Using the NPM Projects Code Method

Although it is useful to package modules already in the NPM registry, adding `node.js` projects under development is a more common developer use case.

> 尽管打包已经在 NPM 注册表中的模块很有用，但添加正在开发的“node.js”项目是更常见的开发人员用例。

This section covers the NPM projects code method, which is very similar to the \"registry\" approach described in the previous section. In the NPM projects method, you provide `devtool` with an URL that points to the source files.

> 本节介绍 NPM 项目的代码方法，它与上一节中描述的“注册表”方法非常相似。在 NPM 项目方法中，您为“devtool”提供一个指向源文件的 URL。

Replicating the same example, (i.e. `cute-files`) use the following command:

> 复制相同的示例（即“可爱的文件”）时使用以下命令：

```
$ devtool add https://github.com/martinaglv/cute-files.git
```

The recipe this command generates is very similar to the recipe created in the previous section. However, the `SRC_URI`{.interpreted-text role="term"} looks like the following:

> 此命令生成的配方与上一节中创建的配方非常相似。但是，`SRC_URI`｛.explored text role=“term”｝如下所示：

```
SRC_URI = " \
    git://github.com/martinaglv/cute-files.git;protocol=https;branch=master \
    npmsw://${THISDIR}/${BPN}/npm-shrinkwrap.json \
    "
```

In this example, the main module is taken from the Git repository and dependencies are taken from the NPM registry. Other than those differences, the recipe is basically the same between the two methods. You can build and deploy the package exactly as described in the previous section that uses the registry modules method.

> 在本例中，主模块取自 Git 存储库，依赖项取自 NPM 注册表。除了这些差异之外，这两种方法的配方基本相同。您可以完全按照上一节中使用注册表模块方法的描述构建和部署包。

# Adding custom metadata to packages

The variable `PACKAGE_ADD_METADATA`{.interpreted-text role="term"} can be used to add additional metadata to packages. This is reflected in the package control/spec file. To take the ipk format for example, the CONTROL file stored inside would contain the additional metadata as additional lines.

> 变量 `PACKAGE_ADD_METADATA`｛.explored text role=“term”｝可用于向包添加其他元数据。这反映在包控制/规范文件中。以 ipk 格式为例，存储在其中的 CONTROL 文件将包含作为附加行的附加元数据。

The variable can be used in multiple ways, including using suffixes to set it for a specific package type and/or package. Note that the order of precedence is the same as this list:

> 变量可以以多种方式使用，包括使用后缀为特定的包类型和/或包设置变量。请注意，优先顺序与此列表相同：

- `PACKAGE_ADD_METADATA_<PKGTYPE>:<PN>`
- `PACKAGE_ADD_METADATA_<PKGTYPE>`
- `PACKAGE_ADD_METADATA:<PN>`
- `PACKAGE_ADD_METADATA`{.interpreted-text role="term"}

[\<PKGTYPE\>]{.title-ref} is a parameter and expected to be a distinct name of specific package type:

> [\<PKGTYPE\>]｛.title-ref｝是一个参数，应该是特定包类型的不同名称：

- IPK for .ipk packages
- DEB for .deb packages
- RPM for .rpm packages

[\<PN\>]{.title-ref} is a parameter and expected to be a package name.

> [\<PN\>]｛.title-ref｝是一个参数，应为包名称。

The variable can contain multiple \[one-line\] metadata fields separated by the literal sequence \'\\n\'. The separator can be redefined using the variable flag `separator`.

> 变量可以包含多个由文字序列“\\n\”分隔的\[单行\]元数据字段。可以使用变量标志“separator”重新定义分隔符。

Here is an example that adds two custom fields for ipk packages:

> 下面是一个为 ipk 包添加两个自定义字段的示例：

```
PACKAGE_ADD_METADATA_IPK = "Vendor: CustomIpk\nGroup:Applications/Spreadsheets"
```
