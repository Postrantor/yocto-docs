---
tip: translate by openai@2023-06-07 22:22:57
...
---
subtitle: Migration notes for 4.1 (langdale)
title: Release 4.1 (langdale)
---
This section provides migration information for moving to the Yocto Project 4.1 Release (codename \"langdale\") from the prior release.

# make 4.0 is now the minimum required make version {#migration-4.1-make-4.0}


glibc now requires `make` 4.0 to build, thus it is now the version required to be installed on the build host. A new `buildtools-make`{.interpreted-text role="term"} tarball has been introduced to provide just make 4.0 for host distros without a current/working make 4.x version; if you also need other tools you can use the updated `buildtools`{.interpreted-text role="term"} tarball. For more information see `ref-manual/system-requirements:required packages for the build host`{.interpreted-text role="ref"}.

> 现在，glibc需要make 4.0来构建，因此它现在是在构建主机上所需安装的版本。引入了一个新的buildtools-make tarball，为没有当前/工作make 4.x版本的主机发行版提供make 4.0；如果您还需要其他工具，您可以使用更新的buildtools tarball。有关更多信息，请参见ref-manual / system-requirements：构建主机所需的软件包。

# Complementary package installation ignores recommends {#migration-4.1-complementary-deps}


When installing complementary packages (e.g. `-dev` and `-dbg` packages when building an SDK, or if you have added `dev-deps` to `IMAGE_FEATURES`{.interpreted-text role="term"}), recommends (as defined by `RRECOMMENDS`{.interpreted-text role="term"}) are no longer installed.

> 当安装补充软件包（例如在构建SDK时安装`-dev`和`-dbg`软件包，或者如果您已将`dev-deps`添加到`IMAGE_FEATURES`{.interpreted-text role="term"}）时，将不再安装推荐的软件包（如`RRECOMMENDS`{.interpreted-text role="term"}定义的）。


If you wish to double-check the contents of your images after this change, see `Checking Image / SDK Changes <migration-general-buildhistory>`{.interpreted-text role="ref"}. If needed you can explicitly install items by adding them to `IMAGE_INSTALL`{.interpreted-text role="term"} in image recipes or `TOOLCHAIN_TARGET_TASK`{.interpreted-text role="term"} for the SDK.

> 如果您希望在此更改后双重检查图像的内容，请参阅“检查图像/ SDK更改<migration-general-buildhistory>”。如果需要，您可以通过将它们添加到图像配方中的`IMAGE_INSTALL`或SDK的`TOOLCHAIN_TARGET_TASK`中来显式安装项目。

# dev dependencies are now recommends {#migration-4.1-dev-recommends}


The default for `${PN}-dev` package is now to use `RRECOMMENDS`{.interpreted-text role="term"} instead of `RDEPENDS`{.interpreted-text role="term"} to pull in the main package. This takes advantage of a change to complimentary package installation to not follow `RRECOMMENDS`{.interpreted-text role="term"} (as mentioned above) and for example means an SDK for an image with both openssh and dropbear components will now build successfully.

> 默认情况下，`${PN}-dev`包现在使用`RRECOMMENDS`（如上所述）而不是`RDEPENDS`来拉取主要包。这利用了配套软件安装的变化，不再遵循`RRECOMMENDS`，例如，具有openssh和dropbear组件的映像现在可以成功构建。

# dropbear now recommends openssh-sftp-server {#migration-4.1-dropbear-sftp}


openssh has switched the scp client to use the sftp protocol instead of scp to move files. This means scp from Fedora 36 and other current distributions will no longer be able to move files to/from a system running dropbear with no sftp server installed.

> OpenSSH 已经将SCP客户端切换到使用SFTP协议而不是SCP来移动文件。这意味着来自Fedora 36和其他当前发行版的SCP将无法将文件移动到/从未安装sftp服务器的Dropbear系统。


The sftp server from openssh is small (200kb uncompressed) and standalone, so adding it to the packagegroup seems to be the best way to preserve the functionality for user sanity. However, if you wish to avoid this dependency, you can either:

> OpenSSH 的 SFTP 服务器很小（未压缩的 200kb），且独立运行，因此将其添加到软件包组似乎是维护用户体验的最佳方式。但是，如果您希望避免这种依赖关系，可以：

> A.  Use `dropbear` in `IMAGE_INSTALL`{.interpreted-text role="term"} instead of `packagegroup-core-ssh-dropbear` (or `ssh-server-dropbear` in `IMAGE_FEATURES`{.interpreted-text role="term"}), or
> B.  Add `openssh-sftp-server` to `BAD_RECOMMENDATIONS`{.interpreted-text role="term"}.

# Classes now split by usage context {#migration-4.1-classes-split}


A split directory structure has now been set up for `.bbclass` files - classes that are intended to be inherited only by recipes (e.g. `inherit` in a recipe file, `IMAGE_CLASSES`{.interpreted-text role="term"} or `KERNEL_CLASSES`{.interpreted-text role="term"}) should be in a `classes-recipe` subdirectory and classes that are intended to be inherited globally (e.g. via `INHERIT +=`, `PACKAGE_CLASSES`{.interpreted-text role="term"}, `USER_CLASSES`{.interpreted-text role="term"} or `INHERIT_DISTRO`{.interpreted-text role="term"}) should be in `classes-global`. Classes in the existing `classes` subdirectory will continue to work in any context as before.

> 现在已经为`.bbclass`文件设置了一个分割的目录结构 - 只有在配方文件（例如在配方文件中的`inherit`，`IMAGE_CLASSES`或`KERNEL_CLASSES`）中继承的类应放在`classes-recipe`子目录中，而全局继承（例如通过`INHERIT +=`，`PACKAGE_CLASSES`，`USER_CLASSES`或`INHERIT_DISTRO`）的类应放在`classes-global`中。现有`classes`子目录中的类仍将在任何情况下继续如前工作。


Other than knowing where to look when manually browsing the class files, this is not likely to require any changes to your configuration. However, if in your configuration you were using some classes in the incorrect context, you will now receive an error during parsing. For example, the following in `local.conf` will now cause an error:

> 除了知道在手动浏览类文件时应该查看哪里之外，这不太可能需要对您的配置进行任何更改。但是，如果您在配置中以错误的上下文使用某些类，则在解析时将收到错误。例如，以下`local.conf`中的内容现在将导致错误：

```
INHERIT += "testimage"
```


Since `ref-classes-testimage`{.interpreted-text role="ref"} is a class intended solely to affect image recipes, this would be correctly specified as:

> 由于`ref-classes-testimage`{.interpreted-text role="ref"}是一个专门用于影响图像配方的类，因此正确的指定方式为：

```
IMAGE_CLASSES += "testimage"
```

# Missing local files in SRC_URI now triggers an error {#migration-4.1-local-file-error}


If a file referenced in `SRC_URI`{.interpreted-text role="term"} does not exist, in 4.1 this will trigger an error at parse time where previously this only triggered a warning. In the past you could ignore these warnings for example if you have multiple build configurations (e.g. for several different target machines) and there were recipes that you were not building in one of the configurations. If you have this scenario you will now need to conditionally add entries to `SRC_URI`{.interpreted-text role="term"} where they are valid, or use `COMPATIBLE_MACHINE`{.interpreted-text role="term"} / `COMPATIBLE_HOST`{.interpreted-text role="term"} to prevent the recipe from being available (and therefore avoid it being parsed) in configurations where the files aren\'t available.

> 如果`SRC_URI`{.interpreted-text role="term"}中引用的文件不存在，在4.1中，这将在解析时触发错误，而以前只会触发警告。过去，您可以忽略这些警告，例如如果您有多个构建配置（例如用于多个不同的目标机），并且有一些您不在其中一个配置中构建的配方。如果您有这种情况，您现在需要有条件地添加条目到`SRC_URI`{.interpreted-text role="term"}，以便它们在有效的情况下，或使用`COMPATIBLE_MACHINE`{.interpreted-text role="term"} / `COMPATIBLE_HOST`{.interpreted-text role="term"}来防止配方在文件不可用的配置中可用（从而避免它被解析）。

# QA check changes {#migration-4.1-qa-checks}


- The `buildpaths <qa-check-buildpaths>`{.interpreted-text role="ref"} QA check is now enabled by default in `WARN_QA`{.interpreted-text role="term"}, and thus any build system paths found in output files will trigger a warning. If you see these warnings for your own recipes, for full binary reproducibility you should make the necessary changes to the recipe build to remove these paths. If you wish to disable the warning for a particular recipe you can use `INSANE_SKIP`{.interpreted-text role="term"}, or for the entire build you can adjust `WARN_QA`{.interpreted-text role="term"}. For more information, see the `buildpaths QA check <qa-check-buildpaths>`{.interpreted-text role="ref"} section.

> - 现在在`WARN_QA`{.interpreted-text role="term"}中默认启用了`buildpaths <qa-check-buildpaths>`{.interpreted-text role="ref"} QA 检查，因此任何在输出文件中发现的构建系统路径都会引发警告。如果您为自己的配方看到这些警告，为了完全的二进制可重现性，您应该对配方构建做出必要的更改以删除这些路径。如果您希望为特定配方禁用警告，可以使用`INSANE_SKIP`{.interpreted-text role="term"}，或者对整个构建可以调整`WARN_QA`{.interpreted-text role="term"}。有关更多信息，请参见`buildpaths QA 检查 <qa-check-buildpaths>`{.interpreted-text role="ref"}部分。

- `do_qa_staging` now checks shebang length in all directories specified by `SYSROOT_DIRS`{.interpreted-text role="term"}, since there is a maximum length defined in the kernel. For native recipes which write scripts to the sysroot, if the shebang line in one of these scripts is too long you will get an error. This can be skipped using `INSANE_SKIP`{.interpreted-text role="term"} if necessary, but the best course of action is of course to fix the script. There is now also a `create_cmdline_shebang_wrapper` function that you can call e.g. from `do_install` (or `do_install:append`) within a recipe to create a wrapper to fix such scripts - see the `libcheck` recipe for an example usage.

> `- `do_qa_staging`现在检查由`SYSROOT_DIRS`指定的所有目录中的shebang长度，因为内核中有一个最大长度的定义。 对于将脚本写入sysroot的本机配方，如果其中一个脚本的shebang行太长，您将得到一个错误。 如果需要，可以使用`INSANE_SKIP`跳过此操作，但最佳做法当然是修复脚本。 现在还有一个`create_cmdline_shebang_wrapper`函数，您可以从配方中的`do_install`（或`do_install：append`）调用它来创建一个包装器来修复此类脚本-有关示例用法，请参见`libcheck`配方。

# Miscellaneous changes


- `mount.blacklist` has been renamed to `mount.ignorelist` in `udev-extraconf`. If you are customising this file via `udev-extraconf` then you will need to update your `udev-extraconf` `.bbappend` as appropriate.

> mount.blacklist已经在udev-extraconf中更名为mount.ignorelist。如果您正在通过udev-extraconf自定义此文件，则需要相应地更新您的udev-extraconf.bbappend。

- `help2man-native` has been removed from implicit sysroot dependencies. If a recipe needs `help2man-native` it should now be explicitly added to `DEPENDS`{.interpreted-text role="term"} within the recipe.

> - `help2man-native` 已从隐式的系统根依赖项中移除。如果配方需要 `help2man-native`，现在应该在配方中的`DEPENDS`中明确添加。

- For images using systemd, the reboot watchdog timeout has been set to 60 seconds (from the upstream default of 10 minutes). If you wish to override this you can set `WATCHDOG_TIMEOUT`{.interpreted-text role="term"} to the desired timeout in seconds. Note that the same `WATCHDOG_TIMEOUT`{.interpreted-text role="term"} variable also specifies the timeout used for the `watchdog` tool (if that is being built).

> 对于使用systemd的图像，重新启动看门狗超时已设置为60秒（从上游默认的10分钟）。如果您希望覆盖此设置，可以将`WATCHDOG_TIMEOUT`{.interpreted-text role="term"} 设置为所需的超时时间（以秒为单位）。请注意，相同的`WATCHDOG_TIMEOUT`{.interpreted-text role="term"} 变量也指定了`watchdog`工具使用的超时时间（如果正在构建）。

- The `ref-classes-image-buildinfo`{.interpreted-text role="ref"} class now writes to `${sysconfdir}/buildinfo` instead of `${sysconfdir}/build` by default (i.e. the default value of `IMAGE_BUILDINFO_FILE`{.interpreted-text role="term"} has been changed). If you have code that reads this from images at build or runtime you will need to update it or specify your own value for `IMAGE_BUILDINFO_FILE`{.interpreted-text role="term"}.

> - 默认情况下，`ref-classes-image-buildinfo`{.interpreted-text role="ref"}类现在将写入`${sysconfdir}/buildinfo`而不是`${sysconfdir}/build`（即`IMAGE_BUILDINFO_FILE`{.interpreted-text role="term"}的默认值已更改）。如果您在构建或运行时从图像中读取此内容，则需要更新它或为`IMAGE_BUILDINFO_FILE`{.interpreted-text role="term"}指定自己的值。

- In the `ref-classes-archiver`{.interpreted-text role="ref"} class, the default `ARCHIVER_OUTDIR` value no longer includes the `MACHINE`{.interpreted-text role="term"} value in order to avoid the archive task running multiple times in a multiconfig setup. If you have custom code that does something with the files archived by the `ref-classes-archiver`{.interpreted-text role="ref"} class then you may need to adjust it to the new structure.

> 在`ref-classes-archiver`{.interpreted-text role="ref"}类中，默认的`ARCHIVER_OUTDIR`值不再包括`MACHINE`{.interpreted-text role="term"}值，以避免在多配置设置中多次运行归档任务。如果您有自定义代码可以处理`ref-classes-archiver`{.interpreted-text role="ref"}类归档的文件，则可能需要将其调整到新结构。

- If you are not using [systemd]{.title-ref} then udev is now configured to use labels (`LABEL` or `PARTLABEL`) to set the mount point for the device. For example:

> 如果您不使用systemd，那么udev现在已经配置为使用标签（LABEL或PARTLABEL）来设置设备的挂载点。例如：

  ```
  /run/media/rootfs-sda2
  ```

  instead of:

  ```
  /run/media/sda2
  ```

- `icu` no longer provides the `icu-config` configuration tool - upstream have indicated `icu-config` is deprecated and should no longer be used. Code with references to it will need to be updated, for example to use `pkg-config` instead.

> - ICU 不再提供 `icu-config` 配置工具 - 上游表明 `icu-config` 已被弃用，不应再使用。引用它的代码需要更新，例如使用 `pkg-config` 替代。
- The `rng-tools` systemd service name has changed from `rngd` to `rng-tools`

- The `largefile` `DISTRO_FEATURES`{.interpreted-text role="term"} item has been removed, large file support is now always enabled where it was previously optional.

> 大文件`DISTRO_FEATURES`项已被移除，原本可选的大文件支持现在总是被启用。
- The Python `zoneinfo` module is now split out to its own `python3-zoneinfo` package.

- The `PACKAGECONFIG`{.interpreted-text role="term"} option to enable wpa_supplicant in the `connman` recipe has been renamed to \"wpa-supplicant\". If you have set `PACKAGECONFIG`{.interpreted-text role="term"} for the `connman` recipe to include this option you will need to update your configuration. Related to this, the `WIRELESS_DAEMON`{.interpreted-text role="term"} variable now expects the new `wpa-supplicant` naming and affects `packagegroup-base` as well as `connman`.

> 选项`PACKAGECONFIG`以启用connman配方中的wpa_supplicant已更名为“wpa-supplicant”。如果您为connman配方设置了`PACKAGECONFIG`以包括此选项，则需要更新您的配置。与此相关，变量`WIRELESS_DAEMON`现在期望新的`wpa-supplicant`命名，并影响`packagegroup-base`以及`connman`。

- The `wpa-supplicant` recipe no longer uses a static (and stale) `defconfig` file, instead it uses the upstream version with appropriate edits for the `PACKAGECONFIG`{.interpreted-text role="term"}. If you are customising this file you will need to update your customisations.

> -`wpa-supplicant`食谱不再使用静态（和过时的）`defconfig`文件，而是使用适当编辑过的上游版本`PACKAGECONFIG`。如果您正在自定义此文件，您将需要更新您的自定义内容。

- With the introduction of picobuild in `ref-classes-python_pep517`{.interpreted-text role="ref"}, The `PEP517_BUILD_API` variable is no longer supported. If you have any references to this variable you should remove them.

> 随着`ref-classes-python_pep517`{.interpreted-text role="ref"}中picobuild的引入，`PEP517_BUILD_API`变量不再受支持。如果你有对此变量的引用，你应该将它们移除。

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
