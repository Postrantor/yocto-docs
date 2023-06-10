---
tip: translate by openai@2023-06-10 12:22:06
...
---
title: Speeding Up a Build
--------------------------

Build time can be an issue. By default, the build system uses simple controls to try and maximize build efficiency. In general, the default settings for all the following variables result in the most efficient build times when dealing with single socket systems (i.e. a single CPU). If you have multiple CPUs, you might try increasing the default values to gain more speed. See the descriptions in the glossary for each variable for more information:

> 构建时间可能是一个问题。默认情况下，构建系统使用简单的控件来尽量提高构建效率。通常，所有以下变量的默认设置在处理单插槽系统（即单个 CPU）时会产生最高效的构建时间。如果您有多个 CPU，您可以尝试增加默认值以获得更快的速度。有关每个变量的更多信息，请参见术语表：

- `BB_NUMBER_THREADS`{.interpreted-text role="term"}: The maximum number of threads BitBake simultaneously executes.
- `BB_NUMBER_PARSE_THREADS`{.interpreted-text role="term"}: The number of threads BitBake uses during parsing.
- `PARALLEL_MAKE`{.interpreted-text role="term"}: Extra options passed to the `make` command during the `ref-tasks-compile`{.interpreted-text role="ref"} task in order to specify parallel compilation on the local build host.

> `PARALLEL_MAKE`：在本地构建主机上指定并行编译的 `ref-tasks-compile` 任务期间传递给 `make` 命令的额外选项。

- `PARALLEL_MAKEINST`{.interpreted-text role="term"}: Extra options passed to the `make` command during the `ref-tasks-install`{.interpreted-text role="ref"} task in order to specify parallel installation on the local build host.

> - `PARALLEL_MAKEINST`：在本地构建主机上指定并行安装时传递给 `make` 命令的额外选项，用于在 `ref-tasks-install` 任务期间执行。

As mentioned, these variables all scale to the number of processor cores available on the build system. For single socket systems, this auto-scaling ensures that the build system fundamentally takes advantage of potential parallel operations during the build based on the build machine\'s capabilities.

> 按照提到的，这些变量都会根据构建系统上可用的处理器核心数量进行缩放。对于单插槽系统，这种自动缩放可以确保构建系统基于构建机器的能力，从根本上利用潜在的并行操作来进行构建。

Following are additional factors that can affect build speed:

- File system type: The file system type that the build is being performed on can also influence performance. Using `ext4` is recommended as compared to `ext2` and `ext3` due to `ext4` improved features such as extents.

> 文件系统类型：执行构建时使用的文件系统类型也会影响性能。与 ext2 和 ext3 相比，建议使用 ext4，因为 ext4 具有改进的特性，如区块。

- Disabling the updating of access time using `noatime`: The `noatime` mount option prevents the build system from updating file and directory access times.

> 使用 `noatime` 禁用访问时间的更新：`noatime` 挂载选项可以阻止构建系统更新文件和目录的访问时间。

- Setting a longer commit: Using the \"commit=\" mount option increases the interval in seconds between disk cache writes. Changing this interval from the five second default to something longer increases the risk of data loss but decreases the need to write to the disk, thus increasing the build performance.

> 使用“commit=”挂载选项可以增加磁盘缓存写入之间的秒数间隔。将此间隔从默认的五秒钟改为更长的时间会增加数据丢失的风险，但会降低写入磁盘的需求，从而提高构建性能。

- Choosing the packaging backend: Of the available packaging backends, IPK is the fastest. Additionally, selecting a singular packaging backend also helps.

> 选择打包后端：在可用的打包后端中，IPK 是最快的。此外，选择单一的打包后端也有帮助。

- Using `tmpfs` for `TMPDIR`{.interpreted-text role="term"} as a temporary file system: While this can help speed up the build, the benefits are limited due to the compiler using `-pipe`. The build system goes to some lengths to avoid `sync()` calls into the file system on the principle that if there was a significant failure, the `Build Directory`{.interpreted-text role="term"} contents could easily be rebuilt.

> 使用 tmpfs 作为临时文件系统：虽然这有助于加速构建，但由于编译器使用-pipe，因此其好处有限。构建系统采取一定措施避免在文件系统中调用 sync（），其原则是，如果出现重大故障，可以轻松重建构建目录内容。

- Inheriting the `ref-classes-rm-work`{.interpreted-text role="ref"} class: Inheriting this class has shown to speed up builds due to significantly lower amounts of data stored in the data cache as well as on disk. Inheriting this class also makes cleanup of `TMPDIR`{.interpreted-text role="term"} faster, at the expense of being easily able to dive into the source code. File system maintainers have recommended that the fastest way to clean up large numbers of files is to reformat partitions rather than delete files due to the linear nature of partitions. This, of course, assumes you structure the disk partitions and file systems in a way that this is practical.

> 继承 `ref-classes-rm-work`{.interpreted-text role="ref"}类：继承这个类已经显示出由于存储在数据缓存和磁盘上的数据量大大减少，可以加快构建速度。继承这个类也使 `TMPDIR`{.interpreted-text role="term"}的清理变得更快，但是很难深入源代码。文件系统维护人员建议，清理大量文件的最快方法是重新格式化分区而不是删除文件，因为分区有线性结构。当然，这假设您以一种实用的方式结构化磁盘分区和文件系统。

Aside from the previous list, you should keep some trade offs in mind that can help you speed up the build:

- Remove items from `DISTRO_FEATURES`{.interpreted-text role="term"} that you might not need.
- Exclude debug symbols and other debug information: If you do not need these symbols and other debug information, disabling the `*-dbg` package generation can speed up the build. You can disable this generation by setting the `INHIBIT_PACKAGE_DEBUG_SPLIT`{.interpreted-text role="term"} variable to \"1\".

> 如果您不需要这些符号和其他调试信息，可以通过将 `INHIBIT_PACKAGE_DEBUG_SPLIT` 变量设置为“1”来禁用 `*-dbg` 包生成，以加速构建。

- Disable static library generation for recipes derived from `autoconf` or `libtool`: Following is an example showing how to disable static libraries and still provide an override to handle exceptions:

> 禁用从 `autoconf` 或 `libtool` 派生的配方的静态库生成：以下是一个示例，显示如何禁用静态库，并提供一个覆盖以处理异常：

```
STATICLIBCONF = "--disable-static"
STATICLIBCONF:sqlite3-native = ""
EXTRA_OECONF += "${STATICLIBCONF}"
```

::: note
::: title
Note
:::

- Some recipes need static libraries in order to work correctly (e.g. `pseudo-native` needs `sqlite3-native`). Overrides, as in the previous example, account for these kinds of exceptions.

> 一些食谱需要静态库才能正常工作（例如，`pseudo-native` 需要 `sqlite3-native`）。像前面例子中的覆盖一样，可以解决这类异常情况。

- Some packages have packaging code that assumes the presence of the static libraries. If so, you might need to exclude them as well.
  :::
