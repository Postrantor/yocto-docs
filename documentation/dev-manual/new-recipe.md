---
tip: translate by openai@2023-06-10 11:17:14
...
---
title: Writing a New Recipe
---------------------------

Recipes (`.bb` files) are fundamental components in the Yocto Project environment. Each software component built by the OpenEmbedded build system requires a recipe to define the component. This section describes how to create, write, and test a new recipe.

> recipes(`.bb` 文件)是 Yocto 项目环境中的基本组件。由 OpenEmbedded 构建系统构建的每个软件组件都需要一个 recipes 来定义该组件。本节介绍如何创建、编写和测试一个新的 recipes。

::: note
::: title
Note
:::

For information on variables that are useful for recipes and for information about recipe naming issues, see the \"`ref-manual/varlocality:recipes`\" section of the Yocto Project Reference Manual.

> 要了解有用于 recipes 的变量以及有关 recipes 命名问题的信息，请参阅 Yocto Project 参考手册中的“ref-manual/varlocality:recipes”部分。
> :::

# Overview

The following figure shows the basic process for creating a new recipe. The remainder of the section provides details for the steps.

![image](figures/recipe-workflow.png)

# Locate or Automatically Create a Base Recipe

You can always write a recipe from scratch. However, there are three choices that can help you quickly get started with a new recipe:

- `devtool add`: A command that assists in creating a recipe and an environment conducive to development.
- `recipetool create`: A command provided by the Yocto Project that automates creation of a base recipe based on the source files.
- *Existing Recipes:* Location and modification of an existing recipe that is similar in function to the recipe you need.

::: note
::: title
Note
:::

For information on recipe syntax, see the \"`dev-manual/new-recipe:recipe syntax`\" section.
:::

## Creating the Base Recipe Using `devtool add`

The `devtool add` command uses the same logic for auto-creating the recipe as `recipetool create`, which is listed below. Additionally, however, `devtool add` sets up an environment that makes it easy for you to patch the source and to make changes to the recipe as is often necessary when adding a recipe to build a new piece of software to be included in a build.

> 命令 `devtool add` 使用与 `recipetool create` 相同的逻辑自动创建配方。此外，`devtool add` 还会设置一个环境，使您可以轻松地修补源代码，并对配方做出更改，这通常是添加一个配方以构建要包含在构建中的新软件所必需的。

You can find a complete description of the `devtool add` command in the \"``sdk-manual/extensible:a closer look at \`\`devtool add\`\` ``\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 你可以在 Yocto 项目应用开发和可扩展软件开发套件(eSDK)手册中的“sdk-manual/extensible：更深入了解“devtool add””部分找到关于“devtool add”命令的完整描述。

## Creating the Base Recipe Using `recipetool create`

`recipetool create` automates creation of a base recipe given a set of source code files. As long as you can extract or point to the source files, the tool will construct a recipe and automatically configure all pre-build information into the recipe. For example, suppose you have an application that builds using Autotools. Creating the base recipe using `recipetool` results in a recipe that has the pre-build dependencies, license requirements, and checksums configured.

> `recipetool create` 可以根据一组源代码文件自动创建基础配方。只要你能提取或指向源文件，该工具就可以构建配方并自动将所有预构建信息配置到配方中。例如，假设你有一个使用 Autotools 构建的应用程序。使用 `recipetool` 创建基础配方将会得到一个配方，其中包含预构建依赖关系、许可要求和校验和已经配置完成。

To run the tool, you just need to be in your `Build Directory`). To get help on the tool, use the following command:

> 要运行这个工具，你只需要在你的“构建目录”中，并且已经源自构建环境设置脚本(即 `structure-core-script`)。要获取关于该工具的帮助，请使用以下命令：

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

> 运行 `recipetool create -o OUTFILE` 可以创建基本配方，并将其正确定位到包含源文件的层中。以下是一些语法示例：

> - Use this syntax to generate a recipe based on source. Once generated, the recipe resides in the existing source code layer:
>
>   ```
>   recipetool create -o OUTFILE source
>   ```
> - Use this syntax to generate a recipe using code that you extract from source. The extracted code is placed in its own layer defined by `EXTERNALSRC`:
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

Before writing a recipe from scratch, it is often useful to discover whether someone else has already written one that meets (or comes close to meeting) your needs. The Yocto Project and OpenEmbedded communities maintain many recipes that might be candidates for what you are doing. You can find a good central index of these recipes in the :oe_layerindex:[OpenEmbedded Layer Index \<\>].

> 在从头开始编写 recipes 之前，通常有用的是发现是否有其他人已经写了一个满足(或接近满足)您的需求的 recipes。Yocto 项目和 OpenEmbedded 社区维护了许多可能是您正在做的候选 recipes。您可以在 oe_layerindex：[OpenEmbedded Layer Index \<\>]中找到一个很好的中央索引。

Working from an existing recipe or a skeleton recipe is the best way to get started. Here are some points on both methods:

- *Locate and modify a recipe that is close to what you want to do:* This method works when you are familiar with the current recipe space. The method does not work so well for those new to the Yocto Project or writing recipes.

> 找到并修改接近你想要做的 recipes：当你熟悉当前 recipes 空间时，这种方法就可行。对于那些初次接触 Yocto 项目或编写 recipes 的人来说，这种方法就不太好用了。

Some risks associated with this method are using a recipe that has areas totally unrelated to what you are trying to accomplish with your recipe, not recognizing areas of the recipe that you might have to add from scratch, and so forth. All these risks stem from unfamiliarity with the existing recipe space.

> 一些与此方法相关的风险包括使用一个与你试图通过你的 recipes 实现的目标完全无关的 recipes，无法识别可能需要从头开始添加的 recipes 部分等等。所有这些风险源于对现有 recipes 空间的不熟悉。

- *Use and modify the following skeleton recipe:* If for some reason you do not want to use `recipetool` and you cannot find an existing recipe that is close to meeting your needs, you can use the following structure to provide the fundamental areas of a new recipe:

> 如果您出于某种原因不想使用 `recipetool`，并且找不到满足您需求的现有 recipes，您可以使用以下结构提供新 recipes 的基本领域：

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

> 一旦你有了基础配方，你应该把它放在自己的层里，并给它恰当的名字。正确定位它可以确保当你用 BitBake 处理配方时，OpenEmbedded 构建系统能够找到它。

- *Storing Your Recipe:* The OpenEmbedded build system locates your recipe through the layer\'s `conf/layer.conf` file and the `BBFILES` variable. This variable sets up a path from which the build system can locate recipes. Here is the typical use:

> - *存储您的配方：*OpenEmbedded 构建系统通过层的 `conf / layer.conf` 文件和 `BBFILES` 变量来定位您的配方。此变量设置了构建系统可以定位配方的路径。以下是典型用法：

```
BBFILES += "$/recipes-*/*/*.bb \
            $/recipes-*/*/*.bbappend"
```

Consequently, you need to be sure you locate your new recipe inside your layer such that it can be found.

You can find more information on how layers are structured in the \"`dev-manual/layers:understanding and creating layers`\" section.

> 你可以在“dev-manual/layers：理解和创建层”部分找到更多有关层结构的信息。

- *Naming Your Recipe:* When you name your recipe, you need to follow this naming convention:

  ```
  basename_version.bb
  ```

  Use lower-cased characters and do not include the reserved suffixes `-native`, `-cross`, `-initial`, or `-dev` casually (i.e. do not use them as part of your recipe name unless the string applies). Here are some examples:

> 使用小写字符，不要随意使用保留的后缀“-native”、“-cross”、“-initial”或“-dev”(即，除非字符串适用，否则不要将它们用作你的配方名称)。下面是一些示例：
> 使用简体中文。

```none
cups_1.7.0.bb
gawk_4.0.2.bb
irssi_0.8.16-rc1.bb
```

# Running a Build on the Recipe

Creating a new recipe is usually an iterative process that requires using BitBake to process the recipe multiple times in order to progressively discover and add information to the recipe file.

> 创建一个新的 recipes 通常是一个迭代过程，需要使用 BitBake 多次处理 recipes，以逐步发现和添加信息到 recipes 文件中。

