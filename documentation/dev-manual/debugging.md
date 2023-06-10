---
tip: translate by openai@2023-06-10 10:27:40
...
---
title: Debugging Tools and Techniques
-------------------------------------

The exact method for debugging build failures depends on the nature of the problem and on the system\'s area from which the bug originates. Standard debugging practices such as comparison against the last known working version with examination of the changes and the re-application of steps to identify the one causing the problem are valid for the Yocto Project just as they are for any other system. Even though it is impossible to detail every possible potential failure, this section provides some general tips to aid in debugging given a variety of situations.

> 确定调试构建失败的确切方法取决于问题的性质以及缺陷源自的系统。Yocto 项目与其他系统一样，也可以使用标准调试实践，比如与最后一个已知的可工作版本进行比较，检查更改，并重新应用步骤以确定导致问题的那一步。尽管不可能详细描述每一种可能的故障，但本节提供了一些帮助调试的常规提示，以适应各种情况。

::: note
::: title
Note
:::

A useful feature for debugging is the error reporting tool. Configuring the Yocto Project to use this tool causes the OpenEmbedded build system to produce error reporting commands as part of the console output. You can enter the commands after the build completes to log error information into a common database, that can help you figure out what might be going wrong. For information on how to enable and use this feature, see the \"`dev-manual/error-reporting-tool:using the error reporting tool`{.interpreted-text role="ref"}\" section.

> 一个有用的调试功能是错误报告工具。配置 Yocto 项目使用此工具会导致 OpenEmbedded 构建系统在控制台输出中生成错误报告命令。构建完成后，您可以输入这些命令以将错误信息记录到一个公共数据库中，这有助于您弄清可能出现的问题。有关如何启用和使用此功能的信息，请参见“dev-manual/error-reporting-tool：使用错误报告工具”部分。
> :::

The following list shows the debugging topics in the remainder of this section:

- \"`dev-manual/debugging:viewing logs from failed tasks`{.interpreted-text role="ref"}\" describes how to find and view logs from tasks that failed during the build process.

> "dev-manual/debugging:viewing logs from failed tasks" 描述了如何找到并查看构建过程中失败任务的日志。

- \"`dev-manual/debugging:viewing variable values`{.interpreted-text role="ref"}\" describes how to use the BitBake `-e` option to examine variable values after a recipe has been parsed.

> 《dev-manual/debugging:viewing variable values》描述了在解析完配方后，如何使用 BitBake 的-e 选项来检查变量值。

- \"``dev-manual/debugging:viewing package information with \`\`oe-pkgdata-util\`\` ``{.interpreted-text role="ref"}\" describes how to use the `oe-pkgdata-util` utility to query `PKGDATA_DIR`{.interpreted-text role="term"} and display package-related information for built packages.

> 描述了如何使用 oe-pkgdata-util 实用程序查询 PKGDATA_DIR 并显示与构建包相关的信息。

- \"`dev-manual/debugging:viewing dependencies between recipes and tasks`{.interpreted-text role="ref"}\" describes how to use the BitBake `-g` option to display recipe dependency information used during the build.

> "dev-manual/debugging:viewing dependencies between recipes and tasks" 描述了如何使用 BitBake '-g' 选项来显示在构建过程中使用的配方依赖信息。

- \"`dev-manual/debugging:viewing task variable dependencies`{.interpreted-text role="ref"}\" describes how to use the `bitbake-dumpsig` command in conjunction with key subdirectories in the `Build Directory`{.interpreted-text role="term"} to determine variable dependencies.

> "dev-manual/debugging:viewing task variable dependencies" 描述如何使用 bitbake-dumpsig 命令与 Build Directory 中的关键子目录一起来确定变量依赖关系。

- \"`dev-manual/debugging:running specific tasks`{.interpreted-text role="ref"}\" describes how to use several BitBake options (e.g. `-c`, `-C`, and `-f`) to run specific tasks in the build chain. It can be useful to run tasks \"out-of-order\" when trying isolate build issues.

> "dev-manual/debugging:running specific tasks"{.interpreted-text role="ref"}描述了如何使用几个 BitBake 选项（例如 `-c`、`-C` 和 `-f`）来在构建链中运行特定任务。 当尝试隔离构建问题时，运行“非顺序”任务可能很有用。

- \"`dev-manual/debugging:general BitBake problems`{.interpreted-text role="ref"}\" describes how to use BitBake\'s `-D` debug output option to reveal more about what BitBake is doing during the build.

> 《dev-manual/debugging:general BitBake Problems》描述了如何使用 BitBake 的-D 调试输出选项来揭示构建期间 BitBake 正在做什么。

- \"`dev-manual/debugging:building with no dependencies`{.interpreted-text role="ref"}\" describes how to use the BitBake `-b` option to build a recipe while ignoring dependencies.

> "dev-manual/debugging:building with no dependencies"{.interpreted-text role="ref"}描述了如何使用 BitBake `-b` 选项忽略依赖关系来构建一个配方。

- \"`dev-manual/debugging:recipe logging mechanisms`{.interpreted-text role="ref"}\" describes how to use the many recipe logging functions to produce debugging output and report errors and warnings.

> "dev-manual/debugging:recipe logging mechanisms"{.interpreted-text role="ref"}描述了如何使用许多配方日志功能来产生调试输出并报告错误和警告。

- \"`dev-manual/debugging:debugging parallel make races`{.interpreted-text role="ref"}\" describes how to debug situations where the build consists of several parts that are run simultaneously and when the output or result of one part is not ready for use with a different part of the build that depends on that output.

> "dev-manual/debugging:debugging parallel make races"{.interpreted-text role="ref"}描述了如何调试构建由几个部分组成的情况，其中一部分的输出或结果尚未准备好，以便依赖于该输出的构建的另一部分使用。

- \"`dev-manual/debugging:debugging with the gnu project debugger (gdb) remotely`{.interpreted-text role="ref"}\" describes how to use GDB to allow you to examine running programs, which can help you fix problems.

> 《dev-manual/debugging：使用 GNU 项目调试器（GDB）远程调试》描述了如何使用 GDB 来检查正在运行的程序，这可以帮助您解决问题。

- \"`dev-manual/debugging:debugging with the gnu project debugger (gdb) on the target`{.interpreted-text role="ref"}\" describes how to use GDB directly on target hardware for debugging.

> "使用 GNU 项目调试器（GDB）在目标上调试"描述了如何直接在目标硬件上使用 GDB 进行调试。

- \"`dev-manual/debugging:other debugging tips`{.interpreted-text role="ref"}\" describes miscellaneous debugging tips that can be useful.

# Viewing Logs from Failed Tasks

You can find the log for a task in the file `${``WORKDIR`{.interpreted-text role="term"}`}/temp/log.do_`[taskname]{.title-ref}. For example, the log for the `ref-tasks-compile`{.interpreted-text role="ref"} task of the QEMU minimal image for the x86 machine (`qemux86`) might be in `tmp/work/qemux86-poky-linux/core-image-minimal/1.0-r0/temp/log.do_compile`. To see the commands `BitBake`{.interpreted-text role="term"} ran to generate a log, look at the corresponding `run.do_`[taskname]{.title-ref} file in the same directory.

> 你可以在文件 `${WORKDIR}/temp/log.do_[taskname]` 中找到一个任务的日志。例如，x86 机器（qemux86）上 QEMU 最小镜像的 ref-tasks-compile 任务的日志可能在 `tmp/work/qemux86-poky-linux/core-image-minimal/1.0-r0/temp/log.do_compile` 中。要查看 BitBake 运行的命令以生成日志，请在同一目录中查看相应的 `run.do_[taskname]` 文件。

`log.do_`[taskname]{.title-ref} and `run.do_`[taskname]{.title-ref} are actually symbolic links to `log.do_`[taskname]{.title-ref}`.`[pid]{.title-ref} and `log.run_`[taskname]{.title-ref}`.`[pid]{.title-ref}, where [pid]{.title-ref} is the PID the task had when it ran. The symlinks always point to the files corresponding to the most recent run.

> log.do_[任务名称]{.title-ref}和 run.do_[任务名称]{.title-ref}实际上是指向 log.do_[任务名称]{.title-ref}.`[pid]{.title-ref}和log.run_[任务名称]{.title-ref}.`[pid]{.title-ref}的符号链接，其中[pid]{.title-ref}是任务运行时的 PID。符号链接总是指向最近运行的文件。

# Viewing Variable Values

Sometimes you need to know the value of a variable as a result of BitBake\'s parsing step. This could be because some unexpected behavior occurred in your project. Perhaps an attempt to `modify a variable <bitbake-user-manual/bitbake-user-manual-metadata:modifying existing variables>`{.interpreted-text role="ref"} did not work out as expected.

> 有时你需要知道变量的值作为 BitBake 解析步骤的结果。这可能是因为您的项目中发生了一些意外行为。也许尝试修改变量没有按预期工作。

BitBake\'s `-e` option is used to display variable values after parsing. The following command displays the variable values after the configuration files (i.e. `local.conf`, `bblayers.conf`, `bitbake.conf` and so forth) have been parsed:

> BitBake 的 `-e` 选项用于在解析后显示变量值。以下命令在解析配置文件（即 `local.conf`、`bblayers.conf`、`bitbake.conf` 等）之后显示变量值：

