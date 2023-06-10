---
tip: translate by openai@2023-06-10 10:01:39
...
---
title: "`devtool` Quick Reference"
-------------------------

The `devtool` command-line tool provides a number of features that help you build, test, and package software. This command is available alongside the `bitbake` command. Additionally, the `devtool` command is a key part of the extensible SDK.

> 命令行工具 `devtool` 提供了许多功能，可帮助您构建、测试和打包软件。该命令与 `bitbake` 命令一起可用。此外，`devtool` 命令是可扩展 SDK 的关键部分。

This chapter provides a Quick Reference for the `devtool` command. For more information on how to apply the command when using the extensible SDK, see the \"`/sdk-manual/extensible`{.interpreted-text role="doc"}\" chapter in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 本章提供了 `devtool` 命令的快速参考。有关如何在使用可扩展 SDK 时应用该命令的更多信息，请参阅 Yocto Project 应用开发和可扩展软件开发套件（eSDK）手册中的“/sdk-manual/extensible”章节。

# Getting Help {#devtool-getting-help}

The `devtool` command line is organized similarly to Git in that it has a number of sub-commands for each function. You can run `devtool --help` to see all the commands:

> `devtool` 命令行的组织方式类似于 Git，它具有许多用于每个功能的子命令。您可以运行 `devtool --help` 来查看所有命令：

```
$ devtool -h
NOTE: Starting bitbake server...
usage: devtool [--basepath BASEPATH] [--bbpath BBPATH] [-d] [-q] [--color COLOR] [-h] <subcommand> ...

OpenEmbedded development tool

options:
  --basepath BASEPATH   Base directory of SDK / build directory
  --bbpath BBPATH       Explicitly specify the BBPATH, rather than getting it from the metadata
  -d, --debug           Enable debug output
  -q, --quiet           Print only errors
  --color COLOR         Colorize output (where COLOR is auto, always, never)
  -h, --help            show this help message and exit

subcommands:
  Beginning work on a recipe:
    add                   Add a new recipe
    modify                Modify the source for an existing recipe
    upgrade               Upgrade an existing recipe
  Getting information:
    status                Show workspace status
    latest-version        Report the latest version of an existing recipe
    check-upgrade-status  Report upgradability for multiple (or all) recipes
    search                Search available recipes
  Working on a recipe in the workspace:
    build                 Build a recipe
    rename                Rename a recipe file in the workspace
    edit-recipe           Edit a recipe file
    find-recipe           Find a recipe file
    configure-help        Get help on configure script options
    update-recipe         Apply changes from external source tree to recipe
    reset                 Remove a recipe from your workspace
    finish                Finish working on a recipe in your workspace
  Testing changes on target:
    deploy-target         Deploy recipe output files to live target machine
    undeploy-target       Undeploy recipe output files in live target machine
    build-image           Build image including workspace recipe packages
  Advanced:
    create-workspace      Set up workspace in an alternative location
    extract               Extract the source for an existing recipe
    sync                  Synchronize the source tree for an existing recipe
    menuconfig            Alter build-time configuration for a recipe
    import                Import exported tar archive into workspace
    export                Export workspace into a tar archive
  other:
    selftest-reverse      Reverse value (for selftest)
    pluginfile            Print the filename of this plugin
    bbdir                 Print the BBPATH directory of this plugin
    count                 How many times have this plugin been registered.
    multiloaded           How many times have this plugin been initialized
Use devtool <subcommand> --help to get help on a specific command
```

As directed in the general help output, you can get more syntax on a specific command by providing the command name and using `--help`:

```
$ devtool add --help
NOTE: Starting bitbake server...
usage: devtool add [-h] [--same-dir | --no-same-dir] [--fetch URI] [--npm-dev] [--version VERSION] [--no-git] [--srcrev SRCREV | --autorev] [--srcbranch SRCBRANCH] [--binary] [--also-native] [--src-subdir SUBDIR] [--mirrors]
                   [--provides PROVIDES]
                   [recipename] [srctree] [fetchuri]

Adds a new recipe to the workspace to build a specified source tree. Can optionally fetch a remote URI and unpack it to create the source tree.

arguments:
  recipename            Name for new recipe to add (just name - no version, path or extension). If not specified, will attempt to auto-detect it.
  srctree               Path to external source tree. If not specified, a subdirectory of /media/build1/poky/build/workspace/sources will be used.
  fetchuri              Fetch the specified URI and extract it to create the source tree

options:
  -h, --help            show this help message and exit
  --same-dir, -s        Build in same directory as source
  --no-same-dir         Force build in a separate build directory
  --fetch URI, -f URI   Fetch the specified URI and extract it to create the source tree (deprecated - pass as positional argument instead)
  --npm-dev             For npm, also fetch devDependencies
  --version VERSION, -V VERSION
                        Version to use within recipe (PV)
  --no-git, -g          If fetching source, do not set up source tree as a git repository
  --srcrev SRCREV, -S SRCREV
                        Source revision to fetch if fetching from an SCM such as git (default latest)
  --autorev, -a         When fetching from a git repository, set SRCREV in the recipe to a floating revision instead of fixed
  --srcbranch SRCBRANCH, -B SRCBRANCH
                        Branch in source repository if fetching from an SCM such as git (default master)
  --binary, -b          Treat the source tree as something that should be installed verbatim (no compilation, same directory structure). Useful with binary packages e.g. RPMs.
  --also-native         Also add native variant (i.e. support building recipe for the build host as well as the target machine)
  --src-subdir SUBDIR   Specify subdirectory within source tree to use
  --mirrors             Enable PREMIRRORS and MIRRORS for source tree fetching (disable by default).
  --provides PROVIDES, -p PROVIDES
                        Specify an alias for the item provided by the recipe. E.g. virtual/libgl
```

# The Workspace Layer Structure {#devtool-the-workspace-layer-structure}

`devtool` uses a \"Workspace\" layer in which to accomplish builds. This layer is not specific to any single `devtool` command but is rather a common working area used across the tool.

> `Devtool` 使用一个“工作区”层来完成构建。这个层不是特定于任何单个 `devtool` 命令的，而是被工具广泛使用的共同工作区。

The following figure shows the workspace structure:

![image](figures/build-workspace-directory.png)

```none
attic - A directory created if devtool believes it must preserve
        anything when you run "devtool reset".  For example, if you
        run "devtool add", make changes to the recipe, and then
        run "devtool reset", devtool takes notice that the file has
        been changed and moves it into the attic should you still
        want the recipe.

README - Provides information on what is in workspace layer and how to
         manage it.

.devtool_md5 - A checksum file used by devtool.

appends - A directory that contains *.bbappend files, which point to
          external source.

conf - A configuration directory that contains the layer.conf file.

recipes - A directory containing recipes.  This directory contains a
          folder for each directory added whose name matches that of the
          added recipe.  devtool places the recipe.bb file
          within that sub-directory.

sources - A directory containing a working copy of the source files used
          when building the recipe.  This is the default directory used
          as the location of the source tree when you do not provide a
          source tree path.  This directory contains a folder for each
          set of source files matched to a corresponding recipe.
```

# Adding a New Recipe to the Workspace Layer {#devtool-adding-a-new-recipe-to-the-workspace}

Use the `devtool add` command to add a new recipe to the workspace layer. The recipe you add should not exist \-\-- `devtool` creates it for you. The source files the recipe uses should exist in an external area.

> 使用 `devtool add` 命令将新食谱添加到工作区层。您添加的食谱不应该存在--`devtool` 会为您创建它。使用的源文件应该存在于外部区域中。

The following example creates and adds a new recipe named `jackson` to a workspace layer the tool creates. The source code built by the recipes resides in `/home/user/sources/jackson`:

> 以下示例创建并向工具创建的工作区层添加一个名为 `jackson` 的新配方。由配方构建的源代码位于 `/home/user/sources/jackson`：

```
$ devtool add jackson /home/user/sources/jackson
```

If you add a recipe and the workspace layer does not exist, the command creates the layer and populates it as described in \"`devtool-the-workspace-layer-structure`{.interpreted-text role="ref"}\" section.

> 如果您添加了一个食谱，但工作空间层不存在，该命令将创建该层并按照“devtool-the-workspace-layer-structure”部分中的描述填充它。

Running `devtool add` when the workspace layer exists causes the tool to add the recipe, append files, and source files into the existing workspace layer. The `.bbappend` file is created to point to the external source tree.

