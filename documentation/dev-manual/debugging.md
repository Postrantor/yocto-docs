---
tip: translate by baidu@2023-06-07 17:10:31
...
---
title: Debugging Tools and Techniques
-------------------------------------

The exact method for debugging build failures depends on the nature of the problem and on the system\'s area from which the bug originates. Standard debugging practices such as comparison against the last known working version with examination of the changes and the re-application of steps to identify the one causing the problem are valid for the Yocto Project just as they are for any other system. Even though it is impossible to detail every possible potential failure, this section provides some general tips to aid in debugging given a variety of situations.

> 调试构建失败的确切方法取决于问题的性质和错误产生的系统区域。标准调试实践，如与上一个已知的工作版本进行比较，检查更改，并重新应用步骤来确定导致问题的原因，对 Yocto 项目有效，就像对任何其他系统一样。尽管不可能详细说明每一个可能的潜在故障，但本节提供了一些通用提示，以帮助在各种情况下进行调试。

::: note
::: title
Note
:::

A useful feature for debugging is the error reporting tool. Configuring the Yocto Project to use this tool causes the OpenEmbedded build system to produce error reporting commands as part of the console output. You can enter the commands after the build completes to log error information into a common database, that can help you figure out what might be going wrong. For information on how to enable and use this feature, see the \"`dev-manual/error-reporting-tool:using the error reporting tool`{.interpreted-text role="ref"}\" section.

> 调试的一个有用功能是错误报告工具。将 Yocto 项目配置为使用此工具会导致 OpenEmbedded 构建系统生成错误报告命令，作为控制台输出的一部分。您可以在构建完成后输入命令，将错误信息记录到公共数据库中，这可以帮助您找出可能出现的问题。有关如何启用和使用此功能的信息，请参阅\“`dev manual/error reporting tool:using the error reporting tool`｛.depreted text role=”ref“｝\”一节。
> :::

The following list shows the debugging topics in the remainder of this section:

> 以下列表显示了本节剩余部分中的调试主题：

- \"`dev-manual/debugging:viewing logs from failed tasks`{.interpreted-text role="ref"}\" describes how to find and view logs from tasks that failed during the build process.

> -\“`dev manual/debugging:查看失败任务中的日志`｛.explored text role=“ref”｝\”描述了如何查找和查看生成过程中失败任务的日志。

- \"`dev-manual/debugging:viewing variable values`{.interpreted-text role="ref"}\" describes how to use the BitBake `-e` option to examine variable values after a recipe has been parsed.

> -\“`dev manual/debugging:viewing variable values`｛.explored text role=“ref”｝\”描述了如何在解析配方后使用 BitBake `-e` 选项检查变量值。

- \"``dev-manual/debugging:viewing package information with \`\`oe-pkgdata-util\`\` ``{.interpreted-text role="ref"}\" describes how to use the `oe-pkgdata-util` utility to query `PKGDATA_DIR`{.interpreted-text role="term"} and display package-related information for built packages.

> -\“``dev manual/debugging:使用\`\`oe pkgdata util\`\` `｛.expreted text role=“ref”｝查看包信息”描述了如何使用` oe pkgdata util `实用程序查询` pkgdata_DIR`｛.repreted text role=“term”｝并显示已构建包的包相关信息。

- \"`dev-manual/debugging:viewing dependencies between recipes and tasks`{.interpreted-text role="ref"}\" describes how to use the BitBake `-g` option to display recipe dependency information used during the build.

> -\“`dev manual/debugging:查看配方和任务之间的依赖关系`{.depreted text role=“ref”}\”描述了如何使用 BitBake `-g` 选项来显示构建过程中使用的配方依赖关系信息。

- \"`dev-manual/debugging:viewing task variable dependencies`{.interpreted-text role="ref"}\" describes how to use the `bitbake-dumpsig` command in conjunction with key subdirectories in the `Build Directory`{.interpreted-text role="term"} to determine variable dependencies.

> -\“`dev manual/debugging:viewing task variable dependencies`｛.respered text role=“ref”｝\”描述了如何将 `bitbake dumpsig` 命令与 `Build Directory`｛.espered text role=“term”｝中的关键子目录结合使用来确定变量依赖关系。

- \"`dev-manual/debugging:running specific tasks`{.interpreted-text role="ref"}\" describes how to use several BitBake options (e.g. `-c`, `-C`, and `-f`) to run specific tasks in the build chain. It can be useful to run tasks \"out-of-order\" when trying isolate build issues.

> -\“`dev manual/debugging:运行特定任务`｛.explored text role=“ref”｝\”描述了如何使用几个 BitBake 选项（例如“-c”、“-c”和“-f”）来运行构建链中的特定任务。当试图隔离生成问题时，运行“无序”的任务可能很有用。

- \"`dev-manual/debugging:general BitBake problems`{.interpreted-text role="ref"}\" describes how to use BitBake\'s `-D` debug output option to reveal more about what BitBake is doing during the build.

> -“`dev manual/debugging:general BitBake problems`｛.explored text role=“ref”｝”描述了如何使用 BitBake 的 `-D` 调试输出选项来揭示 BitBake 在构建过程中所做的更多工作。

- \"`dev-manual/debugging:building with no dependencies`{.interpreted-text role="ref"}\" describes how to use the BitBake `-b` option to build a recipe while ignoring dependencies.

> -\“`dev manual/debugging:building without dependencies`｛.explored text role=“ref”｝\”描述了如何使用 BitBake `-b` 选项在忽略依赖项的情况下构建配方。

- \"`dev-manual/debugging:recipe logging mechanisms`{.interpreted-text role="ref"}\" describes how to use the many recipe logging functions to produce debugging output and report errors and warnings.

> -\“`dev manual/debugging:precipe logging mechanism`｛.respered text role=“ref”｝\”描述了如何使用许多配方日志记录函数来生成调试输出并报告错误和警告。

- \"`dev-manual/debugging:debugging parallel make races`{.interpreted-text role="ref"}\" describes how to debug situations where the build consists of several parts that are run simultaneously and when the output or result of one part is not ready for use with a different part of the build that depends on that output.

> -\“`dev manual/debug：debugging parallel make race`｛.depreted text role=“ref”｝\”描述了如何调试构建由同时运行的多个部分组成的情况，以及一个部分的输出或结果未准备好与依赖于该输出的构建的不同部分一起使用的情况。

- \"`dev-manual/debugging:debugging with the gnu project debugger (gdb) remotely`{.interpreted-text role="ref"}\" describes how to use GDB to allow you to examine running programs, which can help you fix problems.

> -\“`dev manual/debugging:使用gnu项目调试器（gdb）远程调试`｛.depreted text role=“ref”｝\”描述了如何使用 gdb 来检查正在运行的程序，这可以帮助您解决问题。

- \"`dev-manual/debugging:debugging with the gnu project debugger (gdb) on the target`{.interpreted-text role="ref"}\" describes how to use GDB directly on target hardware for debugging.

> -\“`dev manual/debugging:在目标上使用gnu项目调试器（gdb）进行调试`{.depreted text role=“ref”}\”描述了如何在目标硬件上直接使用 gdb 进行调试。

- \"`dev-manual/debugging:other debugging tips`{.interpreted-text role="ref"}\" describes miscellaneous debugging tips that can be useful.

> -\“`dev manual/debugging:其他调试提示`｛.explored text role=“ref”｝\”描述了可能有用的其他调试提示。

# Viewing Logs from Failed Tasks

You can find the log for a task in the file `${``WORKDIR`{.interpreted-text role="term"}`}/temp/log.do_`[taskname]{.title-ref}. For example, the log for the `ref-tasks-compile`{.interpreted-text role="ref"} task of the QEMU minimal image for the x86 machine (`qemux86`) might be in `tmp/work/qemux86-poky-linux/core-image-minimal/1.0-r0/temp/log.do_compile`. To see the commands `BitBake`{.interpreted-text role="term"} ran to generate a log, look at the corresponding `run.do_`[taskname]{.title-ref} file in the same directory.

> 您可以在文件 `$｛` WORKDIR `｛.explored text role=“term”｝`｝/temp/log.do_`[taskname]｛.title ref｝中找到任务的日志。例如，x86机器的QEMU最小映像（` qemux86 `）的` ref tasks compile `｛.depredicted text role=“ref”｝任务的日志可能在` tmp/work/qemux86 poky linux/core image minimal/1.0-r0/temp/log.do_compile `中。要查看为生成日志而运行的命令` BitBake `｛.epredicted textrole=”term“｝，请查看同一目录中相应的` run.do_`[taskname]｛.title-ref}文件。

`log.do_`[taskname]{.title-ref} and `run.do_`[taskname]{.title-ref} are actually symbolic links to `log.do_`[taskname]{.title-ref}`.`[pid]{.title-ref} and `log.run_`[taskname]{.title-ref}`.`[pid]{.title-ref}, where [pid]{.title-ref} is the PID the task had when it ran. The symlinks always point to the files corresponding to the most recent run.

> `log.do_`[taskname]{.title-ref}和 `run.do_`[taskname]{.title-ref｝实际上是到 `log.do_`[taskname]{.title-ref}`.`[pid]{.tittle-ref}`和` log.run_`[Ttaskname]｛.title-ref}`.[pid]｛.title ref｝的符号链接，其中[pid]｛.title_ref｝是任务运行时的 pid。符号链接始终指向与最近运行相对应的文件。

# Viewing Variable Values

Sometimes you need to know the value of a variable as a result of BitBake\'s parsing step. This could be because some unexpected behavior occurred in your project. Perhaps an attempt to `modify a variable <bitbake-user-manual/bitbake-user-manual-metadata:modifying existing variables>`{.interpreted-text role="ref"} did not work out as expected.

> 有时，由于 BitBake 的解析步骤，您需要知道变量的值。这可能是因为您的项目中发生了一些意外行为。也许试图“修改变量 <bitbake 用户手册/bitbake 用户手动元数据：修改现有变量 >`{.depredicted text role=“ref”}并没有如预期的那样成功。

