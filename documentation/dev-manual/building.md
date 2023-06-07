---
tip: translate by baidu@2023-06-07 17:08:53
...
---
title: Building
---------------

This section describes various build procedures, such as the steps needed for a simple build, building a target for multiple configurations, generating an image for more than one machine, and so forth.

> 本节介绍了各种构建过程，例如简单构建所需的步骤、为多个配置构建目标、为多台机器生成映像等等。

# Building a Simple Image

In the development environment, you need to build an image whenever you change hardware support, add or change system libraries, or add or change services that have dependencies. There are several methods that allow you to build an image within the Yocto Project. This section presents the basic steps you need to build a simple image using BitBake from a build host running Linux.

> 在开发环境中，无论何时更改硬件支持、添加或更改系统库，或添加或更改具有依赖关系的服务，都需要构建映像。有几种方法可以让您在 Yocto 项目中构建图像。本节介绍了在运行 Linux 的构建主机上使用 BitBake 构建简单图像所需的基本步骤。

::: note
::: title
Note
:::

- For information on how to build an image using `Toaster`{.interpreted-text role="term"}, see the `/toaster-manual/index`{.interpreted-text role="doc"}.

> -有关如何使用 `Toaster`｛.depreted text role=“term”｝构建图像的信息，请参阅 `/Toaster manual/index`｛.epreted text role=“doc”｝。

- For information on how to use `devtool` to build images, see the \"``sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow``{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> -有关如何使用“devtool”构建映像的信息，请参阅 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的“`` sdk手册/可扩展：在您的sdk工作流中使用`` devtool\``”一节。

- For a quick example on how to build an image using the OpenEmbedded build system, see the `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"} document.

> -有关如何使用 OpenEmbedded 构建系统构建图像的快速示例，请参阅 `/brefriel yoctoprojectqs/index`{.depreted text role=“doc”}文档。
> :::

The build process creates an entire Linux distribution from source and places it in your `Build Directory`{.interpreted-text role="term"} under `tmp/deploy/images`. For detailed information on the build process using BitBake, see the \"`overview-manual/concepts:images`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> 构建过程从源代码创建一个完整的 Linux 发行版，并将其放置在 `tmp/deploy/images` 下的 `build Directory`｛.depredicted text role=“term”｝中。有关使用 BitBake 构建过程的详细信息，请参阅 Yocto 项目概述和概念手册中的“`overview manual/concepts:images`｛.depreted text role=“ref”｝”一节。

The following figure and list overviews the build process:

> 下图和列表概述了构建过程：

![image](figures/bitbake-build-flow.png){width="100.0%"}

1. *Set up Your Host Development System to Support Development Using the Yocto Project*: See the \"`start`{.interpreted-text role="doc"}\" section for options on how to get a build host ready to use the Yocto Project.

> 1.*设置您的主机开发系统以支持使用 Yocto 项目的开发*：有关如何使生成主机准备好使用 YoctoProject 的选项，请参阅\“`start`｛.depreted text role=“doc”｝\”部分。

2. *Initialize the Build Environment:* Initialize the build environment by sourcing the build environment script (i.e. `structure-core-script`{.interpreted-text role="ref"}):

> 2.*初始化构建环境：*通过获取构建环境脚本（即“结构核心脚本”｛.explored text role=“ref”｝）来初始化构建环境

```

> ```

$ source oe-init-build-env [build_dir]

> $source初始构建环境[build_dir]

```

> ```
> ```

When you use the initialization script, the OpenEmbedded build system uses `build` as the default `Build Directory`{.interpreted-text role="term"} in your current work directory. You can use a [build_dir]{.title-ref} argument with the script to specify a different `Build Directory`{.interpreted-text role="term"}.

> 当您使用初始化脚本时，OpenEmbedded 生成系统会将“build”用作当前工作目录中的默认“build Directory `｛.explored text role=“term”｝。您可以在脚本中使用[build_dir]｛.title-ref｝参数来指定不同的` build Directory`｛.depreted text role=“term”｝。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

A common practice is to use a different `Build Directory`{.interpreted-text role="term"} for different targets; for example, `~/build/x86` for a `qemux86` target, and `~/build/arm` for a `qemuarm` target. In any event, it\'s typically cleaner to locate the `Build Directory`{.interpreted-text role="term"} somewhere outside of your source directory.

> 一种常见的做法是对不同的目标使用不同的“构建目录”{.depreted text role=“term”}；例如，“~/build/x86”用于“qemux86”目标，而“~/bbuild/arm”用于“qemuarm”目标。在任何情况下，通常将“构建目录”｛.depreced text role=“term”｝定位在源目录之外的某个位置更为干净。

:::

> ：：：

3. *Make Sure Your* `local.conf` *File is Correct*: Ensure the `conf/local.conf` configuration file, which is found in the `Build Directory`{.interpreted-text role="term"}, is set up how you want it. This file defines many aspects of the build environment including the target machine architecture through the `MACHINE`{.interpreted-text role="term"} variable, the packaging format used during the build (`PACKAGE_CLASSES`{.interpreted-text role="term"}), and a centralized tarball download directory through the `DL_DIR`{.interpreted-text role="term"} variable.

> 3.*确保您的* `local.conf` *文件是正确的*：确保在 `Build Directory`｛.depredicted text role=“term”｝中找到的 `conf/local.conf` 配置文件按照您的意愿设置。该文件通过 `machine`｛.epredicted textrole=”term“｝变量定义了构建环境的许多方面，包括目标机器体系结构，构建过程中使用的打包格式（`PACKAGE_CLASES`｛.depreced text role=“term”｝），以及通过 `DL_DIR`｛.repreced textrole=”term“｝变量的集中式 tarball 下载目录。

4. *Build the Image:* Build the image using the `bitbake` command:

> 4.*构建图像：*使用“bitbake”命令构建图像：

```

> ```

$ bitbake target

> $bitbake目标

```

> ```
> ```

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

For information on BitBake, see the `bitbake:index`{.interpreted-text role="doc"}.

> 有关 BitBake 的信息，请参阅 `BitBake:index`｛.explored text role=“doc”｝。

:::

> ：：：

The target is the name of the recipe you want to build. Common targets are the images in `meta/recipes-core/images`, `meta/recipes-sato/images`, and so forth all found in the `Source Directory`{.interpreted-text role="term"}. Alternatively, the target can be the name of a recipe for a specific piece of software such as BusyBox. For more details about the images the OpenEmbedded build system supports, see the \"`ref-manual/images:Images`{.interpreted-text role="ref"}\" chapter in the Yocto Project Reference Manual.

> 目标是您想要构建的配方的名称。常见的目标是“meta/recipes core/images”、“meta/recipes sato/images”等中的图像，所有这些都可以在“源目录”｛.explored text role=“term”｝中找到。或者，目标可以是特定软件（如 BusyBox）的配方名称。有关 OpenEmbedded 构建系统支持的图像的更多详细信息，请参阅 Yocto 项目参考手册中的“`ref manual/images:images`{.depreted text role=“ref”}\”一章。

As an example, the following command builds the `core-image-minimal` image:

> 例如，以下命令构建“核心图像最小值”图像：

```

> ```

$ bitbake core-image-minimal

> $bitbake核心图像最小值

