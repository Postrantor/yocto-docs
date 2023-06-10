---
tip: translate by openai@2023-06-10 09:59:21
...
---
title: Building
---------------

This section describes various build procedures, such as the steps needed for a simple build, building a target for multiple configurations, generating an image for more than one machine, and so forth.

> 这一节描述了各种构建过程，比如简单构建所需的步骤，为多个配置构建目标，为多台机器生成映像等等。

# Building a Simple Image

In the development environment, you need to build an image whenever you change hardware support, add or change system libraries, or add or change services that have dependencies. There are several methods that allow you to build an image within the Yocto Project. This section presents the basic steps you need to build a simple image using BitBake from a build host running Linux.

> 在开发环境中，您需要在更改硬件支持、添加或更改系统库或添加或更改具有依赖性的服务时构建映像。 Yocto 项目提供了几种允许您在运行 Linux 的构建主机上构建映像的方法。本节介绍使用 BitBake 构建简单映像所需的基本步骤。

::: note
::: title
Note
:::

- For information on how to build an image using `Toaster`{.interpreted-text role="term"}, see the `/toaster-manual/index`{.interpreted-text role="doc"}.

> 要了解如何使用 Toaster 构建图像的信息，请参阅/toaster-manual/index 文档。

- For information on how to use `devtool` to build images, see the \"``sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow``{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 对于如何使用 `devtool` 来构建镜像的信息，请参见 Yocto Project 应用开发和可扩展软件开发套件（eSDK）手册中的“sdk-manual/extensible：在您的 sdk 工作流程中使用 `devtool`”部分。

