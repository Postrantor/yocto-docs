---
subtitle: Migration notes for 4.2 (mickledore)
title: Release 4.2 (mickledore)
---
This section provides migration information for moving to the Yocto Project 4.2 Release (codename \"mickledore\") from the prior release.

# Supported distributions {#migration-4.2-supported-distributions}

This release supports running BitBake on new GNU/Linux distributions:

- Fedora 36 and 37
- AlmaLinux 8.7 and 9.1
- OpenSuse 15.4

On the other hand, some earlier distributions are no longer supported:

- Debian 10.x
- Fedora 34 and 35
- AlmaLinux 8.5

See `all supported distributions <system-requirements-supported-distros>`{.interpreted-text role="ref"}.

# Python 3.8 is now the minimum required Python version version {#migration-4.2-python-3.8}

BitBake and OpenEmbedded-Core now require Python 3.8 or newer, making it a requirement to use a distribution providing at least this version, or to install a `buildtools`{.interpreted-text role="term"} tarball.

# gcc 8.0 is now the minimum required GNU C compiler version {#migration-4.2-gcc-8.0}

This version, released in 2018, is a minimum requirement to build the `mesa-native` recipe and as the latter is in the default dependency chain when building QEMU this has now been made a requirement for all builds.

In the event that your host distribution does not provide this or a newer version of gcc, you can install a `buildtools-extended`{.interpreted-text role="term"} tarball.

# Fetching the NVD vulnerability database through the 2.0 API {#migration-4.2-new-nvd-api}

This new version adds a new fetcher for the NVD database using the 2.0 API, as the 1.0 API will be retired in 2023.

The implementation changes as little as possible, keeping the current database format (but using a different database file for the transition period), with a notable exception of not using the META table.

Here are minor changes that you may notice:

- The database starts in 1999 instead of 2002
- The complete fetch is longer (30 minutes typically)

# Rust: mandatory checksums for crates {#migration-4.2-rust-crate-checksums}

This release now supports checksums for Rust crates and makes them mandatory for each crate in a recipe. See :yocto\_[git:%60python3_bcrypt](git:%60python3_bcrypt) recipe changes \</poky/commit/?h=mickledore&id=0dcb5ab3462fdaaf1646b05a00c7150eea711a9a\>\` for example.

The `cargo-update-recipe-crates` utility :yocto\_[git:%60has](git:%60has) been extended \</poky/commit/?h=mickledore&id=eef7fbea2c5bf59369390be4d5efa915591b7b22\>[ to include such checksums. So, in case you need to add the list of checksums to a recipe just inheriting the :ref:\`ref-classes-cargo]{.title-ref} class so far, you can follow these steps:

1. Make the recipe inherit `ref-classes-cargo-update-recipe-crates`{.interpreted-text role="ref"}
2. Remove all `crate://` lines from the recipe
3. Create an empty `${BPN}-crates.inc` file and make your recipe require it
4. Execute `bitbake -c update_crates your_recipe`
5. Copy and paste the output of BitBake about the missing checksums into the `${BPN}-crates.inc` file.

# Python library code extensions {#migration-4.2-addpylib}

BitBake in this release now supports a new `addpylib` directive to enable Python libraries within layers.

This directive should be added to your layer configuration as in the below example from `meta/conf/layer.conf`:

```
addpylib ${LAYERDIR}/lib oe
```

Layers currently adding a lib directory to extend Python library code should now use this directive as `BBPATH`{.interpreted-text role="term"} is not going to be added automatically by OE-Core in future. Note that the directives are immediate operations, so it does make modules available for use sooner than the current BBPATH-based approach.

For more information, see `bitbake-user-manual/bitbake-user-manual-metadata:extending python library code`{.interpreted-text role="ref"}.

# Removed variables {#migration-4.2-removed-variables}

The following variables have been removed:

- `SERIAL_CONSOLE`, deprecated since version 2.6, replaced by `SERIAL_CONSOLES`{.interpreted-text role="term"}.
- `PACKAGEBUILDPKGD`, a mostly internal variable in the ref:[ref-classes-package]{.title-ref} class was rarely used to customise packaging. If you were using this in your custom recipes or bbappends, you will need to switch to using `PACKAGE_PREPROCESS_FUNCS`{.interpreted-text role="term"} or `PACKAGESPLITFUNCS`{.interpreted-text role="term"} instead.

# Removed recipes {#migration-4.2-removed-recipes}

The following recipes have been removed in this release:

- `python3-picobuild`: after switching to `python3-build`
- `python3-strict-rfc3339`: unmaintained and not needed by anything in :oe\_[git:%60openembedded-core](git:%60openembedded-core) \</openembedded-core\>[ or :oe_git:\`meta-openembedded \</meta-openembedded\>]{.title-ref}.
- `linux-yocto`: removed version 5.19 recipes (6.1 and 5.15 still provided)

# Removed classes {#migration-4.2-removed-classes}

The following classes have been removed in this release:

- `rust-bin`: no longer used
- `package_tar`: could not be used for actual packaging, and thus not particularly useful.

# LAYERSERIES_COMPAT for custom layers and devtool workspace

Some layer maintainers have been setting `LAYERSERIES_COMPAT`{.interpreted-text role="term"} in their layer\'s `conf/layer.conf` to the value of `LAYERSERIES_CORENAMES` to effectively bypass the compatibility check - this is no longer permitted. Layer maintainers should set `LAYERSERIES_COMPAT`{.interpreted-text role="term"} appropriately to help users understand the compatibility status of the layer.

Additionally, the `LAYERSERIES_COMPAT`{.interpreted-text role="term"} value for the devtool workspace layer is now set at the time of creation, thus if you upgrade with the workspace layer enabled and you wish to retain it, you will need to manually update the `LAYERSERIES_COMPAT`{.interpreted-text role="term"} value in `workspace/conf/layer.conf` (or remove the path from `BBLAYERS`{.interpreted-text role="term"} in `conf/bblayers.conf` and delete/move the `workspace` directory out of the way if you no longer need it).

# runqemu now limits slirp host port forwarding to localhost {#migration-4.2-runqemu-slirp}

With default slirp port forwarding configuration in runqemu, qemu previously listened on TCP ports 2222 and 2323 on all IP addresses available on the build host. Most use cases with runqemu only need it for localhost and it is not safe to run qemu images with root login without password enabled and listening on all available, possibly Internet reachable network interfaces. Thus, in this release we limit qemu port forwarding to localhost (127.0.0.1).

However, if you need the qemu machine to be reachable from the network, then it can be enabled via `conf/local.conf` or machine config variable `QB_SLIRP_OPT`:

```
QB_SLIRP_OPT = "-netdev user,id=net0,hostfwd=tcp::2222-:22"
```

# Patch QA checks {#migration-4.2-patch-qa}

The QA checks for patch fuzz and Upstream-Status have been reworked slightly in this release. The Upstream-Status checking is now configurable from `WARN_QA`{.interpreted-text role="term"} / `ERROR_QA`{.interpreted-text role="term"} (`patch-status-core` for the core layer, and `patch-status-noncore` for other layers).

The `patch-fuzz` and `patch-status-core` checks are now in the default value of `ERROR_QA`{.interpreted-text role="term"} so that they will cause the build to fail if triggered. If you prefer to avoid this you will need to adjust the value of `ERROR_QA`{.interpreted-text role="term"} in your configuration as desired.

# Native/nativesdk mesa usage and graphics drivers {#migration-4.2-mesa}

This release includes mesa 23.0, and with that mesa release it is not longer possible to use drivers from the host system, as mesa upstream has added strict checks for matching builds between drivers and libraries that load them.

This is particularly relevant when running QEMU built within the build system. A check has been added to runqemu so that there is a helpful error when there is no native/nativesdk opengl/virgl support available.

To support this, a number of drivers have been enabled when building `mesa-native`. The one major dependency pulled in by this change is `llvm-native` which will add a few minutes to the build on a modern machine. If this is undesirable, you can set the value of `DISTRO_FEATURES_NATIVE`{.interpreted-text role="term"} in your configuration such that `opengl` is excluded.

# Miscellaneous changes {#migration-4.2-misc-changes}

- The `IMAGE_NAME`{.interpreted-text role="term"} variable is now set based on `IMAGE_LINK_NAME`{.interpreted-text role="term"}. This means that if you are setting `IMAGE_LINK_NAME`{.interpreted-text role="term"} to \"\" to disable unversioned image symlink creation, you also now need to set `IMAGE_NAME`{.interpreted-text role="term"} to still have a reasonable value e.g.:

  ```
  IMAGE_LINK_NAME = ""
  IMAGE_NAME = "${IMAGE_BASENAME}${IMAGE_MACHINE_SUFFIX}${IMAGE_VERSION_SUFFIX}"
  ```
- In `/etc/os-release`, the `VERSION_CODENAME` field is now used instead of `DISTRO_CODENAME` (though its value is still set from the `DISTRO_CODENAME`{.interpreted-text role="term"} variable) for better conformance to standard os-release usage. If you have runtime code reading this from `/etc/os-release` it may need to be updated.
- The kmod recipe now enables OpenSSL support by default in order to support module signing. If you do not need this and wish to reclaim some space/avoid the dependency you should set `PACKAGECONFIG`{.interpreted-text role="term"} in a kmod bbappend (or `PACKAGECONFIG:pn-kmod` at the configuration level) to exclude `openssl`.
- The `OEBasic` signature handler (see `BB_SIGNATURE_HANDLER`{.interpreted-text role="term"}) has been removed. It is unlikely that you would have selected to use this, but if you have you will need to remove this setting.
- The `ref-classes-package`{.interpreted-text role="ref"} class now checks if package names conflict via `PKG:${PN}` override during `do_package`. If you receive the associated error you will need to address the `PKG`{.interpreted-text role="term"} usage so that the conflict is resolved.
- openssh no longer uses `RRECOMMENDS`{.interpreted-text role="term"} to pull in `rng-tools`, since rngd is no longer needed as of Linux kernel 5.6. If you still need `rng-tools` installed for other reasons, you should add `rng-tools` explicitly to your image. If you additionally need rngd to be started as a service you will also need to add the `rng-tools-service` package as that has been split out.
- The cups recipe no longer builds with the web interface enabled, saving \~1.8M of space in the final image. If you wish to enable it, you should set `PACKAGECONFIG`{.interpreted-text role="term"} in a cups bbappend (or `PACKAGECONFIG:pn-cups` at the configuration level) to include `webif`.
- The `ref-classes-scons`{.interpreted-text role="ref"} class now passes a `MAXLINELENGTH` argument to scons in order to fix an issue with scons and command line lengths when ccache is enabled. However, some recipes may be using older scons versions which don\'t support this argument. If that is the case you can set the following in the recipe in order to disable this:

  ```
  SCONS_MAXLINELENGTH = ""
  ```
