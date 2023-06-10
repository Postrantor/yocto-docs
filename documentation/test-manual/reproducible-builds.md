---
tip: translate by openai@2023-06-07 20:56:08
...
---
title: Reproducible Builds
--------------------------

# How we define it

The Yocto Project defines reproducibility as where a given input build configuration will give the same binary output regardless of when it is built (now or in 5 years time), regardless of the path on the filesystem the build is run in, and regardless of the distro and tools on the underlying host system the build is running on.

> 项目 Yocto 将可重复性定义为：无论何时（现在或 5 年后），无论文件系统中构建运行的路径如何，以及无论底层主机系统上的分发和工具如何，给定的输入构建配置都将产生相同的二进制输出。

# Why it matters

The project aligns with the [Reproducible Builds project](https://reproducible-builds.org/), which shares information about why reproducibility matters. The primary focus of the project is the ability to detect security issues being introduced. However, from a Yocto Project perspective, it is also hugely important that our builds are deterministic. When you build a given input set of metadata, we expect you to get consistent output. This has always been a key focus but, `since release 3.1 ("dunfell") <migration-guides/migration-3.1:reproducible builds now enabled by default>`{.interpreted-text role="ref"}, it is now true down to the binary level including timestamps.

> 该项目与[可重复构建项目](https://reproducible-builds.org/)保持一致，该项目分享了关于可重复性的重要性的信息。该项目的主要焦点是检测引入安全问题的能力。但是，从 Yocto 项目的角度来看，我们的构建也是至关重要的。当您构建给定的元数据输入集时，我们期望您获得一致的输出。这一直是一个关键焦点，但是自从发布 3.1（“dunfell”）<migration-guides/migration-3.1：reproducible builds now enabled by default>{.interpreted-text role="ref"}以来，它现在已经真正到达二进制层次，包括时间戳。

For example, at some point in the future life of a product, you find that you need to rebuild to add a security fix. If this happens, only the components that have been modified should change at the binary level. This would lead to much easier and clearer bounds on where validation is needed.

> 例如，在产品未来的某个时刻，您发现需要重建以添加安全修复程序。如果发生这种情况，只有已修改的组件应该在二进制层面上发生变化。这将导致验证所需的范围变得更容易和更清晰。

This also gives an additional benefit to the project builds themselves, our `overview-manual/concepts:Hash Equivalence`{.interpreted-text role="ref"} for `overview-manual/concepts:Shared State`{.interpreted-text role="ref"} object reuse works much more effectively when the binary output remains the same.

> 这也为项目构建本身带来了额外的好处，我们的“概览手册/概念：哈希等效性”对于“概览手册/概念：共享状态”对象重用时，二进制输出保持不变时更有效。

::: note
::: title
Note
:::

We strongly advise you to make sure your project builds reproducibly before finalizing your production images. It would be too late if you only address this issue when the first updates are required.

> 我们强烈建议您在最终完成生产图像之前确保项目可重现构建。如果您只在需要第一次更新时才解决此问题，那将为时已晚。
> :::

# How we implement it

There are many different aspects to build reproducibility, but some particular things we do within the build system to ensure reproducibility include:

> 有许多不同的方面可以建立可重现性，但我们在构建系统中为确保可重现性所做的一些特殊事情包括：

- Adding mappings to the compiler options to ensure debug filepaths are mapped to consistent target compatible paths. This is done through the `DEBUG_PREFIX_MAP`{.interpreted-text role="term"} variable which sets the `-fmacro-prefix-map` and `-fdebug-prefix-map` compiler options correctly to map to target paths.

> 将映射添加到编译器选项，以确保调试文件路径映射到一致的目标兼容路径。这是通过 `DEBUG_PREFIX_MAP` 变量来完成的，该变量正确设置了 `-fmacro-prefix-map` 和 `-fdebug-prefix-map` 编译器选项，以映射到目标路径。

- Being explicit about recipe dependencies and their configuration (no floating configure options or host dependencies creeping in). In particular this means making sure `PACKAGECONFIG`{.interpreted-text role="term"} coverage covers configure options which may otherwise try and auto-detect host dependencies.

> 明确记录食谱依赖关系及其配置（不要有浮动的配置选项或主机依赖性的潜入）。特别是要确保 `PACKAGECONFIG` 覆盖可能尝试自动检测主机依赖性的配置选项。

- Using recipe specific sysroots to isolate recipes so they only see their dependencies. These are visible as `recipe-sysroot` and `recipe-sysroot-native` directories within the `WORKDIR`{.interpreted-text role="term"} of a given recipe and are populated only with the dependencies a recipe has.

> 使用特定的菜谱系统根来隔离菜谱，以便它们只能看到它们的依赖项。这些在给定菜谱的 `WORKDIR` 中可见为 `recipe-sysroot` 和 `recipe-sysroot-native` 目录，只包含菜谱所具有的依赖项。

- Build images from a reduced package set: only packages from recipes the image depends upon.
- Filtering the tools available from the host\'s `PATH` to only a specific set of tools, set using the `HOSTTOOLS`{.interpreted-text role="term"} variable.

> 过滤主机的 `PATH` 中可用的工具，仅使用 `HOSTTOOLS` 变量设置的特定工具集。

::: note
::: title
Note
:::

Because of an open bug in GCC, using `DISTRO_FEATURES:append = " lto"` or adding `-flto` (Link Time Optimization) to `CFLAGS`{.interpreted-text role="term"} makes the resulting binary non-reproducible, in that it depends on the full absolute build path to `recipe-sysroot-native`, so installing the Yocto Project in a different directory results in a different binary.

> 由于 GCC 中的一个开放式错误，使用“DISTRO_FEATURES：append =” lto“或将“-flto”（链接时优化）添加到“CFLAGS”，会导致生成的二进制文件无法重现，因为它依赖于到“recipe-sysroot-native”的完整绝对构建路径，因此在不同的目录中安装 Yocto 项目会导致不同的二进制文件。

This issue is addressed by :yocto_bugs:[bug 14481 - Programs built with -flto are not reproducible\</show_bug.cgi?id=14481\>]{.title-ref}.

> 这个问题通过 :yocto_bugs:[bug 14481 - 用-flto 构建的程序不可重现\</show_bug.cgi?id=14481\>]{.title-ref}来解决。
> :::

# Can we prove the project is reproducible?

Yes, we can prove it and we regularly test this on the Autobuilder. At the time of writing (release 3.3, \"hardknott\"), `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"} is 100% reproducible for all its recipes (i.e. world builds) apart from the Go language and Ruby documentation packages. Unfortunately, the current implementation of the Go language has fundamental reproducibility problems as it always depends upon the paths it is built in.

> 是的，我们可以证明这一点，我们定期在自动构建器上测试这一点。在撰写本文时（发行版 3.3，“hardknott”），OpenEmbedded-Core（OE-Core）除了 Go 语言和 Ruby 文档包外，所有的配方（即世界构建）都是 100％可重现的。不幸的是，Go 语言的当前实现始终取决于其构建的路径，因此具有根本的可重现性问题。

::: note
::: title
Note
:::

Only BitBake and `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"}, which is the `meta` layer in Poky, guarantee complete reproducibility. The moment you add another layer, this warranty is voided, because of additional configuration files, `bbappend` files, overridden classes, etc.

> 只有 BitBake 和 Poky 中的元层 OpenEmbedded-Core（OE-Core）才能保证完全可重现性。一旦您添加另一层，此保修就会失效，因为会有额外的配置文件、bbappend 文件、覆盖的类等。
> :::

To run our automated selftest, as we use in our CI on the Autobuilder, you can run:

> 要运行我们在自动构建器上使用的 CI 中的自动化自检测，你可以运行：

```
oe-selftest -r reproducible.ReproducibleTests.test_reproducible_builds
```

This defaults to including a `world` build so, if other layers are added, it would also run the tests for recipes in the additional layers. The first build will be run using `Shared State <overview-manual/concepts:Shared State>`{.interpreted-text role="ref"} if available, the second build explicitly disables `Shared State <overview-manual/concepts:Shared State>`{.interpreted-text role="ref"} and builds on the specific host the build is running on. This means we can test reproducibility builds between different host distributions over time on the Autobuilder.

> 这默认包括一个 `world` 构建，因此，如果添加其他层，它也将运行额外层中的菜谱测试。第一次构建将使用 `共享状态<overview-manual/concepts:Shared State>`{.interpreted-text role="ref"}（如果可用）运行，第二次构建显式禁用 `共享状态<overview-manual/concepts:Shared State>`{.interpreted-text role="ref"}并在构建正在运行的特定主机上构建。这意味着我们可以在 Autobuilder 上随着时间的推移测试不同主机分布之间的可重现性构建。

If `OEQA_DEBUGGING_SAVED_OUTPUT` is set, any differing packages will be saved here. The test is also able to run the `diffoscope` command on the output to generate HTML files showing the differences between the packages, to aid debugging. On the Autobuilder, these appear under [https://autobuilder.yocto.io/pub/repro-fail/](https://autobuilder.yocto.io/pub/repro-fail/) in the form `oe-reproducible + <date> + <random ID>`, e.g. `oe-reproducible-20200202-1lm8o1th`.

> 如果设置了 `OEQA_DEBUGGING_SAVED_OUTPUT`，任何不同的软件包都将保存在这里。测试还能够在输出上运行 `diffoscope` 命令以生成显示软件包之间差异的 HTML 文件，以帮助调试。在自动构建器上，这些出现在 [https://autobuilder.yocto.io/pub/repro-fail/](https://autobuilder.yocto.io/pub/repro-fail/)，以 `oe-reproducible + <date> + <random ID>` 的形式，例如 `oe-reproducible-20200202-1lm8o1th`。

The project\'s current reproducibility status can be seen at :yocto_home:[/reproducible-build-results/]{.title-ref}

> 项目当前的可复现性状态可在 yocto_home 上查看：[/reproducible-build-results/]{.title-ref}

You can also check the reproducibility status on supported host distributions:

> 你也可以检查支持的主机发行版上的可重现性状态：

- CentOS: :yocto_ab:[/typhoon/#/builders/reproducible-centos]{.title-ref}
- Debian: :yocto_ab:[/typhoon/#/builders/reproducible-debian]{.title-ref}
- Fedora: :yocto_ab:[/typhoon/#/builders/reproducible-fedora]{.title-ref}
- Ubuntu: :yocto_ab:[/typhoon/#/builders/reproducible-ubuntu]{.title-ref}

# Can I test my layer or recipes?

Once again, you can run a `world` test using the `oe-selftest <ref-manual/release-process:Testing and Quality Assurance>`{.interpreted-text role="ref"} command provided above. This functionality is implemented in :oe\_[git:%60meta/lib/oeqa/selftest/cases/reproducible.py](git:%60meta/lib/oeqa/selftest/cases/reproducible.py) \</openembedded-core/tree/meta/lib/oeqa/selftest/cases/reproducible.py\>\`.

> 再次，您可以使用上面提供的 `oe-selftest <ref-manual/release-process:Testing and Quality Assurance>`{.interpreted-text role="ref"} 命令来运行 `world` 测试。 该功能实现在：oe\_[git:%60meta/lib/oeqa/selftest/cases/reproducible.py](git:%60meta/lib/oeqa/selftest/cases/reproducible.py) \</openembedded-core/tree/meta/lib/oeqa/selftest/cases/reproducible.py\>\`中。

You could subclass the test and change `targets` to a different target.

> 你可以对测试进行子类化，并将 `targets` 更改为不同的目标。

You may also change `sstate_targets` which would allow you to \"pre-cache\" some set of recipes before the test, meaning they are excluded from reproducibility testing. As a practical example, you could set `sstate_targets` to `core-image-sato`, then setting `targets` to `core-image-sato-sdk` would run reproducibility tests only on the targets belonging only to `core-image-sato-sdk`.

> 您也可以更改 `sstate_targets`，这将允许您在测试之前"预先缓存"一些配方集，这意味着它们被排除在可重复性测试之外。作为一个实际的例子，您可以将 `sstate_targets` 设置为 `core-image-sato`，然后将 `targets` 设置为 `core-image-sato-sdk` 将只对属于 `core-image-sato-sdk` 的目标运行可重复性测试。
