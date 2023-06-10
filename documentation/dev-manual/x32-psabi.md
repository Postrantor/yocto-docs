---
tip: translate by openai@2023-06-10 12:50:13
...
---
title: Using x32 psABI
----------------------

x32 processor-specific Application Binary Interface ([x32 psABI](https://software.intel.com/en-us/node/628948)) is a native 32-bit processor-specific ABI for Intel 64 (x86-64) architectures. An ABI defines the calling conventions between functions in a processing environment. The interface determines what registers are used and what the sizes are for various C data types.

> x32 处理器特定应用程序二进制接口（[x32 psABI]（[https://software.intel.com/en-us/node/628948](https://software.intel.com/en-us/node/628948)））是 Intel 64（x86-64）架构的本机 32 位处理器特定 ABI。ABI 定义处理环境中函数之间的调用约定。该接口确定使用哪些寄存器以及各种 C 数据类型的大小。

Some processing environments prefer using 32-bit applications even when running on Intel 64-bit platforms. Consider the i386 psABI, which is a very old 32-bit ABI for Intel 64-bit platforms. The i386 psABI does not provide efficient use and access of the Intel 64-bit processor resources, leaving the system underutilized. Now consider the x86_64 psABI. This ABI is newer and uses 64-bits for data sizes and program pointers. The extra bits increase the footprint size of the programs, libraries, and also increases the memory and file system size requirements. Executing under the x32 psABI enables user programs to utilize CPU and system resources more efficiently while keeping the memory footprint of the applications low. Extra bits are used for registers but not for addressing mechanisms.

> 一些处理环境即使在运行 Intel 64 位平台时也更喜欢使用 32 位应用程序。请考虑 i386 psABI，这是一种非常老的用于 Intel 64 位平台的 32 位 ABI。i386 psABI 不能有效地使用和访问 Intel 64 位处理器资源，使系统未充分利用。现在考虑 x86_64 psABI。这个 ABI 更新，使用 64 位用于数据大小和程序指针。额外的位增加了程序，库和内存文件系统大小的脚本。在 x32 psABI 下执行可以使用户程序更有效地利用 CPU 和系统资源，同时保持应用程序的内存占用量低。额外的位用于寄存器，而不是用于地址机制。

The Yocto Project supports the final specifications of x32 psABI as follows:

- You can create packages and images in x32 psABI format on x86_64 architecture targets.
- You can successfully build recipes with the x32 toolchain.
- You can create and boot `core-image-minimal` and `core-image-sato` images.
- There is RPM Package Manager (RPM) support for x32 binaries.
- There is support for large images.

To use the x32 psABI, you need to edit your `conf/local.conf` configuration file as follows:

```
MACHINE = "qemux86-64"
DEFAULTTUNE = "x86-64-x32"
baselib = "${@d.getVar('BASE_LIB:tune-' + (d.getVar('DEFAULTTUNE') \
    or 'INVALID')) or 'lib'}"
```

Once you have set up your configuration file, use BitBake to build an image that supports the x32 psABI. Here is an example:

```
$ bitbake core-image-sato
```
