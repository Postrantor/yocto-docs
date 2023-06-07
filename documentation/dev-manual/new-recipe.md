---
tip: translate by baidu@2023-06-07 17:13:30
...
---
title: Writing a New Recipe
---------------------------

Recipes (`.bb` files) are fundamental components in the Yocto Project environment. Each software component built by the OpenEmbedded build system requires a recipe to define the component. This section describes how to create, write, and test a new recipe.

> 配方（`.bb` 文件）是 Yocto 项目环境中的基本组件。OpenEmbedded 构建系统构建的每个软件组件都需要一个配方来定义组件。本节介绍如何创建、编写和测试新配方。

::: note
::: title
Note
:::

For information on variables that are useful for recipes and for information about recipe naming issues, see the \"`ref-manual/varlocality:recipes`{.interpreted-text role="ref"}\" section of the Yocto Project Reference Manual.

> 有关对配方有用的变量的信息以及有关配方命名问题的信息，请参阅 Yocto 项目参考手册的“`ref manual/varlocality:precipes`｛.explored text role=”ref“｝”一节。
> :::

# Overview

The following figure shows the basic process for creating a new recipe. The remainder of the section provides details for the steps.

> 下图显示了创建新配方的基本过程。本节的其余部分提供了有关步骤的详细信息。

![image](figures/recipe-workflow.png){.align-center width="50.0%"}

# Locate or Automatically Create a Base Recipe

You can always write a recipe from scratch. However, there are three choices that can help you quickly get started with a new recipe:

> 你总是可以从头开始写食谱。然而，有三种选择可以帮助你快速开始新的食谱：

- `devtool add`: A command that assists in creating a recipe and an environment conducive to development.

> -“devtool add”：一个有助于创建有利于开发的配方和环境的命令。

- `recipetool create`: A command provided by the Yocto Project that automates creation of a base recipe based on the source files.

> -`recipetool-create`：Yocto 项目提供的一个命令，可根据源文件自动创建基本配方。

- *Existing Recipes:* Location and modification of an existing recipe that is similar in function to the recipe you need.

> -*现有配方：*与您需要的配方功能相似的现有配方的位置和修改。

::: note
::: title
Note
:::

For information on recipe syntax, see the \"`dev-manual/new-recipe:recipe syntax`{.interpreted-text role="ref"}\" section.

> 有关配方语法的信息，请参阅“`dev manual/new recipe:precipe syntax`｛.depreted text role=“ref”｝\”一节。
> :::

## Creating the Base Recipe Using `devtool add`

The `devtool add` command uses the same logic for auto-creating the recipe as `recipetool create`, which is listed below. Additionally, however, `devtool add` sets up an environment that makes it easy for you to patch the source and to make changes to the recipe as is often necessary when adding a recipe to build a new piece of software to be included in a build.

> “devtool add”命令使用与“recipetool create”相同的逻辑自动创建配方，如下所示。然而，此外，“devtool add”设置了一个环境，使您可以轻松地修补源代码并对配方进行更改，这在添加配方以构建要包含在构建中的新软件时通常是必要的。

You can find a complete description of the `devtool add` command in the \"``sdk-manual/extensible:a closer look at \`\`devtool add\`\` ``{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 您可以在 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的\“`` sdk manual/extensible中找到`devtool add`命令的完整描述：更详细地查看\`` devtool-add\````｛.explored text role=“ref”｝\”部分。

## Creating the Base Recipe Using `recipetool create`

`recipetool create` automates creation of a base recipe given a set of source code files. As long as you can extract or point to the source files, the tool will construct a recipe and automatically configure all pre-build information into the recipe. For example, suppose you have an application that builds using Autotools. Creating the base recipe using `recipetool` results in a recipe that has the pre-build dependencies, license requirements, and checksums configured.

> `recipetoolcreate` 在给定一组源代码文件的情况下自动创建基本配方。只要您可以提取或指向源文件，该工具就会构建一个配方，并自动将所有预构建信息配置到配方中。例如，假设您有一个使用 Autotools 构建的应用程序。使用“recipetool”创建基本配方会生成一个配置了预构建依赖项、许可证要求和校验和的配方。

To run the tool, you just need to be in your `Build Directory`{.interpreted-text role="term"} and have sourced the build environment setup script (i.e. `structure-core-script`{.interpreted-text role="ref"}). To get help on the tool, use the following command:

> 要运行该工具，您只需要在“构建目录”中｛.explored text role=“term”｝，并已获取构建环境设置脚本（即“结构核心脚本”｛.expered text rol=“ref”｝）。要获得有关该工具的帮助，请使用以下命令：

```
$ recipetool -h
NOTE: Starting bitbake server...
usage: recipetool [-d] [-q] [--color COLOR] [-h] <subcommand> ...

OpenEmbedded recipe tool

options:
  -d, --debug     Enable debug output
  -q, --quiet     Print only errors
  --color COLOR   Colorize output (where COLOR is auto, always, never)
  -h, --help      show this help message and exit

subcommands:
  create          Create a new recipe
  newappend       Create a bbappend for the specified target in the specified
                    layer
  setvar          Set a variable within a recipe
  appendfile      Create/update a bbappend to replace a target file
  appendsrcfiles  Create/update a bbappend to add or replace source files
  appendsrcfile   Create/update a bbappend to add or replace a source file
Use recipetool <subcommand> --help to get help on a specific command
```

Running `recipetool create -o OUTFILE` creates the base recipe and locates it properly in the layer that contains your source files. Following are some syntax examples:

> 运行“recipetool create-o OUTFILE”将创建基本配方，并将其正确定位在包含源文件的层中。以下是一些语法示例：

> - Use this syntax to generate a recipe based on source. Once generated, the recipe resides in the existing source code layer:
>
>   ```
>   recipetool create -o OUTFILE source
>   ```
> - Use this syntax to generate a recipe using code that you extract from source. The extracted code is placed in its own layer defined by `EXTERNALSRC`{.interpreted-text role="term"}:
>
>   ```
>   recipetool create -o OUTFILE -x EXTERNALSRC source
>   ```
> - Use this syntax to generate a recipe based on source. The options direct `recipetool` to generate debugging information. Once generated, the recipe resides in the existing source code layer:
>
>   ```
>   recipetool create -d -o OUTFILE source
>   ```

## Locating and Using a Similar Recipe

Before writing a recipe from scratch, it is often useful to discover whether someone else has already written one that meets (or comes close to meeting) your needs. The Yocto Project and OpenEmbedded communities maintain many recipes that might be candidates for what you are doing. You can find a good central index of these recipes in the :oe_layerindex:[OpenEmbedded Layer Index \<\>]{.title-ref}.

> 在从头开始写食谱之前，发现别人是否已经写了一个满足（或接近满足）你需求的食谱通常很有用。Yocto 项目和 OpenEmbedded 社区维护了许多可能适合您所做工作的食谱。您可以在：oe_layeindex:[OpenEmbedded Layer index\<\>]｛.title ref｝中找到这些配方的一个很好的中心索引。

Working from an existing recipe or a skeleton recipe is the best way to get started. Here are some points on both methods:

> 从现有的配方或基本配方开始工作是最好的开始方式。以下是关于这两种方法的一些要点：

- *Locate and modify a recipe that is close to what you want to do:* This method works when you are familiar with the current recipe space. The method does not work so well for those new to the Yocto Project or writing recipes.

> -*找到并修改与您想要做的内容接近的配方：*当您熟悉当前配方空间时，此方法有效。这种方法对那些新加入 Yocto 项目或编写食谱的人来说效果不太好。

Some risks associated with this method are using a recipe that has areas totally unrelated to what you are trying to accomplish with your recipe, not recognizing areas of the recipe that you might have to add from scratch, and so forth. All these risks stem from unfamiliarity with the existing recipe space.

> 与这种方法相关的一些风险是，使用的配方中的区域与您试图用配方完成的内容完全无关，没有识别出配方中可能需要从头开始添加的区域，等等。所有这些风险都源于对现有配方空间的不熟悉。

- *Use and modify the following skeleton recipe:* If for some reason you do not want to use `recipetool` and you cannot find an existing recipe that is close to meeting your needs, you can use the following structure to provide the fundamental areas of a new recipe:

> -*使用和修改以下基本配方：*如果出于某种原因您不想使用“recipetool”，并且找不到接近满足您需求的现有配方，您可以使用以下结构来提供新配方的基本领域：

```
DESCRIPTION = ""
HOMEPAGE = ""
LICENSE = ""
SECTION = ""
DEPENDS = ""
LIC_FILES_CHKSUM = ""

SRC_URI = ""
```

# Storing and Naming the Recipe

Once you have your base recipe, you should put it in your own layer and name it appropriately. Locating it correctly ensures that the OpenEmbedded build system can find it when you use BitBake to process the recipe.

> 一旦你有了基本配方，你就应该把它放在自己的层里，并适当地命名。正确定位它可以确保 OpenEmbedded 构建系统在您使用 BitBake 处理配方时能够找到它。

- *Storing Your Recipe:* The OpenEmbedded build system locates your recipe through the layer\'s `conf/layer.conf` file and the `BBFILES`{.interpreted-text role="term"} variable. This variable sets up a path from which the build system can locate recipes. Here is the typical use:

> -*存储您的配方：*OpenEmbedded 构建系统通过层的“conf/layer.conf”文件和“BBFILES”{.depredicted text role=“term”}变量定位您的配方。该变量设置了一个路径，构建系统可以从该路径定位配方。以下是典型用途：

```
BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
            ${LAYERDIR}/recipes-*/*/*.bbappend"
```

Consequently, you need to be sure you locate your new recipe inside your layer such that it can be found.

> 因此，你需要确保你的新配方位于你的层内，这样它才能被找到。

You can find more information on how layers are structured in the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section.

> 您可以在\“`dev manual/layers:understanding and createing layers`｛.depreted text role=”ref“｝\”一节中找到有关层结构的更多信息。

- *Naming Your Recipe:* When you name your recipe, you need to follow this naming convention:

  ```
  basename_version.bb
  ```

  Use lower-cased characters and do not include the reserved suffixes `-native`, `-cross`, `-initial`, or `-dev` casually (i.e. do not use them as part of your recipe name unless the string applies). Here are some examples:

> 使用小写字符，不要随意包含保留后缀“-nature”、“-cross”、“-initical”或“-dev”（即，除非字符串适用，否则不要将它们用作配方名称的一部分）。以下是一些示例：

```none
cups_1.7.0.bb
gawk_4.0.2.bb
irssi_0.8.16-rc1.bb
```

# Running a Build on the Recipe

Creating a new recipe is usually an iterative process that requires using BitBake to process the recipe multiple times in order to progressively discover and add information to the recipe file.

> 创建新配方通常是一个迭代过程，需要使用 BitBake 多次处理配方，以便逐步发现信息并将其添加到配方文件中。

Assuming you have sourced the build environment setup script (i.e. `structure-core-script`{.interpreted-text role="ref"}) and you are in the `Build Directory`{.interpreted-text role="term"}, use BitBake to process your recipe. All you need to provide is the `basename` of the recipe as described in the previous section:

> 假设您已经获取了构建环境设置脚本（即“结构核心脚本”｛.depreted text role=“ref”｝），并且您在“构建目录”｛.epreted text role=“term”｝中，请使用 BitBake 处理您的配方。您只需提供上一节所述配方的“基本名称”：

```
$ bitbake basename
```

During the build, the OpenEmbedded build system creates a temporary work directory for each recipe (`${``WORKDIR`{.interpreted-text role="term"}`}`) where it keeps extracted source files, log files, intermediate compilation and packaging files, and so forth.

> 在构建过程中，OpenEmbedded 构建系统为每个配方创建一个临时工作目录（`${` WORKDIR `{.depreced text role=“term”}`），其中保存提取的源文件、日志文件、中间编译和打包文件等。

The path to the per-recipe temporary work directory depends on the context in which it is being built. The quickest way to find this path is to have BitBake return it by running the following:

> 每个配方临时工作目录的路径取决于构建该目录的上下文。找到该路径的最快方法是让 BitBake 通过运行以下命令返回该路径：

```
$ bitbake -e basename | grep ^WORKDIR=
```

As an example, assume a Source Directory top-level folder named `poky`, a default `Build Directory`{.interpreted-text role="term"} at `poky/build`, and a `qemux86-poky-linux` machine target system. Furthermore, suppose your recipe is named `foo_1.3.0.bb`. In this case, the work directory the build system uses to build the package would be as follows:

> 例如，假设一个名为“poky”的源目录顶级文件夹，一个位于“poky/Build”的默认“Build Directory”｛.expected text role=“term”｝，以及一个“qemux86 poky linux”机器目标系统。此外，假设您的配方名为“foo_1.3.0.bb”。在这种情况下，构建系统用于构建包的工作目录如下：

```
poky/build/tmp/work/qemux86-poky-linux/foo/1.3.0-r0
```

Inside this directory you can find sub-directories such as `image`, `packages-split`, and `temp`. After the build, you can examine these to determine how well the build went.

> 在该目录中，您可以找到诸如“image”、“packages-split”和“temp”之类的子目录。构建完成后，您可以检查这些内容，以确定构建的进展情况。

::: note
::: title
Note
:::

You can find log files for each task in the recipe\'s `temp` directory (e.g. `poky/build/tmp/work/qemux86-poky-linux/foo/1.3.0-r0/temp`). Log files are named `log.taskname` (e.g. `log.do_configure`, `log.do_fetch`, and `log.do_compile`).

> 您可以在配方的“temp”目录中找到每个任务的日志文件（例如“poky/build/tmp/work/qemux86 poky linux/foo/1.3.0-r0/temp”）。日志文件名为“log.taskname”（例如“log.do_configure”、“log.do_fetch”和“log.do_compile”）。
> :::

You can find more information about the build process in \"`/overview-manual/development-environment`{.interpreted-text role="doc"}\" chapter of the Yocto Project Overview and Concepts Manual.

> 您可以在 Yocto 项目概述和概念手册的“`/overview manual/development environment`｛.depreted text role=“doc”｝\”一章中找到有关构建过程的更多信息。

# Fetching Code

The first thing your recipe must do is specify how to fetch the source files. Fetching is controlled mainly through the `SRC_URI`{.interpreted-text role="term"} variable. Your recipe must have a `SRC_URI`{.interpreted-text role="term"} variable that points to where the source is located. For a graphical representation of source locations, see the \"`overview-manual/concepts:sources`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> 配方必须做的第一件事是指定如何获取源文件。获取主要通过 `SRC_URI`｛.explored text role=“term”｝变量进行控制。您的配方必须有一个指向源所在位置的 `SRC_URI`｛.explored text role=“term”｝变量。有关源位置的图形表示，请参阅 Yocto 项目概述和概念手册中的\“`overview manual/concepts:sources`｛.explored text role=”ref“｝\”一节。

The `ref-tasks-fetch`{.interpreted-text role="ref"} task uses the prefix of each entry in the `SRC_URI`{.interpreted-text role="term"} variable value to determine which `fetcher <bitbake-user-manual/bitbake-user-manual-fetching:fetchers>`{.interpreted-text role="ref"} to use to get your source files. It is the `SRC_URI`{.interpreted-text role="term"} variable that triggers the fetcher. The `ref-tasks-patch`{.interpreted-text role="ref"} task uses the variable after source is fetched to apply patches. The OpenEmbedded build system uses `FILESOVERRIDES`{.interpreted-text role="term"} for scanning directory locations for local files in `SRC_URI`{.interpreted-text role="term"}.