Assuming you have sourced the build environment setup script (i.e. `structure-core-script`, use BitBake to process your recipe. All you need to provide is the `basename` of the recipe as described in the previous section:

> 假设你已经源自构建环境设置脚本(即 `structure-core-script`)，并且你位于 `Build Directory`，使用 BitBake 来处理你的配方。你所需要提供的是前一节描述的配方的 `basename`：

```
$ bitbake basename
```

During the build, the OpenEmbedded build system creates a temporary work directory for each recipe (`$`) where it keeps extracted source files, log files, intermediate compilation and packaging files, and so forth.

> 在构建期间，OpenEmbedded 构建系统为每个配方(`$`)创建一个临时工作目录，其中保存提取的源文件、日志文件、中间编译和打包文件等等。

The path to the per-recipe temporary work directory depends on the context in which it is being built. The quickest way to find this path is to have BitBake return it by running the following:

> 这个每个配方临时工作目录的路径取决于它正在构建的上下文。最快的方法是通过运行以下内容让 BitBake 返回它：

```
$ bitbake -e basename | grep ^WORKDIR=
```

As an example, assume a Source Directory top-level folder named `poky`, a default `Build Directory` at `poky/build`, and a `qemux86-poky-linux` machine target system. Furthermore, suppose your recipe is named `foo_1.3.0.bb`. In this case, the work directory the build system uses to build the package would be as follows:

> 假设一个名为 `poky` 的源目录顶级文件夹，默认的 `构建目录` 为 `poky/build`，以及 `qemux86-poky-linux` 机器目标系统。此外，假设您的配方名为 `foo_1.3.0.bb`。在这种情况下，构建系统用于构建软件包的工作目录如下：

```
poky/build/tmp/work/qemux86-poky-linux/foo/1.3.0-r0
```

Inside this directory you can find sub-directories such as `image`, `packages-split`, and `temp`. After the build, you can examine these to determine how well the build went.

> 在这个目录中，您可以找到诸如“image”，“packages-split”和“temp”等子目录。构建完成后，您可以检查这些内容以确定构建的效果如何。

::: note
::: title
Note
:::

You can find log files for each task in the recipe\'s `temp` directory (e.g. `poky/build/tmp/work/qemux86-poky-linux/foo/1.3.0-r0/temp`). Log files are named `log.taskname` (e.g. `log.do_configure`, `log.do_fetch`, and `log.do_compile`).

> 你可以在 recipes 的 `temp` 目录中找到每个任务的日志文件(例如 `poky/build/tmp/work/qemux86-poky-linux/foo/1.3.0-r0/temp`)。日志文件的命名格式为 `log.taskname`(例如 `log.do_configure`、`log.do_fetch` 和 `log.do_compile`)。
> :::

You can find more information about the build process in \"`/overview-manual/development-environment`\" chapter of the Yocto Project Overview and Concepts Manual.

> 你可以在 Yocto 项目概览与概念手册的"/overview-manual/development-environment"章节中找到更多关于构建过程的信息。

# Fetching Code

The first thing your recipe must do is specify how to fetch the source files. Fetching is controlled mainly through the `SRC_URI`\" section in the Yocto Project Overview and Concepts Manual.

> 首先，你的 recipes 必须指定如何获取源文件。获取主要通过 `SRC_URI` 变量控制。你的 recipes 必须有一个 `SRC_URI` 变量，指向源位置。有关源位置的图形表示，请参见 Yocto 项目概述和概念手册中的“概览-手册/概念：源”部分。

The `ref-tasks-fetch`.

> 任务 `ref-tasks-fetch` 中的本地文件所在的目录位置。

The `SRC_URI``, which causes the fetch process to use the version specified in the recipe filename. Specifying the version in this manner means that upgrading the recipe to a future version is as simple as renaming the recipe to match the new version.

> `SRC_URI``，这会导致获取过程使用配方文件名中指定的版本。以这种方式指定版本意味着将配方升级到未来版本只需将配方重命名为与新版本匹配即可。

Here is a simple example from the `meta/recipes-devtools/strace/strace_5.5.bb` recipe where the source comes from a single tarball. Notice the use of the `PV` variable:

> 这里是来自 `meta/recipes-devtools/strace/strace_5.5.bb` recipes 的简单示例，源文件来自单个 tarball。注意使用 `PV` 变量：

```
SRC_URI = "https://strace.io/files/$.tar.xz \
```

Files mentioned in `SRC_URI`\" section.

> 文件名以典型的归档扩展名(例如 `.tar`、`.tar.gz`、`.tar.bz2`、`.zip` 等)结尾的文件，会在 `ref-tasks-unpack` 任务期间被自动提取。要查看另一个指定这些类型文件的示例，请参阅“dev-manual/new-recipe:building an autotooled package”部分。

Another way of specifying source is from an SCM. For Git repositories, you must specify `SRCREV`. Here is an example from the recipe `meta/recipes-core/musl/gcompat_git.bb`:

> 另一种指定源的方式是来自 SCM。对于 Git 存储库，您必须指定 SRCREV，并且应该指定 PV 以包括 SRCPV 中的修订。以下是来自 recipes meta / recipes-core / musl / gcompat_git.bb 的示例：

```
SRC_URI = "git://git.adelielinux.org/adelie/gcompat.git;protocol=https;branch=current"

PV = "1.0.0+1.1+git$"
SRCREV = "af5a49e489fdc04b9cf02547650d7aeaccd43793"
```

If your `SRC_URI` statement includes URLs pointing to individual files fetched from a remote server other than a version control system, BitBake attempts to verify the files against checksums defined in your recipe to ensure they have not been tampered with or otherwise modified since the recipe was written. Two checksums are used: `SRC_URI[md5sum]` and `SRC_URI[sha256sum]`.

> 如果您的 `SRC_URI` 声明包含指向从远程服务器获取的单个文件的 URL，而不是版本控制系统，BitBake 将尝试验证这些文件与配方中定义的校验和，以确保自配方编写以来它们没有被篡改或以其他方式修改。使用两个校验和：`SRC_URI[md5sum]` 和 `SRC_URI[sha256sum]`。

If your `SRC_URI` and then reference that name in the subsequent checksum statements. Here is an example combining lines from the files `git.inc` and `git_2.24.1.bb`:

> 如果你的 `SRC_URI` 变量指向多个 URL(不包括 SCM URLs)，你需要为每个 URL 提供 `md5` 和 `sha256` 校验和。在这些情况下，您需要在 `SRC_URI` 中为每个 URL 提供一个名称，然后在随后的校验和语句中引用该名称。以下是结合 `git.inc` 和 `git_2.24.1.bb` 文件行的示例：

```
SRC_URI = "$.tar.gz;name=tarball \
           $.tar.gz;name=manpages"

SRC_URI[tarball.md5sum] = "166bde96adbbc11c8843d4f8f4f9811b"
SRC_URI[tarball.sha256sum] = "ad5334956301c86841eb1e5b1bb20884a6bad89a10a6762c958220c7cf64da02"
SRC_URI[manpages.md5sum] = "31c2272a8979022497ba3d4202df145d"
SRC_URI[manpages.sha256sum] = "9a7ae3a093bea39770eb96ca3e5b40bff7af0b9f6123f089d7821d0e5b8e1230"
```

Proper values for `md5` and `sha256` checksums might be available with other signatures on the download page for the upstream source (e.g. `md5`, `sha1`, `sha256`, `GPG`, and so forth). Because the OpenEmbedded build system only deals with `sha256sum` and `md5sum`, you should verify all the signatures you find by hand.

> 下载源的下载页面可能会提供适当的 `md5` 和 `sha256` 校验和(例如 `md5`、`sha1`、`sha256`、`GPG` 等)。由于 OpenEmbedded 构建系统只处理 `sha256sum` 和 `md5sum`，您应该手动验证所有找到的签名。

If no `SRC_URI` checksums are specified when you attempt to build the recipe, or you provide an incorrect checksum, the build will produce an error for each missing or incorrect checksum. As part of the error message, the build system provides the checksum string corresponding to the fetched file. Once you have the correct checksums, you can copy and paste them into your recipe and then run the build again to continue.

> 如果您尝试构建配方时没有指定 `SRC_URI` 校验和，或者提供了不正确的校验和，则构建将为每个缺失或不正确的校验和生成错误。作为错误消息的一部分，构建系统提供与获取的文件对应的校验和字符串。一旦您获得正确的校验和，您就可以将它们复制粘贴到您的配方中，然后再次运行构建以继续。

::: note
::: title
Note
:::

As mentioned, if the upstream source provides signatures for verifying the downloaded source code, you should verify those manually before setting the checksum values in the recipe and continuing with the build.

> 如所提及，如果上游源提供签名来验证下载的源代码，在设置配方中的校验和值并继续构建之前，您应该手动验证这些签名。
> :::

This final example is a bit more complicated and is from the `meta/recipes-sato/rxvt-unicode/rxvt-unicode_9.20.bb` recipe. The example\'s `SRC_URI` statement identifies multiple files as the source files for the recipe: a tarball, a patch file, a desktop file, and an icon:

> 这最后一个示例更加复杂，来自 `meta/recipes-sato/rxvt-unicode/rxvt-unicode_9.20.bb` 配方。示例中的 `SRC_URI` 语句将多个文件标识为配方的源文件：一个 tarball、一个补丁文件、一个桌面文件和一个图标。

```
SRC_URI = "http://dist.schmorp.de/rxvt-unicode/Attic/rxvt-unicode-$.tar.bz2 \
           file://xwc.patch \
           file://rxvt.desktop \
           file://rxvt.png"
```

When you specify local files using the `file://` URI protocol, the build system fetches files from the local machine. The path is relative to the `FILESPATH``, and ` files`. The directories are assumed to be subdirectories of the directory in which the recipe or append file resides. For another example that specifies these types of files, see the \"[building a single .c file package](#building-a-single-.c-file-package)\" section.

> 当您使用 `file://` URI 协议指定本地文件时，构建系统会从本机获取文件。路径是相对于 `FILESPATH`` 和 ` files`。这些目录被假定为菜谱或附加文件所在目录的子目录。有关指定这些类型文件的另一个示例，请参见“[构建单个.c 文件包](#building-a-single-.c-file-package)”部分。

The previous example also specifies a patch file. Patch files are files whose names usually end in `.patch` or `.diff` but can end with compressed suffixes such as `diff.gz` and `patch.bz2`, for example. The build system automatically applies patches as described in the \"`dev-manual/new-recipe:patching code`\" section.

> 上一个示例也指定了一个补丁文件。补丁文件的文件名通常以 `.patch` 或 `.diff` 结尾，但也可以以压缩后缀，如 `diff.gz` 和 `patch.bz2` 结尾，例如。构建系统会按照“dev-manual/new-recipe：patching code”部分中的说明自动应用补丁。

## Fetching Code Through Firewalls

Some users are behind firewalls and need to fetch code through a proxy. See the \"`/ref-manual/faq`\" chapter for advice.

> 一些用户被防火墙拦截，需要通过代理服务器获取代码。请参考“/ref-manual/faq”章节获取建议。

## Limiting the Number of Parallel Connections

Some users are behind firewalls or use servers where the number of parallel connections is limited. In such cases, you can limit the number of fetch tasks being run in parallel by adding the following to your `local.conf` file:

> 一些用户位于防火墙后面或使用其中并行连接数量有限的服务器。在这种情况下，您可以通过将以下内容添加到您的 `local.conf` 文件来限制并行运行的获取任务的数量：

```
do_fetch[number_threads] = "4"
```

# Unpacking Code

During the build, the `ref-tasks-unpack`` pointing to where it is unpacked.

> 在构建过程中，`ref-tasks-unpack`` 指向解压缩的源代码。

If you are fetching your source files from an upstream source archived tarball and the tarball\'s internal structure matches the common convention of a top-level subdirectory named `$.

> 如果你从上游源的归档 tarball 中提取源文件，并且 tarball 的内部结构符合顶级子目录命名为 `$` 的共同惯例，那么你不需要设置 S。但是，如果 SRC_URI 指定从不使用此惯例的归档文件或像 Git 或 Subversion 这样的 SCM 中获取源文件，则你的配方需要定义 S。

If processing your recipe using BitBake successfully unpacks the source files, you need to be sure that the directory pointed to by `$` matches the structure of the source.

> 如果使用 BitBake 成功处理您的配方，解压源文件后，您需要确保由 `$` 指向的目录与源文件的结构匹配。

# Patching Code

Sometimes it is necessary to patch code after it has been fetched. Any files mentioned in `SRC_URI` task automatically applies these patches.

> 有时需要在获取代码后补丁。任何在 `SRC_URI` 任务会自动应用这些补丁。

The build system should be able to apply patches with the \"-p1\" option (i.e. one directory level in the path will be stripped off). If your patch needs to have more directory levels stripped off, specify the number of levels using the \"striplevel\" option in the `SRC_URI` entry for the patch. Alternatively, if your patch needs to be applied in a specific subdirectory that is not specified in the patch file, use the \"patchdir\" option in the entry.

> 系统构建应能够使用“-p1”选项应用补丁(即在路径中将一个目录级别剥离)。如果您的补丁需要剥离更多的目录级别，请在补丁的 `SRC_URI` 条目中使用“striplevel”选项指定级别数。或者，如果您的补丁需要在补丁文件中未指定的特定子目录中应用，请在条目中使用“patchdir”选项。

As with all local files referenced in `SRC_URI`) or \"files\".

