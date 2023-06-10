---
tip: translate by openai@2023-06-10 10:40:18
...
---
subtitle: Common Questions and Solutions
title: Kernel Development FAQ
-----------------------------

Here are some solutions for common questions.

# How do I use my own Linux kernel `.config` file?

Refer to the \"`kernel-dev/common:changing the configuration`\" section for information.

# How do I create configuration fragments?

A: Refer to the \"`kernel-dev/common:creating configuration fragments`\" section for information.

# How do I use my own Linux kernel sources?

Refer to the \"`kernel-dev/common:working with your own sources`\" section for information.

# How do I install/not-install the kernel image on the root filesystem?

The kernel image (e.g. `vmlinuz`) is provided by the `kernel-image` package. Image recipes depend on `kernel-base`. To specify whether or not the kernel image is installed in the generated root filesystem, override `RRECOMMENDS:$\" section in the Yocto Project Development Tasks Manual for information on how to use an append file to override metadata.

> 内核映像(例如 `vmlinuz`)由 `kernel-image` 软件包提供。映像配方取决于 `kernel-base`。要指定是否将内核映像安装在生成的根文件系统中，请覆盖 `RRECOMMENDS：$-base` 以包括或不包括“kernel-image”。有关如何使用追加文件覆盖元数据的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/layers：使用您的层附加其他层元数据”部分。

# How do I install a specific kernel module?

Linux kernel modules are packaged individually. To ensure a specific kernel module is included in an image, include it in the appropriate machine `RRECOMMENDS`

> 模块单独打包。要确保某个特定的内核模块包含在镜像中，请将其包含在适当的机器 `RRECOMMENDS`

模块单独打包。要确保某个特定的内核模块包含在镜像中，请将其包含在适当的机器 `RRECOMMENDS` 变量中。这些其他变量对于安装特定模块很有用：- `MACHINE_ESSENTIAL_EXTRA_RDEPENDS` - `MACHINE_ESSENTIAL_EXTRA_RRECOMMENDS` - `MACHINE_EXTRA_RDEPENDS` - `MACHINE_EXTRA_RRECOMMENDS`

For example, set the following in the `qemux86.conf` file to include the `ab123` kernel modules with images built for the `qemux86` machine:

```
MACHINE_EXTRA_RRECOMMENDS += "kernel-module-ab123"
```

For more information, see the \"`kernel-dev/common:incorporating out-of-tree modules`\" section.

# How do I change the Linux kernel command line?

The Linux kernel command line is typically specified in the machine config using the `APPEND` variable. For example, you can add some helpful debug information doing the following:

> Linux 内核命令行通常在机器配置中使用 `APPEND` 变量指定。例如，您可以添加一些有用的调试信息，执行以下操作：

```
APPEND += "printk.time=y initcall_debug debug"
```
