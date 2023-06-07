---
tip: translate by baidu@2023-06-07 17:12:02
...
---
title: Enabling GObject Introspection Support
---------------------------------------------

[GObject introspection](https://gi.readthedocs.io/en/latest/) is the standard mechanism for accessing GObject-based software from runtime environments. GObject is a feature of the GLib library that provides an object framework for the GNOME desktop and related software. GObject Introspection adds information to GObject that allows objects created within it to be represented across different programming languages. If you want to construct GStreamer pipelines using Python, or control UPnP infrastructure using Javascript and GUPnP, GObject introspection is the only way to do it.

> [对象内省](https://gi.readthedocs.io/en/latest/)是从运行时环境访问基于 GObject 的软件的标准机制。GObject 是 GLib 库的一个特性，它为 GNOME 桌面和相关软件提供了一个对象框架。GObject Introspection 向 GObject 添加信息，允许在其中创建的对象在不同的编程语言中表示。如果您想使用 Python 构建 GStreamer 管道，或者使用 Javascript 和 GUPnP 控制 UPnP 基础设施，GObject 内省是唯一的方法。

This section describes the Yocto Project support for generating and packaging GObject introspection data. GObject introspection data is a description of the API provided by libraries built on top of the GLib framework, and, in particular, that framework\'s GObject mechanism. GObject Introspection Repository (GIR) files go to `-dev` packages, `typelib` files go to main packages as they are packaged together with libraries that are introspected.

> 本节介绍 Yocto 项目对生成和打包 GObject 内省数据的支持。GObject 自省数据是对构建在 GLib 框架之上的库提供的 API 的描述，特别是该框架的 GObjects 机制。GObject 内省存储库（GIR）文件转到“-dev”包，“typelib”文件转到主包，因为它们与内省的库打包在一起。

The data is generated when building such a library, by linking the library with a small executable binary that asks the library to describe itself, and then executing the binary and processing its output.

> 数据是在构建这样一个库时生成的，方法是将库与一个小型可执行二进制文件链接，该二进制文件要求库描述自己，然后执行二进制文件并处理其输出。

Generating this data in a cross-compilation environment is difficult because the library is produced for the target architecture, but its code needs to be executed on the build host. This problem is solved with the OpenEmbedded build system by running the code through QEMU, which allows precisely that. Unfortunately, QEMU does not always work perfectly as mentioned in the \"`dev-manual/gobject-introspection:known issues`{.interpreted-text role="ref"}\" section.

> 在交叉编译环境中生成这些数据很困难，因为库是为目标体系结构生成的，但其代码需要在构建主机上执行。OpenEmbedded 构建系统通过 QEMU 运行代码来解决这个问题，这正是允许的。不幸的是，QEMU 并不总是像“`devmanual/gobject内省：已知问题`｛.depredicted text role=“ref”｝”一节中提到的那样完美工作。

# Enabling the Generation of Introspection Data

Enabling the generation of introspection data (GIR files) in your library package involves the following:

> 在库包中启用内省数据（GIR 文件）的生成涉及以下内容：

1. Inherit the `ref-classes-gobject-introspection`{.interpreted-text role="ref"} class.

> 1.继承 `ref classes gobject内省`{.depredicted text role=“ref”}类。

2. Make sure introspection is not disabled anywhere in the recipe or from anything the recipe includes. Also, make sure that \"gobject-introspection-data\" is not in `DISTRO_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"} and that \"qemu-usermode\" is not in `MACHINE_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"}. In either of these conditions, nothing will happen.

> 2.确保配方中的任何地方或配方中包含的任何内容都没有禁用内省。此外，请确保\“gobject 内省数据\”不在 `DISTRO_FEATURES_BACKFIL_INFERED`｛.spredicted text role=“term”｝中，并且\“qemu usermode \”不位于 `MACHINE_FEATURES_BACKFILL_INFERED `｛.selected text rol=“term“｝中。在这两种情况下，什么都不会发生。

3. Try to build the recipe. If you encounter build errors that look like something is unable to find `.so` libraries, check where these libraries are located in the source tree and add the following to the recipe:

> 3.试着制作食谱。如果您遇到的构建错误看起来像是找不到“.so”库，请检查这些库在源代码树中的位置，并将以下内容添加到配方中：

```

> ```

GIR_EXTRA_LIBS_PATH = "${B}/something/.libs"

> GIR_EXTRA_LIBRS_PATH=“$｛B｝/某物/.LIBS”

