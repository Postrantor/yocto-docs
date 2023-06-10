---
tip: translate by openai@2023-06-10 10:49:18
...
---
title: Enabling GObject Introspection Support
---------------------------------------------

[GObject introspection](https://gi.readthedocs.io/en/latest/) is the standard mechanism for accessing GObject-based software from runtime environments. GObject is a feature of the GLib library that provides an object framework for the GNOME desktop and related software. GObject Introspection adds information to GObject that allows objects created within it to be represented across different programming languages. If you want to construct GStreamer pipelines using Python, or control UPnP infrastructure using Javascript and GUPnP, GObject introspection is the only way to do it.

> GObject Introspection 是从运行环境访问基于 GObject 的软件的标准机制。GObject 是 GLib 库的一个功能，为 GNOME 桌面和相关软件提供对象框架。GObject Introspection 为 GObject 添加信息，允许在其中创建的对象在不同的编程语言中表示。如果您想使用 Python 构建 GStreamer 管道，或使用 Javascript 和 GUPnP 控制 UPnP 基础架构，GObject Introspection 是唯一的方法。

This section describes the Yocto Project support for generating and packaging GObject introspection data. GObject introspection data is a description of the API provided by libraries built on top of the GLib framework, and, in particular, that framework\'s GObject mechanism. GObject Introspection Repository (GIR) files go to `-dev` packages, `typelib` files go to main packages as they are packaged together with libraries that are introspected.

> 这一节描述了 Yocto 项目支持生成和打包 GObject 内省数据的情况。GObject 内省数据是建立在 GLib 框架上的库提供的 API 的描述，特别是该框架的 GObject 机制。GObject 内省库（GIR）文件被打包到 `-dev` 软件包中，`typelib` 文件被打包到主软件包中，因为它们被内省的库一起打包。

The data is generated when building such a library, by linking the library with a small executable binary that asks the library to describe itself, and then executing the binary and processing its output.

> 当构建这样一个库时，通过将库链接到一个要求库描述自身的小可执行二进制文件，并执行该二进制文件并处理其输出，即可生成数据。

Generating this data in a cross-compilation environment is difficult because the library is produced for the target architecture, but its code needs to be executed on the build host. This problem is solved with the OpenEmbedded build system by running the code through QEMU, which allows precisely that. Unfortunately, QEMU does not always work perfectly as mentioned in the \"`dev-manual/gobject-introspection:known issues`{.interpreted-text role="ref"}\" section.

> 在交叉编译环境中生成这些数据很困难，因为该库是为目标架构生成的，但它的代码需要在构建主机上执行。 OpenEmbedded 构建系统通过运行 QEMU 来解决此问题，它允许精确地执行此操作。 不幸的是，正如“dev-manual / gobject-introspection：known issues”部分中所提到的，QEMU 并不总是完美地工作。

# Enabling the Generation of Introspection Data

Enabling the generation of introspection data (GIR files) in your library package involves the following:

1. Inherit the `ref-classes-gobject-introspection`{.interpreted-text role="ref"} class.
2. Make sure introspection is not disabled anywhere in the recipe or from anything the recipe includes. Also, make sure that \"gobject-introspection-data\" is not in `DISTRO_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"} and that \"qemu-usermode\" is not in `MACHINE_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"}. In either of these conditions, nothing will happen.

> 确保食谱中任何地方都没有禁用内省，也没有由食谱包括的任何东西禁用内省。此外，确保"gobject-introspection-data"不在 `DISTRO_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"}中，也确保"qemu-usermode"不在 `MACHINE_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"}中。在任何这种情况下，都不会发生任何事情。

3. Try to build the recipe. If you encounter build errors that look like something is unable to find `.so` libraries, check where these libraries are located in the source tree and add the following to the recipe:

> 尝试构建配方。如果遇到看起来无法找到 `.so` 库的构建错误，请检查这些库在源树中的位置，并添加以下内容到配方中：

```
GIR_EXTRA_LIBS_PATH = "${B}/something/.libs"
```

::: note
::: title
Note
:::

See recipes in the `oe-core` repository that use that `GIR_EXTRA_LIBS_PATH`{.interpreted-text role="term"} variable as an example.
:::

4. Look for any other errors, which probably mean that introspection support in a package is not entirely standard, and thus breaks down in a cross-compilation environment. For such cases, custom-made fixes are needed. A good place to ask and receive help in these cases is the `Yocto Project mailing lists <resources-mailinglist>`{.interpreted-text role="ref"}.

