---
tip: translate by openai@2023-06-07 22:14:52
...
---
title: QA Error and Warning Messages
------------------------------------

# Introduction

When building a recipe, the OpenEmbedded build system performs various QA checks on the output to ensure that common issues are detected and reported. Sometimes when you create a new recipe to build new software, it will build with no problems. When this is not the case, or when you have QA issues building any software, it could take a little time to resolve them.

> 当构建一个配方时，OpenEmbedded 构建系统对输出进行各种 QA 检查，以确保检测和报告常见问题。有时，当您创建一个新的配方来构建新的软件时，它将没有问题地构建。当不是这种情况时，或者当您构建任何软件时出现 QA 问题时，可能需要一些时间才能解决它们。

While it is tempting to ignore a QA message or even to disable QA checks, it is best to try and resolve any reported QA issues. This chapter provides a list of the QA messages and brief explanations of the issues you could encounter so that you can properly resolve problems.

> 虽然忽略 QA 消息或禁用 QA 检查很诱人，但最好尝试解决任何报告的 QA 问题。本章提供了 QA 消息的列表以及可能遇到的问题的简要解释，以便您可以正确解决问题。

The next section provides a list of all QA error and warning messages based on a default configuration. Each entry provides the message or error form along with an explanation.

> 下一节根据默认配置提供了所有 QA 错误和警告消息的列表。每条记录都提供消息或错误表单以及解释。

::: note
::: title
Note
:::

- At the end of each message, the name of the associated QA test (as listed in the \"`ref-classes-insane`\" section) appears within square brackets.

> 在每条消息的末尾，列在“ref-classes-insane”部分中的相关 QA 测试的名称会以方括号括起来。

- As mentioned, this list of error and warning messages is for QA checks only. The list does not cover all possible build errors or warnings you could encounter.

> 如所提及，这份错误和警告消息的列表仅用于 QA 检查。此列表不包括您可能遇到的所有可能的构建错误或警告。

- Because some QA checks are disabled by default, this list does not include all possible QA check errors and warnings.
  :::

# Errors and Warnings

:::
\- `<packagename>: <path> is using libexec please relocate to <libexecdir> [libexec]`
:::

> The specified package contains files in `/usr/libexec` when the distro configuration uses a different path for `<libexecdir>` By default, `<libexecdir>` is `$prefix/libexec`. However, this default can be changed (e.g. `$`).

:::
\- `package <packagename> contains bad RPATH <rpath> in file <file> [rpaths]`
:::

> The specified binary produced by the recipe contains dynamic library load paths (rpaths) that contain build system paths such as `TMPDIR` log. Depending on the build system used by the software being built, there might be a configure option to disable rpath usage completely within the build of the software.

:::
\- `<packagename>: <file> contains probably-redundant RPATH <rpath> [useless-rpaths]`
:::

> The specified binary produced by the recipe contains dynamic library load paths (rpaths) that on a standard system are searched by default by the linker (e.g. `/lib` and `/usr/lib`). While these paths will not cause any breakage, they do waste space and are unnecessary. Depending on the build system used by the software being built, there might be a configure option to disable rpath usage completely within the build of the software.

:::
\- `<packagename> requires <files>, but no providers in its RDEPENDS [file-rdeps]`
:::

> A file-level dependency has been identified from the specified package on the specified files, but there is no explicit corresponding entry in `RDEPENDS` should be declared in the recipe to ensure the packages providing them are built.

:::
\- `<packagename1> rdepends on <packagename2>, but it isn't a build dependency? [build-deps]`
:::

> There is a runtime dependency between the two specified packages, but there is nothing explicit within the recipe to enable the OpenEmbedded build system to ensure that dependency is satisfied. This condition is usually triggered by an `RDEPENDS` for the dependency.

:::
\- `non -dev/-dbg/nativesdk- package contains symlink .so: <packagename> path '<path>' [dev-so]`
:::

> Symlink `.so` files are for development only, and should therefore go into the `-dev` package. This situation might occur if you add `*.so*` rather than `*.so.*` to a non-dev package. Change `FILES`) such that the specified `.so` file goes into an appropriate `-dev` package.

:::
\- `non -staticdev package contains static .a library: <packagename> path '<path>' [staticdev]`
:::

