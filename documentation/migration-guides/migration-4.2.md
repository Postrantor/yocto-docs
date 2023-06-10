---
tip: translate by openai@2023-06-07 22:26:54
...
---
subtitle: Migration notes for 4.2 (mickledore)
title: Release 4.2 (mickledore)
-------------------------------

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

> BitBake 和 OpenEmbedded-Core 现在要求使用 Python 3.8 或更新的版本，因此需要使用提供至少此版本的发行版，或安装 `buildtools` tarball。

# gcc 8.0 is now the minimum required GNU C compiler version {#migration-4.2-gcc-8.0}

This version, released in 2018, is a minimum requirement to build the `mesa-native` recipe and as the latter is in the default dependency chain when building QEMU this has now been made a requirement for all builds.

> 这个版本，发布于 2018 年，是构建 `mesa-native` 配方的最低要求，因为后者是构建 QEMU 时的默认依赖链，因此，这现在已经成为所有构建的要求。

In the event that your host distribution does not provide this or a newer version of gcc, you can install a `buildtools-extended`{.interpreted-text role="term"} tarball.

> 如果您的主机发行版本没有提供这个或更新版本的 gcc，您可以安装 `buildtools-extended` 压缩包。

# Fetching the NVD vulnerability database through the 2.0 API {#migration-4.2-new-nvd-api}

This new version adds a new fetcher for the NVD database using the 2.0 API, as the 1.0 API will be retired in 2023.

The implementation changes as little as possible, keeping the current database format (but using a different database file for the transition period), with a notable exception of not using the META table.

> 实施尽可能少地改变，保持当前的数据库格式（但在过渡期使用不同的数据库文件），其中一个显著的例外是不使用 META 表。

Here are minor changes that you may notice:

- The database starts in 1999 instead of 2002
- The complete fetch is longer (30 minutes typically)

# Rust: mandatory checksums for crates {#migration-4.2-rust-crate-checksums}

