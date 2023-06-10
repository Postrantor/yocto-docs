---
tip: translate by openai@2023-06-10 11:42:19
...
---
title: Working with Packages
----------------------------

This section describes a few tasks that involve packages:

- `dev-manual/packages:excluding packages from an image`
- `dev-manual/packages:incrementing a package version`
- `dev-manual/packages:handling optional module packaging`
- `dev-manual/packages:using runtime package management`
- `dev-manual/packages:generating and using signed packages`
- `Setting up and running package test (ptest) <dev-manual/packages:testing packages with ptest>`
- `dev-manual/packages:creating node package manager (npm) packages`
- `dev-manual/packages:adding custom metadata to packages`

# Excluding Packages from an Image

You might find it necessary to prevent specific packages from being installed into an image. If so, you can use several variables to direct the build system to essentially ignore installing recommended packages or to not install a package at all.

> 你可能会发现有必要阻止特定的软件包被安装到镜像中。如果是这样，你可以使用几个变量来指导构建系统忽略安装推荐的软件包，或者根本不安装软件包。

The following list introduces variables you can use to prevent packages from being installed into your image. Each of these variables only works with IPK and RPM package types, not for Debian packages. Also, you can use these variables from your `local.conf` file or attach them to a specific image recipe by using a recipe name override. For more detail on the variables, see the descriptions in the Yocto Project Reference Manual\'s glossary chapter.

> 以下列表介绍了您可以使用的变量，以防止将软件包安装到您的映像中。这些变量只适用于 IPK 和 RPM 软件包类型，而不适用于 Debian 软件包。此外，您可以从 `local.conf` 文件中使用这些变量，或通过使用配方名称覆盖将它们附加到特定的映像配方。有关变量的更多详细信息，请参阅 Yocto 项目参考手册的术语章节中的描述。

- `BAD_RECOMMENDATIONS`: Use this variable to specify \"recommended-only\" packages that you do not want installed.
- `NO_RECOMMENDATIONS`: Use this variable to prevent all \"recommended-only\" packages from being installed.
- `PACKAGE_EXCLUDE`: Use this variable to prevent specific packages from being installed regardless of whether they are \"recommended-only\" or not. You need to realize that the build process could fail with an error when you prevent the installation of a package whose presence is required by an installed package.

> 使用此变量可以防止安装特定的软件包，无论它们是“仅推荐”的还是不是。您需要意识到，如果阻止安装已安装软件包所需的软件包，构建过程可能会出现错误。

# Incrementing a Package Version

This section provides some background on how binary package versioning is accomplished and presents some of the services, variables, and terminology involved.

> 这一节提供了一些有关如何实现二进制包版本控制的背景知识，介绍了一些服务、变量和术语。

In order to understand binary package versioning, you need to consider the following:

- Binary Package: The binary package that is eventually built and installed into an image.
- Binary Package Version: The binary package version is composed of two components \-\-- a version and a revision.

  ::: note
  ::: title
  Note
  :::

  Technically, a third component, the \"epoch\" (i.e. `PE`.