BitBake\'s `-e` option is used to display variable values after parsing. The following command displays the variable values after the configuration files (i.e. `local.conf`, `bblayers.conf`, `bitbake.conf` and so forth) have been parsed:

> BitBake 的“-e”选项用于在解析后显示变量值。以下命令显示解析配置文件（即“local.conf'、“bblayers.conf'和“bitbake.conf'等）后的变量值：

```
$ bitbake -e
```

The following command displays variable values after a specific recipe has been parsed. The variables include those from the configuration as well:

> 以下命令在解析特定配方后显示变量值。这些变量还包括配置中的变量：

```
$ bitbake -e recipename
```

::: note
::: title
Note
:::

Each recipe has its own private set of variables (datastore). Internally, after parsing the configuration, a copy of the resulting datastore is made prior to parsing each recipe. This copying implies that variables set in one recipe will not be visible to other recipes.

> 每个配方都有自己的私有变量集（数据存储）。在内部，在解析配置之后，在解析每个配方之前，将生成结果数据存储的副本。这种复制意味着一个配方中设置的变量对其他配方不可见。

Likewise, each task within a recipe gets a private datastore based on the recipe datastore, which means that variables set within one task will not be visible to other tasks.

> 同样，配方中的每个任务都会基于配方数据存储获得一个私有数据存储，这意味着在一个任务中设置的变量对其他任务不可见。
> :::

In the output of `bitbake -e`, each variable is preceded by a description of how the variable got its value, including temporary values that were later overridden. This description also includes variable flags (varflags) set on the variable. The output can be very helpful during debugging.

> 在“bitbake-e”的输出中，每个变量前面都有一个变量如何获得其值的描述，包括后来被覆盖的临时值。此描述还包括在变量上设置的变量标志（varflags）。在调试过程中，输出可能非常有用。

Variables that are exported to the environment are preceded by `export` in the output of `bitbake -e`. See the following example:

> 导出到环境中的变量在“bitbake-e”的输出中以“export”开头。请参见以下示例：

```
export CC="i586-poky-linux-gcc -m32 -march=i586 --sysroot=/home/ulf/poky/build/tmp/sysroots/qemux86"
```

In addition to variable values, the output of the `bitbake -e` and `bitbake -e` recipe commands includes the following information:

> 除了可变值外，“bitbake-e”和“bitbake-e”配方命令的输出还包括以下信息：

- The output starts with a tree listing all configuration files and classes included globally, recursively listing the files they include or inherit in turn. Much of the behavior of the OpenEmbedded build system (including the behavior of the `ref-manual/tasks:normal recipe build tasks`{.interpreted-text role="ref"}) is implemented in the `ref-classes-base`{.interpreted-text role="ref"} class and the classes it inherits, rather than being built into BitBake itself.

> -输出以一个树开始，该树列出了全局包含的所有配置文件和类，并依次递归地列出了它们包含或继承的文件。OpenEmbedded 构建系统的大部分行为（包括 `ref manual/tasks:normal recipe build tasks`｛.depreted text role=“ref”｝的行为）是在 `ref classes base`｛.epreted text role=“ref”}类及其继承的类中实现的，而不是构建到 BitBake 本身中。

- After the variable values, all functions appear in the output. For shell functions, variables referenced within the function body are expanded. If a function has been modified using overrides or using override-style operators like `:append` and `:prepend`, then the final assembled function body appears in the output.

> -变量值之后，所有函数都显示在输出中。对于 shell 函数，函数体中引用的变量是展开的。如果使用重写或使用重写样式运算符（如“：append”和“：prepend”）修改了函数，则最终组装的函数体将显示在输出中。

# Viewing Package Information with `oe-pkgdata-util`

You can use the `oe-pkgdata-util` command-line utility to query `PKGDATA_DIR`{.interpreted-text role="term"} and display various package-related information. When you use the utility, you must use it to view information on packages that have already been built.

> 您可以使用 `oe-pkdata-util` 命令行实用程序查询 `pkgdata_DIR`｛.depredicted text role=“term”｝并显示各种与包相关的信息。使用该实用程序时，必须使用它来查看已生成的包的信息。

Following are a few of the available `oe-pkgdata-util` subcommands.

> 以下是一些可用的“oe-pkdata-util”子命令。

::: note
::: title
Note
:::

You can use the standard \* and ? globbing wildcards as part of package names and paths.

> 您可以使用标准\*和？将通配符作为包名称和路径的一部分。
> :::

- `oe-pkgdata-util list-pkgs [pattern]`: Lists all packages that have been built, optionally limiting the match to packages that match pattern.

> -`oe-pkgdata util list pkgs[pattern]`：列出已生成的所有包，可以选择将匹配限制为与模式匹配的包。

- `oe-pkgdata-util list-pkg-files package ...`: Lists the files and directories contained in the given packages.

> -`oe-pkgdata util list pkg文件包…`：列出给定包中包含的文件和目录。

::: note
::: title

Note

> 笔记
> :::

A different way to view the contents of a package is to look at the `${``WORKDIR`{.interpreted-text role="term"}`}/packages-split` directory of the recipe that generates the package. This directory is created by the `ref-tasks-package`{.interpreted-text role="ref"} task and has one subdirectory for each package the recipe generates, which contains the files stored in that package.

> 查看包内容的另一种方法是查看生成包的配方的 `${` WORKDIR `{.depredicted text role=“term”}`}/packages split `目录。该目录由` ref tasks package`｛.explored text role=“ref”｝任务创建，配方生成的每个包都有一个子目录，其中包含存储在该包中的文件。

If you want to inspect the `${WORKDIR}/packages-split` directory, make sure that `ref-classes-rm-work`{.interpreted-text role="ref"} is not enabled when you build the recipe.

> 如果要检查 `${WORKDIR}/packages-split` 目录，请确保在构建配方时未启用 `ref classes rm work`{.depreted text role=“ref”}。
> :::

- `oe-pkgdata-util find-path path ...`: Lists the names of the packages that contain the given paths. For example, the following tells us that `/usr/share/man/man1/make.1` is contained in the `make-doc` package:

> -`oe-pkgdata util查找路径路径…`：列出包含给定路径的包的名称。例如，下面告诉我们，`make-doc` 包中包含 `/usr/share/man/man1/make.1`：

```
$ oe-pkgdata-util find-path /usr/share/man/man1/make.1
make-doc: /usr/share/man/man1/make.1
```

- `oe-pkgdata-util lookup-recipe package ...`: Lists the name of the recipes that produce the given packages.

> -`oe-pkgdata util查找配方包…`：列出生成给定包的配方的名称。

For more information on the `oe-pkgdata-util` command, use the help facility:

> 有关“oe-pkdata-util”命令的更多信息，请使用帮助工具：

```
$ oe-pkgdata-util --help
$ oe-pkgdata-util subcommand --help
```

# Viewing Dependencies Between Recipes and Tasks

Sometimes it can be hard to see why BitBake wants to build other recipes before the one you have specified. Dependency information can help you understand why a recipe is built.

> 有时很难理解 BitBake 为什么要在您指定的食谱之前构建其他食谱。依赖关系信息可以帮助您理解构建配方的原因。

To generate dependency information for a recipe, run the following command:

> 要生成配方的依赖关系信息，请运行以下命令：

```
$ bitbake -g recipename
```

This command writes the following files in the current directory:

> 此命令在当前目录中写入以下文件：

- `pn-buildlist`: A list of recipes/targets involved in building [recipename]{.title-ref}. \"Involved\" here means that at least one task from the recipe needs to run when building [recipename]{.title-ref} from scratch. Targets that are in `ASSUME_PROVIDED`{.interpreted-text role="term"} are not listed.

> -`pn buildlist`：构建[recipename]｛.title-ref｝所涉及的配方/目标的列表。这里的“涉及”意味着从头开始构建[recipename]{.title-ref｝时，至少需要运行配方中的一个任务。未列出 `ASSUME_PROVIDED`｛.explored text role=“term”｝中的目标。

- `task-depends.dot`: A graph showing dependencies between tasks.

The graphs are in `DOT <DOT_%28graph_description_language%29>`{.interpreted-text role="wikipedia"} format and can be converted to images (e.g. using the `dot` tool from [Graphviz](https://www.graphviz.org/)).

> 图形采用 `DOT<DOT_%28graphdescription_language%29>`{.depredicted text role=“wikipedia”}格式，可以转换为图像（例如，使用[Graphviz]中的 `DOT` 工具([https://www.graphviz.org/](https://www.graphviz.org/)))。

::: note
::: title
Note
:::

- DOT files use a plain text format. The graphs generated using the `bitbake -g` command are often so large as to be difficult to read without special pruning (e.g. with BitBake\'s `-I` option) and processing. Despite the form and size of the graphs, the corresponding `.dot` files can still be possible to read and provide useful information.

> -DOT 文件使用纯文本格式。使用“bitbake-g”命令生成的图通常太大，如果不进行特殊修剪（例如使用 bitbake 的“-I”选项）和处理，就很难读取。尽管图形的形式和大小不同，但相应的“.dot”文件仍然可以读取并提供有用的信息。

As an example, the `task-depends.dot` file contains lines such as the following:

> 例如，“task-dependent.dot”文件包含以下行：

```
"libxslt.do_configure" -> "libxml2.do_populate_sysroot"
```

The above example line reveals that the `ref-tasks-configure`{.interpreted-text role="ref"} task in `libxslt` depends on the `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} task in `libxml2`, which is a normal `DEPENDS`{.interpreted-text role="term"} dependency between the two recipes.