```

> ```
> ```

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

See recipes in the `oe-core` repository that use that `GIR_EXTRA_LIBS_PATH`{.interpreted-text role="term"} variable as an example.

> 请参阅 `oe-core` 存储库中使用 `GIR_EXTRA_LIBRS_PATH`｛.respered text role=“term”｝变量的配方作为示例。

:::

> ：：：

4. Look for any other errors, which probably mean that introspection support in a package is not entirely standard, and thus breaks down in a cross-compilation environment. For such cases, custom-made fixes are needed. A good place to ask and receive help in these cases is the `Yocto Project mailing lists <resources-mailinglist>`{.interpreted-text role="ref"}.

> 4.查找任何其他错误，这可能意味着包中的内省支持不是完全标准的，因此在交叉编译环境中会出现故障。对于这种情况，需要进行定制修复。在这种情况下，询问和获得帮助的好地方是 `Yocto Project邮件列表<resources mailinglist>`{.depredicted text role=“ref”}。

::: note
::: title
Note
:::

Using a library that no longer builds against the latest Yocto Project release and prints introspection related errors is a good candidate for the previous procedure.

> 使用一个不再根据最新的 Yocto Project 版本构建并打印与内省相关的错误的库是上一个过程的一个很好的候选者。
> :::

# Disabling the Generation of Introspection Data

You might find that you do not want to generate introspection data. Or, perhaps QEMU does not work on your build host and target architecture combination. If so, you can use either of the following methods to disable GIR file generations:

> 您可能会发现，您不希望生成自省数据。或者，也许 QEMU 不适用于构建主机和目标体系结构的组合。如果是，您可以使用以下任一方法禁用 GIR 文件生成：

- Add the following to your distro configuration:

  ```
  DISTRO_FEATURES_BACKFILL_CONSIDERED = "gobject-introspection-data"
  ```

  Adding this statement disables generating introspection data using QEMU but will still enable building introspection tools and libraries (i.e. building them does not require the use of QEMU).

> 添加此语句将禁用使用 QEMU 生成内省数据，但仍将启用构建内省工具和库（即，构建它们不需要使用 QEMU）。

- Add the following to your machine configuration:

  ```
  MACHINE_FEATURES_BACKFILL_CONSIDERED = "qemu-usermode"
  ```

  Adding this statement disables the use of QEMU when building packages for your machine. Currently, this feature is used only by introspection recipes and has the same effect as the previously described option.

> 添加此语句将禁止在为机器构建包时使用 QEMU。目前，此功能仅由内省配方使用，其效果与前面描述的选项相同。

::: note
::: title

Note

> 笔记
> :::

Future releases of the Yocto Project might have other features affected by this option.

> Yocto 项目的未来版本可能会有其他受此选项影响的功能。
> :::

If you disable introspection data, you can still obtain it through other means such as copying the data from a suitable sysroot, or by generating it on the target hardware. The OpenEmbedded build system does not currently provide specific support for these techniques.

> 如果禁用自省数据，您仍然可以通过其他方式获取数据，例如从合适的系统根目录复制数据，或在目标硬件上生成数据。OpenEmbedded 构建系统目前没有为这些技术提供特定的支持。

# Testing that Introspection Works in an Image

Use the following procedure to test if generating introspection data is working in an image:

> 使用以下步骤测试生成内省数据是否在图像中起作用：

1. Make sure that \"gobject-introspection-data\" is not in `DISTRO_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"} and that \"qemu-usermode\" is not in `MACHINE_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"}.

> 1.确保\“gobject 内省数据\”不在 `DISTRO_FEATURES_BACKFILL_CONVENTED`｛.explored text role=“term”｝中，并且\“qemu usermode \”不位于 `MACHINE_FEATURES_BACKFILL_INVENTED'｛。

2. Build `core-image-sato`.

> 2.构建“核心形象 sato”。

3. Launch a Terminal and then start Python in the terminal.

> 3.启动一个终端，然后在终端中启动 Python。

4. Enter the following in the terminal:

> 4.在终端中输入以下内容：

```

> ```

>>> from gi.repository import GLib

> >>>来自gi.假定进口GLib

>>> GLib.get_host_name()

> >>>GLib.get_host_name（）

```

> ```
> ```

5. For something a little more advanced, enter the following see: [https://python-gtk-3-tutorial.readthedocs.io/en/latest/introduction.html](https://python-gtk-3-tutorial.readthedocs.io/en/latest/introduction.html)

> 5.要获得更高级的内容，请输入以下内容，请参阅：[https://python-gtk-3-tutorial.readthedocs.io/en/latest/introduction.html](https://python-gtk-3-tutorial.readthedocs.io/en/latest/introduction.html)

# Known Issues

Here are know issues in GObject Introspection Support:

> 以下是 GObject 反思支持中的已知问题：

- `qemu-ppc64` immediately crashes. Consequently, you cannot build introspection data on that architecture.

> -“qemu-pc64”立即崩溃。因此，您无法在该体系结构上构建自省数据。

- x32 is not supported by QEMU. Consequently, introspection data is disabled.
- musl causes transient GLib binaries to crash on assertion failures. Consequently, generating introspection data is disabled.

> -musl 导致瞬态 GLib 二进制文件在断言失败时崩溃。因此，禁止生成自省数据。

- Because QEMU is not able to run the binaries correctly, introspection is disabled for some specific packages under specific architectures (e.g. `gcr`, `libsecret`, and `webkit`).

> -由于 QEMU 无法正确运行二进制文件，因此在特定体系结构（例如“gcr”、“libsecret”和“webkit”）下禁用了某些特定包的内省。

- QEMU usermode might not work properly when running 64-bit binaries under 32-bit host machines. In particular, \"qemumips64\" is known to not work under i686.

> -在 32 位主机上运行 64 位二进制文件时，QEMU 用户模式可能无法正常工作。特别是，已知“qemumips64\”在 i686 下不起作用。