> 运行 `devtool add` 时，如果工作区层已存在，则会将该工具添加到现有的工作区层中，包括添加配方、附加文件和源文件。将创建 `.bbappend` 文件以指向外部源树。

::: note
::: title
Note
:::

If your recipe has runtime dependencies defined, you must be sure that these packages exist on the target hardware before attempting to run your application. If dependent packages (e.g. libraries) do not exist on the target, your application, when run, will fail to find those functions. For more information, see the \"`ref-manual/devtool-reference:deploying your software on the target machine`{.interpreted-text role="ref"}\" section.

> 如果您的配方定义了运行时依赖项，则必须确保这些软件包在尝试运行应用程序之前存在于目标硬件上。如果依赖软件包（例如库）不存在于目标上，则在运行时，您的应用程序将无法找到这些函数。有关更多信息，请参见“ref-manual / devtool-reference：在目标机器上部署您的软件”部分。
> :::

By default, `devtool add` uses the latest revision (i.e. master) when unpacking files from a remote URI. In some cases, you might want to specify a source revision by branch, tag, or commit hash. You can specify these options when using the `devtool add` command:

> 默认情况下，使用 `devtool add` 从远程 URI 解压文件时，会使用最新的版本（即 master）。在某些情况下，您可能希望通过分支，标签或提交哈希来指定源版本。您可以在使用 `devtool add` 命令时指定这些选项：

- To specify a source branch, use the `--srcbranch` option:

  ```
  $ devtool add --srcbranch &DISTRO_NAME_NO_CAP; jackson /home/user/sources/jackson
  ```

  In the previous example, you are checking out the &DISTRO_NAME_NO_CAP; branch.
- To specify a specific tag or commit hash, use the `--srcrev` option:

  ```
  $ devtool add --srcrev &DISTRO_REL_TAG; jackson /home/user/sources/jackson
  $ devtool add --srcrev some_commit_hash /home/user/sources/jackson
  ```

  The previous examples check out the &DISTRO_REL_TAG; tag and the commit associated with the some_commit_hash hash.

::: note
::: title
Note
:::

If you prefer to use the latest revision every time the recipe is built, use the options `--autorev` or `-a`.
:::

# Extracting the Source for an Existing Recipe {#devtool-extracting-the-source-for-an-existing-recipe}

Use the `devtool extract` command to extract the source for an existing recipe. When you use this command, you must supply the root name of the recipe (i.e. no version, paths, or extensions), and you must supply the directory to which you want the source extracted.

> 使用 `devtool extract` 命令提取现有配方的源代码。使用此命令时，您必须提供配方的根名称（即没有版本，路径或扩展），并且您必须提供要提取源代码的目录。

Additional command options let you control the name of a development branch into which you can checkout the source and whether or not to keep a temporary directory, which is useful for debugging.

> 附加命令选项可以帮助您控制可以检出源代码的开发分支的名称，以及是否保留临时目录（用于调试时很有用）。

# Synchronizing a Recipe\'s Extracted Source Tree {#devtool-synchronizing-a-recipes-extracted-source-tree}

Use the `devtool sync` command to synchronize a previously extracted source tree for an existing recipe. When you use this command, you must supply the root name of the recipe (i.e. no version, paths, or extensions), and you must supply the directory to which you want the source extracted.

> 使用 `devtool sync` 命令来同步已经提取的现有配方的源树。使用这个命令时，你必须提供配方的根名（即没有版本、路径或扩展名），并且你必须提供要提取源的目录。

Additional command options let you control the name of a development branch into which you can checkout the source and whether or not to keep a temporary directory, which is useful for debugging.

> 附加的命令选项可以帮助您控制要检出源的开发分支的名称，以及是否保留临时目录，这对调试非常有用。

# Modifying an Existing Recipe {#devtool-modifying-a-recipe}

Use the `devtool modify` command to begin modifying the source of an existing recipe. This command is very similar to the `add <devtool-adding-a-new-recipe-to-the-workspace>`{.interpreted-text role="ref"} command except that it does not physically create the recipe in the workspace layer because the recipe already exists in an another layer.

> 使用 `devtool modify` 命令开始修改现有配方的源代码。这个命令与 `add <devtool-adding-a-new-recipe-to-the-workspace>`{.interpreted-text role="ref"}命令非常相似，但它不会在工作空间层中物理创建配方，因为配方已经存在于另一个层中。

