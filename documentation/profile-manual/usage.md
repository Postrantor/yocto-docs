---
tip: translate by openai@2023-06-07 21:14:20
...
---
title: Basic Usage (with examples) for each of the Yocto Tracing Tools
----------------------------------------------------------------------

|

This chapter presents basic usage examples for each of the tracing tools.

> 本章节为每个跟踪工具提供基本的使用示例。

# perf

The \'perf\' tool is the profiling and tracing tool that comes bundled with the Linux kernel.

> 'perf'工具是随 Linux 内核一起捆绑发布的性能分析和跟踪工具。

Don\'t let the fact that it\'s part of the kernel fool you into thinking that it\'s only for tracing and profiling the kernel \-\-- you can indeed use it to trace and profile just the kernel, but you can also use it to profile specific applications separately (with or without kernel context), and you can also use it to trace and profile the kernel and all applications on the system simultaneously to gain a system-wide view of what\'s going on.

> 不要以为它只是用来跟踪和分析内核的，让它作为内核的一部分来欺骗你 - 你确实可以用它来跟踪和分析内核，但你也可以单独用它来分析特定应用程序（有或没有内核上下文），你也可以同时跟踪和分析内核和系统上的所有应用程序，以获得系统范围的视图。

In many ways, perf aims to be a superset of all the tracing and profiling tools available in Linux today, including all the other tools covered in this HOWTO. The past couple of years have seen perf subsume a lot of the functionality of those other tools and, at the same time, those other tools have removed large portions of their previous functionality and replaced it with calls to the equivalent functionality now implemented by the perf subsystem. Extrapolation suggests that at some point those other tools will simply become completely redundant and go away; until then, we\'ll cover those other tools in these pages and in many cases show how the same things can be accomplished in perf and the other tools when it seems useful to do so.

> 许多方面，perf 旨在成为 Linux 今天可用的所有跟踪和分析工具的超集，包括本教程中涵盖的所有其他工具。过去几年中，perf 吸收了这些其他工具的大部分功能，与此同时，这些其他工具也删除了他们以前的大部分功能，并用现在由 perf 子系统实现的等效功能来替换。推断表明，在某个时候，这些其他工具将只是完全多余而消失；在此之前，我们将在这些页面中涵盖这些其他工具，并在许多情况下显示如何在 perf 和其他工具中实现相同的事情，如果这样做似乎有用的话。

The coverage below details some of the most common ways you\'ll likely want to apply the tool; full documentation can be found either within the tool itself or in the man pages at [perf(1)](https://linux.die.net/man/1/perf).

