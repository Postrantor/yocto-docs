---
title: Release 2.1 (krogoth)
---
This section provides migration information for moving to the Yocto Project 2.1 Release (codename \"krogoth\") from the prior release.

# Variable Expansion in Python Functions {#migration-2.1-variable-expansion-in-python-functions}

Variable expressions, such as `${VARNAME}` no longer expand automatically within Python functions. Suppressing expansion was done to allow Python functions to construct shell scripts or other code for situations in which you do not want such expressions expanded. For any existing code that relies on these expansions, you need to change the expansions to expand the value of individual variables through `d.getVar()`. To alternatively expand more complex expressions, use `d.expand()`.

# Overrides Must Now be Lower-Case {#migration-2.1-overrides-must-now-be-lower-case}

The convention for overrides has always been for them to be lower-case characters. This practice is now a requirement as BitBake\'s datastore now assumes lower-case characters in order to give a slight performance boost during parsing. In practical terms, this requirement means that anything that ends up in `OVERRIDES`{.interpreted-text role="term"} must now appear in lower-case characters (e.g. values for `MACHINE`{.interpreted-text role="term"}, `TARGET_ARCH`{.interpreted-text role="term"}, `DISTRO`{.interpreted-text role="term"}, and also recipe names if `_pn-` recipename overrides are to be effective).

# Expand Parameter to `getVar()` and `getVarFlag()` is Now Mandatory {#migration-2.1-expand-parameter-to-getvar-and-getvarflag-now-mandatory}

The expand parameter to `getVar()` and `getVarFlag()` previously defaulted to False if not specified. Now, however, no default exists so one must be specified. You must change any `getVar()` calls that do not specify the final expand parameter to calls that do specify the parameter. You can run the following `sed` command at the base of a layer to make this change:

```
sed -e 's:\(\.getVar([^,()]*\)):\1, False):g' -i `grep -ril getVar *`
sed -e 's:\(\.getVarFlag([^,()]*,[^,()]*\)):\1, False):g' -i `grep -ril getVarFlag *`
```

::: note
::: title
Note
:::

The reason for this change is that it prepares the way for changing the default to True in a future Yocto Project release. This future change is a much more sensible default than False. However, the change needs to be made gradually as a sudden change of the default would potentially cause side-effects that would be difficult to detect.
:::

# Makefile Environment Changes {#migration-2.1-makefile-environment-changes}

`EXTRA_OEMAKE`{.interpreted-text role="term"} now defaults to \"\" instead of \"-e MAKEFLAGS=\". Setting `EXTRA_OEMAKE`{.interpreted-text role="term"} to \"-e MAKEFLAGS=\" by default was a historical accident that has required many classes (e.g. `ref-classes-autotools`{.interpreted-text role="ref"}, `module`) and recipes to override this default in order to work with sensible build systems. When upgrading to the release, you must edit any recipe that relies upon this old default by either setting `EXTRA_OEMAKE`{.interpreted-text role="term"} back to \"-e MAKEFLAGS=\" or by explicitly setting any required variable value overrides using `EXTRA_OEMAKE`{.interpreted-text role="term"}, which is typically only needed when a Makefile sets a default value for a variable that is inappropriate for cross-compilation using the \"=\" operator rather than the \"?=\" operator.

# `libexecdir` Reverted to `${prefix}/libexec` {#migration-2.1-libexecdir-reverted-to-prefix-libexec}

The use of `${libdir}/${BPN}` as `libexecdir` is different as compared to all other mainstream distributions, which either uses `${prefix}/libexec` or `${libdir}`. The use is also contrary to the GNU Coding Standards (i.e. [https://www.gnu.org/prep/standards/html_node/Directory-Variables.html](https://www.gnu.org/prep/standards/html_node/Directory-Variables.html)) that suggest `${prefix}/libexec` and also notes that any package-specific nesting should be done by the package itself. Finally, having `libexecdir` change between recipes makes it very difficult for different recipes to invoke binaries that have been installed into `libexecdir`. The Filesystem Hierarchy Standard (i.e. [https://refspecs.linuxfoundation.org/FHS_3.0/fhs/ch04s07.html](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/ch04s07.html)) now recognizes the use of `${prefix}/libexec/`, giving distributions the choice between `${prefix}/lib` or `${prefix}/libexec` without breaking FHS.

# `ac_cv_sizeof_off_t` is No Longer Cached in Site Files {#migration-2.1-ac-cv-sizeof-off-t-no-longer-cached-in-site-files}

For recipes inheriting the `ref-classes-autotools`{.interpreted-text role="ref"} class, `ac_cv_sizeof_off_t` is no longer cached in the site files for `autoconf`. The reason for this change is because the `ac_cv_sizeof_off_t` value is not necessarily static per architecture as was previously assumed. Rather, the value changes based on whether large file support is enabled. For most software that uses `autoconf`, this change should not be a problem. However, if you have a recipe that bypasses the standard `ref-tasks-configure`{.interpreted-text role="ref"} task from the `ref-classes-autotools`{.interpreted-text role="ref"} class and the software the recipe is building uses a very old version of `autoconf`, the recipe might be incapable of determining the correct size of `off_t` during `ref-tasks-configure`{.interpreted-text role="ref"}.

The best course of action is to patch the software as necessary to allow the default implementation from the `ref-classes-autotools`{.interpreted-text role="ref"} class to work such that `autoreconf` succeeds and produces a working configure script, and to remove the overridden `ref-tasks-configure`{.interpreted-text role="ref"} task such that the default implementation does get used.

# Image Generation is Now Split Out from Filesystem Generation {#migration-2.1-image-generation-split-out-from-filesystem-generation}

Previously, for image recipes the `ref-tasks-rootfs`{.interpreted-text role="ref"} task assembled the filesystem and then from that filesystem generated images. With this Yocto Project release, image generation is split into separate `ref-tasks-image`{.interpreted-text role="ref"} tasks for clarity both in operation and in the code.

For most cases, this change does not present any problems. However, if you have made customizations that directly modify the `ref-tasks-rootfs`{.interpreted-text role="ref"} task or that mention `ref-tasks-rootfs`{.interpreted-text role="ref"}, you might need to update those changes. In particular, if you had added any tasks after `ref-tasks-rootfs`{.interpreted-text role="ref"}, you should make edits so that those tasks are after the `ref-tasks-image-complete`{.interpreted-text role="ref"} task rather than after `ref-tasks-rootfs`{.interpreted-text role="ref"} so that your added tasks run at the correct time.

A minor part of this restructuring is that the post-processing definitions and functions have been moved from the `ref-classes-image`{.interpreted-text role="ref"} class to the `rootfs-postcommands <ref-classes-rootfs*>`{.interpreted-text role="ref"} class. Functionally, however, they remain unchanged.

# Removed Recipes {#migration-2.1-removed-recipes}

The following recipes have been removed in the 2.1 release:

- `gcc` version 4.8: Versions 4.9 and 5.3 remain.
- `qt4`: All support for Qt 4.x has been moved out to a separate `meta-qt4` layer because Qt 4 is no longer supported upstream.
- `x11vnc`: Moved to the `meta-oe` layer.
- `linux-yocto-3.14`: No longer supported.
- `linux-yocto-3.19`: No longer supported.
- `libjpeg`: Replaced by the `libjpeg-turbo` recipe.
- `pth`: Became obsolete.
- `liboil`: Recipe is no longer needed and has been moved to the `meta-multimedia` layer.
- `gtk-theme-torturer`: Recipe is no longer needed and has been moved to the `meta-gnome` layer.
- `gnome-mime-data`: Recipe is no longer needed and has been moved to the `meta-gnome` layer.
- `udev`: Replaced by the `eudev` recipe for compatibility when using `sysvinit` with newer kernels.
- `python-pygtk`: Recipe became obsolete.
- `adt-installer`: Recipe became obsolete. See the \"`migration-guides/migration-2.1:adt removed`{.interpreted-text role="ref"}\" section for more information.

# Class Changes {#migration-2.1-class-changes}

The following classes have changed:

- `autotools_stage`: Removed because the `ref-classes-autotools`{.interpreted-text role="ref"} class now provides its functionality. Recipes that inherited from `autotools_stage` should now inherit from `ref-classes-autotools`{.interpreted-text role="ref"} instead.
- `boot-directdisk`: Merged into the `image-vm` class. The `boot-directdisk` class was rarely directly used. Consequently, this change should not cause any issues.
- `bootimg`: Merged into the `ref-classes-image-live`{.interpreted-text role="ref"} class. The `bootimg` class was rarely directly used. Consequently, this change should not cause any issues.
- `packageinfo`: Removed due to its limited use by the Hob UI, which has itself been removed.

# Build System User Interface Changes {#migration-2.1-build-system-ui-changes}

The following changes have been made to the build system user interface:

- *Hob GTK+-based UI*: Removed because it is unmaintained and based on the outdated GTK+ 2 library. The Toaster web-based UI is much more capable and is actively maintained. See the \"`toaster-manual/setup-and-use:using the toaster web interface`{.interpreted-text role="ref"}\" section in the Toaster User Manual for more information on this interface.
- *\"puccho\" BitBake UI*: Removed because is unmaintained and no longer useful.

# ADT Removed {#migration-2.1-adt-removed}

The Application Development Toolkit (ADT) has been removed because its functionality almost completely overlapped with the `standard SDK <sdk-manual/using:using the standard sdk>`{.interpreted-text role="ref"} and the `extensible SDK <sdk-manual/extensible:using the extensible sdk>`{.interpreted-text role="ref"}. For information on these SDKs and how to build and use them, see the `/sdk-manual/index`{.interpreted-text role="doc"} manual.

::: note
::: title
Note
:::

The Yocto Project Eclipse IDE Plug-in is still supported and is not affected by this change.
:::

# Poky Reference Distribution Changes {#migration-2.1-poky-reference-distribution-changes}

The following changes have been made for the Poky distribution:

- The `meta-yocto` layer has been renamed to `meta-poky` to better match its purpose, which is to provide the Poky reference distribution. The `meta-yocto-bsp` layer retains its original name since it provides reference machines for the Yocto Project and it is otherwise unrelated to Poky. References to `meta-yocto` in your `conf/bblayers.conf` should automatically be updated, so you should not need to change anything unless you are relying on this naming elsewhere.
- The `ref-classes-uninative`{.interpreted-text role="ref"} class is now enabled by default in Poky. This class attempts to isolate the build system from the host distribution\'s C library and makes re-use of native shared state artifacts across different host distributions practical. With this class enabled, a tarball containing a pre-built C library is downloaded at the start of the build.

  The `ref-classes-uninative`{.interpreted-text role="ref"} class is enabled through the `meta/conf/distro/include/yocto-uninative.inc` file, which for those not using the Poky distribution, can include to easily enable the same functionality.

  Alternatively, if you wish to build your own `uninative` tarball, you can do so by building the `uninative-tarball` recipe, making it available to your build machines (e.g. over HTTP/HTTPS) and setting a similar configuration as the one set by `yocto-uninative.inc`.
- Static library generation, for most cases, is now disabled by default in the Poky distribution. Disabling this generation saves some build time as well as the size used for build output artifacts.

  Disabling this library generation is accomplished through a `meta/conf/distro/include/no-static-libs.inc`, which for those not using the Poky distribution can easily include to enable the same functionality.

  Any recipe that needs to opt-out of having the `--disable-static` option specified on the configure command line either because it is not a supported option for the configure script or because static libraries are needed should set the following variable:

  ```
  DISABLE_STATIC = ""
  ```
- The separate `poky-tiny` distribution now uses the musl C library instead of a heavily pared down `glibc`. Using musl results in a smaller distribution and facilitates much greater maintainability because musl is designed to have a small footprint.

  If you have used `poky-tiny` and have customized the `glibc` configuration you will need to redo those customizations with musl when upgrading to the new release.

# Packaging Changes {#migration-2.1-packaging-changes}

The following changes have been made to packaging:

- The `runuser` and `mountpoint` binaries, which were previously in the main `util-linux` package, have been split out into the `util-linux-runuser` and `util-linux-mountpoint` packages, respectively.
- The `python-elementtree` package has been merged into the `python-xml` package.

# Tuning File Changes {#migration-2.1-tuning-file-changes}

The following changes have been made to the tuning files:

- The \"no-thumb-interwork\" tuning feature has been dropped from the ARM tune include files. Because interworking is required for ARM EABI, attempting to disable it through a tuning feature no longer makes sense.

  ::: note
  ::: title
  Note
  :::

  Support for ARM OABI was deprecated in gcc 4.7.
  :::
- The `tune-cortexm*.inc` and `tune-cortexr4.inc` files have been removed because they are poorly tested. Until the OpenEmbedded build system officially gains support for CPUs without an MMU, these tuning files would probably be better maintained in a separate layer if needed.

# Supporting GObject Introspection {#migration-2.1-supporting-gobject-introspection}

This release supports generation of GLib Introspective Repository (GIR) files through GObject introspection, which is the standard mechanism for accessing GObject-based software from runtime environments. You can enable, disable, and test the generation of this data. See the \"`dev-manual/gobject-introspection:enabling gobject introspection support`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for more information.

# Miscellaneous Changes {#migration-2.1-miscellaneous-changes}

These additional changes exist:

- The minimum Git version has been increased to 1.8.3.1. If your host distribution does not provide a sufficiently recent version, you can install the `buildtools`{.interpreted-text role="term"}, which will provide it. See the `ref-manual/system-requirements:required git, tar, python, make and gcc versions`{.interpreted-text role="ref"} section for more information on the `buildtools`{.interpreted-text role="term"} tarball.
- The buggy and incomplete support for the RPM version 4 package manager has been removed. The well-tested and maintained support for RPM version 5 remains.
- Previously, the following list of packages were removed if package-management was not in `IMAGE_FEATURES`{.interpreted-text role="term"}, regardless of any dependencies:

  ```
  update-rc.d
  base-passwd
  shadow
  update-alternatives
  run-postinsts
  ```

  With the Yocto Project 2.1 release, these packages are only removed if \"read-only-rootfs\" is in `IMAGE_FEATURES`{.interpreted-text role="term"}, since they might still be needed for a read-write image even in the absence of a package manager (e.g. if users need to be added, modified, or removed at runtime).
- The ``devtool modify <sdk-manual/extensible:use \`\`devtool modify\`\` to modify the source of an existing component>``{.interpreted-text role="ref"} command now defaults to extracting the source since that is most commonly expected. The `-x` or `--extract` options are now no-ops. If you wish to provide your own existing source tree, you will now need to specify either the `-n` or `--no-extract` options when running `devtool modify`.
- If the formfactor for a machine is either not supplied or does not specify whether a keyboard is attached, then the default is to assume a keyboard is attached rather than assume no keyboard. This change primarily affects the Sato UI.
- The `.debug` directory packaging is now automatic. If your recipe builds software that installs binaries into directories other than the standard ones, you no longer need to take care of setting `FILES_${PN}-dbg` to pick up the resulting `.debug` directories as these directories are automatically found and added.
- Inaccurate disk and CPU percentage data has been dropped from `ref-classes-buildstats`{.interpreted-text role="ref"} output. This data has been replaced with `getrusage()` data and corrected IO statistics. You will probably need to update any custom code that reads the `ref-classes-buildstats`{.interpreted-text role="ref"} data.
- The `meta/conf/distro/include/package_regex.inc` is now deprecated. The contents of this file have been moved to individual recipes.

  ::: note
  ::: title
  Note
  :::

  Because this file will likely be removed in a future Yocto Project release, it is suggested that you remove any references to the file that might be in your configuration.
  :::
- The `v86d/uvesafb` has been removed from the `genericx86` and `genericx86-64` reference machines, which are provided by the `meta-yocto-bsp` layer. Most modern x86 boards do not rely on this file and it only adds kernel error messages during startup. If you do still need to support `uvesafb`, you can simply add `v86d` to your image.
- Build sysroot paths are now removed from debug symbol files. Removing these paths means that remote GDB using an unstripped build system sysroot will no longer work (although this was never documented to work). The supported method to accomplish something similar is to set `IMAGE_GEN_DEBUGFS` to \"1\", which will generate a companion debug image containing unstripped binaries and associated debug sources alongside the image.