```

> ```
> ```

Once an image has been built, it often needs to be installed. The images and kernels built by the OpenEmbedded build system are placed in the `Build Directory`{.interpreted-text role="term"} in `tmp/deploy/images`. For information on how to run pre-built images such as `qemux86` and `qemuarm`, see the `/sdk-manual/index`{.interpreted-text role="doc"} manual. For information about how to install these images, see the documentation for your particular board or machine.

> 一旦建立了一个映像，通常需要安装它。OpenEmbedded 构建系统构建的映像和内核被放置在 `tmp/deploy/images` 中的 `build Directory`｛.depredicted text role=“term”｝中。有关如何运行诸如“qemux86”和“qemuarm”之类的预构建映像的信息，请参阅“/sdk manual/index”｛.depreced text role=“doc”｝手册。有关如何安装这些映像的信息，请参阅特定板或机器的文档。

# Building Images for Multiple Targets Using Multiple Configurations

You can use a single `bitbake` command to build multiple images or packages for different targets where each image or package requires a different configuration (multiple configuration builds). The builds, in this scenario, are sometimes referred to as \"multiconfigs\", and this section uses that term throughout.

> 您可以使用单个“bitbake”命令为不同的目标构建多个图像或包，其中每个图像或包需要不同的配置（多个配置构建）。在这种情况下，构建有时被称为“multiconfigs”，本节始终使用该术语。

This section describes how to set up for multiple configuration builds and how to account for cross-build dependencies between the multiconfigs.

> 本节介绍如何设置多个配置构建，以及如何说明多个配置之间的交叉构建依赖关系。

## Setting Up and Running a Multiple Configuration Build

To accomplish a multiple configuration build, you must define each target\'s configuration separately using a parallel configuration file in the `Build Directory`{.interpreted-text role="term"} or configuration directory within a layer, and you must follow a required file hierarchy. Additionally, you must enable the multiple configuration builds in your `local.conf` file.

> 要完成多配置构建，必须使用“构建目录”中的并行配置文件或层内的配置目录分别定义每个目标的配置，并且必须遵循所需的文件层次结构。此外，您必须在“local.conf”文件中启用多个配置构建。

Follow these steps to set up and execute multiple configuration builds:

> 按照以下步骤设置和执行多个配置构建：

- *Create Separate Configuration Files*: You need to create a single configuration file for each build target (each multiconfig). The configuration definitions are implementation dependent but often each configuration file will define the machine and the temporary directory BitBake uses for the build. Whether the same temporary directory (`TMPDIR`{.interpreted-text role="term"}) can be shared will depend on what is similar and what is different between the configurations. Multiple MACHINE targets can share the same (`TMPDIR`{.interpreted-text role="term"}) as long as the rest of the configuration is the same, multiple `DISTRO`{.interpreted-text role="term"} settings would need separate (`TMPDIR`{.interpreted-text role="term"}) directories.

> -*创建单独的配置文件*：您需要为每个构建目标（每个多配置）创建一个配置文件。配置定义依赖于实现，但通常每个配置文件都会定义机器和 BitBake 用于构建的临时目录。是否可以共享相同的临时目录（`TMPDIR`｛.explored text role=“term”｝）将取决于配置之间的相似之处和不同之处。只要配置的其余部分相同，多个 MACHINE 目标就可以共享同一个（`TMPDIR`｛.depreced text role=“term”｝），多个 `DISTRO`｛.epreced textrole=”term“｝设置将需要单独的（`TMPDIR`｛.repreced text role=）目录。

For example, consider a scenario with two different multiconfigs for the same `MACHINE`{.interpreted-text role="term"}: \"qemux86\" built for two distributions such as \"poky\" and \"poky-lsb\". In this case, you would need to use the different `TMPDIR`{.interpreted-text role="term"}.

> 例如，考虑一个场景，对于相同的 `MACHINE`｛.explored text role=“term”｝：\“qemux86\”，有两个不同的多配置，这两个配置是为\“poky \”和\“poky-lsb \”等两个发行版构建的。在这种情况下，您需要使用不同的 `TMPDIR`｛.explored text role=“term”｝。

Here is an example showing the minimal statements needed in a configuration file for a \"qemux86\" target whose temporary build directory is `tmpmultix86`:

> 以下是一个示例，显示了临时构建目录为“tmpmultix86”的\“qemux86”目标的配置文件中所需的最少语句：

```
MACHINE = "qemux86"
TMPDIR = "${TOPDIR}/tmpmultix86"
```

The location for these multiconfig configuration files is specific. They must reside in the current `Build Directory`{.interpreted-text role="term"} in a sub-directory of `conf` named `multiconfig` or within a layer\'s `conf` directory under a directory named `multiconfig`. Following is an example that defines two configuration files for the \"x86\" and \"arm\" multiconfigs:

> 这些多配置配置文件的位置是特定的。它们必须位于名为“multiconfig'”的“conf”的子目录中的当前“构建目录”｛.explored text role=“term”｝中，或位于名为‘multiconfig'的目录下的层的“conf'”目录中。以下是为\“x86\”和\“arm\”多配置定义两个配置文件的示例：

![image](figures/multiconfig_files.png){.align-center width="50.0%"}

> ！[image]（图/multi-config_files.png）｛.aligne center width=“50.0%”｝

The usual `BBPATH`{.interpreted-text role="term"} search path is used to locate multiconfig files in a similar way to other conf files.

> 通常的 `BBPATH`｛.explored text role=“term”｝搜索路径用于以与其他 conf 文件类似的方式定位多配置文件。

- *Add the BitBake Multi-configuration Variable to the Local Configuration File*: Use the `BBMULTICONFIG`{.interpreted-text role="term"} variable in your `conf/local.conf` configuration file to specify each multiconfig. Continuing with the example from the previous figure, the `BBMULTICONFIG`{.interpreted-text role="term"} variable needs to enable two multiconfigs: \"x86\" and \"arm\" by specifying each configuration file:

> -*将 BitBake 多配置变量添加到本地配置文件*：使用 `conf/Local.conf` 配置文件中的 `BBMULTICONFIG`{.depreted text role=“term”}变量指定每个多配置。继续前面图中的示例，`BBMULTICONFIG`｛.explored text role=“term”｝变量需要通过指定每个配置文件来启用两个多配置：\“x86\”和\“arm \”：

```
BBMULTICONFIG = "x86 arm"
```

::: note
::: title

Note

> 笔记
> :::

A \"default\" configuration already exists by definition. This configuration is named: \"\" (i.e. empty string) and is defined by the variables coming from your `local.conf` file. Consequently, the previous example actually adds two additional configurations to your build: \"arm\" and \"x86\" along with \"\".

> 根据定义，“默认”配置已经存在。此配置名为：\“\”（即空字符串），由“local.conf”文件中的变量定义。因此，前面的示例实际上为您的构建添加了两个额外的配置：\“arm\”和\“x86\”以及\“\”。
> :::

- *Launch BitBake*: Use the following BitBake command form to launch the multiple configuration build:

> -*启动 BitBake*：使用以下 BitBake 命令表单启动多重配置构建：

```
$ bitbake [mc:multiconfigname:]target [[[mc:multiconfigname:]target] ... ]
```

For the example in this section, the following command applies:

> 对于本节中的示例，以下命令适用：

```
$ bitbake mc:x86:core-image-minimal mc:arm:core-image-sato mc::core-image-base
```

The previous BitBake command builds a `core-image-minimal` image that is configured through the `x86.conf` configuration file, a `core-image-sato` image that is configured through the `arm.conf` configuration file and a `core-image-base` that is configured through your `local.conf` configuration file.

> 上一个 BitBake 命令构建了一个通过“x86.conf”配置文件配置的“核心映像最小”映像、一个通过‘arm.conf’配置文件配置“核心映像 sato”映像和一个通过您的“local.conf”配置文件进行配置的“核心映像库”。

::: note
::: title
Note
:::

Support for multiple configuration builds in the Yocto Project &DISTRO; (&DISTRO_NAME;) Release does not include Shared State (sstate) optimizations. Consequently, if a build uses the same object twice in, for example, two different `TMPDIR`{.interpreted-text role="term"} directories, the build either loads from an existing sstate cache for that build at the start or builds the object fresh.

> 支持 Yocto 项目和 DISTRO 中的多个配置构建；（&DISTRO_NAME；）版本不包括共享状态（sstate）优化。因此，例如，如果一个生成在两个不同的 `TMPDIR`｛.explored text role=“term”｝目录中两次使用同一对象，则该生成要么在开始时从该生成的现有 sstate 缓存加载，要么重新生成对象。
> :::

## Enabling Multiple Configuration Build Dependencies

Sometimes dependencies can exist between targets (multiconfigs) in a multiple configuration build. For example, suppose that in order to build a `core-image-sato` image for an \"x86\" multiconfig, the root filesystem of an \"arm\" multiconfig must exist. This dependency is essentially that the `ref-tasks-image`{.interpreted-text role="ref"} task in the `core-image-sato` recipe depends on the completion of the `ref-tasks-rootfs`{.interpreted-text role="ref"} task of the `core-image-minimal` recipe.

> 有时，在多配置构建中，目标（多配置）之间可能存在依赖关系。例如，假设为了为\“x86\”多配置构建“核心映像 sato”映像，\“arm”多配置的根文件系统必须存在。这种依赖性本质上是，“核心图像 sato”配方中的“ref tasks image”｛.explored text role=“ref”｝任务取决于“核心图像 minimum”配方的“ref tasks rootfs”｛..explored textrole=”ref“｝任务的完成情况。

To enable dependencies in a multiple configuration build, you must declare the dependencies in the recipe using the following statement form:

> 要在多配置构建中启用依赖项，必须使用以下语句形式在配方中声明依赖项：

```
task_or_package[mcdepends] = "mc:from_multiconfig:to_multiconfig:recipe_name:task_on_which_to_depend"
```

To better show how to use this statement, consider the example scenario from the first paragraph of this section. The following statement needs to be added to the recipe that builds the `core-image-sato` image:

> 为了更好地展示如何使用此语句，请考虑本节第一段中的示例场景。以下语句需要添加到构建“核心图像 sato”图像的配方中：

```
do_image[mcdepends] = "mc:x86:arm:core-image-minimal:do_rootfs"
```

In this example, the [from_multiconfig]{.title-ref} is \"x86\". The [to_multiconfig]{.title-ref} is \"arm\". The task on which the `ref-tasks-image`{.interpreted-text role="ref"} task in the recipe depends is the `ref-tasks-rootfs`{.interpreted-text role="ref"} task from the `core-image-minimal` recipe associated with the \"arm\" multiconfig.

> 在本例中，[from_multiconfig]｛.title-ref｝是\“x86\”。[to_multiconfig]｛.title-ref｝是\“arm”。配方中的 `ref tasks image`｛.depredicted text role=“ref”｝任务所依赖的任务是与“arm”多重配置关联的 `core image minimum` 配方中的 ` ref tasks rootfs`｛.epredicted textrole=”ref“｝任务。

Once you set up this dependency, you can build the \"x86\" multiconfig using a BitBake command as follows:

> 一旦设置了此依赖项，就可以使用 BitBake 命令构建\“x86\”多配置，如下所示：

```
$ bitbake mc:x86:core-image-sato
```

This command executes all the tasks needed to create the `core-image-sato` image for the \"x86\" multiconfig. Because of the dependency, BitBake also executes through the `ref-tasks-rootfs`{.interpreted-text role="ref"} task for the \"arm\" multiconfig build.

> 此命令执行为\“x86\”多配置创建“核心映像 sato”映像所需的所有任务。由于依赖关系，BitBake 还通过\“arm\”多配置构建的 `ref tasks rootfs`｛.respered text role=“ref”｝任务执行。

Having a recipe depend on the root filesystem of another build might not seem that useful. Consider this change to the statement in the `core-image-sato` recipe:

> 让配方依赖于另一个构建的根文件系统似乎没有那么有用。考虑一下对“核心图像 sato”配方中语句的更改：

```
do_image[mcdepends] = "mc:x86:arm:core-image-minimal:do_image"
```

In this case, BitBake must create the `core-image-minimal` image for the \"arm\" build since the \"x86\" build depends on it.

> 在这种情况下，BitBake 必须为“arm”构建创建“core image minimum”图像，因为“x86”构建依赖于它。

Because \"x86\" and \"arm\" are enabled for multiple configuration builds and have separate configuration files, BitBake places the artifacts for each build in the respective temporary build directories (i.e. `TMPDIR`{.interpreted-text role="term"}).

> 由于“x86\”和“arm\”为多个配置构建启用，并且具有单独的配置文件，BitBake 将每个构建的工件放置在各自的临时构建目录中（即 `TMPDIR`｛.depreted text role=“term”｝）。

# Building an Initial RAM Filesystem (Initramfs) Image

An initial RAM filesystem (`Initramfs`{.interpreted-text role="term"}) image provides a temporary root filesystem used for early system initialization, typically providing tools and loading modules needed to locate and mount the final root filesystem.

> 初始 RAM 文件系统（`Initramfs`{.depredicted text role=“term”}）映像提供用于早期系统初始化的临时根文件系统，通常提供定位和装载最终根文件系统所需的工具和加载模块。

Follow these steps to create an `Initramfs`{.interpreted-text role="term"} image:

> 按照以下步骤创建 `Initramfs`｛.explored text role=“term”｝图像：

1. *Create the :term:\`Initramfs\` Image Recipe:* You can reference the `core-image-minimal-initramfs.bb` recipe found in the `meta/recipes-core` directory of the `Source Directory`{.interpreted-text role="term"} as an example from which to work.

> 1.*创建：term:\`Initramfs\`Image Recipe:*您可以引用在 `Source directory` 的 `meta/precipes core` 目录中找到的 `core Image minimum Initramfs.bb` Recipe 作为工作示例。

2. *Decide if You Need to Bundle the :term:\`Initramfs\` Image Into the Kernel Image:* If you want the `Initramfs`{.interpreted-text role="term"} image that is built to be bundled in with the kernel image, set the `INITRAMFS_IMAGE_BUNDLE`{.interpreted-text role="term"} variable to `"1"` in your `local.conf` configuration file and set the `INITRAMFS_IMAGE`{.interpreted-text role="term"} variable in the recipe that builds the kernel image.

> 2.*决定是否需要将：term:\`Initramfs\`Image 捆绑到内核映像中：*如果您希望构建的 `Initraffs`｛.explored text role=“term”｝映像与内核映像捆绑在一起，在 `local.conf` 配置文件中将 `INITRANFS_IMAGE_BUNDLE`｛.explored text role=“term”｝变量设置为 `“1”'，并在构建内核映像的配方中设置` INIRAMFS_IMAGE`｛.explored textrole=”term“｝变量。

