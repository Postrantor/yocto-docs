---
tip: translate by openai@2023-06-07 22:00:15
...
---
title: Features
---------------

This chapter provides a reference of shipped machine and distro features you can include as part of your image, a reference on image features you can select, and a reference on `ref-features-backfill`.

> 本章提供了可以作为您的映像的一部分包含的已发货机器和发行版特征的参考，可以选择的映像特征的参考，以及 `ref-features-backfill` 的参考。

Features provide a mechanism for working out which packages should be included in the generated images. Distributions can select which features they want to support through the `DISTRO_FEATURES` variable, which is set in the machine configuration file and specifies the hardware features for a given machine.

> 提供了一种机制，可以确定哪些软件包应该包含在生成的镜像中。发行版可以通过 `DISTRO_FEATURES` 变量来选择要支持哪些功能，该变量在发行版的配置文件(如 `poky.conf`，`poky-tiny.conf`，`poky-lsb.conf` 等)中设置或追加。机器功能由 `MACHINE_FEATURES` 变量设置，该变量在机器配置文件中指定给定机器的硬件功能。

These two variables combine to work out which kernel modules, utilities, and other packages to include. A given distribution can support a selected subset of features so some machine features might not be included if the distribution itself does not support them.

> 这两个变量结合起来可以确定要包括哪些内核模块、实用程序和其他软件包。给定的发行版可以支持一组选定的功能，因此如果发行版本自身不支持某些机器功能，则可能不包括这些功能。

One method you can use to determine which recipes are checking to see if a particular feature is contained or not is to `grep` through the `Metadata` for the feature. Here is an example that discovers the recipes whose build is potentially changed based on a given feature:

> 一种方法可以用来确定哪些 recipes 检查以查看是否包含特定功能的是通过 `Metadata` 对特定功能进行 `grep`。这里有一个例子，可以发现基于给定特征潜在更改构建的 recipes：

```
$ cd poky
$ git grep 'contains.*MACHINE_FEATURES.*feature'
```

# Machine Features

The items below are features you can use with `MACHINE_FEATURES` task for a particular recipe.

> 下面的项目是您可以使用 `MACHINE_FEATURES` 任务中是否指定了特定的配置选项。

This feature list only represents features as shipped with the Yocto Project metadata:

> 这个特性列表只代表 Yocto Project 元数据提供的特性。

- *acpi:* Hardware has ACPI (x86/x86_64 only)
- *alsa:* Hardware has ALSA audio drivers
- *apm:* Hardware uses APM (or APM emulation)
- *bluetooth:* Hardware has integrated BT
- *efi:* Support for booting through EFI
- *ext2:* Hardware HDD or Microdrive
- *keyboard:* Hardware has a keyboard
- *numa:* Hardware has non-uniform memory access
- *pcbios:* Support for booting through BIOS
- *pci:* Hardware has a PCI bus
- *pcmcia:* Hardware has PCMCIA or CompactFlash sockets
- *phone:* Mobile phone (voice) support
- *qemu-usermode:* QEMU can support user-mode emulation for this machine
- *qvga:* Machine has a QVGA (320x240) display
- *rtc:* Machine has a Real-Time Clock
- *screen:* Hardware has a screen
- *serial:* Hardware has serial support (usually RS232)
- *touchscreen:* Hardware has a touchscreen
- *usbgadget:* Hardware is USB gadget device capable
- *usbhost:* Hardware is USB Host capable
- *vfat:* FAT file system support
- *wifi:* Hardware has integrated WiFi

# Distro Features

The items below are features you can use with `DISTRO_FEATURES` for the concerned packages is one way of supplying such options.

> 以下是您可以使用 `DISTRO_FEATURES` 是提供这些选项的一种方法。

Some distro features are also machine features. These select features make sense to be controlled both at the machine and distribution configuration level. See the `COMBINED_FEATURES` variable for more information.

> 一些发行版特性也是机器特性。这些选择的特性有理由在机器和发行版配置层面上都能控制。有关更多信息，请参阅“COMBINED_FEATURES”变量。

::: note
::: title
Note
:::

`DISTRO_FEATURES` also relies on support in the kernel, you will also need to ensure that support is enabled in the kernel configuration.

> DISTRO_FEATURES 通常独立于内核配置，因此，如果 DISTRO_FEATURES 中指定的特性也依赖于内核的支持，您还需要确保在内核配置中启用该支持。
> :::