The `devtool modify` command extracts the source for a recipe, sets it up as a Git repository if the source had not already been fetched from Git, checks out a branch for development, and applies any patches from the recipe as commits on top. You can use the following command to checkout the source files:

> `devtool modify` 命令可以提取菜谱的源码，如果源码尚未从 Git 获取，可以将其设置为 Git 存储库，为开发检出一个分支，并将菜谱中的任何补丁作为提交应用。您可以使用以下命令检出源文件：

```
$ devtool modify recipe
```

Using the above command form, `devtool` uses the existing recipe\'s `SRC_URI`{.interpreted-text role="term"} statement to locate the upstream source, extracts the source into the default sources location in the workspace. The default development branch used is \"devtool\".

> 使用上述命令格式，`devtool` 使用现有配方的 `SRC_URI` 声明在工作区中定位上游源，并将源提取到默认源位置。默认使用的开发分支是“devtool”。

# Edit an Existing Recipe {#devtool-edit-an-existing-recipe}

Use the `devtool edit-recipe` command to run the default editor, which is identified using the `EDITOR` variable, on the specified recipe.

When you use the `devtool edit-recipe` command, you must supply the root name of the recipe (i.e. no version, paths, or extensions). Also, the recipe file itself must reside in the workspace as a result of the `devtool add` or `devtool upgrade` commands.

> 当你使用 `devtool edit-recipe` 命令时，你必须提供菜谱的根名称（即没有版本、路径或扩展名）。此外，菜谱文件本身必须作为 `devtool add` 或 `devtool upgrade` 命令的结果存在于工作区中。

# Updating a Recipe {#devtool-updating-a-recipe}

Use the `devtool update-recipe` command to update your recipe with patches that reflect changes you make to the source files. For example, if you know you are going to work on some code, you could first use the `devtool modify <devtool-modifying-a-recipe>`{.interpreted-text role="ref"} command to extract the code and set up the workspace. After which, you could modify, compile, and test the code.

> 使用 `devtool update-recipe` 命令来更新您的配方，以反映您对源文件所做的更改。例如，如果您知道要编辑一些代码，可以首先使用 `devtool modify <devtool-modifying-a-recipe>`{.interpreted-text role="ref"}命令提取代码并设置工作区。之后，您可以修改、编译和测试代码。

When you are satisfied with the results and you have committed your changes to the Git repository, you can then run the `devtool update-recipe` to create the patches and update the recipe:

> 当您对结果满意并且已将更改提交到 Git 存储库时，您可以运行 `devtool update-recipe` 来创建补丁并更新配方：

```
$ devtool update-recipe recipe
```

If you run the `devtool update-recipe` without committing your changes, the command ignores the changes.

Often, you might want to apply customizations made to your software in your own layer rather than apply them to the original recipe. If so, you can use the `-a` or `--append` option with the `devtool update-recipe` command. These options allow you to specify the layer into which to write an append file:

> 通常，您可能希望将对软件所做的自定义应用于自己的层，而不是将其应用于原始配方。如果是这样，您可以使用 `-a` 或 `--append` 选项与 `devtool update-recipe` 命令一起使用。这些选项允许您指定要写入附加文件的层：

```
$ devtool update-recipe recipe -a base-layer-directory
```

The `*.bbappend` file is created at the appropriate path within the specified layer directory, which may or may not be in your `bblayers.conf` file. If an append file already exists, the command updates it appropriately.

> `.bbappend` 文件会在指定层级目录下的适当路径创建，这可能或可能不在您的 `bblayers.conf` 文件中。如果附加文件已存在，则该命令会适当更新它。

# Checking on the Upgrade Status of a Recipe {#devtool-checking-on-the-upgrade-status-of-a-recipe}

Upstream recipes change over time. Consequently, you might find that you need to determine if you can upgrade a recipe to a newer version.

To check on the upgrade status of a recipe, you can use the `devtool latest-version recipe` command, which quickly shows the current version and the latest version available upstream. To get a more global picture, use the `devtool check-upgrade-status` command, which takes a list of recipes as input, or no arguments, in which case it checks all available recipes. This command will only print the recipes for which a new upstream version is available. Each such recipe will have its current version and latest upstream version, as well as the email of the maintainer and any additional information such as the commit hash or reason for not being able to upgrade it, displayed in a table.

