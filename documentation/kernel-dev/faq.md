---
tip: translate by baidu@2023-06-07 10:57:38
...
---
subtitle: Common Questions and Solutions

> 副标题：常见问题和解决方案
> title: Kernel Development FAQ

---

Here are some solutions for common questions.

> 以下是一些常见问题的解决方案。

# How do I use my own Linux kernel `.config` file?

Refer to the \"`kernel-dev/common:changing the configuration`{.interpreted-text role="ref"}\" section for information.

> 有关信息，请参阅“`kernel dev/common:更改配置`{.depreted text role=“ref”}\”一节。

# How do I create configuration fragments?

A: Refer to the \"`kernel-dev/common:creating configuration fragments`{.interpreted-text role="ref"}\" section for information.

> A： 有关信息，请参阅“`kernel dev/common:createing configuration fragments`｛.depreted text role=“ref”｝”一节。

# How do I use my own Linux kernel sources?

Refer to the \"`kernel-dev/common:working with your own sources`{.interpreted-text role="ref"}\" section for information.

> 有关信息，请参阅“`kernel dev/common:使用您自己的源代码`{.depreted text role=“ref”}\”一节。

# How do I install/not-install the kernel image on the root filesystem?

The kernel image (e.g. `vmlinuz`) is provided by the `kernel-image` package. Image recipes depend on `kernel-base`. To specify whether or not the kernel image is installed in the generated root filesystem, override `RRECOMMENDS:${KERNEL_PACKAGE_NAME}-base` to include or not include \"kernel-image\". See the \"`dev-manual/layers:appending other layers metadata with your layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for information on how to use an append file to override metadata.

> 内核映像（例如“vmlinuz”）由“内核映像”包提供。图像配方取决于“内核基础”。要指定内核映像是否安装在生成的根文件系统中，请重写 `RRECOMMENDS:$｛kernel_PACKAGE_NAME｝-base` 以包括或不包括\“内核映像\”。有关如何使用附加文件覆盖元数据的信息，请参阅 Yocto 项目开发任务手册中的\“`devmanual/layers:appending other layers metadata with your layer`｛.depreced text role=“ref”｝\”一节。

# How do I install a specific kernel module?

Linux kernel modules are packaged individually. To ensure a specific kernel module is included in an image, include it in the appropriate machine `RRECOMMENDS`{.interpreted-text role="term"} variable. These other variables are useful for installing specific modules: - `MACHINE_ESSENTIAL_EXTRA_RDEPENDS`{.interpreted-text role="term"} - `MACHINE_ESSENTIAL_EXTRA_RRECOMMENDS`{.interpreted-text role="term"} - `MACHINE_EXTRA_RDEPENDS`{.interpreted-text role="term"} - `MACHINE_EXTRA_RRECOMMENDS`{.interpreted-text role="term"}

> Linux 内核模块是单独打包的。为了确保特定的内核模块包含在映像中，请将其包含在适当的机器 `RRECOMMENDS`{.depreted text role=“term”}变量中。这些其他变量对于安装特定模块很有用：-`MACHINE_ESSENTIAL_EXTRA_RDEPENDS`｛.explored text role=“term”｝-`MACHENE_ESSENTIAL_EXTRA _RCOMMONES`｛.sexplered text rol=“term”｝-`MACHINE _EXTRA-RDEPENDS'｛

For example, set the following in the `qemux86.conf` file to include the `ab123` kernel modules with images built for the `qemux86` machine:

> 例如，在“qemux86.conf”文件中设置以下内容，以包括“ab123”内核模块和为“qemu x86”机器构建的映像：

```
MACHINE_EXTRA_RRECOMMENDS += "kernel-module-ab123"
```

For more information, see the \"`kernel-dev/common:incorporating out-of-tree modules`{.interpreted-text role="ref"}\" section.

> 有关更多信息，请参阅“`kernel dev/common:合并树外模块`{.depreted text role=“ref”}\”一节。

# How do I change the Linux kernel command line?

The Linux kernel command line is typically specified in the machine config using the `APPEND`{.interpreted-text role="term"} variable. For example, you can add some helpful debug information doing the following:

> Linux 内核命令行通常在机器配置中使用 `APPEND `｛.explored text role=“term”｝变量指定。例如，可以通过以下操作添加一些有用的调试信息：

```
APPEND += "printk.time=y initcall_debug debug"
```