> `ref tasks fetch`｛.depreted text role=“ref”｝任务使用 `SRC_URI`｛.epreted text role=“term”｝变量值中每个条目的前缀来确定使用哪个 `fetcher＜bitbake user manual/bitbake user-manual fetching:fetchers＞`{.depreted text-role=“ref”}来获取源文件。触发获取程序的是 `SRC_URI`｛.explored text role=“term”｝变量。`ref tasks patch`｛.explored text role=“ref”｝任务在获取源后使用变量来应用修补程序。OpenEmbedded 生成系统使用 `FILESOVERRIDES`｛.explored text role=“term”｝在目录位置扫描 `SRC_URI` 中的本地文件。

The `SRC_URI`{.interpreted-text role="term"} variable in your recipe must define each unique location for your source files. It is good practice to not hard-code version numbers in a URL used in `SRC_URI`{.interpreted-text role="term"}. Rather than hard-code these values, use `${``PV`{.interpreted-text role="term"}`}`, which causes the fetch process to use the version specified in the recipe filename. Specifying the version in this manner means that upgrading the recipe to a future version is as simple as renaming the recipe to match the new version.

> 配方中的 `SRC_URI`｛.explored text role=“term”｝变量必须为源文件定义每个唯一的位置。最好不要对 `SRC_URI` 中使用的 URL 中的版本号进行硬编码{.depreted text role=“term”}。与其对这些值进行硬编码，不如使用 `${` PV `{.depreced text role=“term”}`}`，这将导致提取过程使用配方文件名中指定的版本。以这种方式指定版本意味着将配方升级到未来版本就像重命名配方以匹配新版本一样简单。

Here is a simple example from the `meta/recipes-devtools/strace/strace_5.5.bb` recipe where the source comes from a single tarball. Notice the use of the `PV`{.interpreted-text role="term"} variable:

> 以下是“meta/precipes devtools/strace/strace_5.5.bb”配方中的一个简单示例，其中源代码来自一个 tarball。请注意使用了 `PV`｛.explored text role=“term”｝变量：

```
SRC_URI = "https://strace.io/files/${PV}/strace-${PV}.tar.xz \
```

Files mentioned in `SRC_URI`{.interpreted-text role="term"} whose names end in a typical archive extension (e.g. `.tar`, `.tar.gz`, `.tar.bz2`, `.zip`, and so forth), are automatically extracted during the `ref-tasks-unpack`{.interpreted-text role="ref"} task. For another example that specifies these types of files, see the \"`dev-manual/new-recipe:building an autotooled package`{.interpreted-text role="ref"}\" section.

> 在 `SRC_URI`｛.explored text role=“term”｝中提到的文件，其名称以典型的存档扩展名结尾（例如 `.tar`、`.tar.gz`、`.tar.bz2`、`.zip ` 等），在 `ref tasks unpack`｛.sexplored textrole=”ref“｝任务期间自动提取。有关指定这些类型文件的另一个示例，请参阅\“`dev manual/new recipe:building an autotooled package`｛.depreted text role=”ref“｝\”一节。

Another way of specifying source is from an SCM. For Git repositories, you must specify `SRCREV`{.interpreted-text role="term"} and you should specify `PV`{.interpreted-text role="term"} to include the revision with `SRCPV`{.interpreted-text role="term"}. Here is an example from the recipe `meta/recipes-core/musl/gcompat_git.bb`:

> 另一种指定来源的方法是从 SCM。对于 Git 存储库，您必须指定 `SRCREV`｛.depredicted text role=“term”｝，并且您应该指定 `PV`｛.epredicted textrole=”term“｝以将修订包含在 `SRCLV`｛.repredicted extrolE=”term“｝中。以下是配方“meta/precipes-core/musl/gcompat_git.bb”中的一个示例：

```
SRC_URI = "git://git.adelielinux.org/adelie/gcompat.git;protocol=https;branch=current"

PV = "1.0.0+1.1+git${SRCPV}"
SRCREV = "af5a49e489fdc04b9cf02547650d7aeaccd43793"
```

If your `SRC_URI`{.interpreted-text role="term"} statement includes URLs pointing to individual files fetched from a remote server other than a version control system, BitBake attempts to verify the files against checksums defined in your recipe to ensure they have not been tampered with or otherwise modified since the recipe was written. Two checksums are used: `SRC_URI[md5sum]` and `SRC_URI[sha256sum]`.

> 如果您的 `SRC_URI`｛.explored text role=“term”｝语句包含指向从版本控制系统以外的远程服务器获取的单个文件的 URL，BitBake 会尝试根据配方中定义的校验和验证这些文件，以确保自编写配方以来，这些文件未被篡改或以其他方式修改。使用了两个校验和：`SRC_URI[md5sum]` 和 `SRC_URI[sha256sum]`。

If your `SRC_URI`{.interpreted-text role="term"} variable points to more than a single URL (excluding SCM URLs), you need to provide the `md5` and `sha256` checksums for each URL. For these cases, you provide a name for each URL as part of the `SRC_URI`{.interpreted-text role="term"} and then reference that name in the subsequent checksum statements. Here is an example combining lines from the files `git.inc` and `git_2.24.1.bb`:

> 如果您的 `SRC_URI`｛.explored text role=“term”｝变量指向多个 URL（不包括 SCM URL），则需要为每个 URL 提供 `md5` 和 `sha256` 校验和。对于这些情况，您为每个 URL 提供一个名称，作为 `SRC_URI`｛.explored text role=“term”｝的一部分，然后在随后的校验和语句中引用该名称。下面是一个合并文件 `git.inc` 和 `git_2.24.1.bb` 中的行的示例：

```
SRC_URI = "${KERNELORG_MIRROR}/software/scm/git/git-${PV}.tar.gz;name=tarball \
           ${KERNELORG_MIRROR}/software/scm/git/git-manpages-${PV}.tar.gz;name=manpages"

SRC_URI[tarball.md5sum] = "166bde96adbbc11c8843d4f8f4f9811b"
SRC_URI[tarball.sha256sum] = "ad5334956301c86841eb1e5b1bb20884a6bad89a10a6762c958220c7cf64da02"
SRC_URI[manpages.md5sum] = "31c2272a8979022497ba3d4202df145d"
SRC_URI[manpages.sha256sum] = "9a7ae3a093bea39770eb96ca3e5b40bff7af0b9f6123f089d7821d0e5b8e1230"
```

Proper values for `md5` and `sha256` checksums might be available with other signatures on the download page for the upstream source (e.g. `md5`, `sha1`, `sha256`, `GPG`, and so forth). Because the OpenEmbedded build system only deals with `sha256sum` and `md5sum`, you should verify all the signatures you find by hand.

> “md5”和“sha256”校验和的正确值可能与上游源的下载页面上的其他签名一起可用（例如“md5’、“sha1”、“sha256’、“GPG”等）。因为 OpenEmbedded 构建系统只处理“sha256sum”和“md5sum”，所以您应该手动验证找到的所有签名。

If no `SRC_URI`{.interpreted-text role="term"} checksums are specified when you attempt to build the recipe, or you provide an incorrect checksum, the build will produce an error for each missing or incorrect checksum. As part of the error message, the build system provides the checksum string corresponding to the fetched file. Once you have the correct checksums, you can copy and paste them into your recipe and then run the build again to continue.

> 如果在尝试构建配方时没有指定 `SRC_URI`｛.explored text role=“term”｝校验和，或者您提供了不正确的校验和，则构建将为每个丢失或不正确的检查和生成一个错误。作为错误消息的一部分，构建系统提供与提取的文件相对应的校验和字符串。一旦你有了正确的校验和，你就可以将它们复制并粘贴到你的食谱中，然后再次运行构建以继续。

::: note
::: title
Note
:::

As mentioned, if the upstream source provides signatures for verifying the downloaded source code, you should verify those manually before setting the checksum values in the recipe and continuing with the build.

> 如前所述，如果上游源提供用于验证下载的源代码的签名，则应在配方中设置校验和值并继续构建之前手动验证这些签名。
> :::

This final example is a bit more complicated and is from the `meta/recipes-sato/rxvt-unicode/rxvt-unicode_9.20.bb` recipe. The example\'s `SRC_URI`{.interpreted-text role="term"} statement identifies multiple files as the source files for the recipe: a tarball, a patch file, a desktop file, and an icon:

> 最后一个例子有点复杂，来自“meta/precipes sato/rxvt unicode/rxvt-unicode_9.20.bb”配方。示例的 `SRC_URI`｛.explored text role=“term”｝语句将多个文件标识为配方的源文件：tarball、补丁文件、桌面文件和图标：

```
SRC_URI = "http://dist.schmorp.de/rxvt-unicode/Attic/rxvt-unicode-${PV}.tar.bz2 \
           file://xwc.patch \
           file://rxvt.desktop \
           file://rxvt.png"
```

When you specify local files using the `file://` URI protocol, the build system fetches files from the local machine. The path is relative to the `FILESPATH`{.interpreted-text role="term"} variable and searches specific directories in a certain order: `${``BP`{.interpreted-text role="term"}`}`, `${``BPN`{.interpreted-text role="term"}`}`, and `files`. The directories are assumed to be subdirectories of the directory in which the recipe or append file resides. For another example that specifies these types of files, see the \"[building a single .c file package](#building-a-single-.c-file-package)\" section.

> 当您使用 `file://` URI 协议指定本地文件时，构建系统会从本地机器获取文件。该路径是相对于 `FILESPATH`｛.depreced text role=“term”｝变量的，并按特定顺序搜索特定目录：`$｛``BP`｛.epreced text-role=“term“｝`｝`、`${` BPN `｛.repreced ext-role=”term“}`｝'和 `files`。假设这些目录是配方或附加文件所在目录的子目录。有关指定这些文件类型的另一个示例，请参阅“[构建单个.c 文件包]（#building-a-single-.c-file-package）”一节。

The previous example also specifies a patch file. Patch files are files whose names usually end in `.patch` or `.diff` but can end with compressed suffixes such as `diff.gz` and `patch.bz2`, for example. The build system automatically applies patches as described in the \"`dev-manual/new-recipe:patching code`{.interpreted-text role="ref"}\" section.

