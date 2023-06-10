---
tip: translate by openai@2023-06-07 22:06:00
...
---
title: Release 3.1 (dunfell)
---
This section provides migration information for moving to the Yocto Project 3.1 Release (codename \"dunfell\") from the prior release.

# Minimum system requirements {#migration-3.1-minimum-system-requirements}

The following versions / requirements of build host components have been updated:

- gcc 5.0
- python 3.5
- tar 1.28

- `rpcgen` is now required on the host (part of the `libc-dev-bin` package on Ubuntu, Debian and related distributions, and the `glibc` package on RPM-based distributions).

> `rpcgen`现在在主机上需要（Ubuntu、Debian及相关发行版的`libc-dev-bin`包的一部分，在基于RPM的发行版中是`glibc`包）。

Additionally, the `makeinfo` and `pod2man` tools are *no longer* required on the host.

# mpc8315e-rdb machine removed {#migration-3.1-mpc8315e-rdb-removed}


The MPC8315E-RDB machine is old/obsolete and unobtainable, thus given the maintenance burden the `mpc8315e-rdb` machine configuration that supported it has been removed in this release. The removal does leave a gap in official PowerPC reference hardware support; this may change in future if a suitable machine with accompanying support resources is found.

> MPC8315E-RDB机器已经过时且无法获取，因此在此版本中移除了支持它的MPC8315E-RDB机器配置。这样做确实给官方PowerPC参考硬件支持带来了一定的缺口；但如果能找到一台合适的机器以及相应的支持资源，这种情况可能会在未来发生变化。

# Python 2 removed {#migration-3.1-python-2-removed}


Due to the expiration of upstream support in January 2020, support for Python 2 has now been removed; it is recommended that you use Python 3 instead. If absolutely needed there is a meta-python2 community layer containing Python 2, related classes and various Python 2-based modules, however it should not be considered as supported.

> 由于2020年1月上游支持到期，Python 2的支持已被删除；建议您使用Python 3。如果绝对需要，有一个meta-python2社区层，包含Python 2、相关类和各种基于Python 2的模块，但不应被视为受支持。

# Reproducible builds now enabled by default {#migration-3.1-reproducible-builds}


In order to avoid unnecessary differences in output files (aiding binary reproducibility), the Poky distribution configuration (`DISTRO = "poky"`) now inherits the `reproducible_build` class by default.

> 为了避免输出文件中的不必要差异（有助于二进制可重现性），Poky发行版配置（'DISTRO = "poky"'）现在默认继承'reproducible_build'类。

# Impact of ptest feature is now more significant {#migration-3.1-ptest-feature-impact}


The Poky distribution configuration (`DISTRO = "poky"`) enables ptests by default to enable runtime testing of various components. In this release, a dependency needed to be added that has resulted in a significant increase in the number of components that will be built just when building a simple image such as core-image-minimal. If you do not need runtime tests enabled for core components, then it is recommended that you remove \"ptest\" from `DISTRO_FEATURES`{.interpreted-text role="term"} to save a significant amount of build time e.g. by adding the following in your configuration:

> Poky 发行配置（`DISTRO = "poky"`）默认启用了 ptests，以便对各种组件进行运行时测试。在此版本中，需要添加一个依赖关系，这导致在构建简单映像（例如core-image-minimal）时将构建的组件数量大大增加。如果您不需要为核心组件启用运行时测试，则建议您从`DISTRO_FEATURES`中删除“ptest”以节省大量构建时间，例如，通过在配置中添加以下内容：

```
DISTRO_FEATURES_remove = "ptest"
```

# Removed recipes {#migration-3.1-removed-recipes}

The following recipes have been removed:

- `chkconfig`: obsolete
- `console-tools`: obsolete
- `enchant`: replaced by `enchant2`
- `foomatic-filters`: obsolete
- `libidn`: no longer needed, moved to meta-oe
- `libmodulemd`: replaced by `libmodulemd-v1`
- `linux-yocto`: drop 4.19, 5.2 version recipes (5.4 now provided)
- `nspr`: no longer needed, moved to meta-oe
- `nss`: no longer needed, moved to meta-oe
- `python`: Python 2 removed (Python 3 preferred)
- `python-setuptools`: Python 2 version removed (python3-setuptools preferred)
- `sysprof`: no longer needed, moved to meta-oe
- `texi2html`: obsolete
- `u-boot-fw-utils`: functionally replaced by `libubootenv`

# features_check class replaces distro_features_check {#migration-3.1-features-check}


The `distro_features_check` class has had its functionality expanded, now supporting `ANY_OF_MACHINE_FEATURES`, `REQUIRED_MACHINE_FEATURES`, `CONFLICT_MACHINE_FEATURES`, `ANY_OF_COMBINED_FEATURES`, `REQUIRED_COMBINED_FEATURES`, `CONFLICT_COMBINED_FEATURES`. As a result the class has now been renamed to `features_check`; the `distro_features_check` class still exists but generates a warning and redirects to the new class. In preparation for a future removal of the old class it is recommended that you update recipes currently inheriting `distro_features_check` to inherit `ref-classes-features_check`{.interpreted-text role="ref"} instead.

> 类`distro_features_check`的功能已经扩展，现在支持`ANY_OF_MACHINE_FEATURES`、`REQUIRED_MACHINE_FEATURES`、`CONFLICT_MACHINE_FEATURES`、`ANY_OF_COMBINED_FEATURES`、`REQUIRED_COMBINED_FEATURES`和`CONFLICT_COMBINED_FEATURES`。因此，该类现在被重命名为`features_check`；`distro_features_check`类仍然存在，但会产生警告并重定向到新类。为了准备未来删除旧类，建议您更新当前继承`distro_features_check`的配方，改为继承`ref-classes-features_check`{.interpreted-text role="ref"}。

# Removed classes {#migration-3.1-removed-classes}

The following classes have been removed:

- `distutils-base`: moved to meta-python2
- `distutils`: moved to meta-python2
- `libc-common`: merged into the glibc recipe as nothing else used it.
- `python-dir`: moved to meta-python2
- `pythonnative`: moved to meta-python2
- `setuptools`: moved to meta-python2
- `tinderclient`: dropped as it was obsolete.

# SRC_URI checksum behaviour {#migration-3.1-src-uri-checksums}


Previously, recipes by tradition included both SHA256 and MD5 checksums for remotely fetched files in `SRC_URI`{.interpreted-text role="term"}, even though only one is actually mandated. However, the MD5 checksum does not add much given its inherent weakness; thus when a checksum fails only the SHA256 sum will now be printed. The md5sum will still be verified if it is specified.

> 以前，根据传统，`SRC_URI`{.interpreted-text role="term"}中远程获取的文件都会包含SHA256和MD5校验和，尽管只有一个是必需的。但是，考虑到MD5校验和本身的弱点，它几乎没有什么作用；因此，如果校验和失败，现在只会打印SHA256和。如果指定了MD5校验和，仍然会进行验证。

# npm fetcher changes {#migration-3.1-npm}


The npm fetcher has been completely reworked in this release. The npm fetcher now only fetches the package source itself and no longer the dependencies; there is now also an npmsw fetcher which explicitly fetches the shrinkwrap file and the dependencies. This removes the slightly awkward `NPM_LOCKDOWN` and `NPM_SHRINKWRAP` variables which pointed to local files; the lockdown file is no longer needed at all. Additionally, the package name in `npm://` entries in `SRC_URI`{.interpreted-text role="term"} is now specified using a `package` parameter instead of the earlier `name` which overlapped with the generic `name` parameter. All recipes using the npm fetcher will need to be changed as a result.

> 在此版本中，npm获取器已经完全重新设计。npm获取器现在只获取软件包源本身，不再获取依赖项；现在还有一个npmsw获取器，可以明确获取收缩包文件和依赖项。这样就不再需要指向本地文件的稍微尴尬的`NPM_LOCKDOWN`和`NPM_SHRINKWRAP`变量了；Lockdown文件完全不再需要了。此外，`SRC_URI`中的`npm：//`条目中的软件包名称现在使用`package`参数指定，而不是先前的`name`，它与通用的`name`参数重叠。因此，所有使用npm获取器的配方都需要进行更改。

An example of the new scheme:

```
SRC_URI = "npm://registry.npmjs.org;package=array-flatten;version=1.1.1 \
           npmsw://${THISDIR}/npm-shrinkwrap.json"
```

Another example where the sources are fetched from git rather than an npm repository:

```
SRC_URI = "git://github.com/foo/bar.git;protocol=https \
           npmsw://${THISDIR}/npm-shrinkwrap.json"
```


devtool and recipetool have also been updated to match with the npm fetcher changes. Other than producing working and more complete recipes for npm sources, there is also a minor change to the command line for devtool: the `--fetch-dev` option has been renamed to `--npm-dev` as it is npm-specific.

> devtool 和 recipetool 已经更新以匹配 npm 抓取器的更改。除了为 npm 源生成可正常使用且更完整的菜谱之外，devtool 的命令行也有一个小变化： `--fetch-dev` 选项已经更名为 `--npm-dev`，因为它是 npm 专用的。

# Packaging changes {#migration-3.1-packaging-changes}


- `intltool` has been removed from `packagegroup-core-sdk` as it is rarely needed to build modern software \-\-- gettext can do most of the things it used to be needed for. `intltool` has also been removed from `packagegroup-core-self-hosted` as it is not needed to for standard builds.

> intltool已从packagegroup-core-sdk中移除，因为它很少需要用来构建现代软件--gettext可以完成它曾经需要做的大部分工作。intltool也已从packagegroup-core-self-hosted中移除，因为它不需要用于标准构建。

- git: `git-am`, `git-difftool`, `git-submodule`, and `git-request-pull` are no longer perl-based, so are now installed with the main `git` package instead of within `git-perltools`.

> -git：`git-am`、`git-difftool`、`git-submodule`和`git-request-pull`不再是基于Perl的，因此现在与主`git`包一起安装，而不是在`git-perltools`中安装。

- The `ldconfig` binary built as part of glibc has now been moved to its own `ldconfig` package (note no `glibc-` prefix). This package is in the `RRECOMMENDS`{.interpreted-text role="term"} of the main `glibc` package if `ldconfig` is present in `DISTRO_FEATURES`{.interpreted-text role="term"}.

> ldconfig 二进制文件作为glibc的一部分现在已经移动到自己的ldconfig软件包（注意没有glibc-前缀）。如果DISTRO_FEATURES中存在ldconfig，则该软件包位于主glibc软件包的RRECOMMENDS中。

- `libevent` now splits each shared library into its own package (as Debian does). Since these are shared libraries and will be pulled in through the normal shared library dependency handling, there should be no impact to existing configurations other than less unnecessary libraries being installed in some cases.

> libevent现在将每个共享库拆分成自己的软件包（就像Debian一样）。由于这些是共享库，将通过正常的共享库依赖处理来拉取，因此在某些情况下应该不会对现有配置产生影响，只是会安装更少的不必要的库。

- linux-firmware now has a new package for `bcm4366c` and includes available NVRAM config files into the `bcm43340`, `bcm43362`, `bcm43430` and `bcm4356-pcie` packages.

> Linux-firmware现在有一个新的包用于“bcm4366c”，并将可用的NVRAM配置文件包含在“bcm43340”、“bcm43362”、“bcm43430”和“bcm4356-pcie”包中。

- `harfbuzz` now splits the new `libharfbuzz-subset.so` library into its own package to reduce the main package size in cases where `libharfbuzz-subset.so` is not needed.

> 现在，harfbuzz将新的libharfbuzz-subset.so库拆分成自己的软件包，以减少主软件包的大小，以防libharfbuzz-subset.so不需要时。

# Additional warnings {#migration-3.1-package-qa-warnings}

Warnings will now be shown at `ref-tasks-package_qa`{.interpreted-text role="ref"} time in the following circumstances:


- A recipe installs `.desktop` files containing `MimeType` keys but does not inherit the new `ref-classes-mime-xdg`{.interpreted-text role="ref"} class

> 一个食谱安装包含MimeType键的`.desktop`文件，但不继承新的ref-classes-mime-xdg类。
- A recipe installs `.xml` files into `${datadir}/mime/packages` but does not inherit the `ref-classes-mime`{.interpreted-text role="ref"} class

# `wic` image type now used instead of `live` by default for x86 {#migration-3.1-x86-live-wic}


`conf/machine/include/x86-base.inc` (inherited by most x86 machine configurations) now specifies `wic` instead of `live` by default in `IMAGE_FSTYPES`{.interpreted-text role="term"}. The `live` image type will likely be removed in a future release so it is recommended that you use `wic` instead.

> 在大多数x86机器配置中继承的`conf/machine/include/x86-base.inc`中，`IMAGE_FSTYPES`默认指定`wic`而不是`live`。在未来的发行版中很可能会移除`live`映像类型，因此建议您使用`wic`。

# Miscellaneous changes {#migration-3.1-misc}


- The undocumented `SRC_DISTRIBUTE_LICENSES` variable has now been removed in favour of a new `AVAILABLE_LICENSES` variable which is dynamically set based upon license files found in `${COMMON_LICENSE_DIR}` and `${LICENSE_PATH}`.

> 未经记录的`SRC_DISTRIBUTE_LICENSES`变量已经被取消，取而代之的是一个新的`AVAILABLE_LICENSES`变量，该变量是根据`${COMMON_LICENSE_DIR}`和`${LICENSE_PATH}`中发现的许可证文件动态设置的。
- The tune definition for big-endian microblaze machines is now `microblaze` instead of `microblazeeb`.

- `newlib` no longer has built-in syscalls. `libgloss` should then provide the syscalls, `crt0.o` and other functions that are no longer part of `newlib` itself. If you are using `TCLIBC = "newlib"` this now means that you must link applications with both `newlib` and `libgloss`, whereas before `newlib` would run in many configurations by itself.

> 新的`newlib`不再包含内置的系统调用。`libgloss`应该提供系统调用、`crt0.o`和其他不再是`newlib`本身一部分的功能。如果你使用`TCLIBC="newlib"`，这意味着你必须将应用程序链接到`newlib`和`libgloss`，而以前`newlib`可以自己在许多配置中运行。
