---
tip: translate by openai@2023-06-07 22:45:19
...
---
title: Tasks
------------

Tasks are units of execution for BitBake. Recipes (`.bb` files) use tasks to complete configuring, compiling, and packaging software. This chapter provides a reference of the tasks defined in the OpenEmbedded build system.

> 任务是 BitBake 的执行单元。recipes(`.bb` 文件)使用任务来完成配置、编译和打包软件。本章提供了 OpenEmbedded 构建系统中定义的任务的参考。

# Normal Recipe Build Tasks

The following sections describe normal tasks associated with building a recipe. For more information on tasks and dependencies, see the \"`bitbake-user-manual/bitbake-user-manual-metadata:tasks`\" sections in the BitBake User Manual.

> 以下部分描述了与构建配方相关的常规任务。有关任务和依赖关系的更多信息，请参阅 BitBake 用户手册中的“bitbake-user-manual / bitbake-user-manual-metadata：tasks”和“bitbake-user-manual / bitbake-user-manual-execution：dependencies”部分。

## `do_build`

The default task for all recipes. This task depends on all other normal tasks required to build a recipe.

## `do_compile`

Compiles the source code. This task runs with the current working directory set to `$`.

The default behavior of this task is to run the `oe_runmake` function if a makefile (`Makefile`, `makefile`, or `GNUmakefile`) is found. If no such file is found, the `ref-tasks-compile` task does nothing.

> 默认行为是，如果找到 Makefile、makefile 或 GNUmakefile，则运行 oe_runmake 函数。如果没有找到这样的文件，ref-tasks-compile 任务则不执行任何操作。

## `do_compile_ptest_base`

Compiles the runtime test suite included in the software being built.

## `do_configure`

Configures the source by enabling and disabling any build-time and configuration options for the software being built. The task runs with the current working directory set to `$`.

> 配置源，通过启用和禁用正在构建的软件的任何构建时和配置选项。该任务以当前工作目录设置为 `$` 运行。

The default behavior of this task is to run `oe_runmake clean` if a makefile (`Makefile`, `makefile`, or `GNUmakefile`) is found and `CLEANBROKEN` task does nothing.

> 默认行为是，如果发现 makefile(Makefile、makefile 或 GNUmakefile)且 CLEANBROKEN 变量未设置为“1”，则运行 oe_runmake clean。如果没有发现此类文件或者 CLEANBROKEN 变量设置为“1”，ref-tasks-configure 任务将不执行任何操作。

## `do_configure_ptest_base`

Configures the runtime test suite included in the software being built.

## `do_deploy`

Writes output files that are to be deployed to `$`.

> 将输出文件写入 $。

Recipes implementing this task should inherit the `ref-classes-deploy``.

> 需要实现此任务的菜谱应继承 `ref-classes-deploy``。

::: note
::: title
Note
:::

Do not write the output directly to `$`, as this causes the sstate mechanism to malfunction.
:::

The `ref-tasks-deploy`, you can add it by doing the following:

> `ref-tasks-deploy` 之后运行，你可以通过以下方式添加：

```
addtask deploy after do_compile
```

Adding `ref-tasks-deploy` after other tasks works the same way.

::: note
::: title
Note
:::

You do not need to add `before do_build` to the `addtask` command (though it is harmless), because the `ref-classes-base` class contains the following:

> 你不需要在 `addtask` 命令中添加 `do_build` 之前的 `ref-classes-base` 类(尽管它是无害的)，因为该类包含以下内容：

```
do_build[recrdeptask] += "do_deploy"
```

See the \"`bitbake-user-manual/bitbake-user-manual-execution:dependencies`\" section in the BitBake User Manual for more information.

> 请参阅 BitBake 用户手册中的“bitbake-user-manual/bitbake-user-manual-execution：dependencies”部分以获取更多信息。
> :::