> 要检查食谱升级状态，您可以使用 `devtool latest-version recipe` 命令，它可以快速显示当前版本和上游可用的最新版本。要获得更全面的图景，请使用 `devtool check-upgrade-status` 命令，它接受一个食谱列表作为输入，或者不带参数，在这种情况下，它会检查所有可用的食谱。此命令仅会打印可以升级到新的上游版本的食谱。每个这样的食谱都会显示其当前版本和最新的上游版本，以及维护者的电子邮件以及任何附加信息（如提交哈希或无法升级的原因），都会显示在表格中。

This upgrade checking mechanism relies on the optional `UPSTREAM_CHECK_URI`{.interpreted-text role="term"}, `UPSTREAM_CHECK_REGEX`{.interpreted-text role="term"}, `UPSTREAM_CHECK_GITTAGREGEX`{.interpreted-text role="term"}, `UPSTREAM_CHECK_COMMITS`{.interpreted-text role="term"} and `UPSTREAM_VERSION_UNKNOWN`{.interpreted-text role="term"} variables in package recipes.

> 这个升级检查机制依赖于包配方中的可选变量 UPSTREAM_CHECK_URI、UPSTREAM_CHECK_REGEX、UPSTREAM_CHECK_GITTAGREGEX、UPSTREAM_CHECK_COMMITS 和 UPSTREAM_VERSION_UNKNOWN。

::: note
::: title
Note
:::

- Most of the time, the above variables are unnecessary. They are only required when upstream does something unusual, and default mechanisms cannot find the new upstream versions.

> 大多数时候，上面的变量是不必要的。只有当上游做了一些不寻常的事情，默认机制无法找到新的上游版本时，才需要它们。

- For the `oe-core` layer, recipe maintainers come from the :yocto\_[git:%60maintainers.inc](git:%60maintainers.inc) \</poky/tree/meta/conf/distro/include/maintainers.inc\>\` file.

> 对于 `oe-core` 层，食谱维护者来自：yocto\_[git:%60maintainers.inc](git:%60maintainers.inc) \</poky/tree/meta/conf/distro/include/maintainers.inc\>\` 文件。

- If the recipe is using the ``bitbake-user-manual/bitbake-user-manual-fetching:git fetcher (\`\`git://\`\`)``{.interpreted-text role="ref"} rather than a tarball, the commit hash points to the commit that matches the recipe\'s latest version tag, or in the absence of suitable tags, to the latest commit (when `UPSTREAM_CHECK_COMMITS`{.interpreted-text role="term"} set to `1` in the recipe).

> 如果食谱使用 ``bitbake-user-manual/bitbake-user-manual-fetching:git fetcher (\`\`git://\`\`)``{.interpreted-text role="ref"}而不是压缩包，提交哈希指向与食谱最新版本标签匹配的提交，或者在没有合适标签的情况下，指向食谱中设置 `UPSTREAM_CHECK_COMMITS`{.interpreted-text role="term"}为 `1` 时的最新提交。
> :::

As with all `devtool` commands, you can get help on the individual command:

```
$ devtool check-upgrade-status -h
NOTE: Starting bitbake server...
usage: devtool check-upgrade-status [-h] [--all] [recipe [recipe ...]]

Prints a table of recipes together with versions currently provided by recipes, and latest upstream versions, when there is a later version available

arguments:
  recipe      Name of the recipe to report (omit to report upgrade info for all recipes)

options:
  -h, --help  show this help message and exit
  --all, -a   Show all recipes, not just recipes needing upgrade