> 上面的示例行表明，`libxslt` 中的 `ref tasks configure`｛.depreted text role=“ref”｝任务取决于 `libxml2'中的` ref-tasks-populate_sysroot `｛.epreted text role=“ref”}任务，这是两个配方之间的正常` depends`｛.repreted text 角色=“term”｝依赖关系。

- For an example of how `.dot` files can be processed, see the `scripts/contrib/graph-tool` Python script, which finds and displays paths between graph nodes.

> -有关如何处理“.dot”文件的示例，请参阅“scripts/contrib/graph tool”Python 脚本，该脚本可查找并显示图形节点之间的路径。
> :::

You can use a different method to view dependency information by using the following command:

> 通过使用以下命令，可以使用其他方法查看依赖关系信息：

```
$ bitbake -g -u taskexp recipename
```

This command displays a GUI window from which you can view build-time and runtime dependencies for the recipes involved in building recipename.

> 此命令显示一个 GUI 窗口，您可以从中查看构建配方所涉及的构建时间和运行时依赖关系。

# Viewing Task Variable Dependencies

As mentioned in the \"`bitbake-user-manual/bitbake-user-manual-execution:checksums (signatures)`{.interpreted-text role="ref"}\" section of the BitBake User Manual, BitBake tries to automatically determine what variables a task depends on so that it can rerun the task if any values of the variables change. This determination is usually reliable. However, if you do things like construct variable names at runtime, then you might have to manually declare dependencies on those variables using `vardeps` as described in the \"`bitbake-user-manual/bitbake-user-manual-metadata:variable flags`{.interpreted-text role="ref"}\" section of the BitBake User Manual.

> 正如《bitbake 用户手册》的“`bitbake用户手册/bitbake用户手册执行：校验和（签名）`{.expreted text role=“ref”}\”一节所述，bitbake 试图自动确定任务依赖哪些变量，以便在变量值发生变化时重新运行任务。这种测定通常是可靠的。但是，如果您在运行时执行诸如构造变量名之类的操作，则可能必须使用“vardeps”手动声明对这些变量的依赖关系，如《bitbake 用户手册》的\“`bitbake user manual/bitbake user-manual metadata:variable flags`｛.depreted text role=”ref“｝\”部分所述。

If you are unsure whether a variable dependency is being picked up automatically for a given task, you can list the variable dependencies BitBake has determined by doing the following:

> 如果您不确定是否为给定任务自动拾取变量依赖项，您可以通过执行以下操作列出 BitBake 确定的变量依赖项：

1. Build the recipe containing the task:

> 1.构建包含任务的配方：

```

> ```

$ bitbake recipename

> $bitbake回收

```

> ```
> ```

2. Inside the `STAMPS_DIR`{.interpreted-text role="term"} directory, find the signature data (`sigdata`) file that corresponds to the task. The `sigdata` files contain a pickled Python database of all the metadata that went into creating the input checksum for the task. As an example, for the `ref-tasks-fetch`{.interpreted-text role="ref"} task of the `db` recipe, the `sigdata` file might be found in the following location:

> 2.在 `STAMPS_DIR`｛.explored text role=“term”｝目录中，找到与任务对应的签名数据（`sigdata`）文件。“sigdata”文件包含一个 pickle Python 数据库，其中包含为任务创建输入校验和时使用的所有元数据。例如，对于“db”配方的“ref tasks fetch”｛.explored text role=“ref”｝任务，可以在以下位置找到“sigdata”文件：

```

> ```

${BUILDDIR}/tmp/stamps/i586-poky-linux/db/6.0.30-r1.do_fetch.sigdata.7c048c18222b16ff0bcee2000ef648b1

> ${BUILDDIR}/tmp/stamps/i586 poky linux/db/6.0.30-r1.do_fetch.sigdata.7c048c18222b16ff0bcee2000ef648b1

```

> ```
> ```

For tasks that are accelerated through the shared state (`sstate <overview-manual/concepts:shared state cache>`{.interpreted-text role="ref"}) cache, an additional `siginfo` file is written into `SSTATE_DIR`{.interpreted-text role="term"} along with the cached task output. The `siginfo` files contain exactly the same information as `sigdata` files.

> 对于通过共享状态（`sstate<overview manual/concepts:shared state cache>`{.depredicted text role=“ref”}）缓存加速的任务，会将一个额外的 `siginfo` 文件与缓存的任务输出一起写入 `sstate_DIR`{.depreted text role=“term”}。“siginfo”文件包含与“sigdata”文件完全相同的信息。

3. Run `bitbake-dumpsig` on the `sigdata` or `siginfo` file. Here is an example:

> 3.对“sigdata”或“siginfo”文件运行“bitbake-dumpsig”。以下是一个示例：

```

> ```

$ bitbake-dumpsig ${BUILDDIR}/tmp/stamps/i586-poky-linux/db/6.0.30-r1.do_fetch.sigdata.7c048c18222b16ff0bcee2000ef648b1

> $bitbake-dumpsig$｛BUILDDIR｝/tmp/stamps/i586 poky linux/db/6.0.30-r1.do_fetch.sigdata.7c048c18222b16ff0bcee2000ef648b1

```

> ```
> ```

In the output of the above command, you will find a line like the following, which lists all the (inferred) variable dependencies for the task. This list also includes indirect dependencies from variables depending on other variables, recursively:

> 在上面命令的输出中，您会发现如下一行，其中列出了任务的所有（推断的）变量依赖项。此列表还包括依赖于其他变量的变量的间接依赖项，递归地：

```

> ```

Task dependencies: ['PV', 'SRCREV', 'SRC_URI', 'SRC_URI[md5sum]', 'SRC_URI[sha256sum]', 'base_do_fetch']

> 任务依赖项：['PV'，'SRCREV'，'SRC_URI'，'SRC_URI[md5sum]'，'SRC_URI[sha256sum]'，'base_do_fetch']

