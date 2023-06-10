---
tip: translate by openai@2023-06-10 11:12:27
...
---
subtitle: Migration notes for 4.0 (kirkstone)
title: Release 4.0 (kirkstone)
------------------------------

This section provides migration information for moving to the Yocto Project 4.0 Release (codename \"kirkstone\") from the prior release.

# Inclusive language improvements

To use more [inclusive language](https://inclusivenaming.org/) in the code and documentation, some variables have been renamed, and some have been deleted where they are no longer needed. In many cases the new names are also easier to understand. BitBake will stop with an error when renamed or removed variables still exist in your recipes or configuration.

> 为了在代码和文档中使用更具包容性的语言，一些变量已重命名，一些不再需要的变量已被删除。在许多情况下，新名称也更容易理解。当重命名或删除的变量仍存在于您的配方或配置中时，BitBake 将停止并出现错误。

Please note that the change applies also to environmental variables, so make sure you use a fresh environment for your build.

The following variables have changed their names:

- `BB_ENV_WHITELIST` became `BB_ENV_PASSTHROUGH`
- `BB_ENV_EXTRAWHITE` became `BB_ENV_PASSTHROUGH_ADDITIONS`
- `BB_HASHBASE_WHITELIST` became `BB_BASEHASH_IGNORE_VARS`
- `BB_HASHCONFIG_WHITELIST` became `BB_HASHCONFIG_IGNORE_VARS`
- `BB_HASHTASK_WHITELIST` became `BB_TASKHASH_IGNORE_TASKS`
- `BB_SETSCENE_ENFORCE_WHITELIST` became `BB_SETSCENE_ENFORCE_IGNORE_TASKS`
- `CVE_CHECK_PN_WHITELIST` became `CVE_CHECK_SKIP_RECIPE`
- `CVE_CHECK_WHITELIST` became `CVE_CHECK_IGNORE`
- `ICECC_USER_CLASS_BL` became `ICECC_CLASS_DISABLE`
- `ICECC_SYSTEM_CLASS_BL` became `ICECC_CLASS_DISABLE`
- `ICECC_USER_PACKAGE_WL` became `ICECC_RECIPE_ENABLE`
- `ICECC_USER_PACKAGE_BL` became `ICECC_RECIPE_DISABLE`
- `ICECC_SYSTEM_PACKAGE_BL` became `ICECC_RECIPE_DISABLE`
- `LICENSE_FLAGS_WHITELIST` became `LICENSE_FLAGS_ACCEPTED`
- `MULTI_PROVIDER_WHITELIST` became `BB_MULTI_PROVIDER_ALLOWED`
- `PNBLACKLIST` became `SKIP_RECIPE`
- `SDK_LOCAL_CONF_BLACKLIST` became `ESDK_LOCALCONF_REMOVE`
- `SDK_LOCAL_CONF_WHITELIST` became `ESDK_LOCALCONF_ALLOW`
- `SDK_INHERIT_BLACKLIST` became `ESDK_CLASS_INHERIT_DISABLE`
- `SSTATE_DUPWHITELIST` became `SSTATE_ALLOW_OVERLAP_FILES`
- `SYSROOT_DIRS_BLACKLIST` became `SYSROOT_DIRS_IGNORE`
- `UNKNOWN_CONFIGURE_WHITELIST` became `UNKNOWN_CONFIGURE_OPT_IGNORE`
- `WHITELIST_<license>` became `INCOMPATIBLE_LICENSE_EXCEPTIONS`

In addition, `BB_STAMP_WHITELIST`, `BB_STAMP_POLICY`, `INHERIT_BLACKLIST`, `TUNEABI`, `TUNEABI_WHITELIST`, and `TUNEABI_OVERRIDE` have been removed.

Many internal variable names have been also renamed accordingly.

In addition, in the `cve-check` output, the CVE issue status `Whitelisted` has been renamed to `Ignored`.

The `BB_DISKMON_DIRS` variable value now uses the term `HALT` instead of `ABORT`.

A :oe_[git:%60convert-variable-renames.py](git:%60convert-variable-renames.py) \</openembedded-core/tree/scripts/contrib/convert-variable-renames.py\>\` script is provided to convert your recipes and configuration, and also warns you about the use of problematic words. The script performs changes and you need to review them before committing. An example warning looks like:

> A:提供了一个 `oe_[git:%60convert-variable-renames.py](git:%60convert-variable-renames.py)</openembedded-core/tree/scripts/contrib/convert-variable-renames.py>` 脚本用于转换你的配方和配置，并警告你关于使用有问题的词。该脚本会执行更改，你需要在提交前检查它们。一个警告的示例如下：

```
poky/scripts/lib/devtool/upgrade.py needs further work at line 275 since it contains abort
```

# Fetching changes

- Because of the uncertainty in future default branch names in git repositories, it is now required to add a branch name to all URLs described by `git://` and `gitsm://` `SRC_URI` entries. For example:

> 由于未来 git 仓库中默认分支名的不确定性，现在需要在由 `git://` 和 `gitsm://` `SRC_URI` 描述的所有 URL 中添加分支名。例如：

```
SRC_URI = "git://git.denx.de/u-boot.git;branch=master"
```

A :oe_[git:%60convert-srcuri](git:%60convert-srcuri) \</openembedded-core/tree/scripts/contrib/convert-srcuri.py\>[ script to convert your recipes is available in :term:\`OpenEmbedded-Core (OE-Core)].

> A: 你可以在 OpenEmbedded-Core (OE-Core)和 Poky 中找到脚本(git:%60convert-srcuri)来转换你的配方。

- Because of [GitHub dropping support for the git: protocol](https://github.blog/2021-09-01-improving-git-protocol-security-github/), recipes now need to use `;protocol=https` at the end of GitHub URLs. The same `convert-srcuri` script mentioned above can be used to convert your recipes.

> 由于 GitHub 取消对 git:协议的支持(https://github.blog/2021-09-01-improving-git-protocol-security-github/)，recipes 现在需要在 GitHub URL 末尾添加“;protocol=https”。上述同一个“convert-srcuri”脚本可以用来转换您的 recipes。

- Network access from tasks is now disabled by default on kernels which support this feature (on most recent distros such as CentOS 8 and Debian 11 onwards). This means that tasks accessing the network need to be marked as such with the `network` flag. For example:

> 任务的网络访问现在默认情况下已在支持此功能的内核上禁用(在最新的发行版中，例如 CentOS 8 和 Debian 11 及以后版本)。这意味着访问网络的任务需要使用 `network` 标志进行标记。例如：

```
do_mytask[network] = "1"
```

This is allowed by default from `ref-tasks-fetch` as it usually undermines fetcher source mirroring, image and licence manifests, software auditing and supply chain security.

> 这是默认允许的 `ref-tasks-fetch`，但不是我们的其他标准任务。recipes 不应该外部访问网络，因为它通常会破坏获取源镜像、镜像和许可证清单、软件审计和供应链安全性。

# License changes

- The ambiguous \"BSD\" license has been removed from the `common-licenses` directory. Each recipe that fetches or builds BSD-licensed code should specify the proper version of the BSD license in its `LICENSE` value.

> "BSD" 授权已从 `common-licenses` 目录中移除。每个获取或构建 BSD 许可代码的配方都应在其 `LICENSE` 值中指定正确版本的 BSD 许可。

- `LICENSE` variable values should now use [SPDX identifiers](https://spdx.org/licenses/). If they do not, by default a warning will be shown. A :oe_[git:%60convert-spdx-licenses.py](git:%60convert-spdx-licenses.py) \</openembedded-core/tree/scripts/contrib/convert-spdx-licenses.py\>\` script can be used to update your recipes.

> `LICENSE` 变量值现在应该使用 [SPDX 许可证标识符](https://spdx.org/licenses/)。如果没有使用，默认会显示警告。可以使用 :oe_[git:%60convert-spdx-licenses.py](git:%60convert-spdx-licenses.py) \</openembedded-core/tree/scripts/contrib/convert-spdx-licenses.py\>\` 脚本来更新您的配方。

- `INCOMPATIBLE_LICENSE` documentation for further information.

> `INCOMPATIBLE_LICENSE` 文档。

- The `AVAILABLE_LICENSES` variable has been removed. This variable was a performance liability and is highly dependent on which layers are added to the configuration, which can cause signature issues for users. In addition the `available_licenses()` function has been removed from the `ref-classes-license` class as it is no longer needed.

> `AVAILABLE_LICENSES` 变量已被移除。此变量对性能有负面影响，并且高度依赖于添加到配置中的层，这可能会导致用户的签名问题。此外，`available_licenses()` 函数已从 `ref-classes-license` 类中移除，因为它不再需要。

# Removed recipes

The following recipes have been removed in this release:

- `dbus-test`: merged into main dbus recipe
- `libid3tag`: moved to meta-oe - no longer needed by anything in OE-Core
- `libportal`: moved to meta-gnome - no longer needed by anything in OE-Core
- `linux-yocto`: removed version 5.14 recipes (5.15 and 5.10 still provided)
- `python3-nose`: has not changed since 2016 upstream, and no longer needed by anything in OE-Core
- `rustfmt`: not especially useful as a standalone recipe

# Python changes

- `distutils` has been deprecated upstream in Python 3.10 and thus the `distutils*` classes have been moved to `meta-python`. Recipes that inherit the `distutils*` classes should be updated to inherit `setuptools*` equivalents instead.

> - `distutils` 已经在 Python 3.10 中被上游弃用，因此 `distutils*` 类已经被移动到 `meta-python` 中。继承 `distutils*` 类的配方应该更新为继承 `setuptools*` 的等效物。

- The Python package build process is now based on [wheels](https://pythonwheels.com/). Here are the new Python packaging classes that should be used: `ref-classes-python_flit_core`.

> Python 包构建过程现在基于 [wheels](https://pythonwheels.com/)。应该使用以下新的 Python 包装类：`ref-classes-python_flit_core`。

- The `ref-classes-setuptools3` should be used instead.

> - `ref-classes-setuptools3` 。

# Prelink removed

Prelink has been dropped by `glibc` upstream in 2.36. It already caused issues with binary corruption, has a number of open bugs and is of questionable benefit without disabling load address randomization and PIE executables.

> glibc 在 2.36 中取消了 Prelink，它已经导致了二进制损坏的问题，有许多未解决的错误，而且如果不禁用加载地址随机化和 PIE 可执行文件，它的好处也值得怀疑。

We disabled prelinking by default in the honister (3.4) release, but left it able to be enabled if desired. However, without glibc support it cannot be maintained any further, so all of the prelinking functionality has been removed in this release. If you were enabling the `image-prelink` class in `INHERIT` etc in your configuration, then you will need to remove the reference(s).

> 在 Honister(3.4)版本中，我们默认禁用了预链接，但也可以根据需要启用它。但是，由于没有 Glibc 支持，无法继续维护，因此在此版本中已经删除了所有预链接功能。如果您在配置中在 `INHERIT` 等中启用了 `image-prelink` 类，则您将需要删除引用。

# Reproducible as standard

Reproducibility is now considered as standard functionality, thus the `reproducible` class has been removed and its previous contents merged into the `ref-classes-base` etc. then they should be removed.

> 可重現性現在被視為標準功能，因此 `reproducible` 類別已被移除，其先前的內容已合併到 `ref-classes-base` 類別中。如果您的配置中有對 `INHERIT`、`USER_CLASSES` 等的 `reproducible` 的參考，則應刪除它們。

Additionally, the `BUILD_REPRODUCIBLE_BINARIES` variable is no longer used. Specifically for the kernel, if you wish to enable build timestamping functionality that is normally disabled for reproducibility reasons, you can do so by setting a new `KERNEL_DEBUG_TIMESTAMPS` variable to \"1\".

> 此外，不再使用 `BUILD_REPRODUCIBLE_BINARIES` 变量。具体来说，如果您希望启用通常因可重现性原因而被禁用的构建时间戳功能，可以通过将新的 `KERNEL_DEBUG_TIMESTAMPS` 变量设置为“1”来实现。

# Supported host distribution changes

- Support for `AlmaLinux <AlmaLinux>`. The following distribution versions were dropped: CentOS 8, Ubuntu 16.04 and Fedora 30, 31 and 32.

> 支持替换 CentOS 的 AlmaLinux 主机。以下发行版本已被取消：CentOS 8，Ubuntu 16.04 和 Fedora 30、31 和 32。

- `gcc` version 7.5 is now required at minimum on the build host. For older host distributions where this is not available, you can use the `buildtools-extended` tarball (easily installable using `scripts/install-buildtools`).

> 在构建主机上至少需要 `GCC` 版本 7.5。对于不可用的旧主机发行版，您可以使用 `buildtools-extended` tarball(使用 `scripts/install-buildtools` 可轻松安装)。

# :append/:prepend in combination with other operators

The `append`, `prepend` and `remove` operators can now only be combined with `=` and `:=` operators. To the exception of the `append` plus `+=` and `prepend` plus `=+` combinations, all combinations could be factored up to the `append`, `prepend` or `remove` in the combination. This brought a lot of confusion on how the override style syntax operators work and should be used. Therefore, those combinations should be replaced by a single `append`, `prepend` or `remove` operator without any additional change. For the `append` plus `+=` (and `prepend` plus `=+`) combinations, the content should be prefixed (respectively suffixed) by a space to maintain the same behavior. You can learn more about override style syntax operators (`append`, `prepend` and `remove`) in the BitBake documentation: `bitbake-user-manual/bitbake-user-manual-metadata:appending and prepending (override style syntax)`.

> 操作符 `append`、`prepend` 和 `remove` 现在只能与 `=` 和 `:=` 操作符组合使用。除了 `append` 加 `+=` 和 `prepend` 加 `=+` 的组合外，所有组合都可以简化为 `append`、`prepend` 或 `remove` 组合。这给覆盖式语法操作符的工作方式和使用方式带来了很多混乱。因此，这些组合应该用单个 `append`、`prepend` 或 `remove` 操作符替换，而不需要任何额外的更改。对于 `append` 加 `+=`(和 `prepend` 加 `=+`)的组合，应该在内容前面(或后面)加上一个空格，以保持相同的行为。您可以在 BitBake 文档中了解有关覆盖式语法操作符(`append`、`prepend` 和 `remove`)的更多信息：`bitbake-user-manual/bitbake-user-manual-metadata:appending and prepending (override style syntax)`。

# Miscellaneous changes

- `blacklist.bbclass` is removed and the functionality moved to the `ref-classes-base` function. The usage remains the same, for example:

> `blacklist.bbclass` 已被移除，其功能已移至 `ref-classes-base` 类，并使用更具描述性的 `varflag` 变量名 `SKIP_RECIPE`，该变量将使用 [bb.parse.SkipRecipe()] 函数。使用方式保持不变，例如：

```
SKIP_RECIPE[my-recipe] = "Reason for skipping recipe"
```

- `ref-classes-allarch`.

> `ref-classes-allarch`。

- The `lnr` script has been removed. `lnr` implemented the same behaviour as [ln \--relative \--symbolic] if you prefer the short version.

> 脚本 `lnr` 已经被移除。`lnr` 实现了与 [ln \--relative \--symbolic](%E5%A6%82%E6%9E%9C%E6%82%A8%E5%96%9C%E6%AC%A2%E7%AE%80%E7%9F%AD%E7%89%88%E6%9C%AC)来替换它们。

- The `package_qa_handle_error()` function formerly in the `ref-classes-insane` class has been moved and renamed - if you have any references in your own custom classes they should be changed to `oe.qa.handle_error()`.

> `package_qa_handle_error()` 函数以前在 `ref-classes-insane` 类中，已经被移动并重新命名 - 如果您在自己的自定义类中有任何引用，应该将它们更改为 `oe.qa.handle_error()`。

- When building `perl`, Berkeley db support is no longer enabled by default, since Berkeley db is largely obsolete. If you wish to reenable it, you can append `bdb` to `PACKAGECONFIG` in a `perl` bbappend or `PACKAGECONFIG:pn-perl` at the configuration level.

> 当构建“Perl”时，Berkeley db 支持不再默认启用，因为 Berkeley db 大部分已经过时了。如果您想重新启用它，可以在“perl” bbappend 中将“bdb”附加到“PACKAGECONFIG”，或者在配置级别上将“PACKAGECONFIG:pn-perl”附加到“PACKAGECONFIG”。

- For the `xserver-xorg` recipe, the `xshmfence`, `xmlto` and `systemd` options previously supported in `PACKAGECONFIG` have been removed, as they are no longer supported since the move from building it with autotools to meson in this release.

> 对于 `xserver-xorg` 配方，以前在 `PACKAGECONFIG` 中支持的 `xshmfence`、`xmlto` 和 `systemd` 选项已被删除，因为自本次发布从自动工具构建改为使用 meson 后，它们不再受支持。

- For the `libsdl2` recipe, various X11 features are now disabled by default (primarily for reproducibility purposes in the native case) with options in `EXTRA_OECMAKE` within the recipe. These can be changed within a bbappend if desired. See the `libsdl2` recipe for more details.

> 对于 `libsdl2` recipes，现在默认情况下启用了各种 X11 功能(主要是为了在本地情况下可重现性)，可以在 recipes 中的 `EXTRA_OECMAKE` 中进行更改。如果需要，可以在 bbappend 中进行更改。有关更多详细信息，请参阅 `libsdl2` recipes。

- The `cortexa72-crc` and `cortexa72-crc-crypto` tunes have been removed since the crc extension is now enabled by default for cortexa72. Replace any references to these with `cortexa72` and `cortexa72-crypto` respectively.

> `cortexa72-crc` 和 `cortexa72-crc-crypto` 调整已被删除，因为 crc 扩展现在默认对 cortexa72 启用。将这些引用替换为 `cortexa72` 和 `cortexa72-crypto` 分别。

- The Python development shell (previously known as `devpyshell`) feature has been renamed to `pydevshell`. To start it you should now run:

  ```
  bitbake <target> -c pydevshell
  ```
- The `packagegroups-core-full-cmdline-libs` packagegroup is no longer produced, as libraries should normally be brought in via dependencies. If you have any references to this then remove them.

> `packagegroups-core-full-cmdline-libs` 包组不再生产，因为库通常应通过依赖关系进行引入。如果您有任何对此的引用，请将其删除。

- The `TOPDIR` variable and the current working directory are no longer modified when parsing recipes. Any code depending on the previous behaviour will no longer work - change any such code to explicitly use appropriate path variables instead.

> - `TOPDIR` 变量和当前工作目录在解析配方时不再被修改。任何依赖于以前行为的代码将不再有效 - 请改变任何这样的代码，以明确使用适当的路径变量。

- In order to exclude the kernel image from the image rootfs, `RRECOMMENDS`-base`.

> 为了排除镜像 rootfs 中的内核镜像，应该设置 `RRECOMMENDS`-base`。
