---
tip: translate by openai@2023-06-07 22:16:48
...
---
title: Migration notes for 3.4 (honister)
-----------------------------------------

This section provides migration information for moving to the Yocto Project 3.4 Release (codename \"honister\") from the prior release.

# Override syntax changes

In this release, the `:` character replaces the use of `_` to refer to an override, most commonly when making a conditional assignment of a variable. This means that an entry like:

> 在此版本中，“：”字符取代了使用“_”来指代覆盖，通常是在对变量进行条件赋值时。这意味着一个条目，如：

```
SRC_URI_qemux86 = "file://somefile"
```

now becomes:

```
SRC_URI:qemux86 = "file://somefile"
```

since `qemux86` is an override. This applies to any use of override syntax, so the following:

```
SRC_URI_append = " file://somefile"
SRC_URI_append_qemux86 = " file://somefile2"
SRC_URI_remove_qemux86-64 = "file://somefile3"
SRC_URI_prepend_qemuarm = "file://somefile4 "
FILES_$/xyz"
IMAGE_CMD_tar = "tar"
BASE_LIB_tune-cortexa76 = "lib"
SRCREV_pn-bash = "abc"
BB_TASK_NICE_LEVEL_task-testimage = '0'
```

would now become:

```
SRC_URI:append = " file://somefile"
SRC_URI:append:qemux86 = " file://somefile2"
SRC_URI:remove:qemux86-64 = "file://somefile3"
SRC_URI:prepend:qemuarm = "file://somefile4 "
FILES:$/xyz"
IMAGE_CMD:tar = "tar"
BASE_LIB:tune-cortexa76 = "lib"
SRCREV:pn-bash = "abc"
BB_TASK_NICE_LEVEL:task-testimage = '0'
```

This also applies to `variable queries to the datastore <bitbake-user-manual/bitbake-user-manual-metadata:functions for accessing datastore variables>`")`.

> 这也适用于对数据存储的变量查询 <bitbake-user-manual/bitbake-user-manual-metadata:functions for accessing datastore variables>")`。

Whilst some of these are fairly obvious such as `MACHINE` but applied conditionally in specific contexts such as packaging. `task-<taskname>` is another context specific override, the context being specific tasks in that case. Tune overrides are another special case where some code does use them as overrides but some does not. We plan to try and make the tune code use overrides more consistently in the future.

> 尽管有些(覆盖)很明显，比如 `MACHINE` 里，而是根据特定的情况(例如打包)有条件地应用。`task-<taskname>` 是另一种特定情况的覆盖，其特定情况是特定任务。调整覆盖也是一种特殊情况，有些代码使用它们作为覆盖，但有些代码不使用。我们计划尝试使调整代码在未来更加一致地使用覆盖。

There are some variables which do not use override syntax which include the suffix to variables in `layer.conf` files such as `BBFILE_PATTERN` override causing some confusion. We do plan to try and improve consistency as these issues are identified.

> 有一些变量不使用覆盖语法，其中包括 `layer.conf` 文件中的变量后缀，例如 `BBFILE_PATTERN` 覆盖相同，从而引起一些混淆。我们确实计划尝试改善一致性，因为这些问题已经被识别出来了。

To help with migration of layers, a script has been provided in OE-Core. Once configured with the overrides used by a layer, this can be run as:

```
<oe-core>/scripts/contrib/convert-overrides.py <layerdir>
```

::: note
::: title
Note
:::

Please read the notes in the script as it isn\'t entirely automatic and it isn\'t expected to handle every case. In particular, it needs to be told which overrides the layer uses (usually machine and distro names/overrides) and the result should be carefully checked since it can be a little enthusiastic and will convert references to `_append`, `_remove` and `_prepend` in function and variable names.

> 请仔细阅读脚本中的注释，因为它不是完全自动化的，也不能处理所有情况。特别是，它需要被告知层使用哪些覆盖(通常是机器和发行版名称/覆盖)，并且应该仔细检查结果，因为它可能有点热情并且会将对函数和变量名称中的 `_append`、`_remove` 和 `_prepend` 的引用转换为。
> :::

For reference, this conversion is important as it allows BitBake to more reliably determine what is an override and what is not, as underscores are also used in variable names without intending to be overrides. This should allow us to proceed with other syntax improvements and simplifications for usability. It also means BitBake no longer has to guess and maintain large lookup lists just in case e.g. `functionname` in `my_functionname` is an override, and thus should improve efficiency.

> 为了参考，这种转换很重要，因为它允许 BitBake 更可靠地确定什么是覆盖，什么不是，因为下划线也用于不打算覆盖的变量名称。这应该允许我们继续进行其他语法改进和简化以提高可用性。这也意味着 BitBake 不再需要猜测并维护大量查找列表，以防例如 `my_functionname` 中的 `functionname` 是一个覆盖，从而提高效率。

# New host dependencies

The `lz4c`, `pzstd` and `zstd` commands are now required to be installed on the build host to support LZ4 and Zstandard compression functionality. These are typically provided by `lz4` and `zstd` packages in most Linux distributions. Alternatively they are available as part of `buildtools`.

> 需要在构建主机上安装 `lz4c`、`pzstd` 和 `zstd` 命令以支持 LZ4 和 Zstandard 压缩功能。这些通常由大多数 Linux 发行版中的 `lz4` 和 `zstd` 软件包提供。或者，如果您的发行版不提供它们，可以从 `buildtools` tarball 中获得。有关更多信息，请参阅 `ref-manual/system-requirements:required packages for the build host`。

# Removed recipes

The following recipes have been removed in this release:

- `assimp`: problematic from a licensing perspective and no longer needed by anything else
- `clutter-1.0`: legacy component moved to meta-gnome
- `clutter-gst-3.0`: legacy component moved to meta-gnome
- `clutter-gtk-1.0`: legacy component moved to meta-gnome
- `cogl-1.0`: legacy component moved to meta-gnome
- `core-image-clutter`: removed along with clutter
- `linux-yocto`: removed version 5.4 recipes (5.14 and 5.10 still provided)
- `mklibs-native`: not actively tested and upstream mklibs still requires Python 2
- `mx-1.0`: obsolete (last release 2012) and isn\'t used by anything in any known layer
- `packagegroup-core-clutter`: removed along with clutter

# Removed classes

- `clutter`: moved to meta-gnome along with clutter itself
- `image-mklibs`: not actively tested and upstream mklibs still requires Python 2
- `meta`: no longer useful. Recipes that need to skip installing packages should inherit `ref-classes-nopackages` instead.

> `-` 元：不再有用。不需要安装软件包的配方应该继承 `ref-classes-nopackages`。

# Prelinking disabled by default

Recent tests have shown that prelinking works only when PIE is not enabled (see [here](https://rlbl.me/prelink-1) and [here](https://rlbl.me/prelink-2)), and as PIE is both a desirable security feature, and the only configuration provided and tested by the Yocto Project, there is simply no sense in continuing to enable prelink.

> 最近的测试表明，当不启用 PIE 时，prelink 才能正常工作(参见[此处](https://rlbl.me/prelink-1)和[此处](https://rlbl.me/prelink-2))，而 PIE 既是一种理想的安全功能，又是 Yocto Project 提供和测试的唯一配置，因此继续启用 prelink 毫无意义。

There\'s also a concern that no one is maintaining the code, and there are open bugs (including :yocto_bugs:[this serious one \</show_bug.cgi?id=14429\>]). Given that prelink does intricate address arithmetic and rewriting of binaries the best option is to disable the feature. It is recommended that you consider disabling this feature in your own configuration if it is currently enabled.

> 也有担心没有人维护代码，而且还有未修复的错误(包括：[这个严重的错误\</show_bug.cgi?id=14429\>])。鉴于 prelink 需要复杂的地址计算和二进制文件的重写，最好的选择是禁用该功能。如果当前已启用，建议您考虑在自己的配置中禁用此功能。

# Virtual runtime provides

Recipes shouldn\'t use the `virtual/` string in `RPROVIDES`).

> recipes 不应在 RPROVIDES 和 RDEPENDS 中使用“virtual/”字符串，因为“virtual/”在 RPROVIDES 和 RDEPENDS 中没有特殊含义(与相应的构建时 PROVIDES 和 DEPENDS 不同)，这很令人困惑。

# Tune files moved to architecture-specific directories

The tune files found in `conf/machine/include` have now been moved into their respective architecture name directories under that same location; e.g. x86 tune files have moved into an `x86` subdirectory, MIPS tune files have moved into a `mips` subdirectory, etc. The ARM tunes have an extra level (`armv8a`, `armv8m`, etc.) and some have been renamed to make them uniform with the rest of the tunes. See :yocto_[git:%60this](git:%60this) commit \</poky/commit/?id=1d381f21f5f13aa0c4e1a45683ed656ebeedd37d\>\` for reference.

> 文件夹 `conf/machine/include` 中的调谐文件现在已经移动到相应的架构名称目录下，例如 x86 调谐文件移动到 `x86` 子目录，MIPS 调谐文件移动到 `mips` 子目录等。ARM 调谐文件有一个额外的层次(`armv8a`，`armv8m` 等)，有些文件已经重命名，以使它们与其他调谐文件一致。参考：yocto \[git:`这个`(git:`这个`)提交\</poky/commit/?id=1d381f21f5f13aa0c4e1a45683ed656ebeedd37d\>\`。

If you have any references to tune files (e.g. in custom machine configuration files) they will need to be updated.

# Extensible SDK host extension

For a normal SDK, some layers append to `TOOLCHAIN_HOST_TASK` unconditionally which is fine, until the eSDK tries to override the variable to its own values. Instead of installing packages specified in this variable it uses native recipes instead \-\-- a very different approach. This has led to confusing errors when binaries are added to the SDK but not relocated.

> 对于一个正常的 SDK，有些层会无条件地附加到 TOOLCHAIN_HOST_TASK，这很正常，直到 eSDK 试图覆盖变量以自己的值为止。它不是安装 TOOLCHAIN_HOST_TASK 中指定的软件包，而是使用本地的配方——一种非常不同的方法。当二进制文件添加到 SDK 但没有重定位时，这就导致了令人困惑的错误。

To avoid these issues, a new `TOOLCHAIN_HOST_TASK_ESDK` variable has been created. If you wish to extend what is installed in the host portion of the eSDK then you will now need to set this variable.

> 为了避免这些问题，已经创建了一个新的 `TOOLCHAIN_HOST_TASK_ESDK` 变量。如果您希望扩展 eSDK 主机部分安装的内容，那么您现在需要设置此变量。

# Package/recipe splitting

- `perl-cross` has been split out from the main `perl` recipe to its own `perlcross` recipe for maintenance reasons. If you have bbappends for the perl recipe then these may need extending.

> `perl-cross` 已从主 `perl` 配方分离出来，单独形成 `perlcross` 配方，以便维护。如果您对 perl 配方有 bbappends，则可能需要扩展它们。

- The `wayland` recipe now packages its binaries in a `wayland-tools` package rather than putting them into `wayland-dev`.
- Xwayland has been split out of the xserver-xorg tree and thus is now in its own `xwayland` recipe. If you need Xwayland in your image then you may now need to add it explicitly.

> Xwayland 已从 xserver-xorg 树中分离出来，因此现在在其自己的 `xwayland` 配方中。如果您需要在镜像中显示 Xwayland，则可能需要显式添加它。

- The `rpm` package no longer has `rpm-build` in its `RRECOMMENDS`; if by chance you still need rpm package building functionality in your image and you have not already done so then you should add `rpm-build` to your image explicitly.

> rpm 包不再在其 RRECOMMENDS 中包含 rpm-build；如果您仍需要在映像中使用 rpm 包构建功能，而您尚未添加 rpm-build，则应明确将 rpm-build 添加到映像中。

- The Python `statistics` standard module is now packaged in its own `python3-statistics` package instead of `python3-misc` as previously.

# Image / SDK generation changes

- Recursive dependencies on the `ref-tasks-build` task are now disabled when building SDKs. These are generally not needed; in the unlikely event that you do encounter problems then it will probably be as a result of missing explicit dependencies that need to be added.

> - 在构建 SDK 时，对 `ref-tasks-build` 任务的递归依赖现已禁用。这通常是不需要的；如果你遇到问题的情况极少发生，那么可能是由于缺少显式依赖项而导致的。

- Errors during \"complementary\" package installation (e.g. for `*-dbg` and `*-dev` packages) during image construction are no longer ignored. Historically some of these packages had installation problems, that is no longer the case. In the unlikely event that you see errors as a result, you will need to fix the installation/packaging issues.

> 在构建镜像时，不再忽略“补充”包安装(例如 `*-dbg` 和 `*-dev` 包)期间的错误。历史上，这些软件包中有安装问题，但现在不再是这种情况。如果您看到错误，您将需要修复安装/打包问题。

- When building an image, only packages that will be used in building the image (i.e. the first entry in `PACKAGE_CLASSES`) will be produced if multiple package types are enabled (which is not a typical configuration). If in your CI system you need to have the original behaviour, use `bitbake --runall build <target>`.

> 在构建镜像时，如果启用了多种包类型(这不是一个典型的配置)，只会生成用于构建镜像的包(即 `PACKAGE_CLASSES` 中的第一项)。如果您的 CI 系统需要恢复原始行为，请使用 `bitbake --runall build <target>`。

- The `-lic` package is no longer automatically added to `RRECOMMENDS`.

> - 当 `LICENSE_CREATE_PACKAGE` 被设置为“1”时，`-lic` 包将不再自动添加到 `RRECOMMENDS` 中。如果您希望在镜像中安装所有许可证包，则应将新的 `lic-pkgs` 功能添加到 `IMAGE_FEATURES` 中。

# Miscellaneous

- Certificates are now properly checked when BitBake fetches sources over HTTPS. If you receive errors as a result for your custom recipes, you will need to use a mirror or address the issue with the operators of the server in question.

> 现在，当 BitBake 通过 HTTPS 获取源时，证书已经得到正确检查。如果您收到自定义配方的错误，您将需要使用镜像或与服务器操作者解决问题。

- `avahi` has had its GTK+ support disabled by default. If you wish to re-enable it, set `AVAHI_GTK = "gtk3"` in a bbappend for the `avahi` recipe or in your custom distro configuration file.

> Avahi 默认情况下已经禁用其 GTK+ 支持。如果您希望重新启用它，请在 avahi 配方的 bbappend 中设置 AVAHI_GTK = "gtk3"，或在自定义发行版配置文件中设置。

- Setting the `BUILD_REPRODUCIBLE_BINARIES` variable to \"0\" no longer uses a strangely old fallback date of April 2011, it instead disables building reproducible binaries as you would logically expect.

> 设置 `BUILD_REPRODUCIBLE_BINARIES` 变量为"0"，不再使用一个奇怪的 2011 年 4 月的旧回退日期，而是按照您的逻辑期望禁用构建可重现的二进制文件。

- Setting noexec/nostamp/fakeroot varflags to any value besides \"1\" will now trigger a warning. These should be either set to \"1\" to enable, or not set at all to disable.

> 设置 noexec/nostamp/fakeroot varflags 为除“1”以外的任何值将会引起警告。这些应该设置为“1”来启用，或者不设置以禁用。

- The previously deprecated `COMPRESS_CMD` and `CVE_CHECK_CVE_WHITELIST` variables have been removed. Use `CONVERSION_CMD` in version 3.5) respectively instead.

> 以前弃用的 `COMPRESS_CMD` 和 `CVE_CHECK_CVE_WHITELIST` 变量已被删除。请改用 `CONVERSION_CMD` 和 `CVE_CHECK_WHITELIST`(在 3.5 版本中被 `CVE_CHECK_IGNORE` 取代)。

- The obsolete `oe_machinstall` function previously provided in the `ref-classes-utils` class has been removed. For machine-specific installation it is recommended that you use the built-in override support in the fetcher or overrides in general instead.

> `oe_machinstall` 功能以前提供在 `ref-classes-utils` 类中已被移除。建议您使用获取器中的内置覆盖支持或一般覆盖来进行特定机器的安装。

- The `-P` (`--clear-password`) option can no longer be used with `useradd` and `usermod` entries in `EXTRA_USERS_PARAMS`.

> - `EXTRA_USERS_PARAMS` 中的示例。
