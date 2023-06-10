---
tip: translate by openai@2023-06-07 21:07:59
...
---
title: Preparing to Use Toaster
-------------------------------

This chapter describes how you need to prepare your system in order to use Toaster.

> 本章节介绍了您需要如何准备您的系统，以便使用 Toaster。

# Setting Up the Basic System Requirements

Before you can use Toaster, you need to first set up your build system to run the Yocto Project. To do this, follow the instructions in the \"`dev-manual/start:preparing the build host`\" section of the Yocto Project Development Tasks Manual. For Ubuntu/Debian, you might also need to do an additional install of pip3. :

> 在使用 Toaster 之前，您需要首先设置构建系统以运行 Yocto Project。要执行此操作，请按照 Yocto Project 开发任务手册中“dev-manual / start：准备构建主机”部分的说明操作。对于 Ubuntu / Debian，您可能还需要额外安装 pip3。

```shell
$ sudo apt install python3-pip
```

# Establishing Toaster System Dependencies

Toaster requires extra Python dependencies in order to run. A Toaster requirements file named `toaster-requirements.txt` defines the Python dependencies. The requirements file is located in the `bitbake` directory, which is located in the root directory of the `Source Directory` (e.g. `poky/bitbake/toaster-requirements.txt`). The dependencies appear in a `pip`, install-compatible format.

> 为了运行，烤面包机需要额外的 Python 依赖项。名为'toaster-requirements.txt'的烤面包机要求文件定义了 Python 依赖项。该要求文件位于源目录的根目录中的 `bitbake` 目录(例如 `poky/bitbake/toaster-requirements.txt`)中。依赖项以 `pip` 安装兼容格式出现。

## Install Toaster Packages

You need to install the packages that Toaster requires. Use this command:

> 你需要安装 Toaster 所需的包。使用这个命令：

```shell
$ pip3 install --user -r bitbake/toaster-requirements.txt
```

The previous command installs the necessary Toaster modules into a local Python 3 cache in your `$HOME` directory. The caches is actually located in `$HOME/.local`. To see what packages have been installed into your `$HOME` directory, do the following:

> 上一个命令将必要的 Toaster 模块安装到您的 `$HOME` 目录中的本地 Python 3 缓存中。缓存实际上位于 `$HOME/.local` 中。要查看已安装到您的 `$HOME` 目录中的哪些软件包，请执行以下操作：

```shell
$ pip3 list installed --local
```

If you need to remove something, the following works:

> 如果你需要移除某些东西，可以使用以下方法：

```shell
$ pip3 uninstall PackageNameToUninstall
```