> 上一个示例还指定了一个修补程序文件。修补程序文件是名称通常以 `.Patch` 或 `.diff` 结尾的文件，但可以以压缩后缀结尾，例如 `diff.gz` 和 `Patch.bz2'。构建系统会自动应用补丁程序，如\“` dev manual/new recipe:patching code`｛.depreted text role=”ref“｝\”部分所述。

## Fetching Code Through Firewalls

Some users are behind firewalls and need to fetch code through a proxy. See the \"`/ref-manual/faq`{.interpreted-text role="doc"}\" chapter for advice.

> 有些用户在防火墙后面，需要通过代理获取代码。有关建议，请参阅“`/ref manual/faq`｛.explored text role=“doc”｝\”一章。

## Limiting the Number of Parallel Connections

Some users are behind firewalls or use servers where the number of parallel connections is limited. In such cases, you can limit the number of fetch tasks being run in parallel by adding the following to your `local.conf` file:

> 一些用户在防火墙后面，或者使用并行连接数量有限的服务器。在这种情况下，您可以通过在“local.conf”文件中添加以下内容来限制并行运行的获取任务的数量：

```
do_fetch[number_threads] = "4"
```

# Unpacking Code

During the build, the `ref-tasks-unpack`{.interpreted-text role="ref"} task unpacks the source with `${``S`{.interpreted-text role="term"}`}` pointing to where it is unpacked.

> 在生成过程中，`ref tasks unpack`｛.depreted text role=“ref”｝任务使用指向解包位置的 `${``S`｛.repreted text role=“term”｝`｝来解包源。

If you are fetching your source files from an upstream source archived tarball and the tarball\'s internal structure matches the common convention of a top-level subdirectory named `${``BPN`{.interpreted-text role="term"}`}-${``PV`{.interpreted-text role="term"}`}`, then you do not need to set `S`{.interpreted-text role="term"}. However, if `SRC_URI`{.interpreted-text role="term"} specifies to fetch source from an archive that does not use this convention, or from an SCM like Git or Subversion, your recipe needs to define `S`{.interpreted-text role="term"}.

> 如果您从上游源存档的 tarball 中获取源文件，并且 tarball 的内部结构与名为 `${` BPN `{.depreced text role=“term”}`}-${`PV`{.depreded text role=“term”}`` 的顶级子目录的通用约定相匹配，则无需设置 `s`{.epreced textrole=”term“}。但是，如果 `SRC_URI`｛.explored text role=“term”｝指定从不使用此约定的存档中，或从像 Git 或 Subversion 这样的 SCM 中获取源，则您的配方需要定义 `S`｛.sexplored textrole=”term“｝。

If processing your recipe using BitBake successfully unpacks the source files, you need to be sure that the directory pointed to by `${S}` matches the structure of the source.

> 如果使用 BitBake 处理配方成功地解包源文件，则需要确保“$｛S｝”指向的目录与源的结构匹配。

# Patching Code

Sometimes it is necessary to patch code after it has been fetched. Any files mentioned in `SRC_URI`{.interpreted-text role="term"} whose names end in `.patch` or `.diff` or compressed versions of these suffixes (e.g. `diff.gz` are treated as patches. The `ref-tasks-patch`{.interpreted-text role="ref"} task automatically applies these patches.

> 有时有必要在获取代码后对其进行修补。在 `SRC_URI` 中提到的名称以 `.patch` 或 `.diff` 结尾的任何文件或这些后缀的压缩版本（例如 `diff.gz`）都被视为修补程序。`ref tasks patch`{.depreted text role=“ref”}任务会自动应用这些修补程序。

The build system should be able to apply patches with the \"-p1\" option (i.e. one directory level in the path will be stripped off). If your patch needs to have more directory levels stripped off, specify the number of levels using the \"striplevel\" option in the `SRC_URI`{.interpreted-text role="term"} entry for the patch. Alternatively, if your patch needs to be applied in a specific subdirectory that is not specified in the patch file, use the \"patchdir\" option in the entry.

> 构建系统应该能够使用“-p1\”选项应用修补程序（即路径中的一个目录级别将被剥离）。如果您的修补程序需要剥离更多的目录级别，请使用修补程序的 `SRC_URI`｛.respered text role=“term”｝条目中的\“stripelevel\”选项指定级别数。或者，如果需要在修补程序文件中未指定的特定子目录中应用修补程序，请使用条目中的“patchdir\”选项。

As with all local files referenced in `SRC_URI`{.interpreted-text role="term"} using `file://`, you should place patch files in a directory next to the recipe either named the same as the base name of the recipe (`BP`{.interpreted-text role="term"} and `BPN`{.interpreted-text role="term"}) or \"files\".

> 与使用 `file://` 在 `SRC_URI` 中引用的所有本地文件一样，您应该将补丁文件放在配方旁边的目录中，该目录的名称与配方的基本名称相同（`BP`{.expered text role=“term”}和 `BPN`{.emped text rol=“term“}）或\“files\”。

# Licensing

Your recipe needs to have both the `LICENSE`{.interpreted-text role="term"} and `LIC_FILES_CHKSUM`{.interpreted-text role="term"} variables:

> 您的配方需要同时具有 `LICENSE`｛.depredicted text role=“term”｝和 `LIC_FILES_CHKSUM`｛.epredicted text role=”term“｝变量：

- `LICENSE`{.interpreted-text role="term"}: This variable specifies the license for the software. If you do not know the license under which the software you are building is distributed, you should go to the source code and look for that information. Typical files containing this information include `COPYING`, `LICENSE`{.interpreted-text role="term"}, and `README` files. You could also find the information near the top of a source file. For example, given a piece of software licensed under the GNU General Public License version 2, you would set `LICENSE`{.interpreted-text role="term"} as follows:

> -`LICENSE`｛.explored text role=“term”｝：此变量指定软件的许可证。如果您不知道您正在构建的软件是根据哪个许可证分发的，那么您应该查看源代码并查找该信息。包含此信息的典型文件包括“COPYING”、“LICENSE”｛.explored text role=“term”｝和“README”文件。您还可以在源文件顶部附近找到信息。例如，给定一个根据 GNU 通用公共许可证第 2 版许可的软件，您可以设置 `License`{.depreted text role=“term”}如下：

```
LICENSE = "GPL-2.0-only"
```

The licenses you specify within `LICENSE`{.interpreted-text role="term"} can have any name as long as you do not use spaces, since spaces are used as separators between license names. For standard licenses, use the names of the files in `meta/files/common-licenses/` or the `SPDXLICENSEMAP`{.interpreted-text role="term"} flag names defined in `meta/conf/licenses.conf`.

> 您在 `LICENSE`｛.explored text role=“term”｝中指定的许可证可以有任何名称，只要您不使用空格，因为空格用作许可证名称之间的分隔符。对于标准许可证，请使用 `meta/files/common licenses/` 中的文件名或 `meta/conf/licenses.conf` 中定义的 `SPDXLICENSEMAP`｛.explored text role=“term”｝标志名。

- `LIC_FILES_CHKSUM`{.interpreted-text role="term"}: The OpenEmbedded build system uses this variable to make sure the license text has not changed. If it has, the build produces an error and it affords you the chance to figure it out and correct the problem.

> -`LIC_FILES_CHKSUM`｛.explored text role=“term”｝：OpenEmbedded 构建系统使用此变量来确保许可证文本没有更改。如果有，构建会产生一个错误，它为您提供了找出并更正问题的机会。

You need to specify all applicable licensing files for the software. At the end of the configuration step, the build process will compare the checksums of the files to be sure the text has not changed. Any differences result in an error with the message containing the current checksum. For more explanation and examples of how to set the `LIC_FILES_CHKSUM`{.interpreted-text role="term"} variable, see the \"`dev-manual/licenses:tracking license changes`{.interpreted-text role="ref"}\" section.

> 您需要为软件指定所有适用的许可文件。在配置步骤结束时，构建过程将比较文件的校验和，以确保文本没有更改。任何差异都会导致包含当前校验和的消息出错。有关如何设置 `LIC_FILES_CHKSUM`｛.explored text role=“term”｝变量的更多解释和示例，请参阅\“`dev manual/licenses:跟踪许可证更改”｛.expered text rol=“ref”｝\”一节。

To determine the correct checksum string, you can list the appropriate files in the `LIC_FILES_CHKSUM`{.interpreted-text role="term"} variable with incorrect md5 strings, attempt to build the software, and then note the resulting error messages that will report the correct md5 strings. See the \"`dev-manual/new-recipe:fetching code`{.interpreted-text role="ref"}\" section for additional information.

> 要确定正确的校验和字符串，您可以在 `LIC_files_CHKSUM`｛.depreced text role=“term”｝变量中列出具有不正确 md5 字符串的相应文件，尝试构建软件，然后记录将报告正确 md5 串的错误消息。有关更多信息，请参阅“`dev manual/new recipe:获取代码`{.depreted text role=“ref”}\”部分。

Here is an example that assumes the software has a `COPYING` file:

> 以下是一个假设软件具有“COPYING”文件的示例：

```
LIC_FILES_CHKSUM = "file://COPYING;md5=xxx"
```

When you try to build the software, the build system will produce an error and give you the correct string that you can substitute into the recipe file for a subsequent build.

> 当您尝试构建软件时，构建系统会产生一个错误，并为您提供正确的字符串，您可以将其替换到配方文件中以进行后续构建。

# Dependencies

Most software packages have a short list of other packages that they require, which are called dependencies. These dependencies fall into two main categories: build-time dependencies, which are required when the software is built; and runtime dependencies, which are required to be installed on the target in order for the software to run.

> 大多数软件包都有一个他们需要的其他包的简短列表，这些包被称为依赖项。这些依赖关系分为两大类：构建时间依赖关系，这是构建软件时所必需的；以及运行时依赖关系，这些依赖关系需要安装在目标上才能运行软件。

Within a recipe, you specify build-time dependencies using the `DEPENDS`{.interpreted-text role="term"} variable. Although there are nuances, items specified in `DEPENDS`{.interpreted-text role="term"} should be names of other recipes. It is important that you specify all build-time dependencies explicitly.

> 在配方中，可以使用 `DEPENDS`｛.explored text role=“term”｝变量指定构建时依赖关系。尽管有细微差别，但在 `DEPENDS`｛.explored text role=“term”｝中指定的项目应该是其他配方的名称。显式指定所有构建时依赖关系非常重要。

Another consideration is that configure scripts might automatically check for optional dependencies and enable corresponding functionality if those dependencies are found. If you wish to make a recipe that is more generally useful (e.g. publish the recipe in a layer for others to use), instead of hard-disabling the functionality, you can use the `PACKAGECONFIG`{.interpreted-text role="term"} variable to allow functionality and the corresponding dependencies to be enabled and disabled easily by other users of the recipe.

> 另一个考虑因素是，配置脚本可能会自动检查可选的依赖项，并在找到这些依赖项时启用相应的功能。如果您希望制作一个更通用的配方（例如，在一个层中发布配方供其他人使用），而不是硬禁用该功能，您可以使用 `PACKACECONFIG`｛.explored text role=“term”｝变量来允许配方的其他用户轻松启用和禁用功能和相应的依赖项。

Similar to build-time dependencies, you specify runtime dependencies through a variable -`RDEPENDS`{.interpreted-text role="term"}, which is package-specific. All variables that are package-specific need to have the name of the package added to the end as an override. Since the main package for a recipe has the same name as the recipe, and the recipe\'s name can be found through the `${``PN`{.interpreted-text role="term"}`}` variable, then you specify the dependencies for the main package by setting `RDEPENDS:${PN}`. If the package were named `${PN}-tools`, then you would set `RDEPENDS:${PN}-tools`, and so forth.

> 与构建时依赖项类似，您可以通过特定于包的变量 `RDEPENDS`｛.explored text role=“term”｝指定运行时依赖项。所有特定于包的变量都需要将包的名称作为覆盖添加到末尾。由于配方的主包与配方具有相同的名称，并且配方的名称可以通过 `$｛` PN `｛.explored text role=“term”｝`｝`变量找到，因此您可以通过设置` RDEPENDS:$｛PN｝`来指定主包的依赖项。如果包名为“$｛PN｝-tools”，那么您将设置“RDEPENDS:$｛PN｝-toools”，依此类推。

Some runtime dependencies will be set automatically at packaging time. These dependencies include any shared library dependencies (i.e. if a package \"example\" contains \"libexample\" and another package \"mypackage\" contains a binary that links to \"libexample\" then the OpenEmbedded build system will automatically add a runtime dependency to \"mypackage\" on \"example\"). See the \"`overview-manual/concepts:automatically added runtime dependencies`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual for further details.

> 某些运行时依赖项将在打包时自动设置。这些依赖项包括任何共享库依赖项（即，如果一个包“example”包含“libexample”，而另一个包\“mypackage”包含链接到“libexample”的二进制文件，则 OpenEmbedded 构建系统将自动在“example \”上向“mypackage\”添加运行时依赖项）。有关更多详细信息，请参阅 Yocto 项目概述和概念手册中的\“`overview manual/concepts:automatically added runtime dependencies`｛.explored text role=“ref”｝\”一节。

# Configuring the Recipe

Most software provides some means of setting build-time configuration options before compilation. Typically, setting these options is accomplished by running a configure script with options, or by modifying a build configuration file.

> 大多数软件在编译之前提供了一些设置构建时配置选项的方法。通常，设置这些选项是通过运行带有选项的配置脚本或修改构建配置文件来完成的。

::: note
::: title
Note
:::

As of Yocto Project Release 1.7, some of the core recipes that package binary configuration scripts now disable the scripts due to the scripts previously requiring error-prone path substitution. The OpenEmbedded build system uses `pkg-config` now, which is much more robust. You can find a list of the `*-config` scripts that are disabled in the \"`migration-1.7-binary-configuration-scripts-disabled`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 从 Yocto Project Release 1.7 开始，一些打包二进制配置脚本的核心配方现在禁用了这些脚本，因为这些脚本以前需要容易出错的路径替换。OpenEmbedded 构建系统现在使用的是“pkg config”，它更加健壮。您可以在 Yocto 项目参考手册的\“`migration-1.7-binary-configuration-scripts-disabled`｛.depredicted text role=“ref”｝\”部分找到禁用的 `*-config` 脚本列表。
> :::

A major part of build-time configuration is about checking for build-time dependencies and possibly enabling optional functionality as a result. You need to specify any build-time dependencies for the software you are building in your recipe\'s `DEPENDS`{.interpreted-text role="term"} value, in terms of other recipes that satisfy those dependencies. You can often find build-time or runtime dependencies described in the software\'s documentation.

> 构建时配置的一个主要部分是检查构建时相关性，并可能因此启用可选功能。您需要根据满足这些依赖关系的其他配方，在配方的 `DEPENDS`｛.explored text role=“term”｝值中指定正在构建的软件的任何构建时依赖关系。您经常可以在软件文档中找到描述的构建时或运行时依赖关系。

The following list provides configuration items of note based on how your software is built:

> 以下列表根据软件的构建方式提供了需要注意的配置项目：

- *Autotools:* If your source files have a `configure.ac` file, then your software is built using Autotools. If this is the case, you just need to modify the configuration.

> -*自动工具：*如果源文件有“configure.ac”文件，则您的软件是使用自动工具构建的。如果是这种情况，您只需要修改配置即可。

When using Autotools, your recipe needs to inherit the `ref-classes-autotools`{.interpreted-text role="ref"} class and it does not have to contain a `ref-tasks-configure`{.interpreted-text role="ref"} task. However, you might still want to make some adjustments. For example, you can set `EXTRA_OECONF`{.interpreted-text role="term"} or `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"} to pass any needed configure options that are specific to the recipe.

> 使用自动工具时，您的配方需要继承 `ref classes Autotools`｛.depreted text role=“ref”｝类，并且不必包含 `ref tasks configure`｛.repreted text role=“ref”}任务。但是，您可能仍然需要进行一些调整。例如，您可以设置 `EXTRA_OECOCONF`｛.explored text role=“term”｝或 `PACKACECONFIG_CONFARGS`｛..explored textrole=”term“｝以传递特定于配方的任何所需配置选项。

- *CMake:* If your source files have a `CMakeLists.txt` file, then your software is built using CMake. If this is the case, you just need to modify the configuration.

> -*CMake:*如果您的源文件有一个“CMakeLists.txt”文件，则您的软件是使用 CMake 构建的。如果是这种情况，您只需要修改配置即可。

When you use CMake, your recipe needs to inherit the `ref-classes-cmake`{.interpreted-text role="ref"} class and it does not have to contain a `ref-tasks-configure`{.interpreted-text role="ref"} task. You can make some adjustments by setting `EXTRA_OECMAKE`{.interpreted-text role="term"} to pass any needed configure options that are specific to the recipe.

> 当您使用 CMake 时，您的配方需要继承 `ref classes CMake`｛.depreted text role=“ref”｝类，并且它不必包含 `ref tasks configure`｛.repreted text role=“ref”}任务。您可以通过设置 `EXTRA_OECMAKE`｛.explored text role=“term”｝进行一些调整，以传递特定于配方的任何所需配置选项。

::: note
::: title

Note

> 笔记
> :::

If you need to install one or more custom CMake toolchain files that are supplied by the application you are building, install the files to `${D}${datadir}/cmake/Modules` during `ref-tasks-install`{.interpreted-text role="ref"}.

> 如果您需要安装由您正在构建的应用程序提供的一个或多个自定义 CMake 工具链文件，请在“ref tasks install”期间将这些文件安装到“$｛D｝$｛datadir｝/CMake/Modules”中。
> :::

- *Other:* If your source files do not have a `configure.ac` or `CMakeLists.txt` file, then your software is built using some method other than Autotools or CMake. If this is the case, you normally need to provide a `ref-tasks-configure`{.interpreted-text role="ref"} task in your recipe unless, of course, there is nothing to configure.

> -*其他：*如果您的源文件没有“configure.ac”或“CMakeLists.txt”文件，则您的软件是使用 Autotools 或 CMake 以外的某种方法构建的。如果是这种情况，您通常需要在配方中提供一个 `ref tasks configure`{.depreted text role=“ref”}任务，当然，除非没有什么可配置的。

Even if your software is not being built by Autotools or CMake, you still might not need to deal with any configuration issues. You need to determine if configuration is even a required step. You might need to modify a Makefile or some configuration file used for the build to specify necessary build options. Or, perhaps you might need to run a provided, custom configure script with the appropriate options.

> 即使您的软件不是由 Autotools 或 CMake 构建的，您仍然可能不需要处理任何配置问题。您需要确定配置是否是必需的步骤。您可能需要修改用于生成的 Makefile 或某些配置文件，以指定必要的生成选项。或者，您可能需要使用适当的选项运行提供的自定义配置脚本。

For the case involving a custom configure script, you would run `./configure --help` and look for the options you need to set.

> 对于涉及自定义配置脚本的情况，您将运行 `/configure--help` 并查找需要设置的选项。

Once configuration succeeds, it is always good practice to look at the `log.do_configure` file to ensure that the appropriate options have been enabled and no additional build-time dependencies need to be added to `DEPENDS`{.interpreted-text role="term"}. For example, if the configure script reports that it found something not mentioned in `DEPENDS`{.interpreted-text role="term"}, or that it did not find something that it needed for some desired optional functionality, then you would need to add those to `DEPENDS`{.interpreted-text role="term"}. Looking at the log might also reveal items being checked for, enabled, or both that you do not want, or items not being found that are in `DEPENDS`{.interpreted-text role="term"}, in which case you would need to look at passing extra options to the configure script as needed. For reference information on configure options specific to the software you are building, you can consult the output of the `./configure --help` command within `${S}` or consult the software\'s upstream documentation.

> 一旦配置成功，最好查看“log.do_configure”文件，以确保已启用适当的选项，并且不需要向“DEPENDS”添加额外的构建时依赖项｛.depredicted text role=“term”｝。例如，如果配置脚本报告它在 `DEPENDS`｛.depredicted text role=“term”｝中发现了未提及的内容，或者它没有找到某些所需可选功能所需的内容，则您需要将这些内容添加到 `DEPENDS`｛.epredicted textrole=”term“｝中。查看日志还可能会发现正在检查、启用或两者都不需要的项目，或者在 `DEPENDS`｛.depredicted text role=“term”｝中找不到的项目，在这种情况下，您需要根据需要将额外的选项传递给配置脚本。有关特定于您正在构建的软件的配置选项的参考信息，您可以查阅“”的输出/configure--help `command within `${S}` 或查阅软件的上游文档。

# Using Headers to Interface with Devices

If your recipe builds an application that needs to communicate with some device or needs an API into a custom kernel, you will need to provide appropriate header files. Under no circumstances should you ever modify the existing `meta/recipes-kernel/linux-libc-headers/linux-libc-headers.inc` file. These headers are used to build `libc` and must not be compromised with custom or machine-specific header information. If you customize `libc` through modified headers all other applications that use `libc` thus become affected.

> 如果您的配方构建了一个需要与某些设备通信的应用程序，或者需要在自定义内核中使用 API，则需要提供适当的头文件。在任何情况下都不应修改现有的“meta/recipes-kernel/linux-libc-headers/linux-libm-headers.inc”文件。这些标头用于构建“libc”，不得与自定义或特定于机器的标头信息相冲突。如果您通过修改的头自定义“libc”，那么使用“libc“的所有其他应用程序都会受到影响。

::: note
::: title
Note
:::

Never copy and customize the `libc` header file (i.e. `meta/recipes-kernel/linux-libc-headers/linux-libc-headers.inc`).

> 切勿复制和自定义 `libc` 头文件（即 `meta/recipes-kernel/linux-libc-headers/linux-labc-headers.inc`）。
> :::

The correct way to interface to a device or custom kernel is to use a separate package that provides the additional headers for the driver or other unique interfaces. When doing so, your application also becomes responsible for creating a dependency on that specific provider.

> 连接到设备或自定义内核的正确方法是使用一个单独的包，该包为驱动程序或其他唯一接口提供额外的标头。这样做时，您的应用程序还将负责创建对该特定提供者的依赖关系。

Consider the following:

> 考虑以下内容：

- Never modify `linux-libc-headers.inc`. Consider that file to be part of the `libc` system, and not something you use to access the kernel directly. You should access `libc` through specific `libc` calls.

> -永远不要修改“linux-libc-headers.inc`”。将该文件视为“libc”系统的一部分，而不是用于直接访问内核的文件。您应该通过特定的“libc”调用来访问“libc“。

- Applications that must talk directly to devices should either provide necessary headers themselves, or establish a dependency on a special headers package that is specific to that driver.

> -必须直接与设备对话的应用程序应该自己提供必要的标头，或者建立对特定于该驱动程序的特殊标头包的依赖关系。

For example, suppose you want to modify an existing header that adds I/O control or network support. If the modifications are used by a small number programs, providing a unique version of a header is easy and has little impact. When doing so, bear in mind the guidelines in the previous list.

> 例如，假设您要修改添加 I/O 控制或网络支持的现有标头。如果少数程序使用这些修改，那么提供一个唯一版本的标头很容易，而且影响很小。在执行此操作时，请记住上一列表中的指导方针。

::: note
::: title
Note
:::

If for some reason your changes need to modify the behavior of the `libc`, and subsequently all other applications on the system, use a `.bbappend` to modify the `linux-kernel-headers.inc` file. However, take care to not make the changes machine specific.

> 如果出于某种原因，您的更改需要修改“libc”的行为，以及随后系统上所有其他应用程序的行为，请使用“.bbappend”修改“linux-kernel-headers.inc”文件。但是，请注意不要使更改特定于机器。
> :::

Consider a case where your kernel is older and you need an older `libc` ABI. The headers installed by your recipe should still be a standard mainline kernel, not your own custom one.

> 考虑一个内核较旧并且需要较旧的“libc”ABI 的情况。您的配方安装的头应该仍然是标准的主线内核，而不是您自己的自定义内核。

When you use custom kernel headers you need to get them from `STAGING_KERNEL_DIR`{.interpreted-text role="term"}, which is the directory with kernel headers that are required to build out-of-tree modules. Your recipe will also need the following:

> 当您使用自定义内核标头时，您需要从 `STAGING_kernel_DIR`｛.depreted text role=“term”｝获取它们，该目录包含构建树外模块所需的内核标头。您的食谱还需要以下内容：

```
do_configure[depends] += "virtual/kernel:do_shared_workdir"
```

# Compilation

During a build, the `ref-tasks-compile`{.interpreted-text role="ref"} task happens after source is fetched, unpacked, and configured. If the recipe passes through `ref-tasks-compile`{.interpreted-text role="ref"} successfully, nothing needs to be done.

> 在生成过程中，`ref tasks compile`｛.explored text role=“ref”｝任务发生在获取、解压缩和配置源之后。如果配方成功通过 `ref tasks compile`｛.explored text role=“ref”｝，则无需执行任何操作。

However, if the compile step fails, you need to diagnose the failure. Here are some common issues that cause failures.

> 但是，如果编译步骤失败，则需要诊断故障。以下是导致故障的一些常见问题。

::: note
::: title
Note
:::

For cases where improper paths are detected for configuration files or for when libraries/headers cannot be found, be sure you are using the more robust `pkg-config`. See the note in section \"`dev-manual/new-recipe:Configuring the Recipe`{.interpreted-text role="ref"}\" for additional information.

> 如果检测到配置文件的路径不正确，或者找不到库/头文件，请确保您使用的是更健壮的“pkg config”。有关更多信息，请参阅“`dev manual/new recipe:配置配方`{.depreted text role=“ref”}\”一节中的注释。
> :::

- *Parallel build failures:* These failures manifest themselves as intermittent errors, or errors reporting that a file or directory that should be created by some other part of the build process could not be found. This type of failure can occur even if, upon inspection, the file or directory does exist after the build has failed, because that part of the build process happened in the wrong order.

> -*并行生成失败：*这些失败表现为间歇性错误，或报告找不到应由生成过程的其他部分创建的文件或目录的错误。即使经过检查，文件或目录在生成失败后确实存在，也可能发生这种类型的失败，因为生成过程的这一部分发生的顺序错误。

To fix the problem, you need to either satisfy the missing dependency in the Makefile or whatever script produced the Makefile, or (as a workaround) set `PARALLEL_MAKE`{.interpreted-text role="term"} to an empty string:

> 要解决此问题，您需要满足 Makefile 或生成 Makefile 的任何脚本中缺少的依赖项，或者（作为一种解决方法）将 `PARALLEL_MAKE`｛.explored text role=“term”｝设置为空字符串：

```
PARALLEL_MAKE = ""
```

For information on parallel Makefile issues, see the \"`dev-manual/debugging:debugging parallel make races`{.interpreted-text role="ref"}\" section.

> 有关并行生成文件问题的信息，请参阅\“`dev manual/debugging:debugging parallel make race`｛.depreted text role=”ref“｝\”一节。

- *Improper host path usage:* This failure applies to recipes building for the target or \"`ref-classes-nativesdk`{.interpreted-text role="ref"}\" only. The failure occurs when the compilation process uses improper headers, libraries, or other files from the host system when cross-compiling for the target.

> -*主机路径使用不当：*此故障仅适用于为目标或\“`ref classes nativesdk`｛.explored text role=”ref“｝\”构建的配方。当编译过程在为目标进行交叉编译时使用了来自主机系统的不正确的头、库或其他文件时，就会发生故障。

To fix the problem, examine the `log.do_compile` file to identify the host paths being used (e.g. `/usr/include`, `/usr/lib`, and so forth) and then either add configure options, apply a patch, or do both.

> 要解决此问题，请检查“log.do_compile”文件以确定正在使用的主机路径（例如“/usr/include”、“/usr/lib”等），然后添加配置选项、应用补丁或同时执行这两种操作。

- *Failure to find required libraries/headers:* If a build-time dependency is missing because it has not been declared in `DEPENDS`{.interpreted-text role="term"}, or because the dependency exists but the path used by the build process to find the file is incorrect and the configure step did not detect it, the compilation process could fail. For either of these failures, the compilation process notes that files could not be found. In these cases, you need to go back and add additional options to the configure script as well as possibly add additional build-time dependencies to `DEPENDS`{.interpreted-text role="term"}.

> -*找不到所需的库/头：*如果由于未在 `DEPENDS` 中声明生成时依赖项，或者由于该依赖项存在，但生成过程用于查找文件的路径不正确，并且配置步骤未检测到该依赖项，则编译过程可能失败。对于这两种故障，编译过程都会注意到找不到文件。在这些情况下，您需要返回并向配置脚本添加额外的选项，以及可能向 `DEPENDS` 添加额外的构建时依赖项{.depredicted text role=“term”}。

Occasionally, it is necessary to apply a patch to the source to ensure the correct paths are used. If you need to specify paths to find files staged into the sysroot from other recipes, use the variables that the OpenEmbedded build system provides (e.g. `STAGING_BINDIR`{.interpreted-text role="term"}, `STAGING_INCDIR`{.interpreted-text role="term"}, `STAGING_DATADIR`{.interpreted-text role="term"}, and so forth).

> 有时，有必要对源应用修补程序，以确保使用正确的路径。如果您需要指定路径来查找从其他配方转移到系统根目录中的文件，请使用 OpenEmbedded 构建系统提供的变量（例如，`STAGING_BINDIR`｛.explored text role=“term”｝、`STAGING_INCDIR`{.explered text rol=“term“｝、` STAGING_DATADIR`｛。

# Installing

During `ref-tasks-install`{.interpreted-text role="ref"}, the task copies the built files along with their hierarchy to locations that would mirror their locations on the target device. The installation process copies files from the `${``S`{.interpreted-text role="term"}`}`, `${``B`{.interpreted-text role="term"}`}`, and `${``WORKDIR`{.interpreted-text role="term"}`}` directories to the `${``D`{.interpreted-text role="term"}`}` directory to create the structure as it should appear on the target system.

> 在“ref tasks install”｛.respered text role=“ref”｝期间，该任务将生成的文件及其层次结构复制到将镜像其在目标设备上的位置的位置。安装过程将文件从 `$｛` S `｛.explored text role=“term”｝``、`${`B`｛.Sexplored textrole=”term“｝`｝`和`${`WORKDIR`｛.sexplored extrole=”term“｝`}` 目录复制到 `$｛` D `｛.expered text role=“term”｝` 目录，以创建应出现在目标系统上的结构。

How your software is built affects what you must do to be sure your software is installed correctly. The following list describes what you must do for installation depending on the type of build system used by the software being built:

> 软件的构建方式会影响您必须做的事情，以确保软件安装正确。以下列表描述了安装时必须执行的操作，具体取决于正在生成的软件所使用的生成系统的类型：

- *Autotools and CMake:* If the software your recipe is building uses Autotools or CMake, the OpenEmbedded build system understands how to install the software. Consequently, you do not have to have a `ref-tasks-install`{.interpreted-text role="ref"} task as part of your recipe. You just need to make sure the install portion of the build completes with no issues. However, if you wish to install additional files not already being installed by `make install`, you should do this using a `do_install:append` function using the install command as described in the \"Manual\" bulleted item later in this list.

> -*自动工具和 CMake:*如果您的配方正在构建的软件使用自动工具或 CMake，则 OpenEmbedded 构建系统了解如何安装该软件。因此，您不必将“ref tasks install”｛.explored text role=“ref”｝任务作为配方的一部分。您只需要确保构建的安装部分顺利完成。但是，如果您希望安装“make-install”尚未安装的其他文件，则应使用“do_install:append”函数，使用安装命令执行此操作，如该列表后面的\“Manual\”项目符号中所述。

- *Other (using* `make install`*)*: You need to define a `ref-tasks-install`{.interpreted-text role="ref"} function in your recipe. The function should call `oe_runmake install` and will likely need to pass in the destination directory as well. How you pass that path is dependent on how the `Makefile` being run is written (e.g. `DESTDIR=${D}`, `PREFIX=${D}`, `INSTALLROOT=${D}`, and so forth).

> -*其他（使用* `make-install`*）*：您需要在配方中定义 `ref tasks install`{.depreted text role=“ref”}函数。该函数应该调用“oe_runmake-install”，并且可能还需要传入目标目录。如何传递该路径取决于正在运行的“Makefile”是如何写入的（例如“DESTDIR=${D}”、“PREFIX=${D｝”、“INSTALLROOT=${D:”等）。

For an example recipe using `make install`, see the \"`dev-manual/new-recipe:building a makefile-based package`{.interpreted-text role="ref"}\" section.

> 有关使用“make-install”的配方示例，请参阅\“`dev manual/new recipe:building a makefile based package`｛.depreted text role=”ref“｝\”一节。

- *Manual:* You need to define a `ref-tasks-install`{.interpreted-text role="ref"} function in your recipe. The function must first use `install -d` to create the directories under `${``D`{.interpreted-text role="term"}`}`. Once the directories exist, your function can use `install` to manually install the built software into the directories.

> -*手动：*您需要在配方中定义一个 `ref tasks install`｛.explored text role=“ref”｝函数。该函数必须首先使用“install-d”在“$｛``d `｛.explored text role=“term”｝`｝”下创建目录。一旦目录存在，您的功能就可以使用“安装”将构建的软件手动安装到目录中。

You can find more information on `install` at [https://www.gnu.org/software/coreutils/manual/html_node/install-invocation.html](https://www.gnu.org/software/coreutils/manual/html_node/install-invocation.html).

> 有关“安装”的详细信息，请访问 [https://www.gnu.org/software/coreutils/manual/html_node/install-invocation.html](https://www.gnu.org/software/coreutils/manual/html_node/install-invocation.html)。

For the scenarios that do not use Autotools or CMake, you need to track the installation and diagnose and fix any issues until everything installs correctly. You need to look in the default location of `${D}`, which is `${WORKDIR}/image`, to be sure your files have been installed correctly.

> 对于不使用 Autotools 或 CMake 的场景，您需要跟踪安装并诊断和修复任何问题，直到一切都正确安装。您需要查找“${D}”的默认位置，即“${WORKDIR}/image”，以确保您的文件已正确安装。

::: note
::: title
Note
:::

- During the installation process, you might need to modify some of the installed files to suit the target layout. For example, you might need to replace hard-coded paths in an initscript with values of variables provided by the build system, such as replacing `/usr/bin/` with `${bindir}`. If you do perform such modifications during `ref-tasks-install`{.interpreted-text role="ref"}, be sure to modify the destination file after copying rather than before copying. Modifying after copying ensures that the build system can re-execute `ref-tasks-install`{.interpreted-text role="ref"} if needed.

> -在安装过程中，您可能需要修改一些已安装的文件以适应目标布局。例如，您可能需要用构建系统提供的变量值替换 initscript 中的硬编码路径，例如用“$｛bindir｝”替换“/usr/bin/”。如果您确实在 `ref tasks install`｛.respered text role=“ref”｝期间执行了此类修改，请确保在复制后而不是复制前修改目标文件。复制后进行修改可确保生成系统可以在需要时重新执行 `ref tasks install`｛.respered text role=“ref”｝。

- `oe_runmake install`, which can be run directly or can be run indirectly by the `ref-classes-autotools`{.interpreted-text role="ref"} and `ref-classes-cmake`{.interpreted-text role="ref"} classes, runs `make install` in parallel. Sometimes, a Makefile can have missing dependencies between targets that can result in race conditions. If you experience intermittent failures during `ref-tasks-install`{.interpreted-text role="ref"}, you might be able to work around them by disabling parallel Makefile installs by adding the following to the recipe:

> -`oe_runmake-install`，它可以直接运行，也可以由 `ref classes autotools`｛.respered text role=“ref”｝和 `ref classes-cmake`｛.espered text role=“ref”}类间接运行，并行运行 `make-install'。有时，Makefile 可能在目标之间缺少依赖项，从而导致竞争条件。如果在“ref tasks install”｛.explored text role=“ref”｝期间遇到间歇性故障，您可以通过在配方中添加以下内容来禁用并行 Makefile 安装来解决这些问题：

```
PARALLEL_MAKEINST = ""
```

See `PARALLEL_MAKEINST`{.interpreted-text role="term"} for additional information.

> 有关更多信息，请参阅 `PARALLEL_MAKEIST`｛.explored text role=“term”｝。

- If you need to install one or more custom CMake toolchain files that are supplied by the application you are building, install the files to `${D}${datadir}/cmake/Modules` during `ref-tasks-install`{.interpreted-text role="ref"}.

> -如果您需要安装由您正在构建的应用程序提供的一个或多个自定义 CMake 工具链文件，请在“ref tasks install”期间将这些文件安装到“$｛D｝$｛datadir｝/CMake/Modules”中。
> :::

# Enabling System Services

If you want to install a service, which is a process that usually starts on boot and runs in the background, then you must include some additional definitions in your recipe.

> 如果你想安装一个服务，这是一个通常在启动时启动并在后台运行的过程，那么你必须在你的食谱中包括一些额外的定义。

If you are adding services and the service initialization script or the service file itself is not installed, you must provide for that installation in your recipe using a `do_install:append` function. If your recipe already has a `ref-tasks-install`{.interpreted-text role="ref"} function, update the function near its end rather than adding an additional `do_install:append` function.

> 如果您正在添加服务，但没有安装服务初始化脚本或服务文件本身，则必须使用“do_install:append”函数在配方中提供该安装。如果您的配方已经有一个 `ref tasks install`｛.explored text role=“ref”｝函数，请在其末尾附近更新该函数，而不是添加一个额外的 `do_install:append` 函数。

When you create the installation for your services, you need to accomplish what is normally done by `make install`. In other words, make sure your installation arranges the output similar to how it is arranged on the target system.

> 当您为服务创建安装时，您需要完成通常由“make-install”完成的操作。换句话说，确保您的安装安排输出的方式与目标系统上的安排方式类似。

The OpenEmbedded build system provides support for starting services two different ways:

> OpenEmbedded 构建系统通过两种不同的方式为启动服务提供支持：

- *SysVinit:* SysVinit is a system and service manager that manages the init system used to control the very basic functions of your system. The init program is the first program started by the Linux kernel when the system boots. Init then controls the startup, running and shutdown of all other programs.

> -*SysVinit:*SysVinit 是一个系统和服务管理器，用于管理用于控制系统基本功能的 init 系统。init 程序是 Linux 内核在系统启动时启动的第一个程序。Init 然后控制所有其他程序的启动、运行和关闭。

To enable a service using SysVinit, your recipe needs to inherit the `ref-classes-update-rc.d`{.interpreted-text role="ref"} class. The class helps facilitate safely installing the package on the target.

> 要使用 SysVinit 启用服务，您的配方需要继承 `ref classes update-rc.d`｛.depreted text role=“ref”｝类。该类有助于在目标上安全地安装程序包。

You will need to set the `INITSCRIPT_PACKAGES`{.interpreted-text role="term"}, `INITSCRIPT_NAME`{.interpreted-text role="term"}, and `INITSCRIPT_PARAMS`{.interpreted-text role="term"} variables within your recipe.

> 您需要在配方中设置 `INITSCRIPT_PACKAGES`｛.explored text role=“term”｝、`INITICRIPT_NAME`｛.explored textrole=”term“｝和 `INITICCRIPT_PARAMS`｛。

- *systemd:* System Management Daemon (systemd) was designed to replace SysVinit and to provide enhanced management of services. For more information on systemd, see the systemd homepage at [https://freedesktop.org/wiki/Software/systemd/](https://freedesktop.org/wiki/Software/systemd/).

> -*systemd:*系统管理守护程序（systemd）旨在取代 SysVinit 并提供增强的服务管理。有关 systemd 的更多信息，请参阅 systemd 主页 [https://freedesktop.org/wiki/Software/systemd/](https://freedesktop.org/wiki/Software/systemd/)。

To enable a service using systemd, your recipe needs to inherit the `ref-classes-systemd`{.interpreted-text role="ref"} class. See the `systemd.bbclass` file located in your `Source Directory`{.interpreted-text role="term"} section for more information.

> 要使用 systemd 启用服务，您的配方需要继承 `ref classes systemd`｛.depreted text role=“ref”｝类。有关详细信息，请参阅“源目录”中的“systemd.bbclass”文件｛.depreted text role=“term”｝部分。

# Packaging

Successful packaging is a combination of automated processes performed by the OpenEmbedded build system and some specific steps you need to take. The following list describes the process:

> 成功的打包是由 OpenEmbedded 构建系统执行的自动化过程和您需要采取的一些特定步骤的组合。以下列表描述了该过程：

- *Splitting Files*: The `ref-tasks-package`{.interpreted-text role="ref"} task splits the files produced by the recipe into logical components. Even software that produces a single binary might still have debug symbols, documentation, and other logical components that should be split out. The `ref-tasks-package`{.interpreted-text role="ref"} task ensures that files are split up and packaged correctly.

> -*拆分文件*：`ref tasks package`｛.explored text role=“ref”｝任务将配方产生的文件拆分为逻辑组件。即使是生成单个二进制文件的软件，也可能仍然有调试符号、文档和其他应该拆分的逻辑组件。`ref tasks package`｛.explored text role=“ref”｝任务可确保正确拆分和打包文件。

- *Running QA Checks*: The `ref-classes-insane`{.interpreted-text role="ref"} class adds a step to the package generation process so that output quality assurance checks are generated by the OpenEmbedded build system. This step performs a range of checks to be sure the build\'s output is free of common problems that show up during runtime. For information on these checks, see the `ref-classes-insane`{.interpreted-text role="ref"} class and the \"`ref-manual/qa-checks:qa error and warning messages`{.interpreted-text role="ref"}\" chapter in the Yocto Project Reference Manual.

> -*运行 QA 检查*：`ref classes neuro`｛.explored text role=“ref”｝类为包生成过程添加了一个步骤，以便由 OpenEmbedded 构建系统生成输出质量保证检查。此步骤执行一系列检查，以确保生成的输出没有在运行时出现的常见问题。有关这些检查的信息，请参阅 Yocto 项目参考手册中的 `ref classes neuro`｛.depreted text role=“ref”｝类和\“`ref manual/qa checks:qa错误和警告消息`｛.repreted text role=”ref“｝\”一章。

- *Hand-Checking Your Packages*: After you build your software, you need to be sure your packages are correct. Examine the `${``WORKDIR`{.interpreted-text role="term"}`}/packages-split` directory and make sure files are where you expect them to be. If you discover problems, you can set `PACKAGES`{.interpreted-text role="term"}, `FILES`{.interpreted-text role="term"}, `do_install(:append)`, and so forth as needed.

> -*手工检查您的软件包*：在您构建软件后，您需要确保您的程序包是正确的。检查 `$｛` WORKDIR `｛.explored text role=“term”｝`｝/packages split `目录，并确保文件位于您期望的位置。如果发现问题，可以根据需要设置` packages `｛.sexplored textrole=”term“｝、` files `｛.expered textrol=”term”}、` do_install（：append）` 等。

- *Splitting an Application into Multiple Packages*: If you need to split an application into several packages, see the \"`dev-manual/new-recipe:splitting an application into multiple packages`{.interpreted-text role="ref"}\" section for an example.

> -*将应用程序拆分为多个程序包*：如果需要将应用程序分解为几个程序包，请参阅“`dev manual/new recipe:将应用程序分割为多个软件包`｛.depreted text role=“ref”｝\”一节以获取示例。

- *Installing a Post-Installation Script*: For an example showing how to install a post-installation script, see the \"`dev-manual/new-recipe:post-installation scripts`{.interpreted-text role="ref"}\" section.

> -*安装安装后脚本*：有关如何安装安装后剧本的示例，请参阅\“`dev manual/new recipe:安装后脚本`{.depreted text role=“ref”}\”一节。

- *Marking Package Architecture*: Depending on what your recipe is building and how it is configured, it might be important to mark the packages produced as being specific to a particular machine, or to mark them as not being specific to a particular machine or architecture at all.

> -*标记包体系结构*：根据您的配方构建的内容和配置方式，将生产的包标记为特定于特定机器，或者将其标记为根本不特定于特定的机器或体系结构，这可能很重要。

By default, packages apply to any machine with the same architecture as the target machine. When a recipe produces packages that are machine-specific (e.g. the `MACHINE`{.interpreted-text role="term"} value is passed into the configure script or a patch is applied only for a particular machine), you should mark them as such by adding the following to the recipe:

> 默认情况下，包应用于与目标计算机具有相同体系结构的任何计算机。当配方产生特定于机器的包时（例如，`machine`｛.explored text role=“term”｝值被传递到配置脚本中，或者补丁仅应用于特定机器），您应该通过在配方中添加以下内容来标记它们：

```
PACKAGE_ARCH = "${MACHINE_ARCH}"
```

On the other hand, if the recipe produces packages that do not contain anything specific to the target machine or architecture at all (e.g. recipes that simply package script files or configuration files), you should use the `ref-classes-allarch`{.interpreted-text role="ref"} class to do this for you by adding this to your recipe:

> 另一方面，如果配方生成的包根本不包含任何特定于目标机器或体系结构的内容（例如，只打包脚本文件或配置文件的配方），则应使用 `ref classes allarch`｛.depreted text role=“ref”｝类为您完成此操作，方法是将其添加到配方中：

```
inherit allarch
```

Ensuring that the package architecture is correct is not critical while you are doing the first few builds of your recipe. However, it is important in order to ensure that your recipe rebuilds (or does not rebuild) appropriately in response to changes in configuration, and to ensure that you get the appropriate packages installed on the target machine, particularly if you run separate builds for more than one target machine.

> 在进行配方的前几次构建时，确保包体系结构正确并不重要。然而，重要的是要确保您的配方根据配置的变化进行适当的重建（或不重建），并确保在目标机器上安装了适当的软件包，特别是如果您为多台目标机器运行单独的构建。

# Sharing Files Between Recipes

Recipes often need to use files provided by other recipes on the build host. For example, an application linking to a common library needs access to the library itself and its associated headers. The way this access is accomplished is by populating a sysroot with files. Each recipe has two sysroots in its work directory, one for target files (`recipe-sysroot`) and one for files that are native to the build host (`recipe-sysroot-native`).

> 配方通常需要使用生成主机上其他配方提供的文件。例如，链接到公共库的应用程序需要访问库本身及其相关联的头。实现这种访问的方法是用文件填充系统根。每个配方的工作目录中都有两个系统根，一个用于目标文件（`recipe sysroot`），另一个用于构建主机的本地文件（`recipe sysroot native`）。

::: note
::: title
Note
:::

You could find the term \"staging\" used within the Yocto project regarding files populating sysroots (e.g. the `STAGING_DIR`{.interpreted-text role="term"} variable).

> 您可以在 Yocto 项目中找到关于填充系统根的文件的术语“staging”（例如，`staging_DIR`｛.depredicted text role=“term”｝变量）。
> :::

Recipes should never populate the sysroot directly (i.e. write files into sysroot). Instead, files should be installed into standard locations during the `ref-tasks-install`{.interpreted-text role="ref"} task within the `${``D`{.interpreted-text role="term"}`}` directory. The reason for this limitation is that almost all files that populate the sysroot are cataloged in manifests in order to ensure the files can be removed later when a recipe is either modified or removed. Thus, the sysroot is able to remain free from stale files.

> 配方不应直接填充 sysroot（即将文件写入 sysroot）。相反，在 `${` D `{.depreted text role=“term”}`}`目录中的` ref tasks install`{.depreced text role=“ref”}任务期间，应将文件安装到标准位置。这种限制的原因是，几乎所有填充 sysroot 的文件都在清单中编目，以确保以后在修改或删除配方时可以删除这些文件。因此，系统根能够保持不受陈旧文件的影响。

A subset of the files installed by the `ref-tasks-install`{.interpreted-text role="ref"} task are used by the `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} task as defined by the `SYSROOT_DIRS`{.interpreted-text role="term"} variable to automatically populate the sysroot. It is possible to modify the list of directories that populate the sysroot. The following example shows how you could add the `/opt` directory to the list of directories within a recipe:

> 由 `ref tasks install`｛.depreted text role=“ref”｝任务安装的文件的子集由 `sysroot_DIRS`｛.epreted text role=“term”｝变量定义的 `ref-tasks-populate_sysroot`｛.repreted text 角色=“ref”}任务使用，以自动填充 sysroot。可以修改填充系统根目录的目录列表。以下示例显示了如何将“/opt”目录添加到配方中的目录列表中：

```
SYSROOT_DIRS += "/opt"
```

::: note
::: title
Note
:::

The [/sysroot-only]{.title-ref} is to be used by recipes that generate artifacts that are not included in the target filesystem, allowing them to share these artifacts without needing to use the `DEPLOY_DIR`{.interpreted-text role="term"}.

> [/sysrootonly]｛.title-ref｝将由生成未包含在目标文件系统中的工件的配方使用，允许它们共享这些工件，而无需使用 `DEPLOY_DIR`｛.depreted text role=“term”｝。
> :::

For a more complete description of the `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} task and its associated functions, see the `staging <ref-classes-staging>`{.interpreted-text role="ref"} class.

> 有关 `ref-tasks-populate_sysroot`｛.depreted text role=“ref”｝任务及其相关功能的更完整描述，请参阅 `staging<ref classes staging>`｛.epreted text role=“ref”}类。

# Using Virtual Providers

Prior to a build, if you know that several different recipes provide the same functionality, you can use a virtual provider (i.e. `virtual/*`) as a placeholder for the actual provider. The actual provider is determined at build-time.

> 在构建之前，如果您知道几个不同的配方提供相同的功能，则可以使用虚拟提供者（即“虚拟/*”）作为实际提供者的占位符。实际提供程序在构建时确定。

A common scenario where a virtual provider is used would be for the kernel recipe. Suppose you have three kernel recipes whose `PN`{.interpreted-text role="term"} values map to `kernel-big`, `kernel-mid`, and `kernel-small`. Furthermore, each of these recipes in some way uses a `PROVIDES`{.interpreted-text role="term"} statement that essentially identifies itself as being able to provide `virtual/kernel`. Here is one way through the `ref-classes-kernel`{.interpreted-text role="ref"} class:

> 使用虚拟提供者的常见场景是内核配方。假设您有三个内核配方，其“PN”｛.explored text role=“term”｝值映射为“kernel big”、“kernel mid”和“kernel small”。此外，这些配方中的每一个都以某种方式使用了一个 `PROVIDES`｛.explored text role=“term”｝语句，该语句本质上将自己标识为能够提供 `virtual/kernel`。以下是 `ref classes kernel`｛.explored text role=“ref”｝类的一种方法：

```
PROVIDES += "virtual/kernel"
```

Any recipe that inherits the `ref-classes-kernel`{.interpreted-text role="ref"} class is going to utilize a `PROVIDES`{.interpreted-text role="term"} statement that identifies that recipe as being able to provide the `virtual/kernel` item.

> 任何继承 `ref classes kernel`｛.depreted text role=“ref”｝类的配方都将使用 `PROVIDES`｛.repreted text role=“term”｝语句，该语句将该配方标识为能够提供 `virtual/kernel` 项。

Now comes the time to actually build an image and you need a kernel recipe, but which one? You can configure your build to call out the kernel recipe you want by using the `PREFERRED_PROVIDER`{.interpreted-text role="term"} variable. As an example, consider the :yocto\_[git:%60x86-base.inc](git:%60x86-base.inc) \</poky/tree/meta/conf/machine/include/x86/x86-base.inc\>[ include file, which is a machine (i.e. :term:\`MACHINE]{.title-ref}) configuration file. This include file is the reason all x86-based machines use the `linux-yocto` kernel. Here are the relevant lines from the include file:

> 现在是真正构建映像的时候了，你需要一个内核配方，但哪一个呢？您可以通过使用 `PREFERRED_PROVIDER`｛.explored text role=“term”｝变量来配置您的构建以调用所需的内核配方。例如，考虑一下：yocto\_[git:%60x86-base.inc]（git:%60x86-base.inc）\</poky/tree/meta/conf/machine/include/x86/x86-base.inc\>[include 文件，它是一个机器（即：term:\`machine]{.title-ref}）配置文件。这个包含文件是所有基于 x86 的机器使用“linux yocto”内核的原因。以下是 include 文件中的相关行：

```
PREFERRED_PROVIDER_virtual/kernel ??= "linux-yocto"
PREFERRED_VERSION_linux-yocto ??= "4.15%"
```

When you use a virtual provider, you do not have to \"hard code\" a recipe name as a build dependency. You can use the `DEPENDS`{.interpreted-text role="term"} variable to state the build is dependent on `virtual/kernel` for example:

> 当您使用虚拟提供程序时，您不必“硬编码”配方名称作为构建依赖项。您可以使用 `DEPENDS`｛.explored text role=“term”｝变量来声明构建依赖于 `virtual/kernel`，例如：

```
DEPENDS = "virtual/kernel"
```

During the build, the OpenEmbedded build system picks the correct recipe needed for the `virtual/kernel` dependency based on the `PREFERRED_PROVIDER`{.interpreted-text role="term"} variable. If you want to use the small kernel mentioned at the beginning of this section, configure your build as follows:

> 在构建过程中，OpenEmbedded 构建系统根据 `PREFERRED_PROVIDER`{.depreted text role=“term”}变量选择“虚拟/内核”依赖项所需的正确配方。如果您想使用本节开头提到的小内核，请按以下方式配置您的构建：

```
PREFERRED_PROVIDER_virtual/kernel ??= "kernel-small"
```

::: note
::: title
Note
:::

Any recipe that `PROVIDES`{.interpreted-text role="term"} a `virtual/*` item that is ultimately not selected through `PREFERRED_PROVIDER`{.interpreted-text role="term"} does not get built. Preventing these recipes from building is usually the desired behavior since this mechanism\'s purpose is to select between mutually exclusive alternative providers.

> 任何“提供”｛.depreced text role=“term”｝一个“虚拟/*”项目的配方，而该项目最终不是通过“PREFERRED_PROVIDER”｛.epreced textrole=”term｝选择的，都不会生成。阻止这些配方的构建通常是所需的行为，因为这种机制的目的是在相互排斥的替代提供者之间进行选择。
> :::

The following lists specific examples of virtual providers:

> 以下列出了虚拟提供程序的具体示例：

- `virtual/kernel`: Provides the name of the kernel recipe to use when building a kernel image.
- `virtual/bootloader`: Provides the name of the bootloader to use when building an image.
- `virtual/libgbm`: Provides `gbm.pc`.
- `virtual/egl`: Provides `egl.pc` and possibly `wayland-egl.pc`.
- `virtual/libgl`: Provides `gl.pc` (i.e. libGL).
- `virtual/libgles1`: Provides `glesv1_cm.pc` (i.e. libGLESv1_CM).
- `virtual/libgles2`: Provides `glesv2.pc` (i.e. libGLESv2).

::: note
::: title
Note
:::

Virtual providers only apply to build time dependencies specified with `PROVIDES`{.interpreted-text role="term"} and `DEPENDS`{.interpreted-text role="term"}. They do not apply to runtime dependencies specified with `RPROVIDES`{.interpreted-text role="term"} and `RDEPENDS`{.interpreted-text role="term"}.

> 虚拟提供程序仅适用于用 `PROVIDES`｛.depredicted text role=“term”｝和 `DEPENDS`｛.epredicted textrole=”term“｝指定的生成时依赖项。它们不适用于用 `RPROVIDES`｛.depreted text role=“term”｝和 `RDEPENDS`｛.repreted text role=“term“｝指定的运行时依赖项。
> :::

# Properly Versioning Pre-Release Recipes

Sometimes the name of a recipe can lead to versioning problems when the recipe is upgraded to a final release. For example, consider the `irssi_0.8.16-rc1.bb` recipe file in the list of example recipes in the \"`dev-manual/new-recipe:storing and naming the recipe`{.interpreted-text role="ref"}\" section. This recipe is at a release candidate stage (i.e. \"rc1\"). When the recipe is released, the recipe filename becomes `irssi_0.8.16.bb`. The version change from `0.8.16-rc1` to `0.8.16` is seen as a decrease by the build system and package managers, so the resulting packages will not correctly trigger an upgrade.

> 有时，当配方升级到最终版本时，配方的名称可能会导致版本控制问题。例如，考虑“dev manual/new recipe:storing and named the recipe”部分中示例配方列表中的“irssi0.8.16-rc1.bb”recipe 文件。此配方处于候选版本阶段（即“rc1\”）。发布配方时，配方文件名变为“irssi-0.8.16.bb”。版本从“0.8.16-rc1”更改为“0.8.16”被构建系统和包管理器视为减少，因此生成的包将不会正确触发升级。

In order to ensure the versions compare properly, the recommended convention is to set `PV`{.interpreted-text role="term"} within the recipe to \"previous_version+current_version\". You can use an additional variable so that you can use the current version elsewhere. Here is an example:

> 为了确保版本之间的正确比较，建议的惯例是将配方中的 `PV`｛.depredicted text role=“term”｝设置为\“previous_version+current_version\”。您可以使用额外的变量，以便在其他地方使用当前版本。以下是一个示例：

```
REALPV = "0.8.16-rc1"
PV = "0.8.15+${REALPV}"
```

# Post-Installation Scripts

Post-installation scripts run immediately after installing a package on the target or during image creation when a package is included in an image. To add a post-installation script to a package, add a `pkg_postinst:`[PACKAGENAME]{.title-ref}`()` function to the recipe file (`.bb`) and replace [PACKAGENAME]{.title-ref} with the name of the package you want to attach to the `postinst` script. To apply the post-installation script to the main package for the recipe, which is usually what is required, specify `${``PN`{.interpreted-text role="term"}`}` in place of PACKAGENAME.

> 在目标上安装程序包后立即运行安装后脚本，或者在映像中包含程序包时在映像创建过程中立即运行。要将安装后脚本添加到程序包中，请将 `pkg_postinst:`[PACKAGENAME]{.title-ref}`（）` 函数添加到配方文件（`.bb`）中，并用要附加到 `postinst` 脚本的程序包的名称替换[PACKAGENAME]{.ttitle-ref}。要将安装后脚本应用于配方的主程序包（这通常是必需的），请指定 `${` PN `{.expreted text role=“term”}`}` 代替 PACKAGENAME。

A post-installation function has the following structure:

> 安装后功能具有以下结构：

```
pkg_postinst:PACKAGENAME() {
    # Commands to carry out
}
```

The script defined in the post-installation function is called when the root filesystem is created. If the script succeeds, the package is marked as installed.

> 在创建根文件系统时，会调用安装后函数中定义的脚本。如果脚本成功，则该包被标记为已安装。

::: note
::: title
Note
:::

Any RPM post-installation script that runs on the target should return a 0 exit code. RPM does not allow non-zero exit codes for these scripts, and the RPM package manager will cause the package to fail installation on the target.

> 在目标上运行的任何 RPM 安装后脚本都应该返回 0 退出代码。RPM 不允许这些脚本使用非零退出代码，并且 RPM 包管理器将导致包在目标上安装失败。
> :::

Sometimes it is necessary for the execution of a post-installation script to be delayed until the first boot. For example, the script might need to be executed on the device itself. To delay script execution until boot time, you must explicitly mark post installs to defer to the target. You can use `pkg_postinst_ontarget()` or call `postinst_intercept delay_to_first_boot` from `pkg_postinst()`. Any failure of a `pkg_postinst()` script (including exit 1) triggers an error during the `ref-tasks-rootfs`{.interpreted-text role="ref"} task.

> 有时，安装后脚本的执行需要延迟到第一次启动。例如，脚本可能需要在设备本身上执行。要将脚本执行延迟到启动时间，必须明确标记后安装以推迟到目标。您可以使用 `pkg_postinst_ontarget（）` 或从 `pkg_positinst（）` 调用 `postinst_intercept delay_to_first_boot`。“pkg_postinst（）”脚本（包括退出 1）的任何失败都会在“ref tasks rootfs”｛.depreted text role=“ref”｝任务期间触发错误。

If you have recipes that use `pkg_postinst` function and they require the use of non-standard native tools that have dependencies during root filesystem construction, you need to use the `PACKAGE_WRITE_DEPS`{.interpreted-text role="term"} variable in your recipe to list these tools. If you do not use this variable, the tools might be missing and execution of the post-installation script is deferred until first boot. Deferring the script to the first boot is undesirable and impossible for read-only root filesystems.

> 如果您有使用“pkg_postinst”函数的配方，并且它们需要使用在根文件系统构建过程中具有依赖关系的非标准本机工具，则需要使用配方中的“PACKAGE_WRITE_DEPS”｛.depredicted text role=“term”｝变量来列出这些工具。如果不使用此变量，工具可能会丢失，安装后脚本的执行将推迟到第一次启动。对于只读根文件系统来说，将脚本推迟到第一次启动是不可取的，也是不可能的。

::: note
::: title
Note
:::

There is equivalent support for pre-install, pre-uninstall, and post-uninstall scripts by way of `pkg_preinst`, `pkg_prerm`, and `pkg_postrm`, respectively. These scrips work in exactly the same way as does `pkg_postinst` with the exception that they run at different times. Also, because of when they run, they are not applicable to being run at image creation time like `pkg_postinst`.

> 分别通过“pkg_preinst”、“pkg_presrm”和“pkg_postrm”对预安装、预卸载和卸载后脚本提供了等效的支持。这些 scrip 的工作方式与“pkg_postinst”完全相同，只是它们在不同的时间运行。此外，由于它们在运行时不适用于像“pkg_postinst”那样在图像创建时运行。
> :::

# Testing

The final step for completing your recipe is to be sure that the software you built runs correctly. To accomplish runtime testing, add the build\'s output packages to your image and test them on the target.

> 完成食谱的最后一步是确保您构建的软件正确运行。要完成运行时测试，请将构建的输出包添加到映像中，并在目标上进行测试。

For information on how to customize your image by adding specific packages, see \"`dev-manual/customizing-images:customizing images`{.interpreted-text role="ref"}\" section.

> 有关如何通过添加特定包来自定义图像的信息，请参阅“`dev manual/customing images:customing images`｛.depreted text role=“ref”｝\”一节。

# Examples

To help summarize how to write a recipe, this section provides some recipe examples given various scenarios:

> 为了帮助总结如何编写食谱，本节提供了一些不同场景下的食谱示例：

- [Building a single .c file package](#building-a-single-.c-file-package)
- [Building a Makefile-based package](#building-a-makefile-based-package)
- [Building an Autotooled package](#building-an-autotooled-package)
- [Building a Meson package](#building-a-meson-package)
- [Splitting an application into multiple packages](#splitting-an-application-into-multiple-packages)
- [Packaging externally produced binaries](#packaging-externally-produced-binaries)

## Building a Single .c File Package

Building an application from a single file that is stored locally (e.g. under `files`) requires a recipe that has the file listed in the `SRC_URI`{.interpreted-text role="term"} variable. Additionally, you need to manually write the `ref-tasks-compile`{.interpreted-text role="ref"} and `ref-tasks-install`{.interpreted-text role="ref"} tasks. The `S`{.interpreted-text role="term"} variable defines the directory containing the source code, which is set to `WORKDIR`{.interpreted-text role="term"} in this case \-\-- the directory BitBake uses for the build:

> 从本地存储的单个文件（例如在“files”下）构建应用程序需要一个配方，该配方将文件列在“SRC_URI”｛.explored text role=“term”｝变量中。此外，您需要手动编写 `ref tasks compile`｛.depreted text role=“ref”｝和 `ref tasks-install`｛.repreted text role=“ref”}任务。`S`｛.depreted text role=“term”｝变量定义了包含源代码的目录，在这种情况下，该目录设置为 `WORKDIR`｛.repreted text role=“term“｝——BitBake 用于构建的目录：

```
SUMMARY = "Simple helloworld application"
SECTION = "examples"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

SRC_URI = "file://helloworld.c"

S = "${WORKDIR}"

do_compile() {
    ${CC} ${LDFLAGS} helloworld.c -o helloworld
}

do_install() {
    install -d ${D}${bindir}
    install -m 0755 helloworld ${D}${bindir}
}
```

By default, the `helloworld`, `helloworld-dbg`, and `helloworld-dev` packages are built. For information on how to customize the packaging process, see the \"`dev-manual/new-recipe:splitting an application into multiple packages`{.interpreted-text role="ref"}\" section.

> 默认情况下，会构建“helloworld”、“hellowworld dbg”和“hellow 天地 dev”包。有关如何自定义打包过程的信息，请参阅\“`dev manual/new recipe:将应用程序拆分为多个包`{.depreted text role=“ref”}\”一节。

## Building a Makefile-Based Package

Applications built with GNU `make` require a recipe that has the source archive listed in `SRC_URI`{.interpreted-text role="term"}. You do not need to add a `ref-tasks-compile`{.interpreted-text role="ref"} step since by default BitBake starts the `make` command to compile the application. If you need additional `make` options, you should store them in the `EXTRA_OEMAKE`{.interpreted-text role="term"} or `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"} variables. BitBake passes these options into the GNU `make` invocation. Note that a `ref-tasks-install`{.interpreted-text role="ref"} task is still required. Otherwise, BitBake runs an empty `ref-tasks-install`{.interpreted-text role="ref"} task by default.

> 使用 GNU“make”构建的应用程序需要一个配方，该配方的源存档列在“SRC_URI”｛.explored text role=“term”｝中。您不需要添加 `ref tasks compile`｛.explored text role=“ref”｝步骤，因为默认情况下 BitBake 会启动 `make` 命令来编译应用程序。如果您需要额外的“make”选项，则应将它们存储在 `EXTRA_OEMAKE`｛.depreced text role=“term”｝或 `PACKACECONFIG_CONFARGS`｛.epreced text role=”term“｝变量中。BitBake 将这些选项传递到 GNU 的“make”调用中。请注意，“ref tasks install”｛.depreted text role=“ref”｝任务仍然是必需的。否则，默认情况下，BitBake 运行一个空的 `ref tasks install`{.depreted text role=“ref”}任务。

Some applications might require extra parameters to be passed to the compiler. For example, the application might need an additional header path. You can accomplish this by adding to the `CFLAGS`{.interpreted-text role="term"} variable. The following example shows this:

> 某些应用程序可能需要将额外的参数传递给编译器。例如，应用程序可能需要一个额外的头路径。您可以通过添加到 `CFLAGS`｛.explored text role=“term”｝变量来实现这一点。以下示例显示了这一点：

```
CFLAGS:prepend = "-I ${S}/include "
```

In the following example, `lz4` is a makefile-based package:

> 在以下示例中，“lz4”是一个基于 makefile 的包：

```
SUMMARY = "Extremely Fast Compression algorithm"
DESCRIPTION = "LZ4 is a very fast lossless compression algorithm, providing compression speed at 400 MB/s per core, scalable with multi-cores CPU. It also features an extremely fast decoder, with speed in multiple GB/s per core, typically reaching RAM speed limits on multi-core systems."
HOMEPAGE = "https://github.com/lz4/lz4"

LICENSE = "BSD-2-Clause | GPL-2.0-only"
LIC_FILES_CHKSUM = "file://lib/LICENSE;md5=ebc2ea4814a64de7708f1571904b32cc \
                    file://programs/COPYING;md5=b234ee4d69f5fce4486a80fdaf4a4263 \
                    file://LICENSE;md5=d57c0d21cb917fb4e0af2454aa48b956 \
                    "

PE = "1"

SRCREV = "d44371841a2f1728a3f36839fd4b7e872d0927d3"

SRC_URI = "git://github.com/lz4/lz4.git;branch=release;protocol=https \
           file://CVE-2021-3520.patch \
           "
UPSTREAM_CHECK_GITTAGREGEX = "v(?P<pver>.*)"

S = "${WORKDIR}/git"

# Fixed in r118, which is larger than the current version.
CVE_CHECK_IGNORE += "CVE-2014-4715"

EXTRA_OEMAKE = "PREFIX=${prefix} CC='${CC}' CFLAGS='${CFLAGS}' DESTDIR=${D} LIBDIR=${libdir} INCLUDEDIR=${includedir} BUILD_STATIC=no"

do_install() {
        oe_runmake install
}

BBCLASSEXTEND = "native nativesdk"
```

## Building an Autotooled Package

Applications built with the Autotools such as `autoconf` and `automake` require a recipe that has a source archive listed in `SRC_URI`{.interpreted-text role="term"} and also inherit the `ref-classes-autotools`{.interpreted-text role="ref"} class, which contains the definitions of all the steps needed to build an Autotool-based application. The result of the build is automatically packaged. And, if the application uses NLS for localization, packages with local information are generated (one package per language). Following is one example: (`hello_2.3.bb`):

> 使用自动工具构建的应用程序（如“autoconf”和“automake”）需要一个配方，该配方的源存档列在“SRC_URI”｛.depreted text role=“term”｝中，并且还继承了“ref classes Autotools”｛.repreted text role=“ref”｝类，该类包含构建基于自动工具的应用程序所需的所有步骤的定义。生成的结果将自动打包。而且，如果应用程序使用 NLS 进行本地化，则会生成具有本地信息的包（每种语言一个包）。以下是一个示例：（`hello_2.3.bb`）：

```
SUMMARY = "GNU Helloworld application"
SECTION = "examples"
LICENSE = "GPL-2.0-or-later"
LIC_FILES_CHKSUM = "file://COPYING;md5=751419260aa954499f7abaabaa882bbe"

SRC_URI = "${GNU_MIRROR}/hello/hello-${PV}.tar.gz"

inherit autotools gettext
```

The variable `LIC_FILES_CHKSUM`{.interpreted-text role="term"} is used to track source license changes as described in the \"`dev-manual/licenses:tracking license changes`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual. You can quickly create Autotool-based recipes in a manner similar to the previous example.

> 变量 `LIC_FILES_CHKSUM`｛.explored text role=“term”｝用于跟踪源许可证更改，如 Yocto 项目概述和概念手册中的\“`dev manual/licenses:跟踪许可证更改”｛。您可以以类似于前面示例的方式快速创建基于 Autotool 的配方。

## Building a Meson Package {#ref-building-meson-package}

Applications built with the [Meson build system](https://mesonbuild.com/) just need a recipe that has sources described in `SRC_URI`{.interpreted-text role="term"} and inherits the `ref-classes-meson`{.interpreted-text role="ref"} class.

> 使用〔Meson 构建系统〕构建的应用程序([https://mesonbuild.com/](https://mesonbuild.com/))只需要一个配方，该配方具有在 `SRC_URI`｛.depreted text role=“term”｝中描述的源，并继承 `ref classes meson`｛.repreted text role=“ref”}类。

The :oe\_[git:%60ipcalc](git:%60ipcalc) recipe \</meta-openembedded/tree/meta-networking/recipes-support/ipcalc\>\` is a simple example of an application without dependencies:

> ：oe\_[git:%60ipcalc]（git:%60ipcalc）recipe\</meta-openembedded/tree/meta-networking/precipes-support/ipcalc\>\`是一个没有依赖关系的应用程序的简单示例：

```
SUMMARY = "Tool to assist in network address calculations for IPv4 and IPv6."
HOMEPAGE = "https://gitlab.com/ipcalc/ipcalc"

SECTION = "net"

LICENSE = "GPL-2.0-only"
LIC_FILES_CHKSUM = "file://COPYING;md5=b234ee4d69f5fce4486a80fdaf4a4263"

SRC_URI = "git://gitlab.com/ipcalc/ipcalc.git;protocol=https;branch=master"
SRCREV = "4c4261a47f355946ee74013d4f5d0494487cc2d6"

S = "${WORKDIR}/git"

inherit meson
```

Applications with dependencies are likely to inherit the `ref-classes-pkgconfig`{.interpreted-text role="ref"} class, as `pkg-config` is the default method used by Meson to find dependencies and compile applications against them.

> 具有依赖项的应用程序可能会继承 `ref-classes pkgconfig`｛.respered text role=“ref”}类，因为 `pkg config` 是 Meson 用来查找依赖项并根据它们编译应用程序的默认方法。

## Splitting an Application into Multiple Packages

You can use the variables `PACKAGES`{.interpreted-text role="term"} and `FILES`{.interpreted-text role="term"} to split an application into multiple packages.

> 您可以使用变量 `PACKAGES`｛.depreced text role=“term”｝和 `FILES`｛.epreced textrole=”term“｝将应用程序拆分为多个程序包。

Following is an example that uses the `libxpm` recipe. By default, this recipe generates a single package that contains the library along with a few binaries. You can modify the recipe to split the binaries into separate packages:

> 下面是一个使用“libxpm”配方的示例。默认情况下，此配方生成一个包含库和一些二进制文件的包。您可以修改配方以将二进制文件拆分为单独的包：

```
require xorg-lib-common.inc

SUMMARY = "Xpm: X Pixmap extension library"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://COPYING;md5=51f4270b012ecd4ab1a164f5f4ed6cf7"
DEPENDS += "libxext libsm libxt"
PE = "1"

XORG_PN = "libXpm"

PACKAGES =+ "sxpm cxpm"
FILES:cxpm = "${bindir}/cxpm"
FILES:sxpm = "${bindir}/sxpm"
```

In the previous example, we want to ship the `sxpm` and `cxpm` binaries in separate packages. Since `bindir` would be packaged into the main `PN`{.interpreted-text role="term"} package by default, we prepend the `PACKAGES`{.interpreted-text role="term"} variable so additional package names are added to the start of list. This results in the extra `FILES:*` variables then containing information that define which files and directories go into which packages. Files included by earlier packages are skipped by latter packages. Thus, the main `PN`{.interpreted-text role="term"} package does not include the above listed files.

> 在前面的示例中，我们希望将“sxpm”和“cxpm”二进制文件分别封装在不同的包中。由于默认情况下 `bindir` 将被打包到主 `PN`｛.depredicted text role=“term”｝包中，因此我们在 `PACKAGES`｛.epredicted textrole=”term“｝变量前面加上一个前缀，以便将其他包名称添加到列表的开头。这导致额外的“FILES:*”变量，然后包含定义哪些文件和目录进入哪些包的信息。前一个包包含的文件被后一个包跳过。因此，主 `PN`{.depredicted text role=“term”}包不包括上面列出的文件。

## Packaging Externally Produced Binaries

Sometimes, you need to add pre-compiled binaries to an image. For example, suppose that there are binaries for proprietary code, created by a particular division of a company. Your part of the company needs to use those binaries as part of an image that you are building using the OpenEmbedded build system. Since you only have the binaries and not the source code, you cannot use a typical recipe that expects to fetch the source specified in `SRC_URI`{.interpreted-text role="term"} and then compile it.

> 有时，您需要将预编译的二进制文件添加到映像中。例如，假设有一个公司的特定部门创建的专有代码的二进制文件。您所在的公司需要将这些二进制文件作为使用 OpenEmbedded 构建系统构建的映像的一部分。由于您只有二进制文件而没有源代码，因此无法使用期望获取 `SRC_URI`｛.depreted text role=“term”｝中指定的源代码然后进行编译的典型配方。

One method is to package the binaries and then install them as part of the image. Generally, it is not a good idea to package binaries since, among other things, it can hinder the ability to reproduce builds and could lead to compatibility problems with ABI in the future. However, sometimes you have no choice.

> 一种方法是打包二进制文件，然后将其作为映像的一部分进行安装。一般来说，打包二进制文件不是一个好主意，因为除其他外，它可能会阻碍复制构建的能力，并可能导致未来与 ABI 的兼容性问题。然而，有时你别无选择。

The easiest solution is to create a recipe that uses the `ref-classes-bin-package`{.interpreted-text role="ref"} class and to be sure that you are using default locations for build artifacts. In most cases, the `ref-classes-bin-package`{.interpreted-text role="ref"} class handles \"skipping\" the configure and compile steps as well as sets things up to grab packages from the appropriate area. In particular, this class sets `noexec` on both the `ref-tasks-configure`{.interpreted-text role="ref"} and `ref-tasks-compile`{.interpreted-text role="ref"} tasks, sets `FILES:${PN}` to \"/\" so that it picks up all files, and sets up a `ref-tasks-install`{.interpreted-text role="ref"} task, which effectively copies all files from `${S}` to `${D}`. The `ref-classes-bin-package`{.interpreted-text role="ref"} class works well when the files extracted into `${S}` are already laid out in the way they should be laid out on the target. For more information on these variables, see the `FILES`{.interpreted-text role="term"}, `PN`{.interpreted-text role="term"}, `S`{.interpreted-text role="term"}, and `D`{.interpreted-text role="term"} variables in the Yocto Project Reference Manual\'s variable glossary.

> 最简单的解决方案是创建一个使用 `ref classes bin package`｛.explored text role=“ref”｝类的配方，并确保您使用的是构建工件的默认位置。在大多数情况下，`ref classes bin package`｛.explored text role=“ref”｝类处理“跳过”配置和编译步骤，并设置从适当区域获取包。特别是，此类在“ref tasks configure”｛.depredicted text role=“ref”｝和“ref task compile”｛.epredicted textrole=”ref“｝任务上都设置了“noexec”，将“FILES:$｛PN｝”设置为\“/\”，以便拾取所有文件，并设置了一个“ref tasks-install”｛.repredicted text rol=“ref”}任务，该任务可以有效地将所有文件从“$｛S｝”复制到“${D｝”。当提取到“$｛S｝”中的文件已经按照它们在目标上的布局方式进行布局时，“ref classes bin package”｛.explored text role=“ref”｝类工作良好。有关这些变量的详细信息，请参阅 Yocto 项目参考手册的变量词汇表中的 `FILES`｛.depredicted text role=“term”｝、`PN`｛.epredicted text role=”term“｝、` S`｛.repredicted textrole=‘term”}和 `D`｛.expredicted extrole='term“}变量。

::: note
::: title
Note
:::

- Using `DEPENDS`{.interpreted-text role="term"} is a good idea even for components distributed in binary form, and is often necessary for shared libraries. For a shared library, listing the library dependencies in `DEPENDS`{.interpreted-text role="term"} makes sure that the libraries are available in the staging sysroot when other recipes link against the library, which might be necessary for successful linking.

> -即使对于以二进制形式分布的组件来说，使用 `DEPENDS`{.depredicted text role=“term”}也是一个好主意，并且对于共享库来说通常是必要的。对于共享库，在 `DEPENDS`｛.explored text role=“term”｝中列出库依赖项可确保当其他配方链接到库时，这些库在临时系统根中可用，这可能是成功链接所必需的。

- Using `DEPENDS`{.interpreted-text role="term"} also allows runtime dependencies between packages to be added automatically. See the \"`overview-manual/concepts:automatically added runtime dependencies`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual for more information.

> -使用 `DEPENDS`｛.explored text role=“term”｝还允许自动添加包之间的运行时依赖关系。有关详细信息，请参阅 Yocto 项目概述和概念手册中的\“`overview manual/concepts:automatically added runtime dependencies`｛.respered text role=“ref”｝\”一节。
> :::

If you cannot use the `ref-classes-bin-package`{.interpreted-text role="ref"} class, you need to be sure you are doing the following:

> 如果您不能使用 `ref classes bin package`｛.explored text role=“ref”｝类，则需要确保您正在执行以下操作：

- Create a recipe where the `ref-tasks-configure`{.interpreted-text role="ref"} and `ref-tasks-compile`{.interpreted-text role="ref"} tasks do nothing: It is usually sufficient to just not define these tasks in the recipe, because the default implementations do nothing unless a Makefile is found in `${``S`{.interpreted-text role="term"}`}`.

> -创建一个配方，其中 `ref tasks configure`{.depreted text role=“ref”}和 `ref tasks-compile`{.depredicted text role=“ref”}tasks 什么都不做：通常在配方中不定义这些任务就足够了，因为除非在 `${` S` 中找到 Makefile，否则默认实现什么也不做。

If `${S}` might contain a Makefile, or if you inherit some class that replaces `ref-tasks-configure`{.interpreted-text role="ref"} and `ref-tasks-compile`{.interpreted-text role="ref"} with custom versions, then you can use the `[``noexec <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>`{.interpreted-text role="ref"}`]` flag to turn the tasks into no-ops, as follows:

> 如果“$｛S｝”可能包含 Makefile，或者如果您继承了某个类，该类用自定义版本替换了“ref tasks configure”｛.explored text role=“ref”｝和“ref tasks-compile”｛..explored text-role=“ref”}，则您可以使用“[`noexec＜bitbake user manual/bitbake user-manual metadata:variable flags＞”｛.sexplered text rol=“ref“｝`]”标志将任务转换为非操作，如下所示：

```
do_configure[noexec] = "1"
do_compile[noexec] = "1"
```

Unlike `bitbake-user-manual/bitbake-user-manual-metadata:deleting a task`{.interpreted-text role="ref"}, using the flag preserves the dependency chain from the `ref-tasks-fetch`{.interpreted-text role="ref"}, `ref-tasks-unpack`{.interpreted-text role="ref"}, and `ref-tasks-patch`{.interpreted-text role="ref"} tasks to the `ref-tasks-install`{.interpreted-text role="ref"} task.

> 与 `bitbake用户手册/bitbake用户手册元数据：删除任务`{.depredicted text role=“ref”}不同，使用该标志将 `ref tasks fetch`{.depreced text rol=“ref”｝、`ref tasks-unpack`{.epredicted text-role=“ref“｝和 `ref tasks-cpatch`{.eppeded text role=“ref“}任务的依赖链保留到 `ref tasks-install`{.deverted text rol=”ref“}。

- Make sure your `ref-tasks-install`{.interpreted-text role="ref"} task installs the binaries appropriately.

> -确保您的“ref tasks install”｛.depreted text role=“ref”｝任务正确安装二进制文件。

- Ensure that you set up `FILES`{.interpreted-text role="term"} (usually `FILES:${``PN`{.interpreted-text role="term"}`}`) to point to the files you have installed, which of course depends on where you have installed them and whether those files are in different locations than the defaults.

> -请确保设置 `FILES`｛.depredicted text role=“term”｝（通常为 `FILES:$｛` PN `｛.epredicted textrole=”term“｝`）以指向已安装的文件，这当然取决于您将这些文件安装在何处，以及这些文件是否位于与默认位置不同的位置。

# Following Recipe Style Guidelines

When writing recipes, it is good to conform to existing style guidelines. The :oe_wiki:[OpenEmbedded Styleguide \</Styleguide\>]{.title-ref} wiki page provides rough guidelines for preferred recipe style.

> 在编写食谱时，遵循现有的风格准则是很好的。：oe_wiki:[OpenEmbedded 样式指南\</Styleguide\>]｛.title-ref｝wiki 页面提供了首选配方样式的大致指南。

It is common for existing recipes to deviate a bit from this style. However, aiming for at least a consistent style is a good idea. Some practices, such as omitting spaces around `=` operators in assignments or ordering recipe components in an erratic way, are widely seen as poor style.

> 现有的食谱与这种风格有点偏离是很常见的。然而，目标至少是一个一致的风格是一个好主意。一些做法，如在分配中省略“=”运算符周围的空格，或以不稳定的方式对配方组件进行排序，被广泛认为是糟糕的风格。

# Recipe Syntax

Understanding recipe file syntax is important for writing recipes. The following list overviews the basic items that make up a BitBake recipe file. For more complete BitBake syntax descriptions, see the \"`bitbake:bitbake-user-manual/bitbake-user-manual-metadata`{.interpreted-text role="doc"}\" chapter of the BitBake User Manual.

> 了解配方文件语法对于编写配方非常重要。以下列表概述了构成 BitBake 配方文件的基本项目。有关更完整的 BitBake 语法描述，请参阅《BitBake 用户手册》的\“`BitBake:BitBake-user manual/BitBake-user-manual metadata`｛.explored text role=“doc”｝\”一章。

- *Variable Assignments and Manipulations:* Variable assignments allow a value to be assigned to a variable. The assignment can be static text or might include the contents of other variables. In addition to the assignment, appending and prepending operations are also supported.

> -*变量赋值和操作：*变量赋值允许为变量赋值。赋值可以是静态文本，也可以包括其他变量的内容。除了赋值之外，还支持追加和预处理操作。

The following example shows some of the ways you can use variables in recipes:

> 以下示例显示了在配方中使用变量的一些方法：

```
S = "${WORKDIR}/postfix-${PV}"
CFLAGS += "-DNO_ASM"
CFLAGS:append = " --enable-important-feature"
```

- *Functions:* Functions provide a series of actions to be performed. You usually use functions to override the default implementation of a task function or to complement a default function (i.e. append or prepend to an existing function). Standard functions use `sh` shell syntax, although access to OpenEmbedded variables and internal methods are also available.

> -*功能：*功能提供了要执行的一系列操作。您通常使用函数来覆盖任务函数的默认实现，或补充默认函数（即附加或预附加到现有函数）。标准函数使用“sh”shell 语法，但也可以访问 OpenEmbedded 变量和内部方法。

Here is an example function from the `sed` recipe:

> 以下是“sed”配方中的一个示例函数：

```
do_install () {
    autotools_do_install
    install -d ${D}${base_bindir}
    mv ${D}${bindir}/sed ${D}${base_bindir}/sed
    rmdir ${D}${bindir}/
}
```

It is also possible to implement new functions that are called between existing tasks as long as the new functions are not replacing or complementing the default functions. You can implement functions in Python instead of shell. Both of these options are not seen in the majority of recipes.

> 也可以实现在现有任务之间调用的新功能，只要新功能不取代或补充默认功能即可。您可以用 Python 而不是 shell 实现函数。这两种选择都没有出现在大多数食谱中。

- *Keywords:* BitBake recipes use only a few keywords. You use keywords to include common functions (`inherit`), load parts of a recipe from other files (`include` and `require`) and export variables to the environment (`export`).

> -*关键词：*BitBake 食谱只使用几个关键词。使用关键字可以包括常见函数（“inherit”）、从其他文件加载配方的部分（“include”和“require”）以及将变量导出到环境（“export”）。

The following example shows the use of some of these keywords:

> 以下示例显示了其中一些关键字的使用：

```
export POSTCONF = "${STAGING_BINDIR}/postconf"
inherit autoconf
require otherfile.inc
```

- *Comments (#):* Any lines that begin with the hash character (`#`) are treated as comment lines and are ignored:

> -*注释（#）：*任何以哈希字符（`#`）开头的行都将被视为注释行并被忽略：

```
# This is a comment
```

This next list summarizes the most important and most commonly used parts of the recipe syntax. For more information on these parts of the syntax, you can reference the \"`bitbake:bitbake-user-manual/bitbake-user-manual-metadata`{.interpreted-text role="doc"}\" chapter in the BitBake User Manual.

> 下一个列表总结了配方语法中最重要和最常用的部分。有关这些语法部分的更多信息，您可以参考《bitbake 用户手册》中的\“`bitbake:bitbake-user manual/bitbake-user-manual metadata`｛.depreted text role=“doc”｝\”一章。

- *Line Continuation (\\):* Use the backward slash (`\`) character to split a statement over multiple lines. Place the slash character at the end of the line that is to be continued on the next line:

> -*换行符（\\）：*使用后斜杠（`\`）字符将语句拆分为多行。将斜线字符放在下一行要继续的行的末尾：

```
VAR = "A really long \
       line"
```

::: note
::: title

Note

> 笔记
> :::

You cannot have any characters including spaces or tabs after the slash character.

> 斜杠后面不能有任何字符，包括空格或制表符。
> :::

- *Using Variables (\${VARNAME}):* Use the `${VARNAME}` syntax to access the contents of a variable:

> -*使用变量（\$｛VARNAME｝）：*使用“$｛VAR NAME｝”语法访问变量的内容：

```
SRC_URI = "${SOURCEFORGE_MIRROR}/libpng/zlib-${PV}.tar.gz"
```

::: note
::: title

Note

> 笔记
> :::

It is important to understand that the value of a variable expressed in this form does not get substituted automatically. The expansion of these expressions happens on-demand later (e.g. usually when a function that makes reference to the variable executes). This behavior ensures that the values are most appropriate for the context in which they are finally used. On the rare occasion that you do need the variable expression to be expanded immediately, you can use the := operator instead of = when you make the assignment, but this is not generally needed.

> 重要的是要理解，以这种形式表示的变量的值不会自动被替换。这些表达式的扩展稍后按需进行（例如，通常在引用变量的函数执行时）。这种行为确保这些值最适合最终使用它们的上下文。在极少数情况下，您确实需要立即展开变量表达式，您可以在进行赋值时使用：=运算符而不是=，但通常不需要这样做。
> :::

- *Quote All Assignments (\"value\"):* Use double quotes around values in all variable assignments (e.g. `"value"`). Following is an example:

> -*引用所有赋值（\“value”）：*在所有变量赋值中的值周围使用双引号（例如“value”`）。以下是一个示例：

```
VAR1 = "${OTHERVAR}"
VAR2 = "The version is ${PV}"
```

- *Conditional Assignment (?=):* Conditional assignment is used to assign a value to a variable, but only when the variable is currently unset. Use the question mark followed by the equal sign (`?=`) to make a \"soft\" assignment used for conditional assignment. Typically, \"soft\" assignments are used in the `local.conf` file for variables that are allowed to come through from the external environment.

> -*条件赋值（？=）：*条件赋值用于为变量赋值，但仅当变量当前未设置时。使用问号和等号（`？=`）进行用于条件赋值的“软”赋值。通常，“soft”赋值在“local.conf”文件中用于允许来自外部环境的变量。

Here is an example where `VAR1` is set to \"New value\" if it is currently empty. However, if `VAR1` has already been set, it remains unchanged:

> 下面是一个示例，其中“VAR1”如果当前为空，则设置为“New value”。但是，如果已经设置了“VAR1”，它将保持不变：

```
VAR1 ?= "New value"
```

In this next example, `VAR1` is left with the value \"Original value\":

> 在下一个示例中，“VAR1”的值为“原始值”：

```
VAR1 = "Original value"
VAR1 ?= "New value"
```

- *Appending (+=):* Use the plus character followed by the equals sign (`+=`) to append values to existing variables.

> -*追加（+=）：*使用加号和等号（`+=`）将值追加到现有变量。

::: note
::: title

Note

> 笔记
> :::

This operator adds a space between the existing content of the variable and the new content.

> 此运算符在变量的现有内容和新内容之间添加一个空格。
> :::

Here is an example:

> 以下是一个示例：

```
SRC_URI += "file://fix-makefile.patch"
```

- *Prepending (=+):* Use the equals sign followed by the plus character (`=+`) to prepend values to existing variables.

> -*前置（=+）：*使用等号后跟加号（`=+`）将值前置到现有变量。

::: note
::: title

Note

> 笔记
> :::

This operator adds a space between the new content and the existing content of the variable.

> 此运算符在变量的新内容和现有内容之间添加一个空格。
> :::

Here is an example:

> 以下是一个示例：

```
VAR =+ "Starts"
```

- *Appending (:append):* Use the `:append` operator to append values to existing variables. This operator does not add any additional space. Also, the operator is applied after all the `+=`, and `=+` operators have been applied and after all `=` assignments have occurred. This means that if `:append` is used in a recipe, it can only be overridden by another layer using the special `:remove` operator, which in turn will prevent further layers from adding it back.

> -*追加（：append）：*使用“：append”运算符将值追加到现有变量。此运算符不添加任何额外的空间。此外，运算符是在应用了所有的“+=”和“=+”运算符之后以及在发生了所有“=”赋值之后应用的。这意味着，如果在配方中使用“：append”，则只能由使用特殊“：remove”运算符的另一层覆盖，这反过来会阻止更多层将其添加回来。

The following example shows the space being explicitly added to the start to ensure the appended value is not merged with the existing value:

> 以下示例显示了显式添加到开头的空格，以确保附加值不会与现有值合并：

```
CFLAGS:append = " --enable-important-feature"
```

You can also use the `:append` operator with overrides, which results in the actions only being performed for the specified target or machine:

> 您还可以将“：append”运算符与覆盖一起使用，这将导致仅对指定的目标或机器执行操作：

```
CFLAGS:append:sh4 = " --enable-important-sh4-specific-feature"
```

- *Prepending (:prepend):* Use the `:prepend` operator to prepend values to existing variables. This operator does not add any additional space. Also, the operator is applied after all the `+=`, and `=+` operators have been applied and after all `=` assignments have occurred.

> -*前置（：prepend）：*使用“：prepend”运算符将值前置到现有变量。此运算符不添加任何额外的空间。此外，运算符是在应用了所有的“+=”和“=+”运算符之后以及在发生了所有“=”赋值之后应用的。

The following example shows the space being explicitly added to the end to ensure the prepended value is not merged with the existing value:

> 以下示例显示了显式添加到末尾的空格，以确保前置值不会与现有值合并：

```
CFLAGS:prepend = "-I${S}/myincludes "
```

You can also use the `:prepend` operator with overrides, which results in the actions only being performed for the specified target or machine:

> 您还可以将“：prepend”运算符与覆盖一起使用，这将导致仅对指定的目标或机器执行操作：

```
CFLAGS:prepend:sh4 = "-I${S}/myincludes "
```

- *Overrides:* You can use overrides to set a value conditionally, typically based on how the recipe is being built. For example, to set the `KBRANCH`{.interpreted-text role="term"} variable\'s value to \"standard/base\" for any target `MACHINE`{.interpreted-text role="term"}, except for qemuarm where it should be set to \"standard/arm-versatile-926ejs\", you would do the following:

> -*覆盖：*您可以使用覆盖有条件地设置值，通常基于配方的构建方式。例如，要将任何目标 `MACHINE` 的 `KBRANCH`｛.explored text role=“term”｝变量\的值设置为\“standard/base\”，除了 qemuarm 应设置为\”standard/arm-versatile-926ejs\“之外，您可以执行以下操作：

```
KBRANCH = "standard/base"
KBRANCH:qemuarm = "standard/arm-versatile-926ejs"
```

Overrides are also used to separate alternate values of a variable in other situations. For example, when setting variables such as `FILES`{.interpreted-text role="term"} and `RDEPENDS`{.interpreted-text role="term"} that are specific to individual packages produced by a recipe, you should always use an override that specifies the name of the package.

> 在其他情况下，覆盖也用于分隔变量的备用值。例如，当设置特定于配方生产的单个包的变量，如 `FILES`｛.depreced text role=“term”｝和 `RDEPENDS`｛.epreced textrole=”term“｝时，应始终使用指定包名称的覆盖。

- *Indentation:* Use spaces for indentation rather than tabs. For shell functions, both currently work. However, it is a policy decision of the Yocto Project to use tabs in shell functions. Realize that some layers have a policy to use spaces for all indentation.

> -*缩进：*使用空格进行缩进，而不是使用制表符。对于 shell 函数，两者目前都可以工作。然而，在 shell 函数中使用选项卡是 Yocto 项目的政策决定。要意识到，有些层有一个策略，可以对所有缩进使用空格。

- *Using Python for Complex Operations:* For more advanced processing, it is possible to use Python code during variable assignments (e.g. search and replacement on a variable).

> -*使用 Python 进行复杂操作：*对于更高级的处理，可以在变量分配期间使用 Python 代码（例如，搜索和替换变量）。

You indicate Python code using the `${@python_code}` syntax for the variable assignment:

> 对于变量赋值，可以使用“$｛@Python_code｝”语法指示 Python 代码：

```
SRC_URI = "ftp://ftp.info-zip.org/pub/infozip/src/zip${@d.getVar('PV',1).replace('.', '')}.tgz
```

- *Shell Function Syntax:* Write shell functions as if you were writing a shell script when you describe a list of actions to take. You should ensure that your script works with a generic `sh` and that it does not require any `bash` or other shell-specific functionality. The same considerations apply to various system utilities (e.g. `sed`, `grep`, `awk`, and so forth) that you might wish to use. If in doubt, you should check with multiple implementations \-\-- including those from BusyBox.

> -*外壳函数语法：*在描述要执行的操作列表时，编写外壳函数就像编写外壳脚本一样。您应该确保您的脚本使用通用的“sh”，并且不需要任何“bash”或其他特定于 shell 的功能。同样的注意事项适用于您可能希望使用的各种系统实用程序（例如“sed”、“grep”、“awk”等）。如果有疑问，您应该检查多个实现\-包括 BusyBox 中的实现。
