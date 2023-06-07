---
tip: translate by baidu@2023-06-07 17:15:29
...
---
title: Using a Python Development Shell
---------------------------------------

Similar to working within a development shell as described in the previous section, you can also spawn and work within an interactive Python development shell. When debugging certain commands or even when just editing packages, `pydevshell` can be a useful tool. When you invoke the `pydevshell` task, all tasks up to and including `ref-tasks-patch`{.interpreted-text role="ref"} are run for the specified target. Then a new terminal is opened. Additionally, key Python objects and code are available in the same way they are to BitBake tasks, in particular, the data store \'d\'. So, commands such as the following are useful when exploring the data store and running functions:

> 与上一节中描述的在开发 shell 中工作类似，您也可以在交互式 Python 开发 shell 中生成和工作。在调试某些命令时，甚至在编辑包时，“pydevshell”都是一个有用的工具。当您调用“pydevshell”任务时，所有直到并包括“ref tasks patch”｛.depreted text role=“ref”｝的任务都将为指定的目标运行。然后打开一个新的终端。此外，关键 Python 对象和代码的可用方式与 BitBake 任务的可用方式相同，特别是数据存储。因此，在探索数据存储和运行函数时，以下命令非常有用：

```
pydevshell> d.getVar("STAGING_DIR")
'/media/build1/poky/build/tmp/sysroots'
pydevshell> d.getVar("STAGING_DIR", False)
'${TMPDIR}/sysroots'
pydevshell> d.setVar("FOO", "bar")
pydevshell> d.getVar("FOO")
'bar'
pydevshell> d.delVar("FOO")
pydevshell> d.getVar("FOO")
pydevshell> bb.build.exec_func("do_unpack", d)
pydevshell>
```

See the \"`bitbake-user-manual/bitbake-user-manual-metadata:functions you can call from within python`{.interpreted-text role="ref"}\" section in the BitBake User Manual for details about available functions.

> 有关可用函数的详细信息，请参阅《bitbake 用户手册》中的\“`bitbake用户手册/bitbake用户手册元数据：您可以从python中调用的函数`{.depreted text role=“ref”}\”一节。

The commands execute just as if the OpenEmbedded build system were executing them. Consequently, working this way can be helpful when debugging a build or preparing software to be used with the OpenEmbedded build system.

> 这些命令的执行就像 OpenEmbedded 构建系统在执行它们一样。因此，在调试构建或准备与 OpenEmbedded 构建系统一起使用的软件时，以这种方式工作可能会有所帮助。

Following is an example that uses `pydevshell` on a target named `matchbox-desktop`:

> 以下是在名为“matchbox desktop”的目标上使用“pydevshell”的示例：

```
$ bitbake matchbox-desktop -c pydevshell
```

This command spawns a terminal and places you in an interactive Python interpreter within the OpenEmbedded build environment. The `OE_TERMINAL`{.interpreted-text role="term"} variable controls what type of shell is opened.

> 该命令生成一个终端，并将您放置在 OpenEmbedded 构建环境中的交互式 Python 解释器中。`OE_TERMINAL`｛.explored text role=“term”｝变量控制打开何种类型的 shell。

When you are finished using `pydevshell`, you can exit the shell either by using Ctrl+d or closing the terminal window.

> 使用完“pydevshell”后，可以使用 Ctrl+d 或关闭终端窗口退出 shell。
