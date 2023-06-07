---
tip: translate by baidu@2023-06-07 17:13:03
...
---
title: Working With Libraries
-----------------------------

Libraries are an integral part of your system. This section describes some common practices you might find helpful when working with libraries to build your system:

> 图书馆是你的系统不可分割的一部分。本节介绍了在使用库构建系统时可能会有所帮助的一些常见做法：

- `How to include static library files <dev-manual/libraries:including static library files>`{.interpreted-text role="ref"}

> -`如何包括静态库文件<devmanual/librarys:including static library files>`｛.depredicted text role=“ref”｝

- `How to use the Multilib feature to combine multiple versions of library files into a single image <dev-manual/libraries:combining multiple versions of library files into one image>`{.interpreted-text role="ref"}

> -`如何使用Multilib功能将多个版本的库文件组合成一个图像<devmanual/librarys:combing multiple version of library files into one image>`｛.depreted text role=“ref”｝

- `How to install multiple versions of the same library in parallel on the same system <dev-manual/libraries:installing multiple versions of the same library>`{.interpreted-text role="ref"}

> -`如何在同一系统上并行安装同一库的多个版本<devmanual/librarys:installing multiple version of the same library>`｛.depredicted text role=“ref”｝

# Including Static Library Files

If you are building a library and the library offers static linking, you can control which static library files (`*.a` files) get included in the built library.

> 如果您正在构建库，并且该库提供静态链接，则可以控制哪些静态库文件（`*.a` 文件）包含在构建的库中。

The `PACKAGES`{.interpreted-text role="term"} and `FILES:* <FILES>`{.interpreted-text role="term"} variables in the `meta/conf/bitbake.conf` configuration file define how files installed by the `ref-tasks-install`{.interpreted-text role="ref"} task are packaged. By default, the `PACKAGES`{.interpreted-text role="term"} variable includes `${PN}-staticdev`, which represents all static library files.

> `meta/conf/bitbake.conf` 配置文件中的 `PACKAGES`｛.depreced text role=“term”｝和 `FILES:*<FILES>`｛.epreced text-role=”term“｝变量定义了如何打包由 `ref tasks-install`｛.repreced ext-role=“ref”｝任务安装的文件。默认情况下，`PACKAGES`｛.explored text role=“term”｝变量包括 `$｛PN｝-staticdev`，它表示所有静态库文件。

::: note
::: title
Note
:::

Some previously released versions of the Yocto Project defined the static library files through `${PN}-dev`.

> Yocto 项目的一些先前发布的版本通过“${PN}-dev”定义了静态库文件。
> :::

Following is part of the BitBake configuration file, where you can see how the static library files are defined:

> 以下是 BitBake 配置文件的一部分，您可以在其中查看静态库文件的定义方式：

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

> 构建系统提供了构建具有不同目标优化或体系结构格式的库的能力，并将这些库组合到一个系统映像中。您可以根据特定用例的需要，将映像中的不同二进制文件链接到不同的库。此功能被称为“Multilib”。

An example would be where you have most of a system compiled in 32-bit mode using 32-bit libraries, but you have something large, like a database engine, that needs to be a 64-bit application and uses 64-bit libraries. Multilib allows you to get the best of both 32-bit and 64-bit libraries.

> 一个例子是，大多数系统都是使用 32 位库以 32 位模式编译的，但您有一些大型的东西，如数据库引擎，需要是 64 位应用程序并使用 64 位库。Multilib 允许您充分利用 32 位和 64 位库。

While the Multilib feature is most commonly used for 32 and 64-bit differences, the approach the build system uses facilitates different target optimizations. You could compile some binaries to use one set of libraries and other binaries to use a different set of libraries. The libraries could differ in architecture, compiler options, or other optimizations.

> 虽然 Multilib 功能最常用于 32 位和 64 位的差异，但构建系统使用的方法有助于不同的目标优化。您可以编译一些二进制文件以使用一组库，而其他二进制文件则使用不同的库。这些库可能在体系结构、编译器选项或其他优化方面有所不同。

There are several examples in the `meta-skeleton` layer found in the `Source Directory`{.interpreted-text role="term"}:

> 在“源目录”中的“元骨架”层中有几个示例｛.explored text role=“term”｝：

