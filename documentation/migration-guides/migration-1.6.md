---
title: Release 1.6 (daisy)
---
This section provides migration information for moving to the Yocto Project 1.6 Release (codename \"daisy\") from the prior release.

# `archiver` Class {#migration-1.6-archiver-class}

The `ref-classes-archiver`{.interpreted-text role="ref"} class has been rewritten and its configuration has been simplified. For more details on the source archiver, see the \"`dev-manual/licenses:maintaining open source license compliance during your product's lifecycle`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

# Packaging Changes {#migration-1.6-packaging-changes}

The following packaging changes have been made:

- The `binutils` recipe no longer produces a `binutils-symlinks` package. `update-alternatives` is now used to handle the preferred `binutils` variant on the target instead.
- The tc (traffic control) utilities have been split out of the main `iproute2` package and put into the `iproute2-tc` package.
- The `gtk-engines` schemas have been moved to a dedicated `gtk-engines-schemas` package.
- The `armv7a` with thumb package architecture suffix has changed. The suffix for these packages with the thumb optimization enabled is \"t2\" as it should be. Use of this suffix was not the case in the 1.5 release. Architecture names will change within package feeds as a result.

# BitBake {#migration-1.6-bitbake}

The following changes have been made to `BitBake`{.interpreted-text role="term"}.

## Matching Branch Requirement for Git Fetching {#migration-1.6-matching-branch-requirement-for-git-fetching}

When fetching source from a Git repository using `SRC_URI`{.interpreted-text role="term"}, BitBake will now validate the `SRCREV`{.interpreted-text role="term"} value against the branch. You can specify the branch using the following form:

```
SRC_URI = "git://server.name/repository;branch=branchname"
```

If you do not specify a branch, BitBake looks in the default \"master\" branch.

Alternatively, if you need to bypass this check (e.g. if you are fetching a revision corresponding to a tag that is not on any branch), you can add \";nobranch=1\" to the end of the URL within `SRC_URI`{.interpreted-text role="term"}.

## Python Definition substitutions {#migration-1.6-bitbake-deps}

BitBake had some previously deprecated Python definitions within its `bb` module removed. You should use their sub-module counterparts instead:

- `bb.MalformedUrl`: Use `bb.fetch.MalformedUrl`.
- `bb.encodeurl`: Use `bb.fetch.encodeurl`.
- `bb.decodeurl`: Use `bb.fetch.decodeurl`
- `bb.mkdirhier`: Use `bb.utils.mkdirhier`.
- `bb.movefile`: Use `bb.utils.movefile`.
- `bb.copyfile`: Use `bb.utils.copyfile`.
- `bb.which`: Use `bb.utils.which`.
- `bb.vercmp_string`: Use `bb.utils.vercmp_string`.
- `bb.vercmp`: Use `bb.utils.vercmp`.

## SVK Fetcher {#migration-1.6-bitbake-fetcher}

The SVK fetcher has been removed from BitBake.

## Console Output Error Redirection {#migration-1.6-bitbake-console-output}

The BitBake console UI will now output errors to `stderr` instead of `stdout`. Consequently, if you are piping or redirecting the output of `bitbake` to somewhere else, and you wish to retain the errors, you will need to add `2>&1` (or something similar) to the end of your `bitbake` command line.

## `task-` taskname Overrides {#migration-1.6-task-taskname-overrides}

`task-` taskname overrides have been adjusted so that tasks whose names contain underscores have the underscores replaced by hyphens for the override so that they now function properly. For example, the task override for `ref-tasks-populate_sdk`{.interpreted-text role="ref"} is `task-populate-sdk`.

# Changes to Variables {#migration-1.6-variable-changes}

The following variables have changed. For information on the OpenEmbedded build system variables, see the \"`/ref-manual/variables`{.interpreted-text role="doc"}\" Chapter.

## `TMPDIR` {#migration-1.6-variable-changes-TMPDIR}

`TMPDIR`{.interpreted-text role="term"} can no longer be on an NFS mount. NFS does not offer full POSIX locking and inode consistency and can cause unexpected issues if used to store `TMPDIR`{.interpreted-text role="term"}.

The check for this occurs on startup. If `TMPDIR`{.interpreted-text role="term"} is detected on an NFS mount, an error occurs.

## `PRINC` {#migration-1.6-variable-changes-PRINC}

The `PRINC` variable has been deprecated and triggers a warning if detected during a build. For `PR`{.interpreted-text role="term"} increments on changes, use the PR service instead. You can find out more about this service in the \"`dev-manual/packages:working with a pr service`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

## `IMAGE_TYPES` {#migration-1.6-variable-changes-IMAGE_TYPES}

The \"sum.jffs2\" option for `IMAGE_TYPES`{.interpreted-text role="term"} has been replaced by the \"jffs2.sum\" option, which fits the processing order.

## `COPY_LIC_MANIFEST` {#migration-1.6-variable-changes-COPY_LIC_MANIFEST}

The `COPY_LIC_MANIFEST`{.interpreted-text role="term"} variable must now be set to \"1\" rather than any value in order to enable it.

## `COPY_LIC_DIRS` {#migration-1.6-variable-changes-COPY_LIC_DIRS}

The `COPY_LIC_DIRS`{.interpreted-text role="term"} variable must now be set to \"1\" rather than any value in order to enable it.

## `PACKAGE_GROUP` {#migration-1.6-variable-changes-PACKAGE_GROUP}

The `PACKAGE_GROUP` variable has been renamed to `FEATURE_PACKAGES`{.interpreted-text role="term"} to more accurately reflect its purpose. You can still use `PACKAGE_GROUP` but the OpenEmbedded build system produces a warning message when it encounters the variable.

## Preprocess and Post Process Command Variable Behavior {#migration-1.6-variable-changes-variable-entry-behavior}

The following variables now expect a semicolon separated list of functions to call and not arbitrary shell commands:

> - `ROOTFS_PREPROCESS_COMMAND`{.interpreted-text role="term"}
> - `ROOTFS_POSTPROCESS_COMMAND`{.interpreted-text role="term"}
> - `SDK_POSTPROCESS_COMMAND`{.interpreted-text role="term"}
> - `POPULATE_SDK_POST_TARGET_COMMAND`{.interpreted-text role="term"}
> - `POPULATE_SDK_POST_HOST_COMMAND`{.interpreted-text role="term"}
> - `IMAGE_POSTPROCESS_COMMAND`{.interpreted-text role="term"}
> - `IMAGE_PREPROCESS_COMMAND`{.interpreted-text role="term"}
> - `ROOTFS_POSTUNINSTALL_COMMAND`{.interpreted-text role="term"}
> - `ROOTFS_POSTINSTALL_COMMAND`{.interpreted-text role="term"}

For migration purposes, you can simply wrap shell commands in a shell function and then call the function. Here is an example:

```
my_postprocess_function() {
   echo "hello" > ${IMAGE_ROOTFS}/hello.txt
}
ROOTFS_POSTPROCESS_COMMAND += "my_postprocess_function; "
```

# Package Test (ptest) {#migration-1.6-package-test-ptest}

Package Tests (ptest) are built but not installed by default. For information on using Package Tests, see the \"`dev-manual/packages:testing packages with ptest`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual. See also the \"`ref-classes-ptest`{.interpreted-text role="ref"}\" section.

# Build Changes {#migration-1.6-build-changes}

Separate build and source directories have been enabled by default for selected recipes where it is known to work and for all recipes that inherit the `ref-classes-cmake`{.interpreted-text role="ref"} class. In future releases the `ref-classes-autotools`{.interpreted-text role="ref"} class will enable a separate `Build Directory`{.interpreted-text role="term"} by default as well. Recipes building Autotools-based software that fails to build with a separate `Build Directory`{.interpreted-text role="term"} should be changed to inherit from the `autotools-brokensep <ref-classes-autotools>`{.interpreted-text role="ref"} class instead of the `ref-classes-autotools`{.interpreted-text role="ref"} or `autotools_stage` classes.

# `qemu-native` {#migration-1.6-building-qemu-native}

`qemu-native` now builds without SDL-based graphical output support by default. The following additional lines are needed in your `local.conf` to enable it:

```
PACKAGECONFIG_pn-qemu-native = "sdl"
ASSUME_PROVIDED += "libsdl-native"
```

::: note
::: title
Note
:::

The default `local.conf` contains these statements. Consequently, if you are building a headless system and using a default `local.conf` file, you will need comment these two lines out.
:::

# `core-image-basic` {#migration-1.6-core-image-basic}

`core-image-basic` has been renamed to `core-image-full-cmdline`.

In addition to `core-image-basic` being renamed, `packagegroup-core-basic` has been renamed to `packagegroup-core-full-cmdline` to match.

# Licensing {#migration-1.6-licensing}

The top-level `LICENSE`{.interpreted-text role="term"} file has been changed to better describe the license of the various components of `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"}. However, the licensing itself remains unchanged.

Normally, this change would not cause any side-effects. However, some recipes point to this file within `LIC_FILES_CHKSUM`{.interpreted-text role="term"} (as `${COREBASE}/LICENSE`) and thus the accompanying checksum must be changed from 3f40d7994397109285ec7b81fdeb3b58 to 4d92cd373abda3937c2bc47fbc49d690. A better alternative is to have `LIC_FILES_CHKSUM`{.interpreted-text role="term"} point to a file describing the license that is distributed with the source that the recipe is building, if possible, rather than pointing to `${COREBASE}/LICENSE`.

# `CFLAGS` Options {#migration-1.6-cflags-options}

The \"-fpermissive\" option has been removed from the default `CFLAGS`{.interpreted-text role="term"} value. You need to take action on individual recipes that fail when building with this option. You need to either patch the recipes to fix the issues reported by the compiler, or you need to add \"-fpermissive\" to `CFLAGS`{.interpreted-text role="term"} in the recipes.

# Custom Image Output Types {#migration-1.6-custom-images}

Custom image output types, as selected using `IMAGE_FSTYPES`{.interpreted-text role="term"}, must declare their dependencies on other image types (if any) using a new `IMAGE_TYPEDEP`{.interpreted-text role="term"} variable.

# Tasks {#migration-1.6-do-package-write-task}

The `do_package_write` task has been removed. The task is no longer needed.

# `update-alternative` Provider {#migration-1.6-update-alternatives-provider}

The default `update-alternatives` provider has been changed from `opkg` to `opkg-utils`. This change resolves some troublesome circular dependencies. The runtime package has also been renamed from `update-alternatives-cworth` to `update-alternatives-opkg`.

# `virtclass` Overrides {#migration-1.6-virtclass-overrides}

The `virtclass` overrides are now deprecated. Use the equivalent class overrides instead (e.g. `virtclass-native` becomes `class-native`.)

# Removed and Renamed Recipes {#migration-1.6-removed-renamed-recipes}

The following recipes have been removed:

- `packagegroup-toolset-native` \-\-- this recipe is largely unused.
- `linux-yocto-3.8` \-\-- support for the Linux yocto 3.8 kernel has been dropped. Support for the 3.10 and 3.14 kernels have been added with the `linux-yocto-3.10` and `linux-yocto-3.14` recipes.
- `ocf-linux` \-\-- this recipe has been functionally replaced using `cryptodev-linux`.
- `genext2fs` \-\-- `genext2fs` is no longer used by the build system and is unmaintained upstream.
- `js` \-\-- this provided an ancient version of Mozilla\'s javascript engine that is no longer needed.
- `zaurusd` \-\-- the recipe has been moved to the `meta-handheld` layer.
- `eglibc 2.17` \-\-- replaced by the `eglibc 2.19` recipe.
- `gcc 4.7.2` \-\-- replaced by the now stable `gcc 4.8.2`.
- `external-sourcery-toolchain` \-\-- this recipe is now maintained in the `meta-sourcery` layer.
- `linux-libc-headers-yocto 3.4+git` \-\-- now using version 3.10 of the `linux-libc-headers` by default.
- `meta-toolchain-gmae` \-\-- this recipe is obsolete.
- `packagegroup-core-sdk-gmae` \-\-- this recipe is obsolete.
- `packagegroup-core-standalone-gmae-sdk-target` \-\-- this recipe is obsolete.

# Removed Classes {#migration-1.6-removed-classes}

The following classes have become obsolete and have been removed:

- `module_strip`
- `pkg_metainfo`
- `pkg_distribute`
- `image-empty`

# Reference Board Support Packages (BSPs) {#migration-1.6-reference-bsps}

The following reference BSPs changes occurred:

- The BeagleBoard (`beagleboard`) ARM reference hardware has been replaced by the BeagleBone (`beaglebone`) hardware.
- The RouterStation Pro (`routerstationpro`) MIPS reference hardware has been replaced by the EdgeRouter Lite (`edgerouter`) hardware.

The previous reference BSPs for the `beagleboard` and `routerstationpro` machines are still available in a new `meta-yocto-bsp-old` layer in the :yocto\_[git:%60Source](git:%60Source) Repositories \<\>[ at :yocto_git:]{.title-ref}/meta-yocto-bsp-old/\`.
