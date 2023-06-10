---
tip: translate by openai@2023-06-07 23:03:34
...
---
title: Classes
--------------

Class files are used to abstract common functionality and share it amongst multiple recipe (`.bb`) files. To use a class file, you simply make sure the recipe inherits the class. In most cases, when a recipe inherits a class it is enough to enable its features. There are cases, however, where in the recipe you might need to set variables or override some default behavior.

> 类文件用于抽象公共功能并在多个配方(`.bb`)文件之间共享。要使用类文件，只需确保配方继承该类即可。在大多数情况下，当一个配方继承一个类时，就足以启用其功能。但也有一些情况，在配方中你可能需要设置变量或覆盖一些默认行为。

Any `Metadata`:

> 任何常见于配方中的元数据也可以放置在类文件中。类文件以 `.bbclass` 为扩展名，通常放置在源目录中 `meta*/` 目录下的一组子目录中：

> - `classes-recipe/` - classes intended to be inherited by recipes individually
> - `classes-global/` - classes intended to be inherited globally
> - `classes/` - classes whose usage context is not clearly defined

Class files can also be pointed to by `BUILDDIR` using the same method by which `.conf` files are searched.

> 类文件也可以通过 `BUILDDIR`(例如 `build/`)来指向，就像 `conf` 目录中的 `.conf` 文件一样。类文件将使用与 `.conf` 文件相同的方法在 `BBPATH` 中搜索。

This chapter discusses only the most useful and important classes. Other classes do exist within the `meta/classes*` directories in the Source Directory. You can reference the `.bbclass` files directly for more information.

> 本章只讨论最有用和最重要的类。在源目录中的 `meta/classes*` 目录中还存在其他类。您可以直接参考 `.bbclass` 文件以获取更多信息。

# `allarch`

The `ref-classes-allarch` class is inherited by recipes that do not produce architecture-specific output. The class disables functionality that is normally needed for recipes that produce executable binaries (such as building the cross-compiler and a C library as pre-requisites, and splitting out of debug symbols during packaging).

> 类 `ref-classes-allarch` 由不生成特定架构输出的配方继承。该类禁用了通常为生成可执行二进制文件所需的功能(例如构建交叉编译器和 C 库作为先决条件，以及在打包期间分离调试符号)。

::: note
::: title
Note
:::

Unlike some distro recipes (e.g. Debian), OpenEmbedded recipes that produce packages that depend on tunings through use of the `RDEPENDS`. This is the case even if the recipes do not produce architecture-specific output.

> 与一些发行版的菜谱(例如 Debian)不同，使用 `RDEPENDS` 为所有架构进行配置。即使菜谱不产生特定架构的输出，也是如此。

Configuring such recipes for all architectures causes the `do_package_write_* <ref-tasks-package_write_deb>` is built even when the recipe never changes.

> 配置这些配方适用于所有架构会导致 `do_package_write_* <ref-tasks-package_write_deb>` 构建镜像时也会发生不必要的重新构建。
> :::

By default, all recipes inherit the `ref-classes-base` class.

> 默认情况下，所有的配方都继承 `ref-classes-base` 类。

# `archiver`

The `ref-classes-archiver` class supports releasing source code and other materials with the binaries.

For more details on the source `ref-classes-archiver` variable for information about the variable flags (varflags) that help control archive creation.

> 对于源 `ref-classes-archiver` 变量，了解有助于控制归档创建的变量标志(varflags)的信息。

# `autotools*`

The `autotools* <ref-classes-autotools>`.

> 这些自动工具类(参见自动工具类)支持使用 GNU Autotools(参见维基百科：GNU Autotools)构建的软件包。

The `autoconf`, `automake`, and `libtool` packages bring standardization. This class defines a set of tasks (e.g. `configure`, `compile` and so forth) that work for all Autotooled packages. It should usually be enough to define a few standard variables and then simply `inherit autotools`. These classes can also work with software that emulates Autotools. For more information, see the \"`dev-manual/new-recipe:building an autotooled package`\" section in the Yocto Project Development Tasks Manual.

> `autoconf`、`automake` 和 `libtool` 软件包带来了标准化。这个类定义了一组任务(例如 `configure`、`compile` 等)，适用于所有的 Autotooled 软件包。通常只需要定义几个标准变量，然后简单地 `inherit autotools` 即可。这些类也可以用于模拟 Autotools 的软件。有关更多信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/new-recipe：构建一个 Autotooled 软件包”部分。

By default, the `autotools* <ref-classes-autotools>` classes use out-of-tree builds (i.e. `autotools.bbclass` building with `B != S`).

> 默认情况下，`autotools* <ref-classes-autotools>` 类使用非树形构建(即使用 `B != S` 构建 `autotools.bbclass`)。

If the software being built by a recipe does not support using out-of-tree builds, you should have the recipe inherit the `autotools-brokensep <ref-classes-autotools>`. This method is useful when out-of-tree build support is either not present or is broken.

> 如果软件是按照配方构建的，并且不支持外部树构建，你应该让配方继承 `autotools-brokensep <ref-classes-autotools>` 进行构建。当外部树构建不存在或者已经损坏时，这种方法很有用。

::: note
::: title
Note
:::

It is recommended that out-of-tree support be fixed and used if at all possible.
:::

It\'s useful to have some idea of how the tasks defined by the `autotools* <ref-classes-autotools>` classes work and what they do behind the scenes.

> 有一些了解 `autotools* <ref-classes-autotools>` 类定义的任务是如何工作以及它们背后做了什么有助于更好地理解它们。

- `ref-tasks-configure` variables.

> - `ref-tasks-configure` 变量将额外的参数传递给 `configure`。

- `ref-tasks-compile` variable.

> 运行 `make`，使用指定编译器和链接器的参数。您可以通过 `EXTRA_OEMAKE` 变量传递额外的参数。

- `ref-tasks-install`` as ` DESTDIR`.

# `base`

The `ref-classes-base` class.

> 类 `ref-classes-base`。

The class also contains some commonly used functions such as `oe_runmake`, which runs `make` with the arguments specified in `EXTRA_OEMAKE` variable as well as the arguments passed directly to `oe_runmake`.

> 类中还包含一些常用函数，如 `oe_runmake`，它可以使用 `EXTRA_OEMAKE` 变量中指定的参数以及直接传递给 `oe_runmake` 的参数来运行 `make`。

# `bash-completion`

Sets up packaging and dependencies appropriate for recipes that build software that includes bash-completion data.

# `bin_package`

The `ref-classes-bin-package` class is a helper class for recipes that extract the contents of a binary package (e.g. an RPM) and install those contents rather than building the binary from source. The binary package is extracted and new packages in the configured output package format are created. Extraction and installation of proprietary binaries is a good example use for this class.

> 这个 `ref-classes-bin-package` 类是一个帮助类，用于提取二进制包(例如 RPM)的内容并安装这些内容，而不是从源代码构建二进制文件。二进制包将被提取，并在配置的输出包格式中创建新的包。提取和安装专有二进制文件是该类的一个很好的例子。

::: note
::: title
Note
:::

For RPMs and other packages that do not contain a subdirectory, you should specify an appropriate fetcher parameter to point to the subdirectory. For example, if BitBake is using the Git fetcher (`git://`), the \"subpath\" parameter limits the checkout to a specific subpath of the tree. Here is an example where `$:

> 对于不包含子目录的 RPM 和其他软件包，您应该指定适当的获取器参数以指向子目录。例如，如果 BitBake 使用 Git 获取器(`git://`)，则“subpath”参数将检出限制为树的特定子路径。这里有一个使用 `$` 的示例，因此文件将提取到默认值 `S` 所预期的子目录中：

```
SRC_URI = "git://example.com/downloads/somepackage.rpm;branch=main;subpath=$"
```

See the \"`bitbake-user-manual/bitbake-user-manual-fetching:fetchers`\" section in the BitBake User Manual for more information on supported BitBake Fetchers.

> 查看 BitBake 用户手册中的“bitbake-user-manual / bitbake-user-manual-fetching：fetchers”部分，了解更多关于支持的 BitBake Fetchers 的信息。
> :::

# `binconfig`

The `ref-classes-binconfig` class helps to correct paths in shell scripts.

Before `pkg-config` had become widespread, libraries shipped shell scripts to give information about the libraries and include paths needed to build software (usually named `LIBNAME-config`). This class assists any recipe using such scripts.

> 在 `pkg-config` 变得普遍之前，库都会附带一些 shell 脚本，来提供关于库和需要构建软件所需的 include 路径的信息(通常命名为 `LIBNAME-config`)。这个类可以帮助任何使用这些脚本的配方。

During staging, the OpenEmbedded build system installs such scripts into the `sysroots/` directory. Inheriting this class results in all paths in these scripts being changed to point into the `sysroots/` directory so that all builds that use the script use the correct directories for the cross compiling layout. See the `BINCONFIG_GLOB` variable for more information.

> 在构建阶段，OpenEmbedded 构建系统将这些脚本安装到 `sysroots/` 目录中。继承此类将导致这些脚本中的所有路径都被更改为指向 `sysroots/` 目录，以便所有使用脚本的构建都使用用于跨编译布局的正确目录。有关更多信息，请参阅 `BINCONFIG_GLOB` 变量。

# `binconfig-disabled`

An alternative version of the `ref-classes-binconfig` variable within the recipe inheriting the class.

> 这个类(ref-classes-binconfig)的替代版本，可以通过使它们返回错误来禁用二进制配置脚本，以便使用 pkg-config 查询信息。要禁用的脚本应该在继承该类的配方中使用 BINCONFIG 变量指定。

# `buildhistory`

The `ref-classes-buildhistory`\" section in the Yocto Project Development Tasks Manual.

> 这个 ref-classes-buildhistory 类记录了构建输出元数据的历史，可以用来检测可能的回归，也可以用于分析构建输出。有关使用构建历史的更多信息，请参见 Yocto 项目开发任务手册中的“dev-manual/build-quality：维护构建输出质量”部分。

# `buildstats`

The `ref-classes-buildstats` class records performance statistics about each task executed during the build (e.g. elapsed time, CPU usage, and I/O usage).

> 类 `ref-classes-buildstats` 记录构建期间执行的每个任务的性能统计信息(例如，耗时、CPU 使用率和 I/O 使用率)。

When you use this class, the output goes into the `BUILDSTATS_BASE`/buildstats/`. You can analyze the elapsed time using ` scripts/pybootchartgui/pybootchartgui.py`, which produces a cascading chart of the entire build process and can be useful for highlighting bottlenecks.

> 当您使用此类时，输出将转移到默认为 `$/buildstats/` 的 `BUILDSTATS_BASE` 目录。您可以使用 `scripts/pybootchartgui/pybootchartgui.py` 分析所用时间，它会生成整个构建过程的级联图表，可以有助于突出瓶颈。

Collecting build statistics is enabled by default through the `USER_CLASSES` list.

> 默认情况下，通过您的 `local.conf` 文件中的 `USER_CLASSES` 变量启用收集构建统计信息。因此，您无需做任何操作即可启用该类。但是，如果要禁用该类，只需从 `USER_CLASSES` 列表中删除“ref-classes-buildstats”即可。

# `buildstats-summary`

When inherited globally, prints statistics at the end of the build on sstate re-use. In order to function, this class requires the `ref-classes-buildstats` class be enabled.

> 当全局继承时，在 sstate 重用结束时会打印统计信息。为了使这个类生效，需要启用 `ref-classes-buildstats` 类。

# `cargo`

The `ref-classes-cargo` class allows to compile Rust language programs using [Cargo](https://doc.rust-lang.org/cargo/). Cargo is Rust\'s package manager, allowing to fetch package dependencies and build your program.

> 类 `ref-classes-cargo` 允许使用 [Cargo](https://doc.rust-lang.org/cargo/) 编译 Rust 语言程序。Cargo 是 Rust 的包管理器，可以获取包依赖项并构建程序。

Using this class makes it very easy to build Rust programs. All you need is to use the `SRC_URI` variable to point to a source repository which can be built by Cargo, typically one that was created by the `cargo new` command, containing a `Cargo.toml` file and a `src` subdirectory.

> 使用这个类可以很容易地构建 Rust 程序。你所需要做的就是使用 `SRC_URI` 变量指向一个可以用 Cargo 构建的源代码库，通常是由 `cargo new` 命令创建的，包含一个 `Cargo.toml` 文件和一个 `src` 子目录。

You will find a simple example in the :oe_[git:%60rust-hello-world_git.bb](git:%60rust-hello-world_git.bb) \</openembedded-core/tree/meta/recipes-extended/rust-example/rust-hello-world_git.bb\>[ recipe. A more complex example, with package dependencies, is the :oe_git:\`uutils-coreutils \</meta-openembedded/tree/meta-oe/recipes-core/uutils-coreutils\>] recipe, which was generated by the [cargo-bitbake](https://crates.io/crates/cargo-bitbake) tool.

> 你可以在 oe_[git:%60rust-hello-world_git.bb](git:%60rust-hello-world_git.bb) \</openembedded-core/tree/meta/recipes-extended/rust-example/rust-hello-world_git.bb\> recipes 中找到一个简单的示例。更复杂的示例，带有包依赖关系，是 oe_git:\`uutils-coreutils \</meta-openembedded/tree/meta-oe/recipes-core/uutils-coreutils\>] recipes，它是由 [cargo-bitbake](https://crates.io/crates/cargo-bitbake) 工具生成的。

This class inherits the `ref-classes-cargo_common` class.

# `cargo_common`

The `ref-classes-cargo_common` class is an internal class that is not intended to be used directly.

An exception is the \"rust\" recipe, to build the Rust compiler and runtime library, which is built by Cargo but cannot use the `ref-classes-cargo` class. This is why this class was introduced.

> 例外是“rust”recipes，用来构建 Rust 编译器和运行时库，它是由 Cargo 构建的，但不能使用 `ref-classes-cargo` 类。这就是为什么引入这个类的原因。

# `cargo-update-recipe-crates`[]

The `ref-classes-cargo-update-recipe-crates` by reading the `Cargo.lock` file in the source tree.

> 这个 ref-classes-cargo-update-recipe-crates 类允许 recipes 开发者通过读取源树中的 Cargo.lock 文件来更新 SRC_URI 中的 Cargo 框架列表。

To do so, create a recipe for your program, for example using `devtool </ref-manual/devtool-reference>` and run:

> 为此，请为您的程序创建一个配方，例如使用 `devtool`</ref-manual/devtool-reference>`，然后运行：

```
bitbake -c update_crates recipe
```

This creates a `recipe-crates.inc` file that you can include in your recipe:

```
require $-crates.inc
```

That\'s also something you can achieve by using the [cargo-bitbake](https://crates.io/crates/cargo-bitbake) tool.

# `ccache`

The `ref-classes-ccache` class enables the C/C++ Compiler Cache for the build. This class is used to give a minor performance boost during the build.

> 这个 `ref-classes-ccache` 类可以为构建启用 C/C++ 编译器缓存。这个类用于在构建期间提供一个轻微的性能提升。

