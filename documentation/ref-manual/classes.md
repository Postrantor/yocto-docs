---
tip: translate by openai@2023-06-07 23:03:34
...
---
title: Classes
---

Class files are used to abstract common functionality and share it amongst multiple recipe (`.bb`) files. To use a class file, you simply make sure the recipe inherits the class. In most cases, when a recipe inherits a class it is enough to enable its features. There are cases, however, where in the recipe you might need to set variables or override some default behavior.

> 类文件用于抽象公共功能并在多个配方（`.bb`）文件之间共享。要使用类文件，只需确保配方继承该类即可。在大多数情况下，当一个配方继承一个类时，就足以启用其功能。但也有一些情况，在配方中你可能需要设置变量或覆盖一些默认行为。


Any `Metadata`{.interpreted-text role="term"} usually found in a recipe can also be placed in a class file. Class files are identified by the extension `.bbclass` and are usually placed in one of a set of subdirectories beneath the `meta*/` directory found in the `Source Directory`{.interpreted-text role="term"}:

> 任何常见于配方中的元数据也可以放置在类文件中。类文件以`.bbclass`为扩展名，通常放置在源目录中`meta*/`目录下的一组子目录中：

> - `classes-recipe/` - classes intended to be inherited by recipes individually
> - `classes-global/` - classes intended to be inherited globally
> - `classes/` - classes whose usage context is not clearly defined


Class files can also be pointed to by `BUILDDIR`{.interpreted-text role="term"} (e.g. `build/`) in the same way as `.conf` files in the `conf` directory. Class files are searched for in `BBPATH`{.interpreted-text role="term"} using the same method by which `.conf` files are searched.

> 类文件也可以通过`BUILDDIR`（例如`build/`）来指向，就像`conf`目录中的`.conf`文件一样。类文件将使用与`.conf`文件相同的方法在`BBPATH`中搜索。


This chapter discusses only the most useful and important classes. Other classes do exist within the `meta/classes*` directories in the Source Directory. You can reference the `.bbclass` files directly for more information.

> 本章只讨论最有用和最重要的类。在源目录中的`meta/classes*`目录中还存在其他类。您可以直接参考`.bbclass`文件以获取更多信息。

# `allarch` {#ref-classes-allarch}


The `ref-classes-allarch`{.interpreted-text role="ref"} class is inherited by recipes that do not produce architecture-specific output. The class disables functionality that is normally needed for recipes that produce executable binaries (such as building the cross-compiler and a C library as pre-requisites, and splitting out of debug symbols during packaging).

> 类`ref-classes-allarch`{.interpreted-text role="ref"}由不生成特定架构输出的配方继承。该类禁用了通常为生成可执行二进制文件所需的功能（例如构建交叉编译器和C库作为先决条件，以及在打包期间分离调试符号）。

::: note
::: title
Note
:::


Unlike some distro recipes (e.g. Debian), OpenEmbedded recipes that produce packages that depend on tunings through use of the `RDEPENDS`{.interpreted-text role="term"} and `TUNE_PKGARCH`{.interpreted-text role="term"} variables, should never be configured for all architectures using `ref-classes-allarch`{.interpreted-text role="ref"}. This is the case even if the recipes do not produce architecture-specific output.

> 与一些发行版的菜谱（例如Debian）不同，使用`RDEPENDS`{.interpreted-text role="term"}和`TUNE_PKGARCH`{.interpreted-text role="term"}变量来产生依赖于调整的软件包的OpenEmbedded菜谱，永远不应该使用`ref-classes-allarch`{.interpreted-text role="ref"}为所有架构进行配置。即使菜谱不产生特定架构的输出，也是如此。


Configuring such recipes for all architectures causes the `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"} tasks to have different signatures for the machines with different tunings. Additionally, unnecessary rebuilds occur every time an image for a different `MACHINE`{.interpreted-text role="term"} is built even when the recipe never changes.

> 配置这些配方适用于所有架构会导致`do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}任务具有不同的签名，用于具有不同调整的机器。此外，即使配方从未更改，每次为不同的`MACHINE`{.interpreted-text role="term"}构建图像时也会发生不必要的重新构建。
:::


By default, all recipes inherit the `ref-classes-base`{.interpreted-text role="ref"} and `ref-classes-package`{.interpreted-text role="ref"} classes, which enable functionality needed for recipes that produce executable output. If your recipe, for example, only produces packages that contain configuration files, media files, or scripts (e.g. Python and Perl), then it should inherit the `ref-classes-allarch`{.interpreted-text role="ref"} class.

> 默认情况下，所有的配方都继承`ref-classes-base`{.interpreted-text role="ref"}和`ref-classes-package`{.interpreted-text role="ref"}类，这些类为产生可执行输出的配方提供了必要的功能。如果您的配方只产生包含配置文件、媒体文件或脚本（例如Python和Perl）的包，那么它应该继承`ref-classes-allarch`{.interpreted-text role="ref"}类。

# `archiver` {#ref-classes-archiver}

The `ref-classes-archiver`{.interpreted-text role="ref"} class supports releasing source code and other materials with the binaries.


For more details on the source `ref-classes-archiver`{.interpreted-text role="ref"}, see the \"`dev-manual/licenses:maintaining open source license compliance during your product's lifecycle`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual. You can also see the `ARCHIVER_MODE`{.interpreted-text role="term"} variable for information about the variable flags (varflags) that help control archive creation.

> 对于源`ref-classes-archiver`{.interpreted-text role="ref"}的更多详情，请参阅Yocto Project开发任务手册中的“`dev-manual/licenses：在您的产品生命周期中维护开源许可合规性`{.interpreted-text role="ref"}”部分。您还可以查看`ARCHIVER_MODE`{.interpreted-text role="term"}变量，了解有助于控制归档创建的变量标志（varflags）的信息。

# `autotools*` {#ref-classes-autotools}


The `autotools* <ref-classes-autotools>`{.interpreted-text role="ref"} classes support packages built with the `GNU Autotools <GNU_Autotools>`{.interpreted-text role="wikipedia"}.

> 这些自动工具类（参见自动工具类）支持使用GNU Autotools（参见维基百科：GNU Autotools）构建的软件包。


The `autoconf`, `automake`, and `libtool` packages bring standardization. This class defines a set of tasks (e.g. `configure`, `compile` and so forth) that work for all Autotooled packages. It should usually be enough to define a few standard variables and then simply `inherit autotools`. These classes can also work with software that emulates Autotools. For more information, see the \"`dev-manual/new-recipe:building an autotooled package`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> `autoconf`、`automake` 和 `libtool` 软件包带来了标准化。这个类定义了一组任务（例如`configure`、`compile`等），适用于所有的Autotooled软件包。通常只需要定义几个标准变量，然后简单地`inherit autotools`即可。这些类也可以用于模拟Autotools的软件。有关更多信息，请参阅Yocto项目开发任务手册中的“dev-manual/new-recipe：构建一个Autotooled软件包”部分。


By default, the `autotools* <ref-classes-autotools>`{.interpreted-text role="ref"} classes use out-of-tree builds (i.e. `autotools.bbclass` building with `B != S`).

> 默认情况下，`autotools* <ref-classes-autotools>`{.interpreted-text role="ref"}类使用非树形构建（即使用`B != S`构建`autotools.bbclass`）。


If the software being built by a recipe does not support using out-of-tree builds, you should have the recipe inherit the `autotools-brokensep <ref-classes-autotools>`{.interpreted-text role="ref"} class. The `autotools-brokensep <ref-classes-autotools>`{.interpreted-text role="ref"} class behaves the same as the `ref-classes-autotools`{.interpreted-text role="ref"} class but builds with `B`{.interpreted-text role="term"} == `S`{.interpreted-text role="term"}. This method is useful when out-of-tree build support is either not present or is broken.

> 如果软件是按照配方构建的，并且不支持外部树构建，你应该让配方继承`autotools-brokensep <ref-classes-autotools>`{.interpreted-text role="ref"}类。`autotools-brokensep <ref-classes-autotools>`{.interpreted-text role="ref"}类的行为和`ref-classes-autotools`{.interpreted-text role="ref"}类相同，但是使用`B`{.interpreted-text role="term"}等于`S`{.interpreted-text role="term"}进行构建。当外部树构建不存在或者已经损坏时，这种方法很有用。

::: note
::: title
Note
:::

It is recommended that out-of-tree support be fixed and used if at all possible.
:::


It\'s useful to have some idea of how the tasks defined by the `autotools* <ref-classes-autotools>`{.interpreted-text role="ref"} classes work and what they do behind the scenes.

> 有一些了解`autotools* <ref-classes-autotools>`{.interpreted-text role="ref"}类定义的任务是如何工作以及它们背后做了什么有助于更好地理解它们。


- `ref-tasks-configure`{.interpreted-text role="ref"} \-\-- regenerates the configure script (using `autoreconf`) and then launches it with a standard set of arguments used during cross-compilation. You can pass additional parameters to `configure` through the `EXTRA_OECONF`{.interpreted-text role="term"} or `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"} variables.

> - `ref-tasks-configure`{.interpreted-text role="ref"} \-\-- 使用`autoreconf`重新生成配置脚本，然后使用跨编译时使用的标准参数启动它。您可以通过`EXTRA_OECONF`{.interpreted-text role="term"} 或 `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"} 变量将额外的参数传递给`configure`。

- `ref-tasks-compile`{.interpreted-text role="ref"} \-\-- runs `make` with arguments that specify the compiler and linker. You can pass additional arguments through the `EXTRA_OEMAKE`{.interpreted-text role="term"} variable.

> 运行`make`，使用指定编译器和链接器的参数。您可以通过`EXTRA_OEMAKE`变量传递额外的参数。
- `ref-tasks-install`{.interpreted-text role="ref"} \-\-- runs `make install` and passes in `${``D`{.interpreted-text role="term"}`}` as `DESTDIR`.

# `base` {#ref-classes-base}


The `ref-classes-base`{.interpreted-text role="ref"} class is special in that every `.bb` file implicitly inherits the class. This class contains definitions for standard basic tasks such as fetching, unpacking, configuring (empty by default), compiling (runs any `Makefile` present), installing (empty by default) and packaging (empty by default). These tasks are often overridden or extended by other classes such as the `ref-classes-autotools`{.interpreted-text role="ref"} class or the `ref-classes-package`{.interpreted-text role="ref"} class.

> 类`ref-classes-base`{.interpreted-text role="ref"}特殊之处在于每个`.bb`文件都隐式继承了该类。此类包含标准基本任务的定义，例如获取、解压、配置（默认为空）、编译（运行任何`Makefile`）、安装（默认为空）和打包（默认为空）。这些任务通常被其他类覆盖或扩展，例如类`ref-classes-autotools`{.interpreted-text role="ref"}或类`ref-classes-package`{.interpreted-text role="ref"}。


The class also contains some commonly used functions such as `oe_runmake`, which runs `make` with the arguments specified in `EXTRA_OEMAKE`{.interpreted-text role="term"} variable as well as the arguments passed directly to `oe_runmake`.

> 类中还包含一些常用函数，如`oe_runmake`，它可以使用`EXTRA_OEMAKE`变量中指定的参数以及直接传递给`oe_runmake`的参数来运行`make`。

# `bash-completion` {#ref-classes-bash-completion}

Sets up packaging and dependencies appropriate for recipes that build software that includes bash-completion data.

# `bin_package` {#ref-classes-bin-package}


The `ref-classes-bin-package`{.interpreted-text role="ref"} class is a helper class for recipes that extract the contents of a binary package (e.g. an RPM) and install those contents rather than building the binary from source. The binary package is extracted and new packages in the configured output package format are created. Extraction and installation of proprietary binaries is a good example use for this class.

> 这个`ref-classes-bin-package`{.interpreted-text role="ref"}类是一个帮助类，用于提取二进制包（例如RPM）的内容并安装这些内容，而不是从源代码构建二进制文件。 二进制包将被提取，并在配置的输出包格式中创建新的包。 提取和安装专有二进制文件是该类的一个很好的例子。

::: note
::: title
Note
:::


For RPMs and other packages that do not contain a subdirectory, you should specify an appropriate fetcher parameter to point to the subdirectory. For example, if BitBake is using the Git fetcher (`git://`), the \"subpath\" parameter limits the checkout to a specific subpath of the tree. Here is an example where `${BP}` is used so that the files are extracted into the subdirectory expected by the default value of `S`{.interpreted-text role="term"}:

> 对于不包含子目录的RPM和其他软件包，您应该指定适当的获取器参数以指向子目录。例如，如果BitBake使用Git获取器（`git://`），则“subpath”参数将检出限制为树的特定子路径。这里有一个使用`${BP}`的示例，因此文件将提取到默认值`S`所预期的子目录中：

```
SRC_URI = "git://example.com/downloads/somepackage.rpm;branch=main;subpath=${BP}"
```


See the \"`bitbake-user-manual/bitbake-user-manual-fetching:fetchers`{.interpreted-text role="ref"}\" section in the BitBake User Manual for more information on supported BitBake Fetchers.

> 查看BitBake用户手册中的“bitbake-user-manual / bitbake-user-manual-fetching：fetchers”部分，了解更多关于支持的BitBake Fetchers的信息。
:::

# `binconfig` {#ref-classes-binconfig}

The `ref-classes-binconfig`{.interpreted-text role="ref"} class helps to correct paths in shell scripts.


Before `pkg-config` had become widespread, libraries shipped shell scripts to give information about the libraries and include paths needed to build software (usually named `LIBNAME-config`). This class assists any recipe using such scripts.

> 在`pkg-config`变得普遍之前，库都会附带一些shell脚本，来提供关于库和需要构建软件所需的include路径的信息（通常命名为`LIBNAME-config`）。这个类可以帮助任何使用这些脚本的配方。


During staging, the OpenEmbedded build system installs such scripts into the `sysroots/` directory. Inheriting this class results in all paths in these scripts being changed to point into the `sysroots/` directory so that all builds that use the script use the correct directories for the cross compiling layout. See the `BINCONFIG_GLOB`{.interpreted-text role="term"} variable for more information.

> 在构建阶段，OpenEmbedded构建系统将这些脚本安装到`sysroots/`目录中。继承此类将导致这些脚本中的所有路径都被更改为指向`sysroots/`目录，以便所有使用脚本的构建都使用用于跨编译布局的正确目录。有关更多信息，请参阅`BINCONFIG_GLOB`{.interpreted-text role="term"}变量。

# `binconfig-disabled` {#ref-classes-binconfig-disabled}


An alternative version of the `ref-classes-binconfig`{.interpreted-text role="ref"} class, which disables binary configuration scripts by making them return an error in favor of using `pkg-config` to query the information. The scripts to be disabled should be specified using the `BINCONFIG`{.interpreted-text role="term"} variable within the recipe inheriting the class.

> 这个类（ref-classes-binconfig）的替代版本，可以通过使它们返回错误来禁用二进制配置脚本，以便使用pkg-config查询信息。要禁用的脚本应该在继承该类的配方中使用BINCONFIG变量指定。

# `buildhistory` {#ref-classes-buildhistory}


The `ref-classes-buildhistory`{.interpreted-text role="ref"} class records a history of build output metadata, which can be used to detect possible regressions as well as used for analysis of the build output. For more information on using Build History, see the \"`dev-manual/build-quality:maintaining build output quality`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 这个ref-classes-buildhistory类记录了构建输出元数据的历史，可以用来检测可能的回归，也可以用于分析构建输出。有关使用构建历史的更多信息，请参见Yocto项目开发任务手册中的“dev-manual/build-quality：维护构建输出质量”部分。

# `buildstats` {#ref-classes-buildstats}


The `ref-classes-buildstats`{.interpreted-text role="ref"} class records performance statistics about each task executed during the build (e.g. elapsed time, CPU usage, and I/O usage).

> 类`ref-classes-buildstats`记录构建期间执行的每个任务的性能统计信息（例如，耗时、CPU使用率和I/O使用率）。


When you use this class, the output goes into the `BUILDSTATS_BASE`{.interpreted-text role="term"} directory, which defaults to `${TMPDIR}/buildstats/`. You can analyze the elapsed time using `scripts/pybootchartgui/pybootchartgui.py`, which produces a cascading chart of the entire build process and can be useful for highlighting bottlenecks.

> 当您使用此类时，输出将转移到默认为`${TMPDIR}/buildstats/`的`BUILDSTATS_BASE`目录。您可以使用`scripts/pybootchartgui/pybootchartgui.py`分析所用时间，它会生成整个构建过程的级联图表，可以有助于突出瓶颈。


Collecting build statistics is enabled by default through the `USER_CLASSES`{.interpreted-text role="term"} variable from your `local.conf` file. Consequently, you do not have to do anything to enable the class. However, if you want to disable the class, simply remove \"`ref-classes-buildstats`{.interpreted-text role="ref"}\" from the `USER_CLASSES`{.interpreted-text role="term"} list.

> 默认情况下，通过您的`local.conf`文件中的`USER_CLASSES`变量启用收集构建统计信息。因此，您无需做任何操作即可启用该类。但是，如果要禁用该类，只需从`USER_CLASSES`列表中删除“ref-classes-buildstats”即可。

# `buildstats-summary` {#ref-classes-buildstats-summary}


When inherited globally, prints statistics at the end of the build on sstate re-use. In order to function, this class requires the `ref-classes-buildstats`{.interpreted-text role="ref"} class be enabled.

> 当全局继承时，在 sstate 重用结束时会打印统计信息。为了使这个类生效，需要启用`ref-classes-buildstats`类。

# `cargo` {#ref-classes-cargo}