> 以下覆盖了您可能希望应用该工具的一些最常见的方式；完整的文档可以在工具本身或 [perf(1)](https://linux.die.net/man/1/perf)中的 man 页面中找到。

## Perf Setup

For this section, we\'ll assume you\'ve already performed the basic setup outlined in the \"`profile-manual/intro:General Setup`{.interpreted-text role="ref"}\" section.

> 对于本节，我们假定您已经执行了“profile-manual/intro：General Setup”部分中概述的基本设置。

In particular, you\'ll get the most mileage out of perf if you profile an image built with the following in your `local.conf` file:

> 特别是，如果在您的 `local.conf` 文件中构建了以下内容的映像，您将从 perf 中获得最大的收益：

```shell
INHIBIT_PACKAGE_STRIP = "1"
```

perf runs on the target system for the most part. You can archive profile data and copy it to the host for analysis, but for the rest of this document we assume you\'ve ssh\'ed to the host and will be running the perf commands on the target.

> perf 在目标系统上运行主要部分。您可以存档配置文件数据并将其复制到主机进行分析，但是在本文档的其余部分中，我们假设您已经使用 ssh 登录到主机并在目标上运行 perf 命令。

## Basic Perf Usage

The perf tool is pretty much self-documenting. To remind yourself of the available commands, simply type \'perf\', which will show you basic usage along with the available perf subcommands:

> perf 工具几乎是自文档化的。要提醒自己可用的命令，只需输入 'perf'，就可以看到基本用法以及可用的 perf 子命令：

```shell
root@crownbay:~# perf

usage: perf [--version] [--help] COMMAND [ARGS]

The most commonly used perf commands are:
  annotate        Read perf.data (created by perf record) and display annotated code
  archive         Create archive with object files with build-ids found in perf.data file
  bench           General framework for benchmark suites
  buildid-cache   Manage build-id cache.
  buildid-list    List the buildids in a perf.data file
  diff            Read two perf.data files and display the differential profile
  evlist          List the event names in a perf.data file
  inject          Filter to augment the events stream with additional information
  kmem            Tool to trace/measure kernel memory(slab) properties
  kvm             Tool to trace/measure kvm guest os
  list            List all symbolic event types
  lock            Analyze lock events
  probe           Define new dynamic tracepoints
  record          Run a command and record its profile into perf.data
  report          Read perf.data (created by perf record) and display the profile
  sched           Tool to trace/measure scheduler properties (latencies)
  script          Read perf.data (created by perf record) and display trace output
  stat            Run a command and gather performance counter statistics
  test            Runs sanity tests.
  timechart       Tool to visualize total system behavior during a workload
  top             System profiling tool.

See 'perf help COMMAND' for more information on a specific command.
```

### Using perf to do Basic Profiling

As a simple test case, we\'ll profile the \'wget\' of a fairly large file, which is a minimally interesting case because it has both file and network I/O aspects, and at least in the case of standard Yocto images, it\'s implemented as part of BusyBox, so the methods we use to analyze it can be used in a very similar way to the whole host of supported BusyBox applets in Yocto. :

> 作为一个简单的测试用例，我们将对一个相当大的文件进行'wget'分析，这是一个最小化的有趣的案例，因为它具有文件和网络 I/O 方面，而且至少在标准 Yocto 图像的情况下，它是作为 BusyBox 的一部分实现的，因此我们用来分析它的方法可以以非常类似的方式应用于 Yocto 中支持的整个 BusyBox applet。

```shell
root@crownbay:~# rm linux-2.6.19.2.tar.bz2; \
                 wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2
```

The quickest and easiest way to get some basic overall data about what\'s going on for a particular workload is to profile it using \'perf stat\'. \'perf stat\' basically profiles using a few default counters and displays the summed counts at the end of the run:

> 最快也最简单的方式获取某个特定工作负载的基本数据是使用'perf stat'进行分析。'perf stat'基本上使用一些默认计数器进行分析，并在运行结束时显示总计数：

```shell
root@crownbay:~# perf stat wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2
Connecting to downloads.yoctoproject.org (140.211.169.59:80)
linux-2.6.19.2.tar.b 100% |***************************************************| 41727k  0:00:00 ETA

Performance counter stats for 'wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2':

      4597.223902 task-clock                #    0.077 CPUs utilized
            23568 context-switches          #    0.005 M/sec
               68 CPU-migrations            #    0.015 K/sec
              241 page-faults               #    0.052 K/sec
       3045817293 cycles                    #    0.663 GHz
  <not supported> stalled-cycles-frontend
  <not supported> stalled-cycles-backend
        858909167 instructions              #    0.28  insns per cycle
        165441165 branches                  #   35.987 M/sec
         19550329 branch-misses             #   11.82% of all branches

     59.836627620 seconds time elapsed
```

Many times such a simple-minded test doesn\'t yield much of interest, but sometimes it does (see Real-world Yocto bug (slow loop-mounted write speed)).

> 很多时候这样一个简单的测试不会产生太多有趣的东西，但有时候会（参见实际世界的 Yocto 错误（循环挂载写入速度慢））。

Also, note that \'perf stat\' isn\'t restricted to a fixed set of counters - basically any event listed in the output of \'perf list\' can be tallied by \'perf stat\'. For example, suppose we wanted to see a summary of all the events related to kernel memory allocation/freeing along with cache hits and misses:

> '此外，请注意，'perf stat'不局限于固定的计数器集 - 基本上，'perf list'输出中列出的任何事件都可以由'perf stat'进行计数。例如，假设我们想看到与内核内存分配/释放相关的所有事件的摘要，以及缓存命中和丢失：

```shell
root@crownbay:~# perf stat -e kmem:* -e cache-references -e cache-misses wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2
Connecting to downloads.yoctoproject.org (140.211.169.59:80)
linux-2.6.19.2.tar.b 100% |***************************************************| 41727k  0:00:00 ETA

Performance counter stats for 'wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2':

             5566 kmem:kmalloc
           125517 kmem:kmem_cache_alloc
                0 kmem:kmalloc_node
                0 kmem:kmem_cache_alloc_node
            34401 kmem:kfree
            69920 kmem:kmem_cache_free
              133 kmem:mm_page_free
               41 kmem:mm_page_free_batched
            11502 kmem:mm_page_alloc
            11375 kmem:mm_page_alloc_zone_locked
                0 kmem:mm_page_pcpu_drain
                0 kmem:mm_page_alloc_extfrag
         66848602 cache-references
          2917740 cache-misses              #    4.365 % of all cache refs

     44.831023415 seconds time elapsed
```

So \'perf stat\' gives us a nice easy way to get a quick overview of what might be happening for a set of events, but normally we\'d need a little more detail in order to understand what\'s going on in a way that we can act on in a useful way.

> 所以'perf stat'给我们一个很好的简单方法来快速概览可能发生的一系列事件，但通常我们需要更多的细节才能理解发生了什么，以便我们以有用的方式采取行动。

To dive down into a next level of detail, we can use \'perf record\'/\'perf report\' which will collect profiling data and present it to use using an interactive text-based UI (or simply as text if we specify `--stdio` to \'perf report\').

> 我们可以使用'perf record'/'perf report'来深入到下一个细节层次，它将收集分析数据并使用交互式文本 UI（如果我们指定 `--stdio` 到'perf report'，则只以文本形式呈现）将其呈现给我们。

As our first attempt at profiling this workload, we\'ll simply run \'perf record\', handing it the workload we want to profile (everything after \'perf record\' and any perf options we hand it \-\-- here none, will be executed in a new shell). perf collects samples until the process exits and records them in a file named \'perf.data\' in the current working directory. :

> 作为我们第一次尝试对这项工作负载进行分析，我们只需运行“perf record”，将我们想要分析的工作负载（所有在“perf record”之后以及我们传递给它的任何 perf 选项 - 这里没有，将在一个新的 shell 中执行）传递给它。 perf 在进程退出之前一直收集样本，并将它们记录在当前工作目录中名为“perf.data”的文件中。

```shell
root@crownbay:~# perf record wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2

Connecting to downloads.yoctoproject.org (140.211.169.59:80)
linux-2.6.19.2.tar.b 100% |************************************************| 41727k  0:00:00 ETA
[ perf record: Woken up 1 times to write data ]
[ perf record: Captured and wrote 0.176 MB perf.data (~7700 samples) ]
```

To see the results in a \'text-based UI\' (tui), simply run \'perf report\', which will read the perf.data file in the current working directory and display the results in an interactive UI:

> 要在文本界面（tui）中查看结果，只需运行“perf report”，它将读取当前工作目录中的 perf.data 文件，并在交互式界面中显示结果：

```shell
root@crownbay:~# perf report
```

![image](figures/perf-wget-flat-stripped.png){.align-center width="70.0%"}

The above screenshot displays a \'flat\' profile, one entry for each \'bucket\' corresponding to the functions that were profiled during the profiling run, ordered from the most popular to the least (perf has options to sort in various orders and keys as well as display entries only above a certain threshold and so on \-\-- see the perf documentation for details). Note that this includes both userspace functions (entries containing a \[.\]) and kernel functions accounted to the process (entries containing a \[k\]). (perf has command-line modifiers that can be used to restrict the profiling to kernel or userspace, among others).

> 以上截图显示了一个“平坦”的配置文件，每个“桶”对应于在监测运行期间检测的功能，从最受欢迎的功能到最不受欢迎的功能（perf 具有用于以各种顺序和键排序以及仅显示超过特定阈值的条目等的选项-参见 perf 文档以获取详细信息）。请注意，这包括用户空间函数（包含\[.\]的条目）和账户给进程的内核函数（包含\[k\]的条目）。 （perf 有命令行修饰符，可用于将监测限制为内核或用户空间等）。

Notice also that the above report shows an entry for \'busybox\', which is the executable that implements \'wget\' in Yocto, but that instead of a useful function name in that entry, it displays a not-so-friendly hex value instead. The steps below will show how to fix that problem.

> 注意，上面的报告中还显示了'busybox'的条目，这是在 Yocto 中实现'wget'的可执行文件，但是在该条目中，它显示的不是一个有用的函数名，而是一个不太友好的十六进制值。以下步骤将演示如何解决该问题。

Before we do that, however, let\'s try running a different profile, one which shows something a little more interesting. The only difference between the new profile and the previous one is that we\'ll add the -g option, which will record not just the address of a sampled function, but the entire callchain to the sampled function as well:

> 在做那件事之前，不过，让我们尝试运行一个不同的配置文件，它显示一些更有趣的东西。新配置文件和先前的配置文件之间唯一的不同之处是我们将添加 -g 选项，它将记录采样函数的地址以及到采样函数的整个调用链：

```shell
root@crownbay:~# perf record -g wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2
Connecting to downloads.yoctoproject.org (140.211.169.59:80)
linux-2.6.19.2.tar.b 100% |************************************************| 41727k  0:00:00 ETA
[ perf record: Woken up 3 times to write data ]
[ perf record: Captured and wrote 0.652 MB perf.data (~28476 samples) ]


root@crownbay:~# perf report
```

![image](figures/perf-wget-g-copy-to-user-expanded-stripped.png){.align-center width="70.0%"}

Using the callgraph view, we can actually see not only which functions took the most time, but we can also see a summary of how those functions were called and learn something about how the program interacts with the kernel in the process.

> 使用调用图视图，我们不仅可以看到哪些函数花费了最多的时间，还可以查看这些函数的调用摘要，了解程序在处理过程中与内核的交互情况。

Notice that each entry in the above screenshot now contains a \'+\' on the left-hand side. This means that we can expand the entry and drill down into the callchains that feed into that entry. Pressing \'enter\' on any one of them will expand the callchain (you can also press \'E\' to expand them all at the same time or \'C\' to collapse them all).

> 在上面的截图中，可以看到每个条目都在左边带有一个'+'号。这意味着我们可以展开这个条目，深入查看调用链。按下任何一个条目上的'enter'键就可以展开调用链（也可以按'E'键一次性展开所有条目，或者按'C'键一次性折叠所有条目）。

In the screenshot above, we\'ve toggled the `__copy_to_user_ll()` entry and several subnodes all the way down. This lets us see which callchains contributed to the profiled `__copy_to_user_ll()` function which contributed 1.77% to the total profile.

> 在上面的截图中，我们已经切换了 `__copy_to_user_ll（）` 条目和几个子节点。这使我们可以看到哪些调用链有助于分析的 `__copy_to_user_ll（）` 函数，该函数占总分析的 1.77％。

As a bit of background explanation for these callchains, think about what happens at a high level when you run wget to get a file out on the network. Basically what happens is that the data comes into the kernel via the network connection (socket) and is passed to the userspace program \'wget\' (which is actually a part of BusyBox, but that\'s not important for now), which takes the buffers the kernel passes to it and writes it to a disk file to save it.

> 作为这些调用链的背景解释，想想当你运行 wget 从网络上获取文件时会发生什么。基本上发生的是，数据通过网络连接（套接字）进入内核，并传递给用户空间程序“wget”（实际上是 BusyBox 的一部分，但现在这不重要），它接收内核传递给它的缓冲区，并将其写入磁盘文件以保存它。

The part of this process that we\'re looking at in the above call stacks is the part where the kernel passes the data it has read from the socket down to wget i.e. a copy-to-user.

> 我们在上面的调用堆栈中看到的这个过程的部分是内核将从套接字读取的数据传递给 wget，即复制到用户的部分。

Notice also that here there\'s also a case where the hex value is displayed in the callstack, here in the expanded `sys_clock_gettime()` function. Later we\'ll see it resolve to a userspace function call in busybox.

> 注意，在此处，还有一种情况，即十六进制值显示在调用堆栈中，这里是在展开的 `sys_clock_gettime()` 函数中。稍后，我们将看到它解析为 Busybox 中的用户空间函数调用。

![image](figures/perf-wget-g-copy-from-user-expanded-stripped.png){.align-center width="70.0%"}

The above screenshot shows the other half of the journey for the data -from the wget program\'s userspace buffers to disk. To get the buffers to disk, the wget program issues a `write(2)`, which does a `copy-from-user` to the kernel, which then takes care via some circuitous path (probably also present somewhere in the profile data), to get it safely to disk.

> 上面的截图展示了数据的另一半旅程——从 wget 程序的用户空间缓冲区到磁盘。为了将缓冲区写入磁盘，wget 程序发出一个“write（2）”，它将数据从用户空间复制到内核，内核再通过某种曲折的路径（可能也在配置文件数据中存在）将数据安全地写入磁盘。

Now that we\'ve seen the basic layout of the profile data and the basics of how to extract useful information out of it, let\'s get back to the task at hand and see if we can get some basic idea about where the time is spent in the program we\'re profiling, wget. Remember that wget is actually implemented as an applet in BusyBox, so while the process name is \'wget\', the executable we\'re actually interested in is BusyBox. So let\'s expand the first entry containing BusyBox:

> 现在我们已经了解了配置文件数据的基本布局和如何从中提取有用信息的基础知识，让我们回到手头的任务上，看看我们是否能够对我们正在分析的程序 wget 花费时间的基本情况有一些了解。请记住，wget 实际上是作为 BusyBox 的一个小程序实现的，因此，尽管进程名称为'wget'，我们实际上感兴趣的可执行文件是 BusyBox。因此，让我们展开包含 BusyBox 的第一个条目：

![image](figures/perf-wget-busybox-expanded-stripped.png){.align-center width="70.0%"}

Again, before we expanded we saw that the function was labeled with a hex value instead of a symbol as with most of the kernel entries. Expanding the BusyBox entry doesn\'t make it any better.

> 再次，在我们扩展之前，我们看到该函数被用十六进制值标记，而不是像大多数内核条目那样用符号标记。扩展 BusyBox 条目并没有变得更好。

The problem is that perf can\'t find the symbol information for the busybox binary, which is actually stripped out by the Yocto build system.

> 问题是 Perf 无法找到 Busybox 二进制文件的符号信息，这是由 Yocto 构建系统剥离出来的。

One way around that is to put the following in your `local.conf` file when you build the image:

> 在构建镜像时，可以在 local.conf 文件中添加以下内容来解决这个问题：

```shell
INHIBIT_PACKAGE_STRIP = "1"
```

However, we already have an image with the binaries stripped, so what can we do to get perf to resolve the symbols? Basically we need to install the debuginfo for the BusyBox package.

> 然而，我们已经有了一个已经剥离二进制文件的图像，那么我们要如何让 perf 来解析符号？基本上我们需要安装 BusyBox 包的调试信息。

To generate the debug info for the packages in the image, we can add `dbg-pkgs` to `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} in `local.conf`. For example:

> 为镜像中的软件包生成调试信息，我们可以在 `local.conf` 中将 `dbg-pkgs` 添加到 `EXTRA_IMAGE_FEATURES` 中。例如：

```shell
EXTRA_IMAGE_FEATURES = "debug-tweaks tools-profile dbg-pkgs"
```

Additionally, in order to generate the type of debuginfo that perf understands, we also need to set `PACKAGE_DEBUG_SPLIT_STYLE`{.interpreted-text role="term"} in the `local.conf` file:

> 此外，为了生成 perf 理解的调试信息，我们还需要在 `local.conf` 文件中设置 `PACKAGE_DEBUG_SPLIT_STYLE`。

```shell
PACKAGE_DEBUG_SPLIT_STYLE = 'debug-file-directory'
```

Once we\'ve done that, we can install the debuginfo for BusyBox. The debug packages once built can be found in `build/tmp/deploy/rpm/*` on the host system. Find the busybox-dbg-\...rpm file and copy it to the target. For example:

> 一旦我们完成了这个，我们可以安装 BusyBox 的调试信息。一旦构建完成，调试包可以在主机系统的 build/tmp/deploy/rpm/*中找到。找到 busybox-dbg-\...rpm 文件，并将其复制到目标上。例如：

```shell
[trz@empanada core2]$ scp /home/trz/yocto/crownbay-tracing-dbg/build/tmp/deploy/rpm/core2_32/busybox-dbg-1.20.2-r2.core2_32.rpm root@192.168.1.31:
busybox-dbg-1.20.2-r2.core2_32.rpm                     100% 1826KB   1.8MB/s   00:01
```

Now install the debug rpm on the target:

> 现在在目标上安装调试 RPM。

```shell
root@crownbay:~# rpm -i busybox-dbg-1.20.2-r2.core2_32.rpm
```

Now that the debuginfo is installed, we see that the BusyBox entries now display their functions symbolically:

> 现在调试信息已安装，我们可以看到 BusyBox 条目现在以符号形式显示其功能：

![image](figures/perf-wget-busybox-debuginfo.png){.align-center width="70.0%"}

If we expand one of the entries and press \'enter\' on a leaf node, we\'re presented with a menu of actions we can take to get more information related to that entry:

> 如果我们展开一个条目，并在叶节点上按下“回车”，我们就会看到一个可以采取的操作菜单，以获取与该条目相关的更多信息。

![image](figures/perf-wget-busybox-dso-zoom-menu.png){.align-center width="70.0%"}

One of these actions allows us to show a view that displays a busybox-centric view of the profiled functions (in this case we\'ve also expanded all the nodes using the \'E\' key):

> 其中一项操作可以让我们显示一个以 busybox 为中心的函数视图（在这种情况下，我们也使用“E”键展开了所有节点）：

![image](figures/perf-wget-busybox-dso-zoom.png){.align-center width="70.0%"}

Finally, we can see that now that the BusyBox debuginfo is installed, the previously unresolved symbol in the `sys_clock_gettime()` entry mentioned previously is now resolved, and shows that the sys_clock_gettime system call that was the source of 6.75% of the copy-to-user overhead was initiated by the `handle_input()` BusyBox function:

> 最后，我们可以看到，现在 BusyBox 调试信息已经安装完毕，先前提到的 `sys_clock_gettime()` 条目中的未解析符号现在已经解析，并显示 sys_clock_gettime 系统调用是复制到用户的开销 6.75％的来源，由 `handle_input（）` BusyBox 函数启动：

![image](figures/perf-wget-g-copy-to-user-expanded-debuginfo.png){.align-center width="70.0%"}

At the lowest level of detail, we can dive down to the assembly level and see which instructions caused the most overhead in a function. Pressing \'enter\' on the \'udhcpc_main\' function, we\'re again presented with a menu:

> 在最低細節層次，我們可以深入到組裝級別，看看哪些指令在函數中造成了最大的開銷。在'udhcpc_main'函數上按下'enter'，我們再次看到一個菜單：

![image](figures/perf-wget-busybox-annotate-menu.png){.align-center width="70.0%"}

Selecting \'Annotate udhcpc_main\', we get a detailed listing of percentages by instruction for the udhcpc_main function. From the display, we can see that over 50% of the time spent in this function is taken up by a couple tests and the move of a constant (1) to a register:

> 选择“注释 udhcpc_main”，我们可以获得 udhcpc_main 功能的详细百分比列表。从显示结果中，我们可以看到，这个函数花费的时间超过 50％都是用来进行一些测试和将一个常量（1）移动到寄存器中。

![image](figures/perf-wget-busybox-annotate-udhcpc.png){.align-center width="70.0%"}

As a segue into tracing, let\'s try another profile using a different counter, something other than the default \'cycles\'.

> 作为跟踪的一个过渡，让我们尝试使用另一个计数器，而不是默认的'cycles'，来尝试另一个配置文件。

The tracing and profiling infrastructure in Linux has become unified in a way that allows us to use the same tool with a completely different set of counters, not just the standard hardware counters that traditional tools have had to restrict themselves to (of course the traditional tools can also make use of the expanded possibilities now available to them, and in some cases have, as mentioned previously).

> 在 Linux 中，跟踪和分析基础设施已经以一种允许我们使用同一个工具来处理完全不同的计数器（而不仅仅是传统工具必须限制自己使用的标准硬件计数器）的方式被统一起来了（当然，传统的工具也可以利用现在可用的扩展可能性，而且在某些情况下也已经如前所述）。

We can get a list of the available events that can be used to profile a workload via \'perf list\':

> 我们可以通过'perf list'获取可用事件的列表，以便对工作负载进行分析。

```shell
root@crownbay:~# perf list

List of pre-defined events (to be used in -e):
 cpu-cycles OR cycles                               [Hardware event]
 stalled-cycles-frontend OR idle-cycles-frontend    [Hardware event]
 stalled-cycles-backend OR idle-cycles-backend      [Hardware event]
 instructions                                       [Hardware event]
 cache-references                                   [Hardware event]
 cache-misses                                       [Hardware event]
 branch-instructions OR branches                    [Hardware event]
 branch-misses                                      [Hardware event]
 bus-cycles                                         [Hardware event]
 ref-cycles                                         [Hardware event]

 cpu-clock                                          [Software event]
 task-clock                                         [Software event]
 page-faults OR faults                              [Software event]
 minor-faults                                       [Software event]
 major-faults                                       [Software event]
 context-switches OR cs                             [Software event]
 cpu-migrations OR migrations                       [Software event]
 alignment-faults                                   [Software event]
 emulation-faults                                   [Software event]

 L1-dcache-loads                                    [Hardware cache event]
 L1-dcache-load-misses                              [Hardware cache event]
 L1-dcache-prefetch-misses                          [Hardware cache event]
 L1-icache-loads                                    [Hardware cache event]
 L1-icache-load-misses                              [Hardware cache event]
 .
 .
 .
 rNNN                                               [Raw hardware event descriptor]
 cpu/t1=v1[,t2=v2,t3 ...]/modifier                  [Raw hardware event descriptor]
  (see 'perf list --help' on how to encode it)

 mem:<addr>[:access]                                [Hardware breakpoint]

 sunrpc:rpc_call_status                             [Tracepoint event]
 sunrpc:rpc_bind_status                             [Tracepoint event]
 sunrpc:rpc_connect_status                          [Tracepoint event]
 sunrpc:rpc_task_begin                              [Tracepoint event]
 skb:kfree_skb                                      [Tracepoint event]
 skb:consume_skb                                    [Tracepoint event]
 skb:skb_copy_datagram_iovec                        [Tracepoint event]
 net:net_dev_xmit                                   [Tracepoint event]
 net:net_dev_queue                                  [Tracepoint event]
 net:netif_receive_skb                              [Tracepoint event]
 net:netif_rx                                       [Tracepoint event]
 napi:napi_poll                                     [Tracepoint event]
 sock:sock_rcvqueue_full                            [Tracepoint event]
 sock:sock_exceed_buf_limit                         [Tracepoint event]
 udp:udp_fail_queue_rcv_skb                         [Tracepoint event]
 hda:hda_send_cmd                                   [Tracepoint event]
 hda:hda_get_response                               [Tracepoint event]
 hda:hda_bus_reset                                  [Tracepoint event]
 scsi:scsi_dispatch_cmd_start                       [Tracepoint event]
 scsi:scsi_dispatch_cmd_error                       [Tracepoint event]
 scsi:scsi_eh_wakeup                                [Tracepoint event]
 drm:drm_vblank_event                               [Tracepoint event]
 drm:drm_vblank_event_queued                        [Tracepoint event]
 drm:drm_vblank_event_delivered                     [Tracepoint event]
 random:mix_pool_bytes                              [Tracepoint event]
 random:mix_pool_bytes_nolock                       [Tracepoint event]
 random:credit_entropy_bits                         [Tracepoint event]
 gpio:gpio_direction                                [Tracepoint event]
 gpio:gpio_value                                    [Tracepoint event]
 block:block_rq_abort                               [Tracepoint event]
 block:block_rq_requeue                             [Tracepoint event]
 block:block_rq_issue                               [Tracepoint event]
 block:block_bio_bounce                             [Tracepoint event]
 block:block_bio_complete                           [Tracepoint event]
 block:block_bio_backmerge                          [Tracepoint event]
 .
 .
 writeback:writeback_wake_thread                    [Tracepoint event]
 writeback:writeback_wake_forker_thread             [Tracepoint event]
 writeback:writeback_bdi_register                   [Tracepoint event]
 .
 .
 writeback:writeback_single_inode_requeue           [Tracepoint event]
 writeback:writeback_single_inode                   [Tracepoint event]
 kmem:kmalloc                                       [Tracepoint event]
 kmem:kmem_cache_alloc                              [Tracepoint event]
 kmem:mm_page_alloc                                 [Tracepoint event]
 kmem:mm_page_alloc_zone_locked                     [Tracepoint event]
 kmem:mm_page_pcpu_drain                            [Tracepoint event]
 kmem:mm_page_alloc_extfrag                         [Tracepoint event]
 vmscan:mm_vmscan_kswapd_sleep                      [Tracepoint event]
 vmscan:mm_vmscan_kswapd_wake                       [Tracepoint event]
 vmscan:mm_vmscan_wakeup_kswapd                     [Tracepoint event]
 vmscan:mm_vmscan_direct_reclaim_begin              [Tracepoint event]
 .
 .
 module:module_get                                  [Tracepoint event]
 module:module_put                                  [Tracepoint event]
 module:module_request                              [Tracepoint event]
 sched:sched_kthread_stop                           [Tracepoint event]
 sched:sched_wakeup                                 [Tracepoint event]
 sched:sched_wakeup_new                             [Tracepoint event]
 sched:sched_process_fork                           [Tracepoint event]
 sched:sched_process_exec                           [Tracepoint event]
 sched:sched_stat_runtime                           [Tracepoint event]
 rcu:rcu_utilization                                [Tracepoint event]
 workqueue:workqueue_queue_work                     [Tracepoint event]
 workqueue:workqueue_execute_end                    [Tracepoint event]
 signal:signal_generate                             [Tracepoint event]
 signal:signal_deliver                              [Tracepoint event]
 timer:timer_init                                   [Tracepoint event]
 timer:timer_start                                  [Tracepoint event]
 timer:hrtimer_cancel                               [Tracepoint event]
 timer:itimer_state                                 [Tracepoint event]
 timer:itimer_expire                                [Tracepoint event]
 irq:irq_handler_entry                              [Tracepoint event]
 irq:irq_handler_exit                               [Tracepoint event]
 irq:softirq_entry                                  [Tracepoint event]
 irq:softirq_exit                                   [Tracepoint event]
 irq:softirq_raise                                  [Tracepoint event]
 printk:console                                     [Tracepoint event]
 task:task_newtask                                  [Tracepoint event]
 task:task_rename                                   [Tracepoint event]
 syscalls:sys_enter_socketcall                      [Tracepoint event]
 syscalls:sys_exit_socketcall                       [Tracepoint event]
 .
 .
 .
 syscalls:sys_enter_unshare                         [Tracepoint event]
 syscalls:sys_exit_unshare                          [Tracepoint event]
 raw_syscalls:sys_enter                             [Tracepoint event]
 raw_syscalls:sys_exit                              [Tracepoint event]
```

::: admonition

Tying it Together

> 把它拼凑在一起

These are exactly the same set of events defined by the trace event subsystem and exposed by ftrace/tracecmd/kernelshark as files in /sys/kernel/debug/tracing/events, by SystemTap as kernel.trace(\"tracepoint_name\") and (partially) accessed by LTTng.

> 这些事件集与 trace event 子系统定义的完全相同，通过 ftrace/tracecmd/kernelshark 在/sys/kernel/debug/tracing/events 中以文件形式暴露，通过 SystemTap 以 kernel.trace（“tracepoint_name”）的形式访问，并且（部分）被 LTTng 访问。
> :::

Only a subset of these would be of interest to us when looking at this workload, so let\'s choose the most likely subsystems (identified by the string before the colon in the Tracepoint events) and do a \'perf stat\' run using only those wildcarded subsystems:

> 我们在查看这个工作负载时，只有其中的一部分会引起我们的兴趣，因此让我们选择最有可能的子系统（由 Tracepoint 事件中冒号前的字符串标识），并使用这些通配符子系统运行'perf stat'：

```shell
root@crownbay:~# perf stat -e skb:* -e net:* -e napi:* -e sched:* -e workqueue:* -e irq:* -e syscalls:* wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2
Performance counter stats for 'wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2':

            23323 skb:kfree_skb
                0 skb:consume_skb
            49897 skb:skb_copy_datagram_iovec
             6217 net:net_dev_xmit
             6217 net:net_dev_queue
             7962 net:netif_receive_skb
                2 net:netif_rx
             8340 napi:napi_poll
                0 sched:sched_kthread_stop
                0 sched:sched_kthread_stop_ret
             3749 sched:sched_wakeup
                0 sched:sched_wakeup_new
                0 sched:sched_switch
               29 sched:sched_migrate_task
                0 sched:sched_process_free
                1 sched:sched_process_exit
                0 sched:sched_wait_task
                0 sched:sched_process_wait
                0 sched:sched_process_fork
                1 sched:sched_process_exec
                0 sched:sched_stat_wait
    2106519415641 sched:sched_stat_sleep
                0 sched:sched_stat_iowait
        147453613 sched:sched_stat_blocked
      12903026955 sched:sched_stat_runtime
                0 sched:sched_pi_setprio
             3574 workqueue:workqueue_queue_work
             3574 workqueue:workqueue_activate_work
                0 workqueue:workqueue_execute_start
                0 workqueue:workqueue_execute_end
            16631 irq:irq_handler_entry
            16631 irq:irq_handler_exit
            28521 irq:softirq_entry
            28521 irq:softirq_exit
            28728 irq:softirq_raise
                1 syscalls:sys_enter_sendmmsg
                1 syscalls:sys_exit_sendmmsg
                0 syscalls:sys_enter_recvmmsg
                0 syscalls:sys_exit_recvmmsg
               14 syscalls:sys_enter_socketcall
               14 syscalls:sys_exit_socketcall
                  .
                  .
                  .
            16965 syscalls:sys_enter_read
            16965 syscalls:sys_exit_read
            12854 syscalls:sys_enter_write
            12854 syscalls:sys_exit_write
                  .
                  .
                  .

     58.029710972 seconds time elapsed
```

Let\'s pick one of these tracepoints and tell perf to do a profile using it as the sampling event:

> 让我们从这些追踪点中选择一个，并告诉 perf 使用它作为采样事件来进行分析：

```shell
root@crownbay:~# perf record -g -e sched:sched_wakeup wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2
```

![image](figures/sched-wakeup-profile.png){.align-center width="70.0%"}

The screenshot above shows the results of running a profile using sched:sched_switch tracepoint, which shows the relative costs of various paths to sched_wakeup (note that sched_wakeup is the name of the tracepoint \-\-- it\'s actually defined just inside ttwu_do_wakeup(), which accounts for the function name actually displayed in the profile:

> 以上截图展示了使用 sched:sched_switch tracepoint 运行一个分析的结果，其中显示了各种路径到 sched_wakeup 的相对成本（请注意，sched_wakeup 是 tracepoint 的名称 - 实际上它只是在 ttwu_do_wakeup（）中定义，这就是为什么在分析中实际显示的函数名）。

```c
/*
 * Mark the task runnable and perform wakeup-preemption.
 */
static void
ttwu_do_wakeup(struct rq *rq, struct task_struct *p, int wake_flags)
{
     trace_sched_wakeup(p, true);
     .
     .
     .
}
```

A couple of the more interesting callchains are expanded and displayed above, basically some network receive paths that presumably end up waking up wget (busybox) when network data is ready.

> 一些更有趣的调用链展开并显示在上面，基本上是一些网络接收路径，当网络数据准备就绪时可能会唤醒 wget（busybox）。

Note that because tracepoints are normally used for tracing, the default sampling period for tracepoints is 1 i.e. for tracepoints perf will sample on every event occurrence (this can be changed using the -c option). This is in contrast to hardware counters such as for example the default \'cycles\' hardware counter used for normal profiling, where sampling periods are much higher (in the thousands) because profiling should have as low an overhead as possible and sampling on every cycle would be prohibitively expensive.

### Using perf to do Basic Tracing

Profiling is a great tool for solving many problems or for getting a high-level view of what\'s going on with a workload or across the system. It is however by definition an approximation, as suggested by the most prominent word associated with it, \'sampling\'. On the one hand, it allows a representative picture of what\'s going on in the system to be cheaply taken, but on the other hand, that cheapness limits its utility when that data suggests a need to \'dive down\' more deeply to discover what\'s really going on. In such cases, the only way to see what\'s really going on is to be able to look at (or summarize more intelligently) the individual steps that go into the higher-level behavior exposed by the coarse-grained profiling data.

> 分析是解决许多问题或了解工作负载或系统状态的一个很好的工具。然而，由于它最显著的关联词“采样”，它在定义上是一种近似。一方面，它允许以廉价的方式获得系统状态的代表性图片，但另一方面，当数据表明有必要“深入”更深地发现真正发生的事情时，这种廉价就限制了它的效用。在这种情况下，要看到真正发生的事情，唯一的办法是能够查看（或更聪明地总结）粗粒度分析数据暴露出的更高级行为的各个步骤。

As a concrete example, we can trace all the events we think might be applicable to our workload:

> 作为一个具体的例子，我们可以追踪所有我们认为可能适用于我们工作负荷的事件：

```shell
root@crownbay:~# perf record -g -e skb:* -e net:* -e napi:* -e sched:sched_switch -e sched:sched_wakeup -e irq:*
 -e syscalls:sys_enter_read -e syscalls:sys_exit_read -e syscalls:sys_enter_write -e syscalls:sys_exit_write
 wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2
```

We can look at the raw trace output using \'perf script\' with no arguments:

> 我们可以使用不带参数的'perf script'来查看原始跟踪输出：

```shell
root@crownbay:~# perf script

      perf  1262 [000] 11624.857082: sys_exit_read: 0x0
      perf  1262 [000] 11624.857193: sched_wakeup: comm=migration/0 pid=6 prio=0 success=1 target_cpu=000
      wget  1262 [001] 11624.858021: softirq_raise: vec=1 [action=TIMER]
      wget  1262 [001] 11624.858074: softirq_entry: vec=1 [action=TIMER]
      wget  1262 [001] 11624.858081: softirq_exit: vec=1 [action=TIMER]
      wget  1262 [001] 11624.858166: sys_enter_read: fd: 0x0003, buf: 0xbf82c940, count: 0x0200
      wget  1262 [001] 11624.858177: sys_exit_read: 0x200
      wget  1262 [001] 11624.858878: kfree_skb: skbaddr=0xeb248d80 protocol=0 location=0xc15a5308
      wget  1262 [001] 11624.858945: kfree_skb: skbaddr=0xeb248000 protocol=0 location=0xc15a5308
      wget  1262 [001] 11624.859020: softirq_raise: vec=1 [action=TIMER]
      wget  1262 [001] 11624.859076: softirq_entry: vec=1 [action=TIMER]
      wget  1262 [001] 11624.859083: softirq_exit: vec=1 [action=TIMER]
      wget  1262 [001] 11624.859167: sys_enter_read: fd: 0x0003, buf: 0xb7720000, count: 0x0400
      wget  1262 [001] 11624.859192: sys_exit_read: 0x1d7
      wget  1262 [001] 11624.859228: sys_enter_read: fd: 0x0003, buf: 0xb7720000, count: 0x0400
      wget  1262 [001] 11624.859233: sys_exit_read: 0x0
      wget  1262 [001] 11624.859573: sys_enter_read: fd: 0x0003, buf: 0xbf82c580, count: 0x0200
      wget  1262 [001] 11624.859584: sys_exit_read: 0x200
      wget  1262 [001] 11624.859864: sys_enter_read: fd: 0x0003, buf: 0xb7720000, count: 0x0400
      wget  1262 [001] 11624.859888: sys_exit_read: 0x400
      wget  1262 [001] 11624.859935: sys_enter_read: fd: 0x0003, buf: 0xb7720000, count: 0x0400
      wget  1262 [001] 11624.859944: sys_exit_read: 0x400
```

This gives us a detailed timestamped sequence of events that occurred within the workload with respect to those events.

> 这为我们提供了一个关于那些事件发生时间的详细时间戳序列，这些事件发生在工作负载中。

In many ways, profiling can be viewed as a subset of tracing -theoretically, if you have a set of trace events that\'s sufficient to capture all the important aspects of a workload, you can derive any of the results or views that a profiling run can.

> 在许多方面，分析可以被视为跟踪的一个子集 - 从理论上讲，如果您有一组足够捕获工作负载所有重要方面的跟踪事件，您可以推导出分析运行可以获得的任何结果或视图。

Another aspect of traditional profiling is that while powerful in many ways, it\'s limited by the granularity of the underlying data. Profiling tools offer various ways of sorting and presenting the sample data, which make it much more useful and amenable to user experimentation, but in the end it can\'t be used in an open-ended way to extract data that just isn\'t present as a consequence of the fact that conceptually, most of it has been thrown away.

> 另一个传统分析的方面是，尽管它在许多方面很强大，但它受到基础数据的粒度限制。分析工具提供了各种方法来排序和呈现样本数据，这使它更加有用，更容易用户实验，但最终它不能以开放式的方式用来提取数据，因为从概念上讲，其中大部分数据已经被丢弃了。

Full-blown detailed tracing data does however offer the opportunity to manipulate and present the information collected during a tracing run in an infinite variety of ways.

> 全面详细的跟踪数据确实为我们提供了机会，可以以无限多的方式操纵和呈现跟踪运行期间收集到的信息。

Another way to look at it is that there are only so many ways that the \'primitive\' counters can be used on their own to generate interesting output; to get anything more complicated than simple counts requires some amount of additional logic, which is typically very specific to the problem at hand. For example, if we wanted to make use of a \'counter\' that maps to the value of the time difference between when a process was scheduled to run on a processor and the time it actually ran, we wouldn\'t expect such a counter to exist on its own, but we could derive one called say \'wakeup_latency\' and use it to extract a useful view of that metric from trace data. Likewise, we really can\'t figure out from standard profiling tools how much data every process on the system reads and writes, along with how many of those reads and writes fail completely. If we have sufficient trace data, however, we could with the right tools easily extract and present that information, but we\'d need something other than pre-canned profiling tools to do that.

> 另一种看法是，只有这么多方法可以单独使用“原始”计数器来生成有趣的输出；要获得比简单计数更复杂的内容，需要一定数量的额外逻辑，这通常与特定问题非常相关。例如，如果我们想使用一个“计数器”来映射进程计划在处理器上运行时和实际运行时的时间差，我们不会期望这样一个计数器本身就存在，但我们可以推导出一个叫做“wakeup_latency”的计数器，并用它从跟踪数据中提取有用的视图。同样，我们无法从标准分析工具中确定系统上的每个进程读取和写入多少数据，以及有多少读取和写入完全失败。但是，如果我们有足够的跟踪数据，我们可以使用正确的工具轻松提取和呈现这些信息，但是我们需要的不是预制的分析工具。

Luckily, there is a general-purpose way to handle such needs, called \'programming languages\'. Making programming languages easily available to apply to such problems given the specific format of data is called a \'programming language binding\' for that data and language. Perf supports two programming language bindings, one for Python and one for Perl.

> 幸运的是，有一种通用的方法来处理这些需求，叫做“编程语言”。使编程语言可以轻松地应用于给定数据格式的这些问题，称为该数据和语言的“编程语言绑定”。Perf 支持两种编程语言绑定，一种是 Python，一种是 Perl。

::: admonition

Tying it Together

> 把它拼接在一起

Language bindings for manipulating and aggregating trace data are of course not a new idea. One of the first projects to do this was IBM\'s DProbes dpcc compiler, an ANSI C compiler which targeted a low-level assembly language running on an in-kernel interpreter on the target system. This is exactly analogous to what Sun\'s DTrace did, except that DTrace invented its own language for the purpose. Systemtap, heavily inspired by DTrace, also created its own one-off language, but rather than running the product on an in-kernel interpreter, created an elaborate compiler-based machinery to translate its language into kernel modules written in C.

> 语言绑定用于操纵和聚合溯源数据当然不是新的想法。最先做这件事的项目之一是 IBM 的 DProbes dpcc 编译器，这是一个针对目标系统上运行的低级汇编语言的 ANSI C 编译器。这与 Sun 的 DTrace 完全相同，只是 DTrace 为此而发明了自己的语言。Systemtap，受 DTrace 的强烈启发，也创建了自己的一次性语言，但是它没有在内核解释器上运行产品，而是创建了一个复杂的基于编译器的机制来将其语言翻译成用 C 编写的内核模块。
> :::

Now that we have the trace data in perf.data, we can use \'perf script -g\' to generate a skeleton script with handlers for the read/write entry/exit events we recorded:

> 现在我们在 perf.data 中有了跟踪数据，我们可以使用“perf script -g”来生成一个带有我们记录的读/写入口/出口事件处理程序的骨架脚本：

```shell
root@crownbay:~# perf script -g python
generated Python script: perf-script.py
```

The skeleton script simply creates a Python function for each event type in the perf.data file. The body of each function simply prints the event name along with its parameters. For example:

> 脚本只是为 perf.data 文件中的每个事件类型创建一个 Python 函数。每个函数的主体只是打印事件名称以及其参数。例如：

```python
def net__netif_rx(event_name, context, common_cpu,
       common_secs, common_nsecs, common_pid, common_comm,
       skbaddr, len, name):
               print_header(event_name, common_cpu, common_secs, common_nsecs,
                       common_pid, common_comm)

               print "skbaddr=%u, len=%u, name=%s\n" % (skbaddr, len, name),
```

We can run that script directly to print all of the events contained in the perf.data file:

> 我们可以直接运行脚本来打印 perf.data 文件中包含的所有事件：

```shell
root@crownbay:~# perf script -s perf-script.py

in trace_begin
syscalls__sys_exit_read     0 11624.857082795     1262 perf                  nr=3, ret=0
sched__sched_wakeup      0 11624.857193498     1262 perf                  comm=migration/0, pid=6, prio=0,      success=1, target_cpu=0
irq__softirq_raise       1 11624.858021635     1262 wget                  vec=TIMER
irq__softirq_entry       1 11624.858074075     1262 wget                  vec=TIMER
irq__softirq_exit        1 11624.858081389     1262 wget                  vec=TIMER
syscalls__sys_enter_read     1 11624.858166434     1262 wget                  nr=3, fd=3, buf=3213019456,      count=512
syscalls__sys_exit_read     1 11624.858177924     1262 wget                  nr=3, ret=512
skb__kfree_skb           1 11624.858878188     1262 wget                  skbaddr=3945041280,           location=3243922184, protocol=0
skb__kfree_skb           1 11624.858945608     1262 wget                  skbaddr=3945037824,      location=3243922184, protocol=0
irq__softirq_raise       1 11624.859020942     1262 wget                  vec=TIMER
irq__softirq_entry       1 11624.859076935     1262 wget                  vec=TIMER
irq__softirq_exit        1 11624.859083469     1262 wget                  vec=TIMER
syscalls__sys_enter_read     1 11624.859167565     1262 wget                  nr=3, fd=3, buf=3077701632,      count=1024
syscalls__sys_exit_read     1 11624.859192533     1262 wget                  nr=3, ret=471
syscalls__sys_enter_read     1 11624.859228072     1262 wget                  nr=3, fd=3, buf=3077701632,      count=1024
syscalls__sys_exit_read     1 11624.859233707     1262 wget                  nr=3, ret=0
syscalls__sys_enter_read     1 11624.859573008     1262 wget                  nr=3, fd=3, buf=3213018496,      count=512
syscalls__sys_exit_read     1 11624.859584818     1262 wget                  nr=3, ret=512
syscalls__sys_enter_read     1 11624.859864562     1262 wget                  nr=3, fd=3, buf=3077701632,      count=1024
syscalls__sys_exit_read     1 11624.859888770     1262 wget                  nr=3, ret=1024
syscalls__sys_enter_read     1 11624.859935140     1262 wget                  nr=3, fd=3, buf=3077701632,      count=1024
syscalls__sys_exit_read     1 11624.859944032     1262 wget                  nr=3, ret=1024
```

That in itself isn\'t very useful; after all, we can accomplish pretty much the same thing by simply running \'perf script\' without arguments in the same directory as the perf.data file.

> 那本身并不是很有用，毕竟，我们可以在与 perf.data 文件相同的目录下简单地运行“perf script”而不带参数就可以实现同样的目的。

We can however replace the print statements in the generated function bodies with whatever we want, and thereby make it infinitely more useful.

> 我们可以用任何我们想要的东西来替换生成的函数体中的打印语句，从而使其更加有用。

As a simple example, let\'s just replace the print statements in the function bodies with a simple function that does nothing but increment a per-event count. When the program is run against a perf.data file, each time a particular event is encountered, a tally is incremented for that event. For example:

> 例如，将函数体中的打印语句替换为一个简单的函数，该函数仅增加每个事件的计数。当程序运行在 perf.data 文件上时，每次遇到特定事件时，该事件的计数就会增加。例如：

```python
def net__netif_rx(event_name, context, common_cpu,
       common_secs, common_nsecs, common_pid, common_comm,
       skbaddr, len, name):
           inc_counts(event_name)
```

Each event handler function in the generated code is modified to do this. For convenience, we define a common function called inc_counts() that each handler calls; inc_counts() simply tallies a count for each event using the \'counts\' hash, which is a specialized hash function that does Perl-like autovivification, a capability that\'s extremely useful for kinds of multi-level aggregation commonly used in processing traces (see perf\'s documentation on the Python language binding for details):

> 每个生成代码中的事件处理程序都被修改来完成此操作。为了方便起见，我们定义一个称为 inc_counts（）的通用函数，每个处理程序都会调用它; inc_counts（）只是使用's counts'哈希统计每个事件的计数，这是一个专门的哈希函数，具有类似 Perl 的自动活化功能，这种功能对于处理跟踪中常用的多级聚合非常有用（有关详细信息，请参阅 Python 语言绑定的 perf 文档）：

```python
counts = autodict()

def inc_counts(event_name):
       try:
               counts[event_name] += 1
       except TypeError:
               counts[event_name] = 1
```

Finally, at the end of the trace processing run, we want to print the result of all the per-event tallies. For that, we use the special \'trace_end()\' function:

> 最后，在跟踪处理运行结束时，我们想要打印出所有每个事件计数的结果。为此，我们使用特殊的“trace_end（）”函数：

```python
def trace_end():
       for event_name, count in counts.iteritems():
               print "%-40s %10s\n" % (event_name, count)
```

The end result is a summary of all the events recorded in the trace:

> 最终结果是对跟踪记录中的所有事件的总结。

```shell
skb__skb_copy_datagram_iovec                  13148
irq__softirq_entry                             4796
irq__irq_handler_exit                          3805
irq__softirq_exit                              4795
syscalls__sys_enter_write                      8990
net__net_dev_xmit                               652
skb__kfree_skb                                 4047
sched__sched_wakeup                            1155
irq__irq_handler_entry                         3804
irq__softirq_raise                             4799
net__net_dev_queue                              652
syscalls__sys_enter_read                      17599
net__netif_receive_skb                         1743
syscalls__sys_exit_read                       17598
net__netif_rx                                     2
napi__napi_poll                                1877
syscalls__sys_exit_write                       8990
```

Note that this is pretty much exactly the same information we get from \'perf stat\', which goes a little way to support the idea mentioned previously that given the right kind of trace data, higher-level profiling-type summaries can be derived from it.

Documentation on using the [\'perf script\' Python binding](https://linux.die.net/man/1/perf-script-python).

> 文档：使用['perf script' Python 绑定](https://linux.die.net/man/1/perf-script-python)

### System-Wide Tracing and Profiling

The examples so far have focused on tracing a particular program or workload \-\-- in other words, every profiling run has specified the program to profile in the command-line e.g. \'perf record wget \...\'.

> 迄今为止的示例都集中在跟踪特定的程序或工作负载 - 也就是说，每次性能分析运行都在命令行中指定要分析的程序，例如'perf record wget \...\'。

It\'s also possible, and more interesting in many cases, to run a system-wide profile or trace while running the workload in a separate shell.

> 也有可能，在许多情况下更有趣，在单独的 shell 中运行工作负载的同时运行系统级别的配置文件或跟踪。

To do system-wide profiling or tracing, you typically use the -a flag to \'perf record\'.

> 要进行系统范围的分析或跟踪，通常使用 '-a' 标志来'perf record'。

To demonstrate this, open up one window and start the profile using the -a flag (press Ctrl-C to stop tracing):

> 为了演示这一点，打开一个窗口，并使用-a 标志启动配置文件（按 Ctrl-C 停止跟踪）：

```shell
root@crownbay:~# perf record -g -a
^C[ perf record: Woken up 6 times to write data ]
[ perf record: Captured and wrote 1.400 MB perf.data (~61172 samples) ]
```

In another window, run the wget test:

> 在另一个窗口中运行 wget 测试：

```shell
root@crownbay:~# wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2
Connecting to downloads.yoctoproject.org (140.211.169.59:80)
linux-2.6.19.2.tar.b 100% \|*******************************\| 41727k 0:00:00 ETA
```

Here we see entries not only for our wget load, but for other processes running on the system as well:

> 在这里，我们不仅可以看到 wget 加载的条目，还可以看到系统上运行的其他进程的条目：

![image](figures/perf-systemwide.png){.align-center width="70.0%"}

In the snapshot above, we can see callchains that originate in libc, and a callchain from Xorg that demonstrates that we\'re using a proprietary X driver in userspace (notice the presence of \'PVR\' and some other unresolvable symbols in the expanded Xorg callchain).

> 在上面的快照中，我们可以看到起源于 libc 的调用链，以及来自 Xorg 的调用链，这表明我们在用户空间使用了专有的 X 驱动程序（注意在展开的 Xorg 调用链中出现了'PVR'和一些其他无法解析的符号）。

Note also that we have both kernel and userspace entries in the above snapshot. We can also tell perf to focus on userspace but providing a modifier, in this case \'u\', to the \'cycles\' hardware counter when we record a profile:

```shell
root@crownbay:~# perf record -g -a -e cycles:u
^C[ perf record: Woken up 2 times to write data ]
[ perf record: Captured and wrote 0.376 MB perf.data (~16443 samples) ]
```

![image](figures/perf-report-cycles-u.png){.align-center width="70.0%"}

Notice in the screenshot above, we see only userspace entries (\[.\])

> 在上面的截图中，我们只看到用户空间条目（\[.\]）

Finally, we can press \'enter\' on a leaf node and select the \'Zoom into DSO\' menu item to show only entries associated with a specific DSO. In the screenshot below, we\'ve zoomed into the \'libc\' DSO which shows all the entries associated with the libc-xxx.so DSO.

> 最后，我们可以在叶节点上按下“回车”键，然后选择“放大 DSO”菜单项，以显示与特定 DSO 相关的条目。在下面的屏幕截图中，我们已经放大了“libc”DSO，其中显示了所有与 libc-xxx.so DSO 相关的条目。

![image](figures/perf-systemwide-libc.png){.align-center width="70.0%"}

We can also use the system-wide -a switch to do system-wide tracing. Here we\'ll trace a couple of scheduler events:

> 我们还可以使用系统级 -a 开关来进行系统级跟踪。这里我们将跟踪几个调度程序事件：

```shell
root@crownbay:~# perf record -a -e sched:sched_switch -e sched:sched_wakeup
^C[ perf record: Woken up 38 times to write data ]
[ perf record: Captured and wrote 9.780 MB perf.data (~427299 samples) ]
```

We can look at the raw output using \'perf script\' with no arguments:

> 我们可以使用没有参数的“perf script”来查看原始输出：

```shell
root@crownbay:~# perf script

           perf  1383 [001]  6171.460045: sched_wakeup: comm=kworker/1:1 pid=21 prio=120 success=1 target_cpu=001
           perf  1383 [001]  6171.460066: sched_switch: prev_comm=perf prev_pid=1383 prev_prio=120 prev_state=R+ ==> next_comm=kworker/1:1 next_pid=21 next_prio=120
    kworker/1:1    21 [001]  6171.460093: sched_switch: prev_comm=kworker/1:1 prev_pid=21 prev_prio=120 prev_state=S ==> next_comm=perf next_pid=1383 next_prio=120
        swapper     0 [000]  6171.468063: sched_wakeup: comm=kworker/0:3 pid=1209 prio=120 success=1 target_cpu=000
        swapper     0 [000]  6171.468107: sched_switch: prev_comm=swapper/0 prev_pid=0 prev_prio=120 prev_state=R ==> next_comm=kworker/0:3 next_pid=1209 next_prio=120
    kworker/0:3  1209 [000]  6171.468143: sched_switch: prev_comm=kworker/0:3 prev_pid=1209 prev_prio=120 prev_state=S ==> next_comm=swapper/0 next_pid=0 next_prio=120
           perf  1383 [001]  6171.470039: sched_wakeup: comm=kworker/1:1 pid=21 prio=120 success=1 target_cpu=001
           perf  1383 [001]  6171.470058: sched_switch: prev_comm=perf prev_pid=1383 prev_prio=120 prev_state=R+ ==> next_comm=kworker/1:1 next_pid=21 next_prio=120
    kworker/1:1    21 [001]  6171.470082: sched_switch: prev_comm=kworker/1:1 prev_pid=21 prev_prio=120 prev_state=S ==> next_comm=perf next_pid=1383 next_prio=120
           perf  1383 [001]  6171.480035: sched_wakeup: comm=kworker/1:1 pid=21 prio=120 success=1 target_cpu=001
```

#### Filtering

Notice that there are a lot of events that don\'t really have anything to do with what we\'re interested in, namely events that schedule \'perf\' itself in and out or that wake perf up. We can get rid of those by using the \'\--filter\' option \-\-- for each event we specify using -e, we can add a \--filter after that to filter out trace events that contain fields with specific values:

> 注意，有许多事件与我们感兴趣的内容没有任何关系，即安排“perf”本身进出或唤醒 perf 的事件。我们可以通过使用“--filter”选项来消除这些事件-对于我们使用-e 指定的每个事件，我们可以在其后添加一个--filter 来过滤掉包含特定值字段的跟踪事件：

```shell
root@crownbay:~# perf record -a -e sched:sched_switch --filter 'next_comm != perf && prev_comm != perf' -e sched:sched_wakeup --filter 'comm != perf'
^C[ perf record: Woken up 38 times to write data ]
[ perf record: Captured and wrote 9.688 MB perf.data (~423279 samples) ]


root@crownbay:~# perf script

        swapper     0 [000]  7932.162180: sched_switch: prev_comm=swapper/0 prev_pid=0 prev_prio=120 prev_state=R ==> next_comm=kworker/0:3 next_pid=1209 next_prio=120
    kworker/0:3  1209 [000]  7932.162236: sched_switch: prev_comm=kworker/0:3 prev_pid=1209 prev_prio=120 prev_state=S ==> next_comm=swapper/0 next_pid=0 next_prio=120
           perf  1407 [001]  7932.170048: sched_wakeup: comm=kworker/1:1 pid=21 prio=120 success=1 target_cpu=001
           perf  1407 [001]  7932.180044: sched_wakeup: comm=kworker/1:1 pid=21 prio=120 success=1 target_cpu=001
           perf  1407 [001]  7932.190038: sched_wakeup: comm=kworker/1:1 pid=21 prio=120 success=1 target_cpu=001
           perf  1407 [001]  7932.200044: sched_wakeup: comm=kworker/1:1 pid=21 prio=120 success=1 target_cpu=001
           perf  1407 [001]  7932.210044: sched_wakeup: comm=kworker/1:1 pid=21 prio=120 success=1 target_cpu=001
           perf  1407 [001]  7932.220044: sched_wakeup: comm=kworker/1:1 pid=21 prio=120 success=1 target_cpu=001
        swapper     0 [001]  7932.230111: sched_wakeup: comm=kworker/1:1 pid=21 prio=120 success=1 target_cpu=001
        swapper     0 [001]  7932.230146: sched_switch: prev_comm=swapper/1 prev_pid=0 prev_prio=120 prev_state=R ==> next_comm=kworker/1:1 next_pid=21 next_prio=120
    kworker/1:1    21 [001]  7932.230205: sched_switch: prev_comm=kworker/1:1 prev_pid=21 prev_prio=120 prev_state=S ==> next_comm=swapper/1 next_pid=0 next_prio=120
        swapper     0 [000]  7932.326109: sched_wakeup: comm=kworker/0:3 pid=1209 prio=120 success=1 target_cpu=000
        swapper     0 [000]  7932.326171: sched_switch: prev_comm=swapper/0 prev_pid=0 prev_prio=120 prev_state=R ==> next_comm=kworker/0:3 next_pid=1209 next_prio=120
    kworker/0:3  1209 [000]  7932.326214: sched_switch: prev_comm=kworker/0:3 prev_pid=1209 prev_prio=120 prev_state=S ==> next_comm=swapper/0 next_pid=0 next_prio=120
```

In this case, we\'ve filtered out all events that have \'perf\' in their \'comm\' or \'comm_prev\' or \'comm_next\' fields. Notice that there are still events recorded for perf, but notice that those events don\'t have values of \'perf\' for the filtered fields. To completely filter out anything from perf will require a bit more work, but for the purpose of demonstrating how to use filters, it\'s close enough.

> 在这种情况下，我们过滤掉了所有在其“comm”，“comm_prev”或“comm_next”字段中包含“perf”的事件。请注意，仍然有一些关于 perf 的事件被记录，但是请注意，这些事件的过滤字段中没有“perf”的值。要完全过滤掉任何来自 perf 的内容需要做更多的工作，但是为了演示如何使用过滤器，它已经足够接近了。

::: admonition

Tying it Together

> 把它拼接在一起

These are exactly the same set of event filters defined by the trace event subsystem. See the ftrace/tracecmd/kernelshark section for more discussion about these event filters.

> 这些与跟踪事件子系统定义的事件过滤器完全相同。有关这些事件过滤器的更多讨论，请参阅 ftrace/tracecmd/kernelshark 部分。
> :::

::: admonition

Tying it Together

> 把它拼接在一起

These event filters are implemented by a special-purpose pseudo-interpreter in the kernel and are an integral and indispensable part of the perf design as it relates to tracing. kernel-based event filters provide a mechanism to precisely throttle the event stream that appears in user space, where it makes sense to provide bindings to real programming languages for postprocessing the event stream. This architecture allows for the intelligent and flexible partitioning of processing between the kernel and user space. Contrast this with other tools such as SystemTap, which does all of its processing in the kernel and as such requires a special project-defined language in order to accommodate that design, or LTTng, where everything is sent to userspace and as such requires a super-efficient kernel-to-userspace transport mechanism in order to function properly. While perf certainly can benefit from for instance advances in the design of the transport, it doesn\'t fundamentally depend on them. Basically, if you find that your perf tracing application is causing buffer I/O overruns, it probably means that you aren\'t taking enough advantage of the kernel filtering engine.

> 这些事件过滤器由内核中的一个特殊用途伪解释器实现，是 perf 设计相关跟踪的不可或缺的组成部分。基于内核的事件过滤器提供了一种机制，可以精确地控制出现在用户空间的事件流，在这里提供实际编程语言的绑定来对事件流进行后处理是有意义的。这种体系结构允许在内核和用户空间之间进行智能且灵活的处理分割。与其他工具（例如 SystemTap）形成对比，它在内核中执行所有处理，因此需要一种特定项目定义的语言来适应其设计，或者 LTTng，其中所有内容都发送到用户空间，因此需要超级高效的内核到用户空间传输机制才能正常工作。虽然 perf 当然可以从传输设计方面获得一些好处，但它不是从根本上依赖它们。基本上，如果您发现您的 perf 跟踪应用程序导致缓冲区 I/O 溢出，则可能意味着您没有充分利用内核过滤引擎。
> :::

### Using Dynamic Tracepoints

perf isn\'t restricted to the fixed set of static tracepoints listed by \'perf list\'. Users can also add their own \'dynamic\' tracepoints anywhere in the kernel. For instance, suppose we want to define our own tracepoint on do_fork(). We can do that using the \'perf probe\' perf subcommand:

> perf 不仅仅局限于'perf list'列出的固定的静态跟踪点。用户也可以在内核的任何地方添加自己的“动态”跟踪点。例如，假设我们想在 do_fork（）上定义自己的跟踪点。我们可以使用'perf probe' perf 子命令来完成：

```shell
root@crownbay:~# perf probe do_fork
Added new event:
  probe:do_fork        (on do_fork)

You can now use it in all perf tools, such as:

  perf record -e probe:do_fork -aR sleep 1
```

Adding a new tracepoint via \'perf probe\' results in an event with all the expected files and format in /sys/kernel/debug/tracing/events, just the same as for static tracepoints (as discussed in more detail in the trace events subsystem section:

> 通过'perf probe'添加新的跟踪点会在/sys/kernel/debug/tracing/events 中产生一个具有所有预期文件和格式的事件，就像静态跟踪点一样（详情参见跟踪事件子系统部分）。

```shell
root@crownbay:/sys/kernel/debug/tracing/events/probe/do_fork# ls -al
drwxr-xr-x    2 root     root             0 Oct 28 11:42 .
drwxr-xr-x    3 root     root             0 Oct 28 11:42 ..
-rw-r--r--    1 root     root             0 Oct 28 11:42 enable
-rw-r--r--    1 root     root             0 Oct 28 11:42 filter
-r--r--r--    1 root     root             0 Oct 28 11:42 format
-r--r--r--    1 root     root             0 Oct 28 11:42 id

root@crownbay:/sys/kernel/debug/tracing/events/probe/do_fork# cat format
name: do_fork
ID: 944
format:
        field:unsigned short common_type;    offset:0;   size:2; signed:0;
        field:unsigned char common_flags;    offset:2;   size:1; signed:0;
        field:unsigned char common_preempt_count;    offset:3;   size:1; signed:0;
        field:int common_pid;    offset:4;   size:4; signed:1;
        field:int common_padding;    offset:8;   size:4; signed:1;

        field:unsigned long __probe_ip;  offset:12;  size:4; signed:0;

print fmt: "(%lx)", REC->__probe_ip
```

We can list all dynamic tracepoints currently in existence:

> 我们可以列出所有当前存在的动态跟踪点：

```shell
root@crownbay:~# perf probe -l
 probe:do_fork (on do_fork)
 probe:schedule (on schedule)
```

Let\'s record system-wide (\'sleep 30\' is a trick for recording system-wide but basically do nothing and then wake up after 30 seconds):

> 让我们记录系统范围（'睡眠 30'是一个记录系统范围的技巧，但基本上什么也不做，然后 30 秒后醒来）：

```shell
root@crownbay:~# perf record -g -a -e probe:do_fork sleep 30
[ perf record: Woken up 1 times to write data ]
[ perf record: Captured and wrote 0.087 MB perf.data (~3812 samples) ]
```

Using \'perf script\' we can see each do_fork event that fired:

> 使用'perf script'，我们可以看到每一次触发的 do_fork 事件：

```shell
root@crownbay:~# perf script

# ========
# captured on: Sun Oct 28 11:55:18 2012
# hostname : crownbay
# os release : 3.4.11-yocto-standard
# perf version : 3.4.11
# arch : i686
# nrcpus online : 2
# nrcpus avail : 2
# cpudesc : Intel(R) Atom(TM) CPU E660 @ 1.30GHz
# cpuid : GenuineIntel,6,38,1
# total memory : 1017184 kB
# cmdline : /usr/bin/perf record -g -a -e probe:do_fork sleep 30
# event : name = probe:do_fork, type = 2, config = 0x3b0, config1 = 0x0, config2 = 0x0, excl_usr = 0, excl_kern
 = 0, id = { 5, 6 }
# HEADER_CPU_TOPOLOGY info available, use -I to display
# ========
#
 matchbox-deskto  1197 [001] 34211.378318: do_fork: (c1028460)
 matchbox-deskto  1295 [001] 34211.380388: do_fork: (c1028460)
         pcmanfm  1296 [000] 34211.632350: do_fork: (c1028460)
         pcmanfm  1296 [000] 34211.639917: do_fork: (c1028460)
 matchbox-deskto  1197 [001] 34217.541603: do_fork: (c1028460)
 matchbox-deskto  1299 [001] 34217.543584: do_fork: (c1028460)
          gthumb  1300 [001] 34217.697451: do_fork: (c1028460)
          gthumb  1300 [001] 34219.085734: do_fork: (c1028460)
          gthumb  1300 [000] 34219.121351: do_fork: (c1028460)
          gthumb  1300 [001] 34219.264551: do_fork: (c1028460)
         pcmanfm  1296 [000] 34219.590380: do_fork: (c1028460)
 matchbox-deskto  1197 [001] 34224.955965: do_fork: (c1028460)
 matchbox-deskto  1306 [001] 34224.957972: do_fork: (c1028460)
 matchbox-termin  1307 [000] 34225.038214: do_fork: (c1028460)
 matchbox-termin  1307 [001] 34225.044218: do_fork: (c1028460)
 matchbox-termin  1307 [000] 34225.046442: do_fork: (c1028460)
 matchbox-deskto  1197 [001] 34237.112138: do_fork: (c1028460)
 matchbox-deskto  1311 [001] 34237.114106: do_fork: (c1028460)
            gaku  1312 [000] 34237.202388: do_fork: (c1028460)
```

And using \'perf report\' on the same file, we can see the callgraphs from starting a few programs during those 30 seconds:

> 使用同一个文件的'perf report'，我们可以看到在这 30 秒内启动几个程序的调用图：

![image](figures/perf-probe-do_fork-profile.png){.align-center width="70.0%"}

::: admonition

Tying it Together

> 将它们结合在一起

The trace events subsystem accommodate static and dynamic tracepoints in exactly the same way \-\-- there\'s no difference as far as the infrastructure is concerned. See the ftrace section for more details on the trace event subsystem.

> 跟踪事件子系统以完全相同的方式容纳静态和动态跟踪点-- 就基础架构而言没有差别。 有关跟踪事件子系统的更多详细信息，请参阅 ftrace 部分。
> :::

::: admonition

Tying it Together

> 把它拼凑在一起

Dynamic tracepoints are implemented under the covers by kprobes and uprobes. kprobes and uprobes are also used by and in fact are the main focus of SystemTap.

> 动态跟踪点由 kprobes 和 uprobes 在底层实现。kprobes 和 uprobes 也被 SystemTap 使用，实际上是 SystemTap 的主要焦点。
> :::

## Perf Documentation

Online versions of the man pages for the commands discussed in this section can be found here:

> 网上版本的本节中讨论的命令的手册页可以在这里找到：

- The [\'perf stat\' manpage](https://linux.die.net/man/1/perf-stat).
- The [\'perf record\' manpage](https://linux.die.net/man/1/perf-record).
- The [\'perf report\' manpage](https://linux.die.net/man/1/perf-report).
- The [\'perf probe\' manpage](https://linux.die.net/man/1/perf-probe).
- The [\'perf script\' manpage](https://linux.die.net/man/1/perf-script).
- Documentation on using the [\'perf script\' Python binding](https://linux.die.net/man/1/perf-script-python).

> 文档：使用[“perf script” Python 绑定](https://linux.die.net/man/1/perf-script-python)。

- The top-level [perf(1) manpage](https://linux.die.net/man/1/perf).

Normally, you should be able to invoke the man pages via perf itself e.g. \'perf help\' or \'perf help record\'.

> 一般来说，你可以通过 perf 本身来调用 man 页面，例如'perf help'或者'perf help record'。

To have the perf manpages installed on your target, modify your configuration as follows:

> 要在目标上安装 perf manpages，请按照以下步骤修改配置：

```shell
IMAGE_INSTALL:append = " perf perf-doc"
DISTRO_FEATURES:append = " api-documentation"
```

The man pages in text form, along with some other files, such as a set of examples, can also be found in the \'perf\' directory of the kernel tree:

> 在内核树的'perf'目录中，还可以找到以文本形式存在的 man 页面以及一些其他文件，比如一组示例：

```shell
tools/perf/Documentation
```

There\'s also a nice perf tutorial on the perf wiki that goes into more detail than we do here in certain areas: [Perf Tutorial](https://perf.wiki.kernel.org/index.php/Tutorial)

> 也有一个关于 perf 的很棒的教程，在某些方面比我们在这里更详细：[Perf Tutorial](https://perf.wiki.kernel.org/index.php/Tutorial)

# ftrace

\'ftrace\' literally refers to the \'ftrace function tracer\' but in reality this encompasses a number of related tracers along with the infrastructure that they all make use of.

> 'ftrace'字面上指的是'ftrace 函数跟踪器'，但实际上，它包括许多相关的跟踪器以及它们都使用的基础设施。

## ftrace Setup

For this section, we\'ll assume you\'ve already performed the basic setup outlined in the \"`profile-manual/intro:General Setup`{.interpreted-text role="ref"}\" section.

> 对于本节，我们假设您已经执行了“profile-manual / intro：General Setup”部分中概述的基本设置。

ftrace, trace-cmd, and kernelshark run on the target system, and are ready to go out-of-the-box \-\-- no additional setup is necessary. For the rest of this section we assume you\'ve ssh\'ed to the host and will be running ftrace on the target. kernelshark is a GUI application and if you use the \'-X\' option to ssh you can have the kernelshark GUI run on the target but display remotely on the host if you want.

> ftrace、trace-cmd 和 kernelshark 在目标系统上运行，可以直接使用，无需进行额外的设置。在本节中，我们假设您已经通过 ssh 连接到主机，并将在目标上运行 ftrace。kernelshark 是一个 GUI 应用程序，如果您使用'-X'选项进行 ssh，则可以在目标上运行 kernelshark GUI，但可以远程显示在主机上。

## Basic ftrace usage

\'ftrace\' essentially refers to everything included in the /tracing directory of the mounted debugfs filesystem (Yocto follows the standard convention and mounts it at /sys/kernel/debug). Here\'s a listing of all the files found in /sys/kernel/debug/tracing on a Yocto system:

> 'ftrace'基本上是指挂载在/sys/kernel/debug 的/tracing 目录中包含的所有内容（Yocto 遵循标准惯例，将其挂载在/sys/kernel/debug）。以下是 Yocto 系统上/sys/kernel/debug/tracing 中发现的所有文件的列表：

```shell
root@sugarbay:/sys/kernel/debug/tracing# ls
README                      kprobe_events               trace
available_events            kprobe_profile              trace_clock
available_filter_functions  options                     trace_marker
available_tracers           per_cpu                     trace_options
buffer_size_kb              printk_formats              trace_pipe
buffer_total_size_kb        saved_cmdlines              tracing_cpumask
current_tracer              set_event                   tracing_enabled
dyn_ftrace_total_info       set_ftrace_filter           tracing_on
enabled_functions           set_ftrace_notrace          tracing_thresh
events                      set_ftrace_pid
free_buffer                 set_graph_function
```

The files listed above are used for various purposes - some relate directly to the tracers themselves, others are used to set tracing options, and yet others actually contain the tracing output when a tracer is in effect. Some of the functions can be guessed from their names, others need explanation; in any case, we\'ll cover some of the files we see here below but for an explanation of the others, please see the ftrace documentation.

> 以上列出的文件可用于各种用途 - 有些与跟踪器本身有关，有些用于设置跟踪选项，还有一些在跟踪器生效时实际包含跟踪输出。一些功能可以从它们的名称猜出，其他需要解释; 无论如何，我们将在下面介绍一些我们在这里看到的文件，但其他文件的解释，请参阅 ftrace 文档。

We\'ll start by looking at some of the available built-in tracers.

> 我们将从查看一些可用的内置跟踪器开始。

cat\'ing the \'available_tracers\' file lists the set of available tracers:

> 查看 `available_tracers` 文件可以列出可用的跟踪器的集合：

```shell
root@sugarbay:/sys/kernel/debug/tracing# cat available_tracers
blk function_graph function nop
```

The \'current_tracer\' file contains the tracer currently in effect:

> 当前的'tracer'文件包含当前有效的跟踪器：

```shell
root@sugarbay:/sys/kernel/debug/tracing# cat current_tracer
nop
```

The above listing of current_tracer shows that the \'nop\' tracer is in effect, which is just another way of saying that there\'s actually no tracer currently in effect.

> 以上列出的 current_tracer 表明'nop'跟踪器正在生效，这只是另一种表达方式，表明目前没有跟踪器生效。

echo\'ing one of the available_tracers into current_tracer makes the specified tracer the current tracer:

> 回声其中一个可用的跟踪器到当前跟踪器，可以使指定的跟踪器成为当前跟踪器。

```shell
root@sugarbay:/sys/kernel/debug/tracing# echo function > current_tracer
root@sugarbay:/sys/kernel/debug/tracing# cat current_tracer
function
```

The above sets the current tracer to be the \'function tracer\'. This tracer traces every function call in the kernel and makes it available as the contents of the \'trace\' file. Reading the \'trace\' file lists the currently buffered function calls that have been traced by the function tracer:

> 上面将当前跟踪器设置为'功能跟踪器'。此跟踪器跟踪内核中的每个函数调用，并将其作为'trace'文件的内容提供。阅读'trace'文件列出了由功能跟踪器跟踪的当前缓冲函数调用：

```shell
root@sugarbay:/sys/kernel/debug/tracing# cat trace | less

# tracer: function
#
# entries-in-buffer/entries-written: 310629/766471   #P:8
#
#                              _-----=> irqs-off
#                             / _----=> need-resched
#                            | / _---=> hardirq/softirq
#                            || / _--=> preempt-depth
#                            ||| /     delay
#           TASK-PID   CPU#  ||||    TIMESTAMP  FUNCTION
#              | |       |   ||||       |         |
         <idle>-0     [004] d..1   470.867169: ktime_get_real <-intel_idle
         <idle>-0     [004] d..1   470.867170: getnstimeofday <-ktime_get_real
         <idle>-0     [004] d..1   470.867171: ns_to_timeval <-intel_idle
         <idle>-0     [004] d..1   470.867171: ns_to_timespec <-ns_to_timeval
         <idle>-0     [004] d..1   470.867172: smp_apic_timer_interrupt <-apic_timer_interrupt
         <idle>-0     [004] d..1   470.867172: native_apic_mem_write <-smp_apic_timer_interrupt
         <idle>-0     [004] d..1   470.867172: irq_enter <-smp_apic_timer_interrupt
         <idle>-0     [004] d..1   470.867172: rcu_irq_enter <-irq_enter
         <idle>-0     [004] d..1   470.867173: rcu_idle_exit_common.isra.33 <-rcu_irq_enter
         <idle>-0     [004] d..1   470.867173: local_bh_disable <-irq_enter
         <idle>-0     [004] d..1   470.867173: add_preempt_count <-local_bh_disable
         <idle>-0     [004] d.s1   470.867174: tick_check_idle <-irq_enter
         <idle>-0     [004] d.s1   470.867174: tick_check_oneshot_broadcast <-tick_check_idle
         <idle>-0     [004] d.s1   470.867174: ktime_get <-tick_check_idle
         <idle>-0     [004] d.s1   470.867174: tick_nohz_stop_idle <-tick_check_idle
         <idle>-0     [004] d.s1   470.867175: update_ts_time_stats <-tick_nohz_stop_idle
         <idle>-0     [004] d.s1   470.867175: nr_iowait_cpu <-update_ts_time_stats
         <idle>-0     [004] d.s1   470.867175: tick_do_update_jiffies64 <-tick_check_idle
         <idle>-0     [004] d.s1   470.867175: _raw_spin_lock <-tick_do_update_jiffies64
         <idle>-0     [004] d.s1   470.867176: add_preempt_count <-_raw_spin_lock
         <idle>-0     [004] d.s2   470.867176: do_timer <-tick_do_update_jiffies64
         <idle>-0     [004] d.s2   470.867176: _raw_spin_lock <-do_timer
         <idle>-0     [004] d.s2   470.867176: add_preempt_count <-_raw_spin_lock
         <idle>-0     [004] d.s3   470.867177: ntp_tick_length <-do_timer
         <idle>-0     [004] d.s3   470.867177: _raw_spin_lock_irqsave <-ntp_tick_length
         .
         .
         .
```

Each line in the trace above shows what was happening in the kernel on a given cpu, to the level of detail of function calls. Each entry shows the function called, followed by its caller (after the arrow).

> 每一行跟踪显示了内核在给定的 CPU 上发生的情况，到函数调用的细节程度。每个条目显示被调用的函数，其调用者在箭头之后。

The function tracer gives you an extremely detailed idea of what the kernel was doing at the point in time the trace was taken, and is a great way to learn about how the kernel code works in a dynamic sense.

> 功能跟踪器可以给你极其详细的了解内核在跟踪取样时的运行情况，是学习内核代码如何在动态状态下运行的一个很好的方式。

::: admonition

Tying it Together

> 把它拴在一起

The ftrace function tracer is also available from within perf, as the ftrace:function tracepoint.

> ftrace 功能跟踪器也可以从 perf 中获得，作为 ftrace：功能跟踪点。
> :::

It is a little more difficult to follow the call chains than it needs to be \-\-- luckily there\'s a variant of the function tracer that displays the callchains explicitly, called the \'function_graph\' tracer:

> 这比需要的要难跟踪调用链条一点点。幸运的是，有一种称为“function_graph”的跟踪器，可以明确显示调用链条：

```shell
root@sugarbay:/sys/kernel/debug/tracing# echo function_graph > current_tracer
root@sugarbay:/sys/kernel/debug/tracing# cat trace | less

 tracer: function_graph

 CPU  DURATION                  FUNCTION CALLS
 |     |   |                     |   |   |   |
7)   0.046 us    |      pick_next_task_fair();
7)   0.043 us    |      pick_next_task_stop();
7)   0.042 us    |      pick_next_task_rt();
7)   0.032 us    |      pick_next_task_fair();
7)   0.030 us    |      pick_next_task_idle();
7)               |      _raw_spin_unlock_irq() {
7)   0.033 us    |        sub_preempt_count();
7)   0.258 us    |      }
7)   0.032 us    |      sub_preempt_count();
7) + 13.341 us   |    } /* __schedule */
7)   0.095 us    |  } /* sub_preempt_count */
7)               |  schedule() {
7)               |    __schedule() {
7)   0.060 us    |      add_preempt_count();
7)   0.044 us    |      rcu_note_context_switch();
7)               |      _raw_spin_lock_irq() {
7)   0.033 us    |        add_preempt_count();
7)   0.247 us    |      }
7)               |      idle_balance() {
7)               |        _raw_spin_unlock() {
7)   0.031 us    |          sub_preempt_count();
7)   0.246 us    |        }
7)               |        update_shares() {
7)   0.030 us    |          __rcu_read_lock();
7)   0.029 us    |          __rcu_read_unlock();
7)   0.484 us    |        }
7)   0.030 us    |        __rcu_read_lock();
7)               |        load_balance() {
7)               |          find_busiest_group() {
7)   0.031 us    |            idle_cpu();
7)   0.029 us    |            idle_cpu();
7)   0.035 us    |            idle_cpu();
7)   0.906 us    |          }
7)   1.141 us    |        }
7)   0.022 us    |        msecs_to_jiffies();
7)               |        load_balance() {
7)               |          find_busiest_group() {
7)   0.031 us    |            idle_cpu();
.
.
.
4)   0.062 us    |        msecs_to_jiffies();
4)   0.062 us    |        __rcu_read_unlock();
4)               |        _raw_spin_lock() {
4)   0.073 us    |          add_preempt_count();
4)   0.562 us    |        }
4) + 17.452 us   |      }
4)   0.108 us    |      put_prev_task_fair();
4)   0.102 us    |      pick_next_task_fair();
4)   0.084 us    |      pick_next_task_stop();
4)   0.075 us    |      pick_next_task_rt();
4)   0.062 us    |      pick_next_task_fair();
4)   0.066 us    |      pick_next_task_idle();
------------------------------------------
4)   kworker-74   =>    <idle>-0
------------------------------------------

