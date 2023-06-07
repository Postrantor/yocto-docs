---
tip: translate by baidu@2023-06-07 17:17:45
...
---
title: Finding Temporary Source Code
------------------------------------

You might find it helpful during development to modify the temporary source code used by recipes to build packages. For example, suppose you are developing a patch and you need to experiment a bit to figure out your solution. After you have initially built the package, you can iteratively tweak the source code, which is located in the `Build Directory`{.interpreted-text role="term"}, and then you can force a re-compile and quickly test your altered code. Once you settle on a solution, you can then preserve your changes in the form of patches.

> 在开发过程中，您可能会发现修改配方用于构建包的临时源代码很有帮助。例如，假设您正在开发一个补丁，并且需要进行一些实验来找出解决方案。在最初构建包之后，您可以迭代地调整位于“构建目录”｛.explored text role=“term”｝中的源代码，然后您可以强制重新编译并快速测试更改后的代码。一旦你确定了一个解决方案，你就可以以补丁的形式保留你的更改。

During a build, the unpacked temporary source code used by recipes to build packages is available in the `Build Directory`{.interpreted-text role="term"} as defined by the `S`{.interpreted-text role="term"} variable. Below is the default value for the `S`{.interpreted-text role="term"} variable as defined in the `meta/conf/bitbake.conf` configuration file in the `Source Directory`{.interpreted-text role="term"}:

> 在生成过程中，配方用于生成包的未打包临时源代码可在 `S`｛.depreted text role=“term”｝变量定义的 `build Directory`｛.repreted text role=“term“｝中找到。以下是在“源目录”中的“meta/conf/bitbake.conf”配置文件中定义的“S”｛.expreted text role=“term”｝变量的默认值：

```
S = "${WORKDIR}/${BP}"
```

You should be aware that many recipes override the `S`{.interpreted-text role="term"} variable. For example, recipes that fetch their source from Git usually set `S`{.interpreted-text role="term"} to `${WORKDIR}/git`.

> 您应该知道，许多配方会覆盖 `S`｛.explored text role=“term”｝变量。例如，从 Git 获取源代码的配方通常将 `S`｛.depredicted text role=“term”｝设置为 `${WORKDIR}/Git`。

::: note
::: title
Note
:::

The `BP`{.interpreted-text role="term"} represents the base recipe name, which consists of the name and version:

> `BP`｛.explored text role=“term”｝表示基本配方名称，包括名称和版本：

```
BP = "${BPN}-${PV}"
```

:::

The path to the work directory for the recipe (`WORKDIR`{.interpreted-text role="term"}) is defined as follows:

> 配方的工作目录路径（`WORKDIR`｛.explored text role=“term”｝）定义如下：

```
${TMPDIR}/work/${MULTIMACH_TARGET_SYS}/${PN}/${EXTENDPE}${PV}-${PR}
```

The actual directory depends on several things:

> 实际目录取决于以下几点：

- `TMPDIR`{.interpreted-text role="term"}: The top-level build output directory.
- `MULTIMACH_TARGET_SYS`{.interpreted-text role="term"}: The target system identifier.
- `PN`{.interpreted-text role="term"}: The recipe name.
- `EXTENDPE`{.interpreted-text role="term"}: The epoch \-\-- if `PE`{.interpreted-text role="term"} is not specified, which is usually the case for most recipes, then `EXTENDPE`{.interpreted-text role="term"} is blank.

> -`EXTENDPE`｛.explored text role=“term”｝：epoch\-如果没有指定 `PE`{.explered text rol=“term“｝，这通常是大多数配方的情况，那么 `EXTENDPE｝｛.expered text role=”term“}为空。

- `PV`{.interpreted-text role="term"}: The recipe version.
- `PR`{.interpreted-text role="term"}: The recipe revision.

As an example, assume a Source Directory top-level folder named `poky`, a default `Build Directory`{.interpreted-text role="term"} at `poky/build`, and a `qemux86-poky-linux` machine target system. Furthermore, suppose your recipe is named `foo_1.3.0.bb`. In this case, the work directory the build system uses to build the package would be as follows:

> 例如，假设一个名为“poky”的源目录顶级文件夹，一个位于“poky/Build”的默认“Build Directory”｛.expected text role=“term”｝，以及一个“qemux86 poky linux”机器目标系统。此外，假设您的配方名为“foo_1.3.0.bb”。在这种情况下，构建系统用于构建包的工作目录如下：

```
poky/build/tmp/work/qemux86-poky-linux/foo/1.3.0-r0
```