```

Unless you provide a specific recipe name on the command line, the command checks all recipes in all configured layers.

Following is a partial example table that reports on all the recipes:

```
$ devtool check-upgrade-status
...
INFO: bind                      9.16.20         9.16.21         Armin Kuster <akuster808@gmail.com>
INFO: inetutils                 2.1             2.2             Tom Rini <trini@konsulko.com>
INFO: iproute2                  5.13.0          5.14.0          Changhyeok Bae <changhyeok.bae@gmail.com>
INFO: openssl                   1.1.1l          3.0.0           Alexander Kanavin <alex.kanavin@gmail.com>
INFO: base-passwd               3.5.29          3.5.51          Anuj Mittal <anuj.mittal@intel.com>  cannot be updated due to: Version 3.5.38 requires cdebconf for update-passwd utility
...
```

Notice the reported reason for not upgrading the `base-passwd` recipe. In this example, while a new version is available upstream, you do not want to use it because the dependency on `cdebconf` is not easily satisfied. Maintainers can explicit the reason that is shown by adding the `RECIPE_NO_UPDATE_REASON`{.interpreted-text role="term"} variable to the corresponding recipe. See :yocto\_[git:%60base-passwd.bb](git:%60base-passwd.bb) \</poky/tree/meta/recipes-core/base-passwd/base-passwd_3.5.29.bb?h=kirkstone\>\` for an example:

> 注意不升级 `base-passwd` 食谱的报告原因。在这个例子中，虽然上游有新版本可用，但您不想使用它，因为 `cdebconf` 的依赖关系不易满足。维护者可以通过将 `RECIPE_NO_UPDATE_REASON`{.interpreted-text role="term"}变量添加到相应的食谱来显式表明该原因。有关示例，请参见：yocto_[git:`base-passwd.bb`](git:%60base-passwd.bb%60)\</poky/tree/meta/recipes-core/base-passwd/base-passwd_3.5.29.bb?h=kirkstone\>\`

```
RECIPE_NO_UPDATE_REASON = "Version 3.5.38 requires cdebconf for update-passwd utility"
```

Last but not least, you may set `UPSTREAM_VERSION_UNKNOWN`{.interpreted-text role="term"} to `1` in a recipe when there\'s currently no way to determine its latest upstream version.

> 最后但并非最不重要的是，当目前无法确定其最新的上游版本时，您可以在配方中将 `UPSTREAM_VERSION_UNKNOWN` 设置为 `1`。

# Upgrading a Recipe {#devtool-upgrading-a-recipe}

As software matures, upstream recipes are upgraded to newer versions. As a developer, you need to keep your local recipes up-to-date with the upstream version releases. There are several ways of upgrading recipes. You can read about them in the \"`dev-manual/upgrading-recipes:upgrading recipes`{.interpreted-text role="ref"}\" section of the Yocto Project Development Tasks Manual. This section overviews the `devtool upgrade` command.

> 随着软件成熟，上游食谱会升级到更新的版本。作为开发者，您需要保持本地食谱与上游版本发布保持一致。有几种升级食谱的方法。您可以在 Yocto 项目开发任务手册的“dev-manual/upgrading-recipes：升级食谱”部分阅读有关信息。该部分概述了 `devtool upgrade` 命令。

Before you upgrade a recipe, you can check on its upgrade status. See the \"`devtool-checking-on-the-upgrade-status-of-a-recipe`{.interpreted-text role="ref"}\" section for more information.

> 在升级食谱之前，您可以检查其升级状态。有关详细信息，请参阅“devtool-checking-on-the-upgrade-status-of-a-recipe”部分。

The `devtool upgrade` command upgrades an existing recipe to a more recent version of the recipe upstream. The command puts the upgraded recipe file along with any associated files into a \"workspace\" and, if necessary, extracts the source tree to a specified location. During the upgrade, patches associated with the recipe are rebased or added as needed.

> 命令 `devtool upgrade` 可以将现有的食谱升级到更新的食谱版本。该命令将升级的食谱文件以及任何相关文件放入“工作区”，如果需要，还可以将源树抽取到指定位置。在升级的过程中，与食谱相关的补丁将被重新基于或添加。

When you use the `devtool upgrade` command, you must supply the root name of the recipe (i.e. no version, paths, or extensions), and you must supply the directory to which you want the source extracted. Additional command options let you control things such as the version number to which you want to upgrade (i.e. the `PV`{.interpreted-text role="term"}), the source revision to which you want to upgrade (i.e. the `SRCREV`{.interpreted-text role="term"}), whether or not to apply patches, and so forth.

> 当你使用 `devtool upgrade` 命令时，你必须提供食谱的根名（即不带有版本、路径或扩展名），并且你必须提供你想要提取源码的目录。额外的命令选项可以让你控制诸如你想要升级到的版本号（即 `PV`{.interpreted-text role="term"}）、你想要升级到的源代码版本（即 `SRCREV`{.interpreted-text role="term"}）、是否应用补丁等等。

You can read more on the `devtool upgrade` workflow in the \"``sdk-manual/extensible:use \`\`devtool upgrade\`\` to create a version of the recipe that supports a newer version of the software``{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual. You can also see an example of how to use `devtool upgrade` in the \"``dev-manual/upgrading-recipes:using \`\`devtool upgrade\`\` ``{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 你可以在 Yocto 项目应用开发和可扩展软件开发工具（eSDK）手册中的“ devtool upgrade”工作流程部分阅读更多内容。您还可以在 Yocto 项目开发任务手册中的“使用 devtool upgrade”部分查看如何使用 devtool upgrade 的示例。

# Resetting a Recipe {#devtool-resetting-a-recipe}

Use the `devtool reset` command to remove a recipe and its configuration (e.g. the corresponding `.bbappend` file) from the workspace layer. Realize that this command deletes the recipe and the append file. The command does not physically move them for you. Consequently, you must be sure to physically relocate your updated recipe and the append file outside of the workspace layer before running the `devtool reset` command.

> 使用 `devtool reset` 命令可以删除工作层中的一个配方及其配置（例如相应的 `.bbappend` 文件）。请注意，此命令会删除配方和附加文件。该命令不会物理移动它们。因此，在运行 `devtool reset` 命令之前，您必须确保将更新后的配方和附加文件物理移动到工作层之外。

If the `devtool reset` command detects that the recipe or the append files have been modified, the command preserves the modified files in a separate \"attic\" subdirectory under the workspace layer.

> 如果 `devtool reset` 命令检测到配方或附加文件已被修改，该命令将在工作区层下的单独“attic”子目录中保留修改后的文件。

Here is an example that resets the workspace directory that contains the `mtr` recipe:

```
$ devtool reset mtr
NOTE: Cleaning sysroot for recipe mtr...
NOTE: Leaving source tree /home/scottrif/poky/build/workspace/sources/mtr as-is; if you no longer need it then please delete it manually
$
```

# Building Your Recipe {#devtool-building-your-recipe}

Use the `devtool build` command to build your recipe. The `devtool build` command is equivalent to the `bitbake -c populate_sysroot` command.

When you use the `devtool build` command, you must supply the root name of the recipe (i.e. do not provide versions, paths, or extensions). You can use either the `-s` or the `--disable-parallel-make` options to disable parallel makes during the build. Here is an example:

> 当你使用 `devtool build` 命令时，你必须提供食谱的根名称（即不提供版本、路径或扩展名）。您可以使用 `-s` 或 `--disable-parallel-make` 选项来禁用构建期间的并行制作。这里有一个例子：

```
$ devtool build recipe
```

# Building Your Image {#devtool-building-your-image}

Use the `devtool build-image` command to build an image, extending it to include packages from recipes in the workspace. Using this command is useful when you want an image that ready for immediate deployment onto a device for testing. For proper integration into a final image, you need to edit your custom image recipe appropriately.

> 使用 `devtool build-image` 命令来构建一个镜像，并将工作区中的配方中的软件包扩展到镜像中。当你想要一个可以立即部署到设备进行测试的镜像时，使用这个命令很有用。为了能够正确地集成到最终镜像中，你需要适当地编辑自定义镜像配方。

When you use the `devtool build-image` command, you must supply the name of the image. This command has no command line options:

```
$ devtool build-image image
```

# Deploying Your Software on the Target Machine {#devtool-deploying-your-software-on-the-target-machine}

Use the `devtool deploy-target` command to deploy the recipe\'s build output to the live target machine:

```
$ devtool deploy-target recipe target
```

The target is the address of the target machine, which must be running an SSH server (i.e. `user@hostname[:destdir]`).

This command deploys all files installed during the `ref-tasks-install`{.interpreted-text role="ref"} task. Furthermore, you do not need to have package management enabled within the target machine. If you do, the package manager is bypassed.

> 这个命令部署在“ref-tasks-install”任务期间安装的所有文件。此外，您不需要在目标机器上启用包管理。如果您这样做，则会绕过包管理器。

::: note
::: title
Note
:::

The `deploy-target` functionality is for development only. You should never use it to update an image that will be used in production.
:::

Some conditions could prevent a deployed application from behaving as expected. When both of the following conditions are met, your application has the potential to not behave correctly when run on the target:

> 当满足以下两个条件时，您的应用程序在目标上运行时可能会出现不正常的行为：一些条件可能会阻止部署的应用程序按预期运行。

- You are deploying a new application to the target and the recipe you used to build the application had correctly defined runtime dependencies.
- The target does not physically have the packages on which the application depends installed.

If both of these conditions are met, your application will not behave as expected. The reason for this misbehavior is because the `devtool deploy-target` command does not deploy the packages (e.g. libraries) on which your new application depends. The assumption is that the packages are already on the target. Consequently, when a runtime call is made in the application for a dependent function (e.g. a library call), the function cannot be found.

> 如果这两个条件都满足，您的应用程序将无法按预期工作。此外行为的原因是 `devtool deploy-target` 命令不部署新应用程序所依赖的包（例如库）。假设这些包已经在目标上。因此，当在应用程序中调用依赖函数（例如库调用）时，无法找到该函数。

To be sure you have all the dependencies local to the target, you need to be sure that the packages are pre-deployed (installed) on the target before attempting to run your application.

> 确保您在目标上有所有依赖项，您需要确保在尝试运行应用程序之前，在目标上已经部署（安装）了这些软件包。

# Removing Your Software from the Target Machine {#devtool-removing-your-software-from-the-target-machine}

Use the `devtool undeploy-target` command to remove deployed build output from the target machine. For the `devtool undeploy-target` command to work, you must have previously used the \"`devtool deploy-target <ref-manual/devtool-reference:deploying your software on the target machine>`{.interpreted-text role="ref"}\" command:

> 使用 `devtool undeploy-target` 命令可以从目标机器上移除部署的构建输出。要使 `devtool undeploy-target` 命令正常工作，您必须先使用“`devtool deploy-target <ref-manual/devtool-reference:deploying your software on the target machine>`{.interpreted-text role="ref"}”命令：

```
$ devtool undeploy-target recipe target
```

The target is the address of the target machine, which must be running an SSH server (i.e. `user@hostname`).

# Creating the Workspace Layer in an Alternative Location {#devtool-creating-the-workspace}

Use the `devtool create-workspace` command to create a new workspace layer in your `Build Directory`{.interpreted-text role="term"}. When you create a new workspace layer, it is populated with the `README` file and the `conf` directory only.

> 使用 `devtool create-workspace` 命令在您的 `构建目录` 中创建一个新的工作区层。创建新的工作区层时，只会填充 `README` 文件和 `conf` 目录。

The following example creates a new workspace layer in your current working and by default names the workspace layer \"workspace\":

```
$ devtool create-workspace
```

You can create a workspace layer anywhere by supplying a pathname with the command. The following command creates a new workspace layer named \"new-workspace\":

> 你可以通过提供命令路径名称在任何地方创建一个工作区层。以下命令创建一个名为“new-workspace”的新工作区层：

```
$ devtool create-workspace /home/scottrif/new-workspace
```

# Get the Status of the Recipes in Your Workspace {#devtool-get-the-status-of-the-recipes-in-your-workspace}

Use the `devtool status` command to list the recipes currently in your workspace. Information includes the paths to their respective external source trees.

> 使用 `devtool status` 命令可以列出当前工作区中的菜谱。信息包括它们各自的外部源树的路径。

The `devtool status` command has no command-line options:

```
$ devtool status
```

Following is sample output after using `devtool add <ref-manual/devtool-reference:adding a new recipe to the workspace layer>`{.interpreted-text role="ref"} to create and add the `mtr_0.86.bb` recipe to the `workspace` directory:

> 以下是使用 `devtool add <ref-manual/devtool-reference:adding a new recipe to the workspace layer>`{.interpreted-text role="ref"} 创建并添加 `mtr_0.86.bb` 配方到 `workspace` 目录后的示例输出：

```
$ devtool status
mtr:/home/scottrif/poky/build/workspace/sources/mtr (/home/scottrif/poky/build/workspace/recipes/mtr/mtr_0.86.bb)
$
```

# Search for Available Target Recipes {#devtool-search-for-available-target-recipes}

Use the `devtool search` command to search for available target recipes. The command matches the recipe name, package name, description, and installed files. The command displays the recipe name as a result of a match.

> 使用 `devtool search` 命令搜索可用的目标配方。该命令会匹配配方名称、软件包名称、描述和已安装的文件。命令会显示配方名称作为匹配结果。

When you use the `devtool search` command, you must supply a keyword. The command uses the keyword when searching for a match.
