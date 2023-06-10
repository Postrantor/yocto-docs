---
tip: translate by openai@2023-06-10 12:34:18
...
---
title: Finding Temporary Source Code
------------------------------------

You might find it helpful during development to modify the temporary source code used by recipes to build packages. For example, suppose you are developing a patch and you need to experiment a bit to figure out your solution. After you have initially built the package, you can iteratively tweak the source code, which is located in the `Build Directory`, and then you can force a re-compile and quickly test your altered code. Once you settle on a solution, you can then preserve your changes in the form of patches.

> 你可能会发现，在开发过程中修改用于构建软件包的临时源代码很有帮助。例如，假设你正在开发一个补丁，你需要进行一些实验来找到解决方案。在最初构建软件包后，你可以迭代地调整位于 `Build Directory` 中的源代码，然后可以强制重新编译并快速测试你改变的代码。一旦你找到解决方案，你就可以以补丁的形式保存你的更改。

During a build, the unpacked temporary source code used by recipes to build packages is available in the `Build Directory`:

> 在构建过程中，被配方用于构建软件包的解压缩的临时源代码可以在 `构建目录` 中找到，这是由 `S` 变量定义的。以下是 `meta/conf/bitbake.conf` 配置文件中 `源目录` 中 `S` 变量的默认值：

```
S = "$"
```

You should be aware that many recipes override the `S`/git`.

> 你应该意识到许多菜谱会覆盖 `S` 变量。例如，从 Git 获取源代码的菜谱通常会将 `S` 设置为 `$/git`。

::: note
::: title
Note
:::

The `BP` represents the base recipe name, which consists of the name and version:

```
BP = "$"
```

:::

The path to the work directory for the recipe (`WORKDIR`) is defined as follows:

```
$
```

The actual directory depends on several things:

- `TMPDIR`: The top-level build output directory.
- `MULTIMACH_TARGET_SYS`: The target system identifier.
- `PN`: The recipe name.
- `EXTENDPE` is blank.

> - EXTENDPE：如果没有指定 PE(通常大多数 recipes 都是这种情况)，那么 EXTENDPE 就为空。

- `PV`: The recipe version.
- `PR`: The recipe revision.

As an example, assume a Source Directory top-level folder named `poky`, a default `Build Directory` at `poky/build`, and a `qemux86-poky-linux` machine target system. Furthermore, suppose your recipe is named `foo_1.3.0.bb`. In this case, the work directory the build system uses to build the package would be as follows:

> 假设有一个名为 `poky` 的源目录顶级文件夹，默认的 `构建目录` 在 `poky/build`，以及一个 `qemux86-poky-linux` 机器目标系统。此外，假设你的配方名为 `foo_1.3.0.bb`。在这种情况下，构建系统用于构建软件包的工作目录如下：

```
poky/build/tmp/work/qemux86-poky-linux/foo/1.3.0-r0
```
