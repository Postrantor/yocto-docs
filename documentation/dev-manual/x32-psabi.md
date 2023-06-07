---
tip: translate by baidu@2023-06-07 17:19:09
...
---
title: Using x32 psABI
----------------------

x32 processor-specific Application Binary Interface ([x32 psABI](https://software.intel.com/en-us/node/628948)) is a native 32-bit processor-specific ABI for Intel 64 (x86-64) architectures. An ABI defines the calling conventions between functions in a processing environment. The interface determines what registers are used and what the sizes are for various C data types.

> x32 处理器特定的应用程序二进制接口（[x32 psABI](https://software.intel.com/en-us/node/628948))是适用于 Intel 64（x86-64）体系结构的本机 32 位处理器专用 ABI。ABI 定义了处理环境中函数之间的调用约定。该接口确定使用什么寄存器以及各种 C 数据类型的大小。

Some processing environments prefer using 32-bit applications even when running on Intel 64-bit platforms. Consider the i386 psABI, which is a very old 32-bit ABI for Intel 64-bit platforms. The i386 psABI does not provide efficient use and access of the Intel 64-bit processor resources, leaving the system underutilized. Now consider the x86_64 psABI. This ABI is newer and uses 64-bits for data sizes and program pointers. The extra bits increase the footprint size of the programs, libraries, and also increases the memory and file system size requirements. Executing under the x32 psABI enables user programs to utilize CPU and system resources more efficiently while keeping the memory footprint of the applications low. Extra bits are used for registers but not for addressing mechanisms.

> 某些处理环境更喜欢使用 32 位应用程序，即使在英特尔 64 位平台上运行也是如此。以 i386 psABI 为例，它是一个非常古老的 32 位 ABI，适用于 Intel 64 位平台。i386 psABI 无法有效使用和访问 Intel 64 位处理器资源，导致系统未得到充分利用。现在考虑 x86_64 psABI。这个 ABI 是较新的，并且使用 64 位来表示数据大小和程序指针。额外的位增加了程序、库的占用空间大小，也增加了对内存和文件系统大小的要求。在 x32psABI 下执行使用户程序能够更有效地利用 CPU 和系统资源，同时保持应用程序的低内存占用率。额外的位用于寄存器，但不用于寻址机制。

The Yocto Project supports the final specifications of x32 psABI as follows:

> Yocto 项目支持 x32 psABI 的最终规范如下：

- You can create packages and images in x32 psABI format on x86_64 architecture targets.
- You can successfully build recipes with the x32 toolchain.
- You can create and boot `core-image-minimal` and `core-image-sato` images.
- There is RPM Package Manager (RPM) support for x32 binaries.
- There is support for large images.

To use the x32 psABI, you need to edit your `conf/local.conf` configuration file as follows:

> 要使用 x32psABI，您需要编辑“conf/local.conf”配置文件，如下所示：

```
MACHINE = "qemux86-64"
DEFAULTTUNE = "x86-64-x32"
baselib = "${@d.getVar('BASE_LIB:tune-' + (d.getVar('DEFAULTTUNE') \
    or 'INVALID')) or 'lib'}"
```

Once you have set up your configuration file, use BitBake to build an image that supports the x32 psABI. Here is an example:

> 设置好配置文件后，使用 BitBake 构建一个支持 x32 psABI 的映像。以下是一个示例：

```
$ bitbake core-image-sato
```
