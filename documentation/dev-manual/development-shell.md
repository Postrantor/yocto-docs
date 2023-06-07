---
tip: translate by baidu@2023-06-07 17:11:45
...
---
title: Using a Development Shell
--------------------------------

When debugging certain commands or even when just editing packages, `devshell` can be a useful tool. When you invoke `devshell`, all tasks up to and including `ref-tasks-patch`{.interpreted-text role="ref"} are run for the specified target. Then, a new terminal is opened and you are placed in `${``S`{.interpreted-text role="term"}`}`, the source directory. In the new terminal, all the OpenEmbedded build-related environment variables are still defined so you can use commands such as `configure` and `make`. The commands execute just as if the OpenEmbedded build system were executing them. Consequently, working this way can be helpful when debugging a build or preparing software to be used with the OpenEmbedded build system.

> 在调试某些命令时，甚至在编辑包时，“devshell”都是一个有用的工具。当您调用“devshell”时，所有直到并包括“ref tasks patch”｛.explored text role=“ref”｝的任务都将为指定的目标运行。然后，一个新的终端被打开，您被放置在源目录 `${``S`{.depredicted text role=“term”}`}` 中。在新的终端中，所有与 OpenEmbedded 构建相关的环境变量仍然被定义，因此您可以使用诸如“configure”和“make”之类的命令。这些命令的执行就像 OpenEmbedded 构建系统在执行它们一样。因此，在调试构建或准备与 OpenEmbedded 构建系统一起使用的软件时，以这种方式工作可能会有所帮助。

Following is an example that uses `devshell` on a target named `matchbox-desktop`:

> 以下是在名为“matchbox desktop”的目标上使用“devshell”的示例：

```
$ bitbake matchbox-desktop -c devshell
```

This command spawns a terminal with a shell prompt within the OpenEmbedded build environment. The `OE_TERMINAL`{.interpreted-text role="term"} variable controls what type of shell is opened.

> 此命令在 OpenEmbedded 构建环境中生成一个带有 shell 提示的终端。`OE_TERMINAL`｛.explored text role=“term”｝变量控制打开何种类型的 shell。

For spawned terminals, the following occurs:

> 对于衍生的端子，会出现以下情况：

- The `PATH` variable includes the cross-toolchain.
- The `pkgconfig` variables find the correct `.pc` files.
- The `configure` command finds the Yocto Project site files as well as any other necessary files.

Within this environment, you can run configure or compile commands as if they were being run by the OpenEmbedded build system itself. As noted earlier, the working directory also automatically changes to the Source Directory (`S`{.interpreted-text role="term"}).

> 在此环境中，您可以运行配置或编译命令，就好像它们是由 OpenEmbedded 构建系统本身运行一样。如前所述，工作目录也会自动更改为源目录（`S`{.depreted text role=“term”}）。

To manually run a specific task using `devshell`, run the corresponding `run.*` script in the `${``WORKDIR`{.interpreted-text role="term"}`}/temp` directory (e.g., `run.do_configure.`[pid]{.title-ref}). If a task\'s script does not exist, which would be the case if the task was skipped by way of the sstate cache, you can create the task by first running it outside of the `devshell`:

> 要使用“devshell”手动运行特定任务，请在“$｛`WORKDIR`｛.explored text role=“term”｝`｝/temp”目录中运行相应的“run.*”脚本（例如，“run.do_configure.`[pid]｛.title-ref｝”）。如果任务的脚本不存在（如果通过 sstate 缓存跳过该任务，则会出现这种情况），则可以通过首先在“devshell’”外部运行该任务来创建该任务：

```
$ bitbake -c task
```

::: note
::: title
Note
:::

- Execution of a task\'s `run.*` script and BitBake\'s execution of a task are identical. In other words, running the script re-runs the task just as it would be run using the `bitbake -c` command.

> -任务的“run.*”脚本的执行和 BitBake 任务的执行是相同的。换句话说，运行脚本会重新运行任务，就像使用“bitbake-c”命令运行任务一样。

- Any `run.*` file that does not have a `.pid` extension is a symbolic link (symlink) to the most recent version of that file.

> -任何没有“.pid”扩展名的“run.*”文件都是指向该文件最新版本的符号链接（符号链接）。
> :::

Remember, that the `devshell` is a mechanism that allows you to get into the BitBake task execution environment. And as such, all commands must be called just as BitBake would call them. That means you need to provide the appropriate options for cross-compilation and so forth as applicable.

> 请记住，“devshell”是一种允许您进入 BitBake 任务执行环境的机制。因此，所有命令都必须像 BitBake 调用它们一样进行调用。这意味着您需要为交叉编译等提供适当的选项。

When you are finished using `devshell`, exit the shell or close the terminal window.

> 使用完“devshell”后，退出 shell 或关闭终端窗口。

::: note
::: title
Note
:::

- It is worth remembering that when using `devshell` you need to use the full compiler name such as `arm-poky-linux-gnueabi-gcc` instead of just using `gcc`. The same applies to other applications such as `binutils`, `libtool` and so forth. BitBake sets up environment variables such as `CC`{.interpreted-text role="term"} to assist applications, such as `make` to find the correct tools.

> -值得记住的是，当使用“devshell”时，您需要使用完整的编译器名称，如“arm-poky-linux-gnueabi-gcc”，而不是仅使用“gcc”。这同样适用于其他应用程序，如“binutils”、“libtool”等。BitBake 设置环境变量，如“CC”｛.explored text role=“term”｝，以帮助应用程序（如“make”）找到正确的工具。

- It is also worth noting that `devshell` still works over X11 forwarding and similar situations.
  :::
