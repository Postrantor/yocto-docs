---
tip: translate by openai@2023-06-10 10:42:57
...
---
title: Using a Development Shell
--------------------------------

When debugging certain commands or even when just editing packages, `devshell` can be a useful tool. When you invoke `devshell`, all tasks up to and including `ref-tasks-patch`{.interpreted-text role="ref"} are run for the specified target. Then, a new terminal is opened and you are placed in `${``S`{.interpreted-text role="term"}`}`, the source directory. In the new terminal, all the OpenEmbedded build-related environment variables are still defined so you can use commands such as `configure` and `make`. The commands execute just as if the OpenEmbedded build system were executing them. Consequently, working this way can be helpful when debugging a build or preparing software to be used with the OpenEmbedded build system.

> 当调试某些命令或者只是编辑软件包时，`devshell` 可以是一个有用的工具。当您调用 `devshell` 时，所有任务都会为指定的目标运行到 `ref-tasks-patch`{.interpreted-text role="ref"}。然后，会打开一个新的终端，并将您放置在 `${``S`{.interpreted-text role="term"}`}` 源目录中。在新的终端中，所有与 OpenEmbedded 构建相关的环境变量仍然生效，因此您可以使用 `configure` 和 `make` 等命令。这些命令的执行就像 OpenEmbedded 构建系统正在执行它们一样。因此，以这种方式工作可以在调试构建或准备使用 OpenEmbedded 构建系统的软件时有所帮助。

Following is an example that uses `devshell` on a target named `matchbox-desktop`:

```
$ bitbake matchbox-desktop -c devshell
```

This command spawns a terminal with a shell prompt within the OpenEmbedded build environment. The `OE_TERMINAL`{.interpreted-text role="term"} variable controls what type of shell is opened.

> 这个命令在 OpenEmbedded 构建环境中启动一个带有 shell 提示符的终端。`OE_TERMINAL`{.interpreted-text role="term"}变量控制打开的 shell 类型。

For spawned terminals, the following occurs:

- The `PATH` variable includes the cross-toolchain.
- The `pkgconfig` variables find the correct `.pc` files.
- The `configure` command finds the Yocto Project site files as well as any other necessary files.

Within this environment, you can run configure or compile commands as if they were being run by the OpenEmbedded build system itself. As noted earlier, the working directory also automatically changes to the Source Directory (`S`{.interpreted-text role="term"}).

> 在这个环境中，您可以像 OpenEmbedded 构建系统本身一样运行配置或编译命令。正如前面提到的，工作目录也会自动更改为源目录（`S`{.interpreted-text role="term"}）。

To manually run a specific task using `devshell`, run the corresponding `run.*` script in the `${``WORKDIR`{.interpreted-text role="term"}`}/temp` directory (e.g., `run.do_configure.`[pid]{.title-ref}). If a task\'s script does not exist, which would be the case if the task was skipped by way of the sstate cache, you can create the task by first running it outside of the `devshell`:

> 要使用 devshell 手动运行特定任务，请在 ${WORKDIR}/temp 目录中运行相应的 run.*脚本（例如 run.do_configure.[pid]）。如果任务脚本不存在（通过 sstate 缓存跳过任务时会发生），您可以通过首先在 devshell 之外运行该任务来创建该任务。

```
$ bitbake -c task
```

::: note
::: title
Note
:::

- Execution of a task\'s `run.*` script and BitBake\'s execution of a task are identical. In other words, running the script re-runs the task just as it would be run using the `bitbake -c` command.

> 执行任务的 `run.*` 脚本和 BitBake 执行任务是相同的。换句话说，运行脚本就像使用 `bitbake -c` 命令重新运行任务一样。

- Any `run.*` file that does not have a `.pid` extension is a symbolic link (symlink) to the most recent version of that file.
  :::

Remember, that the `devshell` is a mechanism that allows you to get into the BitBake task execution environment. And as such, all commands must be called just as BitBake would call them. That means you need to provide the appropriate options for cross-compilation and so forth as applicable.

> 记住，`devshell` 是一种允许你进入 BitBake 任务执行环境的机制。因此，所有命令都必须就像 BitBake 调用它们一样调用。这意味着您需要提供适当的跨编译选项等。

When you are finished using `devshell`, exit the shell or close the terminal window.

::: note
::: title
Note
:::

- It is worth remembering that when using `devshell` you need to use the full compiler name such as `arm-poky-linux-gnueabi-gcc` instead of just using `gcc`. The same applies to other applications such as `binutils`, `libtool` and so forth. BitBake sets up environment variables such as `CC`{.interpreted-text role="term"} to assist applications, such as `make` to find the correct tools.

> 记住，当使用 `devshell` 时，你需要使用完整的编译器名称，例如 `arm-poky-linux-gnueabi-gcc`，而不是只使用 `gcc`。其他应用程序，如 `binutils`、`libtool` 等也是如此。BitBake 设置环境变量，如 `CC`，以帮助应用程序，如 `make` 找到正确的工具。

- It is also worth noting that `devshell` still works over X11 forwarding and similar situations.
  :::