> 技术上，第三个组件“epoch”(即 `PE`。
> :::

The version and revision are taken from the `PV` variables, respectively.

- `PV` with the binary package version.

> `PV` 与二进制包版本混淆。

- `PR`: The recipe revision.
- `SRCPV` when the source code revision needs to be included in it.

> SRCPV：OpenEmbedded 构建系统使用此字符串来帮助定义当需要在其中包含源代码修订时的 PV 值。

- :yocto_wiki:\`PR Service \</PR_Service\>\`: A network-based service that helps automate keeping package feeds compatible with existing package manager applications such as RPM, APT, and OPKG.

> 一种基于网络的服务，可帮助自动将软件包源与现有的软件包管理应用程序(如 RPM、APT 和 OPKG)保持兼容。

Whenever the binary package content changes, the binary package version must change. Changing the binary package version is accomplished by changing or \"bumping\" the `PR` values. Increasing these values occurs one of two ways:

> 每当二进制包内容发生变化时，二进制包版本必须更改。通过更改或“提升”PR 和/或 PV 值来实现更改二进制包版本。这些值的增加有两种方式：

- Automatically using a Package Revision Service (PR Service).
- Manually incrementing the `PR` variables.

Given a primary challenge of any build system and its users is how to maintain a package feed that is compatible with existing package manager applications such as RPM, APT, and OPKG, using an automated system is much preferred over a manual system. In either system, the main requirement is that binary package version numbering increases in a linear fashion and that there is a number of version components that support that linear progression. For information on how to ensure package revisioning remains linear, see the \"`dev-manual/packages:automatically incrementing a package version number`\" section.

> 给定构建系统及其用户的主要挑战之一是如何维护与 RPM、APT 和 OPKG 等现有包管理应用程序兼容的包订阅，使用自动系统比手动系统更受青睐。无论是哪种系统，主要要求都是二进制包版本号以线性方式增加，并具有若干版本组件支持该线性进展。有关如何确保包修订仍保持线性的信息，请参阅“dev-manual/packages:自动增加包版本号”部分。

The following three sections provide related information on the PR Service, the manual method for \"bumping\" `PR`, and on how to ensure binary package revisioning remains linear.

> 以下三个部分提供有关 PR 服务、手动“bumping”PR 和/或 PV 的相关信息，以及如何确保二进制包修订保持线性。

## Working With a PR Service

As mentioned, attempting to maintain revision numbers in the `Metadata` is error prone, inaccurate, and causes problems for people submitting recipes. Conversely, the PR Service automatically generates increasing numbers, particularly the revision field, which removes the human element.

> 按照提到的，试图在元数据中维护修订号是容易出错、不准确的，并且给提交 recipes 的人带来问题。相反，PR 服务会自动生成递增的编号，特别是修订字段，这消除了人为因素。

::: note
::: title
Note
:::

For additional information on using a PR Service, you can see the :yocto_wiki:[PR Service \</PR_Service\>] wiki page.
:::

The Yocto Project uses variables in order of decreasing priority to facilitate revision numbering (i.e. `PE` for epoch, version, and revision, respectively). The values are highly dependent on the policies and procedures of a given distribution and package feed.

> 项目 Yocto 使用变量按照递减的优先级来促进版本号的编号(即使用“PE”分别表示历史、版本和修订)。这些值高度依赖于给定分发和软件包源的政策和程序。

Because the OpenEmbedded build system uses \"`signatures <overview-manual/concepts:checksums (signatures)>` numbers to trigger a rebuild. The signatures, however, can be used to generate these values.

> 因为 OpenEmbedded 构建系统使用"签名(概览手册/概念：校验和(签名))"，它们是特定构建的唯一标识，构建系统知道何时重建包。所有输入到给定任务的签名可以在不同时触发重建。因此，构建系统本身不依赖于 PR，PV 和 PE 数字来触发重建。但是，签名可以用来生成这些值。

The PR Service works with both `OEBasic` and `OEBasicHash` generators. The value of `PR` bumps when the checksum changes and the different generator mechanisms change signatures under different circumstances.

> 服务 PR 与 OEBasic 和 OEBasicHash 发生器一起工作。当校验和更改时，PR 的值会提升，而不同发生器机制会在不同情况下改变签名。

As implemented, the build system includes values from the PR Service into the `PR` bumps, should it be necessary.

> 实施中，构建系统将 PR 服务的值以"r0.x"的形式附加到 PR 字段中，因此 r0 变成 r0.1、r0.2 等。这种方案允许使用现有的 PR 值，其原因包括如果有必要时手动进行 PR 提升。

By default, the PR Service is not enabled or running. Thus, the packages generated are just \"self consistent\". The build system adds and removes packages and there are no guarantees about upgrade paths but images will be consistent and correct with the latest changes.

> 默认情况下，PR 服务未启用或未运行。因此，生成的软件包只是“自我一致”的。构建系统添加和删除软件包，关于升级路径没有保证，但镜像将与最新更改保持一致和正确。

The simplest form for a PR Service is for a single host development system that builds the package feed (building system). For this scenario, you can enable a local PR Service by setting `PRSERV_HOST`:

> 最简单的 PR 服务形式是为单个主机开发系统构建软件包发布(构建系统)。对于此场景，您可以通过在构建目录中的 local.conf 文件中设置 PRSERV_HOST 来启用本地 PR 服务：

```
PRSERV_HOST = "localhost:0"
```

Once the service is started, packages will automatically get increasing `PR` values and BitBake takes care of starting and stopping the server.

> 一旦服务启动，包将自动获得越来越高的 PR 值，而 BitBake 则负责启动和停止服务器。

If you have a more complex setup where multiple host development systems work against a common, shared package feed, you have a single PR Service running and it is connected to each building system. For this scenario, you need to start the PR Service using the `bitbake-prserv` command:

> 如果您有一个更复杂的设置，其中多个主机开发系统针对公共共享软件包馈送进行工作，您有一个单独的 PR 服务正在运行，并且它连接到每个构建系统。对于此场景，您需要使用 `bitbake-prserv` 命令启动 PR 服务：

```
bitbake-prserv --host ip --port port --start
```

In addition to hand-starting the service, you need to update the `local.conf` file of each building system as described earlier so each system points to the server and port.

> 此外，您还需要根据先前的说明，更新每个楼宇系统的 `local.conf` 文件，以便每个系统指向服务器和端口。

It is also recommended you use build history, which adds some sanity checks to binary package versions, in conjunction with the server that is running the PR Service. To enable build history, add the following to each building system\'s `local.conf` file:

> 建议您还使用构建历史记录，它为二进制包版本添加了一些完整性检查，并与运行 PR 服务的服务器一起使用。要启用构建历史记录，请将以下内容添加到每个构建系统的 `local.conf` 文件中：

```
# It is recommended to activate "buildhistory" for testing the PR service
INHERIT += "buildhistory"
BUILDHISTORY_COMMIT = "1"
```

For information on build history, see the \"`dev-manual/build-quality:maintaining build output quality`\" section.

::: note
::: title
Note
:::

The OpenEmbedded build system does not maintain `PR` information as part of the shared state (sstate) packages. If you maintain an sstate feed, it\'s expected that either all your building systems that contribute to the sstate feed use a shared PR Service, or you do not run a PR Service on any of your building systems. Having some systems use a PR Service while others do not leads to obvious problems.

> 开放式嵌入式构建系统不会将 `PR` 信息作为共享状态(sstate)软件包的一部分维护。如果您维护 sstate 提要，预期您的所有构建系统都使用共享的 PR 服务，或者您的所有构建系统都不运行 PR 服务。有些系统使用 PR 服务而其他系统不使用会导致明显的问题。

For more information on shared state, see the \"`overview-manual/concepts:shared state cache`\" section in the Yocto Project Overview and Concepts Manual.

> 更多关于共享状态的信息，请参阅 Yocto 项目概述和概念手册中“overview-manual/concepts：shared state cache”部分。
> :::

## Manually Bumping PR

The alternative to setting up a PR Service is to manually \"bump\" the `PR` variable.

If a committed change results in changing the package output, then the value of the `PR` variable and set its initial value equal to \"r0\", which is the default. Even though the default value is \"r0\", the practice of adding it to a new recipe makes it harder to forget to bump the variable when you make changes to the recipe in future.

> 如果提交的更改导致更改包输出，则需要作为该提交的一部分增加(或“提升”)`PR` 变量，并将其初始值设置为“r0”，这是默认值。尽管默认值为“r0”，但在将来更改配方时，添加它以防止忘记提升变量的做法是有益的。

Usually, version increases occur only to binary packages. However, if for some reason `PV` variable defaults to \"0\".

> 通常，版本增加只适用于二进制包。但是，如果出于某种原因 PV 发生变化而不增加，您可以增加 PE 变量(包纪元)。PE 变量默认为“0”。

Binary package version numbering strives to follow the [Debian Version Field Policy Guidelines](https://www.debian.org/doc/debian-policy/ch-controlfields.html). These guidelines define how versions are compared and what \"increasing\" a version means.

> 二进制包版本号力求遵循 [Debian 版本字段策略指南](https://www.debian.org/doc/debian-policy/ch-controlfields.html)。这些指南定义了如何比较版本以及什么是“增加”版本的意思。

## Automatically Incrementing a Package Version Number

When fetching a repository, BitBake uses the `SRCREV` to cause the OpenEmbedded build system to automatically use the latest revision of the software:

> 当抓取存储库时，BitBake 使用 `SRCREV` 变量来确定要构建的特定源代码修订版本。您将 `SRCREV` 变量设置为 `AUTOREV` 以使 OpenEmbedded 构建系统自动使用软件的最新修订版本：

```
SRCREV = "$"
```

Furthermore, you need to reference `SRCPV` in order to automatically update the version whenever the revision of the source code changes. Here is an example:

> 此外，您需要在 PV 中引用 SRCPV，以便在源代码修订版本更改时自动更新版本。这里有一个例子：

```
PV = "1.0+git$"
```

The OpenEmbedded build system substitutes `SRCPV` with the following:

```none
AUTOINC+source_code_revision
```

The build system replaces the `AUTOINC` with a number. The number used depends on the state of the PR Service:

- If PR Service is enabled, the build system increments the number, which is similar to the behavior of `PR`. This behavior results in linearly increasing package versions, which is desirable. Here is an example:

> 如果启用了 PR 服务，构建系统会递增数字，这类似于 PR 的行为。这种行为会导致包版本呈线性增长，这是可取的。下面是一个例子：

```none
hello-world-git_0.0+git0+b6558dd387-r0.0_armv7a-neon.ipk
hello-world-git_0.0+git1+dd2f5c3565-r0.0_armv7a-neon.ipk
```

- If PR Service is not enabled, the build system replaces the `AUTOINC` placeholder with zero (i.e. \"0\"). This results in changing the package version since the source revision is included. However, package versions are not increased linearly. Here is an example:

> 如果 PR 服务未启用，则构建系统会用零(即“0”)替换 `AUTOINC` 占位符。这会导致包版本发生变化，因为包括源修订版本。但是，包版本不会逐渐增加。以下是一个例子：

```none
hello-world-git_0.0+git0+b6558dd387-r0.0_armv7a-neon.ipk
hello-world-git_0.0+git0+dd2f5c3565-r0.0_armv7a-neon.ipk
```

In summary, the OpenEmbedded build system does not track the history of binary package versions for this purpose. `AUTOINC`, in this case, is comparable to `PR`. If PR server is not enabled, `AUTOINC` in the package version is simply replaced by \"0\". If PR server is enabled, the build system keeps track of the package versions and bumps the number when the package revision changes.

> 总而言之，OpenEmbedded 构建系统不会跟踪二进制包版本的历史，以此实现此目的。在这种情况下，AUTOINC 可以与 PR 相比。如果 PR 服务器未启用，包版本中的 AUTOINC 将被替换为“0”。如果 PR 服务器启用，构建系统将跟踪包版本，并在包修订版本更改时增加数字。

# Handling Optional Module Packaging

Many pieces of software split functionality into optional modules (or plugins) and the plugins that are built might depend on configuration options. To avoid having to duplicate the logic that determines what modules are available in your recipe or to avoid having to package each module by hand, the OpenEmbedded build system provides functionality to handle module packaging dynamically.

> 许多软件将功能分割成可选模块(或插件)，而且所构建的插件可能取决于配置选项。为了避免在您的配方中重复确定可用模块的逻辑，或者避免手动打包每个模块，OpenEmbedded 构建系统提供了处理模块打包的动态功能。

To handle optional module packaging, you need to do two things:

- Ensure the module packaging is actually done.
- Ensure that any dependencies on optional modules from other recipes are satisfied by your recipe.

## Making Sure the Packaging is Done

To ensure the module packaging actually gets done, you use the `do_split_packages` function within the `populate_packages` Python function in your recipe. The `do_split_packages` function searches for a pattern of files or directories under a specified path and creates a package for each one it finds by appending to the `PACKAGES` variable and setting the appropriate values for `FILES:packagename`, `RDEPENDS:packagename`, `DESCRIPTION:packagename`, and so forth. Here is an example from the `lighttpd` recipe:

> 为确保模块打包实际得以完成，您可以在 recipes 中使用 `populate_packages` Python 函数内的 `do_split_packages` 函数。`do_split_packages` 函数会搜索指定路径下的文件或目录的模式，并为其中每一个找到的项目创建一个包，通过附加 `PACKAGES` 变量并设置 `FILES:packagename`、`RDEPENDS:packagename`、`DESCRIPTION:packagename` 等适当的值。以下是 `lighttpd` recipes 中的一个示例：

```
python populate_packages:prepend () {
    lighttpd_libdir = d.expand('$')
    do_split_packages(d, lighttpd_libdir, '^mod_(.*).so$',
                     'lighttpd-module-%s', 'Lighttpd module for %s',
                      extra_depends='')
}
```

The previous example specifies a number of things in the call to `do_split_packages`.

- A directory within the files installed by your recipe through `ref-tasks-install` in which to search.
- A regular expression used to match module files in that directory. In the example, note the parentheses () that mark the part of the expression from which the module name should be derived.

> 在该目录中用于匹配模块文件的正则表达式。在示例中，注意用于标记从中提取模块名称的表达式部分的括号()。

- A pattern to use for the package names.
- A description for each package.
- An empty string for `extra_depends`, which disables the default dependency on the main `lighttpd` package. Thus, if a file in `$ is set to \"Lighttpd module for alias\".

> 给 `extra_depends` 一个空字符串，这将禁用对主 `lighttpd` 软件包的默认依赖关系。因此，如果在 `$` 中找到名为 `mod_alias.so` 的文件，则会为其创建一个名为 `lighttpd-module-alias` 的软件包，并将 `DESCRIPTION` 设置为“Lighttpd 模块别名”。

Often, packaging modules is as simple as the previous example. However, there are more advanced options that you can use within `do_split_packages` to modify its behavior. And, if you need to, you can add more logic by specifying a hook function that is called for each package. It is also perfectly acceptable to call `do_split_packages` multiple times if you have more than one set of modules to package.

> 经常，打包模块就像之前的例子一样简单。然而，您可以在 `do_split_packages` 中使用更高级的选项来修改其行为。如果需要，您可以通过指定一个钩子函数来添加更多的逻辑，该函数将为每个包调用。如果您有多个模块集要打包，多次调用 `do_split_packages` 也是完全可以接受的。

For more examples that show how to use `do_split_packages`, see the `connman.inc` file in the `meta/recipes-connectivity/connman/` directory of the `poky` `source repository <overview-manual/development-environment:yocto project source repositories>`. You can also find examples in `meta/classes-recipe/kernel.bbclass`.

> 要查看有关如何使用 do_split_packages 的更多示例，请参阅 Poky 源代码库中 meta/recipes-connectivity/connman/目录下的 connman.inc 文件。您还可以在 meta/classes-recipe/kernel.bbclass 中找到示例。

Following is a reference that shows `do_split_packages` mandatory and optional arguments:

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
   ($) --- if you do not want this, pass empty
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

The second part for handling optional module packaging is to ensure that any dependencies on optional modules from other recipes are satisfied by your recipe. You can be sure these dependencies are satisfied by using the `PACKAGES_DYNAMIC` variable. Here is an example that continues with the `lighttpd` recipe shown earlier:

> 第二部分处理可选模块打包是确保其他配方中对可选模块的依赖得到满足。您可以通过使用 `PACKAGES_DYNAMIC` 变量来确保这些依赖得到满足。以下是继续 `lighttpd` 配方示例的一个示例：

```
PACKAGES_DYNAMIC = "lighttpd-module-.*"
```

The name specified in the regular expression can of course be anything. In this example, it is `lighttpd-module-` and is specified as the prefix to ensure that any `RDEPENDS` should correspond to the name pattern specified in the call to `do_split_packages`.

> 正则表达式中指定的名称当然可以是任何东西。在本例中，它是 `lighttpd-module-`，并被指定为前缀，以确保在构建时间满足以前缀开头的任何 `RDEPENDS` 和 `RRECOMMENDS`。如果您正在使用上一节中描述的 `do_split_packages`，则放入 `PACKAGES_DYNAMIC` 中的值应与调用 `do_split_packages` 时指定的名称模式相对应。

# Using Runtime Package Management

During a build, BitBake always transforms a recipe into one or more packages. For example, BitBake takes the `bash` recipe and produces a number of packages (e.g. `bash`, `bash-bashbug`, `bash-completion`, `bash-completion-dbg`, `bash-completion-dev`, `bash-completion-extra`, `bash-dbg`, and so forth). Not all generated packages are included in an image.

> 在构建过程中，BitBake 总是将配方转换为一个或多个软件包。例如，BitBake 会处理 `bash` 配方，生成许多软件包(例如 `bash`、`bash-bashbug`、`bash-completion`、`bash-completion-dbg`、`bash-completion-dev`、`bash-completion-extra`、`bash-dbg` 等等)。但是，不是所有生成的软件包都会包含在镜像中。

In several situations, you might need to update, add, remove, or query the packages on a target device at runtime (i.e. without having to generate a new image). Examples of such situations include:

> 在一些情况下，您可能需要在运行时更新、添加、删除或查询目标设备上的软件包(即无需生成新映像)。此类情况的示例包括：

- You want to provide in-the-field updates to deployed devices (e.g. security updates).
- You want to have a fast turn-around development cycle for one or more applications that run on your device.
- You want to temporarily install the \"debug\" packages of various applications on your device so that debugging can be greatly improved by allowing access to symbols and source debugging.

> 你想在你的设备上暂时安装各种应用的“调试”包，以便通过允许访问符号和源调试来大大提高调试能力。

- You want to deploy a more minimal package selection of your device but allow in-the-field updates to add a larger selection for customization.

In all these situations, you have something similar to a more traditional Linux distribution in that in-field devices are able to receive pre-compiled packages from a server for installation or update. Being able to install these packages on a running, in-field device is what is termed \"runtime package management\".

> 在所有这些情况下，您都有类似于传统 Linux 发行版的东西，即现场设备能够从服务器接收预编译的软件包进行安装或更新。能够在运行的现场设备上安装这些软件包就是所谓的“实时软件包管理”。

In order to use runtime package management, you need a host or server machine that serves up the pre-compiled packages plus the required metadata. You also need package manipulation tools on the target. The build machine is a likely candidate to act as the server. However, that machine does not necessarily have to be the package server. The build machine could push its artifacts to another machine that acts as the server (e.g. Internet-facing). In fact, doing so is advantageous for a production environment as getting the packages away from the development system\'s `Build Directory` prevents accidental overwrites.

> 为了使用运行时包管理，您需要一台主机或服务器机器来提供预先编译的包以及所需的元数据。您还需要在目标上安装包操作工具。构建机器很可能会充当服务器。但是，该机器不一定必须是包服务器。构建机器可以将其产物推送到另一台充当服务器的机器(例如面向 Internet)。事实上，在生产环境中这样做是有利的，因为将包从开发系统的“构建目录”中获取可以防止意外覆盖。

A simple build that targets just one device produces more than one package database. In other words, the packages produced by a build are separated out into a couple of different package groupings based on criteria such as the target\'s CPU architecture, the target board, or the C library used on the target. For example, a build targeting the `qemux86` device produces the following three package databases: `noarch`, `i586`, and `qemux86`. If you wanted your `qemux86` device to be aware of all the packages that were available to it, you would need to point it to each of these databases individually. In a similar way, a traditional Linux distribution usually is configured to be aware of a number of software repositories from which it retrieves packages.

> 一个针对仅一个设备的简单构建会产生多个软件包数据库。换句话说，由构建产生的软件包会根据诸如目标 CPU 架构、目标板或目标上使用的 C 库等标准分组到不同的软件包组中。例如，针对 `qemux86` 设备的构建会产生以下三个软件包数据库：`noarch`、`i586` 和 `qemux86`。如果你希望你的 `qemux86` 设备知道所有可用的软件包，你需要将其分别指向这些数据库。类似地，传统的 Linux 发行版通常配置为知晓多个软件存储库，从中获取软件包。

Using runtime package management is completely optional and not required for a successful build or deployment in any way. But if you want to make use of runtime package management, you need to do a couple things above and beyond the basics. The remainder of this section describes what you need to do.

> 使用运行时软件包管理是完全可选的，对于成功构建或部署没有任何必要性。但是，如果您想利用运行时软件包管理，您需要做一些超出基本要求的事情。本节的其余部分将描述您需要做的事情。

## Build Considerations

This section describes build considerations of which you need to be aware in order to provide support for runtime package management.

When BitBake generates packages, it needs to know what format or formats to use. In your configuration, you use the `PACKAGE_CLASSES` variable to specify the format:

> 当 BitBake 生成包时，它需要知道使用什么格式。在您的配置中，您使用 `PACKAGE_CLASSES` 变量来指定格式：

1. Open the `local.conf` file inside your `Build Directory` (e.g. `poky/build/conf/local.conf`).
2. Select the desired package format as follows:

   ```
   PACKAGE_CLASSES ?= "package_packageformat"
   ```

   where packageformat can be \"ipk\", \"rpm\", \"deb\", or \"tar\" which are the supported package formats.

   ::: note
   ::: title
   Note
   :::

   Because the Yocto Project supports four different package formats, you can set the variable with more than one argument. However, the OpenEmbedded build system only uses the first argument when creating an image or Software Development Kit (SDK).

> 由于 Yocto 项目支持四种不同的包格式，您可以使用多个参数设置变量。但是，OpenEmbedded 构建系统在创建映像或软件开发工具包(SDK)时只使用第一个参数。
> :::

If you would like your image to start off with a basic package database containing the packages in your current build as well as to have the relevant tools available on the target for runtime package management, you can include \"package-management\" in the `IMAGE_FEATURES` variable if you are using the IPK package format. You can then initialize your target\'s package database(s) later once your image is up and running.

> 如果您希望镜像以包含当前构建中的包以及在目标上具有运行时包管理所需的相关工具的基本软件包数据库开始，您可以在 `IMAGE_FEATURES` 变量中包括“package-management”。在此配置变量中包括“package-management”可确保，当为您的目标组装镜像时，镜像将包括当前已知的软件包数据库以及用于在目标上执行运行时包管理所需的特定于目标的工具。但是，这不是必需的。您可以在没有任何数据库的情况下开始您的镜像，但只包括所需的目标软件包工具。例如，如果您使用 IPK 软件包格式，可以将“opkg”包括在您的 `IMAGE_INSTALL` 变量中。然后，可以在镜像启动后稍后初始化目标的软件包数据库。

Whenever you perform any sort of build step that can potentially generate a package or modify existing package, it is always a good idea to re-generate the package index after the build by using the following command:

> 每当你执行任何可能生成包或修改现有包的构建步骤时，总是建议在构建后使用以下命令重新生成包索引：

```
$ bitbake package-index
```

It might be tempting to build the package and the package index at the same time with a command such as the following:

```
$ bitbake some-package package-index
```

Do not do this as BitBake does not schedule the package index for after the completion of the package you are building. Consequently, you cannot be sure of the package index including information for the package you just built. Thus, be sure to run the package update step separately after building any packages.

> 不要这么做，因为 BitBake 不会在构建完你要构建的软件包之后安排软件包索引。因此，你不能确定软件包索引是否包含刚构建的软件包的信息。因此，在构建任何软件包之后，一定要单独运行软件包更新步骤。

You can use the `PACKAGE_FEED_ARCHS` variables to pre-configure target images to use a package feed. If you do not define these variables, then manual steps as described in the subsequent sections are necessary to configure the target. You should set these variables before building the image in order to produce a correctly configured image.

> 你可以使用 `PACKAGE_FEED_ARCHS` 变量来预先配置目标镜像以使用软件包源。如果您没有定义这些变量，则需要按照后面部分中描述的步骤手动配置目标。为了生成正确配置的镜像，您应该在构建镜像之前设置这些变量。

When your build is complete, your packages reside in the `$` is `tmp` and your selected package type is RPM, then your RPM packages are available in `tmp/deploy/rpm`.

> 当构建完成后，您的软件包将位于 `$` 是 `tmp`，而您选择的软件包类型是 RPM，那么您的 RPM 软件包可在 `tmp/deploy/rpm` 中找到。

## Host or Server Machine Setup

Although other protocols are possible, a server using HTTP typically serves packages. If you want to use HTTP, then set up and configure a web server such as Apache 2, lighttpd, or Python web server on the machine serving the packages.

> 尽管有其他协议可用，但使用 HTTP 的服务器通常会提供包。如果您想使用 HTTP，请在提供包的机器上设置和配置 Web 服务器，如 Apache 2，lighttpd 或 Python Web 服务器。

To keep things simple, this section describes how to set up a Python web server to share package feeds from the developer\'s machine. Although this server might not be the best for a production environment, the setup is simple and straight forward. Should you want to use a different server more suited for production (e.g. Apache 2, Lighttpd, or Nginx), take the appropriate steps to do so.

> 为了保持简单，本节描述了如何设置一个 Python Web 服务器来共享开发人员机器上的软件包发布。虽然这个服务器可能不适合生产环境，但是设置简单直接。如果您想使用更适合生产环境的其他服务器(例如 Apache 2，Lighttpd 或 Nginx)，请采取适当的步骤进行设置。

From within the `Build Directory`\":

> 从构建目录(即 `PACKAGE_CLASSES` 设置)中，根据您的打包选择构建的镜像，只需启动服务器即可。以下示例假定 `Build Directory` 为 `poky/build`，`PACKAGE_CLASSES` 设置为“`ref-classes-package_rpm`”：

```
$ cd poky/build/tmp/deploy/rpm
$ python3 -m http.server
```

## Target Setup

Setting up the target differs depending on the package management system. This section provides information for RPM, IPK, and DEB.

### Using RPM

The `Dandified Packaging <DNF_(software)>` (DNF) performs runtime package management of RPM packages. In order to use DNF for runtime package management, you must perform an initial setup on the target machine for cases where the `PACKAGE_FEED_*` variables were not set as part of the image that is running on the target. This means if you built your image and did not use these variables as part of the build and your image is now running on the target, you need to perform the steps in this section if you want to use runtime package management.

> DNF(Dandified Packaging)是一种运行时包管理 RPM 包的工具。为了使用 DNF 进行运行时包管理，您必须在目标机器上执行初始设置，以处理在构建镜像时没有使用 PACKAGE_FEED_*变量的情况。这意味着，如果您构建了镜像，没有使用这些变量作为构建的一部分，并且您的镜像现在正在目标上运行，则如果您想要使用运行时包管理，则需要执行本节中的步骤。

::: note
::: title
Note
:::

For information on the `PACKAGE_FEED_*` variables, see `PACKAGE_FEED_ARCHS` in the Yocto Project Reference Manual variables glossary.

> 对于 `PACKAGE_FEED_*` 变量的信息，请参阅 Yocto 项目参考手册变量词汇表中的 `PACKAGE_FEED_ARCHS`。
> :::

On the target, you must inform DNF that package databases are available. You do this by creating a file named `/etc/yum.repos.d/oe-packages.repo` and defining the `oe-packages`.

> 在目标上，您必须通知 DNF，软件包数据库可用。要做到这一点，您需要创建一个名为'/etc/yum.repos.d/oe-packages.repo'的文件，并定义'oe-packages'。

As an example, assume the target is able to use the following package databases: `all`, `i586`, and `qemux86` from a server named `my.server`. The specifics for setting up the web server are up to you. The critical requirement is that the URIs in the target repository configuration point to the correct remote location for the feeds.

> 举个例子，假设目标可以从名为 `my.server` 的服务器上使用以下软件包数据库：`all`、`i586` 和 `qemux86`。设置 Web 服务器的具体细节取决于你。关键的要求是，目标存储库配置中的 URI 指向发布源的正确远程位置。

::: note
::: title
Note
:::

For development purposes, you can point the web server to the build system\'s `deploy` directory. However, for production use, it is better to copy the package directories to a location outside of the build area and use that location. Doing so avoids situations where the build system overwrites or changes the `deploy` directory.

> 为了开发目的，你可以把网络服务器指向构建系统的“部署”目录。但是，在生产环境中，最好将软件包目录复制到构建区域之外的位置，并使用该位置。这样做可以避免构建系统覆盖或更改“部署”目录的情况发生。
> :::

When telling DNF where to look for the package databases, you must declare individual locations per architecture or a single location used for all architectures. You cannot do both:

> 当指示 DNF 在哪里查找包数据库时，您必须为每个架构声明单独的位置，或者使用单个位置用于所有架构。您不能两者兼得：

- *Create an Explicit List of Architectures:* Define individual base URLs to identify where each package database is located:

  ```none
  [oe-packages]
  baseurl=http://my.server/rpm/i586  http://my.server/rpm/qemux86 http://my.server/rpm/all
  ```

  This example informs DNF about individual package databases for all three architectures.
- *Create a Single (Full) Package Index:* Define a single base URL that identifies where a full package database is located:

  ```
  [oe-packages]
  baseurl=http://my.server/rpm
  ```

  This example informs DNF about a single package database that contains all the package index information for all supported architectures.

Once you have informed DNF where to find the package databases, you need to fetch them:

```none
# dnf makecache
```

DNF is now able to find, install, and upgrade packages from the specified repository or repositories.

::: note
::: title
Note
:::

See the [DNF documentation](https://dnf.readthedocs.io/en/latest/) for additional information.
:::

### Using IPK

The `opkg` application performs runtime package management of IPK packages. You must perform an initial setup for `opkg` on the target machine if the `PACKAGE_FEED_ARCHS` variables have not been set or the target image was built before the variables were set.

> 应用程序 `opkg` 对 IPK 软件包进行运行时包管理。如果变量 `PACKAGE_FEED_ARCHS` 尚未设置，或者目标映像在设置变量之前构建，则必须在目标机器上进行 `opkg` 的初始设置。

The `opkg` application uses configuration files to find available package databases. Thus, you need to create a configuration file inside the `/etc/opkg/` directory, which informs `opkg` of any repository you want to use.

> `opkg` 应用程序使用配置文件来查找可用的软件包数据库。因此，您需要在 `/etc/opkg/` 目录中创建一个配置文件，以告知 `opkg` 您要使用的任何存储库。

As an example, suppose you are serving packages from a `ipk/` directory containing the `i586`, `all`, and `qemux86` databases through an HTTP server named `my.server`. On the target, create a configuration file (e.g. `my_repo.conf`) inside the `/etc/opkg/` directory containing the following:

> 假设你正在通过名为 `my.server` 的 HTTP 服务器从 `ipk/` 目录中提供 `i586`、`all` 和 `qemux86` 数据库，作为一个例子。在目标上，在 `/etc/opkg/` 目录中创建一个配置文件(例如 `my_repo.conf`)，其中包含以下内容：

```none
src/gz all http://my.server/ipk/all
src/gz i586 http://my.server/ipk/i586
src/gz qemux86 http://my.server/ipk/qemux86
```

Next, instruct `opkg` to fetch the repository information:

```none
# opkg update
```

The `opkg` application is now able to find, install, and upgrade packages from the specified repository.

### Using DEB

The `apt` application performs runtime package management of DEB packages. This application uses a source list file to find available package databases. You must perform an initial setup for `apt` on the target machine if the `PACKAGE_FEED_ARCHS` variables have not been set or the target image was built before the variables were set.

> `apt` 应用程序执行 DEB 包的运行时包管理。此应用程序使用源列表文件来查找可用的软件包数据库。如果未设置 `PACKAGE_FEED_ARCHS` 变量，或者在设置变量之前构建了目标映像，则必须在目标机器上执行 `apt` 的初始设置。

To inform `apt` of the repository you want to use, you might create a list file (e.g. `my_repo.list`) inside the `/etc/apt/sources.list.d/` directory. As an example, suppose you are serving packages from a `deb/` directory containing the `i586`, `all`, and `qemux86` databases through an HTTP server named `my.server`. The list file should contain:

> 要让 `apt` 知道要使用的存储库，您可以在 `/etc/apt/sources.list.d/` 目录中创建一个列表文件(例如 `my_repo.list`)。例如，假设您正在通过名为 `my.server` 的 HTTP 服务器提供名为 `deb/` 的目录中包含 `i586`，`all` 和 `qemux86` 数据库的软件包。列表文件应包含：

```none
deb http://my.server/deb/all ./
deb http://my.server/deb/i586 ./
deb http://my.server/deb/qemux86 ./
```

Next, instruct the `apt` application to fetch the repository information:

```none
$ sudo apt update
```

After this step, `apt` is able to find, install, and upgrade packages from the specified repository.

# Generating and Using Signed Packages

In order to add security to RPM packages used during a build, you can take steps to securely sign them. Once a signature is verified, the OpenEmbedded build system can use the package in the build. If security fails for a signed package, the build system stops the build.

> 为了增加在构建过程中使用的 RPM 包的安全性，您可以采取措施来安全地签署它们。一旦签名被验证，OpenEmbedded 构建系统就可以在构建中使用该包。如果签名的包的安全性失败，构建系统将停止构建。

This section describes how to sign RPM packages during a build and how to use signed package feeds (repositories) when doing a build.

## Signing RPM Packages

To enable signing RPM packages, you must set up the following configurations in either your `local.config` or `distro.config` file:

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

Be sure to supply appropriate values for both [key_name].
:::

Aside from the `RPM_GPG_NAME` and `RPM_GPG_PASSPHRASE` variables in the previous example, two optional variables related to signing are available:

- *GPG_BIN:* Specifies a `gpg` binary/wrapper that is executed when the package is signed.
- *GPG_PATH:* Specifies the `gpg` home directory used when the package is signed.

## Processing Package Feeds

In addition to being able to sign RPM packages, you can also enable signed package feeds for IPK and RPM packages.

The steps you need to take to enable signed package feed use are similar to the steps used to sign RPM packages. You must define the following in your `local.config` or `distro.config` file:

> 在您的 `local.config` 或 `distro.config` 文件中定义以下内容，以启用签名的软件包配送，这与签署 RPM 软件包所采用的步骤相似：

```
INHERIT += "sign_package_feed"
PACKAGE_FEED_GPG_NAME = "key_name"
PACKAGE_FEED_GPG_PASSPHRASE_FILE = "path_to_file_containing_passphrase"
```

For signed package feeds, the passphrase must be specified in a separate file, which is pointed to by the `PACKAGE_FEED_GPG_PASSPHRASE_FILE` variable. Regarding security, keeping a plain text passphrase out of the configuration is more secure.

> 对于签名的软件包源，必须在一个单独的文件中指定密码短语，该文件由 `PACKAGE_FEED_GPG_PASSPHRASE_FILE` 变量指向。关于安全性，将纯文本密码短语保存在配置之外更安全。

Aside from the `PACKAGE_FEED_GPG_NAME` and `PACKAGE_FEED_GPG_PASSPHRASE_FILE` variables, three optional variables related to signed package feeds are available:

> 除了 `PACKAGE_FEED_GPG_NAME` 和 `PACKAGE_FEED_GPG_PASSPHRASE_FILE` 变量之外，还有三个与签名的软件包源相关的可选变量：

- *GPG_BIN* Specifies a `gpg` binary/wrapper that is executed when the package is signed.
- *GPG_PATH:* Specifies the `gpg` home directory used when the package is signed.
- *PACKAGE_FEED_GPG_SIGNATURE_TYPE:* Specifies the type of `gpg` signature. This variable applies only to RPM and IPK package feeds. Allowable values for the `PACKAGE_FEED_GPG_SIGNATURE_TYPE` are \"ASC\", which is the default and specifies ascii armored, and \"BIN\", which specifies binary.

> *PACKAGE_FEED_GPG_SIGNATURE_TYPE：*指定 `gpg` 签名的类型。此变量仅适用于 RPM 和 IPK 软件包源。允许的值为 `PACKAGE_FEED_GPG_SIGNATURE_TYPE` 是“ASC”，默认值，指示 ASCII 护甲，以及“BIN”，指示二进制。

# Testing Packages With ptest

A Package Test (ptest) runs tests against packages built by the OpenEmbedded build system on the target machine. A ptest contains at least two items: the actual test, and a shell script (`run-ptest`) that starts the test. The shell script that starts the test must not contain the actual test \-\-- the script only starts the test. On the other hand, the test can be anything from a simple shell script that runs a binary and checks the output to an elaborate system of test binaries and data files.

> 测试包(ptest)可以在目标机器上对由 OpenEmbedded 构建系统构建的包进行测试。一个 ptest 至少包含两个项目：实际测试和启动测试的 shell 脚本(“run-ptest”)。启动测试的 shell 脚本不能包含实际测试——该脚本只启动测试。另一方面，测试可以是任何内容，从运行二进制文件并检查输出的简单 shell 脚本到复杂的测试二进制文件和数据文件系统。

The test generates output in the format used by Automake:

```
result: testname
```

where the result can be `PASS`, `FAIL`, or `SKIP`, and the testname can be any identifying string.

For a list of Yocto Project recipes that are already enabled with ptest, see the :yocto_wiki:[Ptest \</Ptest\>] wiki page.

::: note
::: title
Note
:::

A recipe is \"ptest-enabled\" if it inherits the `ref-classes-ptest` class.
:::

## Adding ptest to Your Build

To add package testing to your build, add the `DISTRO_FEATURES`:

> 要在构建中添加包测试，请将 `DISTRO_FEATURES` 和 `EXTRA_IMAGE_FEATURES` 变量添加到位于 `构建目录` 中的 `local.conf` 文件中。

```
DISTRO_FEATURES:append = " ptest"
EXTRA_IMAGE_FEATURES += "ptest-pkgs"
```

Once your build is complete, the ptest files are installed into the `/usr/lib/package/ptest` directory within the image, where `package` is the name of the package.

> 一旦构建完成，ptest 文件就会安装到镜像中的 `/usr/lib/package/ptest` 目录中，其中 `package` 是包的名称。

## Running ptest

The `ptest-runner` package installs a shell script that loops through all installed ptest test suites and runs them in sequence. Consequently, you might want to add this package to your image.

> `ptest-runner` 包安装了一个 shell 脚本，可以循环遍历所有安装的 ptest 测试套件，并依次运行它们。因此，您可能需要将此包添加到您的镜像中。

## Getting Your Package Ready

In order to enable a recipe to run installed ptests on target hardware, you need to prepare the recipes that build the packages you want to test. Here is what you have to do for each recipe:

> 要使 recipes 在目标硬件上运行已安装的 ptests，您需要准备构建您要测试的软件包的 recipes。以下是您为每个 recipes 必须做的事情：

- *Be sure the recipe inherits the* `ref-classes-ptest` *class:* Include the following line in each recipe:

  ```
  inherit ptest
  ```
- *Create run-ptest:* This script starts your test. Locate the script where you will refer to it using `SRC_URI`. Here is an example that starts a test for `dbus`:

> *创建 run-ptest：*此脚本会启动您的测试。使用 `SRC_URI` 将脚本定位到您要引用它的位置。以下是一个启动 `dbus` 测试的示例：

```
#!/bin/sh
cd test
make -k runtest-TESTS
```

- *Ensure dependencies are met:* If the test adds build or runtime dependencies that normally do not exist for the package (such as requiring \"make\" to run the test suite), use the `DEPENDS` variables in your recipe in order for the package to meet the dependencies. Here is an example where the package has a runtime dependency on \"make\":

> 确保依赖关系得到满足：如果测试添加了通常不存在于软件包中的构建或运行时依赖项(例如需要“make”来运行测试套件)，请在配方中使用 `DEPENDS` 和 `RDEPENDS` 变量，以便软件包满足依赖关系。以下是一个软件包具有“make”运行时依赖关系的示例：

```
RDEPENDS:$-ptest += "make"
```

- *Add a function to build the test suite:* Not many packages support cross-compilation of their test suites. Consequently, you usually need to add a cross-compilation function to the package.

> 在许多包中都不支持跨编译它们的测试套件，因此，通常需要为该包添加一个跨编译功能。

Many packages based on Automake compile and run the test suite by using a single command such as `make check`. However, the host `make check` builds and runs on the same computer, while cross-compiling requires that the package is built on the host but executed for the target architecture (though often, as in the case for ptest, the execution occurs on the host). The built version of Automake that ships with the Yocto Project includes a patch that separates building and execution. Consequently, packages that use the unaltered, patched version of `make check` automatically cross-compiles.

> 许多基于 Automake 的包都可以使用单个命令(如 `make check`)来编译和运行测试套件。但是，主机上的 `make check` 构建和运行都是在同一台计算机上进行的，而跨编译需要在主机上构建包，但是需要在目标架构上执行(尽管在 ptest 的情况下，执行通常发生在主机上)。Yocto Project 所捆绑的 Automake 构建版本包含一个补丁，用于分离构建和执行。因此，使用未修改的，补丁版本的 `make check` 的包会自动跨编译。

Regardless, you still must add a `do_compile_ptest` function to build the test suite. Add a function similar to the following to your recipe:

```
do_compile_ptest() {
    oe_runmake buildtest-TESTS
}
```

- *Ensure special configurations are set:* If the package requires special configurations prior to compiling the test code, you must insert a `do_configure_ptest` function into the recipe.

> 确保设置特殊配置：如果包需要在编译测试代码之前设置特殊配置，您必须在配方中插入一个“do_configure_ptest”函数。

- *Install the test suite:* The `ref-classes-ptest` class automatically copies the file `run-ptest` to the target and then runs make `install-ptest` to run the tests. If this is not enough, you need to create a `do_install_ptest` function and make sure it gets called after the \"make install-ptest\" completes.

> 安装测试套件：`ref-classes-ptest` 类自动将文件 `run-ptest` 复制到目标，然后运行 make `install-ptest` 来运行测试。如果这还不够，您需要创建一个 `do_install_ptest` 函数，并确保它在“make install-ptest”完成后调用。

# Creating Node Package Manager (NPM) Packages

`NPM <Npm_(software)>` to create recipes that produce NPM packages.

> NPM(软件)是一个用于 JavaScript 编程语言的包管理器。Yocto 项目支持 NPM 的 fetcher。您可以使用此 fetcher 与 devtool 结合使用，以创建生成 NPM 软件包的配方。

There are two workflows that allow you to create NPM packages using `devtool`: the NPM registry modules method and the NPM project code method.

::: note
::: title
Note
:::

While it is possible to create NPM recipes manually, using `devtool` is far simpler.
:::

Additionally, some requirements and caveats exist.

## Requirements and Caveats

You need to be aware of the following before using `devtool` to create NPM packages:

- Of the two methods that you can use `devtool` to create NPM packages, the registry approach is slightly simpler. However, you might consider the project approach because you do not have to publish your module in the [NPM registry](https://docs.npmjs.com/misc/registry), which is NPM\'s public registry.

> 两种使用 `devtool` 创建 NPM 包的方法中，注册表方法略为简单。然而，您可能会考虑使用项目方法，因为您无需在 [NPM 注册表](https://docs.npmjs.com/misc/registry)(NPM 的公共注册表)中发布模块。

- Be familiar with `devtool </ref-manual/devtool-reference>`.
- The NPM host tools need the native `nodejs-npm` package, which is part of the OpenEmbedded environment. You need to get the package by cloning the :oe_[git:%60meta-openembedded](git:%60meta-openembedded) \</meta-openembedded\>[ repository. Be sure to add the path to your local copy to your ]\` file.

> 需要使用 NPM 主机工具，需要安装 OpenEmbedded 环境中的原生 nodejs-npm 包。您需要通过克隆 oe_[git：`meta-openembedded`](git%EF%BC%9A%60meta-openembedded%60) \</meta-openembedded\>仓库来获取该包。一定要将本地副本的路径添加到您的[bblayers.conf]文件中。

- `devtool` cannot detect native libraries in module dependencies. Consequently, you must manually add packages to your recipe.
- While deploying NPM packages, `devtool` cannot determine which dependent packages are missing on the target (e.g. the node runtime `nodejs`). Consequently, you need to find out what files are missing and be sure they are on the target.

> 在部署 NPM 包时，`devtool` 无法确定目标上缺少哪些依赖包(例如节点运行时 `nodejs`)。因此，您需要找出缺少哪些文件，并确保它们都在目标上。

- Although you might not need NPM to run your node package, it is useful to have NPM on your target. The NPM package name is `nodejs-npm`.

## Using the Registry Modules Method

This section presents an example that uses the `cute-files` module, which is a file browser web application.

::: note
::: title
Note
:::

You must know the `cute-files` module version.
:::

The first thing you need to do is use `devtool` and the NPM fetcher to create the recipe:

```
$ devtool add "npm://registry.npmjs.org;package=cute-files;version=1.0.2"
```

The `devtool add` command runs `recipetool create` and uses the same fetch URI to download each dependency and capture license details where possible. The result is a generated recipe.

> 命令 `devtool add` 运行 `recipetool create`，并使用相同的获取 URI 下载每个依赖项并尽可能捕获许可证详细信息。结果是生成的 recipes。

After running for quite a long time, in particular building the `nodejs-native` package, the command should end as follows:

```
INFO: Recipe /home/.../build/workspace/recipes/cute-files/cute-files_1.0.2.bb has been automatically created; further editing may be required to make it fully functional
```

The recipe file is fairly simple and contains every license that `recipetool` finds and includes the licenses in the recipe\'s `LIC_FILES_CHKSUM` field. You need to track down the license information for \"unknown\" modules and manually add the information to the recipe.

> 菜谱文件相当简单，包含 `recipetool` 发现的每个许可证，并将许可证包含在菜谱的 `LIC_FILES_CHKSUM` 变量中。您需要检查变量，并查找 `LICENSE` 字段中带有“unknown”的变量。您需要跟踪“未知”模块的许可证信息，并将该信息手动添加到菜谱中。

`recipetool` creates a \"shrinkwrap\" file for your recipe. Shrinkwrap files capture the version of all dependent modules. Many packages do not provide shrinkwrap files but `recipetool` will create a shrinkwrap file as it runs.

> `recipetool` 用于为您的配方创建“收缩包”文件。收缩包文件捕获所有依赖模块的版本。许多包不提供收缩包文件，但是 `recipetool` 在运行时会创建一个收缩包文件。

::: note
::: title
Note
:::

A package is created for each sub-module. This policy is the only practical way to have the licenses for all of the dependencies represented in the license manifest of the image.

> 为每个子模块创建一个包。这是唯一实用的方式，可以在镜像的许可证清单中表示所有依赖项的许可证。
> :::

The `devtool edit-recipe` command lets you take a look at the recipe:

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
    npm://registry.npmjs.org/;package=cute-files;version=$ \
    npmsw://$/npm-shrinkwrap.json \
    "

S = "$/npm"

inherit npm

LICENSE:$ = "MIT"
LICENSE:$-accepts = "MIT"
LICENSE:$-array-flatten = "MIT"
...
LICENSE:$-vary = "MIT"
```

Here are three key points in the previous example:

- `SRC_URI` uses the NPM scheme so that the NPM fetcher is used.
- `recipetool` collects all the license information. If a sub-module\'s license is unavailable, the sub-module\'s name appears in the comments.
- The `inherit npm` statement causes the `ref-classes-npm` class to package up all the modules.

You can run the following command to build the `cute-files` package:

```
$ devtool build cute-files
```

Remember that `nodejs` must be installed on the target before your package.

Assuming 192.168.7.2 for the target\'s IP address, use the following command to deploy your package:

```
$ devtool deploy-target -s cute-files root@192.168.7.2
```

Once the package is installed on the target, you can test the application to show the contents of any directory:

```
$ cd /usr/lib/node_modules/cute-files
$ cute-files
```

On a browser, go to `http://192.168.7.2:3000` and you see the following:

![image](figures/cute-files-npm-example.png)

You can find the recipe in `workspace/recipes/cute-files`. You can use the recipe in any layer you choose.

## Using the NPM Projects Code Method

Although it is useful to package modules already in the NPM registry, adding `node.js` projects under development is a more common developer use case.

> 尽管将模块打包到 NPM 注册表中很有用，但添加正在开发中的 node.js 项目更为常见的开发人员用例。

This section covers the NPM projects code method, which is very similar to the \"registry\" approach described in the previous section. In the NPM projects method, you provide `devtool` with an URL that points to the source files.

> 这一节介绍 NPM 项目代码方法，它与上一节描述的“注册表”方法非常相似。在 NPM 项目方法中，您向 devtool 提供指向源文件的 URL。

Replicating the same example, (i.e. `cute-files`) use the following command:

```
$ devtool add https://github.com/martinaglv/cute-files.git
```

The recipe this command generates is very similar to the recipe created in the previous section. However, the `SRC_URI` looks like the following:

> 这个命令生成的配方与前一节中创建的配方非常相似。但是，`SRC_URI` 看起来像下面这样：

```
SRC_URI = " \
    git://github.com/martinaglv/cute-files.git;protocol=https;branch=master \
    npmsw://$/npm-shrinkwrap.json \
    "
```

In this example, the main module is taken from the Git repository and dependencies are taken from the NPM registry. Other than those differences, the recipe is basically the same between the two methods. You can build and deploy the package exactly as described in the previous section that uses the registry modules method.

> 在这个例子中，主模块是从 Git 存储库中获取的，而依赖项是从 NPM 注册表中获取的。除了这些差异之外，两种方法之间的 recipes 基本上是相同的。您可以按照使用注册表模块方法的前面一节中所述的方式构建和部署包。

# Adding custom metadata to packages

The variable `PACKAGE_ADD_METADATA` can be used to add additional metadata to packages. This is reflected in the package control/spec file. To take the ipk format for example, the CONTROL file stored inside would contain the additional metadata as additional lines.

> 变量 `PACKAGE_ADD_METADATA` 可用于向包添加附加元数据。这反映在包控制/规范文件中。以 ipk 格式为例，存储在其中的 CONTROL 文件将包含附加元数据作为附加行。

The variable can be used in multiple ways, including using suffixes to set it for a specific package type and/or package. Note that the order of precedence is the same as this list:

> 变量可以有多种用法，包括使用后缀为特定的包类型和/或包设置它。请注意，优先级的顺序与此列表相同：

- `PACKAGE_ADD_METADATA_<PKGTYPE>:<PN>`
- `PACKAGE_ADD_METADATA_<PKGTYPE>`
- `PACKAGE_ADD_METADATA:<PN>`
- `PACKAGE_ADD_METADATA`

[\<PKGTYPE\>] is a parameter and expected to be a distinct name of specific package type:

- IPK for .ipk packages
- DEB for .deb packages
- RPM for .rpm packages

[\<PN\>] is a parameter and expected to be a package name.

The variable can contain multiple \[one-line] metadata fields separated by the literal sequence \'\\n\'. The separator can be redefined using the variable flag `separator`.

> 变量可以包含由文字序列 '\n' 分隔的多个[单行]元数据字段。可以使用变量标志 `separator` 重新定义分隔符。

Here is an example that adds two custom fields for ipk packages:

```
PACKAGE_ADD_METADATA_IPK = "Vendor: CustomIpk\nGroup:Applications/Spreadsheets"
```
