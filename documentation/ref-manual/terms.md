---
tip: translate by openai@2023-06-07 22:55:04
...
---
title: Yocto Project Terms
--------------------------

Following is a list of terms and definitions users new to the Yocto Project development environment might find helpful. While some of these terms are universal, the list includes them just in case:

> 以下是新手使用 Yocto 项目开发环境可能会有帮助的术语和定义列表。虽然其中一些术语是普遍的，但列表中还是包括了它们，以防万一：

::: glossary

`Append Files`{.interpreted-text role="term"}

:   Files that append build information to a recipe file. Append files are known as BitBake append files and `.bbappend` files. The OpenEmbedded build system expects every append file to have a corresponding recipe (`.bb`) file. Furthermore, the append file and corresponding recipe file must use the same root filename. The filenames can differ only in the file type suffix used (e.g. `formfactor_0.0.bb` and `formfactor_0.0.bbappend`).

```
Information in append files extends or overrides the information in the similarly-named recipe file. For an example of an append file in use, see the \"`dev-manual/layers:appending other layers metadata with your layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

When you name an append file, you can use the \"`%`\" wildcard character to allow for matching recipe names. For example, suppose you have an append file named as follows:

    busybox_1.21.%.bbappend

That append file would match any `busybox_1.21.x.bb` version of the recipe. So, the append file would match any of the following recipe names:

``` shell
busybox_1.21.1.bb
busybox_1.21.2.bb
busybox_1.21.3.bb
busybox_1.21.10.bb
busybox_1.21.25.bb
```

::: note
::: title
Note
:::

The use of the \"%\" character is limited in that it only works directly in front of the .bbappend portion of the append file\'s name. You cannot use the wildcard character in any other location of the name.
:::

```

`BitBake`{.interpreted-text role="term"}

:   The task executor and scheduler used by the OpenEmbedded build system to build images. For more information on BitBake, see the `BitBake User Manual <bitbake:index>`{.interpreted-text role="doc"}.

`Board Support Package (BSP)`{.interpreted-text role="term"}

:   A group of drivers, definitions, and other components that provide support for a specific hardware configuration. For more information on BSPs, see the `/bsp-guide/index`{.interpreted-text role="doc"}.

`Build Directory`{.interpreted-text role="term"}

:   This term refers to the area used by the OpenEmbedded build system for builds. The area is created when you `source` the setup environment script that is found in the Source Directory (i.e. ``ref-manual/structure:\`\`oe-init-build-env\`\` ``{.interpreted-text role="ref"}). The `TOPDIR`{.interpreted-text role="term"} variable points to the `Build Directory`{.interpreted-text role="term"}.

```

You have a lot of flexibility when creating the `Build Directory`{.interpreted-text role="term"}. Following are some examples that show how to create the directory. The examples assume your `Source Directory`{.interpreted-text role="term"} is named `poky`:

> - Create the `Build Directory`{.interpreted-text role="term"} inside your Source Directory and let the name of the `Build Directory`{.interpreted-text role="term"} default to `build`:
>
>   ```shell
>   $ cd poky
>   $ source oe-init-build-env
>   ```
> - Create the `Build Directory`{.interpreted-text role="term"} inside your home directory and specifically name it `test-builds`:
>
>   ```shell
>   $ source poky/oe-init-build-env test-builds
>   ```
> - Provide a directory path and specifically name the `Build Directory`{.interpreted-text role="term"}. Any intermediate folders in the pathname must exist. This next example creates a `Build Directory`{.interpreted-text role="term"} named `YP-&DISTRO;` within the existing directory `mybuilds`:
>
>   ```shell
>   $ source poky/oe-init-build-env mybuilds/YP-&DISTRO;
>   ```

::: note
::: title
Note
:::

By default, the `Build Directory`{.interpreted-text role="term"} contains `TMPDIR`{.interpreted-text role="term"}, which is a temporary directory the build system uses for its work. `TMPDIR`{.interpreted-text role="term"} cannot be under NFS. Thus, by default, the `Build Directory`{.interpreted-text role="term"} cannot be under NFS. However, if you need the `Build Directory`{.interpreted-text role="term"} to be under NFS, you can set this up by setting `TMPDIR`{.interpreted-text role="term"} in your `local.conf` file to use a local drive. Doing so effectively separates `TMPDIR`{.interpreted-text role="term"} from `TOPDIR`{.interpreted-text role="term"}, which is the `Build Directory`{.interpreted-text role="term"}.
:::

