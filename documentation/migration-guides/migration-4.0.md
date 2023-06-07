---
subtitle: Migration notes for 4.0 (kirkstone)
title: Release 4.0 (kirkstone)
---
This section provides migration information for moving to the Yocto Project 4.0 Release (codename \"kirkstone\") from the prior release.

# Inclusive language improvements {#migration-4.0-inclusive-language}

To use more [inclusive language](https://inclusivenaming.org/) in the code and documentation, some variables have been renamed, and some have been deleted where they are no longer needed. In many cases the new names are also easier to understand. BitBake will stop with an error when renamed or removed variables still exist in your recipes or configuration.

Please note that the change applies also to environmental variables, so make sure you use a fresh environment for your build.

The following variables have changed their names:

- `BB_ENV_WHITELIST` became `BB_ENV_PASSTHROUGH`{.interpreted-text role="term"}
- `BB_ENV_EXTRAWHITE` became `BB_ENV_PASSTHROUGH_ADDITIONS`{.interpreted-text role="term"}
- `BB_HASHBASE_WHITELIST` became `BB_BASEHASH_IGNORE_VARS`{.interpreted-text role="term"}
- `BB_HASHCONFIG_WHITELIST` became `BB_HASHCONFIG_IGNORE_VARS`{.interpreted-text role="term"}
- `BB_HASHTASK_WHITELIST` became `BB_TASKHASH_IGNORE_TASKS`
- `BB_SETSCENE_ENFORCE_WHITELIST` became `BB_SETSCENE_ENFORCE_IGNORE_TASKS`
- `CVE_CHECK_PN_WHITELIST` became `CVE_CHECK_SKIP_RECIPE`{.interpreted-text role="term"}
- `CVE_CHECK_WHITELIST` became `CVE_CHECK_IGNORE`{.interpreted-text role="term"}
- `ICECC_USER_CLASS_BL` became `ICECC_CLASS_DISABLE`{.interpreted-text role="term"}
- `ICECC_SYSTEM_CLASS_BL` became `ICECC_CLASS_DISABLE`{.interpreted-text role="term"}
- `ICECC_USER_PACKAGE_WL` became `ICECC_RECIPE_ENABLE`{.interpreted-text role="term"}
- `ICECC_USER_PACKAGE_BL` became `ICECC_RECIPE_DISABLE`{.interpreted-text role="term"}
- `ICECC_SYSTEM_PACKAGE_BL` became `ICECC_RECIPE_DISABLE`{.interpreted-text role="term"}
- `LICENSE_FLAGS_WHITELIST` became `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"}
- `MULTI_PROVIDER_WHITELIST` became `BB_MULTI_PROVIDER_ALLOWED`{.interpreted-text role="term"}
- `PNBLACKLIST` became `SKIP_RECIPE`{.interpreted-text role="term"}
- `SDK_LOCAL_CONF_BLACKLIST` became `ESDK_LOCALCONF_REMOVE`{.interpreted-text role="term"}
- `SDK_LOCAL_CONF_WHITELIST` became `ESDK_LOCALCONF_ALLOW`{.interpreted-text role="term"}
- `SDK_INHERIT_BLACKLIST` became `ESDK_CLASS_INHERIT_DISABLE`{.interpreted-text role="term"}
- `SSTATE_DUPWHITELIST` became `SSTATE_ALLOW_OVERLAP_FILES`
- `SYSROOT_DIRS_BLACKLIST` became `SYSROOT_DIRS_IGNORE`{.interpreted-text role="term"}
- `UNKNOWN_CONFIGURE_WHITELIST` became `UNKNOWN_CONFIGURE_OPT_IGNORE`{.interpreted-text role="term"}
- `WHITELIST_<license>` became `INCOMPATIBLE_LICENSE_EXCEPTIONS`{.interpreted-text role="term"}

In addition, `BB_STAMP_WHITELIST`, `BB_STAMP_POLICY`, `INHERIT_BLACKLIST`, `TUNEABI`, `TUNEABI_WHITELIST`, and `TUNEABI_OVERRIDE` have been removed.

Many internal variable names have been also renamed accordingly.

In addition, in the `cve-check` output, the CVE issue status `Whitelisted` has been renamed to `Ignored`.

The `BB_DISKMON_DIRS`{.interpreted-text role="term"} variable value now uses the term `HALT` instead of `ABORT`.

A :oe\_[git:%60convert-variable-renames.py](git:%60convert-variable-renames.py) \</openembedded-core/tree/scripts/contrib/convert-variable-renames.py\>\` script is provided to convert your recipes and configuration, and also warns you about the use of problematic words. The script performs changes and you need to review them before committing. An example warning looks like:

```
poky/scripts/lib/devtool/upgrade.py needs further work at line 275 since it contains abort
```

# Fetching changes

- Because of the uncertainty in future default branch names in git repositories, it is now required to add a branch name to all URLs described by `git://` and `gitsm://` `SRC_URI`{.interpreted-text role="term"} entries. For example:

  ```
  SRC_URI = "git://git.denx.de/u-boot.git;branch=master"
  ```

  A :oe\_[git:%60convert-srcuri](git:%60convert-srcuri) \</openembedded-core/tree/scripts/contrib/convert-srcuri.py\>[ script to convert your recipes is available in :term:\`OpenEmbedded-Core (OE-Core)]{.title-ref} and in `Poky`{.interpreted-text role="term"}.
- Because of [GitHub dropping support for the git: protocol](https://github.blog/2021-09-01-improving-git-protocol-security-github/), recipes now need to use `;protocol=https` at the end of GitHub URLs. The same `convert-srcuri` script mentioned above can be used to convert your recipes.
- Network access from tasks is now disabled by default on kernels which support this feature (on most recent distros such as CentOS 8 and Debian 11 onwards). This means that tasks accessing the network need to be marked as such with the `network` flag. For example:

  ```
  do_mytask[network] = "1"
  ```

  This is allowed by default from `ref-tasks-fetch`{.interpreted-text role="ref"} but not from any of our other standard tasks. Recipes shouldn\'t be accessing the network outside of `ref-tasks-fetch`{.interpreted-text role="ref"} as it usually undermines fetcher source mirroring, image and licence manifests, software auditing and supply chain security.

# License changes

- The ambiguous \"BSD\" license has been removed from the `common-licenses` directory. Each recipe that fetches or builds BSD-licensed code should specify the proper version of the BSD license in its `LICENSE`{.interpreted-text role="term"} value.
- `LICENSE`{.interpreted-text role="term"} variable values should now use [SPDX identifiers](https://spdx.org/licenses/). If they do not, by default a warning will be shown. A :oe\_[git:%60convert-spdx-licenses.py](git:%60convert-spdx-licenses.py) \</openembedded-core/tree/scripts/contrib/convert-spdx-licenses.py\>\` script can be used to update your recipes.
- `INCOMPATIBLE_LICENSE`{.interpreted-text role="term"} should now use [SPDX identifiers](https://spdx.org/licenses/). Additionally, wildcarding is now limited to specifically supported values -see the `INCOMPATIBLE_LICENSE`{.interpreted-text role="term"} documentation for further information.
- The `AVAILABLE_LICENSES` variable has been removed. This variable was a performance liability and is highly dependent on which layers are added to the configuration, which can cause signature issues for users. In addition the `available_licenses()` function has been removed from the `ref-classes-license`{.interpreted-text role="ref"} class as it is no longer needed.

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
- The Python package build process is now based on [wheels](https://pythonwheels.com/). Here are the new Python packaging classes that should be used: `ref-classes-python_flit_core`{.interpreted-text role="ref"}, `ref-classes-python_setuptools_build_meta`{.interpreted-text role="ref"} and `ref-classes-python_poetry_core`{.interpreted-text role="ref"}.
- The `ref-classes-setuptools3`{.interpreted-text role="ref"} class `ref-tasks-install`{.interpreted-text role="ref"} task now installs the `wheel` binary archive. In current versions of `setuptools` the legacy `setup.py install` method is deprecated. If the `setup.py` cannot be used with wheels, for example it creates files outside of the Python module or standard entry points, then `ref-classes-setuptools3_legacy`{.interpreted-text role="ref"} should be used instead.

# Prelink removed

Prelink has been dropped by `glibc` upstream in 2.36. It already caused issues with binary corruption, has a number of open bugs and is of questionable benefit without disabling load address randomization and PIE executables.

We disabled prelinking by default in the honister (3.4) release, but left it able to be enabled if desired. However, without glibc support it cannot be maintained any further, so all of the prelinking functionality has been removed in this release. If you were enabling the `image-prelink` class in `INHERIT`{.interpreted-text role="term"}, `IMAGE_CLASSES`{.interpreted-text role="term"}, `USER_CLASSES`{.interpreted-text role="term"} etc in your configuration, then you will need to remove the reference(s).

# Reproducible as standard

Reproducibility is now considered as standard functionality, thus the `reproducible` class has been removed and its previous contents merged into the `ref-classes-base`{.interpreted-text role="ref"} class. If you have references in your configuration to `reproducible` in `INHERIT`{.interpreted-text role="term"}, `USER_CLASSES`{.interpreted-text role="term"} etc. then they should be removed.

Additionally, the `BUILD_REPRODUCIBLE_BINARIES` variable is no longer used. Specifically for the kernel, if you wish to enable build timestamping functionality that is normally disabled for reproducibility reasons, you can do so by setting a new `KERNEL_DEBUG_TIMESTAMPS`{.interpreted-text role="term"} variable to \"1\".

# Supported host distribution changes

- Support for `AlmaLinux <AlmaLinux>`{.interpreted-text role="wikipedia"} hosts replacing `CentOS <CentOS>`{.interpreted-text role="wikipedia"}. The following distribution versions were dropped: CentOS 8, Ubuntu 16.04 and Fedora 30, 31 and 32.
- `gcc` version 7.5 is now required at minimum on the build host. For older host distributions where this is not available, you can use the `buildtools-extended`{.interpreted-text role="term"} tarball (easily installable using `scripts/install-buildtools`).

# :append/:prepend in combination with other operators

The `append`, `prepend` and `remove` operators can now only be combined with `=` and `:=` operators. To the exception of the `append` plus `+=` and `prepend` plus `=+` combinations, all combinations could be factored up to the `append`, `prepend` or `remove` in the combination. This brought a lot of confusion on how the override style syntax operators work and should be used. Therefore, those combinations should be replaced by a single `append`, `prepend` or `remove` operator without any additional change. For the `append` plus `+=` (and `prepend` plus `=+`) combinations, the content should be prefixed (respectively suffixed) by a space to maintain the same behavior. You can learn more about override style syntax operators (`append`, `prepend` and `remove`) in the BitBake documentation: `bitbake-user-manual/bitbake-user-manual-metadata:appending and prepending (override style syntax)`{.interpreted-text role="ref"} and `bitbake-user-manual/bitbake-user-manual-metadata:removal (override style syntax)`{.interpreted-text role="ref"}.

# Miscellaneous changes

- `blacklist.bbclass` is removed and the functionality moved to the `ref-classes-base`{.interpreted-text role="ref"} class with a more descriptive `varflag` variable named `SKIP_RECIPE`{.interpreted-text role="term"} which will use the [bb.parse.SkipRecipe()]{.title-ref} function. The usage remains the same, for example:

  ```
  SKIP_RECIPE[my-recipe] = "Reason for skipping recipe"
  ```
- `ref-classes-allarch`{.interpreted-text role="ref"} packagegroups can no longer depend on packages which use `PKG`{.interpreted-text role="term"} renaming such as `ref-classes-debian`{.interpreted-text role="ref"}. Such packagegroups recipes should be changed to avoid inheriting `ref-classes-allarch`{.interpreted-text role="ref"}.
- The `lnr` script has been removed. `lnr` implemented the same behaviour as [ln \--relative \--symbolic]{.title-ref}, since at the time of creation [\--relative]{.title-ref} was only available in coreutils 8.16 onwards which was too new for the older supported distros. Current supported host distros have a new enough version of coreutils, so it is no longer needed. If you have any calls to `lnr` in your recipes or classes, they should be replaced with [ln \--relative \--symbolic]{.title-ref} or [ln -rs]{.title-ref} if you prefer the short version.
- The `package_qa_handle_error()` function formerly in the `ref-classes-insane`{.interpreted-text role="ref"} class has been moved and renamed - if you have any references in your own custom classes they should be changed to `oe.qa.handle_error()`.
- When building `perl`, Berkeley db support is no longer enabled by default, since Berkeley db is largely obsolete. If you wish to reenable it, you can append `bdb` to `PACKAGECONFIG`{.interpreted-text role="term"} in a `perl` bbappend or `PACKAGECONFIG:pn-perl` at the configuration level.
- For the `xserver-xorg` recipe, the `xshmfence`, `xmlto` and `systemd` options previously supported in `PACKAGECONFIG`{.interpreted-text role="term"} have been removed, as they are no longer supported since the move from building it with autotools to meson in this release.
- For the `libsdl2` recipe, various X11 features are now disabled by default (primarily for reproducibility purposes in the native case) with options in `EXTRA_OECMAKE`{.interpreted-text role="term"} within the recipe. These can be changed within a bbappend if desired. See the `libsdl2` recipe for more details.
- The `cortexa72-crc` and `cortexa72-crc-crypto` tunes have been removed since the crc extension is now enabled by default for cortexa72. Replace any references to these with `cortexa72` and `cortexa72-crypto` respectively.
- The Python development shell (previously known as `devpyshell`) feature has been renamed to `pydevshell`. To start it you should now run:

  ```
  bitbake <target> -c pydevshell
  ```
- The `packagegroups-core-full-cmdline-libs` packagegroup is no longer produced, as libraries should normally be brought in via dependencies. If you have any references to this then remove them.
- The `TOPDIR`{.interpreted-text role="term"} variable and the current working directory are no longer modified when parsing recipes. Any code depending on the previous behaviour will no longer work - change any such code to explicitly use appropriate path variables instead.
- In order to exclude the kernel image from the image rootfs, `RRECOMMENDS`{.interpreted-text role="term"}`:${KERNEL_PACKAGE_NAME}-base` should be set instead of `RDEPENDS`{.interpreted-text role="term"}`:${KERNEL_PACKAGE_NAME}-base`.
