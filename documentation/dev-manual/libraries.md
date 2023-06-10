---
tip: translate by openai@2023-06-10 11:04:11
...
---
title: Working With Libraries
-----------------------------

Libraries are an integral part of your system. This section describes some common practices you might find helpful when working with libraries to build your system:

> 图书馆是你系统的重要组成部分。本节描述了一些在使用图书馆构建系统时可能会有所帮助的常见做法。

- `How to include static library files <dev-manual/libraries:including static library files>`{.interpreted-text role="ref"}
- `How to use the Multilib feature to combine multiple versions of library files into a single image <dev-manual/libraries:combining multiple versions of library files into one image>`{.interpreted-text role="ref"}

> 如何使用多库特性将多个版本的库文件组合成一个图像 <dev-manual/libraries:combining multiple versions of library files into one image>？

- `How to install multiple versions of the same library in parallel on the same system <dev-manual/libraries:installing multiple versions of the same library>`{.interpreted-text role="ref"}

> 如何在同一系统上并行安装同一库的多个版本？<dev-manual/libraries:installing multiple versions of the same library>`{.interpreted-text role="ref"}

# Including Static Library Files

If you are building a library and the library offers static linking, you can control which static library files (`*.a` files) get included in the built library.

> 如果您正在构建一个库，并且该库提供静态链接，您可以控制哪些静态库文件（`*.a` 文件）包含在构建的库中。

The `PACKAGES`{.interpreted-text role="term"} and `FILES:* <FILES>`{.interpreted-text role="term"} variables in the `meta/conf/bitbake.conf` configuration file define how files installed by the `ref-tasks-install`{.interpreted-text role="ref"} task are packaged. By default, the `PACKAGES`{.interpreted-text role="term"} variable includes `${PN}-staticdev`, which represents all static library files.

> 在 meta/conf/bitbake.conf 配置文件中的 PACKAGES 和 FILES:* <FILES>变量定义了 ref-tasks-install 任务安装的文件如何打包。默认情况下，PACKAGES 变量包括 ${PN}-staticdev，它代表所有静态库文件。

::: note
::: title
Note
:::

Some previously released versions of the Yocto Project defined the static library files through `${PN}-dev`.
:::

Following is part of the BitBake configuration file, where you can see how the static library files are defined:

```
PACKAGE_BEFORE_PN ?= ""
PACKAGES = "${PN}-src ${PN}-dbg ${PN}-staticdev ${PN}-dev ${PN}-doc ${PN}-locale ${PACKAGE_BEFORE_PN} ${PN}"
PACKAGES_DYNAMIC = "^${PN}-locale-.*"
FILES = ""

FILES:${PN} = "${bindir}/* ${sbindir}/* ${libexecdir}/* ${libdir}/lib*${SOLIBS} \
            ${sysconfdir} ${sharedstatedir} ${localstatedir} \
            ${base_bindir}/* ${base_sbindir}/* \
            ${base_libdir}/*${SOLIBS} \
            ${base_prefix}/lib/udev ${prefix}/lib/udev \
            ${base_libdir}/udev ${libdir}/udev \
            ${datadir}/${BPN} ${libdir}/${BPN}/* \
            ${datadir}/pixmaps ${datadir}/applications \
            ${datadir}/idl ${datadir}/omf ${datadir}/sounds \
            ${libdir}/bonobo/servers"

FILES:${PN}-bin = "${bindir}/* ${sbindir}/*"

FILES:${PN}-doc = "${docdir} ${mandir} ${infodir} ${datadir}/gtk-doc \
            ${datadir}/gnome/help"
SECTION:${PN}-doc = "doc"

FILES_SOLIBSDEV ?= "${base_libdir}/lib*${SOLIBSDEV} ${libdir}/lib*${SOLIBSDEV}"
FILES:${PN}-dev = "${includedir} ${FILES_SOLIBSDEV} ${libdir}/*.la \
                ${libdir}/*.o ${libdir}/pkgconfig ${datadir}/pkgconfig \
                ${datadir}/aclocal ${base_libdir}/*.o \
                ${libdir}/${BPN}/*.la ${base_libdir}/*.la \
                ${libdir}/cmake ${datadir}/cmake"
SECTION:${PN}-dev = "devel"
ALLOW_EMPTY:${PN}-dev = "1"
RDEPENDS:${PN}-dev = "${PN} (= ${EXTENDPKGV})"