```

> ```
> ```

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

Functions (e.g. `base_do_fetch`) also count as variable dependencies. These functions in turn depend on the variables they reference.

> 函数（例如“base_do_fetch”）也算作变量依赖项。这些函数又取决于它们引用的变量。

:::

> ：：：

The output of `bitbake-dumpsig` also includes the value each variable had, a list of dependencies for each variable, and `BB_BASEHASH_IGNORE_VARS`{.interpreted-text role="term"} information.

> “bitbake-dumpsig”的输出还包括每个变量的值、每个变量的依赖项列表以及“BB_BASEHASH_IGNORE_ARS”｛.explored text role=“term”｝信息。

There is also a `bitbake-diffsigs` command for comparing two `siginfo` or `sigdata` files. This command can be helpful when trying to figure out what changed between two versions of a task. If you call `bitbake-diffsigs` with just one file, the command behaves like `bitbake-dumpsig`.

> 还有一个“bitbake-diffsigs”命令用于比较两个“siginfo”或“sigdata”文件。当试图弄清楚任务的两个版本之间发生了什么变化时，此命令会很有帮助。如果只使用一个文件调用“bitbake diffsigs”，则该命令的行为类似于“bitbake-dumpsig”。

You can also use BitBake to dump out the signature construction information without executing tasks by using either of the following BitBake command-line options:

> 您也可以使用 BitBake 来转储签名构造信息，而无需使用以下任一 BitBake 命令行选项执行任务：

```
‐‐dump-signatures=SIGNATURE_HANDLER
-S SIGNATURE_HANDLER
```

::: note
::: title
Note
:::

Two common values for [SIGNATURE_HANDLER]{.title-ref} are \"none\" and \"printdiff\", which dump only the signature or compare the dumped signature with the cached one, respectively.

> [SIGNATURE_HANDLER]｛.title-ref｝的两个常见值是\“none\”和\“printdiff\”，它们分别只转储签名或将转储的签名与缓存的签名进行比较。
> :::

Using BitBake with either of these options causes BitBake to dump out `sigdata` files in the `stamps` directory for every task it would have executed instead of building the specified target package.

> 将 BitBake 与这两个选项中的任何一个结合使用，都会导致 BitBake 为它本应执行的每个任务转储“stamps”目录中的“sigdata”文件，而不是构建指定的目标包。

# Viewing Metadata Used to Create the Input Signature of a Shared State Task

Seeing what metadata went into creating the input signature of a shared state (sstate) task can be a useful debugging aid. This information is available in signature information (`siginfo`) files in `SSTATE_DIR`{.interpreted-text role="term"}. For information on how to view and interpret information in `siginfo` files, see the \"`dev-manual/debugging:viewing task variable dependencies`{.interpreted-text role="ref"}\" section.

> 查看创建共享状态（sstate）任务的输入签名时使用了哪些元数据，这可能是一个有用的调试辅助工具。此信息可在 `SSTATE_DIR`｛.explored text role=“term”｝中的签名信息（`siginfo`）文件中获得。有关如何查看和解释“siginfo”文件中的信息的信息，请参阅\“`dev manual/debugging:viewing task variable dependencies`｛.depreted text role=“ref”｝\”一节。

For conceptual information on shared state, see the \"`overview-manual/concepts:shared state`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> 有关共享状态的概念信息，请参阅 Yocto 项目概述和概念手册中的“`overview manual/concepts:shared state`｛.explored text role=“ref”｝”一节。

# Invalidating Shared State to Force a Task to Run

The OpenEmbedded build system uses `checksums <overview-manual/concepts:checksums (signatures)>`{.interpreted-text role="ref"} and `overview-manual/concepts:shared state`{.interpreted-text role="ref"} cache to avoid unnecessarily rebuilding tasks. Collectively, this scheme is known as \"shared state code\".

> OpenEmbedded 构建系统使用 `checksums<overview manual/concepts:checksums（signatures）>`{.depredicted text role=“ref”}和 `overview manual-concepts:shared state`{.depreced text role=“ref”｝缓存来避免不必要的重建任务。总的来说，这个方案被称为“共享状态代码”。

As with all schemes, this one has some drawbacks. It is possible that you could make implicit changes to your code that the checksum calculations do not take into account. These implicit changes affect a task\'s output but do not trigger the shared state code into rebuilding a recipe. Consider an example during which a tool changes its output. Assume that the output of `rpmdeps` changes. The result of the change should be that all the `package` and `package_write_rpm` shared state cache items become invalid. However, because the change to the output is external to the code and therefore implicit, the associated shared state cache items do not become invalidated. In this case, the build process uses the cached items rather than running the task again. Obviously, these types of implicit changes can cause problems.

> 与所有方案一样，这一方案也有一些缺点。您可能会对代码进行未考虑校验和计算的隐式更改。这些隐含的更改会影响任务的输出，但不会触发共享状态代码重新生成配方。考虑一个工具更改其输出的示例。假设“rpmdeps”的输出发生了更改。更改的结果应该是所有“package”和“package_write_rpm”共享状态缓存项都变为无效。但是，由于对输出的更改是代码外部的，因此是隐式的，因此相关联的共享状态缓存项不会变得无效。在这种情况下，构建过程使用缓存的项，而不是再次运行任务。显然，这些类型的隐含变化可能会引发问题。

To avoid these problems during the build, you need to understand the effects of any changes you make. Realize that changes you make directly to a function are automatically factored into the checksum calculation. Thus, these explicit changes invalidate the associated area of shared state cache. However, you need to be aware of any implicit changes that are not obvious changes to the code and could affect the output of a given task.

> 为了避免在构建过程中出现这些问题，您需要了解所做任何更改的影响。要意识到，直接对函数所做的更改会自动计入校验和计算。因此，这些显式更改使共享状态缓存的相关区域无效。但是，您需要注意任何隐含的更改，这些更改不是对代码的明显更改，可能会影响给定任务的输出。

When you identify an implicit change, you can easily take steps to invalidate the cache and force the tasks to run. The steps you can take are as simple as changing a function\'s comments in the source code. For example, to invalidate package shared state files, change the comment statements of `ref-tasks-package`{.interpreted-text role="ref"} or the comments of one of the functions it calls. Even though the change is purely cosmetic, it causes the checksum to be recalculated and forces the build system to run the task again.

> 当您识别出一个隐含的更改时，您可以很容易地采取步骤使缓存无效并强制运行任务。您可以采取的步骤非常简单，只需更改源代码中函数的注释即可。例如，要使包共享状态文件无效，请更改 `ref tasks package` 的注释语句{.depreted text role=“ref”}或其调用的某个函数的注释。即使更改纯粹是表面上的，它也会导致校验和被重新计算，并迫使构建系统再次运行该任务。

::: note
::: title
Note
:::

For an example of a commit that makes a cosmetic change to invalidate shared state, see this :yocto\_[git:%60commit](git:%60commit) \</poky/commit/meta/classes/package.bbclass?id=737f8bbb4f27b4837047cb9b4fbfe01dfde36d54\>\`.

> 有关进行外观更改以使共享状态无效的提交的示例，请参见：yocto\_[git:%60commit]（git:%60commit）\</poky/commit/meta/classes/package.bbclass？id=737f8bbb4f27b4837047cb9b4fbfe01fde36d54\>\`。
> :::

# Running Specific Tasks

Any given recipe consists of a set of tasks. The standard BitBake behavior in most cases is: `ref-tasks-fetch`{.interpreted-text role="ref"}, `ref-tasks-unpack`{.interpreted-text role="ref"}, `ref-tasks-patch`{.interpreted-text role="ref"}, `ref-tasks-configure`{.interpreted-text role="ref"}, `ref-tasks-compile`{.interpreted-text role="ref"}, `ref-tasks-install`{.interpreted-text role="ref"}, `ref-tasks-package`{.interpreted-text role="ref"}, `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}, and `ref-tasks-build`{.interpreted-text role="ref"}. The default task is `ref-tasks-build`{.interpreted-text role="ref"} and any tasks on which it depends build first. Some tasks, such as `ref-tasks-devshell`{.interpreted-text role="ref"}, are not part of the default build chain. If you wish to run a task that is not part of the default build chain, you can use the `-c` option in BitBake. Here is an example:

> 任何给定的配方都由一组任务组成。在大多数情况下，标准的 BitBake 行为是：`ref tasks fetch`｛.explored text role=“ref”｝，`ref tasks-unpack`｛..explored text-role=“ref”}，` ref tasks-patch`｛.sexplored ext-role=”ref“｝，` ref任务配置`｛.explored text role=”ref“}，`ref-tasks-compile`｛.expered text-role=”ref”}，`ref tasks package`｛.depredicted text role=“ref”｝、`do_package_write_*<ref-tasks-package_write_deb>`｛.repredicted text-role=“ref”}和 `ref tasks-build`｛.epredicted ext-role=”ref“｝。默认任务是“ref tasks build”｛.explored text role=“ref”｝，它所依赖的任何任务都是先构建的。某些任务，如 `ref tasks devshell `｛.explored text role=“ref”｝，不是默认构建链的一部分。如果您希望运行不属于默认构建链的任务，可以使用 BitBake 中的“-c”选项。以下是一个示例：

```
$ bitbake matchbox-desktop -c devshell
```

The `-c` option respects task dependencies, which means that all other tasks (including tasks from other recipes) that the specified task depends on will be run before the task. Even when you manually specify a task to run with `-c`, BitBake will only run the task if it considers it \"out of date\". See the \"`overview-manual/concepts:stamp files and the rerunning of tasks`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual for how BitBake determines whether a task is \"out of date\".

> “-c”选项尊重任务相关性，这意味着指定任务所依赖的所有其他任务（包括其他配方中的任务）将在任务之前运行。即使手动指定要使用“-c”运行的任务，BitBake 也只会在认为该任务“过时”时运行该任务。关于 BitBake 如何确定任务是否“过时”，请参阅 Yocto 项目概述和概念手册中的“概述手册/概念：标记文件和任务的重新运行”｛.explored text role=“ref”｝。