```
$ bitbake -e
```

The following command displays variable values after a specific recipe has been parsed. The variables include those from the configuration as well:

```
$ bitbake -e recipename
```

::: note
::: title
Note
:::

Each recipe has its own private set of variables (datastore). Internally, after parsing the configuration, a copy of the resulting datastore is made prior to parsing each recipe. This copying implies that variables set in one recipe will not be visible to other recipes.

> 每个食谱都有自己的私有变量集（数据存储）。在解析配置之后，在解析每个食谱之前，会对结果数据存储进行一份拷贝。这意味着在一个食谱中设置的变量不会对其他食谱可见。

Likewise, each task within a recipe gets a private datastore based on the recipe datastore, which means that variables set within one task will not be visible to other tasks.

> 同样，每个食谱中的任务都会根据食谱数据库获得一个私有数据库，这意味着在一个任务中设置的变量不会对其他任务可见。
> :::

In the output of `bitbake -e`, each variable is preceded by a description of how the variable got its value, including temporary values that were later overridden. This description also includes variable flags (varflags) set on the variable. The output can be very helpful during debugging.

> 在 `bitbake -e` 的输出中，每个变量都会被一个描述所前置，该描述包括了变量的值是如何得到的，包括后来被覆盖的临时值。这个描述也包括设置在变量上的变量标志（varflags）。输出可以在调试时非常有帮助。

Variables that are exported to the environment are preceded by `export` in the output of `bitbake -e`. See the following example:

```
export CC="i586-poky-linux-gcc -m32 -march=i586 --sysroot=/home/ulf/poky/build/tmp/sysroots/qemux86"
```

In addition to variable values, the output of the `bitbake -e` and `bitbake -e` recipe commands includes the following information:

- The output starts with a tree listing all configuration files and classes included globally, recursively listing the files they include or inherit in turn. Much of the behavior of the OpenEmbedded build system (including the behavior of the `ref-manual/tasks:normal recipe build tasks`{.interpreted-text role="ref"}) is implemented in the `ref-classes-base`{.interpreted-text role="ref"} class and the classes it inherits, rather than being built into BitBake itself.

> 输出以树的形式开始，列出所有全局包含的配置文件和类，依次递归列出它们所包含或继承的文件。OpenEmbedded 构建系统的大部分行为（包括 `ref-manual/tasks：normal recipe build tasks`{.interpreted-text role="ref"}的行为）是由 `ref-classes-base`{.interpreted-text role="ref"}类及其继承的类实现的，而不是直接构建到 BitBake 中。

- After the variable values, all functions appear in the output. For shell functions, variables referenced within the function body are expanded. If a function has been modified using overrides or using override-style operators like `:append` and `:prepend`, then the final assembled function body appears in the output.

> 在变量值之后，所有函数都会出现在输出中。对于 shell 函数，在函数体中引用的变量将被展开。如果一个函数已经使用覆盖或使用覆盖式操作符（例如 `:append` 和 `:prepend`）进行修改，那么最终组装的函数体将出现在输出中。

# Viewing Package Information with `oe-pkgdata-util`

You can use the `oe-pkgdata-util` command-line utility to query `PKGDATA_DIR`{.interpreted-text role="term"} and display various package-related information. When you use the utility, you must use it to view information on packages that have already been built.

> 你可以使用 `oe-pkgdata-util` 命令行实用程序查询 `PKGDATA_DIR` 并显示各种与包相关的信息。当你使用该实用程序时，你必须使用它来查看已经构建的包的信息。

Following are a few of the available `oe-pkgdata-util` subcommands.

::: note
::: title
Note
:::

You can use the standard \* and ? globbing wildcards as part of package names and paths.
:::

- `oe-pkgdata-util list-pkgs [pattern]`: Lists all packages that have been built, optionally limiting the match to packages that match pattern.
- `oe-pkgdata-util list-pkg-files package ...`: Lists the files and directories contained in the given packages.

  ::: note
  ::: title
  Note
  :::

  A different way to view the contents of a package is to look at the `${``WORKDIR`{.interpreted-text role="term"}`}/packages-split` directory of the recipe that generates the package. This directory is created by the `ref-tasks-package`{.interpreted-text role="ref"} task and has one subdirectory for each package the recipe generates, which contains the files stored in that package.

> 查看包内容的另一种方法是查看生成包的配方的 `WORKDIR`/packages-split `目录。这个目录由` ref-tasks-package` 任务创建，每个配方生成的包都有一个子目录，其中包含存储在该包中的文件。

If you want to inspect the `${WORKDIR}/packages-split` directory, make sure that `ref-classes-rm-work`{.interpreted-text role="ref"} is not enabled when you build the recipe.

> 如果你想检查 `${WORKDIR}/packages-split` 目录，确保在构建食谱时不启用 `ref-classes-rm-work`{.interpreted-text role="ref"}。
> :::

- `oe-pkgdata-util find-path path ...`: Lists the names of the packages that contain the given paths. For example, the following tells us that `/usr/share/man/man1/make.1` is contained in the `make-doc` package:

> `- ` oe-pkgdata-util find-path path ...`: 列出包含给定路径的包的名称。例如，以下告诉我们`/usr/share/man/man1/make.1 `包含在` make-doc` 包中：

```
$ oe-pkgdata-util find-path /usr/share/man/man1/make.1
make-doc: /usr/share/man/man1/make.1
```

- `oe-pkgdata-util lookup-recipe package ...`: Lists the name of the recipes that produce the given packages.

For more information on the `oe-pkgdata-util` command, use the help facility:

```
$ oe-pkgdata-util --help
$ oe-pkgdata-util subcommand --help
```

# Viewing Dependencies Between Recipes and Tasks

Sometimes it can be hard to see why BitBake wants to build other recipes before the one you have specified. Dependency information can help you understand why a recipe is built.

> 有时候很难看出为什么 BitBake 要在指定的配方之前构建其他配方。依赖信息可以帮助您理解为什么要构建某个配方。

To generate dependency information for a recipe, run the following command:

```
$ bitbake -g recipename
```

This command writes the following files in the current directory:

- `pn-buildlist`: A list of recipes/targets involved in building [recipename]{.title-ref}. \"Involved\" here means that at least one task from the recipe needs to run when building [recipename]{.title-ref} from scratch. Targets that are in `ASSUME_PROVIDED`{.interpreted-text role="term"} are not listed.

> - `pn-buildlist`：用于构建[recipename]{.title-ref}的食谱/目标列表。“涉及”这里意味着，从头开始构建[recipename]{.title-ref}时，至少需要运行一个任务来自该食谱。不会列出在 `ASSUME_PROVIDED`{.interpreted-text role="term"}中的目标。

- `task-depends.dot`: A graph showing dependencies between tasks.

