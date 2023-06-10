---
tip: translate by openai@2023-06-07 22:21:41
...
---
title: Yocto Project Releases and the Stable Release Process
------------------------------------------------------------

The Yocto Project release process is predictable and consists of both major and minor (point) releases. This brief chapter provides information on how releases are named, their life cycle, and their stability.

> 项目 Yocto 的发布流程可预测，包括大版本和小版本(点)发布。本简短章节提供有关发布的命名、生命周期和稳定性的信息。

# Major and Minor Release Cadence

The Yocto Project delivers major releases (e.g. &DISTRO;) using a six month cadence roughly timed each April and October of the year. Following are examples of some major YP releases with their codenames also shown. See the \"`ref-manual/release-process:major release codenames`\" section for information on codenames used with major releases.

> Yocto 项目每年四月和十月会发布主要版本(例如&DISTRO;)。以下是一些主要的 YP 版本及其代号，有关代号使用的更多信息，请参阅“ref-manual/release-process：主要发行版代号”部分。

> - 4.1 (\"Langdale\")
> - 4.0 (\"Kirkstone\")
> - 3.4 (\"Honister\")

While the cadence is never perfect, this timescale facilitates regular releases that have strong QA cycles while not overwhelming users with too many new releases. The cadence is predictable and avoids many major holidays in various geographies.

> 虽然节奏从未完美，但这种时间表有助于定期发布具有强大质量控制的产品，而不会令用户受到太多新发布的困扰。节奏可预测，并避免了许多地区的重大节假日。

The Yocto project delivers minor (point) releases on an unscheduled basis and are usually driven by the accumulation of enough significant fixes or enhancements to the associated major release. Following are some example past point releases:

> 页克托项目不定期发布小版本(点号版本)，通常是由相关主要版本中积累的足够重要的修复或增强所驱动的。以下是一些过去的点号版本的例子：

> - 4.1.3
> - 4.0.8
> - 3.4.4

The point release indicates a point in the major release branch where a full QA cycle and release process validates the content of the new branch.

::: note
::: title
Note
:::

Realize that there can be patches merged onto the stable release branches as and when they become available.
:::

# Major Release Codenames

Each major release receives a codename that identifies the release in the `overview-manual/development-environment:yocto project source repositories` with the same codename are likely to be compatible and thus work together.

> 每个主要发行版都会获得一个代号，该代号用于在“概览手册/开发环境：yocto 项目源存储库”中标识发行版。概念是，具有相同代号的元数据分支可能兼容并可以一起工作。

::: note
::: title
Note
:::

Codenames are associated with major releases because a Yocto Project release number (e.g. &DISTRO;) could conflict with a given layer or company versioning scheme. Codenames are unique, interesting, and easily identifiable.

> 代号与主要发行版相关联，因为 Yocto 项目发行版号(例如&DISTRO;)可能与给定层或公司的版本计划冲突。代号是独特的，有趣的，易于识别。
> :::

Releases are given a nominal release version as well but the codename is used in repositories for this reason. You can find information on Yocto Project releases and codenames at :yocto_wiki:[/Releases].

> 发布也会被赋予一个名义上的发布版本，但是代号会在存储库中使用，这是出于这个原因。您可以在 Yocto 项目发行版和代号的信息：yocto_wiki:[/Releases]中找到。

Our `/migration-guides/index` detail how to migrate from one release of the Yocto Project to the next.

# Stable Release Process

Once released, the release enters the stable release process at which time a person is assigned as the maintainer for that stable release. This maintainer monitors activity for the release by investigating and handling nominated patches and backport activity. Only fixes and enhancements that have first been applied on the \"master\" branch (i.e. the current, in-development branch) are considered for backporting to a stable release.

> 一旦发布，发布版本将进入稳定发布流程，此时会有一个人被指定为该稳定发布的维护者。该维护者通过调查和处理提名补丁和回退活动来监控该发布的活动。只有先在“主”分支(即当前正在开发的分支)上应用的修复和增强才会考虑回退到稳定发布版本。

::: note
::: title
Note
:::

The current Yocto Project policy regarding backporting is to consider bug fixes and security fixes only. Policy dictates that features are not backported to a stable release. This policy means generic recipe version upgrades are unlikely to be accepted for backporting. The exception to this policy occurs when there is a strong reason such as the fix happens to also be the preferred upstream approach.

> 目前 Yocto 项目关于回滚的政策是只考虑错误修复和安全修复。政策规定，不会将功能回滚到稳定版本。这一政策意味着通用配方版本升级不太可能被接受回滚。此政策的例外情况是当有强有力的理由，例如修复也是上游的首选方法时。
> :::

# Long Term Support Releases

While stable releases are supported for a duration of seven months, some specific ones are now supported for a longer period by the Yocto Project, and are called Long Term Support (`LTS`) releases.

> 虽然稳定版本的支持时间为七个月，Yocto 项目现在支持一些特定版本的更长支持时间，这些版本被称为长期支持(LTS)版本。

This started with version 3.1 (\"Dunfell\"), released in April 2020, that the project committed to supporting until the next `LTS` release, version 4.0 (\"Kirkstone\"), was released in May 2022 and offered with two years of support too.

> 这从 2020 年 4 月发布的 3.1(“Dunfell”)版本开始，项目承诺在下一个 LTS(长期支持)版本发布之前支持它。这个下一个 LTS(长期支持)版本，4.0(“Kirkstone”)，于 2022 年 5 月发布，并提供两年的支持。

However, as an experiment, support for \"Dunfell\" was extended to four years, until April 2024, therefore offering more stability to projects and leaving more time to upgrade to the latest `LTS` release. The project hasn\'t made any commitment to extending \"Kirkstone\" support too, as this will also depend on available funding for such an effort.

> 然而，作为一项实验，“Dunfell”的支持被延长至 2024 年 4 月，为项目提供了更多的稳定性，并且留出了更多的时间来升级到最新的“LTS”发行版。该项目尚未承诺也延长“Kirkstone”的支持，因为这也取决于为此类努力提供的可用资金。

When significant issues are found, `LTS` releases that are still supported. Older stable releases which have reached their End of Life (EOL) won\'t receive such updates.

> 当发现重大问题时，LTS 发行版允许不仅在当前稳定版本上发布修复程序，而且还可以发布到仍然支持的 LTS 发行版上。已经到达其终生周期(EOL)的旧稳定版本不会收到此类更新。

See :yocto_wiki:[/Stable_Release_and_LTS] releases.

![image](svg/releases.*)

::: note
::: title
Note
:::

In some circumstances, a layer can be created by the community in order to add a specific feature or support a new version of some package for an `LTS` release to \"mix\" a specific feature into that build. These are created on an as-needed basis and maintained by the people who need them.

> 在某些情况下，社区可以创建一个层来添加特定功能或支持某个软件包的新版本，以便在 LTS 发行版中使用。这被称为 Mixin 层。这些层薄而特定的目的层可以与 LTS 版本堆叠，将特定功能混合到该构建中。这些层是根据需要创建的，并由需要它们的人维护。

Policies on testing these layers depend on how widespread their usage is and determined on a case-by-case basis. You can find some `Mixin` layers may be released elsewhere by the wider community.

> 检测这些层的政策取决于它们的使用范围有多广泛，并且根据具体情况进行决定。您可以在:yocto_[git:`meta-lts-mixins`](git:%60meta-lts-mixins%60) \</meta-lts-mixins\>存储库中找到一些 Mixin 层。尽管 Yocto 项目为这些存储库提供托管，但不会对它们进行测试。其他 Mixin 层可能由更广泛的社区在其他地方发布。
> :::

# Testing and Quality Assurance

Part of the Yocto Project development and release process is quality assurance through the execution of test strategies. Test strategies provide the Yocto Project team a way to ensure a release is validated. Additionally, because the test strategies are visible to you as a developer, you can validate your projects. This section overviews the available test infrastructure used in the Yocto Project. For information on how to run available tests on your projects, see the \"`dev-manual/runtime-testing:performing automated runtime testing`\" section in the Yocto Project Development Tasks Manual.

> 部分 Yocto 项目开发和发布过程中的质量保证是通过执行测试策略来实现的。测试策略为 Yocto 项目团队提供了一种确保发布物有效的方式。另外，由于测试策略对您作为开发人员是可见的，您可以验证您的项目。本节概述了 Yocto 项目中使用的可用测试基础设施。有关如何在您的项目上运行可用测试的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual / runtime-testing：执行自动运行时测试”部分。

The QA/testing infrastructure is woven into the project to the point where core developers take some of it for granted. The infrastructure consists of the following pieces:

> 测试基础设施已经紧密地融入到项目中，以至于核心开发人员有时会忽略它。该基础设施包括以下部分：

- `bitbake-selftest`: A standalone command that runs unit tests on key pieces of BitBake and its fetchers.
- `ref-classes-sanity` set incorrectly.

> - `ref-classes-sanity`：此自动包含的类会检查构建环境是否缺少工具(例如 `gcc`)或者 `MACHINE` 设置不正确等常见配置错误。

- `ref-classes-insane`: This class checks the generated output from builds for sanity. For example, if building for an ARM target, did the build produce ARM binaries. If, for example, the build produced PPC binaries then there is a problem.

> - `ref-classes-insane`：此类检查编译生成的输出是否合理。例如，如果为 ARM 目标编译，编译是否生成 ARM 二进制文件。例如，如果编译生成 PPC 二进制文件，则存在问题。

- `ref-classes-testimage` to boot the images and check the combined runtime result boot operation and functions. However, the test can also use the IP address of a machine to test.

> 此类在构建镜像后执行运行时测试。通常使用 `QEMU </dev-manual/qemu>` 来启动镜像并检查组合的运行时结果启动操作和功能。但是，测试也可以使用机器的 IP 地址进行测试。

- `ptest <dev-manual/packages:testing packages with ptest>`: Runs tests against packages produced during the build for a given piece of software. The test allows the packages to be run within a target image.

> 运行测试，以检查在构建过程中生成的软件包。该测试允许在目标镜像中运行包。

- `oe-selftest`: Tests combination BitBake invocations. These tests operate outside the OpenEmbedded build system itself. The `oe-selftest` can run all tests by default or can run selected tests or test suites.

> `oe-selftest`：测试 BitBake 组合的调用。这些测试在 OpenEmbedded 构建系统本身之外运行。`oe-selftest` 可以默认情况下运行所有测试，也可以运行选定的测试或测试套件。

Originally, much of this testing was done manually. However, significant effort has been made to automate the tests so that more people can use them and the Yocto Project development team can run them faster and more efficiently.

> 原本，许多测试都是手动完成的。但是，为了让更多的人使用它们，以及 Yocto 项目开发团队能够更快、更有效地运行它们，我们已经花费了大量精力将测试自动化。

The Yocto Project\'s main Autobuilder (&YOCTO_AB_URL;) publicly tests each Yocto Project release\'s code in the :oe_[git:%60openembedded-core](git:%60openembedded-core) \</openembedded-core\>[, :yocto_git:\`poky \</poky\>] repository.

> 主要自动构建器(&YOCTO_AB_URL;)是 Yocto 项目的主要自动构建器，它公开测试 Yocto 项目发布的代码，包括：oe_[git:`openembedded-core`](git:%60openembedded-core%60)</openembedded-core>，[yocto_git:`poky`</poky>]存储库中的“master-next”分支中进行。

::: note
::: title
Note
:::

You can find all these branches in the `overview-manual/development-environment:yocto project source repositories`.
:::

Testing within these public branches ensures in a publicly visible way that all of the main supposed architectures and recipes in OE-Core successfully build and behave properly.

> 测试这些公共分支可以公开可见的方式确保 OE-Core 中所有主要的架构和配方都能成功构建并正常运行。

Various features such as `multilib`, sub architectures (e.g. `x32`, `poky-tiny`, `musl`, `no-x11` and and so forth), `bitbake-selftest`, and `oe-selftest` are tested as part of the QA process of a release. Complete testing and validation for a release takes the Autobuilder workers several hours.

> 在发布过程的 QA 过程中，会测试各种功能，如 `multilib`、子架构(如 `x32`、`poky-tiny`、`musl`、`no-x11` 等)、`bitbake-selftest` 和 `oe-selftest` 等。完整的测试和验证需要自动构建器工作者花费数小时。

::: note
::: title
Note
:::

The Autobuilder workers are non-homogeneous, which means regular testing across a variety of Linux distributions occurs. The Autobuilder is limited to only testing QEMU-based setups and not real hardware.

> 自动构建工人是非同质的，这意味着定期在各种 Linux 发行版上进行测试。自动构建仅限于测试基于 QEMU 的设置，而不是真实硬件。
> :::

Finally, in addition to the Autobuilder\'s tests, the Yocto Project QA team also performs testing on a variety of platforms, which includes actual hardware, to ensure expected results.

> 最后，除了 Autobuilder 的测试之外，Yocto Project QA 团队还在各种平台上进行测试，其中包括实际的硬件，以确保预期的结果。