> Static `.a` library files should go into a `-staticdev` package. Change `FILES`) such that the specified `.a` file goes into an appropriate `-staticdev` package.

:::
\- `<packagename>: found library in wrong location [libdir]`
:::

> The specified file may have been installed into an incorrect (possibly hardcoded) installation path. For example, this test will catch recipes that install `/lib/bar.so` when `$ for the package.

:::

- `non debug package contains .debug directory: <packagename> path <path> [debug-files]`

  The specified package contains a `.debug` directory, which should not appear in anything but the `-dbg` package. This situation might occur if you add a path which contains a `.debug` directory and do not explicitly add the `.debug` directory to the `-dbg` package. If this is the case, add the `.debug` directory explicitly to `FILES:$.

> 指定的软件包包含一个 `.debug` 目录，除了 `-dbg` 软件包之外，其他地方都不应该出现。如果您添加了一个包含 `.debug` 目录的路径，并且没有显式地将 `.debug` 目录添加到 `-dbg` 软件包中，就会发生这种情况。如果是这种情况，请显式地将 `.debug` 目录添加到 `FILES:$。
> :::

:::

- `<packagename> installs files in <path>, but it is expected to be empty [empty-dirs]`

  The specified package is installing files into a directory that is normally expected to be empty (such as `/tmp`). These files may be more appropriately installed to a different location, or perhaps alternatively not installed at all, usually by updating the `ref-tasks-install` task/function.

> 指定的软件包正在安装文件到通常期望为空的目录(例如 `/tmp`)。通常可以通过更新 `ref-tasks-install` 任务/函数来将这些文件更合适地安装到不同的位置，或者可能不安装它们。
> :::

:::

- `Architecture did not match (<file_arch>, expected <machine_arch>) in <file> [arch]`

  By default, the OpenEmbedded build system checks the Executable and Linkable Format (ELF) type, bit size, and endianness of any binaries to ensure they match the target architecture. This test fails if any binaries do not match the type since there would be an incompatibility. The test could indicate that the wrong compiler or compiler options have been used. Sometimes software, like bootloaders, might need to bypass this check. If the file you receive the error for is firmware that is not intended to be executed within the target operating system or is intended to run on a separate processor within the device, you can add \"arch\" to `INSANE_SKIP` log and verify that the compiler options being used are correct.

> 默认情况下，OpenEmbedded 构建系统会检查任何二进制文件的可执行和链接格式(ELF)类型，位大小和字节序，以确保它们与目标体系结构匹配。如果任何二进制文件类型不匹配，此测试将失败，因为存在不兼容性。该测试可能表明使用了错误的编译器或编译器选项。有时软件(如引导加载程序)可能需要绕过此检查。如果您收到错误的文件是旨在在目标操作系统中不执行的固件，或者旨在在设备内的单独处理器上运行，则可以为包添加“arch”到 INSANE_SKIP 中。另一个选择是检查 ref-tasks-compile 日志，并验证正在使用的编译器选项是否正确。

- `Bit size did not match (<file_bits>, expected <machine_bits>) in <file> [arch]`

  By default, the OpenEmbedded build system checks the Executable and Linkable Format (ELF) type, bit size, and endianness of any binaries to ensure they match the target architecture. This test fails if any binaries do not match the type since there would be an incompatibility. The test could indicate that the wrong compiler or compiler options have been used. Sometimes software, like bootloaders, might need to bypass this check. If the file you receive the error for is firmware that is not intended to be executed within the target operating system or is intended to run on a separate processor within the device, you can add \"arch\" to `INSANE_SKIP` log and verify that the compiler options being used are correct.

> 默认情况下，OpenEmbedded 构建系统会检查任何二进制文件的可执行和链接格式(ELF)类型，位大小和字节序，以确保它们与目标架构匹配。如果任何二进制文件不匹配类型，则此测试将失败，因为存在不兼容性。该测试可能表明使用了错误的编译器或编译器选项。有时，像引导加载程序这样的软件可能需要绕过此检查。如果您收到错误的文件是旨在在目标操作系统之外执行的固件，或者旨在在设备内的单独处理器上运行的固件，则可以为该软件包添加"arch"到 INSANE_SKIP 中。另一个选择是检查 ref-tasks-compile 日志，并验证正在使用的编译器选项是否正确。

 
:::

\- `Endianness did not match (<file_endianness>, expected <machine_endianness>) in <file> [arch]`

> By default, the OpenEmbedded build system checks the Executable and Linkable Format (ELF) type, bit size, and endianness of any binaries to ensure they match the target architecture. This test fails if any binaries do not match the type since there would be an incompatibility. The test could indicate that the wrong compiler or compiler options have been used. Sometimes software, like bootloaders, might need to bypass this check. If the file you receive the error for is firmware that is not intended to be executed within the target operating system or is intended to run on a separate processor within the device, you can add \"arch\" to `INSANE_SKIP` log and verify that the compiler options being used are correct.

:::
\- `ELF binary '<file>' has relocations in .text [textrel]`
:::

> The specified ELF binary contains relocations in its `.text` sections. This situation can result in a performance impact at runtime.
>
> Typically, the way to solve this performance issue is to add \"-fPIC\" or \"-fpic\" to the compiler command-line options. For example, given software that reads `CFLAGS` when you build it, you could add the following to your recipe:
>
> ```
> CFLAGS:append = " -fPIC "
> ```
>
> For more information on text relocations at runtime, see [https://www.akkadia.org/drepper/textrelocs.html](https://www.akkadia.org/drepper/textrelocs.html).

:::
\- `File '<file>' in package '<package>' doesn't have GNU_HASH (didn't pass LDFLAGS?) [ldflags]`
:::

> This indicates that binaries produced when building the recipe have not been linked with the `LDFLAGS` within the recipe as follows:
>
> ```
> TARGET_CC_ARCH += "$"
> ```

:::
\- `Package <packagename> contains Xorg driver (<driver>) but no xorg-abi- dependencies [xorg-driver-abi]`
:::

> The specified package contains an Xorg driver, but does not have a corresponding ABI package dependency. The xserver-xorg recipe provides driver ABI names. All drivers should depend on the ABI versions that they have been built against. Driver recipes that include `xorg-driver-input.inc` or `xorg-driver-video.inc` will automatically get these versions. Consequently, you should only need to explicitly add dependencies to binary driver recipes.

:::
\- `The /usr/share/info/dir file is not meant to be shipped in a particular package. [infodir]`
:::

> The `/usr/share/info/dir` should not be packaged. Add the following line to your `ref-tasks-install` task or to your `do_install:append` within the recipe as follows:
>
> ```
> rm $/dir
> ```

:::
\- `Symlink <path> in <packagename> points to TMPDIR [symlink-to-sysroot]`
:::

> The specified symlink points into `TMPDIR` on the host. Such symlinks will work on the host. However, they are clearly invalid when running on the target. You should either correct the symlink to use a relative path or remove the symlink.

:::
\- `<file> failed sanity test (workdir) in path <path> [la]`
:::

> The specified `.la` file contains `TMPDIR` paths. Any `.la` file containing these paths is incorrect since `libtool` adds the correct sysroot prefix when using the files automatically itself.

:::
\- `<file> failed sanity test (tmpdir) in path <path> [pkgconfig]`
:::

> The specified `.pc` file contains `TMPDIR` paths. Any `.pc` file containing these paths is incorrect since `pkg-config` itself adds the correct sysroot prefix when the files are accessed.

:::
\- `<packagename> rdepends on <debug_packagename> [debug-deps]`
:::

> There is a dependency between the specified non-dbg package (i.e. a package whose name does not end in `-dbg`) and a package that is a `dbg` package. The `dbg` packages contain debug symbols and are brought in using several different methods:
>
> - Using the `dbg-pkgs` `IMAGE_FEATURES` value.
> - Using `IMAGE_INSTALL`.
> - As a dependency of another `dbg` package that was brought in using one of the above methods.
>
> The dependency might have been automatically added because the `dbg` package erroneously contains files that it should not contain (e.g. a non-symlink `.so` file) or it might have been added manually (e.g. by adding to `RDEPENDS`).

:::
\- `<packagename> rdepends on <dev_packagename> [dev-deps]`
:::

> There is a dependency between the specified non-dev package (a package whose name does not end in `-dev`) and a package that is a `dev` package. The `dev` packages contain development headers and are usually brought in using several different methods:
>
> - Using the `dev-pkgs` `IMAGE_FEATURES` value.
> - Using `IMAGE_INSTALL`.
> - As a dependency of another `dev` package that was brought in using one of the above methods.
>
> The dependency might have been automatically added (because the `dev` package erroneously contains files that it should not have (e.g. a non-symlink `.so` file) or it might have been added manually (e.g. by adding to `RDEPENDS`).

:::
\- `<var>:<packagename> is invalid: <comparison> (<value>)   only comparisons <, =, >, <=, and >= are allowed [dep-cmp]`
:::

> If you are adding a versioned dependency relationship to one of the dependency variables (`RDEPENDS`), you must only use the named comparison operators. Change the versioned dependency values you are adding to match those listed in the message.

:::

\- `<recipename>: The compile log indicates that host include and/or library paths were used. Please check the log '<logfile>' for more information. [compile-host-path]`

> \- <recipename>：编译日志指示使用了主机包含和/或库路径。请检查日志'<logfile>'以获取更多信息。[compile-host-path]
> :::

> The log for the `ref-tasks-compile` task indicates that paths on the host were searched for files, which is not appropriate when cross-compiling. Look for \"is unsafe for cross-compilation\" or \"CROSS COMPILE Badness\" in the specified log file.

:::

\- `<recipename>: The install log indicates that host include and/or library paths were used. Please check the log '<logfile>' for more information. [install-host-path]`

> \- <recipename>: 安装日志指出使用了主机包含和/或库路径。请查看日志'<logfile>'以获取更多信息。[install-host-path]
> :::

> The log for the `ref-tasks-install` task indicates that paths on the host were searched for files, which is not appropriate when cross-compiling. Look for \"is unsafe for cross-compilation\" or \"CROSS COMPILE Badness\" in the specified log file.

:::

\- `This autoconf log indicates errors, it looked at host include and/or library paths while determining system capabilities. Rerun configure task after fixing this. [configure-unsafe]`

> 这个自动配置日志指出错误，它在确定系统功能时查看了主机包含文件和/或库路径。修复后重新运行配置任务。[configure-unsafe]
> :::

> The log for the `ref-tasks-configure` task indicates that paths on the host were searched for files, which is not appropriate when cross-compiling. Look for \"is unsafe for cross-compilation\" or \"CROSS COMPILE Badness\" in the specified log file.

:::
\- `<packagename> doesn't match the [a-z0-9.+-]+ regex [pkgname]`
:::