> 对于使用 `file://` 引用的 `SRC_URI` 中的所有本地文件，您应该将补丁文件放置在与配方旁边的目录中，该目录的名称与配方的基本名称(`BP` 和 `BPN`)相同，或者为“files”。

# Licensing

Your recipe needs to have both the `LICENSE` variables:

- `LICENSE` as follows:

> `LICENSE`：

```
LICENSE = "GPL-2.0-only"
```

The licenses you specify within `LICENSE` flag names defined in `meta/conf/licenses.conf`.

> 您在 `LICENSE` 标志名称。

- `LIC_FILES_CHKSUM`: The OpenEmbedded build system uses this variable to make sure the license text has not changed. If it has, the build produces an error and it affords you the chance to figure it out and correct the problem.

> `LIC_FILES_CHKSUM`：OpenEmbedded 构建系统使用此变量来确保许可文本没有发生变化。如果有，构建将产生错误，这将给您提供机会来弄清楚并解决问题。

You need to specify all applicable licensing files for the software. At the end of the configuration step, the build process will compare the checksums of the files to be sure the text has not changed. Any differences result in an error with the message containing the current checksum. For more explanation and examples of how to set the `LIC_FILES_CHKSUM`\" section.

> 你需要为软件指定所有适用的许可文件。在配置步骤结束时，构建过程将比较文件的校验和，以确保文本没有更改。任何差异都会导致错误，消息中包含当前的校验和。有关如何设置 `LIC_FILES_CHKSUM`”部分。

To determine the correct checksum string, you can list the appropriate files in the `LIC_FILES_CHKSUM`\" section for additional information.

> 要确定正确的校验和字符串，您可以使用不正确的 md5 字符串在 `LIC_FILES_CHKSUM` 变量中列出相应的文件，尝试构建软件，然后注意报告正确的 md5 字符串的结果错误消息。有关详细信息，请参阅“dev-manual/new-recipe：获取代码”部分。

Here is an example that assumes the software has a `COPYING` file:

```
LIC_FILES_CHKSUM = "file://COPYING;md5=xxx"
```

When you try to build the software, the build system will produce an error and give you the correct string that you can substitute into the recipe file for a subsequent build.

> 当你尝试构建软件时，构建系统会产生一个错误，并给出一个正确的字符串，你可以将它替换到配方文件中，以便进行后续构建。

# Dependencies

Most software packages have a short list of other packages that they require, which are called dependencies. These dependencies fall into two main categories: build-time dependencies, which are required when the software is built; and runtime dependencies, which are required to be installed on the target in order for the software to run.

> 大多数软件包都有一个简短的其他软件包列表，这些软件包被称为依赖项。这些依赖项分为两个主要类别：构建时依赖项，在构建软件时需要；以及运行时依赖项，为了使软件正常运行，需要在目标上安装运行时依赖项。

Within a recipe, you specify build-time dependencies using the `DEPENDS` should be names of other recipes. It is important that you specify all build-time dependencies explicitly.

> 在配方中，您可以使用 `DEPENDS` 变量指定构建时的依赖关系。尽管存在微妙之处，但 `DEPENDS` 中指定的项目应该是其他配方的名称。重要的是，您应该明确指定所有的构建时依赖关系。

Another consideration is that configure scripts might automatically check for optional dependencies and enable corresponding functionality if those dependencies are found. If you wish to make a recipe that is more generally useful (e.g. publish the recipe in a layer for others to use), instead of hard-disabling the functionality, you can use the `PACKAGECONFIG` variable to allow functionality and the corresponding dependencies to be enabled and disabled easily by other users of the recipe.

> 另一个考虑是，配置脚本可能会自动检查可选依赖项并在发现这些依赖项时启用相应的功能。如果您希望制作一个更通用的 recipes(例如，在层中发布 recipes 供其他人使用)，而不是强制禁用功能，您可以使用 `PACKAGECONFIG` 变量，以便其他 recipes 用户可以轻松地启用和禁用功能及其相应的依赖项。

Similar to build-time dependencies, you specify runtime dependencies through a variable -`RDEPENDS`-tools`, and so forth.

> 相似于构建时依赖，您可以通过变量 `RDEPENDS`-tools`，等等。