The `ref-classes-cargo`{.interpreted-text role="ref"} class allows to compile Rust language programs using [Cargo](https://doc.rust-lang.org/cargo/). Cargo is Rust\'s package manager, allowing to fetch package dependencies and build your program.

> 类`ref-classes-cargo`允许使用[Cargo](https://doc.rust-lang.org/cargo/)编译Rust语言程序。Cargo是Rust的包管理器，可以获取包依赖项并构建程序。


Using this class makes it very easy to build Rust programs. All you need is to use the `SRC_URI`{.interpreted-text role="term"} variable to point to a source repository which can be built by Cargo, typically one that was created by the `cargo new` command, containing a `Cargo.toml` file and a `src` subdirectory.

> 使用这个类可以很容易地构建Rust程序。你所需要做的就是使用`SRC_URI`变量指向一个可以用Cargo构建的源代码库，通常是由`cargo new`命令创建的，包含一个`Cargo.toml`文件和一个`src`子目录。


You will find a simple example in the :oe\_[git:%60rust-hello-world_git.bb](git:%60rust-hello-world_git.bb) \</openembedded-core/tree/meta/recipes-extended/rust-example/rust-hello-world_git.bb\>[ recipe. A more complex example, with package dependencies, is the :oe_git:\`uutils-coreutils \</meta-openembedded/tree/meta-oe/recipes-core/uutils-coreutils\>]{.title-ref} recipe, which was generated by the [cargo-bitbake](https://crates.io/crates/cargo-bitbake) tool.

> 你可以在oe_[git:%60rust-hello-world_git.bb](git:%60rust-hello-world_git.bb) \</openembedded-core/tree/meta/recipes-extended/rust-example/rust-hello-world_git.bb\> 食谱中找到一个简单的示例。更复杂的示例，带有包依赖关系，是oe_git:\`uutils-coreutils \</meta-openembedded/tree/meta-oe/recipes-core/uutils-coreutils\>]{.title-ref} 食谱，它是由[cargo-bitbake](https://crates.io/crates/cargo-bitbake) 工具生成的。

This class inherits the `ref-classes-cargo_common`{.interpreted-text role="ref"} class.

# `cargo_common` {#ref-classes-cargo_common}

The `ref-classes-cargo_common`{.interpreted-text role="ref"} class is an internal class that is not intended to be used directly.


An exception is the \"rust\" recipe, to build the Rust compiler and runtime library, which is built by Cargo but cannot use the `ref-classes-cargo`{.interpreted-text role="ref"} class. This is why this class was introduced.

> 例外是“rust”食谱，用来构建Rust编译器和运行时库，它是由Cargo构建的，但不能使用`ref-classes-cargo`类。这就是为什么引入这个类的原因。

# `cargo-update-recipe-crates`[] {#ref-classes-ccache} {#ref-classes-cargo-update-recipe-crates}


The `ref-classes-cargo-update-recipe-crates`{.interpreted-text role="ref"} class allows recipe developers to update the list of Cargo crates in `SRC_URI`{.interpreted-text role="term"} by reading the `Cargo.lock` file in the source tree.

> 这个ref-classes-cargo-update-recipe-crates类允许食谱开发者通过读取源树中的Cargo.lock文件来更新SRC_URI中的Cargo框架列表。


To do so, create a recipe for your program, for example using `devtool </ref-manual/devtool-reference>`{.interpreted-text role="doc"}, make it inherit the `ref-classes-cargo`{.interpreted-text role="ref"} and `ref-classes-cargo-update-recipe-crates`{.interpreted-text role="ref"} and run:

> 为此，请为您的程序创建一个配方，例如使用`devtool`</ref-manual/devtool-reference>`{.interpreted-text role="doc"}，将其继承`ref-classes-cargo`{.interpreted-text role="ref"}和`ref-classes-cargo-update-recipe-crates`{.interpreted-text role="ref"}，然后运行：

```
bitbake -c update_crates recipe
```

This creates a `recipe-crates.inc` file that you can include in your recipe:

```
require ${BPN}-crates.inc
```

That\'s also something you can achieve by using the [cargo-bitbake](https://crates.io/crates/cargo-bitbake) tool.

# `ccache`


The `ref-classes-ccache`{.interpreted-text role="ref"} class enables the C/C++ Compiler Cache for the build. This class is used to give a minor performance boost during the build.

> 这个`ref-classes-ccache`{.interpreted-text role="ref"}类可以为构建启用C/C++编译器缓存。这个类用于在构建期间提供一个轻微的性能提升。


See [https://ccache.samba.org/](https://ccache.samba.org/) for information on the C/C++ Compiler Cache, and the :oe\_[git:%60ccache.bbclass](git:%60ccache.bbclass) \</openembedded-core/tree/meta/classes/ccache.bbclass\>[ file for details about how to enable this mechanism in your configuration file, how to disable it for specific recipes, and how to share ]{.title-ref}[ccache]{.title-ref}\` files between builds.

> 请访问[https://ccache.samba.org/](https://ccache.samba.org/) 了解C/C++编译器缓存的信息，以及:oe\_[git:%60ccache.bbclass](git:%60ccache.bbclass) \</openembedded-core/tree/meta/classes/ccache.bbclass\>文件，了解如何在配置文件中启用此机制、如何为特定的食谱禁用它，以及如何在构建之间共享[ccache]{.title-ref}文件。

However, using the class can lead to unexpected side-effects. Thus, using this class is not recommended.

# `chrpath` {#ref-classes-chrpath}


The `ref-classes-chrpath`{.interpreted-text role="ref"} class is a wrapper around the \"chrpath\" utility, which is used during the build process for `ref-classes-nativesdk`{.interpreted-text role="ref"}, `ref-classes-cross`{.interpreted-text role="ref"}, and `ref-classes-cross-canadian`{.interpreted-text role="ref"} recipes to change `RPATH` records within binaries in order to make them relocatable.

> 类`ref-classes-chrpath`{.interpreted-text role="ref"}是一个包装程序，用于在构建过程中使用“chrpath”实用程序，用于更改`ref-classes-nativesdk`{.interpreted-text role="ref"}、`ref-classes-cross`{.interpreted-text role="ref"}和`ref-classes-cross-canadian`{.interpreted-text role="ref"}食谱中的二进制文件中的`RPATH`记录，以使其可重定位。

# `cmake` {#ref-classes-cmake}


The `ref-classes-cmake`{.interpreted-text role="ref"} class allows recipes to build software using the [CMake](https://cmake.org/overview/) build system. You can use the `EXTRA_OECMAKE`{.interpreted-text role="term"} variable to specify additional configuration options to pass to the `cmake` command line.

> 这个ref-classes-cmake类允许食谱使用CMake构建系统构建软件。您可以使用EXTRA_OECMAKE变量指定要传递给cmake命令行的其他配置选项。


By default, the `ref-classes-cmake`{.interpreted-text role="ref"} class uses [Ninja](https://ninja-build.org/) instead of GNU make for building, which offers better build performance. If a recipe is broken with Ninja, then the recipe can set the `OECMAKE_GENERATOR`{.interpreted-text role="term"} variable to `Unix Makefiles` to use GNU make instead.

> 默认情况下，`ref-classes-cmake`{.interpreted-text role="ref"}类使用[Ninja](https://ninja-build.org/)而不是GNU make进行构建，可以提供更好的构建性能。如果配方与Ninja出现故障，则可以将`OECMAKE_GENERATOR`{.interpreted-text role="term"}变量设置为`Unix Makefiles`以使用GNU make。


If you need to install custom CMake toolchain files supplied by the application being built, you should install them (during `ref-tasks-install`{.interpreted-text role="ref"}) to the preferred CMake Module directory: `${D}${datadir}/cmake/modules/`.

> 如果您需要安装所构建应用程序提供的自定义CMake工具链文件，应在（`ref-tasks-install`{.interpreted-text role="ref"}）期间将其安装到首选的CMake模块目录：`${D}${datadir}/cmake/modules/`。

# `cml1` {#ref-classes-cml1}

The `ref-classes-cml1`{.interpreted-text role="ref"} class provides basic support for the Linux kernel style build configuration system.

# `compress_doc` {#ref-classes-compress_doc}


Enables compression for man pages and info pages. This class is intended to be inherited globally. The default compression mechanism is gz (gzip) but you can select an alternative mechanism by setting the `DOC_COMPRESS`{.interpreted-text role="term"} variable.

> 启用手册页和信息页的压缩。此类旨在全局继承。默认压缩机制是gz（gzip），但您可以通过设置`DOC_COMPRESS`变量来选择替代机制。

# `copyleft_compliance` {#ref-classes-copyleft_compliance}


The `ref-classes-copyleft_compliance`{.interpreted-text role="ref"} class preserves source code for the purposes of license compliance. This class is an alternative to the `ref-classes-archiver`{.interpreted-text role="ref"} class and is still used by some users even though it has been deprecated in favor of the `ref-classes-archiver`{.interpreted-text role="ref"} class.

> 这个ref-classes-copyleft_compliance类保存源代码以符合许可证要求。尽管它被ref-classes-archiver类取代，但仍有一些用户使用它。

# `copyleft_filter` {#ref-classes-copyleft_filter}


A class used by the `ref-classes-archiver`{.interpreted-text role="ref"} and `ref-classes-copyleft_compliance`{.interpreted-text role="ref"} classes for filtering licenses. The `copyleft_filter` class is an internal class and is not intended to be used directly.

> 这个`copyleft_filter`类被`ref-classes-archiver`和`ref-classes-copyleft_compliance`类用于过滤许可证。这个`copyleft_filter`类是一个内部类，不应该直接使用。

# `core-image` {#ref-classes-core-image}


The `ref-classes-core-image`{.interpreted-text role="ref"} class provides common definitions for the `core-image-*` image recipes, such as support for additional `IMAGE_FEATURES`{.interpreted-text role="term"}.

> `ref-classes-core-image`{.interpreted-text role="ref"} 类提供了 `core-image-*` 图像配方的常见定义，比如支持额外的 `IMAGE_FEATURES`{.interpreted-text role="term"}。

# `cpan*` {#ref-classes-cpan}

The `cpan* <ref-classes-cpan>`{.interpreted-text role="ref"} classes support Perl modules.


Recipes for Perl modules are simple. These recipes usually only need to point to the source\'s archive and then inherit the proper class file. Building is split into two methods depending on which method the module authors used.

> 模块的Perl配方很简单。这些配方通常只需要指向源存档，然后继承正确的类文件。根据模块作者使用的方法，构建分为两种方法。

- Modules that use old `Makefile.PL`-based build system require `cpan.bbclass` in their recipes.
- Modules that use `Build.PL`-based build system require using `cpan_build.bbclass` in their recipes.

Both build methods inherit the `cpan-base <ref-classes-cpan>`{.interpreted-text role="ref"} class for basic Perl support.

# `create-spdx` {#ref-classes-create-spdx}


The `ref-classes-create-spdx`{.interpreted-text role="ref"} class provides support for automatically creating `SPDX`{.interpreted-text role="term"} `SBOM`{.interpreted-text role="term"} documents based upon image and SDK contents.

> 类`ref-classes-create-spdx`提供了基于图像和SDK内容自动创建SPDX SBOM文档的支持。

This class is meant to be inherited globally from a configuration file:

```
INHERIT += "create-spdx"
```


The toplevel `SPDX`{.interpreted-text role="term"} output file is generated in JSON format as a `IMAGE-MACHINE.spdx.json` file in `tmp/deploy/images/MACHINE/` inside the `Build Directory`{.interpreted-text role="term"}. There are other related files in the same directory, as well as in `tmp/deploy/spdx`.

> 顶层`SPDX`输出文件以JSON格式生成，文件名为`IMAGE-MACHINE.spdx.json`，位于`Build Directory`的`tmp/deploy/images/MACHINE/`目录下。同一目录下也有其他相关文件，以及`tmp/deploy/spdx`目录中的文件。


The exact behaviour of this class, and the amount of output can be controlled by the `SPDX_PRETTY`{.interpreted-text role="term"}, `SPDX_ARCHIVE_PACKAGED`{.interpreted-text role="term"}, `SPDX_ARCHIVE_SOURCES`{.interpreted-text role="term"} and `SPDX_INCLUDE_SOURCES`{.interpreted-text role="term"} variables.

> 此类的确切行为以及输出量可以由`SPDX_PRETTY`、`SPDX_ARCHIVE_PACKAGED`、`SPDX_ARCHIVE_SOURCES`和`SPDX_INCLUDE_SOURCES`变量来控制。


See the description of these variables and the \"`dev-manual/sbom:creating a software bill of materials`{.interpreted-text role="ref"}\" section in the Yocto Project Development Manual for more details.

> 查看这些变量的描述，以及Yocto Project开发手册中的“dev-manual / sbom：创建软件物料清单”部分，了解更多细节。

# `cross` {#ref-classes-cross}

The `ref-classes-cross`{.interpreted-text role="ref"} class provides support for the recipes that build the cross-compilation tools.

# `cross-canadian` {#ref-classes-cross-canadian}


The `ref-classes-cross-canadian`{.interpreted-text role="ref"} class provides support for the recipes that build the Canadian Cross-compilation tools for SDKs. See the \"`overview-manual/concepts:cross-development toolchain generation`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual for more discussion on these cross-compilation tools.

> 这个ref-classes-cross-canadian类提供支持来构建用于SDK的加拿大跨编译工具。有关这些跨编译工具的更多讨论，请参见Yocto项目概览和概念手册中的“overview-manual/concepts：cross-development toolchain generation”部分。

# `crosssdk` {#ref-classes-crosssdk}


The `ref-classes-crosssdk`{.interpreted-text role="ref"} class provides support for the recipes that build the cross-compilation tools used for building SDKs. See the \"`overview-manual/concepts:cross-development toolchain generation`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual for more discussion on these cross-compilation tools.

> 类`ref-classes-crosssdk`{.interpreted-text role="ref"}提供支持用于构建用于构建SDK的交叉编译工具的食谱。有关这些交叉编译工具的更多讨论，请参阅Yocto项目概述与概念手册中的“`overview-manual/concepts:cross-development toolchain generation`{.interpreted-text role="ref"}”部分。

# `cve-check` {#ref-classes-cve-check}


The `ref-classes-cve-check`{.interpreted-text role="ref"} class looks for known CVEs (Common Vulnerabilities and Exposures) while building with BitBake. This class is meant to be inherited globally from a configuration file:

> `ref-classes-cve-check` 类在使用 BitBake 构建时会查找已知的CVE（常见漏洞和暴露）。该类旨在从配置文件中全局继承：

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

> 在使用Bitbake构建软件后，CVE检查输出报告可在'tmp/deploy/cve'中获得，特定图像的摘要可在'tmp/deploy/images/*.cve'或'tmp/deploy/images/*.json'文件中获得。


When building, the CVE checker will emit build time warnings for any detected issues which are in the state `Unpatched`, meaning that CVE issue seems to affect the software component and version being compiled and no patches to address the issue are applied. Other states for detected CVE issues are: `Patched` meaning that a patch to address the issue is already applied, and `Ignored` meaning that the issue can be ignored.

> 当构建时，CVE检查程序会为任何检测到的处于“未补丁”状态的问题发出构建时警告，这意味着CVE问题似乎影响编译的软件组件和版本，并且没有补丁来解决该问题。检测到的CVE问题的其他状态有：“已补丁”，意味着已经应用了补丁来解决该问题，以及“已忽略”，意味着可以忽略该问题。


The `Patched` state of a CVE issue is detected from patch files with the format `CVE-ID.patch`, e.g. `CVE-2019-20633.patch`, in the `SRC_URI`{.interpreted-text role="term"} and using CVE metadata of format `CVE: CVE-ID` in the commit message of the patch file.

> 系统从具有格式`CVE-ID.patch`（例如`CVE-2019-20633.patch`）的补丁文件中检测到CVE问题的“已补丁”状态，并在补丁文件的提交消息中使用格式为`CVE：CVE-ID`的CVE元数据。


If the recipe lists the `CVE-ID` in `CVE_CHECK_IGNORE`{.interpreted-text role="term"} variable, then the CVE state is reported as `Ignored`. Multiple CVEs can be listed separated by spaces. Example:

> 如果食谱中列出了CVE-ID变量CVE_CHECK_IGNORE中，那么CVE状态将被报告为“忽略”。多个CVE可以用空格分隔列出。例如：

```
CVE_CHECK_IGNORE += "CVE-2020-29509 CVE-2020-29511"
```


If CVE check reports that a recipe contains false positives or false negatives, these may be fixed in recipes by adjusting the CVE product name using `CVE_PRODUCT`{.interpreted-text role="term"} and `CVE_VERSION`{.interpreted-text role="term"} variables. `CVE_PRODUCT`{.interpreted-text role="term"} defaults to the plain recipe name `BPN`{.interpreted-text role="term"} which can be adjusted to one or more CVE database vendor and product pairs using the syntax:

> 如果CVE检查报告称配方包含误报或漏报，可以通过使用`CVE_PRODUCT`{.interpreted-text role="term"}和`CVE_VERSION`{.interpreted-text role="term"}变量来调整配方。 `CVE_PRODUCT`{.interpreted-text role="term"}默认为普通配方名称`BPN`{.interpreted-text role="term"}，可以使用以下语法调整为一个或多个CVE数据库供应商和产品对：

```
CVE_PRODUCT = "flex_project:flex"
```


where `flex_project` is the CVE database vendor name and `flex` is the product name. Similarly if the default recipe version `PV`{.interpreted-text role="term"} does not match the version numbers of the software component in upstream releases or the CVE database, then the `CVE_VERSION`{.interpreted-text role="term"} variable can be used to set the CVE database compatible version number, for example:

> 如果`flex_project`是CVE数据库供应商的名称，而`flex`是产品名称，那么同样，如果默认食谱版本`PV`不匹配上游发布版本或CVE数据库的版本号，则可以使用`CVE_VERSION`变量来设置与CVE数据库兼容的版本号，例如：

```
CVE_VERSION = "2.39"
```


Any bugs or missing or incomplete information in the CVE database entries should be fixed in the CVE database via the [NVD feedback form](https://nvd.nist.gov/info/contact-form).

> 任何CVE数据库条目中的错误、遗漏或不完整信息，都应通过[NVD反馈表单](https://nvd.nist.gov/info/contact-form)在CVE数据库中进行修复。


Users should note that security is a process, not a product, and thus also CVE checking, analyzing results, patching and updating the software should be done as a regular process. The data and assumptions required for CVE checker to reliably detect issues are frequently broken in various ways. These can only be detected by reviewing the details of the issues and iterating over the generated reports, and following what happens in other Linux distributions and in the greater open source community.

> 用户应该注意，安全是一个过程，而不是一个产品，因此也应该定期进行CVE检查、分析结果、补丁和更新软件。CVE检查器确定可靠问题所需的数据和假设经常以各种方式被打破。这些只能通过审查问题的细节并迭代生成的报告，以及关注其他Linux发行版和更大的开源社区中发生的事情来检测到。


You will find some more details in the \"`dev-manual/vulnerabilities:checking for vulnerabilities`{.interpreted-text role="ref"}\" section in the Development Tasks Manual.

> 你可以在开发任务手册中的“dev-manual/vulnerabilities：检查漏洞”部分找到更多的细节。

# `debian` {#ref-classes-debian}


The `ref-classes-debian`{.interpreted-text role="ref"} class renames output packages so that they follow the Debian naming policy (i.e. `glibc` becomes `libc6` and `glibc-devel` becomes `libc6-dev`.) Renaming includes the library name and version as part of the package name.

> `ref-classes-debian` 类重命名输出包，以便遵循Debian命名策略（即`glibc`变为`libc6`，`glibc-devel`变为`libc6-dev`）。重命名包括库名称和版本作为包名的一部分。


If a recipe creates packages for multiple libraries (shared object files of `.so` type), use the `LEAD_SONAME`{.interpreted-text role="term"} variable in the recipe to specify the library on which to apply the naming scheme.

> 如果一个食谱创建了多个库的包（`.so`类型的共享对象文件），请在食谱中使用`LEAD_SONAME`变量指定要应用命名方案的库。

# `deploy` {#ref-classes-deploy}


The `ref-classes-deploy`{.interpreted-text role="ref"} class handles deploying files to the `DEPLOY_DIR_IMAGE`{.interpreted-text role="term"} directory. The main function of this class is to allow the deploy step to be accelerated by shared state. Recipes that inherit this class should define their own `ref-tasks-deploy`{.interpreted-text role="ref"} function to copy the files to be deployed to `DEPLOYDIR`{.interpreted-text role="term"}, and use `addtask` to add the task at the appropriate place, which is usually after `ref-tasks-compile`{.interpreted-text role="ref"} or `ref-tasks-install`{.interpreted-text role="ref"}. The class then takes care of staging the files from `DEPLOYDIR`{.interpreted-text role="term"} to `DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}.

> 类`ref-classes-deploy`{.interpreted-text role="ref"}负责将文件部署到`DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}目录。该类的主要功能是允许部署步骤通过共享状态加速。继承此类的配方应定义自己的`ref-tasks-deploy`{.interpreted-text role="ref"}函数，将要部署的文件复制到`DEPLOYDIR`{.interpreted-text role="term"}，并使用`addtask`在适当的位置添加任务，通常是`ref-tasks-compile`{.interpreted-text role="ref"}或`ref-tasks-install`{.interpreted-text role="ref"}之后。然后，该类负责将文件从`DEPLOYDIR`{.interpreted-text role="term"}移动到`DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}。

# `devicetree` {#ref-classes-devicetree}


The `ref-classes-devicetree`{.interpreted-text role="ref"} class allows to build a recipe that compiles device tree source files that are not in the kernel tree.

> 这个ref-classes-devicetree类允许构建一个菜谱，用于编译不在内核树中的设备树源文件。


The compilation of out-of-tree device tree sources is the same as the kernel in-tree device tree compilation process. This includes the ability to include sources from the kernel such as SoC `dtsi` files as well as C header files, such as `gpio.h`.

> 编译外树设备树源代码与内树内核设备树编译过程相同。这包括从内树内核中包含源代码，如SoC `dtsi`文件以及C头文件，如`gpio.h`的能力。

The `ref-tasks-compile`{.interpreted-text role="ref"} task will compile two kinds of files:

- Regular device tree sources with a `.dts` extension.
- Device tree overlays, detected from the presence of the `/plugin/;` string in the file contents.


This class behaves in a similar way as the `ref-classes-kernel-devicetree`{.interpreted-text role="ref"} class, also deploying output files into `/boot/devicetree`. However, this class stores the deployed device tree binaries into the `devicetree` subdirectory. This avoids clashes with the `ref-classes-kernel-devicetree`{.interpreted-text role="ref"} output. Additionally, the device trees are populated into the sysroot for access via the sysroot from within other recipes.

> 这个类的行为与`ref-classes-kernel-devicetree`{.interpreted-text role="ref"}类类似，也将输出文件部署到`/boot/devicetree`中。但是，此类将部署的设备树二进制文件存储在`devicetree`子目录中，以避免与`ref-classes-kernel-devicetree`{.interpreted-text role="ref"}输出发生冲突。另外，设备树还会被填充到sysroot中，以便在其他配方中通过sysroot访问。


By default, all device tree sources located in `DT_FILES_PATH`{.interpreted-text role="term"} directory are compiled. To select only particular sources, set `DT_FILES`{.interpreted-text role="term"} to a space-separated list of files (relative to `DT_FILES_PATH`{.interpreted-text role="term"}). For convenience, both `.dts` and `.dtb` extensions can be used.

> 默认情况下，位于`DT_FILES_PATH`目录中的所有设备树源文件都会被编译。要仅选择特定的源文件，请将`DT_FILES`设置为以空格分隔的文件列表（相对于`DT_FILES_PATH`）。为了方便起见，可以使用`.dts`和`.dtb`扩展名。


An extra padding is appended to non-overlay device trees binaries. This can typically be used as extra space for adding extra properties at boot time. The padding size can be modified by setting `DT_PADDING_SIZE`{.interpreted-text role="term"} to the desired size, in bytes.

> 非覆盖设备树二进制文件会追加额外的填充。这通常可用于在引导时添加额外的属性。可以通过将`DT_PADDING_SIZE`设置为所需的大小（以字节为单位）来修改填充大小。


See :oe\_[git:%60devicetree.bbclass](git:%60devicetree.bbclass) sources \</openembedded-core/tree/meta/classes-recipe/devicetree.bbclass\>\` for further variables controlling this class.

> 请参阅：oe_[git:%60devicetree.bbclass](git:%60devicetree.bbclass)源码</openembedded-core/tree/meta/classes-recipe/devicetree.bbclass>，以了解更多控制此类的变量。

Here is an excerpt of an example `recipes-kernel/linux/devicetree-acme.bb` recipe inheriting this class:

```
inherit devicetree
COMPATIBLE_MACHINE = "^mymachine$"
SRC_URI:mymachine = "file://mymachine.dts"
```

# `devshell` {#ref-classes-devshell}


The `ref-classes-devshell`{.interpreted-text role="ref"} class adds the `ref-tasks-devshell`{.interpreted-text role="ref"} task. Distribution policy dictates whether to include this class. See the \"`dev-manual/development-shell:using a development shell`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for more information about using `ref-classes-devshell`{.interpreted-text role="ref"}.

> 类ref-classes-devshell{.interpreted-text role="ref"}添加了任务ref-tasks-devshell{.interpreted-text role="ref"}。发布策略决定是否包含此类。有关使用ref-classes-devshell{.interpreted-text role="ref"}的更多信息，请参阅Yocto项目开发任务手册中的“ dev-manual/development-shell：使用开发shell”部分。

# `devupstream` {#ref-classes-devupstream}


The `ref-classes-devupstream`{.interpreted-text role="ref"} class uses `BBCLASSEXTEND`{.interpreted-text role="term"} to add a variant of the recipe that fetches from an alternative URI (e.g. Git) instead of a tarball. Following is an example:

> 这个`ref-classes-devupstream`{.interpreted-text role="ref"}类使用`BBCLASSEXTEND`{.interpreted-text role="term"}来添加一个变体的配方，它从替代URI（例如Git）而不是tarball获取。以下是一个例子：

```
BBCLASSEXTEND = "devupstream:target"
SRC_URI:class-devupstream = "git://git.example.com/example;branch=main"
SRCREV:class-devupstream = "abcd1234"
```


Adding the above statements to your recipe creates a variant that has `DEFAULT_PREFERENCE`{.interpreted-text role="term"} set to \"-1\". Consequently, you need to select the variant of the recipe to use it. Any development-specific adjustments can be done by using the `class-devupstream` override. Here is an example:

> 加入上述声明到你的配方中，就会创建一个 `DEFAULT_PREFERENCE` 设置为“-1”的变体。因此，你需要选择配方的变体来使用它。任何开发特定的调整可以通过使用 `class-devupstream` 覆盖来完成。这里有一个例子：

```
DEPENDS:append:class-devupstream = " gperf-native"
do_configure:prepend:class-devupstream() {
    touch ${S}/README
}
```


The class currently only supports creating a development variant of the target recipe, not `ref-classes-native`{.interpreted-text role="ref"} or `ref-classes-nativesdk`{.interpreted-text role="ref"} variants.

> 現時類只支援建立目標配方的開發變體，而不支援`ref-classes-native`{.interpreted-text role="ref"}或`ref-classes-nativesdk`{.interpreted-text role="ref"}變體。


The `BBCLASSEXTEND`{.interpreted-text role="term"} syntax (i.e. `devupstream:target`) provides support for `ref-classes-native`{.interpreted-text role="ref"} and `ref-classes-nativesdk`{.interpreted-text role="ref"} variants. Consequently, this functionality can be added in a future release.

> 语法BBCLASSEXTEND（即devupstream:target）支持ref-classes-native和ref-classes-nativesdk变体。因此，这种功能可以在未来的版本中添加。

Support for other version control systems such as Subversion is limited due to BitBake\'s automatic fetch dependencies (e.g. `subversion-native`).

# `externalsrc` {#ref-classes-externalsrc}


The `ref-classes-externalsrc`{.interpreted-text role="ref"} class supports building software from source code that is external to the OpenEmbedded build system. Building software from an external source tree means that the build system\'s normal fetch, unpack, and patch process is not used.

> 类`ref-classes-externalsrc`支持从OpenEmbedded构建系统外部的源代码构建软件。从外部源树构建软件意味着构建系统的正常获取、解压缩和补丁处理过程不会被使用。


By default, the OpenEmbedded build system uses the `S`{.interpreted-text role="term"} and `B`{.interpreted-text role="term"} variables to locate unpacked recipe source code and to build it, respectively. When your recipe inherits the `ref-classes-externalsrc`{.interpreted-text role="ref"} class, you use the `EXTERNALSRC`{.interpreted-text role="term"} and `EXTERNALSRC_BUILD`{.interpreted-text role="term"} variables to ultimately define `S`{.interpreted-text role="term"} and `B`{.interpreted-text role="term"}.

> 默认情况下，OpenEmbedded构建系统使用`S`{.interpreted-text role="term"}和`B`{.interpreted-text role="term"}变量来定位解压的配方源代码，并进行构建。当你的配方继承`ref-classes-externalsrc`{.interpreted-text role="ref"}类时，你可以使用`EXTERNALSRC`{.interpreted-text role="term"}和`EXTERNALSRC_BUILD`{.interpreted-text role="term"}变量来最终定义`S`{.interpreted-text role="term"}和`B`{.interpreted-text role="term"}。


By default, this class expects the source code to support recipe builds that use the `B`{.interpreted-text role="term"} variable to point to the directory in which the OpenEmbedded build system places the generated objects built from the recipes. By default, the `B`{.interpreted-text role="term"} directory is set to the following, which is separate from the source directory (`S`{.interpreted-text role="term"}):

> 默认情况下，该类期望源代码支持使用`B`{.interpreted-text role="term"}变量指向OpenEmbedded构建系统放置从配方生成的对象的目录的配方构建。默认情况下，`B`{.interpreted-text role="term"}目录设置为以下内容，该内容与源目录（`S`{.interpreted-text role="term"}）分开：

```
${WORKDIR}/${BPN}-{PV}/
```


See these variables for more information: `WORKDIR`{.interpreted-text role="term"}, `BPN`{.interpreted-text role="term"}, and `PV`{.interpreted-text role="term"},

> 查看更多信息，请参阅这些变量：`WORKDIR`{.interpreted-text role="term"}、`BPN`{.interpreted-text role="term"}和`PV`{.interpreted-text role="term"}。


For more information on the `ref-classes-externalsrc`{.interpreted-text role="ref"} class, see the comments in `meta/classes/externalsrc.bbclass` in the `Source Directory`{.interpreted-text role="term"}. For information on how to use the `ref-classes-externalsrc`{.interpreted-text role="ref"} class, see the \"`dev-manual/building:building software from an external source`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 要了解有关`ref-classes-externalsrc`{.interpreted-text role="ref"}类的更多信息，请参见源目录`Source Directory`{.interpreted-text role="term"}中的`meta/classes/externalsrc.bbclass`中的注释。要了解如何使用`ref-classes-externalsrc`{.interpreted-text role="ref"}类，请参见Yocto项目开发任务手册中的“`dev-manual/building:building software from an external source`{.interpreted-text role="ref"}”部分。

# `extrausers` {#ref-classes-extrausers}


The `ref-classes-extrausers`{.interpreted-text role="ref"} class allows additional user and group configuration to be applied at the image level. Inheriting this class either globally or from an image recipe allows additional user and group operations to be performed using the `EXTRA_USERS_PARAMS`{.interpreted-text role="term"} variable.

> 类`ref-classes-extrausers`允许在图像级别应用额外的用户和组配置。继承此类（全局或图像配方）允许使用变量`EXTRA_USERS_PARAMS`执行额外的用户和组操作。

::: note
::: title
Note
:::


The user and group operations added using the `ref-classes-extrausers`{.interpreted-text role="ref"} class are not tied to a specific recipe outside of the recipe for the image. Thus, the operations can be performed across the image as a whole. Use the `ref-classes-useradd`{.interpreted-text role="ref"} class to add user and group configuration to a specific recipe.

> 使用`ref-classes-extrausers`{.interpreted-text role="ref"}类添加的用户和组操作不与图像之外的特定配方有关。因此，可以在整个图像上执行这些操作。使用`ref-classes-useradd`{.interpreted-text role="ref"}类将用户和组配置添加到特定配方。
:::

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

> 这里是一个示例，它添加了两个名为“tester-jim”和“tester-sue”的用户，并分配了密码。首先，在主机上创建（转义）密码哈希：

```
printf "%q" $(mkpasswd -m sha256crypt tester01)
```

The resulting hash is set to a variable and used in `useradd` command parameters:

```
inherit extrausers
PASSWD = "\$X\$ABC123\$A-Long-Hash"
EXTRA_USERS_PARAMS = "\
    useradd -p '${PASSWD}' tester-jim; \
    useradd -p '${PASSWD}' tester-sue; \
    "
```

Finally, here is an example that sets the root password:

```
inherit extrausers
EXTRA_USERS_PARAMS = "\
    usermod -p '${PASSWD}' root; \
    "
```

::: note
::: title
Note
:::


From a security perspective, hardcoding a default password is not generally a good idea or even legal in some jurisdictions. It is recommended that you do not do this if you are building a production image.

> 从安全角度来看，硬编码默认密码通常不是一个好主意，甚至在某些司法管辖区是不合法的。如果您正在构建生产图像，建议您不要这样做。
:::

# `features_check` {#ref-classes-features_check}


The `ref-classes-features_check`{.interpreted-text role="ref"} class allows individual recipes to check for required and conflicting `DISTRO_FEATURES`{.interpreted-text role="term"}, `MACHINE_FEATURES`{.interpreted-text role="term"} or `COMBINED_FEATURES`{.interpreted-text role="term"}.

> 类`ref-classes-features_check`{.interpreted-text role="ref"}允许单个配方检查所需的和冲突的`DISTRO_FEATURES`{.interpreted-text role="term"}、`MACHINE_FEATURES`{.interpreted-text role="term"}或`COMBINED_FEATURES`{.interpreted-text role="term"}。

This class provides support for the following variables:

- `REQUIRED_DISTRO_FEATURES`{.interpreted-text role="term"}
- `CONFLICT_DISTRO_FEATURES`{.interpreted-text role="term"}
- `ANY_OF_DISTRO_FEATURES`{.interpreted-text role="term"}
- `REQUIRED_MACHINE_FEATURES`
- `CONFLICT_MACHINE_FEATURES`
- `ANY_OF_MACHINE_FEATURES`
- `REQUIRED_COMBINED_FEATURES`
- `CONFLICT_COMBINED_FEATURES`
- `ANY_OF_COMBINED_FEATURES`


If any conditions specified in the recipe using the above variables are not met, the recipe will be skipped, and if the build system attempts to build the recipe then an error will be triggered.

> 如果使用上述变量指定的配方中的任何条件未满足，则将跳过该配方，如果构建系统尝试构建配方，则会触发错误。

# `fontcache` {#ref-classes-fontcache}


The `ref-classes-fontcache`{.interpreted-text role="ref"} class generates the proper post-install and post-remove (postinst and postrm) scriptlets for font packages. These scriptlets call `fc-cache` (part of `Fontconfig`) to add the fonts to the font information cache. Since the cache files are architecture-specific, `fc-cache` runs using QEMU if the postinst scriptlets need to be run on the build host during image creation.

> `ref-classes-fontcache`{.interpreted-text role="ref"}类为字体包生成了正确的安装后和移除后（postinst和postrm）脚本。这些脚本调用`fc-cache`（`Fontconfig`的一部分）将字体添加到字体信息缓存中。由于缓存文件是特定于架构的，因此如果在图像创建期间需要在构建主机上运行postinst脚本，则会使用QEMU运行`fc-cache`。


If the fonts being installed are in packages other than the main package, set `FONT_PACKAGES`{.interpreted-text role="term"} to specify the packages containing the fonts.

> 如果安装的字体不在主包中，可以设置`FONT_PACKAGES`来指定包含字体的包。

# `fs-uuid` {#ref-classes-fs-uuid}


The `ref-classes-fs-uuid`{.interpreted-text role="ref"} class extracts UUID from `${``ROOTFS`{.interpreted-text role="term"}`}`, which must have been built by the time that this function gets called. The `ref-classes-fs-uuid`{.interpreted-text role="ref"} class only works on `ext` file systems and depends on `tune2fs`.

> 类ref-classes-fs-uuid{.interpreted-text role="ref"}从${``ROOTFS`{.interpreted-text role="term"}`}中提取UUID，这必须在调用此函数之前构建。类ref-classes-fs-uuid{.interpreted-text role="ref"}只适用于ext文件系统，并依赖于tune2fs。

# `gconf` {#ref-classes-gconf}


The `ref-classes-gconf`{.interpreted-text role="ref"} class provides common functionality for recipes that need to install GConf schemas. The schemas will be put into a separate package (`${``PN`{.interpreted-text role="term"}`}-gconf`) that is created automatically when this class is inherited. This package uses the appropriate post-install and post-remove (postinst/postrm) scriptlets to register and unregister the schemas in the target image.

> 该ref-classes-gconf类为需要安装GConf架构的配方提供了公共功能。 架构将被放入单独的包（$ {PN}-gconf）中，在继承此类时将自动创建该包。 此包使用适当的post-install和post-remove（postinst / postrm）脚本片段来在目标映像中注册和取消注册架构。

# `gettext` {#ref-classes-gettext}


The `ref-classes-gettext`{.interpreted-text role="ref"} class provides support for building software that uses the GNU `gettext` internationalization and localization system. All recipes building software that use `gettext` should inherit this class.

> 类`ref-classes-gettext`{.interpreted-text role="ref"}提供支持，用于构建使用GNU `gettext`国际化和本地化系统的软件。所有使用`gettext`构建软件的配方都应该继承这个类。

# `github-releases` {#ref-classes-github-releases}


For recipes that fetch release tarballs from github, the `ref-classes-github-releases`{.interpreted-text role="ref"} class sets up a standard way for checking available upstream versions (to support `devtool upgrade` and the Automated Upgrade Helper (AUH)).

> 对于从Github获取发布tarball的食谱，`ref-classes-github-releases`{.interpreted-text role="ref"}类建立了一种标准方法来检查可用的上游版本（以支持`devtool upgrade`和自动升级助手（AUH））。


To use it, add \"`ref-classes-github-releases`{.interpreted-text role="ref"}\" to the inherit line in the recipe, and if the default value of `GITHUB_BASE_URI`{.interpreted-text role="term"} is not suitable, then set your own value in the recipe. You should then use `${GITHUB_BASE_URI}` in the value you set for `SRC_URI`{.interpreted-text role="term"} within the recipe.

> 要使用它，请在配方的 inherit 行中添加"ref-classes-github-releases"{.interpreted-text role="ref"}，如果`GITHUB_BASE_URI`{.interpreted-text role="term"}的默认值不合适，则在配方中设置自己的值。然后，您应该在配方中为`SRC_URI`{.interpreted-text role="term"}设置的值中使用`${GITHUB_BASE_URI}`。

# `gnomebase` {#ref-classes-gnomebase}


The `ref-classes-gnomebase`{.interpreted-text role="ref"} class is the base class for recipes that build software from the GNOME stack. This class sets `SRC_URI`{.interpreted-text role="term"} to download the source from the GNOME mirrors as well as extending `FILES`{.interpreted-text role="term"} with the typical GNOME installation paths.

> `ref-classes-gnomebase`{.interpreted-text role="ref"} 类是为构建来自 GNOME 堆栈的软件而创建的基类。该类将 `SRC_URI`{.interpreted-text role="term"} 设置为从 GNOME 镜像下载源代码，并使用典型的 GNOME 安装路径扩展 `FILES`{.interpreted-text role="term"}。

# `go` {#ref-classes-go}


The `ref-classes-go`{.interpreted-text role="ref"} class supports building Go programs. The behavior of this class is controlled by the mandatory `GO_IMPORT`{.interpreted-text role="term"} variable, and by the optional `GO_INSTALL`{.interpreted-text role="term"} and `GO_INSTALL_FILTEROUT`{.interpreted-text role="term"} ones.

> 类`ref-classes-go`{.interpreted-text role="ref"}支持构建Go程序。此类的行为由强制性变量`GO_IMPORT`{.interpreted-text role="term"}控制，以及可选的`GO_INSTALL`{.interpreted-text role="term"}和`GO_INSTALL_FILTEROUT`{.interpreted-text role="term"}变量。


To build a Go program with the Yocto Project, you can use the :yocto\_[git:%60go-helloworld_0.1.bb](git:%60go-helloworld_0.1.bb) \</poky/tree/meta/recipes-extended/go-examples/go-helloworld_0.1.bb\>\` recipe as an example.

> 要使用Yocto Project构建Go程序，您可以使用:yocto_[git:`go-helloworld_0.1.bb`](git:`go-helloworld_0.1.bb`)</poky/tree/meta/recipes-extended/go-examples/go-helloworld_0.1.bb>`食谱作为示例。

# `go-mod` {#ref-classes-go-mod}


The `ref-classes-go-mod`{.interpreted-text role="ref"} class allows to use Go modules, and inherits the `ref-classes-go`{.interpreted-text role="ref"} class.

> `ref-classes-go-mod`{.interpreted-text role="ref"}类允许使用Go模块，并继承`ref-classes-go`{.interpreted-text role="ref"}类。

See the associated `GO_WORKDIR`{.interpreted-text role="term"} variable.

# `gobject-introspection` {#ref-classes-gobject-introspection}


Provides support for recipes building software that supports GObject introspection. This functionality is only enabled if the \"gobject-introspection-data\" feature is in `DISTRO_FEATURES`{.interpreted-text role="term"} as well as \"qemu-usermode\" being in `MACHINE_FEATURES`{.interpreted-text role="term"}.

> 提供支持支持GObject内省的菜谱构建软件。只有当"gobject-introspection-data"特性在DISTRO_FEATURES中以及"qemu-usermode"在MACHINE_FEATURES中时，才启用此功能。

::: note
::: title
Note
:::


This functionality is `backfilled <ref-features-backfill>`{.interpreted-text role="ref"} by default and, if not applicable, should be disabled through `DISTRO_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"} or `MACHINE_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"}, respectively.

> 这个功能默认情况下是被补充的<ref-features-backfill>{.interpreted-text role="ref"}，如果不可用，应该通过`DISTRO_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"}或`MACHINE_FEATURES_BACKFILL_CONSIDERED`{.interpreted-text role="term"}来禁用。
:::

# `grub-efi` {#ref-classes-grub-efi}

The `ref-classes-grub-efi`{.interpreted-text role="ref"} class provides `grub-efi`-specific functions for building bootable images.

This class supports several variables:

- `INITRD`{.interpreted-text role="term"}: Indicates list of filesystem images to concatenate and use as an initial RAM disk (initrd) (optional).
- `ROOTFS`{.interpreted-text role="term"}: Indicates a filesystem image to include as the root filesystem (optional).
- `GRUB_GFXSERIAL`{.interpreted-text role="term"}: Set this to \"1\" to have graphics and serial in the boot menu.
- `LABELS`{.interpreted-text role="term"}: A list of targets for the automatic configuration.
- `APPEND`{.interpreted-text role="term"}: An override list of append strings for each `LABEL`.

- `GRUB_OPTS`{.interpreted-text role="term"}: Additional options to add to the configuration (optional). Options are delimited using semi-colon characters (`;`).

> `GRUB_OPTS`：添加到配置中的其他选项（可选）。选项使用分号字符（`;`）进行分隔。
- `GRUB_TIMEOUT`{.interpreted-text role="term"}: Timeout before executing the default `LABEL` (optional).

# `gsettings` {#ref-classes-gsettings}


The `ref-classes-gsettings`{.interpreted-text role="ref"} class provides common functionality for recipes that need to install GSettings (glib) schemas. The schemas are assumed to be part of the main package. Appropriate post-install and post-remove (postinst/postrm) scriptlets are added to register and unregister the schemas in the target image.

> 这个`ref-classes-gsettings`{.interpreted-text role="ref"}类提供了需要安装GSettings（glib）模式的食谱的常见功能。假设模式是主要软件包的一部分。会在目标图像中添加适当的安装后和删除后（postinst / postrm）脚本，以注册和取消注册模式。

# `gtk-doc` {#ref-classes-gtk-doc}


The `ref-classes-gtk-doc`{.interpreted-text role="ref"} class is a helper class to pull in the appropriate `gtk-doc` dependencies and disable `gtk-doc`.

> `ref-classes-gtk-doc`{.interpreted-text role="ref"}类是一个帮助类，用于拉取适当的`gtk-doc`依赖项并禁用`gtk-doc`。

# `gtk-icon-cache` {#ref-classes-gtk-icon-cache}


The `ref-classes-gtk-icon-cache`{.interpreted-text role="ref"} class generates the proper post-install and post-remove (postinst/postrm) scriptlets for packages that use GTK+ and install icons. These scriptlets call `gtk-update-icon-cache` to add the fonts to GTK+\'s icon cache. Since the cache files are architecture-specific, `gtk-update-icon-cache` is run using QEMU if the postinst scriptlets need to be run on the build host during image creation.

> 这个ref-classes-gtk-icon-cache类为使用GTK+并安装图标的软件包生成正确的安装后和卸载后（postinst/postrm）脚本。这些脚本调用gtk-update-icon-cache来将字体添加到GTK+的图标缓存中。由于缓存文件是特定于架构的，如果在创建映像期间需要在构建主机上运行postinst脚本，则使用QEMU运行gtk-update-icon-cache。

# `gtk-immodules-cache` {#ref-classes-gtk-immodules-cache}


The `ref-classes-gtk-immodules-cache`{.interpreted-text role="ref"} class generates the proper post-install and post-remove (postinst/postrm) scriptlets for packages that install GTK+ input method modules for virtual keyboards. These scriptlets call `gtk-update-icon-cache` to add the input method modules to the cache. Since the cache files are architecture-specific, `gtk-update-icon-cache` is run using QEMU if the postinst scriptlets need to be run on the build host during image creation.

> 这个类为安装GTK+虚拟键盘输入法模块的软件包生成正确的安装后和移除后（postinst / postrm）脚本。这些脚本将调用gtk-update-icon-cache来将输入法模块添加到缓存中。由于缓存文件是特定于架构的，如果在创建映像期间需要在构建主机上运行postinst脚本，则使用QEMU运行gtk-update-icon-cache。


If the input method modules being installed are in packages other than the main package, set `GTKIMMODULES_PACKAGES`{.interpreted-text role="term"} to specify the packages containing the modules.

> 如果安装的输入法模块不在主包中，请设置`GTKIMMODULES_PACKAGES`来指定包含模块的包。

# `gzipnative` {#ref-classes-gzipnative}


The `ref-classes-gzipnative`{.interpreted-text role="ref"} class enables the use of different native versions of `gzip` and `pigz` rather than the versions of these tools from the build host.

> 这个ref-classes-gzipnative类可以使用不同的本地版本的gzip和pigz，而不是来自构建主机的这些工具的版本。

# `icecc` {#ref-classes-icecc}


The `ref-classes-icecc`{.interpreted-text role="ref"} class supports [Icecream](https://github.com/icecc/icecream), which facilitates taking compile jobs and distributing them among remote machines.

> 这个ref-classes-icecc类支持Icecream（https://github.com/icecc/icecream），它可以方便地将编译任务分发到远程机器上。


The class stages directories with symlinks from `gcc` and `g++` to `icecc`, for both native and cross compilers. Depending on each configure or compile, the OpenEmbedded build system adds the directories at the head of the `PATH` list and then sets the `ICECC_CXX` and `ICECC_CC` variables, which are the paths to the `g++` and `gcc` compilers, respectively.

> 类阶段将`gcc`和`g++`目录与符号链接从`icecc`连接起来，无论是本地编译器还是跨编译器。根据每个配置或编译，OpenEmbedded构建系统将目录添加到`PATH`列表的开头，然后设置`ICECC_CXX`和`ICECC_CC`变量，分别是`g++`和`gcc`编译器的路径。


For the cross compiler, the class creates a `tar.gz` file that contains the Yocto Project toolchain and sets `ICECC_VERSION`, which is the version of the cross-compiler used in the cross-development toolchain, accordingly.

> 为了交叉编译器，该类创建一个包含Yocto项目工具链的`tar.gz`文件，并相应地设置`ICECC_VERSION`，这是交叉开发工具链中使用的交叉编译器的版本。


The class handles all three different compile stages (i.e native, cross-kernel and target) and creates the necessary environment `tar.gz` file to be used by the remote machines. The class also supports SDK generation.

> 这个类处理所有三个不同的编译阶段（即本地、跨内核和目标），并创建必要的环境`tar.gz`文件，以供远程机器使用。该类还支持SDK生成。


If `ICECC_PATH`{.interpreted-text role="term"} is not set in your `local.conf` file, then the class tries to locate the `icecc` binary using `which`. If `ICECC_ENV_EXEC`{.interpreted-text role="term"} is set in your `local.conf` file, the variable should point to the `icecc-create-env` script provided by the user. If you do not point to a user-provided script, the build system uses the default script provided by the recipe :oe\_[git:%60icecc-create-env_0.1.bb](git:%60icecc-create-env_0.1.bb) \</openembedded-core/tree/meta/recipes-devtools/icecc-create-env/icecc-create-env_0.1.bb\>\`.

> 如果您的local.conf文件中没有设置ICECC_PATH，那么该类将尝试使用which来定位icecc二进制文件。如果您的local.conf文件中设置了ICECC_ENV_EXEC，则该变量应指向用户提供的icecc-create-env脚本。如果您不指向用户提供的脚本，则构建系统将使用食谱提供的默认脚本：oe_[git:`icecc-create-env_0.1.bb`](git:`icecc-create-env_0.1.bb`)</openembedded-core/tree/meta/recipes-devtools/icecc-create-env/icecc-create-env_0.1.bb>`.

::: note
::: title
Note
:::

This script is a modified version and not the one that comes with `icecream`.
:::


If you do not want the Icecream distributed compile support to apply to specific recipes or classes, you can ask them to be ignored by Icecream by listing the recipes and classes using the `ICECC_RECIPE_DISABLE`{.interpreted-text role="term"} and `ICECC_CLASS_DISABLE`{.interpreted-text role="term"} variables, respectively, in your `local.conf` file. Doing so causes the OpenEmbedded build system to handle these compilations locally.

> 如果您不希望Icecream分发编译支持应用于特定的配方或类别，您可以通过在本地.conf文件中列出使用`ICECC_RECIPE_DISABLE`和`ICECC_CLASS_DISABLE`变量的配方和类别，要求Icecream忽略它们。这样做会导致OpenEmbedded构建系统在本地处理这些编译。


Additionally, you can list recipes using the `ICECC_RECIPE_ENABLE`{.interpreted-text role="term"} variable in your `local.conf` file to force `icecc` to be enabled for recipes using an empty `PARALLEL_MAKE`{.interpreted-text role="term"} variable.

> 此外，您可以在`local.conf`文件中使用`ICECC_RECIPE_ENABLE`变量列出食谱，以强制使用空`PARALLEL_MAKE`变量启用`icecc`。


Inheriting the `ref-classes-icecc`{.interpreted-text role="ref"} class changes all sstate signatures. Consequently, if a development team has a dedicated build system that populates `SSTATE_MIRRORS`{.interpreted-text role="term"} and they want to reuse sstate from `SSTATE_MIRRORS`{.interpreted-text role="term"}, then all developers and the build system need to either inherit the `ref-classes-icecc`{.interpreted-text role="ref"} class or nobody should.

> 继承`ref-classes-icecc`{.interpreted-text role="ref"}类会改变所有sstate签名。因此，如果开发团队拥有一个专用的构建系统，该系统会填充`SSTATE_MIRRORS`{.interpreted-text role="term"}，并且他们想要重用`SSTATE_MIRRORS`{.interpreted-text role="term"}中的sstate，那么所有开发人员和构建系统都需要继承`ref-classes-icecc`{.interpreted-text role="ref"}类，或者没有人继承。


At the distribution level, you can inherit the `ref-classes-icecc`{.interpreted-text role="ref"} class to be sure that all builders start with the same sstate signatures. After inheriting the class, you can then disable the feature by setting the `ICECC_DISABLED`{.interpreted-text role="term"} variable to \"1\" as follows:

> 在分发层面，您可以继承`ref-classes-icecc`{.interpreted-text role="ref"}类以确保所有构建器都以相同的sstate签名开始。继承该类后，您可以通过将`ICECC_DISABLED`{.interpreted-text role="term"}变量设置为“1”来禁用该功能，如下所示：

```
INHERIT_DISTRO:append = " icecc"
ICECC_DISABLED ??= "1"
```


This practice makes sure everyone is using the same signatures but also requires individuals that do want to use Icecream to enable the feature individually as follows in your `local.conf` file:

> 这种做法可以确保每个人使用相同的签名，但也要求那些想使用Icecream的个人在本地的`local.conf`文件中单独启用该功能：

```
ICECC_DISABLED = ""
```

# `image` {#ref-classes-image}


The `ref-classes-image`{.interpreted-text role="ref"} class helps support creating images in different formats. First, the root filesystem is created from packages using one of the `rootfs*.bbclass` files (depending on the package format used) and then one or more image files are created.

> `ref-classes-image`类可帮助支持创建不同格式的图像。首先，使用`rootfs*.bbclass`文件之一（取决于所使用的包格式）从包创建根文件系统，然后创建一个或多个图像文件。

- The `IMAGE_FSTYPES`{.interpreted-text role="term"} variable controls the types of images to generate.
- The `IMAGE_INSTALL`{.interpreted-text role="term"} variable controls the list of packages to install into the image.


For information on customizing images, see the \"`dev-manual/customizing-images:customizing images`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual. For information on how images are created, see the \"`overview-manual/concepts:images`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> 要了解自定义图像的信息，请参阅Yocto项目开发任务手册中的“dev-manual/customizing-images：自定义图像”部分。要了解图像是如何创建的，请参阅Yocto项目概述和概念手册中的“overview-manual/concepts：图像”部分。

# `image-buildinfo` {#ref-classes-image-buildinfo}


The `ref-classes-image-buildinfo`{.interpreted-text role="ref"} class writes a plain text file containing build information to the target filesystem at `${sysconfdir}/buildinfo` by default (as specified by `IMAGE_BUILDINFO_FILE`{.interpreted-text role="term"}). This can be useful for manually determining the origin of any given image. It writes out two sections:

> `ref-classes-image-buildinfo`{.interpreted-text role="ref"} 类默认情况下会将包含构建信息的纯文本文件写入目标文件系统 `${sysconfdir}/buildinfo`（按照 `IMAGE_BUILDINFO_FILE`{.interpreted-text role="term"} 指定）。这可以有助于手动确定任何给定图像的来源。它会输出两个部分：


1. \`Build Configuration\`: a list of variables and their values (specified by `IMAGE_BUILDINFO_VARS`{.interpreted-text role="term"}, which defaults to `DISTRO`{.interpreted-text role="term"} and `DISTRO_VERSION`{.interpreted-text role="term"})

> `1. 构建配置：一组变量及其值（由默认值为DISTRO和DISTRO_VERSION的IMAGE_BUILDINFO_VARS指定）。`
2. \`Layer Revisions\`: the revisions of all of the layers used in the build.


Additionally, when building an SDK it will write the same contents to `/buildinfo` by default (as specified by `SDK_BUILDINFO_FILE`{.interpreted-text role="term"}).

> 此外，默认情况下，构建 SDK 时会按照 `SDK_BUILDINFO_FILE`（指定）将相同内容写入 `/buildinfo`。

# `image_types` {#ref-classes-image_types}


The `ref-classes-image_types`{.interpreted-text role="ref"} class defines all of the standard image output types that you can enable through the `IMAGE_FSTYPES`{.interpreted-text role="term"} variable. You can use this class as a reference on how to add support for custom image output types.

> 类`ref-classes-image_types`{.interpreted-text role="ref"}定义了所有可以通过变量`IMAGE_FSTYPES`{.interpreted-text role="term"}启用的标准图像输出类型。您可以使用此类作为如何添加自定义图像输出类型的参考。


By default, the `ref-classes-image`{.interpreted-text role="ref"} class automatically enables the `ref-classes-image_types`{.interpreted-text role="ref"} class. The `ref-classes-image`{.interpreted-text role="ref"} class uses the `IMGCLASSES` variable as follows:

> 默认情况下，`ref-classes-image`{.interpreted-text role="ref"} 类会自动启用 `ref-classes-image_types`{.interpreted-text role="ref"} 类。`ref-classes-image`{.interpreted-text role="ref"} 类使用 `IMGCLASSES` 变量如下：

```
IMGCLASSES = "rootfs_${IMAGE_PKGTYPE} image_types ${IMAGE_CLASSES}"
IMGCLASSES += "${@['populate_sdk_base', 'populate_sdk_ext']['linux' in d.getVar("SDK_OS")]}"
IMGCLASSES += "${@bb.utils.contains_any('IMAGE_FSTYPES', 'live iso hddimg', 'image-live', '', d)}"
IMGCLASSES += "${@bb.utils.contains('IMAGE_FSTYPES', 'container', 'image-container', '', d)}"
IMGCLASSES += "image_types_wic"
IMGCLASSES += "rootfs-postcommands"
IMGCLASSES += "image-postinst-intercepts"
inherit ${IMGCLASSES}
```

The `ref-classes-image_types`{.interpreted-text role="ref"} class also handles conversion and compression of images.

::: note
::: title
Note
:::


To build a VMware VMDK image, you need to add \"wic.vmdk\" to `IMAGE_FSTYPES`{.interpreted-text role="term"}. This would also be similar for Virtual Box Virtual Disk Image (\"vdi\") and QEMU Copy On Write Version 2 (\"qcow2\") images.

> 要构建VMware VMDK镜像，您需要将“wic.vmdk”添加到IMAGE_FSTYPES。这对于Virtual Box虚拟磁盘镜像（“vdi”）和QEMU复制写入版本2（“qcow2”）镜像也是相似的。
:::

# `image-live` {#ref-classes-image-live}


This class controls building \"live\" (i.e. HDDIMG and ISO) images. Live images contain syslinux for legacy booting, as well as the bootloader specified by `EFI_PROVIDER`{.interpreted-text role="term"} if `MACHINE_FEATURES`{.interpreted-text role="term"} contains \"efi\".

> 这个类控制创建“实时”（即HDDIMG和ISO）映像。Live映像包含用于传统引导的syslinux，以及由`EFI_PROVIDER`{.interpreted-text role="term"}指定的引导程序，如果`MACHINE_FEATURES`{.interpreted-text role="term"}包含“efi”。

Normally, you do not use this class directly. Instead, you add \"live\" to `IMAGE_FSTYPES`{.interpreted-text role="term"}.

# `insane` {#ref-classes-insane}


The `ref-classes-insane`{.interpreted-text role="ref"} class adds a step to the package generation process so that output quality assurance checks are generated by the OpenEmbedded build system. A range of checks are performed that check the build\'s output for common problems that show up during runtime. Distribution policy usually dictates whether to include this class.

> 类ref-classes-insane{.interpreted-text role="ref"}增加了一个步骤，以便由OpenEmbedded构建系统生成输出质量保证检查。执行一系列检查，以检查构建的输出是否存在运行时出现的常见问题。是否包括此类通常由分发策略决定。


You can configure the sanity checks so that specific test failures either raise a warning or an error message. Typically, failures for new tests generate a warning. Subsequent failures for the same test would then generate an error message once the metadata is in a known and good condition. See the \"`/ref-manual/qa-checks`{.interpreted-text role="doc"}\" Chapter for a list of all the warning and error messages you might encounter using a default configuration.

> 你可以配置健全检查，使特定的测试失败可以产生警告或错误消息。通常，新测试的失败会产生警告。一旦元数据处于已知和良好的状态，后续相同测试的失败将产生错误消息。有关使用默认配置可能遇到的所有警告和错误消息的列表，请参阅“/ref-manual/qa-checks”章节。


Use the `WARN_QA`{.interpreted-text role="term"} and `ERROR_QA`{.interpreted-text role="term"} variables to control the behavior of these checks at the global level (i.e. in your custom distro configuration). However, to skip one or more checks in recipes, you should use `INSANE_SKIP`{.interpreted-text role="term"}. For example, to skip the check for symbolic link `.so` files in the main package of a recipe, add the following to the recipe. You need to realize that the package name override, in this example `${PN}`, must be used:

> 使用`WARN_QA`{.interpreted-text role="term"}和`ERROR_QA`{.interpreted-text role="term"}变量控制这些检查的全局行为（即在您的自定义发行版配置中）。但是，要跳过一个或多个菜谱中的检查，您应该使用`INSANE_SKIP`{.interpreted-text role="term"}。例如，要跳过菜谱的主软件包中的符号链接`.so`文件的检查，请添加以下内容到菜谱中。您需要意识到，必须使用此示例中的软件包名称覆盖，即`${PN}`：

```
INSANE_SKIP:${PN} += "dev-so"
```


Please keep in mind that the QA checks are meant to detect real or potential problems in the packaged output. So exercise caution when disabling these checks.

> 请记住，QA检查旨在检测打包输出中的实际或潜在问题。因此，在禁用这些检查时要谨慎行事。

Here are the tests you can list with the `WARN_QA`{.interpreted-text role="term"} and `ERROR_QA`{.interpreted-text role="term"} variables:


- `already-stripped:` Checks that produced binaries have not already been stripped prior to the build system extracting debug symbols. It is common for upstream software projects to default to stripping debug symbols for output binaries. In order for debugging to work on the target using `-dbg` packages, this stripping must be disabled.

> 检查生成的二进制文件在构建系统提取调试符号之前是否已被剥离。上游软件项目默认剥离调试符号以便输出二进制文件。为了使使用“-dbg”软件包在目标上调试工作，必须禁用此剥离。

- `arch:` Checks the Executable and Linkable Format (ELF) type, bit size, and endianness of any binaries to ensure they match the target architecture. This test fails if any binaries do not match the type since there would be an incompatibility. The test could indicate that the wrong compiler or compiler options have been used. Sometimes software, like bootloaders, might need to bypass this check.

> - arch：检查可执行文件和可链接格式（ELF）的类型、位大小和字节序，以确保它们与目标架构匹配。如果任何二进制文件不匹配类型，则此测试将失败，因为会出现不兼容性。该测试可能表明使用了错误的编译器或编译器选项。有时候软件，比如引导加载程序，可能需要绕过此检查。

- `buildpaths:` Checks for paths to locations on the build host inside the output files. Not only can these leak information about the build environment, they also hinder binary reproducibility.

> 检查输出文件中构建主机位置的路径。这些不仅可以泄漏有关构建环境的信息，还会妨碍二进制可重现性。

- `build-deps:` Determines if a build-time dependency that is specified through `DEPENDS`{.interpreted-text role="term"}, explicit `RDEPENDS`{.interpreted-text role="term"}, or task-level dependencies exists to match any runtime dependency. This determination is particularly useful to discover where runtime dependencies are detected and added during packaging. If no explicit dependency has been specified within the metadata, at the packaging stage it is too late to ensure that the dependency is built, and thus you can end up with an error when the package is installed into the image during the `ref-tasks-rootfs`{.interpreted-text role="ref"} task because the auto-detected dependency was not satisfied. An example of this would be where the `ref-classes-update-rc.d`{.interpreted-text role="ref"} class automatically adds a dependency on the `initscripts-functions` package to packages that install an initscript that refers to `/etc/init.d/functions`. The recipe should really have an explicit `RDEPENDS`{.interpreted-text role="term"} for the package in question on `initscripts-functions` so that the OpenEmbedded build system is able to ensure that the `initscripts` recipe is actually built and thus the `initscripts-functions` package is made available.

> build-deps：通过DEPENDS、显式RDEPENDS或任务级别的依赖关系确定是否存在运行时依赖关系的构建时依赖关系。这种确定特别有用，可以发现运行时依赖关系是如何在打包过程中检测和添加的。如果元数据中没有明确指定依赖关系，在打包阶段已经太晚了，无法确保依赖项被构建，因此在ref-tasks-rootfs任务中将该软件包安装到映像中时，可能会因自动检测到的依赖关系未满足而出现错误。例如，ref-classes-update-rc.d类会自动为安装引用/etc/init.d/functions的initscript的软件包添加对initscripts-functions软件包的依赖关系。该配方应该真正对initscripts软件包有一个明确的RDEPENDS，以便OpenEmbedded构建系统能够确保initscripts配方实际上被构建，因此initscripts-functions软件包可用。

- `configure-gettext:` Checks that if a recipe is building something that uses automake and the automake files contain an `AM_GNU_GETTEXT` directive, that the recipe also inherits the `ref-classes-gettext`{.interpreted-text role="ref"} class to ensure that gettext is available during the build.

> 检查如果一个食谱正在构建使用自动化的东西，并且自动化文件包含一个`AM_GNU_GETTEXT`指令，那么该食谱也会继承`ref-classes-gettext`{.interpreted-text role="ref"}类，以确保在构建期间可以使用gettext。

- `compile-host-path:` Checks the `ref-tasks-compile`{.interpreted-text role="ref"} log for indications that paths to locations on the build host were used. Using such paths might result in host contamination of the build output.

> 检查“ref-tasks-compile”日志以查看是否使用了构建主机上位置的路径。使用此类路径可能会导致构建输出受到主机污染。
- `debug-deps:` Checks that all packages except `-dbg` packages do not depend on `-dbg` packages, which would cause a packaging bug.

- `debug-files:` Checks for `.debug` directories in anything but the `-dbg` package. The debug files should all be in the `-dbg` package. Thus, anything packaged elsewhere is incorrect packaging.

> 检查除-dbg包外的任何内容中的.debug目录。调试文件应全部包含在-dbg包中。因此，其他包装的任何内容都是不正确的包装。

- `dep-cmp:` Checks for invalid version comparison statements in runtime dependency relationships between packages (i.e. in `RDEPENDS`{.interpreted-text role="term"}, `RRECOMMENDS`{.interpreted-text role="term"}, `RSUGGESTS`{.interpreted-text role="term"}, `RPROVIDES`{.interpreted-text role="term"}, `RREPLACES`{.interpreted-text role="term"}, and `RCONFLICTS`{.interpreted-text role="term"} variable values). Any invalid comparisons might trigger failures or undesirable behavior when passed to the package manager.

> - dep-cmp：检查在包之间的运行时依赖关系（即在RDEPENDS，RRECOMMENDS，RSUGGESTS，RPROVIDES，RREPLACES和RCONFLICTS变量值中）中的无效版本比较语句。任何无效的比较可能在传递给软件包管理器时触发失败或不希望的行为。

- `desktop:` Runs the `desktop-file-validate` program against any `.desktop` files to validate their contents against the specification for `.desktop` files.

> 运行`desktop-file-validate`程序，对任何`.desktop`文件进行验证，以确保其内容符合`.desktop`文件的规范。
- `dev-deps:` Checks that all packages except `-dev` or `-staticdev` packages do not depend on `-dev` packages, which would be a packaging bug.

- `dev-so:` Checks that the `.so` symbolic links are in the `-dev` package and not in any of the other packages. In general, these symlinks are only useful for development purposes. Thus, the `-dev` package is the correct location for them. In very rare cases, such as dynamically loaded modules, these symlinks are needed instead in the main package.

> 检查`.so`符号链接是否在`-dev`包中，而不是在其他任何包中。一般来说，这些符号链接只有在开发过程中才有用。因此，`-dev`包是正确的位置。在极少数情况下，比如动态加载模块，这些符号链接需要放在主包中。

- `empty-dirs:` Checks that packages are not installing files to directories that are normally expected to be empty (such as `/tmp`) The list of directories that are checked is specified by the `QA_EMPTY_DIRS`{.interpreted-text role="term"} variable.

> 检查包是否安装到通常预期为空的目录（例如`/tmp`），要检查的目录由`QA_EMPTY_DIRS`变量指定。

- `file-rdeps:` Checks that file-level dependencies identified by the OpenEmbedded build system at packaging time are satisfied. For example, a shell script might start with the line `#!/bin/bash`. This line would translate to a file dependency on `/bin/bash`. Of the three package managers that the OpenEmbedded build system supports, only RPM directly handles file-level dependencies, resolving them automatically to packages providing the files. However, the lack of that functionality in the other two package managers does not mean the dependencies do not still need resolving. This QA check attempts to ensure that explicitly declared `RDEPENDS`{.interpreted-text role="term"} exist to handle any file-level dependency detected in packaged files.

> file-rdeps检查OpenEmbedded构建系统在打包时识别的文件级依赖关系是否得到满足。例如，shell脚本可能以`#!/bin/bash`开头。这一行会转换为对`/bin/bash`的文件依赖关系。OpenEmbedded构建系统支持的三个包管理器中，只有RPM直接处理文件级依赖关系，自动解析为提供文件的软件包。但是，其他两个包管理器缺乏这种功能并不意味着这些依赖关系仍然不需要解析。此QA检查试图确保显式声明的`RDEPENDS`存在以处理打包文件中检测到的任何文件级依赖关系。
- `files-invalid:` Checks for `FILES`{.interpreted-text role="term"} variable values that contain \"//\", which is invalid.

- `host-user-contaminated:` Checks that no package produced by the recipe contains any files outside of `/home` with a user or group ID that matches the user running BitBake. A match usually indicates that the files are being installed with an incorrect UID/GID, since target IDs are independent from host IDs. For additional information, see the section describing the `ref-tasks-install`{.interpreted-text role="ref"} task.

> 检查由食谱生成的任何包是否包含位于/home之外且具有与运行BitBake的用户匹配的用户或组ID的文件。通常，匹配表明文件正在使用不正确的UID / GID进行安装，因为目标ID与主机ID独立。有关其他信息，请参见描述“ref-tasks-install”任务的部分。

- `incompatible-license:` Report when packages are excluded from being created due to being marked with a license that is in `INCOMPATIBLE_LICENSE`{.interpreted-text role="term"}.

> 报告当标记有不兼容许可证的包被排除在外时，因为它们被标记为`INCOMPATIBLE_LICENSE`。

- `install-host-path:` Checks the `ref-tasks-install`{.interpreted-text role="ref"} log for indications that paths to locations on the build host were used. Using such paths might result in host contamination of the build output.

> 检查'ref-tasks-install'日志以查看是否使用了构建主机上的位置的路径。使用这样的路径可能会导致构建输出的主机污染。

- `installed-vs-shipped:` Reports when files have been installed within `ref-tasks-install`{.interpreted-text role="ref"} but have not been included in any package by way of the `FILES`{.interpreted-text role="term"} variable. Files that do not appear in any package cannot be present in an image later on in the build process. Ideally, all installed files should be packaged or not installed at all. These files can be deleted at the end of `ref-tasks-install`{.interpreted-text role="ref"} if the files are not needed in any package.

> 报告何时文件已在`ref-tasks-install`{.interpreted-text role="ref"}中安装，但未通过`FILES`{.interpreted-text role="term"}变量包含在任何软件包中。不出现在任何软件包中的文件不能在构建过程的后期出现在图像中。理想情况下，所有安装的文件都应该被打包或者根本不安装。如果文件不需要在任何软件包中，可以在`ref-tasks-install`{.interpreted-text role="ref"}结束时删除这些文件。

- `invalid-chars:` Checks that the recipe metadata variables `DESCRIPTION`{.interpreted-text role="term"}, `SUMMARY`{.interpreted-text role="term"}, `LICENSE`{.interpreted-text role="term"}, and `SECTION`{.interpreted-text role="term"} do not contain non-UTF-8 characters. Some package managers do not support such characters.

> 检查食谱元数据变量`DESCRIPTION`{.interpreted-text role="term"}, `SUMMARY`{.interpreted-text role="term"}, `LICENSE`{.interpreted-text role="term"}, 和 `SECTION`{.interpreted-text role="term"}不包含非UTF-8字符。一些软件包管理器不支持这样的字符。

- `invalid-packageconfig:` Checks that no undefined features are being added to `PACKAGECONFIG`{.interpreted-text role="term"}. For example, any name \"foo\" for which the following form does not exist:

> 检查PACKAGECONFIG中是否添加了未定义的特性。例如，任何名称为“foo”的特性，如果不存在以下形式：

  ```
  PACKAGECONFIG[foo] = "..."
  ```

- `la:` Checks `.la` files for any `TMPDIR`{.interpreted-text role="term"} paths. Any `.la` file containing these paths is incorrect since `libtool` adds the correct sysroot prefix when using the files automatically itself.

> 检查`.la`文件中是否有`TMPDIR`路径。如果`.la`文件中包含这些路径，则是不正确的，因为当使用文件时，`libtool`会自动添加正确的系统根前缀。

- `ldflags:` Ensures that the binaries were linked with the `LDFLAGS`{.interpreted-text role="term"} options provided by the build system. If this test fails, check that the `LDFLAGS`{.interpreted-text role="term"} variable is being passed to the linker command.

> `ldflags：`确保二进制文件使用构建系统提供的`LDFLAGS`选项进行链接。如果测试失败，请检查`LDFLAGS`变量是否被传递给链接器命令。

- `libdir:` Checks for libraries being installed into incorrect (possibly hardcoded) installation paths. For example, this test will catch recipes that install `/lib/bar.so` when `${base_libdir}` is \"lib32\". Another example is when recipes install `/usr/lib64/foo.so` when `${libdir}` is \"/usr/lib\".

> - `libdir`：检查是否将库安装到不正确的（可能是硬编码的）安装路径中。例如，当`${base_libdir}`为“lib32”时，此测试将捕获安装`/lib/bar.so`的配方。另一个例子是当`${libdir}`为“/usr/lib”时，配方安装`/usr/lib64/foo.so`。

- `libexec:` Checks if a package contains files in `/usr/libexec`. This check is not performed if the `libexecdir` variable has been set explicitly to `/usr/libexec`.

> 检查软件包是否包含`/usr/libexec`中的文件。如果`libexecdir`变量明确设置为`/usr/libexec`，则不执行此检查。

- `mime:` Check that if a package contains mime type files (`.xml` files in `${datadir}/mime/packages`) that the recipe also inherits the `ref-classes-mime`{.interpreted-text role="ref"} class in order to ensure that these get properly installed.

> 检查如果一个软件包包含MIME类型文件（在`${datadir}/mime/packages`中的`.xml`文件），那么这个食谱也应该继承`ref-classes-mime`{.interpreted-text role="ref"} 类，以确保这些文件能够正确安装。

- `mime-xdg:` Checks that if a package contains a .desktop file with a \'MimeType\' key present, that the recipe inherits the `ref-classes-mime-xdg`{.interpreted-text role="ref"} class that is required in order for that to be activated.

> 检查软件包是否包含具有'MimeType'键的.desktop文件，如果有，则软件包需要继承`ref-classes-mime-xdg`{.interpreted-text role="ref"}类，以便激活该文件。

- `missing-update-alternatives:` Check that if a recipe sets the `ALTERNATIVE`{.interpreted-text role="term"} variable that the recipe also inherits `ref-classes-update-alternatives`{.interpreted-text role="ref"} such that the alternative will be correctly set up.

> 检查配方是否设置了ALTERNATIVE变量，如果设置了，配方也要继承ref-classes-update-alternatives，以便正确设置替代方案。

- `packages-list:` Checks for the same package being listed multiple times through the `PACKAGES`{.interpreted-text role="term"} variable value. Installing the package in this manner can cause errors during packaging.

> 检查通过`PACKAGES`变量值列出的多次列出的相同软件包。以这种方式安装软件包可能会导致打包时出错。
- `patch-fuzz:` Checks for fuzz in patch files that may allow them to apply incorrectly if the underlying code changes.
- `patch-status-core:` Checks that the Upstream-Status is specified and valid in the headers of patches for recipes in the OE-Core layer.
- `patch-status-noncore:` Checks that the Upstream-Status is specified and valid in the headers of patches for recipes in layers other than OE-Core.
- `perllocalpod:` Checks for `perllocal.pod` being erroneously installed and packaged by a recipe.
- `perm-config:` Reports lines in `fs-perms.txt` that have an invalid format.
- `perm-line:` Reports lines in `fs-perms.txt` that have an invalid format.
- `perm-link:` Reports lines in `fs-perms.txt` that specify \'link\' where the specified target already exists.
- `perms:` Currently, this check is unused but reserved.

- `pkgconfig:` Checks `.pc` files for any `TMPDIR`{.interpreted-text role="term"}/`WORKDIR`{.interpreted-text role="term"} paths. Any `.pc` file containing these paths is incorrect since `pkg-config` itself adds the correct sysroot prefix when the files are accessed.

> 检查.pc文件是否包含TMPDIR/WORKDIR路径。任何包含这些路径的.pc文件都是不正确的，因为当文件被访问时，pkg-config自身会添加正确的sysroot前缀。

- `pkgname:` Checks that all packages in `PACKAGES`{.interpreted-text role="term"} have names that do not contain invalid characters (i.e. characters other than 0-9, a-z, ., +, and -).

> `pkgname:` 检查 `PACKAGES`{.interpreted-text role="term"} 中的所有包的名称是否不包含无效字符（即除 0-9、a-z、.、+ 和 - 之外的字符）。

- `pkgv-undefined:` Checks to see if the `PKGV`{.interpreted-text role="term"} variable is undefined during `ref-tasks-package`{.interpreted-text role="ref"}.

> 检查在`ref-tasks-package`{.interpreted-text role="ref"}期间`PKGV`{.interpreted-text role="term"}变量是否未定义。

- `pkgvarcheck:` Checks through the variables `RDEPENDS`{.interpreted-text role="term"}, `RRECOMMENDS`{.interpreted-text role="term"}, `RSUGGESTS`{.interpreted-text role="term"}, `RCONFLICTS`{.interpreted-text role="term"}, `RPROVIDES`{.interpreted-text role="term"}, `RREPLACES`{.interpreted-text role="term"}, `FILES`{.interpreted-text role="term"}, `ALLOW_EMPTY`{.interpreted-text role="term"}, `pkg_preinst`, `pkg_postinst`, `pkg_prerm` and `pkg_postrm`, and reports if there are variable sets that are not package-specific. Using these variables without a package suffix is bad practice, and might unnecessarily complicate dependencies of other packages within the same recipe or have other unintended consequences.

> pkgvarcheck：检查RDEPENDS，RRECOMMENDS，RSUGGESTS，RCONFLICTS，RPROVIDES，RREPLACES，FILES，ALLOW_EMPTY，pkg_preinst，pkg_postinst，pkg_prerm和pkg_postrm变量，并报告是否存在不特定于包的变量集。使用这些没有包后缀的变量是不良的做法，可能会无意中使同一配方中其他软件包的依赖关系变得复杂，或产生其他意外后果。

- `pn-overrides:` Checks that a recipe does not have a name (`PN`{.interpreted-text role="term"}) value that appears in `OVERRIDES`{.interpreted-text role="term"}. If a recipe is named such that its `PN`{.interpreted-text role="term"} value matches something already in `OVERRIDES`{.interpreted-text role="term"} (e.g. `PN`{.interpreted-text role="term"} happens to be the same as `MACHINE`{.interpreted-text role="term"} or `DISTRO`{.interpreted-text role="term"}), it can have unexpected consequences. For example, assignments such as `FILES:${PN} = "xyz"` effectively turn into `FILES = "xyz"`.

> 检查菜谱是否没有名称（PN）值出现在OVERRIDES中。如果菜谱的PN值与OVERRIDES中的内容（例如MACHINE或DISTRO）匹配，可能会产生意想不到的后果。例如，FILES: ${PN} = "xyz"的赋值实际上变成了FILES = "xyz"。

- `rpaths:` Checks for rpaths in the binaries that contain build system paths such as `TMPDIR`{.interpreted-text role="term"}. If this test fails, bad `-rpath` options are being passed to the linker commands and your binaries have potential security issues.

> 检查二进制文件中是否包含构建系统路径（如`TMPDIR`）。如果此测试失败，则会向链接器命令传递不良的`-rpath`选项，您的二进制文件可能存在安全问题。

- `shebang-size:` Check that the shebang line (`#!` in the first line) in a packaged script is not longer than 128 characters, which can cause an error at runtime depending on the operating system.

> 检查打包脚本中的shebang行（第一行的`#!`）是否不超过128个字符，否则可能会根据操作系统出现运行时错误。
- `split-strip:` Reports that splitting or stripping debug symbols from binaries has failed.
- `staticdev:` Checks for static library files (`*.a`) in non-`staticdev` packages.

- `src-uri-bad:` Checks that the `SRC_URI`{.interpreted-text role="term"} value set by a recipe does not contain a reference to `${PN}` (instead of the correct `${BPN}`) nor refers to unstable Github archive tarballs.

> 检查由配方设定的SRC_URI值不含有引用${PN}（而不是正确的${BPN}），也不指向不稳定的Github存档tarballs。

- `symlink-to-sysroot:` Checks for symlinks in packages that point into `TMPDIR`{.interpreted-text role="term"} on the host. Such symlinks will work on the host, but are clearly invalid when running on the target.

> 检查包中指向主机上的TMPDIR的符号链接。这样的符号链接在主机上可以工作，但在目标上显然是无效的。

- `textrel:` Checks for ELF binaries that contain relocations in their `.text` sections, which can result in a performance impact at runtime. See the explanation for the `ELF binary` message in \"`/ref-manual/qa-checks`{.interpreted-text role="doc"}\" for more information regarding runtime performance issues.

> 检查ELF二进制文件，其中包含`.text`部分的重定位，可能会导致运行时性能影响。有关运行时性能问题的更多信息，请参阅“/ref-manual/qa-checks”中的“ELF二进制文件”消息解释。

- `unhandled-features-check:` check that if one of the variables that the `ref-classes-features_check`{.interpreted-text role="ref"} class supports (e.g. `REQUIRED_DISTRO_FEATURES`{.interpreted-text role="term"}) is set by a recipe, then the recipe also inherits `ref-classes-features_check`{.interpreted-text role="ref"} in order for the requirement to actually work.

> 检查如果ref-classes-features_check类支持的变量（例如REQUIRED_DISTRO_FEATURES）由配方设置，则该配方也必须继承ref-classes-features_check，以使要求实际有效。

- `unlisted-pkg-lics:` Checks that all declared licenses applying for a package are also declared on the recipe level (i.e. any license in `LICENSE:*` should appear in `LICENSE`{.interpreted-text role="term"}).

> 检查所有声明为软件包应用的许可证是否也在配方级别上声明（即`LICENSE:*`中的任何许可证都应出现在`LICENSE`中）。

- `useless-rpaths:` Checks for dynamic library load paths (rpaths) in the binaries that by default on a standard system are searched by the linker (e.g. `/lib` and `/usr/lib`). While these paths will not cause any breakage, they do waste space and are unnecessary.

> - 无用的RPATH：检查二进制文件中的动态库加载路径（RPATH），在标准系统中，这些路径由链接器搜索（例如`/lib`和`/usr/lib`）。虽然这些路径不会造成任何破坏，但它们浪费空间并且是不必要的。

- `usrmerge:` If `usrmerge` is in `DISTRO_FEATURES`{.interpreted-text role="term"}, this check will ensure that no package installs files to root (`/bin`, `/sbin`, `/lib`, `/lib64`) directories.

> 如果usrmerge包含在DISTRO_FEATURES中，此检查将确保没有软件包将文件安装到根（/bin、/sbin、/lib、/lib64）目录。

- `var-undefined:` Reports when variables fundamental to packaging (i.e. `WORKDIR`{.interpreted-text role="term"}, `DEPLOY_DIR`{.interpreted-text role="term"}, `D`{.interpreted-text role="term"}, `PN`{.interpreted-text role="term"}, and `PKGD`{.interpreted-text role="term"}) are undefined during `ref-tasks-package`{.interpreted-text role="ref"}.

> 报告当在ref-tasks-package中未定义包装所必需的变量（即WORKDIR、DEPLOY_DIR、D、PN和PKGD）时。

- `version-going-backwards:` If the `ref-classes-buildhistory`{.interpreted-text role="ref"} class is enabled, reports when a package being written out has a lower version than the previously written package under the same name. If you are placing output packages into a feed and upgrading packages on a target system using that feed, the version of a package going backwards can result in the target system not correctly upgrading to the \"new\" version of the package.

> 如果启用了`ref-classes-buildhistory`{.interpreted-text role="ref"}类，则会报告写出的包的版本低于同名包的先前版本。如果您将输出包放入发布源并使用该发布源在目标系统上升级包，则版本向后移动可能会导致目标系统无法正确升级到“新”版本的包。

  ::: note
  ::: title
  Note
  :::

  This is only relevant when you are using runtime package management on your target system.
  :::

- `xorg-driver-abi:` Checks that all packages containing Xorg drivers have ABI dependencies. The `xserver-xorg` recipe provides driver ABI names. All drivers should depend on the ABI versions that they have been built against. Driver recipes that include `xorg-driver-input.inc` or `xorg-driver-video.inc` will automatically get these versions. Consequently, you should only need to explicitly add dependencies to binary driver recipes.

> 检查所有包含Xorg驱动程序的软件包是否具有ABI依赖关系。 `xserver-xorg`配方提供驱动程序ABI名称。所有驱动程序都应该依赖于它们构建的ABI版本。包含`xorg-driver-input.inc`或`xorg-driver-video.inc`的驱动程序配方将自动获得这些版本。因此，您只需要显式添加依赖关系到二进制驱动程序配方即可。

# `insserv` {#ref-classes-insserv}


The `ref-classes-insserv`{.interpreted-text role="ref"} class uses the `insserv` utility to update the order of symbolic links in `/etc/rc?.d/` within an image based on dependencies specified by LSB headers in the `init.d` scripts themselves.

> 这个ref-classes-insserv类使用insserv实用程序根据LSB标头中指定的init.d脚本的依赖关系，在基于映像的/etc/rc?.d/中更新符号链接的顺序。

# `kernel` {#ref-classes-kernel}


The `ref-classes-kernel`{.interpreted-text role="ref"} class handles building Linux kernels. The class contains code to build all kernel trees. All needed headers are staged into the `STAGING_KERNEL_DIR`{.interpreted-text role="term"} directory to allow out-of-tree module builds using the `ref-classes-module`{.interpreted-text role="ref"} class.

> 类`ref-classes-kernel`{.interpreted-text role="ref"}处理构建Linux内核。该类包含构建所有内核树的代码。所有需要的标头都会被缓存到`STAGING_KERNEL_DIR`{.interpreted-text role="term"}目录以允许使用类`ref-classes-module`{.interpreted-text role="ref"}进行树外模块构建。


If a file named `defconfig` is listed in `SRC_URI`{.interpreted-text role="term"}, then by default `ref-tasks-configure`{.interpreted-text role="ref"} copies it as `.config` in the build directory, so it is automatically used as the kernel configuration for the build. This copy is not performed in case `.config` already exists there: this allows recipes to produce a configuration by other means in `do_configure:prepend`.

> 如果在`SRC_URI`中列出了一个名为`defconfig`的文件，那么默认情况下，`ref-tasks-configure`将其复制为构建目录中的`.config`，因此它自动用作构建的内核配置。如果那里已经存在`.config`，则不执行此复制：这允许配方以`do_configure:prepend`的其他方式生成配置。


Each built kernel module is packaged separately and inter-module dependencies are created by parsing the `modinfo` output. If all modules are required, then installing the `kernel-modules` package installs all packages with modules and various other kernel packages such as `kernel-vmlinux`.

> 每个构建的内核模块都是单独打包的，而模块间的依赖关系是通过解析`modinfo`输出来创建的。如果需要所有模块，那么安装`kernel-modules`软件包会安装所有带有模块的软件包以及其他各种内核软件包，如`kernel-vmlinux`。


The `ref-classes-kernel`{.interpreted-text role="ref"} class contains logic that allows you to embed an initial RAM filesystem (`Initramfs`{.interpreted-text role="term"}) image when you build the kernel image. For information on how to build an `Initramfs`{.interpreted-text role="term"}, see the \"`dev-manual/building:building an initial ram filesystem (Initramfs) image`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 类`ref-classes-kernel`{.interpreted-text role="ref"}包含允许您在构建内核映像时嵌入初始RAM文件系统（`Initramfs`{.interpreted-text role="term"}）映像的逻辑。有关如何构建`Initramfs`{.interpreted-text role="term"}的信息，请参阅Yocto项目开发任务手册中的“`dev-manual/building:building an initial ram filesystem (Initramfs) image`{.interpreted-text role="ref"}”部分。


Various other classes are used by the `ref-classes-kernel`{.interpreted-text role="ref"} and `ref-classes-module`{.interpreted-text role="ref"} classes internally including the `ref-classes-kernel-arch`{.interpreted-text role="ref"}, `ref-classes-module-base`{.interpreted-text role="ref"}, and `ref-classes-linux-kernel-base`{.interpreted-text role="ref"} classes.

> 其他各种类被`ref-classes-kernel`{.interpreted-text role="ref"}和`ref-classes-module`{.interpreted-text role="ref"}类内部使用，包括`ref-classes-kernel-arch`{.interpreted-text role="ref"}、`ref-classes-module-base`{.interpreted-text role="ref"}和`ref-classes-linux-kernel-base`{.interpreted-text role="ref"}类。

# `kernel-arch` {#ref-classes-kernel-arch}


The `ref-classes-kernel-arch`{.interpreted-text role="ref"} class sets the `ARCH` environment variable for Linux kernel compilation (including modules).

> `ref-classes-kernel-arch`{.interpreted-text role="ref"}类为Linux内核编译（包括模块）设置`ARCH`环境变量。

# `kernel-devicetree` {#ref-classes-kernel-devicetree}


The `ref-classes-kernel-devicetree`{.interpreted-text role="ref"} class, which is inherited by the `ref-classes-kernel`{.interpreted-text role="ref"} class, supports device tree generation.

> 这个继承自`ref-classes-kernel`{.interpreted-text role="ref"}类的`ref-classes-kernel-devicetree`{.interpreted-text role="ref"}类支持设备树的生成。

Its behavior is mainly controlled by the following variables:

- `KERNEL_DEVICETREE_BUNDLE`{.interpreted-text role="term"}: whether to bundle the kernel and device tree
- `KERNEL_DTBDEST`{.interpreted-text role="term"}: directory where to install DTB files
- `KERNEL_DTBVENDORED`{.interpreted-text role="term"}: whether to keep vendor subdirectories
- `KERNEL_DTC_FLAGS`{.interpreted-text role="term"}: flags for `dtc`, the Device Tree Compiler
- `KERNEL_PACKAGE_NAME`{.interpreted-text role="term"}: base name of the kernel packages

# `kernel-fitimage` {#ref-classes-kernel-fitimage}


The `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} class provides support to pack a kernel image, device trees, a U-boot script, an `Initramfs`{.interpreted-text role="term"} bundle and a RAM disk into a single FIT image. In theory, a FIT image can support any number of kernels, U-boot scripts, `Initramfs`{.interpreted-text role="term"} bundles, RAM disks and device-trees. However, `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} currently only supports limited usecases: just one kernel image, an optional U-boot script, an optional `Initramfs`{.interpreted-text role="term"} bundle, an optional RAM disk, and any number of device trees.

> 类`ref-classes-kernel-fitimage`{.interpreted-text role="ref"}提供支持，将内核映像、设备树、U-boot脚本、`Initramfs`{.interpreted-text role="term"}包和RAM磁盘打包成单个FIT映像。理论上，FIT映像可以支持任意数量的内核、U-boot脚本、`Initramfs`{.interpreted-text role="term"}包、RAM磁盘和设备树。然而，`ref-classes-kernel-fitimage`{.interpreted-text role="ref"}目前仅支持有限的用例：只有一个内核映像，可选的U-boot脚本，可选的`Initramfs`{.interpreted-text role="term"}包，可选的RAM磁盘，以及任意数量的设备树。


To create a FIT image, it is required that `KERNEL_CLASSES`{.interpreted-text role="term"} is set to include \"`ref-classes-kernel-fitimage`{.interpreted-text role="ref"}\" and `KERNEL_IMAGETYPE`{.interpreted-text role="term"} is set to \"fitImage\".

> 要创建FIT图像，需要将`KERNEL_CLASSES`{.interpreted-text role="term"}设置为包括“`ref-classes-kernel-fitimage`{.interpreted-text role="ref"}”，并将`KERNEL_IMAGETYPE`{.interpreted-text role="term"}设置为“fitImage”。


The options for the device tree compiler passed to `mkimage -D` when creating the FIT image are specified using the `UBOOT_MKIMAGE_DTCOPTS`{.interpreted-text role="term"} variable.

> `UBOOT_MKIMAGE_DTCOPTS` 变量用于指定在创建FIT图像时传递给`mkimage -D`的设备树编译器的选项。


Only a single kernel can be added to the FIT image created by `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} and the kernel image in FIT is mandatory. The address where the kernel image is to be loaded by U-Boot is specified by `UBOOT_LOADADDRESS`{.interpreted-text role="term"} and the entrypoint by `UBOOT_ENTRYPOINT`{.interpreted-text role="term"}. Setting `FIT_ADDRESS_CELLS`{.interpreted-text role="term"} to \"2\" is necessary if such addresses are 64 bit ones.

> 只能添加一个内核到由`ref-classes-kernel-fitimage`创建的FIT映像中，FIT中的内核映像是必需的。U-Boot加载内核映像的地址由`UBOOT_LOADADDRESS`指定，入口点由`UBOOT_ENTRYPOINT`指定。如果这些地址是64位的，则必须将`FIT_ADDRESS_CELLS`设置为“2”。


Multiple device trees can be added to the FIT image created by `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} and the device tree is optional. The address where the device tree is to be loaded by U-Boot is specified by `UBOOT_DTBO_LOADADDRESS`{.interpreted-text role="term"} for device tree overlays and by `UBOOT_DTB_LOADADDRESS`{.interpreted-text role="term"} for device tree binaries.

> 可以向`ref-classes-kernel-fitimage`{.interpreted-text role="ref"}创建的FIT图像添加多个设备树，设备树是可选的。U-Boot加载设备树的地址由`UBOOT_DTBO_LOADADDRESS`{.interpreted-text role="term"}指定用于设备树叠加，由`UBOOT_DTB_LOADADDRESS`{.interpreted-text role="term"}指定用于设备树二进制文件。


Only a single RAM disk can be added to the FIT image created by `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} and the RAM disk in FIT is optional. The address where the RAM disk image is to be loaded by U-Boot is specified by `UBOOT_RD_LOADADDRESS`{.interpreted-text role="term"} and the entrypoint by `UBOOT_RD_ENTRYPOINT`{.interpreted-text role="term"}. The ramdisk is added to FIT image when `INITRAMFS_IMAGE`{.interpreted-text role="term"} is specified and that `INITRAMFS_IMAGE_BUNDLE`{.interpreted-text role="term"} is set to 0.

> 只能添加一个RAM磁盘到由`ref-classes-kernel-fitimage`创建的FIT映像中，FIT中的RAM磁盘是可选的。U-Boot加载RAM磁盘映像的地址由`UBOOT_RD_LOADADDRESS`指定，入口点由`UBOOT_RD_ENTRYPOINT`指定。当指定`INITRAMFS_IMAGE`并将`INITRAMFS_IMAGE_BUNDLE`设置为0时，将添加RAM磁盘到FIT映像中。


Only a single `Initramfs`{.interpreted-text role="term"} bundle can be added to the FIT image created by `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} and the `Initramfs`{.interpreted-text role="term"} bundle in FIT is optional. In case of `Initramfs`{.interpreted-text role="term"}, the kernel is configured to be bundled with the root filesystem in the same binary (example: zImage-initramfs-`MACHINE`{.interpreted-text role="term"}.bin). When the kernel is copied to RAM and executed, it unpacks the `Initramfs`{.interpreted-text role="term"} root filesystem. The `Initramfs`{.interpreted-text role="term"} bundle can be enabled when `INITRAMFS_IMAGE`{.interpreted-text role="term"} is specified and that `INITRAMFS_IMAGE_BUNDLE`{.interpreted-text role="term"} is set to 1. The address where the `Initramfs`{.interpreted-text role="term"} bundle is to be loaded by U-boot is specified by `UBOOT_LOADADDRESS`{.interpreted-text role="term"} and the entrypoint by `UBOOT_ENTRYPOINT`{.interpreted-text role="term"}.

> 只能添加一个Initramfs包到由ref-classes-kernel-fitimage创建的FIT图像中，FIT中的Initramfs包是可选的。如果使用Initramfs，则内核配置为与根文件系统在同一个二进制文件中打包（例如：zImage-initramfs-MACHINE.bin）。当内核复制到RAM并执行时，它会解压Initramfs根文件系统。可以在指定INITRAMFS_IMAGE并将INITRAMFS_IMAGE_BUNDLE设置为1时启用Initramfs包。U-boot加载Initramfs包的地址由UBOOT_LOADADDRESS指定，入口点由UBOOT_ENTRYPOINT指定。


Only a single U-boot boot script can be added to the FIT image created by `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} and the boot script is optional. The boot script is specified in the ITS file as a text file containing U-boot commands. When using a boot script the user should configure the U-boot `ref-tasks-install`{.interpreted-text role="ref"} task to copy the script to sysroot. So the script can be included in the FIT image by the `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} class. At run-time, U-boot CONFIG_BOOTCOMMAND define can be configured to load the boot script from the FIT image and executes it.

> 只能添加一个U-boot启动脚本到由“ref-classes-kernel-fitimage”创建的FIT图像中，而且这个启动脚本是可选的。启动脚本在ITS文件中被指定为一个包含U-boot命令的文本文件。当使用启动脚本时，用户应该配置U-boot“ref-tasks-install”任务来将脚本复制到sysroot中，这样就可以通过“ref-classes-kernel-fitimage”类将脚本包含在FIT图像中。在运行时，可以配置U-boot CONFIG_BOOTCOMMAND定义以从FIT图像加载启动脚本并执行它。


The FIT image generated by `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} class is signed when the variables `UBOOT_SIGN_ENABLE`{.interpreted-text role="term"}, `UBOOT_MKIMAGE_DTCOPTS`{.interpreted-text role="term"}, `UBOOT_SIGN_KEYDIR`{.interpreted-text role="term"} and `UBOOT_SIGN_KEYNAME`{.interpreted-text role="term"} are set appropriately. The default values used for `FIT_HASH_ALG`{.interpreted-text role="term"} and `FIT_SIGN_ALG`{.interpreted-text role="term"} in `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} are \"sha256\" and \"rsa2048\" respectively. The keys for signing fitImage can be generated using the `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} class when both `FIT_GENERATE_KEYS`{.interpreted-text role="term"} and `UBOOT_SIGN_ENABLE`{.interpreted-text role="term"} are set to \"1\".

> 图像签名功能由`ref-classes-kernel-fitimage`{.interpreted-text role="ref"}类生成的FIT图像，当`UBOOT_SIGN_ENABLE`{.interpreted-text role="term"}、`UBOOT_MKIMAGE_DTCOPTS`{.interpreted-text role="term"}、`UBOOT_SIGN_KEYDIR`{.interpreted-text role="term"}和`UBOOT_SIGN_KEYNAME`{.interpreted-text role="term"}变量设置正确时，可以进行签名。`ref-classes-kernel-fitimage`{.interpreted-text role="ref"}类中`FIT_HASH_ALG`{.interpreted-text role="term"}和`FIT_SIGN_ALG`{.interpreted-text role="term"}的默认值分别为“sha256”和“rsa2048”。当`FIT_GENERATE_KEYS`{.interpreted-text role="term"}和`UBOOT_SIGN_ENABLE`{.interpreted-text role="term"}都设置为“1”时，可以使用`ref-classes-kernel-fitimage`{.interpreted-text role="ref"}类生成用于签名FIT图像的密钥。

# `kernel-grub` {#ref-classes-kernel-grub}


The `ref-classes-kernel-grub`{.interpreted-text role="ref"} class updates the boot area and the boot menu with the kernel as the priority boot mechanism while installing a RPM to update the kernel on a deployed target.

> 这个`ref-classes-kernel-grub`{.interpreted-text role="ref"}类在在部署的目标上安装RPM更新内核时，会更新引导区和引导菜单，以内核作为优先启动机制。

# `kernel-module-split` {#ref-classes-kernel-module-split}


The `ref-classes-kernel-module-split`{.interpreted-text role="ref"} class provides common functionality for splitting Linux kernel modules into separate packages.

> 这个`ref-classes-kernel-module-split`{.interpreted-text role="ref"}类提供了将Linux内核模块拆分成单独的软件包的常见功能。

# `kernel-uboot` {#ref-classes-kernel-uboot}

The `ref-classes-kernel-uboot`{.interpreted-text role="ref"} class provides support for building from vmlinux-style kernel sources.

# `kernel-uimage` {#ref-classes-kernel-uimage}

The `ref-classes-kernel-uimage`{.interpreted-text role="ref"} class provides support to pack uImage.

# `kernel-yocto` {#ref-classes-kernel-yocto}


The `ref-classes-kernel-yocto`{.interpreted-text role="ref"} class provides common functionality for building from linux-yocto style kernel source repositories.

> 这个ref-classes-kernel-yocto类提供了从linux-yocto样式内核源代码存储库构建的公共功能。

# `kernelsrc` {#ref-classes-kernelsrc}

The `ref-classes-kernelsrc`{.interpreted-text role="ref"} class sets the Linux kernel source and version.

# `lib_package` {#ref-classes-lib_package}


The `ref-classes-lib_package`{.interpreted-text role="ref"} class supports recipes that build libraries and produce executable binaries, where those binaries should not be installed by default along with the library. Instead, the binaries are added to a separate `${``PN`{.interpreted-text role="term"}`}-bin` package to make their installation optional.

> `ref-classes-lib_package`类支持构建库并生成可执行二进制文件的食谱，这些二进制文件不应默认安装在库中。相反，将二进制文件添加到单独的`${``PN`{.interpreted-text role="term"}`}-bin`包中，以使其安装变为可选。

# `libc*` {#ref-classes-libc*}

The `ref-classes-libc*`{.interpreted-text role="ref"} classes support recipes that build packages with `libc`:

- The `libc-common <ref-classes-libc*>`{.interpreted-text role="ref"} class provides common support for building with `libc`.
- The `libc-package <ref-classes-libc*>`{.interpreted-text role="ref"} class supports packaging up `glibc` and `eglibc`.

# `license` {#ref-classes-license}


The `ref-classes-license`{.interpreted-text role="ref"} class provides license manifest creation and license exclusion. This class is enabled by default using the default value for the `INHERIT_DISTRO`{.interpreted-text role="term"} variable.

> 类`ref-classes-license`提供许可证清单创建和许可证排除功能。使用变量`INHERIT_DISTRO`的默认值可以默认启用此类。

# `linux-kernel-base` {#ref-classes-linux-kernel-base}


The `ref-classes-linux-kernel-base`{.interpreted-text role="ref"} class provides common functionality for recipes that build out of the Linux kernel source tree. These builds goes beyond the kernel itself. For example, the Perf recipe also inherits this class.

> 这个`ref-classes-linux-kernel-base`{.interpreted-text role="ref"}类为从Linux内核源代码树构建的配方提供了共同的功能。这些构建超出了内核本身。例如，Perf配方也继承了这个类。

# `linuxloader` {#ref-classes-linuxloader}


Provides the function `linuxloader()`, which gives the value of the dynamic loader/linker provided on the platform. This value is used by a number of other classes.

> 提供函数`linuxloader()`，用于获取平台上提供的动态加载器/链接器的值。这个值被其他许多类所使用。

# `logging` {#ref-classes-logging}


The `ref-classes-logging`{.interpreted-text role="ref"} class provides the standard shell functions used to log messages for various BitBake severity levels (i.e. `bbplain`, `bbnote`, `bbwarn`, `bberror`, `bbfatal`, and `bbdebug`).

> 类`ref-classes-logging`{.interpreted-text role="ref"}提供了用于为各种BitBake严重程度（即`bbplain`、`bbnote`、`bbwarn`、`bberror`、`bbfatal`和`bbdebug`）记录消息的标准shell函数。

This class is enabled by default since it is inherited by the `ref-classes-base`{.interpreted-text role="ref"} class.

# `meson` {#ref-classes-meson}


The `ref-classes-meson`{.interpreted-text role="ref"} class allows to create recipes that build software using the [Meson](https://mesonbuild.com/) build system. You can use the `MESON_BUILDTYPE`{.interpreted-text role="term"} and `EXTRA_OEMESON`{.interpreted-text role="term"} variables to specify additional configuration options to be passed using the `meson` command line.

> 类`ref-classes-meson`{.interpreted-text role="ref"} 允许使用[Meson](https://mesonbuild.com/)构建系统创建制作软件的配方。您可以使用变量`MESON_BUILDTYPE`{.interpreted-text role="term"}和`EXTRA_OEMESON`{.interpreted-text role="term"}来指定要使用`meson`命令行传递的其他配置选项。

# `metadata_scm` {#ref-classes-metadata_scm}


The `ref-classes-metadata_scm`{.interpreted-text role="ref"} class provides functionality for querying the branch and revision of a Source Code Manager (SCM) repository.

> 类ref-classes-metadata_scm提供查询源代码管理器（SCM）仓库分支和修订版本的功能。


The `ref-classes-base`{.interpreted-text role="ref"} class uses this class to print the revisions of each layer before starting every build. The `ref-classes-metadata_scm`{.interpreted-text role="ref"} class is enabled by default because it is inherited by the `ref-classes-base`{.interpreted-text role="ref"} class.

> `ref-classes-base`{.interpreted-text role="ref"} 类会在每次构建之前打印每层的修订版本。 `ref-classes-metadata_scm`{.interpreted-text role="ref"} 类默认会启用，因为它被 `ref-classes-base`{.interpreted-text role="ref"} 类继承了。

# `migrate_localcount` {#ref-classes-migrate_localcount}

The `ref-classes-migrate_localcount`{.interpreted-text role="ref"} class verifies a recipe\'s localcount data and increments it appropriately.

# `mime` {#ref-classes-mime}


The `ref-classes-mime`{.interpreted-text role="ref"} class generates the proper post-install and post-remove (postinst/postrm) scriptlets for packages that install MIME type files. These scriptlets call `update-mime-database` to add the MIME types to the shared database.

> 这个ref-classes-mime类为安装MIME类型文件的软件包生成正确的安装后和删除后（postinst/postrm）脚本。这些脚本调用update-mime-database来将MIME类型添加到共享数据库中。

# `mime-xdg` {#ref-classes-mime-xdg}


The `ref-classes-mime-xdg`{.interpreted-text role="ref"} class generates the proper post-install and post-remove (postinst/postrm) scriptlets for packages that install `.desktop` files containing `MimeType` entries. These scriptlets call `update-desktop-database` to add the MIME types to the database of MIME types handled by desktop files.

> 这个ref-classes-mime-xdg类为安装包生成安装后和删除后（postinst/postrm）的脚本，这些安装包包含包含MimeType条目的.desktop文件。这些脚本调用update-desktop-database来将MIME类型添加到由桌面文件处理的MIME类型数据库中。


Thanks to this class, when users open a file through a file browser on recently created images, they don\'t have to choose the application to open the file from the pool of all known applications, even the ones that cannot open the selected file.

> 谢谢这个课程，当用户通过文件浏览器打开最近创建的图像文件时，他们不必从所有已知应用程序的池中选择要打开文件的应用程序，即使是无法打开所选文件的应用程序也是如此。


If you have recipes installing their `.desktop` files as absolute symbolic links, the detection of such files cannot be done by the current implementation of this class. In this case, you have to add the corresponding package names to the `MIME_XDG_PACKAGES`{.interpreted-text role="term"} variable.

> 如果您使用绝对符号链接安装其`.desktop`文件，则无法通过当前此类实现来检测此类文件。在这种情况下，您必须将相应的包名添加到变量`MIME_XDG_PACKAGES`中。

# `mirrors` {#ref-classes-mirrors}


The `ref-classes-mirrors`{.interpreted-text role="ref"} class sets up some standard `MIRRORS`{.interpreted-text role="term"} entries for source code mirrors. These mirrors provide a fall-back path in case the upstream source specified in `SRC_URI`{.interpreted-text role="term"} within recipes is unavailable.

> 这个ref-classes-mirrors类为源代码镜像设置了一些标准的MIRRORS条目。 如果食谱中的SRC_URI中指定的上游源不可用，则这些镜像提供了一个后备路径。

This class is enabled by default since it is inherited by the `ref-classes-base`{.interpreted-text role="ref"} class.

# `module` {#ref-classes-module}


The `ref-classes-module`{.interpreted-text role="ref"} class provides support for building out-of-tree Linux kernel modules. The class inherits the `ref-classes-module-base`{.interpreted-text role="ref"} and `ref-classes-kernel-module-split`{.interpreted-text role="ref"} classes, and implements the `ref-tasks-compile`{.interpreted-text role="ref"} and `ref-tasks-install`{.interpreted-text role="ref"} tasks. The class provides everything needed to build and package a kernel module.

> 模块类提供支持，用于构建树外的Linux内核模块。该类继承了模块基类和内核模块分割类，并实现了编译和安装任务。该类提供了构建和打包内核模块所需的一切。


For general information on out-of-tree Linux kernel modules, see the \"`kernel-dev/common:incorporating out-of-tree modules`{.interpreted-text role="ref"}\" section in the Yocto Project Linux Kernel Development Manual.

> 关于外树Linux内核模块的一般信息，请参见Yocto Project Linux内核开发手册中的“kernel-dev/common：包含外树模块”部分。

# `module-base` {#ref-classes-module-base}


The `ref-classes-module-base`{.interpreted-text role="ref"} class provides the base functionality for building Linux kernel modules. Typically, a recipe that builds software that includes one or more kernel modules and has its own means of building the module inherits this class as opposed to inheriting the `ref-classes-module`{.interpreted-text role="ref"} class.

> 类`ref-classes-module-base`{.interpreted-text role="ref"}提供了构建Linux内核模块的基本功能。通常，构建包含一个或多个内核模块，并具有自己的构建模块方法的软件的配方会继承这个类，而不是继承类`ref-classes-module`{.interpreted-text role="ref"}。

# `multilib*` {#ref-classes-multilib*}


The `ref-classes-multilib*`{.interpreted-text role="ref"} classes provide support for building libraries with different target optimizations or target architectures and installing them side-by-side in the same image.

> 类`ref-classes-multilib*`{.interpreted-text role="ref"}提供支持，用于使用不同的目标优化或目标架构构建库，并将它们并排安装在同一个映像中。


For more information on using the Multilib feature, see the \"`dev-manual/libraries:combining multiple versions of library files into one image`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 要了解有关使用多库特性的更多信息，请参阅Yocto Project开发任务手册中的“dev-manual/libraries：将多个版本的库文件组合成一个映像”部分。

# `native` {#ref-classes-native}


The `ref-classes-native`{.interpreted-text role="ref"} class provides common functionality for recipes that build tools to run on the `Build Host`{.interpreted-text role="term"} (i.e. tools that use the compiler or other tools from the build host).

> 这个ref-classes-native类为构建在构建主机（即使用构建主机上的编译器或其他工具的工具）上运行的工具提供了常见的功能。

You can create a recipe that builds tools that run natively on the host a couple different ways:


- Create a `myrecipe-native.bb` recipe that inherits the `ref-classes-native`{.interpreted-text role="ref"} class. If you use this method, you must order the inherit statement in the recipe after all other inherit statements so that the `ref-classes-native`{.interpreted-text role="ref"} class is inherited last.

> 创建一个继承`ref-classes-native`{.interpreted-text role="ref"}类的`myrecipe-native.bb`配方。如果你使用这种方法，你必须在配方中把继承语句放在所有其他继承语句之后，以便最后继承`ref-classes-native`{.interpreted-text role="ref"}类。

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

> 在食谱中，使用`:class-native`和`:class-target`覆盖以指定各自本机或目标情况下的任何特定功能。


Although applied differently, the `ref-classes-native`{.interpreted-text role="ref"} class is used with both methods. The advantage of the second method is that you do not need to have two separate recipes (assuming you need both) for native and target. All common parts of the recipe are automatically shared.

> 尽管应用方式不同，但`ref-classes-native`{.interpreted-text role="ref"}类与两种方法都是使用的。第二种方法的优势在于，您不需要为本地和目标分别拥有两个单独的配方（假设您需要两者）。配方的所有共同部分都会自动共享。

# `nativesdk` {#ref-classes-nativesdk}


The `ref-classes-nativesdk`{.interpreted-text role="ref"} class provides common functionality for recipes that wish to build tools to run as part of an SDK (i.e. tools that run on `SDKMACHINE`{.interpreted-text role="term"}).

> 类`ref-classes-nativesdk`提供了希望构建作为SDK一部分运行的工具（即在`SDKMACHINE`上运行的工具）的公共功能。

You can create a recipe that builds tools that run on the SDK machine a couple different ways:


- Create a `nativesdk-myrecipe.bb` recipe that inherits the `ref-classes-nativesdk`{.interpreted-text role="ref"} class. If you use this method, you must order the inherit statement in the recipe after all other inherit statements so that the `ref-classes-nativesdk`{.interpreted-text role="ref"} class is inherited last.

> 创建一个名为nativesdk-myrecipe.bb的配方，继承ref-classes-nativesdk类。如果使用此方法，必须在配方中的继承声明之后排列ref-classes-nativesdk类，以便最后继承该类。
- Create a `ref-classes-nativesdk`{.interpreted-text role="ref"} variant of any recipe by adding the following:

  ```
  BBCLASSEXTEND = "nativesdk"
  ```


  Inside the recipe, use `:class-nativesdk` and `:class-target` overrides to specify any functionality specific to the respective SDK machine or target case.

> 在食谱中，使用`:class-nativesdk`和`:class-target`覆盖来指定相应SDK机器或目标情况下的任何特定功能。

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


Although applied differently, the `ref-classes-nativesdk`{.interpreted-text role="ref"} class is used with both methods. The advantage of the second method is that you do not need to have two separate recipes (assuming you need both) for the SDK machine and the target. All common parts of the recipe are automatically shared.

> 尽管应用方式不同，但`ref-classes-nativesdk`{.interpreted-text role="ref"}类在这两种方法中都得到了使用。第二种方法的优势在于，您不需要为SDK机器和目标分别编写两个不同的配方（假设您需要两者）。所有共同的部分都会自动共享。

# `nopackages` {#ref-classes-nopackages}

Disables packaging tasks for those recipes and classes where packaging is not needed.

# `npm` {#ref-classes-npm}

Provides support for building Node.js software fetched using the `node package manager (NPM) <Npm_(software)>`{.interpreted-text role="wikipedia"}.

::: note
::: title
Note
:::

Currently, recipes inheriting this class must use the `npm://` fetcher to have dependencies fetched and packaged automatically.
:::


For information on how to create NPM packages, see the \"`dev-manual/packages:creating node package manager (npm) packages`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 要了解如何创建NPM包，请参阅Yocto项目开发任务手册中的“dev-manual/packages：创建节点包管理器（NPM）包”部分。

# `oelint` {#ref-classes-oelint}


The `ref-classes-oelint`{.interpreted-text role="ref"} class is an obsolete lint checking tool available in `meta/classes` in the `Source Directory`{.interpreted-text role="term"}.

> 这个ref-classes-oelint类是源目录中meta/classes中提供的一个过时的检查工具。


There are some classes that could be generally useful in OE-Core but are never actually used within OE-Core itself. The `ref-classes-oelint`{.interpreted-text role="ref"} class is one such example. However, being aware of this class can reduce the proliferation of different versions of similar classes across multiple layers.

> 在OE-Core中有一些可能通用的类，但它们实际上从未在OE-Core中使用过。`ref-classes-oelint`{.interpreted-text role="ref"} 类就是一个例子。然而，了解这个类可以减少多个层次上不同版本的相似类的激增。

# `overlayfs` {#ref-classes-overlayfs}


It\'s often desired in Embedded System design to have a read-only root filesystem. But a lot of different applications might want to have read-write access to some parts of a filesystem. It can be especially useful when your update mechanism overwrites the whole root filesystem, but you may want your application data to be preserved between updates. The `ref-classes-overlayfs`{.interpreted-text role="ref"} class provides a way to achieve that by means of `overlayfs` and at the same time keeping the base root filesystem read-only.

> 在嵌入式系统设计中，通常希望拥有只读的根文件系统。但是许多不同的应用程序可能希望对文件系统的某些部分具有读写访问权限。当更新机制覆盖整个根文件系统时，这可能尤其有用，但是您可能希望应用程序数据在更新之间保留。`ref-classes-overlayfs`{.interpreted-text role="ref"}类提供了一种通过`overlayfs`实现此目的的方法，同时保持基本根文件系统为只读。


To use this class, set a mount point for a partition `overlayfs` is going to use as upper layer in your machine configuration. The underlying file system can be anything that is supported by `overlayfs`. This has to be done in your machine configuration:

> 要使用此类，请在您的机器配置中为要用作上层的分区设置一个挂载点`overlayfs`。底层文件系统可以是`overlayfs`支持的任何内容。这必须在您的机器配置中完成：

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

> 要了解更多关于overlayfs的细节、内部情况和支持的操作，请参考[Linux内核](https://www.kernel.org/doc/html/latest/filesystems/overlayfs.html)的官方文档。
  :::


The class assumes you have a `data.mount` systemd unit defined elsewhere in your BSP (e.g. in `systemd-machine-units` recipe) and it\'s installed into the image.

> 该类假设您在 BSP（例如在'systemd-machine-units'配方中）中定义了一个`data.mount` systemd 单元，并且已将其安装到镜像中。

Then you can specify writable directories on a recipe basis (e.g. in my-application.bb):

```
OVERLAYFS_WRITABLE_PATHS[data] = "/usr/share/my-custom-application"
```


To support several mount points you can use a different variable flag. Assuming we want to have a writable location on the file system, but do not need that the data survives a reboot, then we could have a `mnt-overlay.mount` unit for a `tmpfs` file system.

> 为了支持多个挂载点，您可以使用不同的变量标志。假设我们希望在文件系统上有一个可写的位置，但不需要数据在重新启动后保持，那么我们可以为`tmpfs`文件系统提供一个`mnt-overlay.mount`单元。

In your machine configuration:

```
OVERLAYFS_MOUNT_POINT[mnt-overlay] = "/mnt/overlay"
```

and then in your recipe:

```
OVERLAYFS_WRITABLE_PATHS[mnt-overlay] = "/usr/share/another-application"
```


On a practical note, your application recipe might require multiple overlays to be mounted before running to avoid writing to the underlying file system (which can be forbidden in case of read-only file system) To achieve that `ref-classes-overlayfs`{.interpreted-text role="ref"} provides a `systemd` helper service for mounting overlays. This helper service is named `${PN}-overlays.service` and can be depended on in your application recipe (named `application` in the following example) `systemd` unit by adding to the unit the following:

> 在实际操作上，您的应用程序配方可能需要在运行之前挂载多个覆盖，以避免写入底层文件系统（如果是只读文件系统，则可能被禁止）。为此，ref-classes-overlayfs提供了一个systemd助手服务用于挂载覆盖。此助手服务命名为${PN}-overlays.service，可以在应用程序配方（以下示例中命名为“应用程序”）中的systemd单元中进行依赖，方法是添加以下内容：

```
[Unit]
After=application-overlays.service
Requires=application-overlays.service
```

::: note
::: title
Note
:::


The class does not support the `/etc` directory itself, because `systemd` depends on it. In order to get `/etc` in overlayfs, see `ref-classes-overlayfs-etc`{.interpreted-text role="ref"}.

> 类不支持`/etc`目录本身，因为`systemd`依赖它。要在overlayfs中获取`/etc`，请参阅`ref-classes-overlayfs-etc`{.interpreted-text role="ref"}。
:::

# `overlayfs-etc` {#ref-classes-overlayfs-etc}


In order to have the `/etc` directory in overlayfs a special handling at early boot stage is required. The idea is to supply a custom init script that mounts `/etc` before launching the actual init program, because the latter already requires `/etc` to be mounted.

> 为了在overlayfs中拥有`/etc`目录，需要在早期启动阶段进行特殊处理。这个想法是提供一个自定义的初始脚本，在启动实际的初始程序之前挂载`/etc`，因为后者已经需要挂载`/etc`。

Example usage in image recipe:

```
IMAGE_FEATURES += "overlayfs-etc"
```

::: note
::: title
Note
:::


This class must not be inherited directly. Use `IMAGE_FEATURES`{.interpreted-text role="term"} or `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"}

> 这个类不能直接继承。请使用`IMAGE_FEATURES`{.interpreted-text role="term"} 或 `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"}。
:::

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

> 默认选项是将原始的`/sbin/init`重命名为`/sbin/init.orig`，然后将生成的init放置在原始名称下，即`/sbin/init`。它具有一个优势，即您无需更改任何内核参数即可使其工作，但它造成一个限制，即无法使用包管理，因为更新init管理器将删除生成的脚本。
- If you wish to keep original init as is, you can set:

  ```
  OVERLAYFS_ETC_USE_ORIG_INIT_NAME = "0"
  ```


  Then the generated init will be named `/sbin/preinit` and you would need to extend your kernel parameters manually in your bootloader configuration.

> 那么生成的初始化程序将被命名为“/sbin/preinit”，您需要在启动加载程序配置中手动扩展内核参数。

# `own-mirrors` {#ref-classes-own-mirrors}


The `ref-classes-own-mirrors`{.interpreted-text role="ref"} class makes it easier to set up your own `PREMIRRORS`{.interpreted-text role="term"} from which to first fetch source before attempting to fetch it from the upstream specified in `SRC_URI`{.interpreted-text role="term"} within each recipe.

> 类`ref-classes-own-mirrors`{.interpreted-text role="ref"}可以让您更容易地设置自己的`PREMIRRORS`{.interpreted-text role="term"}，从中获取源代码，然后再尝试从每个配方中指定的`SRC_URI`{.interpreted-text role="term"}中获取它。

To use this class, inherit it globally and specify `SOURCE_MIRROR_URL`{.interpreted-text role="term"}. Here is an example:

```
INHERIT += "own-mirrors"
SOURCE_MIRROR_URL = "http://example.com/my-source-mirror"
```

You can specify only a single URL in `SOURCE_MIRROR_URL`{.interpreted-text role="term"}.

# `package` {#ref-classes-package}


The `ref-classes-package`{.interpreted-text role="ref"} class supports generating packages from a build\'s output. The core generic functionality is in `package.bbclass`. The code specific to particular package types resides in these package-specific classes: `ref-classes-package_deb`{.interpreted-text role="ref"}, `ref-classes-package_rpm`{.interpreted-text role="ref"}, `ref-classes-package_ipk`{.interpreted-text role="ref"}.

> 类`ref-classes-package`{.interpreted-text role="ref"}支持从构建输出生成软件包。核心通用功能位于`package.bbclass`中。特定于特定软件包类型的代码位于这些特定于软件包类型的类中：`ref-classes-package_deb`{.interpreted-text role="ref"}、`ref-classes-package_rpm`{.interpreted-text role="ref"}和`ref-classes-package_ipk`{.interpreted-text role="ref"}。


You can control the list of resulting package formats by using the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable defined in your `conf/local.conf` configuration file, which is located in the `Build Directory`{.interpreted-text role="term"}. When defining the variable, you can specify one or more package types. Since images are generated from packages, a packaging class is needed to enable image generation. The first class listed in this variable is used for image generation.

> 你可以通过在位于构建目录中的`conf/local.conf`配置文件中定义的`PACKAGE_CLASSES`变量来控制生成的软件包格式列表。在定义变量时，你可以指定一个或多个软件包类型。由于图像是从软件包生成的，因此需要打包类来启用图像生成。该变量中列出的第一个类用于图像生成。


If you take the optional step to set up a repository (package feed) on the development host that can be used by DNF, you can install packages from the feed while you are running the image on the target (i.e. runtime installation of packages). For more information, see the \"`dev-manual/packages:using runtime package management`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 如果您采取可选步骤，在开发主机上设置存储库（软件包源），可以由DNF使用，您可以在目标上运行映像时安装软件包（即软件包的运行时安装）。有关更多信息，请参阅Yocto项目开发任务手册中的“dev-manual/packages：使用运行时包管理”部分。


The package-specific class you choose can affect build-time performance and has space ramifications. In general, building a package with IPK takes about thirty percent less time as compared to using RPM to build the same or similar package. This comparison takes into account a complete build of the package with all dependencies previously built. The reason for this discrepancy is because the RPM package manager creates and processes more `Metadata`{.interpreted-text role="term"} than the IPK package manager. Consequently, you might consider setting `PACKAGE_CLASSES`{.interpreted-text role="term"} to \"`ref-classes-package_ipk`{.interpreted-text role="ref"}\" if you are building smaller systems.

> 你选择的特定软件包类可能会影响构建时间性能和空间占用。通常，使用IPK构建相同或类似的包比使用RPM构建相同或类似的包要节省约30％的时间。此比较考虑了包的完整构建，其中所有依赖项均已构建。由于RPM软件包管理器创建和处理的元数据比IPK软件包管理器多，因此出现了这种差异。因此，如果您正在构建较小的系统，可以考虑将PACKAGE_CLASSES设置为“ref-classes-package_ipk”。

Before making your package manager decision, however, you should consider some further things about using RPM:


- RPM starts to provide more abilities than IPK due to the fact that it processes more Metadata. For example, this information includes individual file types, file checksum generation and evaluation on install, sparse file support, conflict detection and resolution for Multilib systems, ACID style upgrade, and repackaging abilities for rollbacks.

> RPM 由于处理更多的元数据，开始提供比 IPK 更多的能力。例如，这些信息包括个别文件类型、文件校验码的生成和安装评估、稀疏文件支持、多架构系统的冲突检测和解决、ACID 风格的升级以及回滚的重新打包能力。

- For smaller systems, the extra space used for the Berkeley Database and the amount of metadata when using RPM can affect your ability to perform on-device upgrades.

> 对于较小的系统，使用Berkeley Database和RPM时所用的额外空间以及元数据可能会影响您在设备上执行升级的能力。

You can find additional information on the effects of the package class at these two Yocto Project mailing list links:

- :yocto_lists:[/pipermail/poky/2011-May/006362.html]{.title-ref}
- :yocto_lists:[/pipermail/poky/2011-May/006363.html]{.title-ref}

# `package_deb` {#ref-classes-package_deb}


The `ref-classes-package_deb`{.interpreted-text role="ref"} class provides support for creating packages that use the Debian (i.e. `.deb`) file format. The class ensures the packages are written out in a `.deb` file format to the `${``DEPLOY_DIR_DEB`{.interpreted-text role="term"}`}` directory.

> 类`ref-classes-package_deb`{.interpreted-text role="ref"} 提供支持，以创建使用Debian（即`.deb`）文件格式的软件包。该类确保将`.deb`文件格式的软件包写入`${``DEPLOY_DIR_DEB`{.interpreted-text role="term"}`}`目录。


This class inherits the `ref-classes-package`{.interpreted-text role="ref"} class and is enabled through the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable in the `local.conf` file.

> 这个类继承了`ref-classes-package`类，并且可以通过`local.conf`文件中的`PACKAGE_CLASSES`变量来启用。

# `package_ipk` {#ref-classes-package_ipk}


The `ref-classes-package_ipk`{.interpreted-text role="ref"} class provides support for creating packages that use the IPK (i.e. `.ipk`) file format. The class ensures the packages are written out in a `.ipk` file format to the `${``DEPLOY_DIR_IPK`{.interpreted-text role="term"}`}` directory.

> 这个ref-classes-package_ipk类提供支持，用于创建使用IPK（即.ipk）文件格式的软件包。该类确保将软件包以.ipk文件格式写入到$DEPLOY_DIR_IPK目录中。


This class inherits the `ref-classes-package`{.interpreted-text role="ref"} class and is enabled through the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable in the `local.conf` file.

> 这个类继承了`ref-classes-package`类，并通过`local.conf`文件中的`PACKAGE_CLASSES`变量启用。

# `package_rpm` {#ref-classes-package_rpm}


The `ref-classes-package_rpm`{.interpreted-text role="ref"} class provides support for creating packages that use the RPM (i.e. `.rpm`) file format. The class ensures the packages are written out in a `.rpm` file format to the `${``DEPLOY_DIR_RPM`{.interpreted-text role="term"}`}` directory.

> 这个ref-classes-package_rpm类提供了创建使用RPM（即.rpm）文件格式的软件包的支持。该类确保将软件包以.rpm文件格式写入到DEPLOY_DIR_RPM目录中。


This class inherits the `ref-classes-package`{.interpreted-text role="ref"} class and is enabled through the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable in the `local.conf` file.

> 这个类继承了`ref-classes-package`类，并通过`local.conf`文件中的`PACKAGE_CLASSES`变量启用。

# `packagedata` {#ref-classes-packagedata}


The `ref-classes-packagedata`{.interpreted-text role="ref"} class provides common functionality for reading `pkgdata` files found in `PKGDATA_DIR`{.interpreted-text role="term"}. These files contain information about each output package produced by the OpenEmbedded build system.

> 类`ref-classes-packagedata`提供了读取`PKGDATA_DIR`中发现的`pkgdata`文件的常见功能。这些文件包含了OpenEmbedded构建系统生成的每个输出包的信息。

This class is enabled by default because it is inherited by the `ref-classes-package`{.interpreted-text role="ref"} class.

# `packagegroup` {#ref-classes-packagegroup}


The `ref-classes-packagegroup`{.interpreted-text role="ref"} class sets default values appropriate for package group recipes (e.g. `PACKAGES`{.interpreted-text role="term"}, `PACKAGE_ARCH`{.interpreted-text role="term"}, `ALLOW_EMPTY`{.interpreted-text role="term"}, and so forth). It is highly recommended that all package group recipes inherit this class.

> `ref-classes-packagegroup` 类设置了适用于软件包组配方（例如 `PACKAGES`、`PACKAGE_ARCH`、`ALLOW_EMPTY` 等）的默认值。强烈建议所有软件包组配方继承此类。


For information on how to use this class, see the \"`dev-manual/customizing-images:customizing images using custom package groups`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 查看有关如何使用此类的信息，请参阅Yocto项目开发任务手册中的“dev-manual / customizing-images：使用自定义软件包组自定义图像”部分。

Previously, this class was called the `task` class.

# `patch` {#ref-classes-patch}


The `ref-classes-patch`{.interpreted-text role="ref"} class provides all functionality for applying patches during the `ref-tasks-patch`{.interpreted-text role="ref"} task.

> 该`ref-classes-patch`{.interpreted-text role="ref"}类提供了在`ref-tasks-patch`{.interpreted-text role="ref"}任务期间应用补丁的所有功能。

This class is enabled by default because it is inherited by the `ref-classes-base`{.interpreted-text role="ref"} class.

# `perlnative` {#ref-classes-perlnative}


When inherited by a recipe, the `ref-classes-perlnative`{.interpreted-text role="ref"} class supports using the native version of Perl built by the build system rather than using the version provided by the build host.

> 当继承一个配方时，`ref-classes-perlnative`{.interpreted-text role="ref"}类支持使用构建系统构建的Perl本机版本，而不是使用构建主机提供的版本。

# `pypi` {#ref-classes-pypi}


The `ref-classes-pypi`{.interpreted-text role="ref"} class sets variables appropriately for recipes that build Python modules from [PyPI](https://pypi.org/), the Python Package Index. By default it determines the PyPI package name based upon `BPN`{.interpreted-text role="term"} (stripping the \"python-\" or \"python3-\" prefix off if present), however in some cases you may need to set it manually in the recipe by setting `PYPI_PACKAGE`{.interpreted-text role="term"}.

> 类`ref-classes-pypi`{.interpreted-text role="ref"}设置变量，适用于从[PyPI](https://pypi.org/)（Python包索引）构建Python模块的配方。默认情况下，它根据`BPN`{.interpreted-text role="term"}确定PyPI包名（如果存在，则会删除“python-”或“python3-”前缀），但在某些情况下，您可能需要在配方中手动设置`PYPI_PACKAGE`{.interpreted-text role="term"}来设置它。


Variables set by the `ref-classes-pypi`{.interpreted-text role="ref"} class include `SRC_URI`{.interpreted-text role="term"}, `SECTION`{.interpreted-text role="term"}, `HOMEPAGE`{.interpreted-text role="term"}, `UPSTREAM_CHECK_URI`{.interpreted-text role="term"}, `UPSTREAM_CHECK_REGEX`{.interpreted-text role="term"} and `CVE_PRODUCT`{.interpreted-text role="term"}.

> 参考类`ref-classes-pypi`设置的变量包括`SRC_URI`、`SECTION`、`HOMEPAGE`、`UPSTREAM_CHECK_URI`、`UPSTREAM_CHECK_REGEX`和`CVE_PRODUCT`。

# `python_flit_core` {#ref-classes-python_flit_core}


The `ref-classes-python_flit_core`{.interpreted-text role="ref"} class enables building Python modules which declare the [PEP-517](https://www.python.org/dev/peps/pep-0517/) compliant `flit_core.buildapi` `build-backend` in the `[build-system]` section of `pyproject.toml` (See [PEP-518](https://www.python.org/dev/peps/pep-0518/)).

> 类`ref-classes-python_flit_core`{.interpreted-text role="ref"}可以构建符合[PEP-517](https://www.python.org/dev/peps/pep-0517/)规范的Python模块，其中在`pyproject.toml`的`[build-system]`部分声明`flit_core.buildapi` `build-backend`（参见[PEP-518](https://www.python.org/dev/peps/pep-0518/)）。

Python modules built with `flit_core.buildapi` are pure Python (no `C` or `Rust` extensions).

Internally this uses the `ref-classes-python_pep517`{.interpreted-text role="ref"} class.

# `python_pep517` {#ref-classes-python_pep517}


The `ref-classes-python_pep517`{.interpreted-text role="ref"} class builds and installs a Python `wheel` binary archive (see [PEP-517](https://peps.python.org/pep-0517/)).

> 类`ref-classes-python_pep517`构建和安装Python `wheel`二进制存档（参见[PEP-517](https://peps.python.org/pep-0517/)）。

Recipes wouldn\'t inherit this directly, instead typically another class will inherit this and add the relevant native dependencies.


Examples of classes which do this are `ref-classes-python_flit_core`{.interpreted-text role="ref"}, `ref-classes-python_setuptools_build_meta`{.interpreted-text role="ref"}, and `ref-classes-python_poetry_core`{.interpreted-text role="ref"}.

> 例子包括python_flit_core{.interpreted-text role="ref"}, python_setuptools_build_meta{.interpreted-text role="ref"}, 和 python_poetry_core{.interpreted-text role="ref"}。

# `python_poetry_core` {#ref-classes-python_poetry_core}


The `ref-classes-python_poetry_core`{.interpreted-text role="ref"} class enables building Python modules which use the [Poetry Core](https://python-poetry.org) build system.

> 这个`ref-classes-python_poetry_core`{.interpreted-text role="ref"}类可以构建使用[Poetry Core](https://python-poetry.org)构建系统的Python模块。

Internally this uses the `ref-classes-python_pep517`{.interpreted-text role="ref"} class.

# `python_pyo3` {#ref-classes-python_pyo3}


The `ref-classes-python_pyo3`{.interpreted-text role="ref"} class helps make sure that Python extensions written in Rust and built with [PyO3](https://pyo3.rs/), properly set up the environment for cross compilation.

> 类`ref-classes-python_pyo3`{.interpreted-text role="ref"} 可以帮助确保使用[PyO3](https://pyo3.rs/)编写的Rust扩展正确设置用于跨编译的环境。


This class is internal to the `ref-classes-python-setuptools3_rust`{.interpreted-text role="ref"} class and is not meant to be used directly in recipes.

> 这个类是内部类，不适合直接在配方中使用ref-classes-python-setuptools3_rust类。

# `python-setuptools3_rust` {#ref-classes-python-setuptools3_rust}


The `ref-classes-python-setuptools3_rust`{.interpreted-text role="ref"} class enables building Python extensions implemented in Rust with [PyO3](https://pyo3.rs/), which allows to compile and distribute Python extensions written in Rust as easily as if they were written in C.

> 这个`ref-classes-python-setuptools3_rust`{.interpreted-text role="ref"}类可以使用[PyO3](https://pyo3.rs/)来构建用Rust实现的Python扩展，这使得用Rust编写的Python扩展的编译和发布就像用C编写的一样容易。

This class inherits the `ref-classes-setuptools3`{.interpreted-text role="ref"} and `ref-classes-python_pyo3`{.interpreted-text role="ref"} classes.

# `pixbufcache` {#ref-classes-pixbufcache}


The `ref-classes-pixbufcache`{.interpreted-text role="ref"} class generates the proper post-install and post-remove (postinst/postrm) scriptlets for packages that install pixbuf loaders, which are used with `gdk-pixbuf`. These scriptlets call `update_pixbuf_cache` to add the pixbuf loaders to the cache. Since the cache files are architecture-specific, `update_pixbuf_cache` is run using QEMU if the postinst scriptlets need to be run on the build host during image creation.

> ref-classes-pixbufcache类为安装pixbuf加载程序的软件包生成适当的安装后和卸载后（postinst / postrm）脚本。这些脚本调用update_pixbuf_cache将pixbuf加载程序添加到缓存中。由于缓存文件是特定于架构的，因此如果在创建映像期间需要在构建主机上运行postinst脚本，则使用QEMU运行update_pixbuf_cache。


If the pixbuf loaders being installed are in packages other than the recipe\'s main package, set `PIXBUF_PACKAGES`{.interpreted-text role="term"} to specify the packages containing the loaders.

> 如果安装的pixbuf加载程序不在食谱的主要软件包中，请将`PIXBUF_PACKAGES`设置为指定包含加载程序的软件包。

# `pkgconfig` {#ref-classes-pkgconfig}


The `ref-classes-pkgconfig`{.interpreted-text role="ref"} class provides a standard way to get header and library information by using `pkg-config`. This class aims to smooth integration of `pkg-config` into libraries that use it.

> 这个ref-classes-pkgconfig类提供了一种通过使用pkg-config获取头文件和库信息的标准方法。这个类旨在简化将pkg-config集成到使用它的库中的过程。


During staging, BitBake installs `pkg-config` data into the `sysroots/` directory. By making use of sysroot functionality within `pkg-config`, the `ref-classes-pkgconfig`{.interpreted-text role="ref"} class no longer has to manipulate the files.

> 在搭建期间，BitBake将`pkg-config`数据安装到`sysroots/`目录中。通过利用`pkg-config`中的sysroot功能，`ref-classes-pkgconfig`{.interpreted-text role="ref"} 类不再需要操作文件。

# `populate_sdk` {#ref-classes-populate-sdk}


The `ref-classes-populate-sdk`{.interpreted-text role="ref"} class provides support for SDK-only recipes. For information on advantages gained when building a cross-development toolchain using the `ref-tasks-populate_sdk`{.interpreted-text role="ref"} task, see the \"`sdk-manual/appendix-obtain:building an sdk installer`{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 类`ref-classes-populate-sdk`{.interpreted-text role="ref"} 提供了支持仅 SDK 配方的支持。有关使用 `ref-tasks-populate_sdk`{.interpreted-text role="ref"} 任务构建跨开发工具链时获得的优势，请参见 Yocto 项目应用开发和可扩展软件开发套件（eSDK）手册中的"`sdk-manual/appendix-obtain:building an sdk installer`{.interpreted-text role="ref"}"部分。

# `populate_sdk_*` {#ref-classes-populate-sdk-*}

The `ref-classes-populate-sdk-*`{.interpreted-text role="ref"} classes support SDK creation and consist of the following classes:


- `populate_sdk_base <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}: The base class supporting SDK creation under all package managers (i.e. DEB, RPM, and opkg).

> 填充SDK基类<ref-classes-populate-sdk-*>：支持在所有包管理器（如DEB、RPM和opkg）下创建SDK的基类。
- `populate_sdk_deb <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}: Supports creation of the SDK given the Debian package manager.
- `populate_sdk_rpm <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}: Supports creation of the SDK given the RPM package manager.

- `populate_sdk_ipk <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}: Supports creation of the SDK given the opkg (IPK format) package manager.

> 支持使用opkg（IPK格式）软件包管理器创建SDK：populate_sdk_ipk <ref-classes-populate-sdk-*>。
- `populate_sdk_ext <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}: Supports extensible SDK creation under all package managers.


The `populate_sdk_base <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"} class inherits the appropriate `populate_sdk_*` (i.e. `deb`, `rpm`, and `ipk`) based on `IMAGE_PKGTYPE`{.interpreted-text role="term"}.

> `populate_sdk_base<ref-classes-populate-sdk-*>`{.interpreted-text role="ref"} 类继承了根据`IMAGE_PKGTYPE`{.interpreted-text role="term"}适当的`populate_sdk_*`（即`deb`、`rpm`和`ipk`）。


The base class ensures all source and destination directories are established and then populates the SDK. After populating the SDK, the `populate_sdk_base <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"} class constructs two sysroots: `${``SDK_ARCH`{.interpreted-text role="term"}`}-nativesdk`, which contains the cross-compiler and associated tooling, and the target, which contains a target root filesystem that is configured for the SDK usage. These two images reside in `SDK_OUTPUT`{.interpreted-text role="term"}, which consists of the following:

> 基类确保所有源和目标目录都已建立，然后填充SDK。在填充SDK之后，`populate_sdk_base <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"} 类构造两个系统根：`${``SDK_ARCH`{.interpreted-text role="term"}`}-nativesdk`，其中包含交叉编译器和相关工具，以及目标，其中包含配置为SDK使用的目标根文件系统。这两个映像文件位于`SDK_OUTPUT`{.interpreted-text role="term"} 中，其中包括：

```
${SDK_OUTPUT}/${SDK_ARCH}-nativesdk-pkgs
${SDK_OUTPUT}/${SDKTARGETSYSROOT}/target-pkgs
```

Finally, the base populate SDK class creates the toolchain environment setup script, the tarball of the SDK, and the installer.


The respective `populate_sdk_deb <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}, `populate_sdk_rpm <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}, and `populate_sdk_ipk <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"} classes each support the specific type of SDK. These classes are inherited by and used with the `populate_sdk_base <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"} class.

> 分别的`populate_sdk_deb <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}、`populate_sdk_rpm <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}和`populate_sdk_ipk <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}类别各自支持特定类型的SDK。这些类别由`populate_sdk_base <ref-classes-populate-sdk-*>`{.interpreted-text role="ref"}类别继承和使用。


For more information on the cross-development toolchain generation, see the \"`overview-manual/concepts:cross-development toolchain generation`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual. For information on advantages gained when building a cross-development toolchain using the `ref-tasks-populate_sdk`{.interpreted-text role="ref"} task, see the \"`sdk-manual/appendix-obtain:building an sdk installer`{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 要了解更多有关跨开发工具链生成的信息，请参阅Yocto项目概述和概念手册中的“概述手册/概念：跨开发工具链生成”部分。要了解使用'ref-tasks-populate_sdk'任务构建跨开发工具链时所获得的优势，请参阅Yocto项目应用开发和可扩展软件开发工具包（eSDK）手册中的“SDK手册/附录：构建SDK安装程序”部分。

# `prexport` {#ref-classes-prexport}

The `ref-classes-prexport`{.interpreted-text role="ref"} class provides functionality for exporting `PR`{.interpreted-text role="term"} values.

::: note
::: title
Note
:::

This class is not intended to be used directly. Rather, it is enabled when using \"`bitbake-prserv-tool export`\".
:::

# `primport` {#ref-classes-primport}

The `ref-classes-primport`{.interpreted-text role="ref"} class provides functionality for importing `PR`{.interpreted-text role="term"} values.

::: note
::: title
Note
:::

This class is not intended to be used directly. Rather, it is enabled when using \"`bitbake-prserv-tool import`\".
:::

# `prserv` {#ref-classes-prserv}


The `ref-classes-prserv`{.interpreted-text role="ref"} class provides functionality for using a `PR service <dev-manual/packages:working with a pr service>`{.interpreted-text role="ref"} in order to automatically manage the incrementing of the `PR`{.interpreted-text role="term"} variable for each recipe.

> 类`ref-classes-prserv`提供了使用PR服务来自动管理每个食谱的PR变量增量的功能。


This class is enabled by default because it is inherited by the `ref-classes-package`{.interpreted-text role="ref"} class. However, the OpenEmbedded build system will not enable the functionality of this class unless `PRSERV_HOST`{.interpreted-text role="term"} has been set.

> 这个类默认是启用的，因为它是继承自`ref-classes-package`类的。但是，OpenEmbedded构建系统不会启用这个类的功能，除非`PRSERV_HOST`已经设置。

# `ptest` {#ref-classes-ptest}


The `ref-classes-ptest`{.interpreted-text role="ref"} class provides functionality for packaging and installing runtime tests for recipes that build software that provides these tests.

> 类`ref-classes-ptest`提供了用于为构建提供运行时测试的软件打包和安装运行时测试的功能。


This class is intended to be inherited by individual recipes. However, the class\' functionality is largely disabled unless \"ptest\" appears in `DISTRO_FEATURES`{.interpreted-text role="term"}. See the \"`dev-manual/packages:testing packages with ptest`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for more information on ptest.

> 这个类旨在被单个菜谱继承。但是，除非在DISTRO_FEATURES中出现“ptest”，否则该类的功能将被大部分禁用。有关ptest的更多信息，请参阅Yocto项目开发任务手册中的“dev-manual/packages：使用ptest测试包”部分。

# `ptest-gnome` {#ref-classes-ptest-gnome}

Enables package tests (ptests) specifically for GNOME packages, which have tests intended to be executed with `gnome-desktop-testing`.


For information on setting up and running ptests, see the \"`dev-manual/packages:testing packages with ptest`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 要了解有关设置和运行ptest的信息，请参阅Yocto Project开发任务手册中的“dev-manual/packages：使用ptest测试软件包”部分。

# `python3-dir` {#ref-classes-python3-dir}

The `ref-classes-python3-dir`{.interpreted-text role="ref"} class provides the base version, location, and site package location for Python 3.

# `python3native` {#ref-classes-python3native}


The `ref-classes-python3native`{.interpreted-text role="ref"} class supports using the native version of Python 3 built by the build system rather than support of the version provided by the build host.

> 类`ref-classes-python3native`{.interpreted-text role="ref"}支持使用构建系统构建的Python 3的原生版本，而不是构建主机提供的版本。

# `python3targetconfig` {#ref-classes-python3targetconfig}


The `ref-classes-python3targetconfig`{.interpreted-text role="ref"} class supports using the native version of Python 3 built by the build system rather than support of the version provided by the build host, except that the configuration for the target machine is accessible (such as correct installation directories). This also adds a dependency on target `python3`, so should only be used where appropriate in order to avoid unnecessarily lengthening builds.

> 这个ref-classes-python3targetconfig类支持使用构建系统构建的本机Python 3版本，而不是构建主机提供的版本，除了可以访问目标机器的配置（例如正确的安装目录）。这也增加了对目标python3的依赖，因此只应在适当的情况下使用，以避免不必要地延长构建时间。

# `qemu` {#ref-classes-qemu}


The `ref-classes-qemu`{.interpreted-text role="ref"} class provides functionality for recipes that either need QEMU or test for the existence of QEMU. Typically, this class is used to run programs for a target system on the build host using QEMU\'s application emulation mode.

> 这个ref-classes-qemu类为需要QEMU或测试QEMU存在性的食谱提供功能。通常，这个类用于使用QEMU的应用程序仿真模式在构建主机上运行目标系统的程序。

# `recipe_sanity` {#ref-classes-recipe_sanity}


The `ref-classes-recipe_sanity`{.interpreted-text role="ref"} class checks for the presence of any host system recipe prerequisites that might affect the build (e.g. variables that are set or software that is present).

> 这个`ref-classes-recipe_sanity`{.interpreted-text role="ref"} 类检查可能会影响构建的主机系统配方先决条件（例如设置的变量或存在的软件）的存在性。

# `relocatable` {#ref-classes-relocatable}

The `ref-classes-relocatable`{.interpreted-text role="ref"} class enables relocation of binaries when they are installed into the sysroot.


This class makes use of the `ref-classes-chrpath`{.interpreted-text role="ref"} class and is used by both the `ref-classes-cross`{.interpreted-text role="ref"} and `ref-classes-native`{.interpreted-text role="ref"} classes.

> 这个类使用`ref-classes-chrpath`{.interpreted-text role="ref"}类，并被`ref-classes-cross`{.interpreted-text role="ref"}和`ref-classes-native`{.interpreted-text role="ref"}类使用。

# `remove-libtool` {#ref-classes-remove-libtool}


The `ref-classes-remove-libtool`{.interpreted-text role="ref"} class adds a post function to the `ref-tasks-install`{.interpreted-text role="ref"} task to remove all `.la` files installed by `libtool`. Removing these files results in them being absent from both the sysroot and target packages.

> 这个ref-classes-remove-libtool类添加了一个后置函数到ref-tasks-install任务，以便删除libtool安装的所有.la文件。删除这些文件会导致它们不存在于sysroot和目标软件包中。

If a recipe needs the `.la` files to be installed, then the recipe can override the removal by setting `REMOVE_LIBTOOL_LA` to \"0\" as follows:

```
REMOVE_LIBTOOL_LA = "0"
```

::: note
::: title
Note
:::

The `ref-classes-remove-libtool`{.interpreted-text role="ref"} class is not enabled by default.
:::

# `report-error` {#ref-classes-report-error}


The `ref-classes-report-error`{.interpreted-text role="ref"} class supports enabling the `error reporting tool <dev-manual/error-reporting-tool:using the error reporting tool>`{.interpreted-text role="ref"}\", which allows you to submit build error information to a central database.

> 类`ref-classes-report-error`{.interpreted-text role="ref"}支持启用`error reporting tool <dev-manual/error-reporting-tool:using the error reporting tool>`{.interpreted-text role="ref"}，它允许您将构建错误信息提交到中央数据库。


The class collects debug information for recipe, recipe version, task, machine, distro, build system, target system, host distro, branch, commit, and log. From the information, report files using a JSON format are created and stored in `${``LOG_DIR`{.interpreted-text role="term"}`}/error-report`.

> 班級收集食譜，食譜版本，任務，機器，发行版，構建系統，目標系統，主機发行版，分支，提交和日誌的除錯信息。根據這些信息，使用JSON格式創建並儲存在`${``LOG_DIR`{.interpreted-text role="term"}`}/error-report`中的報告文件。

# `rm_work` {#ref-classes-rm-work}


The `ref-classes-rm-work`{.interpreted-text role="ref"} class supports deletion of temporary workspace, which can ease your hard drive demands during builds.

> 类`ref-classes-rm-work`支持删除临时工作区，这可以在构建期间缓解您的硬盘需求。


The OpenEmbedded build system can use a substantial amount of disk space during the build process. A portion of this space is the work files under the `${TMPDIR}/work` directory for each recipe. Once the build system generates the packages for a recipe, the work files for that recipe are no longer needed. However, by default, the build system preserves these files for inspection and possible debugging purposes. If you would rather have these files deleted to save disk space as the build progresses, you can enable `ref-classes-rm-work`{.interpreted-text role="ref"} by adding the following to your `local.conf` file, which is found in the `Build Directory`{.interpreted-text role="term"}:

> 开放式嵌入式构建系统在构建过程中可以使用大量的磁盘空间。其中一部分空间是每个配方下`${TMPDIR}/work`目录下的工作文件。一旦构建系统为配方生成了软件包，这些工作文件就不再需要了。但是，默认情况下，构建系统会保留这些文件以供检查和调试。如果您希望在构建过程中删除这些文件以节省磁盘空间，您可以在`构建目录`{.interpreted-text role="term"}中的`local.conf`文件中添加以下内容来启用`ref-classes-rm-work`{.interpreted-text role="ref"}：

```
INHERIT += "rm_work"
```


If you are modifying and building source code out of the work directory for a recipe, enabling `ref-classes-rm-work`{.interpreted-text role="ref"} will potentially result in your changes to the source being lost. To exclude some recipes from having their work directories deleted by `ref-classes-rm-work`{.interpreted-text role="ref"}, you can add the names of the recipe or recipes you are working on to the `RM_WORK_EXCLUDE`{.interpreted-text role="term"} variable, which can also be set in your `local.conf` file. Here is an example:

> 如果您正在修改和构建工作目录中的源代码以供食谱使用，启用`ref-classes-rm-work`{.interpreted-text role="ref"}可能会导致您对源代码的更改丢失。要排除某些食谱不被`ref-classes-rm-work`{.interpreted-text role="ref"}删除其工作目录，您可以将正在工作的食谱的名称添加到`RM_WORK_EXCLUDE`{.interpreted-text role="term"}变量中，该变量也可以在您的`local.conf`文件中设置。下面是一个例子：

```
RM_WORK_EXCLUDE += "busybox glibc"
```

# `rootfs*` {#ref-classes-rootfs*}


The `ref-classes-rootfs*`{.interpreted-text role="ref"} classes support creating the root filesystem for an image and consist of the following classes:

> 这些`ref-classes-rootfs*`{.interpreted-text role="ref"}类支持创建镜像的根文件系统，包括以下类：


- The `rootfs-postcommands <ref-classes-rootfs*>`{.interpreted-text role="ref"} class, which defines filesystem post-processing functions for image recipes.

> `- 用于图像配方的文件系统后处理函数定义的`rootfs-postcommands <ref-classes-rootfs*>`{.interpreted-text role="ref"} 类。

- The `rootfs_deb <ref-classes-rootfs*>`{.interpreted-text role="ref"} class, which supports creation of root filesystems for images built using `.deb` packages.

> 这个rootfs_deb<ref-classes-rootfs*>{.interpreted-text role="ref"}类支持使用`.deb`包构建的映像创建根文件系统。

- The `rootfs_rpm <ref-classes-rootfs*>`{.interpreted-text role="ref"} class, which supports creation of root filesystems for images built using `.rpm` packages.

> 这个`rootfs_rpm <ref-classes-rootfs*>`{.interpreted-text role="ref"} 类支持使用`.rpm`软件包构建的映像文件创建根文件系统。

- The `rootfs_ipk <ref-classes-rootfs*>`{.interpreted-text role="ref"} class, which supports creation of root filesystems for images built using `.ipk` packages.

> - `rootfs_ipk <ref-classes-rootfs*>`{.interpreted-text role="ref"} 类支持使用`.ipk` 包构建的映像文件系统的创建。

- The `rootfsdebugfiles <ref-classes-rootfs*>`{.interpreted-text role="ref"} class, which installs additional files found on the build host directly into the root filesystem.

> 这个 rootfsdebugfiles <ref-classes-rootfs*>{.interpreted-text role="ref"} 类，它会把构建主机上发现的额外文件安装到根文件系统中。


The root filesystem is created from packages using one of the `ref-classes-rootfs*`{.interpreted-text role="ref"} files as determined by the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable.

> 根文件系统是使用`ref-classes-rootfs*`{.interpreted-text role="ref"}文件中的一个（由`PACKAGE_CLASSES`{.interpreted-text role="term"}变量确定）创建的包。


For information on how root filesystem images are created, see the \"`overview-manual/concepts:image generation`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> 对于如何创建根文件系统映像的信息，请参阅Yocto项目概述和概念手册中的“overview-manual / concepts：image generation”部分。

# `rust` {#ref-classes-rust}


The `ref-classes-rust`{.interpreted-text role="ref"} class is an internal class which is just used in the \"rust\" recipe, to build the Rust compiler and runtime library. Except for this recipe, it is not intended to be used directly.

> 类`ref-classes-rust`{.interpreted-text role="ref"}仅在“rust”配方中使用，用于构建Rust编译器和运行时库，是一个内部类。除了这个配方，不打算直接使用它。

# `rust-common` {#ref-classes-rust-common}


The `ref-classes-rust-common`{.interpreted-text role="ref"} class is an internal class to the `ref-classes-cargo_common`{.interpreted-text role="ref"} and `ref-classes-rust`{.interpreted-text role="ref"} classes and is not intended to be used directly.

> `ref-classes-rust-common`{.interpreted-text role="ref"}类是`ref-classes-cargo_common`{.interpreted-text role="ref"}和`ref-classes-rust`{.interpreted-text role="ref"}类的内部类，不应直接使用。

# `sanity` {#ref-classes-sanity}


The `ref-classes-sanity`{.interpreted-text role="ref"} class checks to see if prerequisite software is present on the host system so that users can be notified of potential problems that might affect their build. The class also performs basic user configuration checks from the `local.conf` configuration file to prevent common mistakes that cause build failures. Distribution policy usually determines whether to include this class.

> `ref-classes-sanity`类检查主机系统上是否存在先决软件，以便通知用户可能影响其构建的潜在问题。该类还从`local.conf`配置文件执行基本的用户配置检查，以防止导致构建失败的常见错误。通常由分发策略决定是否包含此类。

# `scons` {#ref-classes-scons}


The `ref-classes-scons`{.interpreted-text role="ref"} class supports recipes that need to build software that uses the SCons build system. You can use the `EXTRA_OESCONS`{.interpreted-text role="term"} variable to specify additional configuration options you want to pass SCons command line.

> 类`ref-classes-scons`支持需要使用SCons构建系统构建软件的配方。您可以使用变量`EXTRA_OESCONS`指定要传递给SCons命令行的其他配置选项。

# `sdl` {#ref-classes-sdl}


The `ref-classes-sdl`{.interpreted-text role="ref"} class supports recipes that need to build software that uses the Simple DirectMedia Layer (SDL) library.

> 这个ref-classes-sdl类支持需要使用Simple DirectMedia Layer（SDL）库构建软件的食谱。

# `python_setuptools_build_meta` {#ref-classes-python_setuptools_build_meta}


The `ref-classes-python_setuptools_build_meta`{.interpreted-text role="ref"} class enables building Python modules which declare the [PEP-517](https://www.python.org/dev/peps/pep-0517/) compliant `setuptools.build_meta` `build-backend` in the `[build-system]` section of `pyproject.toml` (See [PEP-518](https://www.python.org/dev/peps/pep-0518/)).

> 类ref-classes-python_setuptools_build_meta{.interpreted-text role="ref"}可以构建声明[PEP-517](https://www.python.org/dev/peps/pep-0517/)兼容的setuptools.build_meta build-backend的Python模块，该模块位于pyproject.toml的[build-system]部分（参见[PEP-518](https://www.python.org/dev/peps/pep-0518/)）。

Python modules built with `setuptools.build_meta` can be pure Python or include `C` or `Rust` extensions).

Internally this uses the `ref-classes-python_pep517`{.interpreted-text role="ref"} class.

# `setuptools3` {#ref-classes-setuptools3}


The `ref-classes-setuptools3`{.interpreted-text role="ref"} class supports Python version 3.x extensions that use build systems based on `setuptools` (e.g. only have a `setup.py` and have not migrated to the official `pyproject.toml` format). If your recipe uses these build systems, the recipe needs to inherit the `ref-classes-setuptools3`{.interpreted-text role="ref"} class.

> ref-classes-setuptools3类支持使用基于setuptools（例如只有setup.py，没有迁移到官方pyproject.toml格式）的Python 3.x扩展。如果您的配方使用这些构建系统，则该配方需要继承ref-classes-setuptools3类。

> ::: note
> ::: title
> Note
> :::
>
> The `ref-classes-setuptools3`{.interpreted-text role="ref"} class `ref-tasks-compile`{.interpreted-text role="ref"} task now calls `setup.py bdist_wheel` to build the `wheel` binary archive format (See [PEP-427](https://www.python.org/dev/peps/pep-0427/)).
>
> A consequence of this is that legacy software still using deprecated `distutils` from the Python standard library cannot be packaged as `wheels`. A common solution is the replace `from distutils.core import setup` with `from setuptools import setup`.
> :::
>
> ::: note
> ::: title
> Note
> :::
>
> The `ref-classes-setuptools3`{.interpreted-text role="ref"} class `ref-tasks-install`{.interpreted-text role="ref"} task now installs the `wheel` binary archive. In current versions of `setuptools` the legacy `setup.py install` method is deprecated. If the `setup.py` cannot be used with wheels, for example it creates files outside of the Python module or standard entry points, then `ref-classes-setuptools3_legacy`{.interpreted-text role="ref"} should be used.
> :::

# `setuptools3_legacy` {#ref-classes-setuptools3_legacy}


The `ref-classes-setuptools3_legacy`{.interpreted-text role="ref"} class supports Python version 3.x extensions that use build systems based on `setuptools` (e.g. only have a `setup.py` and have not migrated to the official `pyproject.toml` format). Unlike `ref-classes-setuptools3`{.interpreted-text role="ref"}, this uses the traditional `setup.py` `build` and `install` commands and not wheels. This use of `setuptools` like this is [deprecated](https://github.com/pypa/setuptools/blob/main/CHANGES.rst#v5830) but still relatively common.

> 类`ref-classes-setuptools3_legacy`{.interpreted-text role="ref"}支持使用基于`setuptools`的构建系统的Python 3.x扩展（例如，只有`setup.py`，而没有迁移到官方的`pyproject.toml`格式）。与`ref-classes-setuptools3`{.interpreted-text role="ref"}不同，这使用传统的`setup.py``build`和`install`命令，而不是轮子。这种使用`setuptools`的方式已经[弃用](https://github.com/pypa/setuptools/blob/main/CHANGES.rst#v5830)，但仍然相对普遍。

# `setuptools3-base` {#ref-classes-setuptools3-base}


The `ref-classes-setuptools3-base`{.interpreted-text role="ref"} class provides a reusable base for other classes that support building Python version 3.x extensions. If you need functionality that is not provided by the `ref-classes-setuptools3`{.interpreted-text role="ref"} class, you may want to `inherit setuptools3-base`. Some recipes do not need the tasks in the `ref-classes-setuptools3`{.interpreted-text role="ref"} class and inherit this class instead.

> 类`ref-classes-setuptools3-base`{.interpreted-text role="ref"}提供了一个可重用的基础，用于支持构建Python版本3.x的扩展。如果您需要`ref-classes-setuptools3`{.interpreted-text role="ref"}类未提供的功能，您可能需要继承`setuptools3-base`。有些食谱不需要`ref-classes-setuptools3`{.interpreted-text role="ref"}类中的任务，而是继承这个类。

# `sign_rpm` {#ref-classes-sign_rpm}

The `ref-classes-sign_rpm`{.interpreted-text role="ref"} class supports generating signed RPM packages.

# `siteconfig` {#ref-classes-siteconfig}


The `ref-classes-siteconfig`{.interpreted-text role="ref"} class provides functionality for handling site configuration. The class is used by the `ref-classes-autotools`{.interpreted-text role="ref"} class to accelerate the `ref-tasks-configure`{.interpreted-text role="ref"} task.

> 类`ref-classes-siteconfig`{.interpreted-text role="ref"}提供了处理网站配置的功能。该类被类`ref-classes-autotools`{.interpreted-text role="ref"}用来加速任务`ref-tasks-configure`{.interpreted-text role="ref"}。

# `siteinfo` {#ref-classes-siteinfo}


The `ref-classes-siteinfo`{.interpreted-text role="ref"} class provides information about the targets that might be needed by other classes or recipes.

> 这个ref-classes-siteinfo类提供有关其他类或配方可能需要的目标的信息。


As an example, consider Autotools, which can require tests that must execute on the target hardware. Since this is not possible in general when cross compiling, site information is used to provide cached test results so these tests can be skipped over but still make the correct values available. The `meta/site directory` contains test results sorted into different categories such as architecture, endianness, and the `libc` used. Site information provides a list of files containing data relevant to the current build in the `CONFIG_SITE`{.interpreted-text role="term"} variable that Autotools automatically picks up.

> 举个例子，考虑自动工具，它可以要求必须在目标硬件上执行的测试。由于这在跨编译时通常是不可能的，因此使用站点信息以提供缓存的测试结果，以便可以跳过这些测试，但仍然可以获得正确的值。`meta/site directory`包含按不同类别排列的测试结果，例如架构、字节序和使用的`libc`。站点信息提供一个文件列表，其中包含与当前构建相关的数据，该文件列表存储在自动工具自动检索的`CONFIG_SITE`{.interpreted-text role="term"}变量中。


The class also provides variables like `SITEINFO_ENDIANNESS`{.interpreted-text role="term"} and `SITEINFO_BITS`{.interpreted-text role="term"} that can be used elsewhere in the metadata.

> 这个类还提供可用于元数据中的其他地方的变量，如`SITEINFO_ENDIANNESS`{.interpreted-text role="term"}和`SITEINFO_BITS`{.interpreted-text role="term"}。

# `sstate` {#ref-classes-sstate}


The `ref-classes-sstate`{.interpreted-text role="ref"} class provides support for Shared State (sstate). By default, the class is enabled through the `INHERIT_DISTRO`{.interpreted-text role="term"} variable\'s default value.

> 类`ref-classes-sstate`提供了共享状态（sstate）的支持。默认情况下，通过`INHERIT_DISTRO`变量的默认值启用该类。


For more information on sstate, see the \"`overview-manual/concepts:shared state cache`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> 要了解有关sstate的更多信息，请参阅Yocto项目概览与概念手册中的“共享状态缓存”部分。

# `staging` {#ref-classes-staging}


The `ref-classes-staging`{.interpreted-text role="ref"} class installs files into individual recipe work directories for sysroots. The class contains the following key tasks:

> ref-classes-staging类将文件安装到sysroots的各个配方工作目录中。该类包含以下关键任务：


- The `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} task, which is responsible for handing the files that end up in the recipe sysroots.

> 任务`ref-tasks-populate_sysroot`{.interpreted-text role="ref"}负责处理最终会进入配方系统根目录的文件。

- The `ref-tasks-prepare_recipe_sysroot`{.interpreted-text role="ref"} task (a \"partner\" task to the `populate_sysroot` task), which installs the files into the individual recipe work directories (i.e. `WORKDIR`{.interpreted-text role="term"}).

> 任务'ref-tasks-prepare_recipe_sysroot'（与任务'populate_sysroot'相关的"伙伴"任务）将文件安装到各个食谱工作目录（即'WORKDIR'）中。

The code in the `ref-classes-staging`{.interpreted-text role="ref"} class is complex and basically works in two stages:


- *Stage One:* The first stage addresses recipes that have files they want to share with other recipes that have dependencies on the originating recipe. Normally these dependencies are installed through the `ref-tasks-install`{.interpreted-text role="ref"} task into `${``D`{.interpreted-text role="term"}`}`. The `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} task copies a subset of these files into `${SYSROOT_DESTDIR}`. This subset of files is controlled by the `SYSROOT_DIRS`{.interpreted-text role="term"}, `SYSROOT_DIRS_NATIVE`{.interpreted-text role="term"}, and `SYSROOT_DIRS_IGNORE`{.interpreted-text role="term"} variables.

> *第一阶段：* 第一阶段处理想要与其他有依赖于源菜谱的菜谱共享文件的菜谱。通常，这些依赖项通过`ref-tasks-install`{.interpreted-text role="ref"}任务安装到`${``D`{.interpreted-text role="term"}`}`中。`ref-tasks-populate_sysroot`{.interpreted-text role="ref"}任务将这些文件的一个子集复制到`${SYSROOT_DESTDIR}`中。此子集文件由`SYSROOT_DIRS`{.interpreted-text role="term"}、`SYSROOT_DIRS_NATIVE`{.interpreted-text role="term"}和`SYSROOT_DIRS_IGNORE`{.interpreted-text role="term"}变量控制。

  ::: note
  ::: title
  Note
  :::


  Additionally, a recipe can customize the files further by declaring a processing function in the `SYSROOT_PREPROCESS_FUNCS`{.interpreted-text role="term"} variable.

> 此外，通过在`SYSROOT_PREPROCESS_FUNCS`变量中声明处理函数，配方可以进一步定制文件。
  :::


  A shared state (sstate) object is built from these files and the files are placed into a subdirectory of `structure-build-tmp-sysroots-components`{.interpreted-text role="ref"}. The files are scanned for hardcoded paths to the original installation location. If the location is found in text files, the hardcoded locations are replaced by tokens and a list of the files needing such replacements is created. These adjustments are referred to as \"FIXMEs\". The list of files that are scanned for paths is controlled by the `SSTATE_SCAN_FILES`{.interpreted-text role="term"} variable.

> 一个共享状态(sstate)对象是由这些文件构建的，并且这些文件被放置在'structure-build-tmp-sysroots-components'的子目录中。文件会被扫描以查找指向原始安装位置的固定路径。如果在文本文件中发现了位置，那么这些固定的位置将被标记替换，并且会创建一个需要这样替换的文件列表。这些调整被称为“FIXMEs”。被用来扫描路径的文件列表由'SSTATE_SCAN_FILES'变量控制。

- *Stage Two:* The second stage addresses recipes that want to use something from another recipe and declare a dependency on that recipe through the `DEPENDS`{.interpreted-text role="term"} variable. The recipe will have a `ref-tasks-prepare_recipe_sysroot`{.interpreted-text role="ref"} task and when this task executes, it creates the `recipe-sysroot` and `recipe-sysroot-native` in the recipe work directory (i.e. `WORKDIR`{.interpreted-text role="term"}). The OpenEmbedded build system creates hard links to copies of the relevant files from `sysroots-components` into the recipe work directory.

> *第二阶段：*第二阶段处理想要使用另一个配方中的东西并通过`DEPENDS`{.interpreted-text role="term"}变量声明对该配方的依赖关系的配方。该配方将具有`ref-tasks-prepare_recipe_sysroot`{.interpreted-text role="ref"}任务，当该任务执行时，它会在配方工作目录（即`WORKDIR`{.interpreted-text role="term"}）中创建`recipe-sysroot`和`recipe-sysroot-native`。OpenEmbedded构建系统将相关文件的硬链接副本从`sysroots-components`复制到配方工作目录中。

  ::: note
  ::: title
  Note
  :::

  If hard links are not possible, the build system uses actual copies.
  :::

  The build system then addresses any \"FIXMEs\" to paths as defined from the list created in the first stage.

  Finally, any files in `${bindir}` within the sysroot that have the prefix \"`postinst-`\" are executed.

  ::: note
  ::: title
  Note
  :::


  Although such sysroot post installation scripts are not recommended for general use, the files do allow some issues such as user creation and module indexes to be addressed.

> 尽管不推荐一般用户使用这种sysroot安装后脚本，但是这些文件确实可以解决一些问题，比如用户创建和模块索引。
  :::


  Because recipes can have other dependencies outside of `DEPENDS`{.interpreted-text role="term"} (e.g. `do_unpack[depends] += "tar-native:do_populate_sysroot"`), the sysroot creation function `extend_recipe_sysroot` is also added as a pre-function for those tasks whose dependencies are not through `DEPENDS`{.interpreted-text role="term"} but operate similarly.

> 由于食谱可能有DEPENDS之外的其他依赖（例如：do_unpack[depends] += "tar-native:do_populate_sysroot"），因此也将sysroot创建函数extend_recipe_sysroot添加为那些依赖不是通过DEPENDS但类似操作的任务的预功能。


  When installing dependencies into the sysroot, the code traverses the dependency graph and processes dependencies in exactly the same way as the dependencies would or would not be when installed from sstate. This processing means, for example, a native tool would have its native dependencies added but a target library would not have its dependencies traversed or installed. The same sstate dependency code is used so that builds should be identical regardless of whether sstate was used or not. For a closer look, see the `setscene_depvalid()` function in the `ref-classes-sstate`{.interpreted-text role="ref"} class.

> 当安装依赖项到sysroot时，代码会遍历依赖关系图，并以与从sstate安装时完全相同的方式处理依赖项。这种处理意味着，例如，原生工具会添加其原生依赖项，但目标库不会遍历或安装其依赖项。使用相同的sstate依赖代码，因此无论是否使用sstate，构建结果都应该相同。要进一步了解，请参阅`ref-classes-sstate`{.interpreted-text role="ref"}类中的`setscene_depvalid（）`函数。


  The build system is careful to maintain manifests of the files it installs so that any given dependency can be installed as needed. The sstate hash of the installed item is also stored so that if it changes, the build system can reinstall it.

> 系统构建时会仔细维护安装文件的清单，以便根据需要安装任何给定的依赖项。还会存储安装项的sstate哈希，以便如果它发生更改，构建系统可以重新安装它。

# `syslinux` {#ref-classes-syslinux}

The `ref-classes-syslinux`{.interpreted-text role="ref"} class provides syslinux-specific functions for building bootable images.

The class supports the following variables:


- `INITRD`{.interpreted-text role="term"}: Indicates list of filesystem images to concatenate and use as an initial RAM disk (initrd). This variable is optional.

> INITRD：指示要连接并用作初始RAM盘（initrd）的文件系统图像列表。此变量是可选的。
- `ROOTFS`{.interpreted-text role="term"}: Indicates a filesystem image to include as the root filesystem. This variable is optional.
- `AUTO_SYSLINUXMENU`{.interpreted-text role="term"}: Enables creating an automatic menu when set to \"1\".
- `LABELS`{.interpreted-text role="term"}: Lists targets for automatic configuration.
- `APPEND`{.interpreted-text role="term"}: Lists append string overrides for each label.

- `SYSLINUX_OPTS`{.interpreted-text role="term"}: Lists additional options to add to the syslinux file. Semicolon characters separate multiple options.

> `- SYSLINUX_OPTS`：列出要添加到syslinux文件的其他选项。分号字符分隔多个选项。
- `SYSLINUX_SPLASH`{.interpreted-text role="term"}: Lists a background for the VGA boot menu when you are using the boot menu.
- `SYSLINUX_DEFAULT_CONSOLE`{.interpreted-text role="term"}: Set to \"console=ttyX\" to change kernel boot default console.

- `SYSLINUX_SERIAL`{.interpreted-text role="term"}: Sets an alternate serial port. Or, turns off serial when the variable is set with an empty string.

> `SYSLINUX_SERIAL`：设置一个替代的串行端口，或者当变量被设置为空字符串时关闭串行。
- `SYSLINUX_SERIAL_TTY`{.interpreted-text role="term"}: Sets an alternate \"console=tty\...\" kernel boot argument.

# `systemd` {#ref-classes-systemd}

The `ref-classes-systemd`{.interpreted-text role="ref"} class provides support for recipes that install systemd unit files.

The functionality for this class is disabled unless you have \"systemd\" in `DISTRO_FEATURES`{.interpreted-text role="term"}.


Under this class, the recipe or Makefile (i.e. whatever the recipe is calling during the `ref-tasks-install`{.interpreted-text role="ref"} task) installs unit files into `${``D`{.interpreted-text role="term"}`}${systemd_unitdir}/system`. If the unit files being installed go into packages other than the main package, you need to set `SYSTEMD_PACKAGES`{.interpreted-text role="term"} in your recipe to identify the packages in which the files will be installed.

> 在这个类中，食谱或Makefile（即`ref-tasks-install`{.interpreted-text role="ref"}任务期间调用的任何食谱）将单元文件安装到`${``D`{.interpreted-text role="term"}`}${systemd_unitdir}/system`中。如果要安装的单元文件不在主软件包中，您需要在食谱中设置`SYSTEMD_PACKAGES`{.interpreted-text role="term"}以标识将文件安装的软件包。


You should set `SYSTEMD_SERVICE`{.interpreted-text role="term"} to the name of the service file. You should also use a package name override to indicate the package to which the value applies. If the value applies to the recipe\'s main package, use `${``PN`{.interpreted-text role="term"}`}`. Here is an example from the connman recipe:

> 你应该将`SYSTEMD_SERVICE`设置为服务文件的名称。你也应该使用包名覆盖来指示该值适用于哪个包。如果该值适用于配方的主要包，请使用`${PN}`。这里是connman配方的一个例子：

```
SYSTEMD_SERVICE:${PN} = "connman.service"
```

Services are set up to start on boot automatically unless you have set `SYSTEMD_AUTO_ENABLE`{.interpreted-text role="term"} to \"disable\".


For more information on `ref-classes-systemd`{.interpreted-text role="ref"}, see the \"`dev-manual/init-manager:selecting an initialization manager`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 对于`ref-classes-systemd`{.interpreted-text role="ref"}的更多信息，请参阅Yocto Project开发任务手册中的“`dev-manual/init-manager:selecting an initialization manager`{.interpreted-text role="ref"}”部分。

# `systemd-boot` {#ref-classes-systemd-boot}


The `ref-classes-systemd-boot`{.interpreted-text role="ref"} class provides functions specific to the systemd-boot bootloader for building bootable images. This is an internal class and is not intended to be used directly.

> 这个`ref-classes-systemd-boot`{.interpreted-text role="ref"}类提供了特定于systemd-boot启动程序的功能，用于构建可引导映像。这是一个内部类，不建议直接使用。

::: note
::: title
Note
:::


The `ref-classes-systemd-boot`{.interpreted-text role="ref"} class is a result from merging the `gummiboot` class used in previous Yocto Project releases with the `systemd` project.

> 这个`ref-classes-systemd-boot`{.interpreted-text role="ref"}类是在之前的Yocto Project发布中使用的`gummiboot`类与`systemd`项目合并的结果。
:::


Set the `EFI_PROVIDER`{.interpreted-text role="term"} variable to \"`ref-classes-systemd-boot`{.interpreted-text role="ref"}\" to use this class. Doing so creates a standalone EFI bootloader that is not dependent on systemd.

> 设置`EFI_PROVIDER`变量为"ref-classes-systemd-boot"以使用此类。这样做会创建一个独立的EFI引导程序，不依赖于systemd。


For information on more variables used and supported in this class, see the `SYSTEMD_BOOT_CFG`{.interpreted-text role="term"}, `SYSTEMD_BOOT_ENTRIES`{.interpreted-text role="term"}, and `SYSTEMD_BOOT_TIMEOUT`{.interpreted-text role="term"} variables.

> 要了解本类中使用和支持的更多变量，请参阅`SYSTEMD_BOOT_CFG`{.interpreted-text role="term"}、`SYSTEMD_BOOT_ENTRIES`{.interpreted-text role="term"}和`SYSTEMD_BOOT_TIMEOUT`{.interpreted-text role="term"}变量。

You can also see the [Systemd-boot documentation](https://www.freedesktop.org/wiki/Software/systemd/systemd-boot/) for more information.

# `terminal` {#ref-classes-terminal}


The `ref-classes-terminal`{.interpreted-text role="ref"} class provides support for starting a terminal session. The `OE_TERMINAL`{.interpreted-text role="term"} variable controls which terminal emulator is used for the session.

> `ref-classes-terminal`{.interpreted-text role="ref"}类提供了启动终端会话的支持。`OE_TERMINAL`{.interpreted-text role="term"}变量控制用于会话的终端模拟器。


Other classes use the `ref-classes-terminal`{.interpreted-text role="ref"} class anywhere a separate terminal session needs to be started. For example, the `ref-classes-patch`{.interpreted-text role="ref"} class assuming `PATCHRESOLVE`{.interpreted-text role="term"} is set to \"user\", the `ref-classes-cml1`{.interpreted-text role="ref"} class, and the `ref-classes-devshell`{.interpreted-text role="ref"} class all use the `ref-classes-terminal`{.interpreted-text role="ref"} class.

> 其他类可以在需要启动单独的终端会话的任何地方使用“ref-classes-terminal”类。例如，假设“PATCHRESOLVE”被设置为“user”，“ref-classes-cml1”类以及“ref-classes-devshell”类都使用“ref-classes-terminal”类。

# `testimage` {#ref-classes-testimage}


The `ref-classes-testimage`{.interpreted-text role="ref"} class supports running automated tests against images using QEMU and on actual hardware. The classes handle loading the tests and starting the image. To use the classes, you need to perform steps to set up the environment.

> 类`ref-classes-testimage`{.interpreted-text role="ref"}支持使用QEMU和实际硬件对图像执行自动化测试。该类处理加载测试和启动图像。要使用这些类，您需要执行步骤来设置环境。

To enable this class, add the following to your configuration:

```
IMAGE_CLASSES += "testimage"
```

The tests are commands that run on the target system over `ssh`. Each test is written in Python and makes use of the `unittest` module.

The `ref-classes-testimage`{.interpreted-text role="ref"} class runs tests on an image when called using the following:

```
$ bitbake -c testimage image
```


Alternatively, if you wish to have tests automatically run for each image after it is built, you can set `TESTIMAGE_AUTO`{.interpreted-text role="term"}:

> 如果您希望每次构建图像后自动运行测试，您可以设置`TESTIMAGE_AUTO`：

```
TESTIMAGE_AUTO = "1"
```


For information on how to enable, run, and create new tests, see the \"`dev-manual/runtime-testing:performing automated runtime testing`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 要了解如何启用、运行和创建新测试，请参阅Yocto Project开发任务手册中的“dev-manual/runtime-testing：执行自动运行时测试”部分。

# `testsdk` {#ref-classes-testsdk}


This class supports running automated tests against software development kits (SDKs). The `ref-classes-testsdk`{.interpreted-text role="ref"} class runs tests on an SDK when called using the following:

> 这个类支持对软件开发工具包（SDKs）进行自动测试。`ref-classes-testsdk`{.interpreted-text role="ref"} 类可以通过以下方式调用来运行对 SDK 的测试：

```
$ bitbake -c testsdk image
```

::: note
::: title
Note
:::


Best practices include using `IMAGE_CLASSES`{.interpreted-text role="term"} rather than `INHERIT`{.interpreted-text role="term"} to inherit the `ref-classes-testsdk`{.interpreted-text role="ref"} class for automated SDK testing.

> 最佳实践包括使用`IMAGE_CLASSES`{.interpreted-text role="term"}而不是`INHERIT`{.interpreted-text role="term"}来继承`ref-classes-testsdk`{.interpreted-text role="ref"}类以进行自动SDK测试。
:::

# `texinfo` {#ref-classes-texinfo}


This class should be inherited by recipes whose upstream packages invoke the `texinfo` utilities at build-time. Native and cross recipes are made to use the dummy scripts provided by `texinfo-dummy-native`, for improved performance. Target architecture recipes use the genuine Texinfo utilities. By default, they use the Texinfo utilities on the host system.

> 这个类应该被源自的软件包在构建时调用`texinfo`工具的配方所继承。本地和跨平台的配方使用`texinfo-dummy-native`提供的虚拟脚本，以提高性能。目标架构配方使用真正的Texinfo工具。默认情况下，它们在主机系统上使用Texinfo工具。

::: note
::: title
Note
:::


If you want to use the Texinfo recipe shipped with the build system, you can remove \"texinfo-native\" from `ASSUME_PROVIDED`{.interpreted-text role="term"} and makeinfo from `SANITY_REQUIRED_UTILITIES`{.interpreted-text role="term"}.

> 如果你想使用构建系统提供的Texinfo食谱，你可以从`ASSUME_PROVIDED`{.interpreted-text role="term"}中移除"texinfo-native"，并将makeinfo从`SANITY_REQUIRED_UTILITIES`{.interpreted-text role="term"}中移除。
:::

# `toaster` {#ref-classes-toaster}


The `ref-classes-toaster`{.interpreted-text role="ref"} class collects information about packages and images and sends them as events that the BitBake user interface can receive. The class is enabled when the Toaster user interface is running.

> `ref-classes-toaster`类收集有关包和图像的信息，并将其作为事件发送，BitBake用户界面可以接收。当Toaster用户界面运行时，该类启用。

This class is not intended to be used directly.

# `toolchain-scripts` {#ref-classes-toolchain-scripts}

The `ref-classes-toolchain-scripts`{.interpreted-text role="ref"} class provides the scripts used for setting up the environment for installed SDKs.

# `typecheck` {#ref-classes-typecheck}


The `ref-classes-typecheck`{.interpreted-text role="ref"} class provides support for validating the values of variables set at the configuration level against their defined types. The OpenEmbedded build system allows you to define the type of a variable using the \"type\" varflag. Here is an example:

> `ref-classes-typecheck`类提供了支持，用于验证配置级别设置的变量的值与其定义的类型是否匹配。OpenEmbedded构建系统允许您使用“type”varflag来定义变量的类型。以下是一个例子：

```
IMAGE_FEATURES[type] = "list"
```

# `uboot-config` {#ref-classes-uboot-config}


The `ref-classes-uboot-config`{.interpreted-text role="ref"} class provides support for U-Boot configuration for a machine. Specify the machine in your recipe as follows:

> `ref-classes-uboot-config`{.interpreted-text role="ref"}类提供对机器的U-Boot配置的支持。在你的配方中按照以下方式指定机器：

```
UBOOT_CONFIG ??= <default>
UBOOT_CONFIG[foo] = "config,images"
```

You can also specify the machine using this method:

```
UBOOT_MACHINE = "config"
```

See the `UBOOT_CONFIG`{.interpreted-text role="term"} and `UBOOT_MACHINE`{.interpreted-text role="term"} variables for additional information.

# `uboot-sign` {#ref-classes-uboot-sign}


The `ref-classes-uboot-sign`{.interpreted-text role="ref"} class provides support for U-Boot verified boot. It is intended to be inherited from U-Boot recipes.

> 这个ref-classes-uboot-sign类提供了U-Boot验证启动的支持。它旨在从U-Boot食谱继承。

Here are variables used by this class:

- `SPL_MKIMAGE_DTCOPTS`{.interpreted-text role="term"}: DTC options for U-Boot `mkimage` when building the FIT image.
- `SPL_SIGN_ENABLE`{.interpreted-text role="term"}: enable signing the FIT image.
- `SPL_SIGN_KEYDIR`{.interpreted-text role="term"}: directory containing the signing keys.
- `SPL_SIGN_KEYNAME`{.interpreted-text role="term"}: base filename of the signing keys.
- `UBOOT_FIT_ADDRESS_CELLS`{.interpreted-text role="term"}: `#address-cells` value for the FIT image.
- `UBOOT_FIT_DESC`{.interpreted-text role="term"}: description string encoded into the FIT image.
- `UBOOT_FIT_GENERATE_KEYS`{.interpreted-text role="term"}: generate the keys if they don\'t exist yet.
- `UBOOT_FIT_HASH_ALG`{.interpreted-text role="term"}: hash algorithm for the FIT image.
- `UBOOT_FIT_KEY_GENRSA_ARGS`{.interpreted-text role="term"}: `openssl genrsa` arguments.
- `UBOOT_FIT_KEY_REQ_ARGS`{.interpreted-text role="term"}: `openssl req` arguments.
- `UBOOT_FIT_SIGN_ALG`{.interpreted-text role="term"}: signature algorithm for the FIT image.
- `UBOOT_FIT_SIGN_NUMBITS`{.interpreted-text role="term"}: size of the private key for FIT image signing.
- `UBOOT_FIT_KEY_SIGN_PKCS`{.interpreted-text role="term"}: algorithm for the public key certificate for FIT image signing.
- `UBOOT_FITIMAGE_ENABLE`{.interpreted-text role="term"}: enable the generation of a U-Boot FIT image.
- `UBOOT_MKIMAGE_DTCOPTS`{.interpreted-text role="term"}: DTC options for U-Boot `mkimage` when rebuilding the FIT image containing the kernel.


See U-Boot\'s documentation for details about [verified boot](https://source.denx.de/u-boot/u-boot/-/blob/master/doc/uImage.FIT/verified-boot.txt) and the [signature process](https://source.denx.de/u-boot/u-boot/-/blob/master/doc/uImage.FIT/signature.txt).

> 查看U-Boot的文档，了解有关[已验证启动](https://source.denx.de/u-boot/u-boot/-/blob/master/doc/uImage.FIT/verified-boot.txt)和[签名过程](https://source.denx.de/u-boot/u-boot/-/blob/master/doc/uImage.FIT/signature.txt)的详细信息。

See also the description of `ref-classes-kernel-fitimage`{.interpreted-text role="ref"} class, which this class imitates.

# `uninative` {#ref-classes-uninative}


Attempts to isolate the build system from the host distribution\'s C library in order to make re-use of native shared state artifacts across different host distributions practical. With this class enabled, a tarball containing a pre-built C library is downloaded at the start of the build. In the Poky reference distribution this is enabled by default through `meta/conf/distro/include/yocto-uninative.inc`. Other distributions that do not derive from poky can also \"`require conf/distro/include/yocto-uninative.inc`\" to use this. Alternatively if you prefer, you can build the uninative-tarball recipe yourself, publish the resulting tarball (e.g. via HTTP) and set `UNINATIVE_URL` and `UNINATIVE_CHECKSUM` appropriately. For an example, see the `meta/conf/distro/include/yocto-uninative.inc`.

> 尝试将构建系统与主机发行版的C库隔离开来，以便在不同的主机发行版之间可以重复使用本地共享状态的工件。启用此类后，在构建开始时会下载一个包含预构建C库的压缩包。在Poky参考发行版中，这是通过`meta/conf/distro/include/yocto-uninative.inc`默认启用的。其他不从Poky派生的发行版也可以“要求conf/distro/include/yocto-uninative.inc”来使用它。或者，如果您愿意，您可以自己构建uninative-tarball配方，发布生成的压缩包（例如通过HTTP），并适当设置`UNINATIVE_URL`和`UNINATIVE_CHECKSUM`。有关示例，请参见`meta/conf/distro/include/yocto-uninative.inc`。


The `ref-classes-uninative`{.interpreted-text role="ref"} class is also used unconditionally by the extensible SDK. When building the extensible SDK, `uninative-tarball` is built and the resulting tarball is included within the SDK.

> 类`ref-classes-uninative`{.interpreted-text role="ref"}也被可扩展SDK无条件地使用。在构建可扩展SDK时，会构建`uninative-tarball`，并将生成的tarball包含在SDK中。

# `update-alternatives` {#ref-classes-update-alternatives}


The `ref-classes-update-alternatives`{.interpreted-text role="ref"} class helps the alternatives system when multiple sources provide the same command. This situation occurs when several programs that have the same or similar function are installed with the same name. For example, the `ar` command is available from the `busybox`, `binutils` and `elfutils` packages. The `ref-classes-update-alternatives`{.interpreted-text role="ref"} class handles renaming the binaries so that multiple packages can be installed without conflicts. The `ar` command still works regardless of which packages are installed or subsequently removed. The class renames the conflicting binary in each package and symlinks the highest priority binary during installation or removal of packages.

> 类`ref-classes-update-alternatives`{.interpreted-text role="ref"}可以在多个源提供相同命令时帮助替代系统。当安装多个具有相同或类似功能的程序并使用相同的名称时，就会出现这种情况。例如，`ar`命令可以从`busybox`、`binutils`和`elfutils`软件包中获得。类`ref-classes-update-alternatives`{.interpreted-text role="ref"}可以处理重命名二进制文件，以便可以安装多个软件包而不会发生冲突。无论安装哪个软件包或随后删除哪个软件包，`ar`命令仍然可以工作。该类在安装或删除软件包时会重命名冲突的二进制文件，并在安装或删除软件包时符号链接最高优先级的二进制文件。

To use this class, you need to define a number of variables:

- `ALTERNATIVE`{.interpreted-text role="term"}
- `ALTERNATIVE_LINK_NAME`{.interpreted-text role="term"}
- `ALTERNATIVE_TARGET`{.interpreted-text role="term"}
- `ALTERNATIVE_PRIORITY`{.interpreted-text role="term"}


These variables list alternative commands needed by a package, provide pathnames for links, default links for targets, and so forth. For details on how to use this class, see the comments in the :yocto\_[git:%60update-alternatives.bbclass](git:%60update-alternatives.bbclass) \</poky/tree/meta/classes-recipe/update-alternatives.bbclass\>\` file.

> 这些变量列出了一个包所需的替代命令，提供链接的路径名，目标的默认链接等等。要了解如何使用这个类，请参阅：yocto_[git:`update-alternatives.bbclass`](git:`update-alternatives.bbclass`) \</poky/tree/meta/classes-recipe/update-alternatives.bbclass\>\`文件中的注释。

::: note
::: title
Note
:::

You can use the `update-alternatives` command directly in your recipes. However, this class simplifies things in most cases.
:::

# `update-rc.d` {#ref-classes-update-rc.d}


The `ref-classes-update-rc.d`{.interpreted-text role="ref"} class uses `update-rc.d` to safely install an initialization script on behalf of the package. The OpenEmbedded build system takes care of details such as making sure the script is stopped before a package is removed and started when the package is installed.

> 这个ref-classes-update-rc.d类使用update-rc.d安全地代表包安装初始化脚本。OpenEmbedded构建系统负责处理诸如在包被移除前停止脚本，以及在安装包时启动脚本等细节。


Three variables control this class: `INITSCRIPT_PACKAGES`{.interpreted-text role="term"}, `INITSCRIPT_NAME`{.interpreted-text role="term"} and `INITSCRIPT_PARAMS`{.interpreted-text role="term"}. See the variable links for details.

> 三个变量控制此类：`INITSCRIPT_PACKAGES`{.interpreted-text role="term"}、`INITSCRIPT_NAME`{.interpreted-text role="term"}和`INITSCRIPT_PARAMS`{.interpreted-text role="term"}。有关详细信息，请参阅变量链接。

# `useradd*` {#ref-classes-useradd}


The `useradd* <ref-classes-useradd>`{.interpreted-text role="ref"} classes support the addition of users or groups for usage by the package on the target. For example, if you have packages that contain system services that should be run under their own user or group, you can use these classes to enable creation of the user or group. The :oe\_[git:%60meta-skeleton/recipes-skeleton/useradd/useradd-example.bb](git:%60meta-skeleton/recipes-skeleton/useradd/useradd-example.bb) \</openembedded-core/tree/meta-skeleton/recipes-skeleton/useradd/useradd-example.bb\>[ recipe in the :term:\`Source Directory]{.title-ref} provides a simple example that shows how to add three users and groups to two packages.

> 类`useradd* <ref-classes-useradd>`{.interpreted-text role="ref"}支持在目标上为包添加用户或组。例如，如果您有包含应在其自己的用户或组下运行的系统服务的包，您可以使用这些类来启用创建用户或组的功能。在:term:\`源目录]{.title-ref}中的:oe\_[git:%60meta-skeleton/recipes-skeleton/useradd/useradd-example.bb](git:%60meta-skeleton/recipes-skeleton/useradd/useradd-example.bb) \</openembedded-core/tree/meta-skeleton/recipes-skeleton/useradd/useradd-example.bb\>菜谱提供了一个简单的示例，显示如何为两个包添加三个用户和组。

The `useradd_base <ref-classes-useradd>`{.interpreted-text role="ref"} class provides basic functionality for user or groups settings.


The `useradd* <ref-classes-useradd>`{.interpreted-text role="ref"} classes support the `USERADD_PACKAGES`{.interpreted-text role="term"}, `USERADD_PARAM`{.interpreted-text role="term"}, `GROUPADD_PARAM`{.interpreted-text role="term"}, and `GROUPMEMS_PARAM`{.interpreted-text role="term"} variables.

> 这些useradd*类支持USERADD_PACKAGES、USERADD_PARAM、GROUPADD_PARAM和GROUPMEMS_PARAM变量。


The `useradd-staticids <ref-classes-useradd>`{.interpreted-text role="ref"} class supports the addition of users or groups that have static user identification (`uid`) and group identification (`gid`) values.

> 这个useradd-staticids类支持添加具有静态用户标识（uid）和组标识（gid）值的用户或组。


The default behavior of the OpenEmbedded build system for assigning `uid` and `gid` values when packages add users and groups during package install time is to add them dynamically. This works fine for programs that do not care what the values of the resulting users and groups become. In these cases, the order of the installation determines the final `uid` and `gid` values. However, if non-deterministic `uid` and `gid` values are a problem, you can override the default, dynamic application of these values by setting static values. When you set static values, the OpenEmbedded build system looks in `BBPATH`{.interpreted-text role="term"} for `files/passwd` and `files/group` files for the values.

> 默认情况下，OpenEmbedded构建系统在安装软件包时为添加的用户和组分配uid和gid值时是动态添加的。这对于不关心结果用户和组的值的程序工作得很好。在这些情况下，安装的顺序决定了最终的uid和gid值。但是，如果非确定性的uid和gid值是一个问题，您可以通过设置静态值来覆盖默认的动态应用这些值。当您设置静态值时，OpenEmbedded构建系统会在BBPATH中查找files/passwd和files/group文件来获取这些值。


To use static `uid` and `gid` values, you need to set some variables. See the `USERADDEXTENSION`{.interpreted-text role="term"}, `USERADD_UID_TABLES`{.interpreted-text role="term"}, `USERADD_GID_TABLES`{.interpreted-text role="term"}, and `USERADD_ERROR_DYNAMIC`{.interpreted-text role="term"} variables. You can also see the `ref-classes-useradd`{.interpreted-text role="ref"} class for additional information.

> 要使用静态的`uid`和`gid`值，您需要设置一些变量。请参见`USERADDEXTENSION`{.interpreted-text role="term"}、`USERADD_UID_TABLES`{.interpreted-text role="term"}、`USERADD_GID_TABLES`{.interpreted-text role="term"}和`USERADD_ERROR_DYNAMIC`{.interpreted-text role="term"}变量。您还可以参见`ref-classes-useradd`{.interpreted-text role="ref"}类以获取额外信息。

::: note
::: title
Note
:::


You do not use the `useradd-staticids <ref-classes-useradd>`{.interpreted-text role="ref"} class directly. You either enable or disable the class by setting the `USERADDEXTENSION`{.interpreted-text role="term"} variable. If you enable or disable the class in a configured system, `TMPDIR`{.interpreted-text role="term"} might contain incorrect `uid` and `gid` values. Deleting the `TMPDIR`{.interpreted-text role="term"} directory will correct this condition.

> 不要直接使用`useradd-staticids <ref-classes-useradd>`{.interpreted-text role="ref"}类。您可以通过设置`USERADDEXTENSION`{.interpreted-text role="term"}变量来启用或禁用该类。如果在配置的系统中启用或禁用该类，`TMPDIR`{.interpreted-text role="term"}可能包含不正确的`uid`和`gid`值。删除`TMPDIR`{.interpreted-text role="term"}目录将纠正此条件。
:::

# `utility-tasks` {#ref-classes-utility-tasks}


The `ref-classes-utility-tasks`{.interpreted-text role="ref"} class provides support for various \"utility\" type tasks that are applicable to all recipes, such as `ref-tasks-clean`{.interpreted-text role="ref"} and `ref-tasks-listtasks`{.interpreted-text role="ref"}.

> 类`ref-classes-utility-tasks`{.interpreted-text role="ref"}提供了适用于所有配方的各种“实用”类型任务的支持，例如`ref-tasks-clean`{.interpreted-text role="ref"}和`ref-tasks-listtasks`{.interpreted-text role="ref"}。

This class is enabled by default because it is inherited by the `ref-classes-base`{.interpreted-text role="ref"} class.

# `utils` {#ref-classes-utils}


The `ref-classes-utils`{.interpreted-text role="ref"} class provides some useful Python functions that are typically used in inline Python expressions (e.g. `${@...}`). One example use is for `bb.utils.contains()`.

> `ref-classes-utils`类提供了一些通常用于内联Python表达式（例如`${@...}`）的有用的Python函数。一个示例用法是`bb.utils.contains()`。

This class is enabled by default because it is inherited by the `ref-classes-base`{.interpreted-text role="ref"} class.

# `vala` {#ref-classes-vala}

The `ref-classes-vala`{.interpreted-text role="ref"} class supports recipes that need to build software written using the Vala programming language.

# `waf` {#ref-classes-waf}


The `ref-classes-waf`{.interpreted-text role="ref"} class supports recipes that need to build software that uses the Waf build system. You can use the `EXTRA_OECONF`{.interpreted-text role="term"} or `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"} variables to specify additional configuration options to be passed on the Waf command line.

> 类`ref-classes-waf`{.interpreted-text role="ref"}支持需要使用Waf构建系统构建软件的配方。您可以使用`EXTRA_OECONF`{.interpreted-text role="term"}或`PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"}变量指定要传递到Waf命令行的其他配置选项。