This release now supports checksums for Rust crates and makes them mandatory for each crate in a recipe. See :yocto\_[git:%60python3_bcrypt](git:%60python3_bcrypt) recipe changes \</poky/commit/?h=mickledore&id=0dcb5ab3462fdaaf1646b05a00c7150eea711a9a\>\` for example.

> 这个版本现在支持 Rust 架构的校验和，并且要求每个架构在配方中都必须有校验和。例如，参见:yocto_[git:%60python3_bcrypt](git:%60python3_bcrypt) 配方变更\</poky/commit/?h=mickledore&id=0dcb5ab3462fdaaf1646b05a00c7150eea711a9a\>\`。

The `cargo-update-recipe-crates` utility :yocto\_[git:%60has](git:%60has) been extended \</poky/commit/?h=mickledore&id=eef7fbea2c5bf59369390be4d5efa915591b7b22\>[ to include such checksums. So, in case you need to add the list of checksums to a recipe just inheriting the :ref:\`ref-classes-cargo]{.title-ref} class so far, you can follow these steps:

> `cargo-update-recipe-crates` 实用程序：yocto_[git:`has`](git:%60has%60)已经扩展到包括这些校验和。因此，如果您需要将校验和列表添加到目前仅继承:ref:`ref-classes-cargo` 类的配方中，可以按照以下步骤操作：

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

> 现在，将 lib 目录添加到扩展 Python 库代码的层不再会被 OE-Core 自动添加 `BBPATH`，而是应使用此指令。请注意，这些指令是立即操作，因此它们比当前基于 BBPATH 的方法更快地使模块可用。

For more information, see `bitbake-user-manual/bitbake-user-manual-metadata:extending python library code`{.interpreted-text role="ref"}.

# Removed variables {#migration-4.2-removed-variables}

The following variables have been removed:

- `SERIAL_CONSOLE`, deprecated since version 2.6, replaced by `SERIAL_CONSOLES`{.interpreted-text role="term"}.
- `PACKAGEBUILDPKGD`, a mostly internal variable in the ref:[ref-classes-package]{.title-ref} class was rarely used to customise packaging. If you were using this in your custom recipes or bbappends, you will need to switch to using `PACKAGE_PREPROCESS_FUNCS`{.interpreted-text role="term"} or `PACKAGESPLITFUNCS`{.interpreted-text role="term"} instead.

> - `PACKAGEBUILDPKGD`，在 ref：[ref-classes-package]{.title-ref}类中主要是内部变量，很少用于定制打包。如果您在自定义配方或 bbappends 中使用此变量，您将需要改用 `PACKAGE_PREPROCESS_FUNCS`{.interpreted-text role="term"}或 `PACKAGESPLITFUNCS`{.interpreted-text role="term"}。

# Removed recipes {#migration-4.2-removed-recipes}

The following recipes have been removed in this release:

- `python3-picobuild`: after switching to `python3-build`
- `python3-strict-rfc3339`: unmaintained and not needed by anything in :oe\_[git:%60openembedded-core](git:%60openembedded-core) \</openembedded-core\>[ or :oe_git:\`meta-openembedded \</meta-openembedded\>]{.title-ref}.

> python3-strict-rfc3339：在 openembedded-core（Git：openembedded-core）或 meta-openembedded（Git：meta-openembedded）中都不需要且未维护。

- `linux-yocto`: removed version 5.19 recipes (6.1 and 5.15 still provided)

# Removed classes {#migration-4.2-removed-classes}

The following classes have been removed in this release:

- `rust-bin`: no longer used
- `package_tar`: could not be used for actual packaging, and thus not particularly useful.

# LAYERSERIES_COMPAT for custom layers and devtool workspace

Some layer maintainers have been setting `LAYERSERIES_COMPAT`{.interpreted-text role="term"} in their layer\'s `conf/layer.conf` to the value of `LAYERSERIES_CORENAMES` to effectively bypass the compatibility check - this is no longer permitted. Layer maintainers should set `LAYERSERIES_COMPAT`{.interpreted-text role="term"} appropriately to help users understand the compatibility status of the layer.

> 一些层维护者已经将他们层的 `conf/layer.conf` 中的 `LAYERSERIES_COMPAT`{.interpreted-text role="term"}设置为 `LAYERSERIES_CORENAMES` 的值以有效地绕过兼容性检查-这不再被允许。层维护者应该适当设置 `LAYERSERIES_COMPAT`{.interpreted-text role="term"}以帮助用户了解层的兼容性状态。

Additionally, the `LAYERSERIES_COMPAT`{.interpreted-text role="term"} value for the devtool workspace layer is now set at the time of creation, thus if you upgrade with the workspace layer enabled and you wish to retain it, you will need to manually update the `LAYERSERIES_COMPAT`{.interpreted-text role="term"} value in `workspace/conf/layer.conf` (or remove the path from `BBLAYERS`{.interpreted-text role="term"} in `conf/bblayers.conf` and delete/move the `workspace` directory out of the way if you no longer need it).

> 此外，devtool 工作空间层的“LAYERSERIES_COMPAT”值现在会在创建时设置，因此，如果您在启用了工作空间层的情况下升级，并希望保留它，则需要在“workspace/conf/layer.conf”中手动更新“LAYERSERIES_COMPAT”值（或者从“conf/bblayers.conf”中的“BBLAYERS”中删除路径，如果您不再需要它，则删除/移动“workspace”目录）。

# runqemu now limits slirp host port forwarding to localhost {#migration-4.2-runqemu-slirp}

With default slirp port forwarding configuration in runqemu, qemu previously listened on TCP ports 2222 and 2323 on all IP addresses available on the build host. Most use cases with runqemu only need it for localhost and it is not safe to run qemu images with root login without password enabled and listening on all available, possibly Internet reachable network interfaces. Thus, in this release we limit qemu port forwarding to localhost (127.0.0.1).

> 在 runqemu 中默认的 slirp 端口转发配置中，QEMU 以前在构建主机上可用的所有 IP 地址上侦听 TCP 端口 2222 和 2323。大多数使用 runqemu 的情况只需要它用于本地主机，而不安全地在可能被互联网访问的所有可用网络接口上运行 QEMU 映像，而不需要启用 root 登录密码。因此，在本次发布中，我们将 QEMU 端口转发限制为本地主机（127.0.0.1）。

However, if you need the qemu machine to be reachable from the network, then it can be enabled via `conf/local.conf` or machine config variable `QB_SLIRP_OPT`:

> 如果你需要 qemu 机器可以从网络访问，可以通过 `conf/local.conf` 或机器配置变量 `QB_SLIRP_OPT` 来启用：

```
QB_SLIRP_OPT = "-netdev user,id=net0,hostfwd=tcp::2222-:22"
```

# Patch QA checks {#migration-4.2-patch-qa}

The QA checks for patch fuzz and Upstream-Status have been reworked slightly in this release. The Upstream-Status checking is now configurable from `WARN_QA`{.interpreted-text role="term"} / `ERROR_QA`{.interpreted-text role="term"} (`patch-status-core` for the core layer, and `patch-status-noncore` for other layers).

> 在这次发布中，补丁模糊检查和上游状态检查稍微做了一些改动。现在可以从 `WARN_QA`{.interpreted-text role="term"} / `ERROR_QA`{.interpreted-text role="term"} (`patch-status-core` 用于核心层，`patch-status-noncore` 用于其他层)中配置上游状态检查。

The `patch-fuzz` and `patch-status-core` checks are now in the default value of `ERROR_QA`{.interpreted-text role="term"} so that they will cause the build to fail if triggered. If you prefer to avoid this you will need to adjust the value of `ERROR_QA`{.interpreted-text role="term"} in your configuration as desired.

> 检查 `patch-fuzz` 和 `patch-status-core` 现在已经是 `ERROR_QA`{.interpreted-text role="term"}的默认值，因此如果被触发就会导致构建失败。如果您希望避免这种情况，您需要根据需要调整配置中的 `ERROR_QA`{.interpreted-text role="term"}的值。

# Native/nativesdk mesa usage and graphics drivers {#migration-4.2-mesa}

This release includes mesa 23.0, and with that mesa release it is not longer possible to use drivers from the host system, as mesa upstream has added strict checks for matching builds between drivers and libraries that load them.

> 此发行版包括 Mesa 23.0，并且随着 Mesa 发行版的发布，不再可以使用来自主机系统的驱动程序，因为 Mesa 上游已经添加了严格的检查来匹配驱动程序和加载它们的库之间的构建。

This is particularly relevant when running QEMU built within the build system. A check has been added to runqemu so that there is a helpful error when there is no native/nativesdk opengl/virgl support available.

> 这在使用构建系统构建的 QEMU 时尤为重要。我们已经在 runqemu 中添加了一个检查，以便在没有本机/本机 SDK opengl/virgl 支持时提供有用的错误。

To support this, a number of drivers have been enabled when building `mesa-native`. The one major dependency pulled in by this change is `llvm-native` which will add a few minutes to the build on a modern machine. If this is undesirable, you can set the value of `DISTRO_FEATURES_NATIVE`{.interpreted-text role="term"} in your configuration such that `opengl` is excluded.

> 为了支持这一点，在构建'mesa-native'时启用了多个驱动程序。这一变化带来的主要依赖项是 `llvm-native`，它将在现代机器上增加几分钟的构建时间。如果这不可取，您可以在配置中设置 `DISTRO_FEATURES_NATIVE`{.interpreted-text role="term"}的值，以便排除 `opengl`。

# Miscellaneous changes {#migration-4.2-misc-changes}

- The `IMAGE_NAME`{.interpreted-text role="term"} variable is now set based on `IMAGE_LINK_NAME`{.interpreted-text role="term"}. This means that if you are setting `IMAGE_LINK_NAME`{.interpreted-text role="term"} to \"\" to disable unversioned image symlink creation, you also now need to set `IMAGE_NAME`{.interpreted-text role="term"} to still have a reasonable value e.g.:

> 变量 `IMAGE_NAME`{.interpreted-text role="term"}现在基于 `IMAGE_LINK_NAME`{.interpreted-text role="term"}设置。这意味着如果你将 `IMAGE_LINK_NAME`{.interpreted-text role="term"}设置为“”以禁用未版本化的图像符号链接创建，你也现在需要将 `IMAGE_NAME`{.interpreted-text role="term"}设置为仍然具有合理值，例如：

```
IMAGE_LINK_NAME = ""
IMAGE_NAME = "${IMAGE_BASENAME}${IMAGE_MACHINE_SUFFIX}${IMAGE_VERSION_SUFFIX}"
```

- In `/etc/os-release`, the `VERSION_CODENAME` field is now used instead of `DISTRO_CODENAME` (though its value is still set from the `DISTRO_CODENAME`{.interpreted-text role="term"} variable) for better conformance to standard os-release usage. If you have runtime code reading this from `/etc/os-release` it may need to be updated.

> 在/etc/os-release 中，VERSION_CODENAME 字段现在被用来代替 DISTRO_CODENAME（尽管它的值仍然是从 DISTRO_CODENAME 变量设置的）以更好地符合标准 os-release 的使用。如果您有从/etc/os-release 读取的运行时代码，则可能需要进行更新。

- The kmod recipe now enables OpenSSL support by default in order to support module signing. If you do not need this and wish to reclaim some space/avoid the dependency you should set `PACKAGECONFIG`{.interpreted-text role="term"} in a kmod bbappend (or `PACKAGECONFIG:pn-kmod` at the configuration level) to exclude `openssl`.

> 现在，kmod 配方默认启用 OpenSSL 支持以支持模块签名。如果您不需要此功能并希望节省空间/避免依赖关系，则应在 kmod bbappend 中设置 `PACKAGECONFIG`（或在配置级别上设置 `PACKAGECONFIG:pn-kmod`）以排除 `openssl`。

- The `OEBasic` signature handler (see `BB_SIGNATURE_HANDLER`{.interpreted-text role="term"}) has been removed. It is unlikely that you would have selected to use this, but if you have you will need to remove this setting.

> OEBasic 签名处理程序（参见 BB_SIGNATURE_HANDLER）已被移除。您不太可能选择使用此项，但如果您已经使用，则需要删除此设置。

- The `ref-classes-package`{.interpreted-text role="ref"} class now checks if package names conflict via `PKG:${PN}` override during `do_package`. If you receive the associated error you will need to address the `PKG`{.interpreted-text role="term"} usage so that the conflict is resolved.

> - 现在，`ref-classes-package`{.interpreted-text role="ref"} 类在 `do_package` 时通过 `PKG:${PN}` 覆盖检查包名是否存在冲突。如果您收到相关错误，您需要解决 `PKG`{.interpreted-text role="term"} 的使用，以解决冲突。

- openssh no longer uses `RRECOMMENDS`{.interpreted-text role="term"} to pull in `rng-tools`, since rngd is no longer needed as of Linux kernel 5.6. If you still need `rng-tools` installed for other reasons, you should add `rng-tools` explicitly to your image. If you additionally need rngd to be started as a service you will also need to add the `rng-tools-service` package as that has been split out.

> OpenSSH 不再使用 RRECOMMENDS 来安装 rng-tools，因为自 Linux 内核 5.6 开始，不再需要 rngd。如果您仍然需要安装 rng-tools，则应显式将 rng-tools 添加到您的映像中。如果您还需要将 rngd 作为服务启动，您还需要添加“rng-tools-service”包，因为该包已被分离出来。

- The cups recipe no longer builds with the web interface enabled, saving \~1.8M of space in the final image. If you wish to enable it, you should set `PACKAGECONFIG`{.interpreted-text role="term"} in a cups bbappend (or `PACKAGECONFIG:pn-cups` at the configuration level) to include `webif`.

> 茶杯配方不再使用 Web 界面构建，可以节省最终图像的 1.8M 空间。如果您希望启用它，您应该在 cups bbappend 中设置 PACKAGECONFIG（或在配置级别设置 PACKAGECONFIG:pn-cups），以包括 webif。

- The `ref-classes-scons`{.interpreted-text role="ref"} class now passes a `MAXLINELENGTH` argument to scons in order to fix an issue with scons and command line lengths when ccache is enabled. However, some recipes may be using older scons versions which don\'t support this argument. If that is the case you can set the following in the recipe in order to disable this:

> `ref-classes-scons` 类现在传递一个 `MAXLINELENGTH` 参数给 scons 来解决 ccache 启用时 scons 和命令行长度的问题。但是，一些食谱可能正在使用较旧的 scons 版本，它们不支持此参数。如果是这种情况，您可以在食谱中设置以下内容以禁用此功能：

```
SCONS_MAXLINELENGTH = ""
```