If you want to force an up-to-date task to be rerun (e.g. because you made manual modifications to the recipe\'s `WORKDIR`{.interpreted-text role="term"} that you want to try out), then you can use the `-f` option.

> 如果您想强制重新运行最新的任务（例如，因为您手动修改了要尝试的配方的 `WORKDIR`｛.explored text role=“term”｝），则可以使用 `-f'选项。

::: note
::: title
Note
:::

The reason `-f` is never required when running the `ref-tasks-devshell`{.interpreted-text role="ref"} task is because the \[`nostamp <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>`{.interpreted-text role="ref"}\] variable flag is already set for the task.

> 运行 `ref tasks devshell`｛.depredicted text role=“ref”｝任务时从不需要 `-f` 的原因是，已经为该任务设置了\[`nostamp<bitbake user manual/bitbake user-manual metadata:variable flags>`｛.epredicted text-role=“ref”}\]变量标志。
> :::

The following example shows one way you can use the `-f` option:

> 以下示例显示了使用“-f”选项的一种方法：

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

> 此序列首先构建然后重新编译“火柴盒桌面”。最后一个命令在编译后重新运行所有任务（基本上是打包任务）。BitBake 认识到“ref tasks compile”｛.explored text role=“ref”｝任务已重新运行，因此理解其他任务也需要再次运行。

Another, shorter way to rerun a task and all `ref-manual/tasks:normal recipe build tasks`{.interpreted-text role="ref"} that depend on it is to use the `-C` option.

> 另一种较短的方法是使用“-C”选项来重新运行任务和所有依赖于它的“ref manual/tasks:normal recipe build tasks”｛.depredicted text role=“ref”｝。

::: note
::: title
Note
:::

This option is upper-cased and is separate from the `-c` option, which is lower-cased.

> 此选项是大写的，与小写的“-c”选项是分开的。
> :::

Using this option invalidates the given task and then runs the `ref-tasks-build`{.interpreted-text role="ref"} task, which is the default task if no task is given, and the tasks on which it depends. You could replace the final two commands in the previous example with the following single command:

> 使用此选项将使给定的任务无效，然后运行 `ref tasks build`{.depreted text role=“ref”}任务，如果未给定任务，则该任务是默认任务，以及它所依赖的任务。您可以将上一个示例中的最后两个命令替换为以下单个命令：

```
$ bitbake matchbox-desktop -C compile
```

Internally, the `-f` and `-C` options work by tainting (modifying) the input checksum of the specified task. This tainting indirectly causes the task and its dependent tasks to be rerun through the normal task dependency mechanisms.

> 在内部，“-f”和“-C”选项通过污染（修改）指定任务的输入校验和来工作。这种污染间接地导致任务及其依赖任务通过正常的任务依赖机制重新运行。

::: note
::: title
Note
:::

BitBake explicitly keeps track of which tasks have been tainted in this fashion, and will print warnings such as the following for builds involving such tasks:

> BitBake 明确跟踪哪些任务以这种方式受到污染，并将为涉及此类任务的构建打印以下警告：

```none
WARNING: /home/ulf/poky/meta/recipes-sato/matchbox-desktop/matchbox-desktop_2.1.bb.do_compile is tainted from a forced run
```

The purpose of the warning is to let you know that the work directory and build output might not be in the clean state they would be in for a \"normal\" build, depending on what actions you took. To get rid of such warnings, you can remove the work directory and rebuild the recipe, as follows:

> 警告的目的是让您知道工作目录和生成输出可能不处于“正常”生成时的干净状态，具体取决于您所采取的操作。要消除此类警告，您可以删除工作目录并重新生成配方，如下所示：

```
$ bitbake matchbox-desktop -c clean
$ bitbake matchbox-desktop
```

:::

You can view a list of tasks in a given package by running the `ref-tasks-listtasks`{.interpreted-text role="ref"} task as follows:

> 您可以通过以下方式运行 `ref tasks listtasks`｛.depreted text role=“ref”｝任务来查看给定包中的任务列表：

```
$ bitbake matchbox-desktop -c listtasks
```

The results appear as output to the console and are also in the file `${WORKDIR}/temp/log.do_listtasks`.

> 结果显示为控制台的输出，也在文件“${WORKDIR}/temp/log.do_listtasks”中。

# General BitBake Problems

You can see debug output from BitBake by using the `-D` option. The debug output gives more information about what BitBake is doing and the reason behind it. Each `-D` option you use increases the logging level. The most common usage is `-DDD`.

> 您可以使用“-D”选项查看 BitBake 的调试输出。调试输出提供了有关 BitBake 正在做什么及其背后原因的更多信息。您使用的每个“-D”选项都会增加日志记录级别。最常见的用法是“-DDD”。

The output from `bitbake -DDD -v targetname` can reveal why BitBake chose a certain version of a package or why BitBake picked a certain provider. This command could also help you in a situation where you think BitBake did something unexpected.

> “bitbake-DDD-v targetname”的输出可以揭示 bitbake 为什么选择某个包版本，或者 bitbake 选择某个提供程序的原因。这个命令也可以在你认为 BitBake 做了一些意想不到的事情的情况下帮助你。

# Building with No Dependencies

To build a specific recipe (`.bb` file), you can use the following command form:

> 要构建特定的配方（`.bb` 文件），可以使用以下命令形式：

```
$ bitbake -b somepath/somerecipe.bb
```

This command form does not check for dependencies. Consequently, you should use it only when you know existing dependencies have been met.

> 此命令表单不检查依赖项。因此，只有当您知道已经满足了现有的依赖关系时，才应该使用它。

::: note
::: title
Note
:::

You can also specify fragments of the filename. In this case, BitBake checks for a unique match.

> 您也可以指定文件名的片段。在这种情况下，BitBake 会检查唯一的匹配。
> :::

# Recipe Logging Mechanisms

The Yocto Project provides several logging functions for producing debugging output and reporting errors and warnings. For Python functions, the following logging functions are available. All of these functions log to `${T}/log.do_`[task]{.title-ref}, and can also log to standard output (stdout) with the right settings:

> Yocto 项目提供了几个日志记录功能，用于生成调试输出和报告错误和警告。对于 Python 函数，可以使用以下日志记录函数。所有这些函数都登录到 `${T}/log.do_`[task]{.title-ref}，还可以使用正确的设置登录到标准输出（stdout）：

- `bb.plain(msg)`: Writes msg as is to the log while also logging to stdout.
- `bb.note(msg)`: Writes \"NOTE: msg\" to the log. Also logs to stdout if BitBake is called with \"-v\".

> -`bb.note（msg）`：将\“note:msg\”写入日志。如果使用\“-v\”调用 BitBake，也会登录到 stdout。

- `bb.debug(level, msg)`: Writes \"DEBUG: msg\" to the log. Also logs to stdout if the log level is greater than or equal to level. See the \"`bitbake-user-manual/bitbake-user-manual-intro:usage and syntax`{.interpreted-text role="ref"}\" option in the BitBake User Manual for more information.

> -`bb.debug（level，msg）`：将\“debug:msg\”写入日志。如果日志级别大于或等于级别，也会记录到 stdout。有关详细信息，请参阅《bitbake 用户手册》中的\“`bitbake用户手册/bitbake用户指南简介：用法和语法`{.depreted text role=”ref“}\”选项。

- `bb.warn(msg)`: Writes \"WARNING: msg\" to the log while also logging to stdout.
- `bb.error(msg)`: Writes \"ERROR: msg\" to the log while also logging to standard out (stdout).

  ::: note
  ::: title

  Note

> 笔记
> :::

Calling this function does not cause the task to fail.

> 调用此函数不会导致任务失败。
> :::

- `bb.fatal(msg)`: This logging function is similar to `bb.error(msg)` but also causes the calling task to fail.

> -`bb.thall（msg）`：此日志记录函数类似于 `bb.error（msg）`，但也会导致调用任务失败。

::: note
::: title

Note

> 笔记
> :::

`bb.fatal()` raises an exception, which means you do not need to put a \"return\" statement after the function.

> `bb.fatal（）` 引发一个异常，这意味着您不需要在函数后面放置\“return \”语句。
> :::

The same logging functions are also available in shell functions, under the names `bbplain`, `bbnote`, `bbdebug`, `bbwarn`, `bberror`, and `bbfatal`. The `ref-classes-logging`{.interpreted-text role="ref"} class implements these functions. See that class in the `meta/classes` folder of the `Source Directory`{.interpreted-text role="term"} for information.

> shell 函数中也提供了相同的日志记录函数，名称分别为“bbplain”、“bbnote”、“bbsdebug”、”bbwarn“、”bberror“和”bbfatal“。`ref classes logging`｛.explored text role=“ref”｝类实现了这些函数。有关信息，请参阅“源目录”的“meta/classes”文件夹中的该类。

## Logging With Python

When creating recipes using Python and inserting code that handles build logs, keep in mind the goal is to have informative logs while keeping the console as \"silent\" as possible. Also, if you want status messages in the log, use the \"debug\" loglevel.

> 当使用 Python 创建配方并插入处理构建日志的代码时，请记住，目标是在保持控制台尽可能“静音”的同时，提供信息丰富的日志。此外，如果您希望在日志中显示状态消息，请使用\“debug\”日志级别。

Following is an example written in Python. The code handles logging for a function that determines the number of tasks needed to be run. See the \"`ref-tasks-listtasks`{.interpreted-text role="ref"}\" section for additional information:

> 下面是一个用 Python 编写的示例。该代码处理一个函数的日志记录，该函数确定需要运行的任务数。有关其他信息，请参阅\“`ref tasks listtasks`｛.explored text role=“ref”｝\”部分：

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

> 当使用 Bash 创建配方并插入处理构建日志的代码时，您有相同的目标\-以最少的控制台输出提供信息。用 Bash 编写的配方使用的语法与上一节中描述的用 Python 编写的配方类似。

Following is an example written in Bash. The code logs the progress of the `do_my_function` function:

> 下面是用 Bash 写的一个例子。该代码记录了“do_my_function”函数的进度：

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

> 当构建由同时运行的几个部分组成时，会发生并行的“生成”竞赛，而当一个部分的输出或结果未准备好与依赖于该输出的构建的不同部分一起使用时，会出现这种情况。平行制作比赛很烦人，有时很难复制和修复。但是，有一些简单的提示和技巧可以帮助您调试和修复它们。本节介绍了 Yocto 项目自动生成器上遇到的错误以及用于修复该错误的过程的真实示例。

::: note
::: title
Note
:::

If you cannot properly fix a `make` race condition, you can work around it by clearing either the `PARALLEL_MAKE`{.interpreted-text role="term"} or `PARALLEL_MAKEINST`{.interpreted-text role="term"} variables.

> 如果您无法正确修复“生成”竞争条件，您可以通过清除“PARALLEL_make `｛.expected text role=“term”｝或“PARALLEL _MAKEIST`｛.rexpected textrole=”term”}变量来解决此问题。
> :::

## The Failure

For this example, assume that you are building an image that depends on the \"neard\" package. And, during the build, BitBake runs into problems and creates the following output.

> 对于本例，假设您正在构建一个依赖于“neard”包的映像。而且，在构建过程中，BitBake 遇到问题并创建以下输出。

::: note
::: title
Note
:::

This example log file has longer lines artificially broken to make the listing easier to read.

> 此示例日志文件人为地打断了较长的行，以使列表更易于阅读。
> :::

If you examine the output or the log file, you see the failure during `make`:

> 如果您检查输出或日志文件，您会在“make”期间看到故障：

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

> 因为比赛条件是间歇性的，所以它们不会在每次构建时都表现出来。事实上，大多数情况下，即使存在潜在的种族条件，构建也会顺利完成。因此，一旦出现错误，您就需要一种方法来重现它。

In this example, compiling the \"neard\" package is causing the problem. So the first thing to do is build \"neard\" locally. Before you start the build, set the `PARALLEL_MAKE`{.interpreted-text role="term"} variable in your `local.conf` file to a high number (e.g. \"-j 20\"). Using a high value for `PARALLEL_MAKE`{.interpreted-text role="term"} increases the chances of the race condition showing up:

> 在本例中，编译“neard”包导致了此问题。因此，首先要做的是在本地构建“neard”。在开始构建之前，请将 `local.conf` 文件中的 `PARALLEL_MAKE`｛.explored text role=“term”｝变量设置为一个较大的数字（例如\“-j 20\”）。对 `PARALLEL_MAKE`｛.explored text role=“term”｝使用高值会增加出现竞争条件的几率：

```
$ bitbake neard
```

Once the local build for \"neard\" completes, start a `devshell` build:

> “neard”的本地构建完成后，启动“devshell”构建：

```
$ bitbake neard -c devshell
```

For information on how to use a `devshell`, see the \"`dev-manual/development-shell:using a development shell`{.interpreted-text role="ref"}\" section.

> 有关如何使用“devshell”的信息，请参阅\“`devmanual/development-shell:using a development-hell`｛.explored text role=”ref“｝\”一节。

In the `devshell`, do the following:

> 在“devshell”中，执行以下操作：

```
$ make clean
$ make tools/snep-send.o
```

The `devshell` commands cause the failure to clearly be visible. In this case, there is a missing dependency for the `neard` Makefile target. Here is some abbreviated, sample output with the missing dependency clearly visible at the end:

> “devshell”命令导致故障清晰可见。在这种情况下，“neard”Makefile 目标缺少依赖项。以下是一些缩写的示例输出，其缺失的依赖项在末尾清晰可见：

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

> 由于 Makefile 目标缺少依赖项，您需要修补从“Makefile.in”生成的“Makefile.am”文件。您可以使用 Quilt 创建修补程序：

```
$ quilt new parallelmake.patch
Patch patches/parallelmake.patch is now on top
$ quilt add Makefile.am
File Makefile.am added to patch patches/parallelmake.patch
```

For more information on using Quilt, see the \"`dev-manual/quilt:using quilt in your workflow`{.interpreted-text role="ref"}\" section.

> 有关使用被子的更多信息，请参阅\“`dev manual/coase:在工作流中使用被子`{.depreted text role=“ref”}\”一节。

At this point you need to make the edits to `Makefile.am` to add the missing dependency. For our example, you have to add the following line to the file:

> 此时，您需要对“Makefile.am”进行编辑，以添加丢失的依赖项。对于我们的示例，您必须将以下行添加到文件中：

```
tools/snep-send.$(OBJEXT): include/near/dbus.h
```

Once you have edited the file, use the `refresh` command to create the patch:

> 编辑完文件后，使用“刷新”命令创建修补程序：

```
$ quilt refresh
Refreshed patch patches/parallelmake.patch
```

Once the patch file is created, you need to add it back to the originating recipe folder. Here is an example assuming a top-level `Source Directory`{.interpreted-text role="term"} named `poky`:

> 创建补丁文件后，您需要将其添加回原始配方文件夹。以下是一个假设名为“poky”的顶级“源目录”｛.explored text role=“term”｝的示例：

```
$ cp patches/parallelmake.patch poky/meta/recipes-connectivity/neard/neard
```

The final thing you need to do to implement the fix in the build is to update the \"neard\" recipe (i.e. `neard-0.14.bb`) so that the `SRC_URI`{.interpreted-text role="term"} statement includes the patch file. The recipe file is in the folder above the patch. Here is what the edited `SRC_URI`{.interpreted-text role="term"} statement would look like:

> 要在构建中实现修复，您需要做的最后一件事是更新\“neard\”配方（即 `neard-0.14.bb`），以便 `SRC_URI`｛.explored text role=“term”｝语句包含修补程序文件。配方文件位于补丁上方的文件夹中。以下是经过编辑的 `SRC_URI`｛.explored text role=“term”｝语句的样子：

```
SRC_URI = "${KERNELORG_MIRROR}/linux/network/nfc/${BPN}-${PV}.tar.xz \
           file://neard.in \
           file://neard.service.in \
           file://parallelmake.patch \
          "
```

With the patch complete and moved to the correct folder and the `SRC_URI`{.interpreted-text role="term"} statement updated, you can exit the `devshell`:

> 修补程序完成并移到正确的文件夹，并且更新了 `SRC_URI`｛.explored text role=“term”｝语句后，您可以退出 `devshell'：

```
$ exit
```

## Testing the Build

With everything in place, you can get back to trying the build again locally:

> 一切就绪后，您可以在本地重新尝试构建：

```
$ bitbake neard
```

This build should succeed.

> 此构建应该会成功。

Now you can open up a `devshell` again and repeat the clean and make operations as follows:

> 现在，您可以再次打开“devshell”，并按如下所示重复 clean 和 make 操作：

```
$ bitbake neard -c devshell
$ make clean
$ make tools/snep-send.o
```

The build should work without issue.

> 构建应该可以正常工作。

As with all solved problems, if they originated upstream, you need to submit the fix for the recipe in OE-Core and upstream so that the problem is taken care of at its source. See the \"`dev-manual/changes:submitting a change to the yocto project`{.interpreted-text role="ref"}\" section for more information.

> 与所有已解决的问题一样，如果它们起源于上游，您需要在 OE Core 和上游提交配方的修复程序，以便从源头上解决问题。有关详细信息，请参阅\“`dev manual/changes:submitting a change to the yocto project`｛.explored text role=”ref“｝\”一节。

# Debugging With the GNU Project Debugger (GDB) Remotely

GDB allows you to examine running programs, which in turn helps you to understand and fix problems. It also allows you to perform post-mortem style analysis of program crashes. GDB is available as a package within the Yocto Project and is installed in SDK images by default. See the \"`ref-manual/images:Images`{.interpreted-text role="ref"}\" chapter in the Yocto Project Reference Manual for a description of these images. You can find information on GDB at [https://sourceware.org/gdb/](https://sourceware.org/gdb/).

> GDB 允许您检查正在运行的程序，从而帮助您理解和解决问题。它还允许您对程序崩溃执行事后风格的分析。GDB 在 Yocto 项目中作为一个包提供，默认情况下安装在 SDK 映像中。有关这些图像的描述，请参阅 Yocto 项目参考手册中的“`ref manual/images:images`｛.explored text role=“ref”｝\”一章。有关 GDB 的信息，请访问 [https://sourceware.org/gdb/](https://sourceware.org/gdb/)。

::: note
::: title
Note
:::

For best results, install debug (`-dbg`) packages for the applications you are going to debug. Doing so makes extra debug symbols available that give you more meaningful output.

> 为了获得最佳结果，请为要调试的应用程序安装调试（“-dbg”）包。这样做会使额外的调试符号可用，从而为您提供更有意义的输出。
> :::

Sometimes, due to memory or disk space constraints, it is not possible to use GDB directly on the remote target to debug applications. These constraints arise because GDB needs to load the debugging information and the binaries of the process being debugged. Additionally, GDB needs to perform many computations to locate information such as function names, variable names and values, stack traces and so forth \-\-- even before starting the debugging process. These extra computations place more load on the target system and can alter the characteristics of the program being debugged.

> 有时，由于内存或磁盘空间限制，无法直接在远程目标上使用 GDB 来调试应用程序。出现这些约束是因为 GDB 需要加载调试信息和正在调试的进程的二进制文件。此外，即使在开始调试过程之前，GDB 也需要执行许多计算来定位信息，如函数名、变量名和值、堆栈跟踪等。这些额外的计算给目标系统带来了更多的负载，并可能改变正在调试的程序的特性。

To help get past the previously mentioned constraints, there are two methods you can use: running a debuginfod server and using gdbserver.

> 为了帮助克服前面提到的限制，可以使用两种方法：运行 debuginfod 服务器和使用 gdbserver。

## Using the debuginfod server method

`debuginfod` from `elfutils` is a way to distribute `debuginfo` files. Running a `debuginfod` server makes debug symbols readily available, which means you don\'t need to download debugging information and the binaries of the process being debugged. You can just fetch debug symbols from the server.

> `debuginfod` from `elfutils'是分发` debuginfo` 文件的一种方式。运行“debuginfod”服务器可以使调试符号随时可用，这意味着您不需要下载调试信息和正在调试的进程的二进制文件。您可以从服务器中获取调试符号。

To run a `debuginfod` server, you need to do the following:

> 要运行“debuginfod”服务器，您需要执行以下操作：

- Ensure that `debuginfod` is present in `DISTRO_FEATURES`{.interpreted-text role="term"} (it already is in `OpenEmbedded-core` defaults and `poky` reference distribution). If not, set in your distro config file or in `local.conf`:

> -确保 `debuginfod` 存在于 `DISTRO_FEATURE`｛.depredicted text role=“term”｝中（它已经存在于 `OpenEmbedded core` 默认值和 `poky` 引用分布中）。如果没有，请在您的发行版配置文件或“local.conf”中设置：

```
DISTRO_FEATURES:append = " debuginfod"
```

This distro feature enables the server and client library in `elfutils`, and enables `debuginfod` support in clients (at the moment, `gdb` and `binutils`).

> 此发行版功能在“elutils”中启用服务器和客户端库，并在客户端中启用“debuginfod”支持（目前为“gdb”和“binutils”）。

- Run the following commands to launch the `debuginfod` server on the host:

  ```
  $ oe-debuginfod
  ```
- To use `debuginfod` on the target, you need to know the ip:port where `debuginfod` is listening on the host (port defaults to 8002), and export that into the shell environment, for example in `qemu`:

> -要在目标上使用“debuginfod”，您需要知道“debuginfod'在主机上侦听的 ip:port（端口默认为 8002），并将其导出到 shell 环境中，例如在“qemu”中：

```
root@qemux86-64:~# export DEBUGINFOD_URLS="http://192.168.7.1:8002/"
```

- Then debug info fetching should simply work when running the target `gdb`, `readelf` or `objdump`, for example:

> -然后，调试信息获取应该在运行目标“gdb”、“readelf”或“objdump”时简单地工作，例如：

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

> gdbserver，它在远程目标上运行，不从已调试的进程加载任何调试信息。相反，GDB 实例处理在远程计算机（主机 GDB）上运行的调试信息。然后，主机 GDB 向 gdbserver 发送控制命令，使其停止或启动调试程序，以及读取或写入该调试程序的内存区域。所有加载和处理的调试信息以及所有繁重的调试都由主机 GDB 完成。卸载这些进程使在目标上运行的 gdbserver 有机会保持小型和快速。

Because the host GDB is responsible for loading the debugging information and for doing the necessary processing to make actual debugging happen, you have to make sure the host can access the unstripped binaries complete with their debugging information and also be sure the target is compiled with no optimizations. The host GDB must also have local access to all the libraries used by the debugged program. Because gdbserver does not need any local debugging information, the binaries on the remote target can remain stripped. However, the binaries must also be compiled without optimization so they match the host\'s binaries.

> 因为主机 GDB 负责加载调试信息并进行必要的处理以进行实际调试，所以您必须确保主机能够访问带有调试信息的未压缩二进制文件，还必须确保编译目标时没有进行任何优化。主机 GDB 还必须具有对调试程序使用的所有库的本地访问权限。因为 gdbserver 不需要任何本地调试信息，所以远程目标上的二进制文件可以保持剥离状态。但是，二进制文件也必须在没有优化的情况下编译，以便与主机的二进制文件匹配。

To remain consistent with GDB documentation and terminology, the binary being debugged on the remote target machine is referred to as the \"inferior\" binary. For documentation on GDB see the [GDB site](https://sourceware.org/gdb/documentation/).

> 为了与 GDB 文档和术语保持一致，在远程目标计算机上调试的二进制文件被称为“劣质”二进制文件。有关 GDB 的文档，请参阅 [GDB 网站](https://sourceware.org/gdb/documentation/)。

The following steps show you how to debug using the GNU project debugger.

> 以下步骤向您展示了如何使用 GNU 项目调试器进行调试。

1. *Configure your build system to construct the companion debug filesystem:*

> 1.*配置您的构建系统以构建配套的调试文件系统：*

In your `local.conf` file, set the following:

> 在“local.conf”文件中，设置以下内容：

```

> ```

IMAGE_GEN_DEBUGFS = "1"

> IMAGE_GEN_DEBUGFS=“1”

IMAGE_FSTYPES_DEBUGFS = "tar.bz2"

> IMAGE_FSTYPES_DEBUGFS=“tar.bz2”

```

> ```
> ```

These options cause the OpenEmbedded build system to generate a special companion filesystem fragment, which contains the matching source and debug symbols to your deployable filesystem. The build system does this by looking at what is in the deployed filesystem, and pulling the corresponding `-dbg` packages.

> 这些选项使 OpenEmbedded 构建系统生成一个特殊的配套文件系统片段，其中包含与可部署文件系统匹配的源代码和调试符号。构建系统通过查看部署的文件系统中的内容，并提取相应的“-dbg”包来实现这一点。

The companion debug filesystem is not a complete filesystem, but only contains the debug fragments. This filesystem must be combined with the full filesystem for debugging. Subsequent steps in this procedure show how to combine the partial filesystem with the full filesystem.

> 伴随的调试文件系统不是一个完整的文件系统，而是只包含调试片段。这个文件系统必须与完整的文件系统结合起来进行调试。此过程中的后续步骤显示了如何将部分文件系统与完整文件系统结合起来。

2. *Configure the system to include gdbserver in the target filesystem:*

> 2.*将系统配置为在目标文件系统中包括 gdbserver：*

Make the following addition in your `local.conf` file:

> 在“local.conf”文件中添加以下内容：

```

> ```

EXTRA_IMAGE_FEATURES:append = " tools-debug"

> EXTRA_IMAGE_FEATURES:append=“工具调试”

```

> ```
> ```

The change makes sure the `gdbserver` package is included.

> 此更改确保包含“gdbserver”包。

3. *Build the environment:*

> 3.*营造环境：*

Use the following command to construct the image and the companion Debug Filesystem:

> 使用以下命令构建映像和配套的调试文件系统：

```

> ```

$ bitbake image

> $bitbake图像

```

> ```
> ```

Build the cross GDB component and make it available for debugging. Build the SDK that matches the image. Building the SDK is best for a production build that can be used later for debugging, especially during long term maintenance:

> 构建跨 GDB 组件并使其可用于调试。生成与映像匹配的 SDK。构建 SDK 最适合以后用于调试的生产构建，尤其是在长期维护期间：

```

> ```

$ bitbake -c populate_sdk image

> $bitbake-c populate_sdk图像

```

> ```
> ```

Alternatively, you can build the minimal toolchain components that match the target. Doing so creates a smaller than typical SDK and only contains a minimal set of components with which to build simple test applications, as well as run the debugger:

> 或者，您可以构建与目标匹配的最小工具链组件。这样做会创建一个比典型 SDK 更小的 SDK，并且只包含用于构建简单测试应用程序以及运行调试器的最小组件集：

```

> ```

$ bitbake meta-toolchain

> $bitbake元工具链

```

> ```
> ```

A final method is to build Gdb itself within the build system:

> 最后一种方法是在构建系统中构建 Gdb 本身：

```

> ```

$ bitbake gdb-cross-<architecture>

> $bitbake-gdb交叉-＜架构＞

```

> ```
> ```

Doing so produces a temporary copy of `cross-gdb` you can use for debugging during development. While this is the quickest approach, the two previous methods in this step are better when considering long-term maintenance strategies.

> 这样做会生成“cross-gdb”的临时副本，您可以在开发过程中使用该副本进行调试。虽然这是最快的方法，但在考虑长期维护策略时，本步骤中的前两种方法更好。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

If you run `bitbake gdb-cross`, the OpenEmbedded build system suggests the actual image (e.g. `gdb-cross-i586`). The suggestion is usually the actual name you want to use.

> 如果运行“bitbake-gdb-cross”，OpenEmbedded 构建系统会建议实际图像（例如“gdb-crossi586”）。建议通常是您想要使用的实际名称。

:::

> ：：：

4. *Set up the* `debugfs`*:*

> 4.*设置* `debugfs`*：*

Run the following commands to set up the `debugfs`:

> 运行以下命令以设置“debugfs”：

```

> ```

$ mkdir debugfs

> $mkdir调试

$ cd debugfs

> $cd调试

$ tar xvfj build-dir/tmp/deploy/images/machine/image.rootfs.tar.bz2

> $tar xvfj构建目录/tmp/deploy/images/machine/image.rootfs.tar.bz2

$ tar xvfj build-dir/tmp/deploy/images/machine/image-dbg.rootfs.tar.bz2

> $tar xvfj构建目录/tmp/deploy/images/machine/image-dbg.rootfs.tar.bz2

```

> ```
> ```

5. *Set up GDB:*

> 5.*建立 GDB：*

Install the SDK (if you built one) and then source the correct environment file. Sourcing the environment file puts the SDK in your `PATH` environment variable and sets `$GDB` to the SDK\'s debugger.

> 安装 SDK（如果您构建了 SDK），然后获取正确的环境文件。获取环境文件会将 SDK 放入“PATH”环境变量中，并将“$GDB”设置为 SDK 的调试器。

If you are using the build system, Gdb is located in [build-dir]{.title-ref}`/tmp/sysroots/`[host]{.title-ref}`/usr/bin/`[architecture]{.title-ref}`/`[architecture]{.title-ref}`-gdb`

> 如果您使用的是生成系统，则 Gdb 位于[build-dir]｛.title-ref}`/tmp/sysroots/`[host]｛.title-ref｝`/usr/bin/`[architecture]｛\title-ref-}`/`[accitecture]{.title-ref}`-Gdb中`

