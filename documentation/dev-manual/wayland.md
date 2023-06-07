---
tip: translate by baidu@2023-06-07 17:18:32
...
---
title: Using Wayland and Weston
-------------------------------

`Wayland <Wayland_(display_server_protocol)>`{.interpreted-text role="wikipedia"} is a computer display server protocol that provides a method for compositing window managers to communicate directly with applications and video hardware and expects them to communicate with input hardware using other libraries. Using Wayland with supporting targets can result in better control over graphics frame rendering than an application might otherwise achieve.

> `Wayland<Wayland_（display_server_procol）>`{.depredicted text role=“wikipedia”}是一种计算机显示服务器协议，它提供了一种合成窗口管理器的方法，以直接与应用程序和视频硬件通信，并期望它们使用其他库与输入硬件通信。将 Wayland 与支持目标一起使用，可以比应用程序更好地控制图形帧渲染。

The Yocto Project provides the Wayland protocol libraries and the reference `Weston <Wayland_(display_server_protocol)#Weston>`{.interpreted-text role="wikipedia"} compositor as part of its release. You can find the integrated packages in the `meta` layer of the `Source Directory`{.interpreted-text role="term"}. Specifically, you can find the recipes that build both Wayland and Weston at `meta/recipes-graphics/wayland`.

> Yocto 项目提供了 Wayland 协议库和参考 `Weston<Wayland_（display_server_procol）#Weston>`{.depredicted text role=“wikipedia”}合成器，作为其发布的一部分。您可以在“源目录”的“元”层中找到集成包｛.depreted text role=“term”｝。具体地说，你可以在“meta/precipes graphics/Wayland”上找到构建 Wayland 和 Weston 的食谱。

You can build both the Wayland and Weston packages for use only with targets that accept the `Mesa 3D and Direct Rendering Infrastructure <Mesa_(computer_graphics)>`{.interpreted-text role="wikipedia"}, which is also known as Mesa DRI. This implies that you cannot build and use the packages if your target uses, for example, the Intel Embedded Media and Graphics Driver (Intel EMGD) that overrides Mesa DRI.

> 您可以构建 Wayland 和 Weston 软件包，仅用于接受 `Mesa 3D和直接渲染基础设施<Mesa_（computer_graphics）>`{.depredicted text role=“wikipedia”}（也称为 Mesa DRI）的目标。这意味着，如果您的目标使用（例如）覆盖 Mesa DRI 的 Intel Embedded Media and Graphics Driver（Intel EMGD），则无法构建和使用这些包。

::: note
::: title
Note
:::

Due to lack of EGL support, Weston 1.0.3 will not run directly on the emulated QEMU hardware. However, this version of Weston will run under X emulation without issues.

> 由于缺乏 EGL 支持，Weston 1.0.3 将不会直接在模拟的 QEMU 硬件上运行。然而，这个版本的 Weston 将在 X 仿真下运行而不会出现问题。
> :::

This section describes what you need to do to implement Wayland and use the Weston compositor when building an image for a supporting target.

> 本节描述了在为支持目标构建图像时，实现 Wayland 和使用 Weston 合成器需要做什么。

# Enabling Wayland in an Image

To enable Wayland, you need to enable it to be built and enable it to be included (installed) in the image.

> 要启用 Wayland，您需要启用它并将其包含（安装）在映像中。

## Building Wayland

To cause Mesa to build the `wayland-egl` platform and Weston to build Wayland with Kernel Mode Setting ([KMS](https://wiki.archlinux.org/index.php/Kernel_Mode_Setting)) support, include the \"wayland\" flag in the `DISTRO_FEATURES`{.interpreted-text role="term"} statement in your `local.conf` file:

> 使 Mesa 建立“wayland egl”平台，并使 Weston 建立具有内核模式设置的 wayland（[KMS](https://wiki.archlinux.org/index.php/Kernel_Mode_Setting))支持，请将\“wayland\”标志包含在您的“local.conf”文件中的 `DISTRO_FEATURE`｛.depreted text role=“term”｝语句中：

```
DISTRO_FEATURES:append = " wayland"
```

::: note
::: title
Note
:::

If X11 has been enabled elsewhere, Weston will build Wayland with X11 support

> 如果 X11 已经在其他地方启用，Weston 将使用 X11 支持构建 Wayland
> :::

## Installing Wayland and Weston

To install the Wayland feature into an image, you must include the following `CORE_IMAGE_EXTRA_INSTALL`{.interpreted-text role="term"} statement in your `local.conf` file:

> 要将 Wayland 功能安装到映像中，您必须在 `local.conf` 文件中包含以下 `CORE_image_EXTRA_INSTAL`{.depreted text role=“term”}语句：

```
CORE_IMAGE_EXTRA_INSTALL += "wayland weston"
```

# Running Weston

To run Weston inside X11, enabling it as described earlier and building a Sato image is sufficient. If you are running your image under Sato, a Weston Launcher appears in the \"Utility\" category.

> 要在 X11 中运行 Weston，如前所述启用它并构建一个 Sato 映像就足够了。如果您在 Sato 下运行映像，Weston Launcher 会出现在“实用程序”类别中。

Alternatively, you can run Weston through the command-line interpretor (CLI), which is better suited for development work. To run Weston under the CLI, you need to do the following after your image is built:

> 或者，您可以通过命令行解释器（CLI）运行 Weston，这更适合于开发工作。要在 CLI 下运行 Weston，您需要在构建映像后执行以下操作：

1. Run these commands to export `XDG_RUNTIME_DIR`:

> 1.运行以下命令导出 `XDG_RUNTIME_DIR`：

```

> ```

mkdir -p /tmp/$USER-weston

> mkdir-p/tmp/$USER韦斯顿

chmod 0700 /tmp/$USER-weston

> chmod 0700/tmp/$USER韦斯顿

export XDG_RUNTIME_DIR=/tmp/$USER-weston

> export XDG_RUNTIME_DIR=/tmp/$USER韦斯顿

```

> ```
> ```

2. Launch Weston in the shell:

> 2.在壳中发射韦斯顿：

```

> ```

weston

> 韦斯顿

```

> ```
> ```
