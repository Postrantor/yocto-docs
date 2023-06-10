---
tip: translate by openai@2023-06-10 12:06:48
...
---
title: Using Quilt in Your Workflow
-----------------------------------

[Quilt](https://savannah.nongnu.org/projects/quilt) is a powerful tool that allows you to capture source code changes without having a clean source tree. This section outlines the typical workflow you can use to modify source code, test changes, and then preserve the changes in the form of a patch all using Quilt.

> Quilt([https://savannah.nongnu.org/projects/quilt](https://savannah.nongnu.org/projects/quilt))是一种强大的工具，可以在没有干净的源树的情况下捕获源代码更改。本节概述了使用 Quilt 修改源代码、测试更改并将更改以补丁的形式保存的典型工作流程。

::: note
::: title
Note
:::

With regard to preserving changes to source files, if you clean a recipe or have `ref-classes-rm-work` as described in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual is a safer development flow than the flow that uses Quilt.

> 就源文件的修改保存而言，如果你清理了配方或者启用了 `ref-classes-rm-work`，根据 Yocto 项目应用开发和可扩展软件开发套件(eSDK)手册中所述的 `devtool workflow`，比使用 Quilt 的流程更安全。
> :::

Follow these general steps:

1. *Find the Source Code:* Temporary source code used by the OpenEmbedded build system is kept in the `Build Directory`\" section to learn how to locate the directory that has the temporary source code for a particular package.

> 1.*查找源代码：*OpenEmbedded 构建系统使用的临时源代码保存在 `构建目录` 中。要了解如何定位具有特定软件包的临时源代码的目录，请参阅"`dev-manual/temporary-source-code:finding temporary source code`"部分。

2. *Change Your Working Directory:* You need to be in the directory that has the temporary source code. That directory is defined by the `S` variable.

> 2. *更改工作目录：*你需要进入具有临时源代码的目录。该目录由 `S` 变量定义。

3. *Create a New Patch:* Before modifying source code, you need to create a new patch. To create a new patch file, use `quilt new` as below:

   ```
   $ quilt new my_changes.patch
   ```
4. *Notify Quilt and Add Files:* After creating the patch, you need to notify Quilt about the files you plan to edit. You notify Quilt by adding the files to the patch you just created:

> 4. 通知 Quilt 并添加文件： 创建补丁后，您需要通知 Quilt 要编辑哪些文件。您可以通过将文件添加到刚刚创建的补丁中来通知 Quilt：

```
$ quilt add file1.c file2.c file3.c
```

5. *Edit the Files:* Make your changes in the source code to the files you added to the patch.
6. *Test Your Changes:* Once you have modified the source code, the easiest way to test your changes is by calling the `ref-tasks-compile` task as shown in the following example:

> 6. *测试您的更改：* 一旦您修改了源代码，最简单的方法是通过调用 `ref-tasks-compile` 任务来测试您的更改，如下面的示例所示：

```
$ bitbake -c compile -f package
```

The `-f` or `--force` option forces the specified task to execute. If you find problems with your code, you can just keep editing and re-testing iteratively until things work as expected.

> `-f` 或 `--force` 选项强制执行指定的任务。如果发现代码有问题，可以不断编辑和重新测试，直到结果满足预期。

::: note
::: title
Note
:::

All the modifications you make to the temporary source code disappear once you run the `ref-tasks-clean`\" section.

> 所有你对临时源代码做的修改，一旦使用 BitBake 运行 `ref-tasks-clean` 功能，也会消失。
> :::

7. *Generate the Patch:* Once your changes work as expected, you need to use Quilt to generate the final patch that contains all your modifications:

   ```
   $ quilt refresh
   ```

   At this point, the `my_changes.patch` file has all your edits made to the `file1.c`, `file2.c`, and `file3.c` files.

   You can find the resulting patch file in the `patches/` subdirectory of the source (`S`) directory.
8. *Copy the Patch File:* For simplicity, copy the patch file into a directory named `files`, which you can create in the same directory that holds the recipe (`.bb`) file or the append (`.bbappend`) file. Placing the patch here guarantees that the OpenEmbedded build system will find the patch. Next, add the patch into the `SRC_URI` of the recipe. Here is an example:

> 复制补丁文件：为了简化，将补丁文件复制到名为“files”的目录中，您可以在保存配方(`.bb`)文件或附加(`.bbappend`)文件的同一目录中创建该目录。将补丁放在这里可以确保 OpenEmbedded 构建系统能够找到补丁。然后，将补丁添加到配方的 `SRC_URI` 中。这里是一个例子：

```
SRC_URI += "file://my_changes.patch"
```