```

`Build Host`{.interpreted-text role="term"}

:   The system used to build images in a Yocto Project Development environment. The build system is sometimes referred to as the development host.

`buildtools`{.interpreted-text role="term"}

:   Build tools in binary form, providing required versions of development tools (such as Git, GCC, Python and make), to run the OpenEmbedded build system on a development host without such minimum versions.

```

See the \"`system-requirements-buildtools`{.interpreted-text role="ref"}\" paragraph in the Reference Manual for details about downloading or building an archive of such tools.

```

`buildtools-extended`{.interpreted-text role="term"}

:   A set of `buildtools`{.interpreted-text role="term"} binaries extended with additional development tools, such as a required version of the GCC compiler to run the OpenEmbedded build system.

```

See the \"`system-requirements-buildtools`{.interpreted-text role="ref"}\" paragraph in the Reference Manual for details about downloading or building an archive of such tools.

```

`buildtools-make`{.interpreted-text role="term"}

:   A variant of `buildtools`{.interpreted-text role="term"}, just providing the required version of `make` to run the OpenEmbedded build system.

`Classes`{.interpreted-text role="term"}

:   Files that provide for logic encapsulation and inheritance so that commonly used patterns can be defined once and then easily used in multiple recipes. For reference information on the Yocto Project classes, see the \"`ref-manual/classes:Classes`{.interpreted-text role="ref"}\" chapter. Class files end with the `.bbclass` filename extension.

`Configuration File`{.interpreted-text role="term"}

:   Files that hold global definitions of variables, user-defined variables, and hardware configuration information. These files tell the OpenEmbedded build system what to build and what to put into the image to support a particular platform.

```

Configuration files end with a `.conf` filename extension. The `conf/local.conf`{.interpreted-text role="file"} configuration file in the `Build Directory`{.interpreted-text role="term"} contains user-defined variables that affect every build. The `meta-poky/conf/distro/poky.conf`{.interpreted-text role="file"} configuration file defines Yocto \"distro\" configuration variables used only when building with this policy. Machine configuration files, which are located throughout the `Source Directory`{.interpreted-text role="term"}, define variables for specific hardware and are only used when building for that target (e.g. the `machine/beaglebone.conf`{.interpreted-text role="file"} configuration file defines variables for the Texas Instruments ARM Cortex-A8 development board).

```

`Container Layer`{.interpreted-text role="term"}

:   A flexible definition that typically refers to a single Git checkout which contains multiple (and typically related) sub-layers which can be included independently in your project\'s `bblayers.conf` file.

```

In some cases, such as with OpenEmbedded\'s :oe\_[git:%60meta-openembedded](git:%60meta-openembedded) \</meta-openembedded\>[ layer, the top level ]{.title-ref}[meta-openembedded/]{.title-ref}[ directory is not itself an actual layer, so you would never explicitly include it in a ]{.title-ref}[bblayers.conf]{.title-ref}[ file; rather, you would include any number of its layer subdirectories, such as :oe_git:\`meta-oe \</meta-openembedded/tree/meta-oe\>]{.title-ref}, :oe\_[git:%60meta-python](git:%60meta-python) \</meta-openembedded/tree/meta-python\>\` and so on.

On the other hand, some container layers (such as :yocto\_[git:%60meta-security](git:%60meta-security) \</meta-security\>[) have a top-level directory that is itself an actual layer, as well as a variety of sub-layers, both of which could be included in your ]{.title-ref}[bblayers.conf]{.title-ref}\` file.

In either case, the phrase \"container layer\" is simply used to describe a directory structure which contains multiple valid OpenEmbedded layers.

```

`Cross-Development Toolchain`{.interpreted-text role="term"}

:   In general, a cross-development toolchain is a collection of software development tools and utilities that run on one architecture and allow you to develop software for a different, or targeted, architecture. These toolchains contain cross-compilers, linkers, and debuggers that are specific to the target architecture.

```

The Yocto Project supports two different cross-development toolchains:

- A toolchain only used by and within BitBake when building an image for a target architecture.
- A relocatable toolchain used outside of BitBake by developers when developing applications that will run on a targeted device.

Creation of these toolchains is simple and automated. For information on toolchain concepts as they apply to the Yocto Project, see the \"`overview-manual/concepts:Cross-Development Toolchain Generation`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual. You can also find more information on using the relocatable toolchain in the `/sdk-manual/index`{.interpreted-text role="doc"} manual.

```

`Extensible Software Development Kit (eSDK)`{.interpreted-text role="term"}

:   A custom SDK for application developers. This eSDK allows developers to incorporate their library and programming changes back into the image to make their code available to other application developers.

```

For information on the eSDK, see the `/sdk-manual/index`{.interpreted-text role="doc"} manual.

```

