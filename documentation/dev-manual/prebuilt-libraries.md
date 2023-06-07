---
tip: translate by baidu@2023-06-07 17:15:25
...
---
title: Working with Pre-Built Libraries
---------------------------------------

# Introduction

Some library vendors do not release source code for their software but do release pre-built binaries. When shared libraries are built, they should be versioned (see [this article](https://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html) for some background), but sometimes this is not done.

> 一些库供应商不发布其软件的源代码，而是发布预构建的二进制文件。当构建共享库时，应该对它们进行版本控制（参见本文）([https://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html](https://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html))出于某种背景），但有时这并没有做到。

To summarize, a versioned library must meet two conditions:

> 总之，版本库必须满足两个条件：

1. The filename must have the version appended, for example: `libfoo.so.1.2.3`.

> 1.文件名必须附加版本，例如：`libfoo.so.1.2.3`。

2. The library must have the ELF tag `SONAME` set to the major version of the library, for example: `libfoo.so.1`. You can check this by running `readelf -d filename | grep SONAME`.

> 2.该库必须将 ELF 标记“SONAME”设置为该库的主要版本，例如：“libfoo.so.1'”。您可以通过运行“readelf-d filename|grep SONAME”进行检查。

This section shows how to deal with both versioned and unversioned pre-built libraries.

> 本节介绍如何处理已版本化和未版本化的预构建库。

# Versioned Libraries

In this example we work with pre-built libraries for the FT4222H USB I/O chip. Libraries are built for several target architecture variants and packaged in an archive as follows:

> 在本例中，我们使用 FT4222H USB I/O 芯片的预构建库。库是为几种目标体系结构变体构建的，并打包在档案中，如下所示：

```
├── build-arm-hisiv300
│   └── libft4222.so.1.4.4.44
├── build-arm-v5-sf
│   └── libft4222.so.1.4.4.44
├── build-arm-v6-hf
│   └── libft4222.so.1.4.4.44
├── build-arm-v7-hf
│   └── libft4222.so.1.4.4.44
├── build-arm-v8
│   └── libft4222.so.1.4.4.44
├── build-i386
│   └── libft4222.so.1.4.4.44
├── build-i486
│   └── libft4222.so.1.4.4.44
├── build-mips-eglibc-hf
│   └── libft4222.so.1.4.4.44
├── build-pentium
│   └── libft4222.so.1.4.4.44
├── build-x86_64
│   └── libft4222.so.1.4.4.44
├── examples
│   ├── get-version.c
│   ├── i2cm.c
│   ├── spim.c
│   └── spis.c
├── ftd2xx.h
├── install4222.sh
├── libft4222.h
├── ReadMe.txt
└── WinTypes.h
```

To write a recipe to use such a library in your system:

> 要编写配方以在您的系统中使用这样的库：

- The vendor will probably have a proprietary licence, so set `LICENSE_FLAGS`{.interpreted-text role="term"} in your recipe.

> -供应商可能拥有专有许可证，因此在您的配方中设置 `LICENSE_FLAGS`{.depreted text role=“term”}。

- The vendor provides a tarball containing libraries so set `SRC_URI`{.interpreted-text role="term"} appropriately.

> -供应商提供了一个包含库的 tarball，因此可以适当地设置 `SRC_URI`｛.explored text role=“term”｝。

- Set `COMPATIBLE_HOST`{.interpreted-text role="term"} so that the recipe cannot be used with an unsupported architecture. In the following example, we only support the 32 and 64 bit variants of the `x86` architecture.

> -设置 `COMPATIBLE_HOST`｛.explored text role=“term”｝，使配方不能与不受支持的体系结构一起使用。在下面的示例中，我们仅支持“x86”体系结构的 32 位和 64 位变体。

- As the vendor provides versioned libraries, we can use `oe_soinstall` from `ref-classes-utils`{.interpreted-text role="ref"} to install the shared library and create symbolic links. If the vendor does not do this, we need to follow the non-versioned library guidelines in the next section.

> -由于供应商提供版本化的库，我们可以使用 `ref classes utils`{.depreted text role=“ref”}中的 `oe_soistall` 来安装共享库并创建符号链接。如果供应商不这样做，我们需要遵循下一节中的非版本库指南。

- As the vendor likely used `LDFLAGS`{.interpreted-text role="term"} different from those in your Yocto Project build, disable the corresponding checks by adding `ldflags` to `INSANE_SKIP`{.interpreted-text role="term"}.

> -由于供应商可能使用了与 Yocto Project 版本中不同的 `LDFLAGS`｛.depredicted text role=“term”｝，请通过将 `LDFLAGS` 添加到 `INSANE_SKIP`｛.epredicted textrole=”term“｝来禁用相应的检查。

- The vendor will typically ship release builds without debugging symbols. Avoid errors by preventing the packaging task from stripping out the symbols and adding them to a separate debug package. This is done by setting the `INHIBIT_` flags shown below.

> -供应商通常会在没有调试符号的情况下发布版本。通过防止打包任务剥离符号并将其添加到单独的调试包中来避免错误。这是通过设置下面显示的“禁止_”标志来完成的。

The complete recipe would look like this:

> 完整的配方如下所示：

```
SUMMARY = "FTDI FT4222H Library"
SECTION = "libs"
LICENSE_FLAGS = "ftdi"
LICENSE = "CLOSED"

COMPATIBLE_HOST = "(i.86|x86_64).*-linux"

# Sources available in a .tgz file in .zip archive
# at https://ftdichip.com/wp-content/uploads/2021/01/libft4222-linux-1.4.4.44.zip
# Found on https://ftdichip.com/software-examples/ft4222h-software-examples/
# Since dealing with this particular type of archive is out of topic here,
# we use a local link.
SRC_URI = "file://libft4222-linux-${PV}.tgz"

S = "${WORKDIR}"

ARCH_DIR:x86-64 = "build-x86_64"
ARCH_DIR:i586 = "build-i386"
ARCH_DIR:i686 = "build-i386"

INSANE_SKIP:${PN} = "ldflags"
INHIBIT_PACKAGE_STRIP = "1"
INHIBIT_SYSROOT_STRIP = "1"
INHIBIT_PACKAGE_DEBUG_SPLIT = "1"

do_install () {
        install -m 0755 -d ${D}${libdir}
        oe_soinstall ${S}/${ARCH_DIR}/libft4222.so.${PV} ${D}${libdir}
        install -d ${D}${includedir}
        install -m 0755 ${S}/*.h ${D}${includedir}
}
```

If the precompiled binaries are not statically linked and have dependencies on other libraries, then by adding those libraries to `DEPENDS`{.interpreted-text role="term"}, the linking can be examined and the appropriate `RDEPENDS`{.interpreted-text role="term"} automatically added.

> 如果预编译的二进制文件不是静态链接的，并且与其他库有依赖关系，则通过将这些库添加到 `DEPENDS`｛.explored text role=“term”｝，可以检查链接，并自动添加适当的 `RDEPENDS`{.explered text rol=“term“｝。

# Non-Versioned Libraries

## Some Background

Libraries in Linux systems are generally versioned so that it is possible to have multiple versions of the same library installed, which eases upgrades and support for older software. For example, suppose that in a versioned library, an actual library is called `libfoo.so.1.2`, a symbolic link named `libfoo.so.1` points to `libfoo.so.1.2`, and a symbolic link named `libfoo.so` points to `libfoo.so.1.2`. Given these conditions, when you link a binary against a library, you typically provide the unversioned file name (i.e. `-lfoo` to the linker). However, the linker follows the symbolic link and actually links against the versioned filename. The unversioned symbolic link is only used at development time. Consequently, the library is packaged along with the headers in the development package `${PN}-dev` along with the actual library and versioned symbolic links in `${PN}`. Because versioned libraries are far more common than unversioned libraries, the default packaging rules assume versioned libraries.

> Linux 系统中的库通常是经过版本控制的，因此可以安装同一库的多个版本，这简化了对旧软件的升级和支持。例如，假设在一个版本化的库中，一个实际的库被称为“libfoo.so.1.2”，一个名为“libfoo.so.1”的符号链接指向“libfoo.so.1.2”；一个名“libfoo.seo”的符号链路指向“libfoo.seo.1.2”。在给定这些条件的情况下，当您将二进制文件链接到一个库时，通常会提供未经版本化的文件名（即“-lfoo”到链接器）。但是，链接器遵循符号链接，实际上是根据版本化的文件名进行链接。未转换的符号链接仅在开发时使用。因此，该库与开发包“${PN}-dev”中的头文件以及“${PN}'”中的实际库和版本化符号链接一起打包。因为有版本的库比没有版本的库要常见得多，所以默认的打包规则假定有版本的图书馆。

## Yocto Library Packaging Overview

It follows that packaging an unversioned library requires a bit of work in the recipe. By default, `libfoo.so` gets packaged into `${PN}-dev`, which triggers a QA warning that a non-symlink library is in a `-dev` package, and binaries in the same recipe link to the library in `${PN}-dev`, which triggers more QA warnings. To solve this problem, you need to package the unversioned library into `${PN}` where it belongs. The following are the abridged default `FILES`{.interpreted-text role="term"} variables in `bitbake.conf`:

> 因此，打包一个未经转换的库需要在配方中做一些工作。默认情况下，“libfoo.so”被打包到“$｛PN｝-dev”中，这会触发一个非符号链接库在“-dev”包中的QA警告，而同一配方中的二进制文件链接到“${PN｝/dev”中的库，这会引发更多的 QA 警告。要解决这个问题，您需要将未转换的库打包到它所属的“$｛PN｝”中。以下是 `bitbake.conf` 中的缩写默认 `FILES`｛.explored text role=“term”｝变量：

```
SOLIBS = ".so.*"
SOLIBSDEV = ".so"
FILES:${PN} = "... ${libdir}/lib*${SOLIBS} ..."
FILES_SOLIBSDEV ?= "... ${libdir}/lib*${SOLIBSDEV} ..."
FILES:${PN}-dev = "... ${FILES_SOLIBSDEV} ..."
```

`SOLIBS`{.interpreted-text role="term"} defines a pattern that matches real shared object libraries. `SOLIBSDEV`{.interpreted-text role="term"} matches the development form (unversioned symlink). These two variables are then used in `FILES:${PN}` and `FILES:${PN}-dev`, which puts the real libraries into `${PN}` and the unversioned symbolic link into `${PN}-dev`. To package unversioned libraries, you need to modify the variables in the recipe as follows:

> `SOLIBS`｛.explored text role=“term”｝定义了一个匹配实际共享对象库的模式 `SOLIBSDEV`｛.explored text role=“term”｝与开发形式（未转换的符号链接）匹配。然后，这两个变量在“FILES:$｛PN｝”和“FILES:${PN｝-dev”中使用，这将实际库放入“$｛PN}”，并将未经转换的符号链接放入“${PN}-dev”。要打包未转换的库，您需要按如下方式修改配方中的变量：

```
SOLIBS = ".so"
FILES_SOLIBSDEV = ""
```

The modifications cause the `.so` file to be the real library and unset `FILES_SOLIBSDEV`{.interpreted-text role="term"} so that no libraries get packaged into `${PN}-dev`. The changes are required because unless `PACKAGES`{.interpreted-text role="term"} is changed, `${PN}-dev` collects files before [\${PN}]{.title-ref}. `${PN}-dev` must not collect any of the files you want in `${PN}`.

> 这些修改导致 `.so` 文件成为真正的库，并取消设置 `FILES_SOLIBSDEV`｛.depreted text role=“term”｝，这样就不会将库打包到 `$｛PN｝-dev'中。之所以需要进行更改，是因为除非更改` PACKAGES `｛.explored text role=“term”｝，否则`$｛PN｝-dev`将收集[\$｛PN}]｛.title ref｝之前的文件。`${PN}-dev` 不得在 `${PN｝中收集所需的任何文件。

Finally, loadable modules, essentially unversioned libraries that are linked at runtime using `dlopen()` instead of at build time, should generally be installed in a private directory. However, if they are installed in `${libdir}`, then the modules can be treated as unversioned libraries.

> 最后，可加载模块，本质上是未转换的库，在运行时使用“dlopen（）”而不是在构建时链接，通常应该安装在专用目录中。但是，如果它们安装在“${libdir}”中，则这些模块可以被视为未转换的库。

## Example

The example below installs an unversioned x86-64 pre-built library named `libfoo.so`. The `COMPATIBLE_HOST`{.interpreted-text role="term"} variable limits recipes to the x86-64 architecture while the `INSANE_SKIP`{.interpreted-text role="term"}, `INHIBIT_PACKAGE_STRIP`{.interpreted-text role="term"} and `INHIBIT_SYSROOT_STRIP`{.interpreted-text role="term"} variables are all set as in the above versioned library example. The \"magic\" is setting the `SOLIBS`{.interpreted-text role="term"} and `FILES_SOLIBSDEV`{.interpreted-text role="term"} variables as explained above:

> 下面的示例安装了一个名为 `libfoo.so` 的未经版本转换的 x86-64 预建库。`COMPATIBLE_HOST`｛.explored text role=“term”｝变量将配方限制为 x86-64 体系结构，`INHIBIT_PACKAGE_STRIP`｛.explored text role=“term”｝和 `INHIBT_SYSROOT_STRIP`{.explered text rol=”term“｝变量都设置为与上述版本库示例相同。“魔法”是设置 `SOLIBS`｛.depredicted text role=“term”｝和 `FILES_SOLIBSDEV`｛.epredicted textrole=”term“｝变量，如上所述：

```
SUMMARY = "libfoo sample recipe"
SECTION = "libs"
LICENSE = "CLOSED"

SRC_URI = "file://libfoo.so"

COMPATIBLE_HOST = "x86_64.*-linux"

INSANE_SKIP:${PN} = "ldflags"
INHIBIT_PACKAGE_STRIP = "1"
INHIBIT_SYSROOT_STRIP = "1"
SOLIBS = ".so"
FILES_SOLIBSDEV = ""

do_install () {
        install -d ${D}${libdir}
        install -m 0755 ${WORKDIR}/libfoo.so ${D}${libdir}
}
```
