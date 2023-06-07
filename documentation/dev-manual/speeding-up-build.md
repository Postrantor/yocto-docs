---
tip: translate by baidu@2023-06-07 17:16:34
...
---
title: Speeding Up a Build
--------------------------

Build time can be an issue. By default, the build system uses simple controls to try and maximize build efficiency. In general, the default settings for all the following variables result in the most efficient build times when dealing with single socket systems (i.e. a single CPU). If you have multiple CPUs, you might try increasing the default values to gain more speed. See the descriptions in the glossary for each variable for more information:

> 构建时间可能是个问题。默认情况下，构建系统使用简单的控件来尝试并最大限度地提高构建效率。通常，当处理单个套接字系统（即单个 CPU）时，以下所有变量的默认设置会产生最有效的构建时间。如果您有多个 CPU，您可以尝试增加默认值以获得更高的速度。有关详细信息，请参阅词汇表中每个变量的描述：

- `BB_NUMBER_THREADS`{.interpreted-text role="term"}: The maximum number of threads BitBake simultaneously executes.

> -`BB_NUMBER_THREADS`｛.explored text role=“term”｝：BitBake 同时执行的最大线程数。

- `BB_NUMBER_PARSE_THREADS`{.interpreted-text role="term"}: The number of threads BitBake uses during parsing.

> -`BB_NUMBER_PARSE_THREADS`｛.explored text role=“term”｝：BitBake 在解析过程中使用的线程数。

- `PARALLEL_MAKE`{.interpreted-text role="term"}: Extra options passed to the `make` command during the `ref-tasks-compile`{.interpreted-text role="ref"} task in order to specify parallel compilation on the local build host.

> -`PARALLEL_MAKE`｛.depreted text role=“term”｝：在 `ref tasks compile`｛.repreted text role=“ref”｝任务期间传递给 `MAKE` 命令的额外选项，以便在本地生成主机上指定并行编译。

- `PARALLEL_MAKEINST`{.interpreted-text role="term"}: Extra options passed to the `make` command during the `ref-tasks-install`{.interpreted-text role="ref"} task in order to specify parallel installation on the local build host.

> -`PARALLEL_MAKEIST`｛.depreted text role=“term”｝：在 `ref tasks install`｛.repreted text role=“ref”｝任务期间传递给 `make` 命令的额外选项，以便指定在本地生成主机上的并行安装。

As mentioned, these variables all scale to the number of processor cores available on the build system. For single socket systems, this auto-scaling ensures that the build system fundamentally takes advantage of potential parallel operations during the build based on the build machine\'s capabilities.

> 如前所述，这些变量都根据构建系统上可用的处理器内核的数量进行缩放。对于单套接字系统，这种自动扩展确保了构建系统在基于构建机器的能力的构建过程中从根本上利用潜在的并行操作。

Following are additional factors that can affect build speed:

> 以下是可能影响构建速度的其他因素：

- File system type: The file system type that the build is being performed on can also influence performance. Using `ext4` is recommended as compared to `ext2` and `ext3` due to `ext4` improved features such as extents.

> -文件系统类型：正在执行生成的文件系统类型也会影响性能。与‘ext2’和‘ext3’相比，建议使用‘ext4’，因为‘ext4”改进了扩展等功能。

- Disabling the updating of access time using `noatime`: The `noatime` mount option prevents the build system from updating file and directory access times.

> -禁用使用“noatime”更新访问时间：“noatime”装载选项可防止生成系统更新文件和目录访问时间。

- Setting a longer commit: Using the \"commit=\" mount option increases the interval in seconds between disk cache writes. Changing this interval from the five second default to something longer increases the risk of data loss but decreases the need to write to the disk, thus increasing the build performance.

> -设置更长的提交时间：使用\“commit=\”mount 选项会增加磁盘缓存写入之间的间隔（以秒为单位）。将此间隔从默认的 5 秒更改为更长的时间会增加数据丢失的风险，但会减少写入磁盘的需要，从而提高构建性能。