- For a quick example on how to build an image using the OpenEmbedded build system, see the `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"} document.

> 对于如何使用 OpenEmbedded 构建系统构建图像的快速示例，请参阅/brief-yoctoprojectqs/index 文档。
> :::

The build process creates an entire Linux distribution from source and places it in your `Build Directory`{.interpreted-text role="term"} under `tmp/deploy/images`. For detailed information on the build process using BitBake, see the \"`overview-manual/concepts:images`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> 编译过程会从源代码创建整个 Linux 发行版，并将其放置在您的“构建目录”{.interpreted-text role="term"}下的 `tmp/deploy/images` 中。有关使用 BitBake 进行构建过程的详细信息，请参阅 Yocto 项目概览和概念手册中的“`overview-manual/concepts:images`{.interpreted-text role="ref"}”部分。

The following figure and list overviews the build process:

![image](figures/bitbake-build-flow.png){width="100.0%"}

1. *Set up Your Host Development System to Support Development Using the Yocto Project*: See the \"`start`{.interpreted-text role="doc"}\" section for options on how to get a build host ready to use the Yocto Project.

> 查看“开始”部分，了解如何准备主机来使用 Yocto 项目。

2. *Initialize the Build Environment:* Initialize the build environment by sourcing the build environment script (i.e. `structure-core-script`{.interpreted-text role="ref"}):

> 2. *初始化构建环境：* 通过源构建环境脚本（即 `structure-core-script`{.interpreted-text role="ref"}）来初始化构建环境：

```
$ source oe-init-build-env [build_dir]
```

When you use the initialization script, the OpenEmbedded build system uses `build` as the default `Build Directory`{.interpreted-text role="term"} in your current work directory. You can use a [build_dir]{.title-ref} argument with the script to specify a different `Build Directory`{.interpreted-text role="term"}.

> 当你使用初始化脚本时，OpenEmbedded 构建系统会在当前工作目录中将 `build` 作为默认的 `构建目录`。你可以在脚本中使用[build_dir]参数来指定不同的 `构建目录`。

::: note
::: title
Note
:::

A common practice is to use a different `Build Directory`{.interpreted-text role="term"} for different targets; for example, `~/build/x86` for a `qemux86` target, and `~/build/arm` for a `qemuarm` target. In any event, it\'s typically cleaner to locate the `Build Directory`{.interpreted-text role="term"} somewhere outside of your source directory.

> 一个常见的做法是为不同的目标使用不同的“构建目录”；例如，为“qemux86”目标使用“~/build/x86”，为“qemuarm”目标使用“~/build/arm”。无论如何，通常把“构建目录”放在源代码目录之外会更清洁。
> :::

3. *Make Sure Your* `local.conf` *File is Correct*: Ensure the `conf/local.conf` configuration file, which is found in the `Build Directory`{.interpreted-text role="term"}, is set up how you want it. This file defines many aspects of the build environment including the target machine architecture through the `MACHINE`{.interpreted-text role="term"} variable, the packaging format used during the build (`PACKAGE_CLASSES`{.interpreted-text role="term"}), and a centralized tarball download directory through the `DL_DIR`{.interpreted-text role="term"} variable.

> 确保您的 `local.conf` 文件正确：检查 `Build Directory` 中的 `conf/local.conf` 配置文件，确保其符合您的需求。该文件定义了构建环境的许多方面，包括通过 `MACHINE` 变量定义的目标机器架构，构建过程中使用的打包格式（`PACKAGE_CLASSES`），以及通过 `DL_DIR` 变量定义的集中归档文件下载目录。

4. *Build the Image:* Build the image using the `bitbake` command:

   ```
   $ bitbake target
   ```

   ::: note
   ::: title
   Note
   :::

   For information on BitBake, see the `bitbake:index`{.interpreted-text role="doc"}.
   :::

   The target is the name of the recipe you want to build. Common targets are the images in `meta/recipes-core/images`, `meta/recipes-sato/images`, and so forth all found in the `Source Directory`{.interpreted-text role="term"}. Alternatively, the target can be the name of a recipe for a specific piece of software such as BusyBox. For more details about the images the OpenEmbedded build system supports, see the \"`ref-manual/images:Images`{.interpreted-text role="ref"}\" chapter in the Yocto Project Reference Manual.

> 目标是您要构建的配方的名称。常见的目标是 `meta/recipes-core/images`，`meta/recipes-sato/images` 等等，都位于源目录中。或者，目标可以是特定软件的配方的名称，例如 BusyBox。有关 OpenEmbedded 构建系统支持的图像的更多详细信息，请参阅 Yocto 项目参考手册中的“`ref-manual/images:Images`{.interpreted-text role="ref"}”章节。

As an example, the following command builds the `core-image-minimal` image:

```
$ bitbake core-image-minimal
```

Once an image has been built, it often needs to be installed. The images and kernels built by the OpenEmbedded build system are placed in the `Build Directory`{.interpreted-text role="term"} in `tmp/deploy/images`. For information on how to run pre-built images such as `qemux86` and `qemuarm`, see the `/sdk-manual/index`{.interpreted-text role="doc"} manual. For information about how to install these images, see the documentation for your particular board or machine.

> 一旦构建了一个镜像，通常需要安装它。OpenEmbedded 构建系统生成的镜像和内核放置在“构建目录”（Build Directory）中的 `tmp/deploy/images` 中。有关如何运行预构建的镜像（如 qemux86 和 qemuarm）的信息，请参阅 `/sdk-manual/index` 手册。有关如何安装这些镜像的信息，请参阅您特定板或机器的文档。

# Building Images for Multiple Targets Using Multiple Configurations

You can use a single `bitbake` command to build multiple images or packages for different targets where each image or package requires a different configuration (multiple configuration builds). The builds, in this scenario, are sometimes referred to as \"multiconfigs\", and this section uses that term throughout.

> 你可以使用单个 `bitbake` 命令来构建不同目标的多个镜像或包，每个镜像或包都需要不同的配置（多配置构建）。 在这种情况下，构建有时被称为“多配置”，本节在整个文档中使用该术语。

This section describes how to set up for multiple configuration builds and how to account for cross-build dependencies between the multiconfigs.

## Setting Up and Running a Multiple Configuration Build

To accomplish a multiple configuration build, you must define each target\'s configuration separately using a parallel configuration file in the `Build Directory`{.interpreted-text role="term"} or configuration directory within a layer, and you must follow a required file hierarchy. Additionally, you must enable the multiple configuration builds in your `local.conf` file.

> 要完成多配置构建，您必须使用图层中的 `构建目录`{.interpreted-text role="term"}或配置目录中的并行配置文件分别定义每个目标的配置，并且必须遵循必需的文件层次结构。此外，您还必须在 `local.conf` 文件中启用多配置构建。

Follow these steps to set up and execute multiple configuration builds:

- *Create Separate Configuration Files*: You need to create a single configuration file for each build target (each multiconfig). The configuration definitions are implementation dependent but often each configuration file will define the machine and the temporary directory BitBake uses for the build. Whether the same temporary directory (`TMPDIR`{.interpreted-text role="term"}) can be shared will depend on what is similar and what is different between the configurations. Multiple MACHINE targets can share the same (`TMPDIR`{.interpreted-text role="term"}) as long as the rest of the configuration is the same, multiple `DISTRO`{.interpreted-text role="term"} settings would need separate (`TMPDIR`{.interpreted-text role="term"}) directories.

> 你需要为每个构建目标（每个多配置）创建单独的配置文件。配置定义取决于实现，但通常每个配置文件将定义机器和 BitBake 用于构建的临时目录。可以共享相同的临时目录（TMPDIR）取决于配置之间的相似和不同之处。多个 MACHINE 目标可以共享相同的（TMPDIR），只要其余的配置相同，多个 DISTRO 设置需要单独的（TMPDIR）目录。

For example, consider a scenario with two different multiconfigs for the same `MACHINE`{.interpreted-text role="term"}: \"qemux86\" built for two distributions such as \"poky\" and \"poky-lsb\". In this case, you would need to use the different `TMPDIR`{.interpreted-text role="term"}.

> 例如，考虑一个有两个不同的多配置的场景，这些配置都是为同一个机器（MACHINE）“qemux86”构建的，分别是“poky”和“poky-lsb”。在这种情况下，你需要使用不同的 TMPDIR。

Here is an example showing the minimal statements needed in a configuration file for a \"qemux86\" target whose temporary build directory is `tmpmultix86`:

> 这里有一个示例，显示了为具有临时构建目录为'tmpmultix86'的“qemux86”目标所需的配置文件中的最小语句：

```
MACHINE = "qemux86"
TMPDIR = "${TOPDIR}/tmpmultix86"
```

The location for these multiconfig configuration files is specific. They must reside in the current `Build Directory`{.interpreted-text role="term"} in a sub-directory of `conf` named `multiconfig` or within a layer\'s `conf` directory under a directory named `multiconfig`. Following is an example that defines two configuration files for the \"x86\" and \"arm\" multiconfigs:

> 这些多配置文件的位置是特定的。它们必须位于当前的“构建目录”{interpreted-text role =“term”}中的一个名为“multiconfig”的子目录或在层的“conf”目录下的一个名为“multiconfig”的目录中。以下是一个定义两个配置文件的例子，用于“x86”和“arm”多配置：

![image](figures/multiconfig_files.png){.align-center width="50.0%"}

The usual `BBPATH`{.interpreted-text role="term"} search path is used to locate multiconfig files in a similar way to other conf files.

- *Add the BitBake Multi-configuration Variable to the Local Configuration File*: Use the `BBMULTICONFIG`{.interpreted-text role="term"} variable in your `conf/local.conf` configuration file to specify each multiconfig. Continuing with the example from the previous figure, the `BBMULTICONFIG`{.interpreted-text role="term"} variable needs to enable two multiconfigs: \"x86\" and \"arm\" by specifying each configuration file:

> *在本地配置文件中添加 BitBake 多种配置变量：在您的 `conf/local.conf` 配置文件中使用 `BBMULTICONFIG` 变量来指定每个多种配置。继续使用前面图片中的示例，`BBMULTICONFIG` 变量需要通过指定每个配置文件来启用两个多种配置：“x86”和“arm”。*

```
BBMULTICONFIG = "x86 arm"
```

::: note
::: title
Note
:::

A \"default\" configuration already exists by definition. This configuration is named: \"\" (i.e. empty string) and is defined by the variables coming from your `local.conf` file. Consequently, the previous example actually adds two additional configurations to your build: \"arm\" and \"x86\" along with \"\".

> 默认配置已经存在，其名称为空字符串，由您的 `local.conf` 文件中的变量定义。因此，上述示例实际上为您的构建添加了两个额外的配置：“arm”和“x86”以及“”。
> :::

- *Launch BitBake*: Use the following BitBake command form to launch the multiple configuration build:

  ```
  $ bitbake [mc:multiconfigname:]target [[[mc:multiconfigname:]target] ... ]
  ```

  For the example in this section, the following command applies:

  ```
  $ bitbake mc:x86:core-image-minimal mc:arm:core-image-sato mc::core-image-base
  ```

  The previous BitBake command builds a `core-image-minimal` image that is configured through the `x86.conf` configuration file, a `core-image-sato` image that is configured through the `arm.conf` configuration file and a `core-image-base` that is configured through your `local.conf` configuration file.

> 上一个 BitBake 命令构建了一个通过 `x86.conf` 配置文件配置的 `core-image-minimal` 镜像，一个通过 `arm.conf` 配置文件配置的 `core-image-sato` 镜像，以及一个通过您的 `local.conf` 配置文件配置的 `core-image-base` 镜像。

::: note
::: title
Note
:::

Support for multiple configuration builds in the Yocto Project &DISTRO; (&DISTRO_NAME;) Release does not include Shared State (sstate) optimizations. Consequently, if a build uses the same object twice in, for example, two different `TMPDIR`{.interpreted-text role="term"} directories, the build either loads from an existing sstate cache for that build at the start or builds the object fresh.

> 在 Yocto Project &DISTRO; (&DISTRO_NAME;) Release 中，对多个配置构建的支持不包括 Shared State（sstate）优化。因此，如果构建在两个不同的 `TMPDIR`{.interpreted-text role="term"}目录中使用同一个对象，构建要么从构建的现有 sstate 缓存中加载，要么重新构建该对象。
> :::

## Enabling Multiple Configuration Build Dependencies

Sometimes dependencies can exist between targets (multiconfigs) in a multiple configuration build. For example, suppose that in order to build a `core-image-sato` image for an \"x86\" multiconfig, the root filesystem of an \"arm\" multiconfig must exist. This dependency is essentially that the `ref-tasks-image`{.interpreted-text role="ref"} task in the `core-image-sato` recipe depends on the completion of the `ref-tasks-rootfs`{.interpreted-text role="ref"} task of the `core-image-minimal` recipe.

> 有时候，多种配置构建中的目标（多配置）之间会存在依赖关系。例如，为了构建针对“x86”多配置的“core-image-sato”镜像，必须先存在“arm”多配置的根文件系统。这种依赖实质上是“core-image-sato”配方中的“ref-tasks-image”（参见参考）任务依赖于“core-image-minimal”配方中的“ref-tasks-rootfs”（参见参考）任务的完成。

To enable dependencies in a multiple configuration build, you must declare the dependencies in the recipe using the following statement form:

```
task_or_package[mcdepends] = "mc:from_multiconfig:to_multiconfig:recipe_name:task_on_which_to_depend"
```

To better show how to use this statement, consider the example scenario from the first paragraph of this section. The following statement needs to be added to the recipe that builds the `core-image-sato` image:

> 为了更好地展示如何使用这个声明，请考虑本节第一段的示例场景。 需要添加以下语句到构建“core-image-sato”映像的配方中：

```
do_image[mcdepends] = "mc:x86:arm:core-image-minimal:do_rootfs"
```

In this example, the [from_multiconfig]{.title-ref} is \"x86\". The [to_multiconfig]{.title-ref} is \"arm\". The task on which the `ref-tasks-image`{.interpreted-text role="ref"} task in the recipe depends is the `ref-tasks-rootfs`{.interpreted-text role="ref"} task from the `core-image-minimal` recipe associated with the \"arm\" multiconfig.

> 在这个例子中，[from_multiconfig]{.title-ref}是“x86”。[to_multiconfig]{.title-ref}是“arm”。配方中的 `ref-tasks-image`{.interpreted-text role="ref"}任务所依赖的任务是与“arm”多组配置相关联的 `core-image-minimal` 配方中的 `ref-tasks-rootfs`{.interpreted-text role="ref"}任务。

Once you set up this dependency, you can build the \"x86\" multiconfig using a BitBake command as follows:

```
$ bitbake mc:x86:core-image-sato
```

This command executes all the tasks needed to create the `core-image-sato` image for the \"x86\" multiconfig. Because of the dependency, BitBake also executes through the `ref-tasks-rootfs`{.interpreted-text role="ref"} task for the \"arm\" multiconfig build.

> 这个命令执行所有为 x86 多构架创建 `core-image-sato` 镜像所需的任务。由于依赖性，BitBake 也会通过“arm”多构架的 `ref-tasks-rootfs` 任务执行。

Having a recipe depend on the root filesystem of another build might not seem that useful. Consider this change to the statement in the `core-image-sato` recipe:

> 把另一个构建的根文件系统作为菜谱的依赖可能不是很有用。考虑一下在 `core-image-sato` 菜谱中对这个声明的更改：

```
do_image[mcdepends] = "mc:x86:arm:core-image-minimal:do_image"
```

In this case, BitBake must create the `core-image-minimal` image for the \"arm\" build since the \"x86\" build depends on it.

Because \"x86\" and \"arm\" are enabled for multiple configuration builds and have separate configuration files, BitBake places the artifacts for each build in the respective temporary build directories (i.e. `TMPDIR`{.interpreted-text role="term"}).

> 因为 x86 和 arm 可以用于多种配置构建，并且有单独的配置文件，BitBake 将每个构建的工件放在各自的临时构建目录（即 TMPDIR）中。

# Building an Initial RAM Filesystem (Initramfs) Image

An initial RAM filesystem (`Initramfs`{.interpreted-text role="term"}) image provides a temporary root filesystem used for early system initialization, typically providing tools and loading modules needed to locate and mount the final root filesystem.

> 初始 RAM 文件系统（Initramfs）映像提供一个用于早期系统初始化的临时根文件系统，通常提供用于查找和挂载最终根文件系统所需的工具和加载模块。

Follow these steps to create an `Initramfs`{.interpreted-text role="term"} image:

1. *Create the :term:\`Initramfs\` Image Recipe:* You can reference the `core-image-minimal-initramfs.bb` recipe found in the `meta/recipes-core` directory of the `Source Directory`{.interpreted-text role="term"} as an example from which to work.

> 创建 Initramfs 镜像食谱：您可以参考源代码目录中 meta/recipes-core 目录下的 core-image-minimal-initramfs.bb 食谱作为一个参考例子。

2. *Decide if You Need to Bundle the :term:\`Initramfs\` Image Into the Kernel Image:* If you want the `Initramfs`{.interpreted-text role="term"} image that is built to be bundled in with the kernel image, set the `INITRAMFS_IMAGE_BUNDLE`{.interpreted-text role="term"} variable to `"1"` in your `local.conf` configuration file and set the `INITRAMFS_IMAGE`{.interpreted-text role="term"} variable in the recipe that builds the kernel image.

> 如果您希望将构建的 `Initramfs`{.interpreted-text role="term"}映像与内核映像捆绑在一起，请在您的 `local.conf` 配置文件中将 `INITRAMFS_IMAGE_BUNDLE`{.interpreted-text role="term"}变量设置为 `"1"`，并在构建内核映像的配方中设置 `INITRAMFS_IMAGE`{.interpreted-text role="term"}变量。

Setting the `INITRAMFS_IMAGE_BUNDLE`{.interpreted-text role="term"} flag causes the `Initramfs`{.interpreted-text role="term"} image to be unpacked into the `${B}/usr/` directory. The unpacked `Initramfs`{.interpreted-text role="term"} image is then passed to the kernel\'s `Makefile` using the `CONFIG_INITRAMFS_SOURCE`{.interpreted-text role="term"} variable, allowing the `Initramfs`{.interpreted-text role="term"} image to be built into the kernel normally.

> 设置 `INITRAMFS_IMAGE_BUNDLE` 标志将 `Initramfs` 映像解压缩到 `${B}/usr/` 目录中。然后将解像的 `Initramfs` 映像传递给内核的 `Makefile`，使用 `CONFIG_INITRAMFS_SOURCE` 变量，允许 `Initramfs` 映像正常构建到内核中。

3. *Optionally Add Items to the Initramfs Image Through the Initramfs Image Recipe:* If you add items to the `Initramfs`{.interpreted-text role="term"} image by way of its recipe, you should use `PACKAGE_INSTALL`{.interpreted-text role="term"} rather than `IMAGE_INSTALL`{.interpreted-text role="term"}. `PACKAGE_INSTALL`{.interpreted-text role="term"} gives more direct control of what is added to the image as compared to the defaults you might not necessarily want that are set by the `ref-classes-image`{.interpreted-text role="ref"} or `ref-classes-core-image`{.interpreted-text role="ref"} classes.

> 3. *可选择通过 Initramfs 映像配方添加项目到 Initramfs 映像：* 如果通过其配方添加项目到 `Initramfs`{.interpreted-text role="term"}映像，应该使用 `PACKAGE_INSTALL`{.interpreted-text role="term"}而不是 `IMAGE_INSTALL`{.interpreted-text role="term"}。与使用 `ref-classes-image`{.interpreted-text role="ref"}或 `ref-classes-core-image`{.interpreted-text role="ref"}类设置的默认值相比，`PACKAGE_INSTALL`{.interpreted-text role="term"}提供了更直接的控制权，可以防止添加不必要的项目到映像中。

4. *Build the Kernel Image and the Initramfs Image:* Build your kernel image using BitBake. Because the `Initramfs`{.interpreted-text role="term"} image recipe is a dependency of the kernel image, the `Initramfs`{.interpreted-text role="term"} image is built as well and bundled with the kernel image if you used the `INITRAMFS_IMAGE_BUNDLE`{.interpreted-text role="term"} variable described earlier.

> 4. *构建内核映像和 Initramfs 映像：*使用 BitBake 构建内核映像。由于 Initramfs 映像配方是内核映像的依赖项，因此如果您使用前面描述的 INITRAMFS_IMAGE_BUNDLE 变量，则 Initramfs 映像也会被构建并与内核映像捆绑在一起。

## Bundling an Initramfs Image From a Separate Multiconfig

There may be a case where we want to build an `Initramfs`{.interpreted-text role="term"} image which does not inherit the same distro policy as our main image, for example, we may want our main image to use `TCLIBC="glibc"`, but to use `TCLIBC="musl"` in our `Initramfs`{.interpreted-text role="term"} image to keep a smaller footprint. However, by performing the steps mentioned above the `Initramfs`{.interpreted-text role="term"} image will inherit `TCLIBC="glibc"` without allowing us to override it.

> 可能有一种情况，我们想要构建一个不继承主镜像的同一发行版策略的 Initramfs 映像，例如，我们可能希望主镜像使用 TCLIBC="glibc"，但在 Initramfs 映像中使用 TCLIBC="musl"以保持较小的足迹。但是，通过执行上述步骤， Initramfs 映像将继承 TCLIBC="glibc"，而不允许我们覆盖它。

To achieve this, you need to perform some additional steps:

1. *Create a multiconfig for your Initramfs image:* You can perform the steps on \"`dev-manual/building:building images for multiple targets using multiple configurations`{.interpreted-text role="ref"}\" to create a separate multiconfig. For the sake of simplicity let\'s assume such multiconfig is called: `initramfscfg.conf` and contains the variables:

> 1. *创建 Initramfs 图像的多配置：* 您可以按照“dev-manual/building：使用多个配置为多个目标构建图像”中的步骤来创建单独的多配置。 为了简化起见，让我们假设这样的多配置被称为：`initramfscfg.conf` 并包含变量：

```
TMPDIR="${TOPDIR}/tmp-initramfscfg"
TCLIBC="musl"
```

2. *Set additional Initramfs variables on your main configuration:* Additionally, on your main configuration (`local.conf`) you need to set the variables:

> 2. *在您的主要配置上设置其他 Initramfs 变量：*此外，您需要在主要配置（`local.conf`）上设置变量：

```
INITRAMFS_MULTICONFIG = "initramfscfg"
INITRAMFS_DEPLOY_DIR_IMAGE = "${TOPDIR}/tmp-initramfscfg/deploy/images/${MACHINE}"
```

The variables `INITRAMFS_MULTICONFIG`{.interpreted-text role="term"} and `INITRAMFS_DEPLOY_DIR_IMAGE`{.interpreted-text role="term"} are used to create a multiconfig dependency from the kernel to the `INITRAMFS_IMAGE`{.interpreted-text role="term"} to be built coming from the `initramfscfg` multiconfig, and to let the buildsystem know where the `INITRAMFS_IMAGE`{.interpreted-text role="term"} will be located.

> 变量 `INITRAMFS_MULTICONFIG` 和 `INITRAMFS_DEPLOY_DIR_IMAGE` 用于从内核创建来自 `initramfscfg` 多配置的 `INITRAMFS_IMAGE` 的多配置依赖，并让构建系统知道 `INITRAMFS_IMAGE` 将位于何处。

Building a system with such configuration will build the kernel using the main configuration but the `ref-tasks-bundle_initramfs`{.interpreted-text role="ref"} task will grab the selected `INITRAMFS_IMAGE`{.interpreted-text role="term"} from `INITRAMFS_DEPLOY_DIR_IMAGE`{.interpreted-text role="term"} instead, resulting in a musl based `Initramfs`{.interpreted-text role="term"} image bundled in the kernel but a glibc based main image.

> 建立一个具有这样配置的系统将使用主配置构建内核，但是 `ref-tasks-bundle_initramfs`{.interpreted-text role="ref"} 任务将从 `INITRAMFS_DEPLOY_DIR_IMAGE`{.interpreted-text role="term"} 中抓取选定的 `INITRAMFS_IMAGE`{.interpreted-text role="term"}，导致内核中包含一个基于 musl 的 `Initramfs`{.interpreted-text role="term"}镜像，但主镜像是基于 glibc 的。

The same is applicable to avoid inheriting `DISTRO_FEATURES`{.interpreted-text role="term"} on `INITRAMFS_IMAGE`{.interpreted-text role="term"} or to build a different `DISTRO`{.interpreted-text role="term"} for it such as `poky-tiny`.

> 同样适用于避免继承 INITRAMFS_IMAGE 上的 DISTRO_FEATURES，或者为其编译一个不同的 DISTRO，比如 poky-tiny。

# Building a Tiny System

Very small distributions have some significant advantages such as requiring less on-die or in-package memory (cheaper), better performance through efficient cache usage, lower power requirements due to less memory, faster boot times, and reduced development overhead. Some real-world examples where a very small distribution gives you distinct advantages are digital cameras, medical devices, and small headless systems.

> 非常小的发行版有一些重要的优势，例如需要更少的片上或封装内存（更便宜），通过高效的缓存使用提高性能，由于内存更少而降低功耗，更快的引导时间和减少开发开销。一些实际应用中，非常小的发行版给你带来明显的优势，例如数码相机，医疗设备和小型无头系统。

This section presents information that shows you how you can trim your distribution to even smaller sizes than the `poky-tiny` distribution, which is around 5 Mbytes, that can be built out-of-the-box using the Yocto Project.

> 这一节提供的信息可以帮助您将发行版缩减至比 `poky-tiny` 发行版更小的尺寸，`poky-tiny` 发行版大约有 5M 字节，可以使用 Yocto Project 直接构建。

## Tiny System Overview

The following list presents the overall steps you need to consider and perform to create distributions with smaller root filesystems, achieve faster boot times, maintain your critical functionality, and avoid initial RAM disks:

> 以下列表提供了您需要考虑和执行的步骤，以创建具有较小根文件系统的发行版，实现更快的启动时间，维护您的关键功能，并避免初始 RAM 磁盘：

- `Determine your goals and guiding principles <dev-manual/building:goals and guiding principles>`{.interpreted-text role="ref"}
- `dev-manual/building:understand what contributes to your image size`{.interpreted-text role="ref"}
- `Reduce the size of the root filesystem <dev-manual/building:trim the root filesystem>`{.interpreted-text role="ref"}
- `Reduce the size of the kernel <dev-manual/building:trim the kernel>`{.interpreted-text role="ref"}
- `dev-manual/building:remove package management requirements`{.interpreted-text role="ref"}
- `dev-manual/building:look for other ways to minimize size`{.interpreted-text role="ref"}
- `dev-manual/building:iterate on the process`{.interpreted-text role="ref"}

## Goals and Guiding Principles

Before you can reach your destination, you need to know where you are going. Here is an example list that you can use as a guide when creating very small distributions:

> 在你到达目的地之前，你需要知道你要去哪里。这里有一个示例列表，你可以在创建非常小的发行版时使用它作为指南。

- Determine how much space you need (e.g. a kernel that is 1 Mbyte or less and a root filesystem that is 3 Mbytes or less).
- Find the areas that are currently taking 90% of the space and concentrate on reducing those areas.
- Do not create any difficult \"hacks\" to achieve your goals.
- Leverage the device-specific options.
- Work in a separate layer so that you keep changes isolated. For information on how to create layers, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section.

> 请使用单独的图层来进行更改，以便将更改隔离开来。有关如何创建图层的信息，请参阅“dev-manual/layers：理解和创建图层”部分。

## Understand What Contributes to Your Image Size

It is easiest to have something to start with when creating your own distribution. You can use the Yocto Project out-of-the-box to create the `poky-tiny` distribution. Ultimately, you will want to make changes in your own distribution that are likely modeled after `poky-tiny`.

> 当创建自己的发行版时，最容易的方式是从一些东西开始。您可以使用 Yocto Project 的开箱即用功能来创建 `poky-tiny` 发行版。最终，您可能会根据 `poky-tiny` 来做出自己的发行版上的更改。

::: note
::: title
Note
:::

To use `poky-tiny` in your build, set the `DISTRO`{.interpreted-text role="term"} variable in your `local.conf` file to \"poky-tiny\" as described in the \"`dev-manual/custom-distribution:creating your own distribution`{.interpreted-text role="ref"}\" section.

> 要在您的构建中使用 `poky-tiny`，请按照“dev-manual / custom-distribution：creating your own distribution”部分中的说明，将您的 `local.conf` 文件中的 `DISTRO` 变量设置为“poky-tiny”。
> :::

Understanding some memory concepts will help you reduce the system size. Memory consists of static, dynamic, and temporary memory. Static memory is the TEXT (code), DATA (initialized data in the code), and BSS (uninitialized data) sections. Dynamic memory represents memory that is allocated at runtime: stacks, hash tables, and so forth. Temporary memory is recovered after the boot process. This memory consists of memory used for decompressing the kernel and for the `__init__` functions.

> 理解一些内存概念将有助于减少系统尺寸。内存包括静态、动态和临时内存。静态内存是文本（代码）、数据（代码中的初始化数据）和 BSS（未初始化数据）部分。动态内存表示在运行时分配的内存：堆栈、哈希表等。临时内存在启动过程结束后被回收。这种内存包括用于解压内核和 `__init__` 函数的内存。

To help you see where you currently are with kernel and root filesystem sizes, you can use two tools found in the `Source Directory`{.interpreted-text role="term"} in the `scripts/tiny/` directory:

> 为了帮助您查看当前内核和根文件系统大小，您可以使用 `源目录` 中 `scripts/tiny/` 目录中的两个工具：

- `ksize.py`: Reports component sizes for the kernel build objects.
- `dirsize.py`: Reports component sizes for the root filesystem.

This next tool and command help you organize configuration fragments and view file dependencies in a human-readable form:

- `merge_config.sh`: Helps you manage configuration files and fragments within the kernel. With this tool, you can merge individual configuration fragments together. The tool allows you to make overrides and warns you of any missing configuration options. The tool is ideal for allowing you to iterate on configurations, create minimal configurations, and create configuration files for different machines without having to duplicate your process.

> `merge_config.sh`：帮助您管理内核中的配置文件和片段。使用此工具，您可以将单个配置片段合并在一起。该工具允许您进行覆盖，并警告您缺少任何配置选项。该工具非常适合允许您迭代配置、创建最小配置，以及为不同计算机创建配置文件而无需重复您的过程。

The `merge_config.sh` script is part of the Linux Yocto kernel Git repositories (i.e. `linux-yocto-3.14`, `linux-yocto-3.10`, `linux-yocto-3.8`, and so forth) in the `scripts/kconfig` directory.

> 脚本 `merge_config.sh` 是 Linux Yocto 内核 Git 存储库（即 `linux-yocto-3.14`、`linux-yocto-3.10`、`linux-yocto-3.8` 等）中的 `scripts/kconfig` 目录的一部分。

For more information on configuration fragments, see the \"`kernel-dev/common:creating configuration fragments`{.interpreted-text role="ref"}\" section in the Yocto Project Linux Kernel Development Manual.

> 要了解有关配置片段的更多信息，请参阅 Yocto 项目 Linux 内核开发手册中的“kernel-dev/common：创建配置片段”部分。

- `bitbake -u taskexp -g bitbake_target`: Using the BitBake command with these options brings up a Dependency Explorer from which you can view file dependencies. Understanding these dependencies allows you to make informed decisions when cutting out various pieces of the kernel and root filesystem.

> 使用这些选项的 BitBake 命令会打开一个依赖项浏览器，您可以查看文件依赖关系。了解这些依赖关系可以使您在削减内核和根文件系统的各个部分时做出明智的决定。

## Trim the Root Filesystem

The root filesystem is made up of packages for booting, libraries, and applications. To change things, you can configure how the packaging happens, which changes the way you build them. You can also modify the filesystem itself or select a different filesystem.

> 根文件系统由用于引导、库和应用程序的软件包组成。要更改内容，您可以配置软件包的方式，从而改变构建它们的方式。您还可以修改文件系统本身或选择不同的文件系统。

First, find out what is hogging your root filesystem by running the `dirsize.py` script from your root directory:

```
$ cd root-directory-of-image
$ dirsize.py 100000 > dirsize-100k.log
$ cat dirsize-100k.log
```

You can apply a filter to the script to ignore files under a certain size. The previous example filters out any files below 100 Kbytes. The sizes reported by the tool are uncompressed, and thus will be smaller by a relatively constant factor in a compressed root filesystem. When you examine your log file, you can focus on areas of the root filesystem that take up large amounts of memory.

> 你可以给脚本应用一个过滤器，以忽略某个特定大小以下的文件。前面的例子过滤掉了 100 Kbytes 以下的所有文件。这个工具报告的大小是未压缩的，因此在压缩的根文件系统中会有一个相对恒定的因子。当你检查你的日志文件时，你可以专注于那些占用大量内存的根文件系统区域。

You need to be sure that what you eliminate does not cripple the functionality you need. One way to see how packages relate to each other is by using the Dependency Explorer UI with the BitBake command:

> 你需要确保你消除的东西不会削弱你所需要的功能。一种查看包之间的关系的方法是使用 BitBake 命令的依赖关系浏览器 UI。

```
$ cd image-directory
$ bitbake -u taskexp -g image
```

Use the interface to select potential packages you wish to eliminate and see their dependency relationships.

When deciding how to reduce the size, get rid of packages that result in minimal impact on the feature set. For example, you might not need a VGA display. Or, you might be able to get by with `devtmpfs` and `mdev` instead of `udev`.

> 在决定如何减小尺寸时，删除对功能集的影响最小的包。例如，您可能不需要 VGA 显示器。或者，您可以使用 `devtmpfs` 和 `mdev` 而不是 `udev`。

Use your `local.conf` file to make changes. For example, to eliminate `udev` and `glib`, set the following in the local configuration file:

```
VIRTUAL-RUNTIME_dev_manager = ""
```

Finally, you should consider exactly the type of root filesystem you need to meet your needs while also reducing its size. For example, consider `cramfs`, `squashfs`, `ubifs`, `ext2`, or an `Initramfs`{.interpreted-text role="term"} using `initramfs`. Be aware that `ext3` requires a 1 Mbyte journal. If you are okay with running read-only, you do not need this journal.

> 最后，您应该考虑恰当的根文件系统类型，以满足您的需求，同时减小其大小。例如，考虑使用 cramfs，squashfs，ubifs，ext2 或 Initramfs 来使用 initramfs。请注意，ext3 需要 1 M 字节的日志。如果您只允许读取，则不需要此日志。

::: note
::: title
Note
:::

After each round of elimination, you need to rebuild your system and then use the tools to see the effects of your reductions.
:::

## Trim the Kernel

The kernel is built by including policies for hardware-independent aspects. What subsystems do you enable? For what architecture are you building? Which drivers do you build by default?

> 内核是通过包括硬件独立方面的策略而构建的。您要启用哪些子系统？您正在构建哪种体系结构？您默认情况下要构建哪些驱动程序？

::: note
::: title
Note
:::

You can modify the kernel source if you want to help with boot time.
:::

Run the `ksize.py` script from the top-level Linux build directory to get an idea of what is making up the kernel:

```
$ cd top-level-linux-build-directory
$ ksize.py > ksize.log
$ cat ksize.log
```

When you examine the log, you will see how much space is taken up with the built-in `.o` files for drivers, networking, core kernel files, filesystem, sound, and so forth. The sizes reported by the tool are uncompressed, and thus will be smaller by a relatively constant factor in a compressed kernel image. Look to reduce the areas that are large and taking up around the \"90% rule.\"

> 当您检查日志时，您将看到驱动程序，网络，核心内核文件，文件系统，声音等内置的 `.o` 文件占用了多少空间。该工具报告的大小是未压缩的，因此压缩内核映像时会比较小一个相对恒定的因子。请参照“90％规则”来减少大小和占用空间的区域。

To examine, or drill down, into any particular area, use the `-d` option with the script:

```
$ ksize.py -d > ksize.log
```

Using this option breaks out the individual file information for each area of the kernel (e.g. drivers, networking, and so forth).

Use your log file to see what you can eliminate from the kernel based on features you can let go. For example, if you are not going to need sound, you do not need any drivers that support sound.

> 使用您的日志文件来查看您可以根据可以放弃的功能从内核中消除什么。例如，如果您不需要声音，则不需要任何支持声音的驱动程序。

After figuring out what to eliminate, you need to reconfigure the kernel to reflect those changes during the next build. You could run `menuconfig` and make all your changes at once. However, that makes it difficult to see the effects of your individual eliminations and also makes it difficult to replicate the changes for perhaps another target device. A better method is to start with no configurations using `allnoconfig`, create configuration fragments for individual changes, and then manage the fragments into a single configuration file using `merge_config.sh`. The tool makes it easy for you to iterate using the configuration change and build cycle.

> 在确定要消除什么之后，您需要在下次构建时重新配置内核以反映这些变化。您可以运行 `menuconfig` 并一次性进行所有更改。但是，这使得很难看到单个消除的效果，也使得很难复制可能用于另一个目标设备的更改。更好的方法是使用 `allnoconfig` 从无配置开始，为单个更改创建配置片段，然后使用 `merge_config.sh` 将片段管理到单个配置文件中。该工具可以轻松地使用配置更改和构建循环进行迭代。

Each time you make configuration changes, you need to rebuild the kernel and check to see what impact your changes had on the overall size.

## Remove Package Management Requirements

Packaging requirements add size to the image. One way to reduce the size of the image is to remove all the packaging requirements from the image. This reduction includes both removing the package manager and its unique dependencies as well as removing the package management data itself.

> 要求包装会增加图像的大小。减小图像大小的一种方法是删除所有的包装要求。这种减少包括删除包管理器及其独特的依赖关系，以及删除包管理数据本身。

To eliminate all the packaging requirements for an image, be sure that \"package-management\" is not part of your `IMAGE_FEATURES`{.interpreted-text role="term"} statement for the image. When you remove this feature, you are removing the package manager as well as its dependencies from the root filesystem.

> 要消除图像的所有包装要求，请确保图像的“ IMAGE_FEATURES”语句中不包含“ package-management”。删除此功能时，您将从根文件系统中删除包管理器及其依赖项。

## Look for Other Ways to Minimize Size

Depending on your particular circumstances, other areas that you can trim likely exist. The key to finding these areas is through tools and methods described here combined with experimentation and iteration. Here are a couple of areas to experiment with:

> 根据您的特殊情况，可能还存在其他可以削减的领域。找到这些领域的关键是通过这里描述的工具和方法，结合实验和迭代。这里有几个可以进行实验的领域：

- `glibc`: In general, follow this process:
  1. Remove `glibc` features from `DISTRO_FEATURES`{.interpreted-text role="term"} that you think you do not need.
  2. Build your distribution.
  3. If the build fails due to missing symbols in a package, determine if you can reconfigure the package to not need those features. For example, change the configuration to not support wide character support as is done for `ncurses`. Or, if support for those characters is needed, determine what `glibc` features provide the support and restore the configuration.

> 如果由于包中缺少符号而导致构建失败，请确定是否可以重新配置该包以不需要这些功能。例如，更改配置以不支持宽字符支持，就像 `ncurses` 所做的那样。或者，如果需要这些字符的支持，请确定 `glibc` 功能提供了哪些支持，并恢复配置。

4. Rebuild and repeat the process.

- `busybox`: For BusyBox, use a process similar as described for `glibc`. A difference is you will need to boot the resulting system to see if you are able to do everything you expect from the running system. You need to be sure to integrate configuration fragments into Busybox because BusyBox handles its own core features and then allows you to add configuration fragments on top.

> 对于 BusyBox，使用与描述的 `glibc` 类似的过程。不同之处在于，您需要启动生成的系统，以查看您是否能够从运行系统中完成所有您期望的操作。您需要确保将配置片段集成到 Busybox 中，因为 BusyBox 处理其自己的核心功能，然后允许您在其上添加配置片段。

## Iterate on the Process

If you have not reached your goals on system size, you need to iterate on the process. The process is the same. Use the tools and see just what is taking up 90% of the root filesystem and the kernel. Decide what you can eliminate without limiting your device beyond what you need.

> 如果您尚未达到系统大小的目标，您需要在流程上迭代。流程是相同的。使用工具，查看正在占用根文件系统和内核的 90％。决定您可以在不限制您所需的设备的情况下消除什么。

Depending on your system, a good place to look might be Busybox, which provides a stripped down version of Unix tools in a single, executable file. You might be able to drop virtual terminal services or perhaps ipv6.

> 根据您的系统，一个很好的地方可能是 Busybox，它提供了一个单一的可执行文件，其中包含精简版的 Unix 工具。您可能可以删除虚拟终端服务或者 IPv6。

# Building Images for More than One Machine

A common scenario developers face is creating images for several different machines that use the same software environment. In this situation, it is tempting to set the tunings and optimization flags for each build specifically for the targeted hardware (i.e. \"maxing out\" the tunings). Doing so can considerably add to build times and package feed maintenance collectively for the machines. For example, selecting tunes that are extremely specific to a CPU core used in a system might enable some micro optimizations in GCC for that particular system but would otherwise not gain you much of a performance difference across the other systems as compared to using a more general tuning across all the builds (e.g. setting `DEFAULTTUNE`{.interpreted-text role="term"} specifically for each machine\'s build). Rather than \"max out\" each build\'s tunings, you can take steps that cause the OpenEmbedded build system to reuse software across the various machines where it makes sense.

> 开发人员面临的一个常见场景是为使用相同软件环境的多台机器创建图像。在这种情况下，很容易诱惑设置每个构建的调优和优化标志特定于目标硬件（即“最大化”调优）。这样做可以显着增加构建时间和所有机器的软件包发布维护。例如，专门选择适用于系统中使用的 CPU 核心的调整可能会为该特定系统启用 GCC 中的一些微调，但与在所有构建中使用更一般的调整相比，在其他系统上获得的性能差异不大（例如，为每台机器的构建特别设置 `DEFAULTTUNE`{.interpreted-text role="term"}）。与“最大化”每个构建的调整相比，您可以采取措施，使 OpenEmbedded 构建系统在合理的情况下在各种机器上重用软件。

If build speed and package feed maintenance are considerations, you should consider the points in this section that can help you optimize your tunings to best consider build times and package feed maintenance.

> 如果考虑构建速度和包装料料维护，您应该考虑本节中可以帮助您优化调整以最佳考虑构建时间和包装料料维护的点。

- *Share the :term:\`Build Directory\`:* If at all possible, share the `TMPDIR`{.interpreted-text role="term"} across builds. The Yocto Project supports switching between different `MACHINE`{.interpreted-text role="term"} values in the same `TMPDIR`{.interpreted-text role="term"}. This practice is well supported and regularly used by developers when building for multiple machines. When you use the same `TMPDIR`{.interpreted-text role="term"} for multiple machine builds, the OpenEmbedded build system can reuse the existing native and often cross-recipes for multiple machines. Thus, build time decreases.

> 尽可能地共享 `Build Directory`{.interpreted-text role="term"}。Yocto 项目支持在同一 `TMPDIR`{.interpreted-text role="term"}中切换不同的 `MACHINE`{.interpreted-text role="term"}值。当开发人员为多台机器构建时，这种做法得到很好的支持并经常被使用。当您为多台机器使用相同的 `TMPDIR`{.interpreted-text role="term"}时，OpenEmbedded 构建系统可以重用现有的本机和跨机器配方。因此，构建时间会减少。

::: note
::: title
Note
:::

If `DISTRO`{.interpreted-text role="term"} settings change or fundamental configuration settings such as the filesystem layout, you need to work with a clean `TMPDIR`{.interpreted-text role="term"}. Sharing `TMPDIR`{.interpreted-text role="term"} under these circumstances might work but since it is not guaranteed, you should use a clean `TMPDIR`{.interpreted-text role="term"}.

> 如果 `DISTRO` 设置更改或基本配置设置（如文件系统布局）发生变化，您需要使用一个干净的 `TMPDIR`。在这种情况下共享 `TMPDIR` 可能会有效，但由于不能保证，您应该使用一个干净的 `TMPDIR`。
> :::

- *Enable the Appropriate Package Architecture:* By default, the OpenEmbedded build system enables three levels of package architectures: \"all\", \"tune\" or \"package\", and \"machine\". Any given recipe usually selects one of these package architectures (types) for its output. Depending for what a given recipe creates packages, making sure you enable the appropriate package architecture can directly impact the build time.

> 启用适当的软件包架构：默认情况下，OpenEmbedded 构建系统启用三个层次的软件包架构：“all”、“tune”或“package”和“machine”。任何给定的配方通常会为其输出选择其中一个软件包架构（类型）。根据给定配方创建的软件包，确保启用适当的软件包架构可以直接影响构建时间。

A recipe that just generates scripts can enable \"all\" architecture because there are no binaries to build. To specifically enable \"all\" architecture, be sure your recipe inherits the `ref-classes-allarch`{.interpreted-text role="ref"} class. This class is useful for \"all\" architectures because it configures many variables so packages can be used across multiple architectures.

> 一个只生成脚本的配方可以启用“所有”架构，因为没有要构建的二进制文件。要特别启用“所有”架构，请确保您的配方继承 `ref-classes-allarch`{.interpreted-text role="ref"}类。此类对于“所有”架构非常有用，因为它配置了许多变量，因此可以在多个架构上使用软件包。

If your recipe needs to generate packages that are machine-specific or when one of the build or runtime dependencies is already machine-architecture dependent, which makes your recipe also machine-architecture dependent, make sure your recipe enables the \"machine\" package architecture through the `MACHINE_ARCH`{.interpreted-text role="term"} variable:

> 如果你的食谱需要生成特定机器的包，或者其中一个构建或运行时依赖已经是机器架构依赖的，这使得你的食谱也变成了机器架构依赖的，请确保你的食谱通过 `MACHINE_ARCH` 变量启用“机器”包架构：

```
PACKAGE_ARCH = "${MACHINE_ARCH}"
```

When you do not specifically enable a package architecture through the `PACKAGE_ARCH`{.interpreted-text role="term"}, The OpenEmbedded build system defaults to the `TUNE_PKGARCH`{.interpreted-text role="term"} setting:

> 当您没有通过 `PACKAGE_ARCH`{.interpreted-text role="term"}特别启用包架构时，OpenEmbedded 构建系统将默认为 `TUNE_PKGARCH`{.interpreted-text role="term"}设置。

```
PACKAGE_ARCH = "${TUNE_PKGARCH}"
```

- *Choose a Generic Tuning File if Possible:* Some tunes are more generic and can run on multiple targets (e.g. an `armv5` set of packages could run on `armv6` and `armv7` processors in most cases). Similarly, `i486` binaries could work on `i586` and higher processors. You should realize, however, that advances on newer processor versions would not be used.

> 选择一个通用的调整文件如果可能的话：有些调整可以在多个目标上运行更加通用（例如一组 `armv5` 的程序包可以在 `armv6` 和 `armv7` 处理器上大多数情况下运行）。同样，`i486` 二进制文件可以在 `i586` 及更高版本处理器上运行。但是你应该意识到，在更新的处理器版本上的进步不会被使用。

If you select the same tune for several different machines, the OpenEmbedded build system reuses software previously built, thus speeding up the overall build time. Realize that even though a new sysroot for each machine is generated, the software is not recompiled and only one package feed exists.

> 如果您为多台不同的机器选择相同的调谐，OpenEmbedded 构建系统将重用先前构建的软件，从而加快整体构建时间。要知道，即使为每台机器生成一个新的 sysroot，也不会重新编译软件，只有一个包含反馈。

- *Manage Granular Level Packaging:* Sometimes there are cases where injecting another level of package architecture beyond the three higher levels noted earlier can be useful. For example, consider how NXP (formerly Freescale) allows for the easy reuse of binary packages in their layer :yocto\_[git:%60meta-freescale](git:%60meta-freescale) \</meta-freescale/\>[. In this example, the :yocto_git:\`fsl-dynamic-packagearch \</meta-freescale/tree/classes/fsl-dynamic-packagearch.bbclass\>]{.title-ref} class shares GPU packages for i.MX53 boards because all boards share the AMD GPU. The i.MX6-based boards can do the same because all boards share the Vivante GPU. This class inspects the BitBake datastore to identify if the package provides or depends on one of the sub-architecture values. If so, the class sets the `PACKAGE_ARCH`{.interpreted-text role="term"} value based on the `MACHINE_SUBARCH` value. If the package does not provide or depend on one of the sub-architecture values but it matches a value in the machine-specific filter, it sets `MACHINE_ARCH`{.interpreted-text role="term"}. This behavior reduces the number of packages built and saves build time by reusing binaries.

> 管理粒度级别的打包：有时候，在前面提到的三个高级别之外注入另一个包架构层次可能会很有用。例如，考虑 NXP（原 Freescale）如何在其层：yocto_[git:％60meta-freescale](git:%EF%BC%8560meta-freescale) \ </ meta-freescale / \>中轻松重用二进制包。在这个例子中，:yocto_git: \`fsl-dynamic-packagearch \ </ meta-freescale / tree / classes / fsl-dynamic-packagearch.bbclass \> \]{.title-ref}类共享 AMD GPU 的 i.MX53 板的 GPU 包，因为所有板都共享 AMD GPU。基于 i.MX6 的板也可以做到这一点，因为所有板都共享 Vivante GPU。该类检查 BitBake 数据存储以确定包是否提供或依赖于子架构值之一。如果是这样，则该类基于 `MACHINE_SUBARCH` 值设置 `PACKAGE_ARCH`{.interpreted-text role="term"}值。如果包不提供或不依赖于子架构值，但它与机器特定过滤器中的值匹配，则设置 `MACHINE_ARCH`{.interpreted-text role="term"}。这种行为减少了构建的包数量，并通过重用二进制文件节省了构建时间。

- *Use Tools to Debug Issues:* Sometimes you can run into situations where software is being rebuilt when you think it should not be. For example, the OpenEmbedded build system might not be using shared state between machines when you think it should be. These types of situations are usually due to references to machine-specific variables such as `MACHINE`{.interpreted-text role="term"}, `SERIAL_CONSOLES`{.interpreted-text role="term"}, `XSERVER`{.interpreted-text role="term"}, `MACHINE_FEATURES`{.interpreted-text role="term"}, and so forth in code that is supposed to only be tune-specific or when the recipe depends (`DEPENDS`{.interpreted-text role="term"}, `RDEPENDS`{.interpreted-text role="term"}, `RRECOMMENDS`{.interpreted-text role="term"}, `RSUGGESTS`{.interpreted-text role="term"}, and so forth) on some other recipe that already has `PACKAGE_ARCH`{.interpreted-text role="term"} defined as \"\${MACHINE_ARCH}\".

> 使用工具调试问题：有时候，当您认为软件不应该重新构建时，您可能会遇到这种情况。例如，当您认为 OpenEmbedded 构建系统应该在多台机器之间共享状态时，它可能不会使用共享状态。这些类型的情况通常是由于在代码中引用了特定于机器的变量，例如 `MACHINE`{.interpreted-text role="term"}，`SERIAL_CONSOLES`{.interpreted-text role="term"}，`XSERVER`{.interpreted-text role="term"}，`MACHINE_FEATURES`{.interpreted-text role="term"}等，而这些代码应该只是调整特定的，或者当配方依赖（`DEPENDS`{.interpreted-text role="term"}，`RDEPENDS`{.interpreted-text role="term"}，`RRECOMMENDS`{.interpreted-text role="term"}，`RSUGGESTS`{.interpreted-text role="term"}等）某个其他配方时，该配方已将 `PACKAGE_ARCH`{.interpreted-text role="term"}定义为“\${MACHINE_ARCH}”。

::: note
::: title
Note
:::

Patches to fix any issues identified are most welcome as these issues occasionally do occur.
:::

For such cases, you can use some tools to help you sort out the situation:

- `state-diff-machines.sh`*:* You can find this tool in the `scripts` directory of the Source Repositories. See the comments in the script for information on how to use the tool.

> 你可以在源代码仓库的 `脚本` 目录中找到这个工具 - `state-diff-machines.sh`*。有关如何使用此工具的信息，请参阅脚本中的注释。

- *BitBake\'s \"-S printdiff\" Option:* Using this option causes BitBake to try to establish the closest signature match it can (e.g. in the shared state cache) and then run `bitbake-diffsigs` over the matches to determine the stamps and delta where these two stamp trees diverge.

> - BitBake 的“-S printdiff”选项：使用此选项会导致 BitBake 尝试建立最接近的签名匹配（例如在共享状态缓存中），然后在匹配上运行 `bitbake-diffsigs` 来确定这两个戳树的分歧。

# Building Software from an External Source

By default, the OpenEmbedded build system uses the `Build Directory`{.interpreted-text role="term"} when building source code. The build process involves fetching the source files, unpacking them, and then patching them if necessary before the build takes place.

> 默认情况下，OpenEmbedded 构建系统在构建源代码时使用“构建目录”。构建过程包括获取源文件、解压缩它们，如果有必要，还要对它们进行补丁，然后才开始构建。

There are situations where you might want to build software from source files that are external to and thus outside of the OpenEmbedded build system. For example, suppose you have a project that includes a new BSP with a heavily customized kernel. And, you want to minimize exposing the build system to the development team so that they can focus on their project and maintain everyone\'s workflow as much as possible. In this case, you want a kernel source directory on the development machine where the development occurs. You want the recipe\'s `SRC_URI`{.interpreted-text role="term"} variable to point to the external directory and use it as is, not copy it.

> 在某些情况下，您可能希望从 OpenEmbedded 构建系统之外的外部源文件构建软件。例如，假设您的项目包括具有大量定制内核的新 BSP。并且，您希望将构建系统尽可能少地暴露给开发团队，以便他们可以专注于自己的项目并最大限度地保持每个人的工作流程。在这种情况下，您希望开发机器上有一个内核源目录，用于发生开发。您希望食谱的“SRC_URI”{.interpreted-text role="term"}变量指向外部目录并直接使用，而不是复制它。

To build from software that comes from an external source, all you need to do is inherit the `ref-classes-externalsrc`{.interpreted-text role="ref"} class and then set the `EXTERNALSRC`{.interpreted-text role="term"} variable to point to your external source code. Here are the statements to put in your `local.conf` file:

> 要从外部来源构建软件，您所需要做的就是继承 `ref-classes-externalsrc`{.interpreted-text role="ref"}类，然后将 `EXTERNALSRC`{.interpreted-text role="term"}变量指向您的外部源代码。 这里是要放入 `local.conf` 文件中的语句：

```
INHERIT += "externalsrc"
EXTERNALSRC:pn-myrecipe = "path-to-your-source-tree"
```

This next example shows how to accomplish the same thing by setting `EXTERNALSRC`{.interpreted-text role="term"} in the recipe itself or in the recipe\'s append file:

> 下一个示例展示了如何通过在配方本身或配方附加文件中设置 EXTERNALSRC 来实现同样的目的：

```
EXTERNALSRC = "path"
EXTERNALSRC_BUILD = "path"
```

::: note
::: title
Note
:::

In order for these settings to take effect, you must globally or locally inherit the `ref-classes-externalsrc`{.interpreted-text role="ref"} class.
:::

By default, `ref-classes-externalsrc`{.interpreted-text role="ref"} builds the source code in a directory separate from the external source directory as specified by `EXTERNALSRC`{.interpreted-text role="term"}. If you need to have the source built in the same directory in which it resides, or some other nominated directory, you can set `EXTERNALSRC_BUILD`{.interpreted-text role="term"} to point to that directory:

> 默认情况下，`ref-classes-externalsrc`{.interpreted-text role="ref"}会在 `EXTERNALSRC`{.interpreted-text role="term"}指定的外部源代码目录之外的另一个目录中构建源代码。如果需要在源代码所在的目录或其他指定的目录中构建源代码，可以将 `EXTERNALSRC_BUILD`{.interpreted-text role="term"}设置为指向该目录。

```
EXTERNALSRC_BUILD:pn-myrecipe = "path-to-your-source-tree"
```

# Replicating a Build Offline

It can be useful to take a \"snapshot\" of upstream sources used in a build and then use that \"snapshot\" later to replicate the build offline. To do so, you need to first prepare and populate your downloads directory your \"snapshot\" of files. Once your downloads directory is ready, you can use it at any time and from any machine to replicate your build.

> 可以有用地采取用于构建的上游源的“快照”，然后稍后使用该“快照”离线复制构建。为此，您需要首先准备并填充下载目录您的“快照”文件。准备好下载目录后，您可以随时随地从任何机器复制构建。

Follow these steps to populate your Downloads directory:

1. *Create a Clean Downloads Directory:* Start with an empty downloads directory (`DL_DIR`{.interpreted-text role="term"}). You start with an empty downloads directory by either removing the files in the existing directory or by setting `DL_DIR`{.interpreted-text role="term"} to point to either an empty location or one that does not yet exist.

> 1.*创建一个干净的下载目录：*从一个空的下载目录（`DL_DIR`{.interpreted-text role="term"}）开始。您可以通过删除现有目录中的文件或将 `DL_DIR`{.interpreted-text role="term"}指向一个空位置或一个尚不存在的位置来开始一个空的下载目录。

2. *Generate Tarballs of the Source Git Repositories:* Edit your `local.conf` configuration file as follows:

   ```
   DL_DIR = "/home/your-download-dir/"
   BB_GENERATE_MIRROR_TARBALLS = "1"
   ```

   During the fetch process in the next step, BitBake gathers the source files and creates tarballs in the directory pointed to by `DL_DIR`{.interpreted-text role="term"}. See the `BB_GENERATE_MIRROR_TARBALLS`{.interpreted-text role="term"} variable for more information.

> 在下一步的抓取过程中，BitBake 收集源文件并在由 `DL_DIR`{.interpreted-text role="term"}指向的目录中创建 tarball。有关更多信息，请参阅 `BB_GENERATE_MIRROR_TARBALLS`{.interpreted-text role="term"}变量。

3. *Populate Your Downloads Directory Without Building:* Use BitBake to fetch your sources but inhibit the build:

   ```
   $ bitbake target --runonly=fetch
   ```

   The downloads directory (i.e. `${DL_DIR}`) now has a \"snapshot\" of the source files in the form of tarballs, which can be used for the build.
4. *Optionally Remove Any Git or other SCM Subdirectories From the Downloads Directory:* If you want, you can clean up your downloads directory by removing any Git or other Source Control Management (SCM) subdirectories such as `${DL_DIR}/git2/*`. The tarballs already contain these subdirectories.

> 如果您愿意，您可以通过移除下载目录中的任何 Git 或其他源代码控制管理（SCM）子目录（例如 `${DL_DIR}/git2/*`）来清理您的下载目录。这些 tarballs 已经包含这些子目录。

Once your downloads directory has everything it needs regarding source files, you can create your \"own-mirror\" and build your target. Understand that you can use the files to build the target offline from any machine and at any time.

> 一旦你的下载目录中有了所有关于源文件的东西，你就可以创建你自己的“镜像”并构建你的目标。要明白，你可以使用这些文件从任何机器和任何时间离线构建目标。

Follow these steps to build your target using the files in the downloads directory:

1. *Using Local Files Only:* Inside your `local.conf` file, add the `SOURCE_MIRROR_URL`{.interpreted-text role="term"} variable, inherit the `ref-classes-own-mirrors`{.interpreted-text role="ref"} class, and use the `BB_NO_NETWORK`{.interpreted-text role="term"} variable to your `local.conf`:

> 在您的 `local.conf` 文件中，添加 `SOURCE_MIRROR_URL`{.interpreted-text role="term"}变量，继承 `ref-classes-own-mirrors`{.interpreted-text role="ref"}类，并将 `BB_NO_NETWORK`{.interpreted-text role="term"}变量添加到您的 `local.conf`：

```
SOURCE_MIRROR_URL ?= "file:///home/your-download-dir/"
INHERIT += "own-mirrors"
BB_NO_NETWORK = "1"
```

The `SOURCE_MIRROR_URL`{.interpreted-text role="term"} and `ref-classes-own-mirrors`{.interpreted-text role="ref"} class set up the system to use the downloads directory as your \"own mirror\". Using the `BB_NO_NETWORK`{.interpreted-text role="term"} variable makes sure that BitBake\'s fetching process in step 3 stays local, which means files from your \"own-mirror\" are used.

> `SOURCE_MIRROR_URL`{.interpreted-text role="term"} 和 `ref-classes-own-mirrors`{.interpreted-text role="ref"} 类将系统设置为使用下载目录作为您的“自有镜像”。使用 `BB_NO_NETWORK`{.interpreted-text role="term"}变量可确保步骤 3 中 BitBake 的获取过程保持本地，这意味着将使用来自您“自有镜像”的文件。

2. *Start With a Clean Build:* You can start with a clean build by removing the `${``TMPDIR`{.interpreted-text role="term"}`}` directory or using a new `Build Directory`{.interpreted-text role="term"}.

> 2. *从清理构建开始：*您可以通过删除 `${TMPDIR}` 目录或使用新的构建目录来开始清理构建。

3. *Build Your Target:* Use BitBake to build your target:

   ```
   $ bitbake target
   ```

   The build completes using the known local \"snapshot\" of source files from your mirror. The resulting tarballs for your \"snapshot\" of source files are in the downloads directory.

> 构建使用您镜像中已知的本地源文件“快照”完成。您源文件“快照”的生成 tarball 位于下载目录中。

::: note
::: title
Note
:::

The offline build does not work if recipes attempt to find the latest version of software by setting `SRCREV`{.interpreted-text role="term"} to `${``AUTOREV`{.interpreted-text role="term"}`}`:

> 离线构建如果尝试通过将 SRCREV 设置为 ${AUTOREV}来查找最新版本的软件将不起作用。

```
SRCREV = "${AUTOREV}"
```

When a recipe sets `SRCREV`{.interpreted-text role="term"} to `${``AUTOREV`{.interpreted-text role="term"}`}`, the build system accesses the network in an attempt to determine the latest version of software from the SCM. Typically, recipes that use `AUTOREV`{.interpreted-text role="term"} are custom or modified recipes. Recipes that reside in public repositories usually do not use `AUTOREV`{.interpreted-text role="term"}.

> 当一个配方设置 SRCREV 为 ${AUTOREV}时，构建系统会尝试从源代码管理系统中获取最新版本的软件。通常，使用 AUTOREV 的配方是自定义或修改过的配方。位于公共存储库中的配方通常不使用 AUTOREV。

If you do have recipes that use `AUTOREV`{.interpreted-text role="term"}, you can take steps to still use the recipes in an offline build. Do the following:

> 如果您有使用 `AUTOREV`{.interpreted-text role="term"}的食谱，您可以采取措施仍然在离线构建中使用这些食谱。请按照以下步骤操作：

1. Use a configuration generated by enabling `build history <dev-manual/build-quality:maintaining build output quality>`{.interpreted-text role="ref"}.

> 使用通过启用“构建历史 <dev-manual/build-quality：维护构建输出质量 >”生成的配置。

2. Use the `buildhistory-collect-srcrevs` command to collect the stored `SRCREV`{.interpreted-text role="term"} values from the build\'s history. For more information on collecting these values, see the \"`dev-manual/build-quality:build history package information`{.interpreted-text role="ref"}\" section.

> 使用 `buildhistory-collect-srcrevs` 命令从构建的历史记录中收集存储的 `SRCREV` 值。有关收集这些值的更多信息，请参见“dev-manual/build-quality：构建历史软件包信息”部分。

3. Once you have the correct source revisions, you can modify those recipes to set `SRCREV`{.interpreted-text role="term"} to specific versions of the software.

> 一旦您有了正确的源版本，您就可以修改这些食谱，将 `SRCREV` 设置为软件的特定版本。
> :::