4)               |      finish_task_switch() {
4)               |        _raw_spin_unlock_irq() {
4)   0.100 us    |          sub_preempt_count();
4)   0.582 us    |        }
4)   1.105 us    |      }
4)   0.088 us    |      sub_preempt_count();
4) ! 100.066 us  |    }
.
.
.
3)               |  sys_ioctl() {
3)   0.083 us    |    fget_light();
3)               |    security_file_ioctl() {
3)   0.066 us    |      cap_file_ioctl();
3)   0.562 us    |    }
3)               |    do_vfs_ioctl() {
3)               |      drm_ioctl() {
3)   0.075 us    |        drm_ut_debug_printk();
3)               |        i915_gem_pwrite_ioctl() {
3)               |          i915_mutex_lock_interruptible() {
3)   0.070 us    |            mutex_lock_interruptible();
3)   0.570 us    |          }
3)               |          drm_gem_object_lookup() {
3)               |            _raw_spin_lock() {
3)   0.080 us    |              add_preempt_count();
3)   0.620 us    |            }
3)               |            _raw_spin_unlock() {
3)   0.085 us    |              sub_preempt_count();
3)   0.562 us    |            }
3)   2.149 us    |          }
3)   0.133 us    |          i915_gem_object_pin();
3)               |          i915_gem_object_set_to_gtt_domain() {
3)   0.065 us    |            i915_gem_object_flush_gpu_write_domain();
3)   0.065 us    |            i915_gem_object_wait_rendering();
3)   0.062 us    |            i915_gem_object_flush_cpu_write_domain();
3)   1.612 us    |          }
3)               |          i915_gem_object_put_fence() {
3)   0.097 us    |            i915_gem_object_flush_fence.constprop.36();
3)   0.645 us    |          }
3)   0.070 us    |          add_preempt_count();
3)   0.070 us    |          sub_preempt_count();
3)   0.073 us    |          i915_gem_object_unpin();
3)   0.068 us    |          mutex_unlock();
3)   9.924 us    |        }
3) + 11.236 us   |      }
3) + 11.770 us   |    }
3) + 13.784 us   |  }
3)               |  sys_ioctl() {
```

As you can see, the function_graph display is much easier to follow. Also note that in addition to the function calls and associated braces, other events such as scheduler events are displayed in context. In fact, you can freely include any tracepoint available in the trace events subsystem described in the next section by simply enabling those events, and they\'ll appear in context in the function graph display. Quite a powerful tool for understanding kernel dynamics.

> 看到了吧，函数图显示更容易跟踪。另外，除了函数调用和相关的花括号外，其他事件，如调度程序事件也会在上下文中显示出来。实际上，您可以通过简单地启用这些事件，就可以在函数图显示中自由地包含下一节中描述的跟踪事件子系统中的任何跟踪点，它们将在上下文中显示出来。这是理解内核动态的一个强大的工具。

Also notice that there are various annotations on the left hand side of the display. For example if the total time it took for a given function to execute is above a certain threshold, an exclamation point or plus sign appears on the left hand side. Please see the ftrace documentation for details on all these fields.

> 也请注意，显示器的左侧有各种注释。例如，如果给定函数执行所需的总时间超过某个阈值，则左侧会出现感叹号或加号。有关所有这些字段的详细信息，请参阅 ftrace 文档。

## The \'trace events\' Subsystem

One especially important directory contained within the /sys/kernel/debug/tracing directory is the \'events\' subdirectory, which contains representations of every tracepoint in the system. Listing out the contents of the \'events\' subdirectory, we see mainly another set of subdirectories:

> /sys/kernel/debug/tracing 目录中的一个特别重要的目录是 'events' 子目录，其中包含系统中每个跟踪点的表示。列出 'events' 子目录的内容，我们主要看到另一组子目录：

```shell
root@sugarbay:/sys/kernel/debug/tracing# cd events
root@sugarbay:/sys/kernel/debug/tracing/events# ls -al
drwxr-xr-x   38 root     root             0 Nov 14 23:19 .
drwxr-xr-x    5 root     root             0 Nov 14 23:19 ..
drwxr-xr-x   19 root     root             0 Nov 14 23:19 block
drwxr-xr-x   32 root     root             0 Nov 14 23:19 btrfs
drwxr-xr-x    5 root     root             0 Nov 14 23:19 drm
-rw-r--r--    1 root     root             0 Nov 14 23:19 enable
drwxr-xr-x   40 root     root             0 Nov 14 23:19 ext3
drwxr-xr-x   79 root     root             0 Nov 14 23:19 ext4
drwxr-xr-x   14 root     root             0 Nov 14 23:19 ftrace
drwxr-xr-x    8 root     root             0 Nov 14 23:19 hda
-r--r--r--    1 root     root             0 Nov 14 23:19 header_event
-r--r--r--    1 root     root             0 Nov 14 23:19 header_page
drwxr-xr-x   25 root     root             0 Nov 14 23:19 i915
drwxr-xr-x    7 root     root             0 Nov 14 23:19 irq
drwxr-xr-x   12 root     root             0 Nov 14 23:19 jbd
drwxr-xr-x   14 root     root             0 Nov 14 23:19 jbd2
drwxr-xr-x   14 root     root             0 Nov 14 23:19 kmem
drwxr-xr-x    7 root     root             0 Nov 14 23:19 module
drwxr-xr-x    3 root     root             0 Nov 14 23:19 napi
drwxr-xr-x    6 root     root             0 Nov 14 23:19 net
drwxr-xr-x    3 root     root             0 Nov 14 23:19 oom
drwxr-xr-x   12 root     root             0 Nov 14 23:19 power
drwxr-xr-x    3 root     root             0 Nov 14 23:19 printk
drwxr-xr-x    8 root     root             0 Nov 14 23:19 random
drwxr-xr-x    4 root     root             0 Nov 14 23:19 raw_syscalls
drwxr-xr-x    3 root     root             0 Nov 14 23:19 rcu
drwxr-xr-x    6 root     root             0 Nov 14 23:19 rpm
drwxr-xr-x   20 root     root             0 Nov 14 23:19 sched
drwxr-xr-x    7 root     root             0 Nov 14 23:19 scsi
drwxr-xr-x    4 root     root             0 Nov 14 23:19 signal
drwxr-xr-x    5 root     root             0 Nov 14 23:19 skb
drwxr-xr-x    4 root     root             0 Nov 14 23:19 sock
drwxr-xr-x   10 root     root             0 Nov 14 23:19 sunrpc
drwxr-xr-x  538 root     root             0 Nov 14 23:19 syscalls
drwxr-xr-x    4 root     root             0 Nov 14 23:19 task
drwxr-xr-x   14 root     root             0 Nov 14 23:19 timer
drwxr-xr-x    3 root     root             0 Nov 14 23:19 udp
drwxr-xr-x   21 root     root             0 Nov 14 23:19 vmscan
drwxr-xr-x    3 root     root             0 Nov 14 23:19 vsyscall
drwxr-xr-x    6 root     root             0 Nov 14 23:19 workqueue
drwxr-xr-x   26 root     root             0 Nov 14 23:19 writeback
```

Each one of these subdirectories corresponds to a \'subsystem\' and contains yet again more subdirectories, each one of those finally corresponding to a tracepoint. For example, here are the contents of the \'kmem\' subsystem:

> 每个子目录对应一个'子系统'，并且又包含更多的子目录，每个子目录最终对应一个跟踪点。例如，这是'kmem'子系统的内容：

```shell
root@sugarbay:/sys/kernel/debug/tracing/events# cd kmem
root@sugarbay:/sys/kernel/debug/tracing/events/kmem# ls -al
drwxr-xr-x   14 root     root             0 Nov 14 23:19 .
drwxr-xr-x   38 root     root             0 Nov 14 23:19 ..
-rw-r--r--    1 root     root             0 Nov 14 23:19 enable
-rw-r--r--    1 root     root             0 Nov 14 23:19 filter
drwxr-xr-x    2 root     root             0 Nov 14 23:19 kfree
drwxr-xr-x    2 root     root             0 Nov 14 23:19 kmalloc
drwxr-xr-x    2 root     root             0 Nov 14 23:19 kmalloc_node
drwxr-xr-x    2 root     root             0 Nov 14 23:19 kmem_cache_alloc
drwxr-xr-x    2 root     root             0 Nov 14 23:19 kmem_cache_alloc_node
drwxr-xr-x    2 root     root             0 Nov 14 23:19 kmem_cache_free
drwxr-xr-x    2 root     root             0 Nov 14 23:19 mm_page_alloc
drwxr-xr-x    2 root     root             0 Nov 14 23:19 mm_page_alloc_extfrag
drwxr-xr-x    2 root     root             0 Nov 14 23:19 mm_page_alloc_zone_locked
drwxr-xr-x    2 root     root             0 Nov 14 23:19 mm_page_free
drwxr-xr-x    2 root     root             0 Nov 14 23:19 mm_page_free_batched
drwxr-xr-x    2 root     root             0 Nov 14 23:19 mm_page_pcpu_drain
```

Let\'s see what\'s inside the subdirectory for a specific tracepoint, in this case the one for kmalloc:

> 让我们看看子目录里面有什么特定的跟踪点，在这种情况下是 kmalloc 的：

```shell
root@sugarbay:/sys/kernel/debug/tracing/events/kmem# cd kmalloc
root@sugarbay:/sys/kernel/debug/tracing/events/kmem/kmalloc# ls -al
drwxr-xr-x    2 root     root             0 Nov 14 23:19 .
drwxr-xr-x   14 root     root             0 Nov 14 23:19 ..
-rw-r--r--    1 root     root             0 Nov 14 23:19 enable
-rw-r--r--    1 root     root             0 Nov 14 23:19 filter
-r--r--r--    1 root     root             0 Nov 14 23:19 format
-r--r--r--    1 root     root             0 Nov 14 23:19 id
```

The \'format\' file for the tracepoint describes the event in memory, which is used by the various tracing tools that now make use of these tracepoint to parse the event and make sense of it, along with a \'print fmt\' field that allows tools like ftrace to display the event as text. Here\'s what the format of the kmalloc event looks like:

> 文件'tracepoint'描述了内存中的事件，这些事件被各种跟踪工具使用，这些工具使用这些 tracepoint 来解析事件并弄清楚它，以及允许像 ftrace 这样的工具将事件显示为文本的'print fmt'字段。这里是 kmalloc 事件的格式：

```shell
root@sugarbay:/sys/kernel/debug/tracing/events/kmem/kmalloc# cat format
name: kmalloc
ID: 313
format:
        field:unsigned short common_type;    offset:0;   size:2; signed:0;
        field:unsigned char common_flags;    offset:2;   size:1; signed:0;
        field:unsigned char common_preempt_count;    offset:3;   size:1; signed:0;
        field:int common_pid;    offset:4;   size:4; signed:1;
        field:int common_padding;    offset:8;   size:4; signed:1;

        field:unsigned long call_site;   offset:16;  size:8; signed:0;
        field:const void * ptr;  offset:24;  size:8; signed:0;
        field:size_t bytes_req;  offset:32;  size:8; signed:0;
        field:size_t bytes_alloc;    offset:40;  size:8; signed:0;
        field:gfp_t gfp_flags;   offset:48;  size:4; signed:0;

print fmt: "call_site=%lx ptr=%p bytes_req=%zu bytes_alloc=%zu gfp_flags=%s", REC->call_site, REC->ptr, REC->bytes_req, REC->bytes_alloc,
(REC->gfp_flags) ? __print_flags(REC->gfp_flags, "|", {(unsigned long)(((( gfp_t)0x10u) | (( gfp_t)0x40u) | (( gfp_t)0x80u) | ((
gfp_t)0x20000u) | (( gfp_t)0x02u) | (( gfp_t)0x08u)) | (( gfp_t)0x4000u) | (( gfp_t)0x10000u) | (( gfp_t)0x1000u) | (( gfp_t)0x200u) | ((
gfp_t)0x400000u)), "GFP_TRANSHUGE"}, {(unsigned long)((( gfp_t)0x10u) | (( gfp_t)0x40u) | (( gfp_t)0x80u) | (( gfp_t)0x20000u) | ((
gfp_t)0x02u) | (( gfp_t)0x08u)), "GFP_HIGHUSER_MOVABLE"}, {(unsigned long)((( gfp_t)0x10u) | (( gfp_t)0x40u) | (( gfp_t)0x80u) | ((
gfp_t)0x20000u) | (( gfp_t)0x02u)), "GFP_HIGHUSER"}, {(unsigned long)((( gfp_t)0x10u) | (( gfp_t)0x40u) | (( gfp_t)0x80u) | ((
gfp_t)0x20000u)), "GFP_USER"}, {(unsigned long)((( gfp_t)0x10u) | (( gfp_t)0x40u) | (( gfp_t)0x80u) | (( gfp_t)0x80000u)), GFP_TEMPORARY"},
{(unsigned long)((( gfp_t)0x10u) | (( gfp_t)0x40u) | (( gfp_t)0x80u)), "GFP_KERNEL"}, {(unsigned long)((( gfp_t)0x10u) | (( gfp_t)0x40u)),
"GFP_NOFS"}, {(unsigned long)((( gfp_t)0x20u)), "GFP_ATOMIC"}, {(unsigned long)((( gfp_t)0x10u)), "GFP_NOIO"}, {(unsigned long)((
gfp_t)0x20u), "GFP_HIGH"}, {(unsigned long)(( gfp_t)0x10u), "GFP_WAIT"}, {(unsigned long)(( gfp_t)0x40u), "GFP_IO"}, {(unsigned long)((
gfp_t)0x100u), "GFP_COLD"}, {(unsigned long)(( gfp_t)0x200u), "GFP_NOWARN"}, {(unsigned long)(( gfp_t)0x400u), "GFP_REPEAT"}, {(unsigned
long)(( gfp_t)0x800u), "GFP_NOFAIL"}, {(unsigned long)(( gfp_t)0x1000u), "GFP_NORETRY"},      {(unsigned long)(( gfp_t)0x4000u), "GFP_COMP"},
{(unsigned long)(( gfp_t)0x8000u), "GFP_ZERO"}, {(unsigned long)(( gfp_t)0x10000u), "GFP_NOMEMALLOC"}, {(unsigned long)(( gfp_t)0x20000u),
"GFP_HARDWALL"}, {(unsigned long)(( gfp_t)0x40000u), "GFP_THISNODE"}, {(unsigned long)(( gfp_t)0x80000u), "GFP_RECLAIMABLE"}, {(unsigned
long)(( gfp_t)0x08u), "GFP_MOVABLE"}, {(unsigned long)(( gfp_t)0), "GFP_NOTRACK"}, {(unsigned long)(( gfp_t)0x400000u), "GFP_NO_KSWAPD"},
{(unsigned long)(( gfp_t)0x800000u), "GFP_OTHER_NODE"} ) : "GFP_NOWAIT"
```

The \'enable\' file in the tracepoint directory is what allows the user (or tools such as trace-cmd) to actually turn the tracepoint on and off. When enabled, the corresponding tracepoint will start appearing in the ftrace \'trace\' file described previously. For example, this turns on the kmalloc tracepoint:

> tracepoint 目录中的 'enable' 文件允许用户（或像 trace-cmd 这样的工具）实际开启和关闭 tracepoint。当开启时，相应的 tracepoint 将开始出现在先前描述的 ftrace 'trace' 文件中。例如，这会开启 kmalloc tracepoint：

```shell
root@sugarbay:/sys/kernel/debug/tracing/events/kmem/kmalloc# echo 1 > enable
```

At the moment, we\'re not interested in the function tracer or some other tracer that might be in effect, so we first turn it off, but if we do that, we still need to turn tracing on in order to see the events in the output buffer:

> 目前，我们不感兴趣功能跟踪器或其他可能生效的跟踪器，因此我们首先将其关闭，但如果我们这样做，仍然需要打开跟踪以便在输出缓冲区中查看事件：

```shell
root@sugarbay:/sys/kernel/debug/tracing# echo nop > current_tracer
root@sugarbay:/sys/kernel/debug/tracing# echo 1 > tracing_on
```

Now, if we look at the \'trace\' file, we see nothing but the kmalloc events we just turned on:

> 现在，如果我们看一下'trace'文件，我们只会看到我们刚才打开的 kmalloc 事件：

```shell
root@sugarbay:/sys/kernel/debug/tracing# cat trace | less
# tracer: nop
#
# entries-in-buffer/entries-written: 1897/1897   #P:8
#
#                              _-----=> irqs-off
#                             / _----=> need-resched
#                            | / _---=> hardirq/softirq
#                            || / _--=> preempt-depth
#                            ||| /     delay
#           TASK-PID   CPU#  ||||    TIMESTAMP  FUNCTION
#              | |       |   ||||       |         |
       dropbear-1465  [000] ...1 18154.620753: kmalloc: call_site=ffffffff816650d4 ptr=ffff8800729c3000 bytes_req=2048 bytes_alloc=2048 gfp_flags=GFP_KERNEL
         <idle>-0     [000] ..s3 18154.621640: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d555800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
         <idle>-0     [000] ..s3 18154.621656: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d555800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
matchbox-termin-1361  [001] ...1 18154.755472: kmalloc: call_site=ffffffff81614050 ptr=ffff88006d5f0e00 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_KERNEL|GFP_REPEAT
           Xorg-1264  [002] ...1 18154.755581: kmalloc: call_site=ffffffff8141abe8 ptr=ffff8800734f4cc0 bytes_req=168 bytes_alloc=192 gfp_flags=GFP_KERNEL|GFP_NOWARN|GFP_NORETRY
           Xorg-1264  [002] ...1 18154.755583: kmalloc: call_site=ffffffff814192a3 ptr=ffff88001f822520 bytes_req=24 bytes_alloc=32 gfp_flags=GFP_KERNEL|GFP_ZERO
           Xorg-1264  [002] ...1 18154.755589: kmalloc: call_site=ffffffff81419edb ptr=ffff8800721a2f00 bytes_req=64 bytes_alloc=64 gfp_flags=GFP_KERNEL|GFP_ZERO
matchbox-termin-1361  [001] ...1 18155.354594: kmalloc: call_site=ffffffff81614050 ptr=ffff88006db35400 bytes_req=576 bytes_alloc=1024 gfp_flags=GFP_KERNEL|GFP_REPEAT
           Xorg-1264  [002] ...1 18155.354703: kmalloc: call_site=ffffffff8141abe8 ptr=ffff8800734f4cc0 bytes_req=168 bytes_alloc=192 gfp_flags=GFP_KERNEL|GFP_NOWARN|GFP_NORETRY
           Xorg-1264  [002] ...1 18155.354705: kmalloc: call_site=ffffffff814192a3 ptr=ffff88001f822520 bytes_req=24 bytes_alloc=32 gfp_flags=GFP_KERNEL|GFP_ZERO
           Xorg-1264  [002] ...1 18155.354711: kmalloc: call_site=ffffffff81419edb ptr=ffff8800721a2f00 bytes_req=64 bytes_alloc=64 gfp_flags=GFP_KERNEL|GFP_ZERO
         <idle>-0     [000] ..s3 18155.673319: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d555800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
       dropbear-1465  [000] ...1 18155.673525: kmalloc: call_site=ffffffff816650d4 ptr=ffff8800729c3000 bytes_req=2048 bytes_alloc=2048 gfp_flags=GFP_KERNEL
         <idle>-0     [000] ..s3 18155.674821: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d554800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
         <idle>-0     [000] ..s3 18155.793014: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d554800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
       dropbear-1465  [000] ...1 18155.793219: kmalloc: call_site=ffffffff816650d4 ptr=ffff8800729c3000 bytes_req=2048 bytes_alloc=2048 gfp_flags=GFP_KERNEL
         <idle>-0     [000] ..s3 18155.794147: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d555800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
         <idle>-0     [000] ..s3 18155.936705: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d555800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
       dropbear-1465  [000] ...1 18155.936910: kmalloc: call_site=ffffffff816650d4 ptr=ffff8800729c3000 bytes_req=2048 bytes_alloc=2048 gfp_flags=GFP_KERNEL
         <idle>-0     [000] ..s3 18155.937869: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d554800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
matchbox-termin-1361  [001] ...1 18155.953667: kmalloc: call_site=ffffffff81614050 ptr=ffff88006d5f2000 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_KERNEL|GFP_REPEAT
           Xorg-1264  [002] ...1 18155.953775: kmalloc: call_site=ffffffff8141abe8 ptr=ffff8800734f4cc0 bytes_req=168 bytes_alloc=192 gfp_flags=GFP_KERNEL|GFP_NOWARN|GFP_NORETRY
           Xorg-1264  [002] ...1 18155.953777: kmalloc: call_site=ffffffff814192a3 ptr=ffff88001f822520 bytes_req=24 bytes_alloc=32 gfp_flags=GFP_KERNEL|GFP_ZERO
           Xorg-1264  [002] ...1 18155.953783: kmalloc: call_site=ffffffff81419edb ptr=ffff8800721a2f00 bytes_req=64 bytes_alloc=64 gfp_flags=GFP_KERNEL|GFP_ZERO
         <idle>-0     [000] ..s3 18156.176053: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d554800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
       dropbear-1465  [000] ...1 18156.176257: kmalloc: call_site=ffffffff816650d4 ptr=ffff8800729c3000 bytes_req=2048 bytes_alloc=2048 gfp_flags=GFP_KERNEL
         <idle>-0     [000] ..s3 18156.177717: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d555800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
         <idle>-0     [000] ..s3 18156.399229: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d555800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
       dropbear-1465  [000] ...1 18156.399434: kmalloc: call_site=ffffffff816650d4 ptr=ffff8800729c3000 bytes_http://rostedt.homelinux.com/kernelshark/req=2048 bytes_alloc=2048 gfp_flags=GFP_KERNEL
         <idle>-0     [000] ..s3 18156.400660: kmalloc: call_site=ffffffff81619b36 ptr=ffff88006d554800 bytes_req=512 bytes_alloc=512 gfp_flags=GFP_ATOMIC
matchbox-termin-1361  [001] ...1 18156.552800: kmalloc: call_site=ffffffff81614050 ptr=ffff88006db34800 bytes_req=576 bytes_alloc=1024 gfp_flags=GFP_KERNEL|GFP_REPEAT
```

To again disable the kmalloc event, we need to send 0 to the enable file:

> 要再次禁用 kmalloc 事件，我们需要向 enable 文件发送 0。

```shell
root@sugarbay:/sys/kernel/debug/tracing/events/kmem/kmalloc# echo 0 > enable
```

You can enable any number of events or complete subsystems (by using the \'enable\' file in the subsystem directory) and get an arbitrarily fine-grained idea of what\'s going on in the system by enabling as many of the appropriate tracepoints as applicable.

> 你可以通过在子系统目录中使用“enable”文件来启用任意数量的事件或完整的子系统，并通过启用尽可能多的适当的跟踪点来获得对系统中发生的事情的任意精细的了解。

A number of the tools described in this HOWTO do just that, including trace-cmd and kernelshark in the next section.

> 本教程中描述的许多工具都可以做到这一点，其中包括下一节中的 trace-cmd 和 kernelshark。

::: admonition

Tying it Together

> 把它拼接在一起

These tracepoints and their representation are used not only by ftrace, but by many of the other tools covered in this document and they form a central point of integration for the various tracers available in Linux. They form a central part of the instrumentation for the following tools: perf, lttng, ftrace, blktrace and SystemTap

> 这些跟踪点及其表示不仅被 ftrace 使用，而且被本文档中涵盖的其他许多工具所使用，它们构成了 Linux 中各种跟踪器的集成中心。它们是以下工具的一个核心构件：perf、lttng、ftrace、blktrace 和 SystemTap。
> :::

::: admonition

Tying it Together

> 把它拼在一起

Eventually all the special-purpose tracers currently available in /sys/kernel/debug/tracing will be removed and replaced with equivalent tracers based on the \'trace events\' subsystem.

> 最终，所有当前可在/sys/kernel/debug/tracing 中使用的专用跟踪器都将被移除，并用基于'trace events'子系统的等效跟踪器代替。
> :::

## trace-cmd/kernelshark

trace-cmd is essentially an extensive command-line \'wrapper\' interface that hides the details of all the individual files in /sys/kernel/debug/tracing, allowing users to specify specific particular events within the /sys/kernel/debug/tracing/events/ subdirectory and to collect traces and avoid having to deal with those details directly.

> trace-cmd 基本上是一个广泛的命令行'包装'界面，它隐藏了/sys/kernel/debug/tracing 中所有单独文件的细节，允许用户指定/sys/kernel/debug/tracing/events/子目录中的特定事件，并收集跟踪信息，避免直接处理这些细节。

As yet another layer on top of that, kernelshark provides a GUI that allows users to start and stop traces and specify sets of events using an intuitive interface, and view the output as both trace events and as a per-CPU graphical display. It directly uses \'trace-cmd\' as the plumbing that accomplishes all that underneath the covers (and actually displays the trace-cmd command it uses, as we\'ll see).

> 作为另一层在此之上，Kernelshark 提供了一个 GUI，允许用户使用直观的界面启动和停止跟踪，并指定事件集，并将输出显示为跟踪事件和每个 CPU 的图形显示。它直接使用“trace-cmd”作为实现所有这些背后的管道（实际上会显示它使用的 trace-cmd 命令，正如我们将看到的）。

To start a trace using kernelshark, first start kernelshark:

> 要使用 Kernelshark 开始跟踪，首先启动 Kernelshark：

```shell
root@sugarbay:~# kernelshark
```

Then bring up the \'Capture\' dialog by choosing from the kernelshark menu:

> 然后从 KernelShark 菜单中选择，弹出'捕获'对话框：

```shell
Capture | Record
```

That will display the following dialog, which allows you to choose one or more events (or even one or more complete subsystems) to trace:

> 以下对话框将会显示，允许您选择一个或多个事件（甚至一个或多个完整的子系统）进行跟踪：

![image](figures/kernelshark-choose-events.png){.align-center width="70.0%"}

Note that these are exactly the same sets of events described in the previous trace events subsystem section, and in fact is where trace-cmd gets them for kernelshark.

In the above screenshot, we\'ve decided to explore the graphics subsystem a bit and so have chosen to trace all the tracepoints contained within the \'i915\' and \'drm\' subsystems.

> 在上面的截图中，我们决定探索一下图形子系统，因此我们选择跟踪包含在'i915'和'drm'子系统中的所有跟踪点。

After doing that, we can start and stop the trace using the \'Run\' and \'Stop\' button on the lower right corner of the dialog (the same button will turn into the \'Stop\' button after the trace has started):

> 在做完这件事之后，我们可以使用对话框右下角的“运行”和“停止”按钮来启动和停止跟踪（跟踪启动后，同一个按钮会变成“停止”按钮）：

![image](figures/kernelshark-output-display.png){.align-center width="70.0%"}

Notice that the right-hand pane shows the exact trace-cmd command-line that\'s used to run the trace, along with the results of the trace-cmd run.

> 注意，右侧窗格显示了用于运行跟踪的确切的 trace-cmd 命令行，以及 trace-cmd 运行的结果。

Once the \'Stop\' button is pressed, the graphical view magically fills up with a colorful per-cpu display of the trace data, along with the detailed event listing below that:

> 一旦按下“停止”按钮，图形视图就会神奇地填充上一个丰富多彩的每个 CPU 的跟踪数据显示，以及下面的详细事件列表：

![image](figures/kernelshark-i915-display.png){.align-center width="70.0%"}

Here\'s another example, this time a display resulting from tracing \'all events\':

> 这是另一个例子，这次是跟踪“所有事件”后的显示：

![image](figures/kernelshark-all.png){.align-center width="70.0%"}

The tool is pretty self-explanatory, but for more detailed information on navigating through the data, see the [kernelshark website](https://kernelshark.org/Documentation.html).

> 工具很容易理解，但要了解更多有关如何浏览数据的详细信息，请参阅 [kernelshark 网站](https://kernelshark.org/Documentation.html)。

## ftrace Documentation

The documentation for ftrace can be found in the kernel Documentation directory:

> 内核文档目录中可以找到 ftrace 的文档：

```shell
Documentation/trace/ftrace.txt
```

The documentation for the trace event subsystem can also be found in the kernel Documentation directory:

> 文档中关于跟踪事件子系统的信息也可以在内核文档目录中找到：

```shell
Documentation/trace/events.txt
```

There is a nice series of articles on using ftrace and trace-cmd at LWN:

> 在 LWN 上有一系列关于使用 ftrace 和 trace-cmd 的文章，很不错：

- [Debugging the kernel using Ftrace - part 1](https://lwn.net/Articles/365835/)
- [Debugging the kernel using Ftrace - part 2](https://lwn.net/Articles/366796/)
- [Secrets of the Ftrace function tracer](https://lwn.net/Articles/370423/)
- [trace-cmd: A front-end for Ftrace](https://lwn.net/Articles/410200/)

See also [KernelShark\'s documentation](https://kernelshark.org/Documentation.html) for further usage details.

> 另见 [KernelShark 的文档](https://kernelshark.org/Documentation.html)以获取更多使用细节。

An amusing yet useful README (a tracing mini-HOWTO) can be found in `/sys/kernel/debug/tracing/README`.

> 在/sys/kernel/debug/tracing/README 中可以找到一个有趣又有用的 README（一个跟踪小型 HOWTO）。

# systemtap

SystemTap is a system-wide script-based tracing and profiling tool.

> SystemTap 是一个全系统范围内的基于脚本的跟踪和分析工具。

SystemTap scripts are C-like programs that are executed in the kernel to gather/print/aggregate data extracted from the context they end up being invoked under.

> 系统 Tap 脚本是一种类似 C 语言的程序，它们在内核中执行，从它们最终被调用的上下文中收集/打印/聚合数据。

For example, this probe from the [SystemTap tutorial](https://sourceware.org/systemtap/tutorial/) simply prints a line every time any process on the system open()s a file. For each line, it prints the executable name of the program that opened the file, along with its PID, and the name of the file it opened (or tried to open), which it extracts from the open syscall\'s argstr.

> 例如，这个来自 [SystemTap 教程](https://sourceware.org/systemtap/tutorial/)的探针每次系统上的任何进程执行 open()操作时，都会打印一行。每行都会打印出打开文件的可执行程序的名称以及它的 PID，以及它打开（或尝试打开）的文件名，它从 open 系统调用的 argstr 中提取出来。

```none
probe syscall.open
{
        printf ("%s(%d) open (%s)\n", execname(), pid(), argstr)
}

probe timer.ms(4000) # after 4 seconds
{
        exit ()
}
```

Normally, to execute this probe, you\'d simply install systemtap on the system you want to probe, and directly run the probe on that system e.g. assuming the name of the file containing the above text is trace_open.stp:

> 一般来说，要执行此探针，您只需在要探测的系统上安装 systemtap，并直接在该系统上运行探针，例如，假设包含上述文本的文件名为 trace_open.stp：

```shell
# stap trace_open.stp
```

What systemtap does under the covers to run this probe is 1) parse and convert the probe to an equivalent \'C\' form, 2) compile the \'C\' form into a kernel module, 3) insert the module into the kernel, which arms it, and 4) collect the data generated by the probe and display it to the user.

> 系统针在运行此探针的时候会做的事情是 1) 将探针转换为等价的'C'形式，2) 将'C'形式编译成内核模块，3) 将模块插入内核，以激活它，4) 收集由探针生成的数据并将其显示给用户。

In order to accomplish steps 1 and 2, the \'stap\' program needs access to the kernel build system that produced the kernel that the probed system is running. In the case of a typical embedded system (the \'target\'), the kernel build system unfortunately isn\'t typically part of the image running on the target. It is normally available on the \'host\' system that produced the target image however; in such cases, steps 1 and 2 are executed on the host system, and steps 3 and 4 are executed on the target system, using only the systemtap \'runtime\'.

> 为了完成步骤 1 和 2，'stap'程序需要访问生成被探测系统正在运行的内核的内核构建系统。在典型的嵌入式系统（'target'）的情况下，不幸的是内核构建系统通常不是运行在目标上的图像的一部分。它通常可以在生成目标图像的'host'系统上获得；在这种情况下，步骤 1 和 2 在主机系统上执行，步骤 3 和 4 使用系统探测'runtime'在目标系统上执行。

The systemtap support in Yocto assumes that only steps 3 and 4 are run on the target; it is possible to do everything on the target, but this section assumes only the typical embedded use-case.

> 系统探针在 Yocto 中的支持假定只在目标上运行步骤 3 和 4；可以在目标上完成所有操作，但本节假定只是典型的嵌入式用例。

So basically what you need to do in order to run a systemtap script on the target is to 1) on the host system, compile the probe into a kernel module that makes sense to the target, 2) copy the module onto the target system and 3) insert the module into the target kernel, which arms it, and 4) collect the data generated by the probe and display it to the user.

> 那么基本上你需要做的是要在目标上运行 systemtap 脚本，1）在主机系统上编译探针到一个对目标有意义的内核模块，2）将模块复制到目标系统上，3）将模块插入目标内核，以启动它，4）收集由探针生成的数据并将其显示给用户。

## systemtap Setup

Those are a lot of steps and a lot of details, but fortunately Yocto includes a script called \'crosstap\' that will take care of those details, allowing you to simply execute a systemtap script on the remote target, with arguments if necessary.

> 这些步骤和细节很多，但幸运的是 Yocto 包括了一个叫做'crosstap'的脚本，它可以处理这些细节，让你可以简单地在远程目标上执行系统拓扑脚本，如果需要的话还可以传递参数。

In order to do this from a remote host, however, you need to have access to the build for the image you booted. The \'crosstap\' script provides details on how to do this if you run the script on the host without having done a build:

> 要从远程主机执行此操作，您需要访问启动的映像的构建。如果您在未进行构建的情况下在主机上运行“crosstap”脚本，则该脚本会提供有关如何执行此操作的详细信息。

```shell
$ crosstap root@192.168.1.88 trace_open.stp

Error: No target kernel build found.
Did you forget to create a local build of your image?

'crosstap' requires a local sdk build of the target system
(or a build that includes 'tools-profile') in order to build
kernel modules that can probe the target system.

Practically speaking, that means you need to do the following:
 - If you're running a pre-built image, download the release
   and/or BSP tarballs used to build the image.
 - If you're working from git sources, just clone the metadata
   and BSP layers needed to build the image you'll be booting.
 - Make sure you're properly set up to build a new image (see
   the BSP README and/or the widely available basic documentation
   that discusses how to build images).
 - Build an -sdk version of the image e.g.:
     $ bitbake core-image-sato-sdk
 OR
 - Build a non-sdk image but include the profiling tools:
     [ edit local.conf and add 'tools-profile' to the end of
       the EXTRA_IMAGE_FEATURES variable ]
     $ bitbake core-image-sato

Once you've build the image on the host system, you're ready to
boot it (or the equivalent pre-built image) and use 'crosstap'
to probe it (you need to source the environment as usual first):

   $ source oe-init-build-env
   $ cd ~/my/systemtap/scripts
   $ crosstap root@192.168.1.xxx myscript.stp
```

::: note
::: title
Note
:::

SystemTap, which uses \'crosstap\', assumes you can establish an ssh connection to the remote target. Please refer to the crosstap wiki page for details on verifying ssh connections at . Also, the ability to ssh into the target system is not enabled by default in \*-minimal images.

> 系统钉（SystemTap）使用“crosstap”，假设您可以建立到远程目标的 ssh 连接。有关验证 ssh 连接的详细信息，请参阅 crosstap wiki 页面。此外，*-minimal 图像默认情况下不允许 ssh 进入目标系统。
> :::

So essentially what you need to do is build an SDK image or image with \'tools-profile\' as detailed in the \"`profile-manual/intro:General Setup`{.interpreted-text role="ref"}\" section of this manual, and boot the resulting target image.

> 所以基本上你需要做的就是按照本手册中“profile-manual/intro：General Setup”部分所述的建立一个 SDK 图像或者带有“tools-profile”的图像，并启动最终的目标图像。

::: note
::: title
Note
:::

If you have a `Build Directory`{.interpreted-text role="term"} containing multiple machines, you need to have the `MACHINE`{.interpreted-text role="term"} you\'re connecting to selected in local.conf, and the kernel in that machine\'s `Build Directory`{.interpreted-text role="term"} must match the kernel on the booted system exactly, or you\'ll get the above \'crosstap\' message when you try to invoke a script.

> 如果您有一个包含多台机器的 `构建目录`，您需要在 local.conf 中选择您要连接的 `MACHINE`，该机器的 `构建目录` 中的内核必须与启动系统上的内核完全匹配，否则在尝试调用脚本时，您将获得上面的'crosstap'消息。
> :::

## Running a Script on a Target

Once you\'ve done that, you should be able to run a systemtap script on the target:

> 一旦你完成了，你应该能够在目标上运行系统脚本：

```shell
$ cd /path/to/yocto
$ source oe-init-build-env

### Shell environment set up for builds. ###

You can now run 'bitbake <target>'

Common targets are:
         core-image-minimal
         core-image-sato
         meta-toolchain
         meta-ide-support

You can also run generated QEMU images with a command like 'runqemu qemux86-64'
```

Once you\'ve done that, you can cd to whatever directory contains your scripts and use \'crosstap\' to run the script:

> 一旦你做完了，你可以 cd 到包含你的脚本的任何目录，并使用 'crosstap' 来运行脚本：

```shell
$ cd /path/to/my/systemap/script
$ crosstap root@192.168.7.2 trace_open.stp
```

If you get an error connecting to the target e.g.:

> 如果您连接目标时出错，例如：

```shell
$ crosstap root@192.168.7.2 trace_open.stp
error establishing ssh connection on remote 'root@192.168.7.2'
```

Try ssh\'ing to the target and see what happens:

> 尝试使用 SSH 连接到目标，看看会发生什么？

```shell
$ ssh root@192.168.7.2
```

A lot of the time, connection problems are due specifying a wrong IP address or having a \'host key verification error\'.

> 很多时候，连接问题是由于指定错误的 IP 地址或遇到'主机密钥验证错误'引起的。

If everything worked as planned, you should see something like this (enter the password when prompted, or press enter if it\'s set up to use no password):

> 如果一切按计划运行，你应该会看到类似的情况（提示时输入密码，或者如果设置不需要密码，则按回车键）：

```none
$ crosstap root@192.168.7.2 trace_open.stp
root@192.168.7.2's password:
matchbox-termin(1036) open ("/tmp/vte3FS2LW", O_RDWR|O_CREAT|O_EXCL|O_LARGEFILE, 0600)
matchbox-termin(1036) open ("/tmp/vteJMC7LW", O_RDWR|O_CREAT|O_EXCL|O_LARGEFILE, 0600)
```

## systemtap Documentation

The SystemTap language reference can be found here: [SystemTap Language Reference](https://sourceware.org/systemtap/langref/)

> 系统 Tap 语言参考文档可以在这里找到：[SystemTap 语言参考](https://sourceware.org/systemtap/langref/)

Links to other SystemTap documents, tutorials, and examples can be found here: [SystemTap documentation page](https://sourceware.org/systemtap/documentation.html)

> 在这里可以找到其他 SystemTap 文档、教程和示例的链接：[SystemTap 文档页面](https://sourceware.org/systemtap/documentation.html)

# Sysprof

Sysprof is a very easy to use system-wide profiler that consists of a single window with three panes and a few buttons which allow you to start, stop, and view the profile from one place.

> Sysprof 是一款非常容易使用的系统级分析器，只有一个窗口，包含三个面板和几个按钮，可以让您从一个地方启动、停止和查看配置文件。

## Sysprof Setup

For this section, we\'ll assume you\'ve already performed the basic setup outlined in the \"`profile-manual/intro:General Setup`{.interpreted-text role="ref"}\" section.

> 对于这一部分，我们假设您已经在“profile-manual/intro：General Setup”部分中执行了基本设置。

Sysprof is a GUI-based application that runs on the target system. For the rest of this document we assume you\'ve ssh\'ed to the host and will be running Sysprof on the target (you can use the \'-X\' option to ssh and have the Sysprof GUI run on the target but display remotely on the host if you want).

> Sysprof 是一个基于 GUI 的应用程序，可以在目标系统上运行。 在本文档的其余部分中，我们假设您已经 ssh 到主机，并将在目标上运行 Sysprof（您可以使用“-X”选项 ssh，并在目标上运行 Sysprof GUI，但在主机上远程显示）。

## Basic Sysprof Usage

To start profiling the system, you simply press the \'Start\' button. To stop profiling and to start viewing the profile data in one easy step, press the \'Profile\' button.

> 开始对系统进行分析，只需按下“开始”按钮即可。要停止分析并以一个简单的步骤开始查看分析数据，请按下“分析”按钮。

Once you\'ve pressed the profile button, the three panes will fill up with profiling data:

> 一旦您按下配置按钮，三个窗格将填充配置数据：

![image](figures/sysprof-copy-to-user.png){.align-center width="70.0%"}

The left pane shows a list of functions and processes. Selecting one of those expands that function in the right pane, showing all its callees. Note that this caller-oriented display is essentially the inverse of perf\'s default callee-oriented callchain display.

> 左侧窗格显示了一个函数和进程列表。选择其中一个会在右侧窗格中展开该函数，显示所有被调用者。请注意，这种以调用者为导向的显示方式实际上与 perf 的默认以被调用者为导向的调用链显示方式是相反的。

In the screenshot above, we\'re focusing on `__copy_to_user_ll()` and looking up the callchain we can see that one of the callers of `__copy_to_user_ll` is sys_read() and the complete callpath between them. Notice that this is essentially a portion of the same information we saw in the perf display shown in the perf section of this page.

> 在上面的截图中，我们关注 `__copy_to_user_ll（）`，查看调用链，我们可以看到 `__copy_to_user_ll` 的调用者之一是 sys_read（），以及它们之间的完整调用路径。请注意，这实际上是本页 perf 部分所展示的 perf 显示中的一部分信息。

![image](figures/sysprof-copy-from-user.png){.align-center width="70.0%"}

Similarly, the above is a snapshot of the Sysprof display of a copy-from-user callchain.

> 同样，上面是一个从用户复制调用链的 Sysprof 显示的快照。

Finally, looking at the third Sysprof pane in the lower left, we can see a list of all the callers of a particular function selected in the top left pane. In this case, the lower pane is showing all the callers of `__mark_inode_dirty`:

> 最后，看一下左下角的第三个 Sysprof 面板，我们可以看到左上角选中的特定函数的所有调用者的列表。在这种情况下，下面的面板显示了 `__mark_inode_dirty` 的所有调用者：

![image](figures/sysprof-callers.png){.align-center width="70.0%"}

Double-clicking on one of those functions will in turn change the focus to the selected function, and so on.

> 双击其中一个功能会将焦点切换到所选功能，依此类推。

::: admonition

Tying it Together

> 把它拼在一起

If you like sysprof\'s \'caller-oriented\' display, you may be able to approximate it in other tools as well. For example, \'perf report\' has the -g (\--call-graph) option that you can experiment with; one of the options is \'caller\' for an inverted caller-based callgraph display.

> 如果你喜欢 sysprof 的“以调用者为中心”的显示，你也可以在其他工具中近似实现它。例如，'perf report'有-g（\ --call-graph）选项，你可以尝试一下；其中一个选项是'caller'，用于基于调用者的倒置调用图显示。
> :::

## Sysprof Documentation

There doesn\'t seem to be any documentation for Sysprof, but maybe that\'s because it\'s pretty self-explanatory. The Sysprof website, however, is here: [Sysprof, System-wide Performance Profiler for Linux](http://sysprof.com/)

> 似乎没有任何关于 Sysprof 的文档，但也许这是因为它很容易理解。不过，Sysprof 的网站在这里：[Sysprof，Linux 系统性能分析器](http://sysprof.com/)

# LTTng (Linux Trace Toolkit, next generation)

## LTTng Setup

For this section, we\'ll assume you\'ve already performed the basic setup outlined in the \"`profile-manual/intro:General Setup`{.interpreted-text role="ref"}\" section. LTTng is run on the target system by ssh\'ing to it.

> 对于本节，我们假设您已经执行了“ profile-manual / intro：General Setup”部分中概述的基本设置。通过 ssh 到目标系统上运行 LTTng。

## Collecting and Viewing Traces

Once you\'ve applied the above commits and built and booted your image (you need to build the core-image-sato-sdk image or use one of the other methods described in the \"`profile-manual/intro:General Setup`{.interpreted-text role="ref"}\" section), you\'re ready to start tracing.

> 一旦你应用了以上提交并建立并启动了你的镜像（你需要建立 core-image-sato-sdk 镜像或使用在“profile-manual/intro：General Setup”部分描述的其他方法之一），你就准备好开始跟踪了。

### Collecting and viewing a trace on the target (inside a shell)

First, from the host, ssh to the target:

> 首先，从主机上，使用 ssh 连接到目标。

```shell
$ ssh -l root 192.168.1.47
The authenticity of host '192.168.1.47 (192.168.1.47)' can't be established.
RSA key fingerprint is 23:bd:c8:b1:a8:71:52:00:ee:00:4f:64:9e:10:b9:7e.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.1.47' (RSA) to the list of known hosts.
root@192.168.1.47's password:
```

Once on the target, use these steps to create a trace:

> 一旦到达目标，请使用以下步骤创建跟踪：

```shell
root@crownbay:~# lttng create
Spawning a session daemon
Session auto-20121015-232120 created.
Traces will be written in /home/root/lttng-traces/auto-20121015-232120
```

Enable the events you want to trace (in this case all kernel events):

> 启用你想跟踪的事件（在这种情况下是所有内核事件）：

```shell
root@crownbay:~# lttng enable-event --kernel --all
All kernel events are enabled in channel channel0
```

Start the trace:

> 开始跟踪：

```shell
root@crownbay:~# lttng start
Tracing started for session auto-20121015-232120
```

And then stop the trace after awhile or after running a particular workload that you want to trace:

> 然后在运行了你想追踪的特定工作负载之后，停止追踪一段时间。

```shell
root@crownbay:~# lttng stop
Tracing stopped for session auto-20121015-232120
```

You can now view the trace in text form on the target:

> 现在您可以在目标上以文本形式查看跟踪信息。

```shell
root@crownbay:~# lttng view
[23:21:56.989270399] (+?.?????????) sys_geteuid: { 1 }, { }
[23:21:56.989278081] (+0.000007682) exit_syscall: { 1 }, { ret = 0 }
[23:21:56.989286043] (+0.000007962) sys_pipe: { 1 }, { fildes = 0xB77B9E8C }
[23:21:56.989321802] (+0.000035759) exit_syscall: { 1 }, { ret = 0 }
[23:21:56.989329345] (+0.000007543) sys_mmap_pgoff: { 1 }, { addr = 0x0, len = 10485760, prot = 3, flags = 131362, fd = 4294967295, pgoff = 0 }
[23:21:56.989351694] (+0.000022349) exit_syscall: { 1 }, { ret = -1247805440 }
[23:21:56.989432989] (+0.000081295) sys_clone: { 1 }, { clone_flags = 0x411, newsp = 0xB5EFFFE4, parent_tid = 0xFFFFFFFF, child_tid = 0x0 }
[23:21:56.989477129] (+0.000044140) sched_stat_runtime: { 1 }, { comm = "lttng-consumerd", tid = 1193, runtime = 681660, vruntime = 43367983388 }
[23:21:56.989486697] (+0.000009568) sched_migrate_task: { 1 }, { comm = "lttng-consumerd", tid = 1193, prio = 20, orig_cpu = 1, dest_cpu = 1 }
[23:21:56.989508418] (+0.000021721) hrtimer_init: { 1 }, { hrtimer = 3970832076, clockid = 1, mode = 1 }
[23:21:56.989770462] (+0.000262044) hrtimer_cancel: { 1 }, { hrtimer = 3993865440 }
[23:21:56.989771580] (+0.000001118) hrtimer_cancel: { 0 }, { hrtimer = 3993812192 }
[23:21:56.989776957] (+0.000005377) hrtimer_expire_entry: { 1 }, { hrtimer = 3993865440, now = 79815980007057, function = 3238465232 }
[23:21:56.989778145] (+0.000001188) hrtimer_expire_entry: { 0 }, { hrtimer = 3993812192, now = 79815980008174, function = 3238465232 }
[23:21:56.989791695] (+0.000013550) softirq_raise: { 1 }, { vec = 1 }
[23:21:56.989795396] (+0.000003701) softirq_raise: { 0 }, { vec = 1 }
[23:21:56.989800635] (+0.000005239) softirq_raise: { 0 }, { vec = 9 }
[23:21:56.989807130] (+0.000006495) sched_stat_runtime: { 1 }, { comm = "lttng-consumerd", tid = 1193, runtime = 330710, vruntime = 43368314098 }
[23:21:56.989809993] (+0.000002863) sched_stat_runtime: { 0 }, { comm = "lttng-sessiond", tid = 1181, runtime = 1015313, vruntime = 36976733240 }
[23:21:56.989818514] (+0.000008521) hrtimer_expire_exit: { 0 }, { hrtimer = 3993812192 }
[23:21:56.989819631] (+0.000001117) hrtimer_expire_exit: { 1 }, { hrtimer = 3993865440 }
[23:21:56.989821866] (+0.000002235) hrtimer_start: { 0 }, { hrtimer = 3993812192, function = 3238465232, expires = 79815981000000, softexpires = 79815981000000 }
[23:21:56.989822984] (+0.000001118) hrtimer_start: { 1 }, { hrtimer = 3993865440, function = 3238465232, expires = 79815981000000, softexpires = 79815981000000 }
[23:21:56.989832762] (+0.000009778) softirq_entry: { 1 }, { vec = 1 }
[23:21:56.989833879] (+0.000001117) softirq_entry: { 0 }, { vec = 1 }
[23:21:56.989838069] (+0.000004190) timer_cancel: { 1 }, { timer = 3993871956 }
[23:21:56.989839187] (+0.000001118) timer_cancel: { 0 }, { timer = 3993818708 }
[23:21:56.989841492] (+0.000002305) timer_expire_entry: { 1 }, { timer = 3993871956, now = 79515980, function = 3238277552 }
[23:21:56.989842819] (+0.000001327) timer_expire_entry: { 0 }, { timer = 3993818708, now = 79515980, function = 3238277552 }
[23:21:56.989854831] (+0.000012012) sched_stat_runtime: { 1 }, { comm = "lttng-consumerd", tid = 1193, runtime = 49237, vruntime = 43368363335 }
[23:21:56.989855949] (+0.000001118) sched_stat_runtime: { 0 }, { comm = "lttng-sessiond", tid = 1181, runtime = 45121, vruntime = 36976778361 }
[23:21:56.989861257] (+0.000005308) sched_stat_sleep: { 1 }, { comm = "kworker/1:1", tid = 21, delay = 9451318 }
[23:21:56.989862374] (+0.000001117) sched_stat_sleep: { 0 }, { comm = "kworker/0:0", tid = 4, delay = 9958820 }
[23:21:56.989868241] (+0.000005867) sched_wakeup: { 0 }, { comm = "kworker/0:0", tid = 4, prio = 120, success = 1, target_cpu = 0 }
[23:21:56.989869358] (+0.000001117) sched_wakeup: { 1 }, { comm = "kworker/1:1", tid = 21, prio = 120, success = 1, target_cpu = 1 }
[23:21:56.989877460] (+0.000008102) timer_expire_exit: { 1 }, { timer = 3993871956 }
[23:21:56.989878577] (+0.000001117) timer_expire_exit: { 0 }, { timer = 3993818708 }
.
.
.
```

You can now safely destroy the trace session (note that this doesn\'t delete the trace \-\-- it\'s still there in \~/lttng-traces):

> 你现在可以安全销毁跟踪会话（请注意，这并不会删除跟踪-它仍然存在于~/lttng-traces 中）：

```shell
root@crownbay:~# lttng destroy
Session auto-20121015-232120 destroyed at /home/root
```

Note that the trace is saved in a directory of the same name as returned by \'lttng create\', under the \~/lttng-traces directory (note that you can change this by supplying your own name to \'lttng create\'):

```shell
root@crownbay:~# ls -al ~/lttng-traces
drwxrwx---    3 root     root          1024 Oct 15 23:21 .
drwxr-xr-x    5 root     root          1024 Oct 15 23:57 ..
drwxrwx---    3 root     root          1024 Oct 15 23:21 auto-20121015-232120
```

### Collecting and viewing a userspace trace on the target (inside a shell)

For LTTng userspace tracing, you need to have a properly instrumented userspace program. For this example, we\'ll use the \'hello\' test program generated by the lttng-ust build.

> 对于 LTTng 用户空间跟踪，您需要有一个正确检测的用户空间程序。对于此示例，我们将使用 lttng-ust 构建生成的'hello'测试程序。

The \'hello\' test program isn\'t installed on the root filesystem by the lttng-ust build, so we need to copy it over manually. First cd into the build directory that contains the hello executable:

> 编译的 lttng-ust 没有将 `hello` 测试程序安装到根文件系统中，因此我们需要手动将它复制过去。首先进入包含 hello 可执行文件的构建目录：

```shell
$ cd build/tmp/work/core2_32-poky-linux/lttng-ust/2.0.5-r0/git/tests/hello/.libs
```

Copy that over to the target machine:

> 复制到目标机器上：

```shell
$ scp hello root@192.168.1.20:
```

You now have the instrumented lttng \'hello world\' test program on the target, ready to test.

> 你现在在目标上有了可以测试的带有仪表功能的 LTTNG 'hello world'测试程序了。

First, from the host, ssh to the target:

> 首先，从主机上 ssh 到目标：

```shell
$ ssh -l root 192.168.1.47
The authenticity of host '192.168.1.47 (192.168.1.47)' can't be established.
RSA key fingerprint is 23:bd:c8:b1:a8:71:52:00:ee:00:4f:64:9e:10:b9:7e.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.1.47' (RSA) to the list of known hosts.
root@192.168.1.47's password:
```

Once on the target, use these steps to create a trace:

> 一旦到达目标，请使用这些步骤创建跟踪：

```shell
root@crownbay:~# lttng create
Session auto-20190303-021943 created.
Traces will be written in /home/root/lttng-traces/auto-20190303-021943
```

Enable the events you want to trace (in this case all userspace events):

> 启用你想跟踪的事件（在这种情况下是所有用户空间事件）：

```shell
root@crownbay:~# lttng enable-event --userspace --all
All UST events are enabled in channel channel0
```

Start the trace:

> 开始跟踪

```shell
root@crownbay:~# lttng start
Tracing started for session auto-20190303-021943
```

Run the instrumented hello world program:

> 运行带有仪器的“你好，世界”程序：

```shell
root@crownbay:~# ./hello
Hello, World!
Tracing... done.
```

And then stop the trace after awhile or after running a particular workload that you want to trace:

> 然后在运行了你想要跟踪的特定工作负载之后，停止跟踪一段时间。

```shell
root@crownbay:~# lttng stop
Tracing stopped for session auto-20190303-021943
```

You can now view the trace in text form on the target:

> 现在您可以在目标上以文本形式查看跟踪信息。

```shell
root@crownbay:~# lttng view
[02:31:14.906146544] (+?.?????????) hello:1424 ust_tests_hello:tptest: { cpu_id = 1 }, { intfield = 0, intfield2 = 0x0, longfield = 0, netintfield = 0, netintfieldhex = 0x0, arrfield1 = [ [0] = 1, [1] = 2, [2] = 3 ], arrfield2 = "test", _seqfield1_length = 4, seqfield1 = [ [0] = 116, [1] = 101, [2] = 115, [3] = 116 ], _seqfield2_length = 4,  seqfield2 = "test", stringfield = "test", floatfield = 2222, doublefield = 2, boolfield = 1 }
[02:31:14.906170360] (+0.000023816) hello:1424 ust_tests_hello:tptest: { cpu_id = 1 }, { intfield = 1, intfield2 = 0x1, longfield = 1, netintfield = 1, netintfieldhex = 0x1, arrfield1 = [ [0] = 1, [1] = 2, [2] = 3 ], arrfield2 = "test", _seqfield1_length = 4, seqfield1 = [ [0] = 116, [1] = 101, [2] = 115, [3] = 116 ], _seqfield2_length = 4, seqfield2 = "test", stringfield = "test", floatfield = 2222, doublefield = 2, boolfield = 1 }
[02:31:14.906183140] (+0.000012780) hello:1424 ust_tests_hello:tptest: { cpu_id = 1 }, { intfield = 2, intfield2 = 0x2, longfield = 2, netintfield = 2, netintfieldhex = 0x2, arrfield1 = [ [0] = 1, [1] = 2, [2] = 3 ], arrfield2 = "test", _seqfield1_length = 4, seqfield1 = [ [0] = 116, [1] = 101, [2] = 115, [3] = 116 ], _seqfield2_length = 4, seqfield2 = "test", stringfield = "test", floatfield = 2222, doublefield = 2, boolfield = 1 }
[02:31:14.906194385] (+0.000011245) hello:1424 ust_tests_hello:tptest: { cpu_id = 1 }, { intfield = 3, intfield2 = 0x3, longfield = 3, netintfield = 3, netintfieldhex = 0x3, arrfield1 = [ [0] = 1, [1] = 2, [2] = 3 ], arrfield2 = "test", _seqfield1_length = 4, seqfield1 = [ [0] = 116, [1] = 101, [2] = 115, [3] = 116 ], _seqfield2_length = 4, seqfield2 = "test", stringfield = "test", floatfield = 2222, doublefield = 2, boolfield = 1 }
.
.
.
```

You can now safely destroy the trace session (note that this doesn\'t delete the trace \-\-- it\'s still there in \~/lttng-traces):

> 你现在可以安全地销毁跟踪会话（请注意，这不会删除跟踪-它仍然存在于~/lttng-traces 中）：

```shell
root@crownbay:~# lttng destroy
Session auto-20190303-021943 destroyed at /home/root
```

## LTTng Documentation

You can find the primary LTTng Documentation on the [LTTng Documentation](https://lttng.org/docs/) site. The documentation on this site is appropriate for intermediate to advanced software developers who are working in a Linux environment and are interested in efficient software tracing.

> 你可以在 [LTTng 文档](https://lttng.org/docs/)网站上找到主要的 LTTng 文档。本网站上的文档适合在 Linux 环境下工作，并有兴趣进行有效软件跟踪的中高级软件开发人员。

For information on LTTng in general, visit the [LTTng Project](https://lttng.org/lttng2.0) site. You can find a \"Getting Started\" link on this site that takes you to an LTTng Quick Start.

> 要了解 LTTng 的一般信息，请访问 [LTTng 项目](https://lttng.org/lttng2.0)网站。您可以在此网站上找到一个“开始”链接，可以带您进入 LTTng 快速入门。

# blktrace

blktrace is a tool for tracing and reporting low-level disk I/O. blktrace provides the tracing half of the equation; its output can be piped into the blkparse program, which renders the data in a human-readable form and does some basic analysis:

> blktrace 是一款用于跟踪和报告低级磁盘 I/O 的工具。blktrace 提供跟踪的一半方程；它的输出可以管道传送到 blkparse 程序，该程序以人类可读的形式呈现数据并进行一些基本分析。

## blktrace Setup

For this section, we\'ll assume you\'ve already performed the basic setup outlined in the \"`profile-manual/intro:General Setup`{.interpreted-text role="ref"}\" section.

> 对于本节，我们假设您已经完成了“profile-manual / intro：General Setup”部分中概述的基本设置。

blktrace is an application that runs on the target system. You can run the entire blktrace and blkparse pipeline on the target, or you can run blktrace in \'listen\' mode on the target and have blktrace and blkparse collect and analyze the data on the host (see the \"`profile-manual/usage:Using blktrace Remotely`{.interpreted-text role="ref"}\" section below). For the rest of this section we assume you\'ve ssh\'ed to the host and will be running blkrace on the target.

> blktrace 是一个运行在目标系统上的应用程序。您可以在目标上运行整个 blktrace 和 blkparse 管道，也可以在目标上以“监听”模式运行 blktrace，并让 blktrace 和 blkparse 在主机上收集和分析数据（请参阅下面的“profile-manual/usage：使用 blktrace 远程”部分）。对于本节的其余部分，我们假设您已经使用 SSH 登录到主机，并将在目标上运行 blkrace。

## Basic blktrace Usage

To record a trace, simply run the \'blktrace\' command, giving it the name of the block device you want to trace activity on:

> 要记录跟踪，只需运行'blktrace'命令，并给出要跟踪活动的块设备的名称：

```shell
root@crownbay:~# blktrace /dev/sdc
```

In another shell, execute a workload you want to trace. :

> 在另一个 shell 中，执行你想要跟踪的工作负载。

```shell
root@crownbay:/media/sdc# rm linux-2.6.19.2.tar.bz2; wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2; sync
Connecting to downloads.yoctoproject.org (140.211.169.59:80)
linux-2.6.19.2.tar.b 100% \|*******************************\| 41727k 0:00:00 ETA
```

Press Ctrl-C in the blktrace shell to stop the trace. It will display how many events were logged, along with the per-cpu file sizes (blktrace records traces in per-cpu kernel buffers and simply dumps them to userspace for blkparse to merge and sort later). :

> 按下 Ctrl-C 在 blktrace 终端停止跟踪。它会显示有多少事件被记录，以及每个 CPU 的文件大小（blktrace 在每个 CPU 的内核缓冲区中记录跟踪，并简单地将它们转储到用户空间以供 blkparse 合并和排序）。

```shell
^C=== sdc ===
 CPU  0:                 7082 events,      332 KiB data
 CPU  1:                 1578 events,       74 KiB data
 Total:                  8660 events (dropped 0),      406 KiB data
```

If you examine the files saved to disk, you see multiple files, one per CPU and with the device name as the first part of the filename:

> 如果检查保存到磁盘上的文件，你会看到多个文件，每个 CPU 一个，文件名的第一部分是设备名称：

```shell
root@crownbay:~# ls -al
drwxr-xr-x    6 root     root          1024 Oct 27 22:39 .
drwxr-sr-x    4 root     root          1024 Oct 26 18:24 ..
-rw-r--r--    1 root     root        339938 Oct 27 22:40 sdc.blktrace.0
-rw-r--r--    1 root     root         75753 Oct 27 22:40 sdc.blktrace.1
```

To view the trace events, simply invoke \'blkparse\' in the directory containing the trace files, giving it the device name that forms the first part of the filenames:

> 在包含跟踪文件的目录中简单地调用'blkparse'，给它构成文件名称第一部分的设备名称，以查看跟踪事件：

```shell
root@crownbay:~# blkparse sdc

 8,32   1        1     0.000000000  1225  Q  WS 3417048 + 8 [jbd2/sdc-8]
 8,32   1        2     0.000025213  1225  G  WS 3417048 + 8 [jbd2/sdc-8]
 8,32   1        3     0.000033384  1225  P   N [jbd2/sdc-8]
 8,32   1        4     0.000043301  1225  I  WS 3417048 + 8 [jbd2/sdc-8]
 8,32   1        0     0.000057270     0  m   N cfq1225 insert_request
 8,32   1        0     0.000064813     0  m   N cfq1225 add_to_rr
 8,32   1        5     0.000076336  1225  U   N [jbd2/sdc-8] 1
 8,32   1        0     0.000088559     0  m   N cfq workload slice:150
 8,32   1        0     0.000097359     0  m   N cfq1225 set_active wl_prio:0 wl_type:1
 8,32   1        0     0.000104063     0  m   N cfq1225 Not idling. st->count:1
 8,32   1        0     0.000112584     0  m   N cfq1225 fifo=  (null)
 8,32   1        0     0.000118730     0  m   N cfq1225 dispatch_insert
 8,32   1        0     0.000127390     0  m   N cfq1225 dispatched a request
 8,32   1        0     0.000133536     0  m   N cfq1225 activate rq, drv=1
 8,32   1        6     0.000136889  1225  D  WS 3417048 + 8 [jbd2/sdc-8]
 8,32   1        7     0.000360381  1225  Q  WS 3417056 + 8 [jbd2/sdc-8]
 8,32   1        8     0.000377422  1225  G  WS 3417056 + 8 [jbd2/sdc-8]
 8,32   1        9     0.000388876  1225  P   N [jbd2/sdc-8]
 8,32   1       10     0.000397886  1225  Q  WS 3417064 + 8 [jbd2/sdc-8]
 8,32   1       11     0.000404800  1225  M  WS 3417064 + 8 [jbd2/sdc-8]
 8,32   1       12     0.000412343  1225  Q  WS 3417072 + 8 [jbd2/sdc-8]
 8,32   1       13     0.000416533  1225  M  WS 3417072 + 8 [jbd2/sdc-8]
 8,32   1       14     0.000422121  1225  Q  WS 3417080 + 8 [jbd2/sdc-8]
 8,32   1       15     0.000425194  1225  M  WS 3417080 + 8 [jbd2/sdc-8]
 8,32   1       16     0.000431968  1225  Q  WS 3417088 + 8 [jbd2/sdc-8]
 8,32   1       17     0.000435251  1225  M  WS 3417088 + 8 [jbd2/sdc-8]
 8,32   1       18     0.000440279  1225  Q  WS 3417096 + 8 [jbd2/sdc-8]
 8,32   1       19     0.000443911  1225  M  WS 3417096 + 8 [jbd2/sdc-8]
 8,32   1       20     0.000450336  1225  Q  WS 3417104 + 8 [jbd2/sdc-8]
 8,32   1       21     0.000454038  1225  M  WS 3417104 + 8 [jbd2/sdc-8]
 8,32   1       22     0.000462070  1225  Q  WS 3417112 + 8 [jbd2/sdc-8]
 8,32   1       23     0.000465422  1225  M  WS 3417112 + 8 [jbd2/sdc-8]
 8,32   1       24     0.000474222  1225  I  WS 3417056 + 64 [jbd2/sdc-8]
 8,32   1        0     0.000483022     0  m   N cfq1225 insert_request
 8,32   1       25     0.000489727  1225  U   N [jbd2/sdc-8] 1
 8,32   1        0     0.000498457     0  m   N cfq1225 Not idling. st->count:1
 8,32   1        0     0.000503765     0  m   N cfq1225 dispatch_insert
 8,32   1        0     0.000512914     0  m   N cfq1225 dispatched a request
 8,32   1        0     0.000518851     0  m   N cfq1225 activate rq, drv=2
 .
 .
 .
 8,32   0        0    58.515006138     0  m   N cfq3551 complete rqnoidle 1
 8,32   0     2024    58.516603269     3  C  WS 3156992 + 16 [0]
 8,32   0        0    58.516626736     0  m   N cfq3551 complete rqnoidle 1
 8,32   0        0    58.516634558     0  m   N cfq3551 arm_idle: 8 group_idle: 0
 8,32   0        0    58.516636933     0  m   N cfq schedule dispatch
 8,32   1        0    58.516971613     0  m   N cfq3551 slice expired t=0
 8,32   1        0    58.516982089     0  m   N cfq3551 sl_used=13 disp=6 charge=13 iops=0 sect=80
 8,32   1        0    58.516985511     0  m   N cfq3551 del_from_rr
 8,32   1        0    58.516990819     0  m   N cfq3551 put_queue

CPU0 (sdc):
 Reads Queued:           0,        0KiB   Writes Queued:         331,   26,284KiB
 Read Dispatches:        0,        0KiB   Write Dispatches:      485,   40,484KiB
 Reads Requeued:         0        Writes Requeued:         0
 Reads Completed:        0,        0KiB   Writes Completed:      511,   41,000KiB
 Read Merges:            0,        0KiB   Write Merges:           13,      160KiB
 Read depth:             0            Write depth:             2
 IO unplugs:            23            Timer unplugs:           0
CPU1 (sdc):
 Reads Queued:           0,        0KiB   Writes Queued:         249,   15,800KiB
 Read Dispatches:        0,        0KiB   Write Dispatches:       42,    1,600KiB
 Reads Requeued:         0        Writes Requeued:         0
 Reads Completed:        0,        0KiB   Writes Completed:       16,    1,084KiB
 Read Merges:            0,        0KiB   Write Merges:           40,      276KiB
 Read depth:             0            Write depth:             2
 IO unplugs:            30            Timer unplugs:           1

Total (sdc):
 Reads Queued:           0,        0KiB   Writes Queued:         580,   42,084KiB
 Read Dispatches:        0,        0KiB   Write Dispatches:      527,   42,084KiB
 Reads Requeued:         0        Writes Requeued:         0
 Reads Completed:        0,        0KiB   Writes Completed:      527,   42,084KiB
 Read Merges:            0,        0KiB   Write Merges:           53,      436KiB
 IO unplugs:            53            Timer unplugs:           1

Throughput (R/W): 0KiB/s / 719KiB/s
Events (sdc): 6,592 entries
Skips: 0 forward (0 -   0.0%)
Input file sdc.blktrace.0 added
Input file sdc.blktrace.1 added
```

The report shows each event that was found in the blktrace data, along with a summary of the overall block I/O traffic during the run. You can look at the [blkparse](https://linux.die.net/man/1/blkparse) manpage to learn the meaning of each field displayed in the trace listing.

> 报告显示了在 blktrace 数据中发现的每个事件，以及运行期间块 I/O 流量的总结。您可以查看 [blkparse](https://linux.die.net/man/1/blkparse) 手册页以了解跟踪列表中显示的每个字段的含义。

### Live Mode

blktrace and blkparse are designed from the ground up to be able to operate together in a \'pipe mode\' where the stdout of blktrace can be fed directly into the stdin of blkparse:

> blktrace 和 blkparse 是从头开始设计的，可以在“管道模式”下一起工作，其中 blktrace 的标准输出可以直接输入到 blkparse 的标准输入中：

```shell
root@crownbay:~# blktrace /dev/sdc -o - | blkparse -i -
```

This enables long-lived tracing sessions to run without writing anything to disk, and allows the user to look for certain conditions in the trace data in \'real-time\' by viewing the trace output as it scrolls by on the screen or by passing it along to yet another program in the pipeline such as grep which can be used to identify and capture conditions of interest.

> 这使得可以在不将任何内容写入磁盘的情况下运行持久的跟踪会话，并允许用户通过查看屏幕上滚动的跟踪输出或将其传递给管道中的另一个程序（如 grep）来实时查找跟踪数据中的某些条件，从而可以用来识别和捕获感兴趣的条件。

There\'s actually another blktrace command that implements the above pipeline as a single command, so the user doesn\'t have to bother typing in the above command sequence:

> 实际上，还有另一个 blktrace 命令，可以将上述管道作为单个命令实现，因此用户不必打字输入上述命令序列：

```shell
root@crownbay:~# btrace /dev/sdc
```

### Using blktrace Remotely

Because blktrace traces block I/O and at the same time normally writes its trace data to a block device, and in general because it\'s not really a great idea to make the device being traced the same as the device the tracer writes to, blktrace provides a way to trace without perturbing the traced device at all by providing native support for sending all trace data over the network.

> 因为 blktrace 跟踪块 I/O，同时通常将其跟踪数据写入块设备，而一般来说，将被跟踪的设备和跟踪器写入的设备设置为同一个不是一个好主意，blktrace 提供了一种通过提供本机支持来将所有跟踪数据发送到网络而完全不干扰被跟踪设备的方法。

To have blktrace operate in this mode, start blktrace on the target system being traced with the -l option, along with the device to trace:

> 在这种模式下使用 blktrace，请在要跟踪的目标系统上使用-l 选项以及要跟踪的设备启动 blktrace：

```shell
root@crownbay:~# blktrace -l /dev/sdc
server: waiting for connections...
```

On the host system, use the -h option to connect to the target system, also passing it the device to trace:

> 在主机系统上，使用-h 选项连接到目标系统，并将跟踪设备传递给它：

```shell
$ blktrace -d /dev/sdc -h 192.168.1.43
blktrace: connecting to 192.168.1.43
blktrace: connected!
```

On the target system, you should see this:

> 在目标系统上，你应该看到这个：

```shell
server: connection from 192.168.1.43
```

In another shell, execute a workload you want to trace. :

> 在另一个 shell 中，执行你想要跟踪的工作负载。

```shell
root@crownbay:/media/sdc# rm linux-2.6.19.2.tar.bz2; wget &YOCTO_DL_URL;/mirror/sources/linux-2.6.19.2.tar.bz2; sync
Connecting to downloads.yoctoproject.org (140.211.169.59:80)
linux-2.6.19.2.tar.b 100% \|*******************************\| 41727k 0:00:00 ETA
```

When it\'s done, do a Ctrl-C on the host system to stop the trace:

> 当完成后，在主机系统上按 Ctrl-C 停止跟踪。

```shell
^C=== sdc ===
 CPU  0:                 7691 events,      361 KiB data
 CPU  1:                 4109 events,      193 KiB data
 Total:                 11800 events (dropped 0),      554 KiB data
```

On the target system, you should also see a trace summary for the trace just ended:

> 在目标系统上，您也应该看到刚刚结束的跟踪摘要：

```shell
server: end of run for 192.168.1.43:sdc
=== sdc ===
 CPU  0:                 7691 events,      361 KiB data
 CPU  1:                 4109 events,      193 KiB data
 Total:                 11800 events (dropped 0),      554 KiB data
```

The blktrace instance on the host will save the target output inside a hostname-timestamp directory:

> 在主机上的 blktrace 实例将会将目标输出保存在一个以主机名-时间戳命名的目录中：

```shell
$ ls -al
drwxr-xr-x   10 root     root          1024 Oct 28 02:40 .
drwxr-sr-x    4 root     root          1024 Oct 26 18:24 ..
drwxr-xr-x    2 root     root          1024 Oct 28 02:40 192.168.1.43-2012-10-28-02:40:56
```

cd into that directory to see the output files:

> 进入该目录查看输出文件：

```shell
$ ls -l
-rw-r--r--    1 root     root        369193 Oct 28 02:44 sdc.blktrace.0
-rw-r--r--    1 root     root        197278 Oct 28 02:44 sdc.blktrace.1
```

And run blkparse on the host system using the device name:

> 在主系统上使用设备名称运行 blkparse：

```shell
$ blkparse sdc

 8,32   1        1     0.000000000  1263  Q  RM 6016 + 8 [ls]
 8,32   1        0     0.000036038     0  m   N cfq1263 alloced
 8,32   1        2     0.000039390  1263  G  RM 6016 + 8 [ls]
 8,32   1        3     0.000049168  1263  I  RM 6016 + 8 [ls]
 8,32   1        0     0.000056152     0  m   N cfq1263 insert_request
 8,32   1        0     0.000061600     0  m   N cfq1263 add_to_rr
 8,32   1        0     0.000075498     0  m   N cfq workload slice:300
 .
 .
 .
 8,32   0        0   177.266385696     0  m   N cfq1267 arm_idle: 8 group_idle: 0
 8,32   0        0   177.266388140     0  m   N cfq schedule dispatch
 8,32   1        0   177.266679239     0  m   N cfq1267 slice expired t=0
 8,32   1        0   177.266689297     0  m   N cfq1267 sl_used=9 disp=6 charge=9 iops=0 sect=56
 8,32   1        0   177.266692649     0  m   N cfq1267 del_from_rr
 8,32   1        0   177.266696560     0  m   N cfq1267 put_queue

CPU0 (sdc):
 Reads Queued:           0,        0KiB   Writes Queued:         270,   21,708KiB
 Read Dispatches:       59,    2,628KiB   Write Dispatches:      495,   39,964KiB
 Reads Requeued:         0        Writes Requeued:         0
 Reads Completed:       90,    2,752KiB   Writes Completed:      543,   41,596KiB
 Read Merges:            0,        0KiB   Write Merges:            9,      344KiB
 Read depth:             2            Write depth:             2
 IO unplugs:            20            Timer unplugs:           1
CPU1 (sdc):
 Reads Queued:         688,    2,752KiB   Writes Queued:         381,   20,652KiB
 Read Dispatches:       31,      124KiB   Write Dispatches:       59,    2,396KiB
 Reads Requeued:         0        Writes Requeued:         0
 Reads Completed:        0,        0KiB   Writes Completed:       11,      764KiB
 Read Merges:          598,    2,392KiB   Write Merges:           88,      448KiB
 Read depth:             2            Write depth:             2
 IO unplugs:            52            Timer unplugs:           0

Total (sdc):
 Reads Queued:         688,    2,752KiB   Writes Queued:         651,   42,360KiB
 Read Dispatches:       90,    2,752KiB   Write Dispatches:      554,   42,360KiB
 Reads Requeued:         0        Writes Requeued:         0
 Reads Completed:       90,    2,752KiB   Writes Completed:      554,   42,360KiB
 Read Merges:          598,    2,392KiB   Write Merges:           97,      792KiB
 IO unplugs:            72            Timer unplugs:           1

Throughput (R/W): 15KiB/s / 238KiB/s
Events (sdc): 9,301 entries
Skips: 0 forward (0 -   0.0%)
```

You should see the trace events and summary just as you would have if you\'d run the same command on the target.

> 你应该像在目标上运行相同命令一样查看跟踪事件和摘要。

### Tracing Block I/O via \'ftrace\'

It\'s also possible to trace block I/O using only `profile-manual/usage:The 'trace events' Subsystem`{.interpreted-text role="ref"}, which can be useful for casual tracing if you don\'t want to bother dealing with the userspace tools.

> 可以使用“跟踪事件”子系统（profile-manual/usage：）来跟踪块 I/O，如果不想费心处理用户空间工具，这可能会很有用。

To enable tracing for a given device, use /sys/block/xxx/trace/enable, where xxx is the device name. This for example enables tracing for /dev/sdc:

> 要为给定设备启用跟踪，请使用/sys/block/xxx/trace/enable，其中 xxx 是设备名称。 例如，这将为/dev/sdc 启用跟踪：

```shell
root@crownbay:/sys/kernel/debug/tracing# echo 1 > /sys/block/sdc/trace/enable
```

Once you\'ve selected the device(s) you want to trace, selecting the \'blk\' tracer will turn the blk tracer on:

> 一旦你选择了要跟踪的设备，选择'blk'跟踪器将会打开 blk 跟踪器：

```shell
root@crownbay:/sys/kernel/debug/tracing# cat available_tracers
blk function_graph function nop

root@crownbay:/sys/kernel/debug/tracing# echo blk > current_tracer
```

Execute the workload you\'re interested in:

> 执行你感兴趣的工作负载：

```shell
root@crownbay:/sys/kernel/debug/tracing# cat /media/sdc/testfile.txt
```

And look at the output (note here that we\'re using \'trace_pipe\' instead of trace to capture this trace \-\-- this allows us to wait around on the pipe for data to appear):

> 看看输出（注意，我们这里使用'trace_pipe'而不是 trace 来捕获这个 trace——这样我们可以在管道上等待数据出现）：

```shell
root@crownbay:/sys/kernel/debug/tracing# cat trace_pipe
            cat-3587  [001] d..1  3023.276361:   8,32   Q   R 1699848 + 8 [cat]
            cat-3587  [001] d..1  3023.276410:   8,32   m   N cfq3587 alloced
            cat-3587  [001] d..1  3023.276415:   8,32   G   R 1699848 + 8 [cat]
            cat-3587  [001] d..1  3023.276424:   8,32   P   N [cat]
            cat-3587  [001] d..2  3023.276432:   8,32   I   R 1699848 + 8 [cat]
            cat-3587  [001] d..1  3023.276439:   8,32   m   N cfq3587 insert_request
            cat-3587  [001] d..1  3023.276445:   8,32   m   N cfq3587 add_to_rr
            cat-3587  [001] d..2  3023.276454:   8,32   U   N [cat] 1
            cat-3587  [001] d..1  3023.276464:   8,32   m   N cfq workload slice:150
            cat-3587  [001] d..1  3023.276471:   8,32   m   N cfq3587 set_active wl_prio:0 wl_type:2
            cat-3587  [001] d..1  3023.276478:   8,32   m   N cfq3587 fifo=  (null)
            cat-3587  [001] d..1  3023.276483:   8,32   m   N cfq3587 dispatch_insert
            cat-3587  [001] d..1  3023.276490:   8,32   m   N cfq3587 dispatched a request
            cat-3587  [001] d..1  3023.276497:   8,32   m   N cfq3587 activate rq, drv=1
            cat-3587  [001] d..2  3023.276500:   8,32   D   R 1699848 + 8 [cat]
```

And this turns off tracing for the specified device:

> 这将关闭指定设备的跟踪：

```shell
root@crownbay:/sys/kernel/debug/tracing# echo 0 > /sys/block/sdc/trace/enable
```

## blktrace Documentation

Online versions of the man pages for the commands discussed in this section can be found here:

> 网上版本的本节讨论的命令的手册页可以在这里找到：

- [https://linux.die.net/man/8/blktrace](https://linux.die.net/man/8/blktrace)
- [https://linux.die.net/man/1/blkparse](https://linux.die.net/man/1/blkparse)
- [https://linux.die.net/man/8/btrace](https://linux.die.net/man/8/btrace)

The above manpages, along with manpages for the other blktrace utilities (btt, blkiomon, etc) can be found in the /doc directory of the blktrace tools git repo:

> 以上的 manpages，以及其他 blktrace 工具（btt，blkiomon 等）的 manpages 可以在 blktrace 工具 git 仓库的 /doc 目录中找到：

```shell
$ git clone git://git.kernel.dk/blktrace.git
```