The graphs are in `DOT <DOT_%28graph_description_language%29>`{.interpreted-text role="wikipedia"} format and can be converted to images (e.g. using the `dot` tool from [Graphviz](https://www.graphviz.org/)).

> 图表使用 DOT（图形描述语言）格式，可以转换为图像（例如使用 Graphviz 中的“dot”工具）。

::: note
::: title
Note
:::

- DOT files use a plain text format. The graphs generated using the `bitbake -g` command are often so large as to be difficult to read without special pruning (e.g. with BitBake\'s `-I` option) and processing. Despite the form and size of the graphs, the corresponding `.dot` files can still be possible to read and provide useful information.

> DOT 文件使用纯文本格式。使用 `bitbake -g` 命令生成的图形通常很大，很难在没有特殊修剪（例如使用 BitBake 的 `-I` 选项）和处理的情况下阅读。尽管图形的形式和大小，但相应的 `.dot` 文件仍然可以阅读，并提供有用的信息。

As an example, the `task-depends.dot` file contains lines such as the following:

```
"libxslt.do_configure" -> "libxml2.do_populate_sysroot"
```

The above example line reveals that the `ref-tasks-configure`{.interpreted-text role="ref"} task in `libxslt` depends on the `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} task in `libxml2`, which is a normal `DEPENDS`{.interpreted-text role="term"} dependency between the two recipes.

> 这个例子表明 libxslt 中的 ref-tasks-configure 任务依赖于 libxml2 中的 ref-tasks-populate_sysroot 任务，这是两个配方之间的正常 DEPENDS 依赖关系。

- For an example of how `.dot` files can be processed, see the `scripts/contrib/graph-tool` Python script, which finds and displays paths between graph nodes.

> 例如如何处理 `.dot` 文件，可以参考 `scripts/contrib/graph-tool` Python 脚本，它可以查找和显示图节点之间的路径。
> :::

You can use a different method to view dependency information by using the following command:

```
$ bitbake -g -u taskexp recipename
```

This command displays a GUI window from which you can view build-time and runtime dependencies for the recipes involved in building recipename.

# Viewing Task Variable Dependencies

As mentioned in the \"`bitbake-user-manual/bitbake-user-manual-execution:checksums (signatures)`{.interpreted-text role="ref"}\" section of the BitBake User Manual, BitBake tries to automatically determine what variables a task depends on so that it can rerun the task if any values of the variables change. This determination is usually reliable. However, if you do things like construct variable names at runtime, then you might have to manually declare dependencies on those variables using `vardeps` as described in the \"`bitbake-user-manual/bitbake-user-manual-metadata:variable flags`{.interpreted-text role="ref"}\" section of the BitBake User Manual.

> 根据 BitBake 用户手册中“bitbake-user-manual/bitbake-user-manual-execution:checksums（签名）”部分的描述，BitBake 试图自动确定任务所依赖的变量，以便在任何变量的值更改时重新运行任务。这种确定通常是可靠的。但是，如果您在运行时构建变量名，则可能必须使用 BitBake 用户手册中“bitbake-user-manual/bitbake-user-manual-metadata：变量标志”部分中描述的 `vardeps` 手动声明对这些变量的依赖关系。

If you are unsure whether a variable dependency is being picked up automatically for a given task, you can list the variable dependencies BitBake has determined by doing the following:

> 如果您不确定特定任务是否会自动捕获变量依赖关系，可以通过以下步骤列出 BitBake 确定的变量依赖关系：

1. Build the recipe containing the task:

   ```
   $ bitbake recipename
   ```
2. Inside the `STAMPS_DIR`{.interpreted-text role="term"} directory, find the signature data (`sigdata`) file that corresponds to the task. The `sigdata` files contain a pickled Python database of all the metadata that went into creating the input checksum for the task. As an example, for the `ref-tasks-fetch`{.interpreted-text role="ref"} task of the `db` recipe, the `sigdata` file might be found in the following location:

> 在 `STAMPS_DIR` 目录中，找到与任务相对应的签名数据（`sigdata`）文件。 `sigdata` 文件包含一个用于创建任务输入校验和的 Python 数据库的 pickled。 例如，对于 `db` 配方的 `ref-tasks-fetch` 任务，可以在以下位置找到 `sigdata` 文件：

```
${BUILDDIR}/tmp/stamps/i586-poky-linux/db/6.0.30-r1.do_fetch.sigdata.7c048c18222b16ff0bcee2000ef648b1
```

For tasks that are accelerated through the shared state (`sstate <overview-manual/concepts:shared state cache>`{.interpreted-text role="ref"}) cache, an additional `siginfo` file is written into `SSTATE_DIR`{.interpreted-text role="term"} along with the cached task output. The `siginfo` files contain exactly the same information as `sigdata` files.

> 对于通过共享状态（`sstate <overview-manual/concepts:shared state cache>`{.interpreted-text role="ref"}）缓存加速的任务，除了缓存的任务输出之外，还会在 `SSTATE_DIR`{.interpreted-text role="term"}中写入一个额外的 `siginfo` 文件。 `siginfo` 文件包含与 `sigdata` 文件完全相同的信息。

3. Run `bitbake-dumpsig` on the `sigdata` or `siginfo` file. Here is an example:

   ```
   $ bitbake-dumpsig ${BUILDDIR}/tmp/stamps/i586-poky-linux/db/6.0.30-r1.do_fetch.sigdata.7c048c18222b16ff0bcee2000ef648b1
   ```

   In the output of the above command, you will find a line like the following, which lists all the (inferred) variable dependencies for the task. This list also includes indirect dependencies from variables depending on other variables, recursively:

> 在上述命令的输出中，您将找到一行，如下所示，它列出了任务的所有（推断）变量依赖关系。该列表还包括由依赖于其他变量的变量间接依赖关系，递归地：

```
Task dependencies: ['PV', 'SRCREV', 'SRC_URI', 'SRC_URI[md5sum]', 'SRC_URI[sha256sum]', 'base_do_fetch']
```

::: note
::: title
Note
:::

Functions (e.g. `base_do_fetch`) also count as variable dependencies. These functions in turn depend on the variables they reference.
:::

The output of `bitbake-dumpsig` also includes the value each variable had, a list of dependencies for each variable, and `BB_BASEHASH_IGNORE_VARS`{.interpreted-text role="term"} information.

> 输出 `bitbake-dumpsig` 也包括每个变量的值、每个变量的依赖列表以及 `BB_BASEHASH_IGNORE_VARS` 信息。

There is also a `bitbake-diffsigs` command for comparing two `siginfo` or `sigdata` files. This command can be helpful when trying to figure out what changed between two versions of a task. If you call `bitbake-diffsigs` with just one file, the command behaves like `bitbake-dumpsig`.

> 也有一个 `bitbake-diffsigs` 命令用于比较两个 `siginfo` 或 `sigdata` 文件。当试图弄清楚两个任务版本之间的变化时，这个命令可能会有所帮助。如果只调用一个文件的 `bitbake-diffsigs`，该命令的行为就像 `bitbake-dumpsig` 一样。

You can also use BitBake to dump out the signature construction information without executing tasks by using either of the following BitBake command-line options:

> 你也可以使用 BitBake 来转储签名构造信息，而无需执行任务，可以使用以下 BitBake 命令行选项之一：

```
‐‐dump-signatures=SIGNATURE_HANDLER
-S SIGNATURE_HANDLER
```

::: note
::: title
Note
:::

Two common values for [SIGNATURE_HANDLER]{.title-ref} are \"none\" and \"printdiff\", which dump only the signature or compare the dumped signature with the cached one, respectively.

> 两个常见的[SIGNATURE_HANDLER]{.title-ref}的值是“none”和“printdiff”，分别仅转储签名或将转储的签名与缓存的签名进行比较。
> :::

Using BitBake with either of these options causes BitBake to dump out `sigdata` files in the `stamps` directory for every task it would have executed instead of building the specified target package.

> 使用 BitBake 以这些选项之一，会导致 BitBake 在 `stamps` 目录中为每个将要执行的任务而不是构建指定的目标包而转储出 `sigdata` 文件。

# Viewing Metadata Used to Create the Input Signature of a Shared State Task

Seeing what metadata went into creating the input signature of a shared state (sstate) task can be a useful debugging aid. This information is available in signature information (`siginfo`) files in `SSTATE_DIR`{.interpreted-text role="term"}. For information on how to view and interpret information in `siginfo` files, see the \"`dev-manual/debugging:viewing task variable dependencies`{.interpreted-text role="ref"}\" section.

> 看到共享状态（sstate）任务的输入签名中包含了什么元数据可能是有用的调试辅助工具。这些信息可以在 `SSTATE_DIR` 中的签名信息（`siginfo`）文件中找到。有关如何查看和解释 `siginfo` 文件中的信息，请参见“`dev-manual/debugging:viewing task variable dependencies`{.interpreted-text role="ref"}”部分。

For conceptual information on shared state, see the \"`overview-manual/concepts:shared state`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> 对于关于共享状态的概念信息，请参见 Yocto 项目概述和概念手册中的“共享状态”部分。

# Invalidating Shared State to Force a Task to Run

The OpenEmbedded build system uses `checksums <overview-manual/concepts:checksums (signatures)>`{.interpreted-text role="ref"} and `overview-manual/concepts:shared state`{.interpreted-text role="ref"} cache to avoid unnecessarily rebuilding tasks. Collectively, this scheme is known as \"shared state code\".

> 开放式嵌入式构建系统使用校验和和共享状态缓存来避免不必要地重新构建任务。集体上，这个方案被称为“共享状态代码”。

As with all schemes, this one has some drawbacks. It is possible that you could make implicit changes to your code that the checksum calculations do not take into account. These implicit changes affect a task\'s output but do not trigger the shared state code into rebuilding a recipe. Consider an example during which a tool changes its output. Assume that the output of `rpmdeps` changes. The result of the change should be that all the `package` and `package_write_rpm` shared state cache items become invalid. However, because the change to the output is external to the code and therefore implicit, the associated shared state cache items do not become invalidated. In this case, the build process uses the cached items rather than running the task again. Obviously, these types of implicit changes can cause problems.

> 所有方案都有一些缺点，这个也不例外。你可能会对代码做出隐式改变，但这些改变不会被校验和计算考虑进去。这些隐式改变会影响任务的输出，但不会触发共享状态代码重新构建配方。举个例子，假设 `rpmdeps` 的输出发生了改变。结果就是所有 `package` 和 `package_write_rpm` 共享状态缓存项都会失效。但是，由于改变是代码以外的，因此是隐式的，相关的共享状态缓存项不会失效。在这种情况下，构建过程会使用缓存项而不是重新运行任务。显然，这些隐式改变可能会导致问题。

To avoid these problems during the build, you need to understand the effects of any changes you make. Realize that changes you make directly to a function are automatically factored into the checksum calculation. Thus, these explicit changes invalidate the associated area of shared state cache. However, you need to be aware of any implicit changes that are not obvious changes to the code and could affect the output of a given task.

> 为了避免在构建过程中出现这些问题，您需要理解对任何更改所产生的影响。要意识到，对函数所做的更改会自动被计算进校验和。因此，这些显式更改会使相关的共享状态缓存区失效。但是，您需要注意任何不明显的隐式更改，这些更改可能会影响给定任务的输出。

When you identify an implicit change, you can easily take steps to invalidate the cache and force the tasks to run. The steps you can take are as simple as changing a function\'s comments in the source code. For example, to invalidate package shared state files, change the comment statements of `ref-tasks-package`{.interpreted-text role="ref"} or the comments of one of the functions it calls. Even though the change is purely cosmetic, it causes the checksum to be recalculated and forces the build system to run the task again.

> 当您识别出一个隐式变化时，您可以轻松采取步骤来使缓存失效，并强制任务重新运行。您可以采取的步骤就像在源代码中更改函数的注释一样简单。例如，要使包共享状态文件失效，请更改 `ref-tasks-package`{.interpreted-text role="ref"}的注释语句或其调用的函数之一的注释。即使更改只是装饰性的，也会导致校验和重新计算，并强制构建系统再次运行该任务。

::: note
::: title
Note
:::

For an example of a commit that makes a cosmetic change to invalidate shared state, see this :yocto\_[git:%60commit](git:%60commit) \</poky/commit/meta/classes/package.bbclass?id=737f8bbb4f27b4837047cb9b4fbfe01dfde36d54\>\`.

> 为了例子，看一个提交，它做了一个改变，使共享状态无效：yocto\_[git:%60commit](git:%60commit) \</poky/commit/meta/classes/package.bbclass?id=737f8bbb4f27b4837047cb9b4fbfe01dfde36d54\>\`。
> :::

# Running Specific Tasks

Any given recipe consists of a set of tasks. The standard BitBake behavior in most cases is: `ref-tasks-fetch`{.interpreted-text role="ref"}, `ref-tasks-unpack`{.interpreted-text role="ref"}, `ref-tasks-patch`{.interpreted-text role="ref"}, `ref-tasks-configure`{.interpreted-text role="ref"}, `ref-tasks-compile`{.interpreted-text role="ref"}, `ref-tasks-install`{.interpreted-text role="ref"}, `ref-tasks-package`{.interpreted-text role="ref"}, `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}, and `ref-tasks-build`{.interpreted-text role="ref"}. The default task is `ref-tasks-build`{.interpreted-text role="ref"} and any tasks on which it depends build first. Some tasks, such as `ref-tasks-devshell`{.interpreted-text role="ref"}, are not part of the default build chain. If you wish to run a task that is not part of the default build chain, you can use the `-c` option in BitBake. Here is an example:

> 给定的食谱由一系列任务组成。在大多数情况下，BitBake 的标准行为是：`ref-tasks-fetch`{.interpreted-text role="ref"}, `ref-tasks-unpack`{.interpreted-text role="ref"}, `ref-tasks-patch`{.interpreted-text role="ref"}, `ref-tasks-configure`{.interpreted-text role="ref"}, `ref-tasks-compile`{.interpreted-text role="ref"}, `ref-tasks-install`{.interpreted-text role="ref"}, `ref-tasks-package`{.interpreted-text role="ref"}, `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}, 以及 `ref-tasks-build`{.interpreted-text role="ref"}。默认任务是 `ref-tasks-build`{.interpreted-text role="ref"}，并且先构建它所依赖的任务。有些任务，如 `ref-tasks-devshell`{.interpreted-text role="ref"}，不是默认构建链的一部分。如果您希望运行不属于默认构建链的任务，可以在 BitBake 中使用 `-c` 选项。下面是一个例子：

```
$ bitbake matchbox-desktop -c devshell
```

The `-c` option respects task dependencies, which means that all other tasks (including tasks from other recipes) that the specified task depends on will be run before the task. Even when you manually specify a task to run with `-c`, BitBake will only run the task if it considers it \"out of date\". See the \"`overview-manual/concepts:stamp files and the rerunning of tasks`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual for how BitBake determines whether a task is \"out of date\".

> `-c` 选项遵循任务依赖关系，这意味着所有其他任务（包括其他配方中的任务）都将在指定任务之前运行。即使您使用 `-c` 手动指定要运行的任务，BitBake 也只有在认为它“过时”时才会运行该任务。有关 BitBake 如何确定任务是否“过时”的信息，请参阅 Yocto 项目概述和概念手册中的 “概述-手册/概念：戳印文件和重新运行任务”部分。

If you want to force an up-to-date task to be rerun (e.g. because you made manual modifications to the recipe\'s `WORKDIR`{.interpreted-text role="term"} that you want to try out), then you can use the `-f` option.

> 如果你想强制重新运行一个最新的任务（例如，因为你对食谱的 `WORKDIR`{.interpreted-text role="term"}做了手动修改，你想试一下），那么你可以使用 `-f` 选项。

::: note
::: title
Note
:::

The reason `-f` is never required when running the `ref-tasks-devshell`{.interpreted-text role="ref"} task is because the \[`nostamp <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>`{.interpreted-text role="ref"}\] variable flag is already set for the task.

> 原因是在运行 `ref-tasks-devshell` 任务时不需要使用 `-f` 参数，是因为已经为该任务设置了 `nostamp <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>` 变量标志。
> :::

The following example shows one way you can use the `-f` option:

```
$ bitbake matchbox-desktop
          .
          .
make some changes to the source code in the work directory
          .
          .
$ bitbake matchbox-desktop -c compile -f
$ bitbake matchbox-desktop
```

This sequence first builds and then recompiles `matchbox-desktop`. The last command reruns all tasks (basically the packaging tasks) after the compile. BitBake recognizes that the `ref-tasks-compile`{.interpreted-text role="ref"} task was rerun and therefore understands that the other tasks also need to be run again.

> 这个序列首先构建，然后重新编译 matchbox-desktop。最后一个命令在编译后重新运行所有任务（基本上是打包任务）。BitBake 识别 ref-tasks-compile 任务已经重新运行，因此理解其他任务也需要重新运行。

Another, shorter way to rerun a task and all `ref-manual/tasks:normal recipe build tasks`{.interpreted-text role="ref"} that depend on it is to use the `-C` option.

> 另一种更简单的方法重新运行任务并重新运行 `ref-manual/tasks:normal recipe build tasks`{.interpreted-text role="ref"}，该任务依赖它，是使用 `-C` 选项。

::: note
::: title
Note
:::

This option is upper-cased and is separate from the `-c` option, which is lower-cased.
:::

Using this option invalidates the given task and then runs the `ref-tasks-build`{.interpreted-text role="ref"} task, which is the default task if no task is given, and the tasks on which it depends. You could replace the final two commands in the previous example with the following single command:

> 使用此选项会使给定的任务无效，然后运行 `ref-tasks-build`{.interpreted-text role="ref"}任务，这是如果没有给定任务时的默认任务，以及它所依赖的任务。你可以用以下单个命令替换前面示例中的最后两个命令：

```
$ bitbake matchbox-desktop -C compile
```

Internally, the `-f` and `-C` options work by tainting (modifying) the input checksum of the specified task. This tainting indirectly causes the task and its dependent tasks to be rerun through the normal task dependency mechanisms.

> 内部，`-f` 和 `-C` 选项通过污染（修改）指定任务的输入校验和来工作。这种污染间接导致任务及其依赖任务通过正常任务依赖机制重新运行。

::: note
::: title
Note
:::

BitBake explicitly keeps track of which tasks have been tainted in this fashion, and will print warnings such as the following for builds involving such tasks:

> BitBake 明确跟踪哪些任务以这种方式受到污染，并且会对涉及这些任务的构建打印出如下警告：

```none
WARNING: /home/ulf/poky/meta/recipes-sato/matchbox-desktop/matchbox-desktop_2.1.bb.do_compile is tainted from a forced run
```

The purpose of the warning is to let you know that the work directory and build output might not be in the clean state they would be in for a \"normal\" build, depending on what actions you took. To get rid of such warnings, you can remove the work directory and rebuild the recipe, as follows:

> 警告的目的是让您知道，根据您采取的行动，工作目录和构建输出可能不会处于“正常”构建中的干净状态。要消除此类警告，您可以删除工作目录并重新构建配方，如下所示：

```
$ bitbake matchbox-desktop -c clean
$ bitbake matchbox-desktop
```

:::

You can view a list of tasks in a given package by running the `ref-tasks-listtasks`{.interpreted-text role="ref"} task as follows:

```
$ bitbake matchbox-desktop -c listtasks
```

The results appear as output to the console and are also in the file `${WORKDIR}/temp/log.do_listtasks`.

# General BitBake Problems

You can see debug output from BitBake by using the `-D` option. The debug output gives more information about what BitBake is doing and the reason behind it. Each `-D` option you use increases the logging level. The most common usage is `-DDD`.

> 您可以使用 `-D` 选项查看 BitBake 的调试输出。调试输出提供了更多有关 BitBake 正在做什么以及其背后原因的信息。您使用的每个 `-D` 选项都会增加日志级别。最常见的用法是 `-DDD`。

The output from `bitbake -DDD -v targetname` can reveal why BitBake chose a certain version of a package or why BitBake picked a certain provider. This command could also help you in a situation where you think BitBake did something unexpected.

> `bitbake -DDD -v targetname` 的输出可以揭示为什么 BitBake 选择了某个版本的软件包或者为什么 BitBake 选择了某个提供者。当你认为 BitBake 做了一些意想不到的事时，这个命令也可以帮助你。

# Building with No Dependencies

To build a specific recipe (`.bb` file), you can use the following command form:

```
$ bitbake -b somepath/somerecipe.bb
```

This command form does not check for dependencies. Consequently, you should use it only when you know existing dependencies have been met.

::: note
::: title
Note
:::

You can also specify fragments of the filename. In this case, BitBake checks for a unique match.
:::

# Recipe Logging Mechanisms

The Yocto Project provides several logging functions for producing debugging output and reporting errors and warnings. For Python functions, the following logging functions are available. All of these functions log to `${T}/log.do_`[task]{.title-ref}, and can also log to standard output (stdout) with the right settings:

> Yocto 项目提供了几个用于生成调试输出、报告错误和警告的日志功能。对于 Python 函数，可以使用以下日志功能。所有这些函数都会记录到 `${T}/log.do_`[task]{.title-ref}，并且也可以在正确的设置下记录到标准输出（stdout）中。

- `bb.plain(msg)`: Writes msg as is to the log while also logging to stdout.
- `bb.note(msg)`: Writes \"NOTE: msg\" to the log. Also logs to stdout if BitBake is called with \"-v\".
- `bb.debug(level, msg)`: Writes \"DEBUG: msg\" to the log. Also logs to stdout if the log level is greater than or equal to level. See the \"`bitbake-user-manual/bitbake-user-manual-intro:usage and syntax`{.interpreted-text role="ref"}\" option in the BitBake User Manual for more information.

> bb.debug(级别, msg)：将"DEBUG：msg"写入日志。如果日志级别大于或等于级别，也将日志记录到 stdout。有关更多信息，请参阅 BitBake 用户手册中的"bitbake-user-manual / bitbake-user-manual-intro：用法和语法"选项。

- `bb.warn(msg)`: Writes \"WARNING: msg\" to the log while also logging to stdout.
- `bb.error(msg)`: Writes \"ERROR: msg\" to the log while also logging to standard out (stdout).

  ::: note
  ::: title
  Note
  :::

  Calling this function does not cause the task to fail.
  :::
- `bb.fatal(msg)`: This logging function is similar to `bb.error(msg)` but also causes the calling task to fail.

  ::: note
  ::: title
  Note
  :::

  `bb.fatal()` raises an exception, which means you do not need to put a \"return\" statement after the function.
  :::

The same logging functions are also available in shell functions, under the names `bbplain`, `bbnote`, `bbdebug`, `bbwarn`, `bberror`, and `bbfatal`. The `ref-classes-logging`{.interpreted-text role="ref"} class implements these functions. See that class in the `meta/classes` folder of the `Source Directory`{.interpreted-text role="term"} for information.

> 这些日志功能也可以在 shell 函数中使用，它们的名字分别是 `bbplain`，`bbnote`，`bbdebug`，`bbwarn`，`bberror` 和 `bbfatal`。`ref-classes-logging`{.interpreted-text role="ref"}类实现了这些函数。有关更多信息，请参阅 `Source Directory`{.interpreted-text role="term"}的 `meta/classes` 文件夹中的该类。

## Logging With Python

When creating recipes using Python and inserting code that handles build logs, keep in mind the goal is to have informative logs while keeping the console as \"silent\" as possible. Also, if you want status messages in the log, use the \"debug\" loglevel.

> 当使用 Python 创建食谱并插入处理构建日志的代码时，请记住目标是拥有有用的日志，同时使控制台尽可能“静默”。此外，如果想要在日志中看到状态消息，请使用“debug”日志级别。

Following is an example written in Python. The code handles logging for a function that determines the number of tasks needed to be run. See the \"`ref-tasks-listtasks`{.interpreted-text role="ref"}\" section for additional information:

> 以下是用 Python 编写的一个示例。该代码处理用于确定需要运行的任务数量的函数的日志。有关其他信息，请参见“ref-tasks-listtasks”部分。

```
python do_listtasks() {
    bb.debug(2, "Starting to figure out the task list")
    if noteworthy_condition:
        bb.note("There are 47 tasks to run")
    bb.debug(2, "Got to point xyz")
    if warning_trigger:
        bb.warn("Detected warning_trigger, this might be a problem later.")
    if recoverable_error:
        bb.error("Hit recoverable_error, you really need to fix this!")
    if fatal_error:
        bb.fatal("fatal_error detected, unable to print the task list")
    bb.plain("The tasks present are abc")
    bb.debug(2, "Finished figuring out the tasklist")
}
```

## Logging With Bash

When creating recipes using Bash and inserting code that handles build logs, you have the same goals \-\-- informative with minimal console output. The syntax you use for recipes written in Bash is similar to that of recipes written in Python described in the previous section.

> 在使用 Bash 创建配方并插入处理构建日志的代码时，您的目标是相同的--信息量多，控制台输出最少。 用于 Bash 中编写的配方的语法与前一节中描述的用于 Python 中编写的配方的语法相似。

Following is an example written in Bash. The code logs the progress of the `do_my_function` function:

```
do_my_function() {
    bbdebug 2 "Running do_my_function"
    if [ exceptional_condition ]; then
        bbnote "Hit exceptional_condition"
    fi
    bbdebug 2  "Got to point xyz"
    if [ warning_trigger ]; then
        bbwarn "Detected warning_trigger, this might cause a problem later."
    fi
    if [ recoverable_error ]; then
        bberror "Hit recoverable_error, correcting"
    fi
    if [ fatal_error ]; then
        bbfatal "fatal_error detected"
    fi
    bbdebug 2 "Completed do_my_function"
}
```

# Debugging Parallel Make Races

A parallel `make` race occurs when the build consists of several parts that are run simultaneously and a situation occurs when the output or result of one part is not ready for use with a different part of the build that depends on that output. Parallel make races are annoying and can sometimes be difficult to reproduce and fix. However, there are some simple tips and tricks that can help you debug and fix them. This section presents a real-world example of an error encountered on the Yocto Project autobuilder and the process used to fix it.

> 当构建由几个部分组成，并且其中一部分的输出或结果尚未准备好用于另一部分构建所依赖的时候，就会发生并行“make”赛跑。并行 make 赛跑很烦人，有时很难重现和修复。但是，有一些简单的技巧可以帮助您调试和修复它们。本节介绍了 Yocto 项目自动构建器遇到的一个错误，以及用于修复该错误的过程。

::: note
::: title
Note
:::

If you cannot properly fix a `make` race condition, you can work around it by clearing either the `PARALLEL_MAKE`{.interpreted-text role="term"} or `PARALLEL_MAKEINST`{.interpreted-text role="term"} variables.

> 如果您无法正确修复 `make` 竞争条件，您可以通过清除 `PARALLEL_MAKE`{.interpreted-text role="term"}或 `PARALLEL_MAKEINST`{.interpreted-text role="term"}变量来解决它。
> :::

## The Failure

For this example, assume that you are building an image that depends on the \"neard\" package. And, during the build, BitBake runs into problems and creates the following output.

> 对于这个例子，假设您正在构建一个依赖于“neard”包的图像。而且，在构建过程中，BitBake 遇到问题并产生以下输出。

::: note
::: title
Note
:::

This example log file has longer lines artificially broken to make the listing easier to read.
:::

If you examine the output or the log file, you see the failure during `make`:

```none
| DEBUG: SITE files ['endian-little', 'bit-32', 'ix86-common', 'common-linux', 'common-glibc', 'i586-linux', 'common']
| DEBUG: Executing shell function do_compile
| NOTE: make -j 16
| make --no-print-directory all-am
| /bin/mkdir -p include/near
| /bin/mkdir -p include/near
| /bin/mkdir -p include/near
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/types.h include/near/types.h
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/log.h include/near/log.h
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/plugin.h include/near/plugin.h
| /bin/mkdir -p include/near
| /bin/mkdir -p include/near
| /bin/mkdir -p include/near
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/tag.h include/near/tag.h
| /bin/mkdir -p include/near
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/adapter.h include/near/adapter.h
| /bin/mkdir -p include/near
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/ndef.h include/near/ndef.h
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/tlv.h include/near/tlv.h
| /bin/mkdir -p include/near
| /bin/mkdir -p include/near
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/setting.h include/near/setting.h
| /bin/mkdir -p include/near
| /bin/mkdir -p include/near
| /bin/mkdir -p include/near
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/device.h include/near/device.h
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/nfc_copy.h include/near/nfc_copy.h
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/snep.h include/near/snep.h
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/version.h include/near/version.h
| ln -s /home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/work/i586-poky-linux/neard/
  0.14-r0/neard-0.14/include/dbus.h include/near/dbus.h
| ./src/genbuiltin nfctype1 nfctype2 nfctype3 nfctype4 p2p > src/builtin.h
| i586-poky-linux-gcc  -m32 -march=i586 --sysroot=/home/pokybuild/yocto-autobuilder/nightly-x86/
  build/build/tmp/sysroots/qemux86 -DHAVE_CONFIG_H -I. -I./include -I./src -I./gdbus  -I/home/pokybuild/
  yocto-autobuilder/nightly-x86/build/build/tmp/sysroots/qemux86/usr/include/glib-2.0
  -I/home/pokybuild/yocto-autobuilder/nightly-x86/build/build/tmp/sysroots/qemux86/usr/
  lib/glib-2.0/include  -I/home/pokybuild/yocto-autobuilder/nightly-x86/build/build/
  tmp/sysroots/qemux86/usr/include/dbus-1.0 -I/home/pokybuild/yocto-autobuilder/
  nightly-x86/build/build/tmp/sysroots/qemux86/usr/lib/dbus-1.0/include  -I/home/pokybuild/yocto-autobuilder/
  nightly-x86/build/build/tmp/sysroots/qemux86/usr/include/libnl3
  -DNEAR_PLUGIN_BUILTIN -DPLUGINDIR=\""/usr/lib/near/plugins"\"
  -DCONFIGDIR=\""/etc/neard\"" -O2 -pipe -g -feliminate-unused-debug-types -c
  -o tools/snep-send.o tools/snep-send.c
| In file included from tools/snep-send.c:16:0:
| tools/../src/near.h:41:23: fatal error: near/dbus.h: No such file or directory
|  #include <near/dbus.h>
|                        ^
| compilation terminated.
| make[1]: *** [tools/snep-send.o] Error 1
| make[1]: *** Waiting for unfinished jobs....
| make: *** [all] Error 2
| ERROR: oe_runmake failed
```

## Reproducing the Error

Because race conditions are intermittent, they do not manifest themselves every time you do the build. In fact, most times the build will complete without problems even though the potential race condition exists. Thus, once the error surfaces, you need a way to reproduce it.

> 因为竞争条件是间歇性的，所以每次构建时它们并不会体现出来。事实上，大多数情况下构建都会完成，即使潜在的竞争条件存在。因此，一旦出现错误，你需要一种方法来重现它。

In this example, compiling the \"neard\" package is causing the problem. So the first thing to do is build \"neard\" locally. Before you start the build, set the `PARALLEL_MAKE`{.interpreted-text role="term"} variable in your `local.conf` file to a high number (e.g. \"-j 20\"). Using a high value for `PARALLEL_MAKE`{.interpreted-text role="term"} increases the chances of the race condition showing up:

> 在这个例子中，编译"neard"包正在导致问题。因此，首先要做的是在本地构建"neard"。在开始构建之前，请在 `local.conf` 文件中将 `PARALLEL_MAKE` 变量设置为一个较高的数字（例如“-j 20”）。使用 `PARALLEL_MAKE` 变量的高值可以增加竞争条件出现的可能性：

```
$ bitbake neard
```

Once the local build for \"neard\" completes, start a `devshell` build:

```
$ bitbake neard -c devshell
```

For information on how to use a `devshell`, see the \"`dev-manual/development-shell:using a development shell`{.interpreted-text role="ref"}\" section.

> 要了解如何使用“devshell”，请参阅“dev-manual/development-shell：使用开发环境”部分。

In the `devshell`, do the following:

```
$ make clean
$ make tools/snep-send.o
```

The `devshell` commands cause the failure to clearly be visible. In this case, there is a missing dependency for the `neard` Makefile target. Here is some abbreviated, sample output with the missing dependency clearly visible at the end:

> `devshell` 命令使失败明显可见。在这种情况下，`neard` Makefile 目标缺少依赖项。以下是缺少依赖项明显可见的简化样本输出：

```
i586-poky-linux-gcc  -m32 -march=i586 --sysroot=/home/scott-lenovo/......
   .
   .
   .
tools/snep-send.c
In file included from tools/snep-send.c:16:0:
tools/../src/near.h:41:23: fatal error: near/dbus.h: No such file or directory
 #include <near/dbus.h>
                  ^
compilation terminated.
make: *** [tools/snep-send.o] Error 1
$
```

## Creating a Patch for the Fix

Because there is a missing dependency for the Makefile target, you need to patch the `Makefile.am` file, which is generated from `Makefile.in`. You can use Quilt to create the patch:

> 因为 Makefile 目标缺少依赖项，所以你需要修补由 Makefile.in 生成的 Makefile.am 文件。你可以使用 Quilt 来创建补丁：

```
$ quilt new parallelmake.patch
Patch patches/parallelmake.patch is now on top
$ quilt add Makefile.am
File Makefile.am added to patch patches/parallelmake.patch
```

For more information on using Quilt, see the \"`dev-manual/quilt:using quilt in your workflow`{.interpreted-text role="ref"}\" section.

At this point you need to make the edits to `Makefile.am` to add the missing dependency. For our example, you have to add the following line to the file:

> 在这一点，您需要编辑 `Makefile.am` 以添加缺失的依赖关系。对于我们的示例，您必须将以下行添加到文件中：

```
tools/snep-send.$(OBJEXT): include/near/dbus.h
```

Once you have edited the file, use the `refresh` command to create the patch:

```
$ quilt refresh
Refreshed patch patches/parallelmake.patch
```

Once the patch file is created, you need to add it back to the originating recipe folder. Here is an example assuming a top-level `Source Directory`{.interpreted-text role="term"} named `poky`:

> 一旦补丁文件创建完成，你需要将其返回到原始的配方文件夹中。以下是一个假设有一个顶层源代码目录名为 `poky` 的例子：

```
$ cp patches/parallelmake.patch poky/meta/recipes-connectivity/neard/neard
```

The final thing you need to do to implement the fix in the build is to update the \"neard\" recipe (i.e. `neard-0.14.bb`) so that the `SRC_URI`{.interpreted-text role="term"} statement includes the patch file. The recipe file is in the folder above the patch. Here is what the edited `SRC_URI`{.interpreted-text role="term"} statement would look like:

> 最后一件事你需要做的是在构建中实施修复，就是更新“neard”配方（即 `neard-0.14.bb`），以便 `SRC_URI`{.interpreted-text role="term"}语句包括补丁文件。配方文件在补丁上面的文件夹中。这里是编辑后的 `SRC_URI`{.interpreted-text role="term"}语句看起来像什么：

```
SRC_URI = "${KERNELORG_MIRROR}/linux/network/nfc/${BPN}-${PV}.tar.xz \
           file://neard.in \
           file://neard.service.in \
           file://parallelmake.patch \
          "
```

With the patch complete and moved to the correct folder and the `SRC_URI`{.interpreted-text role="term"} statement updated, you can exit the `devshell`:

> 经补丁完成并移动到正确的文件夹，并更新 `SRC_URI` 声明后，您可以退出 `devshell`：

```
$ exit
```

## Testing the Build

With everything in place, you can get back to trying the build again locally:

```
$ bitbake neard
```

This build should succeed.

Now you can open up a `devshell` again and repeat the clean and make operations as follows:

```
$ bitbake neard -c devshell
$ make clean
$ make tools/snep-send.o
```

The build should work without issue.

As with all solved problems, if they originated upstream, you need to submit the fix for the recipe in OE-Core and upstream so that the problem is taken care of at its source. See the \"`dev-manual/changes:submitting a change to the yocto project`{.interpreted-text role="ref"}\" section for more information.

> 随着所有解决的问题，如果它们来自上游，您需要在 OE-Core 和上游中提交对配方的修复，以便将问题解决在源头上。有关更多信息，请参阅“dev-manual / changes：提交 Yocto 项目的变更”部分。

# Debugging With the GNU Project Debugger (GDB) Remotely

GDB allows you to examine running programs, which in turn helps you to understand and fix problems. It also allows you to perform post-mortem style analysis of program crashes. GDB is available as a package within the Yocto Project and is installed in SDK images by default. See the \"`ref-manual/images:Images`{.interpreted-text role="ref"}\" chapter in the Yocto Project Reference Manual for a description of these images. You can find information on GDB at [https://sourceware.org/gdb/](https://sourceware.org/gdb/).

> GDB 可以让您检查正在运行的程序，从而帮助您理解和解决问题。它还允许您对程序崩溃进行后瞻性分析。GDB 在 Yocto Project 中作为一个软件包提供，并且默认情况下已经安装在 SDK 镜像中。有关这些镜像的描述，请参阅 Yocto Project 参考手册中的“ref-manual/images：Images”章节。您可以在 [https://sourceware.org/gdb/](https://sourceware.org/gdb/)上找到有关 GDB 的信息。

::: note
::: title
Note
:::

For best results, install debug (`-dbg`) packages for the applications you are going to debug. Doing so makes extra debug symbols available that give you more meaningful output.

> 为了获得最佳结果，请为您要调试的应用程序安装调试（`-dbg`）软件包。这样可以提供额外的调试符号，使您获得更有意义的输出。
> :::

Sometimes, due to memory or disk space constraints, it is not possible to use GDB directly on the remote target to debug applications. These constraints arise because GDB needs to load the debugging information and the binaries of the process being debugged. Additionally, GDB needs to perform many computations to locate information such as function names, variable names and values, stack traces and so forth \-\-- even before starting the debugging process. These extra computations place more load on the target system and can alter the characteristics of the program being debugged.

> 有时，由于内存或磁盘空间的限制，无法直接在远程目标上使用 GDB 来调试应用程序。这些限制是因为 GDB 需要加载调试信息和被调试进程的二进制文件。此外，GDB 需要执行许多计算来定位信息，例如函数名，变量名和值，堆栈跟踪等等--甚至在开始调试过程之前。这些额外的计算会给目标系统带来更多的负载，并可能改变被调试程序的特性。

To help get past the previously mentioned constraints, there are two methods you can use: running a debuginfod server and using gdbserver.

## Using the debuginfod server method

`debuginfod` from `elfutils` is a way to distribute `debuginfo` files. Running a `debuginfod` server makes debug symbols readily available, which means you don\'t need to download debugging information and the binaries of the process being debugged. You can just fetch debug symbols from the server.

> debuginfod（来自 elfutils）是一种分发 debuginfo 文件的方式。运行 debuginfod 服务器可以使调试符号容易获取，这意味着你不需要下载调试信息和被调试进程的二进制文件。你可以从服务器上获取调试符号。

To run a `debuginfod` server, you need to do the following:

- Ensure that `debuginfod` is present in `DISTRO_FEATURES`{.interpreted-text role="term"} (it already is in `OpenEmbedded-core` defaults and `poky` reference distribution). If not, set in your distro config file or in `local.conf`:

> 确保 `debuginfod` 在 `DISTRO_FEATURES` 中存在（它已经在 `OpenEmbedded-core` 默认值和 `poky` 参考分发中存在）。如果没有，请在您的发行版配置文件或 `local.conf` 中设置：

```
DISTRO_FEATURES:append = " debuginfod"
```

This distro feature enables the server and client library in `elfutils`, and enables `debuginfod` support in clients (at the moment, `gdb` and `binutils`).

> 这个发行版本特性使服务器和客户端库在 elfutils 中可用，并且在客户端（目前是 gdb 和 binutils）中启用 debuginfod 支持。

- Run the following commands to launch the `debuginfod` server on the host:

  ```
  $ oe-debuginfod
  ```
- To use `debuginfod` on the target, you need to know the ip:port where `debuginfod` is listening on the host (port defaults to 8002), and export that into the shell environment, for example in `qemu`:

> 要在目标上使用 debuginfod，您需要知道主机上 debuginfod 监听的 IP：端口（端口默认为 8002），并将其导出到 shell 环境中，例如在 qemu 中：

```
root@qemux86-64:~# export DEBUGINFOD_URLS="http://192.168.7.1:8002/"
```

- Then debug info fetching should simply work when running the target `gdb`, `readelf` or `objdump`, for example:

  ```
  root@qemux86-64:~# gdb /bin/cat
  ...
  Reading symbols from /bin/cat...
  Downloading separate debug info for /bin/cat...
  Reading symbols from /home/root/.cache/debuginfod_client/923dc4780cfbc545850c616bffa884b6b5eaf322/debuginfo...
  ```
- It\'s also possible to use `debuginfod-find` to just query the server:

  ```
  root@qemux86-64:~# debuginfod-find debuginfo /bin/ls
  /home/root/.cache/debuginfod_client/356edc585f7f82d46f94fcb87a86a3fe2d2e60bd/debuginfo
  ```

## Using the gdbserver method

gdbserver, which runs on the remote target and does not load any debugging information from the debugged process. Instead, a GDB instance processes the debugging information that is run on a remote computer -the host GDB. The host GDB then sends control commands to gdbserver to make it stop or start the debugged program, as well as read or write memory regions of that debugged program. All the debugging information loaded and processed as well as all the heavy debugging is done by the host GDB. Offloading these processes gives the gdbserver running on the target a chance to remain small and fast.

> gdbserver 运行在远程目标上，不从被调试进程加载任何调试信息。相反，GDB 实例处理运行在远程计算机上的调试信息-主 GDB。主 GDB 然后向 gdbserver 发送控制命令，使其停止或启动被调试程序，以及读取或写入该被调试程序的内存区域。所有加载和处理的调试信息以及所有重型调试都由主 GDB 完成。将这些过程移出可以让运行在目标上的 gdbserver 有机会保持小巧且快速。

Because the host GDB is responsible for loading the debugging information and for doing the necessary processing to make actual debugging happen, you have to make sure the host can access the unstripped binaries complete with their debugging information and also be sure the target is compiled with no optimizations. The host GDB must also have local access to all the libraries used by the debugged program. Because gdbserver does not need any local debugging information, the binaries on the remote target can remain stripped. However, the binaries must also be compiled without optimization so they match the host\'s binaries.

> 因为主 GDB 负责加载调试信息并执行必要的处理，以使实际调试发生，因此您必须确保主机可以访问未剥离的二进制文件，以及确保目标编译时不使用优化。主 GDB 还必须有被调试程序使用的所有库的本地访问权限。由于 gdbserver 不需要任何本地调试信息，因此远程目标上的二进制文件可以保持剥离状态。但是，二进制文件也必须编译为不进行优化，以使其与主机的二进制文件匹配。

To remain consistent with GDB documentation and terminology, the binary being debugged on the remote target machine is referred to as the \"inferior\" binary. For documentation on GDB see the [GDB site](https://sourceware.org/gdb/documentation/).

> 为了与 GDB 文档和术语保持一致，在远程目标机上被调试的二进制文件被称为“inferior”二进制文件。有关 GDB 的文档，请参见 [GDB 站点](https://sourceware.org/gdb/documentation/)。

The following steps show you how to debug using the GNU project debugger.

1. *Configure your build system to construct the companion debug filesystem:*

   In your `local.conf` file, set the following:

   ```
   IMAGE_GEN_DEBUGFS = "1"
   IMAGE_FSTYPES_DEBUGFS = "tar.bz2"
   ```

   These options cause the OpenEmbedded build system to generate a special companion filesystem fragment, which contains the matching source and debug symbols to your deployable filesystem. The build system does this by looking at what is in the deployed filesystem, and pulling the corresponding `-dbg` packages.

> 这些选项会导致 OpenEmbedded 构建系统生成一个特殊的附属文件系统片段，其中包含与部署文件系统匹配的源代码和调试符号。构建系统通过查看部署文件系统中的内容，并拉取相应的 `-dbg` 包来实现这一点。

The companion debug filesystem is not a complete filesystem, but only contains the debug fragments. This filesystem must be combined with the full filesystem for debugging. Subsequent steps in this procedure show how to combine the partial filesystem with the full filesystem.

> 附属调试文件系统不是一个完整的文件系统，只包含调试片段。这个文件系统必须与完整的文件系统结合起来才能进行调试。本程序的后续步骤将演示如何将部分文件系统与完整文件系统结合起来。

2. *Configure the system to include gdbserver in the target filesystem:*

   Make the following addition in your `local.conf` file:

   ```
   EXTRA_IMAGE_FEATURES:append = " tools-debug"
   ```

   The change makes sure the `gdbserver` package is included.
3. *Build the environment:*

   Use the following command to construct the image and the companion Debug Filesystem:

   ```
   $ bitbake image
   ```

   Build the cross GDB component and make it available for debugging. Build the SDK that matches the image. Building the SDK is best for a production build that can be used later for debugging, especially during long term maintenance:

> 构建交叉 GDB 组件，使其可用于调试。构建与图像匹配的 SDK。构建 SDK 最适合用于生产构建，以便稍后用于调试，特别是在长期维护期间：

```
$ bitbake -c populate_sdk image
```

Alternatively, you can build the minimal toolchain components that match the target. Doing so creates a smaller than typical SDK and only contains a minimal set of components with which to build simple test applications, as well as run the debugger:

> 另外，您可以构建与目标匹配的最小工具链组件。这样做可以创建一个比典型的 SDK 更小的 SDK，只包含一组最小的组件，用于构建简单的测试应用程序，以及运行调试器：

```
$ bitbake meta-toolchain
```

A final method is to build Gdb itself within the build system:

```
$ bitbake gdb-cross-<architecture>
```

Doing so produces a temporary copy of `cross-gdb` you can use for debugging during development. While this is the quickest approach, the two previous methods in this step are better when considering long-term maintenance strategies.

> 这样做可以临时生成一个 `cross-gdb` 的副本，用于开发期间的调试。虽然这是最快的方法，但考虑到长期维护策略，前两种方法更好。

::: note
::: title
Note
:::

If you run `bitbake gdb-cross`, the OpenEmbedded build system suggests the actual image (e.g. `gdb-cross-i586`). The suggestion is usually the actual name you want to use.

> 如果你运行 `bitbake gdb-cross`，OpenEmbedded 构建系统会建议实际的镜像（例如 `gdb-cross-i586`）。 这个建议通常是你想要使用的实际名称。
> :::

4. *Set up the* `debugfs`*:*

   Run the following commands to set up the `debugfs`:

   ```
   $ mkdir debugfs
   $ cd debugfs
   $ tar xvfj build-dir/tmp/deploy/images/machine/image.rootfs.tar.bz2
   $ tar xvfj build-dir/tmp/deploy/images/machine/image-dbg.rootfs.tar.bz2
   ```
5. *Set up GDB:*

   Install the SDK (if you built one) and then source the correct environment file. Sourcing the environment file puts the SDK in your `PATH` environment variable and sets `$GDB` to the SDK\'s debugger.

> 安装 SDK（如果您构建了一个），然后源自正确的环境文件。源自环境文件将 SDK 放入您的 `PATH` 环境变量中，并将 `$GDB` 设置为 SDK 的调试器。

If you are using the build system, Gdb is located in [build-dir]{.title-ref}`/tmp/sysroots/`[host]{.title-ref}`/usr/bin/`[architecture]{.title-ref}`/`[architecture]{.title-ref}`-gdb`

> 如果您正在使用构建系统，Gdb 位于[build-dir]{.title-ref}`/tmp/sysroots/`[host]{.title-ref}`/usr/bin/`[architecture]{.title-ref}`/`[architecture]{.title-ref}`-gdb` 中。

6. *Boot the target:*

   For information on how to run QEMU, see the [QEMU Documentation](https://wiki.qemu.org/Documentation/GettingStartedDevelopers).

   ::: note
   ::: title
   Note
   :::

   Be sure to verify that your host can access the target via TCP.
   :::
7. *Debug a program:*

   Debugging a program involves running gdbserver on the target and then running Gdb on the host. The example in this step debugs `gzip`:

   ```shell
   root@qemux86:~# gdbserver localhost:1234 /bin/gzip —help
   ```

   For additional gdbserver options, see the [GDB Server Documentation](https://www.gnu.org/software/gdb/documentation/).

   After running gdbserver on the target, you need to run Gdb on the host and configure it and connect to the target. Use these commands:

   ```
   $ cd directory-holding-the-debugfs-directory
   $ arch-gdb
   (gdb) set sysroot debugfs
   (gdb) set substitute-path /usr/src/debug debugfs/usr/src/debug
   (gdb) target remote IP-of-target:1234
   ```

   At this point, everything should automatically load (i.e. matching binaries, symbols and headers).

   ::: note
   ::: title
   Note
   :::

   The Gdb `set` commands in the previous example can be placed into the users `~/.gdbinit` file. Upon starting, Gdb automatically runs whatever commands are in that file.

> 上一个例子中的 Gdb `set` 命令可以放入用户的 `~/.gdbinit` 文件中。启动时，Gdb 会自动运行该文件中的任何命令。
> :::

8. *Deploying without a full image rebuild:*

   In many cases, during development you want a quick method to deploy a new binary to the target and debug it, without waiting for a full image build.

> 在许多情况下，在开发过程中，您希望有一种快速的方法来将新二进制文件部署到目标并进行调试，而无需等待完整的映像构建。

One approach to solving this situation is to just build the component you want to debug. Once you have built the component, copy the executable directly to both the target and the host `debugfs`.

> 一种解决此情况的方法是构建要调试的组件。构建组件后，将可执行文件直接复制到目标和主机的'debugfs'中。

If the binary is processed through the debug splitting in OpenEmbedded, you should also copy the debug items (i.e. `.debug` contents and corresponding `/usr/src/debug` files) from the work directory. Here is an example:

> 如果二进制文件经由 OpenEmbedded 的调试分割处理，您也应该从工作目录中复制调试项（即 `.debug` 内容和相应的 `/usr/src/debug` 文件）。这里有一个例子：

```
$ bitbake bash
$ bitbake -c devshell bash
$ cd ..
$ scp packages-split/bash/bin/bash target:/bin/bash
$ cp -a packages-split/bash-dbg/\* path/debugfs
```

# Debugging with the GNU Project Debugger (GDB) on the Target

The previous section addressed using GDB remotely for debugging purposes, which is the most usual case due to the inherent hardware limitations on many embedded devices. However, debugging in the target hardware itself is also possible with more powerful devices. This section describes what you need to do in order to support using GDB to debug on the target hardware.

> 上一节讨论了使用 GDB 进行远程调试的用途，这是由于许多嵌入式设备具有固有的硬件限制而最常见的情况。但是，在目标硬件本身也可以使用更强大的设备进行调试。本节介绍了您需要做什么来支持使用 GDB 在目标硬件上调试。

To support this kind of debugging, you need do the following:

- Ensure that GDB is on the target. You can do this by making the following addition to your `local.conf` file:

  ```
  EXTRA_IMAGE_FEATURES:append = " tools-debug"
  ```
- Ensure that debug symbols are present. You can do so by adding the corresponding `-dbg` package to `IMAGE_INSTALL`{.interpreted-text role="term"}:

  ```
  IMAGE_INSTALL:append = " packagename-dbg"
  ```

  Alternatively, you can add the following to `local.conf` to include all the debug symbols:

  ```
  EXTRA_IMAGE_FEATURES:append = " dbg-pkgs"
  ```

::: note
::: title
Note
:::

To improve the debug information accuracy, you can reduce the level of optimization used by the compiler. For example, when adding the following line to your `local.conf` file, you will reduce optimization from `FULL_OPTIMIZATION`{.interpreted-text role="term"} of \"-O2\" to `DEBUG_OPTIMIZATION`{.interpreted-text role="term"} of \"-O -fno-omit-frame-pointer\":

> 为了提高调试信息的准确性，你可以减少编译器使用的优化级别。例如，在您的 `local.conf` 文件中添加以下行时，将优化从\"-O2\"的 `FULL_OPTIMIZATION`{.interpreted-text role="term"}降低到\"-O -fno-omit-frame-pointer\"的 `DEBUG_OPTIMIZATION`{.interpreted-text role="term"}：

```
DEBUG_BUILD = "1"
```

Consider that this will reduce the application\'s performance and is recommended only for debugging purposes.
:::

# Other Debugging Tips

Here are some other tips that you might find useful:

- When adding new packages, it is worth watching for undesirable items making their way into compiler command lines. For example, you do not want references to local system files like `/usr/lib/` or `/usr/include/`.

> 当添加新的软件包时，值得留意是否有不需要的项目出现在编译器命令行中。例如，你不希望参考本地系统文件，比如 `/usr/lib/` 或 `/usr/include/`。

- If you want to remove the `psplash` boot splashscreen, add `psplash=false` to the kernel command line. Doing so prevents `psplash` from loading and thus allows you to see the console. It is also possible to switch out of the splashscreen by switching the virtual console (e.g. Fn+Left or Fn+Right on a Zaurus).

> 如果你想移除 `psplash` 引导闪屏，可以在内核命令行中添加 `psplash=false`。这样可以阻止 `psplash` 的加载，从而让你看到控制台。也可以通过切换虚拟控制台（例如 Zaurus 上的 Fn+Left 或 Fn+Right）来退出闪屏。

- Removing `TMPDIR`{.interpreted-text role="term"} (usually `tmp/`, within the `Build Directory`{.interpreted-text role="term"}) can often fix temporary build issues. Removing `TMPDIR`{.interpreted-text role="term"} is usually a relatively cheap operation, because task output will be cached in `SSTATE_DIR`{.interpreted-text role="term"} (usually `sstate-cache/`, which is also in the `Build Directory`{.interpreted-text role="term"}).

> 移除 `TMPDIR`（通常在 `构建目录` 中的 `tmp/`）通常可以解决临时构建问题。移除 `TMPDIR` 通常是一个相对便宜的操作，因为任务输出将被缓存在 `SSTATE_DIR`（通常在 `构建目录` 中的 `sstate-cache/`）中。

::: note
::: title
Note
:::

Removing `TMPDIR`{.interpreted-text role="term"} might be a workaround rather than a fix. Consequently, trying to determine the underlying cause of an issue before removing the directory is a good idea.

> 移除 TMPDIR 可能只是一种变通方法，而不是真正的解决方案。因此，在移除该目录之前，最好先尝试确定问题的根本原因。
> :::

- Understanding how a feature is used in practice within existing recipes can be very helpful. It is recommended that you configure some method that allows you to quickly search through files.

> 了解特性如何在现有配方中使用可能会非常有帮助。建议您配置一些能够快速搜索文件的方法。

Using GNU Grep, you can use the following shell function to recursively search through common recipe-related files, skipping binary files, `.git` directories, and the `Build Directory`{.interpreted-text role="term"} (assuming its name starts with \"build\"):

> 使用 GNU Grep，您可以使用以下 shell 函数递归搜索常见的配方相关文件，跳过二进制文件，`.git` 目录和“Build Directory”（假设其名称以“build”开头）：

```
g() {
    grep -Ir \
         --exclude-dir=.git \
         --exclude-dir='build*' \
         --include='*.bb*' \
         --include='*.inc*' \
         --include='*.conf*' \
         --include='*.py*' \
         "$@"
}
```

Following are some usage examples:

```
$ g FOO # Search recursively for "FOO"
$ g -i foo # Search recursively for "foo", ignoring case
$ g -w FOO # Search recursively for "FOO" as a word, ignoring e.g. "FOOBAR"
```

If figuring out how some feature works requires a lot of searching, it might indicate that the documentation should be extended or improved. In such cases, consider filing a documentation bug using the Yocto Project implementation of :yocto_bugs:[Bugzilla \<\>]{.title-ref}. For information on how to submit a bug against the Yocto Project, see the Yocto Project Bugzilla :yocto_wiki:[wiki page \</Bugzilla_Configuration_and_Bug_Tracking\>]{.title-ref} and the \"`dev-manual/changes:submitting a defect against the yocto project`{.interpreted-text role="ref"}\" section.

> 如果弄清某些功能的工作原理需要花费很多时间搜索，这可能意味着文档需要扩展或改进。在这种情况下，请考虑使用 Yocto Project 实现：yocto_bugs:[Bugzilla \<\>]{.title-ref}提交文档错误。有关如何提交针对 Yocto Project 的错误的信息，请参阅 Yocto Project Bugzilla：yocto_wiki:[wiki 页面\</Bugzilla_Configuration_and_Bug_Tracking\>]{.title-ref}和“dev-manual/changes:submitting a defect against the yocto project”部分。

::: note
::: title
Note
:::

The manuals might not be the right place to document variables that are purely internal and have a limited scope (e.g. internal variables used to implement a single `.bbclass` file).

> 手册可能不是记录仅具有有限范围的内部变量（例如用于实现单个 `.bbclass` 文件的内部变量）的正确位置。
> :::