`Image`{.interpreted-text role="term"}

:   An image is an artifact of the BitBake build process given a collection of recipes and related Metadata. Images are the binary output that run on specific hardware or QEMU and are used for specific use-cases. For a list of the supported image types that the Yocto Project provides, see the \"`ref-manual/images:Images`{.interpreted-text role="ref"}\" chapter.

`Initramfs`{.interpreted-text role="term"}

:   An Initial RAM Filesystem (`Initramfs`{.interpreted-text role="term"}) is an optionally compressed `cpio <Cpio>`{.interpreted-text role="wikipedia"} archive which is extracted by the Linux kernel into RAM in a special `tmpfs <Tmpfs>`{.interpreted-text role="wikipedia"} instance, used as the initial root filesystem.

```

This is a replacement for the legacy init RAM disk (\"initrd\") technique, booting on an emulated block device in RAM, but being less efficient because of the overhead of going through a filesystem and having to duplicate accessed file contents in the file cache in RAM, as for any block device.

> As far as bootloaders are concerned, `Initramfs`{.interpreted-text role="term"} and \"initrd\" images are still copied to RAM in the same way. That\'s why most

most bootloaders refer to `Initramfs`{.interpreted-text role="term"} images as \"initrd\" or \"init RAM disk\".

This kind of mechanism is typically used for two reasons:

\- For booting the same kernel binary on multiple systems requiring

:   different device drivers. The `Initramfs`{.interpreted-text role="term"} image is then customized

for each type of system, to include the specific kernel modules

:   necessary to access the final root filesystem. This technique

is used on all GNU / Linux distributions for desktops and servers.

\- For booting faster. As the root filesystem is extracted into RAM,

:   accessing the first user-space applications is very fast, compared to having to initialize a block device, to access multiple blocks from it, and to go through a filesystem having its own overhead. For example, this allows to display a splashscreen very early,

and to later take care of mounting the final root filesystem and

:   loading less time-critical kernel drivers.

This cpio archive can either be loaded to RAM by the bootloader, or be included in the kernel binary.

For information on creating and using an `Initramfs`{.interpreted-text role="term"}, see the \"`dev-manual/building:building an initial ram filesystem (Initramfs) image`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

```

`Layer`{.interpreted-text role="term"}

:   A collection of related recipes. Layers allow you to consolidate related metadata to customize your build. Layers also isolate information used when building for multiple architectures. Layers are hierarchical in their ability to override previous specifications. You can include any number of available layers from the Yocto Project and customize the build by adding your layers after them. You can search the Layer Index for layers used within Yocto Project.

```

For introductory information on layers, see the \"`overview-manual/yp-intro:The Yocto Project Layer Model`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual. For more detailed information on layers, see the \"`dev-manual/layers:Understanding and Creating Layers`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual. For a discussion specifically on BSP Layers, see the \"`bsp-guide/bsp:BSP Layers`{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Packages (BSP) Developer\'s Guide.

```

`LTS`{.interpreted-text role="term"}

:   This term means \"Long Term Support\", and in the context of the Yocto Project, it corresponds to selected stable releases for which bug and security fixes are provided for at least two years. See the `ref-long-term-support-releases`{.interpreted-text role="ref"} section for details.

`Metadata`{.interpreted-text role="term"}

:   A key element of the Yocto Project is the Metadata that is used to construct a Linux distribution and is contained in the files that the `OpenEmbedded Build System`{.interpreted-text role="term"} parses when building an image. In general, Metadata includes recipes, configuration files, and other information that refers to the build instructions themselves, as well as the data used to control what things get built and the effects of the build. Metadata also includes commands and data used to indicate what versions of software are used, from where they are obtained, and changes or additions to the software itself (patches or auxiliary files) that are used to fix bugs or customize the software for use in a particular situation. OpenEmbedded-Core is an important set of validated metadata.

```

In the context of the kernel (\"kernel Metadata\"), the term refers to the kernel config fragments and features contained in the :yocto\_[git:%60yocto-kernel-cache](git:%60yocto-kernel-cache) \</yocto-kernel-cache\>\` Git repository.

```

`Mixin`{.interpreted-text role="term"}

:   A `Mixin`{.interpreted-text role="term"} layer is a layer which can be created by the community to add a specific feature or support a new version of some package for an `LTS`{.interpreted-text role="term"} release. See the `ref-long-term-support-releases`{.interpreted-text role="ref"} section for details.

`OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"}

:   OE-Core is metadata comprised of foundational recipes, classes, and associated files that are meant to be common among many different OpenEmbedded-derived systems, including the Yocto Project. OE-Core is a curated subset of an original repository developed by the OpenEmbedded community that has been pared down into a smaller, core set of continuously validated recipes. The result is a tightly controlled and an quality-assured core set of recipes.

```

You can see the Metadata in the `meta` directory of the Yocto Project :yocto\_[git:%60Source](git:%60Source) Repositories \</poky\>\`.

```

`OpenEmbedded Build System`{.interpreted-text role="term"}

:   The build system specific to the Yocto Project. The OpenEmbedded build system is based on another project known as \"Poky\", which uses `BitBake`{.interpreted-text role="term"} as the task executor. Throughout the Yocto Project documentation set, the OpenEmbedded build system is sometimes referred to simply as \"the build system\". If other build systems, such as a host or target build system are referenced, the documentation clearly states the difference.

```

::: note
::: title
Note
:::

For some historical information about Poky, see the `Poky`{.interpreted-text role="term"} term.
:::

```

`Package`{.interpreted-text role="term"}

:   In the context of the Yocto Project, this term refers to a recipe\'s packaged output produced by BitBake (i.e. a \"baked recipe\"). A package is generally the compiled binaries produced from the recipe\'s sources. You \"bake\" something by running it through BitBake.

```

It is worth noting that the term \"package\" can, in general, have subtle meanings. For example, the packages referred to in the \"`ref-manual/system-requirements:required packages for the build host`{.interpreted-text role="ref"}\" section are compiled binaries that, when installed, add functionality to your Linux distribution.

Another point worth noting is that historically within the Yocto Project, recipes were referred to as packages \-\-- thus, the existence of several BitBake variables that are seemingly mis-named, (e.g. `PR`{.interpreted-text role="term"}, `PV`{.interpreted-text role="term"}, and `PE`{.interpreted-text role="term"}).

```

`Package Groups`{.interpreted-text role="term"}

:   Arbitrary groups of software Recipes. You use package groups to hold recipes that, when built, usually accomplish a single task. For example, a package group could contain the recipes for a company\'s proprietary or value-add software. Or, the package group could contain the recipes that enable graphics. A package group is really just another recipe. Because package group files are recipes, they end with the `.bb` filename extension.

`Poky`{.interpreted-text role="term"}

:   Poky, which is pronounced *Pock*-ee, is a reference embedded distribution and a reference test configuration. Poky provides the following:

```

- A base-level functional distro used to illustrate how to customize a distribution.
- A means by which to test the Yocto Project components (i.e. Poky is used to validate the Yocto Project).
- A vehicle through which you can download the Yocto Project.

Poky is not a product level distro. Rather, it is a good starting point for customization.

::: note
::: title
Note
:::

Poky began as an open-source project initially developed by OpenedHand. OpenedHand developed Poky from the existing OpenEmbedded build system to create a commercially supportable build system for embedded Linux. After Intel Corporation acquired OpenedHand, the poky project became the basis for the Yocto Project\'s build system.
:::

```

`Recipe`{.interpreted-text role="term"}

:   A set of instructions for building packages. A recipe describes where you get source code, which patches to apply, how to configure the source, how to compile it and so on. Recipes also describe dependencies for libraries or for other recipes. Recipes represent the logical unit of execution, the software to build, the images to build, and use the `.bb` file extension.

`Reference Kit`{.interpreted-text role="term"}

:   A working example of a system, which includes a `BSP<Board Support Package (BSP)>`{.interpreted-text role="term"} as well as a `build host<Build Host>`{.interpreted-text role="term"} and other components, that can work on specific hardware.

`SBOM`{.interpreted-text role="term"}

:   This term means *Software Bill of Materials*. When you distribute software, it offers a description of all the components you used, their corresponding licenses, their dependencies, the changes that were applied and the known vulnerabilities that were fixed.

```

This can be used by the recipients of the software to assess their exposure to license compliance and security vulnerability issues.

See the `Software Supply Chain <Software_supply_chain>`{.interpreted-text role="wikipedia"} article on Wikipedia for more details.

The OpenEmbedded Build System can generate such documentation for your project, in `SPDX`{.interpreted-text role="term"} format, based on all the metadata it used to build the software images. See the \"`dev-manual/sbom:creating a software bill of materials`{.interpreted-text role="ref"}\" section of the Development Tasks manual.

```

`Source Directory`{.interpreted-text role="term"}

:   This term refers to the directory structure created as a result of creating a local copy of the `poky` Git repository `git://git.yoctoproject.org/poky` or expanding a released `poky` tarball.