- :oe\_[git:%60conf/multilib-example.conf](git:%60conf/multilib-example.conf) \</openembedded-core/tree/meta-skeleton/conf/multilib-example.conf\>\` configuration file.

> -：oe\_[git:%60conf/multlib example.conf]（git:%60onf/multlib sample.conf]）\</openembedded core/tree/metacketle/conf/multilib example.coonf\>\`配置文件。

- :oe\_[git:%60conf/multilib-example2.conf](git:%60conf/multilib-example2.conf) \</openembedded-core/tree/meta-skeleton/conf/multilib-example2.conf\>\` configuration file.

> -：oe\_[git:%60conf/multib-example2.conf]（git:%60conf/multib-example2.onf）\</openembedded-core/tree/metacketle/conf/multilib-example2\>\`配置文件。

- :oe\_[git:%60recipes-multilib/images/core-image-multilib-example.bb](git:%60recipes-multilib/images/core-image-multilib-example.bb) \</openembedded-core/tree/meta-skeleton/recipes-multilib/images/core-image-multilib-example.bb\>\` recipe

> -：oe[git:%60recipes multilib/images/core image multilib-example.bb]（git:%60recipes multilib/images/core image multilib-inxample.bb）\</openembedded core/tree/meta-straine/recipes multiplib/images-core image multilib example.bb\>\`reciped

## Preparing to Use Multilib

User-specific requirements drive the Multilib feature. Consequently, there is no one \"out-of-the-box\" configuration that would meet your needs.

> 用户特定的需求推动了 Multilib 功能。因此，没有一种“开箱即用”的配置能够满足您的需求。

In order to enable Multilib, you first need to ensure your recipe is extended to support multiple libraries. Many standard recipes are already extended and support multiple libraries. You can check in the `meta/conf/multilib.conf` configuration file in the `Source Directory`{.interpreted-text role="term"} to see how this is done using the `BBCLASSEXTEND`{.interpreted-text role="term"} variable. Eventually, all recipes will be covered and this list will not be needed.

> 为了启用 Multilib，您首先需要确保您的配方被扩展为支持多个库。许多标准配方已经扩展并支持多个库。您可以检入“源目录”中的“meta/conf/multilib.conf”配置文件｛.depreted text role=“term”｝，以查看如何使用“BBCLASSEXTEND”｛.deverted text rol=“term“｝变量完成此操作。最终，所有的食谱都将被涵盖，而这份清单将不再需要。

For the most part, the `Multilib <ref-classes-multilib*>`{.interpreted-text role="ref"} class extension works automatically to extend the package name from `${PN}` to `${MLPREFIX}${PN}`, where `MLPREFIX`{.interpreted-text role="term"} is the particular multilib (e.g. \"lib32-\" or \"lib64-\"). Standard variables such as `DEPENDS`{.interpreted-text role="term"}, `RDEPENDS`{.interpreted-text role="term"}, `RPROVIDES`{.interpreted-text role="term"}, `RRECOMMENDS`{.interpreted-text role="term"}, `PACKAGES`{.interpreted-text role="term"}, and `PACKAGES_DYNAMIC`{.interpreted-text role="term"} are automatically extended by the system. If you are extending any manual code in the recipe, you can use the `${MLPREFIX}` variable to ensure those names are extended correctly.

> 在大多数情况下，`Multilib<ref classes Multilib*>`｛.explored text role=“ref”｝类扩展会自动将包名称从 `$｛PN｝` 扩展到 `${MLPREFIX}$｛PN}`，其中 `MLPREFIX`｛.explored textrole=”term“｝是特定的 Multilib（例如\“lib32-\”或\“lib64-\”）。系统会自动扩展标准变量，如 `DEPENDS`｛.explored text role=“term”｝、`RDEPENDS`{.explered text rol=“term“｝、` RPROVIDES`｛..explored textrole=”term“}、`RRECOMMENDS`｝.explosed text role=“term”}、`PACKAGES`{.exploreed text role=“term'｝和 `PACKAGES_DYNAMIC`{。如果要扩展配方中的任何手动代码，可以使用“${MLPREFIX}”变量来确保这些名称得到正确扩展。

## Using Multilib

After you have set up the recipes, you need to define the actual combination of multiple libraries you want to build. You accomplish this through your `local.conf` configuration file in the `Build Directory`{.interpreted-text role="term"}. An example configuration would be as follows:

> 设置好配方后，需要定义要构建的多个库的实际组合。您可以通过“构建目录”中的“local.conf”配置文件来实现这一点。｛.explored text role=“term”｝。配置示例如下：

```
MACHINE = "qemux86-64"
require conf/multilib.conf
MULTILIBS = "multilib:lib32"
DEFAULTTUNE:virtclass-multilib-lib32 = "x86"
IMAGE_INSTALL:append = " lib32-glib-2.0"
```

This example enables an additional library named `lib32` alongside the normal target packages. When combining these \"lib32\" alternatives, the example uses \"x86\" for tuning. For information on this particular tuning, see `meta/conf/machine/include/ia32/arch-ia32.inc`.

> 此示例在普通目标包的旁边启用了一个名为“lib32”的附加库。当组合这些“lib32\”替代方案时，示例使用“x86\”进行调优。有关此特定调优的信息，请参阅 `meta/conf/machine/include/ia32/arch-ia32.inc`。

The example then includes `lib32-glib-2.0` in all the images, which illustrates one method of including a multiple library dependency. You can use a normal image build to include this dependency, for example:

> 然后，该示例在所有图像中包括“lib32-glib-2.0”，这说明了包括多库依赖项的一种方法。您可以使用普通映像构建来包含此依赖项，例如：

```
$ bitbake core-image-sato
```

You can also build Multilib packages specifically with a command like this:

> 您还可以使用以下命令专门构建 Multilib 包：

```
$ bitbake lib32-glib-2.0
```

## Additional Implementation Details

There are generic implementation details as well as details that are specific to package management systems. Following are implementation details that exist regardless of the package management system:

> 有通用的实现细节，也有包管理系统特有的细节。以下是与包管理系统无关的实施细节：

- The typical convention used for the class extension code as used by Multilib assumes that all package names specified in `PACKAGES`{.interpreted-text role="term"} that contain `${PN}` have `${PN}` at the start of the name. When that convention is not followed and `${PN}` appears at the middle or the end of a name, problems occur.

> -Multilib 使用的类扩展代码所使用的典型约定假设在 `PACKAGES`｛.explored text role=“term”｝中指定的所有包含 `$｛PN｝` 的包名称在名称的开头都有 `${PN｝'。如果不遵守该约定，并且“$｛PN｝”出现在名称的中间或末尾，就会出现问题。

- The `TARGET_VENDOR`{.interpreted-text role="term"} value under Multilib will be extended to \"-vendormlmultilib\" (e.g. \"-pokymllib32\" for a \"lib32\" Multilib with Poky). The reason for this slightly unwieldy contraction is that any \"-\" characters in the vendor string presently break Autoconf\'s `config.sub`, and other separators are problematic for different reasons.

> -Multilib 下的 `TARGET_VENDOR`｛.explored text role=“term”｝值将扩展为\“-vendommultilib\”（例如，\“-pokymllib32\”表示带有 Poky 的\“lib32\”Multilib）。这种略显笨拙的收缩的原因是，供应商字符串中的任何“-”字符目前都会破坏 Autoconf 的“config.sub”，而其他分隔符由于不同的原因而存在问题。

Here are the implementation details for the RPM Package Management System:

> 以下是 RPM 软件包管理系统的实施细节：

- A unique architecture is defined for the Multilib packages, along with creating a unique deploy folder under `tmp/deploy/rpm` in the `Build Directory`{.interpreted-text role="term"}. For example, consider `lib32` in a `qemux86-64` image. The possible architectures in the system are \"all\", \"qemux86_64\", \"lib32:qemux86_64\", and \"lib32:x86\".

> -为 Multilib 包定义了一个独特的体系结构，同时在“构建目录”中的“tmp/deploy/rpm”下创建了一个唯一的部署文件夹{.depreted text role=“term”}。例如，考虑“qemux86-64”图像中的“lib32”。系统中可能的体系结构有“all”、“qemux86_64\”、“lib32:qemux86_64\”和“lib32\x86\”。

- The `${MLPREFIX}` variable is stripped from `${PN}` during RPM packaging. The naming for a normal RPM package and a Multilib RPM package in a `qemux86-64` system resolves to something similar to `bash-4.1-r2.x86_64.rpm` and `bash-4.1.r2.lib32_x86.rpm`, respectively.

> -在 RPM 打包过程中，从“$｛PN｝”中剥离了“${MLPREFIX｝”变量。“qemux86-64”系统中普通 RPM 包和 Multilib RPM 包的命名分别解析为类似于“bash-41-r2.x86.RPM”和“bash-41.r2.lib32_x86.RPM”的名称。

- When installing a Multilib image, the RPM backend first installs the base image and then installs the Multilib libraries.

> -安装 Multilib 映像时，RPM 后端首先安装基本映像，然后安装 Multilib 库。

- The build system relies on RPM to resolve the identical files in the two (or more) Multilib packages.

> -构建系统依赖 RPM 来解析两个（或多个）Multilib 包中的相同文件。

Here are the implementation details for the IPK Package Management System:

> 以下是 IPK 包管理系统的实施细节：

- The `${MLPREFIX}` is not stripped from `${PN}` during IPK packaging. The naming for a normal RPM package and a Multilib IPK package in a `qemux86-64` system resolves to something like `bash_4.1-r2.x86_64.ipk` and `lib32-bash_4.1-rw:x86.ipk`, respectively.

> -在 IPK 封装过程中，不会从“$｛PN｝”中剥离“${MLPREFIX｝”。“qemux86-64”系统中的普通 RPM 包和 Multilib IPK 包的命名分别解析为“bash_4.1-r2.x86_64.IPK”和“lib32-bash_4.1/rw:x86.IPK”。

- The IPK deploy folder is not modified with `${MLPREFIX}` because packages with and without the Multilib feature can exist in the same folder due to the `${PN}` differences.

> -IPK 部署文件夹未使用“$｛MLPREFIX｝”进行修改，因为由于“${PN｝”的差异，具有和不具有 Multilib 功能的包可能存在于同一文件夹中。

- IPK defines a sanity check for Multilib installation using certain rules for file comparison, overridden, etc.

> -IPK 使用某些文件比较、重写等规则为 Multilib 安装定义了健全性检查。

# Installing Multiple Versions of the Same Library

There are be situations where you need to install and use multiple versions of the same library on the same system at the same time. This almost always happens when a library API changes and you have multiple pieces of software that depend on the separate versions of the library. To accommodate these situations, you can install multiple versions of the same library in parallel on the same system.

> 在某些情况下，您需要在同一系统上同时安装和使用同一库的多个版本。当库 API 发生更改，并且您有多个依赖于库的不同版本的软件时，几乎总是会发生这种情况。为了适应这些情况，可以在同一系统上并行安装同一库的多个版本。

The process is straightforward as long as the libraries use proper versioning. With properly versioned libraries, all you need to do to individually specify the libraries is create separate, appropriately named recipes where the `PN`{.interpreted-text role="term"} part of the name includes a portion that differentiates each library version (e.g. the major part of the version number). Thus, instead of having a single recipe that loads one version of a library (e.g. `clutter`), you provide multiple recipes that result in different versions of the libraries you want. As an example, the following two recipes would allow the two separate versions of the `clutter` library to co-exist on the same system:

> 只要库使用正确的版本控制，这个过程就很简单。对于版本正确的库，单独指定库所需要做的就是创建单独的、命名适当的配方，其中名称的 `PN`{.depreced text role=“term”}部分包括区分每个库版本的部分（例如版本号的主要部分）。因此，您可以提供多个配方，从而生成所需的不同版本的库，而不是使用单个配方加载一个版本的库（例如“杂波”）。例如，以下两个配方将允许“混乱”库的两个独立版本在同一系统上共存：

```none
clutter-1.6_1.6.20.bb
clutter-1.8_1.8.4.bb
```

Additionally, if you have other recipes that depend on a given library, you need to use the `DEPENDS`{.interpreted-text role="term"} variable to create the dependency. Continuing with the same example, if you want to have a recipe depend on the 1.8 version of the `clutter` library, use the following in your recipe:

> 此外，如果您有其他依赖于给定库的配方，则需要使用 `DEPENDS`{.depredicted text role=“term”}变量来创建依赖关系。继续同一个例子，如果你想让一个食谱依赖于 1.8 版本的“混乱”库，请在你的食谱中使用以下内容：

```
DEPENDS = "clutter-1.8"
```