> The convention within the OpenEmbedded build system (sometimes enforced by the package manager itself) is to require that package names are all lower case and to allow a restricted set of characters. If your recipe name does not match this, or you add packages to `PACKAGES`, change the package name appropriately.

:::
\- `<recipe>: configure was passed unrecognized options: <options> [unknown-configure-option]`
:::

> The configure script is reporting that the specified options are unrecognized. This situation could be because the options were previously valid but have been removed from the configure script. Or, there was a mistake when the options were added and there is another option that should be used instead. If you are unsure, consult the upstream build documentation, the `./configure --help` output, and the upstream change log or release notes. Once you have worked out what the appropriate change is, you can update `EXTRA_OECONF` option values accordingly.

:::
\- `Recipe <recipefile> has PN of "<recipename>" which is in OVERRIDES, this can result in unexpected behavior. [pn-overrides]`
:::

> The specified recipe has a name (`PN` for additional information.

:::

- `<recipefile>: Variable <variable> is set as not being package specific, please fix this. [pkgvarcheck]`

  Certain variables (`RDEPENDS` = "value"`rather than` RDEPENDS = "value"`). If you receive this error, correct any assignments to these variables within your recipe.

> 某些变量(`RDEPENDS`、`RRECOMMENDS`、`RSUGGESTS`、`RCONFLICTS`、`RPROVIDES`、`RREPLACES`、`FILES`、`pkg_preinst`、`pkg_postinst`、`pkg_prerm`、`pkg_postrm` 和 `ALLOW_EMPTY`)应该专门设置给某个包(即应该使用带有包名覆盖的赋值，例如 `RDEPENDS:$ = "value"`，而不是 `RDEPENDS = "value"`)。如果您收到此错误，请更正 recipes 中对这些变量的任何赋值。

- `recipe uses DEPENDS:$, should use DEPENDS [pkgvarcheck]`

  > This check looks for instances of setting `DEPENDS:$ instead.
  >

> 此检查查找设置“DEPENDS：$”的实例，这是错误的(DEPENDS 是针对特定配方的变量，因此不能为特定包指定它，也不会生效)。请改用 DEPENDS。
> :::

:::
\- `File '<file>' from <recipename> was already stripped, this will prevent future debugging! [already-stripped]`
:::

> Produced binaries have already been stripped prior to the build system extracting debug symbols. It is common for upstream software projects to default to stripping debug symbols for output binaries. In order for debugging to work on the target using `-dbg` packages, this stripping must be disabled.
>
> Depending on the build system used by the software being built, disabling this stripping could be as easy as specifying an additional configure option. If not, disabling stripping might involve patching the build scripts. In the latter case, look for references to \"strip\" or \"STRIP\", or the \"-s\" or \"-S\" command-line options being specified on the linker command line (possibly through the compiler command line if preceded with \"-Wl,\").
>
> ::: note
> ::: title
> Note
> :::
>
> Disabling stripping here does not mean that the final packaged binaries will be unstripped. Once the OpenEmbedded build system splits out debug symbols to the `-dbg` package, it will then strip the symbols from the binaries.
> :::

:::
\- `<packagename> is listed in PACKAGES multiple times, this leads to packaging errors. [packages-list]`
:::

> Package names must appear only once in the `PACKAGES` that is already in the variable\'s value.

:::

\- `FILES variable for package <packagename> contains '//' which is invalid. Attempting to fix this but you should correct the metadata. [files-invalid]`

> \- `<packagename>` 包的 FILES 变量包含无效的'//'。正在尝试修复，但您应该修正元数据。[files-invalid]
> :::

> The string \"//\" is invalid in a Unix path. Correct all occurrences where this string appears in a `FILES` variable so that there is only a single \"/\".

:::

- `<recipename>: Files/directories were installed but not shipped in any package [installed-vs-shipped]`

  Files have been installed within the `ref-tasks-install` variable. Files that do not appear in any package cannot be present in an image later on in the build process. You need to do one of the following:

> 文件已经安装在 `ref-tasks-install` 任务中，但没有通过 `FILES` 变量包含在任何包中。不出现在任何包中的文件在构建过程中的后续阶段不能出现在镜像中。您需要执行以下操作之一：

- Add the files to `FILES`` for the main package).

> 将文件添加到要在其中出现的包的 `FILES`(例如，主包的 `FILES:$`)。

- Delete the files at the end of the `ref-tasks-install` task if the files are not needed in any package.
- `<oldpackage>-<oldpkgversion> was registered as shlib provider for <library>, changing it to <newpackage>-<newpkgversion> because it was built later`

> - 因为<newpackage>-<newpkgversion>更新更晚，所以将<oldpackage>-<oldpkgversion>注册为<library>的 shlib 提供者改为<newpackage>-<newpkgversion>。

This message means that both `<oldpackage>` and `<newpackage>` provide the specified shared library. You can expect this message when a recipe has been renamed. However, if that is not the case, the message might indicate that a private version of a library is being erroneously picked up as the provider for a common library. If that is the case, you should add the library\'s `.so` filename to `PRIVATE_LIBS` in the recipe that provides the private version of the library.

> 这条消息意味着<oldpackage>和<newpackage>都提供了指定的共享库。当一个 recipes 被重命名时，你可以期待这条消息。但是，如果情况并非如此，该消息可能表明一个私有库被误认为是公共库的提供者。如果是这种情况，你应该在提供私有库的 recipes 中将库的 `.so` 文件名添加到 `PRIVATE_LIBS` 中。
> :::

:::

- `LICENSE:<packagename> includes licenses (<licenses>) that are not listed in LICENSE [unlisted-pkg-lics]`

  The `LICENSE`.

> `LICENSE` 中。
> :::

:::

- `AM_GNU_GETTEXT used but no inherit gettext [configure-gettext]`

  > If a recipe is building something that uses automake and the automake files contain an `AM_GNU_GETTEXT` directive then this check will fail if there is no `inherit gettext` statement in the recipe to ensure that gettext is available during the build. Add `inherit gettext` to remove the warning.
  >

> 如果一个配方正在构建使用 automake 的东西，而 automake 文件包含一个 `AM_GNU_GETTEXT` 指令，那么如果配方中没有 `inherit gettext` 语句来确保在构建期间可以使用 gettext，这个检查将失败。添加 `inherit gettext` 以消除警告。
> :::

:::

- `package contains mime types but does not inherit mime: <packagename> path '<file>' [mime]`

  > The specified package contains mime type files (`.xml` files in `$ step if they are not needed.
  >

> 指定的软件包包含 MIME 类型文件(在 `$ 步骤中删除这些文件，如果它们不需要的话。
> :::

:::

- `package contains desktop file with key 'MimeType' but does not inhert mime-xdg: <packagename> path '<file>' [mime-xdg]`

  > The specified package contains a .desktop file with a \'MimeType\' key present, but does not inherit the `ref-classes-mime-xdg` step if they are not needed.
  >

> 指定的软件包包含一个带有'MimeType'键的.desktop 文件，但没有继承所需的'ref-classes-mime-xdg'步骤中删除文件。
> :::

:::

- `<recipename>: SRC_URI uses unstable GitHub archives [src-uri-bad]`

  > GitHub provides \"archive\" tarballs, however these can be re-generated on the fly and thus the file\'s signature will not necessarily match that in the `SRC_URI` checksums in future leading to build failures. It is recommended that you use an official release tarball or switch to pulling the corresponding revision in the actual git repository instead.
  >

> GitHub 提供“归档”tarball，但是这些可以实时重新生成，因此文件的签名可能不会与 `SRC_URI` 中的校验和匹配，从而导致构建失败。建议您使用官方发布的 tarball 或者切换到实际的 git 存储库中拉取相应的修订版本。

- `SRC_URI uses PN not BPN [src-uri-bad]`

  > If some part of `SRC_URI`` instead.
  >

> 如果 SRC_URI 中的某些部分需要引用 recipes 名称，则应使用 $。
> :::

:::

- `<recipename>: recipe doesn't inherit features_check [unhandled-features-check]`

  > This check ensures that if one of the variables that the `ref-classes-features_check` in order for the requirement to actually work. If you are seeing this message, either add `inherit features_check` to your recipe or remove the reference to the variable if it is not needed.
  >

> 此检查确保，如果使用了 `ref-classes-features_check`，以使要求实际有效。如果您看到此消息，请向配方添加 `inherit features_check` 或删除变量的引用(如果不需要)。
> :::

:::

- `<recipename>: recipe defines ALTERNATIVE:<packagename> but doesn't inherit update-alternatives. This might fail during do_rootfs later! [missing-update-alternatives]`

> `- <recipename>: 该配方定义了ALTERNATIVE:<packagename>，但没有继承update-alternatives。这可能会在do_rootfs阶段失败！[missing-update-alternatives]`

> This check ensures that if a recipe sets the `ALTERNATIVE` such that the alternative will be correctly set up. If you are seeing this message, either add `inherit update-alternatives` to your recipe or remove the reference to the variable if it is not needed.

> 这个检查确保，如果配方设置了 `ALTERNATIVE` 变量，那么配方也会继承 `ref-classes-update-alternatives`，以便正确设置替代选项。如果您看到此消息，请向您的配方添加 `inherit update-alternatives` 或者如果不需要，则删除对该变量的引用。
> :::

:::

- `<packagename>: <file> maximum shebang size exceeded, the maximum size is 128. [shebang-size]`

  > This check ensures that the shebang line (`#!` in the first line) for a script is not longer than 128 characters, which can cause an error at runtime depending on the operating system. If you are seeing this message then the specified script may need to be patched to have a shorter in order to avoid runtime problems.
  >

> 这个检查确保脚本的 shebang 行(第一行中的 `#!`)不超过 128 个字符，否则可能会根据操作系统在运行时出错。如果您看到此消息，则可能需要修补指定的脚本以缩短以避免运行时问题。
> :::

:::

- `<packagename> contains perllocal.pod (<files>), should not be installed [perllocalpod]`

  > `perllocal.pod` is an index file of locally installed modules and so shouldn\'t be installed by any distribution packages. The `ref-classes-cpan` class already sets `NO_PERLLOCAL` to stop this file being generated by most Perl recipes, but if a recipe is using `MakeMaker` directly then they might not be doing this correctly. This check ensures that perllocal.pod is not in any package in order to avoid multiple packages shipping this file and thus their packages conflicting if installed together.
  >

> `perllocal.pod` 是本地安装模块的索引文件，不应该由任何发行版本安装。`ref-classes-cpan` 类已经设置了 `NO_PERLLOCAL`，以阻止大多数 Perl 配方生成此文件，但如果配方直接使用 `MakeMaker`，则可能没有正确执行此操作。此检查确保 perllocal.pod 不在任何包中，以避免多个包同时发送此文件，从而如果一起安装则可能会发生冲突。
> :::

:::

- `<packagename> package is not obeying usrmerge distro feature. /<path> should be relocated to /usr. [usrmerge]`

  > If `usrmerge` is in `DISTRO_FEATURES` is calling into, e.g. `make install` is using hardcoded paths instead of the variables set up for this (`bindir`, `sbindir`, etc.), and should be changed so that it does.
  >

> 如果 `usrmerge` 在 `DISTRO_FEATURES` 中，此检查将确保没有包安装文件到根(`/bin`，`/sbin`，`/lib`，`/lib64`)目录。如果您看到此消息，则表示 `ref-tasks-install` 步骤(或可能是 `ref-tasks-install` 调用的构建过程，例如 `make install` 正在使用硬编码路径而不是为此设置的变量(`bindir`，`sbindir` 等)，应该更改以这样做。
> :::

:::

- `Fuzz detected: <patch output> [patch-fuzz]`

  > This check looks for evidence of \"fuzz\" when applying patches within the `ref-tasks-patch` task. Patch fuzz is a situation when the `patch` tool ignores some of the context lines in order to apply the patch. Consider this example:
  >

> 这个检查旨在检查在 `ref-tasks-patch` 任务中应用补丁时是否存在“fuzz”的证据。补丁 fuzz 是指 `patch` 工具忽略一些上下文行以便应用补丁的情况。举个例子：
>
> Patch to be applied:
>
> ```
> --- filename
> +++ filename
>  context line 1
>  context line 2
>  context line 3
> +newly added line
>  context line 4
>  context line 5
>  context line 6
> ```
>
> Original source code:
>
> ```
> different context line 1
> different context line 2
> context line 3
> context line 4
> different context line 5
> different context line 6
> ```
>
> Outcome (after applying patch with fuzz):
>
> ```
> different context line 1
> different context line 2
> context line 3
> newly added line
> context line 4
> different context line 5
> different context line 6
> ```

> Chances are, the newly added line was actually added in a completely wrong location, or it was already in the original source and was added for the second time. This is especially possible if the context line 3 and 4 are blank or have only generic things in them, such as `#endif` or `}`. Depending on the patched code, it is entirely possible for an incorrectly patched file to still compile without errors.

> 可能性很大，新添加的行实际上是被添加到完全错误的位置，或者它已经存在于原始源代码中，并且被重复添加了。如果上下文行 3 和 4 是空的或者只有一些通用的东西，比如“#endif”或“}”，这尤其有可能发生。根据补丁代码的不同，错误补丁文件仍然可能编译而不出错。
>
> *How to eliminate patch fuzz warnings*
>
> Use the `devtool` command as explained by the warning. First, unpack the source into devtool workspace:
>
> ```
> devtool modify <recipe>
> ```
>
> This will apply all of the patches, and create new commits out of them in the workspace \-\-- with the patch context updated.
>
> Then, replace the patches in the recipe layer:
>
> ```
> devtool finish --force-patch-refresh <recipe> <layer_path>
> ```
>
> The patch updates then need be reviewed (preferably with a side-by-side diff tool) to ensure they are indeed doing the right thing i.e.:
>
> 1. they are applied in the correct location within the file;
> 2. they do not introduce duplicate lines, or otherwise do things that are no longer necessary.

> To confirm these things, you can also review the patched source code in devtool\'s workspace, typically in `<build_dir>/workspace/sources/<recipe>/`

> 确认这些内容，您还可以在 devtool 的工作区中查看已补丁源代码，通常在 `<build_dir>/workspace/sources/<recipe>/` 中。

> Once the review is done, you can create and publish a layer commit with the patch updates that modify the context. Devtool may also refresh other things in the patches, those can be discarded.

> 一旦审核完成，您可以创建并发布一个带有修补程序更新的图层提交，以修改上下文。Devtool 也可能会刷新补丁中的其他内容，这些可以被丢弃。
> :::

:::

- `Missing Upstream-Status in patch <patchfile> Please add according to <url> [patch-status-core/patch-status-noncore]`

  > The Upstream-Status value is missing in the specified patch file\'s header. This value is intended to track whether or not the patch has been sent upstream, whether or not it has been merged, etc.
  >

> 在指定的补丁文件头部缺少 Upstream-Status 值。该值旨在跟踪补丁是否已经发送到上游，是否已经合并等。

> There are two options for this same check - `patch-status-core` (for recipes in OE-Core) and `patch-status-noncore` (for recipes in any other layer).

> 这个检查有两个选项 - `patch-status-core`(用于 OE-Core 中的配方)和 `patch-status-noncore`(用于其他层中的配方)。

> For more information on setting Upstream-Status see: [https://www.openembedded.org/wiki/Commit_Patch_Message_Guidelines#Patch_Header_Recommendations:_Upstream-Status](https://www.openembedded.org/wiki/Commit_Patch_Message_Guidelines#Patch_Header_Recommendations:_Upstream-Status)

> 更多有关设置 Upstream-Status 的信息请参见：[https://www.openembedded.org/wiki/Commit_Patch_Message_Guidelines#Patch_Header_Recommendations:_Upstream-Status](https://www.openembedded.org/wiki/Commit_Patch_Message_Guidelines#Patch_Header_Recommendations:_Upstream-Status)

- `Malformed Upstream-Status in patch <patchfile> Please correct according to <url> [patch-status-core/patch-status-noncore]`

  > The Upstream-Status value in the specified patch file\'s header is invalid -it must be a specific format. See the \"Missing Upstream-Status\" entry above for more information.
  >

> 指定的补丁文件头中的 Upstream-Status 值无效——必须是特定格式。有关更多信息，请参阅上面的“Missing Upstream-Status”条目。
> :::

:::

- `File <filename> in package <packagename> contains reference to TMPDIR [buildpaths]`

  > This check ensures that build system paths (including `TMPDIR`) do not appear in output files, which not only leaks build system configuration into the target, but also hinders binary reproducibility as the output will change if the build system configuration changes.
  >

> 这个检查确保构建系统路径(包括 `TMPDIR`)不会出现在输出文件中，这不仅会泄露构建系统配置到目标，而且会阻碍二进制可重复性，因为如果构建系统配置改变，输出也会改变。

> Typically these paths will enter the output through some mechanism in the configuration or compilation of the software being built by the recipe. To resolve this issue you will need to determine how the detected path is entering the output. Sometimes it may require adjusting scripts or code to use a relative path rather than an absolute one, or to pick up the path from runtime configuration or environment variables.

> 通常，这些路径将通过 recipes 中构建的软件的配置或编译中的某种机制进入输出。为了解决这个问题，您需要确定检测到的路径是如何进入输出的。有时，可能需要调整脚本或代码以使用相对路径而不是绝对路径，或从运行时配置或环境变量中提取路径。
> :::

# Configuring and Disabling QA Checks

You can configure the QA checks globally so that specific check failures either raise a warning or an error message, using the `WARN_QA`\" section.

> 你可以使用 `WARN_QA`”部分。

::: note
::: title
Note
:::

Please keep in mind that the QA checks are meant to detect real or potential problems in the packaged output. So exercise caution when disabling these checks.

> 请记住，QA 检查旨在检测打包输出中的实际或潜在问题。因此，在禁用这些检查时要小心。
> :::