See [https://ccache.samba.org/](https://ccache.samba.org/) for information on the C/C++ Compiler Cache, and the :oe_[git:%60ccache.bbclass](git:%60ccache.bbclass) \</openembedded-core/tree/meta/classes/ccache.bbclass\>[ file for details about how to enable this mechanism in your configuration file, how to disable it for specific recipes, and how to share ]\` files between builds.

> 请访问 [https://ccache.samba.org/](https://ccache.samba.org/) 了解 C/C++ 编译器缓存的信息，以及:oe_[git:%60ccache.bbclass](git:%60ccache.bbclass) \</openembedded-core/tree/meta/classes/ccache.bbclass\>文件，了解如何在配置文件中启用此机制、如何为特定的 recipes 禁用它，以及如何在构建之间共享[ccache]文件。

However, using the class can lead to unexpected side-effects. Thus, using this class is not recommended.

# `chrpath`

The `ref-classes-chrpath` recipes to change `RPATH` records within binaries in order to make them relocatable.

> 类 `ref-classes-chrpath` recipes 中的二进制文件中的 `RPATH` 记录，以使其可重定位。

# `cmake`

The `ref-classes-cmake` variable to specify additional configuration options to pass to the `cmake` command line.

> 这个 ref-classes-cmake 类允许 recipes 使用 CMake 构建系统构建软件。您可以使用 EXTRA_OECMAKE 变量指定要传递给 cmake 命令行的其他配置选项。

By default, the `ref-classes-cmake` variable to `Unix Makefiles` to use GNU make instead.

> 默认情况下，`ref-classes-cmake` 变量设置为 `Unix Makefiles` 以使用 GNU make。

If you need to install custom CMake toolchain files supplied by the application being built, you should install them (during `ref-tasks-install`/cmake/modules/`.

> 如果您需要安装所构建应用程序提供的自定义 CMake 工具链文件，应在(`ref-tasks-install`/cmake/modules/`。

# `cml1`

The `ref-classes-cml1` class provides basic support for the Linux kernel style build configuration system.

# `compress_doc`

Enables compression for man pages and info pages. This class is intended to be inherited globally. The default compression mechanism is gz (gzip) but you can select an alternative mechanism by setting the `DOC_COMPRESS` variable.

> 启用手册页和信息页的压缩。此类旨在全局继承。默认压缩机制是 gz(gzip)，但您可以通过设置 `DOC_COMPRESS` 变量来选择替代机制。

# `copyleft_compliance`

The `ref-classes-copyleft_compliance` class.

> 这个 ref-classes-copyleft_compliance 类保存源代码以符合许可证要求。尽管它被 ref-classes-archiver 类取代，但仍有一些用户使用它。

# `copyleft_filter`

A class used by the `ref-classes-archiver` classes for filtering licenses. The `copyleft_filter` class is an internal class and is not intended to be used directly.

> 这个 `copyleft_filter` 类被 `ref-classes-archiver` 和 `ref-classes-copyleft_compliance` 类用于过滤许可证。这个 `copyleft_filter` 类是一个内部类，不应该直接使用。

# `core-image`

The `ref-classes-core-image`.

> `ref-classes-core-image`。

# `cpan*`

The `cpan* <ref-classes-cpan>` classes support Perl modules.

Recipes for Perl modules are simple. These recipes usually only need to point to the source\'s archive and then inherit the proper class file. Building is split into two methods depending on which method the module authors used.

> 模块的 Perl 配方很简单。这些配方通常只需要指向源存档，然后继承正确的类文件。根据模块作者使用的方法，构建分为两种方法。

- Modules that use old `Makefile.PL`-based build system require `cpan.bbclass` in their recipes.
- Modules that use `Build.PL`-based build system require using `cpan_build.bbclass` in their recipes.

Both build methods inherit the `cpan-base <ref-classes-cpan>` class for basic Perl support.

# `create-spdx`

The `ref-classes-create-spdx` documents based upon image and SDK contents.

> 类 `ref-classes-create-spdx` 提供了基于镜像和 SDK 内容自动创建 SPDX SBOM 文档的支持。

This class is meant to be inherited globally from a configuration file:

```
INHERIT += "create-spdx"
```

The toplevel `SPDX`. There are other related files in the same directory, as well as in `tmp/deploy/spdx`.

> 顶层 `SPDX` 输出文件以 JSON 格式生成，文件名为 `IMAGE-MACHINE.spdx.json`，位于 `Build Directory` 的 `tmp/deploy/images/MACHINE/` 目录下。同一目录下也有其他相关文件，以及 `tmp/deploy/spdx` 目录中的文件。

The exact behaviour of this class, and the amount of output can be controlled by the `SPDX_PRETTY` variables.

> 此类的确切行为以及输出量可以由 `SPDX_PRETTY`、`SPDX_ARCHIVE_PACKAGED`、`SPDX_ARCHIVE_SOURCES` 和 `SPDX_INCLUDE_SOURCES` 变量来控制。

See the description of these variables and the \"`dev-manual/sbom:creating a software bill of materials`\" section in the Yocto Project Development Manual for more details.

> 查看这些变量的描述，以及 Yocto Project 开发手册中的“dev-manual / sbom：创建软件物料清单”部分，了解更多细节。

# `cross`

The `ref-classes-cross` class provides support for the recipes that build the cross-compilation tools.

# `cross-canadian`

The `ref-classes-cross-canadian`\" section in the Yocto Project Overview and Concepts Manual for more discussion on these cross-compilation tools.

> 这个 ref-classes-cross-canadian 类提供支持来构建用于 SDK 的加拿大跨编译工具。有关这些跨编译工具的更多讨论，请参见 Yocto 项目概览和概念手册中的“overview-manual/concepts：cross-development toolchain generation”部分。

# `crosssdk`

The `ref-classes-crosssdk`\" section in the Yocto Project Overview and Concepts Manual for more discussion on these cross-compilation tools.

> 类 `ref-classes-crosssdk`”部分。

# `cve-check`

The `ref-classes-cve-check` class looks for known CVEs (Common Vulnerabilities and Exposures) while building with BitBake. This class is meant to be inherited globally from a configuration file:

> `ref-classes-cve-check` 类在使用 BitBake 构建时会查找已知的 CVE(常见漏洞和暴露)。该类旨在从配置文件中全局继承：

```
INHERIT += "cve-check"
```

To filter out obsolete CVE database entries which are known not to impact software from Poky and OE-Core, add following line to the build configuration file:

> 在 Poky 和 OE-Core 的构建配置文件中添加以下行以过滤出已知不会影响软件的过时的 CVE 数据库条目：

```
include cve-extra-exclusions.inc
```

You can also look for vulnerabilities in specific packages by passing `-c cve_check` to BitBake.

After building the software with Bitbake, CVE check output reports are available in `tmp/deploy/cve` and image specific summaries in `tmp/deploy/images/*.cve` or `tmp/deploy/images/*.json` files.

> 在使用 Bitbake 构建软件后，CVE 检查输出报告可在'tmp/deploy/cve'中获得，特定镜像的摘要可在'tmp/deploy/images/*.cve'或'tmp/deploy/images/*.json'文件中获得。

When building, the CVE checker will emit build time warnings for any detected issues which are in the state `Unpatched`, meaning that CVE issue seems to affect the software component and version being compiled and no patches to address the issue are applied. Other states for detected CVE issues are: `Patched` meaning that a patch to address the issue is already applied, and `Ignored` meaning that the issue can be ignored.

> 当构建时，CVE 检查程序会为任何检测到的处于“未补丁”状态的问题发出构建时警告，这意味着 CVE 问题似乎影响编译的软件组件和版本，并且没有补丁来解决该问题。检测到的 CVE 问题的其他状态有：“已补丁”，意味着已经应用了补丁来解决该问题，以及“已忽略”，意味着可以忽略该问题。

The `Patched` state of a CVE issue is detected from patch files with the format `CVE-ID.patch`, e.g. `CVE-2019-20633.patch`, in the `SRC_URI` and using CVE metadata of format `CVE: CVE-ID` in the commit message of the patch file.

> 系统从具有格式 `CVE-ID.patch`(例如 `CVE-2019-20633.patch`)的补丁文件中检测到 CVE 问题的“已补丁”状态，并在补丁文件的提交消息中使用格式为 `CVE：CVE-ID` 的 CVE 元数据。

If the recipe lists the `CVE-ID` in `CVE_CHECK_IGNORE` variable, then the CVE state is reported as `Ignored`. Multiple CVEs can be listed separated by spaces. Example:

> 如果 recipes 中列出了 CVE-ID 变量 CVE_CHECK_IGNORE 中，那么 CVE 状态将被报告为“忽略”。多个 CVE 可以用空格分隔列出。例如：

```
CVE_CHECK_IGNORE += "CVE-2020-29509 CVE-2020-29511"
```

If CVE check reports that a recipe contains false positives or false negatives, these may be fixed in recipes by adjusting the CVE product name using `CVE_PRODUCT` which can be adjusted to one or more CVE database vendor and product pairs using the syntax:

> 如果 CVE 检查报告称配方包含误报或漏报，可以通过使用 `CVE_PRODUCT`，可以使用以下语法调整为一个或多个 CVE 数据库供应商和产品对：

```
CVE_PRODUCT = "flex_project:flex"
```

where `flex_project` is the CVE database vendor name and `flex` is the product name. Similarly if the default recipe version `PV` variable can be used to set the CVE database compatible version number, for example:

> 如果 `flex_project` 是 CVE 数据库供应商的名称，而 `flex` 是产品名称，那么同样，如果默认 recipes 版本 `PV` 不匹配上游发布版本或 CVE 数据库的版本号，则可以使用 `CVE_VERSION` 变量来设置与 CVE 数据库兼容的版本号，例如：

```
CVE_VERSION = "2.39"
```

Any bugs or missing or incomplete information in the CVE database entries should be fixed in the CVE database via the [NVD feedback form](https://nvd.nist.gov/info/contact-form).

> 任何 CVE 数据库条目中的错误、遗漏或不完整信息，都应通过 [NVD 反馈表单](https://nvd.nist.gov/info/contact-form)在 CVE 数据库中进行修复。

Users should note that security is a process, not a product, and thus also CVE checking, analyzing results, patching and updating the software should be done as a regular process. The data and assumptions required for CVE checker to reliably detect issues are frequently broken in various ways. These can only be detected by reviewing the details of the issues and iterating over the generated reports, and following what happens in other Linux distributions and in the greater open source community.

> 用户应该注意，安全是一个过程，而不是一个产品，因此也应该定期进行 CVE 检查、分析结果、补丁和更新软件。CVE 检查器确定可靠问题所需的数据和假设经常以各种方式被打破。这些只能通过审查问题的细节并迭代生成的报告，以及关注其他 Linux 发行版和更大的开源社区中发生的事情来检测到。

You will find some more details in the \"`dev-manual/vulnerabilities:checking for vulnerabilities`\" section in the Development Tasks Manual.

> 你可以在开发任务手册中的“dev-manual/vulnerabilities：检查漏洞”部分找到更多的细节。

# `debian`

The `ref-classes-debian` class renames output packages so that they follow the Debian naming policy (i.e. `glibc` becomes `libc6` and `glibc-devel` becomes `libc6-dev`.) Renaming includes the library name and version as part of the package name.

> `ref-classes-debian` 类重命名输出包，以便遵循 Debian 命名策略(即 `glibc` 变为 `libc6`，`glibc-devel` 变为 `libc6-dev`)。重命名包括库名称和版本作为包名的一部分。

If a recipe creates packages for multiple libraries (shared object files of `.so` type), use the `LEAD_SONAME` variable in the recipe to specify the library on which to apply the naming scheme.

> 如果一个 recipes 创建了多个库的包(`.so` 类型的共享对象文件)，请在 recipes 中使用 `LEAD_SONAME` 变量指定要应用命名方案的库。

# `deploy`

The `ref-classes-deploy`.

> 类 `ref-classes-deploy`。

# `devicetree`

The `ref-classes-devicetree` class allows to build a recipe that compiles device tree source files that are not in the kernel tree.

> 这个 ref-classes-devicetree 类允许构建一个菜谱，用于编译不在内核树中的设备树源文件。

The compilation of out-of-tree device tree sources is the same as the kernel in-tree device tree compilation process. This includes the ability to include sources from the kernel such as SoC `dtsi` files as well as C header files, such as `gpio.h`.

> 编译外树设备树源代码与内树内核设备树编译过程相同。这包括从内树内核中包含源代码，如 SoC `dtsi` 文件以及 C 头文件，如 `gpio.h` 的能力。

The `ref-tasks-compile` task will compile two kinds of files:

- Regular device tree sources with a `.dts` extension.
- Device tree overlays, detected from the presence of the `/plugin/;` string in the file contents.

This class behaves in a similar way as the `ref-classes-kernel-devicetree` output. Additionally, the device trees are populated into the sysroot for access via the sysroot from within other recipes.

> 这个类的行为与 `ref-classes-kernel-devicetree` 输出发生冲突。另外，设备树还会被填充到 sysroot 中，以便在其他配方中通过 sysroot 访问。

By default, all device tree sources located in `DT_FILES_PATH`). For convenience, both `.dts` and `.dtb` extensions can be used.

> 默认情况下，位于 `DT_FILES_PATH` 目录中的所有设备树源文件都会被编译。要仅选择特定的源文件，请将 `DT_FILES` 设置为以空格分隔的文件列表(相对于 `DT_FILES_PATH`)。为了方便起见，可以使用 `.dts` 和 `.dtb` 扩展名。

An extra padding is appended to non-overlay device trees binaries. This can typically be used as extra space for adding extra properties at boot time. The padding size can be modified by setting `DT_PADDING_SIZE` to the desired size, in bytes.

> 非覆盖设备树二进制文件会追加额外的填充。这通常可用于在引导时添加额外的属性。可以通过将 `DT_PADDING_SIZE` 设置为所需的大小(以字节为单位)来修改填充大小。

See :oe_[git:%60devicetree.bbclass](git:%60devicetree.bbclass) sources \</openembedded-core/tree/meta/classes-recipe/devicetree.bbclass\>\` for further variables controlling this class.

> 请参阅：oe_[git:%60devicetree.bbclass](git:%60devicetree.bbclass) 源码 </openembedded-core/tree/meta/classes-recipe/devicetree.bbclass>，以了解更多控制此类的变量。

Here is an excerpt of an example `recipes-kernel/linux/devicetree-acme.bb` recipe inheriting this class:

```
inherit devicetree
COMPATIBLE_MACHINE = "^mymachine$"
SRC_URI:mymachine = "file://mymachine.dts"
```

# `devshell`

The `ref-classes-devshell`.

> 类 ref-classes-devshell 的更多信息，请参阅 Yocto 项目开发任务手册中的“ dev-manual/development-shell：使用开发 shell”部分。

# `devupstream`

The `ref-classes-devupstream` to add a variant of the recipe that fetches from an alternative URI (e.g. Git) instead of a tarball. Following is an example:

> 这个 `ref-classes-devupstream` 来添加一个变体的配方，它从替代 URI(例如 Git)而不是 tarball 获取。以下是一个例子：

```
BBCLASSEXTEND = "devupstream:target"
SRC_URI:class-devupstream = "git://git.example.com/example;branch=main"
SRCREV:class-devupstream = "abcd1234"
```

Adding the above statements to your recipe creates a variant that has `DEFAULT_PREFERENCE` set to \"-1\". Consequently, you need to select the variant of the recipe to use it. Any development-specific adjustments can be done by using the `class-devupstream` override. Here is an example:

> 加入上述声明到你的配方中，就会创建一个 `DEFAULT_PREFERENCE` 设置为“-1”的变体。因此，你需要选择配方的变体来使用它。任何开发特定的调整可以通过使用 `class-devupstream` 覆盖来完成。这里有一个例子：

```
DEPENDS:append:class-devupstream = " gperf-native"
do_configure:prepend:class-devupstream() {
    touch $/README
}
```

The class currently only supports creating a development variant of the target recipe, not `ref-classes-native` variants.

> 現時類只支援建立目標配方的開發變體，而不支援 `ref-classes-native` 變體。

The `BBCLASSEXTEND` variants. Consequently, this functionality can be added in a future release.

> 语法 BBCLASSEXTEND(即 devupstream:target)支持 ref-classes-native 和 ref-classes-nativesdk 变体。因此，这种功能可以在未来的版本中添加。

Support for other version control systems such as Subversion is limited due to BitBake\'s automatic fetch dependencies (e.g. `subversion-native`).

# `externalsrc`

The `ref-classes-externalsrc` class supports building software from source code that is external to the OpenEmbedded build system. Building software from an external source tree means that the build system\'s normal fetch, unpack, and patch process is not used.

> 类 `ref-classes-externalsrc` 支持从 OpenEmbedded 构建系统外部的源代码构建软件。从外部源树构建软件意味着构建系统的正常获取、解压缩和补丁处理过程不会被使用。

By default, the OpenEmbedded build system uses the `S`.

> 默认情况下，OpenEmbedded 构建系统使用 `S`。

By default, this class expects the source code to support recipe builds that use the `B`):

> 默认情况下，该类期望源代码支持使用 `B`)分开：

```
$/
```

See these variables for more information: `WORKDIR`,

> 查看更多信息，请参阅这些变量：`WORKDIR`。

For more information on the `ref-classes-externalsrc`\" section in the Yocto Project Development Tasks Manual.

> 要了解有关 `ref-classes-externalsrc`”部分。

# `extrausers`

The `ref-classes-extrausers` variable.

> 类 `ref-classes-extrausers` 允许在镜像级别应用额外的用户和组配置。继承此类(全局或镜像配方)允许使用变量 `EXTRA_USERS_PARAMS` 执行额外的用户和组操作。

::: note
::: title
Note
:::

The user and group operations added using the `ref-classes-extrausers` class to add user and group configuration to a specific recipe.

> 使用 `ref-classes-extrausers` 类将用户和组配置添加到特定配方。
> :::

Here is an example that uses this class in an image recipe:

```
inherit extrausers
EXTRA_USERS_PARAMS = "\
    useradd -p '' tester; \
    groupadd developers; \
    userdel nobody; \
    groupdel -g video; \
    groupmod -g 1020 developers; \
    usermod -s /bin/sh tester; \
    "
```

Here is an example that adds two users named \"tester-jim\" and \"tester-sue\" and assigns passwords. First on host, create the (escaped) password hash:

> 这里是一个示例，它添加了两个名为“tester-jim”和“tester-sue”的用户，并分配了密码。首先，在主机上创建(转义)密码哈希：

```
printf "%q" $(mkpasswd -m sha256crypt tester01)
```

The resulting hash is set to a variable and used in `useradd` command parameters:

```
inherit extrausers
PASSWD = "\$X\$ABC123\$A-Long-Hash"
EXTRA_USERS_PARAMS = "\
    useradd -p '$' tester-jim; \
    useradd -p '$' tester-sue; \
    "
```

Finally, here is an example that sets the root password:

```
inherit extrausers
EXTRA_USERS_PARAMS = "\
    usermod -p '$' root; \
    "
```

::: note
::: title
Note
:::

From a security perspective, hardcoding a default password is not generally a good idea or even legal in some jurisdictions. It is recommended that you do not do this if you are building a production image.

> 从安全角度来看，硬编码默认密码通常不是一个好主意，甚至在某些司法管辖区是不合法的。如果您正在构建生产镜像，建议您不要这样做。
> :::

# `features_check`

The `ref-classes-features_check`.

> 类 `ref-classes-features_check`。

This class provides support for the following variables:

- `REQUIRED_DISTRO_FEATURES`
- `CONFLICT_DISTRO_FEATURES`
- `ANY_OF_DISTRO_FEATURES`
- `REQUIRED_MACHINE_FEATURES`
- `CONFLICT_MACHINE_FEATURES`
- `ANY_OF_MACHINE_FEATURES`
- `REQUIRED_COMBINED_FEATURES`
- `CONFLICT_COMBINED_FEATURES`
- `ANY_OF_COMBINED_FEATURES`

If any conditions specified in the recipe using the above variables are not met, the recipe will be skipped, and if the build system attempts to build the recipe then an error will be triggered.

> 如果使用上述变量指定的配方中的任何条件未满足，则将跳过该配方，如果构建系统尝试构建配方，则会触发错误。

# `fontcache`

The `ref-classes-fontcache` class generates the proper post-install and post-remove (postinst and postrm) scriptlets for font packages. These scriptlets call `fc-cache` (part of `Fontconfig`) to add the fonts to the font information cache. Since the cache files are architecture-specific, `fc-cache` runs using QEMU if the postinst scriptlets need to be run on the build host during image creation.

> `ref-classes-fontcache` 类为字体包生成了正确的安装后和移除后(postinst 和 postrm)脚本。这些脚本调用 `fc-cache`(`Fontconfig` 的一部分)将字体添加到字体信息缓存中。由于缓存文件是特定于架构的，因此如果在镜像创建期间需要在构建主机上运行 postinst 脚本，则会使用 QEMU 运行 `fc-cache`。

If the fonts being installed are in packages other than the main package, set `FONT_PACKAGES` to specify the packages containing the fonts.

> 如果安装的字体不在主包中，可以设置 `FONT_PACKAGES` 来指定包含字体的包。

# `fs-uuid`

The `ref-classes-fs-uuid` class only works on `ext` file systems and depends on `tune2fs`.

> 类 ref-classes-fs-uuid 只适用于 ext 文件系统，并依赖于 tune2fs。

# `gconf`

The `ref-classes-gconf`-gconf`) that is created automatically when this class is inherited. This package uses the appropriate post-install and post-remove (postinst/postrm) scriptlets to register and unregister the schemas in the target image.

> 该 ref-classes-gconf 类为需要安装 GConf 架构的配方提供了公共功能。架构将被放入单独的包($ -gconf)中，在继承此类时将自动创建该包。此包使用适当的 post-install 和 post-remove(postinst / postrm)脚本片段来在目标映像中注册和取消注册架构。

# `gettext`

The `ref-classes-gettext` class provides support for building software that uses the GNU `gettext` internationalization and localization system. All recipes building software that use `gettext` should inherit this class.

> 类 `ref-classes-gettext` 提供支持，用于构建使用 GNU `gettext` 国际化和本地化系统的软件。所有使用 `gettext` 构建软件的配方都应该继承这个类。

# `github-releases`

For recipes that fetch release tarballs from github, the `ref-classes-github-releases` class sets up a standard way for checking available upstream versions (to support `devtool upgrade` and the Automated Upgrade Helper (AUH)).

> 对于从 Github 获取发布 tarball 的 recipes，`ref-classes-github-releases` 类建立了一种标准方法来检查可用的上游版本(以支持 `devtool upgrade` 和自动升级助手(AUH))。

To use it, add \"`ref-classes-github-releases` within the recipe.

> 要使用它，请在配方的 inherit 行中添加"ref-classes-github-releases"`。

# `gnomebase`

The `ref-classes-gnomebase` with the typical GNOME installation paths.

> `ref-classes-gnomebase`。

# `go`

The `ref-classes-go` ones.

> 类 `ref-classes-go` 变量。

To build a Go program with the Yocto Project, you can use the :yocto_[git:%60go-helloworld_0.1.bb](git:%60go-helloworld_0.1.bb) \</poky/tree/meta/recipes-extended/go-examples/go-helloworld_0.1.bb\>\` recipe as an example.

> 要使用 Yocto Project 构建 Go 程序，您可以使用:yocto_[git:`go-helloworld_0.1.bb`](git:%60go-helloworld_0.1.bb%60)</poky/tree/meta/recipes-extended/go-examples/go-helloworld_0.1.bb>` recipes 作为示例。

# `go-mod`

The `ref-classes-go-mod` class.

> `ref-classes-go-mod` 类。

See the associated `GO_WORKDIR` variable.

# `gobject-introspection`

Provides support for recipes building software that supports GObject introspection. This functionality is only enabled if the \"gobject-introspection-data\" feature is in `DISTRO_FEATURES`.

> 提供支持支持 GObject 内省的菜谱构建软件。只有当"gobject-introspection-data"特性在 DISTRO_FEATURES 中以及"qemu-usermode"在 MACHINE_FEATURES 中时，才启用此功能。

::: note
::: title
Note
:::

This functionality is `backfilled <ref-features-backfill>`, respectively.

> 这个功能默认情况下是被补充的<ref-features-backfill>来禁用。
> :::

# `grub-efi`

The `ref-classes-grub-efi` class provides `grub-efi`-specific functions for building bootable images.

This class supports several variables:

- `INITRD`: Indicates list of filesystem images to concatenate and use as an initial RAM disk (initrd) (optional).
- `ROOTFS`: Indicates a filesystem image to include as the root filesystem (optional).
- `GRUB_GFXSERIAL`: Set this to \"1\" to have graphics and serial in the boot menu.
- `LABELS`: A list of targets for the automatic configuration.
- `APPEND`: An override list of append strings for each `LABEL`.
- `GRUB_OPTS`: Additional options to add to the configuration (optional). Options are delimited using semi-colon characters (`;`).

> `GRUB_OPTS`：添加到配置中的其他选项(可选)。选项使用分号字符(`;`)进行分隔。

- `GRUB_TIMEOUT`: Timeout before executing the default `LABEL` (optional).

# `gsettings`

The `ref-classes-gsettings` class provides common functionality for recipes that need to install GSettings (glib) schemas. The schemas are assumed to be part of the main package. Appropriate post-install and post-remove (postinst/postrm) scriptlets are added to register and unregister the schemas in the target image.

> 这个 `ref-classes-gsettings` 类提供了需要安装 GSettings(glib)模式的 recipes 的常见功能。假设模式是主要软件包的一部分。会在目标镜像中添加适当的安装后和删除后(postinst / postrm)脚本，以注册和取消注册模式。

# `gtk-doc`

The `ref-classes-gtk-doc` class is a helper class to pull in the appropriate `gtk-doc` dependencies and disable `gtk-doc`.

> `ref-classes-gtk-doc` 类是一个帮助类，用于拉取适当的 `gtk-doc` 依赖项并禁用 `gtk-doc`。

# `gtk-icon-cache`

The `ref-classes-gtk-icon-cache` class generates the proper post-install and post-remove (postinst/postrm) scriptlets for packages that use GTK+ and install icons. These scriptlets call `gtk-update-icon-cache` to add the fonts to GTK+\'s icon cache. Since the cache files are architecture-specific, `gtk-update-icon-cache` is run using QEMU if the postinst scriptlets need to be run on the build host during image creation.

> 这个 ref-classes-gtk-icon-cache 类为使用 GTK+ 并安装图标的软件包生成正确的安装后和卸载后(postinst/postrm)脚本。这些脚本调用 gtk-update-icon-cache 来将字体添加到 GTK+ 的图标缓存中。由于缓存文件是特定于架构的，如果在创建映像期间需要在构建主机上运行 postinst 脚本，则使用 QEMU 运行 gtk-update-icon-cache。

# `gtk-immodules-cache`

The `ref-classes-gtk-immodules-cache` class generates the proper post-install and post-remove (postinst/postrm) scriptlets for packages that install GTK+ input method modules for virtual keyboards. These scriptlets call `gtk-update-icon-cache` to add the input method modules to the cache. Since the cache files are architecture-specific, `gtk-update-icon-cache` is run using QEMU if the postinst scriptlets need to be run on the build host during image creation.

> 这个类为安装 GTK+ 虚拟键盘输入法模块的软件包生成正确的安装后和移除后(postinst / postrm)脚本。这些脚本将调用 gtk-update-icon-cache 来将输入法模块添加到缓存中。由于缓存文件是特定于架构的，如果在创建映像期间需要在构建主机上运行 postinst 脚本，则使用 QEMU 运行 gtk-update-icon-cache。

If the input method modules being installed are in packages other than the main package, set `GTKIMMODULES_PACKAGES` to specify the packages containing the modules.

> 如果安装的输入法模块不在主包中，请设置 `GTKIMMODULES_PACKAGES` 来指定包含模块的包。

# `gzipnative`

The `ref-classes-gzipnative` class enables the use of different native versions of `gzip` and `pigz` rather than the versions of these tools from the build host.

> 这个 ref-classes-gzipnative 类可以使用不同的本地版本的 gzip 和 pigz，而不是来自构建主机的这些工具的版本。

# `icecc`

The `ref-classes-icecc` class supports [Icecream](https://github.com/icecc/icecream), which facilitates taking compile jobs and distributing them among remote machines.

> 这个 ref-classes-icecc 类支持 Icecream([https://github.com/icecc/icecream](https://github.com/icecc/icecream))，它可以方便地将编译任务分发到远程机器上。

The class stages directories with symlinks from `gcc` and `g++` to `icecc`, for both native and cross compilers. Depending on each configure or compile, the OpenEmbedded build system adds the directories at the head of the `PATH` list and then sets the `ICECC_CXX` and `ICECC_CC` variables, which are the paths to the `g++` and `gcc` compilers, respectively.

> 类阶段将 `gcc` 和 `g++` 目录与符号链接从 `icecc` 连接起来，无论是本地编译器还是跨编译器。根据每个配置或编译，OpenEmbedded 构建系统将目录添加到 `PATH` 列表的开头，然后设置 `ICECC_CXX` 和 `ICECC_CC` 变量，分别是 `g++` 和 `gcc` 编译器的路径。

For the cross compiler, the class creates a `tar.gz` file that contains the Yocto Project toolchain and sets `ICECC_VERSION`, which is the version of the cross-compiler used in the cross-development toolchain, accordingly.

> 为了交叉编译器，该类创建一个包含 Yocto 项目工具链的 `tar.gz` 文件，并相应地设置 `ICECC_VERSION`，这是交叉开发工具链中使用的交叉编译器的版本。

The class handles all three different compile stages (i.e native, cross-kernel and target) and creates the necessary environment `tar.gz` file to be used by the remote machines. The class also supports SDK generation.

> 这个类处理所有三个不同的编译阶段(即本地、跨内核和目标)，并创建必要的环境 `tar.gz` 文件，以供远程机器使用。该类还支持 SDK 生成。

If `ICECC_PATH` is set in your `local.conf` file, the variable should point to the `icecc-create-env` script provided by the user. If you do not point to a user-provided script, the build system uses the default script provided by the recipe :oe_[git:%60icecc-create-env_0.1.bb](git:%60icecc-create-env_0.1.bb) \</openembedded-core/tree/meta/recipes-devtools/icecc-create-env/icecc-create-env_0.1.bb\>\`.

> 如果您的 local.conf 文件中没有设置 ICECC_PATH，那么该类将尝试使用 which 来定位 icecc 二进制文件。如果您的 local.conf 文件中设置了 ICECC_ENV_EXEC，则该变量应指向用户提供的 icecc-create-env 脚本。如果您不指向用户提供的脚本，则构建系统将使用 recipes 提供的默认脚本：oe_[git:`icecc-create-env_0.1.bb`](git:%60icecc-create-env_0.1.bb%60)</openembedded-core/tree/meta/recipes-devtools/icecc-create-env/icecc-create-env_0.1.bb>`.

::: note
::: title
Note
:::

This script is a modified version and not the one that comes with `icecream`.
:::

If you do not want the Icecream distributed compile support to apply to specific recipes or classes, you can ask them to be ignored by Icecream by listing the recipes and classes using the `ICECC_RECIPE_DISABLE` variables, respectively, in your `local.conf` file. Doing so causes the OpenEmbedded build system to handle these compilations locally.

> 如果您不希望 Icecream 分发编译支持应用于特定的配方或类别，您可以通过在本地.conf 文件中列出使用 `ICECC_RECIPE_DISABLE` 和 `ICECC_CLASS_DISABLE` 变量的配方和类别，要求 Icecream 忽略它们。这样做会导致 OpenEmbedded 构建系统在本地处理这些编译。

Additionally, you can list recipes using the `ICECC_RECIPE_ENABLE` variable.

> 此外，您可以在 `local.conf` 文件中使用 `ICECC_RECIPE_ENABLE` 变量列出 recipes，以强制使用空 `PARALLEL_MAKE` 变量启用 `icecc`。

Inheriting the `ref-classes-icecc` class or nobody should.

> 继承 `ref-classes-icecc` 类，或者没有人继承。

At the distribution level, you can inherit the `ref-classes-icecc` variable to \"1\" as follows:

> 在分发层面，您可以继承 `ref-classes-icecc` 变量设置为“1”来禁用该功能，如下所示：

```
INHERIT_DISTRO:append = " icecc"
ICECC_DISABLED ??= "1"
```

This practice makes sure everyone is using the same signatures but also requires individuals that do want to use Icecream to enable the feature individually as follows in your `local.conf` file:

> 这种做法可以确保每个人使用相同的签名，但也要求那些想使用 Icecream 的个人在本地的 `local.conf` 文件中单独启用该功能：

```
ICECC_DISABLED = ""
```

# `image`

The `ref-classes-image` class helps support creating images in different formats. First, the root filesystem is created from packages using one of the `rootfs*.bbclass` files (depending on the package format used) and then one or more image files are created.

> `ref-classes-image` 类可帮助支持创建不同格式的镜像。首先，使用 `rootfs*.bbclass` 文件之一(取决于所使用的包格式)从包创建根文件系统，然后创建一个或多个镜像文件。

- The `IMAGE_FSTYPES` variable controls the types of images to generate.
- The `IMAGE_INSTALL` variable controls the list of packages to install into the image.

For information on customizing images, see the \"`dev-manual/customizing-images:customizing images`\" section in the Yocto Project Overview and Concepts Manual.

> 要了解自定义镜像的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/customizing-images：自定义镜像”部分。要了解镜像是如何创建的，请参阅 Yocto 项目概述和概念手册中的“overview-manual/concepts：镜像”部分。

# `image-buildinfo`

The `ref-classes-image-buildinfo`). This can be useful for manually determining the origin of any given image. It writes out two sections:

> `ref-classes-image-buildinfo` 指定)。这可以有助于手动确定任何给定镜像的来源。它会输出两个部分：

1. \`Build Configuration\`: a list of variables and their values (specified by `IMAGE_BUILDINFO_VARS`)

> `1. 构建配置：一组变量及其值(由默认值为DISTRO和DISTRO_VERSION的IMAGE_BUILDINFO_VARS指定)。`

2. \`Layer Revisions\`: the revisions of all of the layers used in the build.

Additionally, when building an SDK it will write the same contents to `/buildinfo` by default (as specified by `SDK_BUILDINFO_FILE`).

> 此外，默认情况下，构建 SDK 时会按照 `SDK_BUILDINFO_FILE`(指定)将相同内容写入 `/buildinfo`。

# `image_types`

The `ref-classes-image_types` variable. You can use this class as a reference on how to add support for custom image output types.

> 类 `ref-classes-image_types` 启用的标准镜像输出类型。您可以使用此类作为如何添加自定义镜像输出类型的参考。

By default, the `ref-classes-image` class uses the `IMGCLASSES` variable as follows:

> 默认情况下，`ref-classes-image` 类使用 `IMGCLASSES` 变量如下：

```
IMGCLASSES = "rootfs_$"
IMGCLASSES += "$"
IMGCLASSES += "$"
IMGCLASSES += "$"
IMGCLASSES += "image_types_wic"
IMGCLASSES += "rootfs-postcommands"
IMGCLASSES += "image-postinst-intercepts"
inherit $
```

The `ref-classes-image_types` class also handles conversion and compression of images.

::: note
::: title
Note
:::

To build a VMware VMDK image, you need to add \"wic.vmdk\" to `IMAGE_FSTYPES`. This would also be similar for Virtual Box Virtual Disk Image (\"vdi\") and QEMU Copy On Write Version 2 (\"qcow2\") images.

> 要构建 VMware VMDK 镜像，您需要将“wic.vmdk”添加到 IMAGE_FSTYPES。这对于 Virtual Box 虚拟磁盘镜像(“vdi”)和 QEMU 复制写入版本 2(“qcow2”)镜像也是相似的。
> :::

# `image-live`

This class controls building \"live\" (i.e. HDDIMG and ISO) images. Live images contain syslinux for legacy booting, as well as the bootloader specified by `EFI_PROVIDER` contains \"efi\".

> 这个类控制创建“实时”(即 HDDIMG 和 ISO)映像。Live 映像包含用于传统引导的 syslinux，以及由 `EFI_PROVIDER` 包含“efi”。

Normally, you do not use this class directly. Instead, you add \"live\" to `IMAGE_FSTYPES`.

# `insane`

The `ref-classes-insane` class adds a step to the package generation process so that output quality assurance checks are generated by the OpenEmbedded build system. A range of checks are performed that check the build\'s output for common problems that show up during runtime. Distribution policy usually dictates whether to include this class.

> 类 ref-classes-insane 增加了一个步骤，以便由 OpenEmbedded 构建系统生成输出质量保证检查。执行一系列检查，以检查构建的输出是否存在运行时出现的常见问题。是否包括此类通常由分发策略决定。

You can configure the sanity checks so that specific test failures either raise a warning or an error message. Typically, failures for new tests generate a warning. Subsequent failures for the same test would then generate an error message once the metadata is in a known and good condition. See the \"`/ref-manual/qa-checks`\" Chapter for a list of all the warning and error messages you might encounter using a default configuration.

> 你可以配置健全检查，使特定的测试失败可以产生警告或错误消息。通常，新测试的失败会产生警告。一旦元数据处于已知和良好的状态，后续相同测试的失败将产生错误消息。有关使用默认配置可能遇到的所有警告和错误消息的列表，请参阅“/ref-manual/qa-checks”章节。

Use the `WARN_QA``, must be used:

> 使用 `WARN_QA``：

```
INSANE_SKIP:$ += "dev-so"
```

Please keep in mind that the QA checks are meant to detect real or potential problems in the packaged output. So exercise caution when disabling these checks.

> 请记住，QA 检查旨在检测打包输出中的实际或潜在问题。因此，在禁用这些检查时要谨慎行事。

Here are the tests you can list with the `WARN_QA` variables:

- `already-stripped:` Checks that produced binaries have not already been stripped prior to the build system extracting debug symbols. It is common for upstream software projects to default to stripping debug symbols for output binaries. In order for debugging to work on the target using `-dbg` packages, this stripping must be disabled.

> 检查生成的二进制文件在构建系统提取调试符号之前是否已被剥离。上游软件项目默认剥离调试符号以便输出二进制文件。为了使使用“-dbg”软件包在目标上调试工作，必须禁用此剥离。

- `arch:` Checks the Executable and Linkable Format (ELF) type, bit size, and endianness of any binaries to ensure they match the target architecture. This test fails if any binaries do not match the type since there would be an incompatibility. The test could indicate that the wrong compiler or compiler options have been used. Sometimes software, like bootloaders, might need to bypass this check.

> - arch：检查可执行文件和可链接格式(ELF)的类型、位大小和字节序，以确保它们与目标架构匹配。如果任何二进制文件不匹配类型，则此测试将失败，因为会出现不兼容性。该测试可能表明使用了错误的编译器或编译器选项。有时候软件，比如引导加载程序，可能需要绕过此检查。

- `buildpaths:` Checks for paths to locations on the build host inside the output files. Not only can these leak information about the build environment, they also hinder binary reproducibility.

> 检查输出文件中构建主机位置的路径。这些不仅可以泄漏有关构建环境的信息，还会妨碍二进制可重现性。

- `build-deps:` Determines if a build-time dependency that is specified through `DEPENDS` for the package in question on `initscripts-functions` so that the OpenEmbedded build system is able to ensure that the `initscripts` recipe is actually built and thus the `initscripts-functions` package is made available.

> build-deps：通过 DEPENDS、显式 RDEPENDS 或任务级别的依赖关系确定是否存在运行时依赖关系的构建时依赖关系。这种确定特别有用，可以发现运行时依赖关系是如何在打包过程中检测和添加的。如果元数据中没有明确指定依赖关系，在打包阶段已经太晚了，无法确保依赖项被构建，因此在 ref-tasks-rootfs 任务中将该软件包安装到映像中时，可能会因自动检测到的依赖关系未满足而出现错误。例如，ref-classes-update-rc.d 类会自动为安装引用/etc/init.d/functions 的 initscript 的软件包添加对 initscripts-functions 软件包的依赖关系。该配方应该真正对 initscripts 软件包有一个明确的 RDEPENDS，以便 OpenEmbedded 构建系统能够确保 initscripts 配方实际上被构建，因此 initscripts-functions 软件包可用。

- `configure-gettext:` Checks that if a recipe is building something that uses automake and the automake files contain an `AM_GNU_GETTEXT` directive, that the recipe also inherits the `ref-classes-gettext` class to ensure that gettext is available during the build.

> 检查如果一个 recipes 正在构建使用自动化的东西，并且自动化文件包含一个 `AM_GNU_GETTEXT` 指令，那么该 recipes 也会继承 `ref-classes-gettext` 类，以确保在构建期间可以使用 gettext。

- `compile-host-path:` Checks the `ref-tasks-compile` log for indications that paths to locations on the build host were used. Using such paths might result in host contamination of the build output.

> 检查“ref-tasks-compile”日志以查看是否使用了构建主机上位置的路径。使用此类路径可能会导致构建输出受到主机污染。

- `debug-deps:` Checks that all packages except `-dbg` packages do not depend on `-dbg` packages, which would cause a packaging bug.
- `debug-files:` Checks for `.debug` directories in anything but the `-dbg` package. The debug files should all be in the `-dbg` package. Thus, anything packaged elsewhere is incorrect packaging.

> 检查除-dbg 包外的任何内容中的.debug 目录。调试文件应全部包含在-dbg 包中。因此，其他包装的任何内容都是不正确的包装。

- `dep-cmp:` Checks for invalid version comparison statements in runtime dependency relationships between packages (i.e. in `RDEPENDS` variable values). Any invalid comparisons might trigger failures or undesirable behavior when passed to the package manager.

> - dep-cmp：检查在包之间的运行时依赖关系(即在 RDEPENDS，RRECOMMENDS，RSUGGESTS，RPROVIDES，RREPLACES 和 RCONFLICTS 变量值中)中的无效版本比较语句。任何无效的比较可能在传递给软件包管理器时触发失败或不希望的行为。

- `desktop:` Runs the `desktop-file-validate` program against any `.desktop` files to validate their contents against the specification for `.desktop` files.

> 运行 `desktop-file-validate` 程序，对任何 `.desktop` 文件进行验证，以确保其内容符合 `.desktop` 文件的规范。

- `dev-deps:` Checks that all packages except `-dev` or `-staticdev` packages do not depend on `-dev` packages, which would be a packaging bug.
- `dev-so:` Checks that the `.so` symbolic links are in the `-dev` package and not in any of the other packages. In general, these symlinks are only useful for development purposes. Thus, the `-dev` package is the correct location for them. In very rare cases, such as dynamically loaded modules, these symlinks are needed instead in the main package.

> 检查 `.so` 符号链接是否在 `-dev` 包中，而不是在其他任何包中。一般来说，这些符号链接只有在开发过程中才有用。因此，`-dev` 包是正确的位置。在极少数情况下，比如动态加载模块，这些符号链接需要放在主包中。

- `empty-dirs:` Checks that packages are not installing files to directories that are normally expected to be empty (such as `/tmp`) The list of directories that are checked is specified by the `QA_EMPTY_DIRS` variable.

> 检查包是否安装到通常预期为空的目录(例如 `/tmp`)，要检查的目录由 `QA_EMPTY_DIRS` 变量指定。

- `file-rdeps:` Checks that file-level dependencies identified by the OpenEmbedded build system at packaging time are satisfied. For example, a shell script might start with the line `#!/bin/bash`. This line would translate to a file dependency on `/bin/bash`. Of the three package managers that the OpenEmbedded build system supports, only RPM directly handles file-level dependencies, resolving them automatically to packages providing the files. However, the lack of that functionality in the other two package managers does not mean the dependencies do not still need resolving. This QA check attempts to ensure that explicitly declared `RDEPENDS` exist to handle any file-level dependency detected in packaged files.

> file-rdeps 检查 OpenEmbedded 构建系统在打包时识别的文件级依赖关系是否得到满足。例如，shell 脚本可能以 `#!/bin/bash` 开头。这一行会转换为对 `/bin/bash` 的文件依赖关系。OpenEmbedded 构建系统支持的三个包管理器中，只有 RPM 直接处理文件级依赖关系，自动解析为提供文件的软件包。但是，其他两个包管理器缺乏这种功能并不意味着这些依赖关系仍然不需要解析。此 QA 检查试图确保显式声明的 `RDEPENDS` 存在以处理打包文件中检测到的任何文件级依赖关系。

- `files-invalid:` Checks for `FILES` variable values that contain \"//\", which is invalid.
- `host-user-contaminated:` Checks that no package produced by the recipe contains any files outside of `/home` with a user or group ID that matches the user running BitBake. A match usually indicates that the files are being installed with an incorrect UID/GID, since target IDs are independent from host IDs. For additional information, see the section describing the `ref-tasks-install` task.

> 检查由 recipes 生成的任何包是否包含位于/home 之外且具有与运行 BitBake 的用户匹配的用户或组 ID 的文件。通常，匹配表明文件正在使用不正确的 UID / GID 进行安装，因为目标 ID 与主机 ID 独立。有关其他信息，请参见描述“ref-tasks-install”任务的部分。

- `incompatible-license:` Report when packages are excluded from being created due to being marked with a license that is in `INCOMPATIBLE_LICENSE`.

> 报告当标记有不兼容许可证的包被排除在外时，因为它们被标记为 `INCOMPATIBLE_LICENSE`。

- `install-host-path:` Checks the `ref-tasks-install` log for indications that paths to locations on the build host were used. Using such paths might result in host contamination of the build output.

> 检查'ref-tasks-install'日志以查看是否使用了构建主机上的位置的路径。使用这样的路径可能会导致构建输出的主机污染。

- `installed-vs-shipped:` Reports when files have been installed within `ref-tasks-install` if the files are not needed in any package.

> 报告何时文件已在 `ref-tasks-install` 结束时删除这些文件。

- `invalid-chars:` Checks that the recipe metadata variables `DESCRIPTION` do not contain non-UTF-8 characters. Some package managers do not support such characters.

> 检查 recipes 元数据变量 `DESCRIPTION` 不包含非 UTF-8 字符。一些软件包管理器不支持这样的字符。

- `invalid-packageconfig:` Checks that no undefined features are being added to `PACKAGECONFIG`. For example, any name \"foo\" for which the following form does not exist:

> 检查 PACKAGECONFIG 中是否添加了未定义的特性。例如，任何名称为“foo”的特性，如果不存在以下形式：

```
PACKAGECONFIG[foo] = "..."
```

- `la:` Checks `.la` files for any `TMPDIR` paths. Any `.la` file containing these paths is incorrect since `libtool` adds the correct sysroot prefix when using the files automatically itself.

> 检查 `.la` 文件中是否有 `TMPDIR` 路径。如果 `.la` 文件中包含这些路径，则是不正确的，因为当使用文件时，`libtool` 会自动添加正确的系统根前缀。

- `ldflags:` Ensures that the binaries were linked with the `LDFLAGS` variable is being passed to the linker command.

> `ldflags：` 确保二进制文件使用构建系统提供的 `LDFLAGS` 选项进行链接。如果测试失败，请检查 `LDFLAGS` 变量是否被传递给链接器命令。

- `libdir:` Checks for libraries being installed into incorrect (possibly hardcoded) installation paths. For example, this test will catch recipes that install `/lib/bar.so` when `$` is \"/usr/lib\".

> - `libdir`：检查是否将库安装到不正确的(可能是硬编码的)安装路径中。例如，当 `$` 为“/usr/lib”时，配方安装 `/usr/lib64/foo.so`。

- `libexec:` Checks if a package contains files in `/usr/libexec`. This check is not performed if the `libexecdir` variable has been set explicitly to `/usr/libexec`.

> 检查软件包是否包含 `/usr/libexec` 中的文件。如果 `libexecdir` 变量明确设置为 `/usr/libexec`，则不执行此检查。

- `mime:` Check that if a package contains mime type files (`.xml` files in `$ class in order to ensure that these get properly installed.

> 检查如果一个软件包包含 MIME 类型文件(在 `$ 类，以确保这些文件能够正确安装。

- `mime-xdg:` Checks that if a package contains a .desktop file with a \'MimeType\' key present, that the recipe inherits the `ref-classes-mime-xdg` class that is required in order for that to be activated.

> 检查软件包是否包含具有'MimeType'键的.desktop 文件，如果有，则软件包需要继承 `ref-classes-mime-xdg` 类，以便激活该文件。

- `missing-update-alternatives:` Check that if a recipe sets the `ALTERNATIVE` such that the alternative will be correctly set up.

> 检查配方是否设置了 ALTERNATIVE 变量，如果设置了，配方也要继承 ref-classes-update-alternatives，以便正确设置替代方案。

- `packages-list:` Checks for the same package being listed multiple times through the `PACKAGES` variable value. Installing the package in this manner can cause errors during packaging.

> 检查通过 `PACKAGES` 变量值列出的多次列出的相同软件包。以这种方式安装软件包可能会导致打包时出错。

- `patch-fuzz:` Checks for fuzz in patch files that may allow them to apply incorrectly if the underlying code changes.
- `patch-status-core:` Checks that the Upstream-Status is specified and valid in the headers of patches for recipes in the OE-Core layer.
- `patch-status-noncore:` Checks that the Upstream-Status is specified and valid in the headers of patches for recipes in layers other than OE-Core.
- `perllocalpod:` Checks for `perllocal.pod` being erroneously installed and packaged by a recipe.
- `perm-config:` Reports lines in `fs-perms.txt` that have an invalid format.
- `perm-line:` Reports lines in `fs-perms.txt` that have an invalid format.
- `perm-link:` Reports lines in `fs-perms.txt` that specify \'link\' where the specified target already exists.
- `perms:` Currently, this check is unused but reserved.
- `pkgconfig:` Checks `.pc` files for any `TMPDIR` paths. Any `.pc` file containing these paths is incorrect since `pkg-config` itself adds the correct sysroot prefix when the files are accessed.

> 检查.pc 文件是否包含 TMPDIR/WORKDIR 路径。任何包含这些路径的.pc 文件都是不正确的，因为当文件被访问时，pkg-config 自身会添加正确的 sysroot 前缀。

- `pkgname:` Checks that all packages in `PACKAGES` have names that do not contain invalid characters (i.e. characters other than 0-9, a-z, ., +, and -).

> `pkgname:` 检查 `PACKAGES` 中的所有包的名称是否不包含无效字符(即除 0-9、a-z、.、+ 和 - 之外的字符)。

- `pkgv-undefined:` Checks to see if the `PKGV`.

> 检查在 `ref-tasks-package` 变量是否未定义。

- `pkgvarcheck:` Checks through the variables `RDEPENDS`, `pkg_preinst`, `pkg_postinst`, `pkg_prerm` and `pkg_postrm`, and reports if there are variable sets that are not package-specific. Using these variables without a package suffix is bad practice, and might unnecessarily complicate dependencies of other packages within the same recipe or have other unintended consequences.

> pkgvarcheck：检查 RDEPENDS，RRECOMMENDS，RSUGGESTS，RCONFLICTS，RPROVIDES，RREPLACES，FILES，ALLOW_EMPTY，pkg_preinst，pkg_postinst，pkg_prerm 和 pkg_postrm 变量，并报告是否存在不特定于包的变量集。使用这些没有包后缀的变量是不良的做法，可能会无意中使同一配方中其他软件包的依赖关系变得复杂，或产生其他意外后果。

- `pn-overrides:` Checks that a recipe does not have a name (`PN` = "xyz"`effectively turn into` FILES = "xyz"`.

> 检查菜谱是否没有名称(PN)值出现在 OVERRIDES 中。如果菜谱的 PN 值与 OVERRIDES 中的内容(例如 MACHINE 或 DISTRO)匹配，可能会产生意想不到的后果。例如，FILES: $ = "xyz"的赋值实际上变成了 FILES = "xyz"。

- `rpaths:` Checks for rpaths in the binaries that contain build system paths such as `TMPDIR`. If this test fails, bad `-rpath` options are being passed to the linker commands and your binaries have potential security issues.

> 检查二进制文件中是否包含构建系统路径(如 `TMPDIR`)。如果此测试失败，则会向链接器命令传递不良的 `-rpath` 选项，您的二进制文件可能存在安全问题。

- `shebang-size:` Check that the shebang line (`#!` in the first line) in a packaged script is not longer than 128 characters, which can cause an error at runtime depending on the operating system.

> 检查打包脚本中的 shebang 行(第一行的 `#!`)是否不超过 128 个字符，否则可能会根据操作系统出现运行时错误。

- `split-strip:` Reports that splitting or stripping debug symbols from binaries has failed.
- `staticdev:` Checks for static library files (`*.a`) in non-`staticdev` packages.
- `src-uri-bad:` Checks that the `SRC_URI``) nor refers to unstable Github archive tarballs.

> 检查由配方设定的 SRC_URI 值不含有引用 $)，也不指向不稳定的 Github 存档 tarballs。

- `symlink-to-sysroot:` Checks for symlinks in packages that point into `TMPDIR` on the host. Such symlinks will work on the host, but are clearly invalid when running on the target.

> 检查包中指向主机上的 TMPDIR 的符号链接。这样的符号链接在主机上可以工作，但在目标上显然是无效的。

- `textrel:` Checks for ELF binaries that contain relocations in their `.text` sections, which can result in a performance impact at runtime. See the explanation for the `ELF binary` message in \"`/ref-manual/qa-checks`\" for more information regarding runtime performance issues.

> 检查 ELF 二进制文件，其中包含 `.text` 部分的重定位，可能会导致运行时性能影响。有关运行时性能问题的更多信息，请参阅“/ref-manual/qa-checks”中的“ELF 二进制文件”消息解释。

- `unhandled-features-check:` check that if one of the variables that the `ref-classes-features_check` in order for the requirement to actually work.

> 检查如果 ref-classes-features_check 类支持的变量(例如 REQUIRED_DISTRO_FEATURES)由配方设置，则该配方也必须继承 ref-classes-features_check，以使要求实际有效。

- `unlisted-pkg-lics:` Checks that all declared licenses applying for a package are also declared on the recipe level (i.e. any license in `LICENSE:*` should appear in `LICENSE`).

> 检查所有声明为软件包应用的许可证是否也在配方级别上声明(即 `LICENSE:*` 中的任何许可证都应出现在 `LICENSE` 中)。

- `useless-rpaths:` Checks for dynamic library load paths (rpaths) in the binaries that by default on a standard system are searched by the linker (e.g. `/lib` and `/usr/lib`). While these paths will not cause any breakage, they do waste space and are unnecessary.

> - 无用的 RPATH：检查二进制文件中的动态库加载路径(RPATH)，在标准系统中，这些路径由链接器搜索(例如 `/lib` 和 `/usr/lib`)。虽然这些路径不会造成任何破坏，但它们浪费空间并且是不必要的。

- `usrmerge:` If `usrmerge` is in `DISTRO_FEATURES`, this check will ensure that no package installs files to root (`/bin`, `/sbin`, `/lib`, `/lib64`) directories.

> 如果 usrmerge 包含在 DISTRO_FEATURES 中，此检查将确保没有软件包将文件安装到根(/bin、/sbin、/lib、/lib64)目录。

- `var-undefined:` Reports when variables fundamental to packaging (i.e. `WORKDIR`.

> 报告当在 ref-tasks-package 中未定义包装所必需的变量(即 WORKDIR、DEPLOY_DIR、D、PN 和 PKGD)时。

- `version-going-backwards:` If the `ref-classes-buildhistory` class is enabled, reports when a package being written out has a lower version than the previously written package under the same name. If you are placing output packages into a feed and upgrading packages on a target system using that feed, the version of a package going backwards can result in the target system not correctly upgrading to the \"new\" version of the package.

> 如果启用了 `ref-classes-buildhistory` 类，则会报告写出的包的版本低于同名包的先前版本。如果您将输出包放入发布源并使用该发布源在目标系统上升级包，则版本向后移动可能会导致目标系统无法正确升级到“新”版本的包。

::: note
::: title
Note
:::

This is only relevant when you are using runtime package management on your target system.
:::

- `xorg-driver-abi:` Checks that all packages containing Xorg drivers have ABI dependencies. The `xserver-xorg` recipe provides driver ABI names. All drivers should depend on the ABI versions that they have been built against. Driver recipes that include `xorg-driver-input.inc` or `xorg-driver-video.inc` will automatically get these versions. Consequently, you should only need to explicitly add dependencies to binary driver recipes.

> 检查所有包含 Xorg 驱动程序的软件包是否具有 ABI 依赖关系。`xserver-xorg` 配方提供驱动程序 ABI 名称。所有驱动程序都应该依赖于它们构建的 ABI 版本。包含 `xorg-driver-input.inc` 或 `xorg-driver-video.inc` 的驱动程序配方将自动获得这些版本。因此，您只需要显式添加依赖关系到二进制驱动程序配方即可。

# `insserv`

The `ref-classes-insserv` class uses the `insserv` utility to update the order of symbolic links in `/etc/rc?.d/` within an image based on dependencies specified by LSB headers in the `init.d` scripts themselves.

> 这个 ref-classes-insserv 类使用 insserv 实用程序根据 LSB 标头中指定的 init.d 脚本的依赖关系，在基于映像的/etc/rc?.d/中更新符号链接的顺序。

# `kernel`

The `ref-classes-kernel` class.

> 类 `ref-classes-kernel` 进行树外模块构建。

If a file named `defconfig` is listed in `SRC_URI` copies it as `.config` in the build directory, so it is automatically used as the kernel configuration for the build. This copy is not performed in case `.config` already exists there: this allows recipes to produce a configuration by other means in `do_configure:prepend`.

> 如果在 `SRC_URI` 中列出了一个名为 `defconfig` 的文件，那么默认情况下，`ref-tasks-configure` 将其复制为构建目录中的 `.config`，因此它自动用作构建的内核配置。如果那里已经存在 `.config`，则不执行此复制：这允许配方以 `do_configure:prepend` 的其他方式生成配置。

Each built kernel module is packaged separately and inter-module dependencies are created by parsing the `modinfo` output. If all modules are required, then installing the `kernel-modules` package installs all packages with modules and various other kernel packages such as `kernel-vmlinux`.

> 每个构建的内核模块都是单独打包的，而模块间的依赖关系是通过解析 `modinfo` 输出来创建的。如果需要所有模块，那么安装 `kernel-modules` 软件包会安装所有带有模块的软件包以及其他各种内核软件包，如 `kernel-vmlinux`。

The `ref-classes-kernel`\" section in the Yocto Project Development Tasks Manual.

> 类 `ref-classes-kernel`”部分。

Various other classes are used by the `ref-classes-kernel` classes.

> 其他各种类被 `ref-classes-kernel` 类。

# `kernel-arch`

The `ref-classes-kernel-arch` class sets the `ARCH` environment variable for Linux kernel compilation (including modules).

> `ref-classes-kernel-arch` 类为 Linux 内核编译(包括模块)设置 `ARCH` 环境变量。

# `kernel-devicetree`

The `ref-classes-kernel-devicetree` class, supports device tree generation.

> 这个继承自 `ref-classes-kernel` 类支持设备树的生成。

Its behavior is mainly controlled by the following variables:

- `KERNEL_DEVICETREE_BUNDLE`: whether to bundle the kernel and device tree
- `KERNEL_DTBDEST`: directory where to install DTB files
- `KERNEL_DTBVENDORED`: whether to keep vendor subdirectories
- `KERNEL_DTC_FLAGS`: flags for `dtc`, the Device Tree Compiler
- `KERNEL_PACKAGE_NAME`: base name of the kernel packages

# `kernel-fitimage`

The `ref-classes-kernel-fitimage` bundle, an optional RAM disk, and any number of device trees.

> 类 `ref-classes-kernel-fitimage` 包，可选的 RAM 磁盘，以及任意数量的设备树。

To create a FIT image, it is required that `KERNEL_CLASSES` is set to \"fitImage\".

> 要创建 FIT 镜像，需要将 `KERNEL_CLASSES` 设置为“fitImage”。

The options for the device tree compiler passed to `mkimage -D` when creating the FIT image are specified using the `UBOOT_MKIMAGE_DTCOPTS` variable.

> `UBOOT_MKIMAGE_DTCOPTS` 变量用于指定在创建 FIT 镜像时传递给 `mkimage -D` 的设备树编译器的选项。

Only a single kernel can be added to the FIT image created by `ref-classes-kernel-fitimage` to \"2\" is necessary if such addresses are 64 bit ones.

> 只能添加一个内核到由 `ref-classes-kernel-fitimage` 创建的 FIT 映像中，FIT 中的内核映像是必需的。U-Boot 加载内核映像的地址由 `UBOOT_LOADADDRESS` 指定，入口点由 `UBOOT_ENTRYPOINT` 指定。如果这些地址是 64 位的，则必须将 `FIT_ADDRESS_CELLS` 设置为“2”。

Multiple device trees can be added to the FIT image created by `ref-classes-kernel-fitimage` for device tree binaries.

> 可以向 `ref-classes-kernel-fitimage` 指定用于设备树二进制文件。

Only a single RAM disk can be added to the FIT image created by `ref-classes-kernel-fitimage` is set to 0.

> 只能添加一个 RAM 磁盘到由 `ref-classes-kernel-fitimage` 创建的 FIT 映像中，FIT 中的 RAM 磁盘是可选的。U-Boot 加载 RAM 磁盘映像的地址由 `UBOOT_RD_LOADADDRESS` 指定，入口点由 `UBOOT_RD_ENTRYPOINT` 指定。当指定 `INITRAMFS_IMAGE` 并将 `INITRAMFS_IMAGE_BUNDLE` 设置为 0 时，将添加 RAM 磁盘到 FIT 映像中。

Only a single `Initramfs`.

> 只能添加一个 Initramfs 包到由 ref-classes-kernel-fitimage 创建的 FIT 镜像中，FIT 中的 Initramfs 包是可选的。如果使用 Initramfs，则内核配置为与根文件系统在同一个二进制文件中打包(例如：zImage-initramfs-MACHINE.bin)。当内核复制到 RAM 并执行时，它会解压 Initramfs 根文件系统。可以在指定 INITRAMFS_IMAGE 并将 INITRAMFS_IMAGE_BUNDLE 设置为 1 时启用 Initramfs 包。U-boot 加载 Initramfs 包的地址由 UBOOT_LOADADDRESS 指定，入口点由 UBOOT_ENTRYPOINT 指定。

Only a single U-boot boot script can be added to the FIT image created by `ref-classes-kernel-fitimage` class. At run-time, U-boot CONFIG_BOOTCOMMAND define can be configured to load the boot script from the FIT image and executes it.

> 只能添加一个 U-boot 启动脚本到由“ref-classes-kernel-fitimage”创建的 FIT 镜像中，而且这个启动脚本是可选的。启动脚本在 ITS 文件中被指定为一个包含 U-boot 命令的文本文件。当使用启动脚本时，用户应该配置 U-boot“ref-tasks-install”任务来将脚本复制到 sysroot 中，这样就可以通过“ref-classes-kernel-fitimage”类将脚本包含在 FIT 镜像中。在运行时，可以配置 U-boot CONFIG_BOOTCOMMAND 定义以从 FIT 镜像加载启动脚本并执行它。

The FIT image generated by `ref-classes-kernel-fitimage` are set to \"1\".

> 镜像签名功能由 `ref-classes-kernel-fitimage` 类生成用于签名 FIT 镜像的密钥。

# `kernel-grub`

The `ref-classes-kernel-grub` class updates the boot area and the boot menu with the kernel as the priority boot mechanism while installing a RPM to update the kernel on a deployed target.

> 这个 `ref-classes-kernel-grub` 类在在部署的目标上安装 RPM 更新内核时，会更新引导区和引导菜单，以内核作为优先启动机制。

# `kernel-module-split`

The `ref-classes-kernel-module-split` class provides common functionality for splitting Linux kernel modules into separate packages.

> 这个 `ref-classes-kernel-module-split` 类提供了将 Linux 内核模块拆分成单独的软件包的常见功能。

# `kernel-uboot`

The `ref-classes-kernel-uboot` class provides support for building from vmlinux-style kernel sources.

# `kernel-uimage`

The `ref-classes-kernel-uimage` class provides support to pack uImage.

# `kernel-yocto`

The `ref-classes-kernel-yocto` class provides common functionality for building from linux-yocto style kernel source repositories.

> 这个 ref-classes-kernel-yocto 类提供了从 linux-yocto 样式内核源代码存储库构建的公共功能。

# `kernelsrc`

The `ref-classes-kernelsrc` class sets the Linux kernel source and version.

# `lib_package`

The `ref-classes-lib_package`-bin` package to make their installation optional.

> `ref-classes-lib_package` 类支持构建库并生成可执行二进制文件的 recipes，这些二进制文件不应默认安装在库中。相反，将二进制文件添加到单独的 `$-bin` 包中，以使其安装变为可选。

# `libc*`

The `ref-classes-libc*` classes support recipes that build packages with `libc`:

- The `libc-common <ref-classes-libc*>` class provides common support for building with `libc`.
- The `libc-package <ref-classes-libc*>` class supports packaging up `glibc` and `eglibc`.

# `license`

The `ref-classes-license` variable.

> 类 `ref-classes-license` 提供许可证清单创建和许可证排除功能。使用变量 `INHERIT_DISTRO` 的默认值可以默认启用此类。

# `linux-kernel-base`

The `ref-classes-linux-kernel-base` class provides common functionality for recipes that build out of the Linux kernel source tree. These builds goes beyond the kernel itself. For example, the Perf recipe also inherits this class.

> 这个 `ref-classes-linux-kernel-base` 类为从 Linux 内核源代码树构建的配方提供了共同的功能。这些构建超出了内核本身。例如，Perf 配方也继承了这个类。

# `linuxloader`

Provides the function `linuxloader()`, which gives the value of the dynamic loader/linker provided on the platform. This value is used by a number of other classes.

> 提供函数 `linuxloader()`，用于获取平台上提供的动态加载器/链接器的值。这个值被其他许多类所使用。

# `logging`

The `ref-classes-logging` class provides the standard shell functions used to log messages for various BitBake severity levels (i.e. `bbplain`, `bbnote`, `bbwarn`, `bberror`, `bbfatal`, and `bbdebug`).

> 类 `ref-classes-logging` 提供了用于为各种 BitBake 严重程度(即 `bbplain`、`bbnote`、`bbwarn`、`bberror`、`bbfatal` 和 `bbdebug`)记录消息的标准 shell 函数。

This class is enabled by default since it is inherited by the `ref-classes-base` class.

# `meson`

The `ref-classes-meson` variables to specify additional configuration options to be passed using the `meson` command line.

> 类 `ref-classes-meson` 来指定要使用 `meson` 命令行传递的其他配置选项。

# `metadata_scm`

The `ref-classes-metadata_scm` class provides functionality for querying the branch and revision of a Source Code Manager (SCM) repository.

> 类 ref-classes-metadata_scm 提供查询源代码管理器(SCM)仓库分支和修订版本的功能。

The `ref-classes-base` class.

> `ref-classes-base` 类继承了。

# `migrate_localcount`

The `ref-classes-migrate_localcount` class verifies a recipe\'s localcount data and increments it appropriately.

# `mime`

The `ref-classes-mime` class generates the proper post-install and post-remove (postinst/postrm) scriptlets for packages that install MIME type files. These scriptlets call `update-mime-database` to add the MIME types to the shared database.

> 这个 ref-classes-mime 类为安装 MIME 类型文件的软件包生成正确的安装后和删除后(postinst/postrm)脚本。这些脚本调用 update-mime-database 来将 MIME 类型添加到共享数据库中。

# `mime-xdg`

The `ref-classes-mime-xdg` class generates the proper post-install and post-remove (postinst/postrm) scriptlets for packages that install `.desktop` files containing `MimeType` entries. These scriptlets call `update-desktop-database` to add the MIME types to the database of MIME types handled by desktop files.

> 这个 ref-classes-mime-xdg 类为安装包生成安装后和删除后(postinst/postrm)的脚本，这些安装包包含包含 MimeType 条目的.desktop 文件。这些脚本调用 update-desktop-database 来将 MIME 类型添加到由桌面文件处理的 MIME 类型数据库中。

Thanks to this class, when users open a file through a file browser on recently created images, they don\'t have to choose the application to open the file from the pool of all known applications, even the ones that cannot open the selected file.

> 谢谢这个课程，当用户通过文件浏览器打开最近创建的镜像文件时，他们不必从所有已知应用程序的池中选择要打开文件的应用程序，即使是无法打开所选文件的应用程序也是如此。

If you have recipes installing their `.desktop` files as absolute symbolic links, the detection of such files cannot be done by the current implementation of this class. In this case, you have to add the corresponding package names to the `MIME_XDG_PACKAGES` variable.

> 如果您使用绝对符号链接安装其 `.desktop` 文件，则无法通过当前此类实现来检测此类文件。在这种情况下，您必须将相应的包名添加到变量 `MIME_XDG_PACKAGES` 中。

# `mirrors`

The `ref-classes-mirrors` within recipes is unavailable.

> 这个 ref-classes-mirrors 类为源代码镜像设置了一些标准的 MIRRORS 条目。如果 recipes 中的 SRC_URI 中指定的上游源不可用，则这些镜像提供了一个后备路径。

This class is enabled by default since it is inherited by the `ref-classes-base` class.

# `module`

The `ref-classes-module` tasks. The class provides everything needed to build and package a kernel module.

> 模块类提供支持，用于构建树外的 Linux 内核模块。该类继承了模块基类和内核模块分割类，并实现了编译和安装任务。该类提供了构建和打包内核模块所需的一切。

For general information on out-of-tree Linux kernel modules, see the \"`kernel-dev/common:incorporating out-of-tree modules`\" section in the Yocto Project Linux Kernel Development Manual.

> 关于外树 Linux 内核模块的一般信息，请参见 Yocto Project Linux 内核开发手册中的“kernel-dev/common：包含外树模块”部分。

# `module-base`

The `ref-classes-module-base` class.

> 类 `ref-classes-module-base`。

# `multilib*`

The `ref-classes-multilib*` classes provide support for building libraries with different target optimizations or target architectures and installing them side-by-side in the same image.

> 类 `ref-classes-multilib*` 提供支持，用于使用不同的目标优化或目标架构构建库，并将它们并排安装在同一个映像中。

For more information on using the Multilib feature, see the \"`dev-manual/libraries:combining multiple versions of library files into one image`\" section in the Yocto Project Development Tasks Manual.

> 要了解有关使用多库特性的更多信息，请参阅 Yocto Project 开发任务手册中的“dev-manual/libraries：将多个版本的库文件组合成一个映像”部分。

# `native`

The `ref-classes-native` (i.e. tools that use the compiler or other tools from the build host).

> 这个 ref-classes-native 类为构建在构建主机(即使用构建主机上的编译器或其他工具的工具)上运行的工具提供了常见的功能。

You can create a recipe that builds tools that run natively on the host a couple different ways:

- Create a `myrecipe-native.bb` recipe that inherits the `ref-classes-native` class is inherited last.

> 创建一个继承 `ref-classes-native` 类。

::: note
::: title
Note
:::

When creating a recipe this way, the recipe name must follow this naming convention:

```
myrecipe-native.bb
```

Not using this naming convention can lead to subtle problems caused by existing code that depends on that naming convention.
:::

- Create or modify a target recipe that contains the following:

  ```
  BBCLASSEXTEND = "native"
  ```

  Inside the recipe, use `:class-native` and `:class-target` overrides to specify any functionality specific to the respective native or target case.

> 在 recipes 中，使用 `:class-native` 和 `:class-target` 覆盖以指定各自本机或目标情况下的任何特定功能。

Although applied differently, the `ref-classes-native` class is used with both methods. The advantage of the second method is that you do not need to have two separate recipes (assuming you need both) for native and target. All common parts of the recipe are automatically shared.

> 尽管应用方式不同，但 `ref-classes-native` 类与两种方法都是使用的。第二种方法的优势在于，您不需要为本地和目标分别拥有两个单独的配方(假设您需要两者)。配方的所有共同部分都会自动共享。

# `nativesdk`

The `ref-classes-nativesdk`).

> 类 `ref-classes-nativesdk` 提供了希望构建作为 SDK 一部分运行的工具(即在 `SDKMACHINE` 上运行的工具)的公共功能。

You can create a recipe that builds tools that run on the SDK machine a couple different ways:

- Create a `nativesdk-myrecipe.bb` recipe that inherits the `ref-classes-nativesdk` class is inherited last.

> 创建一个名为 nativesdk-myrecipe.bb 的配方，继承 ref-classes-nativesdk 类。如果使用此方法，必须在配方中的继承声明之后排列 ref-classes-nativesdk 类，以便最后继承该类。

- Create a `ref-classes-nativesdk` variant of any recipe by adding the following:

  ```
  BBCLASSEXTEND = "nativesdk"
  ```

  Inside the recipe, use `:class-nativesdk` and `:class-target` overrides to specify any functionality specific to the respective SDK machine or target case.

> 在 recipes 中，使用 `:class-nativesdk` 和 `:class-target` 覆盖来指定相应 SDK 机器或目标情况下的任何特定功能。

::: note
::: title
Note
:::

When creating a recipe, you must follow this naming convention:

```
nativesdk-myrecipe.bb
```

Not doing so can lead to subtle problems because there is code that depends on the naming convention.
:::

Although applied differently, the `ref-classes-nativesdk` class is used with both methods. The advantage of the second method is that you do not need to have two separate recipes (assuming you need both) for the SDK machine and the target. All common parts of the recipe are automatically shared.

> 尽管应用方式不同，但 `ref-classes-nativesdk` 类在这两种方法中都得到了使用。第二种方法的优势在于，您不需要为 SDK 机器和目标分别编写两个不同的配方(假设您需要两者)。所有共同的部分都会自动共享。

# `nopackages`

Disables packaging tasks for those recipes and classes where packaging is not needed.

# `npm`

Provides support for building Node.js software fetched using the `node package manager (NPM) <Npm_(software)>`.

::: note
::: title
Note
:::

Currently, recipes inheriting this class must use the `npm://` fetcher to have dependencies fetched and packaged automatically.
:::

For information on how to create NPM packages, see the \"`dev-manual/packages:creating node package manager (npm) packages`\" section in the Yocto Project Development Tasks Manual.

> 要了解如何创建 NPM 包，请参阅 Yocto 项目开发任务手册中的“dev-manual/packages：创建节点包管理器(NPM)包”部分。

# `oelint`

The `ref-classes-oelint`.

> 这个 ref-classes-oelint 类是源目录中 meta/classes 中提供的一个过时的检查工具。

There are some classes that could be generally useful in OE-Core but are never actually used within OE-Core itself. The `ref-classes-oelint` class is one such example. However, being aware of this class can reduce the proliferation of different versions of similar classes across multiple layers.

> 在 OE-Core 中有一些可能通用的类，但它们实际上从未在 OE-Core 中使用过。`ref-classes-oelint` 类就是一个例子。然而，了解这个类可以减少多个层次上不同版本的相似类的激增。

# `overlayfs`

It\'s often desired in Embedded System design to have a read-only root filesystem. But a lot of different applications might want to have read-write access to some parts of a filesystem. It can be especially useful when your update mechanism overwrites the whole root filesystem, but you may want your application data to be preserved between updates. The `ref-classes-overlayfs` class provides a way to achieve that by means of `overlayfs` and at the same time keeping the base root filesystem read-only.

> 在嵌入式系统设计中，通常希望拥有只读的根文件系统。但是许多不同的应用程序可能希望对文件系统的某些部分具有读写访问权限。当更新机制覆盖整个根文件系统时，这可能尤其有用，但是您可能希望应用程序数据在更新之间保留。`ref-classes-overlayfs` 类提供了一种通过 `overlayfs` 实现此目的的方法，同时保持基本根文件系统为只读。

To use this class, set a mount point for a partition `overlayfs` is going to use as upper layer in your machine configuration. The underlying file system can be anything that is supported by `overlayfs`. This has to be done in your machine configuration:

> 要使用此类，请在您的机器配置中为要用作上层的分区设置一个挂载点 `overlayfs`。底层文件系统可以是 `overlayfs` 支持的任何内容。这必须在您的机器配置中完成：

```
OVERLAYFS_MOUNT_POINT[data] = "/data"
```

::: note
::: title
Note
:::

- QA checks fail to catch file existence if you redefine this variable in your recipe!
- Only the existence of the systemd mount unit file is checked, not its contents.
- To get more details on `overlayfs`, its internals and supported operations, please refer to the official documentation of the [Linux kernel](https://www.kernel.org/doc/html/latest/filesystems/overlayfs.html).

> 要了解更多关于 overlayfs 的细节、内部情况和支持的操作，请参考 [Linux 内核](https://www.kernel.org/doc/html/latest/filesystems/overlayfs.html)的官方文档。
> :::

The class assumes you have a `data.mount` systemd unit defined elsewhere in your BSP (e.g. in `systemd-machine-units` recipe) and it\'s installed into the image.

> 该类假设您在 BSP(例如在'systemd-machine-units'配方中)中定义了一个 `data.mount` systemd 单元，并且已将其安装到镜像中。

Then you can specify writable directories on a recipe basis (e.g. in my-application.bb):

```
OVERLAYFS_WRITABLE_PATHS[data] = "/usr/share/my-custom-application"
```

To support several mount points you can use a different variable flag. Assuming we want to have a writable location on the file system, but do not need that the data survives a reboot, then we could have a `mnt-overlay.mount` unit for a `tmpfs` file system.

> 为了支持多个挂载点，您可以使用不同的变量标志。假设我们希望在文件系统上有一个可写的位置，但不需要数据在重新启动后保持，那么我们可以为 `tmpfs` 文件系统提供一个 `mnt-overlay.mount` 单元。

In your machine configuration:

```
OVERLAYFS_MOUNT_POINT[mnt-overlay] = "/mnt/overlay"
```

and then in your recipe:

```
OVERLAYFS_WRITABLE_PATHS[mnt-overlay] = "/usr/share/another-application"
```

On a practical note, your application recipe might require multiple overlays to be mounted before running to avoid writing to the underlying file system (which can be forbidden in case of read-only file system) To achieve that `ref-classes-overlayfs`-overlays.service `and can be depended on in your application recipe (named` application `in the following example)` systemd` unit by adding to the unit the following:

> 在实际操作上，您的应用程序配方可能需要在运行之前挂载多个覆盖，以避免写入底层文件系统(如果是只读文件系统，则可能被禁止)。为此，ref-classes-overlayfs 提供了一个 systemd 助手服务用于挂载覆盖。此助手服务命名为 $-overlays.service，可以在应用程序配方(以下示例中命名为“应用程序”)中的 systemd 单元中进行依赖，方法是添加以下内容：

```
[Unit]
After=application-overlays.service
Requires=application-overlays.service
```

::: note
::: title
Note
:::

The class does not support the `/etc` directory itself, because `systemd` depends on it. In order to get `/etc` in overlayfs, see `ref-classes-overlayfs-etc`.

> 类不支持 `/etc` 目录本身，因为 `systemd` 依赖它。要在 overlayfs 中获取 `/etc`，请参阅 `ref-classes-overlayfs-etc`。
> :::

# `overlayfs-etc`

In order to have the `/etc` directory in overlayfs a special handling at early boot stage is required. The idea is to supply a custom init script that mounts `/etc` before launching the actual init program, because the latter already requires `/etc` to be mounted.

> 为了在 overlayfs 中拥有 `/etc` 目录，需要在早期启动阶段进行特殊处理。这个想法是提供一个自定义的初始脚本，在启动实际的初始程序之前挂载 `/etc`，因为后者已经需要挂载 `/etc`。

Example usage in image recipe:

```
IMAGE_FEATURES += "overlayfs-etc"
```

::: note
::: title
Note
:::

This class must not be inherited directly. Use `IMAGE_FEATURES`

> 这个类不能直接继承。请使用 `IMAGE_FEATURES`。
> :::

Your machine configuration should define at least the device, mount point, and file system type you are going to use for `overlayfs`:

```
OVERLAYFS_ETC_MOUNT_POINT = "/data"
OVERLAYFS_ETC_DEVICE = "/dev/mmcblk0p2"
OVERLAYFS_ETC_FSTYPE ?= "ext4"
```

To control more mount options you should consider setting mount options (`defaults` is used by default):

```
OVERLAYFS_ETC_MOUNT_OPTIONS = "wsync"
```

The class provides two options for `/sbin/init` generation:

- The default option is to rename the original `/sbin/init` to `/sbin/init.orig` and place the generated init under original name, i.e. `/sbin/init`. It has an advantage that you won\'t need to change any kernel parameters in order to make it work, but it poses a restriction that package-management can\'t be used, because updating the init manager would remove the generated script.

> 默认选项是将原始的 `/sbin/init` 重命名为 `/sbin/init.orig`，然后将生成的 init 放置在原始名称下，即 `/sbin/init`。它具有一个优势，即您无需更改任何内核参数即可使其工作，但它造成一个限制，即无法使用包管理，因为更新 init 管理器将删除生成的脚本。

- If you wish to keep original init as is, you can set:

  ```
  OVERLAYFS_ETC_USE_ORIG_INIT_NAME = "0"
  ```

  Then the generated init will be named `/sbin/preinit` and you would need to extend your kernel parameters manually in your bootloader configuration.

> 那么生成的初始化程序将被命名为“/sbin/preinit”，您需要在启动加载程序配置中手动扩展内核参数。

# `own-mirrors`

The `ref-classes-own-mirrors` within each recipe.

> 类 `ref-classes-own-mirrors` 中获取它。

To use this class, inherit it globally and specify `SOURCE_MIRROR_URL`. Here is an example:

```
INHERIT += "own-mirrors"
SOURCE_MIRROR_URL = "http://example.com/my-source-mirror"
```

You can specify only a single URL in `SOURCE_MIRROR_URL`.

# `package`

The `ref-classes-package`.

> 类 `ref-classes-package`。

You can control the list of resulting package formats by using the `PACKAGE_CLASSES`. When defining the variable, you can specify one or more package types. Since images are generated from packages, a packaging class is needed to enable image generation. The first class listed in this variable is used for image generation.

> 你可以通过在位于构建目录中的 `conf/local.conf` 配置文件中定义的 `PACKAGE_CLASSES` 变量来控制生成的软件包格式列表。在定义变量时，你可以指定一个或多个软件包类型。由于镜像是从软件包生成的，因此需要打包类来启用镜像生成。该变量中列出的第一个类用于镜像生成。

If you take the optional step to set up a repository (package feed) on the development host that can be used by DNF, you can install packages from the feed while you are running the image on the target (i.e. runtime installation of packages). For more information, see the \"`dev-manual/packages:using runtime package management`\" section in the Yocto Project Development Tasks Manual.

> 如果您采取可选步骤，在开发主机上设置存储库(软件包源)，可以由 DNF 使用，您可以在目标上运行映像时安装软件包(即软件包的运行时安装)。有关更多信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/packages：使用运行时包管理”部分。

The package-specific class you choose can affect build-time performance and has space ramifications. In general, building a package with IPK takes about thirty percent less time as compared to using RPM to build the same or similar package. This comparison takes into account a complete build of the package with all dependencies previously built. The reason for this discrepancy is because the RPM package manager creates and processes more `Metadata`\" if you are building smaller systems.

> 你选择的特定软件包类可能会影响构建时间性能和空间占用。通常，使用 IPK 构建相同或类似的包比使用 RPM 构建相同或类似的包要节省约 30％的时间。此比较考虑了包的完整构建，其中所有依赖项均已构建。由于 RPM 软件包管理器创建和处理的元数据比 IPK 软件包管理器多，因此出现了这种差异。因此，如果您正在构建较小的系统，可以考虑将 PACKAGE_CLASSES 设置为“ref-classes-package_ipk”。

Before making your package manager decision, however, you should consider some further things about using RPM:

- RPM starts to provide more abilities than IPK due to the fact that it processes more Metadata. For example, this information includes individual file types, file checksum generation and evaluation on install, sparse file support, conflict detection and resolution for Multilib systems, ACID style upgrade, and repackaging abilities for rollbacks.

> RPM 由于处理更多的元数据，开始提供比 IPK 更多的能力。例如，这些信息包括个别文件类型、文件校验码的生成和安装评估、稀疏文件支持、多架构系统的冲突检测和解决、ACID 风格的升级以及回滚的重新打包能力。

- For smaller systems, the extra space used for the Berkeley Database and the amount of metadata when using RPM can affect your ability to perform on-device upgrades.

> 对于较小的系统，使用 Berkeley Database 和 RPM 时所用的额外空间以及元数据可能会影响您在设备上执行升级的能力。

You can find additional information on the effects of the package class at these two Yocto Project mailing list links:

- :yocto_lists:[/pipermail/poky/2011-May/006362.html]
- :yocto_lists:[/pipermail/poky/2011-May/006363.html]

# `package_deb`

The `ref-classes-package_deb`` directory.

> 类 `ref-classes-package_deb`` 目录。

This class inherits the `ref-classes-package` variable in the `local.conf` file.

> 这个类继承了 `ref-classes-package` 类，并且可以通过 `local.conf` 文件中的 `PACKAGE_CLASSES` 变量来启用。

# `package_ipk`

The `ref-classes-package_ipk`` directory.

> 这个 ref-classes-package_ipk 类提供支持，用于创建使用 IPK(即.ipk)文件格式的软件包。该类确保将软件包以.ipk 文件格式写入到 $DEPLOY_DIR_IPK 目录中。

This class inherits the `ref-classes-package` variable in the `local.conf` file.

> 这个类继承了 `ref-classes-package` 类，并通过 `local.conf` 文件中的 `PACKAGE_CLASSES` 变量启用。

# `package_rpm`

The `ref-classes-package_rpm`` directory.

> 这个 ref-classes-package_rpm 类提供了创建使用 RPM(即.rpm)文件格式的软件包的支持。该类确保将软件包以.rpm 文件格式写入到 DEPLOY_DIR_RPM 目录中。

This class inherits the `ref-classes-package` variable in the `local.conf` file.

> 这个类继承了 `ref-classes-package` 类，并通过 `local.conf` 文件中的 `PACKAGE_CLASSES` 变量启用。

# `packagedata`

The `ref-classes-packagedata`. These files contain information about each output package produced by the OpenEmbedded build system.

> 类 `ref-classes-packagedata` 提供了读取 `PKGDATA_DIR` 中发现的 `pkgdata` 文件的常见功能。这些文件包含了 OpenEmbedded 构建系统生成的每个输出包的信息。

This class is enabled by default because it is inherited by the `ref-classes-package` class.

# `packagegroup`

The `ref-classes-packagegroup`, and so forth). It is highly recommended that all package group recipes inherit this class.

> `ref-classes-packagegroup` 类设置了适用于软件包组配方(例如 `PACKAGES`、`PACKAGE_ARCH`、`ALLOW_EMPTY` 等)的默认值。强烈建议所有软件包组配方继承此类。

For information on how to use this class, see the \"`dev-manual/customizing-images:customizing images using custom package groups`\" section in the Yocto Project Development Tasks Manual.

> 查看有关如何使用此类的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual / customizing-images：使用自定义软件包组自定义镜像”部分。

Previously, this class was called the `task` class.

# `patch`

The `ref-classes-patch` task.

> 该 `ref-classes-patch` 任务期间应用补丁的所有功能。

This class is enabled by default because it is inherited by the `ref-classes-base` class.

# `perlnative`

When inherited by a recipe, the `ref-classes-perlnative` class supports using the native version of Perl built by the build system rather than using the version provided by the build host.

> 当继承一个配方时，`ref-classes-perlnative` 类支持使用构建系统构建的 Perl 本机版本，而不是使用构建主机提供的版本。

# `pypi`

The `ref-classes-pypi`.

> 类 `ref-classes-pypi` 来设置它。

Variables set by the `ref-classes-pypi`.

> 参考类 `ref-classes-pypi` 设置的变量包括 `SRC_URI`、`SECTION`、`HOMEPAGE`、`UPSTREAM_CHECK_URI`、`UPSTREAM_CHECK_REGEX` 和 `CVE_PRODUCT`。

# `python_flit_core`

The `ref-classes-python_flit_core` class enables building Python modules which declare the [PEP-517](https://www.python.org/dev/peps/pep-0517/) compliant `flit_core.buildapi` `build-backend` in the `[build-system]` section of `pyproject.toml` (See [PEP-518](https://www.python.org/dev/peps/pep-0518/)).

> 类 `ref-classes-python_flit_core` 可以构建符合 [PEP-517](https://www.python.org/dev/peps/pep-0517/) 规范的 Python 模块，其中在 `pyproject.toml` 的 `[build-system]` 部分声明 `flit_core.buildapi` `build-backend`(参见 [PEP-518](https://www.python.org/dev/peps/pep-0518/))。

Python modules built with `flit_core.buildapi` are pure Python (no `C` or `Rust` extensions).

Internally this uses the `ref-classes-python_pep517` class.

# `python_pep517`

The `ref-classes-python_pep517` class builds and installs a Python `wheel` binary archive (see [PEP-517](https://peps.python.org/pep-0517/)).

> 类 `ref-classes-python_pep517` 构建和安装 Python `wheel` 二进制存档(参见 [PEP-517](https://peps.python.org/pep-0517/))。

Recipes wouldn\'t inherit this directly, instead typically another class will inherit this and add the relevant native dependencies.

Examples of classes which do this are `ref-classes-python_flit_core`.

> 例子包括 python_flit_core。

# `python_poetry_core`

The `ref-classes-python_poetry_core` class enables building Python modules which use the [Poetry Core](https://python-poetry.org) build system.

> 这个 `ref-classes-python_poetry_core` 类可以构建使用 [Poetry Core](https://python-poetry.org) 构建系统的 Python 模块。

Internally this uses the `ref-classes-python_pep517` class.

# `python_pyo3`

The `ref-classes-python_pyo3` class helps make sure that Python extensions written in Rust and built with [PyO3](https://pyo3.rs/), properly set up the environment for cross compilation.

> 类 `ref-classes-python_pyo3` 可以帮助确保使用 [PyO3](https://pyo3.rs/) 编写的 Rust 扩展正确设置用于跨编译的环境。

This class is internal to the `ref-classes-python-setuptools3_rust` class and is not meant to be used directly in recipes.

> 这个类是内部类，不适合直接在配方中使用 ref-classes-python-setuptools3_rust 类。

# `python-setuptools3_rust`

The `ref-classes-python-setuptools3_rust` class enables building Python extensions implemented in Rust with [PyO3](https://pyo3.rs/), which allows to compile and distribute Python extensions written in Rust as easily as if they were written in C.

> 这个 `ref-classes-python-setuptools3_rust` 类可以使用 [PyO3](https://pyo3.rs/) 来构建用 Rust 实现的 Python 扩展，这使得用 Rust 编写的 Python 扩展的编译和发布就像用 C 编写的一样容易。

This class inherits the `ref-classes-setuptools3` classes.

# `pixbufcache`

The `ref-classes-pixbufcache` class generates the proper post-install and post-remove (postinst/postrm) scriptlets for packages that install pixbuf loaders, which are used with `gdk-pixbuf`. These scriptlets call `update_pixbuf_cache` to add the pixbuf loaders to the cache. Since the cache files are architecture-specific, `update_pixbuf_cache` is run using QEMU if the postinst scriptlets need to be run on the build host during image creation.

> ref-classes-pixbufcache 类为安装 pixbuf 加载程序的软件包生成适当的安装后和卸载后(postinst / postrm)脚本。这些脚本调用 update_pixbuf_cache 将 pixbuf 加载程序添加到缓存中。由于缓存文件是特定于架构的，因此如果在创建映像期间需要在构建主机上运行 postinst 脚本，则使用 QEMU 运行 update_pixbuf_cache。

If the pixbuf loaders being installed are in packages other than the recipe\'s main package, set `PIXBUF_PACKAGES` to specify the packages containing the loaders.

> 如果安装的 pixbuf 加载程序不在 recipes 的主要软件包中，请将 `PIXBUF_PACKAGES` 设置为指定包含加载程序的软件包。

# `pkgconfig`

The `ref-classes-pkgconfig` class provides a standard way to get header and library information by using `pkg-config`. This class aims to smooth integration of `pkg-config` into libraries that use it.

> 这个 ref-classes-pkgconfig 类提供了一种通过使用 pkg-config 获取头文件和库信息的标准方法。这个类旨在简化将 pkg-config 集成到使用它的库中的过程。

During staging, BitBake installs `pkg-config` data into the `sysroots/` directory. By making use of sysroot functionality within `pkg-config`, the `ref-classes-pkgconfig` class no longer has to manipulate the files.

> 在搭建期间，BitBake 将 `pkg-config` 数据安装到 `sysroots/` 目录中。通过利用 `pkg-config` 中的 sysroot 功能，`ref-classes-pkgconfig` 类不再需要操作文件。

# `populate_sdk`

The `ref-classes-populate-sdk`\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 类 `ref-classes-populate-sdk`"部分。

# `populate_sdk_*`

The `ref-classes-populate-sdk-*` classes support SDK creation and consist of the following classes:

- `populate_sdk_base <ref-classes-populate-sdk-*>`: The base class supporting SDK creation under all package managers (i.e. DEB, RPM, and opkg).

> 填充 SDK 基类 <ref-classes-populate-sdk-*>：支持在所有包管理器(如 DEB、RPM 和 opkg)下创建 SDK 的基类。

- `populate_sdk_deb <ref-classes-populate-sdk-*>`: Supports creation of the SDK given the Debian package manager.
- `populate_sdk_rpm <ref-classes-populate-sdk-*>`: Supports creation of the SDK given the RPM package manager.
- `populate_sdk_ipk <ref-classes-populate-sdk-*>`: Supports creation of the SDK given the opkg (IPK format) package manager.

> 支持使用 opkg(IPK 格式)软件包管理器创建 SDK：populate_sdk_ipk <ref-classes-populate-sdk-*>。

- `populate_sdk_ext <ref-classes-populate-sdk-*>`: Supports extensible SDK creation under all package managers.

The `populate_sdk_base <ref-classes-populate-sdk-*>`.

> `populate_sdk_base<ref-classes-populate-sdk-*>` 适当的 `populate_sdk_*`(即 `deb`、`rpm` 和 `ipk`)。

The base class ensures all source and destination directories are established and then populates the SDK. After populating the SDK, the `populate_sdk_base <ref-classes-populate-sdk-*>`, which consists of the following:

> 基类确保所有源和目标目录都已建立，然后填充 SDK。在填充 SDK 之后，`populate_sdk_base <ref-classes-populate-sdk-*>` 中，其中包括：

```
$-nativesdk-pkgs
$/target-pkgs
```

Finally, the base populate SDK class creates the toolchain environment setup script, the tarball of the SDK, and the installer.

The respective `populate_sdk_deb <ref-classes-populate-sdk-*>` class.

> 分别的 `populate_sdk_deb <ref-classes-populate-sdk-*>` 类别继承和使用。

For more information on the cross-development toolchain generation, see the \"`overview-manual/concepts:cross-development toolchain generation`\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 要了解更多有关跨开发工具链生成的信息，请参阅 Yocto 项目概述和概念手册中的“概述手册/概念：跨开发工具链生成”部分。要了解使用'ref-tasks-populate_sdk'任务构建跨开发工具链时所获得的优势，请参阅 Yocto 项目应用开发和可扩展软件开发工具包(eSDK)手册中的“SDK 手册/附录：构建 SDK 安装程序”部分。

# `prexport`

The `ref-classes-prexport` values.

::: note
::: title
Note
:::

This class is not intended to be used directly. Rather, it is enabled when using \"`bitbake-prserv-tool export`\".
:::

# `primport`

The `ref-classes-primport` values.

::: note
::: title
Note
:::

This class is not intended to be used directly. Rather, it is enabled when using \"`bitbake-prserv-tool import`\".
:::

# `prserv`

The `ref-classes-prserv` variable for each recipe.

> 类 `ref-classes-prserv` 提供了使用 PR 服务来自动管理每个 recipes 的 PR 变量增量的功能。

This class is enabled by default because it is inherited by the `ref-classes-package` has been set.

> 这个类默认是启用的，因为它是继承自 `ref-classes-package` 类的。但是，OpenEmbedded 构建系统不会启用这个类的功能，除非 `PRSERV_HOST` 已经设置。

# `ptest`

The `ref-classes-ptest` class provides functionality for packaging and installing runtime tests for recipes that build software that provides these tests.

> 类 `ref-classes-ptest` 提供了用于为构建提供运行时测试的软件打包和安装运行时测试的功能。

This class is intended to be inherited by individual recipes. However, the class\' functionality is largely disabled unless \"ptest\" appears in `DISTRO_FEATURES`\" section in the Yocto Project Development Tasks Manual for more information on ptest.

> 这个类旨在被单个菜谱继承。但是，除非在 DISTRO_FEATURES 中出现“ptest”，否则该类的功能将被大部分禁用。有关 ptest 的更多信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/packages：使用 ptest 测试包”部分。

# `ptest-gnome`

Enables package tests (ptests) specifically for GNOME packages, which have tests intended to be executed with `gnome-desktop-testing`.

For information on setting up and running ptests, see the \"`dev-manual/packages:testing packages with ptest`\" section in the Yocto Project Development Tasks Manual.

> 要了解有关设置和运行 ptest 的信息，请参阅 Yocto Project 开发任务手册中的“dev-manual/packages：使用 ptest 测试软件包”部分。

# `python3-dir`

The `ref-classes-python3-dir` class provides the base version, location, and site package location for Python 3.

# `python3native`

The `ref-classes-python3native` class supports using the native version of Python 3 built by the build system rather than support of the version provided by the build host.

> 类 `ref-classes-python3native` 支持使用构建系统构建的 Python 3 的原生版本，而不是构建主机提供的版本。

# `python3targetconfig`

The `ref-classes-python3targetconfig` class supports using the native version of Python 3 built by the build system rather than support of the version provided by the build host, except that the configuration for the target machine is accessible (such as correct installation directories). This also adds a dependency on target `python3`, so should only be used where appropriate in order to avoid unnecessarily lengthening builds.

> 这个 ref-classes-python3targetconfig 类支持使用构建系统构建的本机 Python 3 版本，而不是构建主机提供的版本，除了可以访问目标机器的配置(例如正确的安装目录)。这也增加了对目标 python3 的依赖，因此只应在适当的情况下使用，以避免不必要地延长构建时间。

# `qemu`

The `ref-classes-qemu` class provides functionality for recipes that either need QEMU or test for the existence of QEMU. Typically, this class is used to run programs for a target system on the build host using QEMU\'s application emulation mode.

> 这个 ref-classes-qemu 类为需要 QEMU 或测试 QEMU 存在性的 recipes 提供功能。通常，这个类用于使用 QEMU 的应用程序仿真模式在构建主机上运行目标系统的程序。

# `recipe_sanity`

The `ref-classes-recipe_sanity` class checks for the presence of any host system recipe prerequisites that might affect the build (e.g. variables that are set or software that is present).

> 这个 `ref-classes-recipe_sanity` 类检查可能会影响构建的主机系统配方先决条件(例如设置的变量或存在的软件)的存在性。

# `relocatable`

The `ref-classes-relocatable` class enables relocation of binaries when they are installed into the sysroot.

This class makes use of the `ref-classes-chrpath` classes.

> 这个类使用 `ref-classes-chrpath` 类使用。

# `remove-libtool`

The `ref-classes-remove-libtool` task to remove all `.la` files installed by `libtool`. Removing these files results in them being absent from both the sysroot and target packages.

> 这个 ref-classes-remove-libtool 类添加了一个后置函数到 ref-tasks-install 任务，以便删除 libtool 安装的所有.la 文件。删除这些文件会导致它们不存在于 sysroot 和目标软件包中。

If a recipe needs the `.la` files to be installed, then the recipe can override the removal by setting `REMOVE_LIBTOOL_LA` to \"0\" as follows:

```
REMOVE_LIBTOOL_LA = "0"
```

::: note
::: title
Note
:::

The `ref-classes-remove-libtool` class is not enabled by default.
:::

# `report-error`

The `ref-classes-report-error`\", which allows you to submit build error information to a central database.

> 类 `ref-classes-report-error`，它允许您将构建错误信息提交到中央数据库。

The class collects debug information for recipe, recipe version, task, machine, distro, build system, target system, host distro, branch, commit, and log. From the information, report files using a JSON format are created and stored in `$/error-report`.

> 班級收集食譜，食譜版本，任務，機器，发行版，構建系統，目標系統，主機发行版，分支，提交和日誌的除錯信息。根據這些信息，使用 JSON 格式創建並儲存在 `$/error-report` 中的報告文件。

# `rm_work`

The `ref-classes-rm-work` class supports deletion of temporary workspace, which can ease your hard drive demands during builds.

> 类 `ref-classes-rm-work` 支持删除临时工作区，这可以在构建期间缓解您的硬盘需求。

The OpenEmbedded build system can use a substantial amount of disk space during the build process. A portion of this space is the work files under the `$:

> 开放式嵌入式构建系统在构建过程中可以使用大量的磁盘空间。其中一部分空间是每个配方下 `$：

```
INHERIT += "rm_work"
```

If you are modifying and building source code out of the work directory for a recipe, enabling `ref-classes-rm-work` variable, which can also be set in your `local.conf` file. Here is an example:

> 如果您正在修改和构建工作目录中的源代码以供 recipes 使用，启用 `ref-classes-rm-work` 变量中，该变量也可以在您的 `local.conf` 文件中设置。下面是一个例子：

```
RM_WORK_EXCLUDE += "busybox glibc"
```

# `rootfs*`

The `ref-classes-rootfs*` classes support creating the root filesystem for an image and consist of the following classes:

> 这些 `ref-classes-rootfs*` 类支持创建镜像的根文件系统，包括以下类：

- The `rootfs-postcommands <ref-classes-rootfs*>` class, which defines filesystem post-processing functions for image recipes.

> `- 用于镜像配方的文件系统后处理函数定义的` rootfs-postcommands <ref-classes-rootfs*>` 类。

- The `rootfs_deb <ref-classes-rootfs*>` class, which supports creation of root filesystems for images built using `.deb` packages.

> 这个 rootfs_deb<ref-classes-rootfs*> 类支持使用 `.deb` 包构建的映像创建根文件系统。

- The `rootfs_rpm <ref-classes-rootfs*>` class, which supports creation of root filesystems for images built using `.rpm` packages.

> 这个 `rootfs_rpm <ref-classes-rootfs*>` 类支持使用 `.rpm` 软件包构建的映像文件创建根文件系统。

- The `rootfs_ipk <ref-classes-rootfs*>` class, which supports creation of root filesystems for images built using `.ipk` packages.

> - `rootfs_ipk <ref-classes-rootfs*>` 类支持使用 `.ipk` 包构建的映像文件系统的创建。

- The `rootfsdebugfiles <ref-classes-rootfs*>` class, which installs additional files found on the build host directly into the root filesystem.

> 这个 rootfsdebugfiles <ref-classes-rootfs*> 类，它会把构建主机上发现的额外文件安装到根文件系统中。

The root filesystem is created from packages using one of the `ref-classes-rootfs*` variable.

> 根文件系统是使用 `ref-classes-rootfs*` 变量确定)创建的包。

For information on how root filesystem images are created, see the \"`overview-manual/concepts:image generation`\" section in the Yocto Project Overview and Concepts Manual.

> 对于如何创建根文件系统映像的信息，请参阅 Yocto 项目概述和概念手册中的“overview-manual / concepts：image generation”部分。

# `rust`

The `ref-classes-rust` class is an internal class which is just used in the \"rust\" recipe, to build the Rust compiler and runtime library. Except for this recipe, it is not intended to be used directly.

> 类 `ref-classes-rust` 仅在“rust”配方中使用，用于构建 Rust 编译器和运行时库，是一个内部类。除了这个配方，不打算直接使用它。

# `rust-common`

The `ref-classes-rust-common` classes and is not intended to be used directly.

> `ref-classes-rust-common` 类的内部类，不应直接使用。

# `sanity`

The `ref-classes-sanity` class checks to see if prerequisite software is present on the host system so that users can be notified of potential problems that might affect their build. The class also performs basic user configuration checks from the `local.conf` configuration file to prevent common mistakes that cause build failures. Distribution policy usually determines whether to include this class.

> `ref-classes-sanity` 类检查主机系统上是否存在先决软件，以便通知用户可能影响其构建的潜在问题。该类还从 `local.conf` 配置文件执行基本的用户配置检查，以防止导致构建失败的常见错误。通常由分发策略决定是否包含此类。

# `scons`

The `ref-classes-scons` variable to specify additional configuration options you want to pass SCons command line.

> 类 `ref-classes-scons` 支持需要使用 SCons 构建系统构建软件的配方。您可以使用变量 `EXTRA_OESCONS` 指定要传递给 SCons 命令行的其他配置选项。

# `sdl`

The `ref-classes-sdl` class supports recipes that need to build software that uses the Simple DirectMedia Layer (SDL) library.

> 这个 ref-classes-sdl 类支持需要使用 Simple DirectMedia Layer(SDL)库构建软件的 recipes。

# `python_setuptools_build_meta`

The `ref-classes-python_setuptools_build_meta` class enables building Python modules which declare the [PEP-517](https://www.python.org/dev/peps/pep-0517/) compliant `setuptools.build_meta` `build-backend` in the `[build-system]` section of `pyproject.toml` (See [PEP-518](https://www.python.org/dev/peps/pep-0518/)).

> 类 ref-classes-python_setuptools_build_meta 可以构建声明 [PEP-517](https://www.python.org/dev/peps/pep-0517/) 兼容的 setuptools.build_meta build-backend 的 Python 模块，该模块位于 pyproject.toml 的[build-system]部分(参见 [PEP-518](https://www.python.org/dev/peps/pep-0518/))。

Python modules built with `setuptools.build_meta` can be pure Python or include `C` or `Rust` extensions).

Internally this uses the `ref-classes-python_pep517` class.

# `setuptools3`

The `ref-classes-setuptools3` class.

> ref-classes-setuptools3 类支持使用基于 setuptools(例如只有 setup.py，没有迁移到官方 pyproject.toml 格式)的 Python 3.x 扩展。如果您的配方使用这些构建系统，则该配方需要继承 ref-classes-setuptools3 类。

> ::: note
> ::: title
> Note
> :::
>
> The `ref-classes-setuptools3` task now calls `setup.py bdist_wheel` to build the `wheel` binary archive format (See [PEP-427](https://www.python.org/dev/peps/pep-0427/)).
>
> A consequence of this is that legacy software still using deprecated `distutils` from the Python standard library cannot be packaged as `wheels`. A common solution is the replace `from distutils.core import setup` with `from setuptools import setup`.
> :::
>
> ::: note
> ::: title
> Note
> :::
>
> The `ref-classes-setuptools3` should be used.
> :::

# `setuptools3_legacy`

The `ref-classes-setuptools3_legacy`, this uses the traditional `setup.py` `build` and `install` commands and not wheels. This use of `setuptools` like this is [deprecated](https://github.com/pypa/setuptools/blob/main/CHANGES.rst#v5830) but still relatively common.

> 类 `ref-classes-setuptools3_legacy` 不同，这使用传统的 `setup.py``build` 和 `install` 命令，而不是轮子。这种使用 `setuptools` 的方式已经[弃用](https://github.com/pypa/setuptools/blob/main/CHANGES.rst#v5830)，但仍然相对普遍。

# `setuptools3-base`

The `ref-classes-setuptools3-base` class and inherit this class instead.

> 类 `ref-classes-setuptools3-base` 类中的任务，而是继承这个类。

# `sign_rpm`

The `ref-classes-sign_rpm` class supports generating signed RPM packages.

# `siteconfig`

The `ref-classes-siteconfig` task.

> 类 `ref-classes-siteconfig`。

# `siteinfo`

The `ref-classes-siteinfo` class provides information about the targets that might be needed by other classes or recipes.

> 这个 ref-classes-siteinfo 类提供有关其他类或配方可能需要的目标的信息。

As an example, consider Autotools, which can require tests that must execute on the target hardware. Since this is not possible in general when cross compiling, site information is used to provide cached test results so these tests can be skipped over but still make the correct values available. The `meta/site directory` contains test results sorted into different categories such as architecture, endianness, and the `libc` used. Site information provides a list of files containing data relevant to the current build in the `CONFIG_SITE` variable that Autotools automatically picks up.

> 举个例子，考虑自动工具，它可以要求必须在目标硬件上执行的测试。由于这在跨编译时通常是不可能的，因此使用站点信息以提供缓存的测试结果，以便可以跳过这些测试，但仍然可以获得正确的值。`meta/site directory` 包含按不同类别排列的测试结果，例如架构、字节序和使用的 `libc`。站点信息提供一个文件列表，其中包含与当前构建相关的数据，该文件列表存储在自动工具自动检索的 `CONFIG_SITE` 变量中。

The class also provides variables like `SITEINFO_ENDIANNESS` that can be used elsewhere in the metadata.

> 这个类还提供可用于元数据中的其他地方的变量，如 `SITEINFO_ENDIANNESS`。

# `sstate`

The `ref-classes-sstate` variable\'s default value.

> 类 `ref-classes-sstate` 提供了共享状态(sstate)的支持。默认情况下，通过 `INHERIT_DISTRO` 变量的默认值启用该类。

For more information on sstate, see the \"`overview-manual/concepts:shared state cache`\" section in the Yocto Project Overview and Concepts Manual.

> 要了解有关 sstate 的更多信息，请参阅 Yocto 项目概览与概念手册中的“共享状态缓存”部分。

# `staging`

The `ref-classes-staging` class installs files into individual recipe work directories for sysroots. The class contains the following key tasks:

> ref-classes-staging 类将文件安装到 sysroots 的各个配方工作目录中。该类包含以下关键任务：

- The `ref-tasks-populate_sysroot` task, which is responsible for handing the files that end up in the recipe sysroots.

> 任务 `ref-tasks-populate_sysroot` 负责处理最终会进入配方系统根目录的文件。

- The `ref-tasks-prepare_recipe_sysroot`).

> 任务'ref-tasks-prepare_recipe_sysroot'(与任务'populate_sysroot'相关的"伙伴"任务)将文件安装到各个 recipes 工作目录(即'WORKDIR')中。

The code in the `ref-classes-staging` class is complex and basically works in two stages:

- *Stage One:* The first stage addresses recipes that have files they want to share with other recipes that have dependencies on the originating recipe. Normally these dependencies are installed through the `ref-tasks-install` variables.

> *第一阶段：* 第一阶段处理想要与其他有依赖于源菜谱的菜谱共享文件的菜谱。通常，这些依赖项通过 `ref-tasks-install` 变量控制。

::: note
::: title
Note
:::

Additionally, a recipe can customize the files further by declaring a processing function in the `SYSROOT_PREPROCESS_FUNCS` variable.

> 此外，通过在 `SYSROOT_PREPROCESS_FUNCS` 变量中声明处理函数，配方可以进一步定制文件。
> :::

A shared state (sstate) object is built from these files and the files are placed into a subdirectory of `structure-build-tmp-sysroots-components` variable.

> 一个共享状态(sstate)对象是由这些文件构建的，并且这些文件被放置在'structure-build-tmp-sysroots-components'的子目录中。文件会被扫描以查找指向原始安装位置的固定路径。如果在文本文件中发现了位置，那么这些固定的位置将被标记替换，并且会创建一个需要这样替换的文件列表。这些调整被称为“FIXMEs”。被用来扫描路径的文件列表由'SSTATE_SCAN_FILES'变量控制。

- *Stage Two:* The second stage addresses recipes that want to use something from another recipe and declare a dependency on that recipe through the `DEPENDS`). The OpenEmbedded build system creates hard links to copies of the relevant files from `sysroots-components` into the recipe work directory.

> *第二阶段：*第二阶段处理想要使用另一个配方中的东西并通过 `DEPENDS`)中创建 `recipe-sysroot` 和 `recipe-sysroot-native`。OpenEmbedded 构建系统将相关文件的硬链接副本从 `sysroots-components` 复制到配方工作目录中。

::: note
::: title
Note
:::

If hard links are not possible, the build system uses actual copies.
:::

The build system then addresses any \"FIXMEs\" to paths as defined from the list created in the first stage.

Finally, any files in `$` within the sysroot that have the prefix \"`postinst-`\" are executed.

::: note
::: title
Note
:::

Although such sysroot post installation scripts are not recommended for general use, the files do allow some issues such as user creation and module indexes to be addressed.

> 尽管不推荐一般用户使用这种 sysroot 安装后脚本，但是这些文件确实可以解决一些问题，比如用户创建和模块索引。
> :::

Because recipes can have other dependencies outside of `DEPENDS` but operate similarly.

> 由于 recipes 可能有 DEPENDS 之外的其他依赖(例如：do_unpack[depends] += "tar-native:do_populate_sysroot")，因此也将 sysroot 创建函数 extend_recipe_sysroot 添加为那些依赖不是通过 DEPENDS 但类似操作的任务的预功能。

When installing dependencies into the sysroot, the code traverses the dependency graph and processes dependencies in exactly the same way as the dependencies would or would not be when installed from sstate. This processing means, for example, a native tool would have its native dependencies added but a target library would not have its dependencies traversed or installed. The same sstate dependency code is used so that builds should be identical regardless of whether sstate was used or not. For a closer look, see the `setscene_depvalid()` function in the `ref-classes-sstate` class.

> 当安装依赖项到 sysroot 时，代码会遍历依赖关系图，并以与从 sstate 安装时完全相同的方式处理依赖项。这种处理意味着，例如，原生工具会添加其原生依赖项，但目标库不会遍历或安装其依赖项。使用相同的 sstate 依赖代码，因此无论是否使用 sstate，构建结果都应该相同。要进一步了解，请参阅 `ref-classes-sstate` 类中的 `setscene_depvalid()` 函数。

The build system is careful to maintain manifests of the files it installs so that any given dependency can be installed as needed. The sstate hash of the installed item is also stored so that if it changes, the build system can reinstall it.

> 系统构建时会仔细维护安装文件的清单，以便根据需要安装任何给定的依赖项。还会存储安装项的 sstate 哈希，以便如果它发生更改，构建系统可以重新安装它。

# `syslinux`

The `ref-classes-syslinux` class provides syslinux-specific functions for building bootable images.

The class supports the following variables:

- `INITRD`: Indicates list of filesystem images to concatenate and use as an initial RAM disk (initrd). This variable is optional.

> INITRD：指示要连接并用作初始 RAM 盘(initrd)的文件系统镜像列表。此变量是可选的。

- `ROOTFS`: Indicates a filesystem image to include as the root filesystem. This variable is optional.
- `AUTO_SYSLINUXMENU`: Enables creating an automatic menu when set to \"1\".
- `LABELS`: Lists targets for automatic configuration.
- `APPEND`: Lists append string overrides for each label.
- `SYSLINUX_OPTS`: Lists additional options to add to the syslinux file. Semicolon characters separate multiple options.

> `- SYSLINUX_OPTS`：列出要添加到 syslinux 文件的其他选项。分号字符分隔多个选项。

- `SYSLINUX_SPLASH`: Lists a background for the VGA boot menu when you are using the boot menu.
- `SYSLINUX_DEFAULT_CONSOLE`: Set to \"console=ttyX\" to change kernel boot default console.
- `SYSLINUX_SERIAL`: Sets an alternate serial port. Or, turns off serial when the variable is set with an empty string.

> `SYSLINUX_SERIAL`：设置一个替代的串行端口，或者当变量被设置为空字符串时关闭串行。

- `SYSLINUX_SERIAL_TTY`: Sets an alternate \"console=tty\...\" kernel boot argument.

# `systemd`

The `ref-classes-systemd` class provides support for recipes that install systemd unit files.

The functionality for this class is disabled unless you have \"systemd\" in `DISTRO_FEATURES`.

Under this class, the recipe or Makefile (i.e. whatever the recipe is calling during the `ref-tasks-install` in your recipe to identify the packages in which the files will be installed.

> 在这个类中，recipes 或 Makefile(即 `ref-tasks-install` 以标识将文件安装的软件包。

You should set `SYSTEMD_SERVICE``. Here is an example from the connman recipe:

> 你应该将 `SYSTEMD_SERVICE` 设置为服务文件的名称。你也应该使用包名覆盖来指示该值适用于哪个包。如果该值适用于配方的主要包，请使用 `$`。这里是 connman 配方的一个例子：

```
SYSTEMD_SERVICE:$ = "connman.service"
```

Services are set up to start on boot automatically unless you have set `SYSTEMD_AUTO_ENABLE` to \"disable\".

For more information on `ref-classes-systemd`\" section in the Yocto Project Development Tasks Manual.

> 对于 `ref-classes-systemd`”部分。

# `systemd-boot`

The `ref-classes-systemd-boot` class provides functions specific to the systemd-boot bootloader for building bootable images. This is an internal class and is not intended to be used directly.

> 这个 `ref-classes-systemd-boot` 类提供了特定于 systemd-boot 启动程序的功能，用于构建可引导映像。这是一个内部类，不建议直接使用。

::: note
::: title
Note
:::

The `ref-classes-systemd-boot` class is a result from merging the `gummiboot` class used in previous Yocto Project releases with the `systemd` project.

> 这个 `ref-classes-systemd-boot` 类是在之前的 Yocto Project 发布中使用的 `gummiboot` 类与 `systemd` 项目合并的结果。
> :::

Set the `EFI_PROVIDER`\" to use this class. Doing so creates a standalone EFI bootloader that is not dependent on systemd.

> 设置 `EFI_PROVIDER` 变量为"ref-classes-systemd-boot"以使用此类。这样做会创建一个独立的 EFI 引导程序，不依赖于 systemd。

For information on more variables used and supported in this class, see the `SYSTEMD_BOOT_CFG` variables.

> 要了解本类中使用和支持的更多变量，请参阅 `SYSTEMD_BOOT_CFG` 变量。

You can also see the [Systemd-boot documentation](https://www.freedesktop.org/wiki/Software/systemd/systemd-boot/) for more information.

# `terminal`

The `ref-classes-terminal` variable controls which terminal emulator is used for the session.

> `ref-classes-terminal` 变量控制用于会话的终端模拟器。

Other classes use the `ref-classes-terminal` class.

> 其他类可以在需要启动单独的终端会话的任何地方使用“ref-classes-terminal”类。例如，假设“PATCHRESOLVE”被设置为“user”，“ref-classes-cml1”类以及“ref-classes-devshell”类都使用“ref-classes-terminal”类。

# `testimage`

The `ref-classes-testimage` class supports running automated tests against images using QEMU and on actual hardware. The classes handle loading the tests and starting the image. To use the classes, you need to perform steps to set up the environment.

> 类 `ref-classes-testimage` 支持使用 QEMU 和实际硬件对镜像执行自动化测试。该类处理加载测试和启动镜像。要使用这些类，您需要执行步骤来设置环境。

To enable this class, add the following to your configuration:

```
IMAGE_CLASSES += "testimage"
```

The tests are commands that run on the target system over `ssh`. Each test is written in Python and makes use of the `unittest` module.

The `ref-classes-testimage` class runs tests on an image when called using the following:

```
$ bitbake -c testimage image
```

Alternatively, if you wish to have tests automatically run for each image after it is built, you can set `TESTIMAGE_AUTO`:

> 如果您希望每次构建镜像后自动运行测试，您可以设置 `TESTIMAGE_AUTO`：

```
TESTIMAGE_AUTO = "1"
```

For information on how to enable, run, and create new tests, see the \"`dev-manual/runtime-testing:performing automated runtime testing`\" section in the Yocto Project Development Tasks Manual.

> 要了解如何启用、运行和创建新测试，请参阅 Yocto Project 开发任务手册中的“dev-manual/runtime-testing：执行自动运行时测试”部分。

# `testsdk`

This class supports running automated tests against software development kits (SDKs). The `ref-classes-testsdk` class runs tests on an SDK when called using the following:

> 这个类支持对软件开发工具包(SDKs)进行自动测试。`ref-classes-testsdk` 类可以通过以下方式调用来运行对 SDK 的测试：

```
$ bitbake -c testsdk image
```

::: note
::: title
Note
:::

Best practices include using `IMAGE_CLASSES` class for automated SDK testing.

> 最佳实践包括使用 `IMAGE_CLASSES` 类以进行自动 SDK 测试。
> :::

# `texinfo`

This class should be inherited by recipes whose upstream packages invoke the `texinfo` utilities at build-time. Native and cross recipes are made to use the dummy scripts provided by `texinfo-dummy-native`, for improved performance. Target architecture recipes use the genuine Texinfo utilities. By default, they use the Texinfo utilities on the host system.

> 这个类应该被源自的软件包在构建时调用 `texinfo` 工具的配方所继承。本地和跨平台的配方使用 `texinfo-dummy-native` 提供的虚拟脚本，以提高性能。目标架构配方使用真正的 Texinfo 工具。默认情况下，它们在主机系统上使用 Texinfo 工具。

::: note
::: title
Note
:::

If you want to use the Texinfo recipe shipped with the build system, you can remove \"texinfo-native\" from `ASSUME_PROVIDED`.

> 如果你想使用构建系统提供的 Texinfo recipes，你可以从 `ASSUME_PROVIDED` 中移除。
> :::

# `toaster`

The `ref-classes-toaster` class collects information about packages and images and sends them as events that the BitBake user interface can receive. The class is enabled when the Toaster user interface is running.

> `ref-classes-toaster` 类收集有关包和镜像的信息，并将其作为事件发送，BitBake 用户界面可以接收。当 Toaster 用户界面运行时，该类启用。

This class is not intended to be used directly.

# `toolchain-scripts`

The `ref-classes-toolchain-scripts` class provides the scripts used for setting up the environment for installed SDKs.

# `typecheck`

The `ref-classes-typecheck` class provides support for validating the values of variables set at the configuration level against their defined types. The OpenEmbedded build system allows you to define the type of a variable using the \"type\" varflag. Here is an example:

> `ref-classes-typecheck` 类提供了支持，用于验证配置级别设置的变量的值与其定义的类型是否匹配。OpenEmbedded 构建系统允许您使用“type”varflag 来定义变量的类型。以下是一个例子：

```
IMAGE_FEATURES[type] = "list"
```

# `uboot-config`

The `ref-classes-uboot-config` class provides support for U-Boot configuration for a machine. Specify the machine in your recipe as follows:

> `ref-classes-uboot-config` 类提供对机器的 U-Boot 配置的支持。在你的配方中按照以下方式指定机器：

```
UBOOT_CONFIG ??= <default>
UBOOT_CONFIG[foo] = "config,images"
```

You can also specify the machine using this method:

```
UBOOT_MACHINE = "config"
```

See the `UBOOT_CONFIG` variables for additional information.

# `uboot-sign`

The `ref-classes-uboot-sign` class provides support for U-Boot verified boot. It is intended to be inherited from U-Boot recipes.

> 这个 ref-classes-uboot-sign 类提供了 U-Boot 验证启动的支持。它旨在从 U-Boot recipes 继承。

Here are variables used by this class:

- `SPL_MKIMAGE_DTCOPTS`: DTC options for U-Boot `mkimage` when building the FIT image.
- `SPL_SIGN_ENABLE`: enable signing the FIT image.
- `SPL_SIGN_KEYDIR`: directory containing the signing keys.
- `SPL_SIGN_KEYNAME`: base filename of the signing keys.
- `UBOOT_FIT_ADDRESS_CELLS`: `#address-cells` value for the FIT image.
- `UBOOT_FIT_DESC`: description string encoded into the FIT image.
- `UBOOT_FIT_GENERATE_KEYS`: generate the keys if they don\'t exist yet.
- `UBOOT_FIT_HASH_ALG`: hash algorithm for the FIT image.
- `UBOOT_FIT_KEY_GENRSA_ARGS`: `openssl genrsa` arguments.
- `UBOOT_FIT_KEY_REQ_ARGS`: `openssl req` arguments.
- `UBOOT_FIT_SIGN_ALG`: signature algorithm for the FIT image.
- `UBOOT_FIT_SIGN_NUMBITS`: size of the private key for FIT image signing.
- `UBOOT_FIT_KEY_SIGN_PKCS`: algorithm for the public key certificate for FIT image signing.
- `UBOOT_FITIMAGE_ENABLE`: enable the generation of a U-Boot FIT image.
- `UBOOT_MKIMAGE_DTCOPTS`: DTC options for U-Boot `mkimage` when rebuilding the FIT image containing the kernel.

See U-Boot\'s documentation for details about [verified boot](https://source.denx.de/u-boot/u-boot/-/blob/master/doc/uImage.FIT/verified-boot.txt) and the [signature process](https://source.denx.de/u-boot/u-boot/-/blob/master/doc/uImage.FIT/signature.txt).

> 查看 U-Boot 的文档，了解有关[已验证启动](https://source.denx.de/u-boot/u-boot/-/blob/master/doc/uImage.FIT/verified-boot.txt)和[签名过程](https://source.denx.de/u-boot/u-boot/-/blob/master/doc/uImage.FIT/signature.txt)的详细信息。

See also the description of `ref-classes-kernel-fitimage` class, which this class imitates.

# `uninative`

Attempts to isolate the build system from the host distribution\'s C library in order to make re-use of native shared state artifacts across different host distributions practical. With this class enabled, a tarball containing a pre-built C library is downloaded at the start of the build. In the Poky reference distribution this is enabled by default through `meta/conf/distro/include/yocto-uninative.inc`. Other distributions that do not derive from poky can also \"`require conf/distro/include/yocto-uninative.inc`\" to use this. Alternatively if you prefer, you can build the uninative-tarball recipe yourself, publish the resulting tarball (e.g. via HTTP) and set `UNINATIVE_URL` and `UNINATIVE_CHECKSUM` appropriately. For an example, see the `meta/conf/distro/include/yocto-uninative.inc`.

> 尝试将构建系统与主机发行版的 C 库隔离开来，以便在不同的主机发行版之间可以重复使用本地共享状态的工件。启用此类后，在构建开始时会下载一个包含预构建 C 库的压缩包。在 Poky 参考发行版中，这是通过 `meta/conf/distro/include/yocto-uninative.inc` 默认启用的。其他不从 Poky 派生的发行版也可以“要求 conf/distro/include/yocto-uninative.inc”来使用它。或者，如果您愿意，您可以自己构建 uninative-tarball 配方，发布生成的压缩包(例如通过 HTTP)，并适当设置 `UNINATIVE_URL` 和 `UNINATIVE_CHECKSUM`。有关示例，请参见 `meta/conf/distro/include/yocto-uninative.inc`。

The `ref-classes-uninative` class is also used unconditionally by the extensible SDK. When building the extensible SDK, `uninative-tarball` is built and the resulting tarball is included within the SDK.

> 类 `ref-classes-uninative` 也被可扩展 SDK 无条件地使用。在构建可扩展 SDK 时，会构建 `uninative-tarball`，并将生成的 tarball 包含在 SDK 中。

# `update-alternatives`

The `ref-classes-update-alternatives` class handles renaming the binaries so that multiple packages can be installed without conflicts. The `ar` command still works regardless of which packages are installed or subsequently removed. The class renames the conflicting binary in each package and symlinks the highest priority binary during installation or removal of packages.

> 类 `ref-classes-update-alternatives` 可以处理重命名二进制文件，以便可以安装多个软件包而不会发生冲突。无论安装哪个软件包或随后删除哪个软件包，`ar` 命令仍然可以工作。该类在安装或删除软件包时会重命名冲突的二进制文件，并在安装或删除软件包时符号链接最高优先级的二进制文件。

To use this class, you need to define a number of variables:

- `ALTERNATIVE`
- `ALTERNATIVE_LINK_NAME`
- `ALTERNATIVE_TARGET`
- `ALTERNATIVE_PRIORITY`

These variables list alternative commands needed by a package, provide pathnames for links, default links for targets, and so forth. For details on how to use this class, see the comments in the :yocto_[git:%60update-alternatives.bbclass](git:%60update-alternatives.bbclass) \</poky/tree/meta/classes-recipe/update-alternatives.bbclass\>\` file.

> 这些变量列出了一个包所需的替代命令，提供链接的路径名，目标的默认链接等等。要了解如何使用这个类，请参阅：yocto_[git:`update-alternatives.bbclass`](git:%60update-alternatives.bbclass%60) \</poky/tree/meta/classes-recipe/update-alternatives.bbclass\>\`文件中的注释。

::: note
::: title
Note
:::

You can use the `update-alternatives` command directly in your recipes. However, this class simplifies things in most cases.
:::

# `update-rc.d`

The `ref-classes-update-rc.d` class uses `update-rc.d` to safely install an initialization script on behalf of the package. The OpenEmbedded build system takes care of details such as making sure the script is stopped before a package is removed and started when the package is installed.

> 这个 ref-classes-update-rc.d 类使用 update-rc.d 安全地代表包安装初始化脚本。OpenEmbedded 构建系统负责处理诸如在包被移除前停止脚本，以及在安装包时启动脚本等细节。

Three variables control this class: `INITSCRIPT_PACKAGES`. See the variable links for details.

> 三个变量控制此类：`INITSCRIPT_PACKAGES`。有关详细信息，请参阅变量链接。

# `useradd*`

The `useradd* <ref-classes-useradd>` provides a simple example that shows how to add three users and groups to two packages.

> 类 `useradd* <ref-classes-useradd>` 中的:oe_[git:%60meta-skeleton/recipes-skeleton/useradd/useradd-example.bb](git:%60meta-skeleton/recipes-skeleton/useradd/useradd-example.bb) \</openembedded-core/tree/meta-skeleton/recipes-skeleton/useradd/useradd-example.bb\>菜谱提供了一个简单的示例，显示如何为两个包添加三个用户和组。

The `useradd_base <ref-classes-useradd>` class provides basic functionality for user or groups settings.

The `useradd* <ref-classes-useradd>` variables.

> 这些 useradd*类支持 USERADD_PACKAGES、USERADD_PARAM、GROUPADD_PARAM 和 GROUPMEMS_PARAM 变量。

The `useradd-staticids <ref-classes-useradd>` class supports the addition of users or groups that have static user identification (`uid`) and group identification (`gid`) values.

> 这个 useradd-staticids 类支持添加具有静态用户标识(uid)和组标识(gid)值的用户或组。

The default behavior of the OpenEmbedded build system for assigning `uid` and `gid` values when packages add users and groups during package install time is to add them dynamically. This works fine for programs that do not care what the values of the resulting users and groups become. In these cases, the order of the installation determines the final `uid` and `gid` values. However, if non-deterministic `uid` and `gid` values are a problem, you can override the default, dynamic application of these values by setting static values. When you set static values, the OpenEmbedded build system looks in `BBPATH` for `files/passwd` and `files/group` files for the values.

> 默认情况下，OpenEmbedded 构建系统在安装软件包时为添加的用户和组分配 uid 和 gid 值时是动态添加的。这对于不关心结果用户和组的值的程序工作得很好。在这些情况下，安装的顺序决定了最终的 uid 和 gid 值。但是，如果非确定性的 uid 和 gid 值是一个问题，您可以通过设置静态值来覆盖默认的动态应用这些值。当您设置静态值时，OpenEmbedded 构建系统会在 BBPATH 中查找 files/passwd 和 files/group 文件来获取这些值。

To use static `uid` and `gid` values, you need to set some variables. See the `USERADDEXTENSION` class for additional information.

> 要使用静态的 `uid` 和 `gid` 值，您需要设置一些变量。请参见 `USERADDEXTENSION` 类以获取额外信息。

::: note
::: title
Note
:::

You do not use the `useradd-staticids <ref-classes-useradd>` directory will correct this condition.

> 不要直接使用 `useradd-staticids <ref-classes-useradd>` 目录将纠正此条件。
> :::

# `utility-tasks`

The `ref-classes-utility-tasks`.

> 类 `ref-classes-utility-tasks`。

This class is enabled by default because it is inherited by the `ref-classes-base` class.

# `utils`

The `ref-classes-utils``). One example use is for ` bb.utils.contains()`.

> `ref-classes-utils` 类提供了一些通常用于内联 Python 表达式(例如 `$`)的有用的 Python 函数。一个示例用法是 `bb.utils.contains()`。

This class is enabled by default because it is inherited by the `ref-classes-base` class.

# `vala`

The `ref-classes-vala` class supports recipes that need to build software written using the Vala programming language.

# `waf`

The `ref-classes-waf` variables to specify additional configuration options to be passed on the Waf command line.

> 类 `ref-classes-waf` 变量指定要传递到 Waf 命令行的其他配置选项。
