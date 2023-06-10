---
tip: translate by openai@2023-06-10 12:41:56
...
---
title: Using Wayland and Weston
-------------------------------

`Wayland <Wayland_(display_server_protocol)>` is a computer display server protocol that provides a method for compositing window managers to communicate directly with applications and video hardware and expects them to communicate with input hardware using other libraries. Using Wayland with supporting targets can result in better control over graphics frame rendering than an application might otherwise achieve.

> Wayland(显示服务器协议)是一种计算机显示服务器协议，它提供了一种组合窗口管理器直接与应用程序和视频硬件通信的方法，并期望它们使用其他库与输入硬件通信。使用支持目标的 Wayland 可以比应用程序本身可以获得的更好的图形帧渲染控制。

The Yocto Project provides the Wayland protocol libraries and the reference `Weston <Wayland_(display_server_protocol)#Weston>`. Specifically, you can find the recipes that build both Wayland and Weston at `meta/recipes-graphics/wayland`.

> 项目 Yocto 提供 Wayland 协议库和参考 Weston(Wayland 显示服务器协议的一部分)组合器作为其发行版的一部分。您可以在源目录的 `meta` 层中找到集成的软件包。具体来说，您可以在 `meta/recipes-graphics/wayland` 中找到构建 Wayland 和 Weston 的配方。

You can build both the Wayland and Weston packages for use only with targets that accept the `Mesa 3D and Direct Rendering Infrastructure <Mesa_(computer_graphics)>`, which is also known as Mesa DRI. This implies that you cannot build and use the packages if your target uses, for example, the Intel Embedded Media and Graphics Driver (Intel EMGD) that overrides Mesa DRI.

> 你只能用接受 Mesa 3D 和 Direct Rendering Infrastructure <Mesa_(computer_graphics)> 的目标来构建 Wayland 和 Weston 软件包。这意味着如果你的目标使用 Intel Embedded Media and Graphics Driver (Intel EMGD)来覆盖 Mesa DRI，你就不能构建和使用这些软件包。

::: note
::: title
Note
:::

Due to lack of EGL support, Weston 1.0.3 will not run directly on the emulated QEMU hardware. However, this version of Weston will run under X emulation without issues.

> 由于缺少 EGL 支持，Weston 1.0.3 无法直接在模拟的 QEMU 硬件上运行。但是，这个版本的 Weston 可以在 X 模拟下正常运行。
> :::

This section describes what you need to do to implement Wayland and use the Weston compositor when building an image for a supporting target.

# Enabling Wayland in an Image

To enable Wayland, you need to enable it to be built and enable it to be included (installed) in the image.

## Building Wayland

To cause Mesa to build the `wayland-egl` platform and Weston to build Wayland with Kernel Mode Setting ([KMS](https://wiki.archlinux.org/index.php/Kernel_Mode_Setting)) support, include the \"wayland\" flag in the `DISTRO_FEATURES` statement in your `local.conf` file:

> 要使 Mesa 构建 `wayland-egl` 平台并使 Weston 构建具有内核模式设置(KMS)支持的 Wayland，请在 `local.conf` 文件中的 `DISTRO_FEATURES` 语句中包含“wayland”标志。

```
DISTRO_FEATURES:append = " wayland"
```

::: note
::: title
Note
:::

If X11 has been enabled elsewhere, Weston will build Wayland with X11 support
:::

## Installing Wayland and Weston

To install the Wayland feature into an image, you must include the following `CORE_IMAGE_EXTRA_INSTALL` statement in your `local.conf` file:

> 要将 Wayland 功能安装到镜像中，您必须在 local.conf 文件中包含以下 CORE_IMAGE_EXTRA_INSTALL 语句：

```
CORE_IMAGE_EXTRA_INSTALL += "wayland weston"
```

# Running Weston

To run Weston inside X11, enabling it as described earlier and building a Sato image is sufficient. If you are running your image under Sato, a Weston Launcher appears in the \"Utility\" category.

> 要在 X11 中运行 Weston，按照前面描述的方式启用它并构建 Sato 镜像就足够了。如果您正在 Sato 下运行镜像，则 Weston Launcher 会出现在“实用程序”类别中。

Alternatively, you can run Weston through the command-line interpretor (CLI), which is better suited for development work. To run Weston under the CLI, you need to do the following after your image is built:

> 另外，您可以通过命令行解释器(CLI)运行 Weston，这更适合开发工作。在构建镜像后，要在 CLI 下运行 Weston，您需要执行以下操作：

1. Run these commands to export `XDG_RUNTIME_DIR`:

   ```
   mkdir -p /tmp/$USER-weston
   chmod 0700 /tmp/$USER-weston
   export XDG_RUNTIME_DIR=/tmp/$USER-weston
   ```
2. Launch Weston in the shell:

   ```
   weston
   ```