- Choosing the packaging backend: Of the available packaging backends, IPK is the fastest. Additionally, selecting a singular packaging backend also helps.

> -选择打包后端：在可用的打包后端中，IPK 是最快的。此外，选择单一的打包后端也有帮助。

- Using `tmpfs` for `TMPDIR`{.interpreted-text role="term"} as a temporary file system: While this can help speed up the build, the benefits are limited due to the compiler using `-pipe`. The build system goes to some lengths to avoid `sync()` calls into the file system on the principle that if there was a significant failure, the `Build Directory`{.interpreted-text role="term"} contents could easily be rebuilt.

> -将“tmpfs”用于“TMPDIR”｛.explored text role=“term”｝作为临时文件系统：虽然这有助于加快构建速度，但由于编译器使用了“-pipe”，因此好处有限。构建系统在一定程度上避免了对文件系统的“sync（）”调用，其原理是，如果出现重大故障，可以很容易地重建“构建目录”｛.depredicted text role=“term”｝内容。

- Inheriting the `ref-classes-rm-work`{.interpreted-text role="ref"} class: Inheriting this class has shown to speed up builds due to significantly lower amounts of data stored in the data cache as well as on disk. Inheriting this class also makes cleanup of `TMPDIR`{.interpreted-text role="term"} faster, at the expense of being easily able to dive into the source code. File system maintainers have recommended that the fastest way to clean up large numbers of files is to reformat partitions rather than delete files due to the linear nature of partitions. This, of course, assumes you structure the disk partitions and file systems in a way that this is practical.

> -继承 `ref classes rm work`｛.explored text role=“ref”｝类：由于存储在数据缓存和磁盘上的数据量显著减少，因此继承此类可以加快构建。继承此类还可以更快地清理 `TMPDIR`｛.explored text role=“term”｝，但代价是可以轻松地深入到源代码中。文件系统维护人员建议，由于分区的线性性质，清理大量文件的最快方法是重新格式化分区，而不是删除文件。当然，这是假设您以实用的方式构建磁盘分区和文件系统。

Aside from the previous list, you should keep some trade offs in mind that can help you speed up the build:

> 除了前面的列表之外，您还应该记住一些可以帮助您加快构建速度的权衡：

- Remove items from `DISTRO_FEATURES`{.interpreted-text role="term"} that you might not need.
- Exclude debug symbols and other debug information: If you do not need these symbols and other debug information, disabling the `*-dbg` package generation can speed up the build. You can disable this generation by setting the `INHIBIT_PACKAGE_DEBUG_SPLIT`{.interpreted-text role="term"} variable to \"1\".

> -排除调试符号和其他调试信息：如果不需要这些符号和其他的调试信息，禁用“*-dbg”包生成可以加快生成速度。您可以通过将 `INHIBIT_PACKAGE_DEBUG_SPLIT`｛.explored text role=“term”｝变量设置为\“1\”来禁用此生成。

- Disable static library generation for recipes derived from `autoconf` or `libtool`: Following is an example showing how to disable static libraries and still provide an override to handle exceptions:

> -禁用从“autoconf”或“libtool”派生的配方的静态库生成：以下是一个示例，显示了如何禁用静态库并仍然提供覆盖来处理异常：

```
STATICLIBCONF = "--disable-static"
STATICLIBCONF:sqlite3-native = ""
EXTRA_OECONF += "${STATICLIBCONF}"
```

::: note
::: title

Note

> 笔记
> :::

- Some recipes need static libraries in order to work correctly (e.g. `pseudo-native` needs `sqlite3-native`). Overrides, as in the previous example, account for these kinds of exceptions.

> -有些配方需要静态库才能正常工作（例如，“pseudo-native”需要“sqlite3-native”）。覆盖，如前一个示例中所述，说明了这些类型的异常。

- Some packages have packaging code that assumes the presence of the static libraries. If so, you might need to exclude them as well.

> -有些包具有假定存在静态库的打包代码。如果是这样，您可能还需要排除它们。
> :::