If the `ref-tasks-deploy` task re-executes, any previous output is removed (i.e. \"cleaned\").

## `do_fetch`

Fetches the source code. This task uses the `SRC_URI` module.

> 取得源代码。此任务使用 `SRC_URI` 变量和参数的前缀来确定正确的 `fetcher` 模块。

## `do_image`

Starts the image generation process. The `ref-tasks-image` task during which packages are identified for installation into the image and the root filesystem is created, complete with post-processing.

> 开始镜像生成过程。在 OpenEmbedded 构建系统运行 `ref-tasks-rootfs` 任务会运行，其中确定要安装到镜像中的软件包，并创建完整的根文件系统，包括后处理。

The `ref-tasks-image` tasks as needed.

> `ref-tasks-image` 任务。

For more information on image creation, see the \"`overview-manual/concepts:image generation`\" section in the Yocto Project Overview and Concepts Manual.

> 欲了解有关镜像创建的更多信息，请参见 Yocto 项目概述与概念手册中的“概览手册/概念：镜像生成”部分。

## `do_image_complete`

Completes the image generation process. The `do_image_complete <ref-tasks-image-complete>` tasks the image is constructed.

> 完成镜像生成过程。在 OpenEmbedded 构建系统运行 `ref-tasks-image` 任务将在此后运行。

The `do_image_complete <ref-tasks-image-complete>`.

> 任务 `do_image_complete <ref-tasks-image-complete>` 执行镜像的后处理。

For more information on image creation, see the \"`overview-manual/concepts:image generation`\" section in the Yocto Project Overview and Concepts Manual.

> 欲了解有关镜像创建的更多信息，请参阅 Yocto 项目概览和概念手册中的“overview-manual/concepts：image generation”部分。

## `do_install`

Copies files that are to be packaged into the holding area `$.

> 复制要打包的文件到暂存区 $。ref-tasks-install 任务以及其他直接或间接依赖已安装文件的任务(例如 ref-tasks-package、do_package_write_* <ref-tasks-package_write_deb> 和 ref-tasks-rootfs)在 fakeroot <overview-manual/concepts:fakeroot and pseudo> 下运行。

::: note
::: title
Note
:::

When installing files, be careful not to set the owner and group IDs of the installed files to unintended values. Some methods of copying files, notably when using the recursive `cp` command, can preserve the UID and/or GID of the original file, which is usually not what you want. The `host-user-contaminated` QA check checks for files that probably have the wrong ownership.

> 在安装文件时，要小心不要将安装文件的所有者和组 ID 设置为意外的值。某些复制文件的方法，特别是使用递归 `cp` 命令时，可以保留原始文件的 UID 和/或 GID，这通常不是您想要的。`host-user-contaminated` QA 检查可检查可能具有错误所有权的文件。

Safe methods for installing files include the following:

- The `install` utility. This utility is the preferred method.
- The `cp` command with the `--no-preserve=ownership` option.
- The `tar` command with the `--no-same-owner` option. See the `bin_package.bbclass` file in the `meta/classes-recipe` subdirectory of the `Source Directory` for an example.

> 命令 `tar` 配合 `--no-same-owner` 选项。参考 `Source Directory` 中 `meta/classes-recipe` 子目录下的 `bin_package.bbclass` 文件以获得示例。
> :::

## `do_install_ptest_base`

Copies the runtime test suite files from the compilation directory to a holding area.

## `do_package`

Analyzes the content of the holding area `$ variables.

> 分析持有区域的内容 `$ 变量。

The `ref-tasks-package`\" section in the Yocto Project Overview and Concepts Manual.

> `ref-tasks-package`”部分。

## `do_package_qa`

Runs QA checks on packaged files. For more information on these checks, see the `ref-classes-insane` class.

## `do_package_write_deb`

Creates Debian packages (i.e. `*.deb` files) and places them in the `$\" section in the Yocto Project Overview and Concepts Manual.

> 创建 Debian 软件包(即 `*.deb` 文件)，并将其放置在软件包发布区域的 `$` 目录中。有关更多信息，请参阅 Yocto 项目概述和概念手册中的“概述-手册/概念：软件包发布”部分。

## `do_package_write_ipk`

Creates IPK packages (i.e. `*.ipk` files) and places them in the `$\" section in the Yocto Project Overview and Concepts Manual.

> 创建 IPK 包(即 `*.ipk` 文件)，并将它们放置在包含包的发布目录 `$` 中。有关更多信息，请参阅 Yocto 项目概述和概念手册中的“overview-manual/concepts：package feeds”部分。

## `do_package_write_rpm`

Creates RPM packages (i.e. `*.rpm` files) and places them in the `$\" section in the Yocto Project Overview and Concepts Manual.

> 创建 RPM 软件包(即 `*.rpm` 文件)，并将其放置在软件包发布区域的 `$` 目录中。有关更多信息，请参阅 Yocto 项目概述和概念手册中的“概览手册/概念：软件包源”部分。

## `do_packagedata`

Saves package metadata generated by the `ref-tasks-package` to make it available globally.

> 将由 `ref-tasks-package` 任务生成的包元数据保存在 `PKGDATA_DIR` 中，以使其可以全局使用。

## `do_patch`

Locates patch files and applies them to the source code.

After fetching and unpacking source files, the build system uses the recipe\'s `SRC_URI` statements to locate and apply patch files to the source code.

> 在获取和解压源文件之后，构建系统使用 recipes 的“SRC_URI”语句来定位并将补丁文件应用到源代码中。

::: note
::: title
Note
:::

The build system uses the `FILESPATH` variable to determine the default set of directories when searching for patches.

> 系统构建使用 `FILESPATH` 变量来确定搜索补丁时的默认目录集合。
> :::

Patch files, by default, are `*.patch` and `*.diff` files created and kept in a subdirectory of the directory holding the recipe file. For example, consider the :yocto_[git:%60bluez5](git:%60bluez5) \</poky/tree/meta/recipes-connectivity/bluez5\>[ recipe from the OE-Core layer (i.e. ]\`):

> 默认情况下，补丁文件是位于与配方文件所在的目录下的子目录中创建并保存的*.patch 和*.diff 文件。例如，考虑 OE-Core 层(即 poky/meta)中的：yocto_[git：％60bluez5](git%EF%BC%9A%EF%BC%8560bluez5)\ </ poky / tree / meta / recipes-connectivity / bluez5> [配方]：

```
poky/meta/recipes-connectivity/bluez5
```

This recipe has two patch files located here:

```
poky/meta/recipes-connectivity/bluez5/bluez5
```

In the `bluez5` recipe, the `SRC_URI` statements point to the source and patch files needed to build the package.

::: note
::: title
Note
:::

In the case for the `bluez5_5.48.bb` recipe, the `SRC_URI` statements are from an include file `bluez5.inc`.
:::

As mentioned earlier, the build system treats files whose file types are `.patch` and `.diff` as patch files. However, you can use the \"apply=yes\" parameter with the `SRC_URI` statement to indicate any file as a patch file:

> 正如前面提到的，构建系统将文件类型为 `.patch` 和 `.diff` 的文件视为补丁文件。但是，您可以使用 `SRC_URI` 语句中的“apply=yes”参数指示任何文件作为补丁文件：

```
SRC_URI = " \
    git://path_to_repo/some_package \
    file://file;apply=yes \
    "
```

Conversely, if you have a file whose file type is `.patch` or `.diff` and you want to exclude it so that the `ref-tasks-patch` statement:

> 如果您有一个文件类型为 `.patch` 或 `.diff` 的文件，您想要排除它，以便 `ref-tasks-patch` 语句中的“apply=no”参数：

```
SRC_URI = " \
    git://path_to_repo/some_package \
    file://file1.patch \
    file://file2.patch;apply=no \
    "
```

In the previous example `file1.patch` would be applied as a patch by default while `file2.patch` would not be applied.

You can find out more about the patching process in the \"`overview-manual/concepts:patching`\" section in the Yocto Project Development Tasks Manual.

> 你可以在 Yocto Project 概览与概念手册的“概览-手册/概念：补丁”部分中了解更多关于补丁过程的信息。

## `do_populate_lic`

Writes license information for the recipe that is collected later when the image is constructed.

## `do_populate_sdk`

Creates the file and directory structure for an installable SDK. See the \"`overview-manual/concepts:sdk generation`\" section in the Yocto Project Overview and Concepts Manual for more information.

> 创建可安装 SDK 的文件和目录结构。有关更多信息，请参阅 Yocto 项目概述和概念手册中的“概览手册/概念：SDK 生成”部分。

## `do_populate_sdk_ext`

Creates the file and directory structure for an installable extensible SDK (eSDK). See the \"`overview-manual/concepts:sdk generation`\" section in the Yocto Project Overview and Concepts Manual for more information.

> 创建可安装的可扩展 SDK(eSDK)的文件和目录结构。有关更多信息，请参阅 Yocto 项目概述和概念手册中的“概览手册/概念：SDK 生成”部分。

## `do_populate_sysroot`

Stages (copies) a subset of the files installed by the `ref-tasks-install` variables. Directories that would typically not be needed by other recipes at build time (e.g. `/etc`) are not copied by default.

> 将 `ref-tasks-install` 任务安装的一组文件复制到适当的系统根目录中。有关如何从其他配方中访问这些文件的信息，请参阅 `STAGING_DIR* <STAGING_DIR_HOST>` 变量。默认情况下，不会复制其他配方在构建时通常不需要的目录(例如 `/etc`)。

For information on what directories are copied by default, see the `SYSROOT_DIRS* <SYSROOT_DIRS>` variables. You can change these variables inside your recipe if you need to make additional (or fewer) directories available to other recipes at build time.

> 对于默认复制的哪些目录的信息，请参阅 `SYSROOT_DIRS* <SYSROOT_DIRS>` 变量。如果您需要在构建时向其他配方提供更多(或更少)的目录，可以在配方内部更改这些变量。

The `ref-tasks-populate_sysroot` task is a shared state (sstate) task, which means that the task can be accelerated through sstate use. Realize also that if the task is re-executed, any previous output is removed (i.e. \"cleaned\").

> 任务 `ref-tasks-populate_sysroot` 是一个共享状态(sstate)任务，这意味着任务可以通过 sstate 使用加速。也要意识到，如果重新执行任务，则会删除任何先前的输出(即“清理”)。

## `do_prepare_recipe_sysroot`

Installs the files into the individual recipe specific sysroots (i.e. `recipe-sysroot` and `recipe-sysroot-native` under `$\" class for more information.

> 将文件安装到基于 `DEPENDS` 指定的依赖项所指定的各个 recipes 特定的系统根(即 `$` 下的 `recipe-sysroot` 和 `recipe-sysroot-native`)中。有关更多信息，请参见“ref-classes-staging”类。

## `do_rm_work`

Removes work files after the OpenEmbedded build system has finished with them. You can learn more by looking at the \"`ref-classes-rm-work`\" section.

> 在 OpenEmbedded 构建系统完成后，移除工作文件。您可以通过查看“ref-classes-rm-work”部分来了解更多信息。

## `do_unpack`

Unpacks the source code into a working directory pointed to by `$ variable descriptions.

> 将源代码解压缩到由 `$` 指向的工作目录中。变量 `S` 也在最终存放解压缩源文件的位置中发挥作用。有关如何解压缩源文件的更多信息，请参见 Yocto 项目概述和概念手册中的“源获取”部分，还可以参见 `WORKDIR` 和 `S` 变量描述。

# Manually Called Tasks

These tasks are typically manually triggered (e.g. by using the `bitbake -c` command-line option):

## `do_checkuri`

Validates the `SRC_URI` value.

## `do_clean`

Removes all output files for a target from the `ref-tasks-unpack`).

> 移除目标对应的 `ref-tasks-unpack`)。

You can run this task using BitBake as follows:

```
$ bitbake -c clean recipe
```

Running this task does not remove the `sstate <overview-manual/concepts:shared state cache>` task instead (i.e. `bitbake -c cleansstate` recipe).

> 运行此任务不会删除 `sstate <overview-manual/concepts:shared state cache>` 任务(即 `bitbake -c cleansstate` 配方)。

## `do_cleanall`

Removes all output files, shared state (`sstate <overview-manual/concepts:shared state cache>` task with the added removal of downloaded source files.

> 移除所有输出文件、共享状态(sstate <overview-manual/concepts:shared state cache> 任务相同，只是增加了移除下载的源文件。

You can run this task using BitBake as follows:

```
$ bitbake -c cleanall recipe
```

Typically, you would not normally use the `ref-tasks-cleanall` task.

> 一般来说，你不会使用 `ref-tasks-cleanall` 任务重新开始，只有在这种情况下才使用。

## `do_cleansstate`

Removes all output files and shared state (`sstate <overview-manual/concepts:shared state cache>`) cache.

> 移除所有输出文件和共享状态(`sstate <overview-manual/concepts:shared state cache>`)缓存的功能。

You can run this task using BitBake as follows:

```
$ bitbake -c cleansstate recipe
```

When you run the `ref-tasks-cleansstate` task, the OpenEmbedded build system no longer uses any sstate. Consequently, building the recipe from scratch is guaranteed.

> 当你运行 `ref-tasks-cleansstate` 任务时，OpenEmbedded 构建系统不再使用任何 sstate。因此，保证从头开始构建 recipes。

::: note
::: title
Note
:::

The `ref-tasks-cleansstate` task cannot remove sstate from a remote sstate mirror. If you need to build a target from scratch using remote mirrors, use the \"-f\" option as follows:

> `ref-tasks-cleansstate` 任务无法从远程 sstate 镜像中删除 sstate。如果您需要使用远程镜像从头构建目标，请使用“-f”选项，如下所示：

```
$ bitbake -f -c do_cleansstate target
```

:::

## `do_pydevshell`

Starts a shell in which an interactive Python interpreter allows you to interact with the BitBake build environment. From within this shell, you can directly examine and set bits from the data store and execute functions as if within the BitBake environment. See the \"`dev-manual/python-development-shell:using a Python development shell`\" section in the Yocto Project Development Tasks Manual for more information about using `pydevshell`.

> 启动一个 shell，其中的交互式 Python 解释器允许您与 BitBake 构建环境交互。在此 shell 中，您可以直接检查和设置数据存储中的位，并像在 BitBake 环境中一样执行函数。有关使用“pydevshell”的更多信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/python-development-shell：使用 Python 开发 shell”部分。

## `do_devshell`

Starts a shell whose environment is set up for development, debugging, or both. See the \"`dev-manual/development-shell:using a development shell`\" section in the Yocto Project Development Tasks Manual for more information about using `devshell`.

> 开启一个 shell，环境被设置为开发、调试或两者皆有。更多关于使用 `devshell` 的信息，请参阅 Yocto Project 开发任务手册中的“dev-manual/development-shell:使用开发 shell”部分。

## `do_listtasks`

Lists all defined tasks for a target.

## `do_package_index`

Creates or updates the index in the `overview-manual/concepts:package feeds` area.

::: note
::: title
Note
:::

This task is not triggered with the `bitbake -c` command-line option as are the other tasks in this section. Because this task is specifically for the `package-index` recipe, you run it using `bitbake package-index`.

> 这个任务不能像本节中其他任务一样使用 `bitbake -c` 命令行选项触发。因为这个任务专门用于 `package-index` 配方，你可以使用 `bitbake package-index` 来运行它。
> :::

# Image-Related Tasks

The following tasks are applicable to image recipes.

## `do_bootimg`

Creates a bootable live image. See the `IMAGE_FSTYPES` variable for additional information on live image types.

## `do_bundle_initramfs`

Combines an `Initramfs` image and kernel together to form a single image.

## `do_rootfs`

Creates the root filesystem (file and directory structure) for an image. See the \"`overview-manual/concepts:image generation`\" section in the Yocto Project Overview and Concepts Manual for more information on how the root filesystem is created.

> 创建镜像的根文件系统(文件和目录结构)。有关如何创建根文件系统的更多信息，请参阅 Yocto 项目概述和概念手册中的“概述-手册/概念：镜像生成”部分。

## `do_testimage`

Boots an image and performs runtime tests within the image. For information on automatically testing images, see the \"`dev-manual/runtime-testing:performing automated runtime testing`\" section in the Yocto Project Development Tasks Manual.

> 烧录镜像并在镜像内执行运行时测试。有关自动测试镜像的信息，请参阅 Yocto Project 开发任务手册中的“dev-manual/runtime-testing：执行自动运行时测试”部分。

## `do_testimage_auto`

Boots an image and performs runtime tests within the image immediately after it has been built. This task is enabled when you set `TESTIMAGE_AUTO` equal to \"1\".

> 当您将 `TESTIMAGE_AUTO` 设置为“1”时，将启动镜像并在构建后立即对其进行运行时测试。

For information on automatically testing images, see the \"`dev-manual/runtime-testing:performing automated runtime testing`\" section in the Yocto Project Development Tasks Manual.

> 要了解有关自动测试镜像的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual / runtime-testing：执行自动运行时测试”部分。

# Kernel-Related Tasks

The following tasks are applicable to kernel recipes. Some of these tasks (e.g. the `ref-tasks-menuconfig` task) are also applicable to recipes that use Linux kernel style configuration such as the BusyBox recipe.

> 以下任务适用于内核配方。其中一些任务(例如 `ref-tasks-menuconfig` 任务)也适用于使用 Linux 内核风格配置的配方，例如 BusyBox 配方。

## `do_compile_kernelmodules`

Runs the step that builds the kernel modules (if needed). Building a kernel consists of two steps: 1) the kernel (`vmlinux`) is built, and 2) the modules are built (i.e. `make modules`).

> 运行构建内核模块的步骤(如果需要的话)。构建内核包括两个步骤：1)构建内核(`vmlinux`)，2)构建模块(即 `make modules`)。

## `do_diffconfig`

When invoked by the user, this task creates a file containing the differences between the original config as produced by `ref-tasks-kernel_configme`). Once the file of differences is created, it can be used to create a config fragment that only contains the differences. You can invoke this task from the command line as follows:

> 当用户调用时，此任务会创建一个文件，其中包含由 `ref-tasks-kernel_configme`)所做出的更改之间的差异。一旦创建了差异文件，就可以使用它来创建仅包含差异的配置片段。您可以从命令行中调用此任务，如下所示：

```
$ bitbake linux-yocto -c diffconfig
```

For more information, see the \"`kernel-dev/common:creating configuration fragments`\" section in the Yocto Project Linux Kernel Development Manual.

> 若要获取更多信息，请参阅 Yocto 项目 Linux 内核开发手册中的“kernel-dev/common：创建配置片段”部分。

## `do_kernel_checkout`

Converts the newly unpacked kernel source into a form with which the OpenEmbedded build system can work. Because the kernel source can be fetched in several different ways, the `ref-tasks-kernel_checkout` task makes sure that subsequent tasks are given a clean working tree copy of the kernel with the correct branches checked out.

> 将新解压的内核源代码转换为 OpenEmbedded 构建系统可以使用的形式。由于内核源可以以多种不同的方式获取，`ref-tasks-kernel_checkout` 任务确保后续任务获得一个清洁的工作树副本，其中检查了正确的分支。

## `do_kernel_configcheck`

Validates the configuration produced by the `ref-tasks-kernel_menuconfig` task produces warnings when a requested configuration does not appear in the final `.config` file or when you override a policy configuration in a hardware configuration fragment. You can run this task explicitly and view the output by using the following command:

> 验证 `ref-tasks-kernel_menuconfig` 任务会产生警告。您可以显式运行此任务并使用以下命令查看输出：

```
$ bitbake linux-yocto -c kernel_configcheck -f
```

For more information, see the \"`kernel-dev/common:validating configuration`\" section in the Yocto Project Linux Kernel Development Manual.

> 更多信息，请参阅 Yocto Project Linux 内核开发手册中的“kernel-dev/common：验证配置”部分。

## `do_kernel_configme`

After the kernel is patched by the `ref-tasks-patch` task assembles and merges all the kernel config fragments into a merged configuration that can then be passed to the kernel configuration phase proper. This is also the time during which user-specified defconfigs are applied if present, and where configuration modes such as `--allnoconfig` are applied.

> 在使用 `ref-tasks-patch` 任务将所有内核配置片段组装并合并成一个合并后的配置，然后可以传递给内核配置阶段本身。这也是用户指定 defconfigs 被应用的时候(如果存在的话)，以及应用诸如 `--allnoconfig` 之类的配置模式的时候。

## `do_kernel_menuconfig`

Invoked by the user to manipulate the `.config` file used to build a linux-yocto recipe. This task starts the Linux kernel configuration tool, which you then use to modify the kernel configuration.

> 用户调用此任务以操作用于构建 Linux-Yocto 配方的 `.config` 文件。此任务启动 Linux 内核配置工具，您可以使用该工具来修改内核配置。

::: note
::: title
Note
:::

You can also invoke this tool from the command line as follows:

```
$ bitbake linux-yocto -c menuconfig
```

:::

See the \"``kernel-dev/common:using \`\`menuconfig\`\` ``\" section in the Yocto Project Linux Kernel Development Manual for more information on this configuration tool.

> 请参阅 Yocto 项目 Linux 内核开发手册中关于此配置工具的“kernel-dev/common：使用 `menuconfig`”部分，以获取更多信息。

## `do_kernel_metadata`

Collects all the features required for a given kernel build, whether the features come from `SRC_URI`.

> 收集给定内核构建所需的所有功能，无论这些功能是来自 SRC_URI 还是来自 Git 存储库。收集完成后，ref-tasks-kernel_metadata 任务将功能处理为一系列配置片段和补丁，然后可以由后续任务(如 ref-tasks-patch 和 ref-tasks-kernel_configme)应用这些补丁。

## `do_menuconfig`

Runs `make menuconfig` for the kernel. For information on `menuconfig`, see the \"``kernel-dev/common:using \`\`menuconfig\`\` ``\" section in the Yocto Project Linux Kernel Development Manual.

> 运行 ``make menuconfig`` 来配置内核。关于 ``menuconfig`` 的信息，请参阅 Yocto Project Linux 内核开发手册中的"kernel-dev/common：使用 `menuconfig`"部分。

## `do_savedefconfig`

When invoked by the user, creates a defconfig file that can be used instead of the default defconfig. The saved defconfig contains the differences between the default defconfig and the changes made by the user using other methods (i.e. the `ref-tasks-kernel_menuconfig` task. You can invoke the task using the following command:

> 当用户调用时，会创建一个可以代替默认 defconfig 的 defconfig 文件。保存的 defconfig 包含了默认 defconfig 和用户使用其他方法(即 `ref-tasks-kernel_menuconfig` 任务)所做的更改之间的差异。您可以使用以下命令调用该任务：

```
$ bitbake linux-yocto -c savedefconfig
```

## `do_shared_workdir`

After the kernel has been compiled but before the kernel modules have been compiled, this task copies files required for module builds and which are generated from the kernel build into the shared work directory. With these copies successfully copied, the `ref-tasks-compile_kernelmodules` task can successfully build the kernel modules in the next step of the build.

> 在内核编译完成但在内核模块编译之前，此任务会将从内核构建生成的用于模块构建所需的文件复制到共享工作目录中。随着这些文件成功复制，`ref-tasks-compile_kernelmodules` 任务可以在构建的下一步成功构建内核模块。

## `do_sizecheck`

After the kernel has been built, this task checks the size of the stripped kernel image against `KERNEL_IMAGE_MAXSIZE`. If that variable was set and the size of the stripped kernel exceeds that size, the kernel build produces a warning to that effect.

> 在内核构建之后，此任务会检查剥离的内核映像的大小与 `KERNEL_IMAGE_MAXSIZE`。如果该变量被设置，并且剥离的内核的大小超过了该大小，内核构建就会产生一个相应的警告。

## `do_strip`

If `KERNEL_IMAGE_STRIP_EXTRA_SECTIONS` is defined, this task strips the sections named in that variable from `vmlinux`. This stripping is typically used to remove nonessential sections such as `.comment` sections from a size-sensitive configuration.

> 如果定义了 `KERNEL_IMAGE_STRIP_EXTRA_SECTIONS`，此任务将从 `vmlinux` 中剥离该变量中指定的部分。这种剥离通常用于从尺寸敏感的配置中移除非必要的部分，如 `.comment` 部分。

## `do_validate_branches`

After the kernel is unpacked but before it is patched, this task makes sure that the machine and metadata branches as specified by the `SRCREV` task fails during the build.

> 在内核被解压但在被补丁之前，此任务确保按照 `SRCREV` 变量指定的机器和元数据分支实际存在于指定的分支上。否则，如果不使用 `AUTOREV`，则在构建期间 `ref-tasks-validate_branches` 任务将失败。