Setting the `INITRAMFS_IMAGE_BUNDLE`{.interpreted-text role="term"} flag causes the `Initramfs`{.interpreted-text role="term"} image to be unpacked into the `${B}/usr/` directory. The unpacked `Initramfs`{.interpreted-text role="term"} image is then passed to the kernel\'s `Makefile` using the `CONFIG_INITRAMFS_SOURCE`{.interpreted-text role="term"} variable, allowing the `Initramfs`{.interpreted-text role="term"} image to be built into the kernel normally.

> 设置 `INITRAMFS_IMAGE_BUNDLE`｛.depreted text role=“term”｝标志会导致 `INITRAMFS`｛.epreted text role=“term“｝图像被解压缩到 `$｛B｝/usr/` 目录中。然后，使用 CONFIG_Initramfs_SOURCE 变量将解压缩后的 `Initramfs`｛.depreced text role=“term”｝图像传递到内核的 `Makefile`，从而使 `Initramps`{.depreded text rol=“term“｝图像能够正常构建到内核中。

3. *Optionally Add Items to the Initramfs Image Through the Initramfs Image Recipe:* If you add items to the `Initramfs`{.interpreted-text role="term"} image by way of its recipe, you should use `PACKAGE_INSTALL`{.interpreted-text role="term"} rather than `IMAGE_INSTALL`{.interpreted-text role="term"}. `PACKAGE_INSTALL`{.interpreted-text role="term"} gives more direct control of what is added to the image as compared to the defaults you might not necessarily want that are set by the `ref-classes-image`{.interpreted-text role="ref"} or `ref-classes-core-image`{.interpreted-text role="ref"} classes.

> 3.*可选择通过 Initramfs 图像配方将项目添加到 Initramfs 图像：*如果您通过其配方将项目增加到 `Initramfs`｛.depredicted text role=“term”｝图像，则应使用 `PACKAGE_INSTAL`｛.expredicted textrole=”term“｝，而不是 `Image_INSTAL'｛.repredicted extrol=”term”}` 与您可能不一定想要的由 `ref classes image`｛.explored text role=“ref”｝或 `ref classs core image`{.explered text rol=“ref”}类设置的默认值相比，PACKAGE_INSTALL`｛.sexplored text-role=“term”｝可以更直接地控制添加到图像中的内容。

4. *Build the Kernel Image and the Initramfs Image:* Build your kernel image using BitBake. Because the `Initramfs`{.interpreted-text role="term"} image recipe is a dependency of the kernel image, the `Initramfs`{.interpreted-text role="term"} image is built as well and bundled with the kernel image if you used the `INITRAMFS_IMAGE_BUNDLE`{.interpreted-text role="term"} variable described earlier.

> 4.*构建内核映像和 Initramfs 映像：*使用 BitBake 构建内核映像。由于 `Initramfs`｛.depredicted text role=“term”｝图像配方是内核图像的依赖项，因此如果您使用前面描述的 `Initramfs_image_BUNDLE`｛.epredicted text role=”term“｝变量，则也会构建 `Initraffs'｛.derredicted text role=”term”}图像并将其与内核图像捆绑在一起。

## Bundling an Initramfs Image From a Separate Multiconfig

There may be a case where we want to build an `Initramfs`{.interpreted-text role="term"} image which does not inherit the same distro policy as our main image, for example, we may want our main image to use `TCLIBC="glibc"`, but to use `TCLIBC="musl"` in our `Initramfs`{.interpreted-text role="term"} image to keep a smaller footprint. However, by performing the steps mentioned above the `Initramfs`{.interpreted-text role="term"} image will inherit `TCLIBC="glibc"` without allowing us to override it.

> 在这种情况下，我们可能希望构建一个 `Initramfs`｛.explored text role=“term”｝映像，该映像与我们的主映像不继承相同的发行版策略，例如，我们可能想要我们的主图像使用 `TCLIBC=“glibc”`，但在 `Initramps`{.explered text rol=“term“｝映像中使用 `TCLIBC=“musl”` 以保持较小的占地面积。但是，通过执行上面提到的步骤，`Initramfs`｛.explored text role=“term”｝图像将继承 `TCLIBC=“glibc”`，而不允许我们覆盖它。

To achieve this, you need to perform some additional steps:

> 要实现这一点，您需要执行一些附加步骤：

1. *Create a multiconfig for your Initramfs image:* You can perform the steps on \"`dev-manual/building:building images for multiple targets using multiple configurations`{.interpreted-text role="ref"}\" to create a separate multiconfig. For the sake of simplicity let\'s assume such multiconfig is called: `initramfscfg.conf` and contains the variables:

> 1.*为您的 Initramfs 映像创建一个多配置：*您可以执行“`dev manual/building：使用多个配置为多个目标构建映像”上的步骤来创建一个单独的多配置。为了简单起见，让我们假设这样的多配置被称为：` initramfscfg.conf`，并包含以下变量：

```

> ```

TMPDIR="${TOPDIR}/tmp-initramfscfg"

> TMPDIR=“$｛TOPDIR｝/tmp initramfscfg”

TCLIBC="musl"

> TCLIBC=“音乐”

```

> ```
> ```

2. *Set additional Initramfs variables on your main configuration:* Additionally, on your main configuration (`local.conf`) you need to set the variables:

> 2.*在主配置上设置额外的 Initramfs 变量：*此外，在主配置（`local.conf`）上，您需要设置变量：

```

> ```

INITRAMFS_MULTICONFIG = "initramfscfg"

> INITRAMFS_MULTICONFIG=“initramfscfg”

INITRAMFS_DEPLOY_DIR_IMAGE = "${TOPDIR}/tmp-initramfscfg/deploy/images/${MACHINE}"

> INITRANFS_DEPLOY_DIR_IMAGE=“$｛TOPDIR｝/tmp initramfscfg/DEPLOY/images/$｛MACHINE｝”

```

> ```
> ```

The variables `INITRAMFS_MULTICONFIG`{.interpreted-text role="term"} and `INITRAMFS_DEPLOY_DIR_IMAGE`{.interpreted-text role="term"} are used to create a multiconfig dependency from the kernel to the `INITRAMFS_IMAGE`{.interpreted-text role="term"} to be built coming from the `initramfscfg` multiconfig, and to let the buildsystem know where the `INITRAMFS_IMAGE`{.interpreted-text role="term"} will be located.

> 变量 `INITRAMFS_MULTICONFIG`｛.explored text role=“term”｝和 `INITRAMAFS_DELOY_DIR_IMAGE`｛.sexplored text role=”term“｝用于创建从内核到 `initramfscfg` 多配置要构建的 `INITRAFS_IMAGE'｛，并让构建系统知道` INITRAMFS_IMAGE`{.depreted text role=“term”}将位于何处。

Building a system with such configuration will build the kernel using the main configuration but the `ref-tasks-bundle_initramfs`{.interpreted-text role="ref"} task will grab the selected `INITRAMFS_IMAGE`{.interpreted-text role="term"} from `INITRAMFS_DEPLOY_DIR_IMAGE`{.interpreted-text role="term"} instead, resulting in a musl based `Initramfs`{.interpreted-text role="term"} image bundled in the kernel but a glibc based main image.

> 用这样的配置构建系统将使用主配置构建内核，但 `ref-tasks-bundle_initramfs`｛.depredicted text role=“ref”｝任务将从 `initramfs_DELOY_DIR_IMAGE`｛.epredicted text-role=“term”｝中获取选定的 `INITRAMAFS_IMAGE`｛.repredicted ext-role=”term“｝，导致内核中捆绑了一个基于 musl 的 `Initramfs`{.depredicted text role=“term”}映像，但这是一个基于 glibc 的主映像。

The same is applicable to avoid inheriting `DISTRO_FEATURES`{.interpreted-text role="term"} on `INITRAMFS_IMAGE`{.interpreted-text role="term"} or to build a different `DISTRO`{.interpreted-text role="term"} for it such as `poky-tiny`.

> 这同样适用于避免在 `INITRAMFS_IMAGE` 上继承 `DISTRO_FEATURE`{.depreted text role=“term”}，或为其构建不同的 `DISTRO`{.deploted text role=“term”}，如 `poky tiny`。

# Building a Tiny System

Very small distributions have some significant advantages such as requiring less on-die or in-package memory (cheaper), better performance through efficient cache usage, lower power requirements due to less memory, faster boot times, and reduced development overhead. Some real-world examples where a very small distribution gives you distinct advantages are digital cameras, medical devices, and small headless systems.

> 非常小的发行版具有一些显著的优势，例如需要更少的片上或封装内存（更便宜）、通过高效的缓存使用获得更好的性能、由于更少的内存而降低的电源需求、更快的启动时间以及减少的开发开销。数字相机、医疗设备和小型无头系统是一些非常小的分布给你带来明显优势的现实世界例子。

This section presents information that shows you how you can trim your distribution to even smaller sizes than the `poky-tiny` distribution, which is around 5 Mbytes, that can be built out-of-the-box using the Yocto Project.

> 本节提供的信息向您展示了如何将您的分发精简到比使用 Yocto 项目开箱即用构建的“poky tiny”分发更小的大小，后者约为 5 兆字节。

## Tiny System Overview

The following list presents the overall steps you need to consider and perform to create distributions with smaller root filesystems, achieve faster boot times, maintain your critical functionality, and avoid initial RAM disks:

> 以下列表列出了您需要考虑和执行的总体步骤，以创建具有较小根文件系统的分发版，实现更快的启动时间，维护关键功能，并避免使用初始 RAM 磁盘：

- `Determine your goals and guiding principles <dev-manual/building:goals and guiding principles>`{.interpreted-text role="ref"}

> -`确定你的目标和指导原则<devmanual/building:goals and guiding principles>`｛.explored text role=“ref”｝

- `dev-manual/building:understand what contributes to your image size`{.interpreted-text role="ref"}

> -`devmanual/building:了解是什么导致了您的图像大小`{.depredicted text role=“ref”}

- `Reduce the size of the root filesystem <dev-manual/building:trim the root filesystem>`{.interpreted-text role="ref"}

> -`减小根文件系统的大小<devmanual/building:trim the root filesystem>`｛.depredicted text role=“ref”｝

- `Reduce the size of the kernel <dev-manual/building:trim the kernel>`{.interpreted-text role="ref"}

> -`减小内核的大小<devmanual/building:traint the kernel>`｛.depredicted text role=“ref”｝

- `dev-manual/building:remove package management requirements`{.interpreted-text role="ref"}
- `dev-manual/building:look for other ways to minimize size`{.interpreted-text role="ref"}
- `dev-manual/building:iterate on the process`{.interpreted-text role="ref"}

## Goals and Guiding Principles

Before you can reach your destination, you need to know where you are going. Here is an example list that you can use as a guide when creating very small distributions:

> 在你到达目的地之前，你需要知道你要去哪里。以下是一个示例列表，您可以在创建非常小的发行版时将其用作指南：

- Determine how much space you need (e.g. a kernel that is 1 Mbyte or less and a root filesystem that is 3 Mbytes or less).

> -确定您需要多少空间（例如，1 兆字节或更少的内核和 3 兆字节或更小的根文件系统）。

- Find the areas that are currently taking 90% of the space and concentrate on reducing those areas.

> -找出目前占据 90% 空间的区域，并集中精力减少这些区域。

- Do not create any difficult \"hacks\" to achieve your goals.
- Leverage the device-specific options.
- Work in a separate layer so that you keep changes isolated. For information on how to create layers, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section.

> -在单独的图层中工作，以便将更改隔离。有关如何创建层的信息，请参阅\“`dev manual/layers:understanding and createing layers`｛.depreted text role=“ref”｝\”一节。

## Understand What Contributes to Your Image Size

It is easiest to have something to start with when creating your own distribution. You can use the Yocto Project out-of-the-box to create the `poky-tiny` distribution. Ultimately, you will want to make changes in your own distribution that are likely modeled after `poky-tiny`.

> 在创建自己的发行版时，最简单的方法是先做一些事情。您可以使用 Yocto 项目开箱即用来创建“poky tiny”分发。最终，您将希望在自己的分布中进行更改，这些更改很可能是以“poky tiny”为模型的。

::: note
::: title
Note
:::

To use `poky-tiny` in your build, set the `DISTRO`{.interpreted-text role="term"} variable in your `local.conf` file to \"poky-tiny\" as described in the \"`dev-manual/custom-distribution:creating your own distribution`{.interpreted-text role="ref"}\" section.

> 要在构建中使用 `poky tiny`，请将 `local.conf` 文件中的 `DISTRO`｛.depredicted text role=“term”｝变量设置为\“poky tini\”，如“dev manual/custom distribution:createing your own distribution`｛.epredicted textrole=”ref“｝\”部分所述。
> :::

Understanding some memory concepts will help you reduce the system size. Memory consists of static, dynamic, and temporary memory. Static memory is the TEXT (code), DATA (initialized data in the code), and BSS (uninitialized data) sections. Dynamic memory represents memory that is allocated at runtime: stacks, hash tables, and so forth. Temporary memory is recovered after the boot process. This memory consists of memory used for decompressing the kernel and for the `__init__` functions.

> 了解一些内存概念将有助于减小系统大小。内存包括静态内存、动态内存和临时内存。静态内存是 TEXT（代码）、DATA（代码中的初始化数据）和 BSS（未初始化数据）部分。动态内存表示在运行时分配的内存：堆栈、哈希表等等。临时内存在引导过程之后恢复。该内存由用于解压缩内核和“__init__”函数的内存组成。

To help you see where you currently are with kernel and root filesystem sizes, you can use two tools found in the `Source Directory`{.interpreted-text role="term"} in the `scripts/tiny/` directory:

> 为了帮助您了解内核和根文件系统的当前大小，您可以使用“scripts/tiny/”目录中的“源目录”｛.depreted text role=“term”｝中的两个工具：

- `ksize.py`: Reports component sizes for the kernel build objects.
- `dirsize.py`: Reports component sizes for the root filesystem.

This next tool and command help you organize configuration fragments and view file dependencies in a human-readable form:

> 下一个工具和命令帮助您以可读的形式组织配置片段和查看文件依赖项：

- `merge_config.sh`: Helps you manage configuration files and fragments within the kernel. With this tool, you can merge individual configuration fragments together. The tool allows you to make overrides and warns you of any missing configuration options. The tool is ideal for allowing you to iterate on configurations, create minimal configurations, and create configuration files for different machines without having to duplicate your process.

> -`merge_config.sh`：帮助您管理内核中的配置文件和碎片。使用此工具，您可以将各个配置片段合并在一起。该工具允许您进行覆盖，并警告您任何丢失的配置选项。该工具非常适合您迭代配置、创建最小配置以及为不同的机器创建配置文件，而不必重复您的过程。

The `merge_config.sh` script is part of the Linux Yocto kernel Git repositories (i.e. `linux-yocto-3.14`, `linux-yocto-3.10`, `linux-yocto-3.8`, and so forth) in the `scripts/kconfig` directory.

> “merge_config.sh”脚本是 Linux Yocto 内核 Git 存储库（即“scripts/kconfig”目录中的“Linux-Yocto-3.14”、“Linux-iocto-3.10”、“Linux-yoct-3.8”等）的一部分。

For more information on configuration fragments, see the \"`kernel-dev/common:creating configuration fragments`{.interpreted-text role="ref"}\" section in the Yocto Project Linux Kernel Development Manual.

> 有关配置片段的更多信息，请参阅 Yocto Project Linux 内核开发手册中的\“`kernel dev/common:createing configuration fragments`｛.depreted text role=“ref”｝\”一节。

- `bitbake -u taskexp -g bitbake_target`: Using the BitBake command with these options brings up a Dependency Explorer from which you can view file dependencies. Understanding these dependencies allows you to make informed decisions when cutting out various pieces of the kernel and root filesystem.

> -`bitbake-u taskexp-g bitbake_target`：将 bitbake 命令与这些选项一起使用会打开依赖项资源管理器，您可以从中查看文件依赖项。了解这些依赖关系可以让您在裁剪内核和根文件系统的各个部分时做出明智的决定。

## Trim the Root Filesystem

The root filesystem is made up of packages for booting, libraries, and applications. To change things, you can configure how the packaging happens, which changes the way you build them. You can also modify the filesystem itself or select a different filesystem.

> 根文件系统由用于启动的包、库和应用程序组成。要更改内容，您可以配置打包的方式，从而更改构建它们的方式。您还可以修改文件系统本身或选择不同的文件系统。

First, find out what is hogging your root filesystem by running the `dirsize.py` script from your root directory:

> 首先，通过从根目录运行“dirsize.py”脚本，找出占用根文件系统的原因：

```
$ cd root-directory-of-image
$ dirsize.py 100000 > dirsize-100k.log
$ cat dirsize-100k.log
```

You can apply a filter to the script to ignore files under a certain size. The previous example filters out any files below 100 Kbytes. The sizes reported by the tool are uncompressed, and thus will be smaller by a relatively constant factor in a compressed root filesystem. When you examine your log file, you can focus on areas of the root filesystem that take up large amounts of memory.

> 您可以对脚本应用筛选器以忽略特定大小下的文件。上一个示例过滤掉所有低于 100 KB 的文件。该工具报告的大小是未压缩的，因此在压缩的根文件系统中会以相对恒定的因子较小。当您检查日志文件时，可以将重点放在占用大量内存的根文件系统区域。

You need to be sure that what you eliminate does not cripple the functionality you need. One way to see how packages relate to each other is by using the Dependency Explorer UI with the BitBake command:

> 您需要确保所消除的内容不会削弱您所需的功能。查看包之间关系的一种方法是使用依赖关系浏览器 UI 和 BitBake 命令：

```
$ cd image-directory
$ bitbake -u taskexp -g image
```

Use the interface to select potential packages you wish to eliminate and see their dependency relationships.

> 使用该界面可以选择要消除的潜在包，并查看它们的依赖关系。

When deciding how to reduce the size, get rid of packages that result in minimal impact on the feature set. For example, you might not need a VGA display. Or, you might be able to get by with `devtmpfs` and `mdev` instead of `udev`.

> 在决定如何缩小尺寸时，去掉对功能集影响最小的包。例如，您可能不需要 VGA 显示器。或者，您可以使用“devtmpfs”和“mdev”而不是“udev”。

Use your `local.conf` file to make changes. For example, to eliminate `udev` and `glib`, set the following in the local configuration file:

> 使用“local.conf”文件进行更改。例如，要消除“udev”和“glib”，请在本地配置文件中设置以下内容：

```
VIRTUAL-RUNTIME_dev_manager = ""
```

Finally, you should consider exactly the type of root filesystem you need to meet your needs while also reducing its size. For example, consider `cramfs`, `squashfs`, `ubifs`, `ext2`, or an `Initramfs`{.interpreted-text role="term"} using `initramfs`. Be aware that `ext3` requires a 1 Mbyte journal. If you are okay with running read-only, you do not need this journal.

> 最后，您应该准确地考虑根文件系统的类型，以满足您的需求，同时减少其大小。例如，考虑使用 `Initramfs` 的 `cramfs`、`squshfs`、`ubifs'、` ext2'或 `Initramfs`｛.explored text role=“term”｝。请注意，“ext3”需要 1M 字节的日志。如果您可以以只读方式运行，则不需要此日志。

::: note
::: title
Note
:::

After each round of elimination, you need to rebuild your system and then use the tools to see the effects of your reductions.

> 在每一轮淘汰之后，你需要重建你的系统，然后使用这些工具来查看削减的效果。
> :::

## Trim the Kernel

The kernel is built by including policies for hardware-independent aspects. What subsystems do you enable? For what architecture are you building? Which drivers do you build by default?

> 内核是通过包含与硬件无关的方面的策略来构建的。您启用了哪些子系统？你在建造什么建筑？您默认构建哪些驱动程序？

::: note
::: title
Note
:::

You can modify the kernel source if you want to help with boot time.

> 如果您想帮助引导时间，可以修改内核源代码。
> :::

Run the `ksize.py` script from the top-level Linux build directory to get an idea of what is making up the kernel:

> 从顶级 Linux 构建目录运行“ksize.py”脚本，了解内核的组成：

```
$ cd top-level-linux-build-directory
$ ksize.py > ksize.log
$ cat ksize.log
```

When you examine the log, you will see how much space is taken up with the built-in `.o` files for drivers, networking, core kernel files, filesystem, sound, and so forth. The sizes reported by the tool are uncompressed, and thus will be smaller by a relatively constant factor in a compressed kernel image. Look to reduce the areas that are large and taking up around the \"90% rule.\"

> 当您检查日志时，您将看到内置的“.o”文件占用了多少空间，这些文件用于驱动程序、网络、核心内核文件、文件系统、声音等等。该工具报告的大小是未压缩的，因此在压缩的内核图像中会小一个相对恒定的因子。尽量减少占据“90% 规则”周围的大面积区域

To examine, or drill down, into any particular area, use the `-d` option with the script:

> 要检查或深入到任何特定区域，请在脚本中使用“-d”选项：

```
$ ksize.py -d > ksize.log
```

Using this option breaks out the individual file information for each area of the kernel (e.g. drivers, networking, and so forth).

> 使用此选项可以分解内核每个区域的单个文件信息（例如，驱动程序、网络等）。

Use your log file to see what you can eliminate from the kernel based on features you can let go. For example, if you are not going to need sound, you do not need any drivers that support sound.

> 使用日志文件查看可以根据可以放弃的功能从内核中删除哪些内容。例如，如果您不需要声音，则不需要任何支持声音的驱动程序。

After figuring out what to eliminate, you need to reconfigure the kernel to reflect those changes during the next build. You could run `menuconfig` and make all your changes at once. However, that makes it difficult to see the effects of your individual eliminations and also makes it difficult to replicate the changes for perhaps another target device. A better method is to start with no configurations using `allnoconfig`, create configuration fragments for individual changes, and then manage the fragments into a single configuration file using `merge_config.sh`. The tool makes it easy for you to iterate using the configuration change and build cycle.

> 在弄清楚要消除什么之后，您需要重新配置内核，以便在下一次构建期间反映这些更改。您可以运行“menuconfig”并一次进行所有更改。然而，这使得很难看到你个人淘汰的影响，也使得很难为另一个目标设备复制这些变化。一个更好的方法是使用“allnoconfig”从没有配置开始，为单个更改创建配置片段，然后使用“merge_config.sh”将这些片段管理到一个配置文件中。该工具使您可以轻松地使用配置更改和构建周期进行迭代。

Each time you make configuration changes, you need to rebuild the kernel and check to see what impact your changes had on the overall size.

> 每次进行配置更改时，都需要重新构建内核，并检查更改对总体大小的影响。

## Remove Package Management Requirements

Packaging requirements add size to the image. One way to reduce the size of the image is to remove all the packaging requirements from the image. This reduction includes both removing the package manager and its unique dependencies as well as removing the package management data itself.

> 包装要求增加了图像的尺寸。减少图像大小的一种方法是从图像中去除所有包装要求。这种减少包括删除包管理器及其唯一依赖项，以及删除包管理数据本身。

To eliminate all the packaging requirements for an image, be sure that \"package-management\" is not part of your `IMAGE_FEATURES`{.interpreted-text role="term"} statement for the image. When you remove this feature, you are removing the package manager as well as its dependencies from the root filesystem.

> 要消除图像的所有包装要求，请确保“包装管理”不是图像的 `image_FATURE`｛.depreted text role=“term”｝语句的一部分。删除此功能时，就是从根文件系统中删除包管理器及其依赖项。

## Look for Other Ways to Minimize Size

Depending on your particular circumstances, other areas that you can trim likely exist. The key to finding these areas is through tools and methods described here combined with experimentation and iteration. Here are a couple of areas to experiment with:

> 根据您的具体情况，可能存在其他可以修剪的区域。找到这些领域的关键是通过本文描述的工具和方法，结合实验和迭代。以下是几个可以进行实验的领域：

- `glibc`: In general, follow this process:

  1. Remove `glibc` features from `DISTRO_FEATURES`{.interpreted-text role="term"} that you think you do not need.

> 1.从 `DISTRO_FEATURE` 中删除您认为不需要的 `glibc` 功能。

2. Build your distribution.

> 2.建立您的分布。

3. If the build fails due to missing symbols in a package, determine if you can reconfigure the package to not need those features. For example, change the configuration to not support wide character support as is done for `ncurses`. Or, if support for those characters is needed, determine what `glibc` features provide the support and restore the configuration.

> 3.如果由于包中缺少符号而导致生成失败，请确定是否可以将包重新配置为不需要这些功能。例如，将配置更改为不支持宽字符支持，就像对“ncurses”所做的那样。或者，如果需要对这些字符的支持，请确定哪些“glibc”功能提供了支持并恢复配置。

4. Rebuild and repeat the process.

> 4.重建并重复此过程。

- `busybox`: For BusyBox, use a process similar as described for `glibc`. A difference is you will need to boot the resulting system to see if you are able to do everything you expect from the running system. You need to be sure to integrate configuration fragments into Busybox because BusyBox handles its own core features and then allows you to add configuration fragments on top.

> -“busybox”：对于 busybox，使用与“glibc”类似的过程。不同的是，您需要启动生成的系统，看看您是否能够从运行的系统中完成您所期望的一切。您需要确保将配置片段集成到 Busybox 中，因为 Busybox 处理自己的核心功能，然后允许您在上面添加配置片段。

## Iterate on the Process

If you have not reached your goals on system size, you need to iterate on the process. The process is the same. Use the tools and see just what is taking up 90% of the root filesystem and the kernel. Decide what you can eliminate without limiting your device beyond what you need.

> 如果您还没有达到系统规模的目标，那么您需要对流程进行迭代。过程是一样的。使用这些工具，看看是什么占据了根文件系统和内核的 90%。决定在不将设备限制在所需范围之外的情况下可以消除哪些内容。

Depending on your system, a good place to look might be Busybox, which provides a stripped down version of Unix tools in a single, executable file. You might be able to drop virtual terminal services or perhaps ipv6.

> 根据您的系统，Busybox 可能是一个不错的选择，它在一个可执行文件中提供了 Unix 工具的精简版本。您可能可以放弃虚拟终端服务或 ipv6。

# Building Images for More than One Machine

A common scenario developers face is creating images for several different machines that use the same software environment. In this situation, it is tempting to set the tunings and optimization flags for each build specifically for the targeted hardware (i.e. \"maxing out\" the tunings). Doing so can considerably add to build times and package feed maintenance collectively for the machines. For example, selecting tunes that are extremely specific to a CPU core used in a system might enable some micro optimizations in GCC for that particular system but would otherwise not gain you much of a performance difference across the other systems as compared to using a more general tuning across all the builds (e.g. setting `DEFAULTTUNE`{.interpreted-text role="term"} specifically for each machine\'s build). Rather than \"max out\" each build\'s tunings, you can take steps that cause the OpenEmbedded build system to reuse software across the various machines where it makes sense.

> 开发人员面临的一个常见场景是为使用相同软件环境的几个不同机器创建图像。在这种情况下，很容易为每个构建设置专门针对目标硬件的调整和优化标志（即“最大化”调整）。这样做可以大大增加机器的构建时间和打包进料维护。例如选择非常特定于系统中使用的 CPU 核心的调优可能会在 GCC 中为该特定系统实现一些微观优化，但与在所有构建中使用更通用的调优相比，在其他系统中不会获得太大的性能差异（例如，设置“DEFAULTTUNE”{.depredicted text role=“term”}特别是针对每台机器的构建）。您可以采取措施，使 OpenEmbedded 构建系统在各种有意义的机器上重用软件，而不是“最大化”每个构建的调优。

If build speed and package feed maintenance are considerations, you should consider the points in this section that can help you optimize your tunings to best consider build times and package feed maintenance.

> 如果构建速度和包提要维护是需要考虑的，那么您应该考虑本节中的要点，这些要点可以帮助您优化调优，以最佳地考虑构建时间和包提要的维护。

- *Share the :term:\`Build Directory\`:* If at all possible, share the `TMPDIR`{.interpreted-text role="term"} across builds. The Yocto Project supports switching between different `MACHINE`{.interpreted-text role="term"} values in the same `TMPDIR`{.interpreted-text role="term"}. This practice is well supported and regularly used by developers when building for multiple machines. When you use the same `TMPDIR`{.interpreted-text role="term"} for multiple machine builds, the OpenEmbedded build system can reuse the existing native and often cross-recipes for multiple machines. Thus, build time decreases.

> -*共享：term:\`Build Directory\`：*如果可能的话，在多个构建之间共享 `TMPDIR`｛.explored text role=“term”｝。Yocto 项目支持在同一 `TMPDIR` 中的不同 `MACHINE`{.expreted text role=“term”}值之间切换。开发人员在为多台机器构建时，很好地支持并经常使用这种做法。当您对多台机器构建使用相同的 `TMPDIR`｛.explored text role=“term”｝时，OpenEmbedded 构建系统可以为多台机器重用现有的本机且通常是交叉配方。因此，构建时间减少。

::: note
::: title

Note

> 笔记
> :::

If `DISTRO`{.interpreted-text role="term"} settings change or fundamental configuration settings such as the filesystem layout, you need to work with a clean `TMPDIR`{.interpreted-text role="term"}. Sharing `TMPDIR`{.interpreted-text role="term"} under these circumstances might work but since it is not guaranteed, you should use a clean `TMPDIR`{.interpreted-text role="term"}.

> 如果 `DISTRO`｛.explored text role=“term”｝设置发生更改或基本配置设置（如文件系统布局）发生更改，则需要使用干净的 `TMPDIR`{.explered text rol=“term“｝。在这种情况下，共享 `TMPDIR`｛.explored text role=“term”｝可能会起作用，但由于不能保证，您应该使用干净的 `TMPDIR'｛.expered text rol=“term“｝。
> :::

- *Enable the Appropriate Package Architecture:* By default, the OpenEmbedded build system enables three levels of package architectures: \"all\", \"tune\" or \"package\", and \"machine\". Any given recipe usually selects one of these package architectures (types) for its output. Depending for what a given recipe creates packages, making sure you enable the appropriate package architecture can directly impact the build time.

> -*启用适当的软件包架构：*默认情况下，OpenEmbedded 构建系统启用三个级别的软件包体系结构：\“all \”、\“tune \”或\“Package \”和\“machine \”。任何给定的配方通常都会选择这些包体系结构（类型）中的一种作为其输出。根据给定的配方创建包的方式，确保启用适当的包体系结构会直接影响构建时间。

A recipe that just generates scripts can enable \"all\" architecture because there are no binaries to build. To specifically enable \"all\" architecture, be sure your recipe inherits the `ref-classes-allarch`{.interpreted-text role="ref"} class. This class is useful for \"all\" architectures because it configures many variables so packages can be used across multiple architectures.

> 一个只生成脚本的配方可以启用“all”体系结构，因为没有二进制文件可以构建。要特别启用“all”体系结构，请确保您的配方继承了 `ref classes allarch`｛.depreted text role=“ref”｝类。此类对于“所有”体系结构非常有用，因为它配置了许多变量，因此包可以跨多个体系结构使用。

If your recipe needs to generate packages that are machine-specific or when one of the build or runtime dependencies is already machine-architecture dependent, which makes your recipe also machine-architecture dependent, make sure your recipe enables the \"machine\" package architecture through the `MACHINE_ARCH`{.interpreted-text role="term"} variable:

> 如果您的配方需要生成特定于机器的包，或者其中一个构建或运行时依赖项已经依赖于机器体系结构，这使得您的配方也依赖于机器架构，请确保您的配方通过 `machine_ARCH`｛.depreted text role=“term”｝变量启用\“machine\”包体系结构：

```
PACKAGE_ARCH = "${MACHINE_ARCH}"
```

When you do not specifically enable a package architecture through the `PACKAGE_ARCH`{.interpreted-text role="term"}, The OpenEmbedded build system defaults to the `TUNE_PKGARCH`{.interpreted-text role="term"} setting:

> 当您没有通过 `package_ARCH`｛.explored text role=“term”｝专门启用包体系结构时，OpenEmbedded 构建系统默认为 `TUNE_PKGARCH`{.explered text rol=“term“｝设置：

```
PACKAGE_ARCH = "${TUNE_PKGARCH}"
```

- *Choose a Generic Tuning File if Possible:* Some tunes are more generic and can run on multiple targets (e.g. an `armv5` set of packages could run on `armv6` and `armv7` processors in most cases). Similarly, `i486` binaries could work on `i586` and higher processors. You should realize, however, that advances on newer processor versions would not be used.

> -*如果可能，请选择通用调整文件：*有些调整更通用，可以在多个目标上运行（例如，在大多数情况下，“armv5”包集可以在“armv6”和“armv7”处理器上运行）。类似地，“i486”二进制文件可以在“i586”和更高的处理器上工作。然而，您应该意识到，不会使用较新处理器版本的进步。

If you select the same tune for several different machines, the OpenEmbedded build system reuses software previously built, thus speeding up the overall build time. Realize that even though a new sysroot for each machine is generated, the software is not recompiled and only one package feed exists.

> 如果您为几个不同的机器选择相同的调优，OpenEmbedded 构建系统会重用以前构建的软件，从而加快整个构建时间。要意识到，即使为每台机器生成了一个新的 sysroot，软件也不会重新编译，并且只存在一个包提要。

- *Manage Granular Level Packaging:* Sometimes there are cases where injecting another level of package architecture beyond the three higher levels noted earlier can be useful. For example, consider how NXP (formerly Freescale) allows for the easy reuse of binary packages in their layer :yocto\_[git:%60meta-freescale](git:%60meta-freescale) \</meta-freescale/\>[. In this example, the :yocto_git:\`fsl-dynamic-packagearch \</meta-freescale/tree/classes/fsl-dynamic-packagearch.bbclass\>]{.title-ref} class shares GPU packages for i.MX53 boards because all boards share the AMD GPU. The i.MX6-based boards can do the same because all boards share the Vivante GPU. This class inspects the BitBake datastore to identify if the package provides or depends on one of the sub-architecture values. If so, the class sets the `PACKAGE_ARCH`{.interpreted-text role="term"} value based on the `MACHINE_SUBARCH` value. If the package does not provide or depend on one of the sub-architecture values but it matches a value in the machine-specific filter, it sets `MACHINE_ARCH`{.interpreted-text role="term"}. This behavior reduces the number of packages built and saves build time by reusing binaries.

> -*管理粒度级封装：*有时，在前面提到的三个更高级别之外注入另一个级别的封装体系结构可能会很有用。例如考虑恩智浦（前身为 Freescale）如何允许在其层中轻松重用二进制包：yocto\_[git:%60meta-Freescale]（git:%60meta-Freescale）\</meta-Freescale/\>[。在本例中，：yocto_git:\`fsl dynamic packagearch\</meta-foreescale/tree/classes/fsl-dynamic packagearch.bbclass\>]｛.title-ref｝类共享 i.MX53 板的 GPU 包，因为所有板都共享 AMD GPU。基于 i.MX6 的板也可以这样做，因为所有板都共享 Vivante GPU。此类检查 BitBake 数据存储，以确定包是否提供或依赖于其中一个子体系结构值。如果是，该类将基于 `MACHINE_SUBARCH` 值设置 `PACKAGE_ARCH`｛.explored text role=“term”｝值。如果程序包不提供或不依赖于其中一个子体系结构值，但它与特定于机器的筛选器中的值匹配，则会设置 `machine_ARCH`｛.depreted text role=“term”｝。这种行为减少了生成的包的数量，并通过重用二进制文件节省了生成时间。

- *Use Tools to Debug Issues:* Sometimes you can run into situations where software is being rebuilt when you think it should not be. For example, the OpenEmbedded build system might not be using shared state between machines when you think it should be. These types of situations are usually due to references to machine-specific variables such as `MACHINE`{.interpreted-text role="term"}, `SERIAL_CONSOLES`{.interpreted-text role="term"}, `XSERVER`{.interpreted-text role="term"}, `MACHINE_FEATURES`{.interpreted-text role="term"}, and so forth in code that is supposed to only be tune-specific or when the recipe depends (`DEPENDS`{.interpreted-text role="term"}, `RDEPENDS`{.interpreted-text role="term"}, `RRECOMMENDS`{.interpreted-text role="term"}, `RSUGGESTS`{.interpreted-text role="term"}, and so forth) on some other recipe that already has `PACKAGE_ARCH`{.interpreted-text role="term"} defined as \"\${MACHINE_ARCH}\".

> -*使用工具调试问题：*有时你可能会遇到这样的情况，即软件在你认为不应该重建的情况下正在重建。例如，OpenEmbedded 构建系统可能没有在你认为应该使用的情况下使用机器之间的共享状态。这些类型的情况通常是由于引用了机器特定的变量，如“machine”｛.explored text role=“term”｝，`SERIAL_CONSOLES`｛.explored text role=“term”｝，`XSERVER`｛..explored textrole=”term“｝，` MACHINE_FEATURE`｛.expered text rol=”term”}，等等）依赖于已经定义为\“\${MACHINE_ARCH｝\”的其他配方。

::: note
::: title

Note

> 笔记
> :::

Patches to fix any issues identified are most welcome as these issues occasionally do occur.

> 修复发现的任何问题的补丁程序是最受欢迎的，因为这些问题偶尔会发生。
> :::

For such cases, you can use some tools to help you sort out the situation:

> 对于这种情况，您可以使用一些工具来帮助您解决问题：

- `state-diff-machines.sh`*:* You can find this tool in the `scripts` directory of the Source Repositories. See the comments in the script for information on how to use the tool.

> -`state diff machines.sh`*：*您可以在源存储库的'scripts'目录中找到此工具。有关如何使用该工具的信息，请参阅脚本中的注释。

- *BitBake\'s \"-S printdiff\" Option:* Using this option causes BitBake to try to establish the closest signature match it can (e.g. in the shared state cache) and then run `bitbake-diffsigs` over the matches to determine the stamps and delta where these two stamp trees diverge.

> -*BitBake 的“-s printdiff”选项：*使用此选项会导致 BitBake 尝试建立最接近的签名匹配（例如，在共享状态缓存中），然后在匹配上运行“BitBake-diffsigs”，以确定这两个戳树分叉的戳和增量。

# Building Software from an External Source

By default, the OpenEmbedded build system uses the `Build Directory`{.interpreted-text role="term"} when building source code. The build process involves fetching the source files, unpacking them, and then patching them if necessary before the build takes place.

> 默认情况下，OpenEmbedded 构建系统在构建源代码时使用“构建目录”｛.depreted text role=“term”｝。构建过程包括获取源文件，拆包它们，然后在进行构建之前对它们进行必要的修补。

There are situations where you might want to build software from source files that are external to and thus outside of the OpenEmbedded build system. For example, suppose you have a project that includes a new BSP with a heavily customized kernel. And, you want to minimize exposing the build system to the development team so that they can focus on their project and maintain everyone\'s workflow as much as possible. In this case, you want a kernel source directory on the development machine where the development occurs. You want the recipe\'s `SRC_URI`{.interpreted-text role="term"} variable to point to the external directory and use it as is, not copy it.

> 在某些情况下，您可能希望从 OpenEmbedded 构建系统外部的源文件构建软件。例如，假设您有一个项目，其中包含一个具有高度定制内核的新 BSP。而且，您希望尽量减少将构建系统暴露给开发团队，以便他们能够专注于自己的项目并尽可能维护每个人的工作流程。在这种情况下，您希望在进行开发的开发机器上有一个内核源目录。您希望配方的 `SRC_URI`｛.explored text role=“term”｝变量指向外部目录并按原样使用，而不是复制它。

To build from software that comes from an external source, all you need to do is inherit the `ref-classes-externalsrc`{.interpreted-text role="ref"} class and then set the `EXTERNALSRC`{.interpreted-text role="term"} variable to point to your external source code. Here are the statements to put in your `local.conf` file:

> 要从来自外部源的软件构建，您所需要做的就是继承 `ref classes externalsrc`｛.depreted text role=“ref”｝类，然后将 `externalsrc`｛.epreted text role=“term”｝变量设置为指向外部源代码。以下是要放入“local.conf”文件中的语句：

```
INHERIT += "externalsrc"
EXTERNALSRC:pn-myrecipe = "path-to-your-source-tree"
```

This next example shows how to accomplish the same thing by setting `EXTERNALSRC`{.interpreted-text role="term"} in the recipe itself or in the recipe\'s append file:

> 下一个例子展示了如何通过在配方本身或配方的附加文件中设置 `EXTERNALSRC`｛.explored text role=“term”｝来完成同样的事情：

```
EXTERNALSRC = "path"
EXTERNALSRC_BUILD = "path"
```

::: note
::: title
Note
:::

In order for these settings to take effect, you must globally or locally inherit the `ref-classes-externalsrc`{.interpreted-text role="ref"} class.

> 为了使这些设置生效，您必须全局或本地继承 `ref classes externalsrc`{.depreted text role=“ref”}类。
> :::

By default, `ref-classes-externalsrc`{.interpreted-text role="ref"} builds the source code in a directory separate from the external source directory as specified by `EXTERNALSRC`{.interpreted-text role="term"}. If you need to have the source built in the same directory in which it resides, or some other nominated directory, you can set `EXTERNALSRC_BUILD`{.interpreted-text role="term"} to point to that directory:

> 默认情况下，`ref classes externalsrc`｛.depreted text role=“ref”｝在与 `externalsrc`｛.epreted text role=“term”｝指定的外部源目录分离的目录中构建源代码。如果您需要在源所在的同一目录或其他指定目录中构建源，您可以设置 `EXTERNALSRC_BUILD`｛.depreted text role=“term”｝指向该目录：

```
EXTERNALSRC_BUILD:pn-myrecipe = "path-to-your-source-tree"
```

# Replicating a Build Offline

It can be useful to take a \"snapshot\" of upstream sources used in a build and then use that \"snapshot\" later to replicate the build offline. To do so, you need to first prepare and populate your downloads directory your \"snapshot\" of files. Once your downloads directory is ready, you can use it at any time and from any machine to replicate your build.

> 获取生成中使用的上游源的“快照”，然后使用该“快照”脱机复制生成，这可能很有用。为此，您需要首先准备并填充下载目录中的文件“快照”。下载目录准备好后，您可以在任何时间从任何机器使用它来复制您的构建。

Follow these steps to populate your Downloads directory:

> 按照以下步骤填充下载目录：

1. *Create a Clean Downloads Directory:* Start with an empty downloads directory (`DL_DIR`{.interpreted-text role="term"}). You start with an empty downloads directory by either removing the files in the existing directory or by setting `DL_DIR`{.interpreted-text role="term"} to point to either an empty location or one that does not yet exist.

> 1.*创建一个干净的下载目录：*从一个空的下载目录（`DL_DIR`｛.depredicted text role=“term”｝）开始。您可以从一个空的下载目录开始，方法是删除现有目录中的文件，或者将 `DL_DIR`{.depredicted text role=“term”}设置为指向一个空位置或一个尚不存在的位置。

2. *Generate Tarballs of the Source Git Repositories:* Edit your `local.conf` configuration file as follows:

> 2.*生成源 Git 存储库的 Tarball：*编辑您的“local.conf”配置文件如下：

```

> ```

DL_DIR = "/home/your-download-dir/"

> DL_DIR=“/home/您的下载目录/”

BB_GENERATE_MIRROR_TARBALLS = "1"

> BB_GENERATE_MIRROR_tarwalls=“1”

```

> ```
> ```

During the fetch process in the next step, BitBake gathers the source files and creates tarballs in the directory pointed to by `DL_DIR`{.interpreted-text role="term"}. See the `BB_GENERATE_MIRROR_TARBALLS`{.interpreted-text role="term"} variable for more information.

> 在下一步的提取过程中，BitBake 收集源文件，并在 `DL_DIR`｛.depredicted text role=“term”｝指向的目录中创建 tarball。有关详细信息，请参阅 `BB_GENERATE_MIRROR_TARBALLS`｛.explored text role=“term”｝变量。

3. *Populate Your Downloads Directory Without Building:* Use BitBake to fetch your sources but inhibit the build:

> 3.*在不构建的情况下填充下载目录：*使用 BitBake 获取源，但禁止构建：

```

> ```

$ bitbake target --runonly=fetch

> $bitbake target--runonly=获取

```

> ```
> ```

The downloads directory (i.e. `${DL_DIR}`) now has a \"snapshot\" of the source files in the form of tarballs, which can be used for the build.

> 下载目录（即 `${DL_DIR}`）现在有一个 tarball 形式的源文件的“快照”，可以用于构建。

4. *Optionally Remove Any Git or other SCM Subdirectories From the Downloads Directory:* If you want, you can clean up your downloads directory by removing any Git or other Source Control Management (SCM) subdirectories such as `${DL_DIR}/git2/*`. The tarballs already contain these subdirectories.

> 4.*从下载目录中删除任何 Git 或其他 SCM 子目录（可选）：*如果需要，您可以通过删除任何 Git 或其他源代码管理（SCM）子目录（如 `${DL_DIR}/git2/*`）来清理下载目录。tarball 已经包含这些子目录。

Once your downloads directory has everything it needs regarding source files, you can create your \"own-mirror\" and build your target. Understand that you can use the files to build the target offline from any machine and at any time.

> 一旦你的下载目录拥有了关于源文件的一切需要，你就可以创建“自己的镜像”并构建你的目标。了解您可以使用这些文件在任何时间从任何机器离线构建目标。

Follow these steps to build your target using the files in the downloads directory:

> 按照以下步骤使用下载目录中的文件构建目标：

1. *Using Local Files Only:* Inside your `local.conf` file, add the `SOURCE_MIRROR_URL`{.interpreted-text role="term"} variable, inherit the `ref-classes-own-mirrors`{.interpreted-text role="ref"} class, and use the `BB_NO_NETWORK`{.interpreted-text role="term"} variable to your `local.conf`:

> 1.*仅使用本地文件：*在 `Local.conf` 文件中，添加 `SOURCE_MIRROR_URL`｛.depreted text role=“term”｝变量，继承 `ref classes own mirrors`｛.repreted text role=“ref”｝类，并在 `Local.conf ` 中使用 `BB_NO_NETWORK`｛.epreted text role=“erm”}变量：

```

> ```

SOURCE_MIRROR_URL ?= "file:///home/your-download-dir/"

> 源镜像URL？=”file:///home/your-download-dir/”

INHERIT += "own-mirrors"

> INHERIT+=“自己的镜子”

BB_NO_NETWORK = "1"

> BB_NO_NETWORK=“1”

```

> ```
> ```

The `SOURCE_MIRROR_URL`{.interpreted-text role="term"} and `ref-classes-own-mirrors`{.interpreted-text role="ref"} class set up the system to use the downloads directory as your \"own mirror\". Using the `BB_NO_NETWORK`{.interpreted-text role="term"} variable makes sure that BitBake\'s fetching process in step 3 stays local, which means files from your \"own-mirror\" are used.

> `SOURCE_MIRROR_URL`｛.depreted text role=“term”｝和 `ref classes own MIRROR`｛.repreted text role=“ref”｝类设置系统，将下载目录用作“自己的镜像”。使用 `BB_NO_NETWORK`｛.explored text role=“term”｝变量可以确保 BitBake\在步骤 3 中的获取过程保持本地，这意味着使用“自己的镜像”中的文件。

2. *Start With a Clean Build:* You can start with a clean build by removing the `${``TMPDIR`{.interpreted-text role="term"}`}` directory or using a new `Build Directory`{.interpreted-text role="term"}.

> 2.*从干净的生成开始：*您可以通过删除 `$｛` TMPDIR `｛.depreted text role=“term”｝` 目录或使用新的 `Build directory`｛.repreted text role=“term”｝来从干净的构建开始。

3. *Build Your Target:* Use BitBake to build your target:

> 3.*构建目标：*使用 BitBake 构建目标：

```

> ```

$ bitbake target

> $bitbake目标

```

> ```
> ```

The build completes using the known local \"snapshot\" of source files from your mirror. The resulting tarballs for your \"snapshot\" of source files are in the downloads directory.

> 生成使用镜像中源文件的已知本地“快照”完成。为源文件的“快照”生成的 tarball 位于下载目录中。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

The offline build does not work if recipes attempt to find the latest version of software by setting `SRCREV`{.interpreted-text role="term"} to `${``AUTOREV`{.interpreted-text role="term"}`}`:

> 如果配方试图通过将“SRCREV”｛.explored text role=“term”｝设置为“$｛”`AUTOREV”{.explered text role=“term”｝｝来查找最新版本的软件，则离线构建不起作用：

```

> ```

SRCREV = "${AUTOREV}"

> SRCREV=“$｛AUTOREV｝”

```

> ```
> ```

When a recipe sets `SRCREV`{.interpreted-text role="term"} to `${``AUTOREV`{.interpreted-text role="term"}`}`, the build system accesses the network in an attempt to determine the latest version of software from the SCM. Typically, recipes that use `AUTOREV`{.interpreted-text role="term"} are custom or modified recipes. Recipes that reside in public repositories usually do not use `AUTOREV`{.interpreted-text role="term"}.

> 当配方将“SRCREV”｛.explored text role=“term”｝设置为“$”｛`AUTOREV”{.explered text role=“term”｝｝时，构建系统会访问网络，试图从SCM中确定软件的最新版本。通常，使用` AUTOREV `｛.explored text role=“term”｝的配方是自定义或修改的配方。驻留在公共存储库中的配方通常不使用` AUTOREV`｛.explored text role=“term”｝。

If you do have recipes that use `AUTOREV`{.interpreted-text role="term"}, you can take steps to still use the recipes in an offline build. Do the following:

> 如果您确实有使用 `AUTOREV`｛.explored text role=“term”｝的配方，您可以采取措施在离线构建中仍然使用这些配方。请执行以下操作：

1. Use a configuration generated by enabling `build history <dev-manual/build-quality:maintaining build output quality>`{.interpreted-text role="ref"}.

> 1.使用通过启用 `build history<dev manual/build quality:maintaining build output quality>`{.depredicted text role=“ref”}生成的配置。

2. Use the `buildhistory-collect-srcrevs` command to collect the stored `SRCREV`{.interpreted-text role="term"} values from the build\'s history. For more information on collecting these values, see the \"`dev-manual/build-quality:build history package information`{.interpreted-text role="ref"}\" section.

> 2.使用“buildhistory collect srcrevs”命令从构建的历史中收集存储的“SRCREV”｛.explored text role=“term”｝值。有关收集这些值的更多信息，请参阅\“`dev manual/build quality:build history package information`｛.depreted text role=“ref”｝\”一节。

3. Once you have the correct source revisions, you can modify those recipes to set `SRCREV`{.interpreted-text role="term"} to specific versions of the software.

> 3.一旦你有了正确的源版本，你就可以修改这些配方，将 `SRCREV`{.depredicted text role=“term”}设置为特定版本的软件。
> :::