FILES:${PN}-staticdev = "${libdir}/*.a ${base_libdir}/*.a ${libdir}/${BPN}/*.a"
SECTION:${PN}-staticdev = "devel"
RDEPENDS:${PN}-staticdev = "${PN}-dev (= ${EXTENDPKGV})"
```

# Combining Multiple Versions of Library Files into One Image

The build system offers the ability to build libraries with different target optimizations or architecture formats and combine these together into one system image. You can link different binaries in the image against the different libraries as needed for specific use cases. This feature is called \"Multilib\".

> 系统构建提供了使用不同的目标优化或架构格式构建库的能力，并将它们结合在一个系统映像中。您可以根据特定用例将图像中的不同二进制文件链接到不同的库。此功能称为“Multilib”。

An example would be where you have most of a system compiled in 32-bit mode using 32-bit libraries, but you have something large, like a database engine, that needs to be a 64-bit application and uses 64-bit libraries. Multilib allows you to get the best of both 32-bit and 64-bit libraries.

> 一个例子是，你用 32 位库编译了大部分系统，但是有某些大型的东西，比如数据库引擎，需要是 64 位应用程序，并使用 64 位库。多库技术允许你获得 32 位和 64 位库的最佳组合。

While the Multilib feature is most commonly used for 32 and 64-bit differences, the approach the build system uses facilitates different target optimizations. You could compile some binaries to use one set of libraries and other binaries to use a different set of libraries. The libraries could differ in architecture, compiler options, or other optimizations.

> 尽管多库功能（Multilib feature）最常用于 32 位和 64 位的差异，构建系统使用的方法也可以实现不同的目标优化。您可以编译一些二进制文件使用一组库，而其他二进制文件使用另一组库。这些库可以在架构、编译器选项或其他优化方面有所不同。

There are several examples in the `meta-skeleton` layer found in the `Source Directory`{.interpreted-text role="term"}:

- :oe\_[git:%60conf/multilib-example.conf](git:%60conf/multilib-example.conf) \</openembedded-core/tree/meta-skeleton/conf/multilib-example.conf\>\` configuration file.

> - :oe\_[git:%60conf/multilib-example.conf](git:%60conf/multilib-example.conf) \</openembedded-core/tree/meta-skeleton/conf/multilib-example.conf\>\` 配置文件的简体中文翻译

- :oe\_[git:%60conf/multilib-example2.conf](git:%60conf/multilib-example2.conf) \</openembedded-core/tree/meta-skeleton/conf/multilib-example2.conf\>\` configuration file.

> - :oe\_[git:%60conf/multilib-example2.conf](git:%60conf/multilib-example2.conf) \</openembedded-core/tree/meta-skeleton/conf/multilib-example2.conf\>\` 配置文件的简体中文翻译。

- :oe\_[git:%60recipes-multilib/images/core-image-multilib-example.bb](git:%60recipes-multilib/images/core-image-multilib-example.bb) \</openembedded-core/tree/meta-skeleton/recipes-multilib/images/core-image-multilib-example.bb\>\` recipe

> - :oe\_[git:%60recipes-multilib/images/core-image-multilib-example.bb](git:%60recipes-multilib/images/core-image-multilib-example.bb) \</openembedded-core/tree/meta-skeleton/recipes-multilib/images/core-image-multilib-example.bb\>\` 配方

## Preparing to Use Multilib

User-specific requirements drive the Multilib feature. Consequently, there is no one \"out-of-the-box\" configuration that would meet your needs.

In order to enable Multilib, you first need to ensure your recipe is extended to support multiple libraries. Many standard recipes are already extended and support multiple libraries. You can check in the `meta/conf/multilib.conf` configuration file in the `Source Directory`{.interpreted-text role="term"} to see how this is done using the `BBCLASSEXTEND`{.interpreted-text role="term"} variable. Eventually, all recipes will be covered and this list will not be needed.

> 为了启用多库，您首先需要确保您的配方已扩展以支持多个库。许多标准配方已经扩展并支持多个库。您可以在源目录中的 `meta/conf/multilib.conf` 配置文件中查看 `BBCLASSEXTEND` 变量是如何实现的。最终，所有配方都将受到覆盖，此列表将不再需要。

For the most part, the `Multilib <ref-classes-multilib*>`{.interpreted-text role="ref"} class extension works automatically to extend the package name from `${PN}` to `${MLPREFIX}${PN}`, where `MLPREFIX`{.interpreted-text role="term"} is the particular multilib (e.g. \"lib32-\" or \"lib64-\"). Standard variables such as `DEPENDS`{.interpreted-text role="term"}, `RDEPENDS`{.interpreted-text role="term"}, `RPROVIDES`{.interpreted-text role="term"}, `RRECOMMENDS`{.interpreted-text role="term"}, `PACKAGES`{.interpreted-text role="term"}, and `PACKAGES_DYNAMIC`{.interpreted-text role="term"} are automatically extended by the system. If you are extending any manual code in the recipe, you can use the `${MLPREFIX}` variable to ensure those names are extended correctly.

> 大部分情况下，Multilib 类扩展会自动将包名从${PN}扩展到${MLPREFIX}${PN}，其中MLPREFIX是特定的多库（例如“lib32-”或“lib64-”）。标准变量，如DEPENDS，RDEPENDS，RPROVIDES，RRECOMMENDS，PACKAGES和PACKAGES_DYNAMIC都会自动由系统扩展。如果您在配方中扩展了任何手动代码，您可以使用${MLPREFIX}变量来确保这些名称被正确扩展。

## Using Multilib

After you have set up the recipes, you need to define the actual combination of multiple libraries you want to build. You accomplish this through your `local.conf` configuration file in the `Build Directory`{.interpreted-text role="term"}. An example configuration would be as follows:

> 在设置好配方后，您需要通过 `Build Directory` 中的 `local.conf` 配置文件来定义要构建的多个库的实际组合。 例如，示例配置如下：

```
MACHINE = "qemux86-64"
require conf/multilib.conf
MULTILIBS = "multilib:lib32"
DEFAULTTUNE:virtclass-multilib-lib32 = "x86"
IMAGE_INSTALL:append = " lib32-glib-2.0"
```

This example enables an additional library named `lib32` alongside the normal target packages. When combining these \"lib32\" alternatives, the example uses \"x86\" for tuning. For information on this particular tuning, see `meta/conf/machine/include/ia32/arch-ia32.inc`.

> 这个例子除了正常的目标包之外，还启用了一个叫做 `lib32` 的附加库。当结合这些“lib32”的替代方案时，该例子使用“x86”进行调整。有关此特定调整的信息，请参阅 `meta/conf/machine/include/ia32/arch-ia32.inc`。

The example then includes `lib32-glib-2.0` in all the images, which illustrates one method of including a multiple library dependency. You can use a normal image build to include this dependency, for example:

> 例子中包括了 `lib32-glib-2.0` 在所有的图片中，这描绘了一种包含多个库依赖的方法。你可以使用一个普通的图片构建来包含这个依赖，例如：

```
$ bitbake core-image-sato
```

You can also build Multilib packages specifically with a command like this:

```
$ bitbake lib32-glib-2.0
```

## Additional Implementation Details

There are generic implementation details as well as details that are specific to package management systems. Following are implementation details that exist regardless of the package management system:

> 以及特定于包管理系统的细节。以下是不论包管理系统如何，都存在的实现细节：

- The typical convention used for the class extension code as used by Multilib assumes that all package names specified in `PACKAGES`{.interpreted-text role="term"} that contain `${PN}` have `${PN}` at the start of the name. When that convention is not followed and `${PN}` appears at the middle or the end of a name, problems occur.

> 典型的多库类扩展代码使用的惯例是，在 `PACKAGES`{.interpreted-text role="term"}中指定的所有包名都以 `${PN}` 开头。当不遵循这一惯例，即 `${PN}` 出现在名称的中间或末尾时，就会出现问题。

- The `TARGET_VENDOR`{.interpreted-text role="term"} value under Multilib will be extended to \"-vendormlmultilib\" (e.g. \"-pokymllib32\" for a \"lib32\" Multilib with Poky). The reason for this slightly unwieldy contraction is that any \"-\" characters in the vendor string presently break Autoconf\'s `config.sub`, and other separators are problematic for different reasons.

> "TARGET_VENDOR"的值将在多级构建下扩展为“-vendormlmultilib”（例如，使用 Poky 的“lib32”多级构建下为“-pokymllib32”）。这种略显笨拙的收缩是因为在 vendor 字符串中的任何“-”字符目前会破坏 Autoconf 的“config.sub”，而其他分隔符会因不同原因出现问题。

Here are the implementation details for the RPM Package Management System:

- A unique architecture is defined for the Multilib packages, along with creating a unique deploy folder under `tmp/deploy/rpm` in the `Build Directory`{.interpreted-text role="term"}. For example, consider `lib32` in a `qemux86-64` image. The possible architectures in the system are \"all\", \"qemux86_64\", \"lib32:qemux86_64\", and \"lib32:x86\".

> 为多库软件包定义了一种独特的架构，并在构建目录的 `tmp/deploy/rpm` 下创建一个独特的部署文件夹。例如，在 `qemux86-64` 映像中考虑 `lib32`。系统中可能的架构为“all”、“qemux86_64”、“lib32：qemux86_64”和“lib32：x86”。

- The `${MLPREFIX}` variable is stripped from `${PN}` during RPM packaging. The naming for a normal RPM package and a Multilib RPM package in a `qemux86-64` system resolves to something similar to `bash-4.1-r2.x86_64.rpm` and `bash-4.1.r2.lib32_x86.rpm`, respectively.

> `${MLPREFIX}` 变量从 `${PN}` 中剥离，以便在 RPM 打包过程中使用。在 `qemux86-64` 系统中，一般 RPM 包和多库 RPM 包的命名将分别解析为类似 `bash-4.1-r2.x86_64.rpm` 和 `bash-4.1.r2.lib32_x86.rpm`。

- When installing a Multilib image, the RPM backend first installs the base image and then installs the Multilib libraries.
- The build system relies on RPM to resolve the identical files in the two (or more) Multilib packages.

Here are the implementation details for the IPK Package Management System:

- The `${MLPREFIX}` is not stripped from `${PN}` during IPK packaging. The naming for a normal RPM package and a Multilib IPK package in a `qemux86-64` system resolves to something like `bash_4.1-r2.x86_64.ipk` and `lib32-bash_4.1-rw:x86.ipk`, respectively.

> - 在 IPK 打包期间，不会从“${MLPREFIX}”中剥离“${PN}”。在“qemux86-64”系统中，普通 RPM 包和多库 IPK 包的命名分别对应于“bash_4.1-r2.x86_64.ipk”和“lib32-bash_4.1-rw:x86.ipk”。

- The IPK deploy folder is not modified with `${MLPREFIX}` because packages with and without the Multilib feature can exist in the same folder due to the `${PN}` differences.

> IPK 部署文件夹不会因为"${MLPREFIX}"的不同而被修改，因为由于"${PN}"的不同，带有和不带有多核心特性的包可以存在同一个文件夹中。

- IPK defines a sanity check for Multilib installation using certain rules for file comparison, overridden, etc.

# Installing Multiple Versions of the Same Library

There are be situations where you need to install and use multiple versions of the same library on the same system at the same time. This almost always happens when a library API changes and you have multiple pieces of software that depend on the separate versions of the library. To accommodate these situations, you can install multiple versions of the same library in parallel on the same system.

> 在同一系统上同时安装和使用同一库的多个版本时，有时会遇到这种情况。当库的 API 发生变化，并且有多个软件依赖于库的不同版本时，这几乎总是发生的。为了适应这些情况，可以在同一系统上并行安装多个版本的同一库。

The process is straightforward as long as the libraries use proper versioning. With properly versioned libraries, all you need to do to individually specify the libraries is create separate, appropriately named recipes where the `PN`{.interpreted-text role="term"} part of the name includes a portion that differentiates each library version (e.g. the major part of the version number). Thus, instead of having a single recipe that loads one version of a library (e.g. `clutter`), you provide multiple recipes that result in different versions of the libraries you want. As an example, the following two recipes would allow the two separate versions of the `clutter` library to co-exist on the same system:

> 这个过程很简单，只要库使用正确的版本号。有了正确版本的库，您要做的就是创建单独的、正确命名的配方，其中 PN 部分的名称包括用于区分每个库版本的部分（例如版本号的主要部分）。因此，您不会提供单个加载一个库版本（例如 clutter）的配方，而是提供多个配方，以产生您想要的不同版本的库。例如，以下两个配方将允许两个 clutter 库的不同版本在同一系统上共存：

```none
clutter-1.6_1.6.20.bb
clutter-1.8_1.8.4.bb
```

Additionally, if you have other recipes that depend on a given library, you need to use the `DEPENDS`{.interpreted-text role="term"} variable to create the dependency. Continuing with the same example, if you want to have a recipe depend on the 1.8 version of the `clutter` library, use the following in your recipe:

> 如果你有其他的食谱依赖于给定的库，你需要使用 `DEPENDS` 变量来创建依赖关系。继续使用同样的例子，如果你想让你的食谱依赖于 `clutter` 库的 1.8 版本，在你的食谱中使用以下内容：

```
DEPENDS = "clutter-1.8"
```