Some runtime dependencies will be set automatically at packaging time. These dependencies include any shared library dependencies (i.e. if a package \"example\" contains \"libexample\" and another package \"mypackage\" contains a binary that links to \"libexample\" then the OpenEmbedded build system will automatically add a runtime dependency to \"mypackage\" on \"example\"). See the \"`overview-manual/concepts:automatically added runtime dependencies`\" section in the Yocto Project Overview and Concepts Manual for further details.

> 在打包时，一些运行时依赖关系将自动设置。这些依赖关系包括任何共享库依赖关系(即，如果包“example”包含“libexample”，而另一个包“mypackage”包含链接到“libexample”的二进制文件，那么 OpenEmbedded 构建系统将自动向“mypackage”添加对“example”的运行时依赖关系)。有关更多详细信息，请参阅 Yocto 项目概览和概念手册中的“概览-手册/概念：自动添加的运行时依赖关系”部分。

# Configuring the Recipe

Most software provides some means of setting build-time configuration options before compilation. Typically, setting these options is accomplished by running a configure script with options, or by modifying a build configuration file.

> 大多数软件在编译之前提供一些设置构建时间配置选项的方法。通常，设置这些选项是通过运行带有选项的配置脚本或修改构建配置文件来实现的。

::: note
::: title
Note
:::

As of Yocto Project Release 1.7, some of the core recipes that package binary configuration scripts now disable the scripts due to the scripts previously requiring error-prone path substitution. The OpenEmbedded build system uses `pkg-config` now, which is much more robust. You can find a list of the `*-config` scripts that are disabled in the \"`migration-1.7-binary-configuration-scripts-disabled`\" section in the Yocto Project Reference Manual.

> 随着 Yocto Project Release 1.7 的发布，由于之前需要容易出错的路径替换，一些核心配方现在禁用了二进制配置脚本。OpenEmbedded 构建系统现在使用 `pkg-config`，这要稳定得多。您可以在 Yocto Project 参考手册中的“migration-1.7-binary-configuration-scripts-disabled”部分找到被禁用的“*-config”脚本的列表。
> :::

A major part of build-time configuration is about checking for build-time dependencies and possibly enabling optional functionality as a result. You need to specify any build-time dependencies for the software you are building in your recipe\'s `DEPENDS` value, in terms of other recipes that satisfy those dependencies. You can often find build-time or runtime dependencies described in the software\'s documentation.

> 主要的构建时间配置是关于检查构建时间依赖性，并可能根据此结果启用可选功能。您需要在配方的 `DEPENDS` 值中指定构建的软件的任何构建时间依赖性，以其他满足这些依赖性的配方为基础。您通常可以在软件的文档中找到构建时间或运行时依赖性。

The following list provides configuration items of note based on how your software is built:

- *Autotools:* If your source files have a `configure.ac` file, then your software is built using Autotools. If this is the case, you just need to modify the configuration.

> 如果您的源文件有一个 `configure.ac` 文件，那么您的软件将使用自动工具构建。如果是这种情况，您只需要修改配置即可。

When using Autotools, your recipe needs to inherit the `ref-classes-autotools` to pass any needed configure options that are specific to the recipe.

> 当使用自动工具时，您的配方需要继承 `ref-classes-autotools` 以传递任何特定于配方的需要的配置选项。

- *CMake:* If your source files have a `CMakeLists.txt` file, then your software is built using CMake. If this is the case, you just need to modify the configuration.

> 如果您的源文件有一个 `CMakeLists.txt` 文件，那么您的软件就是使用 CMake 构建的。如果是这种情况，您只需要修改配置即可。

When you use CMake, your recipe needs to inherit the `ref-classes-cmake` to pass any needed configure options that are specific to the recipe.

> 当你使用 CMake 时，你的配方需要继承 `ref-classes-cmake` 来进行一些调整，以传递特定于配方的任何所需的配置选项。

::: note
::: title
Note
:::

If you need to install one or more custom CMake toolchain files that are supplied by the application you are building, install the files to `$.

> 如果您需要安装由构建的应用程序提供的一个或多个自定义 CMake 工具链文件，请在 `ref-tasks-install` 期间将文件安装到 `$/cmake/Modules`。
> :::

- *Other:* If your source files do not have a `configure.ac` or `CMakeLists.txt` file, then your software is built using some method other than Autotools or CMake. If this is the case, you normally need to provide a `ref-tasks-configure` task in your recipe unless, of course, there is nothing to configure.

> 如果您的源文件没有 `configure.ac` 或 `CMakeLists.txt` 文件，那么您的软件是使用除 Autotools 或 CMake 以外的其他方法构建的。如果是这种情况，您通常需要在配方中提供一个 `ref-tasks-configure` 任务，当然，如果没有配置，也不需要。

Even if your software is not being built by Autotools or CMake, you still might not need to deal with any configuration issues. You need to determine if configuration is even a required step. You might need to modify a Makefile or some configuration file used for the build to specify necessary build options. Or, perhaps you might need to run a provided, custom configure script with the appropriate options.

> 即使您的软件不是由 Autotools 或 CMake 构建的，您仍然可能不需要处理任何配置问题。您需要确定是否需要配置步骤。您可能需要修改用于构建的 Makefile 或某些配置文件来指定必要的构建选项。或者，也许您需要使用适当的选项运行提供的自定义配置脚本。

For the case involving a custom configure script, you would run `./configure --help` and look for the options you need to set.

Once configuration succeeds, it is always good practice to look at the `log.do_configure` file to ensure that the appropriate options have been enabled and no additional build-time dependencies need to be added to `DEPENDS`` or consult the software\'s upstream documentation.

> 一旦配置成功，最好查看 `log.do_configure` 文件以确保已启用适当的选项，并且不需要在 `DEPENDS` 中添加其他构建时依赖项。例如，如果配置脚本报告发现了 `DEPENDS` 中没有提及的东西，或者没有找到某些必需的可选功能，则需要将它们添加到 `DEPENDS` 中。查看日志也可能会发现您不想要的正在检查、启用或两者都有的项目，或者没有发现 `DEPENDS` 中的项目，在这种情况下，您需要按照需要向配置脚本传递额外的选项。要查阅与构建的软件相关的配置选项参考信息，可以查看 `$` 中的 `./configure --help` 命令的输出，或参考软件的上游文档。

# Using Headers to Interface with Devices

If your recipe builds an application that needs to communicate with some device or needs an API into a custom kernel, you will need to provide appropriate header files. Under no circumstances should you ever modify the existing `meta/recipes-kernel/linux-libc-headers/linux-libc-headers.inc` file. These headers are used to build `libc` and must not be compromised with custom or machine-specific header information. If you customize `libc` through modified headers all other applications that use `libc` thus become affected.

> 如果您的配方构建的应用程序需要与某个设备通信或需要自定义内核的 API，您将需要提供适当的头文件。在任何情况下，您都不应该修改现有的 `meta/recipes-kernel/linux-libc-headers/linux-libc-headers.inc` 文件。这些头文件用于构建 `libc`，不得受到定制或特定机器的头文件信息的影响。如果您通过修改的头文件来定制 `libc`，则所有使用 `libc` 的其他应用程序都会受到影响。

::: note
::: title
Note
:::

Never copy and customize the `libc` header file (i.e. `meta/recipes-kernel/linux-libc-headers/linux-libc-headers.inc`).
:::

The correct way to interface to a device or custom kernel is to use a separate package that provides the additional headers for the driver or other unique interfaces. When doing so, your application also becomes responsible for creating a dependency on that specific provider.

> 正确的方式来与设备或自定义内核进行交互是使用一个单独的包来提供驱动程序或其他独特接口的额外头文件。这样做时，您的应用程序也负责创建对特定提供者的依赖关系。

Consider the following:

- Never modify `linux-libc-headers.inc`. Consider that file to be part of the `libc` system, and not something you use to access the kernel directly. You should access `libc` through specific `libc` calls.

> 不要修改 linux-libc-headers.inc。把它当作是 libc 系统的一部分，而不是直接访问内核的东西。你应该通过特定的 libc 调用来访问 libc。

- Applications that must talk directly to devices should either provide necessary headers themselves, or establish a dependency on a special headers package that is specific to that driver.

> 应用程序必须直接与设备通信时，要么提供必要的头文件，要么建立对特定驱动程序的特殊头文件包的依赖关系。

For example, suppose you want to modify an existing header that adds I/O control or network support. If the modifications are used by a small number programs, providing a unique version of a header is easy and has little impact. When doing so, bear in mind the guidelines in the previous list.

> 例如，假设您想修改现有的头文件以添加 I/O 控制或网络支持。如果该修改被少量程序使用，则提供唯一版本的头文件很容易，影响也很小。在这样做时，请记住前面列出的准则。

::: note
::: title
Note
:::

If for some reason your changes need to modify the behavior of the `libc`, and subsequently all other applications on the system, use a `.bbappend` to modify the `linux-kernel-headers.inc` file. However, take care to not make the changes machine specific.

> 如果由于某种原因，您的更改需要修改 `libc` 的行为，并随后修改系统上的所有其他应用程序，请使用 `bbappend` 来修改 `linux-kernel-headers.inc` 文件。但是，要小心不要使更改特定于计算机。
> :::

Consider a case where your kernel is older and you need an older `libc` ABI. The headers installed by your recipe should still be a standard mainline kernel, not your own custom one.

> 考虑一种情况，您的内核较旧，需要较旧的 `libc` ABI。您的配方安装的头文件仍应是标准的主线内核，而不是您自己的自定义内核。

When you use custom kernel headers you need to get them from `STAGING_KERNEL_DIR`, which is the directory with kernel headers that are required to build out-of-tree modules. Your recipe will also need the following:

> 当你使用自定义内核头文件时，你需要从 `STAGING_KERNEL_DIR` 获取它们，这是一个需要构建树外模块所需的内核头文件目录。你的配方还需要以下内容：

```
do_configure[depends] += "virtual/kernel:do_shared_workdir"
```

# Compilation

During a build, the `ref-tasks-compile` successfully, nothing needs to be done.

> 在构建过程中，`ref-tasks-compile`，则无需做任何事情。

However, if the compile step fails, you need to diagnose the failure. Here are some common issues that cause failures.

::: note
::: title
Note
:::

For cases where improper paths are detected for configuration files or for when libraries/headers cannot be found, be sure you are using the more robust `pkg-config`. See the note in section \"`dev-manual/new-recipe:Configuring the Recipe`\" for additional information.

> 对于检测到配置文件的不正确路径或无法找到库/头文件的情况，请务必使用更强大的 `pkg-config`。有关更多信息，请参阅“dev-manual/new-recipe：配置配方”部分中的注释。
> :::

- *Parallel build failures:* These failures manifest themselves as intermittent errors, or errors reporting that a file or directory that should be created by some other part of the build process could not be found. This type of failure can occur even if, upon inspection, the file or directory does exist after the build has failed, because that part of the build process happened in the wrong order.

> 并行构建失败：这些失败表现为间歇性错误，或错误报告某个由构建过程的其他部分创建的文件或目录找不到。即使构建失败后检查发现该文件或目录确实存在，也会发生此类故障，因为构建过程的那部分发生在错误的顺序中。

To fix the problem, you need to either satisfy the missing dependency in the Makefile or whatever script produced the Makefile, or (as a workaround) set `PARALLEL_MAKE` to an empty string:

> 解决这个问题，你需要满足 Makefile 或者其他产生 Makefile 脚本的缺失依赖，或者(作为一个替代方案)将 `PARALLEL_MAKE` 设置为空字符串：

```
PARALLEL_MAKE = ""
```

For information on parallel Makefile issues, see the \"`dev-manual/debugging:debugging parallel make races`\" section.

> 有关并行 Makefile 问题的信息，请参见“dev-manual / debugging：调试并行 make races”部分。

- *Improper host path usage:* This failure applies to recipes building for the target or \"`ref-classes-nativesdk`\" only. The failure occurs when the compilation process uses improper headers, libraries, or other files from the host system when cross-compiling for the target.

> 不正确的主机路径使用：此故障仅适用于为目标或“ref-classes-nativesdk”构建的配方。当为目标进行交叉编译时，如果编译过程使用不正确的头文件、库或其他文件来自主机系统，则会发生故障。

To fix the problem, examine the `log.do_compile` file to identify the host paths being used (e.g. `/usr/include`, `/usr/lib`, and so forth) and then either add configure options, apply a patch, or do both.

> 要解决问题，检查 `log.do_compile` 文件以识别正在使用的主机路径(例如 `/usr/include`，`/usr/lib` 等)，然后添加配置选项，应用补丁或两者都做。

- *Failure to find required libraries/headers:* If a build-time dependency is missing because it has not been declared in `DEPENDS`.

> 如果缺少构建时依赖性，因为它未在 `DEPENDS` 中声明，或者依赖性存在但构建过程使用的路径不正确，而配置步骤未检测到它，则编译过程可能会失败。对于这两种失败，编译过程会指出无法找到文件。在这些情况下，您需要回去添加额外的选项到配置脚本，以及可能添加额外的构建时依赖性到 `DEPENDS`。

Occasionally, it is necessary to apply a patch to the source to ensure the correct paths are used. If you need to specify paths to find files staged into the sysroot from other recipes, use the variables that the OpenEmbedded build system provides (e.g. `STAGING_BINDIR`, and so forth).

> 偶尔，需要应用补丁到源代码以确保使用正确的路径。如果需要指定路径来查找从其他配方阶段到 sysroot 的文件，请使用 OpenEmbedded 构建系统提供的变量(例如 `STAGING_BINDIR` 等)。

# Installing

During `ref-tasks-install`` directory to create the structure as it should appear on the target system.

> 在 `ref-tasks-install`` 目录，以创建与目标系统上应有的结构相同的结构。

How your software is built affects what you must do to be sure your software is installed correctly. The following list describes what you must do for installation depending on the type of build system used by the software being built:

> 软件的构建方式会影响您确保软件安装正确的步骤。以下列表描述了根据软件使用的构建系统类型，您必须进行的安装步骤：

- *Autotools and CMake:* If the software your recipe is building uses Autotools or CMake, the OpenEmbedded build system understands how to install the software. Consequently, you do not have to have a `ref-tasks-install` task as part of your recipe. You just need to make sure the install portion of the build completes with no issues. However, if you wish to install additional files not already being installed by `make install`, you should do this using a `do_install:append` function using the install command as described in the \"Manual\" bulleted item later in this list.

> 如果你的 recipes 正在构建的软件使用 Autotools 或 CMake，OpenEmbedded 构建系统了解如何安装软件。因此，您不必在 recipes 中拥有 `ref-tasks-install` 任务。您只需确保构建的安装部分没有问题即可。但是，如果您希望安装 `make install` 未安装的其他文件，则应使用“手册”中介绍的 `do_install:append` 函数使用安装命令来进行此操作。

- *Other (using* `make install`*)*: You need to define a `ref-tasks-install``, and so forth).

> 你需要在你的配方中定义一个 `ref-tasks-install`` 等)。

For an example recipe using `make install`, see the \"`dev-manual/new-recipe:building a makefile-based package`\" section.

> 为了查看使用“make install”的示例 recipes，请参阅“dev-manual / new-recipe：构建基于 makefile 的包”部分。

- *Manual:* You need to define a `ref-tasks-install``. Once the directories exist, your function can use ` install` to manually install the built software into the directories.

> 你需要在你的配方中定义一个 `ref-tasks-install`` 下创建目录。一旦目录存在，你的函数可以使用 ` install` 手动将构建的软件安装到目录中。

