---
title: Release 1.5 (dora)
---
This section provides migration information for moving to the Yocto Project 1.5 Release (codename \"dora\") from the prior release.

# Host Dependency Changes {#migration-1.5-host-dependency-changes}

The OpenEmbedded build system now has some additional requirements on the host system:

- Python 2.7.3+
- Tar 1.24+
- Git 1.7.8+
- Patched version of Make if you are using 3.82. Most distributions that provide Make 3.82 use the patched version.

If the Linux distribution you are using on your build host does not provide packages for these, you can install and use the Buildtools tarball, which provides an SDK-like environment containing them.

For more information on this requirement, see the \"`system-requirements-buildtools`{.interpreted-text role="ref"}\" section.

# `atom-pc` Board Support Package (BSP) {#migration-1.5-atom-pc-bsp}

The `atom-pc` hardware reference BSP has been replaced by a `genericx86` BSP. This BSP is not necessarily guaranteed to work on all x86 hardware, but it will run on a wider range of systems than the `atom-pc` did.

::: note
::: title
Note
:::

Additionally, a `genericx86-64` BSP has been added for 64-bit Atom systems.
:::

# BitBake {#migration-1.5-bitbake}

The following changes have been made that relate to BitBake:

- BitBake now supports a `_remove` operator. The addition of this operator means you will have to rename any items in recipe space (functions, variables) whose names currently contain `_remove_` or end with `_remove` to avoid unexpected behavior.
- BitBake\'s global method pool has been removed. This method is not particularly useful and led to clashes between recipes containing functions that had the same name.
- The \"none\" server backend has been removed. The \"process\" server backend has been serving well as the default for a long time now.
- The `bitbake-runtask` script has been removed.
- `${``P`{.interpreted-text role="term"}`}` and `${``PF`{.interpreted-text role="term"}`}` are no longer added to `PROVIDES`{.interpreted-text role="term"} by default in `bitbake.conf`. These version-specific `PROVIDES`{.interpreted-text role="term"} items were seldom used. Attempting to use them could result in two versions being built simultaneously rather than just one version due to the way BitBake resolves dependencies.

# QA Warnings {#migration-1.5-qa-warnings}

The following changes have been made to the package QA checks:

- If you have customized `ERROR_QA`{.interpreted-text role="term"} or `WARN_QA`{.interpreted-text role="term"} values in your configuration, check that they contain all of the issues that you wish to be reported. Previous Yocto Project versions contained a bug that meant that any item not mentioned in `ERROR_QA`{.interpreted-text role="term"} or `WARN_QA`{.interpreted-text role="term"} would be treated as a warning. Consequently, several important items were not already in the default value of `WARN_QA`{.interpreted-text role="term"}. All of the possible QA checks are now documented in the \"`ref-classes-insane`{.interpreted-text role="ref"}\" section.
- An additional QA check has been added to check if `/usr/share/info/dir` is being installed. Your recipe should delete this file within `ref-tasks-install`{.interpreted-text role="ref"} if \"make install\" is installing it.
- If you are using the `ref-classes-buildhistory`{.interpreted-text role="ref"} class, the check for the package version going backwards is now controlled using a standard QA check. Thus, if you have customized your `ERROR_QA`{.interpreted-text role="term"} or `WARN_QA`{.interpreted-text role="term"} values and still wish to have this check performed, you should add \"version-going-backwards\" to your value for one or the other variables depending on how you wish it to be handled. See the documented QA checks in the \"`ref-classes-insane`{.interpreted-text role="ref"}\" section.

# Directory Layout Changes {#migration-1.5-directory-layout-changes}

The following directory changes exist:

- Output SDK installer files are now named to include the image name and tuning architecture through the `SDK_NAME`{.interpreted-text role="term"} variable.
- Images and related files are now installed into a directory that is specific to the machine, instead of a parent directory containing output files for multiple machines. The `DEPLOY_DIR_IMAGE`{.interpreted-text role="term"} variable continues to point to the directory containing images for the current `MACHINE`{.interpreted-text role="term"} and should be used anywhere there is a need to refer to this directory. The `runqemu` script now uses this variable to find images and kernel binaries and will use BitBake to determine the directory. Alternatively, you can set the `DEPLOY_DIR_IMAGE`{.interpreted-text role="term"} variable in the external environment.
- When buildhistory is enabled, its output is now written under the `Build Directory`{.interpreted-text role="term"} rather than `TMPDIR`{.interpreted-text role="term"}. Doing so makes it easier to delete `TMPDIR`{.interpreted-text role="term"} and preserve the build history. Additionally, data for produced SDKs is now split by `IMAGE_NAME`{.interpreted-text role="term"}.
- When `ref-classes-buildhistory`{.interpreted-text role="ref"} is enabled, its output is now written under the `Build Directory`{.interpreted-text role="term"} rather than `TMPDIR`{.interpreted-text role="term"}. Doing so makes it easier to delete `TMPDIR`{.interpreted-text role="term"} and preserve the build history. Additionally, data for produced SDKs is now split by `IMAGE_NAME`{.interpreted-text role="term"}.
- The `pkgdata` directory produced as part of the packaging process has been collapsed into a single machine-specific directory. This directory is located under `sysroots` and uses a machine-specific name (i.e. `tmp/sysroots/machine/pkgdata`).

# Shortened Git `SRCREV` Values {#migration-1.5-shortened-git-srcrev-values}

BitBake will now shorten revisions from Git repositories from the normal 40 characters down to 10 characters within `SRCPV`{.interpreted-text role="term"} for improved usability in path and filenames. This change should be safe within contexts where these revisions are used because the chances of spatially close collisions is very low. Distant collisions are not a major issue in the way the values are used.

# `IMAGE_FEATURES` {#migration-1.5-image-features}

The following changes have been made that relate to `IMAGE_FEATURES`{.interpreted-text role="term"}:

- The value of `IMAGE_FEATURES`{.interpreted-text role="term"} is now validated to ensure invalid feature items are not added. Some users mistakenly add package names to this variable instead of using `IMAGE_INSTALL`{.interpreted-text role="term"} in order to have the package added to the image, which does not work. This change is intended to catch those kinds of situations. Valid `IMAGE_FEATURES`{.interpreted-text role="term"} are drawn from `PACKAGE_GROUP` definitions, `COMPLEMENTARY_GLOB`{.interpreted-text role="term"} and a new \"validitems\" varflag on `IMAGE_FEATURES`{.interpreted-text role="term"}. The \"validitems\" varflag change allows additional features to be added if they are not provided using the previous two mechanisms.
- The previously deprecated \"apps-console-core\" `IMAGE_FEATURES`{.interpreted-text role="term"} item is no longer supported. Add \"splash\" to `IMAGE_FEATURES`{.interpreted-text role="term"} if you wish to have the splash screen enabled, since this is all that apps-console-core was doing.

# `/run` {#migration-1.5-run}

The `/run` directory from the Filesystem Hierarchy Standard 3.0 has been introduced. You can find some of the implications for this change :oe\_[git:%60here](git:%60here) \</openembedded-core/commit/?id=0e326280a15b0f2c4ef2ef4ec441f63f55b75873\>[. The change also means that recipes that install files to ]{.title-ref}[/var/run]{.title-ref}[ must be changed. You can find a guide on how to make these changes \`here \<[https://www.mail-archive.com/openembedded-devel@lists.openembedded.org/msg31649.html](https://www.mail-archive.com/openembedded-devel@lists.openembedded.org/msg31649.html)\>]{.title-ref}\_\_.

# Removal of Package Manager Database Within Image Recipes {#migration-1.5-removal-of-package-manager-database-within-image-recipes}

The image `core-image-minimal` no longer adds `remove_packaging_data_files` to `ROOTFS_POSTPROCESS_COMMAND`{.interpreted-text role="term"}. This addition is now handled automatically when \"package-management\" is not in `IMAGE_FEATURES`{.interpreted-text role="term"}. If you have custom image recipes that make this addition, you should remove the lines, as they are not needed and might interfere with correct operation of postinstall scripts.

# Images Now Rebuild Only on Changes Instead of Every Time {#migration-1.5-images-now-rebuild-only-on-changes-instead-of-every-time}

The `ref-tasks-rootfs`{.interpreted-text role="ref"} and other related image construction tasks are no longer marked as \"nostamp\". Consequently, they will only be re-executed when their inputs have changed. Previous versions of the OpenEmbedded build system always rebuilt the image when requested rather when necessary.

# Task Recipes {#migration-1.5-task-recipes}

The previously deprecated `task.bbclass` has now been dropped. For recipes that previously inherited from this class, you should rename them from `task-*` to `packagegroup-*` and inherit `ref-classes-packagegroup`{.interpreted-text role="ref"} instead.

For more information, see the \"`ref-classes-packagegroup`{.interpreted-text role="ref"}\" section.

# BusyBox {#migration-1.5-busybox}

By default, we now split BusyBox into two binaries: one that is suid root for those components that need it, and another for the rest of the components. Splitting BusyBox allows for optimization that eliminates the `tinylogin` recipe as recommended by upstream. You can disable this split by setting `BUSYBOX_SPLIT_SUID`{.interpreted-text role="term"} to \"0\".

# Automated Image Testing {#migration-1.5-automated-image-testing}

A new automated image testing framework has been added through the `ref-classes-testimage`{.interpreted-text role="ref"} classes. This framework replaces the older `imagetest-qemu` framework.

You can learn more about performing automated image tests in the \"`dev-manual/runtime-testing:performing automated runtime testing`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

# Build History {#migration-1.5-build-history}

Following are changes to Build History:

- Installed package sizes: `installed-package-sizes.txt` for an image now records the size of the files installed by each package instead of the size of each compressed package archive file.
- The dependency graphs (`depends*.dot`) now use the actual package names instead of replacing dashes, dots and plus signs with underscores.
- The `buildhistory-diff` and `buildhistory-collect-srcrevs` utilities have improved command-line handling. Use the `--help` option for each utility for more information on the new syntax.

For more information on Build History, see the \"`dev-manual/build-quality:maintaining build output quality`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

# `udev` {#migration-1.5-udev}

Following are changes to `udev`:

- `udev` no longer brings in `udev-extraconf` automatically through `RRECOMMENDS`{.interpreted-text role="term"}, since this was originally intended to be optional. If you need the extra rules, then add `udev-extraconf` to your image.
- `udev` no longer brings in `pciutils-ids` or `usbutils-ids` through `RRECOMMENDS`{.interpreted-text role="term"}. These are not needed by `udev` itself and removing them saves around 350KB.

# Removed and Renamed Recipes {#migration-1.5-removed-renamed-recipes}

- The `linux-yocto` 3.2 kernel has been removed.
- `libtool-nativesdk` has been renamed to `nativesdk-libtool`.
- `tinylogin` has been removed. It has been replaced by a suid portion of Busybox. See the \"`migration-1.5-busybox`{.interpreted-text role="ref"}\" section for more information.
- `external-python-tarball` has been renamed to `buildtools-tarball`.
- `web-webkit` has been removed. It has been functionally replaced by `midori`.
- `imake` has been removed. It is no longer needed by any other recipe.
- `transfig-native` has been removed. It is no longer needed by any other recipe.
- `anjuta-remote-run` has been removed. Anjuta IDE integration has not been officially supported for several releases.

# Other Changes {#migration-1.5-other-changes}

Following is a list of short entries describing other changes:

- `run-postinsts`: Make this generic.
- `base-files`: Remove the unnecessary `media/` xxx directories.
- `alsa-state`: Provide an empty `asound.conf` by default.
- `classes/image`: Ensure `BAD_RECOMMENDATIONS`{.interpreted-text role="term"} supports pre-renamed package names.
- `classes/rootfs_rpm`: Implement `BAD_RECOMMENDATIONS`{.interpreted-text role="term"} for RPM.
- `systemd`: Remove `systemd_unitdir` if `systemd` is not in `DISTRO_FEATURES`{.interpreted-text role="term"}.
- `systemd`: Remove `init.d` dir if `systemd` unit file is present and `sysvinit` is not a distro feature.
- `libpam`: Deny all services for the `OTHER` entries.
- `ref-classes-image`{.interpreted-text role="ref"}: Move `runtime_mapping_rename` to avoid conflict with `multilib`. See :yocto_bugs:[YOCTO #4993 \</show_bug.cgi?id=4993\>]{.title-ref} in Bugzilla for more information.
- `linux-dtb`: Use kernel build system to generate the `dtb` files.
- `kern-tools`: Switch from guilt to new `kgit-s2q` tool.
