---
tip: translate by openai@2023-06-07 22:14:34
...
---
title: Release 3.3 (hardknott)
------------------------------

This section provides migration information for moving to the Yocto Project 3.3 Release (codename \"hardknott\") from the prior release.

# Minimum system requirements

You will now need at least Python 3.6 installed on your build host. Most recent distributions provide this, but should you be building on a distribution that does not have it, you can use the `buildtools` for details.

> 你现在需要在构建主机上安装至少 Python 3.6。最新的发行版提供了这一点，但如果你在一个没有它的发行版上构建，你可以使用 `buildtools` tarball(可以使用 `scripts/install-buildtools` 轻松安装)- 详情请参见 `ref-manual/system-requirements:required git, tar, python, make and gcc versions`。

# Removed recipes

The following recipes have been removed:

- `go-dep`: obsolete with the advent of go modules
- `gst-validate`: replaced by `gst-devtools`
- `linux-yocto`: removed 5.8 version recipes (5.4 / 5.10 still provided)
- `vulkan-demos`: replaced by `vulkan-samples`

# Single version common license file naming

Some license files in `meta/files/common-licenses` have been renamed to match current SPDX naming conventions:

- AGPL-3.0 -\> AGPL-3.0-only
- GPL-1.0 -\> GPL-1.0-only
- GPL-2.0 -\> GPL-2.0-only
- GPL-3.0 -\> GPL-3.0-only
- LGPL-2.0 -\> LGPL-2.0-only
- LGPL-2.1 -\> LGPL-2.1-only
- LGPL-3.0 -\> LGPL-3.0-only

Additionally, corresponding \"-or-later\" suffixed files have been added e.g. `GPL-2.0-or-later`.

It is not required that you change `LICENSE` to point to file(s) in `meta/files/common-licenses` (which in any case is not recommended) you will need to update those.

> 不需要您更改 `LICENSE` 指向 `meta/files/common-licenses` 中的文件(这也不建议)的配方，您将需要更新它们。

# New `python3targetconfig` class

A new `ref-classes-python3targetconfig`. This also adds a dependency on target `python3`, so it should only be used where appropriate in order to avoid unnecessarily lengthening builds.

> 一个新的 `ref-classes-python3targetconfig`。这也增加了对目标 `python3` 的依赖性，因此只有在适当的情况下才应使用，以避免不必要地延长构建时间。

Some example recipes where this change has been made: `gpgme`, `libcap-ng`, `python3-pycairo`.

# `setup.py` path for Python modules

In a Python module, sometimes `setup.py` can be buried deep in the source tree. Previously this was handled in recipes by setting `S``, for example:

> 在 Python 模块中，有时 `setup.py` 可能深埋在源树中。以前，这在 recipes 中通过将 `S` 设置为指向源中 `setup.py` 所在的子目录来处理。但是随着最近的伪 <overview-manual/concepts:fakeroot and pseudo> 的变化，一些 Python 模块会对 `$` 下的文件做出更改，例如：

```
S = "$/git/python/pythonmodule"
```

then in `setup.py` it works with source code in a relative fashion, such as `../../src`. This causes pseudo to fail as it isn\'t able to track the paths properly. This release introduces a new `DISTUTILS_SETUP_PATH` variable so that recipes can specify it explicitly, for example:

> 然后在 `setup.py` 中，它使用相对路径的源代码，例如 `../../src`。这使得 Pseudo 无法正确跟踪路径。本发行版引入了一个新的 `DISTUTILS_SETUP_PATH` 变量，以便配方可以明确指定它，例如：

```
S = "$/git"
DISTUTILS_SETUP_PATH = "$/python/pythonmodule"
```

Recipes that inherit from `distutils3` (or `ref-classes-setuptools3` to point to a Python module within a subdirectory in the aforementioned manner should be changed to set `DISTUTILS_SETUP_PATH` instead.

> 使用 `distutils3`(或者 `ref-classes-setuptools3` 指向上述目录中的 Python 模块，应该改为设置 `DISTUTILS_SETUP_PATH`。

# BitBake changes

- BitBake is now configured to use a default `umask` of `022` for all tasks (specified via a new `BB_DEFAULT_UMASK` variable). If needed, `umask` can still be set on a per-task basis via the `umask` varflag on the task function, but that is unlikely to be necessary in most cases.

> BitBake 现在被配置为使用默认的 `umask`(通过新的 `BB_DEFAULT_UMASK` 变量指定)为所有任务设置 022。如果需要，仍然可以通过任务函数上的 `umask` varflag 为每个任务设置 `umask`，但在大多数情况下不太可能需要。

- If a version specified in `PREFERRED_VERSION` is not available this will now trigger a warning instead of just a note, making such issues more visible.

> 如果 `PREFERRED_VERSION` 中指定的版本不可用，现在将触发警告而不仅仅是注意，使这些问题更加明显。

# Packaging changes

The following packaging changes have been made; in all cases the main package still depends upon the split out packages so you should not need to do anything unless you want to take advantage of the improved granularity:

> 以下的包装更改已经完成；在所有情况下，主包仍然依赖于分离出的包，因此除非您想利用改进的细粒度，否则您不需要做任何事情。

- `dbus`: `-common` and `-tools` split out
- `iproute2`: split `ip` binary to its own package
- `net-tools`: split `mii-tool` into its own package
- `procps`: split `ps` and `sysctl` into their own packages
- `rpm`: split build and extra functionality into separate packages
- `sudo`: split `sudo` binary into `sudo-sudo` and libs into `sudo-lib`
- `systemtap`: examples, Python scripts and runtime material split out
- `util-linux`: `libuuid` has been split out to its own `util-linux-libuuid` recipe (and corresponding packages) to avoid circular dependencies if `libgcrypt` support is enabled in `util-linux`. (`util-linux` depends upon `util-linux-libuuid`.)

> - `util-linux`：如果在 `util-linux` 中启用了 `libgcrypt` 支持，`libuuid` 就会被拆分到其自己的 `util-linux-libuuid` 配方(和相应的软件包)中，以避免出现循环依赖。(`util-linux` 依赖于 `util-linux-libuuid`。)

# Miscellaneous changes

- The default poky `DISTRO_VERSION` value as desired (particularly if you create your own custom distro configuration).

> 默认的 poky `DISTRO_VERSION` 值(特别是如果您创建自己的自定义发行版配置时)。

- `adwaita-icon-theme` version 3.34.3 has been added back, and is selected as the default via `PREFERRED_VERSION` in `meta/conf/distro/include/default-versions.inc` due to newer versions not working well with `librsvg` 2.40. `librsvg` is not practically upgradeable at the moment as it has been ported to Rust, and Rust is not (yet) in OE-Core, but this will change in a future release.

> `Adwaita-icon-theme` 版本 3.34.3 已被添加回来，并通过 `meta/conf/distro/include/default-versions.inc` 中的 `PREFERRED_VERSION` 被选择为默认值，因为较新版本与 `librsvg` 2.40 不能很好地工作。由于 `librsvg` 已经被移植到 Rust，而 Rust 尚未(尚未)进入 OE-Core，因此目前无法实际升级 `librsvg`，但在未来的发行版中会有所改变。

- `ffmpeg` is now configured to disable GPL-licensed portions by default to make it harder to accidentally violate the GPL. To explicitly enable GPL licensed portions, add `gpl` to `PACKAGECONFIG` for `ffmpeg` using a bbappend (or use `PACKAGECONFIG_append_pn-ffmpeg = " gpl"` in your configuration.)

> `ffmpeg` 现在默认禁用 GPL 许可部分，以防止意外违反 GPL。要明确启用 GPL 许可部分，请使用 bbappend(或在您的配置中使用 `PACKAGECONFIG_append_pn-ffmpeg = " gpl"`)将 `gpl` 添加到 `PACKAGECONFIG` 中的 `ffmpeg`。

- `connman` is now set to conflict with `systemd-networkd` as they overlap functionally and may interfere with each other at runtime.
- Canonical SPDX license names are now used in image license manifests in order to avoid aliases of the same license from showing up together (e.g. `GPLv2` and `GPL-2.0`)

> 使用规范的 SPDX 许可证名称现在用于镜像许可证清单，以避免同一许可证的别名同时出现(例如 `GPLv2` 和 `GPL-2.0`)
