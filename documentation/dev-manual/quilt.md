---
tip: translate by baidu@2023-06-07 17:15:47
...
---
title: Using Quilt in Your Workflow
-----------------------------------

[Quilt](https://savannah.nongnu.org/projects/quilt) is a powerful tool that allows you to capture source code changes without having a clean source tree. This section outlines the typical workflow you can use to modify source code, test changes, and then preserve the changes in the form of a patch all using Quilt.

> [被子](https://savannah.nongnu.org/projects/quilt)是一个强大的工具，允许您在没有干净的源代码树的情况下捕获源代码更改。本节概述了可用于修改源代码、测试更改，然后以补丁形式保留更改的典型工作流程，所有这些都使用 Quilt。

::: note
::: title
Note
:::

With regard to preserving changes to source files, if you clean a recipe or have `ref-classes-rm-work`{.interpreted-text role="ref"} enabled, the ``devtool workflow <sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow>``{.interpreted-text role="ref"} as described in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual is a safer development flow than the flow that uses Quilt.

> 关于保留对源文件的更改，如果您清理了一个配方或启用了 `ref classes rm work`{.depredicted text role=“ref”}，Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中描述的 ``devtool 工作流 <sdk manual/extensible：在您的 sdk 工作流中使用\`\`devtool\`\`>`{.expreted text role=“ref”}是比使用 Quilt 的流程更安全的开发流程。
> :::

Follow these general steps:

> 遵循以下一般步骤：

1. *Find the Source Code:* Temporary source code used by the OpenEmbedded build system is kept in the `Build Directory`{.interpreted-text role="term"}. See the \"`dev-manual/temporary-source-code:finding temporary source code`{.interpreted-text role="ref"}\" section to learn how to locate the directory that has the temporary source code for a particular package.

> 1.*查找源代码：*OpenEmbedded 构建系统使用的临时源代码保存在“构建目录”中｛.explored text role=“term”｝。请参阅\“`dev manual/temporary source code:finding temporary source-code`｛.explored text role=“ref”｝\”一节，了解如何查找包含特定包的临时源代码的目录。

2. *Change Your Working Directory:* You need to be in the directory that has the temporary source code. That directory is defined by the `S`{.interpreted-text role="term"} variable.

> 2.*更改您的工作目录：*您需要在具有临时源代码的目录中。该目录由 `S`｛.explored text role=“term”｝变量定义。

3. *Create a New Patch:* Before modifying source code, you need to create a new patch. To create a new patch file, use `quilt new` as below:

> 3.*创建新补丁：*在修改源代码之前，您需要创建一个新补丁。要创建新的修补程序文件，请按如下所示使用“缝合新”：

```

> ```

$ quilt new my_changes.patch

> $coated新my_changes.patch

```

> ```
> ```

4. *Notify Quilt and Add Files:* After creating the patch, you need to notify Quilt about the files you plan to edit. You notify Quilt by adding the files to the patch you just created:

> 4.*通知 Quilt 并添加文件：*创建补丁后，您需要通知 Quilt 您计划编辑的文件。您可以通过将文件添加到刚刚创建的补丁中来通知 Quilt：

```

> ```

$ quilt add file1.c file2.c file3.c

> $combe添加文件1.c文件2.c文件3.c

```

> ```
> ```

5. *Edit the Files:* Make your changes in the source code to the files you added to the patch.

> 5.*编辑文件：*对添加到补丁中的文件的源代码进行更改。

6. *Test Your Changes:* Once you have modified the source code, the easiest way to test your changes is by calling the `ref-tasks-compile`{.interpreted-text role="ref"} task as shown in the following example:

> 6.*测试您的更改：*一旦您修改了源代码，测试更改的最简单方法就是调用 `ref tasks compile`｛.explored text role=“ref”｝任务，如下例所示：

```

> ```

$ bitbake -c compile -f package

> $bitbake-c编译-f包

```

> ```
> ```

The `-f` or `--force` option forces the specified task to execute. If you find problems with your code, you can just keep editing and re-testing iteratively until things work as expected.

> “-f”或“--force”选项强制执行指定的任务。如果您发现代码有问题，您可以不断地编辑和重新测试，直到事情按预期进行。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

All the modifications you make to the temporary source code disappear once you run the `ref-tasks-clean`{.interpreted-text role="ref"} or `ref-tasks-cleanall`{.interpreted-text role="ref"} tasks using BitBake (i.e. `bitbake -c clean package` and `bitbake -c cleanall package`). Modifications will also disappear if you use the `ref-classes-rm-work`{.interpreted-text role="ref"} feature as described in the \"`dev-manual/disk-space:conserving disk space during builds`{.interpreted-text role="ref"}\" section.

> 一旦使用 BitBake（即“BitBake-c clean package”和“bitbak-c clean All package”）运行“ref tasks clean”｛.respered text role=“ref”｝或“ref tasks-cleanall”｛.espered text role=“ref”}任务，对临时源代码所做的所有修改都将消失。如果您使用“dev manual/disk space:在构建期间保留磁盘空间”一节中所述的“ref classes rm work”｛.depreted text role=“ref”｝功能，则修改也将消失。

:::

> ：：：

7. *Generate the Patch:* Once your changes work as expected, you need to use Quilt to generate the final patch that contains all your modifications:

> 7.*生成补丁：*一旦您的更改按预期进行，您需要使用 Quilt 生成包含所有修改的最终补丁：

```

> ```

$ quilt refresh

> $被子刷新

```

> ```
> ```

At this point, the `my_changes.patch` file has all your edits made to the `file1.c`, `file2.c`, and `file3.c` files.

> 此时，“my_changes.patch”文件已包含对“file1.c”、“file2.c”和“file3.c”文件所做的所有编辑。

You can find the resulting patch file in the `patches/` subdirectory of the source (`S`{.interpreted-text role="term"}) directory.

> 您可以在源目录（`S`｛.explored text role=“term”｝）的 `patches/` 子目录中找到生成的修补程序文件。

8. *Copy the Patch File:* For simplicity, copy the patch file into a directory named `files`, which you can create in the same directory that holds the recipe (`.bb`) file or the append (`.bbappend`) file. Placing the patch here guarantees that the OpenEmbedded build system will find the patch. Next, add the patch into the `SRC_URI`{.interpreted-text role="term"} of the recipe. Here is an example:

> 8.*复制修补程序文件：*为了简单起见，将修补程序文件复制到名为“files”的目录中，您可以在保存配方（`.bb`）文件或附加（`.bappend`）文件的同一目录中创建该目录。在此处放置修补程序可以保证 OpenEmbedded 构建系统能够找到修补程序。接下来，将补丁添加到配方的“SRC_URI”｛.explored text role=“term”｝中。以下是一个示例：

```

> ```

SRC_URI += "file://my_changes.patch"

> SRC_URI+=“file://my_changes.patch”

```

> ```
> ```
