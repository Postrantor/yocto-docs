---
subtitle: Migration notes for 4.1 (langdale)
title: Release 4.1 (langdale)
---
This section provides migration information for moving to the Yocto Project 4.1 Release (codename \"langdale\") from the prior release.

# make 4.0 is now the minimum required make version {#migration-4.1-make-4.0}

glibc now requires `make` 4.0 to build, thus it is now the version required to be installed on the build host. A new `buildtools-make`{.interpreted-text role="term"} tarball has been introduced to provide just make 4.0 for host distros without a current/working make 4.x version; if you also need other tools you can use the updated `buildtools`{.interpreted-text role="term"} tarball. For more information see `ref-manual/system-requirements:required packages for the build host`{.interpreted-text role="ref"}.

# Complementary package installation ignores recommends {#migration-4.1-complementary-deps}

When installing complementary packages (e.g. `-dev` and `-dbg` packages when building an SDK, or if you have added `dev-deps` to `IMAGE_FEATURES`{.interpreted-text role="term"}), recommends (as defined by `RRECOMMENDS`{.interpreted-text role="term"}) are no longer installed.

If you wish to double-check the contents of your images after this change, see `Checking Image / SDK Changes <migration-general-buildhistory>`{.interpreted-text role="ref"}. If needed you can explicitly install items by adding them to `IMAGE_INSTALL`{.interpreted-text role="term"} in image recipes or `TOOLCHAIN_TARGET_TASK`{.interpreted-text role="term"} for the SDK.

# dev dependencies are now recommends {#migration-4.1-dev-recommends}

The default for `${PN}-dev` package is now to use `RRECOMMENDS`{.interpreted-text role="term"} instead of `RDEPENDS`{.interpreted-text role="term"} to pull in the main package. This takes advantage of a change to complimentary package installation to not follow `RRECOMMENDS`{.interpreted-text role="term"} (as mentioned above) and for example means an SDK for an image with both openssh and dropbear components will now build successfully.

# dropbear now recommends openssh-sftp-server {#migration-4.1-dropbear-sftp}

openssh has switched the scp client to use the sftp protocol instead of scp to move files. This means scp from Fedora 36 and other current distributions will no longer be able to move files to/from a system running dropbear with no sftp server installed.

The sftp server from openssh is small (200kb uncompressed) and standalone, so adding it to the packagegroup seems to be the best way to preserve the functionality for user sanity. However, if you wish to avoid this dependency, you can either:

> A.  Use `dropbear` in `IMAGE_INSTALL`{.interpreted-text role="term"} instead of `packagegroup-core-ssh-dropbear` (or `ssh-server-dropbear` in `IMAGE_FEATURES`{.interpreted-text role="term"}), or
> B.  Add `openssh-sftp-server` to `BAD_RECOMMENDATIONS`{.interpreted-text role="term"}.

# Classes now split by usage context {#migration-4.1-classes-split}

A split directory structure has now been set up for `.bbclass` files - classes that are intended to be inherited only by recipes (e.g. `inherit` in a recipe file, `IMAGE_CLASSES`{.interpreted-text role="term"} or `KERNEL_CLASSES`{.interpreted-text role="term"}) should be in a `classes-recipe` subdirectory and classes that are intended to be inherited globally (e.g. via `INHERIT +=`, `PACKAGE_CLASSES`{.interpreted-text role="term"}, `USER_CLASSES`{.interpreted-text role="term"} or `INHERIT_DISTRO`{.interpreted-text role="term"}) should be in `classes-global`. Classes in the existing `classes` subdirectory will continue to work in any context as before.

Other than knowing where to look when manually browsing the class files, this is not likely to require any changes to your configuration. However, if in your configuration you were using some classes in the incorrect context, you will now receive an error during parsing. For example, the following in `local.conf` will now cause an error:

```
INHERIT += "testimage"
```

Since `ref-classes-testimage`{.interpreted-text role="ref"} is a class intended solely to affect image recipes, this would be correctly specified as:

```
IMAGE_CLASSES += "testimage"
```

# Missing local files in SRC_URI now triggers an error {#migration-4.1-local-file-error}

If a file referenced in `SRC_URI`{.interpreted-text role="term"} does not exist, in 4.1 this will trigger an error at parse time where previously this only triggered a warning. In the past you could ignore these warnings for example if you have multiple build configurations (e.g. for several different target machines) and there were recipes that you were not building in one of the configurations. If you have this scenario you will now need to conditionally add entries to `SRC_URI`{.interpreted-text role="term"} where they are valid, or use `COMPATIBLE_MACHINE`{.interpreted-text role="term"} / `COMPATIBLE_HOST`{.interpreted-text role="term"} to prevent the recipe from being available (and therefore avoid it being parsed) in configurations where the files aren\'t available.

# QA check changes {#migration-4.1-qa-checks}

- The `buildpaths <qa-check-buildpaths>`{.interpreted-text role="ref"} QA check is now enabled by default in `WARN_QA`{.interpreted-text role="term"}, and thus any build system paths found in output files will trigger a warning. If you see these warnings for your own recipes, for full binary reproducibility you should make the necessary changes to the recipe build to remove these paths. If you wish to disable the warning for a particular recipe you can use `INSANE_SKIP`{.interpreted-text role="term"}, or for the entire build you can adjust `WARN_QA`{.interpreted-text role="term"}. For more information, see the `buildpaths QA check <qa-check-buildpaths>`{.interpreted-text role="ref"} section.
- `do_qa_staging` now checks shebang length in all directories specified by `SYSROOT_DIRS`{.interpreted-text role="term"}, since there is a maximum length defined in the kernel. For native recipes which write scripts to the sysroot, if the shebang line in one of these scripts is too long you will get an error. This can be skipped using `INSANE_SKIP`{.interpreted-text role="term"} if necessary, but the best course of action is of course to fix the script. There is now also a `create_cmdline_shebang_wrapper` function that you can call e.g. from `do_install` (or `do_install:append`) within a recipe to create a wrapper to fix such scripts - see the `libcheck` recipe for an example usage.

# Miscellaneous changes

- `mount.blacklist` has been renamed to `mount.ignorelist` in `udev-extraconf`. If you are customising this file via `udev-extraconf` then you will need to update your `udev-extraconf` `.bbappend` as appropriate.
- `help2man-native` has been removed from implicit sysroot dependencies. If a recipe needs `help2man-native` it should now be explicitly added to `DEPENDS`{.interpreted-text role="term"} within the recipe.
- For images using systemd, the reboot watchdog timeout has been set to 60 seconds (from the upstream default of 10 minutes). If you wish to override this you can set `WATCHDOG_TIMEOUT`{.interpreted-text role="term"} to the desired timeout in seconds. Note that the same `WATCHDOG_TIMEOUT`{.interpreted-text role="term"} variable also specifies the timeout used for the `watchdog` tool (if that is being built).
- The `ref-classes-image-buildinfo`{.interpreted-text role="ref"} class now writes to `${sysconfdir}/buildinfo` instead of `${sysconfdir}/build` by default (i.e. the default value of `IMAGE_BUILDINFO_FILE`{.interpreted-text role="term"} has been changed). If you have code that reads this from images at build or runtime you will need to update it or specify your own value for `IMAGE_BUILDINFO_FILE`{.interpreted-text role="term"}.
- In the `ref-classes-archiver`{.interpreted-text role="ref"} class, the default `ARCHIVER_OUTDIR` value no longer includes the `MACHINE`{.interpreted-text role="term"} value in order to avoid the archive task running multiple times in a multiconfig setup. If you have custom code that does something with the files archived by the `ref-classes-archiver`{.interpreted-text role="ref"} class then you may need to adjust it to the new structure.
- If you are not using [systemd]{.title-ref} then udev is now configured to use labels (`LABEL` or `PARTLABEL`) to set the mount point for the device. For example:

  ```
  /run/media/rootfs-sda2
  ```

  instead of:

  ```
  /run/media/sda2
  ```
- `icu` no longer provides the `icu-config` configuration tool - upstream have indicated `icu-config` is deprecated and should no longer be used. Code with references to it will need to be updated, for example to use `pkg-config` instead.
- The `rng-tools` systemd service name has changed from `rngd` to `rng-tools`
- The `largefile` `DISTRO_FEATURES`{.interpreted-text role="term"} item has been removed, large file support is now always enabled where it was previously optional.
- The Python `zoneinfo` module is now split out to its own `python3-zoneinfo` package.
- The `PACKAGECONFIG`{.interpreted-text role="term"} option to enable wpa_supplicant in the `connman` recipe has been renamed to \"wpa-supplicant\". If you have set `PACKAGECONFIG`{.interpreted-text role="term"} for the `connman` recipe to include this option you will need to update your configuration. Related to this, the `WIRELESS_DAEMON`{.interpreted-text role="term"} variable now expects the new `wpa-supplicant` naming and affects `packagegroup-base` as well as `connman`.
- The `wpa-supplicant` recipe no longer uses a static (and stale) `defconfig` file, instead it uses the upstream version with appropriate edits for the `PACKAGECONFIG`{.interpreted-text role="term"}. If you are customising this file you will need to update your customisations.
- With the introduction of picobuild in `ref-classes-python_pep517`{.interpreted-text role="ref"}, The `PEP517_BUILD_API` variable is no longer supported. If you have any references to this variable you should remove them.

# Removed recipes {#migration-4.1-removed-recipes}

The following recipes have been removed in this release:

- `alsa-utils-scripts`: merged into alsa-utils
- `cargo-cross-canadian`: optimised out
- `lzop`: obsolete, unmaintained upstream
- `linux-yocto (5.10)`: 5.15 and 5.19 are currently provided
- `rust-cross`: optimised out
- `rust-crosssdk`: optimised out
- `rust-tools-cross-canadian`: optimised out
- `xf86-input-keyboard`: obsolete (replaced by libinput/evdev)
