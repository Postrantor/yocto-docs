---
tip: translate by openai@2023-06-10 11:57:21
...
---
title: Working with Pre-Built Libraries
---------------------------------------

# Introduction

Some library vendors do not release source code for their software but do release pre-built binaries. When shared libraries are built, they should be versioned (see [this article](https://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html) for some background), but sometimes this is not done.

> 一些库提供商不会发布自己软件的源代码，但会发布预编译的二进制文件。当构建共享库时，它们应该有版本号（可以参考[本文](https://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html) 了解一些背景知识），但有时不会这样做。

To summarize, a versioned library must meet two conditions:

1. The filename must have the version appended, for example: `libfoo.so.1.2.3`.
2. The library must have the ELF tag `SONAME` set to the major version of the library, for example: `libfoo.so.1`. You can check this by running `readelf -d filename | grep SONAME`.

> 图书馆必须将 ELF 标签 `SONAME` 设置为库的主要版本，例如：`libfoo.so.1`。您可以通过运行 `readelf -d filename | grep SONAME` 来检查此项。

This section shows how to deal with both versioned and unversioned pre-built libraries.

# Versioned Libraries

In this example we work with pre-built libraries for the FT4222H USB I/O chip. Libraries are built for several target architecture variants and packaged in an archive as follows:

> 在这个例子中，我们使用预先构建的 FT4222H USB I/O 芯片库。为几种目标架构变体构建的库被打包成一个存档，如下所示：

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

- The vendor will probably have a proprietary licence, so set `LICENSE_FLAGS`{.interpreted-text role="term"} in your recipe.
- The vendor provides a tarball containing libraries so set `SRC_URI`{.interpreted-text role="term"} appropriately.
- Set `COMPATIBLE_HOST`{.interpreted-text role="term"} so that the recipe cannot be used with an unsupported architecture. In the following example, we only support the 32 and 64 bit variants of the `x86` architecture.

> 设置 `COMPATIBLE_HOST`，以防止使用不支持的架构。在下面的示例中，我们仅支持 `x86` 架构的 32 位和 64 位变体。

- As the vendor provides versioned libraries, we can use `oe_soinstall` from `ref-classes-utils`{.interpreted-text role="ref"} to install the shared library and create symbolic links. If the vendor does not do this, we need to follow the non-versioned library guidelines in the next section.

> 由于供应商提供了带版本号的库，我们可以使用 ref-classes-utils 中的 oe_soinstall 来安装共享库并创建符号链接。如果供应商没有这样做，我们需要按照下一节中的非版本库指南来操作。

- As the vendor likely used `LDFLAGS`{.interpreted-text role="term"} different from those in your Yocto Project build, disable the corresponding checks by adding `ldflags` to `INSANE_SKIP`{.interpreted-text role="term"}.

> 作为供应商可能使用了与您的 Yocto 项目构建中的不同的 LDFLAGS，请通过将 ldflags 添加到 INSANE_SKIP 中来禁用相应的检查。

- The vendor will typically ship release builds without debugging symbols. Avoid errors by preventing the packaging task from stripping out the symbols and adding them to a separate debug package. This is done by setting the `INHIBIT_` flags shown below.

> 供应商通常会在不带调试符号的发布版本中进行发货。通过防止打包任务剥离符号并将其添加到单独的调试包中，可以避免错误。这是通过设置下面显示的 `INHIBIT_` 标志来完成的。

The complete recipe would look like this:

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

> 如果预编译的二进制文件不是静态链接的，并且依赖于其他库，那么通过将这些库添加到 `DEPENDS`，可以检查链接，并自动添加相应的 `RDEPENDS`。

# Non-Versioned Libraries

## Some Background

Libraries in Linux systems are generally versioned so that it is possible to have multiple versions of the same library installed, which eases upgrades and support for older software. For example, suppose that in a versioned library, an actual library is called `libfoo.so.1.2`, a symbolic link named `libfoo.so.1` points to `libfoo.so.1.2`, and a symbolic link named `libfoo.so` points to `libfoo.so.1.2`. Given these conditions, when you link a binary against a library, you typically provide the unversioned file name (i.e. `-lfoo` to the linker). However, the linker follows the symbolic link and actually links against the versioned filename. The unversioned symbolic link is only used at development time. Consequently, the library is packaged along with the headers in the development package `${PN}-dev` along with the actual library and versioned symbolic links in `${PN}`. Because versioned libraries are far more common than unversioned libraries, the default packaging rules assume versioned libraries.

> 在 Linux 系统中，库通常都是有版本的，这样就可以安装多个版本的同一个库，从而简化升级和支持旧软件。例如，假设在一个有版本的库中，实际的库被称为 `libfoo.so.1.2`，一个符号链接 `libfoo.so.1` 指向 `libfoo.so.1.2`，另一个符号链接 `libfoo.so` 指向 `libfoo.so.1.2`。有了这些条件，当你将一个二进制文件链接到一个库时，你通常会向链接器提供非版本文件名（即 `-lfoo`）。但是，链接器会跟随符号链接，实际上链接到带有版本号的文件名。非版本的符号链接只在开发时使用。因此，库将与头文件一起打包在开发包 `${PN}-dev` 中，实际的库和带有版本号的符号链接在 `${PN}` 中打包。由于带有版本号的库比不带版本号的库要普遍得多，因此默认的打包规则假定库是带有版本号的。

## Yocto Library Packaging Overview

It follows that packaging an unversioned library requires a bit of work in the recipe. By default, `libfoo.so` gets packaged into `${PN}-dev`, which triggers a QA warning that a non-symlink library is in a `-dev` package, and binaries in the same recipe link to the library in `${PN}-dev`, which triggers more QA warnings. To solve this problem, you need to package the unversioned library into `${PN}` where it belongs. The following are the abridged default `FILES`{.interpreted-text role="term"} variables in `bitbake.conf`:

> 这意味着在配方中打包一个未版本化的库需要一些工作。 默认情况下，`libfoo.so` 被打包到 `${PN}-dev` 中，这会引发一个 QA 警告，即一个非符号链接库位于 `-dev` 软件包中，并且同一配方中的二进制文件链接到 `${PN}-dev` 中的库，这会引发更多的 QA 警告。 为了解决这个问题，您需要将未版本化的库打包到它应该属于的 `${PN}` 中。 以下是 `bitbake.conf` 中的缩略 `FILES` 变量：

```
SOLIBS = ".so.*"
SOLIBSDEV = ".so"
FILES:${PN} = "... ${libdir}/lib*${SOLIBS} ..."
FILES_SOLIBSDEV ?= "... ${libdir}/lib*${SOLIBSDEV} ..."
FILES:${PN}-dev = "... ${FILES_SOLIBSDEV} ..."
```

`SOLIBS`{.interpreted-text role="term"} defines a pattern that matches real shared object libraries. `SOLIBSDEV`{.interpreted-text role="term"} matches the development form (unversioned symlink). These two variables are then used in `FILES:${PN}` and `FILES:${PN}-dev`, which puts the real libraries into `${PN}` and the unversioned symbolic link into `${PN}-dev`. To package unversioned libraries, you need to modify the variables in the recipe as follows:

> `SOLIBS` 定义了一个匹配真实共享对象库的模式。`SOLIBSDEV` 匹配开发形式（未版本化的符号链接）。然后，这两个变量被用在 `FILES:${PN}` 和 `FILES:${PN}-dev` 中，将真实库放入 `${PN}`，未版本化的符号链接放入 `${PN}-dev`。要打包未版本化的库，您需要修改食谱中的变量，如下所示：

```
SOLIBS = ".so"
FILES_SOLIBSDEV = ""
```

The modifications cause the `.so` file to be the real library and unset `FILES_SOLIBSDEV`{.interpreted-text role="term"} so that no libraries get packaged into `${PN}-dev`. The changes are required because unless `PACKAGES`{.interpreted-text role="term"} is changed, `${PN}-dev` collects files before [\${PN}]{.title-ref}. `${PN}-dev` must not collect any of the files you want in `${PN}`.

> 修改会导致 `.so` 文件成为真正的库，并将 `FILES_SOLIBSDEV` 取消设置，以便不会将任何库打包到 `${PN}-dev` 中。这些更改是必需的，因为除非更改 `PACKAGES`，否则 `${PN}-dev` 会在[\${PN}]{.title-ref}之前收集文件。`${PN}-dev` 绝不能收集你想要的 `${PN}` 中的任何文件。

Finally, loadable modules, essentially unversioned libraries that are linked at runtime using `dlopen()` instead of at build time, should generally be installed in a private directory. However, if they are installed in `${libdir}`, then the modules can be treated as unversioned libraries.

> 最后，可加载模块，基本上是指使用 `dlopen（）` 在运行时链接而不是在构建时链接的未版本库，通常应该安装在私有目录中。但是，如果它们安装在 `${libdir}` 中，那么可以将模块视为未版本库。

## Example

The example below installs an unversioned x86-64 pre-built library named `libfoo.so`. The `COMPATIBLE_HOST`{.interpreted-text role="term"} variable limits recipes to the x86-64 architecture while the `INSANE_SKIP`{.interpreted-text role="term"}, `INHIBIT_PACKAGE_STRIP`{.interpreted-text role="term"} and `INHIBIT_SYSROOT_STRIP`{.interpreted-text role="term"} variables are all set as in the above versioned library example. The \"magic\" is setting the `SOLIBS`{.interpreted-text role="term"} and `FILES_SOLIBSDEV`{.interpreted-text role="term"} variables as explained above:

> 下面的示例安装一个未标记版本的 x86-64 预构建库，名为 `libfoo.so`。`COMPATIBLE_HOST` 变量将配方限制为 x86-64 架构，而 `INSANE_SKIP`、`INHIBIT_PACKAGE_STRIP` 和 `INHIBIT_SYSROOT_STRIP` 变量都与上面的版本库示例相同。“魔法”在于设置 `SOLIBS` 和 `FILES_SOLIBSDEV` 变量，如上文所述：

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