6. *Boot the target:*

> 6.*引导目标：*

For information on how to run QEMU, see the [QEMU Documentation](https://wiki.qemu.org/Documentation/GettingStartedDevelopers).

> 有关如何运行 QEMU 的信息，请参阅 [QEMU 文档](https://wiki.qemu.org/Documentation/GettingStartedDevelopers)。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

Be sure to verify that your host can access the target via TCP.

> 请确保验证您的主机是否可以通过 TCP 访问目标。

:::

> ：：：

7. *Debug a program:*

> 7.*调试程序：*

Debugging a program involves running gdbserver on the target and then running Gdb on the host. The example in this step debugs `gzip`:

> 调试程序包括在目标上运行 gdbserver，然后在主机上运行 Gdb。此步骤中的示例调试“gzip”：

```shell

> ```外壳

root@qemux86:~# gdbserver localhost:1234 /bin/gzip —help

> root@qemux86：~#gdbserver localhost:1234/bin/gzip-help

```

> ```
> ```

For additional gdbserver options, see the [GDB Server Documentation](https://www.gnu.org/software/gdb/documentation/).

> 有关其他 gdbserver 选项，请参阅 [GDB Server Documentation](https://www.gnu.org/software/gdb/documentation/)。

After running gdbserver on the target, you need to run Gdb on the host and configure it and connect to the target. Use these commands:

> 在目标上运行 gdbserver 之后，您需要在主机上运行 Gdb 并对其进行配置，然后连接到目标。使用以下命令：

```

> ```

$ cd directory-holding-the-debugfs-directory

> $cd目录，其中包含debugfs目录

$ arch-gdb

> 澳元gdb

(gdb) set sysroot debugfs

> （gdb）设置sysroot调试

(gdb) set substitute-path /usr/src/debug debugfs/usr/src/debug

> （gdb）设置替代路径/usr/src/debug-debugfs/usr/src/debug

(gdb) target remote IP-of-target:1234

> （gdb）目标的目标远程IP:1234

```

> ```
> ```

At this point, everything should automatically load (i.e. matching binaries, symbols and headers).

> 此时，所有内容都应该自动加载（即匹配的二进制文件、符号和标头）。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

The Gdb `set` commands in the previous example can be placed into the users `~/.gdbinit` file. Upon starting, Gdb automatically runs whatever commands are in that file.

> 上一个例子中的 Gdb“set”命令可以放在用户的“~/.gdbinit”文件中。启动后，Gdb 会自动运行该文件中的任何命令。

:::

> ：：：

8. *Deploying without a full image rebuild:*

> 8.*在不重建完整映像的情况下进行部署：*

In many cases, during development you want a quick method to deploy a new binary to the target and debug it, without waiting for a full image build.

> 在许多情况下，在开发过程中，您需要一种快速方法来将新的二进制文件部署到目标并对其进行调试，而无需等待完整的映像构建。

One approach to solving this situation is to just build the component you want to debug. Once you have built the component, copy the executable directly to both the target and the host `debugfs`.

> 解决这种情况的一种方法是只构建要调试的组件。构建组件后，将可执行文件直接复制到目标和主机“debugfs”。

If the binary is processed through the debug splitting in OpenEmbedded, you should also copy the debug items (i.e. `.debug` contents and corresponding `/usr/src/debug` files) from the work directory. Here is an example:

> 如果二进制文件是通过 OpenEmbedded 中的调试拆分处理的，则还应从工作目录中复制调试项（即“.debug”内容和相应的“/usr/src/debug”文件）。以下是一个示例：

```

> ```

$ bitbake bash

> $bitbake狂欢

$ bitbake -c devshell bash

> $bitbake-c devshell bash

$ cd ..

> $cd。。

$ scp packages-split/bash/bin/bash target:/bin/bash

> $scp包split/bash/bin/bash目标：/bin/bassh

$ cp -a packages-split/bash-dbg/\* path/debugfs

> $cp-a包拆分/bash-dbg/\*path/debugfs

```

> ```
> ```

# Debugging with the GNU Project Debugger (GDB) on the Target

The previous section addressed using GDB remotely for debugging purposes, which is the most usual case due to the inherent hardware limitations on many embedded devices. However, debugging in the target hardware itself is also possible with more powerful devices. This section describes what you need to do in order to support using GDB to debug on the target hardware.

> 上一节讨论了出于调试目的远程使用 GDB，这是由于许多嵌入式设备固有的硬件限制而导致的最常见情况。然而，使用更强大的设备也可以在目标硬件本身中进行调试。本节描述了为了支持在目标硬件上使用 GDB 进行调试，您需要做些什么。

To support this kind of debugging, you need do the following:

> 要支持这种调试，您需要执行以下操作：

- Ensure that GDB is on the target. You can do this by making the following addition to your `local.conf` file:

> -确保 GDB 在目标上。您可以通过在“local.conf”文件中添加以下内容来完成此操作：

```
EXTRA_IMAGE_FEATURES:append = " tools-debug"
```

- Ensure that debug symbols are present. You can do so by adding the corresponding `-dbg` package to `IMAGE_INSTALL`{.interpreted-text role="term"}:

> -请确保存在调试符号。您可以通过将相应的“-dbg”包添加到 `IMAGE_INSTALL`｛.explored text role=“term”｝中来完成此操作：

```
IMAGE_INSTALL:append = " packagename-dbg"
```

Alternatively, you can add the following to `local.conf` to include all the debug symbols:

> 或者，您可以将以下内容添加到“local.conf”以包括所有调试符号：

```
EXTRA_IMAGE_FEATURES:append = " dbg-pkgs"
```

::: note
::: title
Note
:::

To improve the debug information accuracy, you can reduce the level of optimization used by the compiler. For example, when adding the following line to your `local.conf` file, you will reduce optimization from `FULL_OPTIMIZATION`{.interpreted-text role="term"} of \"-O2\" to `DEBUG_OPTIMIZATION`{.interpreted-text role="term"} of \"-O -fno-omit-frame-pointer\":

> 为了提高调试信息的准确性，可以降低编译器使用的优化级别。例如，当将以下行添加到您的“local.conf”文件中时，您将把优化从\“-O2\”的“FULL_optimization”｛.depredicted text role=“term”｝减少到\“-O-fno 省略帧指针\”的“DEBUG_optimization”｛.epredicted textrole=”term“｝：

```
DEBUG_BUILD = "1"
```

Consider that this will reduce the application\'s performance and is recommended only for debugging purposes.

> 请考虑这会降低应用程序的性能，建议仅用于调试目的。
> :::

# Other Debugging Tips

Here are some other tips that you might find useful:

> 以下是一些你可能会觉得有用的其他提示：

- When adding new packages, it is worth watching for undesirable items making their way into compiler command lines. For example, you do not want references to local system files like `/usr/lib/` or `/usr/include/`.

> -在添加新包时，值得注意的是，是否有不需要的项进入编译器命令行。例如，您不希望引用诸如“/usr/lib/”或“/usr/include/”之类的本地系统文件。

- If you want to remove the `psplash` boot splashscreen, add `psplash=false` to the kernel command line. Doing so prevents `psplash` from loading and thus allows you to see the console. It is also possible to switch out of the splashscreen by switching the virtual console (e.g. Fn+Left or Fn+Right on a Zaurus).

> -如果您想删除“pslash”引导 splashscreen，请在内核命令行中添加“pslash=false”。这样做可以防止“psflash”加载，从而允许您查看控制台。也可以通过切换虚拟控制台（例如 Zaurus 上的 Fn+Left 或 Fn+Right）来切换出防溅屏。

- Removing `TMPDIR`{.interpreted-text role="term"} (usually `tmp/`, within the `Build Directory`{.interpreted-text role="term"}) can often fix temporary build issues. Removing `TMPDIR`{.interpreted-text role="term"} is usually a relatively cheap operation, because task output will be cached in `SSTATE_DIR`{.interpreted-text role="term"} (usually `sstate-cache/`, which is also in the `Build Directory`{.interpreted-text role="term"}).

> -删除 `TMPDIR`｛.depreted text role=“term”｝（通常为 `tmp/`，在 `Build Directory`｛.repreted text role=”term“｝中）通常可以解决临时生成问题。删除 `TMPDIR`｛.depredicted text role=“term”｝通常是一项相对便宜的操作，因为任务输出将缓存在 `SSTATE_DIR`{.depredicte text role=”term“｝中（通常为 `SSTATE-cache/`，也在 `Build Directory`｛.epredicted text-role=”term”}中）。

::: note
::: title

Note

> 笔记
> :::

Removing `TMPDIR`{.interpreted-text role="term"} might be a workaround rather than a fix. Consequently, trying to determine the underlying cause of an issue before removing the directory is a good idea.

> 删除 `TMPDIR`｛.explored text role=“term”｝可能是一种变通方法，而不是修复方法。因此，在删除目录之前尝试确定问题的根本原因是一个好主意。
> :::

- Understanding how a feature is used in practice within existing recipes can be very helpful. It is recommended that you configure some method that allows you to quickly search through files.

> -了解一个功能是如何在现有配方中实际使用的，这将非常有帮助。建议您配置一些方法，以便快速搜索文件。

Using GNU Grep, you can use the following shell function to recursively search through common recipe-related files, skipping binary files, `.git` directories, and the `Build Directory`{.interpreted-text role="term"} (assuming its name starts with \"build\"):

> 使用 GNU Grep，您可以使用以下 shell 函数递归搜索常见的配方相关文件，跳过二进制文件、`.git` 目录和 `Build Directory`｛.depreted text role=“term”｝（假设其名称以\“Build\”开头）：

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

> 以下是一些用法示例：

```
$ g FOO # Search recursively for "FOO"
$ g -i foo # Search recursively for "foo", ignoring case
$ g -w FOO # Search recursively for "FOO" as a word, ignoring e.g. "FOOBAR"
```

If figuring out how some feature works requires a lot of searching, it might indicate that the documentation should be extended or improved. In such cases, consider filing a documentation bug using the Yocto Project implementation of :yocto_bugs:[Bugzilla \<\>]{.title-ref}. For information on how to submit a bug against the Yocto Project, see the Yocto Project Bugzilla :yocto_wiki:[wiki page \</Bugzilla_Configuration_and_Bug_Tracking\>]{.title-ref} and the \"`dev-manual/changes:submitting a defect against the yocto project`{.interpreted-text role="ref"}\" section.

> 如果要弄清楚某些功能是如何工作的需要大量搜索，这可能表明文档应该进行扩展或改进。在这种情况下，可以考虑使用 Yocto Project 实现的：Yocto_bugs:[Bugzilla \<\>]｛.title-ref｝来提交文档错误。有关如何针对 Yocto 项目提交错误的信息，请参阅 Yocto 项目 Bugzilla：Yocto_wiki:[wiki-page\</Bugzilla _Configuration_and_Bug_Track\>]｛.title-ref｝和“`dev manual/changes:submit a defect against the Yocto Project`｛.depredicted text role=“ref”｝”一节。

::: note
::: title

Note

> 笔记
> :::

The manuals might not be the right place to document variables that are purely internal and have a limited scope (e.g. internal variables used to implement a single `.bbclass` file).

> 手册可能不是记录纯内部变量和范围有限的变量（例如，用于实现单个“.bbclass”文件的内部变量）的合适位置。
> :::