> 4. 查找其他可能意味着包中内省支持不完全标准，因此在跨编译环境中破坏的错误。 对于这种情况，需要定制修复。 在这些情况下寻求和接收帮助的好地方是 Yocto 项目邮件列表<resources-mailinglist>。

::: note
::: title
Note
:::

Using a library that no longer builds against the latest Yocto Project release and prints introspection related errors is a good candidate for the previous procedure.

> 使用不再与最新的 Yocto 项目发布构建并打印内省相关错误的库是前面程序的好候选。
> :::

# Disabling the Generation of Introspection Data

You might find that you do not want to generate introspection data. Or, perhaps QEMU does not work on your build host and target architecture combination. If so, you can use either of the following methods to disable GIR file generations:

> 您可能会发现您不想生成内省数据。或者，也许 QEMU 不适用于您的构建主机和目标架构组合。如果是这样，您可以使用以下任一方法来禁用 GIR 文件生成：

- Add the following to your distro configuration:

  ```
  DISTRO_FEATURES_BACKFILL_CONSIDERED = "gobject-introspection-data"
  ```

  Adding this statement disables generating introspection data using QEMU but will still enable building introspection tools and libraries (i.e. building them does not require the use of QEMU).

> 添加此声明会禁用使用 QEMU 生成内省数据，但仍然可以构建内省工具和库（即构建它们不需要使用 QEMU）。

- Add the following to your machine configuration:

  ```
  MACHINE_FEATURES_BACKFILL_CONSIDERED = "qemu-usermode"
  ```

  Adding this statement disables the use of QEMU when building packages for your machine. Currently, this feature is used only by introspection recipes and has the same effect as the previously described option.

> 此声明的添加会在为您的机器构建软件包时禁用 QEMU 的使用。目前，此功能仅由内省配方使用，其效果与先前描述的选项相同。

::: note
::: title
Note
:::

Future releases of the Yocto Project might have other features affected by this option.
:::

If you disable introspection data, you can still obtain it through other means such as copying the data from a suitable sysroot, or by generating it on the target hardware. The OpenEmbedded build system does not currently provide specific support for these techniques.

> 如果您禁用内省数据，您仍然可以通过其他手段获取它，例如从合适的 sysroot 复制数据，或者在目标硬件上生成它。OpenEmbedded 构建系统目前不提供这些技术的特定支持。

# Testing that Introspection Works in an Image

Use the following procedure to test if generating introspection data is working in an image:

1. Make sure that \"gobject-introspection-data\" is not in `DISTRO_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"} and that \"qemu-usermode\" is not in `MACHINE_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"}.

> 确保“gobject-introspection-data”不在 `DISTRO_FEATURES_BACKFILL_CONSIDERED` 中，而“qemu-usermode”不在 `MACHINE_FEATURES_BACKFILL_CONSIDERED` 中。

2. Build `core-image-sato`.
3. Launch a Terminal and then start Python in the terminal.
4. Enter the following in the terminal:

   ```
   >>> from gi.repository import GLib
   >>> GLib.get_host_name()
   ```
5. For something a little more advanced, enter the following see: [https://python-gtk-3-tutorial.readthedocs.io/en/latest/introduction.html](https://python-gtk-3-tutorial.readthedocs.io/en/latest/introduction.html)

> 如果想要更高级一点，请输入以下链接：[https://python-gtk-3-tutorial.readthedocs.io/en/latest/introduction.html](https://python-gtk-3-tutorial.readthedocs.io/en/latest/introduction.html)

# Known Issues

Here are know issues in GObject Introspection Support:

- `qemu-ppc64` immediately crashes. Consequently, you cannot build introspection data on that architecture.
- x32 is not supported by QEMU. Consequently, introspection data is disabled.
- musl causes transient GLib binaries to crash on assertion failures. Consequently, generating introspection data is disabled.
- Because QEMU is not able to run the binaries correctly, introspection is disabled for some specific packages under specific architectures (e.g. `gcr`, `libsecret`, and `webkit`).

> 由于 QEMU 无法正确运行二进制文件，在特定架构（例如 `gcr`、`libsecret` 和 `webkit`）下，一些特定的包的内省功能被禁用。

- QEMU usermode might not work properly when running 64-bit binaries under 32-bit host machines. In particular, \"qemumips64\" is known to not work under i686.

> QEMU 用户模式在 32 位主机上运行 64 位二进制文件时可能无法正常工作。特别是，已知“qemumips64”在 i686 上无法正常工作。