This list only represents features as shipped with the Yocto Project metadata, as extra layers can define their own:

> 这个列表仅代表 Yocto Project 元数据提供的功能，因为额外的层可以定义自己的：

- *3g:* Include support for cellular data.
- *acl:* Include `Access Control List <Access-control_list>` support.

> 包括支持“访问控制列表(Access Control List)”。

- *alsa:* Include `Advanced Linux Sound Architecture <Advanced_Linux_Sound_Architecture>` support (OSS compatibility kernel modules installed if available).

> 包括支持 Advanced Linux Sound Architecture(Advanced_Linux_Sound_Architecture)(如果可用，则安装 OSS 兼容内核模块)。

- *api-documentation:* Enables generation of API documentation during recipe builds. The resulting documentation is added to SDK tarballs when the `bitbake -c populate_sdk` command is used. See the \"`sdk-manual/appendix-customizing-standard:adding api documentation to the standard sdk`\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 启用在配方构建期间生成 API 文档。使用 `bitbake -c populate_sdk` 命令时，将添加结果文档到 SDK 归档文件中。请参见 Yocto 项目应用程序开发和可扩展软件开发工具包(eSDK)手册中的“sdk-manual/appendix-customizing-standard：将 API 文档添加到标准 SDK”部分。

- *bluetooth:* Include bluetooth support (integrated BT only).
- *cramfs:* Include CramFS support.
- *debuginfod:* Include support for getting ELF debugging information through a `debuginfod <dev-manual/debugging:using the debuginfod server method>` server.

> 支持通过 `debuginfod <dev-manual/debugging:using the debuginfod server method>` 服务器获取 ELF 调试信息。

- *directfb:* Include DirectFB support.
- *ext2:* Include tools for supporting for devices with internal HDD/Microdrive for storing files (instead of Flash only devices).

> -*ext2：*包含支持内置硬盘/微驱动器存储文件(而不是仅仅是 Flash 设备)的工具。

