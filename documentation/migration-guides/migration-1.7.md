---
title: Release 1.7 (dizzy)
---
This section provides migration information for moving to the Yocto Project 1.7 Release (codename \"dizzy\") from the prior release.

# Changes to Setting QEMU `PACKAGECONFIG` Options in `local.conf` {#migration-1.7-changes-to-setting-qemu-packageconfig-options}

The QEMU recipe now uses a number of `PACKAGECONFIG`{.interpreted-text role="term"} options to enable various optional features. The method used to set defaults for these options means that existing `local.conf` files will need to be modified to append to `PACKAGECONFIG`{.interpreted-text role="term"} for `qemu-native` and `nativesdk-qemu` instead of setting it. In other words, to enable graphical output for QEMU, you should now have these lines in `local.conf`:

```
PACKAGECONFIG_append_pn-qemu-native = " sdl"
PACKAGECONFIG_append_pn-nativesdk-qemu = " sdl"
```

# Minimum Git version {#migration-1.7-minimum-git-version}

The minimum `overview-manual/development-environment:git`{.interpreted-text role="ref"} version required on the build host is now 1.7.8 because the `--list` option is now required by BitBake\'s Git fetcher. As always, if your host distribution does not provide a version of Git that meets this requirement, you can use the `buildtools`{.interpreted-text role="term"} tarball that does. See the \"`ref-manual/system-requirements:required git, tar, python, make and gcc versions`{.interpreted-text role="ref"}\" section for more information.

# Autotools Class Changes {#migration-1.7-autotools-class-changes}

The following `ref-classes-autotools`{.interpreted-text role="ref"} class changes occurred:

- *A separate :term:\`Build Directory\` is now used by default:* The `ref-classes-autotools`{.interpreted-text role="ref"} class has been changed to use a directory for building (`B`{.interpreted-text role="term"}), which is separate from the source directory (`S`{.interpreted-text role="term"}). This is commonly referred to as `B != S`, or an out-of-tree build.

  If the software being built is already capable of building in a directory separate from the source, you do not need to do anything. However, if the software is not capable of being built in this manner, you will need to either patch the software so that it can build separately, or you will need to change the recipe to inherit the `autotools-brokensep <ref-classes-autotools>`{.interpreted-text role="ref"} class instead of the `ref-classes-autotools`{.interpreted-text role="ref"} or `autotools_stage` classes.
- The `--foreign` option is no longer passed to `automake` when running `autoconf`: This option tells `automake` that a particular software package does not follow the GNU standards and therefore should not be expected to distribute certain files such as `ChangeLog`, `AUTHORS`, and so forth. Because the majority of upstream software packages already tell `automake` to enable foreign mode themselves, the option is mostly superfluous. However, some recipes will need patches for this change. You can easily make the change by patching `configure.ac` so that it passes \"foreign\" to `AM_INIT_AUTOMAKE()`. See :oe\_[git:%60this](git:%60this) commit \</openembedded-core/commit/?id=01943188f85ce6411717fb5bf702d609f55813f2\>\` for an example showing how to make the patch.

# Binary Configuration Scripts Disabled {#migration-1.7-binary-configuration-scripts-disabled}

Some of the core recipes that package binary configuration scripts now disable the scripts due to the scripts previously requiring error-prone path substitution. Software that links against these libraries using these scripts should use the much more robust `pkg-config` instead. The list of recipes changed in this version (and their configuration scripts) is as follows:

```
directfb (directfb-config)
freetype (freetype-config)
gpgme (gpgme-config)
libassuan (libassuan-config)
libcroco (croco-6.0-config)
libgcrypt (libgcrypt-config)
libgpg-error (gpg-error-config)
libksba (ksba-config)
libpcap (pcap-config)
libpcre (pcre-config)
libpng (libpng-config, libpng16-config)
libsdl (sdl-config)
libusb-compat (libusb-config)
libxml2 (xml2-config)
libxslt (xslt-config)
ncurses (ncurses-config)
neon (neon-config)
npth (npth-config)
pth (pth-config)
taglib (taglib-config)
```

Additionally, support for `pkg-config` has been added to some recipes in the previous list in the rare cases where the upstream software package does not already provide it.

# `eglibc 2.19` Replaced with `glibc 2.20` {#migration-1.7-glibc-replaces-eglibc}

Because `eglibc` and `glibc` were already fairly close, this replacement should not require any significant changes to other software that links to `eglibc`. However, there were a number of minor changes in `glibc 2.20` upstream that could require patching some software (e.g. the removal of the `_BSD_SOURCE` feature test macro).

`glibc 2.20` requires version 2.6.32 or greater of the Linux kernel. Thus, older kernels will no longer be usable in conjunction with it.

For full details on the changes in `glibc 2.20`, see the upstream release notes [here](https://sourceware.org/ml/libc-alpha/2014-09/msg00088.html).

# Kernel Module Autoloading {#migration-1.7-kernel-module-autoloading}

The `module_autoload_* <module_autoload>`{.interpreted-text role="term"} variable is now deprecated and a new `KERNEL_MODULE_AUTOLOAD`{.interpreted-text role="term"} variable should be used instead. Also, `module_conf_* <module_conf>`{.interpreted-text role="term"} must now be used in conjunction with a new `KERNEL_MODULE_PROBECONF`{.interpreted-text role="term"} variable. The new variables no longer require you to specify the module name as part of the variable name. This change not only simplifies usage but also allows the values of these variables to be appropriately incorporated into task signatures and thus trigger the appropriate tasks to re-execute when changed. You should replace any references to `module_autoload_*` with `KERNEL_MODULE_AUTOLOAD`{.interpreted-text role="term"}, and add any modules for which `module_conf_*` is specified to `KERNEL_MODULE_PROBECONF`{.interpreted-text role="term"}.

# QA Check Changes {#migration-1.7-qa-check-changes}

The following changes have occurred to the QA check process:

- Additional QA checks `file-rdeps` and `build-deps` have been added in order to verify that file dependencies are satisfied (e.g. package contains a script requiring `/bin/bash`) and build-time dependencies are declared, respectively. For more information, please see the \"`/ref-manual/qa-checks`{.interpreted-text role="doc"}\" chapter.
- Package QA checks are now performed during a new `ref-tasks-package_qa`{.interpreted-text role="ref"} task rather than being part of the `ref-tasks-package`{.interpreted-text role="ref"} task. This allows more parallel execution. This change is unlikely to be an issue except for highly customized recipes that disable packaging tasks themselves by marking them as `noexec`. For those packages, you will need to disable the `ref-tasks-package_qa`{.interpreted-text role="ref"} task as well.
- Files being overwritten during the `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} task now trigger an error instead of a warning. Recipes should not be overwriting files written to the sysroot by other recipes. If you have these types of recipes, you need to alter them so that they do not overwrite these files.

  You might now receive this error after changes in configuration or metadata resulting in orphaned files being left in the sysroot. If you do receive this error, the way to resolve the issue is to delete your `TMPDIR`{.interpreted-text role="term"} or to move it out of the way and then re-start the build. Anything that has been fully built up to that point and does not need rebuilding will be restored from the shared state cache and the rest of the build will be able to proceed as normal.

# Removed Recipes {#migration-1.7-removed-recipes}

The following recipes have been removed:

- `x-load`: This recipe has been superseded by U-Boot SPL for all Cortex-based TI SoCs. For legacy boards, the `meta-ti` layer, which contains a maintained recipe, should be used instead.
- `ubootchart`: This recipe is obsolete. A `bootchart2` recipe has been added to functionally replace it.
- `linux-yocto 3.4`: Support for the linux-yocto 3.4 kernel has been dropped. Support for the 3.10 and 3.14 kernels remains, while support for version 3.17 has been added.
- `eglibc` has been removed in favor of `glibc`. See the \"`migration-1.7-glibc-replaces-eglibc`{.interpreted-text role="ref"}\" section for more information.

# Miscellaneous Changes {#migration-1.7-miscellaneous-changes}

The following miscellaneous change occurred:

- The build history feature now writes `build-id.txt` instead of `build-id`. Additionally, `build-id.txt` now contains the full build header as printed by BitBake upon starting the build. You should manually remove old \"build-id\" files from your existing build history repositories to avoid confusion. For information on the build history feature, see the \"`dev-manual/build-quality:maintaining build output quality`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.