```

::: note
::: title
Note
:::

Creating a local copy of the poky Git repository is the recommended method for setting up your Source Directory.
:::

Sometimes you might hear the term \"poky directory\" used to refer to this directory structure.

::: note
::: title
Note
:::

The OpenEmbedded build system does not support file or directory names that contain spaces. Be sure that the Source Directory you use does not contain these types of names.
:::

The Source Directory contains BitBake, Documentation, Metadata and other files that all support the Yocto Project. Consequently, you must have the Source Directory in place on your development system in order to do any development using the Yocto Project.

When you create a local copy of the Git repository, you can name the repository anything you like. Throughout much of the documentation, \"poky\" is used as the name of the top-level folder of the local copy of the poky Git repository. So, for example, cloning the `poky` Git repository results in a local Git repository whose top-level folder is also named \"poky\".

While it is not recommended that you use tarball extraction to set up the Source Directory, if you do, the top-level directory name of the Source Directory is derived from the Yocto Project release tarball. For example, downloading and unpacking poky tarballs from :yocto_dl:[/releases/yocto/&DISTRO_REL_TAG;/]{.title-ref} results in a Source Directory whose root folder is named poky.

It is important to understand the differences between the Source Directory created by unpacking a released tarball as compared to cloning `git://git.yoctoproject.org/poky`. When you unpack a tarball, you have an exact copy of the files based on the time of release \-\-- a fixed release point. Any changes you make to your local files in the Source Directory are on top of the release and will remain local only. On the other hand, when you clone the `poky` Git repository, you have an active development repository with access to the upstream repository\'s branches and tags. In this case, any local changes you make to the local Source Directory can be later applied to active development branches of the upstream `poky` Git repository.

For more information on concepts related to Git repositories, branches, and tags, see the \"`overview-manual/development-environment:repositories, tags, and branches`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

```

`SPDX`{.interpreted-text role="term"}

:   This term means *Software Package Data Exchange*, and is used as a open standard for providing a *Software Bill of Materials* (`SBOM`{.interpreted-text role="term"}). This standard is developed through a [Linux Foundation project](https://spdx.dev/) and is used by the OpenEmbedded Build System to provide an `SBOM`{.interpreted-text role="term"} associated to each a software image.

```

For details, see Wikipedia\'s `SPDX page <Software_Package_Data_Exchange>`{.interpreted-text role="wikipedia"} and the \"`dev-manual/sbom:creating a software bill of materials`{.interpreted-text role="ref"}\" section of the Development Tasks manual.

```

`Sysroot`{.interpreted-text role="term"}

:   When cross-compiling, the target file system may be differently laid out and contain different things compared to the host system. The concept of a *sysroot* is directory which looks like the target filesystem and can be used to cross-compile against.

```

In the context of cross-compiling toolchains, a *sysroot* typically contains C library and kernel headers, plus the compiled binaries for the C library. A *multilib toolchain* can contain multiple variants of the C library binaries, each compiled for a target instruction set (such as `armv5`, `armv7` and `armv8`), and possibly optimized for a specific CPU core.

In the more specific context of the OpenEmbedded build System and of the Yocto Project, each recipe has two sysroots:

- A *target sysroot* contains all the **target** libraries and headers needed to build the recipe.
- A *native sysroot* contains all the **host** files and executables needed to build the recipe.

See the `SYSROOT_* <SYSROOT_DESTDIR>`{.interpreted-text role="term"} variables controlling how sysroots are created and stored.

```

`Task`{.interpreted-text role="term"}

:   A per-recipe unit of execution for BitBake (e.g. `ref-tasks-compile`{.interpreted-text role="ref"}, `ref-tasks-fetch`{.interpreted-text role="ref"}, `ref-tasks-patch`{.interpreted-text role="ref"}, and so forth). One of the major benefits of the build system is that, since each recipe will typically spawn the execution of numerous tasks, it is entirely possible that many tasks can execute in parallel, either tasks from separate recipes or independent tasks within the same recipe, potentially up to the parallelism of your build system.

`Toaster`{.interpreted-text role="term"}

:   A web interface to the Yocto Project\'s `OpenEmbedded Build System`{.interpreted-text role="term"}. The interface enables you to configure and run your builds. Information about builds is collected and stored in a database. For information on Toaster, see the `/toaster-manual/index`{.interpreted-text role="doc"}.

`Upstream`{.interpreted-text role="term"}

:   A reference to source code or repositories that are not local to the development system but located in a remote area that is controlled by the maintainer of the source code. For example, in order for a developer to work on a particular piece of code, they need to first get a copy of it from an \"upstream\" source.
:::
```