- *gobject-introspection-data:* Include data to support [GObject Introspection](https://gi.readthedocs.io/en/latest/).

> - *gobject-introspection-data：*包括支持 [GObject Introspection](https://gi.readthedocs.io/en/latest/) 的数据。

- *ipsec:* Include IPSec support.
- *ipv4:* Include IPv4 support.
- *ipv6:* Include IPv6 support.
- *keyboard:* Include keyboard support (e.g. keymaps will be loaded during boot).
- *multiarch:* Enable building applications with multiple architecture support.
- *ld-is-gold:* Use the `gold <Gold_(linker)>` linker instead of the standard GCC linker (bfd).

> 使用 `gold <Gold_(linker)>` 链接器而不是标准 GCC 链接器(bfd)。

- *ldconfig:* Include support for ldconfig and `ld.so.conf` on the target.
- *lto:* Enable [Link-Time Optimisation](https://gcc.gnu.org/wiki/LinkTimeOptimization).
- *nfc:* Include support for [Near Field Communication](https://en.wikipedia.org/wiki/Near-field_communication).

> 包括支持[近场通信](https://en.wikipedia.org/wiki/Near-field_communication)的功能。

- *nfs:* Include NFS client support (for mounting NFS exports on device).
- *nls:* Include National Language Support (NLS).
- *opengl:* Include the Open Graphics Library, which is a cross-language, multi-platform application programming interface used for rendering two and three-dimensional graphics.

> 包括 Open Graphics Library，这是一种跨语言、多平台的应用程序编程接口，用于呈现二维和三维图形。

- *overlayfs:* Include [OverlayFS](https://docs.kernel.org/filesystems/overlayfs.html) support.
- *pam:* Include `Pluggable Authentication Module (PAM) <Pluggable_authentication_module>` support.

> 包括可插拔身份验证模块(PAM)支持。

- *pci:* Include PCI bus support.
- *pcmcia:* Include PCMCIA/CompactFlash support.
- *polkit:* Include `Polkit <Polkit>` support.
- *ppp:* Include PPP dialup support.
- *ptest:* Enables building the package tests where supported by individual recipes. For more information on package tests, see the \"`dev-manual/packages:testing packages with ptest`\" section in the Yocto Project Development Tasks Manual.

> - *ptest：*在支持的情况下，启用构建软件包测试。有关软件包测试的更多信息，请参阅 Yocto Project 开发任务手册中的“dev-manual/packages：使用 ptest 测试软件包”部分。

- *pulseaudio:* Include support for [PulseAudio](https://www.freedesktop.org/wiki/Software/PulseAudio/).

> 支持 [PulseAudio](https://www.freedesktop.org/wiki/Software/PulseAudio/)。

- *selinux:* Include support for `Security-Enhanced Linux (SELinux) <Security-Enhanced_Linux>` (requires [meta-selinux](https://layers.openembedded.org/layerindex/layer/meta-selinux/)).

> 支持安全增强型 Linux(SELinux)(需要 [meta-selinux](https://layers.openembedded.org/layerindex/layer/meta-selinux/))。

- *seccomp:* Enables building applications with `seccomp <Seccomp>` support, to allow them to strictly restrict the system calls that they are allowed to invoke.

> 启用 seccomp 支持，可以让应用程序严格限制允许调用的系统调用。

- *smbfs:* Include SMB networks client support (for mounting Samba/Microsoft Windows shares on device).

> 包括 SMB 网络客户端支持(用于在设备上挂载 Samba/Microsoft Windows 共享)。

- *systemd:* Include support for this `init` manager, which is a full replacement of for `init` with parallel starting of services, reduced shell overhead, and other features. This `init` manager is used by many distributions.

> *Systemd：* 包含对这个 `init` 管理器的支持，它是 `init` 的完整替代品，可以并行启动服务，减少 shell 开销以及其他功能。许多发行版都使用这个 `init` 管理器。

- *usbgadget:* Include USB Gadget Device support (for USB networking/serial/storage).
- *usbhost:* Include USB Host support (allows to connect external keyboard, mouse, storage, network etc).

> 包括 USB 主机支持(允许连接外部键盘、鼠标、存储器、网络等)。

- *usrmerge:* Merges the `/bin`, `/sbin`, `/lib`, and `/lib64` directories into their respective counterparts in the `/usr` directory to provide better package and application compatibility.

> - *usrmerge*：将 `/bin`、`/sbin`、`/lib` 和 `/lib64` 目录合并到 `/usr` 目录中的各自对应目录，以提供更好的包和应用程序兼容性。

- *vfat:* Include `FAT filesystem <File_Allocation_Table>` support.

> 包括 FAT 文件系统(文件分配表)支持。

- *vulkan:* Include support for the `Vulkan API <Vulkan>`.
- *wayland:* Include the Wayland display server protocol and the library that supports it.
- *wifi:* Include WiFi support (integrated only).
- *x11:* Include the X server and libraries.
- *xattr:* Include support for `extended file attributes <Extended_file_attributes>`.

> 包括支持扩展文件属性。

- *zeroconf:* Include support for [zero configuration networking](https://en.wikipedia.org/wiki/Zero-configuration_networking).

> 支持[零配置网络](https://en.wikipedia.org/wiki/Zero-configuration_networking)。

# Image Features

The contents of images generated by the OpenEmbedded build system can be controlled by the `IMAGE_FEATURES` variables that you typically configure in your image recipes. Through these variables, you can add several different predefined packages such as development utilities or packages with debug information needed to investigate application problems or profile applications.

> 通过 OpenEmbedded 构建系统生成的镜像的内容可以通过通常在镜像配方中配置的 `IMAGE_FEATURES` 和 `EXTRA_IMAGE_FEATURES` 变量来控制。通过这些变量，您可以添加几种不同的预定义软件包，例如开发实用程序或用于调查应用程序问题或分析应用程序的带有调试信息的软件包。

Here are the image features available for all images:

> 这里有所有镜像可用的镜像特征：

- *allow-empty-password:* Allows Dropbear and OpenSSH to accept logins from accounts having an empty password string.

> - *允许空密码：*允许 Dropbear 和 OpenSSH 接受具有空密码字符串的帐户的登录。

- *allow-root-login:* Allows Dropbear and OpenSSH to accept root logins.
- *dbg-pkgs:* Installs debug symbol packages for all packages installed in a given image.
- *debug-tweaks:* Makes an image suitable for development (e.g. allows root logins, logins without passwords \-\--including root ones, and enables post-installation logging). See the `allow-empty-password`, `allow-root-login`, `empty-root-password`, and `post-install-logging` features in this list for additional information.

> - *调试调整：*使镜像适合开发(例如允许 root 登录，不需要密码的登录(包括 root)，并启用安装后的日志记录)。有关更多信息，请参阅此列表中的“允许空密码”，“允许 root 登录”，“空 root 密码”和“安装后日志记录”功能。

- *dev-pkgs:* Installs development packages (headers and extra library links) for all packages installed in a given image.

> - *dev-pkgs：*为给定镜像中安装的所有软件包安装开发包(头文件和额外的库链接)。

- *doc-pkgs:* Installs documentation packages for all packages installed in a given image.
- *empty-root-password:* This feature or `debug-tweaks` is required if you want to allow root login with an empty password. If these features are not present in `IMAGE_FEATURES`, a non-empty password is forced in `/etc/passwd` and `/etc/shadow` if such files exist.

> - *空根密码：*如果您希望允许以空密码登录 root，则需要此功能或“debug-tweaks”。如果在 IMAGE_FEATURES 中不存在这些功能，则如果存在/etc/passwd 和/etc/shadow 文件，则会强制使用非空密码。

::: note
::: title

Note

> 注意
> :::

`empty-root-password` doesn\'t set an empty root password by itself. You get an initial empty root password thanks to the :oe_[git:%60base-passwd](git:%60base-passwd) \</openembedded-core/tree/meta/recipes-core/base-passwd/\>[ and :oe_git:\`shadow \</openembedded-core/tree/meta/recipes-extended/shadow/\>] recipes, and the presence of `empty-root-password` or `debug-tweaks` just disables the mechanism which forces an non-empty password for the root user.

> 没有设置空的 root 密码本身。您可以通过:oe_[git:`base-passwd`](git:%60base-passwd%60) \</openembedded-core/tree/meta/recipes-core/base-passwd/\> 和:oe_git:\`shadow \</openembedded-core/tree/meta/recipes-extended/shadow/\>] 配方获得初始空 root 密码，而 `empty-root-password` 或 `debug-tweaks` 的存在只是禁用了为 root 用户强制设置非空密码的机制。
> :::

- *lic-pkgs:* Installs license packages for all packages installed in a given image.
- *overlayfs-etc:* Configures the `/etc` directory to be in `overlayfs`. This allows to store device specific information elsewhere, especially if the root filesystem is configured to be read-only.

> -*overlayfs-etc：*将 `/etc` 目录配置为 `overlayfs`。这样可以将设备特定信息存储在其他地方，特别是如果根文件系统被配置为只读时。

- *package-management:* Installs package management tools and preserves the package manager database.

> - 包管理：安装包管理工具并保留包管理器数据库。

- *post-install-logging:* Enables logging postinstall script runs to the `/var/log/postinstall.log` file on first boot of the image on the target system.

> 启用后安装脚本日志记录功能，将在目标系统首次启动时将日志记录到 `/var/log/postinstall.log` 文件中。

::: note
::: title

Note

> 注意
> :::

To make the `/var/log` directory on the target persistent, use the `VOLATILE_LOG_DIR` variable by setting it to \"no\".

> 要使目标上的 `/var/log` 目录持久，请使用 `VOLATILE_LOG_DIR` 变量并将其设置为“no”。
> :::

- *ptest-pkgs:* Installs ptest packages for all ptest-enabled recipes.
- *read-only-rootfs:* Creates an image whose root filesystem is read-only. See the \"`dev-manual/read-only-rootfs:creating a read-only root filesystem`\" section in the Yocto Project Development Tasks Manual for more information.

> - *只读根文件系统：*创建一个根文件系统是只读的映像。有关更多信息，请参阅 Yocto 项目开发任务手册中的“dev-manual / read-only-rootfs：创建只读根文件系统”部分。

- *read-only-rootfs-delayed-postinsts:* when specified in conjunction with `read-only-rootfs`, specifies that post-install scripts are still permitted (this assumes that the root filesystem will be made writeable for the first boot; this feature does not do anything to ensure that - it just disables the check for post-install scripts.)

> 当与“只读根文件系统”一起指定时，*read-only-rootfs-delayed-postinsts* 指定仍允许运行安装后脚本(这假设根文件系统将在第一次启动时被设置为可写；此功能不会做任何事来确保这一点，它只会禁用安装后脚本的检查。)

- *serial-autologin-root:* when specified in conjunction with `empty-root-password` will automatically login as root on the serial console. This of course opens up a security hole if the serial console is potentially accessible to an attacker, so use with caution.

> -*串行自动登录 root：* 如果与 `empty-root-password` 一起指定，则会自动登录串行控制台的 root。如果串行控制台可能被攻击者访问，则会开辟安全漏洞，因此请谨慎使用。

- *splash:* Enables showing a splash screen during boot. By default, this screen is provided by `psplash`, which does allow customization. If you prefer to use an alternative splash screen package, you can do so by setting the `SPLASH` variable to a different package name (or names) within the image recipe or at the distro configuration level.

> - *Splash：* 启动时可以显示启动画面。默认情况下，这个画面由 `psplash` 提供，可以进行定制。如果您希望使用其他启动画面包，可以通过在镜像配方或发行版配置级别中将 `SPLASH` 变量设置为不同的包名称(或名称)来实现。

- *stateless-rootfs:*: specifies that the image should be created as stateless - when using `systemd`, `systemctl-native` will not be run on the image, leaving the image for population at runtime by systemd.

> - *无状态根文件系统：*：指定应该创建为无状态的镜像 - 当使用 `systemd` 时，将不会在镜像上运行 `systemctl-native`，从而使镜像可以在运行时由 systemd 进行填充。

- *staticdev-pkgs:* Installs static development packages, which are static libraries (i.e. `*.a` files), for all packages installed in a given image.

> - *staticdev-pkgs：*在给定的镜像中安装静态开发包，这些静态开发包是静态库(即 `*.a` 文件)。

Some image features are available only when you inherit the `ref-classes-core-image` class. The current list of these valid features is as follows:

> 一些镜像特征只有当您继承“ref-classes-core-image”类时才可用。当前有效特征的列表如下：

- *hwcodecs:* Installs hardware acceleration codecs.
- *nfs-server:* Installs an NFS server.
- *perf:* Installs profiling tools such as `perf`, `systemtap`, and `LTTng`. For general information on user-space tools, see the `/sdk-manual/index` manual.

> - *perf：*安装诸如 `perf`、`systemtap` 和 `LTTng` 之类的分析工具。有关用户空间工具的一般信息，请参阅 `/sdk-manual/index` 手册。

- *ssh-server-dropbear:* Installs the Dropbear minimal SSH server.

  ::: note
  ::: title

  Note

> 注意
> :::

As of the 4.1 release, the `ssh-server-dropbear` feature also recommends the `openssh-sftp-server` package, which by default will be pulled into the image. This is because recent versions of the OpenSSH `scp` client now use the SFTP protocol, and thus require an SFTP server to be present to connect to. However, if you wish to use the Dropbear ssh server [without] as follows:

> 随着 4.1 版本的发布，ssh-server-dropbear 功能也会推荐安装 openssh-sftp-server 包，这将默认包含在镜像中。这是因为最新版本的 OpenSSH scp 客户端现在使用 SFTP 协议，因此需要 SFTP 服务器来连接。但是，如果您希望使用 Dropbear ssh 服务器而不安装 SFTP 服务器，您可以从 IMAGE_FEATURES 中删除 ssh-server-dropbear，并将 dropbear 添加到 IMAGE_INSTALL，或者仍然使用该功能，但将 BAD_RECOMMENDATIONS 设置如下：

```
BAD_RECOMMENDATIONS += "openssh-sftp-server"
```

:::

- *ssh-server-openssh:* Installs the OpenSSH SSH server, which is more full-featured than Dropbear. Note that if both the OpenSSH SSH server and the Dropbear minimal SSH server are present in `IMAGE_FEATURES`, then OpenSSH will take precedence and Dropbear will not be installed.

> 安装 OpenSSH SSH 服务器，比 Dropbear 更加功能齐全。注意，如果在 IMAGE_FEATURES 中同时存在 OpenSSH SSH 服务器和 Dropbear 最小 SSH 服务器，那么 OpenSSH 将优先，Dropbear 将不会被安装。

- *tools-debug:* Installs debugging tools such as `strace` and `gdb`. For information on GDB, see the \"`dev-manual/debugging:debugging with the gnu project debugger (gdb) remotely`.

> - *调试工具：*安装调试工具，如 `strace` 和 `gdb`。有关 GDB 的信息，请参阅 Yocto Project 开发任务手册中的“dev-manual/debugging：使用 GNU 项目调试器(GDB)远程调试”部分。有关跟踪和分析的信息，请参阅 `/profile-manual/index`。

- *tools-sdk:* Installs a full SDK that runs on the device.
- *tools-testapps:* Installs device testing tools (e.g. touchscreen debugging).
- *weston:* Installs Weston (reference Wayland environment).
- *x11:* Installs the X server.
- *x11-base:* Installs the X server with a minimal environment.
- *x11-sato:* Installs the OpenedHand Sato environment.

# Feature Backfilling

Sometimes it is necessary in the OpenEmbedded build system to add new functionality to `MACHINE_FEATURES`, but at the same time, allow existing distributions or machine definitions to opt out of such new features, to retain the same overall level of functionality.

> 有时，在 OpenEmbedded 构建系统中，需要向 `MACHINE_FEATURES` 中添加新功能，但同时允许现有的发行版或机器定义选择退出这些新功能，以保持相同的整体功能水平。

To make this possible, the OpenEmbedded build system has a mechanism to automatically \"backfill\" features into existing distro or machine configurations. You can see the list of features for which this is done by checking the `DISTRO_FEATURES_BACKFILL` variables in the `meta/conf/bitbake.conf` file.

> 为了实现这一目标，OpenEmbedded 构建系统具有自动将特性“填充”到现有发行版或机器配置中的机制。您可以通过检查 `meta/conf/bitbake.conf` 文件中的 `DISTRO_FEATURES_BACKFILL` 变量来查看需要进行此操作的特性列表。

These two variables are paired with the `DISTRO_FEATURES_BACKFILL_CONSIDERED` any added feature, and decide when they wish to keep or exclude such feature, thus preventing the backfilling from happening.

> 这两个变量配对使用 `DISTRO_FEATURES_BACKFILL_CONSIDERED` 变量，可以让发行版或机器配置维护者考虑任何添加的功能，并决定何时保留或排除此类功能，从而防止回填发生。

Here are two examples to illustrate feature backfilling:

> 这里有两个例子来说明特征回填：

- *The \"pulseaudio\" distro feature option*: Previously, PulseAudio support was enabled within the Qt and GStreamer frameworks. Because of this, the feature is now backfilled and thus enabled for all distros through the `DISTRO_FEATURES_BACKFILL`, effectively disabling the feature for that particular distro.

> "PulseAudio" 发行特性选项：以前，PulseAudio 支持在 Qt 和 GStreamer 框架内启用。因此，该特性现在通过 `meta/conf/bitbake.conf` 文件中的 `DISTRO_FEATURES_BACKFILL` 变量为所有发行版提供支持。但是，如果您的发行版需要禁用该特性，您可以在不影响其他需要 PulseAudio 支持的现有发行版配置的情况下这样做。可以通过在发行版的 `.conf` 文件中将"pulseaudio"添加到 `DISTRO_FEATURES_BACKFILL_CONSIDERED` 中来实现。因此，当它也存在于 `DISTRO_FEATURES_BACKFILL` 变量中时，将该特性添加到此变量可以防止构建系统将特性添加到配置的 `DISTRO_FEATURES` 中，从而有效地为该特定发行版禁用该特性。

- *The \"rtc\" machine feature option*: Previously, real time clock (RTC) support was enabled for all target devices. Because of this, the feature is backfilled and thus enabled for all machines through the `MACHINE_FEATURES_BACKFILL`, effectively disabling RTC support for that particular machine.

> "RTC"机器功能选项：以前，所有目标设备都启用了实时时钟(RTC)支持。因此，该功能已通过 `meta / conf / bitbake.conf` 文件中的 `MACHINE_FEATURES_BACKFILL` 变量为所有机器进行了回填。但是，如果您的目标设备没有此功能，您可以在不影响其他需要 RTC 支持的机器的情况下禁用 RTC 支持。您可以通过在机器的 `.conf` 文件中将“rtc”功能添加到 `MACHINE_FEATURES_BACKFILL_CONSIDERED` 列表中来实现此目的。因此，当该功能也存在于 `MACHINE_FEATURES_BACKFILL` 变量中时，将该功能添加到此变量可以防止构建系统将该功能添加到配置的 `MACHINE_FEATURES` 中，从而有效地禁用该特定机器的 RTC 支持。