You can find more information on `install` at [https://www.gnu.org/software/coreutils/manual/html_node/install-invocation.html](https://www.gnu.org/software/coreutils/manual/html_node/install-invocation.html).

> 您可以在 [https://www.gnu.org/software/coreutils/manual/html_node/install-invocation.html](https://www.gnu.org/software/coreutils/manual/html_node/install-invocation.html) 上找到更多有关 `安装` 的信息。

For the scenarios that do not use Autotools or CMake, you need to track the installation and diagnose and fix any issues until everything installs correctly. You need to look in the default location of `$/image`, to be sure your files have been installed correctly.

> 对于不使用 Autotools 或 CMake 的场景，您需要跟踪安装，诊断和修复任何问题，直到一切正常安装。您需要查看默认位置 `$/image`，以确保您的文件已正确安装。

::: note
::: title
Note
:::

- During the installation process, you might need to modify some of the installed files to suit the target layout. For example, you might need to replace hard-coded paths in an initscript with values of variables provided by the build system, such as replacing `/usr/bin/` with `$ if needed.

> 在安装过程中，您可能需要修改一些已安装文件以适应目标布局。例如，您可能需要用构建系统提供的变量值替换 initscript 中的硬编码路径，例如将'/usr/bin/'替换为'$。

- `oe_runmake install`, which can be run directly or can be run indirectly by the `ref-classes-autotools`, you might be able to work around them by disabling parallel Makefile installs by adding the following to the recipe:

> `- ` oe_runmake install `，可以直接运行，也可以通过` ref-classes-autotools ` 期间遇到间歇性失败，您可以通过在配方中添加以下内容来禁用并行 Makefile 安装来解决这些问题：

```
PARALLEL_MAKEINST = ""
```

See `PARALLEL_MAKEINST` for additional information.

- If you need to install one or more custom CMake toolchain files that are supplied by the application you are building, install the files to `$.

> 如果你需要安装一个或多个由您正在构建的应用程序提供的自定义 CMake 工具链文件，请在 `ref-tasks-install` 期间将文件安装到 `$/cmake/Modules`。
> :::

# Enabling System Services

If you want to install a service, which is a process that usually starts on boot and runs in the background, then you must include some additional definitions in your recipe.

> 如果你想安装一个服务，这是一个通常在引导时启动并在后台运行的进程，那么你必须在你的配方中包含一些额外的定义。

If you are adding services and the service initialization script or the service file itself is not installed, you must provide for that installation in your recipe using a `do_install:append` function. If your recipe already has a `ref-tasks-install` function, update the function near its end rather than adding an additional `do_install:append` function.

> 如果您正在添加服务，而服务初始化脚本或服务文件本身未安装，则必须使用“do_install：append”功能在您的 recipes 中提供安装。如果您的 recipes 已经有一个“ref-tasks-install”功能，请在其末尾更新该功能，而不是添加额外的“do_install：append”功能。

When you create the installation for your services, you need to accomplish what is normally done by `make install`. In other words, make sure your installation arranges the output similar to how it is arranged on the target system.

> 当您为服务创建安装程序时，您需要完成通常由“ make install”完成的操作。换句话说，确保您的安装将输出排列为与目标系统上的排列方式相似的方式。

The OpenEmbedded build system provides support for starting services two different ways:

- *SysVinit:* SysVinit is a system and service manager that manages the init system used to control the very basic functions of your system. The init program is the first program started by the Linux kernel when the system boots. Init then controls the startup, running and shutdown of all other programs.

> SysVinit 是一个系统和服务管理器，用于管理用于控制系统的基本功能的 init 系统。当系统启动时，init 程序是 Linux 内核启动的第一个程序。然后，init 控制所有其他程序的启动、运行和关闭。

To enable a service using SysVinit, your recipe needs to inherit the `ref-classes-update-rc.d` class. The class helps facilitate safely installing the package on the target.

> 要使用 SysVinit 启用服务，您的配方需要继承“ref-classes-update-rc.d”类。该类有助于安全地在目标上安装软件包。

You will need to set the `INITSCRIPT_PACKAGES` variables within your recipe.

> 你需要在你的 recipes 中设置 `INITSCRIPT_PACKAGES` 变量。

- *systemd:* System Management Daemon (systemd) was designed to replace SysVinit and to provide enhanced management of services. For more information on systemd, see the systemd homepage at [https://freedesktop.org/wiki/Software/systemd/](https://freedesktop.org/wiki/Software/systemd/).

> systemd：Systemd 是为了取代 SysVinit 而设计的系统管理守护进程。要了解更多关于 systemd 的信息，请参阅 freedesktop.org 的 systemd 主页：[https://freedesktop.org/wiki/Software/systemd/](https://freedesktop.org/wiki/Software/systemd/)。

To enable a service using systemd, your recipe needs to inherit the `ref-classes-systemd` section for more information.

> 要使用 systemd 启用一项服务，您的 recipes 需要继承 `ref-classes-systemd` 中的 `systemd.bbclass` 文件。

# Packaging

Successful packaging is a combination of automated processes performed by the OpenEmbedded build system and some specific steps you need to take. The following list describes the process:

> 成功的打包是由 OpenEmbedded 构建系统执行的自动化流程与您需要采取的一些特定步骤的结合。以下列表描述了该流程：

- *Splitting Files*: The `ref-tasks-package` task ensures that files are split up and packaged correctly.

> - *拆分文件*：`ref-tasks-package` 任务确保文件被正确拆分和打包。

- *Running QA Checks*: The `ref-classes-insane`\" chapter in the Yocto Project Reference Manual.

> -*运行质量检查*: `ref-classes-insane`”章节。

- *Hand-Checking Your Packages*: After you build your software, you need to be sure your packages are correct. Examine the `$, ` do_install(:append)`, and so forth as needed.

> *检查您的软件包*：在构建您的软件之后，您需要确保您的软件包是正确的。检查 `$、` do_install(:append)` 等。

- *Splitting an Application into Multiple Packages*: If you need to split an application into several packages, see the \"`dev-manual/new-recipe:splitting an application into multiple packages`\" section for an example.

> - *将应用程序拆分成多个包*：如果需要将应用程序拆分成多个包，请参见“dev-manual/new-recipe：将应用程序拆分成多个包”部分以获取示例。

- *Installing a Post-Installation Script*: For an example showing how to install a post-installation script, see the \"`dev-manual/new-recipe:post-installation scripts`\" section.

> *安装后安装脚本*：要查看如何安装后安装脚本的示例，请参阅“ dev-manual/new-recipe：后安装脚本”部分。

- *Marking Package Architecture*: Depending on what your recipe is building and how it is configured, it might be important to mark the packages produced as being specific to a particular machine, or to mark them as not being specific to a particular machine or architecture at all.

> 标记软件包架构：根据您的配方构建和配置的内容，标记生成的软件包是特定于特定机器的，还是标记它们不是特定于特定机器或架构的，可能都是重要的。

By default, packages apply to any machine with the same architecture as the target machine. When a recipe produces packages that are machine-specific (e.g. the `MACHINE` value is passed into the configure script or a patch is applied only for a particular machine), you should mark them as such by adding the following to the recipe:

> 默认情况下，软件包适用于与目标机器具有相同架构的任何机器。当配方产生专用于某台机器的软件包(例如将 `MACHINE` 值传递给配置脚本或仅为特定机器应用补丁)时，您应通过添加以下内容来将其标记为：

```
PACKAGE_ARCH = "$"
```

On the other hand, if the recipe produces packages that do not contain anything specific to the target machine or architecture at all (e.g. recipes that simply package script files or configuration files), you should use the `ref-classes-allarch` class to do this for you by adding this to your recipe:

> 另一方面，如果 recipes 不包含任何特定于目标机器或架构的内容(例如，仅打包脚本文件或配置文件的 recipes)，您应该使用 `ref-classes-allarch` 类来为您做到这一点，方法是将其添加到您的 recipes 中：

```
inherit allarch
```

Ensuring that the package architecture is correct is not critical while you are doing the first few builds of your recipe. However, it is important in order to ensure that your recipe rebuilds (or does not rebuild) appropriately in response to changes in configuration, and to ensure that you get the appropriate packages installed on the target machine, particularly if you run separate builds for more than one target machine.

> 确保包的架构正确在您做第一次构建时并不关键。但是，为了确保您的配方能够对配置的变化做出正确的重建(或不重建)，以及确保您在目标机器上安装了适当的包，特别是如果您针对多个目标机器运行单独的构建，这一点很重要。

# Sharing Files Between Recipes

Recipes often need to use files provided by other recipes on the build host. For example, an application linking to a common library needs access to the library itself and its associated headers. The way this access is accomplished is by populating a sysroot with files. Each recipe has two sysroots in its work directory, one for target files (`recipe-sysroot`) and one for files that are native to the build host (`recipe-sysroot-native`).

> recipes 通常需要使用构建主机上其他 recipes 提供的文件。例如，链接到公共库的应用程序需要访问库本身及其关联的头文件。实现此访问的方法是通过填充 sysroot 文件。每个 recipes 在其工作目录中有两个 sysroot，一个用于目标文件(“recipe-sysroot”)，另一个用于构建主机本地文件(“recipe-sysroot-native”)。

::: note
::: title
Note
:::

You could find the term \"staging\" used within the Yocto project regarding files populating sysroots (e.g. the `STAGING_DIR` variable).

> 在 Yocto 项目中，可以使用“staging”一词来描述填充 sysroots(例如 `STAGING_DIR` 变量)的文件。
> :::

Recipes should never populate the sysroot directly (i.e. write files into sysroot). Instead, files should be installed into standard locations during the `ref-tasks-install`` directory. The reason for this limitation is that almost all files that populate the sysroot are cataloged in manifests in order to ensure the files can be removed later when a recipe is either modified or removed. Thus, the sysroot is able to remain free from stale files.

> recipes 不应该直接填充 sysroot(即在 sysroot 中写入文件)。相反，文件应该在 `ref-tasks-install` 任务期间安装到标准位置，该任务位于 `$` 目录中。这个限制的原因是几乎所有填充 sysroot 的文件都在清单中进行了目录，以确保在 recipes 被修改或删除时可以将文件删除。因此，sysroot 可以保持不受陈旧文件的影响。

A subset of the files installed by the `ref-tasks-install` variable to automatically populate the sysroot. It is possible to modify the list of directories that populate the sysroot. The following example shows how you could add the `/opt` directory to the list of directories within a recipe:

> `ref-tasks-install` 任务安装的文件的一个子集被 `ref-tasks-populate_sysroot` 任务使用，这是由 `SYSROOT_DIRS` 变量定义的，用于自动填充 sysroot。可以修改填充 sysroot 的目录列表。下面的示例显示了如何在配方中将 `/opt` 目录添加到目录列表中：

```
SYSROOT_DIRS += "/opt"
```

::: note
::: title
Note
:::

The [/sysroot-only].

> 只有[/sysroot]的情况下共享这些工件。
> :::

For a more complete description of the `ref-tasks-populate_sysroot` class.

> 对于 `ref-tasks-populate_sysroot` 类。

# Using Virtual Providers

Prior to a build, if you know that several different recipes provide the same functionality, you can use a virtual provider (i.e. `virtual/*`) as a placeholder for the actual provider. The actual provider is determined at build-time.

> 在构建之前，如果您知道几种不同的配方提供相同的功能，您可以使用虚拟提供程序(即 `virtual/*`)作为实际提供程序的占位符。实际提供程序在构建时确定。

A common scenario where a virtual provider is used would be for the kernel recipe. Suppose you have three kernel recipes whose `PN` class:

> 一个常见的使用虚拟提供者的场景是内核配方。假设您有三个内核配方，它们的 PN 值映射到 kernel-big，kernel-mid 和 kernel-small。此外，这些配方中的每一个都以某种方式使用 PROVIDES 语句，该语句本质上将其标识为能够提供 virtual / kernel。这是通过 ref-classes-kernel 类的一种方式：

```
PROVIDES += "virtual/kernel"
```

Any recipe that inherits the `ref-classes-kernel` statement that identifies that recipe as being able to provide the `virtual/kernel` item.

> 任何继承 `ref-classes-kernel` 类的配方都会使用一个 `PROVIDES` 声明，用来标识该配方能够提供 `virtual/kernel` 项目。

Now comes the time to actually build an image and you need a kernel recipe, but which one? You can configure your build to call out the kernel recipe you want by using the `PREFERRED_PROVIDER`) configuration file. This include file is the reason all x86-based machines use the `linux-yocto` kernel. Here are the relevant lines from the include file:

> 现在是构建镜像的时候了，你需要一个内核配方，但是哪一个呢？你可以通过使用 `PREFERRED_PROVIDER` 变量来配置你的构建，以调用你想要的内核配方。例如，考虑:yocto_[git:`x86-base.inc`](git:%60x86-base.inc%60) \</poky/tree/meta/conf/machine/include/x86/x86-base.inc\>包含文件，它是一个机器(即:term:`MACHINE`)配置文件。这个包含文件是所有基于 x86 的机器都使用 `linux-yocto` 内核的原因。以下是该包含文件的相关行：

```
PREFERRED_PROVIDER_virtual/kernel ??= "linux-yocto"
PREFERRED_VERSION_linux-yocto ??= "4.15%"
```

When you use a virtual provider, you do not have to \"hard code\" a recipe name as a build dependency. You can use the `DEPENDS` variable to state the build is dependent on `virtual/kernel` for example:

> 当你使用虚拟提供者时，你不必将配方名称硬编码为构建依赖项。你可以使用 `DEPENDS` 变量来表明构建依赖于 `virtual/kernel`，例如：

```
DEPENDS = "virtual/kernel"
```

During the build, the OpenEmbedded build system picks the correct recipe needed for the `virtual/kernel` dependency based on the `PREFERRED_PROVIDER` variable. If you want to use the small kernel mentioned at the beginning of this section, configure your build as follows:

> 在构建期间，OpenEmbedded 构建系统会根据 `PREFERRED_PROVIDER` 变量选择 `virtual/kernel` 依赖项所需的正确配方。如果您想使用本节开头提到的小型内核，请按照以下步骤配置构建：

```
PREFERRED_PROVIDER_virtual/kernel ??= "kernel-small"
```

::: note
::: title
Note
:::

Any recipe that `PROVIDES` does not get built. Preventing these recipes from building is usually the desired behavior since this mechanism\'s purpose is to select between mutually exclusive alternative providers.

> 任何通过首选提供者最终未被选择的提供虚拟项目的配方都不会被构建。阻止这些配方构建通常是所需的行为，因为此机制的目的是在互斥的备选提供者之间进行选择。
> :::

The following lists specific examples of virtual providers:

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

Virtual providers only apply to build time dependencies specified with `PROVIDES`.

> 虚拟提供商仅适用于使用 `PROVIDES` 和 `DEPENDS` 指定的构建时依赖项。它们不适用于使用 `RPROVIDES` 和 `RDEPENDS` 指定的运行时依赖项。
> :::

# Properly Versioning Pre-Release Recipes

Sometimes the name of a recipe can lead to versioning problems when the recipe is upgraded to a final release. For example, consider the `irssi_0.8.16-rc1.bb` recipe file in the list of example recipes in the \"`dev-manual/new-recipe:storing and naming the recipe`\" section. This recipe is at a release candidate stage (i.e. \"rc1\"). When the recipe is released, the recipe filename becomes `irssi_0.8.16.bb`. The version change from `0.8.16-rc1` to `0.8.16` is seen as a decrease by the build system and package managers, so the resulting packages will not correctly trigger an upgrade.

> 有时候，当菜谱升级到最终版本时，菜谱的名字可能会导致版本问题。例如，在“存储和命名菜谱”部分的示例菜谱列表中，有一个 `irssi_0.8.16-rc1.bb` 菜谱文件。这个菜谱是处于发布候选阶段(即“rc1”)。当菜谱发布时，菜谱文件名称变成 `irssi_0.8.16.bb`。从 `0.8.16-rc1` 到 `0.8.16` 的版本变化被构建系统和软件包管理器视为降级，因此生成的软件包将不能正确触发升级。

In order to ensure the versions compare properly, the recommended convention is to set `PV` within the recipe to \"previous_version+current_version\". You can use an additional variable so that you can use the current version elsewhere. Here is an example:

> 为了确保版本比较正确，建议的约定是将“PV”设置为“上一个版本 + 当前版本”。您可以使用另一个变量，以便您可以在其他地方使用当前版本。这是一个例子：

```
REALPV = "0.8.16-rc1"
PV = "0.8.15+$"
```

# Post-Installation Scripts

Post-installation scripts run immediately after installing a package on the target or during image creation when a package is included in an image. To add a post-installation script to a package, add a `pkg_postinst:`[PACKAGENAME]` in place of PACKAGENAME.

> 安装后脚本会在安装包到目标上或在创建镜像时(如果包已包含在镜像中)立即运行。要将后安装脚本添加到包中，请在配方文件(`.bb`)中添加一个 `pkg_postinst:`[PACKAGENAME]`。

A post-installation function has the following structure:

```
pkg_postinst:PACKAGENAME() {
    # Commands to carry out
}
```

The script defined in the post-installation function is called when the root filesystem is created. If the script succeeds, the package is marked as installed.

> 当根文件系统创建时，会调用在后安装函数中定义的脚本。如果脚本成功，则将该软件包标记为已安装。

::: note
::: title
Note
:::

Any RPM post-installation script that runs on the target should return a 0 exit code. RPM does not allow non-zero exit codes for these scripts, and the RPM package manager will cause the package to fail installation on the target.

> 任何在目标上运行的 RPM 安装后脚本都应该返回 0 的退出代码。RPM 不允许这些脚本的非零退出代码，RPM 包管理器将导致包在目标上安装失败。
> :::

Sometimes it is necessary for the execution of a post-installation script to be delayed until the first boot. For example, the script might need to be executed on the device itself. To delay script execution until boot time, you must explicitly mark post installs to defer to the target. You can use `pkg_postinst_ontarget()` or call `postinst_intercept delay_to_first_boot` from `pkg_postinst()`. Any failure of a `pkg_postinst()` script (including exit 1) triggers an error during the `ref-tasks-rootfs` task.

> 有时，必须将安装后脚本的执行推迟到第一次启动时才执行。例如，脚本可能需要在设备本身上执行。要将脚本执行推迟到启动时，必须明确标记将安装后任务延迟到目标。您可以使用 `pkg_postinst_ontarget()` 或从 `pkg_postinst()` 调用 `postinst_intercept delay_to_first_boot`。`ref-tasks-rootfs` 任务期间，任何 `pkg_postinst()` 脚本的失败(包括退出 1)都会引发错误。

If you have recipes that use `pkg_postinst` function and they require the use of non-standard native tools that have dependencies during root filesystem construction, you need to use the `PACKAGE_WRITE_DEPS` variable in your recipe to list these tools. If you do not use this variable, the tools might be missing and execution of the post-installation script is deferred until first boot. Deferring the script to the first boot is undesirable and impossible for read-only root filesystems.

> 如果您的 recipes 中使用了 `pkg_postinst` 函数，并且需要在根文件系统构建期间使用非标准本机工具，则需要在 recipes 中使用 `PACKAGE_WRITE_DEPS` 变量来列出这些工具。如果您不使用此变量，则可能会缺少工具，并且在第一次启动时延迟执行后安装脚本。延迟脚本到第一次启动是不可取的，对于只读根文件系统是不可能的。

::: note
::: title
Note
:::

There is equivalent support for pre-install, pre-uninstall, and post-uninstall scripts by way of `pkg_preinst`, `pkg_prerm`, and `pkg_postrm`, respectively. These scrips work in exactly the same way as does `pkg_postinst` with the exception that they run at different times. Also, because of when they run, they are not applicable to being run at image creation time like `pkg_postinst`.

> 有相等的支持，可以通过 `pkg_preinst`、`pkg_prerm` 和 `pkg_postrm` 分别预安装、预卸载和后卸载脚本。这些脚本的工作方式与 `pkg_postinst` 完全相同，只是运行的时间不同。此外，由于运行的时间，它们不适用于像 `pkg_postinst` 一样在镜像创建时运行。
> :::

# Testing

The final step for completing your recipe is to be sure that the software you built runs correctly. To accomplish runtime testing, add the build\'s output packages to your image and test them on the target.

> 最后一步完成你的 recipes 是确保你构建的软件运行正确。为了完成运行时测试，将构建的输出包添加到你的镜像中，并在目标上进行测试。

For information on how to customize your image by adding specific packages, see \"`dev-manual/customizing-images:customizing images`\" section.

> 要了解如何通过添加特定的软件包来定制镜像的信息，请参阅“dev-manual/customizing-images：定制镜像”部分。

# Examples

To help summarize how to write a recipe, this section provides some recipe examples given various scenarios:

- [Building a single .c file package](#building-a-single-.c-file-package)
- [Building a Makefile-based package](#building-a-makefile-based-package)
- [Building an Autotooled package](#building-an-autotooled-package)
- [Building a Meson package](#building-a-meson-package)
- [Splitting an application into multiple packages](#splitting-an-application-into-multiple-packages)
- [Packaging externally produced binaries](#packaging-externally-produced-binaries)

## Building a Single .c File Package

Building an application from a single file that is stored locally (e.g. under `files`) requires a recipe that has the file listed in the `SRC_URI` in this case \-\-- the directory BitBake uses for the build:

> 从本地存储的单个文件(例如在 `files` 下)构建应用程序需要一个配方，在 `SRC_URI` 变量中列出该文件。此外，您还需要手动编写 `ref-tasks-compile` 和 `ref-tasks-install` 任务。`S` 变量定义包含源代码的目录，在本例中设置为 BitBake 用于构建的 `WORKDIR` 目录。

```
SUMMARY = "Simple helloworld application"
SECTION = "examples"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://$/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

SRC_URI = "file://helloworld.c"

S = "$"

do_compile() {
    $ helloworld.c -o helloworld
}

do_install() {
    install -d $
    install -m 0755 helloworld $
}
```

By default, the `helloworld`, `helloworld-dbg`, and `helloworld-dev` packages are built. For information on how to customize the packaging process, see the \"`dev-manual/new-recipe:splitting an application into multiple packages`\" section.

> 默认情况下，会构建 `helloworld`、`helloworld-dbg` 和 `helloworld-dev` 软件包。有关如何自定义打包过程的信息，请参阅“dev-manual/new-recipe：将应用程序拆分成多个软件包”部分。

## Building a Makefile-Based Package

Applications built with GNU `make` require a recipe that has the source archive listed in `SRC_URI` task by default.

> 应用程序使用 GNU `make` 构建时，需要在 `SRC_URI` 任务。

Some applications might require extra parameters to be passed to the compiler. For example, the application might need an additional header path. You can accomplish this by adding to the `CFLAGS` variable. The following example shows this:

> 一些应用程序可能需要传递额外的参数给编译器。例如，应用程序可能需要额外的头文件路径。您可以通过添加 `CFLAGS` 变量来实现此目的。以下示例显示此操作：

```
CFLAGS:prepend = "-I $/include "
```

In the following example, `lz4` is a makefile-based package:

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

S = "$/git"

# Fixed in r118, which is larger than the current version.
CVE_CHECK_IGNORE += "CVE-2014-4715"

EXTRA_OEMAKE = "PREFIX=$ BUILD_STATIC=no"

do_install() {
        oe_runmake install
}

BBCLASSEXTEND = "native nativesdk"
```

## Building an Autotooled Package

Applications built with the Autotools such as `autoconf` and `automake` require a recipe that has a source archive listed in `SRC_URI` class, which contains the definitions of all the steps needed to build an Autotool-based application. The result of the build is automatically packaged. And, if the application uses NLS for localization, packages with local information are generated (one package per language). Following is one example: (`hello_2.3.bb`):

> 应用程序使用 Autotools 构建，例如 `autoconf` 和 `automake`，需要一个 `SRC_URI` 类，该类包含构建基于 Autotool 的应用程序所需的所有步骤的定义。构建的结果会自动打包。如果应用程序使用 NLS 进行本地化，则会生成具有本地信息的软件包(每种语言一个软件包)。以下是一个示例：(`hello_2.3.bb`)：

```
SUMMARY = "GNU Helloworld application"
SECTION = "examples"
LICENSE = "GPL-2.0-or-later"
LIC_FILES_CHKSUM = "file://COPYING;md5=751419260aa954499f7abaabaa882bbe"

SRC_URI = "$.tar.gz"

inherit autotools gettext
```

The variable `LIC_FILES_CHKSUM`\" section in the Yocto Project Overview and Concepts Manual. You can quickly create Autotool-based recipes in a manner similar to the previous example.

> 变量 `LIC_FILES_CHKSUM` 用于跟踪源许可证的变更，如 Yocto 项目概览和概念手册中“dev-manual/licenses:tracking license changes”部分所述。您可以以类似于前面示例的方式快速创建基于 Autotool 的配方。

## Building a Meson Package

Applications built with the [Meson build system](https://mesonbuild.com/) just need a recipe that has sources described in `SRC_URI` class.

> 使用 Meson 构建系统构建的应用程序只需要一个在 `SRC_URI` 中描述的源码的配方，并继承 `ref-classes-meson` 类。

The :oe_[git:%60ipcalc](git:%60ipcalc) recipe \</meta-openembedded/tree/meta-networking/recipes-support/ipcalc\>\` is a simple example of an application without dependencies:

> 这个:oe_[git:%60ipcalc](git:%60ipcalc) recipes </meta-openembedded/tree/meta-networking/recipes-support/ipcalc>` 是一个没有依赖的简单应用程序的例子。

```
SUMMARY = "Tool to assist in network address calculations for IPv4 and IPv6."
HOMEPAGE = "https://gitlab.com/ipcalc/ipcalc"

SECTION = "net"

LICENSE = "GPL-2.0-only"
LIC_FILES_CHKSUM = "file://COPYING;md5=b234ee4d69f5fce4486a80fdaf4a4263"

SRC_URI = "git://gitlab.com/ipcalc/ipcalc.git;protocol=https;branch=master"
SRCREV = "4c4261a47f355946ee74013d4f5d0494487cc2d6"

S = "$/git"

inherit meson
```

Applications with dependencies are likely to inherit the `ref-classes-pkgconfig` class, as `pkg-config` is the default method used by Meson to find dependencies and compile applications against them.

> 应用程序有依赖性时很可能继承 `ref-classes-pkgconfig` 类，因为 `pkg-config` 是 Meson 用来查找依赖项并将应用程序编译与它们相关联的默认方法。

## Splitting an Application into Multiple Packages

You can use the variables `PACKAGES` to split an application into multiple packages.

> 你可以使用变量 `PACKAGES` 将应用程序分割成多个包。

Following is an example that uses the `libxpm` recipe. By default, this recipe generates a single package that contains the library along with a few binaries. You can modify the recipe to split the binaries into separate packages:

> 以下是使用 `libxpm` recipes 的示例。默认情况下，此 recipes 生成一个包，其中包含库以及几个可执行文件。您可以修改 recipes 以将可执行文件拆分成单独的包：

```
require xorg-lib-common.inc

SUMMARY = "Xpm: X Pixmap extension library"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://COPYING;md5=51f4270b012ecd4ab1a164f5f4ed6cf7"
DEPENDS += "libxext libsm libxt"
PE = "1"

XORG_PN = "libXpm"

PACKAGES =+ "sxpm cxpm"
FILES:cxpm = "$/cxpm"
FILES:sxpm = "$/sxpm"
```

In the previous example, we want to ship the `sxpm` and `cxpm` binaries in separate packages. Since `bindir` would be packaged into the main `PN` package does not include the above listed files.

> 在前面的例子中，我们希望将 `sxpm` 和 `cxpm` 二进制文件打包到不同的包中。由于 `bindir` 默认情况下会打包到主 `PN` 包不会包含上面列出的文件。

## Packaging Externally Produced Binaries

Sometimes, you need to add pre-compiled binaries to an image. For example, suppose that there are binaries for proprietary code, created by a particular division of a company. Your part of the company needs to use those binaries as part of an image that you are building using the OpenEmbedded build system. Since you only have the binaries and not the source code, you cannot use a typical recipe that expects to fetch the source specified in `SRC_URI` and then compile it.

> 有时，您需要将预编译的二进制文件添加到映像中。例如，假设存在由公司的特定部门创建的专有代码的二进制文件。您的公司部分需要将这些二进制文件作为使用 OpenEmbedded 构建系统构建的映像的一部分使用。由于您只有二进制文件而没有源代码，因此无法使用期望获取 `SRC_URI` 中指定的源代码并进行编译的典型配方。

One method is to package the binaries and then install them as part of the image. Generally, it is not a good idea to package binaries since, among other things, it can hinder the ability to reproduce builds and could lead to compatibility problems with ABI in the future. However, sometimes you have no choice.

> 一种方法是将二进制文件打包，然后将它们作为镜像的一部分安装。一般来说，打包二进制文件不是一个好主意，因为在其他方面，它会阻碍重现构建的能力，并可能导致将来与 ABI 的兼容性问题。但是，有时您别无选择。

The easiest solution is to create a recipe that uses the `ref-classes-bin-package` variables in the Yocto Project Reference Manual\'s variable glossary.

> 最简单的解决方案是创建一个使用 `ref-classes-bin-package` 变量。

::: note
::: title
Note
:::

- Using `DEPENDS` makes sure that the libraries are available in the staging sysroot when other recipes link against the library, which might be necessary for successful linking.

> 使用 DEPENDS 是一个很好的主意，即使是用二进制形式分发的组件也是如此，而且对于共享库来说，在 DEPENDS 中列出库的依赖性可以确保当其他配方链接到库时，这些库可以在暂存系统根目录中获得，这可能是链接成功所必需的。

- Using `DEPENDS`\" section in the Yocto Project Overview and Concepts Manual for more information.

> 使用 `DEPENDS` 也可以自动添加软件包之间的运行时依赖关系。有关更多信息，请参阅 Yocto 项目概述和概念手册中的“自动添加的运行时依赖关系”部分。
> :::

If you cannot use the `ref-classes-bin-package` class, you need to be sure you are doing the following:

- Create a recipe where the `ref-tasks-configure``.

> 创建一个 recipes，其中 `ref-tasks-configure` 中找到 Makefile，否则不会做任何事情。

If `$`]` flag to turn the tasks into no-ops, as follows:

> 如果 `$`]` 标志将任务转换为无操作，如下所示：

```
do_configure[noexec] = "1"
do_compile[noexec] = "1"
```

Unlike `bitbake-user-manual/bitbake-user-manual-metadata:deleting a task` task.

> 与使用标记不同，使用 bitbake-user-manual / bitbake-user-manual-metadata：删除任务任务的依赖链。

- Make sure your `ref-tasks-install` task installs the binaries appropriately.
- Ensure that you set up `FILES``) to point to the files you have installed, which of course depends on where you have installed them and whether those files are in different locations than the defaults.

> 确保设置文件(通常是 PN)指向你安装的文件，当然，这取决于你安装它们的位置以及这些文件是否与默认位置不同。

# Following Recipe Style Guidelines

When writing recipes, it is good to conform to existing style guidelines. The :oe_wiki:[OpenEmbedded Styleguide \</Styleguide\>] wiki page provides rough guidelines for preferred recipe style.

> 写 recipes 时，最好遵守现有的样式指南。:oe_wiki:[OpenEmbedded Styleguide \</Styleguide\>] 维基页面提供了首选 recipes 样式的大致指南。

It is common for existing recipes to deviate a bit from this style. However, aiming for at least a consistent style is a good idea. Some practices, such as omitting spaces around `=` operators in assignments or ordering recipe components in an erratic way, are widely seen as poor style.

> 一般来说，现有 recipes 会有一定的偏差。不过，尽量保持一致的风格是个好主意。某些做法，比如在赋值运算符 `=` 两边省略空格，或者把 recipes 组件以不规则的方式排列，都被广泛认为是不好的风格。

# Recipe Syntax

Understanding recipe file syntax is important for writing recipes. The following list overviews the basic items that make up a BitBake recipe file. For more complete BitBake syntax descriptions, see the \"`bitbake:bitbake-user-manual/bitbake-user-manual-metadata`\" chapter of the BitBake User Manual.

> 了解配方文件语法对于编写配方非常重要。下面的列表概述了构成 BitBake 配方文件的基本项目。有关更完整的 BitBake 语法描述，请参阅 BitBake 用户手册的“BitBake-user-manual / bitbake-user-manual-metadata”章节。

- *Variable Assignments and Manipulations:* Variable assignments allow a value to be assigned to a variable. The assignment can be static text or might include the contents of other variables. In addition to the assignment, appending and prepending operations are also supported.

> 变量赋值和操作：变量赋值允许将值分配给变量。赋值可以是静态文本，也可以包括其他变量的内容。除了赋值之外，还支持附加和前置操作。

The following example shows some of the ways you can use variables in recipes:

```
S = "$"
CFLAGS += "-DNO_ASM"
CFLAGS:append = " --enable-important-feature"
```

- *Functions:* Functions provide a series of actions to be performed. You usually use functions to override the default implementation of a task function or to complement a default function (i.e. append or prepend to an existing function). Standard functions use `sh` shell syntax, although access to OpenEmbedded variables and internal methods are also available.

> 功能：功能提供一系列要执行的操作。通常使用函数来覆盖任务函数的默认实现，或者补充现有函数(即附加或在前面添加)。标准函数使用 `sh` shell 语法，尽管也可以访问 OpenEmbedded 变量和内部方法。

Here is an example function from the `sed` recipe:

```
do_install () {
    autotools_do_install
    install -d $
    mv $/sed
    rmdir $/
}
```

It is also possible to implement new functions that are called between existing tasks as long as the new functions are not replacing or complementing the default functions. You can implement functions in Python instead of shell. Both of these options are not seen in the majority of recipes.

> 也可以在现有任务之间实现新的功能，只要新功能不替换或补充默认功能即可。您可以用 Python 而不是 shell 来实现功能。这两种选择在大多数配方中都不见。

- *Keywords:* BitBake recipes use only a few keywords. You use keywords to include common functions (`inherit`), load parts of a recipe from other files (`include` and `require`) and export variables to the environment (`export`).

> - *关键词：*BitBake recipes 只使用几个关键词。您可以使用关键词来包括常见函数(`inherit`)，从其他文件加载 recipes 的部分(`include` 和 `require`)以及将变量导出到环境(`export`)。

The following example shows the use of some of these keywords:

```
export POSTCONF = "$/postconf"
inherit autoconf
require otherfile.inc
```

- *Comments (#):* Any lines that begin with the hash character (`#`) are treated as comment lines and are ignored:

  ```
  # This is a comment
  ```

This next list summarizes the most important and most commonly used parts of the recipe syntax. For more information on these parts of the syntax, you can reference the \"`bitbake:bitbake-user-manual/bitbake-user-manual-metadata`\" chapter in the BitBake User Manual.

> 下一个列表总结了 recipes 语法中最重要和最常用的部分。要了解更多关于语法的信息，您可以参考 BitBake 用户手册中的“bitbake：bitbake-user-manual / bitbake-user-manual-metadata”章节。

- *Line Continuation (\\):* Use the backward slash (`\`) character to split a statement over multiple lines. Place the slash character at the end of the line that is to be continued on the next line:

> 使用反斜杠(\)字符将语句拆分到多行。将斜杠字符放在要继续在下一行的行末：

```
VAR = "A really long \
       line"
```

::: note
::: title
Note
:::

You cannot have any characters including spaces or tabs after the slash character.
:::

- *Using Variables (\$` syntax to access the contents of a variable:

  ```
  SRC_URI = "$.tar.gz"
  ```

  ::: note
  ::: title
  Note
  :::

  It is important to understand that the value of a variable expressed in this form does not get substituted automatically. The expansion of these expressions happens on-demand later (e.g. usually when a function that makes reference to the variable executes). This behavior ensures that the values are most appropriate for the context in which they are finally used. On the rare occasion that you do need the variable expression to be expanded immediately, you can use the := operator instead of = when you make the assignment, but this is not generally needed.

> 重要的是要理解，以这种形式表达的变量的值不会自动替换。这些表达式的扩展是在需要时延迟执行的(例如，通常是引用变量的函数执行时)。这种行为确保了最终使用时最适合的值。在极少数情况下，您确实需要立即扩展变量表达式，您可以在赋值时使用:=操作符而不是=，但通常不需要。
> :::

- *Quote All Assignments (\"value\"):* Use double quotes around values in all variable assignments (e.g. `"value"`). Following is an example:

  ```
  VAR1 = "$"
  VAR2 = "The version is $"
  ```
- *Conditional Assignment (?=):* Conditional assignment is used to assign a value to a variable, but only when the variable is currently unset. Use the question mark followed by the equal sign (`?=`) to make a \"soft\" assignment used for conditional assignment. Typically, \"soft\" assignments are used in the `local.conf` file for variables that are allowed to come through from the external environment.

> 条件赋值(?=)：条件赋值用于将值分配给变量，但仅当变量当前未设置时才使用。使用问号后跟等号(`?=`)进行“软”赋值，用于条件赋值。通常，“软”赋值用于 `local.conf` 文件中的变量，允许从外部环境传递。

Here is an example where `VAR1` is set to \"New value\" if it is currently empty. However, if `VAR1` has already been set, it remains unchanged:

```
VAR1 ?= "New value"
```

In this next example, `VAR1` is left with the value \"Original value\":

```
VAR1 = "Original value"
VAR1 ?= "New value"
```

- *Appending (+=):* Use the plus character followed by the equals sign (`+=`) to append values to existing variables.

  ::: note
  ::: title
  Note
  :::

  This operator adds a space between the existing content of the variable and the new content.
  :::

  Here is an example:

  ```
  SRC_URI += "file://fix-makefile.patch"
  ```
- *Prepending (=+):* Use the equals sign followed by the plus character (`=+`) to prepend values to existing variables.

  ::: note
  ::: title
  Note
  :::

  This operator adds a space between the new content and the existing content of the variable.
  :::

  Here is an example:

  ```
  VAR =+ "Starts"
  ```
- *Appending (:append):* Use the `:append` operator to append values to existing variables. This operator does not add any additional space. Also, the operator is applied after all the `+=`, and `=+` operators have been applied and after all `=` assignments have occurred. This means that if `:append` is used in a recipe, it can only be overridden by another layer using the special `:remove` operator, which in turn will prevent further layers from adding it back.

> *追加(:append):* 使用 `:append` 操作符将值附加到现有变量。此操作符不添加任何额外空间。此外，该操作符在应用所有 `+=` 和 `=+` 操作符之后以及所有 `=` 赋值之后才应用。这意味着如果在配方中使用 `:append`，则只能由使用特殊 `:remove` 操作符的另一层覆盖，从而阻止其他层次将其添加回来。

The following example shows the space being explicitly added to the start to ensure the appended value is not merged with the existing value:

```
CFLAGS:append = " --enable-important-feature"
```

You can also use the `:append` operator with overrides, which results in the actions only being performed for the specified target or machine:

```
CFLAGS:append:sh4 = " --enable-important-sh4-specific-feature"
```

- *Prepending (:prepend):* Use the `:prepend` operator to prepend values to existing variables. This operator does not add any additional space. Also, the operator is applied after all the `+=`, and `=+` operators have been applied and after all `=` assignments have occurred.

> *前置(:prepend)：*使用 `:prepend` 运算符将值附加到现有变量。此运算符不会添加任何额外的空间。此外，在应用所有 `+=` 和 `=+` 运算符以及所有 `=` 赋值之后，才会应用此运算符。

The following example shows the space being explicitly added to the end to ensure the prepended value is not merged with the existing value:

```
CFLAGS:prepend = "-I$/myincludes "
```

You can also use the `:prepend` operator with overrides, which results in the actions only being performed for the specified target or machine:

```
CFLAGS:prepend:sh4 = "-I$/myincludes "
```

- *Overrides:* You can use overrides to set a value conditionally, typically based on how the recipe is being built. For example, to set the `KBRANCH`, except for qemuarm where it should be set to \"standard/arm-versatile-926ejs\", you would do the following:

> *覆盖：*您可以使用覆盖来根据构建菜谱的方式有条件地设置值。例如，要将 `KBRANCH` 变量的值设置为“standard/base”，除了 qemuarm，其值应设置为“standard/arm-versatile-926ejs”，您可以执行以下操作：

```
KBRANCH = "standard/base"
KBRANCH:qemuarm = "standard/arm-versatile-926ejs"
```

Overrides are also used to separate alternate values of a variable in other situations. For example, when setting variables such as `FILES` that are specific to individual packages produced by a recipe, you should always use an override that specifies the name of the package.

> 覆盖也用于在其他情况下分离变量的替代值。例如，当设置特定于由配方生成的单个包的变量(如 `FILES`)时，您应始终使用指定包名称的覆盖。

- *Indentation:* Use spaces for indentation rather than tabs. For shell functions, both currently work. However, it is a policy decision of the Yocto Project to use tabs in shell functions. Realize that some layers have a policy to use spaces for all indentation.

> *缩进：* 对于缩进，使用空格而不是制表符。对于 shell 函数，目前两者都有效。但是，这是 Yocto 项目的政策决定，在 shell 函数中使用制表符。请注意，一些层具有使用空格进行所有缩进的政策。

- *Using Python for Complex Operations:* For more advanced processing, it is possible to use Python code during variable assignments (e.g. search and replacement on a variable).

> 使用 Python 进行复杂操作：对于更高级的处理，可以在变量赋值期间使用 Python 代码(例如，对变量进行搜索和替换)。

You indicate Python code using the `$` syntax for the variable assignment:

```
SRC_URI = "ftp://ftp.info-zip.org/pub/infozip/src/zip$.tgz
```

- *Shell Function Syntax:* Write shell functions as if you were writing a shell script when you describe a list of actions to take. You should ensure that your script works with a generic `sh` and that it does not require any `bash` or other shell-specific functionality. The same considerations apply to various system utilities (e.g. `sed`, `grep`, `awk`, and so forth) that you might wish to use. If in doubt, you should check with multiple implementations \-\-- including those from BusyBox.

> 函数语法：描述要执行的操作时，就像编写 shell 脚本一样编写 shell 函数。您应确保脚本可以使用通用的 sh，而不需要任何 bash 或其他特定于 shell 的功能。同样的考虑也适用于您可能希望使用的各种系统实用程序(例如 sed、grep、awk 等)。如有疑问，您应该检查包括 BusyBox 中的多个实现。
