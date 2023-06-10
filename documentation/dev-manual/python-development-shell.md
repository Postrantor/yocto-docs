---
tip: translate by openai@2023-06-10 11:59:49
...
---
title: Using a Python Development Shell
---------------------------------------

Similar to working within a development shell as described in the previous section, you can also spawn and work within an interactive Python development shell. When debugging certain commands or even when just editing packages, `pydevshell` can be a useful tool. When you invoke the `pydevshell` task, all tasks up to and including `ref-tasks-patch` are run for the specified target. Then a new terminal is opened. Additionally, key Python objects and code are available in the same way they are to BitBake tasks, in particular, the data store \'d\'. So, commands such as the following are useful when exploring the data store and running functions:

> 类似于在上一节中描述的开发 shell 中的工作，您也可以生成和在交互式 Python 开发 shell 中工作。在调试某些命令或甚至编辑软件包时，`pydevshell` 可能是一个有用的工具。当您调用 `pydevshell` 任务时，为指定的目标运行所有任务，包括 `ref-tasks-patch`。然后打开一个新的终端。另外，关键的 Python 对象和代码以与 BitBake 任务相同的方式可用，特别是数据存储' d '。因此，当探索数据存储并运行函数时，诸如以下命令是有用的：

```
pydevshell> d.getVar("STAGING_DIR")
'/media/build1/poky/build/tmp/sysroots'
pydevshell> d.getVar("STAGING_DIR", False)
'$/sysroots'
pydevshell> d.setVar("FOO", "bar")
pydevshell> d.getVar("FOO")
'bar'
pydevshell> d.delVar("FOO")
pydevshell> d.getVar("FOO")
pydevshell> bb.build.exec_func("do_unpack", d)
pydevshell>
```

See the \"`bitbake-user-manual/bitbake-user-manual-metadata:functions you can call from within python`\" section in the BitBake User Manual for details about available functions.

> 请参阅 BitBake 用户手册中的“bitbake-user-manual / bitbake-user-manual-metadata：您可以从 Python 中调用的函数”部分，了解可用函数的详细信息。

The commands execute just as if the OpenEmbedded build system were executing them. Consequently, working this way can be helpful when debugging a build or preparing software to be used with the OpenEmbedded build system.

> 命令就像 OpenEmbedded 构建系统正在执行它们一样执行。因此，以这种方式工作可以在调试构建或准备使用 OpenEmbedded 构建系统的软件时有所帮助。

Following is an example that uses `pydevshell` on a target named `matchbox-desktop`:

```
$ bitbake matchbox-desktop -c pydevshell
```

This command spawns a terminal and places you in an interactive Python interpreter within the OpenEmbedded build environment. The `OE_TERMINAL` variable controls what type of shell is opened.

> 这个命令会生成一个终端，并将您放置在 OpenEmbedded 构建环境中的交互式 Python 解释器中。`OE_TERMINAL` 变量控制打开的是什么类型的 shell。

When you are finished using `pydevshell`, you can exit the shell either by using Ctrl+d or closing the terminal window.
